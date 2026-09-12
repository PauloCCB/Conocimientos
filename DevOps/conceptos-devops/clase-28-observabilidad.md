# Clase 28 — ¿Qué es Monitoreo y Observabilidad?
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

**Monitoreo** = estar pendiente de si el sistema funciona bien. Recolectas datos predefinidos y generas alertas cuando algo supera un umbral.

**Observabilidad** = poder entender POR QUÉ el sistema se comporta como se comporta, incluso en situaciones que nadie anticipó. Es una propiedad del sistema: está diseñado para que puedas hacerle cualquier pregunta sobre su estado interno.

**La diferencia clave:**
- El monitoreo te dice que **algo está mal** ("el CPU está al 98%")
- La observabilidad te ayuda a entender **qué, dónde y por qué** ("el proceso X consume el 98% de CPU porque la función Y tiene un loop infinito cuando el parámetro Z llega vacío")

En la práctica, la observabilidad se construye sobre **3 pilares**:

---

## Pilar 1 — Logs (Registros)

Son el historial de eventos que ocurrieron en el sistema. Cada línea registra qué pasó, cuándo, en qué componente y con qué detalle.

```
2026-06-05 14:23:01 ERROR  [auth-service] User 123 login failed - invalid password
2026-06-05 14:23:05 INFO   [payment-service] Payment processed for order #456 - $29.99
2026-06-05 14:23:06 WARN   [db-service] Connection pool at 90% capacity
```

**Niveles de log más comunes:**
- `DEBUG` → información detallada para desarrollo
- `INFO` → eventos normales del sistema
- `WARN` → algo inusual pero no es error todavía
- `ERROR` → algo falló, hay que revisarlo
- `FATAL` → fallo crítico, el servicio se cae

**Herramientas:** Elasticsearch + Kibana (ELK Stack), CloudWatch Logs, Datadog, Grafana Loki

---

## Pilar 2 — Métricas

Son mediciones numéricas del sistema a lo largo del tiempo. Se grafican para ver tendencias y detectar anomalías.

**Ejemplos de métricas:**
- CPU usage: 45% → 72% → 98% (alerta: servidor saturado)
- Memoria usada: 4GB de 8GB disponibles
- Peticiones por segundo: 1,200 req/s
- Tiempo de respuesta promedio: 120ms
- Tasa de errores: 0.1% de peticiones fallan

**Las métricas responden a la pregunta:** ¿cómo está el sistema ahora y en el tiempo?

**Herramientas:** Prometheus (recolecta) + Grafana (visualiza dashboards), CloudWatch Metrics, Datadog

---

## Pilar 3 — Trazas (Traces)

Registran el **camino completo** que siguió una petición a través de múltiples servicios. Esenciales en arquitecturas de microservicios.

```
Usuario pide /checkout:
  → API Gateway:          2ms
  → Servicio de carrito:  15ms
  → Servicio de pagos:    ← 850ms ⚠️ (cuello de botella)
  → Base de datos:        45ms
  → Servicio de email:    30ms
  Total: 942ms

Sin trazas: "el checkout es lento" (¿dónde exactamente?)
Con trazas: "el servicio de pagos tarda 850ms, hay que investigarlo"
```

**Herramientas:** Jaeger, Zipkin, AWS X-Ray, Datadog APM

---

## Idea gráfica

Dibuja tres paneles lado a lado:

```
📝 LOGS                    📊 MÉTRICAS              🔍 TRAZAS
──────────────             ──────────────────       ────────────────────
14:23 ERROR:               CPU: ████░░ 70%          Petición /api/pay:
  login failed             RAM: ████░░ 60%            → App:     2ms
14:24 INFO:                Req/s: 1,200              → DB:     150ms ⚠️
  payment OK               Error%: 0.1%              → Pagos:  80ms
14:25 WARN:                Latencia: 120ms           Total:   232ms
  pool al 90%

"¿Qué pasó?"              "¿Cómo está?"            "¿Dónde está
                                                      el problema?"
```

---

## ¿Qué sigue?

> "El monitoreo nos dice si algo está mal. La observabilidad nos ayuda a entender por qué. Pero hay una disciplina que va más allá — no espera a que algo falle para descubrirlo. Rompe el sistema a propósito, en condiciones controladas, para encontrar debilidades antes de que los usuarios las encuentren. Se llama Chaos Engineering, y es la última clase."

**→ Siguiente clase: [Clase 29 — ¿Qué es Chaos Engineering?](clase-29-chaos-engineering.md)**
