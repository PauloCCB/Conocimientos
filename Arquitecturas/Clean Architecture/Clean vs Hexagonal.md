# Clean Architecture vs Hexagonal Architecture

> **Definición:** Análisis comparativo entre las dos arquitecturas más influyentes del diseño de software moderno. Comparten el mismo principio fundamental pero difieren en nomenclatura, diagrama y nivel de detalle.

Pertenece a: [[Clean Architecture]]

---

## Orígenes

| | [[Hexagonal Architecture]] | Clean Architecture |
|---|---|---|
| Autor | Alistair Cockburn | Robert C. Martin |
| Año | 2005 | 2012 |
| Libro/Artículo | Artículo "Hexagonal Architecture" | Libro "Clean Architecture" |
| Inspiración | Observación propia | Unificación de Hexagonal, DCI, BCE |

> Clean Architecture es **heredera directa** de Hexagonal. Uncle Bob tomó la idea de puertos/adaptadores y la formalizó en un sistema de círculos con una regla explícita.

---

## Comparación visual

```
HEXAGONAL                          CLEAN ARCHITECTURE
                                   
  ┌──────────────┐                  ┌────────────────────┐
  │  Adaptador  ││                  │ Frameworks         │
  │ ┌──────────┐ ││                  │  ┌──────────────┐  │
  │ │  Puerto  │ ││                  │  │  Interface   │  │
  │ │┌────────┐│ ││                  │  │  Adapters    │  │
  │ ││Dominio ││ ││                  │  │ ┌──────────┐ │  │
  │ │└────────┘│ ││                  │  │ │Use Cases │ │  │
  │ └──────────┘ ││                  │  │ │┌────────┐│ │  │
  └──────────────┘│                  │  │ ││Entities││ │  │
                   │                  │  │ │└────────┘│ │  │
  Simetría: todos  │                  │  │ └──────────┘ │  │
  los lados son    │                  │  └──────────────┘  │
  iguales          │                  └────────────────────┘
                                      Regla de dependencia
                                      explícita
```

---

## Tabla comparativa detallada

| Aspecto | Hexagonal | Clean Architecture |
|---|---|---|
| **Principio rector** | Puertos y adaptadores intercambiables | Regla de Dependencia (hacia adentro) |
| **Terminología** | Puerto primario, puerto secundario, adaptador | Entities, Use Cases, Interface Adapters, Frameworks |
| **Capas** | 3 (dominio, puertos, adaptadores) | 4+ círculos concéntricos |
| **Diagrama** | Hexágono simétrico | Círculos concéntricos |
| **Énfasis** | Intercambiabilidad de adaptadores | Independencia del dominio |
| **Testing** | Implícito en el diseño | Explícito con Humble Object Pattern |
| **Curva de aprendizaje** | Baja (dos conceptos: puerto y adaptador) | Media (4 círculos, DIP, cruce de fronteras) |
| **Convenciones** | Pocas. Más libertad. | Muchas. Más reglas explícitas. |

---

## Mapeo de conceptos entre ambas

| Hexagonal | Clean Architecture |
|---|---|
| Puerto Primario (Driving) | Input Port del caso de uso |
| Puerto Secundario (Driven) | Output Port / Gateway interface |
| Adaptador Primario | Controller (en Interface Adapters) |
| Adaptador Secundario | Gateway / Repository implementation |
| Dominio | Entities + Use Cases (juntos) |
| — | Humble Object Pattern (exclusivo de Clean) |
| — | Crossing Boundaries (explícito en Clean) |

---

## ¿Cuál usar?

| Situación | Recomendación |
|---|---|
| Proyecto chico/mediano, equipo pequeño | **Hexagonal** — menos ceremonias, mismos beneficios |
| Proyecto grande, múltiples equipos | **Clean Architecture** — más estructura, reglas explícitas |
| Querés flexibilidad en la organización | Hexagonal |
| Necesitás estándares estrictos | Clean Architecture |
| Estás aprendiendo | Empezá por Hexagonal, evolucioná a Clean |

> En la práctica, muchos proyectos usan **una mezcla**: los puertos de Hexagonal con la regla de dependencia de Clean. No son excluyentes.

---

## Lo que comparten

1. El dominio/entities en el centro, sin dependencias externas
2. Interfaces definidas por el dominio (inversión de dependencias)
3. Infraestructura intercambiable (cambiar BD no rompe el negocio)
4. Testeabilidad del dominio sin infraestructura real
5. El framework es un detalle, no el centro

Véase también: [[Clean Architecture vs Hexagonal Architecture]] — análisis complementario con diferencias adicionales.

---

## Referencias
- [[Clean Architecture]]
- [[Hexagonal Architecture]]
- [[Clean Architecture vs Hexagonal Architecture]]
- [[DDD - Domain Driven Design]]
