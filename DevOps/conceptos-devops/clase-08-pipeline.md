# Clase 08 — ¿Qué es un Pipeline? (Workflow)
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

Un **pipeline** es la secuencia de pasos automatizados que van desde el `git push` del desarrollador hasta que el software está en producción.

Se llama "pipeline" (tubería, ducto) porque el código fluye por etapas en orden, como agua por una cañería. Si una etapa falla, el pipeline se **detiene** — no avanza al siguiente paso.

**Etapas típicas de un pipeline:**

| Etapa | ¿Qué hace? |
|-------|-----------|
| **Lint** | Revisa el estilo y formato del código (como el corrector ortográfico) |
| **Test** | Ejecuta las pruebas automatizadas |
| **Build** | Convierte el código en un paquete instalable (imagen Docker, `.jar`, `.zip`) |
| **Scan** | Busca vulnerabilidades de seguridad en el código y dependencias |
| **Deploy Staging** | Publica en el entorno de prueba (no es producción) |
| **Deploy Prod** | Publica en producción (lo que el usuario final ve) |

Al resultado del Build se le llama **artefacto** — es el paquete listo para instalar en cualquier servidor.

**Herramientas para construir pipelines:**
- **GitHub Actions** — integrado en GitHub
- **Jenkins** — el clásico open source
- **GitLab CI** — integrado en GitLab
- **Azure Pipelines** — de Microsoft
- **AWS CodePipeline** — de Amazon

**Regla de oro:** Si cualquier etapa falla, el pipeline para ahí. El código NO llega a producción hasta que todo esté verde.

---

## Idea gráfica

Dibuja cajas conectadas con flechas:

```
git push
   ↓
[Lint] → [Test] → [Build] → [Scan] → [Staging] → [Prod ✅]
              ↑
         Si falla acá: ❌ STOP
         Nadie avanza
         Se notifica al desarrollador
         Prod no recibe nada

Herramientas que ejecutan cada paso:
  GitHub Actions / Jenkins / GitLab CI
```

Muestra un ejemplo real: abre GitHub Actions de cualquier repo público y señala las etapas.

---

## ¿Qué sigue?

> "Perfecto — ahora sabemos qué es un pipeline y cómo fluye el código hasta producción. Pero ¿qué pasa cuando el código llega a producción y tiene un bug crítico? ¿Cómo volvemos atrás? ¿Cómo deshacemos lo que acabamos de hacer? Eso se llama rollback, y es la siguiente clase."

**→ Siguiente clase: [Clase 09 — ¿Qué es un Rollback?](clase-09-rollback.md)**
