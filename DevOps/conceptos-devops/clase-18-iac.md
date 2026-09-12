# Clase 18 — ¿Qué es IaC? (Infrastructure as Code)
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

**IaC = Infrastructure as Code** (Infraestructura como Código).

En vez de crear servidores, redes y bases de datos haciendo clic en la consola de AWS o Azure, escribes un **archivo de texto con código** que describe exactamente lo que quieres. La herramienta lee ese archivo y crea todo automáticamente.

**¿Por qué es mejor que hacer clic?**

| Sin IaC (manual) | Con IaC |
|------------------|---------|
| Alguien hace clic aquí y allá en la consola | El código describe lo que quieres |
| Nadie sabe exactamente qué se configuró | El código IS la documentación |
| Para replicar, hay que hacer todo de nuevo a mano | Ejecutas el código y obtienes el mismo resultado siempre |
| Si el admin se va, nadie sabe cómo está hecho | Cualquier persona puede leer el código |
| Errores humanos frecuentes | Reproducible, consistente, auditable |

**¿Qué se puede definir con IaC?**
- Servidores (EC2, máquinas virtuales)
- Redes y subredes (VPC)
- Bases de datos (RDS, DynamoDB)
- Balanceadores de carga
- Reglas de seguridad (Security Groups, firewall)
- Cuentas y permisos (IAM)
- Prácticamente cualquier recurso en la nube

**Tipos de IaC:**

- **Declarativo:** Describes el estado final que quieres. "Quiero 3 servidores con estas características." La herramienta calcula cómo llegar ahí. (Terraform, CloudFormation)
- **Imperativo:** Describes los pasos exactos. "Primero crea esto, luego instala aquello." (Ansible, scripts Bash)

---

## Idea gráfica

Dibuja el contraste:

```
SIN IaC:
Alguien abre la consola de AWS
→ hace clic aquí
→ configura una opción
→ hace clic allá
→ nadie documentó nada
→ si hay que rehacer: imposible replicar exacto
→ tarda horas o días

CON IaC:
main.tf (código de 20 líneas)
    ↓
terraform apply
    ↓
Se crean automáticamente:
  ✅ VPC (red virtual)
  ✅ 3 servidores EC2
  ✅ Base de datos RDS
  ✅ Balanceador de carga
  ✅ Grupos de seguridad
Guardado en Git, reproducible en 5 minutos
```

Luego muestra los beneficios adicionales:

```
main.tf vive en Git:
→ Historial de quién cambió qué servidor y cuándo
→ Pull Request para aprobar cambios de infraestructura
→ Rollback de infraestructura (como rollback de código)
→ El equipo puede revisar antes de aplicar
```

---

## ¿Qué sigue?

> "Entonces IaC nos permite definir infraestructura como código. La herramienta más popular para hacer esto se llama Terraform. ¿Cómo funciona Terraform? ¿Qué lo hace tan especial frente a otras opciones? Siguiente clase."

**→ Siguiente clase: [Clase 19 — ¿Qué es Terraform?](clase-19-terraform.md)**
