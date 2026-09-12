# Clase 14 — ¿Qué es Linux?
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

**Linux** es un **sistema operativo** open source basado en Unix. No es una empresa ni un solo producto: hay muchas **distribuciones** (Ubuntu, Debian, Amazon Linux, RHEL) que comparten el mismo **kernel** (el núcleo que habla con el hardware).

En DevOps y cloud, Linux es el estándar: la mayoría de servidores, contenedores y clusters de Kubernetes corren sobre Linux.

---

## ¿Por qué Linux en DevOps?

- **Servidores en la nube:** EC2, VMs, nodos de Kubernetes — casi siempre Linux
- **Contenedores:** Docker comparte el kernel del host; en producción ese host es Linux
- **Automatización:** scripts, Ansible, pipelines — se ejecutan en shell Linux
- **Costo y flexibilidad:** sin licencias de Windows Server en cada nodo; tooling masivo open source
- **Estándar de la industria:** documentación, tutoriales y herramientas asumen terminal Linux

---

## Conceptos clave

| Concepto | Qué es |
|----------|--------|
| **Kernel** | Núcleo del SO: memoria, CPU, disco, red, procesos |
| **Shell** | Interfaz de línea de comandos (bash es la más común) |
| **Proceso** | Un programa en ejecución (cada contenedor es un proceso aislado en el host) |
| **Usuario / permisos** | Quién puede leer, escribir o ejecutar archivos (`rwx`) |
| **systemd** | Servicio que arranca y supervisa otros servicios al boot (ej. nginx, ssh) |
| **Archivo** | En Linux casi todo es un archivo: configs, logs, dispositivos |

---

## Comandos básicos que verás siempre

| Comando | ¿Qué hace? |
|---------|-----------|
| `pwd` | Muestra en qué carpeta estás |
| `ls -la` | Lista archivos (incluye ocultos y permisos) |
| `cd /ruta` | Cambia de directorio |
| `cat archivo` | Muestra el contenido de un archivo |
| `grep "texto" archivo` | Busca texto dentro de archivos o logs |
| `ps aux` | Lista procesos en ejecución |
| `top` o `htop` | Uso de CPU y memoria en tiempo real |
| `df -h` | Espacio en disco |
| `free -h` | Memoria RAM disponible y usada |
| `journalctl -u servicio` | Logs de un servicio gestionado por systemd |

No necesitas memorizarlos todos hoy — solo reconocer que **DevOps = mucha terminal Linux**.

---

## Idea gráfica

```
┌──────────────────────────────────────────────────────────┐
│  SERVIDOR LINUX (EC2, VM, nodo K8s)                       │
│  ┌────────────────────────────────────────────────────┐  │
│  │  Kernel Linux — CPU · RAM · Disco · Red            │  │
│  └────────────────────────────────────────────────────┘  │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐   │
│  │ nginx    │ │ tu app   │ │ docker   │ │ sshd     │   │
│  │ (proceso)│ │ (proceso)│ │ (daemon) │ │ (acceso) │   │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘   │
│                                                          │
│  Tú te conectas:  ssh usuario@servidor                   │
│  Trabajas en:     shell (bash) + comandos                │
└──────────────────────────────────────────────────────────┘
```

---

## Quick win

Abre una terminal (WSL, Linux o Cloud Shell) y ejecuta: `uname -a` (versión del kernel), `pwd`, `ls`, `free -h`. Con eso ya viste el entorno donde correrá casi todo lo que despliegues.

---

## ¿Qué sigue?

> "Linux gestiona memoria y procesos. ¿Qué pasa cuando un servidor o un contenedor se queda sin RAM? Ahí entra el OOM — Out Of Memory — y el OOM Killer. Es uno de los errores más comunes en producción. Siguiente clase."

**→ Siguiente clase: [Clase 15 — ¿Qué es OOM (Out Of Memory)?](clase-15-oom.md)**
