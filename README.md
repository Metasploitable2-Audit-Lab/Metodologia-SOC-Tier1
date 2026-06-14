# Guía Completa de Laboratorios de Linux para Analistas de SOC (Módulos 1 al 4\)

Este documento centraliza todos los comandos, escenarios prácticos y análisis de seguridad desarrollados desde el Módulo 1 al Módulo 4\. Está estructurado como una guía de referencia rápida para el monitoreo de infraestructura, auditoría forense de logs, gestión de permisos y análisis de conectividad de red desde la perspectiva de un Analista de Ciberseguridad Junior.

---

## 🗺️ Módulo 1: Navegación, Ubicación y Orientación en el Sistema

### Escenario del SOC

Al responder a un incidente o ingresar a un servidor bajo investigación, el analista debe orientarse inmediatamente: saber en qué directorio está parado y qué archivos ocultos o visibles existen en el entorno.

### Comandos Clave

* **`pwd`** (Print Working Directory): Muestra la ruta absoluta del directorio actual.  
* **`ls -la`**: Lista todos los archivos y directorios (incluyendo los ocultos que empiezan con un punto `.`), mostrando detalles de tamaño, propietario, grupo y permisos.

### Ejemplo de Aplicación

pwd

\# Salida esperada: /home/alejandro

ls \-la

\# Muestra la lista completa de archivos con permisos detallados (drwxr-xr-x, \-rw-r--r--, etc.)

---

## 📁 Módulo 2: Seguridad de Archivos, Permisos Blandos/Duros y Filtros Forenses

### Escenario del SOC

Un analista detecta un archivo o script confidencial que expone información sensible. Es obligatorio restringir sus permisos para que ningún otro usuario o proceso no autorizado del sistema pueda leerlo, modificarlo o ejecutarlo. Asimismo, se requiere filtrar información específica en listas masivas mediante cañerías.

### Comandos Clave

* **`touch <nombre_archivo>`**: Crea un archivo vacío de forma rápida.  
* **`chmod <permiso> <nombre_archivo>`**: Modifica los permisos de acceso del archivo. El uso de `chmod 700` asegura el control total para el propietario (`rwx`) y deniega absolutamente todo acceso (`---`) al grupo y a otros usuarios.  
* **`|` (Cañería / Pipe)**: Redirige la salida estándar de un comando para que sirva como entrada del siguiente comando.  
* **`grep <término>`**: Busca coincidencias de texto exactas dentro de un flujo de datos o archivos.

### Ejemplo de Aplicación

\# 1\. Crear el archivo sensible

touch secreto.txt

\# 2\. Blindar el archivo (Solo el propietario tiene permisos rwx)

chmod 700 secreto.txt

\# 3\. Aplicar un filtro forense para aislar el archivo en la lista

ls \-l | grep secreto

\# Salida esperada:

\# \-rwx------ 1 alejandro alejandro 0 Jun 13 17:16 secreto.txt

---

## 💾 Módulo 3: Monitoreo Local, Control de Procesos y Auditoría de Logs

### Escenario del SOC

Monitorear la salud del servidor local y auditar intentos de acceso. Si un proceso malicioso o colgado consume los recursos, o si el espacio en disco llega al 100%, el servidor puede sufrir una denegación de servicio (DoS) o dejar de registrar eventos críticos de seguridad (puntos ciegos).

### Comandos Clave

* **`top`**: Muestra en tiempo real los procesos activos, consumo de CPU, memoria RAM y tiempo de ejecución (se sale presionando la tecla `q`).  
* **`kill <PID>`** y **`kill -9 <PID>`**: Envían señales para terminar un proceso. La bandera `-9` fuerza el cierre inmediato y no negociable del proceso colgado o malicioso.  
* **`df -h`**: Muestra el espacio libre y ocupado en los discos y particiones del sistema en formato de lectura humana (Gigabytes/Megabytes).  
* **`ss -tuln`**: Muestra los puertos de red locales que están actualmente en estado de escucha (`LISTEN`), detallando protocolos TCP/UDP de forma numérica.  
* **`sudo tail -n 10 /var/log/auth.log`**: Muestra las últimas 10 líneas del archivo de registro de autenticaciones, crítico para auditar quién inició sesión o escaló privilegios (`sudo`).

### Ejemplo de Aplicación

\# Revisar almacenamiento del servidor

df \-h

\# Analizar conexiones y puertos abiertos de forma local

ss \-tuln

\# Auditar los últimos eventos de seguridad de usuarios

sudo tail \-n 10 /var/log/auth.log

---

## 📡 Módulo 4: Redes, Protocolos y Conectividad Externa

### Escenario del SOC

El análisis de red permite investigar la interacción del servidor con el mundo exterior. Un analista utiliza estas herramientas para verificar interfaces locales, validar la salida a internet, mapear las IPs públicas de atacantes, auditar registros DNS y rastrear la ruta física de los paquetes para detectar desvíos sospechosos de tráfico.

### Comandos Clave

* **`ip a`**: Identifica las tarjetas de red instaladas y las direcciones IP privadas asignadas a la máquina local (ej. la interfaz `enp0s3`).  
* **`ping -c 4 <IP_o_Dominio>`**: Envía paquetes ICMP de prueba para comprobar si un servidor remoto está encendido y accesible. El modificador `-c 4` limita la prueba a 4 paquetes.  
* **`nslookup <dominio>`**: Consulta a los servidores DNS para traducir un nombre de dominio (ej. `google.com`) a su dirección IP pública correspondiente (IPv4 e IPv6).  
* **`curl ifconfig.me`**: Realiza una petición web externa simplificada para obtener la dirección IP pública real con la que la red local se identifica en internet.  
* **`traceroute <IP_o_Dominio>`**: Registra y muestra paso a paso cada uno de los routers intermedios (saltos) que recorre un paquete de datos hasta llegar a su destino. Permite identificar cuellos de botella (altos milisegundos) o bloqueos (asteriscos `* * *`).

### Ejemplo de Aplicación

\# 1\. Comprobar direccionamiento IP interno

ip a

\# 2\. Validar conexión externa contra un DNS público conocido (Cloudflare)

ping \-c 4 1.1.1.1

\# 3\. Investigar la IP pública detrás de un nombre de dominio

nslookup google.com

\# 4\. Conocer la IP pública de salida de nuestra red

curl ifconfig.me

\# 5\. Mapear la ruta completa de saltos en internet

traceroute 1.1.1.1

---

## 📊 Tabla Resumen de Comandos para el SOC

| Módulo | Comando | Función Principal en Ciberseguridad |
| :---- | :---- | :---- |
| **M1** | `pwd` | Verificación de ubicación actual en la consola. |
| **M1** | `ls -la` | Detección de archivos ocultos y análisis de metadatos básicos. |
| **M2** | `chmod 700` | Aislamiento y blindaje estricto de archivos confidenciales. |
| **M2** | `ls -l | grep palabra` | Filtrado rápido de evidencias o artefactos dentro de directorios masivos. |
| **M3** | `top` | Identificación de picos de consumo por malware o procesos colgados. |
| **M3** | `kill -9 <PID>` | Mitigación inmediata mediante la finalización forzada de un proceso. |
| **M3** | `df -h` | Monitoreo de espacio en disco para evitar la pérdida de logs del sistema. |
| **M3** | `ss -tuln` | Auditoría de puertos abiertos expuestos a conexiones internas o externas. |
| **M3** | `tail -n 10 /var/log/...` | Monitoreo de eventos de seguridad en tiempo real e intentos de intrusión. |
| **M4** | `ip a` | Confirmación de direccionamiento IP privado e interfaces activas. |
| **M4** | `ping -c 4` | Verificación rápida de disponibilidad y estado de activos remotos. |
| **M4** | `nslookup` | Investigación de infraestructura y detección de secuestro de DNS (DNS Hijacking). |
| **M4** | `curl ifconfig.me` | Identificación del direccionamiento IP público perimetral. |
| **M4** | `traceroute` | Análisis de enrutamiento físico y detección de desvíos de tráfico maliciosos. |

