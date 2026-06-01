# Despliegue con Docker Stack, GitHub Container Registry (GHCR) y Makefile

## Objetivo

Implementar un despliegue profesional utilizando Docker Stack, Docker Swarm, GitHub Actions, GitHub Container Registry (GHCR) y Makefile para automatizar tareas comunes de construcción, publicación y despliegue.

---

# Arquitectura

```text
Desarrollador
      ↓
Git Commit
      ↓
Git Push
      ↓
GitHub
      ↓
GitHub Actions
      ↓
Pruebas Automatizadas
      ↓
Docker Build
      ↓
Docker Push
      ↓
GHCR
      ↓
Docker Stack Deploy
      ↓
3 Réplicas en Producción
```

---

# Estructura del Proyecto

```text
holaflask/
│
├── app.py
├── test_app.py
├── Dockerfile
├── stack.yml
├── Makefile
│
└── .github/
    └── workflows/
        └── ci.yml
```

---

# Aplicación Base

## app.py

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hola Mundo desde Docker Stack"

@app.route("/saludo")
def saludo():
    return "Bienvenidos a DevOps"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

---

# Pruebas

## test_app.py

```python
from app import app

def test_home():
    client = app.test_client()
    response = client.get("/")

    assert response.status_code == 200

def test_saludo():
    client = app.test_client()
    response = client.get("/saludo")

    assert response.status_code == 200
```

---

# Dockerfile

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY . .

RUN pip install flask pytest

EXPOSE 5000

CMD ["python", "app.py"]
```

---

# Imagen Publicada en GHCR

Simularemos una imagen almacenada en:

```text
ghcr.io/usuario/holaflask:latest
```

Ejemplo real:

```text
ghcr.io/byronmoreno/holaflask:latest
```

---

# Docker Stack

## stack.yml

```yaml
version: '3.9'

services:

  holaflask:

    image: ghcr.io/usuario/holaflask:latest

    ports:
      - "5000:5000"

    deploy:

      replicas: 3

      restart_policy:
        condition: on-failure

      update_config:
        parallelism: 1
        delay: 10s

    networks:
      - appnet

networks:

  appnet:
    driver: overlay
```

---

# Inicializar Docker Swarm

```bash
docker swarm init
```

---

# Desplegar el Stack

```bash
docker stack deploy -c stack.yml holaflask
```

---

# Verificar Servicios

```bash
docker service ls
```

```bash
docker stack services holaflask
```

```bash
docker stack ps holaflask
```

---

# Ver Réplicas

Resultado esperado:

```text
NAME                    MODE        REPLICAS
holaflask_holaflask     replicated  3/3
```

---

# Eliminar el Stack

```bash
docker stack rm holaflask
```

---

# Automatización con Makefile

## Makefile

```makefile
APP=holaflask
VERSION=1.0.0

IMAGE=ghcr.io/usuario/$(APP):$(VERSION)

build:
	docker build -t $(IMAGE) .

run:
	docker run -p 5000:5000 $(IMAGE)

push:
	docker push $(IMAGE)

pull:
	docker pull $(IMAGE)

deploy:
	docker stack deploy -c stack.yml $(APP)

status:
	docker stack services $(APP)

logs:
	docker service logs $(APP)_holaflask

remove:
	docker stack rm $(APP)

all: build push deploy
```

---

# Uso del Makefile

## Construir Imagen

```bash
make build
```

---

## Ejecutar Localmente

```bash
make run
```

---

## Publicar en GHCR

```bash
make push
```

---

## Descargar Imagen

```bash
make pull
```

---

## Desplegar Stack

```bash
make deploy
```

---

## Ver Estado

```bash
make status
```

---

## Ver Logs

```bash
make logs
```

---

## Eliminar Stack

```bash
make remove
```

---

# GitHub Actions

## .github/workflows/ci.yml

```yaml
name: CI-CD

on:
  push:
    branches:
      - main

jobs:

  test:

    runs-on: ubuntu-latest

    steps:

      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.12'

      - name: Instalar Dependencias
        run: |
          pip install flask pytest

      - name: Ejecutar Pruebas
        run: |
          pytest

  docker:

    needs: test

    runs-on: ubuntu-latest

    permissions:
      contents: read
      packages: write

    steps:

      - name: Checkout
        uses: actions/checkout@v4

      - name: Login GHCR
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build Docker
        run: |
          docker build -t ghcr.io/${{ github.repository_owner }}/holaflask:latest .

      - name: Push Docker
        run: |
          docker push ghcr.io/${{ github.repository_owner }}/holaflask:latest
```

---

# Flujo Completo de CI/CD

```text
1. Programador modifica app.py

2. git add .

3. git commit -m "Nueva funcionalidad"

4. git push origin main

5. GitHub Actions se ejecuta automáticamente

6. Se ejecutan las pruebas unitarias

7. Se construye la imagen Docker

8. Se publica en GHCR

9. Docker Stack descarga la nueva versión

10. Se actualizan las 3 réplicas automáticamente
```

---

# Actividad Práctica para Estudiantes

## Trabajo en Parejas

Desarrollar una API Flask con las rutas:

```text
/
```

Respuesta:

```text
Hola Mundo
```

y

```text
/saludo
```

Respuesta:

```text
Bienvenidos a DevOps
```

### Requisitos

1. Crear repositorio en GitHub.
2. Configurar Git.
3. Implementar pruebas unitarias.
4. Crear Dockerfile.
5. Crear stack.yml.
6. Crear Makefile.
7. Configurar GitHub Actions.
8. Publicar imagen en GHCR.
9. Desplegar con Docker Stack.
10. Verificar 3 réplicas en ejecución.

---

# Evidencias

* Captura del repositorio GitHub.
* Captura del workflow ejecutado correctamente.
* Captura de la imagen publicada en GHCR.
* Captura del comando:

```bash
docker service ls
```

* Captura del comando:

```bash
docker stack services holaflask
```

* Código fuente completo.

---

# Conclusión

Esta práctica integra conceptos modernos de DevOps:

* Git
* GitHub
* Pruebas Automatizadas
* GitHub Actions
* Docker
* Docker Swarm
* Docker Stack
* GitHub Container Registry (GHCR)
* Integración Continua (CI)
* Despliegue Automatizado

Representando un flujo real utilizado en entornos empresariales para garantizar calidad, trazabilidad y despliegues rápidos y confiables.
