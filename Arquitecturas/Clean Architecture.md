# Clean Architecture

> **Definición:** Patrón arquitectónico propuesto por **Robert C. Martin** en 2012 que organiza el software en capas concéntricas gobernadas por la **Regla de Dependencia**: las dependencias de código fuente solo pueden apuntar hacia adentro. Nada en un círculo interior puede saber nada sobre algo en un círculo exterior.

---

## ¿Por qué existe?

Robert C. Martin observó que con los años, distintas arquitecturas proponían lo mismo con distintos nombres: [[Hexagonal Architecture]] (Cockburn, 2005), DCI (Reenskaug), BCE (Jacobson). Todas coincidían en: **separar la lógica de negocio de los detalles de infraestructura.**

Clean Architecture unifica esas ideas bajo un solo framework con reglas explícitas.

```
"La arquitectura no es sobre frameworks. La arquitectura es sobre el dominio."
— Robert C. Martin
```

---

## Los círculos concéntricos

```
┌──────────────────────────────────────────────┐
│            Frameworks & Drivers              │
│  ┌──────────────────────────────────────┐    │
│  │       Interface Adapters             │    │
│  │  ┌──────────────────────────────┐    │    │
│  │  │      Application             │    │    │
│  │  │      Business Rules          │    │    │
│  │  │  ┌──────────────────────┐    │    │    │
│  │  │  │     Enterprise       │    │    │    │
│  │  │  │   Business Rules     │    │    │    │
│  │  │  │                      │    │    │    │
│  │  │  │   (Entities)         │    │    │    │
│  │  │  └──────────────────────┘    │    │    │
│  │  └──────────────────────────────┘    │    │
│  └──────────────────────────────────────┘    │
└──────────────────────────────────────────────┘

   ──────────── DEPENDENCIA ────────────►
   (siempre hacia adentro)
```

| Círculo | Contiene | Depende de... |
|---|---|---|
| **Entities** | Reglas de negocio enterprise. Entidades de alto nivel. | Nada. Es el centro. |
| **Use Cases** | Casos de uso (Application Business Rules). Orquestan entities. | Entities |
| **Interface Adapters** | Controllers, Gateways, Presenters. Traducen formatos. | Use Cases + Entities |
| **Frameworks & Drivers** | Web, BD, frameworks. Todo lo externo. | Interface Adapters |

---

## La Regla de Dependencia

> *"El código fuente solo puede apuntar hacia adentro."*

Nada en un círculo interior puede mencionar una clase, función, o tipo de un círculo exterior.

Véase en detalle: [[Dependency Rule]] — enunciado formal, verificación, DIP aplicado.

---

## Los 4 círculos en profundidad

### Entities — Enterprise Business Rules

El círculo más interno. Contiene las reglas de negocio más abstractas y de más alto nivel. Son independientes de la aplicación específica.

Véase: [[Entities]] — definición, ejemplos, relación con DDD.

### Use Cases — Application Business Rules

Contiene los casos de uso de la aplicación. Orquesta el flujo de datos hacia y desde las Entities. Es la capa donde vive la lógica específica de esta aplicación.

Véase: [[Use Cases]] — DTOs de entrada/salida, inyección de puertos.

### Interface Adapters

Convierte datos entre el formato más conveniente para los casos de uso/entities y el formato más conveniente para frameworks externos. Controllers, Gateways, Presenters viven acá.

Véase: [[Interface Adapters]] — MVC, Presenters, ViewModels.

### Frameworks & Drivers

La capa más externa. Acá vive todo lo concreto: base de datos, web framework, librerías de terceros.

Véase: [[Frameworks & Drivers]] — la cáscara intercambiable.

---

## Flujo de control y cruce de fronteras

Cuando una petición atraviesa un círculo hacia adentro, el flujo de control va en dirección opuesta a la dependencia. Para resolver esta paradoja se usa el **Principio de Inversión de Dependencias (DIP)**.

Véase en detalle: [[Crossing Boundaries]] — cómo cruzar fronteras sin violar la regla.

---

## 🧪 El Humble Object Pattern

Patrón para testear componentes difíciles de testear (como vistas o controladores HTTP). Separá el comportamiento en dos partes: el **Humble Object** (difícil de testear, sin lógica) y el objeto testeable (con toda la lógica).

Véase: [[Humble Object Pattern]] — ejemplos con controllers y presenters.

---

## 🧩 Clean Architecture, Hexagonal y DDD

| | Clean Architecture | [[Hexagonal Architecture]] | [[DDD - Domain Driven Design]] |
|---|---|---|---|
| **Foco** | Regla de dependencia, círculos | Puertos y adaptadores, simetría | Modelado del dominio, lenguaje ubicuo |
| **Diagrama** | Círculos concéntricos | Hexágono con puertos | Bounded Contexts, Context Map |
| **Autor** | Robert C. Martin (2012) | Alistair Cockburn (2005) | Eric Evans (2003) |
| **Compatibilidad** | — | Compatible. Clean hereda de Hexagonal. | Complementario. DDD modela lo que va en Entities + Use Cases. |

Véase comparativa detallada: [[Clean vs Hexagonal]] y [[Clean Architecture vs Hexagonal Architecture]].

---

## Cambio de framework: el momento de la verdad

**Escenario:** migrar de Spring Boot a Quarkus.

```
Sin Clean Architecture:
   Reescribir TODO. Las reglas de negocio están mezcladas con @RestController,
   @Autowired, @Transactional.

Con Clean Architecture:
   El dominio y casos de uso NO tienen anotaciones de framework.
   Solo reescribís los adaptadores. El 80% del código no se toca.
```

---

## 🗺️ Cómo empezar

1. **Diseñá las Entities** — ¿Cuáles son las reglas de negocio más abstractas?
2. **Definí los Use Cases** — ¿Qué puede hacer un usuario con el sistema?
3. **Creá los Interface Adapters** — Controllers, Presenters, Gateways
4. **Enchufá los Frameworks** — Spring/NestJS, PostgreSQL, RabbitMQ
5. **Escribí tests del dominio primero** — sin infraestructura

---

## 📚 Referencias

- 📖 *"Clean Architecture: A Craftsman's Guide to Software Structure and Design"* — Robert C. Martin (2017)
- 🌐 [[Hexagonal Architecture]] — la arquitectura que inspiró Clean Architecture
- 🌐 [[DDD - Domain Driven Design]] — cómo modelar el dominio que va en el centro
- 🌐 [[Clean Architecture vs Hexagonal Architecture]] — comparativa detallada

### Nodos especializados
- [[Entities]]
- [[Use Cases]]
- [[Interface Adapters]]
- [[Frameworks & Drivers]]
- [[Dependency Rule]]
- [[Crossing Boundaries]]
- [[Humble Object Pattern]]
- [[Clean vs Hexagonal]]
