# Ports (Puertos)

> **Definición:** Interfaz agnóstica a la tecnología que define un contrato entre la lógica de negocio y el mundo exterior. El puerto no sabe nada de HTTP, SQL, gRPC, colas ni archivos.

Pertenece a: [[Hexagonal Architecture]] → Puertos y Adaptadores

---

## La idea central

Un puerto es un **punto de entrada o salida** del hexágono. Es el "enchufe" donde se conectan los adaptadores. El dominio solo habla con puertos, nunca con adaptadores concretos.

```
    Mundo exterior                    Hexágono
   ┌─────────────┐              ┌─────────────────┐
   │  Adaptador   │────enchufa──►│     PUERTO       │──► Lógica de negocio
   │  (REST/SQL)  │              │   (interfaz)     │
   └─────────────┘              └─────────────────┘
```

> Si cambiás de REST a gRPC, cambiás el adaptador. El puerto es el mismo. El dominio ni se entera.

---

## Los dos tipos de puertos

### Puertos Primarios (Driving Ports) — "Los que me hablan"

Definen **cómo el mundo exterior invoca a la aplicación**. Son la API de casos de uso.

También se llaman:
- **Driving Ports** (ellos "conducen" la aplicación)
- **Input Ports**
- **API del hexágono**

```java
public interface CrearPedidoUseCase {
    Pedido ejecutar(DatosPedido datos);
}

public interface ConsultarPedidoUseCase {
    Pedido ejecutar(PedidoId id);
}

public interface CancelarPedidoUseCase {
    void ejecutar(PedidoId id, MotivoCancelacion motivo);
}
```

```typescript
// TypeScript: Puertos primarios
export interface CrearPedidoUseCase {
  ejecutar(datos: DatosPedido): Pedido;
}

export interface ConsultarPedidoUseCase {
  ejecutar(id: PedidoId): Pedido;
}

export interface CancelarPedidoUseCase {
  ejecutar(id: PedidoId, motivo: MotivoCancelacion): void;
}
```

Características:
- Definen **qué puede hacer** la aplicación
- Son interfaces puras, sin anotaciones de framework
- Un puerto primario = un caso de uso
- Se implementan en la capa de aplicación (nunca en el dominio)
- Son el equivalente a los **Casos de Uso** en [[Clean Architecture]]

> ⚠️ **No confundir** con controladores REST. El puerto primario es la interfaz; el controlador REST es un adaptador que la invoca.

### Puertos Secundarios (Driven Ports) — "A quienes hablo"

Definen **qué necesita la aplicación del mundo exterior**. Son SPIs (Service Provider Interfaces).

También se llaman:
- **Driven Ports** (la aplicación los "maneja")
- **Output Ports**
- **SPI del hexágono**

```java
public interface PedidoRepository {
    void guardar(Pedido pedido);
    Optional<Pedido> buscarPorId(PedidoId id);
    List<Pedido> buscarPorCliente(ClienteId clienteId);
}

public interface NotificacionService {
    void enviar(Email email);
}

public interface PagoGateway {
    ResultadoPago procesar(Pago pago);
}
```

```typescript
// TypeScript: Puertos secundarios
export interface PedidoRepository {
  guardar(pedido: Pedido): Promise&lt;void&gt;;
  buscarPorId(id: PedidoId): Promise&lt;Pedido | null&gt;;
  buscarPorCliente(clienteId: ClienteId): Promise&lt;Pedido[]&gt;;
}

export interface NotificacionService {
  enviar(email: Email): Promise&lt;void&gt;;
}

export interface PagoGateway {
  procesar(pago: Pago): Promise&lt;ResultadoPago&gt;;
}
```

Características:
- Definen **qué necesita** la aplicación, no **cómo** se obtiene
- Son interfaces definidas por el dominio/aplicación
- La implementación concreta es un [[Adapters|Adaptador]] secundario
- Son exactamente los [[Repositories]] y [[Services]] en DDD
- Permiten invertir la dependencia: el dominio define la interfaz, la infraestructura la implementa

---

## ¿Dónde viven los puertos?

```
com.tuapp
├── domain/                         ← Dominio puro
│   └── model/
│
├── application/                    ← CAPA DONDE VIVEN LOS PUERTOS
│   ├── port/
│   │   ├── input/                  ← Puertos primarios
│   │   │   ├── CrearPedidoUseCase.java
│   │   │   └── ConsultarPedidoUseCase.java
│   │   └── output/                 ← Puertos secundarios
│   │       ├── PedidoRepository.java
│   │       └── NotificacionService.java
│   └── service/                    ← Implementaciones de puertos primarios
│       └── CrearPedidoService.java
│
└── adapter/                        ← Adaptadores (implementan puertos secundarios)
    └── out/
        └── PostgresPedidoRepository.java
```

> **Regla:** los puertos primarios y secundarios viven en `application/port/`. El dominio no los define (a diferencia de DDD puro), porque un puerto primario es un caso de uso, y un caso de uso orquesta al dominio, no es parte de él.

---

## Principio de Inversión de Dependencias (DIP) en acción

Los puertos secundarios materializan el **Principio de Inversión de Dependencias**:

```
Sin DIP:
   Dominio ──► Infraestructura (el dominio depende de la BD)
   
Con DIP (vía puerto secundario):
   Dominio ──► PedidoRepository (interfaz) ◄── Infraestructura
   (el dominio define la interfaz, la infraestructura la implementa)
```

La flecha de dependencia en tiempo de compilación apunta hacia el dominio. La flecha de flujo en tiempo de ejecución va hacia la infraestructura. **Se invierte la dependencia, no el flujo.**

---

## Cuántos puertos necesitás

No hay un número mágico. Depende de tu aplicación:

| Tipo de app | Puertos primarios | Puertos secundarios |
|---|---|---|
| CRUD simple | 1 genérico | 1 repositorio |
| E-commerce | 10-20 casos de uso | BD, pasarela de pago, email, stock, facturación |
| Microservicio | 2-5 casos de uso | BD, otros microservicios, cola de eventos |

> Si tu app tiene 2 puertos, no dibujes un hexágono. Dibujá un rectángulo con dos lados. Cockburn mismo lo dice.

---

## Referencias

- [[Hexagonal Architecture]]
- [[Adapters]]
- [[Dependency Rule]]
- [[DDD - Domain Driven Design]] → [[Repositories]]
- [[Clean Architecture]]
