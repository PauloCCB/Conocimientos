# Clase 04 — ¿Qué es un SRE? (Site Reliability Engineer)
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

**SRE** (Site Reliability Engineer) es el profesional responsable de que el servicio **no se caiga**. Su enfoque: tratar la operación de sistemas como un problema de ingeniería de software. En vez de apagar incendios a mano, automatiza todo lo que se puede automatizar y mide con precisión lo que no.

**Conceptos clave que maneja un SRE:**

| Concepto | Qué significa |
|---------|--------------|
| **SLI** (Service Level Indicator) | Una métrica que mide si el servicio funciona bien. Ej: "% de peticiones que responden en menos de 200ms" |
| **SLO** (Service Level Objective) | La meta. Ej: "El 99.9% de las peticiones deben responder bien" |
| **Error Budget** | El margen permitido de fallos. Si la meta es 99.9%, el error budget es el 0.1% que puedes fallar. Si lo gastas: paras de lanzar features nuevas hasta estabilizar |
| **Toil** | Trabajo manual y repetitivo que el SRE debe eliminar automatizando |
| **Postmortem sin culpa** | Cuando algo falla, se analiza qué salió mal en el sistema, NO a quién culpar |

**¿Cómo se diferencia del DevOps Engineer?**

| | DevOps Engineer | SRE |
|---|---|---|
| Enfoque | Velocidad de entrega | Confiabilidad del servicio |
| Pregunta clave | ¿Cómo llega el código a producción? | ¿Por qué se cayó? ¿Cómo no vuelve a pasar? |
| Métrica principal | Frecuencia de deploy | Disponibilidad (uptime) |
| Énfasis | Automatización de entrega | Confiabilidad y respuesta a incidentes |

---

## Idea gráfica

Dibuja un medidor tipo semáforo:

```
SLO: 99.9% disponible al mes
(eso = solo 43 minutos de caída permitidos al mes)

Error Budget disponible este mes:
[██████████░░] 80% gastado  ← ¡Cuidado!
                               Si llega a 0: pausamos
                               features nuevas

POSTMORTEM (sin culpa):
❌ "¿Quién lo rompió?"
✅ "¿Qué proceso falló? ¿Cómo lo prevenimos?"
```

---

## ¿Qué sigue?

> "El SRE garantiza que el servicio esté disponible. El DevOps Engineer lleva el código a producción. Pero hay otro rol que no trabaja para un equipo específico — trabaja para TODOS los equipos de la empresa a la vez. Construye las herramientas que todos usan. Se llama Cloud Engineer. ¿Qué hace exactamente? Siguiente clase."

**→ Siguiente clase: [Clase 05 — ¿Qué es un Cloud Engineer?](clase-05-cloud-engineer.md)**
