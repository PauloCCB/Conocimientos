# Dependency Rule (Regla de Dependencia)

> **Definición:** La regla fundamental de Clean Architecture: **las dependencias de código fuente solo pueden apuntar hacia adentro**. Nada en un círculo interior puede saber nada sobre algo en un círculo exterior.

Pertenece a: [[Clean Architecture]] → Principio fundamental

---

## El enunciado formal

```
┌──────────────────────────────────────────────┐
│   Frameworks      ──►  Interface   ──►       │
│   & Drivers            Adapters              │
│                           │                  │
│                           ▼                  │
│                      Application             │
│                      (Use Cases)             │
│                           │                  │
│                           ▼                  │
│                       Enterprise             │
│                       (Entities)             │
│                                              │
│    ← nada acá menciona algo de acá →         │
│         ═══════════════════════════►         │
│           DEPENDENCIA (hacia adentro)        │
└──────────────────────────────────────────────┘
```

> El nombre de algo declarado en un círculo exterior **no debe ser mencionado** por el código en un círculo interior.

---

## Qué significa en código

```java
// ✅ Bien: Use Cases (adentro) define PedidoRepository
package com.app.usecases.port;
public interface PedidoRepository {
    void guardar(Pedido pedido);
}

// ✅ Bien: Interface Adapters (afuera) lo implementa
package com.app.adapters;
import com.app.usecases.port.PedidoRepository;
@Repository
public class JpaPedidoRepository implements PedidoRepository { }

// ❌ Mal: Use Cases importa algo de afuera
package com.app.usecases;
import org.springframework.stereotype.Service;  // ¡NO! Spring no existe en Use Cases
```

```typescript
// ✅ Bien: Use Cases define la interfaz
// src/application/port/PedidoRepository.ts
export interface PedidoRepository {
  guardar(pedido: Pedido): Promise<void>;
}

// ✅ Bien: Interface Adapters implementa
// src/adapter/out/PrismaPedidoRepository.ts
import { PedidoRepository } from "../../application/port/PedidoRepository";
@Injectable()
export class PrismaPedidoRepository implements PedidoRepository { }

// ❌ Mal: Use Cases importa NestJS
// src/application/CrearPedidoUseCase.ts
import { Injectable } from "@nestjs/common";  // ¡NO! NestJS no existe acá
```

---

## Cómo verificar que se cumple

La prueba definitiva: **el módulo/jar/paquete de Use Cases debe compilar sin dependencias de frameworks.**

```json
// TypeScript: package.json de use-cases
{
  "name": "@empresa/pedido-use-cases",
  "dependencies": {
    "@empresa/pedido-entities": "^1.0.0"
    // ❌ NADA de @nestjs/core, express, typeorm, prisma
  }
}
```

Si `npm install` en el paquete de use-cases no baja NestJS, Express, ni Prisma... la regla se cumple.

---

## La paradoja del flujo de control

```
COMPILACIÓN (dependencia):       EJECUCIÓN (flujo):
  outer → inner                     outer → inner → outer
  Interface → UseCase              Controller → UseCase → Gateway
```

El flujo de control en ejecución va hacia adentro y luego hacia afuera (ej: Controller → UseCase → Repository → BD). Pero la dependencia en compilación solo va hacia adentro. Esto se resuelve con el **Principio de Inversión de Dependencias**.

Véase: [[Crossing Boundaries]]

---

## Referencias
- [[Clean Architecture]]
- [[Crossing Boundaries]]
- [[Hexagonal Architecture]] → [[Dependency Rule]]
