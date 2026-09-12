# Clase 01 — ¿Qué es DevOps?
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

El nombre es la unión de dos palabras:
- **Dev** = Development (los que escriben el software)
- **Ops** = Operations (los que ponen a funcionar el software)

Antes de DevOps, estos dos equipos vivían separados como departamentos enemigos:
- El equipo de **Dev** escribía el código y lo "lanzaba por encima del muro".
- El equipo de **Ops** lo recibía sin saber cómo funcionaba y tenía que lidiar con todo.
- Cuando algo fallaba en producción, Dev decía: *"En mi máquina funciona"*. Ops decía: *"El código estaba malo"*.

DevOps **NO es un software**. **NO es una herramienta**. Es una **forma de trabajar** donde ambos equipos comparten responsabilidad de todo el ciclo: desde escribir el código hasta que el usuario lo usa.

El símbolo de DevOps es el **infinito (∞)** porque el ciclo nunca termina:

```
Plan → Code → Build → Test → Release → Deploy → Operate → Monitor → (vuelve a Plan)
```

**DevOps sí es:**
- Automatizar tareas que se hacían a mano (build, test, deploy)
- Medir lo que pasa en producción (logs, métricas, alertas)
- Reducir el tiempo entre "código listo" y "usuario usando la funcionalidad"
- Responsabilidad compartida: quien escribe el código también se preocupa por cómo opera

**DevOps NO es:**

| ❌ Error común | ✅ La realidad |
|----------------|----------------|
| "DevOps es instalar Docker" | Docker es una herramienta; DevOps es la cultura |
| "DevOps es un equipo separado" | Es una forma de trabajar de todo el equipo |
| "Si tenemos Jenkins, hacemos DevOps" | Las herramientas ayudan, pero sin cultura no hay DevOps |
| "DevOps garantiza cero errores" | Garantiza que los errores se detecten y corrijan más rápido |

---

## Idea gráfica

Dibuja en pizarra o diapositiva:

```
ANTES:
[EQUIPO DEV]  ===muro===  [EQUIPO OPS]  →  [USUARIO 😤]
  "funciona en mi máquina"   "llegó roto"

DESPUÉS (DevOps):
[DEV + OPS juntos] ──────────────────────► [USUARIO 😊]
         ↑___________feedback___________↑
```

Luego dibuja el símbolo ∞ con las 8 fases escritas en cada curva.

---

## ¿Qué sigue?

> "Ahora ya sabemos que DevOps es una cultura de colaboración entre Dev y Ops. Pero hay algo que notaron las empresas: cuando Dev y Ops trabajan juntos y entregan software más rápido... ¿qué pasa con la **seguridad**? ¿Se revisa al final o desde el inicio? Eso lo veremos en la siguiente clase."

**→ Siguiente clase: [Clase 02 — ¿Qué es DevSecOps?](clase-02-devsecops.md)**
