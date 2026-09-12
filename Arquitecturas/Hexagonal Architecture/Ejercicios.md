# Ejercicios de Arquitectura Hexagonal

> **Objetivo:** Evaluar la comprensión de los principios fundamentales de la Arquitectura Hexagonal (Ports & Adapters), la regla de dependencia, y su relación con [[DDD - Domain Driven Design|DDD]] y [[Clean Architecture]].

---

## 📝 Ejercicios

### 1. Identificación de Puertos

Dado el siguiente listado de interfaces de una aplicación de e-commerce, clasifícalas como **Puerto Primario (Driving)** o **Puerto Secundario (Driven)**:

| # | Interfaz | Tipo |
|---|---|---|
| a) | `ConfirmarPedidoUseCase` | |
| b) | `PasarelaPagoGateway` | |
| c) | `NotificacionEmailService` | |
| d) | `CancelarSuscripcionUseCase` | |
| e) | `InventarioRepository` | |
| f) | `GenerarFacturaUseCase` | |
| g) | `AlmacenamientoArchivosGateway` | |

---

### 2. La Regla de Dependencia

Observá el siguiente código y respondé:

```java
// Archivo: domain/model/Cliente.java
package com.tienda.domain.model;

import org.springframework.jdbc.core.JdbcTemplate;

public class Cliente {
    private final ClienteId id;
    private final JdbcTemplate jdbc;  // ← ¿esto está bien?

    public void guardar() {
        jdbc.update("INSERT INTO clientes ...", id);
    }
}
```

a) ¿Qué principio de la Arquitectura Hexagonal se está violando?  
b) ¿Cómo corregirías este diseño?

---

### 3. Verdadero o Falso

Respondé si cada afirmación es **Verdadera (V)** o **Falsa (F)**. Justificá en una línea las falsas.

| # | Afirmación | V/F |
|---|---|---|
| a) | Un adaptador primario puede implementar directamente un puerto secundario. | |
| b) | El dominio puede importar clases de la capa `adapter/` siempre que sea a través de una interfaz. | |
| c) | Un mismo puerto secundario puede tener múltiples adaptadores (ej: PostgreSQL y MongoDB). | |
| d) | Los tests unitarios del dominio son un tipo de adaptador primario. | |
| e) | La Arquitectura Hexagonal exige exactamente 6 puertos. | |
| f) | El mapper entre dominio y entidad de BD pertenece al dominio. | |

---

### 4. Diagrama: Completar el flujo

Completá el siguiente diagrama de flujo para una operación `POST /pedidos` escribiendo en cada casilla vacía qué componente de la Arquitectura Hexagonal corresponde:

```
[1. _______________] ──► [2. _______________] ──► [3. _______________]
     ↑                                                   │
     │                                                   ▼
[HTTP Request]                                   [4. Aggregate Root]
                                                         │
                                                    ┌────┘
                                                    ▼
     ┌──────────────────────────────────────────────────┐
     │ [5. _______________] ──► [6. _______________]     │
     │                              │                    │
     │                         [7. INSERT SQL]           │
     └──────────────────────────────────────────────────┘
```

Opciones: Adaptador BD, Puerto Primario, Puerto Secundario, Adaptador REST, Dominio, Caso de Uso.

---

### 5. Anti-patrón: Lógica en el adaptador

¿Qué está mal en este controlador? ¿Dónde debería ir esa validación?

```java
@RestController
public class PedidoController {
    @PostMapping("/pedidos")
    public ResponseEntity<?> crear(@RequestBody PedidoRequest req) {
        if (req.getMontoTotal() < 100) {
            throw new ResponseStatusException(HttpStatus.BAD_REQUEST, "Pedido mínimo $100");
        }
        if (req.getItems().isEmpty()) {
            throw new ResponseStatusException(HttpStatus.BAD_REQUEST, "Pedido sin items");
        }
        // ... crear pedido
    }
}
```

---

### 6. Mapeo de conceptos

Relacioná cada término de la Arquitectura Hexagonal (izquierda) con su equivalente más cercano en Clean Architecture (derecha):

| Hexagonal | Clean Architecture |
|---|---|
| Puerto Primario | |
| Puerto Secundario | |
| Adaptador Primario | |
| Adaptador Secundario | |
| Dominio | |

Opciones: Frameworks & Drivers, Output Port / Gateway, Entities + Use Cases, Controller (Interface Adapters), Input Port, Gateway implementation.

---

### 7. Caso real: Cambio de base de datos

Tu empresa decide migrar de PostgreSQL a MongoDB. Describí en 4-5 pasos qué archivos/capas modificarías y cuáles NO tocarías en una aplicación con Arquitectura Hexagonal.

---

### 8. Diseño de puertos

Una aplicación de reserva de vuelos necesita las siguientes capacidades. Para cada una, indicá si corresponde a un **Puerto Primario**, un **Puerto Secundario**, o **no es un puerto**:

| # | Capacidad | Clasificación |
|---|---|---|
| a) | Un usuario busca vuelos disponibles | |
| b) | El sistema consulta disponibilidad a la aerolínea vía API externa | |
| c) | El sistema guarda la reserva en la base de datos | |
| d) | El sistema envía un email de confirmación | |
| e) | El frontend renderiza los resultados en React | |
| f) | Un administrador cancela una reserva | |

---

### 9. Refactorización guiada

El siguiente código mezcla dominio con infraestructura. Proponé cómo refactorizarlo aplicando Arquitectura Hexagonal (no hace falta escribir código completo, solo describí los componentes nuevos y cómo se relacionan):

```java
public class ReservaService {
    @Autowired private JdbcTemplate jdbc;
    @Autowired private JavaMailSender mailSender;

    public Reserva crearReserva(ReservaRequest req) {
        if (req.getFecha().isBefore(LocalDate.now()))
            throw new RuntimeException("Fecha inválida");

        jdbc.update("INSERT INTO reservas (vuelo_id, pasajero, fecha) VALUES (?, ?, ?)",
            req.getVueloId(), req.getPasajero(), req.getFecha());

        mailSender.send(new SimpleMailMessage() {{
            setTo(req.getPasajeroEmail());
            setSubject("Reserva confirmada");
        }});

        return new Reserva(req.getVueloId(), req.getPasajero(), req.getFecha());
    }
}
```

---

### 10. Múltiple choice

¿Cuál de las siguientes afirmaciones sobre la Arquitectura Hexagonal es **INCORRECTA**?

- a) El dominio no debe depender de frameworks externos.
- b) Un adaptador secundario implementa un puerto secundario definido por la aplicación.
- c) El hexágono debe tener exactamente 6 puertos, uno por cada lado.
- d) Los puertos primarios definen los casos de uso de la aplicación.
- e) Cambiar de REST a gRPC solo requiere cambiar el adaptador primario.

---

### 11. Análisis de dependencias

Dado el siguiente árbol de paquetes, dibujá las flechas de **dependencia permitida** entre capas:

```
com.tienda
├── domain/
├── application/
│   ├── port/input/
│   └── port/output/
├── adapter/
│   ├── in/web/
│   └── out/persistence/
└── config/
```

---

### 12. TypeScript: Corrección de imports

El siguiente archivo TypeScript está en `src/domain/model/Pedido.ts`. Corregí los imports que violan la regla de dependencia:

```typescript
import { Injectable } from "@nestjs/common";
import { Entity, PrimaryColumn, Column } from "typeorm";
import { Request, Response } from "express";
import { PedidoResponse } from "../../adapter/in/web/PedidoResponse";
import { PedidoId } from "./PedidoId";
```

---

### 13. Escenario: Testing

Explicá por qué con Arquitectura Hexagonal podés testear el siguiente caso de uso **sin** levantar PostgreSQL ni Spring Boot:

```java
public class ConfirmarPedidoService implements ConfirmarPedidoUseCase {
    private final PedidoRepository repo;
    public void ejecutar(PedidoId id) {
        Pedido pedido = repo.buscarPorId(id).orElseThrow();
        pedido.confirmar();
        repo.guardar(pedido);
    }
}
```

¿Qué necesitás crear para que el test funcione en milisegundos?

---

### 14. Comparación arquitectónica

Un colega te dice: *"Hexagonal y Clean Architecture son lo mismo, solo cambia el dibujo"*. ¿Estás de acuerdo? Argumentá a favor o en contra usando al menos 3 diferencias o similitudes concretas. Referenciá [[Clean vs Hexagonal]] si lo necesitás.

---

### 15. Diseño integral

Diseñá la arquitectura de un **sistema de préstamos de libros** para una biblioteca universitaria aplicando Arquitectura Hexagonal. Especificá:

a) **3 puertos primarios** (casos de uso)  
b) **3 puertos secundarios** (lo que necesita del exterior)  
c) **2 adaptadores primarios** concretos  
d) **2 adaptadores secundarios** concretos  
e) **1 aggregate root** con al menos 2 invariantes de negocio  
f) **1 domain event** que se dispare al realizar un préstamo  

No hace falta escribir código completo. Describí cada componente en 1-2 líneas.

---

---

# ✅ Respuestas

---

### Respuesta 1

| # | Interfaz | Tipo |
|---|---|---|
| a) | `ConfirmarPedidoUseCase` | **Primario** — caso de uso que dispara la app |
| b) | `PasarelaPagoGateway` | **Secundario** — necesita hablar con un proveedor externo de pagos |
| c) | `NotificacionEmailService` | **Secundario** — necesita enviar emails al exterior |
| d) | `CancelarSuscripcionUseCase` | **Primario** — caso de uso |
| e) | `InventarioRepository` | **Secundario** — necesita persistir/consultar datos |
| f) | `GenerarFacturaUseCase` | **Primario** — caso de uso |
| g) | `AlmacenamientoArchivosGateway` | **Secundario** — necesita guardar archivos (S3, disco) |

---

### Respuesta 2

a) Se está violando la **[[Dependency Rule|Regla de Dependencia]]**: el dominio (`domain/model/Cliente.java`) está importando `JdbcTemplate` de Spring Framework. El dominio no puede depender de infraestructura.

b) **Corrección:**
1. Extraer `JdbcTemplate` del dominio — `Cliente` debe ser un objeto puro sin dependencias de infraestructura.
2. Crear un **puerto secundario** `ClienteRepository` en `application/port/output/` con método `guardar(Cliente cliente)`.
3. Crear un **adaptador secundario** `PostgresClienteRepository` en `adapter/out/persistence/` que implemente `ClienteRepository` usando `JdbcTemplate`.
4. El caso de uso inyecta `ClienteRepository` (la interfaz) y llama a `clienteRepository.guardar(cliente)`.

---

### Respuesta 3

| # | V/F | Justificación (falsas) |
|---|---|---|
| a) | **F** | Un adaptador primario invoca un **puerto primario** (caso de uso). Los puertos secundarios son implementados por adaptadores secundarios. |
| b) | **F** | El dominio no puede importar nada de `adapter/` ni de infraestructura. Solo puede depender de sus propios tipos y de interfaces que él mismo define. |
| c) | **V** | |
| d) | **V** | Un test que invoca directamente el puerto primario es un adaptador primario legítimo. Dispara la aplicación sin HTTP. |
| e) | **F** | El hexágono es una metáfora visual. Podés tener 2, 4, 8 puertos — los que tu aplicación necesite. Cockburn mismo lo aclara. |
| f) | **F** | El mapper pertenece al **adaptador** (`adapter/out/persistence/`). El dominio no sabe que existe una BD ni cómo se traduce a ella. |

---

### Respuesta 4

```
[1. Adaptador REST] ──► [2. Puerto Primario] ──► [3. Caso de Uso]
     ↑                                                   │
     │                                                   ▼
[HTTP Request]                                   [4. Aggregate Root]
                                                         │
                                                    ┌────┘
                                                    ▼
     ┌──────────────────────────────────────────────────┐
     │ [5. Puerto Secundario] ──► [6. Adaptador BD]      │
     │                              │                    │
     │                         [7. INSERT SQL]           │
     └──────────────────────────────────────────────────┘
```

El flujo correcto es: `HTTP Request → Adaptador REST → Puerto Primario → Caso de Uso → Dominio → Puerto Secundario → Adaptador BD → SQL`.

---

### Respuesta 5

**Problema:** El Controller contiene **lógica de negocio**: validar monto mínimo y que el pedido no esté vacío. Esto viola el principio de que los adaptadores son solo traductores e invocadores.

**Dónde debería ir:**
- La validación `pedido sin items` pertenece al **Aggregate Root** (`Pedido`) en el método `confirmar()` o al constructor/factory.
- La validación `monto mínimo $100` pertenece al dominio: [[Value Objects|Value Object]] `Dinero` o al Aggregate `Pedido`.

```java
// ✅ En el dominio (domain/model/Pedido.java)
public class Pedido {
    private static final Dinero MONTO_MINIMO = new Dinero(100, "MXN");

    public void confirmar() {
        if (lineas.isEmpty()) throw new DomainException("Pedido sin items");
        if (calcularTotal().esMenorQue(MONTO_MINIMO)) throw new DomainException("Pedido mínimo $100");
        this.estado = EstadoPedido.CONFIRMADO;
    }
}
```

---

### Respuesta 6

| Hexagonal | Clean Architecture |
|---|---|
| Puerto Primario | **Input Port** |
| Puerto Secundario | **Output Port / Gateway** |
| Adaptador Primario | **Controller (Interface Adapters)** |
| Adaptador Secundario | **Gateway implementation** |
| Dominio | **Entities + Use Cases** |

---

### Respuesta 7

Pasos para migrar de PostgreSQL a MongoDB:

1. **Crear un nuevo adaptador secundario** `MongoPedidoRepository` que implemente la misma interfaz `PedidoRepository` usando el driver de MongoDB.
2. **Crear un nuevo Mapper** que traduzca entre `Pedido` (dominio) y documentos BSON de MongoDB.
3. **Cambiar la configuración de DI** para que inyecte `MongoPedidoRepository` en lugar de `PostgresPedidoRepository`.
4. **NO tocar** el dominio (`domain/`), los puertos (`application/port/`), los casos de uso (`application/service/`), ni el controlador REST (`adapter/in/web/`).
5. **Testear** que los tests de integración del nuevo adaptador pasan, y que los tests unitarios del dominio (que usan `InMemoryPedidoRepository`) siguen funcionando sin cambios.

---

### Respuesta 8

| # | Capacidad | Clasificación |
|---|---|---|
| a) | Un usuario busca vuelos disponibles | **Primario** (`BuscarVuelosUseCase`) |
| b) | El sistema consulta disponibilidad a la aerolínea vía API externa | **Secundario** (`AerolineaGateway`) |
| c) | El sistema guarda la reserva en la base de datos | **Secundario** (`ReservaRepository`) |
| d) | El sistema envía un email de confirmación | **Secundario** (`NotificacionService`) |
| e) | El frontend renderiza los resultados en React | **No es un puerto** — es externo al hexágono |
| f) | Un administrador cancela una reserva | **Primario** (`CancelarReservaUseCase`) |

---

### Respuesta 9

Refactorización propuesta:

| Componente nuevo | Capa | Responsabilidad |
|---|---|---|
| `Reserva` (Aggregate Root) | `domain/model/` | Reglas de negocio: validar fecha no pasada, invariantes de la reserva |
| `CrearReservaUseCase` (interfaz) | `application/port/input/` | Puerto primario: define el caso de uso |
| `CrearReservaService` | `application/service/` | Implementa el caso de uso, orquesta dominio + puertos |
| `ReservaRepository` (interfaz) | `application/port/output/` | Puerto secundario: abstracción de persistencia |
| `NotificacionService` (interfaz) | `application/port/output/` | Puerto secundario: abstracción de envío de emails |
| `PostgresReservaRepository` | `adapter/out/persistence/` | Adaptador secundario: implementa `ReservaRepository` con JDBC |
| `SmtpNotificacionService` | `adapter/out/notification/` | Adaptador secundario: implementa `NotificacionService` con JavaMail |
| `ReservaController` | `adapter/in/web/` | Adaptador primario: recibe HTTP, traduce, invoca el puerto primario |

**Flujo resultante:** `Controller → CrearPedidoUseCase → Dominio (validar) → ReservaRepository (guardar) → NotificacionService (enviar email)`.

---

### Respuesta 10

La respuesta incorrecta es **c)**.

El hexágono **no** debe tener exactamente 6 puertos. El número 6 es una elección gráfica de Cockburn para poder dibujar múltiples puertos sin saturar el diagrama. Una aplicación real puede tener 2, 3, 8 puertos — los que necesite.

---

### Respuesta 11

```
config ──────► adapter ──────► application ──────► domain
  │               │                  │                 ▲
  │               │                  │                 │
  └───────────────┴──────────────────┴─────────────────┘
         (config conoce todo para cablear dependencias)

Dependencias permitidas:
  domain        → solo java/typescript estándar (sin dependencias externas)
  application   → domain (importa Entities, Value Objects)
  adapter       → application (implementa puertos), domain (usa tipos del dominio)
  config        → todos (cablea beans/providers)

Prohibido:
  domain        → application, adapter, config
  application   → adapter, config
```

---

### Respuesta 12

Archivo corregido `src/domain/model/Pedido.ts`:

```typescript
// ✅ Solo queda lo que pertenece al dominio
import { PedidoId } from "./PedidoId";
import { ClienteId } from "./ClienteId";
import { LineaDePedido } from "./LineaDePedido";
import { Dinero } from "./Dinero";
import { EstadoPedido } from "./EstadoPedido";

// ❌ ELIMINADOS (violan la regla de dependencia):
// import { Injectable } from "@nestjs/common";       → decorador de framework
// import { Entity, PrimaryColumn, Column } from "typeorm"; → ORM
// import { Request, Response } from "express";       → framework web
// import { PedidoResponse } from "../../adapter/..."; → importa de capa externa
```

Los decoradores de NestJS y TypeORM van en entidades separadas dentro de `adapter/`. El dominio debe ser TypeScript puro.

---

### Respuesta 13

Se puede testear sin infraestructura porque `ConfirmarPedidoService` depende de `PedidoRepository`, que es una **interfaz** (puerto secundario). No depende de PostgreSQL, JPA, ni Spring.

Para que el test funcione en milisegundos necesitás:

1. Crear un **fake** `InMemoryPedidoRepository` que implemente `PedidoRepository` usando un `HashMap`.
2. En el test, instanciar `ConfirmarPedidoService` pasándole el fake:
   ```java
   var repo = new InMemoryPedidoRepository();
   var useCase = new ConfirmarPedidoService(repo);
   ```
3. Pre-cargar el fake con un `Pedido` en estado BORRADOR.
4. Ejecutar `useCase.ejecutar(pedidoId)` y verificar que el pedido ahora está CONFIRMADO.

No se necesita Spring Boot, PostgreSQL, ni Docker. El test corre en milisegundos.

---

### Respuesta 14

**No estoy completamente de acuerdo.** Aunque comparten el mismo principio, tienen diferencias concretas:

**Similitudes:**
1. Ambas ponen el dominio en el centro, aislado de infraestructura.
2. Ambas usan inversión de dependencias (interfaces definidas por el dominio).
3. Ambas logran testeabilidad del dominio sin infraestructura real.

**Diferencias:**
1. **Nomenclatura:** Hexagonal habla de "puertos y adaptadores"; Clean habla de "Entities, Use Cases, Interface Adapters, Frameworks".
2. **Nivel de detalle:** Clean Architecture es más prescriptiva (4 círculos con roles específicos, Humble Object Pattern, cruce de fronteras explícito). Hexagonal es más minimalista.
3. **Diagrama:** Hexágono simétrico (todos los lados iguales) vs círculos concéntricos (jerarquía visual de capas).
4. **Énfasis:** Hexagonal enfatiza la **intercambiabilidad de adaptadores**. Clean enfatiza la **regla de dependencia explícita**.
5. **Origen:** Cockburn (2005) diseñó Hexagonal para aplicaciones con puertos bien definidos. Martin (2012) unificó Hexagonal, DCI y BCE en un solo framework.

---

### Respuesta 15

**Sistema de préstamos de libros — Biblioteca Universitaria**

**a) 3 Puertos Primarios (casos de uso):**
- `RealizarPrestamoUseCase` — un estudiante solicita un préstamo de libro
- `DevolverLibroUseCase` — un estudiante devuelve un libro prestado
- `ConsultarMultasUseCase` — consultar multas pendientes de un estudiante

**b) 3 Puertos Secundarios:**
- `PrestamoRepository` — persistir y recuperar préstamos
- `EstudianteGateway` — consultar datos del estudiante (API externa de sistemas académicos)
- `NotificacionService` — enviar email de confirmación o recordatorio de vencimiento

**c) 2 Adaptadores Primarios:**
- `PrestamoRestController` — API REST para que el frontend de la biblioteca realice operaciones
- `PrestamoCliCommand` — comando de terminal para que el bibliotecario procese devoluciones masivas

**d) 2 Adaptadores Secundarios:**
- `PostgresPrestamoRepository` — persistencia en PostgreSQL
- `SendgridNotificacionService` — envío de emails vía SendGrid

**e) Aggregate Root con invariantes:**

`Prestamo` (Aggregate Root):
- Invariante 1: *Un estudiante no puede tener más de 3 libros prestados simultáneamente.*
- Invariante 2: *Si el estudiante tiene multas pendientes, no se puede realizar un nuevo préstamo.*

```java
public class Prestamo {
    private static final int MAX_LIBROS_SIMULTANEOS = 3;

    public void validarPuedePrestar(int librosActuales, boolean tieneMultas) {
        if (librosActuales >= MAX_LIBROS_SIMULTANEOS)
            throw new DomainException("Límite de 3 libros simultáneos alcanzado");
        if (tieneMultas)
            throw new DomainException("El estudiante tiene multas pendientes");
    }
}
```

**f) Domain Event:**

`LibroPrestado` — se dispara cuando un préstamo se registra exitosamente. Contiene: `prestamoId`, `estudianteId`, `libroId`, `fechaPrestamo`, `fechaVencimiento`. Lo consume un handler que actualiza el inventario y envía la notificación.

```java
public class LibroPrestado implements DomainEvent {
    private final PrestamoId prestamoId;
    private final EstudianteId estudianteId;
    private final LibroId libroId;
    private final LocalDate fechaVencimiento;
    private final Instant ocurridoEn = Instant.now();
}
```

---

## 📚 Referencias

- [[Hexagonal Architecture]]
- [[Ports]]
- [[Adapters]]
- [[Dependency Rule]]
- [[Testing]]
- [[Anti-Patterns]]
- [[DDD - Domain Driven Design]]
- [[Clean Architecture]]
