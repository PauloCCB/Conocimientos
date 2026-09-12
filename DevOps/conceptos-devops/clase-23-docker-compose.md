# Clase 23 — ¿Qué es Docker Compose?
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

**Docker Compose** es una herramienta para definir y ejecutar **múltiples contenedores a la vez** usando un solo archivo `docker-compose.yml`.

Una aplicación real raramente es un solo contenedor. Normalmente necesitas:
- Un contenedor con la **app** (Node.js, Python, Java...)
- Un contenedor con la **base de datos** (PostgreSQL, MySQL, MongoDB)
- Quizás un contenedor con el **caché** (Redis)
- Quizás un contenedor con el **servidor web** (Nginx)

Sin Docker Compose, tendrías que arrancar cada contenedor por separado con todos sus parámetros, crear manualmente la red para que se comuniquen, gestionar el orden de arranque. Un caos.

Con Docker Compose, describes todo en un archivo y ejecutas **un solo comando** para levantar todo junto.

---

## ¿Cómo funciona?

**Archivo `docker-compose.yml` de ejemplo:**

```yaml
services:
  app:
    image: mi-app:latest
    ports:
      - "3000:3000"       # puerto_tuMáquina:puerto_contenedor
    depends_on:
      - database          # arranca después de que la DB esté lista
    environment:
      - DATABASE_URL=postgresql://postgres:secreto@database:5432/midb

  database:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: secreto
      POSTGRES_DB: midb
    volumes:
      - datos_db:/var/lib/postgresql/data  # persiste los datos

  cache:
    image: redis:7

volumes:
  datos_db:              # volumen para no perder datos al reiniciar
```

**Comandos principales:**

| Comando | ¿Qué hace? |
|---------|-----------|
| `docker compose up` | Levanta todos los contenedores |
| `docker compose up -d` | Los levanta en segundo plano (detached) |
| `docker compose down` | Para y elimina los contenedores |
| `docker compose logs` | Ver los logs de todos los servicios |
| `docker compose ps` | Ver el estado de cada contenedor |

---

## Docker Compose vs Kubernetes

Docker Compose es genial para **desarrollo local** y **proyectos pequeños**. Pero tiene límites:
- Solo funciona en una máquina
- No escala automáticamente
- No recupera contenedores caídos en producción (a gran escala)

Para producción a gran escala, con múltiples servidores y miles de contenedores, necesitas Kubernetes.

| | Docker Compose | Kubernetes |
|---|---|---|
| Escala | Una máquina | Múltiples servidores |
| Uso típico | Desarrollo local | Producción a escala |
| Complejidad | Baja | Alta |
| Auto-recuperación | Básica | Avanzada |

---

## Idea gráfica

```
docker-compose.yml:
┌─────────────────────────────────────────────┐
│  services:                                  │
│    app → puerto 3000                        │
│    database → PostgreSQL                    │
│    cache → Redis                            │
└─────────────────────────────────────────────┘
         ↓ docker compose up
┌────────────┐  ┌──────────────┐  ┌──────────┐
│    App     │──│   Postgres   │  │  Redis   │
│   :3000    │  │    :5432     │  │  :6379   │
└────────────┘  └──────────────┘  └──────────┘
     Red interna (pueden comunicarse entre sí)
     Tu máquina los ve en localhost:3000
```

Muestra en vivo: `docker compose up` en un proyecto simple y cómo los 3 servicios arrancan.

---

## ¿Qué sigue?

> "Docker Compose nos sirve para correr varios contenedores juntos en nuestra máquina o en un servidor pequeño. Pero ¿qué pasa cuando tu app tiene millones de usuarios? ¿Cuando necesitas miles de contenedores en decenas de servidores? ¿Cuando un servidor se cae y necesitas que los contenedores migren solos? Para eso existe Kubernetes — el sistema de orquestación de contenedores más usado en el mundo. Siguiente clase."

**→ Siguiente clase: [Clase 24 — ¿Qué es Kubernetes?](clase-24-kubernetes.md)**
