# SDLC — Software Development Life Cycle

> **Definición:** Proceso estructurado que define las **fases por las que pasa el software** desde que nace como una idea hasta que se retira. Es el "ciclo de vida" del software: no es solo programar, es todo lo que rodea al código.

El término se formalizó en la década de 1960-70 con el modelo **Waterfall** (cascada) y ha evolucionado hacia modelos iterativos e incrementales.

---

## ¿Por qué existe el SDLC?

Sin un proceso definido, los equipos de software caen en el caos:

```
Sin SDLC:   Idea → [código a ciegas] → Bug → Retrabajo → Fracaso
Con SDLC:   Idea → [proceso definido] → Software funcionando → Evolución
```

El SDLC responde tres preguntas fundamentales:
1. **¿Qué construir?** → Análisis de requisitos
2. **¿Cómo construirlo?** → Diseño, implementación, pruebas
3. **¿Cómo mantenerlo?** → Despliegue, mantenimiento, retiro

---

## Las fases del SDLC (visión canónica)

Independientemente del modelo, todo proyecto de software pasa por estas **7 fases**:

```
┌─────────────────────────────────────────────────────────────┐
│  1. Análisis de requisitos                                  │
│              ↓                                              │
│  2. Diseño                                                  │
│              ↓                                              │
│  3. Implementación (codificación)                           │
│              ↓                                              │
│  4. Pruebas (testing)                                       │
│              ↓                                              │
│  5. Despliegue (deployment)                                 │
│              ↓                                              │
│  6. Mantenimiento                                           │
│              ↓                                              │
│  7. Retiro (cuando aplica)                                  │
└─────────────────────────────────────────────────────────────┘
```

---

# 📋 Fase 1 — Análisis de Requisitos

**Objetivo:** Entender **qué necesita el negocio** y qué problemas debe resolver el software.

Actividades:
- Entrevistas con stakeholders
- Talleres de descubrimiento (Event Storming, Design Thinking)
- Documentación de user stories, casos de uso
- Definición de criterios de aceptación

Entregables típicos:
- `Software Requirements Specification (SRS)`
- `Product Backlog` (en Scrum)
- Historias de usuario priorizadas

> ⚠️ El error más caro en SDLC: saltarse esta fase o hacerla mal. Un requisito mal entendido se arrastra a todas las fases siguientes.

---

# 🎨 Fase 2 — Diseño

**Objetivo:** Traducir los requisitos en una **arquitectura y diseño técnico** que guíe la implementación.

Dos niveles de diseño:

| Nivel | Pregunta | Ejemplo |
|-------|----------|---------|
| **Arquitectónico** | ¿Cómo se organiza el sistema? | Microservicios, monolito, capas |
| **Detallado** | ¿Cómo se estructura cada módulo? | Clases, APIs, esquema de BD |

Salidas comunes:
- Diagramas UML, C4, ER
- Decisiones de arquitectura (ADR)
- Mockups / wireframes
- Contratos de API

> Buen diseño = menos retrabajo en implementación.

---

# 💻 Fase 3 — Implementación

**Objetivo:** Escribir el **código** que materializa el diseño.

Esta es la fase que todo el mundo asocia con "programar", pero es solo **una de las 7**.

Buenas prácticas:
- Seguir estándares de código (linters, formatters)
- Aplicar los patrones definidos en el diseño
- Hacer commits pequeños y descriptivos
- Code reviews entre pares
- Tests junto al código (TDD cuando aplica)

```python
# Ejemplo: código siguiendo diseño de DDD
class Pedido:
    def __init__(self, cliente_id, lineas):
        if not lineas:
            raise ValueError("Pedido sin líneas")
        self.cliente_id = cliente_id
        self.lineas = lineas
        self.estado = EstadoPedido.CREADO

    def confirmar(self):
        if self.estado != EstadoPedido.CREADO:
            raise ValueError("Solo se confirma un pedido en estado CREADO")
        self.estado = EstadoPedido.CONFIRMADO
```

---

# 🧪 Fase 4 — Pruebas (Testing)

**Objetivo:** Verificar que el software **funciona como se espera** y no tiene defectos críticos.

Pirámide de tests (de abajo hacia arriba, de rápidos a lentos):

```
        /\
       /  \       E2E / UI Tests        (pocos, lentos, costosos)
      /────\
     /      \     Integration Tests      (moderados)
    /────────\
   /          \   Unit Tests             (muchos, rápidos, baratos)
  /────────────\
```

Tipos de pruebas:
- **Unitarias:** una función/clase aislada
- **Integración:** varios módulos juntos
- **E2E (end-to-end):** flujo completo del usuario
- **Rendimiento / carga:** cómo escala
- **Seguridad:** vulnerabilidades

> Una buena cobertura no es "100% de líneas", sino cubrir los **riesgos reales**.

---

# 🚀 Fase 5 — Despliegue (Deployment)

**Objetivo:** Llevar el software desde el entorno de desarrollo **hasta producción**, donde lo usan los usuarios reales.

Conceptos clave:
- **Ambientes:** dev → staging → production
- **Build artifacts:** el resultado empaquetado (jar, imagen Docker, binario)
- **Estrategias de despliegue:** blue-green, canary, rolling
- **Infraestructura como código:** Terraform, Ansible, Pulumi

```
Dev → Staging (validación) → Production (usuarios reales)
                  ↑
            smoke tests
            tests E2E
            aprobación manual
```

> Hoy en día, esta fase está casi siempre **automatizada por pipelines CI/CD**.

---

# 🔧 Fase 6 — Mantenimiento

**Objetivo:** Mantener el software **funcionando y relevante** después del lanzamiento. Es la fase más larga de todas: **años**.

Tipos de mantenimiento:

| Tipo | Descripción | Ejemplo |
|------|-------------|---------|
| **Correctivo** | Arreglar bugs | Hotfix de seguridad |
| **Adaptativo** | Adaptarse a cambios del entorno | Nueva versión de API externa |
| **Perfectivo** | Mejorar sin cambiar comportamiento | Refactor de performance |
| **Preventivo** | Evitar problemas futuros | Actualizar dependencias vulnerables |

> La deuda técnica se paga en esta fase. Cuanto mejor el diseño, menos dolorosa es.

---

# 🗑️ Fase 7 — Retiro (Sunset / Decommission)

**Objetivo:** Dar de baja el software de forma ordenada cuando ya no es útil.

Actividades:
- Comunicar a usuarios con anticipación
- Migrar datos si es necesario
- Apagar infraestructura
- Eliminar código del repositorio

> No todo software se retira: muchos sistemas legacy viven décadas. Pero cuando se decide, debe ser **planeado**, no abrupto.

---

## Modelos / Metodologías del SDLC

El SDLC es el **marco teórico**. Los modelos son **implementaciones concretas** de ese marco.

## [[Waterfall|Waterfall]] (Cascada)

El modelo clásico. Cada fase termina antes de que empiece la siguiente.

```
Requisitos → Diseño → Implementación → Pruebas → Despliegue → Mantenimiento
   ✓             ✓           ✓             ✓          ✓            ✓
```

**Pros:** Simple, documentado, fácil de entender.
**Contras:** No hay retroalimentación hasta el final. Los cambios son carísimos.

> Adecuado cuando los requisitos son **muy estables** (ej: software para naves espaciales).

## [[V-Model|V-Model]] (Modelo en V)

Variante de Waterfall donde cada fase de desarrollo tiene su **fase de prueba asociada**.

```
Requisitos          ←→  Pruebas de aceptación
  Diseño de sistema  ←→  Pruebas de sistema
    Diseño detallado ←→  Pruebas de integración
      Codificación   ←→  Pruebas unitarias
```

**Pros:** Más rigor en testing.
**Contras:** Igual de rígido que Waterfall.

## [[Iterative Model|Modelo Iterativo]]

Se repite el ciclo varias veces, cada vez con **más detalle**.

```
Iteración 1:  Requisitos básicos → Prototipo → Feedback
Iteración 2:  Requisitos ampliados → Mejoras → Feedback
Iteración 3:  Versión más completa → Refinamiento → Feedback
```

**Pros:** Feedback temprano, adaptable.
**Contras:** Requiere disciplina para no acumular deuda técnica.

## [[Spiral Model|Modelo Espiral]]

Combina iterativo + análisis de **riesgos** en cada ciclo.

```
        ┌─── Análisis de riesgo ───┐
        │                            │
        ↓                            │
   Planificación ──→ Ingeniería ──→ Evaluación del cliente
                                          │
                                          └────→ siguiente espiral
```

**Pros:** Excelente para proyectos de alto riesgo.
**Contras:** Costoso, requiere expertise en gestión de riesgos.

## [[Agile|Agile]] (Ágil)

No es un modelo único, sino un **conjunto de valores y principios** (Manifiesto Ágil, 2001).

```
12 principios del Manifiesto Ágil:
1.  Satisfacción del cliente mediante entrega temprana y continua
2.  Aceptar requisitos cambiantes (incluso tarde en el desarrollo)
3.  Entregar software funcional frecuentemente (semanas, no meses)
4.  Negocio y desarrollo juntos, diariamente
5.  Construir proyectos en torno a personas motivadas
6.  Conversación cara a cara
7.  Software funcionando es la medida primaria de progreso
8.  Desarrollo sostenible (ritmo constante)
9.  Atención continua a la excelencia técnica
10. Simplicidad — el arte de maximizar el trabajo no hecho
11. Mejor arquitectura y diseño emergen de equipos auto-organizados
12. Reflexión y ajuste periódico
```

Frameworks ágiles populares:
- **Scrum** — sprints de 2-4 semanas, roles definidos (PO, SM, equipo)
- **Kanban** — flujo continuo, sin sprints
- **XP (Extreme Programming)** — prácticas técnicas (TDD, pair programming, CI)

> Agile hoy es el estándar de facto en la industria del software.

## [[DevOps|DevOps]]

Extensión de Agile que integra **desarrollo + operaciones** como un solo flujo.

```
┌────────────────────────────────────────────────────┐
│            PLAN  ──  CODE  ──  BUILD  ──  TEST     │
│                  ↓                  ↑              │
│            RELEASE  ──  DEPLOY  ──  OPERATE        │
│                  ↓                  ↑              │
│            MONITOR  ────────────────               │
└────────────────────────────────────────────────────┘
         (feedback continuo entre todas las fases)
```

DevOps **no reemplaza** Agile, lo complementa llevando la agilidad hasta la operación.

---

## Comparativa de modelos

| Modelo | Rigidez | Feedback | Riesgo | Cuándo usarlo |
|--------|---------|----------|--------|---------------|
| Waterfall | Alta | Tardío | Alto al final | Requisitos estables |
| V-Model | Alta | Por fase | Medio | Sistemas críticos |
| Iterativo | Media | Cada ciclo | Medio | Requisitos parcialmente conocidos |
| Espiral | Media | Cada ciclo | Bajo (gestionado) | Proyectos de alto riesgo |
| Agile | Baja | Continuo | Bajo | Requisitos cambiantes |
| DevOps | Baja | Continuo | Muy bajo | Software como servicio |

---

## Resumen visual

```
SDLC
├── 7 Fases
│   ├── Análisis de requisitos   → QUÉ construir
│   ├── Diseño                   → CÓMO se estructura
│   ├── Implementación           → Escribir código
│   ├── Pruebas                  → Verificar funcionamiento
│   ├── Despliegue               → Llevar a producción
│   ├── Mantenimiento            → Evolucionar y corregir
│   └── Retiro                   → Dar de baja ordenadamente
│
└── Modelos / Metodologías
    ├── Waterfall                → secuencial, rígido
    ├── V-Model                  → cascada + testing
    ├── Iterativo                → ciclos repetidos
    ├── Espiral                  → iterativo + riesgo
    ├── Agile (Scrum, Kanban, XP) → flexible, iterativo
    └── DevOps                   → Agile + operaciones
```

---

## Por dónde empezar

1. **Entender las 7 fases** antes de elegir un modelo
2. **Evaluar el contexto:** ¿requisitos estables o cambiantes? ¿equipo pequeño o grande? ¿software crítico o de negocio?
3. **Empezar simple:** Scrum + prácticas técnicas suele ser un buen punto de partida
4. **Evolucionar hacia DevOps:** integrar despliegues automatizados desde el inicio
5. **Medir y mejorar:** retrospectivas, métricas de DORA (deployment frequency, lead time, change failure rate, MTTR)

---

## Referencias

- 📖 *Software Engineering at Google* — Titus Winters, Tom Manshreck, Hyrum Wright
- 📖 *The Phoenix Project* — Gene Kim (DevOps novelado)
- 📖 *The DevOps Handbook* — Gene Kim, Patrick Debois, John Willis
- 🌐 [Manifiesto Ágil](https://agilemanifesto.org/) — los 4 valores y 12 principios
- 📊 DORA Metrics — medición del rendimiento DevOps
