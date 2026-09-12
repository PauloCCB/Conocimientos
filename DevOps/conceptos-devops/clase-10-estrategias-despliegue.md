# Clase 10 — Estrategias de Despliegue
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

Una **estrategia de despliegue** define cómo reemplazas la versión vieja de tu aplicación por la nueva, sin romper el servicio y minimizando el impacto a los usuarios.

No todos los deploys son iguales. Dependiendo del riesgo, el tamaño del sistema y el equipo, eliges una estrategia distinta. Las más usadas son:

---

## Blue/Green Deployment

Tienes **dos entornos idénticos** corriendo en paralelo:
- **Blue** = el entorno activo con la versión actual (lo que los usuarios usan ahora)
- **Green** = el entorno con la nueva versión, listo pero sin tráfico

**¿Cómo funciona?**

1. Despliegas la nueva versión en el entorno Green
2. Pruebas en Green sin que ningún usuario lo vea
3. Cuando estás listo, cambias el tráfico de Blue a Green (un switch del load balancer)
4. Los usuarios pasan a la nueva versión en segundos, sin downtime
5. Blue queda vivo como respaldo — si algo falla, el rollback es volver a apuntar al Blue

```
[Load Balancer]
      │
      ▼
  [Blue v1.9] ← tráfico ahora
  [Green v2.0] ← nueva versión probada, sin tráfico

  Switch:
[Load Balancer]
      │
      ▼
  [Blue v1.9] ← rollback disponible
  [Green v2.0] ← tráfico ahora ✅
```

**Ventaja:** rollback instantáneo — solo redirigir el switch de vuelta.
**Desventaja:** necesitas el doble de infraestructura corriendo al mismo tiempo.

---

## Canary Deployment

En vez de cambiar todo el tráfico de golpe, envías la nueva versión solo a **un porcentaje pequeño** de usuarios primero.

El nombre viene de la práctica minera de usar canarios para detectar gases tóxicos — si el canario sobrevive, el ambiente es seguro.

**¿Cómo funciona?**

1. Despliega la nueva versión en algunos servidores (no todos)
2. Dirige el 5% del tráfico a la nueva versión, el 95% sigue en la vieja
3. Monitorea: ¿hay errores? ¿la latencia subió? ¿los usuarios se quejan?
4. Si todo está bien → aumenta gradualmente: 10% → 25% → 50% → 100%
5. Si algo falla → redirige ese 5% de vuelta, solo el 5% fue afectado

```
[Load Balancer]
  │         │
  │ 95%     │ 5%
  ▼         ▼
[v1.9]    [v2.0 ← canary]
           (si falla, solo el 5% lo vio)

Si todo bien → aumentas el %, hasta llegar a 100%
```

**Ventaja:** riesgo mínimo, detección temprana de problemas en producción real.
**Desventaja:** más complejo de gestionar, necesitas buena observabilidad para detectar problemas en el 5%.

---

## Rolling Update (Actualización Gradual)

Reemplazas los servidores o Pods viejos por nuevos **de a poco**, en grupos, sin apagar todo al mismo tiempo.

Es la estrategia predeterminada en Kubernetes.

**¿Cómo funciona?**

Tienes 10 instancias corriendo v1.9:

```
[v1.9][v1.9][v1.9][v1.9][v1.9][v1.9][v1.9][v1.9][v1.9][v1.9]

Rolling update (de a 2 en 2):

Paso 1: apaga 2 v1.9 → levanta 2 v2.0
[v2.0][v2.0][v1.9][v1.9][v1.9][v1.9][v1.9][v1.9][v1.9][v1.9]

Paso 2:
[v2.0][v2.0][v2.0][v2.0][v1.9][v1.9][v1.9][v1.9][v1.9][v1.9]

... hasta completar:
[v2.0][v2.0][v2.0][v2.0][v2.0][v2.0][v2.0][v2.0][v2.0][v2.0] ✅
```

El servicio nunca se cae porque siempre hay instancias disponibles.

**Ventaja:** no necesitas infraestructura extra, zero downtime, simple de configurar.
**Desventaja:** durante el update hay dos versiones corriendo al mismo tiempo — puede causar inconsistencias si la app tiene cambios incompatibles entre versiones.

---

## Rolling Update con Additional Batch

Variante del Rolling Update donde **primero agregas instancias nuevas** antes de apagar las viejas, en vez de reemplazarlas directamente.

**Sin Additional Batch:**
- Apagas 2 instancias viejas → capacity baja a 8 → levantas 2 nuevas → vuelves a 10
- Momentáneamente tienes menos capacidad

**Con Additional Batch:**
- Levantas 2 instancias nuevas extra → capacity sube a 12
- Luego apagas 2 viejas → capacity vuelve a 10
- En ningún momento bajas de capacidad

```
SIN additional batch:
[v1.9 x10] → apaga 2 → [v1.9 x8 + ▼ capacidad] → levanta 2 v2.0 → [v1.9 x8 + v2.0 x2]

CON additional batch:
[v1.9 x10] → levanta 2 v2.0 extra → [v1.9 x10 + v2.0 x2] → apaga 2 v1.9 → [v1.9 x8 + v2.0 x2]
              (12 instancias momentáneamente)                  (capacidad nunca baja de 10)
```

**Cuándo usarlo:** cuando tienes tráfico alto y no puedes permitirte tener aunque sea momentáneamente menos capacidad.
**Costo:** necesitas capacidad extra temporalmente (más servidores por unos minutos).

---

## Comparativa rápida

| Estrategia | Riesgo | Costo extra | Rollback | Complejidad |
|-----------|--------|------------|---------|-------------|
| Blue/Green | Bajo | Alto (doble infra) | Instantáneo | Media |
| Canary | Muy bajo | Bajo | Fácil (redirigir %) | Alta |
| Rolling Update | Medio | Ninguno | Lento (revertir pods) | Baja |
| Rolling + Additional Batch | Medio | Mínimo (temporal) | Lento | Baja |

---

## Idea gráfica

Dibuja los cuatro escenarios en la pizarra con rectángulos representando servidores:

```
BLUE/GREEN: dos bloques, una flecha del LB apuntando a uno u otro
CANARY: un bloque grande (95%) y uno pequeño (5%) con porcentajes visibles
ROLLING: fila de 10 cajas cambiando de color de izquierda a derecha
ROLLING + BATCH: fila de 10 cajas, primero aparecen 2 extras, luego desaparecen 2 viejas
```

---

## ¿Qué sigue?

> "Ya sabemos cómo llegar a producción de forma segura y cómo volver atrás si algo falla. Ahora necesitamos hablar del lenguaje con el que se escriben las configuraciones de todo esto — pipelines, Kubernetes, Docker Compose. Ese lenguaje se llama YAML. Siguiente clase."

**→ Siguiente clase: [Clase 11 — ¿Qué es YAML?](clase-11-yaml.md)**
