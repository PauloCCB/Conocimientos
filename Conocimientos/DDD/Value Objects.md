# Value Objects (Objetos de Valor)

> **Definición:** Objetos del dominio que **no tienen identidad propia** — son iguales si tienen los mismos atributos. Son siempre inmutables.

Pertenece a: [[DDD - Domain Driven Design]] → Tactical Design

---

## Por qué existen

El código sin Value Objects usa primitivos para todo:

```java
// Sin Value Objects — "primitive obsession"
void transferir(String cuentaOrigen, String cuentaDestino, double monto, String moneda) {}

// Estos son válidos según el compilador pero incorrectos:
transferir("USD", "100.00", -50.0, "ACC-123");  // args invertidos, nadie se da cuenta
transferir("ACC-123", "ACC-456", 100.0, "DOLAR"); // moneda inválida, solo falla en runtime
```

Con Value Objects el compilador y el dominio protegen:

```java
void transferir(CuentaId origen, CuentaId destino, Dinero monto) {}

transferir(new Dinero(100, Moneda.USD), cuentaId, otraCuentaId);
// ↑ Error de compilación: Dinero no es CuentaId
```

## Características

| Característica | Descripción |
|---------------|-------------|
| **Sin identidad** | Dos VO con mismos datos = iguales |
| **Inmutable** | Una vez creado, nunca cambia |
| **Autovalidado** | El constructor rechaza valores inválidos |
| **Sin efectos laterales** | Operaciones devuelven nuevos VO |
| **Reemplazable** | Para "cambiar", se crea uno nuevo |

## Ejemplos del mundo real

```java
// Email — con validación encapsulada
class Email {
    private final String valor;

    public Email(String valor) {
        if (!valor.matches("^[\\w.]+@[\\w.]+\\.[a-z]{2,}$"))
            throw new IllegalArgumentException("Email inválido: " + valor);
        this.valor = valor.toLowerCase();
    }

    @Override
    public boolean equals(Object o) {
        return o instanceof Email && this.valor.equals(((Email) o).valor);
    }
}

// Dinero — con operaciones que retornan nuevo VO
class Dinero {
    private final BigDecimal cantidad;
    private final Moneda moneda;

    public Dinero sumar(Dinero otro) {
        if (!this.moneda.equals(otro.moneda))
            throw new IllegalArgumentException("No se puede sumar distintas monedas");
        return new Dinero(this.cantidad.add(otro.cantidad), this.moneda);  // nuevo VO
    }

    public boolean equals(Object o) {
        if (!(o instanceof Dinero)) return false;
        Dinero d = (Dinero) o;
        return this.cantidad.compareTo(d.cantidad) == 0 && this.moneda.equals(d.moneda);
    }
}
```

## Inmutabilidad — por qué importa

```java
// Mutabilidad causa bugs sutiles:
Dinero precio = new Dinero(100, USD);
Dinero conDescuento = precio;
conDescuento.setCantidad(80);  // ¡También cambió `precio`! Mismo objeto.

// Inmutabilidad elimina esto:
Dinero precio = new Dinero(100, USD);
Dinero conDescuento = precio.aplicarDescuento(0.20);  // nuevo objeto
// precio sigue siendo 100, conDescuento es 80
```

## Cuándo usar Value Object vs Entity

Hazte estas preguntas:

1. **¿Me importa rastrear este objeto en el tiempo?**
   - Sí → Entity
   - No → Value Object

2. **¿Dos objetos con los mismos datos son intercambiables?**
   - Sí → Value Object
   - No → Entity

3. **¿Tiene "historia" o "ciclo de vida"?**
   - Sí → Entity
   - No → Value Object

```
¿Es un billete de $100 el mismo que otro billete de $100? → SÍ → Value Object (Dinero)
¿Es el usuario #42 el mismo que el usuario #43?          → NO → Entity (Usuario)
¿Es la dirección "Calle 5, CABA" la misma que otra 
 "Calle 5, CABA"?                                        → SÍ → Value Object (Dirección)
¿Es el Pedido #1001 el mismo que el Pedido #1002?        → NO → Entity (Pedido)
```

## Value Objects en la base de datos

Los VO se persistem embebidos en la entidad que los contiene, no en tablas propias:

```sql
-- Tabla de usuarios — Dirección es un VO embebido
CREATE TABLE usuarios (
    id          UUID PRIMARY KEY,
    nombre      VARCHAR(100),
    email       VARCHAR(255),          -- VO Email
    dir_calle   VARCHAR(200),          -- VO Dirección
    dir_ciudad  VARCHAR(100),          -- VO Dirección
    dir_pais    VARCHAR(50)            -- VO Dirección
);
-- No hay tabla "direcciones" con FK — Dirección no tiene identidad propia
```

## Candidatos comunes a Value Object

- `Email`, `Telefono`, `NombreCompleto`
- `Dinero`, `Porcentaje`, `Peso`, `Temperatura`
- `Direccion`, `Coordenadas`, `CodigoPostal`
- `Rango<LocalDate>`, `Intervalo<BigDecimal>`
- `Color`, `URL`, `Version`
- Cualquier ID de Entity: `UsuarioId`, `PedidoId`

---

## Referencias
- [[DDD - Domain Driven Design]]
- [[Entities]]
- [[Aggregates]]
