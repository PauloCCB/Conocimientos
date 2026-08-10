# Hexagonal Architecture (Ports & Adapters)

> **Definición:** Patrón arquitectónico propuesto por **Alistair Cockburn** en 2005 donde la lógica de negocio vive en el centro de la aplicación, completamente aislada del mundo exterior. Toda comunicación con el exterior (web, base de datos, colas, APIs) ocurre a través de **puertos** (interfaces) y **adaptadores** (implementaciones intercambiables).

---

## ¿Por qué existe?

El problema clásico: la lógica de negocio se contamina con detalles de infraestructura.

```
❌ Sin Hexagonal:
   Controlador HTTP → Lógica de negocio (sabe de SQL, JSON, HTTP...)

✅ Con Hexagonal:
   Controlador HTTP → Puerto → Lógica de negocio pura → Puerto → BD
                          ↑                                   ↑
                     Adaptador HTTP                     Adaptador SQL
```

Cambiar de REST a gRPC, o de PostgreSQL a MongoDB, debería ser un cambio de adaptador, **no reescribir la lógica de negocio**.

---

## La metáfora del hexágono

Cockburn eligió el hexágono por tres razones:
- Es una forma que permite dibujar múltiples puertos sin que el diagrama colapse
- Rompe la imagen mental de "capas apiladas" que sugiere jerarquía (presentación "arriba", BD "abajo")
- El número de lados no es mágico: podés tener 3, 8, los que necesites

```
        ┌──────── side 1 ─────────┐
        │    ┌───────────────┐    │
 side 2 │    │               │    │ side 3
  ──────┼────┤   DOMINIO     ├────┼──────
        │    │   PURO        │    │
 side 4 │    └───────────────┘    │ side 5
        └──────── side 6 ─────────┘
```

El diagrama canónico con adaptadores enchufados:

```
                 ┌──────────────────────┐
  ┌──────────────┤     Adaptador Web    ├──────────────┐
  │              │    (REST / gRPC)     │              │
  │              └──────────┬───────────┘              │
  │                         │                         │
  │              ┌──────────▼───────────┐              │
  │              │                      │              │
  │   Usuario    │      PUERTO          │    Base      │
  │   ────────►  │      [Interfaz]      │    de        │
  │              │                      │    Datos     │
  │              │     LÓGICA DE        │              │
  │              │     NEGOCIO          │              │
  │              │                      │              │
  │              └──────────▲───────────┘              │
  │                         │                         │
  │              ┌──────────┴───────────┐              │
  └──────────────┤   Adaptador de BD    ├──────────────┘
                 │   (SQL / Mongo)      │
                 └──────────────────────┘
```

> Los lados del hexágono son [[Ports|puertos]]. Cada adaptador se enchufa a un puerto. El dominio no sabe cuántos lados hay ni qué adaptadores están conectados.

---

## 🔌 Puertos y Adaptadores

La Arquitectura Hexagonal se basa en dos conceptos fundamentales, cada uno con dos variantes:

| | Primarios (Driving) | Secundarios (Driven) |
|---|---|---|
| **[[Ports\|Puertos]]** — Definen QUÉ | La API de casos de uso. Cómo el exterior **invoca** a la aplicación. | Lo que la aplicación necesita del exterior. SPIs que el dominio define. |
| **[[Adapters\|Adaptadores]]** — Implementan CÓMO | REST Controller, gRPC, CLI, Test Runner. Traducen estímulo externo → invocación al puerto. | PostgreSQL, MongoDB, SMTP, Stripe API. Implementan el puerto con tecnología concreta. |

La **[[Dependency Rule|Regla de Dependencia]]** lo gobierna todo: los adaptadores dependen de los puertos, los puertos del dominio. **Nunca al revés.**

Véanse en profundidad:
- [[Ports]] — interfaces, tipos, DIP en acción, cuántos necesitás
- [[Adapters]] — implementaciones, traducción, el Mapper, múltiples adaptadores por puerto
- [[Dependency Rule]] — la cebolla de dependencias, verificación en build, DIP aplicado

---

## 🏗️ Estructura de paquetes

La organización canónica en tres capas:

```
com.tuapp
├── domain/          ← Entities, Value Objects, Domain Services, Domain Events
├── application/     ← Puertos primarios/secundarios (interfaces) + casos de uso
├── adapter/         ← Adaptadores in (REST, CLI) y out (BD, mensajería, APIs)
└── config/          ← Inyección de dependencias
```

Véase en detalle: [[Package Structure]] — árbol completo, variantes "package by feature", módulos Gradle/Maven.

---

## 🧪 Testeabilidad radical

La consecuencia práctica más valiosa del hexágono: **testear la lógica de negocio sin infraestructura real.**

```java
// Test de dominio: sin Spring, sin PostgreSQL, sin HTTP
@Test
void debeCalcularTotalConImpuesto() {
    var repo = new InMemoryPedidoRepository();   // Fake en memoria
    var useCase = new CrearPedidoService(repo);

    var pedido = useCase.ejecutar(datosCon3ItemsDe100());

    assertEquals(new Dinero(330, "MXN"), pedido.getTotal());
}
```

Véase en detalle: [[Testing]] — pirámide de tests, fakes vs mocks, qué testear en cada capa.

---

## 🧩 Hexagonal y DDD: La pareja perfecta

La Arquitectura Hexagonal y [[DDD - Domain Driven Design|Domain-Driven Design]] son complementarias, no competitivas:

| DDD (Táctico) | Rol en Hexagonal |
|---|---|
| [[Entities]] | Viven en `domain/model/` |
| [[Value Objects]] | Viven en `domain/model/` |
| [[Aggregates]] | El corazón del hexágono |
| [[Repositories]] | **Son puertos secundarios**. La implementación es un adaptador `out`. |
| [[DDD - Domain Driven Design#Services (Servicios de Dominio)\|Domain Services]] | Lógica de dominio pura, en `domain/service/` |
| [[Domain Events]] | Viven en `domain/event/`, se publican por puertos secundarios |
| [[DDD - Domain Driven Design#Factories (Fábricas)\|Factories]] | Construcción de aggregates, dentro del dominio |
| Application Services | Orquestan casos de uso, en `application/service/` |
| [[Bounded Context]] | Cada BC puede ser su propio hexágono con sus propios puertos y adaptadores |

> **Hexagonal es el "cómo organizo el código". DDD es el "cómo modelo el negocio".** Se usan juntos: modelás el dominio con DDD y lo protegés con el hexágono.

---

## 🧭 Flujo completo de una petición

```
Usuario ──► [Adaptador REST] ──► [Puerto Primario] ──► Dominio
                                                           │
                                              ┌────────────┘
                                              ▼
                                        Aggregate Root
                                       (validaciones,
                                        invariantes,
                                        reglas de negocio)
                                              │
                                     ┌────────┘
                                     ▼
                              [Puerto Secundario] ──► [Adaptador BD]
                              (PedidoRepository)      (PostgreSQL)
```

Véase en detalle: [[Request Flow]] — paso a paso con código, flujos alternativos (eventos entrantes, schedulers), lo que NUNCA debe pasar.

---

## 🔄 Hexagonal vs otras arquitecturas

Véase el análisis detallado en [[Clean Architecture vs Hexagonal Architecture|Clean Architecture vs Hexagonal Architecture]].

| Aspecto | Hexagonal | [[Clean Architecture]] |
|---|---|---|
| Autor | Alistair Cockburn (2005) | Robert C. Martin (2012) |
| Enfoque | Puertos y adaptadores, simetría | Regla de dependencia, capas concéntricas |
| Diagrama | Hexágono con puertos en los lados | Círculos concéntricos |
| Nomenclatura | Puertos primarios/secundarios | Casos de uso, interfaces, gateways |
| Filosofía | "El exterior son detalles intercambiables" | "Las dependencias apuntan hacia adentro" |
| Relación | Hexagonal inspiró Clean Architecture | Clean Architecture es una evolución/refinamiento |

---

## 🔀 Cambio de adaptador: el momento de la verdad

**Escenario:** pasás de PostgreSQL a MongoDB.

```java
// Sin tocar el dominio, creás UN adaptador nuevo:
public class MongoPedidoRepository implements PedidoRepository {
    // Implementás la misma interfaz con MongoDB
}

// En la configuración de DI, cambiás el bean:
@Bean
public PedidoRepository pedidoRepository() {
    // return new PostgresPedidoRepository(...);  // ← antes
    return new MongoPedidoRepository(...);        // ← ahora
}
```

> El dominio no cambió una sola línea. Eso es arquitectura que cumple lo que promete.

---

## ⚠️ Anti-patrones

Los errores más comunes al implementar hexagonal:

1. **Entidades de BD en el dominio** (`@Entity` en la clase de dominio)
2. **Adaptador → Adaptador** sin pasar por el dominio
3. **Lógica de negocio en Controllers** o Repositories
4. **Puertos que filtran detalles de infraestructura** (SQL, HTTP, paginación de Spring)

Véase en detalle: [[Anti-Patterns]] — cada anti-patrón con código antes/después y por qué está mal.

---

## 🗺️ Cómo empezar con Hexagonal

1. **Identificá los puertos primarios** — ¿Qué casos de uso tiene la aplicación?
2. **Identificá los puertos secundarios** — ¿Qué necesita del exterior? BD, emails, APIs.
3. **Modelá el dominio puro** usando [[DDD - Domain Driven Design|DDD]]: [[Entities]], [[Value Objects]], [[Aggregates]], [[Domain Events]].
4. **Implementá los casos de uso** orquestando el dominio.
5. **Implementá los adaptadores** para cada puerto secundario.
6. **Escribí tests del dominio** con adaptadores secundarios fake en memoria ([[Testing|guía de testing]]).

---

## 📚 Referencias

- 📖 *"Hexagonal Architecture"* — Alistair Cockburn (artículo original, 2005)
- 📖 *"Get Your Hands Dirty on Clean Architecture"* — Tom Hombergs (ejemplos prácticos en Java/Spring)
- 🌐 [[DDD - Domain Driven Design|DDD]] — complemento natural para modelar el dominio dentro del hexágono
- 🌐 [[Clean Architecture]] — evolución formal de los mismos principios
- 🌐 [[Clean Architecture vs Hexagonal Architecture|Comparativa detallada: Clean vs Hexagonal]]

### Nodos especializados
- [[Ports]]
- [[Adapters]]
- [[Dependency Rule]]
- [[Testing]]
- [[Package Structure]]
- [[Anti-Patterns]]
- [[Request Flow]]
