# Clase 24 — ¿Qué es Kubernetes?
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

**Kubernetes** (también llamado **K8s** — el "8" reemplaza las 8 letras entre la "K" y la "s" en "Kubernetes") es la plataforma para **gestionar contenedores a gran escala**, en múltiples servidores, de forma automática.

Es open source, gestionado por la **CNCF** (Cloud Native Computing Foundation), y es el estándar de la industria para correr aplicaciones en producción a escala.

---

## ¿Qué problema resuelve?

Docker te permite correr un contenedor. Pero ¿qué pasa cuando necesitas:
- Correr 500 contenedores de la misma app para aguantar el tráfico?
- Que si uno se cae, otro arranque automáticamente?
- Distribuirlos entre 20 servidores distintos?
- Actualizar la versión sin que los usuarios noten downtime?
- Escalar de 10 a 500 contenedores cuando hay pico de tráfico, y volver a 10 cuando baja?

Para todo eso existe Kubernetes.

---

## Lo que hace Kubernetes automáticamente

- **Escalado automático:** si tu app recibe más tráfico, K8s crea más contenedores; si baja, los elimina
- **Auto-recuperación (self-healing):** si un contenedor se cae, K8s lo reinicia solo, en segundos
- **Balanceo de carga:** distribuye el tráfico entre todos los contenedores disponibles
- **Deploys sin downtime (rolling updates):** reemplaza contenedores viejos por nuevos gradualmente — el usuario no nota nada
- **Rollback automático:** si la nueva versión falla, K8s vuelve a la anterior

---

## Componentes básicos que debes conocer

| Componente | ¿Qué es? |
|-----------|---------|
| **Cluster** | El conjunto de todos los servidores que Kubernetes gestiona |
| **Nodo (Node)** | Cada servidor físico o virtual dentro del cluster |
| **Pod** | La unidad mínima. Uno o más contenedores que van juntos en el mismo nodo |
| **Deployment** | Define cuántos Pods quieres y qué imagen usan |
| **Service** | Expone los Pods al exterior con una IP/DNS estable |
| **Namespace** | Espacio para organizar recursos (como carpetas) |

---

## Idea gráfica

```
SIN Kubernetes (Docker manual en producción):
  Servidor 1: contenedor A, B, C   ← lo gestionas tú a mano
  Servidor 2: contenedor D, E      ← si se cae: tú lo arreglas
  Problema: no escala, no se recupera solo

CON Kubernetes:
┌─────────────────────────────────────────────────┐
│  Kubernetes Cluster                              │
│  ┌───────────┐  ┌───────────┐  ┌───────────┐   │
│  │  Nodo 1   │  │  Nodo 2   │  │  Nodo 3   │   │
│  │ [Pod][Pod]│  │ [Pod][Pod]│  │ [Pod][Pod]│   │
│  └───────────┘  └───────────┘  └───────────┘   │
│                                                  │
│  K8s gestiona automáticamente:                   │
│  ✅ Si un Pod falla → crea otro                  │
│  ✅ Más tráfico → escala a 20 Pods               │
│  ✅ Nueva versión → rolling update sin downtime  │
└─────────────────────────────────────────────────┘
Tú solo declaras: "quiero 10 réplicas de esta imagen"
K8s hace el resto.
```

---

## ¿Qué sigue?

> "Kubernetes gestiona contenedores a escala. Desplegar apps complejas implica muchos YAML — Deployments, Services, Ingress, Secrets. Para empaquetar y versionar eso existe Helm, el gestor de paquetes de Kubernetes. Siguiente clase."

**→ Siguiente clase: [Clase 25 — ¿Qué es Helm?](clase-25-helm.md)**
