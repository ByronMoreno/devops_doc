# Clase semana 4: Versionamiento, Pruebas e Integración Continua con GitHub Actions y Docker

## Datos Generales

**Asignatura:** DevOps  
**Tema:** Versionamiento, Pruebas e Integración Continua  

---

# Objetivo General

Implementar un flujo básico de Integración Continua (CI) utilizando GitHub, GitHub Actions y Docker, aplicando buenas prácticas de versionamiento, automatización de pruebas y publicación de imágenes en GitHub Container Registry (GHCR).

---

# Resultados de Aprendizaje

1. Explicar la importancia del control de versiones en proyectos de software.
2. Utilizar Git y GitHub para gestionar cambios en el código.
3. Crear una aplicación básica en Python.
4. Implementar pruebas automatizadas.
5. Construir imágenes Docker.
6. Configurar un workflow de GitHub Actions.
7. Automatizar la construcción y publicación de imágenes en GHCR.
8. Comprender el concepto de DevOps e Integración Continua.

---

# Competencia

Desarrolla e integra soluciones de software aplicando herramientas modernas de automatización para garantizar calidad, trazabilidad y despliegue continuo.

---

# 1. Introducción al Versionamiento

## Conceptos

- Git
- GitHub
- Repositorio local y remoto
- Commit
- Branch
- Merge
- Pull Request
- Release
- Tag

## Flujo Profesional

```text
Desarrollador
      ↓
Git Commit
      ↓
GitHub
      ↓
Pull Request
      ↓
Revisión
      ↓
Merge
      ↓
GitHub Actions
      ↓
Pruebas
      ↓
Docker Build
      ↓
GHCR
```

---

# 2. Aplicación Base en Python

## app.py

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hola Mundo desde CI/CD"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

---

# 3. Pruebas Automatizadas

## test_app.py

```python
from app import app

def test_home():
    client = app.test_client()
    response = client.get("/")

    assert response.status_code == 200
```

---

# 4. Dockerización

## Dockerfile

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY . .

RUN pip install flask pytest

EXPOSE 5000

CMD ["python", "app.py"]
```

## Construcción

```bash
docker build -t holaflask .
```

## Ejecución

```bash
docker run -p 5000:5000 holaflask
```

---

# 5. GitHub Container Registry (GHCR)

## ¿Qué es GHCR?

GitHub Container Registry permite almacenar imágenes Docker directamente en GitHub.

Ejemplo:

```text
ghcr.io/usuario/holaflask
```

Beneficios:

- Integración con GitHub Actions
- Versionado de imágenes
- Control de acceso
- Automatización de despliegues

---

# 6. Integración Continua con GitHub Actions

## Estructura

```text
.github/
└── workflows/
    └── ci.yml
```

## ci.yml

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

    - name: Descargar repositorio
      uses: actions/checkout@v4

    - name: Instalar Python
      uses: actions/setup-python@v5
      with:
        python-version: '3.12'

    - name: Instalar dependencias
      run: |
        pip install flask pytest

    - name: Ejecutar pruebas
      run: |
        pytest

  docker:

    needs: test

    runs-on: ubuntu-latest

    permissions:
      contents: read
      packages: write

    steps:

    - name: Descargar repositorio
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

# 7. Flujo Completo DevOps

```text
Desarrollador
      ↓
Git Add
      ↓
Git Commit
      ↓
Git Push
      ↓
GitHub
      ↓
GitHub Actions
      ↓
Pruebas Automáticas
      ↓
Docker Build
      ↓
Docker Push
      ↓
GHCR
```

---

# Actividad Práctica

## Trabajo en parejas

Crear una API Flask con dos rutas:

### Ruta 1

```text
/
```

Retorna:

```text
Hola Mundo
```

### Ruta 2

```text
/saludo
```

Retorna:

```text
Bienvenidos a DevOps
```

## Requisitos

1. Crear repositorio en GitHub.
2. Implementar Git.
3. Crear pruebas unitarias.
4. Crear Dockerfile.
5. Configurar GitHub Actions.
6. Publicar imagen en GHCR.
7. Verificar la publicación.

---

# Criterios de Evaluación

| Criterio | Puntos |
|-----------|---------|
| Uso correcto de Git | 1 |
| Aplicación Python funcional | 1 |
| Pruebas automatizadas | 1 |
| Dockerfile funcional | 1 |
| GitHub Actions funcional | 2 |
| Publicación en GHCR | 2 |
| Documentación del proceso | 2 |
| **Total** | **10** |

---

# Cierre

Antes:

```text
Programar → Entregar
```

Ahora:

```text
Programar
   ↓
Versionar
   ↓
Probar
   ↓
Integrar
   ↓
Empaquetar
   ↓
Publicar
```
