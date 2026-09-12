# Ejercicios de Principios SOLID

> **Objetivo:** Evaluar la comprensión de los cinco principios SOLID de diseño orientado a objetos y su aplicación en TypeScript, incluyendo su relación con [[Hexagonal Architecture]].

---

## 📝 Ejercicios

### 1. SRP — Refactorizar clase "Dios"

Dada la siguiente clase `PedidoManager` que concentra múltiples responsabilidades, separala en clases con una sola responsabilidad. Indicá qué clases nuevas crearías y qué responsabilidad tendría cada una.

```typescript
class PedidoManager {
  validar(pedido: Pedido): boolean {
    if (!pedido.items.length) throw new Error("Pedido sin items");
    if (pedido.montoTotal <= 0) throw new Error("Monto inválido");
    return true;
  }

  async guardarEnBD(pedido: Pedido): Promise<void> {
    const db = await mysql.createConnection({ host: "localhost", user: "root", database: "tienda" });
    await db.execute("INSERT INTO pedidos (id, monto, estado) VALUES (?, ?, ?)", [
      pedido.id, pedido.montoTotal, pedido.estado,
    ]);
    await db.end();
  }

  enviarEmailConfirmacion(pedido: Pedido): void {
    const transport = nodemailer.createTransport({ host: "smtp.mailtrap.io", port: 2525 });
    transport.sendMail({
      from: "tienda@example.com",
      to: pedido.emailCliente,
      subject: "Pedido confirmado",
      text: `Tu pedido #${pedido.id} ha sido confirmado.`,
    });
  }

  calcularDescuento(pedido: Pedido): number {
    if (pedido.montoTotal > 1000) return pedido.montoTotal * 0.1;
    if (pedido.montoTotal > 500) return pedido.montoTotal * 0.05;
    return 0;
  }

  calcularImpuestos(pedido: Pedido): number {
    return pedido.montoTotal * 0.21;
  }
}
```

**Preguntas:**
a) ¿Cuántas responsabilidades distintas identificás en `PedidoManager`?  
b) Proponé al menos 4 clases nuevas y describí la responsabilidad de cada una.  
c) ¿Cómo afecta esta refactorización a la testeabilidad del código?

---

### 2. SRP — Detectar violaciones

Observá el siguiente fragmento TypeScript de un `UsuarioController` y marcá con un círculo conceptual (indicándolas por nombre) las violaciones de SRP. Explicá por qué cada una es una violación.

```typescript
class UsuarioController {
  async registrar(req: Request, res: Response): Promise<void> {
    const { nombre, email, password } = req.body;

    // Validación
    if (!email.includes("@")) {
      res.status(400).json({ error: "Email inválido" });
      return;
    }
    if (password.length < 8) {
      res.status(400).json({ error: "Contraseña muy corta" });
      return;
    }

    // Persistencia directa
    const db = new Database();
    await db.query("INSERT INTO usuarios (nombre, email, password) VALUES (?, ?, ?)", [
      nombre, email, password,
    ]);

    // Formateo de respuesta
    const respuesta = {
      success: true,
      data: { nombre, email, registradoEn: new Date().toISOString() },
    };

    // Envío de email
    await EmailService.send(email, "Bienvenido", `Hola ${nombre}, gracias por registrarte.`);

    // Logging
    console.log(`[INFO] Usuario ${email} registrado exitosamente`);

    res.status(201).json(respuesta);
  }
}
```

**Preguntas:**
a) Identificá todas las responsabilidades que mezcla este método.  
b) ¿Cuál es el impacto en mantenibilidad de tener todo junto?  
c) ¿Aplicarías algún otro principio SOLID además de SRP para mejorar este código?

---

### 3. OCP — Multiple choice

¿Cuál es la mejor forma de añadir un nuevo tipo de envío (Express, Normal, SameDay) al siguiente `CalculadorEnvio` sin modificar su código fuente, siguiendo el Principio de Abierto/Cerrado?

```typescript
class CalculadorEnvio {
  calcular(pedido: Pedido, tipo: string): number {
    if (tipo === "normal") {
      return pedido.peso * 5;
    } else if (tipo === "express") {
      return pedido.peso * 10 + 50;
    } else if (tipo === "sameDay") {
      return 200;
    }
    throw new Error("Tipo de envío no soportado");
  }
}
```

a) Agregar un `if/else` más al `CalculadorEnvio`.  
b) Crear una interfaz `EstrategiaEnvio` con clases `EnvioNormal`, `EnvioExpress`, `EnvioSameDay`.  
c) Reescribir `CalculadorEnvio` desde cero cada vez que se necesite un nuevo tipo de envío.  
d) Poner la lógica en un `switch` dentro de la clase `Pedido`.

**Justificá por qué la opción elegida es superior a las demás.**

---

### 4. OCP — Refactorizar switch

El siguiente `switch` aplica descuentos según el tipo de cliente. Refactorizalo aplicando el Principio de Abierto/Cerrado mediante el patrón Strategy.

```typescript
class DescuentoService {
  calcular(tipoCliente: string, monto: number): number {
    switch (tipoCliente) {
      case "regular":
        return monto * 0.05;
      case "vip":
        return monto * 0.15;
      case "corporativo":
        return monto * 0.25;
      default:
        return 0;
    }
  }
}
```

**Preguntas:**
a) Escribí la interfaz y las clases nuevas que crearías.  
b) ¿Dónde se decide qué estrategia de descuento usar?  
c) Si mañana aparece `tipoCliente = "black"` con 30% de descuento, ¿qué archivos modificás?

---

### 5. LSP — Verdadero/Falso

Para cada afirmación, respondé **Verdadero (V)** o **Falso (F)**. Justificá en una línea las falsas.

| # | Afirmación | V/F |
|---|---|---|
| a) | Si `Cuadrado extiende Rectangulo` y `Rectangulo` tiene `setAncho()` y `setAlto()`, la herencia es correcta según LSP. | |
| b) | Si una subclase lanza un `Error` en un método que la superclase no declara lanzar, se viola LSP. | |
| c) | Una subclase puede fortalecer las precondiciones de un método y seguir cumpliendo LSP. | |
| d) | Si `PatoPlastico extiende Pato` y `volar()` lanza `Error`, pero `nadar()` y `hacerCuac()` funcionan, no hay violación de LSP porque la mayoría de los métodos andan. | |
| e) | LSP garantiza que el código que usa una abstracción funcione correctamente con cualquier implementación concreta. | |

---

### 6. LSP — Detectar violación

Dado el siguiente código TypeScript, explicá por qué viola el Principio de Sustitución de Liskov y proponé una solución usando una interfaz `Cancelable`.

```typescript
class Pedido {
  constructor(public readonly id: string, public estado: string) {}

  cancelar(): void {
    if (this.estado === "entregado") throw new Error("No se puede cancelar un pedido entregado");
    this.estado = "cancelado";
  }
}

class PedidoExpress extends Pedido {
  cancelar(): void {
    throw new Error("Los pedidos express no se pueden cancelar");
  }
}

// En algún lugar del código:
function procesarCancelacion(pedidos: Pedido[]): void {
  for (const p of pedidos) {
    p.cancelar(); // 💥 ¿qué pasa con los PedidoExpress?
  }
}
```

**Preguntas:**
a) ¿Por qué `PedidoExpress` viola LSP?  
b) Escribí la interfaz `Cancelable` y cómo se modificarían ambas clases.  
c) ¿Cómo cambiaría la función `procesarCancelacion`?

---

### 7. LSP — Diseño correcto

Diseñá una jerarquía para un sistema de membresías de un gimnasio con las siguientes clases:

- `MembresiaBasica`: incluye `accederAGimnasio()`, `accederADuchas()`
- `MembresiaPremium`: incluye todos los métodos de Básica más `accederASauna()`, `accederAClasesGrupales()`, `usarEntrenadorPersonal()`
- `MembresiaGratuita`: solo incluye `accederAGimnasio()` (sin `accederADuchas()`)

**Preguntas:**
a) ¿Por qué extender `MembresiaPremium extends MembresiaBasica` no viola LSP?  
b) ¿Por qué extender `MembresiaGratuita extends MembresiaBasica` SÍ violaría LSP?  
c) Diseñá la jerarquía de interfaces y clases sin violar LSP.

---

### 8. ISP — Partir interfaz gorda

Dada la interfaz `IRepositorio<T>`, particionala en interfaces segregadas para que un `RepositorioSoloLectura` solo implemente lo que necesita.

```typescript
interface IRepositorio<T> {
  crear(entidad: T): Promise<void>;
  leer(id: string): Promise<T | null>;
  actualizar(entidad: T): Promise<void>;
  eliminar(id: string): Promise<void>;
  exportar(formato: "csv" | "json"): Promise<Buffer>;
  importar(data: Buffer): Promise<T[]>;
  buscar(criterios: Record<string, unknown>): Promise<T[]>;
  filtrar(predicado: (entidad: T) => boolean): Promise<T[]>;
  subirArchivo(archivo: Buffer, nombre: string): Promise<string>;
  auditar(operacion: string): Promise<void>;
}
```

**Preguntas:**
a) Diseñá al menos 3 interfaces segregadas y mostrá qué métodos agrupa cada una.  
b) ¿Cuáles de esas interfaces implementaría un `RepositorioSoloLectura`?  
c) ¿Qué principio adicional de SOLID se relaciona con ISP en este contexto?

---

### 9. ISP — Diseñar puertos

En [[Hexagonal Architecture]], un caso de uso `ConsultarPedidoUseCase` solo necesita leer pedidos. Diseñá la interfaz mínima que debería tener como puerto secundario.

**Preguntas:**
a) Escribí la interfaz `PedidoLecturaRepository` que usaría `ConsultarPedidoUseCase`.  
b) ¿El caso de uso `CancelarPedidoUseCase` necesita la misma interfaz? ¿Por qué?  
c) ¿Cómo se vincula este diseño con el Principio de Segregación de Interfaces?

---

### 10. DIP — Invertir dependencia

El siguiente código viola el Principio de Inversión de Dependencias. Refactorizalo para invertir la dependencia.

```typescript
import { PrismaClient } from "@prisma/client";

class PedidoService {
  private repository: PostgresPedidoRepository;

  constructor() {
    this.repository = new PostgresPedidoRepository(); // 💥 acoplamiento directo
  }

  async confirmarPedido(id: string): Promise<Pedido> {
    const pedido = await this.repository.buscarPorId(id);
    if (!pedido) throw new Error("Pedido no encontrado");
    pedido.confirmar();
    await this.repository.guardar(pedido);
    return pedido;
  }
}

class PostgresPedidoRepository {
  private prisma = new PrismaClient();

  async buscarPorId(id: string): Promise<Pedido | null> {
    return this.prisma.pedido.findUnique({ where: { id } });
  }

  async guardar(pedido: Pedido): Promise<void> {
    await this.prisma.pedido.upsert({ where: { id: pedido.id }, update: pedido, create: pedido });
  }
}
```

**Preguntas:**
a) Escribí la interfaz que define el contrato.  
b) ¿Cómo modificarías `PedidoService` para recibir la dependencia por inyección?  
c) ¿En qué capa de [[Hexagonal Architecture]] vive la interfaz y en cuál vive la implementación?

---

### 11. DIP — Identificar violación

Marcá en el siguiente fragmento qué imports violan DIP y explicá por qué.

```typescript
// Archivo: src/application/use-cases/CancelarSuscripcionUseCase.ts

import { Stripe } from "stripe";
import { PrismaClient } from "@prisma/client";
import { Suscripcion } from "../../domain/model/Suscripcion";
import { SuscripcionId } from "../../domain/model/SuscripcionId";
import { SuscripcionRepository } from "../../domain/ports/SuscripcionRepository";

export class CancelarSuscripcionUseCase {
  private stripe = new Stripe(process.env.STRIPE_SECRET!);
  private prisma = new PrismaClient(); // 💥

  async ejecutar(suscripcionId: string): Promise<void> {
    const suscripcion = await this.prisma.suscripcion.findUnique({ where: { id: suscripcionId } });
    await this.stripe.subscriptions.cancel(suscripcion.stripeId);
    await this.prisma.suscripcion.update({ where: { id: suscripcionId }, data: { estado: "cancelada" } });
  }
}
```

**Preguntas:**
a) Identificá todos los imports o usos que violan DIP. ¿Por qué?  
b) Proponé los puertos secundarios que abstraerían esas dependencias.  
c) ¿Qué capa de la arquitectura se contamina si dejamos este código como está?

---

### 12. DIP — Conectar con Hexagonal

Explicá cómo los [[Ports]] de [[Hexagonal Architecture]] materializan el Principio de Inversión de Dependencias. Dado el siguiente escenario, desarrollá un ejemplo concreto:

_Un caso de uso `GenerarFacturaUseCase` necesita obtener datos de cliente desde un servicio externo (API REST de ERP) y persistir la factura generada._

**Preguntas:**
a) ¿Qué puertos secundarios definirías? Escribí las interfaces.  
b) ¿Dónde se definen esas interfaces? ¿Dónde viven las implementaciones?  
c) Dibujá la dirección de la dependencia (código fuente) vs. la dirección del flujo de control en tiempo de ejecución.

---

### 13. Multiple choice general

¿Cuál de las siguientes NO es una consecuencia típica de aplicar los principios SOLID?

a) El código es más fácil de testear porque las dependencias se pueden mockear.  
b) El código tiene menos archivos, lo que facilita la navegación del proyecto.  
c) El código acepta cambios de requerimientos sin romper funcionalidad existente.  
d) El código se puede extender con nuevas funcionalidades sin modificar lo ya escrito.  

**Justificá tu respuesta.**

---

### 14. Refactorización integral

Dada la siguiente clase `GestorFacturas` de ~60 líneas que mezcla validación, cálculos, persistencia y envío de emails, refactorizala aplicando **al menos 3** de los principios SOLID. Describí los cambios propuestos.

```typescript
import { createTransport } from "nodemailer";
import { Database } from "./Database";
import { Factura } from "./Factura";

class GestorFacturas {
  private db: Database;
  private mailer = createTransport({ host: "smtp.empresa.com", port: 587 });

  constructor() {
    this.db = new Database();
  }

  async procesar(factura: Factura): Promise<void> {
    if (!factura.clienteId) throw new Error("Factura sin cliente");
    if (factura.lineas.length === 0) throw new Error("Factura sin líneas");
    if (factura.fecha > new Date()) throw new Error("Fecha futura no permitida");

    let subtotal = 0;
    for (const linea of factura.lineas) {
      subtotal += linea.cantidad * linea.precioUnitario;
    }

    let descuento = 0;
    if (factura.clienteCategoria === "vip") {
      descuento = subtotal * 0.15;
    } else if (factura.clienteCategoria === "regular") {
      descuento = subtotal * 0.05;
    }

    const iva = (subtotal - descuento) * 0.21;
    const total = subtotal - descuento + iva;

    factura.subtotal = subtotal;
    factura.descuento = descuento;
    factura.iva = iva;
    factura.total = total;

    await this.db.query("INSERT INTO facturas (id, cliente_id, total, fecha) VALUES (?, ?, ?, ?)", [
      factura.id, factura.clienteId, total, factura.fecha,
    ]);

    await this.mailer.sendMail({
      from: "facturacion@empresa.com",
      to: factura.emailCliente,
      subject: `Factura #${factura.id}`,
      text: `Se ha emitido la factura por un total de $${total}.`,
    });
  }
}
```

**Preguntas:**
a) ¿Qué 3 principios SOLID aplicaste primero? ¿Por qué?  
b) Describí la nueva estructura: ¿qué clases e interfaces nuevas aparecen?  
c) Si mañana el cálculo de IVA cambia del 21% al 19%, ¿cuántas clases necesitás modificar?

---

### 15. Diseño desde cero

_"Una aplicación de reserva de canchas de fútbol necesita: buscar canchas disponibles por fecha/hora, reservar una cancha, cancelar una reserva, enviar confirmación por email al usuario, y persistir las reservas en una base de datos."_

Diseñá las clases e interfaces aplicando **los 5 principios SOLID** y [[Hexagonal Architecture]]. No hace falta código completo, solo nombres de clases/interfaces y cómo se relacionan.

**Especificá:**
a) Al menos 2 puertos primarios (casos de uso).  
b) Al menos 3 puertos secundarios (interfaces).  
c) Cómo aplicarías SRP en la capa de dominio.  
d) Cómo aplicarías OCP si mañana se agrega "reservar torneo" (reserva de múltiples canchas).  
e) Cómo segregarías interfaces (ISP) para que el handler de emails solo vea lo que necesita.  
f) Cómo aplicarías DIP para que el dominio no dependa de `PrismaClient`, `nodemailer` ni `express`.

---

---

# ✅ Respuestas

---

### Respuesta 1 — SRP: Refactorizar clase "Dios"

a) `PedidoManager` tiene **5 responsabilidades** distintas: (1) validación de reglas de negocio, (2) persistencia en base de datos, (3) envío de emails, (4) cálculo de descuentos, (5) cálculo de impuestos.

b) Nuevas clases propuestas:

| Clase | Responsabilidad |
|---|---|
| `PedidoValidator` | Validar invariantes del pedido (items no vacío, monto positivo) |
| `PedidoRepository` (interfaz) + `MySQLPedidoRepository` | Abstraer y ejecutar persistencia |
| `NotificacionService` (interfaz) + `EmailNotificacionService` | Enviar notificaciones por email |
| `CalculadoraDescuento` | Calcular descuentos según reglas de negocio |
| `CalculadoraImpuestos` | Calcular impuestos aplicables |

c) La testeabilidad mejora drásticamente porque cada clase puede testearse de forma aislada con mocks de sus dependencias. Antes, testear el descuento requería una conexión MySQL activa. Ahora se puede testear `CalculadoraDescuento` con un test unitario puro sin infraestructura.

```typescript
// Ejemplo de la nueva estructura
class PedidoValidator {
  validar(pedido: Pedido): void {
    if (!pedido.items.length) throw new Error("Pedido sin items");
    if (pedido.montoTotal <= 0) throw new Error("Monto inválido");
  }
}

interface PedidoRepository {
  guardar(pedido: Pedido): Promise<void>;
}

interface CalculadoraDescuento {
  calcular(pedido: Pedido): number;
}
```

Esto se alinea con [[Hexagonal Architecture]]: cada responsabilidad extraída puede convertirse en un puerto (primario para casos de uso, secundario para infraestructura como persistencia y notificaciones).

---

### Respuesta 2 — SRP: Detectar violaciones

a) El método `registrar` mezcla al menos **5 responsabilidades**:
1. **Validación de datos de entrada** (`email.includes("@")`, `password.length < 8`) — debería estar en un `Value Object` como `Email` o en un validador de dominio.
2. **Persistencia directa en BD** (`new Database()`, `db.query`) — viola SRP y [[Dependency Rule]], debería delegarse a un [[Ports|Puerto Secundario]].
3. **Formateo de respuesta HTTP** — es rol del adaptador, pero mezcla lógica de presentación con negocio.
4. **Envío de email de bienvenida** (`EmailService.send`) — responsabilidad de notificación, debería ser un puerto secundario.
5. **Logging** (`console.log`) — no es crítico pero suma ruido.

b) El impacto en mantenibilidad: si se cambia el proveedor de email (SendGrid → Mailgun), hay que tocar el controlador. Si la validación de contraseña cambia (mínimo 12 caracteres), hay que tocar el controlador. Cada cambio de infraestructura fuerza modificar código que debería ser solo un adaptador.

c) Además de SRP, también aplicaría **DIP** (el controlador depende directamente de `Database` y `EmailService` concretos), e **ISP** (el controlador no debería "ver" métodos que no usa — por ejemplo, `eliminar` usuarios).

[[Hexagonal Architecture]] resuelve esto naturalmente: el controlador sería un [[Adapters|Adaptador Primario]], cada responsabilidad extraída sería un puerto secundario implementado por un adaptador.

---

### Respuesta 3 — OCP: Multiple choice

La respuesta correcta es **b)**.

Crear una interfaz `EstrategiaEnvio` con clases concretas `EnvioNormal`, `EnvioExpress`, `EnvioSameDay` permite añadir nuevos tipos de envío sin modificar `CalculadorEnvio`. Esto es la definición misma de OCP: abierto a extensión, cerrado a modificación.

```typescript
interface EstrategiaEnvio {
  calcularCosto(pedido: Pedido): number;
}

class EnvioNormal implements EstrategiaEnvio {
  calcularCosto(pedido: Pedido): number {
    return pedido.peso * 5;
  }
}

class EnvioExpress implements EstrategiaEnvio {
  calcularCosto(pedido: Pedido): number {
    return pedido.peso * 10 + 50;
  }
}

class EnvioSameDay implements EstrategiaEnvio {
  calcularCosto(pedido: Pedido): number {
    return 200;
  }
}

class CalculadorEnvio {
  private estrategia: EstrategiaEnvio;

  constructor(estrategia: EstrategiaEnvio) {
    this.estrategia = estrategia;
  }

  calcular(pedido: Pedido): number {
    return this.estrategia.calcularCosto(pedido);
  }
}
```

Por qué las otras son incorrectas:
- **a)** Agregar `if/else` modifica código existente (viola OCP).
- **c)** Reescribir desde cero es absurdo y no escala.
- **d)** Poner lógica en `Pedido` viola SRP y acopla el dominio a lógica de infraestructura.

---

### Respuesta 4 — OCP: Refactorizar switch

a) Interfaz y clases nuevas:

```typescript
interface EstrategiaDescuento {
  aplicar(monto: number): number;
}

class DescuentoRegular implements EstrategiaDescuento {
  aplicar(monto: number): number {
    return monto * 0.05;
  }
}

class DescuentoVIP implements EstrategiaDescuento {
  aplicar(monto: number): number {
    return monto * 0.15;
  }
}

class DescuentoCorporativo implements EstrategiaDescuento {
  aplicar(monto: number): number {
    return monto * 0.25;
  }
}

class DescuentoService {
  private estrategia: EstrategiaDescuento;

  constructor(estrategia: EstrategiaDescuento) {
    this.estrategia = estrategia;
  }

  calcular(monto: number): number {
    return this.estrategia.aplicar(monto);
  }
}
```

b) La decisión de qué estrategia usar se toma en la **capa de configuración/DI** (por ejemplo, una factory que mapea `tipoCliente` a la estrategia correspondiente, o el propio mecanismo de inyección de dependencias).

c) Si aparece `tipoCliente = "black"` con 30% de descuento, solo tengo que **crear un archivo nuevo** (`DescuentoBlack.ts`) y registrarlo en la factory/DI. No modifico ningún archivo existente — OCP en acción.

```typescript
class DescuentoBlack implements EstrategiaDescuento {
  aplicar(monto: number): number {
    return monto * 0.30;
  }
}
```

---

### Respuesta 5 — LSP: Verdadero/Falso

| # | Afirmación | V/F | Justificación |
|---|---|---|---|
| a) | `Cuadrado extiende Rectangulo` con `setAncho()` y `setAlto()` | **F** | Es el ejemplo clásico de violación de LSP: en un Cuadrado, `setAncho(5)` también cambia el alto. Un cliente que usa `Rectangulo` espera que ancho y alto sean independientes. El Cuadrado no es sustituible. |
| b) | Subclase lanza `Error` que la superclase no declara | **V** | Lanzar una excepción nueva o más restrictiva que la superclase fortalece las postcondiciones, lo cual viola LSP. El cliente espera que el método funcione sin ese error. |
| c) | Una subclase puede fortalecer precondiciones | **F** | Fortalecer precondiciones significa que la subclase exige MÁS que la superclase (ej: acepta menos valores de entrada). Esto rompe la sustituibilidad: código que funcionaba con la superclase puede fallar con la subclase. |
| d) | `PatoPlastico` que falla en `volar()` no viola LSP porque otros métodos andan | **F** | No importa cuántos métodos funcionen. Si UNO falla, la sustituibilidad está rota. El código que itera `patos.forEach(p => p.volar())` revienta con `PatoPlastico`. |
| e) | LSP garantiza corrección con cualquier implementación concreta | **V** | Esa es exactamente la promesa de LSP: si S es subtipo de T, los objetos de T pueden reemplazarse por objetos de S sin alterar el comportamiento deseable del programa. |

---

### Respuesta 6 — LSP: Detectar violación

a) `PedidoExpress` viola LSP porque fortalece las precondiciones del método `cancelar()`: la superclase `Pedido` permite cancelar (salvo si ya está entregado), pero la subclase **nunca** permite cancelar. La función `procesarCancelacion` recibe un `Pedido[]` y espera que `cancelar()` funcione, pero explota cuando recibe un `PedidoExpress`. Esto rompe la sustituibilidad.

b) Solución con interfaz `Cancelable`:

```typescript
interface Cancelable {
  cancelar(): void;
}

class Pedido implements Cancelable {
  constructor(public readonly id: string, public estado: string) {}

  cancelar(): void {
    if (this.estado === "entregado") throw new Error("No se puede cancelar un pedido entregado");
    this.estado = "cancelado";
  }
}

class PedidoExpress {
  constructor(public readonly id: string, public estado: string) {}

  // No implementa Cancelable — es honesto con sus capacidades

  procesarRapido(): void {
    this.estado = "en_camino";
  }
}
```

c) La función `procesarCancelacion` ahora recibe solo cancelables:

```typescript
function procesarCancelacion(cancelables: Cancelable[]): void {
  for (const c of cancelables) {
    c.cancelar();
  }
}
```

`PedidoExpress` simplemente no se pasa a esa función. El compilador nos protege en tiempo de diseño. Esto refleja cómo [[Hexagonal Architecture]] usa interfaces para definir contratos explícitos entre capas.

---

### Respuesta 7 — LSP: Diseño correcto

a) `MembresiaPremium extends MembresiaBasica` **no** viola LSP porque Premium cumple todas las postcondiciones de Básica (`accederAGimnasio()` y `accederADuchas()` funcionan normalmente) y además agrega más capacidades. Es el caso clásico de herencia correcta: el subtipo es un "es-un" genuino.

b) `MembresiaGratuita extends MembresiaBasica` **SÍ** violaría LSP porque Básica tiene `accederADuchas()` y Gratuita no puede ofrecerlo. Si Gratuita extiende Básica, tendría que lanzar `Error` o devolver `false`, rompiendo el contrato.

c) Diseño sin violar LSP usando interfaces segregadas:

```typescript
interface AccesoGimnasio {
  accederAGimnasio(): void;
}

interface AccesoDuchas {
  accederADuchas(): void;
}

interface AccesoSauna {
  accederASauna(): void;
}

interface AccesoClases {
  accederAClasesGrupales(): void;
}

interface EntrenadorPersonal {
  usarEntrenadorPersonal(): void;
}

class MembresiaGratuita implements AccesoGimnasio {
  accederAGimnasio(): void { /* ... */ }
}

class MembresiaBasica implements AccesoGimnasio, AccesoDuchas {
  accederAGimnasio(): void { /* ... */ }
  accederADuchas(): void { /* ... */ }
}

class MembresiaPremium implements AccesoGimnasio, AccesoDuchas, AccesoSauna, AccesoClases, EntrenadorPersonal {
  accederAGimnasio(): void { /* ... */ }
  accederADuchas(): void { /* ... */ }
  accederASauna(): void { /* ... */ }
  accederAClasesGrupales(): void { /* ... */ }
  usarEntrenadorPersonal(): void { /* ... */ }
}
```

Cada tipo implementa exactamente lo que puede cumplir. Esto es LSP + ISP trabajando juntos. Las interfaces pequeñas y cohesivas son clave para no forzar herencias incorrectas.

---

### Respuesta 8 — ISP: Partir interfaz gorda

a) Tres interfaces segregadas:

```typescript
// Operaciones CRUD básicas
interface ILectura<T> {
  leer(id: string): Promise<T | null>;
  buscar(criterios: Record<string, unknown>): Promise<T[]>;
  filtrar(predicado: (entidad: T) => boolean): Promise<T[]>;
}

interface IEscritura<T> {
  crear(entidad: T): Promise<void>;
  actualizar(entidad: T): Promise<void>;
  eliminar(id: string): Promise<void>;
}

// Operaciones de infraestructura/auditoría
interface IExportable<T> {
  exportar(formato: "csv" | "json"): Promise<Buffer>;
  importar(data: Buffer): Promise<T[]>;
}

interface IGestionArchivos {
  subirArchivo(archivo: Buffer, nombre: string): Promise<string>;
}

interface IAuditable {
  auditar(operacion: string): Promise<void>;
}
```

b) Un `RepositorioSoloLectura` implementaría **solo** `ILectura<T>`:

```typescript
class RepositorioSoloLectura<T> implements ILectura<T> {
  async leer(id: string): Promise<T | null> { /* ... */ }
  async buscar(criterios: Record<string, unknown>): Promise<T[]> { /* ... */ }
  async filtrar(predicado: (entidad: T) => boolean): Promise<T[]> { /* ... */ }
}
```

c) ISP y DIP se relacionan fuertemente aquí: cuando un caso de uso depende de interfaces pequeñas (`ILectura<T>` en vez de `IRepositorio<T>`), se cumple DIP (depende de una abstracción) e ISP (depende solo de lo que necesita). En [[Hexagonal Architecture]], cada puerto secundario debería ser una interfaz segregada que exponga solo lo que ese caso de uso requiere — esto se alinea con [[Ports]].

---

### Respuesta 9 — ISP: Diseñar puertos

a) Interfaz `PedidoLecturaRepository`:

```typescript
interface PedidoLecturaRepository {
  buscarPorId(id: string): Promise<Pedido | null>;
  buscarPorCliente(clienteId: string): Promise<Pedido[]>;
  buscarPorFecha(fecha: Date): Promise<Pedido[]>;
}
```

Nada de `guardar`, `actualizar` o `eliminar`. `ConsultarPedidoUseCase` solo necesita leer.

b) `CancelarPedidoUseCase` **NO** necesita la misma interfaz. Este caso de uso necesita leer para buscar el pedido y escribir para cambiar su estado. Necesitaría algo como:

```typescript
interface PedidoCancelacionRepository {
  buscarPorId(id: string): Promise<Pedido | null>;
  guardar(pedido: Pedido): Promise<void>;
}
```

c) Este diseño es ISP aplicado a la capa de aplicación. Cada caso de uso define exactamente qué métodos necesita de sus dependencias, en lugar de depender de una interfaz monolítica `PedidoRepository` con 20 métodos. Esto se conoce como **Role Interfaces** (interfaces definidas por el rol del consumidor, no por el implementador). En [[Hexagonal Architecture]], los [[Ports|Puertos Secundarios]] deberían seguir este principio: cada puerto expone solo lo que el caso de uso requiere, segregado por necesidad.

---

### Respuesta 10 — DIP: Invertir dependencia

a) Interfaz del contrato:

```typescript
interface PedidoRepository {
  buscarPorId(id: string): Promise<Pedido | null>;
  guardar(pedido: Pedido): Promise<void>;
}
```

b) `PedidoService` recibe la dependencia por inyección:

```typescript
class PedidoService {
  private repository: PedidoRepository;

  constructor(repository: PedidoRepository) {
    this.repository = repository;
  }

  async confirmarPedido(id: string): Promise<Pedido> {
    const pedido = await this.repository.buscarPorId(id);
    if (!pedido) throw new Error("Pedido no encontrado");
    pedido.confirmar();
    await this.repository.guardar(pedido);
    return pedido;
  }
}
```

c) En [[Hexagonal Architecture]]:
- La **interfaz** `PedidoRepository` vive en la capa de **aplicación** (`application/port/output/`), definida por el dominio/aplicación según sus necesidades.
- La **implementación** `PostgresPedidoRepository` vive en la capa de **infraestructura/adaptadores** (`adapter/out/persistence/`), implementando la interfaz definida por el dominio.

Esto es la materialización del [[Dependency Rule|Dependency Rule]]: el código fuente apunta hacia adentro (el adaptador depende de la interfaz), pero en tiempo de ejecución el control fluye del dominio hacia el adaptador (Inversión de Dependencias).

---

### Respuesta 11 — DIP: Identificar violación

a) Imports/usos que violan DIP:

| Línea | Violación | Motivo |
|---|---|---|
| `import { Stripe } from "stripe"` | ✅ viola DIP | El caso de uso depende directamente de un SDK externo concreto (Stripe). Debería depender de una abstracción `PasarelaPagoGateway`. |
| `import { PrismaClient } from "@prisma/client"` | ✅ viola DIP | El caso de uso instancia directamente el ORM. Debería depender de `SuscripcionRepository` (interfaz). |
| `new PrismaClient()` | ✅ viola DIP | Instanciación directa de infraestructura dentro de la capa de aplicación. |
| `new Stripe(...)` | ✅ viola DIP | Igual que Prisma, acoplamiento a librería externa. |

El `import` de `SuscripcionRepository` en línea 6 **está bien** — es una abstracción definida por el dominio.

b) Puertos secundarios propuestos:

```typescript
interface SuscripcionRepository {
  buscarPorId(id: string): Promise<Suscripcion | null>;
  actualizarEstado(id: string, estado: string): Promise<void>;
}

interface PasarelaPagoGateway {
  cancelarSuscripcion(suscripcionExternaId: string): Promise<void>;
}
```

c) Si dejamos este código como está, **la capa de aplicación** se contamina con infraestructura. Esto rompe la [[Dependency Rule]] de [[Hexagonal Architecture]]: el uso de `PrismaClient` y `Stripe` hace imposible testear el caso de uso sin levantar servicios externos, y acopla la lógica de negocio a decisiones de infraestructura.

---

### Respuesta 12 — DIP: Conectar con Hexagonal

a) Puertos secundarios necesarios:

```typescript
// Puerto para obtener datos del cliente desde ERP
interface ClienteGateway {
  obtenerCliente(id: string): Promise<DatosCliente>;
}

interface DatosCliente {
  id: string;
  nombre: string;
  email: string;
  direccionFiscal: string;
}

// Puerto para persistir la factura generada
interface FacturaRepository {
  guardar(factura: Factura): Promise<void>;
}
```

b) Las interfaces se definen en **`application/port/output/`** (dentro del dominio/aplicación, hacia adentro del hexágono). Las implementaciones concretas viven en **`adapter/out/`**:
- `ERPClienteGateway` → `adapter/out/gateway/` (llama a la API REST del ERP)
- `PostgresFacturaRepository` → `adapter/out/persistence/` (usa Prisma/SQL)

c) Dirección de dependencias:

```
Código fuente (dependencias en tiempo de compilación):
  adapter/out/ ──────► application/port/output/ (interfaz)
  application/ ──────► domain/
  (las flechas apuntan HACIA ADENTRO)

Flujo de control en tiempo de ejecución:
  Caso de Uso → Puerto Secundario (interfaz) → Adaptador → Infraestructura externa
  (el control SALE del hexágono, pero la dependencia del código apunta HACIA ADENTRO)
```

Esto es la esencia de DIP materializado en [[Hexagonal Architecture]]: los [[Ports|Puertos]] definen el contrato que la aplicación necesita, y los [[Adapters|Adaptadores]] implementan ese contrato invirtiendo la dependencia. Sin DIP, el caso de uso importaría directamente `axios` para llamar al ERP o `PrismaClient` para persistir, rompiendo el aislamiento del dominio.

---

### Respuesta 13 — Multiple choice general

La respuesta incorrecta es **b)**.

**Justificación:** Aplicar SOLID **aumenta** la cantidad de archivos en el proyecto. Cada responsabilidad extraída se convierte en una clase nueva en su propio archivo. Donde antes había una clase `PedidoManager` de 100 líneas, ahora hay 5-6 archivos: `PedidoValidator.ts`, `CalculadoraDescuento.ts`, `CalculadoraImpuestos.ts`, `PedidoRepository.ts` (interfaz), `MySQLPedidoRepository.ts`, `NotificacionService.ts`.

Esto **no** es una desventaja — es una inversión en mantenibilidad:
- Cada archivo tiene un propósito claro y acotado.
- Los cambios están localizados (cambio de cálculo de IVA → 1 archivo).
- La navegación es más predecible porque el nombre del archivo describe exactamente lo que contiene.

---

### Respuesta 14 — Refactorización integral

a) Aplico **SRP**, **OCP** y **DIP** primero:

- **SRP**: `GestorFacturas` tiene 4 responsabilidades: validación, cálculo de totales, persistencia, envío de emails. Cada una va a su propia clase.
- **OCP**: El cálculo de descuentos con `if/else` por `clienteCategoria` se extrae a una interfaz con estrategias. Agregar un nuevo tipo de cliente no modifica código existente.
- **DIP**: El `new Database()` y el `createTransport()` directo se reemplazan por interfaces inyectadas.

b) Nueva estructura propuesta:

```typescript
// --- DOMINIO ---
class FacturaValidator {
  validar(factura: Factura): void {
    if (!factura.clienteId) throw new Error("Factura sin cliente");
    if (factura.lineas.length === 0) throw new Error("Factura sin líneas");
    if (factura.fecha > new Date()) throw new Error("Fecha futura no permitida");
  }
}

class CalculadoraFactura {
  constructor(private estrategiaDescuento: EstrategiaDescuento) {}

  calcular(factura: Factura): FacturaCalculada {
    const subtotal = factura.lineas.reduce((sum, l) => sum + l.cantidad * l.precioUnitario, 0);
    const descuento = this.estrategiaDescuento.aplicar(subtotal, factura.clienteCategoria);
    const iva = (subtotal - descuento) * 0.21;
    return { subtotal, descuento, iva, total: subtotal - descuento + iva };
  }
}

// --- PUERTOS ---
interface FacturaRepository {
  guardar(factura: Factura): Promise<void>;
}

interface NotificacionService {
  enviarFactura(destino: string, facturaId: string, total: number): Promise<void>;
}

interface EstrategiaDescuento {
  aplicar(subtotal: number, categoria: string): number;
}

// --- CASO DE USO REFACTORIZADO ---
class ProcesarFacturaUseCase {
  constructor(
    private validator: FacturaValidator,
    private calculadora: CalculadoraFactura,
    private repository: FacturaRepository,
    private notificacionService: NotificacionService,
  ) {}

  async ejecutar(factura: Factura): Promise<void> {
    this.validator.validar(factura);
    const calculo = this.calculadora.calcular(factura);
    factura.aplicarCalculo(calculo);
    await this.repository.guardar(factura);
    await this.notificacionService.enviarFactura(factura.emailCliente, factura.id, calculo.total);
  }
}
```

c) Si el IVA cambia del 21% al 19%, solo necesito modificar **1 clase**: `CalculadoraFactura`. Ni los validadores, ni los repositorios, ni los servicios de notificación se tocan. Esto demuestra que SRP + OCP bien aplicados localizan el impacto de los cambios.

---

### Respuesta 15 — Diseño desde cero

**Sistema de reserva de canchas de fútbol**

```typescript
// ═══ PUERTOS PRIMARIOS (Casos de Uso) ═══

interface BuscarCanchasUseCase {
  ejecutar(fecha: Date, horaInicio: string): Promise<CanchaDisponible[]>;
}

interface ReservarCanchaUseCase {
  ejecutar(comando: ReservarCanchaComando): Promise<Reserva>;
}

interface CancelarReservaUseCase {
  ejecutar(reservaId: string): Promise<void>;
}

// ═══ PUERTOS SECUNDARIOS (Interfaces para infraestructura) ═══

interface CanchaRepository {
  buscarDisponibles(fecha: Date, horaInicio: string): Promise<Cancha[]>;
}

interface ReservaRepository {
  guardar(reserva: Reserva): Promise<void>;
  buscarPorId(id: string): Promise<Reserva | null>;
  actualizar(reserva: Reserva): Promise<void>;
}

interface NotificacionReservaService {
  enviarConfirmacion(email: string, reservaId: string, detalle: string): Promise<void>;
}

// ═══ DOMINIO: Aplicando SRP ═══

class Cancha {
  constructor(public readonly id: string, public readonly nombre: string) {}

  estaDisponible(fecha: Date, horaInicio: string): boolean {
    // Lógica de disponibilidad
  }
}

class Reserva {
  constructor(
    public readonly id: string,
    public readonly canchaId: string,
    public readonly usuarioId: string,
    public readonly fecha: Date,
    public readonly horaInicio: string,
    public estado: string,
  ) {}

  cancelar(): void {
    if (this.estado === "completada") throw new Error("No se puede cancelar una reserva completada");
    this.estado = "cancelada";
  }
}

class ValidadorReserva {
  validar(comando: ReservarCanchaComando, cancha: Cancha): void {
    if (comando.fecha < new Date()) throw new Error("No se puede reservar en el pasado");
    if (!cancha.estaDisponible(comando.fecha, comando.horaInicio)) throw new Error("Cancha no disponible");
  }
}

// ═══ OCP: Estrategia para distintos tipos de reserva ═══

interface EstrategiaReserva {
  ejecutar(comando: ReservarCanchaComando): Promise<Reserva>;
}

class ReservaIndividual implements EstrategiaReserva {
  async ejecutar(comando: ReservarCanchaComando): Promise<Reserva> {
    // Reserva de 1 cancha
  }
}

class ReservaTorneo implements EstrategiaReserva {
  // Nueva funcionalidad futura: reserva múltiples canchas
  async ejecutar(comando: ReservarCanchaComando): Promise<Reserva> {
    // Lógica para torneo (múltiples canchas simultáneas)
  }
}

// ═══ ISP: Interfaces segregadas para el consumidor ═══

// Solo lo que necesita para notificar
interface ReservaResumen {
  id: string;
  emailUsuario: string;
  canchaNombre: string;
  fecha: Date;
  hora: string;
}

// El handler de emails solo depende de esta interfaz, no de la entidad Reserva completa
class EmailNotificacionHandler {
  constructor(private notificacionService: NotificacionReservaService) {}

  async notificar(reserva: ReservaResumen): Promise<void> {
    await this.notificacionService.enviarConfirmacion(
      reserva.emailUsuario,
      reserva.id,
      `Reserva confirmada: ${reserva.canchaNombre} el ${reserva.fecha} a las ${reserva.hora}`,
    );
  }
}

// ═══ DIP: Inversión de Dependencias ═══

// ❌ MAL — el caso de uso acoplado a infraestructura
class ReservarCanchaUseCaseMal {
  private prisma = new PrismaClient();          // acoplamiento a ORM
  private mailer = createTransport({ /*...*/ }); // acoplamiento a nodemailer

  async ejecutar(req: Request, res: Response): Promise<void> { /* ... */ } // acoplamiento a express
}

// ✅ BIEN — el caso de uso depende solo de abstracciones
class ReservarCanchaUseCaseImpl implements ReservarCanchaUseCase {
  constructor(
    private canchaRepository: CanchaRepository,               // puerto secundario (abstracción)
    private reservaRepository: ReservaRepository,               // puerto secundario (abstracción)
    private notificacionService: NotificacionReservaService,    // puerto secundario (abstracción)
    private validador: ValidadorReserva,                        // dominio puro
    private estrategia: EstrategiaReserva,                      // OCP en acción
  ) {}

  async ejecutar(comando: ReservarCanchaComando): Promise<Reserva> {
    this.validador.validar(comando, cancha);
    const reserva = await this.estrategia.ejecutar(comando);
    await this.reservaRepository.guardar(reserva);
    await this.notificacionService.enviarConfirmacion(/* ... */);
    return reserva;
  }
}
```

**Resumen de cómo se aplican los 5 principios:**

| Principio | Aplicación |
|---|---|
| **SRP** | `ValidadorReserva` solo valida, `Cancha` solo modela la cancha, `EmailNotificacionHandler` solo notifica. |
| **OCP** | `EstrategiaReserva` permite agregar `ReservaTorneo` sin modificar `ReservaIndividual` ni el caso de uso. |
| **LSP** | `Reserva.cancelar()` funciona igual en cualquier variante. Si una variante no puede cancelar, no debe extender `Reserva` (usar interfaz `Cancelable`). |
| **ISP** | `ReservaResumen` expone solo lo que `EmailNotificacionHandler` necesita. No le pasamos la entidad `Reserva` completa. |
| **DIP** | El caso de uso depende de interfaces (`CanchaRepository`, `NotificacionReservaService`), no de `PrismaClient` ni `nodemailer`. Las implementaciones están en `adapter/out/`. |

En [[Hexagonal Architecture]], `CanchaRepository` y `NotificacionReservaService` son [[Ports|Puertos Secundarios]], implementados por [[Adapters|Adaptadores]] concretos (`PostgresCanchaRepository` en `adapter/out/persistence/`, `SendgridNotificacionService` en `adapter/out/notification/`). Los casos de uso (`ReservarCanchaUseCase`) son [[Ports|Puertos Primarios]]. Todo cumple la [[Dependency Rule]]: las dependencias apuntan hacia el dominio.

---

## 📚 Referencias

- [[SOLID]]
- [[Hexagonal Architecture]]
- [[Ports]]
- [[Adapters]]
- [[Dependency Rule]]
- [[DDD - Domain Driven Design]]
- [[Clean Architecture]]
