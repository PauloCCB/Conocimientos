# Testing (Testeabilidad)

> **Definición:** La capacidad de probar la lógica de negocio de forma aislada, sin levantar infraestructura real (bases de datos, servidores HTTP, colas de mensajes), es la consecuencia práctica más valiosa de la Arquitectura Hexagonal.

Pertenece a: [[Hexagonal Architecture]] → Beneficios

---

## El problema sin hexagonal

```java
@SpringBootTest
@AutoConfigureTestDatabase(replace = Replace.NONE)
class PedidoServiceTest {

    @Autowired PedidoService pedidoService;
    @Autowired PedidoRepository pedidoRepository;

    @Test
    void debeCalcularTotal() {
        // Requiere PostgreSQL corriendo (Docker o instalado)
        // El test tarda 5-15 segundos en levantar el contexto de Spring
        // Si la BD tiene datos basura, el test falla aleatoriamente
        var datos = new DatosPedido(/* ... */);
        var pedido = pedidoService.crear(datos);
        assertEquals(new Dinero(330, "MXN"), pedido.getTotal());
    }
}
```

```typescript
// TypeScript: El problema — test con infraestructura real (Vitest)
describe("PedidoService (con BD real)", () => {
  // Requiere PostgreSQL corriendo en Docker
  // Tarda segundos en conectar y limpiar la BD
  // Si la BD tiene datos residuales, el test falla aleatoriamente
  it("debe calcular total con impuesto", async () => {
    const servicio = new PedidoService(prismaRepo);
    const pedido = await servicio.crear(datosCon3ItemsDe100());
    expect(pedido.getTotal()).toEqual(new Dinero(330, "MXN"));
  });
});
```

> Test lento, frágil, dependiente de infraestructura. **Esto no es un test unitario.**

---

## La solución: adaptadores secundarios fake

Gracias a que los [[Ports|puertos secundarios]] son interfaces, podés crear implementaciones en memoria para tests:

```java
public class InMemoryPedidoRepository implements PedidoRepository {
    private final Map<PedidoId, Pedido> store = new HashMap<>();

    @Override
    public void guardar(Pedido pedido) {
        store.put(pedido.getId(), pedido);
    }

    @Override
    public Optional<Pedido> buscarPorId(PedidoId id) {
        return Optional.ofNullable(store.get(id));
    }

    @Override
    public List<Pedido> buscarPorCliente(ClienteId clienteId) {
        return store.values().stream()
            .filter(p -> p.getClienteId().equals(clienteId))
            .toList();
    }
}
```

```typescript
// TypeScript: Adaptador fake en memoria
export class InMemoryPedidoRepository implements PedidoRepository {
  private store: Map<string, Pedido> = new Map();

  async guardar(pedido: Pedido): Promise<void> {
    this.store.set(pedido.id.value, pedido);
  }

  async buscarPorId(id: PedidoId): Promise<Pedido | null> {
    return this.store.get(id.value) ?? null;
  }

  async buscarPorCliente(clienteId: ClienteId): Promise<Pedido[]> {
    return Array.from(this.store.values())
      .filter(p => p.clienteId.equals(clienteId));
  }
}
```

### El test que querés escribir

```java
class CrearPedidoTest {

    private PedidoRepository repo;
    private CrearPedidoUseCase useCase;

    @BeforeEach
    void setUp() {
        repo = new InMemoryPedidoRepository();
        useCase = new CrearPedidoService(repo);
    }

    @Test
    void crearPedido_conTresItems_debeCalcularTotal() {
        var datos = DatosPedidoBuilder.nuevo()
            .paraCliente(clienteId)
            .conItem(productoA, cantidad2, precio100)
            .conItem(productoB, cantidad1, precio50)
            .conItem(productoC, cantidad4, precio25)
            .build();

        Pedido pedido = useCase.ejecutar(datos);

        assertEquals(new Dinero(350, "MXN"), pedido.calcularTotal());
    }

    @Test
    void crearPedido_sinItems_debeFallar() {
        var datos = DatosPedidoBuilder.nuevo()
            .paraCliente(clienteId)
            .sinItems()
            .build();

        assertThrows(DomainException.class, () -> useCase.ejecutar(datos));
    }

    @Test
    void confirmarPedido_sinStock_debeLanzarEventoPendiente() {
        var pedido = pedidoEnBorradorConLineas();
        repo.guardar(pedido);

        // Simulamos que no hay stock con otro fake
        var stockService = new InMemoryStockService().sinStockPara(productoA);
        var confirmarUseCase = new ConfirmarPedidoService(repo, stockService);

        confirmarUseCase.ejecutar(pedido.getId());

        Pedido actualizado = repo.buscarPorId(pedido.getId()).orElseThrow();
        assertEquals(EstadoPedido.PENDIENTE_STOCK, actualizado.getEstado());
    }
}
```

```typescript
// TypeScript: Tests unitarios reales con fakes (Vitest)
describe("CrearPedido", () => {
  let repo: PedidoRepository;
  let useCase: CrearPedidoUseCase;

  beforeEach(() => {
    repo = new InMemoryPedidoRepository();
    useCase = new CrearPedidoService(repo);
  });

  it("debe calcular total con tres items", () => {
    const datos = DatosPedidoBuilder.nuevo()
      .paraCliente(clienteId)
      .conItem(productoA, cantidad2, precio100)
      .conItem(productoB, cantidad1, precio50)
      .conItem(productoC, cantidad4, precio25)
      .build();

    const pedido = useCase.ejecutar(datos);

    expect(pedido.calcularTotal()).toEqual(new Dinero(350, "MXN"));
  });

  it("debe fallar si no tiene items", () => {
    const datos = DatosPedidoBuilder.nuevo()
      .paraCliente(clienteId)
      .sinItems()
      .build();

    expect(() => useCase.ejecutar(datos)).toThrow(DomainException);
  });

  it("debe lanzar evento pendiente si no hay stock", () => {
    const pedido = pedidoEnBorradorConLineas();
    repo.guardar(pedido);

    const stockService = new InMemoryStockService().sinStockPara(productoA);
    const confirmarUseCase = new ConfirmarPedidoService(repo, stockService);

    confirmarUseCase.ejecutar(pedido.id);

    const actualizado = repo.buscarPorId(pedido.id)!;
    expect(actualizado.estado).toBe(EstadoPedido.PENDIENTE_STOCK);
  });
});
```

---

## La pirámide de tests con hexagonal

```
         ╱───────╲
        ╱   E2E   ╲         Pocos tests. Lentos. Levantan toda la app.
       ╱───────────╲        Verifican integración real.
      ╱             ╲
     ╱  Integración  ╲      Pocos. Verifican que los adaptadores reales
    ╱─────────────────╲     funcionan (ej: SQL real, HTTP real).
   ╱                   ╲
  ╱    UNITARIOS        ╲   MUCHOS. Rápidos. Sin infraestructura.
 ╱   (dominio + casos   ╲   Con fakes en memoria.
╱    de uso)             ╲
───────────────────────────
```

| Tipo | Alcance | Infraestructura | Velocidad | Cantidad |
|---|---|---|---|
| **Unitarios** | Dominio + Casos de uso | Fakes en memoria | ms | La mayoría |
| **Integración** | Adaptadores reales | BD real, HTTP real | segundos | Pocos (1 por adaptador) |
| **E2E** | Sistema completo | Todo real | segundos/min | Muy pocos (happy paths) |

> Hexagonal te permite mover la mayoría de los tests a la base de la pirámide (unitarios con fakes), donde son más rápidos y confiables.

---

## Qué testear sin infraestructura

| Componente | Con qué | Verifica |
|---|---|---|
| **[[Value Objects]]** | Test unitario plano | Validaciones, inmutabilidad, igualdad por valor |
| **[[Entities]]** | Test unitario plano | Invariantes, transiciones de estado, lógica de negocio |
| **[[Aggregates]]** | Test unitario plano | Consistencia del cluster, reglas de negocio cross-entidad |
| **Domain Services** | Test con fakes de puertos secundarios | Lógica que involucra múltiples aggregates |
| **Casos de uso** | Test con fakes de puertos secundarios | Orquestación correcta, manejo de errores, eventos |
| **[[Domain Events]]** | Test con handler fake | Que los eventos se publiquen y contengan los datos correctos |

## Qué testear con infraestructura real

| Componente | Con qué | Verifica |
|---|---|---|
| **Adaptador BD** | Testcontainers / BD real | Que el SQL es correcto, mapeo de filas funciona |
| **Adaptador REST** | MockMvc / TestRestTemplate | Serialización JSON, códigos HTTP, validación de requests |
| **Adaptador de mensajería** | RabbitMQ/Kafka embebido | Serialización, reintentos, dead letter |
| **Flujo E2E** | Testcontainers + app real | Que todos los adaptadores reales funcionan juntos |

---

## Fakes vs Mocks

En hexagonal, preferí **fakes** sobre **mocks** para puertos secundarios:

```java
// FAKE: implementación real de la interfaz, simple pero funcional
class InMemoryPedidoRepository implements PedidoRepository {
    private Map<PedidoId, Pedido> store = new HashMap<>();
    public void guardar(Pedido p) { store.put(p.getId(), p); }
    public Optional<Pedido> buscarPorId(PedidoId id) { return Optional.ofNullable(store.get(id)); }
}

// MOCK: no implementa, solo verifica interacciones (más frágil)
PedidoRepository repo = mock(PedidoRepository.class);
when(repo.buscarPorId(any())).thenReturn(Optional.of(pedido));
verify(repo).guardar(any());  // El test sabe DEMASIADO sobre la implementación
```

```typescript
// TypeScript: FAKE — implementación real, reutilizable
export class InMemoryPedidoRepository implements PedidoRepository {
  private store = new Map<string, Pedido>();
  async guardar(p: Pedido): Promise<void> { this.store.set(p.id.value, p); }
  async buscarPorId(id: PedidoId): Promise<Pedido | null> { return this.store.get(id.value) ?? null; }
}

// TypeScript: MOCK con Vitest — frágil, acoplado a implementación
const repo = {
  guardar: vi.fn(),
  buscarPorId: vi.fn().mockResolvedValue(pedido),
};
expect(repo.guardar).toHaveBeenCalled();  // El test sabe demasiado
```

| | Fake | Mock |
|---|---|---|
| Reutilizable | Sí (una sola clase para todos los tests) | No (se configura por test) |
| Acoplado a implementación | No | Sí (verify, when) |
| Refactor seguro | Sí (cambiás el código, el fake sigue funcionando) | No (si cambiás interacciones, el mock falla) |
| Simula comportamiento | Sí (podés hacer `sinStockPara(producto)`) | Sí (pero con stubbing manual) |

---

## Referencias

- [[Hexagonal Architecture]]
- [[Ports]]
- [[Adapters]]
- [[DDD - Domain Driven Design]] → [[Aggregates]], [[Repositories]]
- [[Clean Architecture]] — mismo principio: "The outer circles are for testing"
