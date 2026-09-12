# CI/CD — Integración Continua y Despliegue Continuo

> **Clase dictada por:** Prof. CI/CD  
> **Tema:** Cómo pasar de "funciona en mi máquina" a desplegar con confianza todos los días.

---

## 👋 Bienvenidos a clase

Hoy vamos a hablar de una de las prácticas más importantes en la ingeniería de software moderna: **CI/CD**.

Antes de empezar, una pregunta para ustedes:

> *¿Cuántos de ustedes han escuchado la frase "funciona en mi máquina"?*

Levanten la mano. Todos. Ese es justamente el problema que CI/CD viene a resolver.

**CI/CD no es una herramienta. No es Jenkins. No es GitHub Actions. Es una DISCIPLINA.** Las herramientas son solo el medio.

```
Problema histórico del software:

   Desarrollador A  ─┐
   Desarrollador B  ─┤──→ "Integración" 1 vez al mes  ──→  💥 Desastre
   Desarrollador C  ─┤
   Desarrollador D  ─┘

Solución CI/CD:

   Desarrollador A  ─┐
   Desarrollador B  ─┤──→ Integración CONTINUA (varias veces al día)  ──→ ✅ Tranquilidad
   Desarrollador C  ─┤
   Desarrollador D  ─┘
```

---

## 📚 Lo que van a aprender hoy

1. Qué es CI (Continuous Integration)
2. Qué es CD (Continuous Delivery / Continuous Deployment)
3. Por qué existe este concepto y qué problema resuelve
4. Cómo se ve un pipeline real
5. Buenas prácticas y antipatrones
6. Herramientas del ecosistema

---

# 📖 Lección 1: Continuous Integration (CI)

## Definición formal

> **Continuous Integration (CI)** es la práctica de **integrar frecuentemente los cambios de todos los desarrolladores en una rama compartida**, verificando automáticamente que cada integración no rompa nada.

Traduciendo al español de pasillo: **juntamos nuestro código seguido, y una máquina verifica que todo siga funcionando.**

## El problema que CI resuelve

Les cuento una historia. Equipo de 5 developers trabajando en el mismo proyecto.

```
Mes 1:  Cada uno trabaja en su rama, sin tocarse.
Mes 2:  Cada uno sigue trabajando aislado.
Mes 3:  Hay que "integrar". 
         → Día 1: conflictos de merge por todos lados
         → Día 3: tests rotos en combinaciones inesperadas
         → Día 5: alguien descubre que el código de A rompe el módulo de B
         → Semana 2: logran integrar, perdieron 2 semanas
         
         😱 Eso es "Merge Hell" o "Integration Hell"
```

**CI ataca exactamente esto:** integrar a cada rato, no una vez al mes.

## Las reglas de oro de CI

```
┌──────────────────────────────────────────────────────────┐
│                   LAS 10 REGLAS DE CI                     │
│                                                           │
│  1.  Repositorio único compartido (trunk-based o main)    │
│  2.  Automatizar la build                                 │
│  3.  Hacer commits pequeños y frecuentes                 │
│  4.  Cada commit dispara build automática                │
│  5.  Builds rápidas (menos de 10 minutos ideal)           │
│  6.  Tests automatizados que se ejecutan en cada build    │
│  7.  Si la build se rompe → arreglarla INMEDIATAMENTE    │
│  8.  Mantener el estado de build rápido y limpio         │
│  9.  Todo el mundo ve el estado de la build              │
│  10. Despliegues automatizados (eso ya es CD)             │
└──────────────────────────────────────────────────────────┘
```

## Anatomía de un pipeline CI

Veamos qué pasa cuando un developer hace `git push`:

```
   Developer
       │
       │  git push origin main
       ↓
┌─────────────────┐
│  Git Repository │  (GitHub, GitLab, Bitbucket)
└────────┬────────┘
         │  webhook dispara
         ↓
┌─────────────────┐
│  Runner / Agent│  (máquina que ejecuta)
└────────┬────────┘
         │
         ├──→ 1️⃣ Checkout del código
         │
         ├──→ 2️⃣ Install dependencies
         │
         ├──→ 3️⃣ Compilar / Build
         │
         ├──→ 4️⃣ Ejecutar tests unitarios
         │
         ├──→ 5️⃣ Ejecutar tests de integración
         │
         ├──→ 6️⃣ Linting / análisis estático
         │
         ├──→ 7️⃣ Análisis de seguridad (SAST)
         │
         └──→ ✅ Reporte: verde 🟢 o rojo 🔴
```

## Ejemplo real: GitHub Actions

Les muestro un pipeline CI real, con archivo YAML:

```yaml
# .github/workflows/ci.yml
name: CI Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout código
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      
      - name: Instalar dependencias
        run: npm ci
      
      - name: Lint
        run: npm run lint
      
      - name: Tests unitarios
        run: npm test
      
      - name: Build
        run: npm run build
      
      - name: Tests de integración
        run: npm run test:integration
        env:
          DATABASE_URL: postgres://test:test@localhost:5432/test
```

Esto, muchachos, es un pipeline CI funcional. Cada `git push` ejecuta todo esto automáticamente.

## Un consejo del profesor

> 🎓 Si la build está rota **nadie** debería hacer otra cosa que arreglarla. Es como un paro cardíaco en código: la prioridad #1 es revivirlo.

---

# 📖 Lección 2: Continuous Delivery (CD)

## Definición

> **Continuous Delivery (CD)** es la práctica de mantener el código **siempre en un estado desplegable**, de modo que en cualquier momento se pueda liberar a producción con **un click (o casi)**.

Diferencia sutil pero importante:

```
Continuous Delivery       = artefacto listo para producción
                              → el despliegue final es MANUAL (aprobación)

Continuous Deployment     = artefacto listo para producción
                              → el despliegue es AUTOMÁTICO, sin humanos
```

```
Continuous Integration  → cada commit se integra y valida automáticamente
Continuous Delivery     → cada commit deja el código listo para producción
Continuous Deployment   → cada commit va directo a producción automáticamente
```

Son tres prácticas distintas, en niveles crecientes de automatización.

## La idea central de CD

```
Antes de CD:
   Despliegue = evento raro, doloroso, los viernes a las 11pm, todos asustados
   
Con CD:
   Despliegue = evento rutinario, aburrido, varias veces al día
```

Si su despliegue les da miedo, **no están haciendo CD.**

## Pipeline completo (CI + CD)

Veamos cómo se ve el flujo de punta a punta:

```
   Developer
       │
       │  git push
       ↓
   ┌─────────┐
   │  CI     │  → build, test, lint
   └────┬────┘
        │ ✅ verde
        ↓
   ┌─────────┐
   │  Build  │  → genera artefacto (imagen Docker, jar, etc.)
   └────┬────┘
        │
        ↓
   ┌─────────┐
   │ Staging │  → deploy automático al entorno de staging
   └────┬────┘
        │ ✅ tests E2E, smoke tests
        ↓
   ┌─────────────┐
   │ Aprobación  │  → manual (CD) o automática (Continuous Deployment)
   └────┬────────┘
        │
        ↓
   ┌─────────────┐
   │ Production  │  → deploy a usuarios reales
   └─────────────┘
```

## Ejemplo: agregar deploy a staging

Extiendo el pipeline anterior con etapa de despliegue:

```yaml
# .github/workflows/ci-cd.yml (continuación)
  deploy-staging:
    needs: build-and-test    # espera que CI pase
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      
      - name: Build imagen Docker
        run: docker build -t miapp:${{ github.sha }} .
      
      - name: Push al registry
        run: |
          echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u "${{ secrets.DOCKER_USER }}" --password-stdin
          docker push miapp:${{ github.sha }}
      
      - name: Deploy a staging
        run: |
          kubectl set image deployment/miapp miapp=miapp:${{ github.sha }} -n staging
          kubectl rollout status deployment/miapp -n staging
```

> Noten la palabra `needs:` — CD depende de que CI haya pasado. Esto es lo que se llama **"pipeline gate"**.

---

# 📖 Lección 3: Estrategias de despliegue

Aquí es donde los profesionales separan a los novatos. Les voy a enseñar las **4 estrategias principales**.

## 🔵🟢 Blue-Green

```
        Load Balancer
             │
       ┌─────┴─────┐
       ↓           ↓
   ┌───────┐   ┌───────┐
   │ Blue  │   │ Green │
   │(v1)   │   │(v2)   │
   └───────┘   └───────┘
   
   Paso 1: Blue recibe todo el tráfico (versión actual)
   Paso 2: Deploy a Green (nueva versión) pero SIN tráfico
   Paso 3: Smoke tests en Green
   Paso 4: Switch del Load Balancer → todo el tráfico va a Green
   Paso 5: Si algo falla → switch de vuelta a Blue (rollback instantáneo)
```

**Pros:** Rollback inmediato, zero downtime.
**Contras:** Duplicas infraestructura temporalmente ($$).

## 🐤 Canary Release

```
        Load Balancer
             │
       ┌─────┴─────┐
       ↓ 90%        ↓ 10%
   ┌───────┐   ┌───────┐
   │ v1    │   │ v2    │
   │(estable)│  │(canary)│
   └───────┘   └───────┘
   
   Paso 1: v2 recibe solo el 10% del tráfico
   Paso 2: Monitorear errores, métricas
   Paso 3: Si todo OK → 25%, 50%, 75%, 100%
   Paso 4: Si métricas malas → rollback
```

**Pros:** Riesgo bajo, gradual, validación real con usuarios.
**Contras:** Más complejo de orquestar.

## 🌀 Rolling Update

```
   Instancia 1: v1 → v2 (se actualiza)
   Instancia 2: v1 (sigue con v1)
   Instancia 3: v1 → v2 (se actualiza)
   Instancia 4: v1 (sigue con v1)
   ...
   ... así hasta que todas son v2
```

**Pros:** Sin downtime, sin duplicar infraestructura.
**Contras:** Versiones mezcladas brevemente, rollback más lento.

## 🚩 Feature Flags

```
   Código deployado a producción pero DESHABILITADO
   por un flag en configuración:
   
   if (featureFlags.nuevaFuncionalidad) {
       // código nuevo
   } else {
       // código viejo
   }
```

**Pros:** Despliegue ≠ lanzamiento. Activás la feature cuando querés.
**Contras:** Hay que remover los flags después (deuda técnica).

---

# 📖 Lección 4: Buenas prácticas

Les dejo la lista de cosas que **deberían** hacer:

```
✅ Commit pequeño y frecuente        → no esperar al final del día
✅ Tests automatizados robustos       → confianza para desplegar
✅ Pipeline rápido (< 10 min)         → feedback rápido
✅ Build siempre verde                → trunk-based development
✅ Versionado semántico               → v1.2.3
✅ Infraestructura como código        → reproducible
✅ Monitoreo y logs centralizados     → visibilidad post-deploy
✅ Rollback automatizado              → porque a veces falla
✅ Secretos fuera del código          → usar vaults / secrets manager
✅ Documentar el pipeline             → onboarding de nuevos devs
```

Y la lista de cosas que **NO** deberían hacer:

```
❌ Hacer commits gigantes de 3 semanas    → integrar tarde es sufrir
❌ Ignorar la build rota                  → deuda técnica explosiva
❌ Subir secretos al repositorio          → breach de seguridad
❌ Hacer deploys manuales con ssh         → error humano garantizado
❌ No tener tests                         → "¿funcionará?" nunca
❌ Deployar los viernes a las 5pm         → viernes = bugs garantizados
❌ No monitorear después del deploy       → "deployar y rezar"
```

---

# 📖 Lección 5: Herramientas del ecosistema

No se casen con una herramienta, entiendan los conceptos. Dicho esto, estas son las más usadas:

## Pipelines / Orquestadores

| Herramienta | Comentario |
|-------------|------------|
| **GitHub Actions** | Integrada en GitHub, muy popular |
| **GitLab CI** | Integrada en GitLab, poderosa |
| **Jenkins** | El clásico, máxima flexibilidad, máximo dolor de cabeza |
| **CircleCI** | SaaS, rápido setup |
| **Azure DevOps** | Si estás en el ecosistema Microsoft |
| **Buildkite** | Híbrido (agentes propios, UI cloud) |

## Contenedores / Build

| Herramienta | Comentario |
|-------------|------------|
| **Docker** | Estándar de facto para empaquetar |
| **BuildKit / Buildx** | Builds Docker avanzadas |

## Orquestación / Deploy

| Herramienta | Comentario |
|-------------|------------|
| **Kubernetes** | Estándar para orquestación |
| **Helm** | Gestor de paquetes para K8s |
| **ArgoCD** | GitOps para Kubernetes |
| **Terraform** | Infraestructura como código |

## Calidad / Testing

| Herramienta | Comentario |
|-------------|------------|
| **SonarQube** | Calidad de código |
| **Snyk** | Vulnerabilidades en dependencias |
| **Trivy** | Scan de imágenes Docker |

---

# 📖 Lección 6: La trampa conceptual que ven todos

Les voy a aclarar algo que confunde hasta gente con experiencia.

## ¿Cuál es la diferencia entre CI, CD, Continuous Delivery y Continuous Deployment?

Tómense un momento para entenderlo bien:

```
┌──────────────────────────────────────────────────────────────────┐
│                                                                  │
│   CI                     (Continuous Integration)                │
│   └── Integrar seguido + verificar automáticamente               │
│                                                                  │
│   Continuous Delivery    (CD en su forma moderada)               │
│   └── CI + artefacto siempre listo + deploy manual con 1 click  │
│                                                                  │
│   Continuous Deployment  (CD en su forma extrema)                │
│   └── CI + artefacto siempre listo + deploy 100% automático     │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

**Resumen:**

| Término | ¿Qué es? | ¿Quién aprueba el deploy? |
|---------|----------|---------------------------|
| CI | Integración continua | Nadie (es automático) |
| Continuous Delivery | Entrega continua | Un humano |
| Continuous Deployment | Despliegue continuo | Nadie (es automático) |

> 🎓 Dato de profesor: cuando la gente dice "CD" sin contexto, el 70% de las veces se refiere a **Continuous Delivery**, no a Deployment. No se confundan.

---

# 📖 Lección 7: Ejemplo completo de un día con CI/CD

Les voy a narrar lo que pasa un martes cualquiera en una empresa seria:

```
09:00 — Developer A hace git push con nueva feature
09:01 — Pipeline CI arranca automáticamente
09:04 — Tests pasan. Build verde. ✅
09:05 — Artefacto (imagen Docker) se publica en registry
09:05 — Deploy automático a STAGING

09:15 — Developer B hace git push con fix de bug
09:16 — Pipeline CI arranca
09:17 — Tests fallan en un caso nuevo ❌
09:18 — Developer B recibe notificación. Arregla el test.
09:22 — Re-commit. CI pasa. ✅
09:22 — Deploy automático a STAGING

10:00 — Equipo de QA valida ambas features en staging
10:15 — QA aprueba. Click en "Deploy to Production"
10:16 — Canary release: 10% del tráfico a la nueva versión
10:20 — Métricas OK → 25%, 50%, 75%, 100%
10:30 — 100% del tráfico en nueva versión
10:30 — Slack: 🎉 Deploy exitoso, 0 errores en logs

Todo esto SIN entrar a un servidor por SSH.
```

Y así es como se ve una empresa con CI/CD maduro.

---

# 📝 Examen rápido (para que piensen)

Responda estas preguntas antes de revisar el resumen:

1. ¿Cuál es la diferencia entre Continuous Delivery y Continuous Deployment?
2. ¿Por qué CI es importante para evitar "Integration Hell"?
3. ¿Cuándo conviene una estrategia canary vs blue-green?
4. ¿Qué pasa si la build se rompe y nadie la arregla por una semana?

<details>
<summary>Ver respuestas</summary>

1. Continuous Delivery requiere aprobación humana para deploy a producción; Continuous Deployment es 100% automático sin humanos.
2. Porque integrar el código de muchos developers de una sola vez al mes genera conflictos masivos y bugs difíciles de rastrear.
3. Canary cuando querés validar con tráfico real de forma gradual; Blue-Green cuando necesitás rollback instantáneo y podés costear duplicar infraestructura.
4. La regla de oro de CI se rompe: nadie puede integrar nuevos cambios con confianza, y la deuda técnica se acumula exponencialmente.

</details>

---

## Resumen visual final

```
CI/CD
├── CI — Continuous Integration
│   ├── Integrar frecuentemente
│   ├── Build automática en cada commit
│   ├── Tests automatizados
│   └── Build siempre verde
│
├── CD — Continuous Delivery
│   ├── Artefacto siempre listo
│   ├── Deploy manual con 1 click
│   └── Aprobación humana
│
├── Continuous Deployment
│   ├── Artefacto siempre listo
│   ├── Deploy 100% automático
│   └── Sin humanos en el loop
│
├── Estrategias de despliegue
│   ├── Blue-Green        → switch instantáneo
│   ├── Canary            → gradual con tráfico real
│   ├── Rolling Update    → secuencial sin downtime
│   └── Feature Flags     → código deployado, feature off
│
└── Buenas prácticas
    ├── Commits pequeños
    ├── Pipeline rápido (< 10 min)
    ├── Tests robustos
    ├── Monitoreo post-deploy
    └── Rollback automatizado
```

---

## 📚 Bibliografía recomendada

- 📖 *Continuous Delivery* — Jez Humble, David Farley (el libro de referencia)
- 📖 *The DevOps Handbook* — Gene Kim et al.
- 📖 *Accelerate* — Nicole Forsgren, Jez Humble, Gene Kim (las métricas DORA)
- 🌐 [martinfowler.com](https://martinfowler.com/) — artículos seminales sobre CI/CD
- 🎥 TechWorld with Nana — DevOps y CI/CD en YouTube

---

## Palabras del profesor al cierre

> 🎓 *Recuerden: CI/CD no se trata de instalar Jenkins o GitHub Actions. Se trata de **una cultura de automatización, confianza y mejora continua**. Las herramientas cambian cada 3 años, los principios duran décadas. Si dominan los principios, pueden aprender cualquier herramienta en una semana.*
> 
> *¡Éxito en sus pipelines! 🚀*

---

**Próxima clase:** *Observabilidad — logs, métricas y tracing. Porque después de CI/CD, la pregunta es: "¿está funcionando bien en producción?"*
