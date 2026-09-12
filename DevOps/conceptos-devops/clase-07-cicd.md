# Clase 07 — ¿Qué es CI/CD? (CI, CD Entrega y CD Despliegue)
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

**CI/CD** son las prácticas que automatizan el proceso de llevar código del desarrollador hasta los usuarios. Son tres conceptos distintos que a veces se usan juntos:

---

### CI — Continuous Integration (Integración Continua)

Cuando un desarrollador sube código a Git (`git push`), automáticamente se dispara un proceso que:
1. Compila el código (verifica que no tenga errores de sintaxis)
2. Ejecuta los tests automáticos
3. Analiza la calidad del código
4. Notifica al desarrollador si algo falla — de inmediato, no horas después

La "integración" es mezclar el código nuevo con el código existente de todos los demás del equipo. La "continua" es que pasa **cada vez que alguien sube un cambio**, no una vez a la semana.

**Antes de CI:** los desarrolladores trabajaban en sus ramas durante semanas y luego intentaban unir todo. Eso se llamaba "integration hell" — el código de un chocaba con el del otro y arreglar todo era una pesadilla.

**Con CI:** integras pequeños cambios frecuentes y los problemas se detectan de inmediato, cuando son fáciles de arreglar.

---

### CD1 — Continuous Delivery (Entrega Continua)

Automatiza todo el proceso hasta tener el software **listo para publicar**. El deploy al servidor de producción puede hacerse, pero requiere que alguien lo apruebe o pulse un botón.

La clave: el software está **SIEMPRE** listo para salir a producción. En cualquier momento, alguien puede decir "despliégalo" y funciona.

---

### CD2 — Continuous Deployment (Despliegue Continuo)

Va un paso más allá: si todos los tests pasan, el software llega a producción **automáticamente, sin que nadie pulse ningún botón**.

Netflix, Amazon y empresas similares hacen miles de deploys al día de esta forma.

---

## Resumen de los tres

| Sigla | Nombre | ¿Requiere intervención humana para producción? |
|-------|--------|----------------------------------------------|
| **CI** | Integración Continua | No aplica — solo construye y prueba |
| **CD Delivery** | Entrega Continua | ✅ Sí — alguien aprueba el deploy |
| **CD Deployment** | Despliegue Continuo | ❌ No — sale automático si los tests pasan |

---

## Idea gráfica

```
git push
    │
    ▼
┌────────────────────────────────────────────────┐
│  CI (Integración Continua)                      │
│  ✓ Compilar   ✓ Tests   ✓ Análisis de código   │
└────────────────────────────────────────────────┘
    │
    ▼ (si todo pasa ✅)
┌────────────────────────────────────────────────┐
│  CD1: Continuous DELIVERY                       │
│  Artefacto listo → 🟡 Espera aprobación humana │
└────────────────────────────────────────────────┘
    │
    ▼ (si la empresa tiene madurez suficiente)
┌────────────────────────────────────────────────┐
│  CD2: Continuous DEPLOYMENT                     │
│  → Producción automáticamente ✅               │
│  (sin intervención, solo si los tests pasan)   │
└────────────────────────────────────────────────┘
```

---

## ¿Qué sigue?

> "Entendemos CI/CD como concepto. Pero cuando escuchas 'el pipeline falló' o 'el pipeline está corriendo' — ¿qué es exactamente un pipeline? ¿Qué pasos tiene? ¿Cómo funciona por dentro? Eso lo vemos en la siguiente clase."

**→ Siguiente clase: [Clase 08 — ¿Qué es un Pipeline?](clase-08-pipeline.md)**
