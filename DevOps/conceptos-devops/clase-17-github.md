# Clase 17 — ¿Qué es GitHub? (y alternativas)
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

**GitHub** es la plataforma más popular del mundo para guardar repositorios Git en la nube y colaborar con otros desarrolladores. Aloja la mayoría del software open source del planeta: Linux, Kubernetes, Terraform, VS Code, React — todos están en GitHub.

**La diferencia entre Git y GitHub:**
- **Git** = la herramienta que trabaja en tu computadora (el motor)
- **GitHub** = la plataforma donde subes ese repositorio para compartirlo (el carro completo con navegación, GPS, etc.)

**Conceptos clave de GitHub:**

| Concepto | ¿Qué es? |
|---------|---------|
| **Repository (repo)** | La carpeta del proyecto con todo su historial en la nube |
| **Pull Request (PR)** | Propuesta para unir cambios de una rama a otra — se revisa en equipo antes de aprobar |
| **Issues** | Tickets para reportar bugs o pedir funcionalidades |
| **GitHub Actions** | Sistema de pipelines CI/CD integrado en GitHub — gratis para repos públicos |
| **Fork** | Copia de un repo de otra persona en tu cuenta — para contribuir a proyectos open source |
| **Star** | Marcar un repo como favorito (como un "like") |

---

## Alternativas a GitHub

| Plataforma | Quién la hace | Característica principal |
|-----------|--------------|--------------------------|
| **GitHub** | Microsoft | La más popular. CI/CD con GitHub Actions |
| **GitLab** | GitLab Inc. | Todo en uno: Git + CI/CD + Docker Registry. Puede instalarse on-premise |
| **Bitbucket** | Atlassian | Integra perfectamente con Jira y Confluence |
| **Azure Repos** | Microsoft | Para empresas que usan Azure DevOps |
| **AWS CodeCommit** | Amazon | Git nativo de AWS (en proceso de descontinuación) |

**¿Cuál elegir?**
- Si trabajas solo o en proyectos pequeños → **GitHub**
- Si tu empresa ya usa Jira → **Bitbucket**
- Si necesitas todo en uno (CI/CD + Git + Docker) o quieres self-hosted → **GitLab**
- Si tu stack es 100% Azure → **Azure Repos**

---

## Idea gráfica

Muestra en vivo GitHub.com:
- Entra al repo de **Kubernetes** (github.com/kubernetes/kubernetes)
- Señala: número de stars (∼112k), commits, branches, Pull Requests, Actions
- Señala: un archivo `.yaml` de configuración para mostrar lo que ya aprendieron

Luego dibuja el flujo:

```
Tu máquina:                     GitHub:
─────────────                   ────────────────────────────
git init          →             Repositorio en la nube
git add .         →             Pull Request (revisión)
git commit        →             Code Review por el equipo
git push          →             GitHub Actions (CI/CD)
                                Merge a main ✅
```

---

## ¿Qué sigue?

> "Ahora sabemos cómo se guarda el código y dónde vive. Pero los proyectos no son solo código de la aplicación — también necesitan infraestructura: servidores, bases de datos, redes. ¿Cómo se define esa infraestructura? ¿Se hace a mano haciendo clic en AWS o se puede escribir como código? Eso se llama IaC — Infrastructure as Code. Siguiente clase."

**→ Siguiente clase: [Clase 18 — ¿Qué es IaC?](clase-18-iac.md)**
