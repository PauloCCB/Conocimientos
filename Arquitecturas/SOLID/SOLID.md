# SOLID — Principios de Diseño Orientado a Objetos

> **Definición:** Acrónimo de cinco principios de diseño propuestos por **Robert C. Martin** (Uncle Bob) a principios de los 2000. Son la base teórica sobre la que se construyen arquitecturas como [[Hexagonal Architecture]] y [[Clean Architecture]].

---

## ¿Por qué existen?

El software que no está diseñado con principios sólidos se vuelve **rígido, frágil e inmóvil** con el tiempo:

- **Rigidez:** un cambio en una parte obliga a cambiar muchas otras.
- **Fragilidad:** un cambio rompe funcionalidades no relacionadas.
- **Inmovilidad:** el código no se puede reutilizar en otros proyectos.

SOLID ataca estos tres males desde el diseño de clases y módulos.

| Letra | Principio | Problema que resuelve | Regla mnemónica |
|---|---|---|---|
| [[S - Single Responsibility\|S]] | Responsabilidad Única | Clases que hacen demasiado | "Una clase, una razón para cambiar" |
| [[O - Open Closed\|O]] | Abierto/Cerrado | Modificar código existente para añadir features | "Abierto a extensión, cerrado a modificación" |
| [[L - Liskov Substitution\|L]] | Sustitución de Liskov | Herencia mal usada que rompe el polimorfismo | "Subtipo debe ser sustituible por tipo base" |
| [[I - Interface Segregation\|I]] | Segregación de Interfaz | Interfaces gordas que obligan a implementar métodos innecesarios | "Mejor muchas interfaces pequeñas que una grande" |
| [[D - Dependency Inversion\|D]] | Inversión de Dependencia | Módulos de alto nivel acoplados a detalles | "Dependé de abstracciones, no de concreciones" |

---

## SOLID y las arquitecturas

SOLID no compite con las arquitecturas. Es su **fundamento**. Cada principio se manifiesta directamente en [[Hexagonal Architecture]] y [[Clean Architecture]]:

| Principio | En Hexagonal | En Clean Architecture |
|---|---|---|
| **S** | Cada [[Adapters\|adaptador]] tiene una sola responsabilidad (REST, BD, email) | Cada círculo tiene un único propósito |
| **O** | Añadir un nuevo adaptador no modifica el [[Ports\|puerto]] | Extender con nuevos casos de uso sin tocar Entities |
| **L** | Cualquier implementación de `PedidoRepository` es sustituible | El Gateway implementa el Output Port y es intercambiable |
| **I** | [[Ports\|Puertos]] secundarios pequeños y específicos | Output Ports con métodos mínimos |
| **D** | [[Dependency Rule\|Regla de Dependencia]]: dominio define interfaces, infraestructura las implementa | Círculos internos definen interfaces, círculos externos implementan |

---

## Orden de aprendizaje

1. **Empezá por S y D** — son los de mayor impacto inmediato.
2. **Seguí con I y O** — refinan el diseño de interfaces.
3. **Terminá con L** — es el más sutil, pero crítico para jerarquías de herencia.

> Los cinco principios juntos forman un sistema: no podés aplicar D sin aplicar I, y no podés aplicar O sin aplicar S.

---

## 📚 Referencias

- 📖 *"Agile Software Development: Principles, Patterns, and Practices"* — Robert C. Martin (2002)
- 📖 *"Clean Architecture"* — Robert C. Martin (2017)
- 🌐 [[Hexagonal Architecture]] — aplicación práctica de SOLID
- 🌐 [[Clean Architecture]] — extensión de SOLID a nivel sistema
- 🌐 [[DDD - Domain Driven Design]] — modelado del dominio con principios SOLID

### Nodos especializados
- [[S - Single Responsibility]]
- [[O - Open Closed]]
- [[L - Liskov Substitution]]
- [[I - Interface Segregation]]
- [[D - Dependency Inversion]]
- [[Ejercicios]]
