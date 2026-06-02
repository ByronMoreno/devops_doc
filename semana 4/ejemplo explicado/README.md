# Ejemplo completo del Workflow GitHub Actions para CI/CD con Docker, GHCR y VPS

## Objetivo

Automatizar completamente el ciclo de vida de una aplicación utilizando:

* GitHub
* GitHub Actions
* Docker
* GitHub Container Registry (GHCR)
* SSH
* Docker Stack
* Docker Swarm

Cuando un desarrollador realiza un **git push**, el sistema:

1. Construye la imagen Docker.
2. Publica la imagen en GHCR.
3. Se conecta automáticamente al VPS.
4. Descarga la nueva imagen.
5. Actualiza los servicios Docker Stack.

---

# Flujo General

```text
Desarrollador
      │
      ▼
git push
      │
      ▼
GitHub Repository
      │
      ▼
GitHub Actions
      │
      ├── Construcción Docker
      │
      ├── Publicación GHCR
      │
      ├── Conexión SSH al VPS
      │
      ├── Transferencia stack.yml
      │
      └── Despliegue Docker Stack
      │
      ▼
Servidor VPS
      │
      ▼
Contenedores Actualizados
```

---

# Workflow Completo

```yaml
name: Python application

on:
  push:
    branches: "quintoa"

permissions:
  contents: read
  packages: write

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v4

    - name: Set up Python 3.10
      uses: actions/setup-python@v3
      with:
        python-version: "3.11"

    - name: Login to GitHub Container Registry
      uses: docker/login-action@v3
      with:
        registry: ghcr.io
        username: ${{ github.actor }}
        password: ${{ secrets.GITHUB_TOKEN }}

    - name: Build and push Docker image
      uses: docker/build-push-action@v6
      with:
        push: true
        tags: ghcr.io/byronmoreno/finalquintoa:1.0.0

    - name: Mostrar variables básicas
      run: |
        echo "Host: ${{ secrets.VPS_HOST }}"
        echo "Usuario: ${{ secrets.VPS_USER }}"
        echo "Puerto: ${{ secrets.VPS_SSH_PORT }}"

    - name: Probar conexión SSH
      uses: appleboy/ssh-action@v1
      with:
        host: ${{ secrets.VPS_HOST }}
        username: ${{ secrets.VPS_USER }}
        password: ${{ secrets.VPS_PASSWORD }}
        port: ${{ secrets.VPS_SSH_PORT }}
        script: |
          echo "Conexión exitosa"
          hostname
          whoami
          uptime

    - name: Pasar el stack.yml al servidor
      uses: appleboy/scp-action@v1
      with:
        host: ${{ secrets.VPS_HOST }}
        username: ${{ secrets.VPS_USER }}
        password: ${{ secrets.VPS_PASSWORD }}
        port: ${{ secrets.VPS_SSH_PORT }}
        source: "stack.yml"
        target: "/home/${{ secrets.VPS_USER }}/despliegue"

    - name: Despliegue de los servicios
      uses: appleboy/ssh-action@v1
      with:
        host: ${{ secrets.VPS_HOST }}
        username: ${{ secrets.VPS_USER }}
        password: ${{ secrets.VPS_PASSWORD }}
        port: ${{ secrets.VPS_SSH_PORT }}
        script: |
          cd ~/despliegue

          echo "${{ secrets.GHCR_PAT }}" | docker login ghcr.io -u byronmoreno --password-stdin

          docker pull ghcr.io/byronmoreno/finalquintoa:1.0.0

          docker stack rm cristhian || true

          sleep 30

          docker stack deploy -c stack.yml --with-registry-auth cristhian

          docker ps
```

---

# Explicación Paso a Paso

## 1. Nombre del Workflow

```yaml
name: Python application
```

Define el nombre que aparecerá en la pestaña **Actions** de GitHub.

---

## 2. Evento Disparador

```yaml
on:
  push:
    branches: "quintoa"
```

Este workflow se ejecutará automáticamente cada vez que exista un:

```bash
git push origin quintoa
```

Solamente para la rama:

```text
quintoa
```

---

## 3. Permisos

```yaml
permissions:
  contents: read
  packages: write
```

Permite:

### contents: read

Leer el código fuente.

### packages: write

Publicar imágenes Docker en GHCR.

---

## 4. Crear Máquina Temporal

```yaml
runs-on: ubuntu-latest
```

GitHub crea una máquina Ubuntu temporal para ejecutar todas las tareas.

---

## 5. Descargar Código Fuente

```yaml
- uses: actions/checkout@v4
```

Descarga el repositorio dentro de la máquina virtual de GitHub Actions.

---

## 6. Instalar Python

```yaml
- uses: actions/setup-python@v3
```

Instala Python.

```yaml
python-version: "3.11"
```

Versión utilizada para pruebas o ejecución de scripts.

---

## 7. Login en GitHub Container Registry

```yaml
uses: docker/login-action@v3
```

Permite autenticarse contra:

```text
ghcr.io
```

Utilizando:

```yaml
username: ${{ github.actor }}
password: ${{ secrets.GITHUB_TOKEN }}
```

---

## 8. Construcción y Publicación Docker

```yaml
uses: docker/build-push-action@v6
```

Construye automáticamente la imagen.

```yaml
push: true
```

Indica que debe publicarse.

---

### Imagen generada

```text
ghcr.io/byronmoreno/finalquintoa:1.0.0
```

---

## 9. Mostrar Variables de Entorno

```yaml
echo "Host: ${{ secrets.VPS_HOST }}"
```

Permite verificar que GitHub está leyendo correctamente los secretos.

---

### Secretos utilizados

```text
VPS_HOST
VPS_USER
VPS_PASSWORD
VPS_SSH_PORT
GHCR_PAT
```

Configurados en:

Settings → Secrets and Variables → Actions

---

## 10. Probar Conexión SSH

```yaml
uses: appleboy/ssh-action@v1
```

GitHub Actions se conecta al VPS.

Ejecuta:

```bash
hostname
whoami
uptime
```

Para verificar:

* Acceso al servidor.
* Usuario correcto.
* Estado del sistema.

---

## 11. Transferir stack.yml

```yaml
uses: appleboy/scp-action@v1
```

Copia:

```text
stack.yml
```

Desde GitHub Actions hacia:

```text
/home/usuario/despliegue
```

En el VPS.

---

## 12. Login a GHCR desde el VPS

```bash
echo "${{ secrets.GHCR_PAT }}" | docker login ghcr.io -u byronmoreno --password-stdin
```

Permite que el VPS descargue imágenes privadas desde GHCR.

---

## 13. Descargar Nueva Imagen

```bash
docker pull ghcr.io/byronmoreno/finalquintoa:1.0.0
```

Obtiene la última versión publicada.

---

## 14. Eliminar Stack Actual

```bash
docker stack rm cristhian || true
```

Elimina el stack existente.

El operador:

```bash
|| true
```

Evita errores si el stack no existe.

---

## 15. Esperar Eliminación

```bash
sleep 30
```

Da tiempo para que Docker Swarm elimine completamente los servicios.

---

## 16. Desplegar Nueva Versión

```bash
docker stack deploy -c stack.yml --with-registry-auth cristhian
```

Crea nuevamente el stack.

---

### --with-registry-auth

Envía las credenciales de GHCR a todos los nodos del cluster.

Esto permite descargar imágenes privadas.

---

## 17. Verificar Contenedores

```bash
docker ps
```

Muestra los contenedores activos después del despliegue.

---

# Secretos Necesarios

Configurar en GitHub:

```text
VPS_HOST
VPS_USER
VPS_PASSWORD
VPS_SSH_PORT
GHCR_PAT
```

---

# Resultado Final

Cada vez que se ejecute:

```bash
git add .
git commit -m "Nueva versión"
git push origin quintoa
```

GitHub realizará automáticamente:

```text
1. Construcción Docker
2. Publicación GHCR
3. Conexión SSH
4. Transferencia stack.yml
5. Descarga imagen
6. Actualización Docker Stack
7. Verificación final
```

Logrando un proceso completo de Integración Continua (CI) y Despliegue Continuo (CD) utilizado en entornos profesionales DevOps.
