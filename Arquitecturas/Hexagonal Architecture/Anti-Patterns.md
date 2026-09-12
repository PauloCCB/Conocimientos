# Anti-Patterns (Anti-patrones)

> **Definición:** Patrones de implementación que parecen Arquitectura Hexagonal pero violan sus principios fundamentales, generalmente por acoplar el dominio a la infraestructura o por saltarse los puertos.

Pertenece a: [[Hexagonal Architecture]] → Advertencias

---

## 1. Entidades de BD en el dominio

El anti-patrón más común. Las anotaciones de ORM contaminan el modelo de dominio.

```java
// ❌ MAL: Entidad JPA en el dominio
@Entity
@Table(name = "pedidos")
public class Pedido {
    @Id
    @GeneratedValue
    @Column(name = "id")
    private Long id;

    @Column(name = "cliente_id")
    private Long clienteId;

    @OneToMany(mappedBy = "pedido", cascade = ALL)
    private List<LineaDePedidoJpa> lineas;
}
```

```typescript
// ❌ TypeScript — TypeORM en el dominio
import { Entity, PrimaryColumn, Column, OneToMany } from "typeorm";

@Entity("pedidos")
export class Pedido {
  @PrimaryColumn()
  id: string;

  @Column({ name: "cliente_id" })
  clienteId: string;

  @Column({ type: "decimal" })
  total: number;

  @OneToMany(() => LineaPedidoTypeOrm, linea => linea.pedido)
  lineas: LineaPedidoTypeOrm[];
}
```

**Problemas:**
- El dominio depende de JPA/Hibernate
- La estructura de la BD dicta la estructura del dominio (tabla `pedidos` → clase `Pedido`)
- Las relaciones `@OneToMany` cargan datos en exceso o en defecto (LazyInitializationException)
- No podés usar Value Objects inmutables (JPA necesita setters)

```java
// ✅ BIEN: Dominio puro + Entidad JPA separada en el adaptador

// --- domain/model/Pedido.java ---
public class Pedido {
    private final PedidoId id;
    private final ClienteId clienteId;
    private EstadoPedido estado;
    private final List<LineaDePedido> lineas;

    public Pedido(PedidoId id, ClienteId clienteId) {
        this.id = id;
        this.clienteId = clienteId;
        this.estado = EstadoPedido.BORRADOR;
        this.lineas = new ArrayList<>();
    }

    public void agregarLinea(ProductoId producto, Cantidad cantidad, Dinero precio) {
        if (estado != EstadoPedido.BORRADOR) throw new DomainException("...");
        lineas.add(new LineaDePedido(LineaId.nuevo(), producto, cantidad, precio));
    }
}

// --- adapter/out/persistence/PedidoJpaEntity.java ---
@Entity
@Table(name = "pedidos")
public class PedidoJpaEntity {
    @Id
    @Column(name = "id")
    private UUID id;

    @Column(name = "cliente_id")
    private UUID clienteId;

    @Column(name = "estado")
    private String estado;

    @OneToMany(mappedBy = "pedido", cascade = ALL, orphanRemoval = true)
    private List<LineaPedidoJpaEntity> lineas;
}
```

```typescript
// ✅ TypeScript — Dominio puro + Entidad ORM separada

// --- domain/model/Pedido.ts ---
export class Pedido {
  private readonly id: PedidoId;
  private readonly clienteId: ClienteId;
  private estado: EstadoPedido;
  private readonly lineas: LineaDePedido[];

  constructor(id: PedidoId, clienteId: ClienteId) {
    this.id = id;
    this.clienteId = clienteId;
    this.estado = EstadoPedido.BORRADOR;
    this.lineas = [];
  }

  agregarLinea(producto: ProductoId, cantidad: Cantidad, precio: Dinero): void {
    if (this.estado !== EstadoPedido.BORRADOR) throw new DomainException("...");
    this.lineas.push(new LineaDePedido(LineaId.nuevo(), producto, cantidad, precio));
  }
}

// --- adapter/out/persistence/Pedido.typeorm-entity.ts ---
@Entity("pedidos")
export class PedidoTypeOrmEntity {
  @PrimaryColumn()
  id: string;

  @Column({ name: "cliente_id" })
  clienteId: string;

  @Column()
  estado: string;

  @OneToMany(() => LineaPedidoTypeOrmEntity, linea => linea.pedido)
  lineas: LineaPedidoTypeOrmEntity[];
}
```

La traducción entre `Pedido` (dominio) y `PedidoJpaEntity` (persistencia) la hace el [[Adapters#El Mapper: pieza fundamental|Mapper]] dentro del adaptador.

---

## 2. Adaptador llamando directamente a otro adaptador

Se salta el hexágono por completo. No hay puerto, no hay dominio, no hay nada.

```
❌  Adaptador REST ──► Adaptador BD
    (controller)       (repository)
```

```java
// ❌ MAL: Controller inyecta el repository directamente
@RestController
public class PedidoController {
    @Autowired
    private PedidoRepository repo;  // ¡se salta el puerto primario y el dominio!

    @PostMapping("/pedidos")
    public ResponseEntity<?> crear(@RequestBody PedidoRequest req) {
        repo.guardar(pedido);  // lógica de negocio inexistente
        return ResponseEntity.ok().build();
    }
}
```

```
✅  Adaptador REST → Puerto Primario → Dominio → Puerto Secundario → Adaptador BD
```

```java
// ✅ BIEN: Controller solo invoca el puerto primario
@RestController
public class PedidoController {
    private final CrearPedidoUseCase crearPedido;

    @PostMapping("/pedidos")
    public ResponseEntity<?> crear(@RequestBody PedidoRequest req) {
        DatosPedido datos = req.toDomain();
        Pedido pedido = crearPedido.ejecutar(datos);  // pasa por el caso de uso → dominio
        return ResponseEntity.ok(PedidoResponse.from(pedido));
    }
}
```

```typescript
// ✅ TypeScript — Controller solo invoca el puerto primario
@Controller("/pedidos")
export class PedidoController {
  constructor(private readonly crearPedido: CrearPedidoUseCase) {}

  @Post()
  async crear(@Body() req: PedidoRequest): Promise<PedidoResponse> {
    const datos = req.toDomain();
    const pedido = this.crearPedido.ejecutar(datos);  // pasa por el caso de uso → dominio
    return PedidoResponse.from(pedido);
  }
}
```

---

## 3. Lógica de negocio en los adaptadores

Validaciones, cálculos, reglas de negocio viviendo en Controllers o Repositories.

```java
// ❌ MAL: validación de negocio en el Controller
@PostMapping("/pedidos")
public PedidoResponse crear(@RequestBody CrearPedidoRequest req) {
    if (req.getMontoTotal() < 100.00) {
        throw new ResponseStatusException(BAD_REQUEST, "Pedido mínimo $100");
    }
    if (req.getItems().isEmpty()) {
        throw new ResponseStatusException(BAD_REQUEST, "Pedido sin items");
    }
    // ...
}
```

```java
// ✅ BIEN: la validación vive en el dominio
public class Pedido {
    private static final Dinero MONTO_MINIMO = new Dinero(100, "MXN");

    public void confirmar() {
        if (lineas.isEmpty()) throw new DomainException("Pedido sin items");
        if (calcularTotal().esMenorQue(MONTO_MINIMO)) throw new DomainException("Pedido mínimo $100");
        this.estado = EstadoPedido.CONFIRMADO;
    }
}
```

```typescript
// ✅ TypeScript — la validación vive en el dominio
export class Pedido {
  private static readonly MONTO_MINIMO = new Dinero(100, "MXN");

  confirmar(): void {
    if (this.lineas.length === 0) throw new DomainException("Pedido sin items");
    if (this.calcularTotal().esMenorQue(Pedido.MONTO_MINIMO)) throw new DomainException("Pedido mínimo $100");
    this.estado = EstadoPedido.CONFIRMADO;
  }
}
```

**Truco mental:** preguntate *"si mañana cambiás REST por CLI, ¿esta validación sigue siendo necesaria?"* Si la respuesta es sí, pertenece al dominio o al caso de uso, no al adaptador.

---

## 4. Puertos que filtran detalles de infraestructura

Un puerto debe ser agnóstico a la tecnología. Si el puerto secundario expone conceptos de la BD, está mal diseñado.

```java
// ❌ MAL: el puerto filtra SQL
public interface PedidoRepository {
    List<Pedido> findByNativeQuery(String sql);          // expone SQL
    Pedido findById(Long id);                            // usa Long de BD
    Page<Pedido> findAll(Pageable pageable);             // expone paginación de Spring
}
```

```typescript
// ❌ TypeScript — el puerto filtra Prisma/SQL
export interface PedidoRepository {
  findByNativeQuery(sql: string): Promise<Pedido[]>;       // expone SQL
  findById(id: number): Promise<Pedido>;                    // usa number genérico
  findAll(page: number, size: number): Promise<Page<Pedido>>; // expone paginación del ORM
}
```

```java
// ✅ BIEN: el puerto habla en lenguaje de dominio
public interface PedidoRepository {
    Optional<Pedido> buscarPorId(PedidoId id);            // ID tipado del dominio
    List<Pedido> buscarPorCliente(ClienteId clienteId);
    List<Pedido> buscarPendientes(Paginacion paginacion); // paginación propia del dominio
}
```

---

## 5. Mapeo manual en el dominio

```java
// ❌ MAL: el dominio tiene lógica de mapeo a JSON/SQL
public class Pedido {
    public Map<String, Object> toRow() {
        Map<String, Object> row = new HashMap<>();
        row.put("id", this.id.valor());
        row.put("total", this.calcularTotal().cantidad());
        return row;
    }

    public String toJson() {
        return new Gson().toJson(this);  // dependencia de Gson en el dominio
    }
}
```

```typescript
// ❌ TypeScript — mapeo a JSON en el dominio
export class Pedido {
  toJson(): string {
    return JSON.stringify(this);  // El dominio conoce JSON
  }
}
```

El mapeo entre dominio y formatos externos pertenece al **adaptador**, nunca al dominio. El dominio no debería saber que existe JSON, SQL o XML.

---

## 6. Un solo adaptador para todo

```java
// ❌ MAL: una sola clase hace de Controller, Repository y MessageListener
@Component
public class PedidoHandler {
    public void handleHttp(Request req) { /* ... */ }
    public void handleMessage(Message msg) { /* ... */ }
    public void save(Pedido p) { /* ... */ }
}
```

Cada adaptador tiene **una sola responsabilidad**. Si ves una clase que implementa múltiples puertos o mezcla tecnologías, está mal.

---

## 7. Abusar del hexágono para cosas triviales

No toda aplicación necesita Arquitectura Hexagonal. La arquitectura tiene un costo: más archivos, más indirección, más código de mapeo.

| Tamaño del proyecto | Arquitectura recomendada |
|---|---|
| Script de 100 líneas | Sin arquitectura (todo en un main) |
| CRUD de una sola tabla | Capas simples (Controller → Service → Repository) |
| Aplicación con reglas de negocio reales | Hexagonal + DDD |
| Sistema enterprise multi-equipo | Hexagonal + DDD + CQRS + Event Sourcing |

> *"No resuelvas problemas que no tenés."* — Si tu aplicación no tiene lógica de negocio compleja, el hexágono te va a dar más trabajo del que te ahorra.

---

## Resumen gráfico

```
                    ❌ ANTI-PATRONES comunes
   ┌─────────────────────────────────────────────────┐
   │                                                 │
   │  @Entity                     Lógica de negocio  │
   │  en el dominio ◄── mal       en Controller ◄── mal
   │                                                 │
   │  Controller ──► BD            Puerto con SQL    │
   │  (sin dominio)  ◄── mal       (filtrando)  ◄── mal
   │                                                 │
   └─────────────────────────────────────────────────┘
```

---

## Referencias

- [[Hexagonal Architecture]]
- [[Ports]]
- [[Adapters]]
- [[Dependency Rule]]
- [[DDD - Domain Driven Design]] → [[Aggregates]], [[Repositories]]
