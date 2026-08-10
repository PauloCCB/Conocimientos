# Package Structure (Estructura de Paquetes)

> **Definición:** Organización física del código fuente que refleja la Arquitectura Hexagonal. Cada paquete tiene un rol explícito y las dependencias entre paquetes respetan la [[Dependency Rule|regla de dependencia]].

Pertenece a: [[Hexagonal Architecture]] → Implementación

---

## La estructura canónica

```
com.tuapp
│
├── domain/                    ← CENTRO. Sin dependencias externas.
│   ├── model/
│   │   ├── Pedido.java            ← Aggregate Root / Entity
│   │   ├── PedidoId.java          ← Value Object (ID tipado)
│   │   ├── LineaDePedido.java     ← Entity interna del aggregate
│   │   ├── LineaId.java           ← Value Object
│   │   ├── ClienteId.java         ← Value Object (referencia a otro aggregate)
│   │   ├── ProductoId.java        ← Value Object
│   │   ├── Cantidad.java          ← Value Object
│   │   ├── Dinero.java            ← Value Object
│   │   ├── Email.java             ← Value Object
│   │   └── EstadoPedido.java      ← Value Object (enum enriquecido)
│   ├── service/
│   │   ├── CalcularImpuestoService.java   ← Domain Service
│   │   └── PoliticaDescuentoService.java  ← Domain Service
│   ├── event/
│   │   ├── PedidoCreado.java      ← Domain Event
│   │   ├── PedidoConfirmado.java
│   │   └── PedidoCancelado.java
│   └── exception/
│       └── DomainException.java   ← Excepción de negocio
│
├── application/               ← CAPA DE APLICACIÓN (Casos de uso + Puertos)
│   ├── port/
│   │   ├── input/                  ← Puertos primarios
│   │   │   ├── CrearPedidoUseCase.java
│   │   │   ├── ConsultarPedidoUseCase.java
│   │   │   ├── CancelarPedidoUseCase.java
│   │   │   └── ConfirmarPedidoUseCase.java
│   │   └── output/                 ← Puertos secundarios
│   │       ├── PedidoRepository.java
│   │       ├── ClienteRepository.java
│   │       ├── NotificacionService.java
│   │       ├── PagoGateway.java
│   │       └── EventPublisher.java
│   ├── service/                    ← Implementaciones de puertos primarios
│   │   ├── CrearPedidoService.java
│   │   ├── ConsultarPedidoService.java
│   │   └── ConfirmarPedidoService.java
│   └── dto/                        ← DTOs de aplicación (cruzan la frontera)
│       ├── DatosPedido.java
│       ├── DatosLineaPedido.java
│       └── FiltroPedido.java
│
├── adapter/                    ← ADAPTADORES (fuera del hexágono)
│   ├── in/                         ← Adaptadores primarios (driving)
│   │   ├── web/
│   │   │   ├── PedidoController.java
│   │   │   ├── PedidoResponse.java
│   │   │   └── CrearPedidoRequest.java
│   │   ├── cli/
│   │   │   └── PedidoCommand.java
│   │   └── messaging/
│   │       └── PedidoEventListener.java
│   └── out/                        ← Adaptadores secundarios (driven)
│       ├── persistence/
│       │   ├── PostgresPedidoRepository.java
│       │   ├── PedidoJpaEntity.java
│       │   └── PedidoMapper.java
│       ├── messaging/
│       │   ├── RabbitEventPublisher.java
│       │   └── PedidoCreatedMessage.java
│       ├── payment/
│       │   └── StripePagoGateway.java
│       └── notification/
│           └── SmtpNotificacionService.java
│
└── config/                     ← DI / Inyección de dependencias
    ├── PedidoConfig.java
    ├── PersistenceConfig.java
    └── MessagingConfig.java
```

```typescript
// TypeScript: Estructura equivalente en NestJS / Node.js
src/
├── domain/                       ← CENTRO. Sin dependencias externas.
│   ├── model/
│   │   ├── Pedido.ts                 ← Aggregate Root / Entity
│   │   ├── PedidoId.ts               ← Value Object (ID tipado como branded type)
│   │   ├── LineaDePedido.ts          ← Entity interna
│   │   ├── Cantidad.ts
│   │   ├── Dinero.ts
│   │   └── EstadoPedido.ts           ← Union type / enum enriquecido
│   ├── service/
│   │   ├── CalcularImpuesto.service.ts
│   │   └── PoliticaDescuento.service.ts
│   ├── event/
│   │   ├── PedidoCreado.event.ts
│   │   └── PedidoConfirmado.event.ts
│   └── exception/
│       └── Domain.exception.ts
│
├── application/                  ← Puertos + Casos de uso
│   ├── port/
│   │   ├── input/
│   │   │   ├── CrearPedido.use-case.ts       ← Puerto primario
│   │   │   └── ConsultarPedido.use-case.ts
│   │   └── output/
│   │       ├── Pedido.repository.ts          ← Puerto secundario
│   │       └── Notificacion.service.ts
│   ├── service/
│   │   └── CrearPedido.service.ts            ← Implementación caso de uso
│   └── dto/
│       └── DatosPedido.dto.ts
│
├── adapter/                      ← Adaptadores
│   ├── in/
│   │   ├── web/
│   │   │   ├── Pedido.controller.ts          ← Express / NestJS
│   │   │   ├── Pedido.response.ts
│   │   │   └── CrearPedido.request.ts
│   │   └── messaging/
│   │       └── PedidoEventListener.handler.ts
│   └── out/
│       ├── persistence/
│       │   ├── PrismaPedido.repository.ts    ← Implementación con Prisma
│       │   ├── Pedido.prisma-entity.ts
│       │   └── Pedido.mapper.ts
│       ├── messaging/
│       │   └── RabbitEvent.publisher.ts
│       └── notification/
│           └── SendgridNotificacion.service.ts
│
└── config/                       ← DI / Módulos
    ├── Pedido.module.ts
    └── Persistence.module.ts
```

---

## Reglas de dependencia entre paquetes

```
domain ◄── application ◄── adapter ◄── config
  ↑            ↑             ↑
  │            │             └── conoce todo (cablea beans)
  │            └── conoce domain (usa Entities, Value Objects)
  └── no conoce a nadie
```

| Paquete | Importa de... | NUNCA importa de... |
|---|---|---|
| `domain` | java estándar, sí mismo | `application`, `adapter`, Spring, JPA, Jackson |
| `application` | `domain`, java estándar | `adapter`, Spring (excepto quizás `@Service`) |
| `adapter` | `application`, `domain`, frameworks | Solo es consumido por `config` |
| `config` | todos | — |

---

## Variantes de estructura

### Estilo "Package by Feature" (vertical)

Cuando el proyecto es grande, agrupás por caso de uso/feature en vez de por capa:

```
com.tuapp
├── pedido/
│   ├── domain/
│   │   ├── Pedido.java
│   │   ├── PedidoId.java
│   │   └── LineaDePedido.java
│   ├── application/
│   │   ├── CrearPedidoUseCase.java
│   │   ├── PedidoRepository.java       ← puerto secundario
│   │   └── CrearPedidoService.java
│   └── adapter/
│       ├── PedidoController.java
│       └── PostgresPedidoRepository.java
├── factura/
│   ├── domain/
│   ├── application/
│   └── adapter/
└── shared/
    ├── Dinero.java
    └── DomainException.java
```

```typescript
// TypeScript: Package by Feature
src/
├── pedido/
│   ├── domain/
│   │   ├── Pedido.ts
│   │   └── PedidoId.ts
│   ├── application/
│   │   ├── CrearPedido.use-case.ts
│   │   └── Pedido.repository.ts
│   └── adapter/
│       ├── Pedido.controller.ts
│       └── PrismaPedido.repository.ts
├── factura/
│   ├── domain/
│   ├── application/
│   └── adapter/
└── shared/
    ├── Dinero.ts
    └── Domain.exception.ts
```

> Este estilo escala mejor en equipos grandes, pero requiere disciplina para no acoplar features entre sí.

### Estilo "Módulos separados" (Gradle/Maven)

La máxima protección de la [[Dependency Rule|regla de dependencia]] se logra con módulos de build separados:

```
pedido-domain/          ← build.gradle sin dependencias externas
pedido-application/     ← build.gradle depende de pedido-domain
pedido-adapter-in/      ← build.gradle depende de pedido-application
pedido-adapter-out/     ← build.gradle depende de pedido-application
pedido-boot/            ← build.gradle depende de todos (main class, config)
```

Beneficio: el compilador **impide** que `pedido-domain` importe una clase de `pedido-adapter-out`. La regla de dependencia deja de ser una convención y pasa a ser una restricción del build.

---

## Cómo nombrar las interfaces (puertos)

Hay dos escuelas. Elegí una y sé consistente:

### Escuela 1: nombre descriptivo del rol

```java
// Puerto primario
public interface CrearPedidoUseCase { }

// Puerto secundario
public interface PedidoRepository { }
```

> Clara, legible. No indica que es un puerto en el nombre.

### Escuela 2: sufijo explícito

```java
// Puerto primario
public interface CrearPedidoInputPort { }
public interface CrearPedidoDrivingPort { }

// Puerto secundario
public interface PedidoPersistenceOutputPort { }
public interface NotificacionDrivenPort { }
```

> Explícita para newcomers. Verbosa.

---

## Referencias

- [[Hexagonal Architecture]]
- [[Dependency Rule]]
- [[Ports]]
- [[Adapters]]
- [[DDD - Domain Driven Design]] → [[Layered Architecture]]
