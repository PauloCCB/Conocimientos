# Interface Adapters (Adaptadores de Interfaz)

> **Definición:** El tercer círculo de Clean Architecture. Convierte datos entre el formato más conveniente para los casos de uso/entities y el formato más conveniente para los frameworks externos (web, BD).

Pertenece a: [[Clean Architecture]] → Tercer círculo

---

## La idea central

Esta capa contiene:
- **Controllers** — reciben input HTTP/CLI y lo pasan al Input Port
- **Presenters** — convierten la respuesta del Output Port en ViewModels
- **Gateways** — implementan los puertos de salida definidos por los casos de uso
- **ViewModels** — estructuras de datos optimizadas para la vista (no para el dominio)

```
       Web / CLI        ←──→     Interface Adapters     ←──→    Use Cases
   ┌──────────────┐          ┌──────────────────────┐          ┌───────────┐
   │ HTTP Request  │──►       │ Controller           │──►       │           │
   │              │          │   (traduce)           │          │  Input    │
   │ HTTP Response │◄──      │ Presenter             │◄──       │  Port     │
   │              │          │   (formatea)          │          │           │
   └──────────────┘          └──────────────────────┘          └───────────┘
```

---

## El Controller

Traduce el formato externo (HTTP, CLI, mensaje) al Request Model del caso de uso e invoca el Input Port.

```java
// Java: Controller — adaptador de interfaz
@RestController
public class PedidoController {
    private final CrearPedidoInputPort crearPedidoInputPort;

    @PostMapping("/pedidos")
    public ResponseEntity<CrearPedidoWebResponse> crear(@RequestBody CrearPedidoWebRequest webReq) {
        // 1. Traduce web → request model del caso de uso
        CrearPedidoRequest request = new CrearPedidoRequest(
            new ClienteId(webReq.clienteId()),
            webReq.items().stream()
                .map(i -> new ItemRequest(new ProductoId(i.productoId()), i.cantidad(), i.precio()))
                .toList()
        );

        // 2. Invoca el Input Port
        crearPedidoInputPort.ejecutar(request);

        // 3. Devuelve respuesta HTTP
        return ResponseEntity.accepted().build();
    }
}
```

```typescript
// TypeScript: Controller con NestJS
@Controller("/pedidos")
export class PedidoController {
  constructor(private readonly crearPedido: CrearPedidoInputPort) {}

  @Post()
  async crear(@Body() webReq: CrearPedidoWebRequest): Promise<void> {
    const request: CrearPedidoRequest = {
      clienteId: new ClienteId(webReq.clienteId),
      items: webReq.items.map(i => ({
        productoId: new ProductoId(i.productoId),
        cantidad: i.cantidad,
        precio: i.precio,
      })),
    };
    await this.crearPedido.ejecutar(request);
  }
}
```

---

## El Presenter

El Presenter **implementa el Output Port** que definió el caso de uso. Convierte el Response Model en un ViewModel.

```java
// Java: Presenter — implementa el Output Port del caso de uso
@Component
public class PedidoPresenter implements PedidoOutputPort {
    private PedidoViewModel viewModel;

    @Override
    public void presentar(CrearPedidoResponse response) {
        this.viewModel = new PedidoViewModel(
            response.pedidoId().valor(),
            response.estado(),
            response.total(),
            "Pedido creado exitosamente"
        );
    }

    public PedidoViewModel getViewModel() { return viewModel; }
}
```

```typescript
// TypeScript: Presenter
@Injectable()
export class PedidoPresenter implements PedidoOutputPort {
  private viewModel: PedidoViewModel | null = null;

  presentar(response: CrearPedidoResponse): void {
    this.viewModel = {
      pedidoId: response.pedidoId.value,
      estado: response.estado,
      total: response.total,
      mensaje: "Pedido creado exitosamente",
    };
  }

  getViewModel(): PedidoViewModel | null { return this.viewModel; }
}
```

---

## El Gateway

Implementa las interfaces de repositorio/gateway definidas por los casos de uso usando tecnología concreta.

```java
// Java: Gateway — adaptador a BD
@Repository
public class JpaPedidoGateway implements PedidoRepository {
    private final SpringDataPedidoRepo jpaRepo;

    @Override
    public void guardar(Pedido pedido) {
        PedidoJpaEntity entity = PedidoMapper.toJpa(pedido);
        jpaRepo.save(entity);
    }
}
```

```typescript
// TypeScript: Gateway con Prisma
@Injectable()
export class PrismaPedidoGateway implements PedidoRepository {
  constructor(private readonly prisma: PrismaService) {}

  async guardar(pedido: Pedido): Promise<void> {
    await this.prisma.pedido.create({
      data: {
        id: pedido.id.value,
        clienteId: pedido.clienteId.value,
        estado: pedido.estado,
        total: pedido.calcularTotal().cantidad,
      },
    });
  }
}
```

---

## Referencias
- [[Clean Architecture]]
- [[Use Cases]]
- [[Frameworks & Drivers]]
- [[Hexagonal Architecture]] → [[Adapters]]
