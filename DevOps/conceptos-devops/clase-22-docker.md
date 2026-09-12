# Clase 22 — ¿Qué es Docker?
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

**Docker** es una plataforma para crear y ejecutar **contenedores**. Un contenedor es un paquete que incluye la aplicación Y todo lo que necesita para funcionar: el runtime, las librerías, las dependencias y la configuración.

**El problema que resuelve:**

El clásico "en mi máquina funciona, en producción no." ¿Por qué pasaba eso? Porque el desarrollador tenía Python 3.11 en su laptop, el servidor tenía Python 3.8, y la app dependía de Python 3.11. O una librería instalada en la laptop no estaba en el servidor. Con Docker, empaquetas TODO junto — la app no depende de lo que haya instalado en el servidor.

---

## Los tres conceptos clave de Docker

**1. Dockerfile** = La receta

Es un archivo de texto con instrucciones para construir la imagen. Le dice a Docker cómo debe ser el entorno.

```dockerfile
FROM python:3.11          # parte de una imagen base de Python
WORKDIR /app              # la carpeta de trabajo dentro del contenedor
COPY app.py .             # copia el código desde tu máquina al contenedor
RUN pip install flask     # instala las dependencias
CMD ["python", "app.py"]  # comando que se ejecuta al iniciar el contenedor
```

**2. Imagen** = El molde

El resultado de seguir las instrucciones del Dockerfile. Es un paquete cerrado, inmutable. No está corriendo — es como el molde para hacer pasteles.

**3. Contenedor** = La instancia corriendo

Una imagen puesta en ejecución. Puede haber 1, 10 o 1000 contenedores del mismo molde corriendo al mismo tiempo.

---

## Contenedor vs Máquina Virtual

| | Máquina Virtual (VM) | Contenedor Docker |
|---|---|---|
| Qué incluye | App + OS completo + hypervisor | App + librerías (comparte el OS del host) |
| Tamaño | Gigabytes | Megabytes |
| Tiempo de inicio | Minutos | Segundos |
| Aislamiento | Total (OS separado) | Parcial (comparte kernel del host) |
| Uso típico | Ambientes aislados pesados | Microservicios, CI/CD, apps modernas |

---

## Idea gráfica

```
Sin Docker:
  Dev: "funciona en mi máquina"       [Python 3.11, librería X]
  Servidor: "no funciona"             [Python 3.8, sin librería X]
  Problema: entornos diferentes

Con Docker:
  Dev:       [App + Python 3.11 + librerías] = imagen Docker
  Servidor:  [App + Python 3.11 + librerías] = misma imagen Docker
  ✅ Funciona igual en todas partes

Dockerfile → imagen → contenedor:
  Receta    → Molde  → Pastel corriendo

Contenedor vs VM:
┌──────────────────┐    ┌──────────────────┐
│ VM               │    │ Container Docker  │
│ App              │    │ App              │
│ OS completo 8GB  │    │ Libs solo 200MB  │
│ Tarda 2 min      │    │ Inicia en 2 seg  │
└──────────────────┘    └──────────────────┘
```

---

## ¿Qué sigue?

> "Docker nos permite empaquetar una aplicación en un contenedor. Pero las aplicaciones reales no son solo una cosa — necesitan una base de datos, quizás un caché, un servidor web. ¿Cómo hacemos correr varios contenedores juntos, que se comuniquen entre sí, de forma coordinada? Para eso existe Docker Compose. Siguiente clase."

**→ Siguiente clase: [Clase 23 — ¿Qué es Docker Compose?](clase-23-docker-compose.md)**
