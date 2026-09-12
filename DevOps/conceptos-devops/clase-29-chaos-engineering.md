# Clase 29 — ¿Qué es Chaos Engineering?
> Curso Conceptos DevOps · Clase Final

---

## ¿Qué es?

**Chaos Engineering** = romper cosas a propósito, de forma controlada, para descubrir debilidades antes de que los usuarios las encuentren.

La definición oficial del manifiesto *Principles of Chaos Engineering*:
> *"Chaos Engineering es la disciplina de experimentar sobre un sistema distribuido para construir confianza en la capacidad del sistema de resistir condiciones turbulentas en producción."*

---

---

## ¿Cómo se hace un experimento de Chaos Engineering?

El proceso sigue el método científico:

**1. Define el "estado estable" (steady state)**
¿Cómo luce el sistema cuando todo funciona bien? Ejemplo: "99.9% de peticiones exitosas, latencia menor a 200ms."

**2. Formula una hipótesis**
"Creemos que si el servidor de caché Redis falla, la app seguirá funcionando aunque un poco más lenta, porque tiene fallback a la base de datos."

**3. Introduce el caos**
Apaga el servidor Redis. O inyecta 500ms de latencia extra en la red. O agota la memoria. O corta la conexión a la base de datos.

**4. Observa los resultados**
¿El sistema mantuvo su estado estable? ¿La hipótesis fue correcta? ¿O el sistema colapsó?

**5. Aprende y mejora**
Si el sistema colapsó → encontraste un punto débil. Arréglalo **antes** de que lo encuentren tus usuarios en la peor noche del año.

---

---

## Herramientas

| Herramienta | Quién la hace | Qué hace |
|------------|--------------|---------|
| **Chaos Monkey** | Netflix | Apaga instancias EC2 aleatoriamente |
| **Gremlin** | Gremlin Inc. | Plataforma empresarial de chaos engineering |
| **LitmusChaos** | CNCF (open source) | Experimentos de caos para Kubernetes |
| **AWS FIS** | Amazon | Fault Injection Simulator nativo de AWS |
| **Chaos Toolkit** | Open source | Framework para definir experimentos como código |

---

## Idea gráfica

```
SIN Chaos Engineering:
Sistema → [todo parece bien] → 💥 FALLO inesperado
          "no sabíamos que eso podía fallar"
          downtime, usuarios afectados

CON Chaos Engineering:
Experimento controlado (horario laboral):
  → Apago el servidor de caché Redis intencionalmente
  → Observo: ¿la app sigue funcionando?
    ✅ Sí, con fallback: el sistema es resiliente
    ❌ No, todo colapsó: encontré el punto débil ANTES de que lo encuentren los usuarios

Método científico aplicado:
Hipótesis → Experimento → Observación → Mejora → Repetir
```

---

## Resumen del curso completo

Llegaste al final del recorrido. Mira todo lo que cubrimos:

```
CULTURA:
  Clase 01: DevOps — la forma de trabajar que une Dev y Ops
  Clase 02: DevSecOps — la seguridad integrada desde el inicio
  
ROLES:
  Clase 03: DevOps Engineer — el que automatiza la entrega
  Clase 04: SRE — el que garantiza la disponibilidad
  Clase 05: Cloud Engineer — el especialista en la nube
  Clase 06: Platform Engineer — el que construye herramientas para todos
  
ENTREGA:
  Clase 07: CI/CD — integración y entrega continua
  Clase 08: Pipeline — la línea de montaje del software
  Clase 09: Rollback — el Ctrl+Z de producción
  
FORMATOS:
  Clase 10: YAML — el idioma de las configuraciones DevOps
  Clase 11: JSON — el idioma de las APIs
  
CÓDIGO:
  Clase 12: SCM y Git — el control de versiones
  Clase 13: GitHub — la plataforma de colaboración
  Clase 14: IaC — infraestructura como código
  Clase 15: Terraform — la herramienta de IaC multi-nube
  Clase 16: CloudFormation — IaC nativo de AWS
  Clase 17: CaC y Ansible — configuración del servidor
  
CONTENEDORES:
  Clase 18: Docker — empaqueta la app y todo lo que necesita
  Clase 19: Docker Compose — múltiples contenedores juntos
  Clase 20: Kubernetes — orquestación a escala
  
OPERACIONES:
  Clase 21: GitOps — Git como fuente de verdad para infra
  Clase 22: Crossplane — Kubernetes como panel de control universal
  Clase 23: Observabilidad — Logs, Métricas y Trazas
  Clase 24: Chaos Engineering — romper para fortalecer
```

**El siguiente paso:** empezar a practicar. El conocimiento sin práctica se olvida. Elige uno de estos conceptos y ponlo en marcha esta semana.
