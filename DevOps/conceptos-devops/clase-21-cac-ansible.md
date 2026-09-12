# Clase 21 — ¿Qué es CaC y Ansible? (vs Terraform)
> Curso Conceptos DevOps · Público: principiantes absolutos

---

## ¿Qué es?

**CaC = Configuration as Code** (Configuración como Código). Si IaC se enfoca en **crear** la infraestructura, CaC se enfoca en **configurar lo que hay dentro** de esa infraestructura.

La diferencia con una analogía:
- **IaC (Terraform):** Construye la casa — paredes, techo, fontanería, electricidad.
- **CaC (Ansible):** Amuebla y configura la casa — instala el refrigerador, configura el WiFi, organiza los cuartos.

**Ansible** es la herramienta de CaC más popular. Usa archivos YAML llamados **playbooks** para describir qué configurar en los servidores.

---

## ¿Por qué Ansible y no scripts Bash?

Podrías escribir un script Bash para instalar software en un servidor. Pero los scripts tienen problemas:
- Si el servidor ya tiene el software instalado y corres el script de nuevo, puede romper cosas
- No sabes el estado actual del servidor — el script ejecuta ciegamente los pasos

**Ansible resuelve esto con idempotencia:**

> **Idempotente** = Ejecutar la misma operación múltiples veces produce el mismo resultado. No rompe nada si ya está hecho.

Si le dices a Ansible "instala Nginx", primero verifica si Nginx ya está instalado. Si sí → no hace nada. Si no → lo instala. Puedes correr el mismo playbook 100 veces sin miedo.

---

## ¿Cómo funciona Ansible?

- **Sin agentes (agentless):** No necesitas instalar nada en los servidores que configuras. Ansible se conecta por SSH.
- **Inventario:** Una lista de los servidores que quieres configurar
- **Playbook:** El archivo YAML con las tareas a ejecutar en esos servidores
- **Módulos:** Las acciones disponibles (instalar paquetes, copiar archivos, iniciar servicios)

**Ejemplo de playbook Ansible:**

```yaml
- name: Configurar servidor web
  hosts: mis_servidores
  become: true  # ejecutar como root

  tasks:
    - name: Instalar Nginx
      apt:
        name: nginx
        state: present   # "que esté instalado"

    - name: Copiar configuración
      copy:
        src: nginx.conf
        dest: /etc/nginx/nginx.conf

    - name: Iniciar y habilitar Nginx
      service:
        name: nginx
        state: started
        enabled: true
```

---

## Terraform vs Ansible — ¿son competidores?

**No. Son complementarios.** La confusión es común pero están diseñados para etapas distintas:

| | Terraform | Ansible |
|---|---|---|
| **¿Qué hace?** | Crea y destruye infraestructura | Configura lo que hay dentro |
| **Pregunta que responde** | "¿Qué recursos debo tener?" | "¿Cómo debe estar configurado cada servidor?" |
| **Lenguaje** | HCL | YAML |
| **Requiere agente?** | No | No (usa SSH) |
| **Estado** | Sí (terraform.tfstate) | No — es stateless |

El flujo típico en producción:

```
Terraform → crea el servidor EC2 en AWS
    ↓
Ansible → instala Node.js, copia el código, inicia la app
    ↓
Servidor listo para recibir usuarios ✅
```

---

## Idea gráfica

```
FLUJO COMPLETO EN PRODUCCIÓN:

Paso 1: Terraform
  → VPC creada
  → EC2 creado (servidor vacío)
  → RDS creado (base de datos)
  → Security Groups configurados

Paso 2: Ansible
  → Instala: Node.js, PM2, Nginx
  → Copia: archivos de la app
  → Configura: variables de entorno
  → Inicia: servicios

Resultado: servidor productivo, listo para usuarios ✅
```

---

## ¿Qué sigue?

> "Hasta ahora hemos hablado de infraestructura y configuración. Ahora viene el momento de hablar de cómo se empaqueta y distribuye el software en sí mismo. Los servidores están listos — ¿cómo le llega la aplicación? ¿Cómo nos aseguramos de que funcione igual en el servidor que en la computadora del desarrollador? Para eso existen los contenedores. ¿Qué es Docker? Siguiente clase."

**→ Siguiente clase: [Clase 22 — ¿Qué es Docker?](clase-22-docker.md)**
