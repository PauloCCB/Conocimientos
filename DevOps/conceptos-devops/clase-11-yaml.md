# Clase 10 — ¿Qué es YAML?
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

**YAML** (se pronuncia "yamel") es el acrónimo de *"YAML Ain't Markup Language"* — un chiste de programadores que dice "YAML no es un lenguaje de marcado" (aunque en la práctica funciona como uno).

YAML es un **formato para escribir datos de forma que los humanos puedan leerlos fácilmente**. Es el idioma con que le hablas a las herramientas DevOps:
- Los pipelines de **GitHub Actions** se escriben en YAML
- Los manifiestos de **Kubernetes** se escriben en YAML
- Los archivos de **Docker Compose** se escriben en YAML
- Las configuraciones de **Ansible** (playbooks) se escriben en YAML

**Reglas básicas de YAML:**

1. Los datos se escriben como pares `clave: valor`
2. La **indentación** (espacios al inicio) define la jerarquía — ¡importa mucho!
3. Las listas se marcan con `-` al inicio de cada ítem
4. Los comentarios empiezan con `#`
5. **No uses tabulaciones (Tab)** — solo espacios. Un Tab rompe el archivo.

**Ejemplo:**
```yaml
# Información de un curso
nombre: Conceptos DevOps
nivel: principiante
duracion_horas: 8
temas:
  - DevOps
  - CI/CD
  - Docker
  - Kubernetes
activo: true
```

**El error más común en YAML:**

La indentación. Si usas 3 espacios cuando debían ser 2, el archivo se rompe con un error críptico. Las herramientas son muy estrictas con esto.

```yaml
jobs:
  test:           # ← 2 espacios de indentación
    runs-on: ubuntu-latest   # ← 4 espacios (2 más que el padre)
    steps:        # ← 4 espacios (igual que runs-on, mismo nivel)
      - run: npm test   # ← 6 espacios (2 más que steps)
```

---

## Idea gráfica

Muestra en pantalla un YAML de GitHub Actions y señala cada parte con el cursor:

```yaml
name: Pipeline de CI          # ← nombre del pipeline
on: [push]                    # ← cuándo se dispara

jobs:                         # ← lista de trabajos
  test:                       # ← nombre del trabajo
    runs-on: ubuntu-latest    # ← en qué máquina corre
    steps:                    # ← pasos del trabajo
      - name: Instalar deps   # ← paso 1 (es una lista, lleva -)
        run: npm install
      - name: Ejecutar tests  # ← paso 2
        run: npm test
```

Luego muestra qué pasa si la indentación está mal — el error que da YAML.

---

## ¿Qué sigue?

> "YAML es el formato que usan las herramientas DevOps para configuraciones. Pero cuando los sistemas se comunican entre sí por internet — cuando una app le habla a otra app — usan otro formato diferente que se llama JSON. ¿Qué es JSON y en qué se diferencia de YAML? Siguiente clase."

**→ Siguiente clase: [Clase 11 — ¿Qué es JSON?](clase-11-json.md)**
