# Clase 25 — ¿Qué es Helm?
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

**Helm** es el **gestor de paquetes de Kubernetes**. Instalas aplicaciones complejas en un cluster con un comando, igual que `apt` en Ubuntu o `npm` en Node.js — pero para manifiestos de K8s.

Un paquete en Helm se llama **Chart**. Una **Release** es una instalación concreta de ese chart en tu cluster (con tu configuración y versión).

---

## ¿Qué problema resuelve?

Desplegar una app en Kubernetes puede requerir **decenas de YAML**: Deployment, Service, Ingress, ConfigMap, Secret, ServiceAccount, HPA, etc.

Sin Helm:
- Copias y pegas YAML de internet
- Editas a mano cada archivo por entorno
- No tienes historial claro de qué versión quedó instalada

Con Helm:
- Un chart empaqueta todos los recursos
- **Values** (`values.yaml`) parametrizan entorno (dev vs prod) sin duplicar YAML
- `helm upgrade` y `helm rollback` gestionan versiones de la release

---

## Conceptos clave

| Concepto | ¿Qué es? |
|----------|----------|
| **Chart** | Plantilla empaquetada (carpeta o `.tgz`) con templates + values por defecto |
| **Release** | Instancia instalada de un chart en un namespace (ej. `my-nginx` v3) |
| **Values** | Variables que rellenan las plantillas (réplicas, imagen, dominio) |
| **Repository** | Catálogo de charts (Helm Hub, Artifact Hub, repos privados) |
| **Template** | YAML con placeholders `{{ .Values.replicaCount }}` que Helm renderiza |

---

## Comandos básicos

| Comando | ¿Qué hace? |
|---------|-----------|
| `helm repo add bitnami https://charts.bitnami.com/bitnami` | Añade un catálogo de charts |
| `helm search repo nginx` | Busca charts disponibles |
| `helm install mi-release bitnami/nginx` | Instala el chart en el cluster |
| `helm list` | Lista releases instaladas |
| `helm upgrade mi-release bitnami/nginx -f values-prod.yaml` | Actualiza con nueva config o versión |
| `helm rollback mi-release 2` | Vuelve a la revisión anterior |
| `helm uninstall mi-release` | Elimina la release del cluster |

---

## Helm vs YAML manual vs GitOps

| Enfoque | Cuándo tiene sentido |
|---------|---------------------|
| **YAML manual** | Aprender K8s, apps muy simples, un solo entorno |
| **Helm** | Apps estándar (nginx, prometheus, postgres), muchos entornos, reutilizar charts públicos |
| **GitOps (Argo CD / Flux)** | El cluster sigue lo que hay en Git; Helm puede ser **cómo empaquetas** lo que GitOps aplica |

Helm no reemplaza Kubernetes ni GitOps — **organiza y versiona** despliegues sobre K8s.

---

## Idea gráfica

```
Chart "mi-api"                    Values (prod)
├── templates/                    replicaCount: 5
│   ├── deployment.yaml    +      image: api:v2.1
│   ├── service.yaml              host: api.midominio.com
│   └── ingress.yaml
        │
        ▼  helm install / upgrade
┌─────────────────────────────────────────┐
│  Cluster Kubernetes                      │
│  Release: mi-api-prod (revision 4)       │
│  → Deployment 5 Pods                     │
│  → Service + Ingress                     │
└─────────────────────────────────────────┘
```

---

## Quick win

Instala un chart público en un cluster de prueba: `helm install demo bitnami/nginx` y luego `helm list`. En un minuto tienes nginx corriendo sin escribir YAML desde cero.

---

## ¿Qué sigue?

> "Helm empaqueta y despliega en Kubernetes. La forma moderna de mantener el cluster alineado con lo que el equipo aprueba en Git se llama GitOps: el repositorio es la fuente de verdad y el cluster se sincroniza solo. Siguiente clase."

**→ Siguiente clase: [Clase 26 — ¿Qué es GitOps?](clase-26-gitops.md)**
