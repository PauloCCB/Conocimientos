# Clase 13 — Variables de Entorno y Secretos
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

Una **variable de entorno** es un valor de configuración que vive **fuera del código** de tu aplicación. La app lo lee al arrancar — por ejemplo la URL de la base de datos, el puerto del servidor o el entorno (`development`, `staging`, `production`).

Un **secreto** es un tipo especial de variable: contraseñas, tokens de API, claves privadas. Misma idea (configuración externa), pero **nunca** debe quedar en texto plano en el repositorio ni en logs.

**Por qué importan en DevOps:**
- El mismo código corre en dev, staging y producción — solo cambian las variables
- No hardcodeas credenciales en el código fuente
- Pipelines, contenedores y Kubernetes inyectan estos valores al desplegar

---

## Dónde aparecen en la práctica

| Contexto | Cómo se definen |
|----------|-----------------|
| **Terminal local** | `export DB_HOST=localhost` antes de ejecutar la app |
| **Archivo `.env`** | Pares `CLAVE=valor` en un archivo local (no subir a Git) |
| **Docker** | `docker run -e DB_HOST=...` o sección `environment:` en Compose |
| **Kubernetes** | `env:` en el manifiesto del Pod, o `envFrom` desde ConfigMap / Secret |
| **CI/CD** | Variables en GitHub Actions, GitLab CI, Jenkins — marcadas como "secret" |

---

## ConfigMap vs Secret (Kubernetes)

| | ConfigMap | Secret |
|---|---|---|
| **Para qué** | Configuración no sensible (URLs, nombres, flags) | Datos sensibles (passwords, tokens) |
| **En el cluster** | Texto plano (base64, no cifrado por defecto) | Mismo almacenamiento — **no es cifrado fuerte por sí solo** |
| **Buena práctica** | Versionar plantillas en Git | Usar gestores externos (Vault, AWS Secrets Manager, SSM) en producción |

> En producción seria, los Secret de Kubernetes suelen combinarse con herramientas como **External Secrets** o un vault — no confiar solo en "está en un Secret de K8s".

---

## Errores comunes

| ❌ Mal | ✅ Bien |
|--------|---------|
| Password en el código: `password = "admin123"` | Leer de variable: `os.getenv("DB_PASSWORD")` |
| Subir `.env` con credenciales a GitHub | `.env` en `.gitignore`; usar `.env.example` sin valores reales |
| Mismo secreto en dev y producción | Secretos distintos por entorno |
| Imprimir variables en logs de la app | Logs sin datos sensibles; enmascarar tokens |

---

## Idea gráfica

```
┌─────────────────────────────────────────────────────────┐
│  Repositorio Git (código + plantillas)                   │
│  app.py  ·  docker-compose.yml  ·  .env.example         │
│  (sin passwords reales)                                  │
└──────────────────────────┬──────────────────────────────┘
                           │ deploy / pipeline
                           ▼
┌─────────────────────────────────────────────────────────┐
│  Runtime (servidor, contenedor, Pod)                     │
│  Variables:  APP_ENV=production  DB_HOST=...            │
│  Secretos:   DB_PASSWORD=●●●●●●  API_KEY=●●●●●●         │
│  → la app los lee al iniciar                             │
└─────────────────────────────────────────────────────────┘
```

---

## Quick win

Crea un `.env.example` con las claves que tu app necesita (sin valores reales) y añade `.env` al `.gitignore`. Cualquier compañero sabe qué configurar sin que nadie filtre credenciales.

---

## ¿Qué sigue?

> "Las apps y los pipelines corren sobre servidores. En DevOps, casi todos esos servidores usan Linux. Antes de Git y el control de versiones, conviene entender qué es Linux, por qué domina la nube y qué comandos básicos verás cada día. Siguiente clase."

**→ Siguiente clase: [Clase 14 — ¿Qué es Linux?](clase-14-linux.md)**
