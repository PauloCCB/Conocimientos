# Crossing Boundaries (Cruce de Fronteras)

> **Definición:** Técnica para cruzar los límites entre círculos de Clean Architecture sin violar la [[Dependency Rule|Regla de Dependencia]], usando el **Principio de Inversión de Dependencias (DIP)**.

Pertenece a: [[Clean Architecture]] → Mecanismo

---

## El problema

Cuando un caso de uso necesita guardar datos, el flujo de control va del Use Case hacia la BD:

```
Use Case ──► Repository ──► Base de Datos
(inner)      (outer)        (outermost)
```

Si el Use Case llama directamente al Repository concreto, **rompe la regla de dependencia**: el círculo interior dependería del exterior.

---

## La solución: DIP

El caso de uso (círculo interior) **define una interfaz**. El Gateway (círculo exterior) **la implementa**.

```
                    ┌── Dependencia ──┐
                    ▼                 │
   Use Case ──► IPedidoRepository ◄──┼── JpaPedidoGateway
   (inner)      (interfaz en inner)   │   (outer — implementa)
                    │                 │
                    └── Flujo de ejecución ──►
```

```java
// --- Círculo interior: Use Cases ---
// Define la interfaz (es dueño de la abstracción)
public interface PedidoRepository {
    void guardar(Pedido pedido);
    Pedido buscarPorId(PedidoId id);
}

public class CrearPedidoUseCase {
    private final PedidoRepository repo;  // depende de la abstracción, no de la implementación

    public void ejecutar(CrearPedidoRequest req) {
        Pedido pedido = Pedido.crear(req.clienteId());
        repo.guardar(pedido);  // en runtime se ejecuta la implementación concreta
    }
}

// --- Círculo exterior: Interface Adapters ---
// Implementa la interfaz definida adentro
@Repository
public class JpaPedidoRepository implements PedidoRepository {
    @Override
    public void guardar(Pedido pedido) { /* INSERT SQL */ }
}
```

```typescript
// --- Círculo interior: Use Cases ---
export interface PedidoRepository {
  guardar(pedido: Pedido): Promise<void>;
  buscarPorId(id: PedidoId): Promise<Pedido | null>;
}

@Injectable()
export class CrearPedidoUseCase {
  constructor(private readonly repo: PedidoRepository) {}

  async ejecutar(req: CrearPedidoRequest): Promise<void> {
    const pedido = Pedido.crear(req.clienteId);
    await this.repo.guardar(pedido);  // abstracción, no implementación concreta
  }
}

// --- Círculo exterior: Interface Adapters ---
@Injectable()
export class PrismaPedidoRepository implements PedidoRepository {
  constructor(private readonly prisma: PrismaService) {}

  async guardar(pedido: Pedido): Promise<void> {
    await this.prisma.pedido.create({ data: PedidoMapper.toPersistence(pedido) });
  }
}
```

---

## Cruce de frontera con datos

Cada cruce de frontera debe usar **estructuras de datos simples y aisladas**. No pasés entidades de BD a través de las fronteras. No pasés respuestas HTTP hacia adentro.

```
Círculo externo          Frontera            Círculo interno
─────────────────────────────────────────────────────────────
HTTP Request ──► Request DTO ──► Input Port ──► Use Case
                                        (modelo simple,
                                         sin anotaciones)
```

---

## Referencias
- [[Clean Architecture]]
- [[Dependency Rule]]
- [[Use Cases]]
- [[Hexagonal Architecture]] → [[Ports]]
