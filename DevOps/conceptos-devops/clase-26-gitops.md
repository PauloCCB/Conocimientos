# Clase 26 — ¿Qué es GitOps?
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

**GitOps** = Git + Operations. Es una forma de gestionar infraestructura y aplicaciones donde **Git es la única fuente de verdad** para todo lo que debe existir en el sistema.

La idea central: todo lo que quieres que corra en tu infraestructura está escrito en archivos que viven en un repositorio Git. Un agente automático monitorea ese repositorio y sincroniza el sistema real con lo que dice Git — sin intervención humana.

---

## Los 4 principios de GitOps

1. **Todo declarativo:** defines el estado que QUIERES, no los pasos para llegar ahí. "Quiero 3 réplicas de esta app corriendo." No "ejecuta este comando, luego este otro".

2. **Git como única fuente de verdad:** Git contiene la versión oficial de toda la configuración. Si no está en Git, no existe.

3. **Cambios solo por Pull Request:** cualquier cambio al sistema debe pasar por un PR — con revisión, aprobación y trazabilidad. No se hacen cambios manuales directamente en el servidor.

4. **Reconciliación continua:** un agente (ArgoCD o Flux) compara constantemente el estado real del sistema con lo que dice Git. Si hay diferencia, la corrige automáticamente.

---

## ¿Qué pasa si alguien hace un cambio manual?

En GitOps, si alguien entra al cluster de Kubernetes y hace un cambio manual (edita una configuración directamente), el agente lo detecta y lo **revierte automáticamente** para que el sistema quede igual a lo que dice Git.

Esto garantiza que lo que Git dice y lo que realmente corre en producción siempre coincidan.

---

## Herramientas principales

| Herramienta | ¿Qué hace? |
|------------|-----------|
| **ArgoCD** | Agente de GitOps para Kubernetes. Monitorea Git y sincroniza el cluster |
| **Flux** | Alternativa open source a ArgoCD, creada por Weaveworks |

---

## Idea gráfica

Dibuja el flujo completo:

```
FLUJO GITOPS:

1. Desarrollador:
   git commit → git push → Pull Request → revisión → merge

2. Git (repositorio):
   Estado deseado guardado en archivos YAML

3. ArgoCD (agente — corre dentro del cluster):
   Monitorea el repo cada X segundos
   Detecta: "en Git dice 5 réplicas, en el cluster hay 3"
   Acción: crea 2 réplicas más automáticamente

4. Cluster Kubernetes:
   Estado real = exactamente lo que dice Git ✅

¿Cambio manual directo en el cluster?
  Alguien ejecuta kubectl edit deployment... → cambia algo
  ArgoCD detecta → REVIERTE → Git manda siempre
```

GitOps vs CI/CD tradicional:

```
CI/CD tradicional (push):
  Pipeline → ejecuta kubectl apply → empuja cambios al cluster

GitOps (pull):
  ArgoCD (dentro del cluster) → lee Git → jala cambios al cluster
  (el cluster "jala", no alguien le "empuja")
```

---

## ¿Qué sigue?

> "Con GitOps sabemos que el sistema siempre está en el estado correcto y se reconcilia automáticamente. Pero, ¿qué pasa con la infraestructura subyacente como bases de datos o buckets? ¿Podemos gestionarla también desde Kubernetes? Sí, con una herramienta llamada Crossplane. Siguiente clase."

**→ Siguiente clase: [Clase 27 — ¿Qué es Crossplane?](clase-27-crossplane.md)**
