# Repositories (Repositorios)

> **Definición:** Abstracción que simula una **colección en memoria** de [[Aggregates]]. El dominio no sabe cómo ni dónde se persisten los datos.

Pertenece a: [[DDD - Domain Driven Design]] → Tactical Design

---

## El problema que resuelve

Sin Repository, el dominio conoce la base de datos:

```java
class ConfirmarPedido {
    public void ejecutar(Long pedidoId) {
        // El dominio sabe de SQL ❌
        Connection conn = dataSource.getConnection();
        PreparedStatement ps = conn.prepareStatement("SELECT * FROM pedidos WHERE id = ?");
        ps.setLong(1, pedidoId);
        ResultSet rs = ps.executeQuery();
        // ...mapeo manual, lógica de negocio mezclada con SQL
    }
}
```

Con Repository, el dominio trabaja con objetos, no con SQL:

```java
class ConfirmarPedido {
    public void ejecutar(PedidoId pedidoId) {
        Pedido pedido = pedidoRepository.findById(pedidoId);  // limpio ✅
        pedido.confirmar();
        pedidoRepository.save(pedido);
    }
}
```

## Reglas

1. **Un Repository por Aggregate Root** — no por Entity interna
2. **Interfaz en el dominio** — implementación en infraestructura
3. **Trabaja con objetos del dominio** — no con DTOs ni ResultSets
4. **Semántica de colección** — `find`, `save`, `delete`, no `INSERT`, `UPDATE`

## Estructura

```
dominio/
  PedidoRepository.java       ← interfaz (el dominio la define)

infraestructura/
  PedidoRepositorySQL.java    ← implementación con JPA/JDBC
  PedidoRepositoryMongo.java  ← otra implementación posible
  PedidoRepositoryInMemory.java ← para tests
```

## Ejemplo completo

```java
// ---- DOMINIO: define la interfaz ----
interface PedidoRepository {
    Optional<Pedido> findById(PedidoId id);
    List<Pedido> findByCliente(ClienteId clienteId);
    List<Pedido> findPendientes();
    void save(Pedido pedido);
    void delete(PedidoId id);
}

// ---- INFRAESTRUCTURA: implementa ----
@Repository
class PedidoRepositoryJPA implements PedidoRepository {
    private final PedidoJpaRepository jpa;  // Spring Data JPA
    private final PedidoMapper mapper;       // convierte Entity JPA ↔ Aggregate

    public Optional<Pedido> findById(PedidoId id) {
        return jpa.findById(id.valor())
                  .map(mapper::toDomain);   // JPA Entity → Aggregate del dominio
    }

    public void save(Pedido pedido) {
        PedidoJpaEntity entity = mapper.toJpa(pedido);  // Aggregate → JPA Entity
        jpa.save(entity);
    }
}
```

## El mapper: por qué es necesario

El modelo de dominio y el modelo de persistencia tienen propósitos distintos:

| Modelo de dominio | Modelo de persistencia |
|---|---|
| Expresa el negocio | Optimizado para SQL/NoSQL |
| Tiene lógica (métodos) | Tiene anotaciones (@Column, etc) |
| Inmutable donde corresponde | Mutable para ORMs |
| Nombres del dominio | Puede tener nombres de tabla/columna |

El mapper traduce entre ambos, protegiendo que ninguno contamine al otro.

## Repository en tests

El in-memory Repository permite tests rápidos sin base de datos:

```java
class PedidoRepositoryInMemory implements PedidoRepository {
    private final Map<PedidoId, Pedido> store = new HashMap<>();

    public Optional<Pedido> findById(PedidoId id) {
        return Optional.ofNullable(store.get(id));
    }

    public void save(Pedido pedido) {
        store.put(pedido.getId(), pedido);
    }
}

// Test limpio, sin BD, en milisegundos
class ConfirmarPedidoTest {
    @Test
    void confirmar_pedido_con_lineas() {
        PedidoRepository repo = new PedidoRepositoryInMemory();
        Pedido pedido = Pedido.crear(clienteId);
        pedido.agregarLinea(productoId, Cantidad.de(2), Dinero.de(50, USD));
        repo.save(pedido);

        new ConfirmarPedido(repo).ejecutar(pedido.getId());

        Pedido confirmado = repo.findById(pedido.getId()).orElseThrow();
        assertEquals(EstadoPedido.CONFIRMADO, confirmado.getEstado());
    }
}
```

## Lo que NO hace un Repository

- No contiene lógica de negocio — eso va en el Aggregate o Domain Service
- No hace joins complejos para reportes — usa un modelo de lectura separado (CQRS)
- No hace queries ad-hoc de la UI — eso es responsabilidad de la capa de aplicación

---

## Referencias
- [[DDD - Domain Driven Design]]
- [[Aggregates]]
- [[Entities]]
