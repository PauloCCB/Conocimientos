# Clase 05 — ¿Qué es un Cloud Engineer?
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

El **Cloud Engineer** es el especialista en infraestructura en la nube. Su trabajo es construir, mantener y optimizar todo lo que vive en AWS, Azure o Google Cloud.

**El contexto:**

Antes de la nube, las empresas compraban servidores físicos y los metían en un cuarto propio. Alguien los configuraba a mano, uno por uno, tardando días. Hoy la nube permite alquilar recursos bajo demanda — si necesitas más capacidad, la pides en minutos; si ya no la necesitas, la devuelves y dejas de pagar. El Cloud Engineer es quien sabe manejar ese mundo.

**¿Qué hace un Cloud Engineer?**
- Crea y configura recursos en la nube: máquinas virtuales, bases de datos, redes, almacenamiento
- Gestiona permisos (quién puede acceder a qué — principio de mínimo privilegio)
- Controla y optimiza los costos de la nube
- Diseña arquitecturas que sean resilientes y escalables
- Conoce profundamente los servicios de uno o varios proveedores cloud

**Herramientas:** AWS (EC2, S3, VPC, IAM, RDS), Azure, GCP, Terraform

---

## Idea gráfica

Dibuja dos columnas:

```
ANTES (On-premise)          HOY (Nube / Cloud)
──────────────────          ─────────────────────
Compras el servidor         Alquilas el servidor
Tardas semanas              Tardas minutos
Pagas aunque no lo uses     Pagas solo lo que usas
Tú lo reparas               El proveedor lo mantiene
Capacidad fija              Escala automáticamente
Sysadmin lo configura       Cloud Engineer lo codifica
a mano                      con IaC
```

Luego escribe los 3 grandes proveedores:

```
☁️ AWS    ☁️ Azure    ☁️ GCP
(Amazon)  (Microsoft)  (Google)
```

---

## ¿Qué sigue?

> "Ya conocemos 4 roles: DevOps Engineer, SRE, Cloud Engineer... y hay un cuarto que es más reciente y que resuelve un problema nuevo: ¿qué pasa cuando una empresa tiene 50 o 100 equipos de desarrollo? No es práctico que cada uno configure su propia infraestructura. Necesitan una plataforma compartida. La persona que la construye se llama Platform Engineer. Eso es lo siguiente."

**→ Siguiente clase: [Clase 06 — ¿Qué es Platform Engineering?](clase-06-platform-engineering.md)**
