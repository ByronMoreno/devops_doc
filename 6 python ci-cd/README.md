# 🧩 Clase: Pipelines de CI/CD con GitHub Actions y Docker

## 🎯 Objetivo de la clase
Comprender los conceptos fundamentales de **Integración Continua (CI)** y **Despliegue Continuo (CD)**, y aplicar estos conocimientos mediante la configuración de un **workflow automatizado** en **GitHub Actions** que construye, prueba y publica una imagen Docker en el **GitHub Container Registry (GHCR)**.

---

## 🧠 1. Introducción a CI/CD

### 🔹 ¿Qué es la Integración Continua (CI)?
La **Integración Continua** es una práctica de desarrollo de software donde los desarrolladores integran frecuentemente su código en un repositorio compartido.  
Cada integración se verifica automáticamente mediante pruebas y análisis estáticos del código.

**Objetivo:** detectar errores lo antes posible y mantener el proyecto en un estado funcional.

### 🔹 ¿Qué es el Despliegue Continuo (CD)?
El **Despliegue Continuo** automatiza el proceso posterior: desde que el código pasa las pruebas, hasta que se despliega en un entorno de staging o producción.

**Objetivo:** entregar software de calidad más rápido, confiable y sin intervención manual.

---

## ⚙️ 2. Beneficios de la automatización

| Beneficio | Descripción |
|------------|--------------|
| 🚀 **Velocidad** | Los cambios llegan más rápido a producción. |
| 🧩 **Calidad** | Se ejecutan pruebas y revisiones automáticas. |
| 🔁 **Consistencia** | Cada despliegue sigue el mismo proceso. |
| 🧱 **Confiabilidad** | Menos errores humanos en integración y despliegue. |
| 📈 **Escalabilidad** | Los pipelines crecen junto con el proyecto. |

---

## 🧰 3. GitHub Actions: herramienta de CI/CD

**GitHub Actions** permite automatizar tareas directamente desde un repositorio de GitHub.  
Se define un archivo `.yml` en la carpeta `.github/workflows/` con los pasos del pipeline.

📘 Documentación oficial:  
👉 [docs.github.com/en/actions](https://docs.github.com/en/actions)

---

## 🧪 4. Ejemplo práctico: Proyecto Python con Docker

### 🧩 Estructura del proyecto

```
my-python-app/
│
├── app.py
├── requirements.txt
├── Dockerfile
└── .github/
    └── workflows/
        └── ci.yml
```

---

## 🧱 5. Dockerfile

```dockerfile
FROM python:3.10-slim

WORKDIR /app
COPY . /app

RUN pip install -r requirements.txt

CMD ["python", "app.py"]
```

---

## 🧩 6. Workflow de CI/CD (`.github/workflows/ci.yml`)

Este pipeline ejecuta pruebas, verifica la calidad del código y publica la imagen Docker en el **GitHub Container Registry**.

```yaml
name: CI/CD Pipeline

on:
  push:
    branches:
      - main
  pull_request:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: 🧾 Checkout repository
      uses: actions/checkout@v3

    - name: 🐍 Set up Python
      uses: actions/setup-python@v5
      with:
        python-version: '3.10'

    - name: 📦 Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt

    - name: 🧪 Run tests
      run: pytest

    - name: 🧹 Run lint
      run: flake8 .

    - name: 🔐 Login to GitHub Container Registry
      uses: docker/login-action@v3
      with:
        registry: ghcr.io
        username: ${{ github.actor }}
        password: ${{ secrets.GITHUB_TOKEN }}

    - name: 🏗️ Build and push Docker image
      uses: docker/build-push-action@v6
      with:
        push: true
        tags: ghcr.io/${{ github.repository }}:latest
```

📦 Recursos usados:
- 🔗 [docker/login-action](https://github.com/docker/login-action)
- 🔗 [docker/build-push-action](https://github.com/docker/build-push-action)

---

## 🧰 7. Variables y secretos

El **token** `${{ secrets.GITHUB_TOKEN }}` es generado automáticamente por GitHub y permite publicar la imagen en el **GitHub Container Registry (GHCR)** sin credenciales adicionales.

### Configuración adicional (si se usa otro registro):
1. Ve a **Settings → Secrets and variables → Actions**.  
2. Crea los secretos:
   - `DOCKER_USERNAME`
   - `DOCKER_PASSWORD`

Y luego reemplaza en el login:

```yaml
username: ${{ secrets.DOCKER_USERNAME }}
password: ${{ secrets.DOCKER_PASSWORD }}
```

---

## 🧩 8. Resultado del pipeline

Al ejecutar el pipeline:

1. Se instala el entorno Python.  
2. Se ejecutan pruebas y análisis de código.  
3. Se crea la imagen Docker.  
4. Se publica automáticamente en el registro de contenedores (`ghcr.io`).

Puedes ver el resultado en la pestaña **Actions** del repositorio.

---

## 🧭 9. Mejores prácticas de CI/CD

✅ Mantén los tests actualizados.  
✅ Usa nombres descriptivos para los jobs y pasos.  
✅ Versiona las imágenes Docker (`:v1.0.0`, `:latest`).  
✅ Automatiza la ejecución con *branches* protegidas.  
✅ Usa *linting* (flake8, pylint, black) para mantener la calidad del código.

---

## 🚀 10. Conclusión

El uso de **GitHub Actions** junto con **Docker** permite:
- Automatizar el ciclo completo de integración y despliegue.
- Garantizar calidad mediante pruebas automáticas.
- Publicar contenedores listos para cualquier entorno.

La adopción de **CI/CD** no solo mejora la productividad, sino que también eleva el estándar profesional del desarrollo de software.

---

## 📚 Referencias

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Docker Login Action](https://github.com/docker/login-action)
- [Docker Build Push Action](https://github.com/docker/build-push-action)
