# Dependency Rule (Regla de Dependencia)

> **Definición:** Principio fundamental de la Arquitectura Hexagonal que establece que **todas las dependencias en tiempo de compilación apuntan hacia el centro** (el dominio). El exterior conoce al interior; el interior no conoce al exterior.

Pertenece a: [[Hexagonal Architecture]] → Fundamentos

---

## El enunciado

```
Dependencia →  Adaptadores ──► Puertos (interfaces) ──► Dominio
               ═══════════════════════════════════►
               (dependen de los puertos)
               (nunca al revés)
```

En una frase: **el dominio no importa nada de infraestructura.**

```java
// ❌ ROMPE la regla
import org.springframework.jdbc.core.JdbcTemplate;  // en el dominio

// ✅ CUMPLE la regla
// El dominio solo importa sus propios tipos y puertos (interfaces propias)
```

```typescript
// ❌ ROMPE la regla — import de infraestructura en el dominio
import { PrismaClient } from "@prisma/client";   // NO en domain/

// ✅ CUMPLE la regla
// El dominio solo importa sus propios tipos y puertos
import { Pedido } from "../model/Pedido";
import { PedidoRepository } from "../port/output/PedidoRepository";
```

---

## Las tres capas concéntricas

```
     ┌──────────────────────────────────────┐
     │           ADAPTADORES                │  ← Conocen frameworks, protocolos
     │  ┌────────────────────────────┐      │
     │  │        PUERTOS             │      │  ← Interfaces puras (sin dependencias externas)
     │  │  ┌──────────────────┐      │      │
     │  │  │     DOMINIO       │      │      │  ← Cero dependencias externas
     │  │  │  (no sabe nada    │      │      │     Solo Java/Kotlin/TS estándar
     │  │  │   del exterior)   │      │      │
     │  │  └──────────────────┘      │      │
     │  └────────────────────────────┘      │
     └──────────────────────────────────────┘

   ─────────────── Dependencia ───────────────►
   (exterior depende del interior)
```

| Capa | Qué conoce | Qué NO conoce |
|---|---|---|
| **Dominio** | Entities, Value Objects, Domain Services, Domain Events | HTTP, SQL, Spring, colas, archivos |
| **Puertos** | Interfaces puras, tipos del dominio | Implementaciones concretas |
| **Adaptadores** | Frameworks, protocolos, drivers de BD | — (es la capa externa) |

---

## Cómo se verifica en el build

La prueba de fuego: **el módulo/jar del dominio no debe tener dependencias de infraestructura.**

```xml
<!-- build.gradle o pom.xml del módulo domain -->
dependencies {
    // ✅ Solo librerías de utilidad pura
    // ❌ NADA de Spring Boot, JPA, Jackson, HTTP clients
}
```

```json
// TypeScript: package.json del módulo domain
{
  "name": "@empresa/pedido-domain",
  "dependencies": {
    // ✅ Solo utilidades puras (sin side-effects, sin I/O)
    // ❌ NADA de @nestjs/core, @prisma/client, express, class-validator
  }
}
```

Si el dominio compila sin `spring-boot-starter-web`, sin `hibernate-core`, sin `jackson-databind`... la regla de dependencia se cumple.

---

## Inversión de Dependencias (DIP) aplicado

La regla de dependencia es una aplicación directa del **Principio de Inversión de Dependencias** (la D de SOLID):

> *"Los módulos de alto nivel no deben depender de módulos de bajo nivel. Ambos deben depender de abstracciones."*

```
SIN inversión:
   PedidoService ──► JdbcTemplate  (alto nivel depende de bajo nivel)

CON inversión (Hexagonal):
   PedidoService ──► PedidoRepository (abstracción) ◄── JdbcPedidoRepository
                                                          (implementa)
```

El truco: **la abstracción la define el que la usa** (el dominio), no el que la implementa (infraestructura). Esto invierte la flecha de dependencia.

---

## Flujo en runtime vs dependencia en compilación

Un punto que genera confusión:

```
Tiempo de COMPILACIÓN (dependencia):
   Infrastructure ──► PedidoRepository (interface en domain)
   Infrastructure ──► Pedido (clase en domain)
   Domain NO conoce Infrastructure

Tiempo de EJECUCIÓN (flujo de control):
   Controller ──► UseCase ──► Pedido ──► PedidoRepository ──► BD
                                           ↑
                                     La interfaz está en domain,
                                     pero en runtime se ejecuta
                                     la implementación de infrastructure
```

> En compilación, infrastructure depende de domain. En ejecución, el flujo va de controller a BD. Son dos grafos distintos.

---

## Rompiendo la regla sin darse cuenta

### Caso 1: Anotaciones de framework en el dominio

```java
// ❌ El dominio conoce JPA
@Entity
@Table(name = "pedidos")
public class Pedido {
    @Id
    @Column(name = "id")
    private UUID id;
}
```

Solución: [[Anti-Patterns#1. Entidades de BD en el dominio|entidad JPA separada en el adaptador]].

### Caso 2: DTOs de la API en el dominio

```java
// ❌ El dominio conoce el formato de la API
public class Pedido {
    @JsonProperty("order_id")  // anotación de serialización en el dominio
    private PedidoId id;
}
```

### Caso 3: Excepciones de infraestructura en el dominio

```java
// ❌ El dominio lanza excepciones de BD
if (pedido == null) {
    throw new SQLException("Pedido no encontrado");  // ¡SQL en el dominio!
}
```

```typescript
// ❌ TypeScript: Decoradores de ORM en el dominio
import { Entity, Column, PrimaryColumn } from "typeorm";

@Entity("pedidos")
export class Pedido {
  @PrimaryColumn()
  id: string;

  @Column({ name: "cliente_id" })
  clienteId: string;

  @Column({ type: "decimal" })
  total: number;  // ¡El dominio conoce TypeORM!
}
```

---

## Beneficios visibles

| Si cumplís la regla... | Si la rompés... |
|---|---|
| Cambiás de BD sin tocar el dominio | Reescribís el dominio si cambia la BD |
| Testeás el dominio en milisegundos | Necesitás Docker con PostgreSQL para testear |
| El dominio sobrevive a cambios de framework | Migrar de Spring a Quarkus es reescribir todo |
| Un dev nuevo entiende el negocio sin saber infraestructura | Imposible separar qué es negocio y qué es framework |

---

## Referencias

- [[Hexagonal Architecture]]
- [[Ports]]
- [[Adapters]]
- [[Clean Architecture]] — misma regla, formalizada como "The Dependency Rule"
- [[DDD - Domain Driven Design]] → [[Layered Architecture]]
