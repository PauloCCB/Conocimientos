# Clase 16 — ¿Qué es SCM y Git?
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

**SCM = Source Code Management** (Gestión de Código Fuente) — el conjunto de prácticas y herramientas para controlar y registrar todos los cambios en el código de un proyecto.

**Git** es la herramienta de SCM más usada en el mundo. Está en prácticamente todos los proyectos de software del planeta.

---

## ¿Qué hace Git?

- **Guarda el historial completo:** quién cambió qué, cuándo y con qué mensaje explicativo
- **Permite trabajar en paralelo con ramas (branches):** puedes trabajar en tu nueva funcionalidad sin tocar el código de tu compañero
- **Permite volver a cualquier versión anterior:** como una máquina del tiempo para tu código
- **Permite que múltiples personas trabajen sin pisarse:** cada uno en su rama, luego se unen los cambios

---

## Comandos básicos que debes conocer

| Comando | ¿Qué hace? |
|---------|-----------|
| `git init` | Crea un repositorio Git nuevo en la carpeta actual |
| `git clone <url>` | Descarga un repositorio de internet a tu máquina |
| `git status` | Muestra qué archivos cambiaron |
| `git add .` | Prepara todos los cambios para guardar |
| `git commit -m "mensaje"` | Guarda un punto de control con un mensaje descriptivo |
| `git push` | Sube los cambios al servidor (GitHub, GitLab) |
| `git pull` | Descarga los cambios que otros subieron |
| `git branch` | Lista o crea ramas |
| `git checkout -b mi-feature` | Crea una rama nueva y se cambia a ella |
| `git merge` | Une los cambios de una rama a otra |

---

## El concepto de rama (branch)

```
main:     ──●──────────────────────────●──────●
               \                      /
feature:        ●──●──●──●──●──●──●──●
                   (trabajas aquí sin tocar main)
                                       ↑
                               merge: unes todo
```

- Cada `●` es un **commit** — un punto de control con mensaje
- La rama `main` (o `master`) es el código "oficial"
- Creas una rama por cada nueva funcionalidad o corrección
- Cuando terminas, propones unir tu rama a main mediante un **Pull Request**

---

## Idea gráfica

Dibuja el árbol de ramas:

```
Historial de commits (cada ● es un commit):

main:      ●─────────────────────●─────●
(código      commit inicial      ↑     ↑
 oficial)                      merge  merge
                                ↑
feature:        ●──●──●──●──●──●
                  "trabajo de Jean, sin tocar main"
```

Luego muestra en terminal: `git log --oneline` de cualquier repo para que vean el historial real.

---

## ¿Qué sigue?

> "Git es la herramienta que vive en tu computadora. Pero ¿dónde subes ese código para compartirlo con tu equipo? ¿Dónde propones cambios, revisas el código de otros, ves el historial del proyecto? Para eso existen plataformas como GitHub. ¿Qué es GitHub y qué otras opciones existen? Siguiente clase."

**→ Siguiente clase: [Clase 17 — ¿Qué es GitHub?](clase-17-github.md)**
