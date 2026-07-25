# Ubiquitous Language (Lenguaje Ubicuo)

> **Definición:** Vocabulario común y preciso compartido entre desarrolladores y expertos del negocio, que se refleja literalmente en el código.

Pertenece a: [[DDD - Domain Driven Design]] → Strategic Design

---

## El problema que resuelve

Sin lenguaje ubicuo, existe una traducción constante y peligrosa:

```
Experto negocio:  "El cliente hace un pedido"
Dev traduce a:    UserController.createPurchaseRecord()
Dev traduce a:    INSERT INTO orders_table...
```

Cada traducción introduce errores. El código deja de reflejar el negocio.

## La solución

El mismo término en todos lados, sin excepción:

| Contexto | Sin UL | Con UL |
|----------|--------|--------|
| Conversación | "el usuario compra" | "el cliente realiza un pedido" |
| Código | `User.buy()` | `Cliente.realizarPedido()` |
| Base de datos | `users`, `purchases` | `clientes`, `pedidos` |
| Tests | `createUser()` | `crearCliente()` |
| Documentación | "purchase record" | "Pedido" |

## Cómo construirlo

1. **Event Storming** — taller donde todos descubren el lenguaje juntos → [[Event Storming]]
2. **Glosario vivo** — documento que evoluciona con el dominio
3. **Code review de lenguaje** — rechazar términos que no estén en el glosario
4. **Conversaciones directas** — preguntar "¿cómo llamas tú a esto?"

## Señales de que algo falla

- Un mismo concepto tiene dos nombres distintos en el código
- Un dev debe "traducir" para explicarle al negocio lo que hace el código
- El código tiene términos técnicos donde debería haber términos del negocio (`record`, `entry`, `data`, `object`)

## Relación con Bounded Context

El Ubiquitous Language **es local a un [[Bounded Context]]**. La palabra `Producto` puede significar cosas distintas en Catálogo vs Inventario — eso está bien, son dos contextos diferentes con su propio lenguaje.

```
[Catálogo]              [Inventario]
Producto = descripción  Producto = unidad física con stock
         fotos                    ubicación en almacén
         precio                   número de serie
```

---

## Referencias
- [[DDD - Domain Driven Design]]
- [[Bounded Context]]
- [[Event Storming]]
