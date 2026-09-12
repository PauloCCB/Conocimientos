# Use Cases (Casos de Uso)

> **Definición:** El segundo círculo de Clean Architecture. Contiene las **Application Business Rules**: la lógica específica de esta aplicación que orquesta las Entities para cumplir un caso de uso concreto.

Pertenece a: [[Clean Architecture]] → Segundo círculo

---

## La idea central

Un caso de uso:
- **Orquesta** el flujo de datos entre Entities y el mundo exterior
- **No contiene** reglas de negocio (eso va en Entities)
- **Define** DTOs de entrada y salida (Request/Response Models)
- **Declara** puertos de salida (interfaces que implementará la infraestructura)

```java
// Java: Caso de uso — Application Business Rule
public class CrearPedidoUseCase implements CrearPedidoInputPort {

    private final PedidoRepository pedidoRepository;    // puerto de salida
    private final ClienteGateway clienteGateway;         // puerto de salida
    private final PedidoOutputPort outputPort;           // puerto de salida (presenter)

    // Request Model (DTO de entrada)
    public record CrearPedidoRequest(ClienteId clienteId, List<ItemRequest> items) {}
    public record ItemRequest(ProductoId productoId, int cantidad, BigDecimal precio) {}

    // Response Model (DTO de salida)
    public record CrearPedidoResponse(PedidoId pedidoId, String estado, BigDecimal total) {}

    public void ejecutar(CrearPedidoRequest request) {
        // 1. Validar precondiciones usando puertos de salida
        Cliente cliente = clienteGateway.buscarPorId(request.clienteId());
        if (cliente == null) throw new ClienteNoEncontrado(request.clienteId());

        // 2. Delegar al dominio (Entities)
        Pedido pedido = Pedido.crear(cliente.getId());
        for (ItemRequest item : request.items()) {
            pedido.agregarLinea(item.productoId(), item.cantidad(), item.precio());
        }

        // 3. Persistir via puerto de salida
        pedidoRepository.guardar(pedido);

        // 4. Preparar respuesta via output port
        CrearPedidoResponse response = new CrearPedidoResponse(
            pedido.getId(), pedido.getEstado().name(), pedido.calcularTotal().cantidad()
        );
        outputPort.presentar(response);
    }
}
```

```typescript
// TypeScript: Caso de uso
@Injectable()
export class CrearPedidoUseCase implements CrearPedidoInputPort {
  constructor(
    private readonly pedidoRepository: PedidoRepository,
    private readonly clienteGateway: ClienteGateway,
    private readonly outputPort: PedidoOutputPort,
  ) {}

  async ejecutar(request: CrearPedidoRequest): Promise<void> {
    // 1. Validar precondiciones
    const cliente = await this.clienteGateway.buscarPorId(request.clienteId);
    if (!cliente) throw new ClienteNoEncontrado(request.clienteId);

    // 2. Delegar al dominio
    const pedido = Pedido.crear(cliente.id);
    for (const item of request.items) {
      pedido.agregarLinea(item.productoId, item.cantidad, item.precio);
    }

    // 3. Persistir
    await this.pedidoRepository.guardar(pedido);

    // 4. Presentar respuesta
    this.outputPort.presentar({
      pedidoId: pedido.id,
      estado: pedido.estado,
      total: pedido.calcularTotal().cantidad,
    });
  }
}
```

---

## El Input Port y el Output Port

En Clean Architecture, un caso de uso define **dos puertos**:

### Input Port (Puerto de entrada)

La interfaz que el controlador (círculo externo) invoca.

```java
public interface CrearPedidoInputPort {
    void ejecutar(CrearPedidoRequest request);
}
```

```typescript
export interface CrearPedidoInputPort {
  ejecutar(request: CrearPedidoRequest): Promise<void>;
}
```

### Output Port (Puerto de salida)

La interfaz que el caso de uso necesita para comunicarse con el exterior. El caso de uso la define; el círculo externo la implementa.

```java
public interface PedidoRepository {
    void guardar(Pedido pedido);
    Pedido buscarPorId(PedidoId id);
}
```

```typescript
export interface PedidoRepository {
  guardar(pedido: Pedido): Promise<void>;
  buscarPorId(id: PedidoId): Promise<Pedido | null>;
}
```

---

## DTOs de entrada y salida

Clean Architecture insiste en **modelos específicos para cada frontera**:

```
Controller → RequestDTO → InputPort → UseCase → OutputPort → ResponseDTO → Presenter
```

No pasés la Request HTTP directo al caso de uso. No devuelvas la Entity directo al Controller. Cada frontera tiene su propio modelo.

---

## Referencias
- [[Clean Architecture]]
- [[Entities]]
- [[Interface Adapters]]
- [[Crossing Boundaries]]
- [[Hexagonal Architecture]] → [[Ports]]
