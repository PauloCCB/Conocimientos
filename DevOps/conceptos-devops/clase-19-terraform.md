# Clase 19 — ¿Qué es Terraform?
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

**Terraform** es la herramienta de IaC más popular del mundo. Es **declarativa** y **multi-nube** — el mismo código puede crear infraestructura en AWS, Azure, GCP, o en servidores propios.

**¿Por qué es tan popular?**
- Un solo lenguaje (HCL — HashiCorp Configuration Language) para cualquier nube
- Enorme comunidad: miles de módulos reutilizables ya escritos por la comunidad
- El código vive en Git — historial, revisión y rollback de infraestructura
- Existe una versión open source llamada **OpenTofu**, fork comunitario de Terraform

---

## ¿Cómo funciona Terraform?

Terraform tiene un flujo de trabajo en 4 pasos:

```
1. Escribe código .tf
        ↓
2. terraform init     → descarga los plugins necesarios (proveedores: AWS, Azure...)
        ↓
3. terraform plan     → muestra QUÉ VA A CREAR, cambiar o eliminar (sin hacerlo aún)
        ↓
4. terraform apply    → ejecuta los cambios reales en la nube
```

El paso `terraform plan` es clave: puedes ver exactamente lo que va a pasar antes de que pase. Es como previsualizar antes de publicar.

**El archivo de estado (`terraform.tfstate`):**

Terraform guarda en este archivo todo lo que creó. Sabe exactamente qué recursos existen y cuáles creó él. Si corres `terraform apply` de nuevo sin cambiar nada → no hace nada, porque el estado ya coincide.

**Ejemplo de código Terraform (HCL):**

```hcl
# Crea un servidor en AWS
resource "aws_instance" "mi_servidor" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t3.micro"

  tags = {
    Name = "servidor-devops"
  }
}

# Crea un bucket de almacenamiento
resource "aws_s3_bucket" "mi_bucket" {
  bucket = "mi-proyecto-archivos"
}
```

Con `terraform apply` → AWS crea el servidor y el bucket automáticamente.

**Para eliminar todo:**
```bash
terraform destroy  # elimina TODOS los recursos que creaste con Terraform
```

---

## Idea gráfica

```
Archivo main.tf:              terraform apply:
──────────────────            ─────────────────────────────
resource "aws_instance"  →   📦 EC2 creado en AWS (Ohio)
resource "aws_s3_bucket" →   🪣 S3 Bucket creado
resource "aws_rds_instance"→ 🗄️ Base de datos creada

terraform plan (antes de apply):
+ aws_instance.mi_servidor  (se va a CREAR)
~ aws_s3_bucket.mi_bucket   (se va a MODIFICAR)
- aws_rds_instance.vieja    (se va a ELIMINAR)
¿Aplicar estos cambios? yes/no

Multi-nube: el mismo Terraform funciona en:
☁️ AWS    ☁️ Azure    ☁️ GCP    🏢 On-premise
```

---

## ¿Qué sigue?

> "Terraform es la herramienta más popular de IaC y funciona en cualquier nube. Pero Amazon tiene su propia herramienta de IaC nativa, solo para AWS, que se llama CloudFormation. ¿Cuándo usarías CloudFormation en vez de Terraform? ¿Y qué otras alternativas existen? Siguiente clase."

**→ Siguiente clase: [Clase 20 — ¿Qué es CloudFormation?](clase-20-cloudformation.md)**
