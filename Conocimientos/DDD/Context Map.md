# Context Map (Mapa de Contextos)

> **Definición:** Diagrama que muestra todos los [[Bounded Context|Bounded Contexts]] del sistema y los patrones de relación entre ellos.

Pertenece a: [[DDD - Domain Driven Design]] → Strategic Design

---

## Para qué sirve

Sin Context Map, los equipos se integran de manera ad-hoc y caótica. El mapa hace explícito:

- Qué contextos existen
- Quién depende de quién (upstream/downstream)
- Qué patrón de integración usa cada relación
- Dónde hay riesgo de acoplamiento

## Conceptos base

**Upstream (U):** El que provee. Define el contrato. Sus cambios afectan al downstream.
**Downstream (D):** El que consume. Depende del upstream. Poco poder de negociación.

```
[Contexto A] --U--> [Contexto B] --D-->
```

---

## Los 7 patrones de integración

### Shared Kernel

Dos contextos **comparten un fragmento de código o modelo** explícitamente acordado.

```
[Ventas] ──────── código compartido ──────── [Marketing]
              (modelo de Producto base)
```

- ✅ Evita duplicación en conceptos verdaderamente compartidos
- ⚠️ Cualquier cambio al kernel afecta a ambos equipos → requiere coordinación constante
- Usar cuando: los equipos son muy cercanos y el costo de duplicar es mayor al de coordinar

---

### Customer/Supplier (Cliente/Proveedor)

Relación upstream/downstream donde **el downstream negocia** con el upstream qué necesita.

```
[Inventario] ─U─► [Ventas] ─D─
```

- El upstream (Inventario) escucha las necesidades del downstream (Ventas)
- Existe negociación y planificación conjunta
- El downstream puede influir en el roadmap del upstream
- Más sano que Conformist

---

### Conformist

El downstream **acepta el modelo del upstream sin negociación**. Se adapta a lo que existe.

```
[API de pagos externa] ─U─► [Tu sistema] ─D─ (acepta lo que hay)
```

- El upstream no tiene incentivo de cambiar para ti
- El downstream moldea su código al modelo del upstream
- Común con APIs de terceros (Stripe, PayPal, etc.)
- ⚠️ Riesgo: el modelo externo puede contaminar tu dominio → considera ACL

---

### Anticorruption Layer (ACL) — Capa Anticorrupción

Capa de traducción que **protege tu modelo** del modelo externo. Actúa como adaptador.

```
[Sistema legado] ─► [ACL: Traductor] ─► [Tu Bounded Context]
     modelo viejo      convierte          modelo limpio
```

```java
// Sin ACL: tu dominio usa tipos del sistema legado (contaminación)
class Pedido {
    LegacyOrderRecord legacyOrder; // ❌ dependencia directa
}

// Con ACL: el traductor convierte
class PedidoTranslator {
    Pedido fromLegacy(LegacyOrderRecord legacy) {
        return new Pedido(
            new PedidoId(legacy.getOrderNum()),
            new Cliente(legacy.getCustId())
            // ...mapeo limpio
        );
    }
}
```

- Úsalo cuando integras sistemas legados, APIs mal diseñadas, o modelos muy distintos
- El costo es escribir y mantener el traductor
- Vale la pena cuando el modelo externo es inestable o muy diferente al tuyo

---

### Open Host Service (Servicio Anfitrión Abierto)

El contexto upstream **publica una API bien definida** para que cualquier downstream se integre.

```
[Catálogo] ─► REST API documentada ─► [Ventas]
                                   ─► [Marketing]
                                   ─► [App móvil]
```

- El upstream define un protocolo estable y público
- Múltiples consumers sin acoplamiento punto a punto
- Suele combinarse con Published Language

---

### Published Language (Lenguaje Publicado)

**Formato o esquema compartido** bien documentado para la comunicación entre contextos. El "idioma" de la integración.

```
Contexto A ──► [JSON Schema / Protobuf / AsyncAPI] ──► Contexto B
```

Ejemplos:
- JSON Schema para eventos
- Protobuf para gRPC
- OpenAPI/Swagger para REST
- GraphQL schema

Suele acompañar a Open Host Service: el OHS define la API, el Published Language define el formato.

---

### Separate Ways (Caminos Separados)

Dos contextos **deciden no integrarse**. Cada uno resuelve su problema independientemente.

```
[Contexto A]     [Contexto B]
     ↓                ↓
  solución A      solución B
  (no se hablan)
```

- Válido cuando el costo de integración supera el beneficio
- Cada contexto duplica lo que necesita del otro
- No es fracaso — a veces es la decisión más sana

---

### Big Ball of Mud ⚠️

**Antipatrón.** Sistema sin fronteras claras, donde todo está mezclado y acoplado.

```
[Todo mezclado: ventas + soporte + logística + pagos + ...]
         cambiar X rompe Y que rompe Z
```

- No es un patrón a elegir — es lo que DDD intenta evitar/reemplazar
- Si tienes uno, el Context Map te ayuda a identificar dónde poner las fronteras primero

---

## Ejemplo de Context Map completo

```
                    ┌─────────────────┐
                    │    CATÁLOGO     │
                    │   (upstream)    │
                    └────────┬────────┘
                   OHS+PL    │    OHS+PL
              ┌──────────────┴──────────────┐
              ▼                             ▼
     ┌────────────────┐           ┌────────────────┐
     │    VENTAS      │           │   MARKETING    │
     │  (Customer)    │           │  (Conformist)  │
     └───────┬────────┘           └────────────────┘
             │ Customer/Supplier
             ▼
     ┌────────────────┐
     │   LOGÍSTICA    │
     │  (Supplier)    │──── ACL ──── [Sistema legado de rutas]
     └───────┬────────┘
             │ Domain Events
             ▼
     ┌────────────────┐
     │  NOTIFICACIONES│
     └────────────────┘
```

---

## Referencias
- [[DDD - Domain Driven Design]]
- [[Bounded Context]]
- [[Domain Events]]
