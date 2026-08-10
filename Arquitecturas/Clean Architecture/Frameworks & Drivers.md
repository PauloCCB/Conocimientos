# Frameworks & Drivers (Frameworks y Drivers)

> **Definición:** La capa más externa de Clean Architecture. Contiene todo lo concreto y específico: el framework web, la base de datos, las librerías de terceros. Esta capa **no tiene código propio de negocio** — solo "enchufa" lo que las capas internas necesitan.

Pertenece a: [[Clean Architecture]] → Cuarto círculo (el más externo)

---

## La idea central

Esta capa es **la cáscara intercambiable**. Si mañana decidís cambiar de Spring Boot a Quarkus, o de Express a Fastify, solo esta capa cambia. Las capas internas (Entities, Use Cases, Interface Adapters) no se tocan.

Ejemplos de lo que vive acá:
- `main()` o `bootstrap()` — el punto de entrada
- Configuración de Spring / NestJS / Express
- Configuración de la base de datos
- Implementaciones concretas de interfaces definidas adentro

```java
// Java: Frameworks & Drivers — configuración de Spring Boot
@SpringBootApplication
public class PedidoApplication {
    public static void main(String[] args) {
        SpringApplication.run(PedidoApplication.class, args);
    }
}

@Configuration
public class DependencyConfig {
    @Bean
    public PedidoRepository pedidoRepository(PedidoJpaRepository jpaRepo) {
        return new JpaPedidoGateway(jpaRepo);  // enchufa implementación concreta
    }
}
```

```typescript
// TypeScript: Frameworks & Drivers — bootstrap NestJS
import { NestFactory } from "@nestjs/core";
import { AppModule } from "./config/AppModule";

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  await app.listen(3000);
}
bootstrap();

// Módulo de DI — enchufa implementaciones concretas
@Module({
  providers: [
    { provide: PEDIDO_REPOSITORY, useClass: PrismaPedidoGateway },
    { provide: NOTIFICACION_SERVICE, useClass: SendgridNotificacionService },
  ],
})
export class PersistenceModule {}
```

---

## Cómo se conecta con adentro

```
  ┌──────────────────────────────────────┐
  │     Frameworks & Drivers             │
  │                                       │
  │  main.ts  ──► AppModule ──►          │
  │                │                      │
  │                └──► proporciona       │
  │                     implementaciones  │
  │                     concretas a las   │
  │                     interfaces de     │
  │                     Interface Adapters│
  └──────────────────────────────────────┘
                          │
                          ▼
              ┌──────────────────────┐
              │ Interface Adapters   │
              │ (Controllers,        │
              │  Gateways,           │
              │  Presenters)         │
              └──────────────────────┘
```

La capa externa **conoce** a todas las demás. Pero ninguna capa interna conoce a esta. Es el punto donde se resuelve el grafo de dependencias.

---

## Web, BD, y frameworks como "detalles"

Robert C. Martin insiste: el framework web y la base de datos son **detalles**.

> *"The Web is a detail. The database is a detail. Your architecture should not be about these things."*

Esto no significa que sean irrelevantes. Significa que **tu arquitectura debe poder sobrevivir sin ellos**. Podrías ejecutar tus casos de uso y entities sin web, sin BD, desde tests unitarios.

---

## Referencias
- [[Clean Architecture]]
- [[Interface Adapters]]
- [[Dependency Rule]]
- [[Hexagonal Architecture]] → [[Adapters]]
