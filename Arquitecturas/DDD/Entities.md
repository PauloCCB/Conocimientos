# Entities (Entidades)

> **Definición:** Objetos del dominio que tienen **identidad propia** que persiste en el tiempo, independientemente de que sus atributos cambien.

Pertenece a: [[DDD - Domain Driven Design]] → Tactical Design

---

## La idea central: identidad vs valor

La pregunta clave: **¿qué hace que dos objetos sean "el mismo"?**

- Si la respuesta es **"tienen el mismo ID"** → es una Entity
- Si la respuesta es **"tienen los mismos datos"** → es un [[Value Objects|Value Object]]

```
Persona "Juan" con email juan@a.com  ──┐
                                       ├── ¿Son la misma persona? 
Persona "Juan" con email juan@b.com  ──┘
                                       → SÍ, si tienen el mismo ID (son la misma persona que cambió su email)
                                       → NO, si son IDs distintos (dos Juanes diferentes)
```

## Características

| Característica | Descripción |
|---------------|-------------|
| **Identidad** | Tiene un ID único que no cambia |
| **Mutabilidad** | Sus atributos pueden cambiar |
| **Continuidad** | Existe a través del tiempo, con historia |
| **Igualdad por ID** | `a == b` si `a.id == b.id`, aunque otros atributos difieran |

## Ejemplo en código

```java
class Usuario {
    private final UsuarioId id;    // identidad — nunca cambia
    private String nombre;          // puede cambiar
    private Email email;            // puede cambiar
    private LocalDate fechaNacimiento;

    // Igualdad basada en ID, no en datos
    @Override
    public boolean equals(Object o) {
        if (!(o instanceof Usuario)) return false;
        return this.id.equals(((Usuario) o).id);
    }

    // El negocio puede cambiar nombre/email — sigue siendo el mismo usuario
    public void cambiarEmail(Email nuevoEmail) {
        this.email = nuevoEmail;
    }
}
```

## El ID: nunca un primitivo suelto

Mal: `Long id` — solo un número, sin semántica ni validación

Bien: encapsularlo en un [[Value Objects|Value Object]]:

```java
class UsuarioId {
    private final UUID valor;

    public UsuarioId(UUID valor) {
        if (valor == null) throw new IllegalArgumentException("ID no puede ser nulo");
        this.valor = valor;
    }

    public static UsuarioId nuevo() {
        return new UsuarioId(UUID.randomUUID());
    }
}
```

Beneficios:
- El compilador previene `usuarioRepo.findById(pedidoId)` — tipos distintos
- La validación vive en un lugar
- Mejor legibilidad en firmas de método

## Entidades dentro de Aggregates

Las entidades internas de un [[Aggregates|Aggregate]] **solo son accesibles por la raíz**. No se guardan en repositorios propios.

```
Pedido (Aggregate Root — Entity)
├── LineaDePedido (Entity interna — NO tiene repositorio propio)
└── DireccionEnvio (Value Object)
```

`LineaDePedido` tiene identidad (ID de línea) pero solo existe dentro de `Pedido`. No se consulta directamente en la BD.

## Entity vs Value Object — resumen

| | Entity | Value Object |
|--|--------|--------------|
| Identidad | ID único | Sus atributos |
| Mutabilidad | Mutable | Inmutable |
| Ciclo de vida | Larga duración, historial | Sin historial |
| Ejemplo | Usuario, Pedido, Cuenta | Email, Dinero, Dirección |
| Igualdad | Por ID | Por valor |

---

## Referencias
- [[DDD - Domain Driven Design]]
- [[Value Objects]]
- [[Aggregates]]
- [[Repositories]]
