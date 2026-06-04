# Taller Práctico de DevOps
## Despliegue de una Aplicación Flask con PostgreSQL, pgAdmin, Docker Compose y GitHub Actions

---

# Objetivo

Implementar una aplicación web básica desarrollada en Flask que se conecte a PostgreSQL, administrada mediante pgAdmin, desplegada con Docker Compose y automatizada mediante GitHub Actions para generar y publicar imágenes Docker en GitHub Container Registry (GHCR).

---

# Arquitectura del Proyecto

```text
                   ┌─────────────┐
                   │   Usuario   │
                   └──────┬──────┘
                          │
                          ▼
                  localhost:5000
                          │
                          ▼
                  ┌─────────────┐
                  │ Flask App   │
                  └──────┬──────┘
                         │
                         ▼
                  ┌─────────────┐
                  │ PostgreSQL  │
                  └──────┬──────┘
                         │
                         ▼
                  ┌─────────────┐
                  │  pgAdmin    │
                  └─────────────┘
```

---

# Estructura del Proyecto

```text
devops-flask-postgres/
│
├── app.py
├── requirements.txt
├── Dockerfile
├── docker-compose.yml
│
└── .github/
    └── workflows/
        └── docker.yml
```

---

# Paso 1: Crear la Aplicación Flask

## app.py

```python
from flask import Flask
import psycopg2

app = Flask(__name__)

VERSION = "2.0.0"

@app.route("/")
def inicio():

    try:

        conexion = psycopg2.connect(
            host="db",
            database="empresa",
            user="admin",
            password="admin123"
        )

        cursor = conexion.cursor()

        cursor.execute("SELECT version();")

        version = cursor.fetchone()

        cursor.close()
        conexion.close()

        return f"""
        <h1>Aplicación Flask</h1>
        <h2>Versión {VERSION}</h2>
        <p>Conexión exitosa a PostgreSQL</p>
        <p>{version}</p>
        """

    except Exception as e:
        return str(e)

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

---

# Paso 2: Dependencias

## requirements.txt

```text
flask
psycopg2-binary
```

---

# Paso 3: Dockerfile

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python","app.py"]
```

---

# Paso 4: Construcción Local de la Imagen

## Construir Imagen

```bash
docker build -t ghcr.io/usuario/devops-flask-postgres:2.0.0 .
```

---

## Ejecutar Imagen

```bash
docker run -p 5000:5000 ghcr.io/usuario/devops-flask-postgres:2.0.0
```

---

# Paso 5: Docker Compose

En un entorno DevOps real normalmente NO se utiliza build localmente.

La imagen ya fue generada por GitHub Actions y publicada en GHCR.

Por lo tanto, Docker Compose consumirá directamente la imagen publicada.

## docker-compose.yml

```yaml
version: '3.9'

services:

  db:

    image: postgres:17-alpine

    container_name: postgresdb

    environment:
      POSTGRES_DB: empresa
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: admin123

    ports:
      - "5432:5432"

    volumes:
      - postgres_data:/var/lib/postgresql/data

  pgadmin:

    image: dpage/pgadmin4

    container_name: pgadmin

    environment:
      PGADMIN_DEFAULT_EMAIL: admin@correo.com
      PGADMIN_DEFAULT_PASSWORD: admin123

    ports:
      - "8080:80"

    depends_on:
      - db

  app:

    image: ghcr.io/usuario/devops-flask-postgres:2.0.0

    container_name: flaskapp

    ports:
      - "5000:5000"

    depends_on:
      - db

volumes:
  postgres_data:
```

---

# Paso 6: Levantar los Servicios

```bash
docker compose up -d
```

---

# Paso 7: Verificar Servicios

## Flask

```text
http://localhost:5000
```

---

## pgAdmin

```text
http://localhost:8080
```

### Credenciales

```text
Correo:
admin@correo.com

Contraseña:
admin123
```

---

# Paso 8: Registrar PostgreSQL en pgAdmin

## General

```text
Name:
PostgreSQL
```

## Connection

```text
Host:
db

Port:
5432

Database:
empresa

Username:
admin

Password:
admin123
```

---

# Paso 9: Crear Repositorio GitHub

```bash
git init

git add .

git commit -m "Version 2.0.0"

git branch -M main

git remote add origin https://github.com/USUARIO/devops-flask-postgres.git

git push -u origin main
```

---

# Paso 10: GitHub Container Registry (GHCR)

La imagen será publicada automáticamente en:

```text
ghcr.io/usuario/devops-flask-postgres:2.0.0
```

---

# Paso 11: GitHub Actions

Crear la siguiente estructura:

```text
.github/
└── workflows/
    └── docker.yml
```

---

# Workflow Completo

## .github/workflows/docker.yml

```yaml
name: Build and Publish Docker Image

on:

  push:

    branches:
      - main

permissions:
  contents: read
  packages: write

env:
  IMAGE_NAME: devops-flask-postgres
  IMAGE_TAG: 2.0.0

jobs:

  build:

    runs-on: ubuntu-latest

    steps:

      - name: Descargar Código
        uses: actions/checkout@v4

      - name: Login GHCR
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Construir Imagen
        run: |
          docker build \
          -t ghcr.io/${{ github.repository }}:${{ env.IMAGE_TAG }} \
          -t ghcr.io/${{ github.repository }}:latest \
          .

      - name: Publicar Imagen
        run: |
          docker push ghcr.io/${{ github.repository }}:${{ env.IMAGE_TAG }}
          docker push ghcr.io/${{ github.repository }}:latest
```

---

# Paso 12: Verificar Publicación

Ingresar al repositorio GitHub.

```text
Repositorio
   │
   ├── Actions
   │
   └── Packages
```

Deberá aparecer:

```text
ghcr.io/usuario/devops-flask-postgres
```

con los tags:

```text
latest
2.0.0
```

---

# Paso 13: Actualizar la Versión

Modificar:

```python
VERSION = "3.0.0"
```

Actualizar el workflow:

```yaml
IMAGE_TAG: 3.0.0
```

Realizar commit:

```bash
git add .
git commit -m "Version 3.0.0"
git push
```

GitHub Actions construirá automáticamente:

```text
ghcr.io/usuario/devops-flask-postgres:3.0.0
```

---

# Actividad 1

Levantar correctamente:

- PostgreSQL
- pgAdmin
- Flask

---

# Actividad 2

Verificar la conexión de Flask con PostgreSQL.

---

# Actividad 3

Crear la tabla:

```sql
CREATE TABLE clientes(
    id SERIAL PRIMARY KEY,
    nombre VARCHAR(100)
);
```

---

# Actividad 4

Insertar tres registros desde pgAdmin.

---

# Actividad 5

Modificar Flask para listar los clientes registrados.

---

# Actividad 6

Publicar automáticamente la imagen Docker en GHCR utilizando GitHub Actions.

---

# Actividad 7

Crear una nueva versión:

```text
3.0.0
```

y verificar que GitHub Actions publique correctamente la nueva imagen.

---

# Flujo DevOps Implementado

```text
Desarrollador
      │
      ▼
GitHub Repository
      │
      ▼
GitHub Actions
      │
      ▼
Docker Build
      │
      ▼
GitHub Container Registry (GHCR)
      │
      ▼
Docker Compose
      │
      ▼
Flask + PostgreSQL + pgAdmin
```

# Resultado Esperado

Al finalizar el taller el estudiante será capaz de:

- Crear aplicaciones Dockerizadas.
- Conectarse a PostgreSQL desde Flask.
- Administrar bases de datos con pgAdmin.
- Construir imágenes Docker.
- Publicar imágenes en GHCR.
- Consumir imágenes desde Docker Compose.
- Automatizar el ciclo de construcción mediante GitHub Actions.
- Comprender un flujo básico de CI/CD utilizado en entornos DevOps.