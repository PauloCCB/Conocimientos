# SDLC + CI/CD — Cómo se complementan

> **Idea central:** SDLC te dice **qué fases atraviesa el software**. CI/CD te dice **cómo automatizar y acelerar esas fases**. No son competencia: son piezas del mismo rompecabezas.

Si SDLC es el mapa del viaje, CI/CD es el vehículo que te permite recorrerlo más rápido y seguro.

---

## 🧭 Una analogía para empezar

Piensen en construir una casa:

- **SDLC** = el plan arquitectónico: cimientos, estructura, techos, instalaciones, entrega.
- **CI/CD** = el sistema de grúas, andamios automatizados y controles de calidad que permiten construir cada piso **más rápido, sin errores y de forma repetible**.

```
SDLC  = QUÉ fases existen            → el "¿qué?"
CI/CD = CÓMO automatizarlas          → el "¿cómo?"
```

Sin SDLC no sabés qué construir. Sin CI/CD tardás 2 años en construirlo.

---

## La confusión más común

Mucha gente cree que "CI/CD reemplazó al SDLC". **Falso.** Mirá esta imagen mental:

```
Visión equivocada:
   SDLC ──────────→  (deprecated?)
   CI/CD ────────→  (reemplazo moderno)

Visión correcta:
   SDLC ──────────────┐
                       ├──→ Software funcionando
   CI/CD ─────────────┘
```

**SDLC** define las fases. **CI/CD** automatiza principalmente las fases finales del SDLC (implementación → pruebas → despliegue) y aporta disciplina continua a todo el ciclo.

---

## 📍 Dónde encaja cada uno en el mapa

Mapeo fase por fase, mostrando qué práctica de CI/CD la soporta:

```
┌────────────────────────────────────────────────────────────────────┐
│                          SDLC (el QUÉ)                             │
│                                                                    │
│  1. Análisis de requisitos                                         │
│        └── CI/CD: user stories en repo, Definition of Done         │
│                                                                    │
│  2. Diseño                                                         │
│        └── CI/CD: arquitectura como código, ADRs en el repo        │
│                                                                    │
│  3. Implementación ⭐                                              │
│        └── CI/CD: CI dispara build + tests en cada commit          │
│                                                                    │
│  4. Pruebas ⭐                                                      │
│        └── CI/CD: pipeline automatiza unit, integration, E2E       │
│                                                                    │
│  5. Despliegue ⭐                                                   │
│        └── CI/CD: CD automatiza promoción entre ambientes          │
│                                                                    │
│  6. Mantenimiento                                                  │
│        └── CI/CD: rollback automatizado, monitoreo, hotfixes       │
│                                                                    │
│  7. Retiro                                                         │
│        └── CI/CD: feature flags, sunset pipelines                  │
└────────────────────────────────────────────────────────────────────┘

⭐ = fases donde CI/CD tiene mayor impacto
```

> 📌 CI/CD **no automatiza** el análisis de requisitos ni el diseño (esas fases siguen siendo humanas). **Sí automatiza** implementación, testing y despliegue casi por completo.

---

## 🔄 El antes y el después

### Sin CI/CD (SDLC tradicional)

```
Semana 1-3:   Developer trabaja aislado en su rama
Semana 4:     Otro developer trabaja aislado en su rama
Semana 8:     "Hay que integrar"
Día 1:        Conflictos de merge everywhere
Día 3:        Tests rotos en combinaciones inesperadas
Día 5:        Hotfix urgente
Semana 9:     Logran integrar,perdieron 1 semana
Semana 10:    QA encuentra 47 bugs
Semana 12:    Deploy manual un viernes a las 11pm
Semana 13:    Algo se rompe en producción
Día siguiente: Todos mirando logs, nadie sabe qué pasó

⏱️ Tiempo total: 3 meses para un cambio pequeño
😰 Nivel de estrés: 💀💀💀
```

### Con CI/CD (SDLC + automatización)

```
09:00  Developer hace commit
09:01  Pipeline CI valida build + tests en 4 minutos
09:05  Artefacto desplegado en staging automáticamente
09:15  QA valida la nueva feature
09:30  Aprobación manual para producción (Continuous Delivery)
09:32  Canary release al 10%
09:40  100% del tráfico en la nueva versión
09:41  Métricas OK, deploy cerrado
       🎉 0 errores, equipo se va a casa tranquilo

⏱️ Tiempo total: 1 hora para un cambio pequeño
😌 Nivel de estrés: 😎
```

> 🎯 Mismo SDLC. Misma fase de implementación, pruebas y despliegue. **Drásticamente diferente ejecución.**

---

## 🤝 La relación complementaria en detalle

Les voy a mostrar punto por punto cómo se apoyan mutuamente.

## 1. SDLC le da estructura a CI/CD

Sin SDLC, "CI/CD" es solo ejecutar tests. Con SDLC, sabés **qué fase estás automatizando y por qué**.

```
SDLC dice:    "Hay una fase de Pruebas"
CI/CD dice:    "Vamos a automatizar esa fase con un pipeline"
              → tests unitarios, integración, E2E
              → ejecutados en cada commit
              → bloquean el deploy si fallan
```

## 2. CI/CD acelera SDLC

```
Sin CI/CD:   SDLC toma meses por iteración
Con CI/CD:   SDLC toma días (o horas) por iteración

Resultado:   ciclos más cortos = más feedback = mejor software
```

## 3. CI/CD refuerza la calidad de SDLC

```
SDLC dice:    "Hay que probar antes de desplegar"
CI/CD dice:    "Y si los tests fallan, el pipeline BLOQUEA el deploy"
              → no se puede desplegar código roto aunque quieras
              → la calidad deja de ser "voluntad" y pasa a ser "forzada"
```

## 4. SDLC le da propósito a CI/CD

```
CI/CD sin SDLC = automatización sin rumbo
                → "tenemos pipeline, pero ¿qué estamos construyendo?"

SDLC sin CI/CD = proceso sin velocidad
                → "sabemos qué fases hay, pero tardamos siglos"

SDLC + CI/CD   = proceso con velocidad
                → sabemos qué construir Y lo construimos rápido
```

---

## 📊 Comparativa directa

| Aspecto | SDLC | CI/CD |
|---------|------|-------|
| **Qué responde** | ¿Qué fases tiene el proceso? | ¿Cómo automatizamos? |
| **Qué es** | Marco conceptual / metodología | Práctica técnica / disciplina |
| **Nivel de abstracción** | Alto (estratégico) | Bajo (táctico) |
| **Quién lo define** | PMs, líderes, arquitectos | Ingenieros, DevOps |
| **Herramientas** | Metodologías (Scrum, Waterfall) | Jenkins, GitHub Actions, K8s |
| **Qué automatiza** | Nada por sí mismo | Build, test, deploy, monitoreo |
| **Qué requiere** | Proceso claro | Automatización + tests |
| **Frecuencia** | Por proyecto / sprint | Por commit / varias veces al día |
| **Origen histórico** | Década de 1960-70 (Waterfall) | Década de 1990-2000 (eXtreme Programming) |
| **Foco** | Completitud del proceso | Velocidad y confiabilidad |

---

## 🎯 Los modelos SDLC que mejor se llevan con CI/CD

No todos los modelos SDLC se benefician igual de CI/CD:

```
┌───────────────┬──────────────────────────────────────────────┐
│ Modelo SDLC   │ Compatibilidad con CI/CD                     │
├───────────────┼──────────────────────────────────────────────┤
│ Waterfall     │ ❌ Pobre. Fases rígidas, integraciones raras │
│ V-Model       │ ❌ Pobre. Mismo problema que Waterfall       │
│ Iterativo     │ 🟡 Aceptable. Mejora con CI                  │
│ Espiral       │ 🟡 Aceptable. Cada espiral puede usar CI     │
│ Agile (Scrum) │ ✅ Excelente. CI encaja perfecto con sprints │
│ Kanban        │ ✅ Excelente. CI encaja con flujo continuo   │
│ XP            │ ✅✅ Diseñado junto con CI (de ahí viene)    │
│ DevOps        │ ✅✅✅ Es CI/CD elevado a cultura completa    │
└───────────────┴──────────────────────────────────────────────┘
```

> 🎓 Dato histórico: **Continuous Integration nace de Extreme Programming (XP)**, que es un modelo SDLC. Es decir, CI/CD siempre fue pensado como práctica DENTRO de un SDLC, no como algo separado.

---

## 🧩 El mapa completo integrado

Aquí está la visión unificada. Leanla de izquierda a derecha:

```
          SDLC (fases)                    CI/CD (automatización)
          ─────────────                   ──────────────────────

Requisitos  ─→ Historias en repo   ────────→  Backlog como código
Diseño      ─→ ADRs, diagramas    ────────→  Infra como código (Terraform)
Implementac.─→ Código + commits   ────────→  🟢 CI: build + tests
                                         │
Pruebas     ─→ Estrategia testing ────────→  🟢 CI: tests automatizados
                                         │
Despliegue  ─→ Plan release       ────────→  🟢 CD: deploy automático
                                         │
Mantenim.   ─→ Bugs, features     ────────→  🔄 Hotfix pipelines, rollback
                                         │
Retiro      ─→ Sunset plan        ────────→  🚩 Feature flags, deprecation
```

---

## 🏢 Caso real: una empresa combinando ambos

Les narro cómo se ve una empresa seria usando ambos juntos:

```
Ciclo de vida de una feature en EmpresaX:

1️⃣  SDLC - Análisis
    PM escribe user story en Jira → se sincroniza con repo (SDLC)
    
2️⃣  SDLC - Diseño
    Arquitecto crea ADR en /docs/adr/ (SDLC)
    
3️⃣  SDLC - Implementación
    Developer abre PR (SDLC) → CI corre tests automáticos (CI/CD)
    
4️⃣  SDLC - Pruebas
    Pipeline ejecuta unit + integration + E2E tests (CI/CD)
    QA valida en staging (SDLC)
    
5️⃣  SDLC - Despliegue
    Aprobación manual (SDLC) → Deploy con canary release (CI/CD)
    
6️⃣  SDLC - Mantenimiento
    Métricas en Grafana (CI/CD) → Bugs vuelven al backlog (SDLC)
    
7️⃣  SDLC - Retiro (cuando aplique)
    Feature flag desactiva funcionalidad (CI/CD) → Remoción del código (SDLC)
```

¿Ven? Es una sola historia. SDLC da los pasos, CI/CD automatiza la ejecución.

---

## 🧠 Mentalidad correcta

Resumamos qué mentalidad adoptar:

```
❌  "SDLC vs CI/CD"                → competencia falsa
❌  "CI/CD reemplazó al SDLC"      → no, lo complementa
❌  "Tengo CI/CD, no necesito SDLC" → sin proceso, no sabés qué automatizar
❌  "Tengo SDLC, no necesito CI/CD" → sin automatización, el proceso es lento

✅  "SDLC define el mapa"
✅  "CI/CD acelera el viaje"
✅  "Ambos juntos = software que llega a tiempo y sin bugs"
```

---

## Resumen visual final

```
                  ┌─────────────────┐
                  │     SDLC        │
                  │  (el QUÉ)       │
                  │  - 7 fases      │
                  │  - Modelos      │
                  │  - Proceso      │
                  └────────┬────────┘
                           │
                    se complementa
                           │
                  ┌────────┴────────┐
                  │     CI/CD       │
                  │  (el CÓMO)      │
                  │  - Automatiza   │
                  │  - Acelera      │
                  │  - Asegura      │
                  └─────────────────┘
                           │
                           ↓
                  Software de calidad
                  entregado rápido
```

| Pieza | Rol |
|-------|-----|
| **SDLC** | Define las fases, roles, entregables, cuándo |
| **CI/CD** | Automatiza ejecución, acelera tiempos, garantiza calidad |
| **Juntos** | Proceso completo + ejecución confiable = producto exitoso |

---

## Por dónde empezar si hoy no tenés nada

```
Paso 1 → Adoptar un modelo SDLC claro (Scrum, Kanban, lo que sea)
Paso 2 → Versionar todo en un solo repo
Paso 3 → Agregar tests automatizados
Paso 4 → Armar un pipeline CI básico (build + tests)
Paso 5 → Hacer que la build sea obligatoria para mergear a main
Paso 6 → Automatizar el deploy a staging
Paso 7 → Automatizar el deploy a producción (con aprobación primero)
Paso 8 → Medir: lead time, deployment frequency, change failure rate
```

> 📊 Esas 4 últimas métricas son las llamadas **DORA Metrics**, el estándar de la industria para medir salud DevOps.

---

## Referencias

- 📖 *Continuous Delivery* — Jez Humble, David Farley (cap. 1 sobre relación con SDLC)
- 📖 *The Phoenix Project* — Gene Kim (novela sobre SDLC + DevOps + CI/CD)
- 📖 *Software Engineering at Google* — capítulo sobre "scale of automation"
- 🌐 [martinfowler.com/continuousIntegration](https://martinfowler.com/articles/continuousIntegration.html) — el artículo seminal
- 📊 DORA Metrics — medición de performance en DevOps
- 📁 [[SDLC/SDLC]] — clase anterior sobre las fases
- 📁 [[CI-CD/CI-CD]] — clase anterior sobre la automatización
