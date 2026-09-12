# Clase 03 — ¿Qué es un DevOps Engineer?
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

El **DevOps Engineer** es el profesional que hace que el código que escriben los desarrolladores llegue a los usuarios de forma rápida, automática y sin interrupciones.

**¿Por qué el nombre parece raro?**

Técnicamente, llamar a alguien "DevOps Engineer" es extraño — DevOps es una cultura, no un cargo. Sería como decir "Agilismo Engineer". Pero el mercado laboral necesitaba un nombre para la persona que automatizaba pipelines, gestionaba infraestructura y trabajaba con equipos de desarrollo. El título se popularizó, el mercado lo adoptó, y hoy es **uno de los cargos más buscados en tecnología**.

**¿Qué hace un DevOps Engineer?**
- Diseña y mantiene pipelines de CI/CD (el proceso automático: build → test → deploy)
- Automatiza deploys: el código aprobado llega solo a producción sin intervención manual
- Gestiona infraestructura como código (los servidores se crean con código, no a mano)
- Trabaja junto a los equipos de desarrollo para que entreguen más rápido
- Monitorea el sistema y responde a alertas

**Herramientas típicas:** GitHub Actions, Jenkins, Terraform, Docker, Kubernetes, AWS/Azure/GCP

**¿De dónde vienen los DevOps Engineers?**

Dos caminos comunes:
- El **Sysadmin** (admin de sistemas) que aprendió a programar, automatizar y trabajar con la nube
- El **Desarrollador** que aprendió a operar sus propios servicios, entender la infra y medir en producción

---

## Idea gráfica

```
Desarrollador escribe código
         ↓
[DevOps Engineer construye y mantiene esto:]
         ↓
┌──────────────────────────────────────────┐
│  Pipeline automático:                    │
│  Lint → Test → Build → Deploy → Monitor  │
└──────────────────────────────────────────┘
         ↓
  Usuario usa la app ✅
  (sin que el Dev haya tocado el servidor)
```

---

## ¿Qué sigue?

> "El DevOps Engineer se enfoca en que el código llegue rápido y bien. Pero hay otro rol que se obsesiona con algo diferente: que el sistema **no se caiga**. Que esté disponible 24/7. Que cuando algo falle, se arregle solo o rápido. Ese rol se inventó en Google y se llama SRE. ¿Qué es un SRE? Eso es la siguiente clase."

**→ Siguiente clase: [Clase 04 — ¿Qué es un SRE?](clase-04-sre.md)**
