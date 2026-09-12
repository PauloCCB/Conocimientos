# L — Liskov Substitution Principle (LSP)

> **Definición:** "Si S es un subtipo de T, los objetos de tipo T pueden ser reemplazados por objetos de tipo S sin alterar ninguna de las propiedades deseables del programa." — **Barbara Liskov** (1987, *Data Abstraction and Hierarchy*)

Pertenece a: [[SOLID]] → L

---

## El problema: herencia que miente

El ejemplo clásico de violación de LSP es `Square extends Rectangle`. En matemáticas, un cuadrado **es** un rectángulo. Pero en código orientado a objetos, forzar esa herencia rompe el comportamiento esperado:

```typescript
// ❌ VIOLA LSP: Cuadrado extiende Rectángulo
class Rectangulo {
  constructor(
    protected ancho: number,
    protected alto: number
  ) {}

  setAncho(ancho: number): void {
    this.ancho = ancho;
  }

  setAlto(alto: number): void {
    this.alto = alto;
  }

  getAncho(): number {
    return this.ancho;
  }

  getAlto(): number {
    return this.alto;
  }

  area(): number {
    return this.ancho * this.alto;
  }
}

class Cuadrado extends Rectangulo {
  constructor(lado: number) {
    super(lado, lado);
  }

  setAncho(ancho: number): void {
    this.ancho = ancho;
    this.alto = ancho;    // ← cambia alto también
  }

  setAlto(alto: number): void {
    this.ancho = alto;    // ← cambia ancho también
    this.alto = alto;
  }
}
```

El problema se manifiesta al usar polimorfismo:

```typescript
// Este código funciona con Rectangulo pero falla con Cuadrado
function redimensionarYVerificar(rect: Rectangulo): void {
  rect.setAncho(5);
  rect.setAlto(4);

  const areaEsperada = 20;  // 5 × 4

  if (rect.area() !== areaEsperada) {
    // Esto NUNCA debería pasar si el rectángulo se comporta como rectángulo
    throw new Error(`Área inesperada: ${rect.area()}`);
  }
}

const rectangulo = new Rectangulo(2, 2);
redimensionarYVerificar(rectangulo);  // ✅ Funciona: área = 20

const cuadrado = new Cuadrado(2);
redimensionarYVerificar(cuadrado);    // ❌ Falla: setAlto(4) también puso ancho=4, área = 16
```

> El `Cuadrado` **no es sustituible** por un `Rectangulo`. La función `redimensionarYVerificar` confía en el contrato de `Rectangulo` — que ancho y alto son independientes — y `Cuadrado` lo rompe. La relación "es-un" del mundo real no siempre debe ser herencia en código.

---

## La solución: composición sobre herencia

El error fue forzar herencia donde no hay comportamiento compatible. En vez de `Square extends Rectangle`, ambos comparten una interfaz común de `Shape`:

```typescript
// ✅ CUMPLE LSP: Interfaz común, implementaciones independientes
interface Figura {
  area(): number;
}

class Rectangulo implements Figura {
  constructor(
    private ancho: number,
    private alto: number
  ) {}

  setAncho(ancho: number): void {
    this.ancho = ancho;
  }

  setAlto(alto: number): void {
    this.alto = alto;
  }

  area(): number {
    return this.ancho * this.alto;
  }
}

class Cuadrado implements Figura {
  constructor(private lado: number) {}

  setLado(lado: number): void {
    this.lado = lado;
  }

  area(): number {
    return this.lado ** 2;
  }
}

// Ambas figuras son sustituibles donde se espere Figura
function imprimirArea(figura: Figura): void {
  console.log(`Área: ${figura.area()}`);
}

imprimirArea(new Rectangulo(5, 4));  // ✅ Área: 20
imprimirArea(new Cuadrado(3));       // ✅ Área: 9
```

> Cada clase implementa `area()` según su propio contrato, sin engañar a nadie. `Cuadrado` no promete tener `setAncho` y `setAlto` independientes porque simplemente no los expone.

---

## Ejemplo en el dominio de pedidos

**Escenario:** Un `PedidoExpress` que hereda de `Pedido` pero no puede ser cancelado después de 1 hora de creado.

```typescript
// ===== DOMINIO =====

type Dinero = {
  monto: number;
  moneda: "MXN" | "USD";
};

type EstadoPedido = "PENDIENTE" | "CONFIRMADO" | "CANCELADO" | "ENTREGADO";

class Pedido {
  constructor(
    readonly id: string,
    protected readonly clienteId: string,
    protected readonly items: { producto: string; cantidad: number; precio: Dinero }[],
    protected estado: EstadoPedido,
    readonly creadoEn: Date
  ) {}

  cancelar(): void {
    if (this.estado === "ENTREGADO") {
      throw new Error("No se puede cancelar un pedido ya entregado");
    }
    this.estado = "CANCELADO";
  }

  getEstado(): EstadoPedido {
    return this.estado;
  }

  total(): number {
    return this.items.reduce((acc, i) => acc + i.cantidad * i.precio.monto, 0);
  }
}
```

```typescript
// ❌ VIOLA LSP: PedidoExpress extiende Pedido pero rompe cancelar()
class PedidoExpress extends Pedido {
  private readonly VENTANA_CANCELACION_MS = 60 * 60 * 1000; // 1 hora

  constructor(
    id: string,
    clienteId: string,
    items: { producto: string; cantidad: number; precio: Dinero }[],
    creadoEn: Date
  ) {
    super(id, clienteId, items, "PENDIENTE", creadoEn);
  }

  cancelar(): void {
    const ahora = Date.now();
    const tiempoTranscurrido = ahora - this.creadoEn.getTime();

    if (tiempoTranscurrido > this.VENTANA_CANCELACION_MS) {
      throw new Error(
        "PedidoExpress solo puede cancelarse dentro de la primera hora"
      );
    }

    super.cancelar(); // Solo si está dentro del tiempo
  }

  // Este método es innecesario pero expone comportamiento específico
  esCancelable(): boolean {
    return Date.now() - this.creadoEn.getTime() <= this.VENTANA_CANCELACION_MS;
  }
}

// El código cliente confía en que cancelar() funciona en todo Pedido
function cancelarPedidos(pedidos: Pedido[]): void {
  for (const pedido of pedidos) {
    pedido.cancelar(); // ❌ Si alguno es PedidoExpress y pasó 1 hora, EXPLOTA
  }
}
```

> `PedidoExpress` lanza una excepción donde el tipo base no la lanza. El cliente que recibe un `Pedido[]` no tiene forma de saber que ciertos subtipos van a fallar. Esto viola LSP: **no se puede sustituir `PedidoExpress` donde se espera `Pedido` sin riesgo de error.**

---

## La solución con una interfaz `Cancelable`

En vez de forzar herencia, separamos la capacidad de cancelar en una interfaz. Quien la implementa define su propio contrato:

```typescript
// ✅ CUMPLE LSP: Interfaz Cancelable + composición
interface Cancelable {
  cancelar(): void;
  esCancelable(): boolean;
}

class Pedido {
  constructor(
    readonly id: string,
    protected readonly clienteId: string,
    protected readonly items: { producto: string; cantidad: number; precio: Dinero }[],
    protected estado: EstadoPedido,
    readonly creadoEn: Date
  ) {}

  getEstado(): EstadoPedido {
    return this.estado;
  }

  total(): number {
    return this.items.reduce((acc, i) => acc + i.cantidad * i.precio.monto, 0);
  }
}

// PedidoRegular implementa Cancelable con su propia lógica
class PedidoRegular extends Pedido implements Cancelable {
  cancelar(): void {
    if (this.estado === "ENTREGADO") {
      throw new Error("No se puede cancelar un pedido ya entregado");
    }
    this.estado = "CANCELADO";
  }

  esCancelable(): boolean {
    return this.estado !== "ENTREGADO" && this.estado !== "CANCELADO";
  }
}

// PedidoExpress implementa Cancelable con lógica distinta
class PedidoExpress extends Pedido implements Cancelable {
  private readonly VENTANA_CANCELACION_MS = 60 * 60 * 1000;

  cancelar(): void {
    if (!this.esCancelable()) {
      throw new Error("PedidoExpress solo puede cancelarse dentro de la primera hora");
    }
    this.estado = "CANCELADO";
  }

  esCancelable(): boolean {
    const dentroDeVentana =
      Date.now() - this.creadoEn.getTime() <= this.VENTANA_CANCELACION_MS;
    return dentroDeVentana && this.estado === "PENDIENTE";
  }
}
```

Ahora el código cliente **revisa antes de cancelar** y respeta el contrato de `Cancelable`:

```typescript
// ✅ El cliente respeta el contrato: pregunta si es cancelable antes de cancelar
function cancelarPedidos(cancelables: Cancelable[]): void {
  for (const item of cancelables) {
    if (item.esCancelable()) {
      item.cancelar();
      console.log("Pedido cancelado exitosamente");
    } else {
      console.log("Pedido no cancelable en este momento — se omite");
    }
  }
}

// Alternativa: usar composición en vez de herencia para PedidoExpress
class PedidoExpressCompuesto implements Cancelable {
  constructor(
    private readonly pedido: Pedido,
    private readonly ventanaCancelacionMs: number = 60 * 60 * 1000
  ) {}

  cancelar(): void {
    if (!this.esCancelable()) {
      throw new Error("PedidoExpress solo puede cancelarse dentro de la ventana");
    }
    // Delega en el método original si el Pedido lo expusiera
    // O encapsula la mutación del estado internamente
  }

  esCancelable(): boolean {
    return (
      Date.now() - this.pedido.creadoEn.getTime() <= this.ventanaCancelacionMs &&
      this.pedido.getEstado() === "PENDIENTE"
    );
  }
}
```

> La clave: `Cancelable` establece un contrato claro — `esCancelable()` debe retornar `false` cuando `cancelar()` vaya a fallar. Cada implementación define sus propias reglas sin romper el polimorfismo.

---

## Conexión con Arquitectura Hexagonal

LSP es la razón por la que los [[Ports|puertos]] secundarios funcionan. En [[Hexagonal Architecture]], cualquier implementación de un puerto debe ser **sustituible sin que el caso de uso note diferencia**:

```typescript
// Puerto secundario (definido en application/port/output/)
interface PedidoRepository {
  guardar(pedido: Pedido): Promise<void>;
  buscarPorId(id: string): Promise<Pedido | null>;
  buscarPorCliente(clienteId: string): Promise<Pedido[]>;
}

// El caso de uso trabaja contra la interfaz, no la implementación
class CrearPedidoUseCase {
  constructor(private readonly pedidoRepo: PedidoRepository) {}

  async ejecutar(datos: DatosPedido): Promise<Pedido> {
    const pedido = Pedido.crear(datos);
    await this.pedidoRepo.guardar(pedido);
    return pedido;
  }
}

// Adaptador PostgreSQL — implementa el contrato
class PrismaPedidoRepository implements PedidoRepository {
  constructor(private readonly prisma: PrismaClient) {}

  async guardar(pedido: Pedido): Promise<void> {
    await this.prisma.pedido.create({ data: this.toPrisma(pedido) });
  }

  async buscarPorId(id: string): Promise<Pedido | null> {
    const row = await this.prisma.pedido.findUnique({ where: { id } });
    return row ? this.toDomain(row) : null;
  }

  async buscarPorCliente(clienteId: string): Promise<Pedido[]> {
    const rows = await this.prisma.pedido.findMany({ where: { clienteId } });
    return rows.map(r => this.toDomain(r));
  }

  private toPrisma(pedido: Pedido) { /* mapeo dominio → BD */ }
  private toDomain(row: any): Pedido { /* mapeo BD → dominio */ }
}

// Adaptador InMemory — mismo contrato, cero infraestructura
class InMemoryPedidoRepository implements PedidoRepository {
  private pedidos: Map<string, Pedido> = new Map();

  async guardar(pedido: Pedido): Promise<void> {
    this.pedidos.set(pedido.id, pedido);
  }

  async buscarPorId(id: string): Promise<Pedido | null> {
    return this.pedidos.get(id) ?? null;
  }

  async buscarPorCliente(clienteId: string): Promise<Pedido[]> {
    return [...this.pedidos.values()].filter(p => p.clienteId === clienteId);
  }
}

// Ambas implementaciones son sustituibles. El caso de uso no cambia.
const useCase = new CrearPedidoUseCase(
  process.env.NODE_ENV === "test"
    ? new InMemoryPedidoRepository()
    : new PrismaPedidoRepository(new PrismaClient())
);
```

> El caso de uso no sabe si los pedidos se guardan en PostgreSQL, en memoria o en MongoDB. Eso es LSP aplicado a nivel arquitectura: **toda implementación del puerto es sustituible sin alterar el comportamiento del sistema.**

---

## Errores comunes

### 1. Herencia por reutilización de código, no por comportamiento

```typescript
// ❌ Heredar para reusar el método total(), pero rompiendo el contrato
class PedidoGratuito extends Pedido {
  // Hereda total(), pero un pedido gratuito siempre es $0
  // Tiene métodos heredados que no aplican
}

// ✅ Usar composición o una interfaz común
class PedidoGratuito implements Cancelable {
  total(): number { return 0; }
  // Solo expone lo que realmente aplica
}
```

### 2. Lanzar excepciones en métodos heredados

```typescript
// ❌ El subtipo lanza donde el padre no
class PedidoSoloLectura extends Pedido {
  cancelar(): void {
    throw new Error("Operación no soportada: este pedido es de solo lectura");
  }
}
```

### 3. Debilitar precondiciones en subtipos

```typescript
// ❌ El padre exige un monto positivo. El hijo acepta negativo.
class PedidoConDescuento extends Pedido {
  // Acepta items con precio negativo como "descuento"
  // Rompe la precondición del padre: precio.monto > 0
}
```

> Regla: un subtipo puede fortalecer postcondiciones (garantizar más) y debilitar precondiciones (exigir menos), como permitir entradas adicionales. Pero **nunca** al revés: no puede debilitar postcondiciones ni fortalecer precondiciones.

---

## 📚 Referencias

- 📖 *"Data Abstraction and Hierarchy"* — Barbara Liskov (1987, artículo original)
- 📖 *"Agile Software Development: Principles, Patterns, and Practices"* — Robert C. Martin (Capítulo 10: LSP)
- 🌐 [[SOLID]] — los cinco principios
- 🌐 [[Hexagonal Architecture]] — LSP aplicado a puertos y adaptadores
- 🌐 [[Ports]] — los puertos son contratos que todo adaptador debe cumplir
- 🌐 [[O - Open Closed]] — LSP es precondición para que OCP funcione

### Nodos SOLID
- [[S - Single Responsibility]]
- [[O - Open Closed]]
- [[I - Interface Segregation]]
- [[D - Dependency Inversion]]
