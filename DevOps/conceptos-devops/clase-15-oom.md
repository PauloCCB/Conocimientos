# Clase 15 — ¿Qué es OOM (Out Of Memory)?
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

**OOM** = **Out Of Memory** (sin memoria). Ocurre cuando un sistema — servidor Linux, contenedor Docker o Pod de Kubernetes — **agota la RAM** disponible y ya no puede asignar más.

En Linux, cuando la memoria se satura, entra en acción el **OOM Killer**: el kernel **elige un proceso** y lo termina para liberar RAM y evitar que todo el servidor se congele.

En contenedores y Kubernetes verás el estado **OOMKilled**: el proceso fue matado por falta de memoria.

---

## RAM vs CPU — comportamiento distinto

| Recurso | Si superas el límite | Efecto |
|---------|---------------------|--------|
| **Memoria (RAM)** | Límite alcanzado | Proceso **muerto** de inmediato (OOMKilled) |
| **CPU** | Límite alcanzado | Proceso **ralentizado** (throttling), no muere |

Esta diferencia es clave en entrevistas y en producción: un bug que consume RAM sin límite tumba el Pod; uno que consume CPU solo lo hace más lento.

---

## Causas típicas de OOM

- **Memory leak:** la app reserva memoria y nunca la libera (sube hasta explotar)
- **Límite muy bajo:** el Pod tiene `memory limit: 128Mi` pero la app necesita 512Mi
- **Pico de tráfico:** más usuarios → más objetos en memoria → se agota la RAM
- **Muchos procesos en el mismo nodo:** varios contenedores compiten por la misma RAM del servidor
- **Caché sin control:** buffers, sesiones o colas que crecen sin tope

---

## Cómo detectarlo

**En Kubernetes:**
```bash
kubectl describe pod <nombre-pod>
```
Busca en `Last State` → `Reason: OOMKilled`

**En Linux (servidor):**
```bash
dmesg | grep -i "out of memory"
journalctl -k | grep -i oom
```

**Señales en métricas:**
- Uso de memoria al 95–100 % antes del reinicio
- Pods o contenedores que se reinician solos en bucle (CrashLoopBackOff)

---

## Cómo prevenirlo

| Acción | Detalle |
|--------|---------|
| **Definir requests y limits** | En K8s: cuánta RAM pides y cuánto máximo puedes usar |
| **Probar carga** | Stress tests antes de producción para ver consumo real |
| **Monitorear memoria** | Alertas cuando un servicio supera umbral (ej. > 85 %) |
| **Ajustar réplicas** | Repartir carga entre más Pods en lugar de uno gigante |
| **Revisar el código** | Memory leaks, cachés sin TTL, cargar archivos enormes en RAM |

---

## Idea gráfica

```
RAM del nodo:  [████████████░░]  92%  ← casi llena
                    │
                    ▼
         Kernel: "no hay más RAM"
                    │
                    ▼
         OOM Killer elige proceso
                    │
                    ▼
         Pod "api-backend" → OOMKilled 💀
                    │
                    ▼
         Kubernetes reinicia el Pod (si hay Deployment)
         Usuario puede ver error 502/503 unos segundos
```

---

## Quick win

Si un Pod se reinicia solo, lo primero es `kubectl describe pod` y mirar si dice **OOMKilled**. Si sí: sube el `memory limit` con datos reales (no a ciegas) o corrige el leak — no solo reinicies y esperes.

---

## ¿Qué sigue?

> "Ya vimos Linux y qué pasa cuando se acaba la memoria. Ahora volvemos al código: ¿cómo se guarda, versiona y comparte el software que despliegas? Eso lo hacen Git y los sistemas SCM. Siguiente clase."

**→ Siguiente clase: [Clase 16 — ¿Qué es SCM y Git?](clase-16-scm-git.md)**
