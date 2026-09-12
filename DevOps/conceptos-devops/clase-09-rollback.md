# Clase 09 — ¿Qué es un Rollback?
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

Un **rollback** es volver a la versión anterior del software cuando algo sale mal después de un deploy.

Publicas la versión 2.0 de tu app. Los usuarios empiezan a reportar errores graves: pagos que no procesan, login que no funciona, datos que se corrompen. En lugar de quedarte con la versión rota durante horas mientras la arreglas, haces rollback a la versión 1.9 que funcionaba bien — en minutos, no en horas.

**Para que el rollback sea posible, necesitas:**

1. **Versiones etiquetadas** — no guardes solo `latest`. Guarda `v1.9`, `v2.0`, `v2.1`. Así sabes exactamente a qué versión volver.
2. **Historial de deploys** — saber qué se desplegó, cuándo y quién lo hizo.
3. **Artefactos guardados** — la imagen Docker o paquete anterior debe seguir disponible en algún registro.

**Tipos de rollback:**

| Tipo | Complejidad | Tiempo |
|------|------------|--------|
| **Rollback de código** | Baja — redes ployar la versión anterior | Minutos |
| **Rollback de base de datos** | Alta — hay que revertir cambios en los datos | Horas (si es posible) |

**El rollback de datos es el más complejo:** si la nueva versión cambió la estructura de la base de datos (añadió columnas, renombró tablas), volver al código anterior puede ser incompatible con esos cambios. Por eso las buenas prácticas incluyen hacer **migraciones de base de datos reversibles**.

**Técnicas relacionadas:**
- **Blue/Green deployment:** tienes dos entornos idénticos. Uno está activo (Blue), el otro tiene la nueva versión (Green). Si Green falla, cambias el tráfico de vuelta a Blue en segundos.
- **Canary release:** primero mandas la nueva versión solo al 5% de los usuarios. Si todo va bien, gradualmente subes al 100%. Si falla, solo el 5% fue afectado y reviertes fácil.

---

## Idea gráfica

Dibuja una línea de tiempo:

```
v1.8 ──── v1.9 ──── v2.0 (deploy) ──── 💥 FALLO CRÍTICO
                        ←──── ROLLBACK ────┘
                    "regresamos a v1.9 en 5 minutos"
                    usuarios dejan de ver errores

SIN versiones:
???──── ???──── ??? (deploy) ──── 💥 FALLO
                    "¿a qué versión volvemos? ¿Dónde está el backup? 😱"
                    horas de downtime

BLUE/GREEN (el rollback más rápido):
[Blue v1.9 — activo] ←── tráfico vuelve acá
[Green v2.0 — roto]      (en segundos)
```

---

## ¿Qué sigue?

> "Ya sabemos que cuando algo falla podemos volver atrás. Pero el objetivo es que no falle, o que si falla, afecte al menor número de usuarios posible. Para eso existen las estrategias de despliegue: Blue/Green, Canary, Rolling Update. ¿Cómo funciona cada una y cuándo usarla? Siguiente clase."

**→ Siguiente clase: [Clase 10 — Estrategias de Despliegue](clase-10-estrategias-despliegue.md)**
