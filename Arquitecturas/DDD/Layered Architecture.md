# Layered Architecture (Arquitectura en Capas)

> **Definición:** Organización del código en 4 capas con responsabilidades claras, donde la **regla de dependencia** dicta que las capas externas dependen de las internas, nunca al revés.

Pertenece a: [[DDD - Domain Driven Design]] → Tactical Design

---

## Las 4 capas

```
┌─────────────────────────────────────────┐
│           User Interface                │  ← HTTP, CLI, GraphQL, WebSocket
├─────────────────────────────────────────┤
│          Application Layer              │  ← Casos de uso, orquestación
├─────────────────────────────────────────┤
│            Domain Layer                 │  ← El corazón del negocio
├─────────────────────────────────────────┤
│        Infrastructure Layer             │  ← BD, APIs externas, mensajería
└─────────────────────────────────────────┘

Dependencias: ↓ (cada capa conoce solo las que están debajo)
```

---

## Capa por capa

### User Interface (Presentación)

**Qué hace:** Recibe requests, devuelve responses. Traduce entre el mundo externo y la capa de aplicación.

```java
@RestController
class PedidoController {
    private final ConfirmarPedidoUseCase confirmarPedido;

    @PostMapping("/pedidos/{id}/confirmar")
    ResponseEntity<Void> confirmar(@PathVariable String id) {
        confirmarPedido.ejecutar(new PedidoId(id));  // delega, no tiene lógica
        return ResponseEntity.ok().build();
    }
}
```

**No debe:** Tener lógica de negocio, acceder a la BD directamente, crear Aggregates.

---

### Application Layer (Aplicación)

**Qué hace:** Orquesta el flujo de un caso de uso. Delega lógica al dominio. Es fina.

```java
class ConfirmarPedidoUseCase {
    private final PedidoRepository pedidoRepository;
    private final EventBus eventBus;

    public void ejecutar(PedidoId pedidoId) {
        // 1. Obtener aggregate
        Pedido pedido = pedidoRepository.findById(pedidoId)
            .orElseThrow(() -> new PedidoNotFound(pedidoId));

        // 2. Ejecutar lógica de dominio (delega, no la escribe)
        pedido.confirmar();

        // 3. Persistir
        pedidoRepository.save(pedido);

        // 4. Publicar eventos
        eventBus.publish(pedido.pullEventos());
    }
}
```

**No debe:** Contener reglas de negocio. Si escribe un `if` de negocio, está mal.

---

### Domain Layer (Dominio)

**Qué hace:** Contiene toda la lógica de negocio. Es el núcleo del sistema.

Vive aquí:
- [[Entities]] y [[Value Objects]]
- [[Aggregates]] (con sus invariantes)
- Interfaces de [[Repositories]] (la implementación vive en infra)
- [[Domain Events]]
- Domain Services (lógica que no pertenece a un solo Aggregate)

```java
class Pedido {  // Aggregate — dominio puro
    public void confirmar() {
        if (lineas.isEmpty()) throw new DomainException("Pedido vacío");
        if (estado != EstadoPedido.BORRADOR) throw new DomainException("Estado inválido");
        this.estado = EstadoPedido.CONFIRMADO;
        this.eventos.add(new PedidoConfirmado(id, clienteId));
    }
}
```

**No debe:** Importar nada de Spring, JPA, HTTP, o cualquier framework/infraestructura.

---

### Infrastructure Layer (Infraestructura)

**Qué hace:** Implementa las interfaces del dominio. Conecta con el mundo real.

```java
// Implementa la interfaz definida en el dominio
@Repository
class PedidoRepositoryJPA implements PedidoRepository { ... }

// Implementa el EventBus
@Component
class RabbitMQEventBus implements EventBus { ... }

// Configuración, ORM, clientes HTTP de terceros, etc.
```

**No debe:** Filtrar lógica de negocio aquí.

---

## La regla de dependencia — el punto más importante

```
UI → Application → Domain ← Infrastructure
                   ↑
              (independiente de todo)
```

El **Domain Layer no importa nada de las otras capas**.

```java
// ❌ MAL: el dominio conoce JPA
import javax.persistence.Entity;
@Entity
class Pedido { ... }

// ✅ BIEN: el dominio es POJO puro
class Pedido { ... }
// JPA va en la capa de infraestructura, en una clase PedidoJpaEntity separada
```

## Estructura de carpetas

```
src/main/java/com/empresa/ventas/
├── ui/
│   └── PedidoController.java
├── application/
│   ├── ConfirmarPedidoUseCase.java
│   └── CrearPedidoUseCase.java
├── domain/
│   ├── Pedido.java               ← Aggregate
│   ├── LineaDePedido.java        ← Entity interna
│   ├── PedidoId.java             ← Value Object
│   ├── Dinero.java               ← Value Object
│   ├── PedidoRepository.java     ← interfaz
│   └── PedidoConfirmado.java     ← Domain Event
└── infrastructure/
    ├── PedidoRepositoryJPA.java  ← implementación
    ├── PedidoJpaEntity.java      ← modelo de persistencia
    └── PedidoMapper.java         ← traductor domain ↔ JPA
```

## Variantes modernas

**Hexagonal Architecture (Ports & Adapters):** misma idea, diferente metáfora. El dominio define "puertos" (interfaces); la infraestructura provee "adaptadores" (implementaciones).

**Clean Architecture:** similar, con más capas nombradas. El principio de dependencia es idéntico.

Todas comparten la misma regla: **el dominio es independiente**.

---

## Referencias
- [[DDD - Domain Driven Design]]
- [[Aggregates]]
- [[Repositories]]
- [[Domain Events]]
