# Clase 11 — ¿Qué es JSON?
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

**JSON** (JavaScript Object Notation — Notación de Objetos de JavaScript) es otro formato para representar datos en texto plano. Se pronuncia "Jeyson" o "Jayson".

Es el formato estándar con que los programas se comunican entre sí a través de internet (APIs).

**¿En qué se diferencia de YAML?**

YAML se enfoca en ser fácil de leer para humanos. JSON se enfoca en ser fácil de procesar para computadoras. Es más estricto y más verboso.

**Reglas de JSON:**
- Todo el texto va entre **comillas dobles** `"` (nunca simples)
- Los objetos van entre llaves `{}`
- Las listas van entre corchetes `[]`
- Los pares se separan con `,` (coma)
- NO permite comentarios (a diferencia de YAML)

**El mismo ejemplo de YAML, ahora en JSON:**

```json
{
  "nombre": "Conceptos DevOps",
  "nivel": "principiante",
  "duracion_horas": 8,
  "temas": ["DevOps", "CI/CD", "Docker", "Kubernetes"],
  "activo": true
}
```

**¿Dónde aparece JSON en DevOps?**
- Respuestas de APIs (cuando consultas AWS, GitHub, Kubernetes — responden en JSON)
- Archivos de configuración de algunas herramientas (package.json en Node.js)
- Logs estructurados (muchos sistemas generan logs en formato JSON para que sean fáciles de procesar)

---

## Comparación YAML vs JSON

| | YAML | JSON |
|---|---|---|
| Legibilidad humana | Muy alta | Media |
| Comentarios | ✅ Sí (`#`) | ❌ No |
| Uso principal | Configuraciones DevOps | APIs, comunicación entre sistemas |
| Separadores | Indentación | Llaves `{}`, corchetes `[]`, comas `,` |
| Menos caracteres | ✅ Sí | ❌ Más verboso |

---

## Idea gráfica

Pon lado a lado el mismo dato en YAML y JSON:

```
YAML:                          JSON:
──────────────                 ──────────────────────────────
nombre: Jean                   {
edad: 30                         "nombre": "Jean",
herramientas:                    "edad": 30,
  - Docker                       "herramientas": [
  - Terraform                      "Docker",
activo: true                       "Terraform"
                                 ],
                                 "activo": true
                               }
```

Pregunta al grupo: ¿cuál es más fácil de leer para ustedes? (casi siempre eligen YAML)

---

## ¿Qué sigue?

> "YAML y JSON son formatos de configuración. Antes de Git, hay otro bloque esencial: variables de entorno y secretos — cómo las apps reciben URLs, passwords y tokens sin hardcodearlos en el código. Siguiente clase."

**→ Siguiente clase: [Clase 13 — Variables de Entorno y Secretos](clase-13-variables-entorno-secretos.md)**
