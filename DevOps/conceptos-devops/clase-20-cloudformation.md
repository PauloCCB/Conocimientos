# Clase 20 — ¿Qué es CloudFormation? (Alternativas a Terraform)
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

**AWS CloudFormation** es el servicio nativo de Amazon Web Services para hacer IaC. En vez de HCL (el lenguaje de Terraform), usa archivos **JSON o YAML** para describir la infraestructura — y Amazon los ejecuta directamente.

Es gratuito (pagas solo por los recursos que crea, no por CloudFormation en sí) y está disponible en todas las cuentas de AWS sin instalación.

**¿En qué se diferencia de Terraform?**

| | Terraform | CloudFormation |
|---|---|---|
| ¿Quién lo hace? | HashiCorp (open source) | Amazon |
| ¿En qué nubes funciona? | AWS, Azure, GCP, on-prem | Solo AWS |
| Lenguaje | HCL (propio) | JSON o YAML |
| Precio | Gratis (versión open source) | Gratis |
| Soporte de nuevos servicios AWS | A veces tarda unas semanas | Inmediato (Amazon lo lanza junto con el servicio) |
| Estado | Archivo `terraform.tfstate` | Gestionado por AWS en la nube |

**¿Cuándo usar CloudFormation?**
- Tu empresa trabaja 100% en AWS y no planea migrar a otra nube
- Quieres soporte inmediato de los servicios más nuevos de AWS
- Ya tienes workflows en AWS y quieres mantener todo dentro del ecosistema Amazon

**¿Cuándo usar Terraform?**
- Tienes o planeas tener múltiples nubes (AWS + Azure, por ejemplo)
- Quieres un ecosistema de módulos enorme de la comunidad
- Quieres portabilidad: el mismo equipo puede manejar cualquier nube con una sola herramienta

---

## Otras alternativas a Terraform

| Herramienta | Quién la hace | Característica |
|------------|--------------|----------------|
| **CloudFormation** | AWS | Solo AWS, gratis, soporte inmediato de servicios nuevos |
| **Pulumi** | Pulumi Corp | Escribes infraestructura en Python, TypeScript o Go (lenguajes reales) |
| **Bicep / ARM** | Microsoft | Solo Azure. Bicep es la versión moderna y legible de ARM |
| **AWS CDK** | AWS | Defines infraestructura AWS con Python o TypeScript |
| **OpenTofu** | Comunidad open source | Fork open source de Terraform |

---

## Idea gráfica

Dibuja el alcance de cada herramienta:

```
CloudFormation:        Terraform / OpenTofu:
┌────────────────┐     ┌──────────────────────────────────┐
│   Solo AWS     │     │  AWS │ Azure │ GCP │ On-premise  │
└────────────────┘     └──────────────────────────────────┘
  = cuchillo de chef      = navaja suiza
  específico               multi-herramienta

Pulumi = mismo poder que Terraform, pero con:
  resultado = infraestructura en nube
  código = Python / TypeScript / Go
  (para devs que prefieren lenguajes reales sobre HCL)
```

---

## ¿Qué sigue?

> "Terraform y CloudFormation crean la infraestructura — el servidor existe, la red existe. Pero una vez que el servidor existe, hay que configurarlo: instalar el software necesario, copiar los archivos de configuración, iniciar los servicios. Para eso existe otra categoría de herramientas: Configuration as Code. Y la más popular se llama Ansible. ¿Cómo funciona y en qué se diferencia de Terraform? Siguiente clase."

**→ Siguiente clase: [Clase 21 — ¿Qué es CaC y Ansible?](clase-21-cac-ansible.md)**
