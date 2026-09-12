# D — Dependency Inversion Principle (DIP)

> **Definición:** "Los módulos de alto nivel no deben depender de módulos de bajo nivel. Ambos deben depender de abstracciones. Las abstracciones no deben depender de los detalles. Los detalles deben depender de las abstracciones." — **Robert C. Martin**

Pertenece a: [[SOLID]] → D

---

## El problema: el módulo de alto nivel instancia el detalle

El antipatrón más destructivo en software empresarial: el caso de uso instancia directamente la implementación concreta de infraestructura.

```typescript
// ❌ VIOLA DIP: PedidoService depende directamente de PostgreSQL
import { PrismaClient } from "@prisma/client";

class PedidoService {
  private readonly db: PrismaClient;

  constructor() {
    this.db = new PrismaClient(); // ← detalle concreto instanciado adentro
  }

  async crearPedido(datos: DatosPedido): Promise<Pedido> {
    const pedido = Pedido.crear(datos);

    await this.db.pedido.create({
      data: {
        id: pedido.id,
        clienteId: pedido.clienteId,
        estado: pedido.getEstado(),
        total: pedido.total(),
        creadoEn: pedido.creadoEn,
      },
    });

    return pedido;
  }

  async buscarPedido(id: string): Promise<Pedido | null> {
    const row = await this.db.pedido.findUnique({ where: { id } });
    if (!row) return null;

    return new Pedido(row.id, row.clienteId, /* ... */);
  }
}
```

Problemas de este diseño:

| Problema | Consecuencia |
|---|---|
| **No se puede testear** sin PostgreSQL corriendo | Tests lentos, frágiles, con Docker |
| **Cambiar de BD** requiere reescribir `PedidoService` | Migrar de PostgreSQL a MongoDB = reescribir toda la capa de aplicación |
| **Alto acoplamiento** | `PedidoService` conoce Prisma, SQL, esquemas de tabla |
| **Rompe [[Hexagonal Architecture]]** | La lógica de negocio depende del framework, no al revés |

---

## La solución: depender de una abstracción

`PedidoService` debe depender de una interfaz que **él mismo define** (o que define el dominio). La implementación concreta se inyecta desde afuera:

```typescript
// ✅ CUMPLE DIP: Servicio depende de la abstracción PedidoRepository
interface PedidoRepository {
  guardar(pedido: Pedido): Promise<void>;
  buscarPorId(id: string): Promise<Pedido | null>;
}

class PedidoService {
  // El constructor recibe la abstracción — no le importa quién ni cómo la implementa
  constructor(private readonly pedidoRepo: PedidoRepository) {}

  async crearPedido(datos: DatosPedido): Promise<Pedido> {
    const pedido = Pedido.crear(datos);
    await this.pedidoRepo.guardar(pedido);
    return pedido;
  }

  async buscarPedido(id: string): Promise<Pedido | null> {
    return this.pedidoRepo.buscarPorId(id);
  }
}
```

Las implementaciones concretas viven en infraestructura y **dependen de la interfaz**, no al revés:

```typescript
// ✅ Implementación PostgreSQL — depende de PedidoRepository (abstracción)
class PrismaPedidoRepository implements PedidoRepository {
  constructor(private readonly prisma: PrismaClient) {}

  async guardar(pedido: Pedido): Promise<void> {
    await this.prisma.pedido.create({ data: this.toPrisma(pedido) });
  }

  async buscarPorId(id: string): Promise<Pedido | null> {
    const row = await this.prisma.pedido.findUnique({ where: { id } });
    return row ? this.toDomain(row) : null;
  }

  private toPrisma(pedido: Pedido) { /* serialización dominio → BD */ }
  private toDomain(row: unknown): Pedido { /* deserialización BD → dominio */ }
}

// ✅ Implementación MongoDB — misma interfaz, distinta tecnología
class MongoPedidoRepository implements PedidoRepository {
  constructor(private readonly collection: Collection) {}

  async guardar(pedido: Pedido): Promise<void> {
    await this.collection.insertOne(this.toDocument(pedido));
  }

  async buscarPorId(id: string): Promise<Pedido | null> {
    const doc = await this.collection.findOne({ _id: id });
    return doc ? this.toDomain(doc) : null;
  }

  private toDocument(pedido: Pedido) { /* serialización dominio → BSON */ }
  private toDomain(doc: unknown): Pedido { /* deserialización BSON → dominio */ }
}
```

El gráfico de dependencias se invierte:

```
SIN DIP:
   PedidoService ──► PrismaPedidoRepository ──► PrismaClient ──► PostgreSQL
   (alto nivel)       (detalle)                  (framework)     (BD)

CON DIP:
   PedidoService ──► PedidoRepository ◄── PrismaPedidoRepository ──► PostgreSQL
   (alto nivel)       (abstracción)       (detalle implementa)
```

> La flecha de dependencia en compilación ahora apunta hacia la abstracción. `PedidoService` no conoce Prisma, PostgreSQL ni MongoDB. Solo conoce `PedidoRepository`.

---

## Inyección manual vs contenedor de DI

El Principio de Inversión de Dependencias (DIP) **no requiere** un framework de Inyección de Dependencias. Se puede cumplir con inyección manual:

```typescript
// ✅ DIP con inyección manual — sin frameworks
const prisma = new PrismaClient();
const pedidoRepo = new PrismaPedidoRepository(prisma);
const pedidoService = new PedidoService(pedidoRepo);

// En tests, inyectás una implementación falsa
const fakeRepo = new InMemoryPedidoRepository();
const sut = new PedidoService(fakeRepo);
```

Con un contenedor de DI como el de NestJS, la misma idea se vuelve declarativa:

```typescript
// ✅ DIP con NestJS — tokens de inyección
// domain/pedido-repository.interface.ts
export const PEDIDO_REPOSITORY = Symbol("PedidoRepository");

export interface PedidoRepository {
  guardar(pedido: Pedido): Promise<void>;
  buscarPorId(id: string): Promise<Pedido | null>;
}

// application/pedido.service.ts
@Injectable()
export class PedidoService {
  constructor(
    @Inject(PEDIDO_REPOSITORY)
    private readonly pedidoRepo: PedidoRepository
  ) {}

  async crearPedido(datos: DatosPedido): Promise<Pedido> {
    const pedido = Pedido.crear(datos);
    await this.pedidoRepo.guardar(pedido);
    return pedido;
  }
}

// infrastructure/prisma-pedido.repository.ts
@Injectable()
export class PrismaPedidoRepository implements PedidoRepository {
  constructor(private readonly prisma: PrismaClient) {}

  async guardar(pedido: Pedido): Promise<void> { /* ... */ }
  async buscarPorId(id: string): Promise<Pedido | null> { /* ... */ }
}

// infrastructure/pedido.module.ts — el módulo ata la interfaz a la implementación
@Module({
  providers: [
    PedidoService,
    { provide: PEDIDO_REPOSITORY, useClass: PrismaPedidoRepository },
  ],
  exports: [PedidoService],
})
export class PedidoModule {}
```

> El `Symbol` (`PEDIDO_REPOSITORY`) evita el acoplamiento por nombre de clase. El módulo de infraestructura decide qué implementación usar. El dominio y la aplicación no importan nada de NestJS salvo el token, que es un valor sin lógica.

Para cambiar de PostgreSQL a MongoDB **solo se modifica el binding en el módulo**:

```typescript
// Antes:
{ provide: PEDIDO_REPOSITORY, useClass: PrismaPedidoRepository }

// Después:
{ provide: PEDIDO_REPOSITORY, useClass: MongoPedidoRepository }

// PedidoService no cambió. PedidoRepository no cambió.
// Solo cambió el binding en infrastructure.
```

---

## DIP vs DI — No son lo mismo

Esta es una de las confusiones más frecuentes:

| | DIP (Principio) | DI (Técnica) |
|---|---|---|
| **Qué es** | Principio de diseño: "dependé de abstracciones" | Técnica de implementación: "recibí las dependencias desde afuera" |
| **Quién** | Lo define Robert C. Martin | Lo popularizó Martin Fowler |
| **Propósito** | Desacoplar módulos de alto y bajo nivel | Evitar que un objeto instancie sus propias dependencias |
| **Relación** | Es el QUÉ | Es el CÓMO |

```typescript
// DIP sin DI (manual) — el principio se cumple, la técnica es rudimentaria
const repo = new PrismaPedidoRepository(new PrismaClient());
const service = new PedidoService(repo);

// DIP con DI (framework) — el principio se cumple, la técnica es automatizada
@Injectable()
class PedidoService {
  constructor(@Inject(PEDIDO_REPOSITORY) private readonly repo: PedidoRepository) {}
}

// DI sin DIP — no alcanza para desacoplar si la interfaz vive del lado equivocado
// ❌ PedidoService depende de PrismaPedidoRepository (clase concreta), no de PedidoRepository
@Injectable()
class PedidoService {
  constructor(private readonly repo: PrismaPedidoRepository) {} // ← depende de concreción
}
```

> Podés tener Inyección de Dependencias sin Inversión de Dependencias — tu código sigue acoplado a implementaciones concretas, solo que ahora se inyectan. La DI es un medio; el DIP es el fin.

---

## Conexión con Arquitectura Hexagonal

El Principio de Inversión de Dependencias **es** la [[Dependency Rule|Regla de Dependencia]] de [[Hexagonal Architecture]]. Es la razón de ser de los [[Ports|puertos]]:

```
                   ┌──────────────────────────┐
                   │        DOMINIO            │
                   │                           │
                   │  Define:                   │
                   │  • Pedido (entidad)        │
                   │  • PedidoRepository        │
                   │    (interfaz / puerto)     │
                   │                           │
                   │  NO conoce:                │
                   │  • PrismaClient            │
                   │  • PostgreSQL              │
                   │  • Express / Fastify       │
                   └────────────┬──────────────┘
                                │
                         implements
                                │
          ┌─────────────────────┼─────────────────────┐
          │                     │                     │
   ┌──────▼──────┐      ┌──────▼──────┐      ┌──────▼──────┐
   │ Prisma      │      │ Mongo       │      │ InMemory    │
   │ Repository  │      │ Repository  │      │ Repository  │
   │             │      │             │      │             │
   │ Adaptador   │      │ Adaptador   │      │ Adaptador   │
   │ PostgreSQL  │      │ MongoDB     │      │ Tests       │
   └─────────────┘      └─────────────┘      └─────────────┘

   La dependencia en compilación va hacia adentro (hacia la abstracción).
   Las implementaciones concretas dependen de la interfaz que define el dominio.
```

El flujo completo en una arquitectura hexagonal con DIP aplicado:

```typescript
// ===== DOMAIN ===== (sin dependencias externas)
class Pedido {
  private constructor(
    readonly id: string,
    readonly clienteId: string,
    private estado: EstadoPedido,
    readonly items: LineaPedido[],
    readonly creadoEn: Date
  ) {}

  static crear(datos: DatosPedido): Pedido {
    return new Pedido(
      generarId(),
      datos.clienteId,
      "PENDIENTE",
      datos.items.map(i => new LineaPedido(i.productoId, i.cantidad, i.precioUnitario)),
      new Date()
    );
  }

  total(): Dinero {
    const monto = this.items.reduce((acc, i) => acc + i.subtotal().monto, 0);
    return new Dinero(monto, "MXN");
  }
}

// ===== APPLICATION/PORT/OUTPUT ===== (interfaz = puerto secundario)
interface PedidoRepository {
  guardar(pedido: Pedido): Promise<void>;
  buscarPorId(id: string): Promise<Pedido | null>;
}

// ===== APPLICATION/SERVICE ===== (caso de uso = orquestador)
class CrearPedidoUseCase {
  constructor(private readonly pedidoRepo: PedidoRepository) {}

  async ejecutar(datos: DatosPedido): Promise<Pedido> {
    const pedido = Pedido.crear(datos);

    if (pedido.total().monto <= 0) {
      throw new Error("El pedido debe tener un monto positivo");
    }

    await this.pedidoRepo.guardar(pedido);
    return pedido;
  }
}

// ===== INFRASTRUCTURE ===== (adaptador concreto)
class PrismaPedidoRepository implements PedidoRepository {
  constructor(private readonly prisma: PrismaClient) {}

  async guardar(pedido: Pedido): Promise<void> {
    await this.prisma.pedido.create({
      data: {
        id: pedido.id,
        clienteId: pedido.clienteId,
        estado: pedido.getEstado(),
        total: pedido.total().monto,
        creadoEn: pedido.creadoEn,
      },
    });
  }

  async buscarPorId(id: string): Promise<Pedido | null> {
    const row = await this.prisma.pedido.findUnique({ where: { id } });
    return row ? this.toDomain(row) : null;
  }

  private toDomain(row: any): Pedido {
    return new Pedido(row.id, row.clienteId, row.estado, [], row.creadoEn);
  }
}
```

> El dominio define **qué** necesita (`PedidoRepository`). La infraestructura implementa **cómo** se obtiene (`PrismaPedidoRepository`). La dependencia en compilación siempre apunta hacia el dominio. Eso es DIP. Eso es Arquitectura Hexagonal.

---

## Errores comunes

### 1. Confundir DIP con DI

Creer que por usar un contenedor de DI ya se cumple DIP. **No.** Si tu servicio recibe una clase concreta en vez de una interfaz, tenés DI sin DIP:

```typescript
// ❌ DI sin DIP: se inyecta PrismaPedidoRepository (clase concreta), no PedidoRepository
class PedidoService {
  constructor(private readonly repo: PrismaPedidoRepository) {}
}
```

### 2. Abstracciones demasiado genéricas

`IRepository<T>` con métodos `find`, `save`, `delete` es una abstracción que no expresa el dominio. DIP no significa "poner interfaces a todo" — significa que las abstracciones deben ser **definidas por el que las usa**:

```typescript
// ❌ Genérica y sin significado de dominio
interface IRepository<T> {
  find(id: string): Promise<T>;
  save(entity: T): Promise<void>;
}

// ✅ Específica del dominio, definida desde el caso de uso
interface PedidoRepository {
  guardar(pedido: Pedido): Promise<void>;
  buscarPorId(id: string): Promise<Pedido | null>;
  buscarPendientesDeCliente(clienteId: string): Promise<Pedido[]>;
}
```

### 3. Poner la interfaz del lado equivocado

El error más grave: definir la interfaz en infraestructura y hacer que el dominio la importe:

```
❌ Estructura incorrecta:
   infrastructure/
   └── PedidoRepository.ts  ← define la interfaz
   application/
   └── PedidoService.ts     ← import { PedidoRepository } from "../infrastructure/..."

✅ Estructura correcta:
   application/port/output/
   └── PedidoRepository.ts  ← define la interfaz (el que la USA)
   infrastructure/
   └── PrismaPedidoRepository.ts  ← import { PedidoRepository } from "../../application/..."
```

> La interfaz debe vivir **donde se usa**, no donde se implementa. Quien define el contrato es quien lo consume. La infraestructura se adapta al contrato, no al revés.

---

## 📚 Referencias

- 📖 *"The Dependency Inversion Principle"* — Robert C. Martin (artículo original, 1996)
- 📖 *"Agile Software Development: Principles, Patterns, and Practices"* — Robert C. Martin (Capítulo 11: DIP)
- 📖 *"Clean Architecture"* — Robert C. Martin (Capítulo 11: DIP)
- 🌐 [[SOLID]] — los cinco principios
- 🌐 [[Hexagonal Architecture]] — DIP materializado como arquitectura
- 🌐 [[Dependency Rule]] — la Regla de Dependencia es DIP aplicado a nivel sistema
- 🌐 [[Ports]] — los puertos son la abstracción que DIP requiere
- 🌐 [[I - Interface Segregation]] — ISP y DIP se complementan: abstracciones pequeñas y bien definidas

### Nodos SOLID
- [[S - Single Responsibility]]
- [[O - Open Closed]]
- [[L - Liskov Substitution]]
- [[I - Interface Segregation]]
