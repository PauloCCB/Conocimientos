# Event Storming

> **Definición:** Taller colaborativo donde desarrolladores y expertos del negocio descubren el dominio juntos, usando post-its de colores en una pared, partiendo de los **Domain Events**.

Relacionado con: [[DDD - Domain Driven Design]], [[Domain Events]], [[Bounded Context]]

---

## Para qué sirve

Event Storming es la técnica práctica para descubrir el dominio antes de escribir código. Responde:

- ¿Qué eventos importantes ocurren en el negocio?
- ¿Qué los dispara? ¿Quién los dispara?
- ¿Qué políticas y reacciones existen?
- ¿Dónde están los [[Bounded Context|Bounded Contexts]]?
- ¿Dónde están los cuellos de botella y las dudas del negocio?

## Los post-its (notación)

| Color | Elemento | Ejemplo |
|-------|----------|---------|
| 🟠 Naranja | **Domain Event** | `PedidoConfirmado` |
| 🔵 Azul | **Command** | `ConfirmarPedido` |
| 🟡 Amarillo | **Actor** | `Cliente`, `Operador` |
| 🟣 Lila | **Aggregate** | `Pedido` |
| 🟢 Verde | **Policy / Regla** | "Cuando se confirma, reservar stock" |
| 🔴 Rojo | **External System** | `Pasarela de Pagos`, `API de Courier` |
| Rosa | **Read Model** | `Panel de pedidos del vendedor` |

## Cómo se hace

### Paso 1: Chaos mode (30-45 min)

Todos escriben **eventos de dominio** en post-its naranjas y los pegan en la pared en desorden. Reglas mínimas:

- Nombre en **pasado**: `UsuarioRegistrado`, `PagoFallido`
- Sin filtrar — cualquier evento que alguien considere importante
- Sin debatir todavía

```
[PedidoCreado] [UsuarioRegistrado] [PagoAprobado] [StockActualizado]
[ProductoAgotado] [EnvioSalido] [PedidoCancelado] [FacturaEmitida]
[DevolucionSolicitada] [PagoFallido] [CuentaBloqueada] [PromocionAplicada]
```

### Paso 2: Ordenar en línea de tiempo (20-30 min)

Mover los eventos en orden cronológico de izquierda a derecha:

```
[UsuarioRegistrado] → [PedidoCreado] → [PagoAprobado] → [PedidoConfirmado]
                                                       → [StockReservado]
                                                       → [FacturaEmitida]
                   → [PagoFallido] → [PedidoCancelado]
```

### Paso 3: Agregar Commands y Actores

¿Quién/qué dispara cada evento?

```
[Cliente] → ConfirmarPedido → [PedidoConfirmado]
[Sistema] → ProcesarPago   → [PagoAprobado] / [PagoFallido]
```

### Paso 4: Agregar Policies

"Cuando X ocurre, entonces Y debe pasar":

```
[PedidoConfirmado] → 🟢 "Cuando se confirma, reservar stock" → ReservarStock → [StockReservado]
[PedidoConfirmado] → 🟢 "Cuando se confirma, emitir factura" → EmitirFactura → [FacturaEmitida]
```

### Paso 5: Identificar Aggregates

Agrupar los eventos bajo el Aggregate que los produce:

```
[Pedido]
  PedidoCreado, LineaAgregada, PedidoConfirmado, PedidoCancelado

[Pago]
  PagoIniciado, PagoAprobado, PagoFallido, PagoReembolsado

[Inventario]
  StockReservado, StockLiberado, ProductoAgotado
```

### Paso 6: Identificar Bounded Contexts

Los Aggregates y eventos naturalmente se agrupan en contextos:

```
┌──────────────────┐   ┌──────────────────┐   ┌──────────────────┐
│     VENTAS       │   │     PAGOS        │   │   INVENTARIO     │
│  Pedido          │   │  Pago            │   │  Stock           │
│  PedidoCreado    │   │  PagoAprobado    │   │  StockReservado  │
│  PedidoConfirmado│   │  PagoFallido     │   │  ProductoAgotado │
└──────────────────┘   └──────────────────┘   └──────────────────┘
```

### Paso 7: Marcar puntos de dolor (Hot Spots)

Post-it rojo brillante donde hay:

- Dudas sobre el negocio
- Inconsistencias en el lenguaje
- Desacuerdos entre expertos
- Procesos mal definidos

```
[PedidoConfirmado] → 🔴 "¿Cuándo exactamente se cobra? ¿Al confirmar o al enviar?"
```

---

## Quiénes participan

| Rol | Por qué importa |
|-----|----------------|
| Expertos del negocio | Conocen los procesos reales |
| Desarrolladores | Traducen a código, detectan ambigüedades |
| Product Owner | Prioriza y decide trade-offs |
| UX (opcional) | Aporta perspectiva del usuario final |

**No participan:** managers que solo observan, personas que no pueden decidir.

## Tipos de Event Storming

| Tipo | Duración | Para qué |
|------|----------|----------|
| **Big Picture** | 2-4 horas | Explorar dominio nuevo, ver todo el sistema |
| **Process Level** | 1-2 horas | Profundizar un proceso específico |
| **Design Level** | 4-8 horas | Diseño técnico detallado, listos para codificar |

## Resultado del taller

Al final tienes:
- Lista de [[Domain Events]] del sistema
- [[Ubiquitous Language]] emergente (los nombres de los post-its)
- [[Bounded Context|Bounded Contexts]] identificados visualmente
- [[Aggregates]] principales
- [[Context Map]] borrador
- Lista de Hot Spots (preguntas pendientes del negocio)

---

## Herramienta digital

Para equipos remotos: [Miro](https://miro.com) o [Mural](https://mural.co) con plantilla de Event Storming.

---

## Referencias
- [[DDD - Domain Driven Design]]
- [[Domain Events]]
- [[Bounded Context]]
- [[Ubiquitous Language]]
- [[Aggregates]]
