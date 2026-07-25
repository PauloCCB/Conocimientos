# Domain Events (Eventos de Dominio)

> **Definición:** Hechos significativos que ocurrieron en el dominio, expresados en tiempo pasado. Notifican que algo importante sucedió dentro de un [[Aggregates|Aggregate]].

Pertenece a: [[DDD - Domain Driven Design]] → Tactical Design

---

## La idea central

El dominio no "llama" a otras partes del sistema directamente. En cambio, **anuncia lo que pasó** y deja que otros reaccionen.

```
Sin eventos (acoplamiento directo):
Pedido.confirmar()
  → llama EmailService.enviarConfirmacion()
  → llama InventarioService.reservarStock()
  → llama FacturacionService.crearFactura()
  → llama NotificacionService.notificarVendedor()
  ↑ Pedido conoce y depende de todos estos servicios

Con eventos (desacoplado):
Pedido.confirmar()
  → publica PedidoConfirmado
    ← EmailService escucha y envía email
    ← InventarioService escucha y reserva stock
    ← FacturacionService escucha y crea factura
    ← NotificacionService escucha y notifica
  ↑ Pedido no sabe nada de esos servicios
```

## Características de un Domain Event

| Característica | Descripción |
|---------------|-------------|
| **Pasado** | Nombre siempre en pasado: "algo **ocurrió**" |
| **Inmutable** | Lo que pasó no se cambia |
| **Contiene contexto** | Incluye los datos necesarios para que listeners reaccionen |
| **Del dominio** | Expresa un concepto del negocio, no técnico |

## Estructura

```java
// Mínimo: qué pasó, cuándo, con qué datos
class PedidoConfirmado {
    private final PedidoId pedidoId;
    private final ClienteId clienteId;
    private final Dinero total;
    private final Instant ocurridoEn;  // timestamp de cuando ocurrió en el dominio
    private final UUID eventId;         // para deduplicación

    public PedidoConfirmado(PedidoId pedidoId, ClienteId clienteId, Dinero total) {
        this.pedidoId = pedidoId;
        this.clienteId = clienteId;
        this.total = total;
        this.ocurridoEn = Instant.now();
        this.eventId = UUID.randomUUID();
    }
}
```

## Nombres de eventos (convención)

**Formato:** `[Aggregate][AcciónEnPasado]`

| ✅ Correcto | ❌ Incorrecto |
|------------|--------------|
| `PedidoConfirmado` | `PedidoConfirmation` |
| `UsuarioRegistrado` | `CreateUser` |
| `PagoFallido` | `PaymentError` |
| `ProductoAgotado` | `StockEmpty` |
| `SuscripcionCancelada` | `CancelSubscription` |

## Dónde se generan

Dentro del [[Aggregates|Aggregate]], como resultado de un cambio de estado:

```java
class Pedido {
    private final List<DomainEvent> eventos = new ArrayList<>();

    public void confirmar() {
        if (lineas.isEmpty()) throw new DomainException("Pedido vacío");
        this.estado = EstadoPedido.CONFIRMADO;
        
        // El evento nace aquí, dentro del Aggregate
        this.eventos.add(new PedidoConfirmado(this.id, this.clienteId, calcularTotal()));
    }

    public List<DomainEvent> pullEventos() {
        List<DomainEvent> copia = new ArrayList<>(this.eventos);
        this.eventos.clear();
        return copia;
    }
}
```

## Dónde se publican

El [[Repositories|Repository]] (o el Application Layer) publica los eventos después de persistir:

```java
class ConfirmarPedidoUseCase {
    public void ejecutar(PedidoId pedidoId) {
        Pedido pedido = pedidoRepository.findById(pedidoId);
        
        pedido.confirmar();                          // genera evento internamente
        pedidoRepository.save(pedido);               // persiste estado
        
        List<DomainEvent> eventos = pedido.pullEventos();
        eventBus.publish(eventos);                   // publica eventos
    }
}
```

## Dos tipos de Domain Events

### 1. Intra-contexto (mismo Bounded Context)

Comunicación entre partes del mismo contexto, síncrona o asíncrona:

```
[Pedido] ──PedidoConfirmado──► [NotificadorInterno]
                              ► [ActualizadorEstadísticas]
```

### 2. Inter-contexto (entre Bounded Contexts)

El [[Context Map|Context Map]] usa eventos para integrar contextos sin acoplamiento directo:

```
[Ventas: PedidoConfirmado] ──► Message Bus (Kafka/RabbitMQ) ──► [Inventario]
                                                              ──► [Facturación]
                                                              ──► [Logística]
```

Aquí los eventos suelen serializarse a JSON/Protobuf como parte de un [[Context Map#Published Language|Published Language]].

## Domain Events vs Integration Events

| | Domain Event | Integration Event |
|--|--|--|
| Alcance | Dentro del dominio/BC | Entre sistemas/contextos |
| Formato | Objetos Java/C#/etc | JSON, Protobuf, Avro |
| Transporte | En memoria o bus interno | Message broker (Kafka, RabbitMQ) |
| Acoplamiento | Al modelo de dominio | A un esquema publicado |

Común: un Domain Event dispara la creación de un Integration Event para publicar al exterior.

## Event Sourcing (avanzado)

En lugar de guardar el **estado actual** del Aggregate, se guardan **todos los eventos** que lo llevaron a ese estado.

```
Estado actual de Pedido = aplicar(PedidoCreado, LineaAgregada, LineaAgregada, PedidoConfirmado)
```

- Historial completo de todo lo que pasó
- Se puede reconstruir el estado en cualquier punto del tiempo
- Complejidad alta — no usar sin razón clara
- Se complementa con CQRS

---

## Referencias
- [[DDD - Domain Driven Design]]
- [[Aggregates]]
- [[Context Map]]
- [[Event Storming]]
