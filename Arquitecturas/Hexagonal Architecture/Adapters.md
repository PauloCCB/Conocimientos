# Adapters (Adaptadores)

> **Definición:** Implementación concreta de un [[Ports|Puerto]], acoplada a una tecnología específica. El adaptador traduce entre el lenguaje del mundo exterior (JSON, SQL, HTTP) y el lenguaje del dominio.

Pertenece a: [[Hexagonal Architecture]] → Puertos y Adaptadores

---

## La idea central

El adaptador responde a la pregunta: **"¿con qué tecnología concreta hablo?"**. Mientras el puerto dice QUÉ, el adaptador dice CÓMO.

```
Puerto:        "Necesito guardar un Pedido"        (QUÉ)
Adaptador:     "Lo guardo con INSERT INTO pedidos"  (CÓMO, con SQL)
Adaptador:     "Lo guardo con db.pedidos.insert()"  (CÓMO, con MongoDB)
```

> **Un puerto, muchos adaptadores.** Esa es la potencia: intercambiabilidad sin tocar el dominio.

---

## Los dos tipos de adaptadores

### Adaptadores Primarios — "Los que disparan la aplicación"

Reciben estímulos del mundo exterior, los traducen e invocan un [[Ports#Puertos Primarios (Driving Ports) — "Los que me hablan"|puerto primario]].

```java
@RestController
@RequestMapping("/api/pedidos")
public class PedidoController {
    private final CrearPedidoUseCase crearPedido;
    private final ConsultarPedidoUseCase consultarPedido;

    @PostMapping
    public ResponseEntity<PedidoResponse> crear(@RequestBody @Valid CrearPedidoRequest request) {
        DatosPedido datos = request.toDomain();           // DTO → objeto de dominio
        Pedido pedido = crearPedido.ejecutar(datos);       // invoca puerto primario
        return ResponseEntity
            .created(URI.create("/api/pedidos/" + pedido.getId()))
            .body(PedidoResponse.from(pedido));            // dominio → DTO respuesta
    }
}
```

```typescript
// TypeScript: Adaptador primario con Express
@Controller("/api/pedidos")
export class PedidoController {
  constructor(
    private readonly crearPedido: CrearPedidoUseCase,
    private readonly consultarPedido: ConsultarPedidoUseCase,
  ) {}

  @Post()
  async crear(@Body() request: CrearPedidoRequest): Promise&lt;PedidoResponse&gt; {
    const datos: DatosPedido = request.toDomain();           // DTO → objeto de aplicación
    const pedido = this.crearPedido.ejecutar(datos);         // invoca puerto primario
    return PedidoResponse.from(pedido);                      // dominio → DTO respuesta
  }
}
```

Ejemplos de adaptadores primarios:

| Adaptador | Tecnología | Dispara la app desde... |
|---|---|---|
| `PedidoController` | Spring MVC / REST | HTTP |
| `PedidoGrpcService` | gRPC | Llamada RPC |
| `PedidoCommand` | CLI | Terminal |
| `PedidoMessageListener` | RabbitMQ / Kafka | Mensaje en cola |
| `PedidoScheduler` | Cron / Quartz | Tarea programada |
| `PedidoTest` | JUnit | Test automatizado |

> 🧪 El test es un adaptador primario legítimo. Invoca el mismo puerto que el controller REST, pero sin HTTP.

### Adaptadores Secundarios — "Los que cumplen lo que la aplicación pide"

Implementan un [[Ports#Puertos Secundarios (Driven Ports) — "A quienes hablo"|puerto secundario]] usando una tecnología concreta.

```java
@Repository
public class PostgresPedidoRepository implements PedidoRepository {

    private final JdbcTemplate jdbc;
    private final PedidoMapper mapper;

    @Override
    public void guardar(Pedido pedido) {
        // Traduce Aggregate → filas SQL
        PedidoRow row = mapper.toRow(pedido);
        jdbc.update(
            "INSERT INTO pedidos (id, cliente_id, estado, total) VALUES (?, ?, ?, ?)",
            row.id(), row.clienteId(), row.estado(), row.total()
        );
        for (LineaRow linea : row.lineas()) {
            jdbc.update(
                "INSERT INTO lineas_pedido (id, pedido_id, producto_id, cantidad, precio) VALUES (?, ?, ?, ?, ?)",
                linea.id(), row.id(), linea.productoId(), linea.cantidad(), linea.precio()
            );
        }
    }

    @Override
    public Optional<Pedido> buscarPorId(PedidoId id) {
        // Traduce filas SQL → Aggregate
        var pedidoRow = jdbc.queryForMap("SELECT * FROM pedidos WHERE id = ?", id.valor());
        if (pedidoRow.isEmpty()) return Optional.empty();
        var lineasRows = jdbc.queryForList("SELECT * FROM lineas_pedido WHERE pedido_id = ?", id.valor());
        return Optional.of(mapper.toDomain(pedidoRow, lineasRows));
    }
}
```

```typescript
// TypeScript: Adaptador secundario con Prisma (PostgreSQL)
@Injectable()
export class PrismaPedidoRepository implements PedidoRepository {
  constructor(
    private readonly prisma: PrismaService,
    private readonly mapper: PedidoMapper,
  ) {}

  async guardar(pedido: Pedido): Promise&lt;void&gt; {
    const row = this.mapper.toPersistence(pedido);
    await this.prisma.pedido.create({ data: row });
  }

  async buscarPorId(id: PedidoId): Promise&lt;Pedido | null&gt; {
    const row = await this.prisma.pedido.findUnique({
      where: { id: id.value },
      include: { lineas: true },
    });
    return row ? this.mapper.toDomain(row) : null;
  }
}
```

Ejemplos de adaptadores secundarios:

| Adaptador | Implementa puerto | Tecnología |
|---|---|---|
| `PostgresPedidoRepository` | `PedidoRepository` | PostgreSQL |
| `MongoPedidoRepository` | `PedidoRepository` | MongoDB |
| `InMemoryPedidoRepository` | `PedidoRepository` | HashMap (tests) |
| `SmtpNotificacionService` | `NotificacionService` | SMTP / JavaMail |
| `StripePagoGateway` | `PagoGateway` | API REST de Stripe |
| `S3FileStorage` | `FileStorage` | AWS S3 |
| `RabbitEventPublisher` | `EventPublisher` | RabbitMQ |

---

## Traducción: la responsabilidad clave del adaptador

Todo adaptador hace **traducción bidireccional**:

```
Adaptador Primario:
   Request HTTP → DTO → DatosPedido (dominio) ──► Puerto Primario
                                                        │
                                             ┌──────────┘
                                             ▼
   Response HTTP ◄── DTO ◄── Pedido (dominio) ◄── Resultado del caso de uso

Adaptador Secundario:
   Pedido (dominio) ──► Row SQL ──► INSERT ──► BD
   Pedido (dominio) ◄── Row SQL ◄── SELECT ◄── BD
```

Esta traducción aísla al dominio de formatos, protocolos y esquemas externos. Si la API externa cambia su JSON, solo se modifica el adaptador.

---

## El Mapper: pieza fundamental

El mapper es el objeto que traduce entre modelo de dominio y modelo de infraestructura:

```java
@Component
public class PedidoMapper {

    public Pedido toDomain(Map<String, Object> pedidoRow, List<Map<String, Object>> lineasRows) {
        PedidoId id = new PedidoId((UUID) pedidoRow.get("id"));
        ClienteId clienteId = new ClienteId((UUID) pedidoRow.get("cliente_id"));
        EstadoPedido estado = EstadoPedido.valueOf((String) pedidoRow.get("estado"));

        Pedido pedido = Pedido.reconstruir(id, clienteId, estado);
        for (var lineaRow : lineasRows) {
            pedido.agregarLineaReconstruida(
                new LineaId((UUID) lineaRow.get("id")),
                new ProductoId((UUID) lineaRow.get("producto_id")),
                new Cantidad((int) lineaRow.get("cantidad")),
                new Dinero((BigDecimal) lineaRow.get("precio"), "MXN")
            );
        }
        return pedido;
    }

    public PedidoRow toRow(Pedido pedido) {
        // Traduce Aggregate → estructura plana para SQL
        return new PedidoRow(
            pedido.getId().valor(),
            pedido.getClienteId().valor(),
            pedido.getEstado().name(),
            pedido.calcularTotal().cantidad(),
            pedido.getLineas().stream()
                .map(l -> new LineaRow(l.getId().valor(), l.getProductoId().valor(), l.getCantidad().valor(), l.getPrecioUnitario().cantidad()))
                .toList()
        );
    }
}
```

```typescript
// TypeScript: Mapper — traduce entre dominio y Prisma
@Injectable()
export class PedidoMapper {
  toDomain(row: { id: string; clienteId: string; estado: string; lineas: Array&lt;{ id: string; productoId: string; cantidad: number; precio: string }&gt; }): Pedido {
    const pedido = Pedido.reconstruir(
      new PedidoId(row.id),
      new ClienteId(row.clienteId),
      row.estado as EstadoPedido,
    );
    for (const linea of row.lineas) {
      pedido.agregarLineaReconstruida(
        new LineaId(linea.id),
        new ProductoId(linea.productoId),
        new Cantidad(linea.cantidad),
        new Dinero(Number(linea.precio), "MXN"),
      );
    }
    return pedido;
  }

  toPersistence(pedido: Pedido): PrismaPedidoInput {
    return {
      id: pedido.id.value,
      clienteId: pedido.clienteId.value,
      estado: pedido.estado,
      total: pedido.calcularTotal().cantidad,
      lineas: pedido.lineas.map(l => ({
        id: l.id.value,
        productoId: l.productoId.value,
        cantidad: l.cantidad.value,
        precio: l.precioUnitario.cantidad,
      })),
    };
  }
}
```

---

## Múltiples adaptadores para el mismo puerto: el caso de uso real

```java
@Configuration
public class PersistenceConfig {

    @Bean
    @Profile("postgres")
    public PedidoRepository postgresRepo(DataSource ds, PedidoMapper mapper) {
        return new PostgresPedidoRepository(ds, mapper);
    }

    @Bean
    @Profile("mongo")
    public PedidoRepository mongoRepo(MongoTemplate mongo, PedidoMapper mapper) {
        return new MongoPedidoRepository(mongo, mapper);
    }

    @Bean
    @Profile("test")
    public PedidoRepository inMemoryRepo() {
        return new InMemoryPedidoRepository();
    }
}
```

Con Spring Profiles, cambiás de base de datos con una variable de entorno. El dominio no se modifica. Eso es arquitectura que cumple.

```typescript
// TypeScript: DI con tokens — cambiás de BD sin tocar el dominio
export const PEDIDO_REPOSITORY = Symbol("PedidoRepository");

// in-memory (tests)
@Module({
  providers: [{ provide: PEDIDO_REPOSITORY, useClass: InMemoryPedidoRepository }],
})
export class TestModule {}

// PostgreSQL (producción)
@Module({
  providers: [{ provide: PEDIDO_REPOSITORY, useClass: PrismaPedidoRepository }],
})
export class PostgresModule {}
```

---

## Referencias

- [[Hexagonal Architecture]]
- [[Ports]]
- [[Dependency Rule]]
- [[Testing]]
- [[DDD - Domain Driven Design]] → [[Repositories]]
