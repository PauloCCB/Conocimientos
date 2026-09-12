# Clase 06 — ¿Qué es Platform Engineering?
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

**Platform Engineering** es la disciplina de construir una **plataforma interna** para que TODOS los equipos de desarrollo de la empresa puedan desplegar su software sin tener que configurar infraestructura desde cero cada vez.

**¿Por qué existe?**

DevOps funciona bien en equipos pequeños. Pero cuando una empresa tiene 50, 100 o 200 equipos de desarrollo, no es práctico que cada uno aprenda Kubernetes, Terraform, configure sus propios pipelines y resuelva los mismos problemas una y otra vez. Eso genera:
- Inconsistencia (cada equipo hace las cosas diferente)
- Pérdida de tiempo (todos resuelven el mismo problema por separado)
- Riesgos de seguridad (cada quien aplica seguridad a su manera)

El equipo de plataforma construye las herramientas comunes y todos las usan.

**El resultado se llama IDP (Internal Developer Platform):**

Un portal interno donde el desarrollador puede en minutos:
- Crear un entorno de pruebas
- Hacer deploy de su aplicación
- Ver sus métricas y logs

Sin abrir un ticket, sin esperar a nadie, sin aprender toda la infraestructura por su cuenta.

**Ejemplo real:** Backstage es una plataforma open source de este tipo, muy usada en la industria.

**Frase clave:** Platform Engineering trata la plataforma como un **producto** — con sus usuarios (los desarrolladores), su roadmap y sus métricas de satisfacción.

---

## Idea gráfica

```
SIN Platform Engineering:
Equipo A → configura Kubernetes, pipelines, monitoreo por su cuenta
Equipo B → configura Kubernetes, pipelines, monitoreo por su cuenta
Equipo C → configura Kubernetes, pipelines, monitoreo por su cuenta
= 3x el trabajo, 3x los errores, 3x el tiempo perdido

CON Platform Engineering:
          ┌─────────────────────────────────┐
          │   IDP (Internal Dev Platform)   │
          │   - Deploy en 1 clic            │
          │   - Pipelines listos            │
          │   - Monitoreo integrado         │
          │   - Seguridad aplicada          │
          └─────────────────────────────────┘
              ↙           ↓          ↘
         Equipo A     Equipo B    Equipo C
         (todos usan lo mismo, sin duplicar trabajo)
```

Tabla de comparación de los 4 roles:

| Rol | Misión en una frase |
|-----|---------------------|
| DevOps Engineer | Automatiza el ciclo de entrega de UNA app |
| Cloud Engineer | Construye y mantiene la infraestructura en la nube |
| SRE | Garantiza que el servicio esté disponible |
| Platform Engineer | Construye las herramientas que TODOS los equipos usan |

---

## ¿Qué sigue?

> "Ya conocemos los roles del ecosistema DevOps. Ahora vamos a entrar en cómo funciona el proceso que más define a DevOps en la práctica: CI/CD. Cuando escuchas 'CI/CD' en una entrevista de trabajo o en una oferta, ¿qué significa exactamente? ¿Qué es CI? ¿Qué diferencia hay entre las dos CDs? Eso es lo siguiente."

**→ Siguiente clase: [Clase 07 — ¿Qué es CI/CD?](clase-07-cicd.md)**
