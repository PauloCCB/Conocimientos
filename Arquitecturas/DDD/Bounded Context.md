# Bounded Context (Contexto Delimitado)

> **Definición:** Frontera explícita dentro de la cual un modelo de dominio específico es válido, consistente y tiene un significado preciso.

Pertenece a: [[DDD - Domain Driven Design]] → Strategic Design

---

## El problema que resuelve

En sistemas grandes, el mismo concepto tiene significados distintos según el área del negocio. Intentar tener un modelo "universal" produce un modelo que no sirve bien a nadie.

**Ejemplo real — e-commerce:**

| Concepto | Ventas | Soporte | Logística | Facturación |
|----------|--------|---------|-----------|-------------|
| `Cliente` | historial, descuentos | tickets, prioridad | dirección, horario entrega | RUC/RFC, datos fiscales |
| `Producto` | precio, descripción, fotos | categoría del problema | peso, dimensiones, fragil | código arancelario |
| `Pedido` | items, total, promociones | referencia de queja | bultos, ruta | líneas contables |

Si haces una clase `Pedido` que sirve a todos → Dios objeto lleno de nulos y condicionales.

## La solución: separar fronteras

Cada área del negocio tiene su propio modelo:

```
┌──────────────────────┐    ┌──────────────────────┐
│       VENTAS         │    │      LOGÍSTICA        │
│                      │    │                       │
│  Pedido              │    │  Envio                │
│    - items[]         │    │    - bultos[]         │
│    - descuento       │    │    - rutaEntrega      │
│    - total           │    │    - ventanaHoraria   │
│    - estadoPago      │    │    - transportista    │
└──────────────────────┘    └──────────────────────┘
         ↕  se comunican via eventos/API, no clases compartidas
```

## Cómo identificar Bounded Contexts

**Señales de que necesitas separar un contexto:**
- Un término tiene dos definiciones según con quién hablas
- Un equipo de negocio diferente usa el concepto
- El ciclo de vida del objeto difiere (cuándo se crea, modifica, elimina)
- Distintas reglas de negocio aplican al mismo "objeto"

**Técnica práctica:** [[Event Storming]] — los eventos naturalmente se agrupan en contextos.

## Tamaño de un Bounded Context

No hay regla fija. Guías:

- Un equipo pequeño (2-8 personas) puede dueñar un contexto
- Un microservicio suele mapear a un Bounded Context (o parte de uno)
- Si dos áreas siempre cambian juntas → pueden ser el mismo contexto
- Si dos áreas nunca se tocan → probablemente contextos separados

> Microservicio ≠ Bounded Context. Un contexto puede tener varios servicios, o un servicio puede implementar parte de un contexto. El contexto es conceptual; el microservicio es de despliegue.

## Relaciones entre contextos

Los contextos deben comunicarse. Esas relaciones se documentan en el [[Context Map]]:

- [[Context Map#Shared Kernel|Shared Kernel]] — comparten código
- [[Context Map#Anticorruption Layer|Anticorruption Layer]] — traducen modelos
- [[Context Map#Open Host Service|Open Host Service]] — API pública
- etc.

## Bounded Context y el código

Cada contexto tiene su propio:

```
src/
  ventas/           ← Bounded Context: Ventas
    domain/
      Pedido.java
      Cliente.java
      PedidoRepository.java
    application/
      ConfirmarPedidoUseCase.java
    infrastructure/
      PedidoRepositorySQL.java

  logistica/        ← Bounded Context: Logística
    domain/
      Envio.java
      Ruta.java
    ...
```

Nunca: `ventas/domain/Pedido.java` importa `logistica/domain/Envio.java` directamente.

---

## En resumen

| | Monolito sin BC | Con Bounded Contexts |
|--|--|--|
| Modelo | Uno universal, todo mezclado | Varios, cada uno enfocado |
| Cambios | Riesgo alto, todo conectado | Aislados por frontera |
| Equipo | Todos tocan todo | Cada equipo dueña su contexto |
| Lenguaje | Ambiguo, depende del contexto | Preciso dentro de cada frontera |

---

## Referencias
- [[DDD - Domain Driven Design]]
- [[Ubiquitous Language]]
- [[Context Map]]
- [[Event Storming]]
