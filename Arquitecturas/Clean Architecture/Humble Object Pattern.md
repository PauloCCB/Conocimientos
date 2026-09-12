# Humble Object Pattern

> **Definición:** Patrón de diseño que separa un componente en dos partes: el **Humble Object** (difícil de testear, contiene cero lógica) y el objeto testeable (fácil de testear, contiene toda la lógica). En Clean Architecture se usa para testear Controllers, Presenters y Vistas.

Pertenece a: [[Clean Architecture]] → Patrones de implementación

---

## La idea central

Hay componentes que son inherentemente difíciles de testear:
- **Vistas** (HTML, React components, templates)
- **Controladores HTTP** (dependen del request/response del framework)
- **Conexiones a BD** (dependen de infraestructura real)

El Humble Object Pattern dice: **hacé que esos componentes sean tan tontos que no necesiten ser testeados**, y mové toda la lógica a un objeto fácil de testear.

---

## Ejemplo: Controller + Presenter

```java
// Java: Controller (Humble Object) — cero lógica, difícil de testear
@RestController
public class PedidoController {
    private final CrearPedidoInputPort inputPort;
    private final PedidoPresenter presenter;  // inyectado

    @PostMapping("/pedidos")
    public ResponseEntity<PedidoViewModel> crear(@RequestBody CrearPedidoWebRequest req) {
        inputPort.ejecutar(req.toRequestModel());   // solo invoca
        return ResponseEntity.ok(presenter.getViewModel());  // solo devuelve
    }
}

// Presenter (testeable) — contiene la lógica de presentación
public class PedidoPresenter implements PedidoOutputPort {
    private PedidoViewModel viewModel;

    @Override
    public void presentar(CrearPedidoResponse response) {
        this.viewModel = new PedidoViewModel(
            response.pedidoId().valor().toString(),
            response.estado(),
            response.total().toPlainString(),
            response.estado().equals("CONFIRMADO") ? "✅ Confirmado" : "⏳ Pendiente"
        );
    }
}
```

El Presenter **se puede testear sin HTTP**:

```java
@Test
void debeFormatearEstadoCorrectamente() {
    var presenter = new PedidoPresenter();
    presenter.presentar(new CrearPedidoResponse(pedidoId, "CONFIRMADO", BigDecimal.TEN));
    assertEquals("✅ Confirmado", presenter.getViewModel().getMensaje());
}
```

```typescript
// TypeScript: Controller (Humble Object)
@Controller("/pedidos")
export class PedidoController {
  constructor(
    private readonly inputPort: CrearPedidoInputPort,
    private readonly presenter: PedidoPresenter,
  ) {}

  @Post()
  async crear(@Body() req: CrearPedidoWebRequest): Promise<PedidoViewModel> {
    await this.inputPort.ejecutar(req.toRequestModel());
    return this.presenter.getViewModel()!;
  }
}

// Presenter (testeable sin HTTP)
@Injectable()
export class PedidoPresenter implements PedidoOutputPort {
  private viewModel: PedidoViewModel | null = null;

  presentar(response: CrearPedidoResponse): void {
    this.viewModel = {
      pedidoId: response.pedidoId.value,
      estado: response.estado,
      total: response.total.toString(),
      mensaje: response.estado === "CONFIRMADO" ? "✅ Confirmado" : "⏳ Pendiente",
    };
  }
}

// Test del Presenter (sin HTTP, sin NestJS)
it("debe formatear estado correctamente", () => {
  const presenter = new PedidoPresenter();
  presenter.presentar({ pedidoId: fakeId, estado: "CONFIRMADO", total: 100 });
  expect(presenter.getViewModel()!.mensaje).toBe("✅ Confirmado");
});
```

---

## Dónde aplicar Humble Object

| Componente difícil de testear | Humble Object | Objeto testeable |
|---|---|---|
| Controlador HTTP | Controller (invoca + devuelve) | Presenter (formatea la respuesta) |
| Vista HTML/React | Componente tonto (solo renderiza props) | Presenter/ViewModel (prepara los datos) |
| Gateway a BD | Repositorio (traduce + ejecuta query) | Mapper (traduce dominio ↔ filas) |
| Consumidor de cola | MessageListener (parsea + invoca) | Caso de uso (tiene la lógica) |

---

## Referencias
- [[Clean Architecture]]
- [[Interface Adapters]]
- [[Testing]]
