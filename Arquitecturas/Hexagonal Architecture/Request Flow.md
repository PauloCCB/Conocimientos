# Request Flow (Flujo de una Petición)

> **Definición:** El recorrido completo que hace una petición desde que un actor externo interactúa con el sistema hasta que se produce una respuesta o efecto secundario, atravesando todas las capas del hexágono en el orden correcto.

Pertenece a: [[Hexagonal Architecture]] → Funcionamiento

---

## Diagrama completo

```
                     ┌───────────────────────────────────────────┐
                     │              HEXÁGONO                     │
                     │                                           │
   Usuario           │   ┌─────────┐         ┌──────────────┐   │
   ───────►          │   │ Puerto  │         │   Dominio    │   │
   POST /pedidos     │   │Primario │────────►│              │   │
                     │   │         │         │  Aggregate   │   │
   ◄───────          │   │ Caso de │         │  Root        │   │
   201 Created       │   │   Uso   │◄────────│              │   │
                     │   └─────────┘         │  Validaciones│   │
                     │        │              │  Invariantes │   │
                     │        │              │  Domain      │   │
                     │        │              │  Events      │   │
                     │        │              └──────┬───────┘   │
                     │        │                     │           │
                     │        │              ┌──────▼───────┐   │
                     │        │              │   Puerto     │   │
                     │        └──────────────┤  Secundario  │   │
                     │                       │              │   │
                     │                       │  Repository  │   │
                     │                       └──────┬───────┘   │
                     │                              │           │
                     └──────────────────────────────┼───────────┘
                                                    │
                     ┌──────────────────────────────┼───────────┐
                     │        ADAPTADORES           │           │
                     │                              ▼           │
                     │  ┌──────────┐     ┌──────────────────┐   │
                     │  │ REST     │     │   PostgreSQL     │   │
                     │  │Controller│     │   (INSERT)       │   │
                     │  └──────────┘     └──────────────────┘   │
                     └───────────────────────────────────────────┘
```

---

## Paso a paso: crear un pedido

### Paso 1: El adaptador primario recibe la petición

```
Usuario ──► POST /api/pedidos { "clienteId": "...", "items": [...] }
```

```java
@RestController
@RequestMapping("/api/pedidos")
public class PedidoController {

    private final CrearPedidoUseCase crearPedido;

    @PostMapping
    public ResponseEntity<PedidoResponse> crear(@RequestBody @Valid CrearPedidoRequest request) {
        // 1. Traduce DTO externo → objeto de aplicación
        DatosPedido datos = request.toDomain();

        // 2. Invoca el puerto primario
        Pedido pedido = crearPedido.ejecutar(datos);

        // 3. Traduce dominio → DTO de respuesta
        return ResponseEntity
            .created(URI.create("/api/pedidos/" + pedido.getId()))
            .body(PedidoResponse.from(pedido));
    }
}
```

```typescript
// TypeScript: Paso 1 — Adaptador primario Express/NestJS
@Controller("/api/pedidos")
export class PedidoController {
  constructor(private readonly crearPedido: CrearPedidoUseCase) {}

  @Post()
  async crear(@Body() request: CrearPedidoRequest): Promise<PedidoResponse> {
    // 1. Traduce DTO externo → objeto de aplicación
    const datos: DatosPedido = request.toDomain();

    // 2. Invoca el puerto primario
    const pedido = this.crearPedido.ejecutar(datos);

    // 3. Traduce dominio → DTO de respuesta
    return PedidoResponse.from(pedido);
  }
}
```

El Controller es solo un **traductor + invocador**. Cero lógica de negocio.

---

### Paso 2: El puerto primario (caso de uso) recibe la orden

```java
// Puerto primario (interfaz en application/port/input/)
public interface CrearPedidoUseCase {
    Pedido ejecutar(DatosPedido datos);
}

// Implementación del caso de uso (en application/service/)
public class CrearPedidoService implements CrearPedidoUseCase {

    private final PedidoRepository pedidoRepository;
    private final ClienteRepository clienteRepository;
    private final EventPublisher eventPublisher;

    @Override
    public Pedido ejecutar(DatosPedido datos) {
        // 1. Verifica precondiciones con puertos secundarios
        Cliente cliente = clienteRepository.buscarPorId(datos.clienteId())
            .orElseThrow(() -> new DomainException("Cliente no existe"));

        if (!cliente.puedeCrearPedidos()) {
            throw new DomainException("Cliente bloqueado");
        }

        // 2. Delega al dominio
        Pedido pedido = Pedido.crear(datos.clienteId());
        for (var item : datos.items()) {
            pedido.agregarLinea(item.productoId(), item.cantidad(), item.precioUnitario());
        }

        // 3. Persiste via puerto secundario
        pedidoRepository.guardar(pedido);

        // 4. Publica eventos via puerto secundario
        eventPublisher.publicar(pedido.obtenerEventos());

        return pedido;
    }
}
```

```typescript
// TypeScript: Paso 2 — Puerto primario y caso de uso

// Puerto primario (en application/port/input/)
export interface CrearPedidoUseCase {
  ejecutar(datos: DatosPedido): Pedido;
}

// Implementación del caso de uso (en application/service/)
@Injectable()
export class CrearPedidoService implements CrearPedidoUseCase {
  constructor(
    private readonly pedidoRepository: PedidoRepository,
    private readonly clienteRepository: ClienteRepository,
    private readonly eventPublisher: EventPublisher,
  ) {}

  ejecutar(datos: DatosPedido): Pedido {
    // 1. Verifica precondiciones con puertos secundarios
    const cliente = this.clienteRepository.buscarPorId(datos.clienteId);
    if (!cliente) throw new DomainException("Cliente no existe");
    if (!cliente.puedeCrearPedidos()) throw new DomainException("Cliente bloqueado");

    // 2. Delega al dominio
    const pedido = Pedido.crear(datos.clienteId);
    for (const item of datos.items) {
      pedido.agregarLinea(item.productoId, item.cantidad, item.precioUnitario);
    }

    // 3. Persiste via puerto secundario
    this.pedidoRepository.guardar(pedido);

    // 4. Publica eventos
    this.eventPublisher.publicar(pedido.obtenerEventos());

    return pedido;
  }
}
```

El caso de uso es un **orquestador**: obtiene lo necesario, delega al dominio, persiste, publica eventos. No contiene reglas de negocio.

---

### Paso 3: El dominio aplica las reglas de negocio

```java
public class Pedido {

    private final PedidoId id;
    private final ClienteId clienteId;
    private EstadoPedido estado;
    private final List<LineaDePedido> lineas;
    private final List<DomainEvent> eventos;

    private Pedido(PedidoId id, ClienteId clienteId) {
        this.id = id;
        this.clienteId = clienteId;
        this.estado = EstadoPedido.BORRADOR;
        this.lineas = new ArrayList<>();
        this.eventos = new ArrayList<>();
    }

    public static Pedido crear(ClienteId clienteId) {
        Pedido pedido = new Pedido(PedidoId.nuevo(), clienteId);
        pedido.eventos.add(new PedidoCreado(pedido.id, clienteId, Instant.now()));
        return pedido;
    }

    public void agregarLinea(ProductoId producto, Cantidad cantidad, Dinero precio) {
        if (estado != EstadoPedido.BORRADOR) {
            throw new DomainException("Solo pedidos en borrador aceptan nuevas líneas");
        }
        if (precio.esNegativo() || precio.esCero()) {
            throw new DomainException("El precio debe ser positivo");
        }
        lineas.add(new LineaDePedido(LineaId.nuevo(), producto, cantidad, precio));
    }
}
```

```typescript
// TypeScript: Paso 3 — El dominio aplica reglas de negocio
export class Pedido {
  private readonly id: PedidoId;
  private readonly clienteId: ClienteId;
  private estado: EstadoPedido;
  private readonly lineas: LineaDePedido[];
  private readonly eventos: DomainEvent[];

  private constructor(id: PedidoId, clienteId: ClienteId) {
    this.id = id;
    this.clienteId = clienteId;
    this.estado = EstadoPedido.BORRADOR;
    this.lineas = [];
    this.eventos = [];
  }

  static crear(clienteId: ClienteId): Pedido {
    const pedido = new Pedido(PedidoId.nuevo(), clienteId);
    pedido.eventos.push(new PedidoCreado(pedido.id, clienteId, new Date()));
    return pedido;
  }

  agregarLinea(producto: ProductoId, cantidad: Cantidad, precio: Dinero): void {
    if (this.estado !== EstadoPedido.BORRADOR)
      throw new DomainException("Solo pedidos en borrador aceptan nuevas líneas");
    if (precio.esNegativo() || precio.esCero())
      throw new DomainException("El precio debe ser positivo");
    this.lineas.push(new LineaDePedido(LineaId.nuevo(), producto, cantidad, precio));
  }
}
```

**Acá viven las reglas de negocio.** El dominio no sabe que fue invocado por HTTP, ni que los datos vienen de JSON, ni que se van a guardar en PostgreSQL. Solo aplica las reglas del negocio.

---

### Paso 4: El puerto secundario abstrae la persistencia

```java
// Puerto secundario (interfaz en application/port/output/)
public interface PedidoRepository {
    void guardar(Pedido pedido);
    Optional<Pedido> buscarPorId(PedidoId id);
}
```

```typescript
// TypeScript: Paso 4 — Puerto secundario (interfaz)
export interface PedidoRepository {
  guardar(pedido: Pedido): Promise<void>;
  buscarPorId(id: PedidoId): Promise<Pedido | null>;
}
```

El caso de uso invoca `pedidoRepository.guardar(pedido)`. No sabe si atrás hay PostgreSQL, MongoDB o un archivo de texto.

---

### Paso 5: El adaptador secundario traduce y persiste

```java
// Adaptador secundario (en adapter/out/persistence/)
@Repository
public class PostgresPedidoRepository implements PedidoRepository {

    @Override
    public void guardar(Pedido pedido) {
        PedidoJpaEntity entity = PedidoMapper.toJpaEntity(pedido);
        jpaRepository.save(entity);
    }
}
```

```typescript
// TypeScript: Paso 5 — Adaptador secundario con Prisma
@Injectable()
export class PrismaPedidoRepository implements PedidoRepository {
  constructor(private readonly prisma: PrismaService) {}

  async guardar(pedido: Pedido): Promise<void> {
    const entity = PedidoMapper.toPersistence(pedido);
    await this.prisma.pedido.create({ data: entity });
  }

  async buscarPorId(id: PedidoId): Promise<Pedido | null> {
    const row = await this.prisma.pedido.findUnique({ where: { id: id.value } });
    return row ? PedidoMapper.toDomain(row) : null;
  }
}
```

El adaptador traduce el Aggregate del dominio a una entidad JPA y la persiste. Si mañana cambia la BD, solo se cambia este adaptador.

---

## Flujos alternativos

### Consulta (Read)

```
GET /api/pedidos/123 ──► Controller ──► Puerto Primario ──► Puerto Secundario ──► BD
                         (traduce)      (caso de uso)       (repository)          (SELECT)
                              ◄────────────── ◄────────────────── ◄──────────────────◄
```

Las consultas suelen ser más simples. Pero si la consulta tiene lógica (ej: "pedidos de clientes VIP con descuento aplicado"), esa lógica va en el dominio o caso de uso.

### Evento entrante (mensaje de cola)

```
RabbitMQ ──► PedidoEventListener ──► Puerto Primario ──► Dominio ──► Puerto Secundario
  mensaje      (adaptador primario)   (CancelarPedido)    (validar)    (guardar)
```

El listener de mensajes es un **adaptador primario**. Recibe un estímulo externo (mensaje), lo traduce e invoca el puerto primario correspondiente.

### Tarea programada (scheduler)

```
Cron ──► PedidoScheduler ──► Puerto Primario ──► Dominio ──► Puerto Secundario
          (adaptador primario) (CancelarVencidos)  (validar)   (guardar)
```

```typescript
// TypeScript: Scheduler como adaptador primario
import { Cron, CronExpression } from "@nestjs/schedule";

@Injectable()
export class PedidoScheduler {
  constructor(private readonly cancelarVencidos: CancelarVencidosUseCase) {}

  @Cron(CronExpression.EVERY_HOUR)
  async cancelarPedidosVencidos(): Promise<void> {
    await this.cancelarVencidos.ejecutar();
  }
}
```

El scheduler también es un adaptador primario. Dispara la aplicación sin interacción humana.

---

## Lo que NUNCA debe pasar en el flujo

| ❌ Prohibido | Por qué |
|---|---|
| Controller → Repository directamente | Se saltea el dominio, no hay reglas de negocio |
| Controller con lógica de negocio | Si cambiás el canal de entrada (CLI, gRPC), perdés las reglas |
| Dominio conociendo HTTP/JSON | Rompe la [[Dependency Rule|regla de dependencia]] |
| Repository con lógica de negocio | El repository es solo infraestructura de persistencia |
| Caso de uso con SQL/HTTP | El caso de uso orquesta, no implementa infraestructura |

---

## Referencias

- [[Hexagonal Architecture]]
- [[Ports]]
- [[Adapters]]
- [[Dependency Rule]]
- [[Anti-Patterns]]
