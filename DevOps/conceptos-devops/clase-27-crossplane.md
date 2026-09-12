# Clase 27 — ¿Qué es Crossplane?
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

**Crossplane** es una extensión para Kubernetes que lo convierte en un "panel de control universal" (Universal Control Plane). Te permite gestionar cualquier infraestructura y servicio en la nube (AWS, Azure, GCP) usando la misma API de Kubernetes.

Básicamente, en lugar de usar una herramienta como Terraform para la infraestructura y Kubernetes para los contenedores, Crossplane unifica todo: usas `kubectl` y archivos YAML de Kubernetes para crear bases de datos, buckets, servidores, y todo lo demás.

---

## ¿Qué problema resuelve?

Hasta ahora, si querías desplegar tu app y su base de datos:
1. Usabas **Terraform** para crear la base de datos RDS en AWS.
2. Usabas **Kubernetes** para desplegar los contenedores de la app.

Esto requiere aprender dos herramientas distintas (HCL para Terraform, YAML para K8s), tener dos pipelines separados, y gestionar los secretos (credenciales de la BD) para pasarlos de un lado al otro.

Con Crossplane, escribes un archivo YAML que dice "quiero un Pod de mi app Y una base de datos RDS", lo envías a Kubernetes, y Crossplane se encarga de hablar con AWS para crear la BD y enlazarla con tu app automáticamente.

---

## Los 3 conceptos clave de Crossplane

1. **Provider (Proveedor):** Es un plugin que le enseña a Crossplane cómo hablar con una nube específica (ej. Provider-AWS, Provider-GCP).
2. **Managed Resource (Recurso Gestionado):** Una representación directa de un recurso de la nube (ej. un RDS de AWS, o un bucket S3) dentro de Kubernetes.
3. **Composite Resource (Recurso Compuesto / Abstracción):** Permite a los Platform Engineers crear "plantillas" simplificadas para los desarrolladores. El dev pide "una base de datos genérica" y Crossplane crea por detrás todo lo complejo (VPC, Subnets, RDS, Reglas de seguridad) según lo definió la plataforma.

---

## Crossplane vs Terraform

Ambos sirven para crear infraestructura como código, pero tienen enfoques diferentes:

| Característica | Terraform | Crossplane |
|---------------|-----------|------------|
| **Formato** | Archivos HCL | Archivos YAML (nativos de K8s) |
| **Ejecución** | Herramienta CLI (`terraform apply`) | Bucle de reconciliación dentro del cluster |
| **Prevención de Drift** | Detecta cambios solo al volver a ejecutar `plan/apply` | Corrige cambios automáticamente en tiempo real (como ArgoCD) |
| **Enfoque principal** | Infraestructura estática y pre-configurada | Control plane continuo e integración con K8s |

---

## Idea gráfica

```
SIN Crossplane (El mundo dividido):

  Terraform ───────> AWS (crea la BD)
      ↓ (pasa credenciales a mano)
  Kubernetes ──────> Corre los contenedores


CON Crossplane (K8s como panel de control universal):

  Desarrollador hace `kubectl apply -f mi-app-completa.yaml`
         │
         ▼
  [ Cluster Kubernetes con Crossplane ]
         ├──> Crea los contenedores (Pods, Services)
         ├──> Habla con AWS y crea la base de datos (RDS)
         └──> Inyecta automáticamente la contraseña en la app

Todo gestionado desde un solo lugar, con el mismo idioma (YAML).
```

---

## ¿Qué sigue?

> "Con herramientas como Kubernetes, GitOps y Crossplane, la automatización es total. Pero cuando tienes todo este ecosistema operando solo, ¿cómo sabes qué está pasando por dentro? ¿Cómo te enteras si la base de datos está lenta o la app está fallando? Para eso existe el monitoreo y la observabilidad. Siguiente clase."

**→ Siguiente clase: [Clase 28 — ¿Qué es Monitoreo y Observabilidad?](clase-28-observabilidad.md)**
