# O — Open/Closed (Abierto/Cerrado)

> **Definición:** Las entidades de software deben estar abiertas a extensión pero cerradas a modificación — **Bertrand Meyer**

Pertenece a: [[SOLID]] → O

---

## El problema: `switch` que crece sin control

Cada nuevo tipo de descuento obliga a modificar el método que calcula. Cuantos más casos, más frágil se vuelve el código.

```typescript
// ❌ Violación de OCP: cada nuevo descuento obliga a modificar esta clase
class CalculadorDescuento {
  calcular(tipo: string, total: number): number {
    switch (tipo) {
      case "VIP":
        return total * 0.80;
      case "REGULAR":
        return total * 0.95;
      case "NUEVO":
        return total * 0.90;
      case "MAYORISTA":   // ← cada nuevo tipo exige tocar este archivo
        return total * 0.75;
      default:
        return total;
    }
  }
}

// Uso
const calculador = new CalculadorDescuento();
calculador.calcular("VIP", 5000);     // 4000
calculador.calcular("REGULAR", 5000); // 4750
```

Esto viola OCP porque:
- Agregar `BLACK_FRIDAY` obliga a modificar `calcular()`.
- Agregar `CIBER_MONDAY` obliga a modificar `calcular()` otra vez.
- Cada modificación arriesga romper los casos existentes.

---

## La solución: Strategy Pattern + interfaz extensible

Definís una abstracción y el sistema se extiende agregando **nuevas clases**, no modificando las existentes.

```typescript
// ✅ OCP aplicado: abierto a extensión (nuevas estrategias), cerrado a modificación

interface EstrategiaDescuento {
  aplicar(total: number): number;
}

class DescuentoVIP implements EstrategiaDescuento {
  aplicar(total: number): number {
    return total * 0.80;
  }
}

class DescuentoRegular implements EstrategiaDescuento {
  aplicar(total: number): number {
    return total * 0.95;
  }
}

class DescuentoNuevo implements EstrategiaDescuento {
  aplicar(total: number): number {
    return total * 0.90;
  }
}

class DescuentoMayorista implements EstrategiaDescuento {
  aplicar(total: number): number {
    return total * 0.75;
  }
}

// ── Esta clase está CERRADA a modificación ──
class CalculadorDescuento {
  constructor(private readonly estrategia: EstrategiaDescuento) {}

  calcular(total: number): number {
    return this.estrategia.aplicar(total);
  }
}

// Uso
const vip = new CalculadorDescuento(new DescuentoVIP());
vip.calcular(5000); // 4000

const mayorista = new CalculadorDescuento(new DescuentoMayorista());
mayorista.calcular(5000); // 3750

// ── Agregar un nuevo descuento no toca CalculadorDescuento ──
class DescuentoBlackFriday implements EstrategiaDescuento {
  aplicar(total: number): number {
    return total * 0.50;
  }
}

const blackFriday = new CalculadorDescuento(new DescuentoBlackFriday());
blackFriday.calcular(5000); // 2500
```

`CalculadorDescuento` nunca más se modifica. El sistema está **cerrado a modificación** pero **abierto a extensión** mediante nuevas implementaciones de `EstrategiaDescuento`.

---

### Caso real: procesador de pagos

```typescript
// ❌ OCP violado: cada pasarela de pago agrega un if/else
class ProcesadorPagos {
  async procesar(metodo: string, monto: number): Promise<void> {
    if (metodo === "stripe") {
      await this.stripeApi.charge(monto);
    } else if (metodo === "mercadopago") {
      await this.mpApi.pagar(monto);
    } else if (metodo === "paypal") {  // ← modificar cada vez
      await this.paypalApi.send(monto);
    }
  }
}
```

```typescript
// ✅ OCP aplicado: interfaz + implementaciones intercambiables
interface PasarelaPago {
  procesar(monto: number): Promise<void>;
}

class StripePago implements PasarelaPago {
  constructor(private readonly api: StripeApi) {}
  async procesar(monto: number): Promise<void> {
    await this.api.charge(monto);
  }
}

class MercadoPagoPago implements PasarelaPago {
  constructor(private readonly api: MercadoPagoApi) {}
  async procesar(monto: number): Promise<void> {
    await this.api.pagar(monto);
  }
}

// Agregar PayPal sin modificar nada existente
class PayPalPago implements PasarelaPago {
  constructor(private readonly api: PayPalApi) {}
  async procesar(monto: number): Promise<void> {
    await this.api.send(monto);
  }
}

class ProcesadorPagos {
  constructor(private readonly pasarela: PasarelaPago) {}

  async ejecutar(monto: number): Promise<void> {
    await this.pasarela.procesar(monto);
  }
}
```

---

## Conexión con Hexagonal Architecture

En [[Hexagonal Architecture]], OCP es la razón por la que añadir un nuevo [[Adapters|adaptador]] **no modifica el dominio**:

- Agregar `MongoPedidoRepository` no toca `PedidoRepository` (el [[Ports|puerto]]) ni el dominio.
- Agregar un controlador gRPC no modifica los casos de uso existentes.
- El sistema está **abierto** a nuevos adaptadores y **cerrado** a modificar el núcleo.

El [[Dependency Rule|Principio de Inversión de Dependencias]] es lo que habilita OCP a nivel arquitectónico: las abstracciones viven en el centro, las implementaciones concretas se enchufan desde afuera.

---

## Errores comunes

1. **Aplicar OCP sin necesidad (sobre-ingeniería).** Si solo existen 2 tipos de descuento y nunca habrá más, un simple `if/else` es más legible que una fábrica de estrategias con inyección de dependencias. OCP se aplica donde la extensión es probable, no en cada condicional del código.

2. **Intentar predecir todas las extensiones futuras.** Diseñar una abstracción que cubra "todos los posibles descuentos que podrían existir" genera abstracciones infladas, genéricas e inútiles. Protegé los puntos de extensión que conocés; dejá el resto simple.

3. **OCP aplicado a propiedades en vez de comportamientos.** OCP protege la extensión de **comportamientos** (cálculo de descuento, procesamiento de pago), no de **datos**. No necesitás una abstracción para agregar un campo `direccionEnvio` a una entidad.

---

## 📚 Referencias

- 📖 *"Object-Oriented Software Construction"* — Bertrand Meyer (1988), origen del principio
- 📖 *"Agile Software Development: Principles, Patterns, and Practices"* — Robert C. Martin (2002), Capítulo 9
- 🌐 [[SOLID]] — hub de los cinco principios
- 🌐 [[Hexagonal Architecture]] — OCP a nivel arquitectónico con adaptadores
- 🌐 [[Clean Architecture]] — extensión abierta sin tocar círculos internos

### Nodos especializados
- [[S - Single Responsibility]]
- [[L - Liskov Substitution]]
- [[I - Interface Segregation]]
- [[D - Dependency Inversion]]
