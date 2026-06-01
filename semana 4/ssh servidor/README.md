# Clase Universitaria: Acceso Remoto Seguro mediante SSH a un Servidor VPS

## Datos Generales

**Asignatura:** Redes de Datos / Sistemas Operativos / DevOps  
**Tema:** Acceso remoto seguro mediante SSH a un servidor VPS  
**Duración:** 3 horas académicas  
**Nivel:** Universitario (Tecnología o Ingeniería en Software)

---

# Objetivo General

Implementar conexiones remotas seguras utilizando SSH para administrar servidores VPS Linux desde equipos cliente Windows o Linux.

---

# Resultados de Aprendizaje

Al finalizar la clase, el estudiante será capaz de:

1. Comprender el funcionamiento del protocolo SSH.
2. Diferenciar entre acceso local y acceso remoto.
3. Conectarse a un servidor VPS mediante SSH.
4. Administrar archivos y directorios remotamente.
5. Transferir archivos utilizando SCP.
6. Generar claves SSH para autenticación segura.
7. Aplicar buenas prácticas de seguridad en servidores Linux.

---

# Competencia

Administra servidores remotos mediante protocolos seguros aplicando buenas prácticas de acceso, autenticación y gestión de recursos.

---

# Introducción

En la actualidad la mayoría de servidores se encuentran en centros de datos remotos o en la nube.

Para administrarlos no es necesario estar físicamente frente al servidor.

SSH (Secure Shell) permite conectarse de forma segura a un servidor remoto utilizando una conexión cifrada.

---

# ¿Qué es SSH?

SSH significa:

```text
Secure Shell
```

Es un protocolo de red que permite:

- Acceso remoto seguro.
- Administración de servidores.
- Transferencia de archivos.
- Ejecución de comandos remotos.

---

# Arquitectura SSH

```text
Cliente SSH
(Mi Computadora)

        │
        │ Internet
        │
        ▼

Servidor VPS Linux
(Ubuntu / Debian)
```

---

# ¿Qué es un VPS?

VPS significa:

```text
Virtual Private Server
```

Es una máquina virtual que funciona como un servidor independiente.

Ejemplos:

- VPS Ubuntu
- VPS Debian
- VPS Rocky Linux
- VPS AlmaLinux

---

# Información Necesaria para Conectarse

Normalmente un proveedor entrega:

```text
IP: 192.168.1.100

Usuario: root

Contraseña:
**********
```

o

```text
IP: 192.168.1.100

Usuario: ubuntu

Clave SSH
```

---

# Verificar Conectividad

Antes de conectarse:

```bash
ping 192.168.1.100
```

Ejemplo:

```bash
ping google.com
```

---

# Conexión SSH Básica

## Linux

```bash
ssh usuario@IP
```

Ejemplo:

```bash
ssh root@192.168.1.100
```

---

## Windows PowerShell

```powershell
ssh root@192.168.1.100
```

---

# Primera Conexión

Al conectarse por primera vez:

```text
Are you sure you want to continue connecting?
```

Responder:

```text
yes
```

---

# Verificar Usuario Actual

```bash
whoami
```

Resultado:

```text
root
```

---

# Verificar Sistema Operativo

```bash
cat /etc/os-release
```

---

# Verificar Dirección IP

```bash
ip addr
```

---

# Navegación Básica

## Directorio actual

```bash
pwd
```

## Listar archivos

```bash
ls
```

## Listar detalladamente

```bash
ls -la
```

## Cambiar directorio

```bash
cd /home
```

---

# Crear Directorios

```bash
mkdir pruebas
```

Verificar:

```bash
ls
```

---

# Crear Archivos

```bash
touch archivo.txt
```

---

# Editar Archivos

```bash
nano archivo.txt
```

Guardar:

```text
CTRL + O
ENTER
```

Salir:

```text
CTRL + X
```

---

# Ver Contenido

```bash
cat archivo.txt
```

---

# Información del Sistema

## Memoria RAM

```bash
free -h
```

## Disco Duro

```bash
df -h
```

## Procesador

```bash
lscpu
```

---

# Usuarios

## Ver usuario actual

```bash
whoami
```

## Ver usuarios conectados

```bash
w
```

---

# Transferencia de Archivos

## SCP

Copiar desde PC al servidor:

```bash
scp archivo.txt root@192.168.1.100:/root
```

Copiar desde servidor a PC:

```bash
scp root@192.168.1.100:/root/archivo.txt .
```

---

# Generación de Claves SSH

## Crear Clave

```bash
ssh-keygen
```

Resultado:

```text
id_rsa
id_rsa.pub
```

---

# Ubicación de las Claves

## Linux

```bash
~/.ssh/
```

## Windows

```text
C:\Users\usuario\.ssh
```

---

# Copiar Clave Pública al Servidor

```bash
ssh-copy-id root@192.168.1.100
```

---

# Conexión sin Contraseña

Una vez instalada la clave:

```bash
ssh root@192.168.1.100
```

Ya no solicitará contraseña.

---

# Seguridad Básica

## Crear un Usuario Nuevo

```bash
adduser estudiante
```

## Dar Permisos de Administrador

Ubuntu / Debian:

```bash
usermod -aG sudo estudiante
```

---

# Actualizar el Sistema

```bash
sudo apt update
sudo apt upgrade -y
```

---

# Instalar Docker Remotamente

```bash
sudo apt install docker.io -y
```

Verificar:

```bash
docker --version
```

---

# Flujo Profesional

```text
Administrador
       │
       ▼
   SSH Client
       │
       ▼
Servidor VPS
       │
       ▼
Administración
       │
       ▼
Docker
       │
       ▼
Aplicaciones
```

---

# Actividad Práctica

## Trabajo en Parejas

### Parte 1

Conectarse al VPS mediante SSH.

### Parte 2

Ejecutar:

```bash
whoami
hostname
pwd
```

### Parte 3

Crear un directorio:

```bash
mkdir devops
```

### Parte 4

Crear un archivo:

```bash
touch devops/archivo.txt
```

### Parte 5

Editar el archivo:

```bash
nano devops/archivo.txt
```

Agregar el texto:

```text
Mi primera conexión SSH
```

### Parte 6

Instalar Docker:

```bash
sudo apt update
sudo apt install docker.io -y
```

### Parte 7

Verificar la instalación:

```bash
docker --version
```

---

# Actividad Adicional: Conexión con Claves SSH

1. Generar claves SSH.
2. Copiar la clave pública al servidor.
3. Conectarse sin contraseña.
4. Explicar las ventajas de este mecanismo frente al uso de contraseñas.

---

# Evidencias

- Captura de la conexión SSH.
- Captura de los comandos ejecutados.
- Captura del directorio creado.
- Captura del archivo generado.
- Captura de la instalación de Docker.
- Captura de la versión de Docker.
- Captura de la conexión mediante clave SSH.

---

---

# Preguntas de Reflexión

1. ¿Cuál es la diferencia entre Telnet y SSH?
2. ¿Por qué SSH es considerado un protocolo seguro?
3. ¿Qué ventajas tiene utilizar autenticación mediante claves SSH?
4. ¿Qué riesgos existen al permitir acceso remoto con el usuario root?
5. ¿Qué beneficios aporta Docker cuando se administra un VPS?

---

# Conclusión

SSH es una de las herramientas más importantes en la administración de servidores Linux. Permite gestionar sistemas remotos de manera segura, transferir archivos, automatizar tareas y desplegar aplicaciones sin necesidad de acceso físico al servidor.

El dominio de SSH constituye una habilidad fundamental para administradores de sistemas, ingenieros DevOps, especialistas en ciberseguridad y desarrolladores de software que trabajan con servidores Linux, Docker, Kubernetes y plataformas de computación en la nube.