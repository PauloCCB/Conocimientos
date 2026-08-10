# I — Interface Segregation Principle (ISP)

> **Definición:** "Ningún cliente debe ser forzado a depender de métodos que no usa." — **Robert C. Martin**

Pertenece a: [[SOLID]] → I

---

## El problema: la interfaz gorda

Una interfaz que agrupa demasiados métodos obliga a las implementaciones a definir código que nunca usarán. El caso más común en aplicaciones empresariales: el repositorio todoterreno.

```typescript
// ❌ VIOLA ISP: Interfaz gorda que obliga a implementarlo todo
interface ICrudRepository<T> {
  create(entity: T): Promise<void>;
  read(id: string): Promise<T | null>;
  update(id: string, entity: T): Promise<void>;
  delete(id: string): Promise<void>;
  search(query: string): Promise<T[]>;
  export(format: "csv" | "json" | "pdf"): Promise<Buffer>;
  import(data: Buffer): Promise<T[]>;
}
```

Cuando un contexto solo necesita **leer**, se ve obligado a implementar métodos de escritura que jamás usará:

```typescript
// ❌ El servicio de reportes solo necesita leer, pero debe implementar TODA la interfaz
class ReportePedidoRepository implements ICrudRepository<Pedido> {
  constructor(private readonly db: Database) {}

  async read(id: string): Promise<Pedido | null> {
    return this.db.pedido.findUnique({ where: { id } });
  }

  async search(query: string): Promise<Pedido[]> {
    return this.db.pedido.findMany({ where: { descripcion: { contains: query } } });
  }

  // Métodos que NUNCA se usarán pero hay que implementar igual
  async create(): Promise<void> {
    throw new Error("ReportePedidoRepository es de solo lectura");
  }

  async update(): Promise<void> {
    throw new Error("ReportePedidoRepository es de solo lectura");
  }

  async delete(): Promise<void> {
    throw new Error("ReportePedidoRepository es de solo lectura");
  }

  async export(): Promise<Buffer> {
    throw new Error("Exportación no soportada en este repositorio");
  }

  async import(): Promise<Buffer> {
    throw new Error("Importación no soportada en este repositorio");
  }
}
```

> Cinco de siete métodos lanzan excepciones. La interfaz miente: dice ser un CRUD completo, pero esta implementación es solo lectura. Además, quien consume `ICrudRepository<Pedido>` no sabe que `create()` puede explotar en runtime.

---

## La solución: partir en interfaces pequeñas y cohesivas

Cada interfaz representa **un rol** que un cliente puede necesitar. Ningún cliente se ve forzado a implementar lo que no usa:

```typescript
// ✅ CUMPLE ISP: Interfaces específicas para cada responsabilidad
interface Lector<T> {
  read(id: string): Promise<T | null>;
}

interface Escritor<T> {
  create(entity: T): Promise<void>;
  update(id: string, entity: T): Promise<void>;
  delete(id: string): Promise<void>;
}

interface Buscador<T> {
  search(query: string): Promise<T[]>;
}

interface Exportador<T> {
  export(format: "csv" | "json"): Promise<Buffer>;
}

interface Importador<T> {
  import(data: Buffer): Promise<T[]>;
}
```

Ahora cada implementación solo adopta las interfaces que realmente necesita:

```typescript
// ✅ ReportePedidoRepository solo implementa lo que usa
class ReportePedidoRepository implements Lector<Pedido>, Buscador<Pedido> {
  constructor(private readonly db: Database) {}

  async read(id: string): Promise<Pedido | null> {
    return this.db.pedido.findUnique({ where: { id } });
  }

  async search(query: string): Promise<Pedido[]> {
    return this.db.pedido.findMany({ where: { descripcion: { contains: query } } });
  }
  // Sin create, update, delete, export, import — no los necesita
}

// ✅ El repositorio administrativo sí implementa todo el CRUD
class AdminPedidoRepository
  implements Lector<Pedido>, Escritor<Pedido>, Buscador<Pedido>, Exportador<Pedido>
{
  constructor(private readonly db: Database) {}

  async read(id: string): Promise<Pedido | null> {
    return this.db.pedido.findUnique({ where: { id } });
  }

  async create(pedido: Pedido): Promise<void> {
    await this.db.pedido.create({ data: pedido });
  }

  async update(id: string, pedido: Pedido): Promise<void> {
    await this.db.pedido.update({ where: { id }, data: pedido });
  }

  async delete(id: string): Promise<void> {
    await this.db.pedido.delete({ where: { id } });
  }

  async search(query: string): Promise<Pedido[]> {
    return this.db.pedido.findMany({ where: { clienteId: { contains: query } } });
  }

  async export(format: "csv" | "json"): Promise<Buffer> {
    const pedidos = await this.db.pedido.findMany();
    return format === "csv" ? toCsv(pedidos) : Buffer.from(JSON.stringify(pedidos));
  }
}
```

---

## Ejemplo en el dominio de pedidos

**Escenario:** Un sistema de pedidos tiene varios casos de uso, cada uno con necesidades distintas de acceso a datos.

```typescript
// Interfaces segregadas para el dominio de pedidos
interface PedidoLector {
  buscarPorId(id: string): Promise<Pedido | null>;
  buscarPorCliente(clienteId: string): Promise<Pedido[]>;
}

interface PedidoEscritor {
  guardar(pedido: Pedido): Promise<void>;
  actualizarEstado(id: string, estado: EstadoPedido): Promise<void>;
}

interface PedidoBuscador {
  buscarPorRangoDeFechas(desde: Date, hasta: Date): Promise<Pedido[]>;
  buscarPorEstado(estado: EstadoPedido): Promise<Pedido[]>;
}

interface PedidoContador {
  contarPorCliente(clienteId: string): Promise<number>;
  contarPorEstado(estado: EstadoPedido): Promise<number>;
}
```

Cada caso de uso pide **exactamente** lo que necesita, sin dependencias innecesarias:

```typescript
// ✅ Este caso de uso solo necesita leer — pide PedidoLector
class ConsultarPedidoUseCase {
  constructor(private readonly pedidoLector: PedidoLector) {}

  async ejecutar(id: string): Promise<Pedido | null> {
    return this.pedidoLector.buscarPorId(id);
  }
}

// ✅ Este caso de uso necesita leer y escribir — pide ambos
class CancelarPedidoUseCase {
  constructor(
    private readonly pedidoLector: PedidoLector,
    private readonly pedidoEscritor: PedidoEscritor
  ) {}

  async ejecutar(id: string, motivo: string): Promise<void> {
    const pedido = await this.pedidoLector.buscarPorId(id);
    if (!pedido) throw new Error("Pedido no encontrado");

    pedido.cancelar(motivo);
    await this.pedidoEscritor.actualizarEstado(id, pedido.getEstado());
  }
}

// ✅ Este caso de uso solo necesita estadísticas — pide PedidoContador
class DashboardPedidosUseCase {
  constructor(private readonly pedidoContador: PedidoContador) {}

  async ejecutar(clienteId: string): Promise<DashboardPedidos> {
    const [pendientes, confirmados, cancelados] = await Promise.all([
      this.pedidoContador.contarPorEstado("PENDIENTE"),
      this.pedidoContador.contarPorEstado("CONFIRMADO"),
      this.pedidoContador.contarPorEstado("CANCELADO"),
    ]);

    return { pendientes, confirmados, cancelados };
  }
}
```

La implementación concreta puede combinar todas las interfaces:

```typescript
// ✅ Una sola clase puede implementar múltiples interfaces segregadas
class PrismaPedidoRepository
  implements PedidoLector, PedidoEscritor, PedidoBuscador, PedidoContador
{
  constructor(private readonly prisma: PrismaClient) {}

  async buscarPorId(id: string): Promise<Pedido | null> {
    const row = await this.prisma.pedido.findUnique({ where: { id } });
    return row ? this.toDomain(row) : null;
  }

  async buscarPorCliente(clienteId: string): Promise<Pedido[]> {
    const rows = await this.prisma.pedido.findMany({ where: { clienteId } });
    return rows.map(r => this.toDomain(r));
  }

  async guardar(pedido: Pedido): Promise<void> {
    await this.prisma.pedido.create({ data: this.toPrisma(pedido) });
  }

  async actualizarEstado(id: string, estado: EstadoPedido): Promise<void> {
    await this.prisma.pedido.update({ where: { id }, data: { estado } });
  }

  async buscarPorRangoDeFechas(desde: Date, hasta: Date): Promise<Pedido[]> {
    const rows = await this.prisma.pedido.findMany({
      where: { creadoEn: { gte: desde, lte: hasta } },
    });
    return rows.map(r => this.toDomain(r));
  }

  async buscarPorEstado(estado: EstadoPedido): Promise<Pedido[]> {
    const rows = await this.prisma.pedido.findMany({ where: { estado } });
    return rows.map(r => this.toDomain(r));
  }

  async contarPorCliente(clienteId: string): Promise<number> {
    return this.prisma.pedido.count({ where: { clienteId } });
  }

  async contarPorEstado(estado: EstadoPedido): Promise<number> {
    return this.prisma.pedido.count({ where: { estado } });
  }

  private toDomain(row: unknown): Pedido { /* ... */ }
  private toPrisma(pedido: Pedido): unknown { /* ... */ }
}
```

> `PrismaPedidoRepository` implementa cuatro interfaces, pero quien inyecta la dependencia solo ve la que pidió. `ConsultarPedidoUseCase` recibe un `PedidoLector` — no sabe ni le importa que el objeto concreto también sepa buscar por fechas o contar.

---

## Conexión con Arquitectura Hexagonal

En [[Hexagonal Architecture]], ISP se aplica directamente al diseño de [[Ports|puertos secundarios]]. Cada puerto debe ser **pequeño y específico**:

```
❌ Sin ISP:
   application/port/output/
   └── PedidoInfrastructure.ts    ← 1 archivo, 20 métodos:
                                     guardar(), buscarPorId(), enviarEmail(),
                                     procesarPago(), publicarEvento(),
                                     generarReporte(), notificarCliente()...

✅ Con ISP:
   application/port/output/
   ├── PedidoRepository.ts        ← 4 métodos: guardar, buscar, actualizar, eliminar
   ├── NotificacionService.ts     ← 1 método: enviar
   ├── PagoGateway.ts             ← 1 método: procesar
   ├── EventPublisher.ts          ← 1 método: publicar
   └── ReporteGenerator.ts        ← 1 método: generar
```

No crees un mega-puerto `PedidoInfrastructure` con 20 métodos. Dividí por **responsabilidad de dominio**: `PedidoRepository`, `NotificacionService`, `EventPublisher`, `PagoGateway`. Cada uno lo implementa un [[Adapters|adaptador]] distinto, y cada caso de uso solo depende de los puertos que realmente necesita.

```typescript
// ✅ Puerto secundario pequeño y cohesivo
// application/port/output/NotificacionService.ts
interface NotificacionService {
  enviarEmailConfirmacion(pedido: Pedido): Promise<void>;
}

// ✅ Puerto secundario pequeño y cohesivo
// application/port/output/EventPublisher.ts
interface EventPublisher {
  publicar(evento: DomainEvent): Promise<void>;
}

// Puerto primario que solo depende de lo necesario
class ConfirmarPedidoUseCase {
  constructor(
    private readonly pedidoRepo: PedidoRepository,      // ← solo 4 métodos
    private readonly notificacion: NotificacionService,  // ← solo 1 método
    private readonly eventos: EventPublisher             // ← solo 1 método
  ) {}

  async ejecutar(id: string): Promise<void> {
    const pedido = await this.pedidoRepo.buscarPorId(id);
    if (!pedido) throw new Error("Pedido no encontrado");

    pedido.confirmar();

    await this.pedidoRepo.guardar(pedido);
    await this.notificacion.enviarEmailConfirmacion(pedido);
    await this.eventos.publicar(new PedidoConfirmado(pedido.id));
  }
}
```

---

## Errores comunes

### 1. Crear interfaces "para el futuro"

```typescript
// ❌ "Por si acaso algún día necesitamos importar desde FTP..."
interface PedidoLoader {
  desdeBD(id: string): Promise<Pedido>;       // ← se usa
  desdeFTP(path: string): Promise<Pedido>;    // ← nunca se usó ni se usará
  desdeS3(bucket: string, key: string): Promise<Pedido>;  // ← tampoco
}
```

> ISP dice: no adivinés el futuro. Agregá métodos a la interfaz **cuando aparezca un cliente que los necesita**, no antes.

### 2. Miedo a tener "demasiadas" interfaces

Tener 10 interfaces de 2 métodos cada una **es mejor** que tener 2 interfaces de 10 métodos. Las interfaces pequeñas son más fáciles de nombrar, testear, mockear y entender:

```typescript
// ✅ Mejor mil veces: interfaces chicas con nombres expresivos
interface PedidoPersistible { guardar(p: Pedido): Promise<void>; }
interface PedidoRecuperable { buscarPorId(id: string): Promise<Pedido | null>; }
interface PedidoContable { contarPorCliente(id: string): Promise<number>; }
```

### 3. Agrupar métodos no relacionados "porque sí"

```typescript
// ❌ ¿Qué tienen en común reporte y notificación? Nada. No van en la misma interfaz.
interface PedidoSalida {
  generarReporte(filtros: FiltroReporte): Promise<Buffer>;
  enviarNotificacionCliente(pedido: Pedido): Promise<void>;
  publicarEnRedis(evento: string): Promise<void>;
}
```

> Si dos métodos no son usados por los mismos clientes, **deben estar en interfaces distintas**. La cohesión de una interfaz la definen sus consumidores, no la conveniencia de quien la implementa.

---

## 📚 Referencias

- 📖 *"Agile Software Development: Principles, Patterns, and Practices"* — Robert C. Martin (Capítulo 12: ISP)
- 📖 *"Clean Architecture"* — Robert C. Martin (Capítulo 10: ISP)
- 🌐 [[SOLID]] — los cinco principios
- 🌐 [[Hexagonal Architecture]] — ISP aplicado a puertos secundarios
- 🌐 [[Ports]] — puertos pequeños y cohesivos
- 🌐 [[Adapters]] — cada adaptador implementa las interfaces que necesita

### Nodos SOLID
- [[S - Single Responsibility]]
- [[O - Open Closed]]
- [[L - Liskov Substitution]]
- [[D - Dependency Inversion]]
