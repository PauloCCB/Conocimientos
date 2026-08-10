# S — Single Responsibility (Responsabilidad Única)

> **Definición:** Una clase debe tener una, y solo una, razón para cambiar — **Robert C. Martin**

Pertenece a: [[SOLID]] → S

---

## El problema: la clase "Dios"

Una clase que hace de todo: valida, persiste, envía emails, calcula descuentos. Cada nueva funcionalidad obliga a tocar esta clase, y cualquier cambio puede romper cualquiera de sus responsabilidades.

```typescript
// ❌ Violación de SRP: la clase tiene múltiples razones para cambiar
class GestorPedidos {
  constructor(
    private readonly db: DatabaseConnection,
    private readonly emailService: EmailService,
  ) {}

  async crearPedido(datos: DatosPedido): Promise<Pedido> {
    // 1. Validación
    if (!datos.productos.length) throw new Error("Pedido sin productos");
    if (!datos.clienteId) throw new Error("Falta cliente");

    // 2. Cálculo de descuentos
    let total = datos.productos.reduce((sum, p) => sum + p.precio * p.cantidad, 0);
    if (total > 10000) total *= 0.9;
    if (datos.clienteTipo === "VIP") total *= 0.85;

    // 3. Persistencia en BD
    const pedido = await this.db.query(
      `INSERT INTO pedidos (cliente_id, total, estado) VALUES ($1, $2, $3) RETURNING *`,
      [datos.clienteId, total, "PENDIENTE"],
    );

    // 4. Envío de notificación
    await this.emailService.enviar({
      to: datos.clienteEmail,
      subject: "Pedido creado",
      body: `Tu pedido ${pedido.id} por $${total} fue registrado`,
    });

    return pedido;
  }

  async cancelarPedido(id: string, motivo: string): Promise<void> {
    const pedido = await this.db.query("SELECT * FROM pedidos WHERE id = $1", [id]);
    if (!pedido) throw new Error("Pedido no encontrado");
    if (pedido.estado === "ENTREGADO") throw new Error("No se puede cancelar un pedido entregado");

    await this.db.query("UPDATE pedidos SET estado = $1 WHERE id = $2", ["CANCELADO", id]);

    await this.emailService.enviar({
      to: pedido.clienteEmail,
      subject: "Pedido cancelado",
      body: `Tu pedido ${id} fue cancelado. Motivo: ${motivo}`,
    });
  }
}
```

Esta clase tiene **al menos 4 razones para cambiar**:
1. Cambia la lógica de validación
2. Cambian las reglas de descuento
3. Cambia el esquema de base de datos
4. Cambia el formato de los emails

---

## La solución: una responsabilidad por clase

Separar cada responsabilidad en su propia clase hace que cada una tenga exactamente **una razón para cambiar**.

```typescript
// ✅ SRP aplicado: cada clase tiene una sola razón para cambiar

// ── Validación ──
class ValidadorPedido {
  validar(datos: DatosPedido): void {
    if (!datos.productos.length) throw new Error("Pedido sin productos");
    if (!datos.clienteId) throw new Error("Falta cliente");
  }
}

// ── Cálculo de descuentos ──
class CalculadorDescuentos {
  calcular(datos: DatosPedido, subtotal: number): number {
    let total = subtotal;
    if (total > 10000) total *= 0.9;
    if (datos.clienteTipo === "VIP") total *= 0.85;
    return total;
  }
}

// ── Persistencia ──
interface PedidoRepository {
  guardar(pedido: Pedido): Promise<void>;
  buscarPorId(id: string): Promise<Pedido | null>;
  actualizarEstado(id: string, estado: string): Promise<void>;
}

// ── Notificaciones ──
interface NotificacionService {
  notificarPedidoCreado(pedido: Pedido): Promise<void>;
  notificarPedidoCancelado(pedido: Pedido, motivo: string): Promise<void>;
}

// ── Caso de uso: orquesta las dependencias ──
class CrearPedidoUseCase {
  constructor(
    private readonly validador: ValidadorPedido,
    private readonly calculador: CalculadorDescuentos,
    private readonly pedidoRepo: PedidoRepository,
    private readonly notificaciones: NotificacionService,
  ) {}

  async ejecutar(datos: DatosPedido): Promise<Pedido> {
    this.validador.validar(datos);

    const subtotal = datos.productos.reduce((sum, p) => sum + p.precio * p.cantidad, 0);
    const total = this.calculador.calcular(datos, subtotal);

    const pedido = Pedido.crear(datos.clienteId, total);
    await this.pedidoRepo.guardar(pedido);
    await this.notificaciones.notificarPedidoCreado(pedido);

    return pedido;
  }
}

class CancelarPedidoUseCase {
  constructor(
    private readonly pedidoRepo: PedidoRepository,
    private readonly notificaciones: NotificacionService,
  ) {}

  async ejecutar(id: string, motivo: string): Promise<void> {
    const pedido = await this.pedidoRepo.buscarPorId(id);
    if (!pedido) throw new Error("Pedido no encontrado");

    pedido.cancelar(motivo);
    await this.pedidoRepo.actualizarEstado(id, pedido.estado);
    await this.notificaciones.notificarPedidoCancelado(pedido, motivo);
  }
}
```

Ahora:
- Si cambian las reglas de descuento, solo se modifica `CalculadorDescuentos`.
- Si cambia la BD, solo se modifica la implementación concreta de `PedidoRepository`.
- Si cambia el proveedor de email, solo cambia la implementación de `NotificacionService`.

---

## Conexión con Hexagonal Architecture

En [[Hexagonal Architecture]], SRP es la razón por la que cada [[Adapters|adaptador]] tiene una sola responsabilidad:

- Un `PedidoController` solo traduce HTTP → caso de uso. No persiste, no envía emails.
- Un `PostgresPedidoRepository` solo persiste. No valida, no notifica.
- Un `SmtpNotificacionService` solo envía emails. No conoce la BD.

El hexágono entero se sostiene sobre SRP: **cada adaptador hace una cosa, y la hace bien**.

---

## Errores comunes

1. **Confundir "una razón para cambiar" con "un solo método".** Una clase puede tener varios métodos si todos sirven a la misma responsabilidad. `PedidoRepository` tiene `guardar`, `buscarPorId` y `actualizarEstado` porque todos son "persistencia de pedidos".

2. **Sobre-ingeniería partiendo clases que pertenecen juntas.** Si dos comportamientos siempre cambian por la misma razón, deben vivir en la misma clase. Separarlos crea acoplamiento accidental.

3. **Mezclar lógica de presentación con lógica de negocio.** Un `PedidoController` que calcula descuentos viola SRP. La vista debe ser tonta; el dominio decide.

---

## 📚 Referencias

- 📖 *"Agile Software Development: Principles, Patterns, and Practices"* — Robert C. Martin (2002), Capítulo 8
- 📖 *"Clean Architecture"* — Robert C. Martin (2017), Capítulo 7
- 🌐 [[SOLID]] — hub de los cinco principios
- 🌐 [[Hexagonal Architecture]] — cada adaptador es SRP en acción
- 🌐 [[Clean Architecture]] — la regla de dependencia como consecuencia de SRP

### Nodos especializados
- [[O - Open Closed]]
- [[L - Liskov Substitution]]
- [[I - Interface Segregation]]
- [[D - Dependency Inversion]]
