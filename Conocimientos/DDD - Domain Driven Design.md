# DDD — Domain-Driven Design

> **Definición:** Enfoque de diseño de software donde el **dominio del negocio** es el centro de todas las decisiones técnicas. El código debe hablar el mismo idioma que los expertos del negocio.

Creado por **Eric Evans** en su libro *"Domain-Driven Design: Tackling Complexity in the Heart of Software"* (2003).

---

## ¿Por qué existe DDD?

El problema clásico: los desarrolladores hablan de `tablas`, `clases`, `métodos`, y los expertos del negocio hablan de `pedidos`, `clientes`, `facturas`. Ese gap produce software que no resuelve el problema real. 

DDD propone: **haz que el código refleje el dominio del negocio**, no la base de datos ni el framework.

```
Sin DDD:  Negocio → [traducción caótica] → Código
Con DDD:  Negocio ←→ Código  (mismo lenguaje)
```

---

## Las dos grandes partes de DDD

DDD se divide en **Strategic Design** (el QUÉ y DÓNDE) y **Tactical Design** (el CÓMO).

---

# 🗺️ Strategic Design — El mapa grande

Define cómo organizar el sistema a gran escala. Responde: *¿cómo dividimos el problema?*

## [[DDD/Ubiquitous Language|Ubiquitous Language]] (Lenguaje Ubicuo)

El término más importante de DDD. Es el **vocabulario compartido** entre desarrolladores y expertos del negocio.

- Mismo término = mismo significado para todos
- Se refleja **en el código**: nombres de clases, métodos, variables
- Si el negocio dice `Pedido`, el código tiene clase `Pedido`, no `Order` o `PurchaseRecord`

> ⚠️ Si un término tiene dos significados según el contexto, es señal de que necesitas dos Bounded Contexts.

## [[DDD/Bounded Context|Bounded Context]] (Contexto Delimitado)

**Frontera explícita** dentro de la cual un modelo de dominio es válido y consistente.

Ejemplo real: La palabra `Cliente` significa cosas distintas en:
- **Ventas**: tiene historial de compras, descuentos
- **Soporte**: tiene tickets, prioridad de atención
- **Logística**: tiene dirección de entrega, preferencias

Cada uno es un Bounded Context separado con su propio modelo.

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│     Ventas      │    │    Soporte      │    │   Logística     │
│  Cliente(ventas)│    │ Cliente(ticket) │    │ Cliente(envío)  │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

## [[DDD/Context Map|Context Map]] (Mapa de Contextos)

Diagrama que muestra **todos los Bounded Contexts** y cómo se relacionan entre sí. Es la vista macro del sistema.

Patrones de relación entre contextos:

| Patrón | Descripción |
|--------|-------------|
| **Shared Kernel** | Dos contextos comparten una porción de código/modelo. Riesgo: cambios impactan ambos. |
| **Customer/Supplier** | Upstream (proveedor) define la API, Downstream (cliente) depende de ella. |
| **Conformist** | El downstream acepta el modelo del upstream sin cuestionarlo. Sin poder de negociación. |
| **Anticorruption Layer (ACL)** | Capa de traducción que protege tu modelo del modelo externo. Evita que el modelo ajeno "contamine" el tuyo. |
| **Open Host Service** | El contexto publica una API pública bien definida para que otros se integren. |
| **Published Language** | Lenguaje compartido y documentado (ej: JSON Schema, Protobuf) para la comunicación entre contextos. |
| **Separate Ways** | Dos contextos deciden no integrarse. Cada uno resuelve su problema por separado. |
| **Big Ball of Mud** | Antipatrón. Sistema sin fronteras claras, todo mezclado. Lo que DDD intenta evitar. |

## Continuous Integration (en DDD)

En Strategic Design, CI significa mantener el **modelo del Bounded Context unificado y consistente** dentro del equipo. No fragmentar el modelo por silos de equipo.

> Nota: Aquí CI es concepto de DDD, no solo "correr tests en pipeline" (aunque se complementan).

---

# ⚙️ Tactical Design — Los bloques de construcción

Define cómo modelar el dominio **dentro de un Bounded Context**. Son los patrones concretos de código.

## [[DDD/Layered Architecture|Model-Driven Design]]

El código **es** el modelo. No hay un "modelo de negocio" separado del código. Si el dominio cambia, el código cambia, y viceversa. Elimina la duplicación de documentación vs implementación.

## [[DDD/Layered Architecture|Layered Architecture]] (Arquitectura en Capas)

Organización estándar en DDD con 4 capas:

```
┌──────────────────────────────┐
│      User Interface          │  ← Presenta datos al usuario
├──────────────────────────────┤
│      Application Layer       │  ← Orquesta casos de uso (fina, sin lógica)
├──────────────────────────────┤
│       Domain Layer           │  ← El corazón: entidades, value objects, lógica
├──────────────────────────────┤
│    Infrastructure Layer      │  ← BD, APIs externas, frameworks
└──────────────────────────────┘
```

La regla: **las capas superiores dependen de las inferiores, nunca al revés**. El Domain Layer no sabe nada de la BD.

## [[DDD/Entities|Entities]] (Entidades)
 
Objetos con **identidad propia** que persiste en el tiempo, aunque sus atributos cambien.

```java
// Dos usuarios con mismo nombre ≠ mismo usuario
// Los diferencia su ID, no sus datos
class Usuario {
    private final UserId id;  // identidad
    private String nombre;    // puede cambiar
    private String email;     // puede cambiar
}
```

> Si cambia el nombre del usuario, sigue siendo el mismo usuario. La identidad es el ID.

## [[DDD/Value Objects|Value Objects]] (Objetos de Valor)

Objetos **sin identidad**. Se definen por sus atributos. Dos Value Objects con mismos atributos son iguales.

```java
// Dos montos de $100 son intercambiables
class Dinero {
    private final BigDecimal cantidad;
    private final String moneda;
    // Sin ID. Inmutable. Igualdad por valor.
}
```

Características clave:
- **Inmutables** — nunca se modifican, se reemplazan
- **Sin efecto lateral** — operaciones retornan nuevos objetos
- Encapsulan validaciones del dominio

> Ejemplos: `Email`, `Dinero`, `Coordenadas`, `Rango de fechas`, `Número de teléfono`

## [[DDD/Aggregates|Aggregates]] (Agregados)

**Cluster de objetos** (Entities + Value Objects) que se tratan como una unidad. Tienen una **Aggregate Root** (raíz) que es el único punto de acceso externo.

```
Pedido (Aggregate Root)
├── LineaDePedido (Entity interna)
│   ├── Producto (Value Object referencia)
│   └── Precio (Value Object)
└── DireccionEnvio (Value Object)
```

Reglas:
- Solo se accede al agregado por la raíz (`Pedido`)
- La raíz garantiza las invariantes del agregado
- Cada agregado es una unidad de consistencia transaccional

## [[DDD/Repositories|Repositories]] (Repositorios)

Abstracción para **persistir y recuperar Aggregates**. Simula una colección en memoria, oculta la BD.

```java
interface PedidoRepository {
    Pedido findById(PedidoId id);
    void save(Pedido pedido);
    List<Pedido> findByCliente(ClienteId clienteId);
}
// La implementación puede ser SQL, MongoDB, lo que sea
// El dominio no sabe ni le importa
```

> Un Repository por Aggregate Root. Nunca por entidad interna.

## Factories (Fábricas)

Encapsulan la **lógica compleja de construcción** de Aggregates o Entities. Cuando el constructor se vuelve complejo.

```java
class PedidoFactory {
    Pedido crearPedidoExpress(Cliente cliente, List<Item> items) {
        // lógica compleja: validaciones, cálculos, sub-objetos
        return new Pedido(...);
    }
}
```

## Services (Servicios de Dominio)

Lógica de negocio que **no pertenece naturalmente a ninguna Entity o Value Object**.

```java
// ¿A quién pertenece "transferir dinero"? ¿A CuentaOrigen? ¿CuentaDestino?
// A ninguna. Va en un Domain Service.
class TransferenciaService {
    void transferir(Cuenta origen, Cuenta destino, Dinero monto) { ... }
}
```

> Si una operación involucra múltiples Aggregates, probablemente es un Domain Service.

## [[DDD/Domain Events|Domain Events]] (Eventos de Dominio)

**Hechos que ocurrieron** en el dominio, expresados en tiempo pasado. Notifican que algo importante sucedió.

```java
class PedidoConfirmado {
    private final PedidoId pedidoId;
    private final ClienteId clienteId;
    private final Instant ocurridoEn;
}
```

Usos:
- Comunicación entre Bounded Contexts (sin acoplamiento directo)
- Disparar side effects (enviar email, actualizar inventario)
- Event Sourcing

> Nombre siempre en **pasado**: `PedidoConfirmado`, `UsuarioRegistrado`, `PagoFallido`

---

## Resumen visual

```
STRATEGIC DESIGN
├── Ubiquitous Language     → vocabulario compartido
├── Bounded Context         → frontera del modelo
├── Context Map             → relación entre contextos
│   ├── Shared Kernel
│   ├── Customer/Supplier
│   ├── Conformist
│   ├── Anticorruption Layer
│   ├── Open Host Service
│   ├── Published Language
│   ├── Separate Ways
│   └── Big Ball of Mud (antipatrón)
└── Continuous Integration  → modelo unificado en equipo

TACTICAL DESIGN
├── Model-Driven Design     → código = modelo
├── Layered Architecture    → capas separadas
├── Entities                → identidad por ID
├── Value Objects           → identidad por valor, inmutables
├── Aggregates              → cluster con raíz
├── Repositories            → persistencia abstracta
├── Factories               → construcción compleja
├── Services                → lógica sin dueño natural
└── Domain Events           → hechos del pasado
```

---

## Por dónde empezar

1. **Identificar el dominio** — ¿cuál es el negocio real?
2. **Event Storming** — taller con expertos del negocio para descubrir Domain Events
3. **Definir Bounded Contexts** — agrupar conceptos relacionados
4. **Construir el Ubiquitous Language** — glosario compartido
5. **Modelar Aggregates** — ¿cuáles son las unidades de consistencia?
6. **Implementar** con Entities, Value Objects, Repositories, etc.

---

## Referencias

- 📖 *Domain-Driven Design* — Eric Evans (el "Blue Book")
- 📖 *Implementing Domain-Driven Design* — Vaughn Vernon (el "Red Book")
- 🎥 CodelyTV — DDD en PHP/TypeScript (en español)
- 🌐 [[DDD/Event Storming|Event Storming]] — técnica para descubrir el dominio
