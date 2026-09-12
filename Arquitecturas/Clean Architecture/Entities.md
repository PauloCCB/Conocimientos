# Entities (Reglas de Negocio Enterprise)

> **Definición:** El círculo más interno de Clean Architecture. Contiene las reglas de negocio más abstractas y de más alto nivel, encapsulando las **Enterprise Business Rules**. Son independientes de cualquier aplicación concreta y sobreviven a cambios de framework, UI o base de datos.

Pertenece a: [[Clean Architecture]] → Círculo más interno

---

## La idea central

Clean Architecture llama "Entity" a algo más amplio que las [[Entities]] de DDD. Una Entity en Clean Architecture puede ser:
- Un objeto con métodos
- Un conjunto de estructuras de datos y funciones
- Un módulo que encapsula reglas de negocio críticas

Lo importante: **estas reglas no cambiarían si la aplicación pasara de web a consola, o de REST a gRPC.**

```java
// Java: Entity de Clean Architecture — independiente de la app
public class Pedido {
    private final PedidoId id;
    private List<Linea> lineas;

    // Regla de negocio enterprise: no puede tener más de 50 líneas
    public static final int MAX_LINEAS = 50;

    public void agregarLinea(ProductoId producto, int cantidad, BigDecimal precio) {
        if (lineas.size() >= MAX_LINEAS) {
            throw new BusinessRuleException("Máximo 50 líneas por pedido");
        }
        if (cantidad <= 0) {
            throw new BusinessRuleException("Cantidad debe ser positiva");
        }
        lineas.add(new Linea(producto, cantidad, precio));
    }

    public Dinero calcularTotal() {
        return lineas.stream()
            .map(Linea::subtotal)
            .reduce(Dinero.CERO, Dinero::sumar);
    }
}
```

```typescript
// TypeScript: Entity de Clean Architecture
export class Pedido {
  static readonly MAX_LINEAS = 50;
  private lineas: Linea[];

  constructor(
    readonly id: PedidoId,
    readonly clienteId: ClienteId,
  ) {
    this.lineas = [];
  }

  agregarLinea(producto: ProductoId, cantidad: number, precio: Dinero): void {
    if (this.lineas.length >= Pedido.MAX_LINEAS) {
      throw new BusinessRuleException("Máximo 50 líneas por pedido");
    }
    if (cantidad <= 0) {
      throw new BusinessRuleException("Cantidad debe ser positiva");
    }
    this.lineas.push(new Linea(producto, cantidad, precio));
  }

  calcularTotal(): Dinero {
    return this.lineas
      .map(l => l.subtotal())
      .reduce((a, b) => a.sumar(b), Dinero.CERO);
  }
}
```

---

## Lo más importante que NO puede pasar

Una Entity de Clean Architecture **nunca** debe:

```java
// ❌ Esto NO es una Entity de Clean Architecture
@Entity
@Table(name = "pedidos")
public class Pedido {  // depende de JPA, acoplada a BD
    @Id
    @GeneratedValue
    private Long id;
}
```

```typescript
// ❌ TypeScript — NO es una Entity de Clean Architecture
import { Entity, Column } from "typeorm";
@Entity()
export class Pedido {  // depende de TypeORM
    @Column()
    total: number;
}
```

La Entity es el centro del centro. **Cero dependencias externas.**

---

## Entity de Clean vs Entity de DDD

| | Clean Architecture Entity | [[Entities|DDD Entity]] |
|---|---|---|
| Nivel | Enterprise-wide. La más abstracta. | Dentro de un Bounded Context. |
| Identidad | No es requisito (puede ser función). | Requiere ID único. |
| Ejemplo | Una regla de cálculo de impuestos (ni siquiera es un objeto con ID). | `Usuario`, `Pedido`, `Cuenta` (siempre con ID). |
| Relación | Contiene lo más puro. DDD va dentro. | DDD modela el dominio dentro de este círculo. |

> En la práctica, si usás DDD, tus Aggregates, Entities y Value Objects de DDD **son** las Entities de Clean Architecture.

---

## Referencias
- [[Clean Architecture]]
- [[Use Cases]]
- [[DDD - Domain Driven Design]] → [[Entities]], [[Aggregates]], [[Value Objects]]
