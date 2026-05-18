# 🚀 CI/CD con GitHub Actions y Python
## Proyecto Hola Mundo Profesional

![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-CI%2FCD-blue?logo=githubactions)
![Python](https://img.shields.io/badge/Python-3.11-yellow?logo=python)
![Status](https://img.shields.io/badge/Status-Completed-success)

---

# 📘 Descripción

Este proyecto demuestra cómo implementar un flujo básico de **CI/CD (Continuous Integration / Continuous Deployment)** utilizando:

- Python
- Git
- GitHub
- GitHub Actions
- Pytest

El objetivo es automatizar:

✅ Ejecución del proyecto  
✅ Validación automática  
✅ Pruebas automáticas  
✅ Simulación de despliegue  

---

# 🧠 ¿Qué es CI/CD?

## 🔹 CI — Continuous Integration

Proceso automatizado que permite:

- Integrar código continuamente
- Ejecutar pruebas automáticas
- Detectar errores rápidamente

---

## 🔹 CD — Continuous Deployment

Proceso automatizado para:

- Desplegar aplicaciones
- Publicar nuevas versiones
- Automatizar entornos productivos

---

# 🛠️ Tecnologías Utilizadas

| Tecnología | Descripción |
|---|---|
| Python | Lenguaje principal |
| Git | Control de versiones |
| GitHub | Repositorio remoto |
| GitHub Actions | Automatización CI/CD |
| Pytest | Framework de pruebas |

---

# 📂 Estructura del Proyecto

```text
hola-python-cicd/
│
├── app.py
├── requirements.txt
├── test_app.py
│
└── .github/
    └── workflows/
        └── ci-cd.yml
```

---

# ⚙️ Requisitos Previos

Instalar:

- Python 3.11+
- Git
- Visual Studio Code
- Cuenta en GitHub

---

# 🚀 Paso 1 — Crear el Proyecto

```bash
mkdir hola-python-cicd
cd hola-python-cicd
```

---

# 🚀 Paso 2 — Crear Aplicación Python

## 📄 app.py

```python
print("Hola Mundo desde CI/CD con GitHub Actions")
```

---

# 🚀 Paso 3 — Crear Archivo de Dependencias

## 📄 requirements.txt

```txt

```

---

# 🚀 Paso 4 — Crear Archivo de Pruebas

## 📄 test_app.py

```python
def test_hola():
    mensaje = "Hola Mundo"
    assert "Hola" in mensaje
```

---

# 🚀 Paso 5 — Inicializar Git

```bash
git init
```

---

# 🚀 Paso 6 — Crear Repositorio en GitHub

1. Ingresar a GitHub
2. Crear nuevo repositorio
3. Nombre: hola-python-cicd
4. Presionar Create Repository

---

# 🚀 Paso 7 — Conectar Proyecto con GitHub

```bash
git remote add origin https://github.com/USUARIO/hola-python-cicd.git
```

---

# 🚀 Paso 8 — Crear GitHub Actions Workflow

## Crear directorios

```text
.github/workflows/
```

---

## 📄 ci-cd.yml

```yaml
name: Python CI/CD

on:
  push:
    branches:
      - main

jobs:

  build:

    runs-on: ubuntu-latest

    steps:

      - name: Descargar código
        uses: actions/checkout@v4

      - name: Instalar Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Instalar dependencias
        run: |
          pip install -r requirements.txt
          pip install pytest

      - name: Ejecutar aplicación
        run: python app.py

      - name: Ejecutar pruebas
        run: pytest

      - name: Simulación de Deploy
        run: echo "Desplegando aplicación..."
```

---

# 🚀 Paso 9 — Subir Proyecto a GitHub

```bash
git add .

git commit -m "Primer CI/CD con Python"

git branch -M main

git push -u origin main
```

---

# 🔄 Flujo del Pipeline

```text
Push a GitHub
        ↓
GitHub Actions inicia
        ↓
Descarga el código
        ↓
Instala Python
        ↓
Instala dependencias
        ↓
Ejecuta aplicación
        ↓
Ejecuta pruebas
        ↓
Simula deployment
```

---

# 📊 Resultado Esperado

```text
✅ Build successful
✅ Tests passed
✅ Deploy completed
```

---

# 🐳 Próximo Nivel

Posteriormente se puede integrar:

- Docker
- Docker Compose
- VPS Linux
- AWS
- Azure
- Jenkins
- Kubernetes
- SonarQube
- Microservicios

---

# 👨‍🏫 Autor

Material académico para enseñanza de:

- DevOps
- Automatización
- CI/CD
- GitHub Actions
- Python
