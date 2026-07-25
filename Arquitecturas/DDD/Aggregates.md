# Aggregates (Agregados)

> **Definición:** Cluster de [[Entities]] y [[Value Objects]] que se tratan como una unidad de consistencia. Tienen una **Aggregate Root** que es el único punto de acceso externo.

Pertenece a: [[DDD - Domain Driven Design]] → Tactical Design

---

## El problema que resuelve

Sin agregados, cualquier parte del sistema puede modificar cualquier objeto directamente, rompiendo las reglas del negocio:

```java
// Sin Aggregate — cualquiera puede modificar líneas de pedido
LineaDePedido linea = lineaRepo.findById(lineaId);
linea.setCantidad(0);          // nadie valida si el pedido queda vacío
linea.setPrecio(new Dinero(-5, USD));  // precio negativo — nadie lo impide
```

Con Aggregate, solo la raíz puede cambiar el estado interno:

```java
// Con Aggregate — Pedido es la raíz, controla todo
Pedido pedido = pedidoRepo.findById(pedidoId);
pedido.modificarLinea(lineaId, nuevaCantidad);  // Pedido valida la invariante
// Pedido garantiza: "si queda sin líneas, se cancela"
```

## Estructura

```
Pedido  ← Aggregate Root (Entity)
│  ├── pedidoId: PedidoId           ← Value Object (ID)
│  ├── cliente: ClienteId           ← Value Object (referencia a otro Aggregate)
│  ├── estado: EstadoPedido         ← Value Object (enum enriquecido)
│  └── lineas: List<LineaDePedido>  ← Entities internas
│       ├── lineaId: LineaId        ← Value Object
│       ├── producto: ProductoId    ← Value Object (referencia, no el objeto completo)
│       ├── cantidad: Cantidad      ← Value Object
│       └── precioUnitario: Dinero  ← Value Object
```

## Las 4 reglas de los Aggregates

### 1. Proteger invariantes dentro del boundary

El Aggregate garantiza que sus reglas de negocio nunca se violen:

```java
class Pedido {
    private List<LineaDePedido> lineas;
    private EstadoPedido estado;

    public void agregarLinea(ProductoId producto, Cantidad cantidad, Dinero precio) {
        if (estado != EstadoPedido.BORRADOR)
            throw new DomainException("Solo se pueden agregar líneas a pedidos en borrador");
        
        lineas.add(new LineaDePedido(new LineaId(), producto, cantidad, precio));
        // invariante: el pedido siempre tiene al menos una línea
    }

    public void confirmar() {
        if (lineas.isEmpty())
            throw new DomainException("No se puede confirmar un pedido vacío");
        this.estado = EstadoPedido.CONFIRMADO;
    }
}
```

### 2. Solo la raíz tiene repositorio

```java
// ✅ Correcto
PedidoRepository.save(pedido)
PedidoRepository.findById(pedidoId)

// ❌ Nunca esto
LineaDePedidoRepository.save(linea)  // LineaDePedido no existe fuera de Pedido
```

### 3. Referencias externas solo por ID (no objetos)

```java
class Pedido {
    private ClienteId clienteId;      // ✅ referencia por ID
    // private Cliente cliente;       // ❌ no incluir el Aggregate completo
}
```

Razones:
- Evita cargar objetos innecesarios
- Elimina dependencias circulares entre agregados
- Cada Aggregate gestiona su propia consistencia

### 4. Consistencia eventual entre Aggregates

Dentro del Aggregate: consistencia transaccional (una transacción).
Entre Aggregates: consistencia eventual (via [[Domain Events]]).

```java
// Al confirmar el Pedido, se publica un evento
class Pedido {
    public void confirmar() {
        // ...lógica
        this.estado = EstadoPedido.CONFIRMADO;
        this.eventos.add(new PedidoConfirmado(this.pedidoId, this.clienteId));
    }
}

// Inventario reacciona al evento (otro Aggregate, otra transacción)
class InventarioEventHandler {
    @On(PedidoConfirmado.class)
    void handle(PedidoConfirmado evento) {
        // reservar stock — transacción separada
    }
}
```

## ¿Qué tan grande debe ser un Aggregate?

**Regla:** tan pequeño como sea posible manteniendo las invariantes.

```
❌ Aggregate gigante (antipatrón):
Cliente
├── historialPedidos[]     ← pueden ser miles
├── direcciones[]
├── tarjetas[]
└── sesiones[]

✅ Aggregates pequeños:
Cliente (solo datos propios)
Pedido (solo su propio estado)
DireccionEnvio (solo dirección)
```

Agrandar el Aggregate = más contención, más locks, peor performance.

## Ejemplo completo

```java
class Pedido {  // Aggregate Root
    private final PedidoId id;
    private final ClienteId clienteId;
    private EstadoPedido estado;
    private final List<LineaDePedido> lineas = new ArrayList<>();
    private final List<DomainEvent> eventos = new ArrayList<>();

    // Constructor privado — solo Factory o reconstrucción
    private Pedido(PedidoId id, ClienteId clienteId) {
        this.id = id;
        this.clienteId = clienteId;
        this.estado = EstadoPedido.BORRADOR;
    }

    // Factory method estático
    public static Pedido crear(ClienteId clienteId) {
        return new Pedido(PedidoId.nuevo(), clienteId);
    }

    public void agregarLinea(ProductoId producto, Cantidad cantidad, Dinero precio) {
        validarEstadoBorrador();
        lineas.add(new LineaDePedido(LineaId.nuevo(), producto, cantidad, precio));
    }

    public void confirmar() {
        if (lineas.isEmpty()) throw new DomainException("Pedido vacío");
        this.estado = EstadoPedido.CONFIRMADO;
        this.eventos.add(new PedidoConfirmado(id, clienteId));
    }

    public Dinero calcularTotal() {
        return lineas.stream()
            .map(LineaDePedido::subtotal)
            .reduce(Dinero.CERO, Dinero::sumar);
    }

    private void validarEstadoBorrador() {
        if (estado != EstadoPedido.BORRADOR)
            throw new DomainException("Pedido ya no está en borrador");
    }
}
```

---

## Referencias
- [[DDD - Domain Driven Design]]
- [[Entities]]
- [[Value Objects]]
- [[Domain Events]]
- [[Repositories]]
