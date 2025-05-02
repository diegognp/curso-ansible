	 	 	 	  
Manual del Estudiante \- Curso Taller: Automatización de Despliegues con Ansible

 Duración total: 6 horas

**Módulo 1: Introducción a la Automatización y a Ansible (30 min)**

### **📘 1\. ¿Qué es la automatización de infraestructura?**

La **automatización de infraestructura** es el proceso de gestionar y aprovisionar recursos tecnológicos (servidores, redes, aplicaciones, almacenamiento) mediante código, en lugar de hacerlo manualmente.

**Beneficios principales:**

* Reducción de errores humanos  
* Despliegues más rápidos y consistentes  
* Facilidad para escalar sistemas  
* Mejora en la trazabilidad de cambios

Es el corazón de prácticas modernas como **DevOps**, **SRE** y la **Infraestructura como Código (IaC)**.

### **📘 2\. ¿Por qué usar Ansible?**

**Ansible** es una herramienta de automatización que permite configurar sistemas, desplegar software y orquestar tareas complejas sin necesidad de instalar agentes.

**Ventajas de Ansible:**

* **Simplicidad:** usa YAML, un lenguaje fácil de leer.  
* **Sin agentes:** no necesita instalar nada en los nodos remotos, solo conexión por SSH.  
* **Idempotencia:** garantiza que las tareas no se repitan si ya están aplicadas.  
* **Extensible:** se adapta bien a muchos entornos.  
* **Gran comunidad:** mucha documentación y soporte.

### **📘 3\. Diferencias entre Ansible y otras herramientas**

| Característica | Ansible | Puppet | Chef | Terraform |
| ----- | ----- | ----- | ----- | ----- |
| Lenguaje | YAML | DSL propio | Ruby DSL | HCL (propio) |
| Arquitectura | Sin agentes | Con agentes | Con agentes | Sin agentes |
| Uso principal | Configuración | Configuración | Configuración | Infraestructura |
| Modelo de ejecución | Push | Pull | Pull | Declarativo |
| Curva de aprendizaje | Baja | Media | Alta | Media |

Ansible es ideal para automatización ligera, rápida y sin complicaciones.

### **📘 4\. Arquitectura de Ansible**

Ansible tiene una arquitectura simple pero poderosa:

* **Controlador (Control Node):** es el nodo desde el cual se ejecutan los playbooks. Debe tener Ansible instalado.  
* **Nodos gestionados (Managed Nodes):** son los servidores donde se aplican las configuraciones. Solo requieren acceso por SSH.  
* **SSH:** es el canal de comunicación predeterminado.  
* **Inventario:** archivo donde se definen los nodos.  
* **Playbooks:** archivos YAML que definen las tareas a ejecutar.

  **![][image1]**

### **📘 5\. Introducción breve a Red Hat Ansible Automation Platform**

La **Red Hat Ansible Automation Platform** es una solución empresarial basada en Ansible. Proporciona:

* Gestión centralizada de inventarios y credenciales  
* Interfaces gráficas (AWX / Ansible Tower)  
* Control de accesos y auditorías  
* Integraciones con CI/CD  
* Paneles de monitoreo y reportes

Aunque el curso se centra en **Ansible Open Source**, es útil conocer la plataforma empresarial.

**Ejemplo simple Idempotencia:**  
Se tiene una tarea:

\- name: Instalar nginx  
  apt:  
	name: nginx  
	state: present

* **Primera vez que se ejecuta:** instala nginx si no está instalado.  
* **Segunda, tercera o décima vez:** Ansible detecta que nginx ya está instalado y **no hace nada más**.

### **¿Por qué es importante?**

* **Seguridad:** evita duplicar tareas o sobrescribir cambios.  
* **Confiabilidad:** puedes ejecutar tus playbooks sin miedo a romper nada.  
* **Auditoría y consistencia:** el sistema siempre termina en el estado deseado.

En resumen: **idempotencia \= ejecutar muchas veces sin cambiar el resultado final si ya está correcto**.

### **Ejercicios prácticos**

#### **📝 Preguntas de reflexión**

1. ¿Qué ventajas ofrece la automatización de infraestructura frente a la administración manual?  
2. ¿Qué significa que Ansible sea **idempotente**?

#### **🧪 Manos a la obra (teórico-práctico)**

1. Investiga y anota 2 casos reales donde Ansible podría facilitar tareas repetitivas en una empresa.

## **🧩 Módulo 2: Instalación y configuración básica (30 min)**

### **🎯 Objetivo del módulo**

Al finalizar este módulo, el estudiante podrá instalar Ansible en un sistema Debian/Ubuntu, entender los requisitos previos, establecer una conexión SSH segura sin contraseña a otros servidores, y personalizar parámetros básicos mediante el archivo `ansible.cfg`.

### **🔍 Requisitos previos**

Antes de comenzar con la instalación de Ansible, es importante contar con:

* Una máquina con Linux (preferiblemente Debian o Ubuntu).  
* Acceso a internet para descargar paquetes.  
* Un usuario con privilegios sudo.  
* Conexión SSH habilitada entre el controlador (máquina que ejecuta Ansible) y los nodos a gestionar.  
* Python 3 (usualmente ya instalado).

1. ### **Instalación en Debian/Ubuntu**

1. **Actualizar el sistema:**

sudo apt update && sudo apt upgrade \-y

2. **Instalar Ansible:**

sudo apt install ansible \-y

3. **Verificar instalación:**

ansible \--version

### **📁2. Estructura de directorios**

Aunque Ansible no impone una estructura fija, se recomienda organizar los archivos de la siguiente forma:

mi-proyecto-ansible/  
├── inventory/  
│   └── hosts  
├── playbooks/  
│   ├── site.yml  
│   └── webserver.yml  
├── roles/  
│   └── nginx/  
├── ansible.cfg

* `inventory/`: archivos que indican qué hosts vamos a gestionar.  
* `playbooks/`: archivos YAML con instrucciones.  
* `roles/`: buenas prácticas para organizar tareas.  
* `ansible.cfg`: configuración de Ansible.

Aunque este tema se ampliará en módulos siguientes, es útil crear una carpeta para comenzar a trabajar organizadamente:

mkdir \-p \~/mi-proyecto-ansible/  
cd \~/mi-proyecto-ansible/

## **🔐 Configuración de conexión SSH sin contraseña**

La comunicación entre el controlador (tu máquina) y los nodos gestionados se hace mediante SSH.

Para que Ansible se conecte a los nodos sin pedir contraseña, usamos autenticación SSH con clave pública:

1. **Generar una clave pública (si no tienes):**

ssh-keygen \-t rsa \-b 4096

Presiona Enter en todas las preguntas para aceptar las opciones por defecto.

2. **Copiar la clave pública al nodo remoto:**

ssh-copy-id \-i \~/.ssh/mi\_clave.pub usuario@ip\_del\_nodo

3. **Probar la conexión sin contraseña:**

ssh usuario@ip\_del\_nodo

No debería pedirse contraseña.  
	📝 Asegúrate de que el usuario tenga permisos sudo en el nodo.

## **⚙️ Archivo `ansible.cfg` y configuración básica**

Este archivo personaliza cómo se comporta Ansible. Puedes crearlo en el directorio del proyecto. En este punto solo definiremos algunas opciones básicas:

Crea un archivo con el siguiente contenido:

`[defaults]`  
`#inventory = ./inventory/hosts`  
`remote_user = usuario`  
`private_key_file = /ruta/llave/privada`  
`host_key_checking = False`  
`retry_files_enabled = False`

Guarda este archivo como:  
nano ansible.cfg

Guarda con Ctrl+O, Enter, y sal con Ctrl+X.

### **Parámetros comunes:**

* inventory: ruta al archivo de inventario.  
* remote\_user: usuario que se conecta vía SSH.  
* host\_key\_checking: evita errores por claves SSH nuevas.  
* retry\_files\_enabled: desactiva creación de archivos .retry.

## **🧪 Ejercicio práctico del módulo**

1. **Instala Ansible en tu máquina.**  
2. **Verifica su versión con `ansible --version`.**  
3. **Genera una clave SSH (si no tienes).**  
4. **Copia tu clave pública a un nodo remoto de pruebas.**  
5. **Verifica que puedes conectarte sin contraseña.**  
6. **Crea un archivo `ansible.cfg` con las opciones básicas mencionadas.**  
7. **Guarda todo dentro de una carpeta `ansible-taller`.**  
   

# **📘 Manual del Estudiante**

## **Módulo 3: Inventarios e Introducción a Comandos Ad-Hoc (30 min)**

---

### **🎯 Objetivo del Módulo**

Comprender qué es un inventario en Ansible, cómo se estructura, y cómo ejecutar tareas simples en múltiples servidores usando comandos ad-hoc.

---

## **📁 ¿Qué es un inventario?**

En Ansible, un **inventario** es el archivo donde se listan los servidores (nodos) que van a ser administrados. Ansible necesita saber qué máquinas manejar y cómo conectarse a ellas.

Existen dos formatos comunes:

* Formato INI (más simple y tradicional)  
* Formato YAML (más estructurado, usado en entornos complejos)

### **📝 Ejemplo de inventario en formato INI**

Archivo: inventario.ini

`[nodos]`  
`192.168.56.101 ansible_become_pass=contraseña`  
`192.168.56.102 ansible_become_pass=contraseña`

`[web]`  
`192.168.56.101 ansible_become_pass=contraseña`

`[db]`  
`192.168.56.102 ansible_become_pass=contraseña`

si el nodo remoto pide contraseña para sudo usar ansible\_become\_pass

Puedes agrupar nodos por función (por ejemplo, web o db) y luego usarlos por separado en los comandos.

### **🧾 Ejemplo de inventario en formato YAML**

Archivo: `inventario.yaml`

all:  
  children:  
    web:  
      hosts:  
        192.168.56.101:  
    db:  
      hosts:  
        192.168.56.102:

Este formato es útil en proyectos más grandes y para inventarios generados dinámicamente.

## **⚙️ Verificación del inventario**

Para listar los hosts del inventario, desde el directorio de trabajo:

ansible all \-i inventario.ini \--list-hosts

## **⚡ Comandos Ad-Hoc**

Los **comandos ad-hoc** permiten ejecutar tareas rápidas sobre uno o varios servidores sin necesidad de escribir un playbook.

### **🔸 Verificar conectividad (módulo `ping`)**

ansible all \-i inventario.ini \-m ping

Este comando prueba si Ansible puede conectarse por SSH y ejecutar Python en los nodos.

**🔸 Ver carga del sistema (`uptime`)**  
ansible all \-i inventario.ini \-a "uptime"

**🔸 Copiar un archivo a los nodos**  
ansible all \-i inventario.ini \-m copy \-a "src=archivo.txt dest=/tmp/archivo.txt"

**🔸 Ejecutar comandos de shell**

ansible all \-i inventario.ini \-m shell \-a "df \-h"

Usa `shell` si necesitas redirecciones, pipes o variables de entorno.

**Diferencia entre `command` y `shell`**:

* `command`: no ejecuta redirecciones, pipes, ni variables.  
* `shell`: sí los interpreta como un shell normal.

**🔸 Instalar un paquete con `apt`**  
`ansible all -i inventario.ini -m apt -a "name=curl state=present" --become`  
Este comando instala curl en los nodos. Se necesita privilegios de administrador (--become).

**🧪 Laboratorio : Configuración Básica**

**Instrucciones**:

1. Crea un archivo `inventario.ini` con 2 nodos (por ejemplo, 192.168.56.101 y 192.168.56.102).  
2. Verificar conectividad con `ansible all -m ping`.  
3. Ejecutar `uptime`, `hostname`, y `df -h` usando ad-hoc.  
4. Copiar un archivo de texto desde el controlador a los nodos.  
5. Instalar `curl` en los nodos usando `apt`.

✅ **Comprobación del aprendizaje**

Al finalizar este módulo deberías ser capaz de:

* Crear y entender un archivo de inventario.  
* Ejecutar tareas simples con Ansible sin escribir un playbook.  
* Verificar conectividad, copiar archivos y ejecutar comandos en múltiples nodos.

## **Módulo 4: Playbooks básicos (1 hora)**

---

### **🎯 Objetivo del Módulo**

Aprender qué es un playbook en Ansible, su estructura, sintaxis y cómo usar módulos comunes para automatizar tareas. Al final del módulo, podrás escribir y ejecutar tu primer playbook.

## **🧾 ¿Qué es un Playbook?**

Un **playbook** es un archivo escrito en formato YAML que describe una o varias tareas a ejecutar en uno o varios nodos.

Es la forma recomendada de automatizar tareas repetibles y versionarlas como código.

### **🧩 Sintaxis YAML**

* YAML significa **YAML Ain’t Markup Language**  
* Es **indentado por espacios**, no tabulaciones.  
* Cada nivel de indentación representa una jerarquía.  
* Las listas comienzan con `-`

Ejemplo básico:

\---  
\- name: nombre del playbook  
  hosts: all  
  become: true  
  tasks:  
    \- name: Mostrar un mensaje  
      debug:  
        msg: "Hola desde Ansible"  
corre con:  
ansible-playbook \-i inventario.ini playbook.yaml

### **🧱 Estructura básica de un Playbook**

\- name: Descripción del playbook  
  hosts: grupo\_o\_host  
  become: true     	\# Para ejecutar con privilegios  
  tasks:  
    \- name: Nombre de la tarea  
      módulo: parámetros

## **🔧 Módulos comunes**

### **📂 Módulo `copy`**

Copia archivos desde el equipo controlador a los nodos.

  \- name: Copiar archivo  
    copy:  
      src: archivo.txt  
      dest: /tmp/archivo.txt

### **📄 Módulo `file`**

Crea directorios, cambia permisos o elimina archivos.

\- name: Crear directorio  
  file:  
    path: /opt/miapp  
    state: directory  
    mode: '0755'

### **📦 Módulo `apt`**

Instala paquetes en sistemas basados en Debian.

\- name: Instalar nginx  
  apt:  
    name: nginx  
    state: present  
    update\_cache: yes

Requiere `become: true`

### **🔧 Módulo `service`**

Gestiona servicios: iniciar, detener, habilitar.

\- name: Iniciar nginx  
  service:  
    name: nginx  
    state: started  
    enabled: yes

## **📂 Organización recomendada de archivos**

.  
├── inventario.ini  
├── playbook-nginx.yaml  
└── archivos/  
	└── index.html

## **🧪 Laboratorio : Primer Playbook**

### **🎯 Objetivo**

Automatizar la instalación de un servidor web Nginx, subir una página de bienvenida y asegurar que el servicio esté activo.

### **🧱 Pasos**

1. Crea un archivo `inventario.ini` si aún no lo tienes.

\[web\]  
192.168.56.101  
192.168.56.102

2. Crea el archivo `index.html` con contenido personalizado:

\<h1\>¡Bienvenido a Nginx desde Ansible\!\</h1\>  
\<h1\>NOMBRE\_APELLIDO\</h1\>

3. Crea el playbook `playbook-nginx.yaml`:

\- name: Instalar y configurar Nginx  
  hosts: web  
  become: true  
  tasks:  
    \- name: Instalar nginx  
      apt:  
        name: nginx  
        state: present  
        update\_cache: yes

    \- name: Copiar index.html  
      copy:  
        src: archivos/index.html  
        dest: /var/www/html/index.html

    \- name: Iniciar servicio nginx  
      service:  
        name: nginx  
        state: started  
        enabled: yes

4. Ejecuta el playbook:

ansible-playbook \-i inventario.ini playbook-nginx.yaml

5. Verifica en el navegador: `http://IP_DEL_NODO`  
   o con un curl: `curl http://IP_DEL_NODO`

### **🔍 Posibles errores y soluciones**

| Problema | Causa probable | Solución |
| ----- | ----- | ----- |
| Permission denied | Falta configurar claves SSH | Verifica autenticación sin contraseña |
| Failed to update apt cache | Faltó become: true | Asegúrate que el play tenga privilegios |
| File not found | Ruta src incorrecta | Revisa rutas relativas |

## **✅ Verificación del aprendizaje**

Al finalizar este módulo deberías poder:

* Comprender la sintaxis y estructura de un playbook.  
* Escribir un playbook con múltiples tareas.  
* Usar módulos comunes para automatizar la configuración de servidores.  
* Entender la estructura de un archivo YAML.  
* Desplegar un servicio real (como Nginx) usando automatización.

## **Módulo 5: Variables y facts (30 minutos)**

---

### **🎯 Objetivo del módulo**

En este módulo aprenderás a usar variables en Ansible para hacer tus playbooks más reutilizables y dinámicos. También aprenderás a obtener y utilizar información del sistema (facts) recolectada automáticamente por Ansible.

---

## **🧩 Uso de variables en Ansible**

Ansible permite declarar y usar variables en distintos contextos. Esto permite que nuestros playbooks sean más flexibles y adaptables a distintos entornos.

### **📌 Formas comunes de declarar variables:**

1. **En el playbook mismo:**

\- hosts: web  
 vars:  
   puerto\_web: 80  
 tasks:  
   \- name: Mostrar el puerto configurado  
     debug:  
       msg: "El puerto web es {{ puerto\_web }}"

2. **En archivos `host_vars` o `group_vars`:**

* Crea una carpeta llamada `host_vars` y un archivo con el nombre del host:

host\_vars/servidor1.yaml  
Ingresa el texto:  
puerto\_web: 8080

3. **Desde la línea de comandos:**

ansible-playbook playbook.yaml \-e "puerto\_web=8080"

### **🗃️ Estructura recomendada para variables**

**.**  
**├── group\_vars/**  
**│   └── webservers.yaml**  
**├── host\_vars/**  
**│   └── servidor1.yaml**  
**└── playbook.yaml**

Esto ayuda a organizar y separar variables según grupo o máquina específica.

## **⚡ Precedencia de Variables (de mayor a menor prioridad)**

1. \-e (extra vars en CLI) → ansible-playbook \-e "http\_port=9090"  
2. host\_vars (variables por host).  
3. vars en playbooks.  
4. group\_vars (variables por grupo).  
5. defaults en roles.

## **🛠️ Ansible facts: información del sistema**

Ansible recolecta automáticamente información del sistema en cada host antes de ejecutar tareas. Esto incluye:

* Nombre del sistema operativo  
* Dirección IP  
* Núcleo (kernel)  
* Número de CPU  
* Tamaño de la memoria RAM

### **Ejemplo:**

\- name: Mostrar sistema operativo  
 debug:  
   msg: "Sistema operativo: {{ ansible\_facts\['os\_family'\] }}"  
O también:

\- name: Mostrar IP  
  debug:  
    var: ansible\_default\_ipv4.address

Puedes ver todos los facts disponibles con:

ansible all \-m setup \-i inventario.ini

## **⚠️ Buenas prácticas con variables**

✅ Usa nombres descriptivos: `puerto_aplicacion`, `ruta_archivos`, etc.

✅ Declara variables en archivos cuando sean muchas o compartidas.

✅ Evita sobrescribir variables innecesariamente con `-e` (extra vars) si no es necesario.

---

## **🧪 Laboratorio 3: Variables y Facts**

### **🎯 Objetivo**

Crear un playbook que use variables para configurar el puerto de Nginx y mostrar datos del sistema.

## **📂 Estructura del Proyecto**

### .

### ├── \*\*group\_vars/\*\*

### │   └── \*\*web.yml\*\*          \# Variables personalizadas

### ├── \*\*files/\*\*

### │   └── \*\*index.html\*\*       \# Página web estática

### ├── \*\*inventario.ini\*\*       \# Archivo de inventario

### └── \*\*deploy-web.yaml\*\*      \# Playbook principal

### 

### **🧾 Pasos sugeridos:**

1. **Crear un archivo de variables en `group_vars/web.yaml`:**

\---  
\# Variables para Nginx  
nginx\_port: 8080  
web\_root: /var/www/mi\_app

2. **Archivo HTML de Ejemplo (files/index.html)**

\<\!DOCTYPE html\>  
\<html\>  
\<head\>  
    \<title\>Mi App Estática\</title\>  
\</head\>  
\<body\>  
    \<h1\>¡Bienvenido a mi aplicación\!\</h1\>  
    \<p\>Servidor: {{ ansible\_hostname }}\</p\>  
    \<p\>IP: {{ ansible\_default\_ipv4.address }}\</p\>  
\</body\>  
\</html\>

3. **Crear un playbook `configurar-nginx.yaml` que:**  
* Instale `nginx`.  
* Cree un directorio para la aplicacion web.  
* Copie un archivo HTML estatico.  
* Configure Nginx.  
* Configure el root de la web.  
* Reinicie el servicio.  
* Muestre por pantalla el nombre del sistema y su dirección IP usando facts.

\---  
\- name: Desplegar aplicación web estática con Nginx  
  hosts: web\_servers  
  become: yes  
  gather\_facts: yes   \# Para usar facts en el HTML (opcional)

  tasks:  
    \# 1\. Instalar Nginx  
    \- name: Instalar Nginx  
      apt:  
        name: nginx  
        state: present  
      when: ansible\_os\_family \== 'Debian'

    \# 2\. Crear directorio para la aplicación web  
    \- name: Crear directorio web  
      file:  
        path: "{{ web\_root }}"  
        state: directory  
        mode: '0755'

    \# 3\. Copiar el archivo HTML estático  
    \- name: Copiar index.html  
      copy:  
        src: files/index.html  
        dest: "{{ web\_root }}/index.html"  
        mode: '0644'

    \# 4\. Configurar Nginx (sin Jinja2, usando lineinfile)  
    \- name: Configurar Nginx para usar el puerto personalizado  
      lineinfile:  
        path: /etc/nginx/sites-available/default  
        regexp: '^listen '  
        line: "listen {{ nginx\_port }};"  
        backrefs: yes

    \# 5\. Configurar el root de la web  
    \- name: Definir el root de Nginx  
      lineinfile:  
        path: /etc/nginx/sites-available/default  
        regexp: '^root /var/www/html;'  
        line: "root {{ web\_root }};"  
        backrefs: yes

    \# 6\. Reiniciar Nginx  
    \- name: Reiniciar Nginx  
      service:  
        name: nginx  
        state: restarted

    \# 7\. Mostrar información del despliegue  
    \- name: Mostrar detalles del servidor  
      debug:  
        msg: |  
          ✅ Aplicación desplegada en: http://{{ ansible\_host }}:{{ nginx\_port }}  
          📂 Directorio web: {{ web\_root }}  
          🖥️  Hostname: {{ ansible\_hostname }}  
          🌐 IP: {{ ansible\_default\_ipv4.address }}

3. **Archivo de Inventario (inventario.ini):**

\[web\_servers\]  
server1 ansible\_host=192.168.1.100  
server2 ansible\_host=192.168.1.101

4. **Ejecutar el playbook:**

ansible-playbook \-i inventario.ini configurar-nginx.yaml

5. **Verificar que Nginx esté corriendo en el puerto indicado:**

curl [http://IP\_DEL\_SERVIDOR:8080](http://IP_DEL_SERVIDOR:8080)

O abre en el navegador:  
http://\<IP\_DEL\_SERVIDOR\>:8080

### **✅ Al finalizar el módulo, deberías ser capaz de:**

* Usar variables en diferentes niveles (playbook, host, grupo, CLI).  
* Consultar y usar facts del sistema recolectados por Ansible.  
* Hacer playbooks más reutilizables y adaptables con variables.

## **Laboratorio Extra Personalizar el MOTD (Mensaje del Día) con Ansible**

🎯 Objetivo:  
Modificar el MOTD (/etc/motd) en los servidores remotos usando facts de Ansible (hostname, IP, SO) para mostrar información personalizada al iniciar sesión.  
---

## **📂 Estructura del Proyecto**

`.`

├── group\_vars/  
│   └── all.yml          \# Variables globales  
├── tasks/  
│   └── motd.yml         \# Tarea personalizada  
└── playbook-motd.yaml   \# Playbook principal

### **1\. Variables (group\_vars/all.yml)**

\---  
motd\_template: |  
  ╔══════════════════════════════════╗  
  ║        ¡BIENVENIDO\!             ║  
  ╠══════════════════════════════════╣  
  ║ Host: {{ ansible\_hostname }}     ║  
  ║ IP: {{ ansible\_default\_ipv4.address }} ║  
  ║ SO: {{ ansible\_distribution }} {{ ansible\_distribution\_version }} ║  
  ║ Uso de CPU: {{ ansible\_processor\_vcpus }} núcleos ║  
  ╚══════════════════════════════════╝

### **2\. Playbook Principal (playbook-motd.yaml)**

\---  
\- name: Personalizar MOTD con Facts  
  hosts: all  
  become: yes  
  gather\_facts: yes   \# Necesario para obtener datos del sistema

  tasks:  
    \- name: Crear MOTD personalizado  
      copy:  
        content: "{{ motd\_template }}"  
        dest: /etc/motd  
        owner: root  
        group: root  
        mode: 0644

    \- name: Mostrar preview del MOTD  
      debug:  
        msg: "MOTD configurado:\\n{{ motd\_template }}"

### **3\. Ejecución**

ansible-playbook \-i inventario.ini playbook-motd.yaml

### **4\. Verificación**

Inicia sesión vía SSH en cualquier servidor gestionado:

ssh usuario@servidor

Verás algo como:  
╔══════════════════════════════════╗  
║        ¡BIENVENIDO\!             ║  
╠══════════════════════════════════╣  
║ Host: servidor1                 ║  
║ IP: 192.168.1.100               ║  
║ SO: Ubuntu 22.04                ║  
║ Uso de CPU: 2 núcleos           ║  
╚══════════════════════════════════╝

## **Módulo 6: Plantillas Jinja2 (30 minutos)**

---

### **🎯 Objetivo del módulo**

Aprender a utilizar plantillas Jinja2 para generar archivos de configuración dinámicos, aprovechando variables y facts de Ansible.

---

### **6.1. ¿Qué es Jinja2?**

* **Jinja2** es un motor de plantillas para Python que Ansible usa para renderizar archivos dinámicamente.  
* Permite insertar variables, realizar condicionales y bucles dentro de archivos de configuración.  
* Muy útil para adaptar configuraciones según el entorno (por ejemplo, distintos archivos para diferentes servidores).

---

### **6.2. Estructura básica de una plantilla**

Un archivo Jinja2 es un archivo de texto donde se pueden insertar:

| Elemento | Sintaxis |
| ----- | ----- |
| Variables | {{ variable }} |
| Condicionales | {% if condition %} ... {% endif %} |
| Bucles | {% for item in list %} ... {% endfor %} |

### **📄 Ejemplo simple**

Archivo: `index.html.j2`

\<html\>  
  \<head\>\<title\>Bienvenido a {{ ansible\_hostname }}\</title\>\</head\>  
  \<body\>  
    \<h1\>Hola desde {{ ansible\_hostname }}\</h1\>  
  \</body\>  
\</html\>

### **6.3. Usando la plantilla en un playbook**

\- name: Crear archivo index.html dinámico  
  hosts: web  
  tasks:  
    \- name: Copiar plantilla personalizada  
      template:  
        src: templates/index.html.j2  
        dest: /var/www/html/index.html

### **6.4. Variables comunes que puedes usar**

* `ansible_hostname`: Nombre del host  
* `ansible_distribution`: Sistema operativo  
* `ansible_facts`: Todos los datos recopilados

---

### **🛠️ Laboratorio 4: Creando un archivo dinámico**

**Objetivo:**  
 Generar un archivo `index.html` personalizado para cada nodo web.

**Pasos:**

1️ Crea la carpeta `templates` dentro de tu proyecto.  
 2️ Crea el archivo `index.html.j2` con contenido dinámico.  
 3️ Escribe un playbook que copie esa plantilla al directorio `/var/www/html/`.  
 4️ Ejecuta el playbook y verifica el resultado accediendo al servidor o revisando el archivo generado.

---

## **✅ Buenas prácticas**

* Siempre revisa la sintaxis Jinja2 (evita errores comunes como olvidarte las llaves o usar mal las comillas).  
* Mantén tus plantillas en una carpeta llamada `templates` para claridad.  
* Comenta tu plantilla si es compleja (para que otros entiendan la lógica).

## **Módulo 7: Handlers, loops y condiciones (30 minutos)**

---

### **🎯 Objetivo del módulo**

Aprender a utilizar **handlers**, **loops** y **condiciones** en Ansible para crear playbooks más dinámicos y eficientes.

---

### **7.1. Handlers**

**¿Qué son?**

* Son tareas especiales que se ejecutan solo cuando son notificadas por otras tareas.  
* Útiles para reiniciar servicios después de cambios en la configuración.

**Ejemplo:**

\- name: Copiar archivo de configuración  
  copy:  
    src: nginx.conf  
    dest: /etc/nginx/nginx.conf  
  notify: Reiniciar Nginx

handlers:  
  \- name: Reiniciar Nginx  
    service:  
      name: nginx  
      state: restarted

💡 **Nota:** Si la tarea no cambia nada, el handler no se ejecuta.

---

### **7.2. Loops**

**¿Qué son?**

* Permiten repetir una misma tarea varias veces con diferentes valores.

**Ejemplo básico:**

\- name: Crear varios archivos  
  copy:  
    src: default.txt  
    dest: "/tmp/{{ item }}"  
  loop:  
    \- archivo1.txt  
    \- archivo2.txt  
    \- archivo3.txt

🔄 Se crearán tres archivos en `/tmp`.

---

### **7.3. Condiciones**

**¿Qué son?**

* Permiten ejecutar tareas solo si se cumple una condición.

**Ejemplo:**

\- name: Instalar Apache solo en Debian  
  apt:  
    name: apache2  
    state: present  
  when: ansible\_distribution \== "Debian"

🛑 Esta tarea se salta si el sistema operativo no es Debian.

---

## **🛠️ Laboratorio 5: Handlers, loops y condiciones**

✅ **Objetivo:**  
 Crear un playbook que:

1️ Instale un paquete web (Nginx o Apache).  
 2️ Copie varios archivos de configuración usando loops.  
 3️ Reinicie el servicio usando un handler.  
 4️ Solo ejecute algunas tareas si el sistema es Debian.

**Pasos:**

* Crear un playbook `web_setup.yml`.  
* Usar un loop para copiar 3 archivos de configuración ficticios a `/etc/nginx/conf.d/`.  
* Notificar al handler para reiniciar Nginx si cambia algún archivo.  
* Añadir una condición para instalar solo si el sistema es Debian.

### **📝 Resumen de lo aprendido**

| Concepto | Descripción |
| ----- | ----- |
| Handlers | Ejecutan tareas solo cuando se notifican. |
| Loops | Ejecutan una tarea varias veces con diferentes valores. |
| Condiciones | Ejecutan tareas solo si se cumple una condición. |

## **Módulo 8: Roles y estructura reutilizable (45 minutos)**

---

### **🎯 Objetivo del módulo**

Aprender qué son los **roles** en Ansible y cómo usarlos para estructurar playbooks de manera modular y reutilizable.

---

### **8.1. ¿Qué es un rol?**

* Un **rol** es una forma estructurada de **organizar código** en Ansible.  
* Facilita la reutilización y el mantenimiento dividiendo tareas, handlers, archivos, plantillas y variables en carpetas bien definidas.

---

### **8.2. Estructura de carpetas de un rol**

Cuando creamos un rol con `ansible-galaxy init nombre_del_rol`, se genera una estructura como esta:

nombre\_del\_rol/  
├── defaults/  
│   └── main.yml  
├── files/  
├── handlers/  
│   └── main.yml  
├── meta/  
│   └── main.yml  
├── tasks/  
│   └── main.yml  
├── templates/  
├── tests/  
│   ├── inventory  
│   └── test.yml  
└── vars/  
    └── main.yml

📁 **Explicación rápida:**

* `tasks/`: tareas principales del rol.  
* `handlers/`: handlers específicos del rol.  
* `templates/`: plantillas Jinja2.  
* `files/`: archivos estáticos.  
* `vars/` y `defaults/`: variables.  
* `meta/`: dependencias y metadatos.  
* `tests/`: pruebas rápidas.

---

### **8.3. Crear y usar roles propios**

Pasos básicos para usar un rol:

1️⃣ Crear el rol:

ansible-galaxy init nombre\_del\_rol

2️⃣ Definir las tareas principales en `tasks/main.yml`.

3️⃣ Incluir el rol en un playbook:

\- hosts: all  
  roles:  
    \- nombre\_del\_rol

#### 4️⃣ Uso opcional de Galaxy

**Ansible Galaxy** es un repositorio público donde puedes encontrar y descargar roles listos para usar.  
 Para instalar un rol de Galaxy:

ansible-galaxy install nombre\_del\_rol

## **🛠️ Laboratorio 6: Crear y usar un rol**

**Objetivo:** Automatizar la instalación y configuración de Nginx usando un rol propio.

🔧 **Pasos:**

1️⃣ Crear un rol llamado `nginx_role`.

ansible-galaxy init nginx\_role  
 2️⃣ En `tasks/main.yml` del rol, incluir las tareas para:

* Instalar Nginx.  
* Copiar un archivo `index.html` sencillo (usa la carpeta `files`).  
* Asegurar que el servicio esté iniciado.

\- name: Instalar Nginx  
  apt:  
    name: nginx  
    state: present  
    update\_cache: yes

\- name: Copiar página de inicio  
  copy:  
    src: index.html  
    dest: /var/www/html/index.html

\- name: Asegurar que Nginx esté en ejecución  
  service:  
    name: nginx  
    state: started  
    enabled: yes

 3️⃣ Crear un playbook que incluya este rol.

\- hosts: all  
  become: yes  
  roles:  
    \- nginx\_role

 4️⃣ Ejecutar el playbook y verificar el resultado accediendo a la web desde un navegador o con `curl`.

Usar `curl http://IP_DEL_NODO` o abrir en el navegador.

---

## **✅ Conceptos clave a recordar**

| Concepto | Descripción |
| ----- | ----- |
| Rol | Estructura modular para organizar código Ansible. |
| Galaxy | Repositorio comunitario para compartir y usar roles. |
| Ventajas | Facilitan la reutilización, el orden y la colaboración en proyectos grandes. |

## **Módulo 9: Proyecto práctico final (1 hora)**

---

### **🎯 Objetivos del módulo**

* Aplicar todo lo aprendido en un proyecto completo.

* Configurar y desplegar un servicio web en múltiples nodos.

* Usar plantillas, variables, handlers y roles para lograr un despliegue robusto.

---

### **🛠️ Desafío del proyecto**

El reto es desplegar un servicio web **completo** en 2 o más nodos remotos, siguiendo estas especificaciones:

1️ **Configuración de Nginx:**

* Instalar y configurar Nginx en todos los nodos.  
* Asegurar que el servicio esté en ejecución y habilitado al arranque.

2️ **Página personalizada:**

* Usar una **plantilla Jinja2** para generar un archivo `index.html` dinámico que incluya:

  * Nombre del host.  
  * Fecha y hora de despliegue.  
  * Mensaje de bienvenida personalizado.

3 **Roles y estructura:**

* Organizar la automatización usando **roles** para mantener la estructura limpia y reutilizable.

4 **Variables y handlers:**

* Implementar variables para valores dinámicos.  
* Usar **handlers** para reiniciar Nginx si hay cambios en la configuración.

---

### **📝 Pasos sugeridos**

1️⃣ **Planificación:**

* Define qué tareas necesitas para cada parte (instalación, configuración, pruebas).

* Piensa cómo dividir las tareas dentro del rol (por ejemplo: `tasks/`, `templates/`).

2️⃣ **Creación de roles:**

* Usa `ansible-galaxy init` para crear un rol, por ejemplo: `nginx_site`.

3️⃣ **Desarrollo del playbook:**

* Crea un playbook principal que invoque el rol y pase las variables necesarias.

4️⃣ **Pruebas:**

* Verifica que:

  * Nginx está instalado y corriendo.

  * La página se genera con los datos correctos.

  * La página es accesible desde el navegador o usando `curl`.

---

### **💡 Consejos**

* **Divide el trabajo:** si estás en grupo, distribuyan las partes (por ejemplo, uno se ocupa de la plantilla, otro del firewall).

* **Prueba por partes:** asegúrate de que cada parte funcione antes de juntar todo.

* **Mantén la estructura:** sigue buenas prácticas para carpetas y archivos.

* **Lee los errores:** Ansible suele dar pistas muy claras en caso de fallos.

---

### **✔️ Criterios de éxito**

Tu proyecto se considera exitoso si:

* ✅ Todos los nodos tienen Nginx instalado y en ejecución.

* ✅ La página de inicio muestra información dinámica correctamente.

* ✅ Todo está organizado en roles y sigue la estructura recomendada.

* ✅ El servicio sigue corriendo después de reiniciar el nodo.

---

### **📌 Recursos útiles**

* Documentación oficial de Ansible Roles:  https://docs.ansible.com/ansible/latest/playbook\_guide/playbooks\_reuse\_roles.html

* Guía de módulos Nginx

* Consulta tus apuntes y ejemplos de los módulos anteriores.

## **Módulo 10: Cierre y siguientes pasos (15 min)**

---

### **🎯 Objetivos del módulo**

* Recapitular los conceptos clave del taller.  
* Proporcionar buenas prácticas para aplicar Ansible en entornos reales.  
* Ofrecer recursos para continuar aprendiendo y perfeccionando habilidades en automatización.

---

### **🔑 1\. Buenas prácticas de automatización**

* **Idempotencia:** asegúrate siempre de que tus playbooks sean idempotentes (pueden ejecutarse múltiples veces sin causar cambios innecesarios).

* **Claridad y organización:**

  * Usa **roles** para mantener tu código modular y limpio.  
  * Agrupa variables en `host_vars`, `group_vars` o usa ficheros dedicados.

* **Gestión de secretos:** no guardes contraseñas o secretos en texto plano; usa herramientas como **Ansible Vault**.  
* **Documentación:** comenta tu código y mantén un README para cada proyecto.  
* **Pruebas:** prueba tus playbooks en entornos de desarrollo antes de ejecutarlos en producción.  
* **Control de versiones:** usa Git u otro sistema para versionar tus playbooks.

---

### **🌐 2\. Recomendaciones para proyectos reales**

* **Inventario dinámico:** en entornos grandes, considera usar inventarios dinámicos (integrados con la nube o sistemas CMDB).  
* **Gestión de errores:** usa bloques de `rescue` y `always` para gestionar errores.  
* **Optimización:** evita repetir código; usa plantillas, loops y condicionales para maximizar la eficiencia.  
* **Actualización constante:** Ansible y sus módulos evolucionan rápido, mantente actualizado y revisa cambios en nuevas versiones.

---

### **📚 3\. Recursos para seguir aprendiendo**

* **Documentación oficial:**  
   https://docs.ansible.com/

* **Libros y guías:**

  * *Ansible for DevOps* \- Jeff Geerling.  
  * *Ansible Up & Running* \- O'Reilly.

* **Comunidades y foros:**

  * Ansible en GitHub.  
  * Reddit: r/ansible.  
  * Stack Overflow.

* **Herramientas avanzadas:**

  * **Ansible Galaxy:** comparte y descarga roles públicos.  
  * **Red Hat Ansible Automation Platform (AAP):** plataforma comercial para gestionar la automatización a escala.

---

### **📝 4\. Evaluación teórica (30 min)**

Para reforzar lo aprendido, completa el cuestionario final. Este evalúa conceptos clave como:

* ¿Qué es la idempotencia?  
* ¿Cuál es la diferencia entre un módulo y un rol?  
* ¿Cómo se define un inventario estático?  
* ¿Qué hace un handler?  
* ¿Qué ventajas ofrece Ansible sobre otras herramientas como Puppet?

---

### **🚀 5\. Reflexión final**

Piensa en cómo podrías aplicar la automatización en tu entorno actual o futuro. ¿Qué procesos repetitivos podrías optimizar?

[image1]: <data:image/png;base64,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>