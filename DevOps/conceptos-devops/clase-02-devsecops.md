# Clase 02 — ¿Qué es DevSecOps?
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

**DevSecOps** = **Dev** + **Sec** + **Ops**. Es DevOps con **seguridad integrada en todo el ciclo**, no revisada al final.

En el modelo tradicional, el equipo de seguridad revisaba el software DESPUÉS de que estaba terminado. Si encontraban un problema grave, todo el trabajo se paralizaba. Eso era tardío, costoso y peligroso.

DevSecOps mueve esa revisión de seguridad al inicio del ciclo. El término clave es **"shift left"** — imagina el ciclo de desarrollo como una línea de tiempo de izquierda a derecha. "Shift left" = mover la seguridad hacia la izquierda (el inicio), no dejarla al final (la derecha).

**Ejemplos concretos de DevSecOps:**
- No guardar contraseñas en el código que se sube a Git (seguridad en la fase Code)
- Escanear automáticamente si una librería que usas tiene vulnerabilidades conocidas (fase Build)
- Definir quién puede hacer deploy a producción con permisos mínimos (fase Release)
- Revisar el código con herramientas automáticas antes de que lo revise un humano (fase Test)

| Sin DevSecOps | Con DevSecOps |
|---------------|---------------|
| Seguridad revisa al final del ciclo | Seguridad se verifica en cada fase |
| Vulnerabilidades se descubren tarde | Se bloquean en Code o Build |
| Parches de emergencia en producción | Menos urgencias, problemas corregidos antes |

---

## Idea gráfica

Muestra el símbolo ∞ de DevOps y agrega un **escudo en cada fase**, no solo al final:

```
SIN DevSecOps:
Plan → Code → Build → Test → Deploy → Monitor → 🛡️ (solo al final)
                                                      ↑
                                              "demasiado tarde"

CON DevSecOps:
Plan 🛡️ → Code 🛡️ → Build 🛡️ → Test 🛡️ → Deploy 🛡️ → Monitor 🛡️

SHIFT LEFT:
[Seguridad aquí →]          NO solo [→→→→ aquí al final]
```

---

## ¿Qué sigue?

> "Entendemos DevOps como cultura y DevSecOps como esa misma cultura con seguridad integrada. Pero alguien tiene que vivir esto en el día a día. ¿Quién es la persona en una empresa que hace todo esto posible? ¿Cómo se llama ese rol y qué hace exactamente? Eso lo vemos a continuación."

**→ Siguiente clase: [Clase 03 — ¿Qué es un DevOps Engineer?](clase-03-devops-engineer.md)**
