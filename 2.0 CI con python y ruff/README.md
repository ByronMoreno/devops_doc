# CI con GitHub Actions + Python + Pytest + Ruff

# Objetivo del tema

Comprender e implementar un proceso básico de Integración Continua (CI) utilizando GitHub Actions, Python, Pytest y Ruff, automatizando pruebas y validaciones de calidad de código en proyectos de software.

---

# Objetivos de la clase

Al finalizar esta práctica, el estudiante será capaz de:

1. Comprender qué es CI (Continuous Integration).
2. Crear un proyecto básico en Python.
3. Ejecutar pruebas automáticas con `pytest`.
4. Validar calidad de código usando `ruff`.
5. Configurar un pipeline automático con GitHub Actions.
6. Automatizar validaciones cada vez que se haga `push` o `pull request`.

---

# ¿Qué es CI?

La **Integración Continua (CI)** es una práctica de desarrollo donde cada cambio en el código se valida automáticamente mediante:

- Compilación
- Pruebas
- Validaciones
- Análisis de calidad

Esto permite detectar errores rápidamente.

---

# Tecnologías usadas

| Herramienta | Descripción |
|---|---|
| Python | Lenguaje de programación |
| Pytest | Framework de pruebas |
| Ruff | Linter y analizador de código |
| GitHub Actions | Automatización CI/CD |
| Git | Control de versiones |

---

# ¿Qué es Ruff?

Ruff es una herramienta moderna para Python que funciona como:

- Linter
- Formatter
- Analizador de calidad de código

Ayuda a detectar errores, malas prácticas y problemas de estilo en el código Python.

---

# ¿Qué detecta Ruff?

## Variables no usadas

```python
x = 10
```

---

## Imports innecesarios

```python
import math
```

---

## Problemas de formato

```python
x=1
```

Ruff recomienda:

```python
x = 1
```

---

## Código poco recomendado

```python
if x == True:
```

Ruff recomienda:

```python
if x:
```

---

# Comandos importantes de Ruff

## Revisar proyecto

```bash
ruff check .
```

---

## Corregir automáticamente

```bash
ruff check . --fix
```

---

## Formatear código

```bash
ruff format .
```

---

# Comparación rápida

| Herramienta | Función |
|---|---|
| Pytest | Validar funcionamiento |
| Ruff | Validar calidad de código |
| GitHub Actions | Automatizar procesos |

---

# Estructura del proyecto

```bash
mi-proyecto-ci/
│
├── app/
│   └── calculadora.py
│
├── tests/
│   └── test_calculadora.py
│
├── requirements.txt
│
└── .github/
    └── workflows/
        └── ci.yml
```

---

# Paso 1: Crear el proyecto

```bash
mkdir mi-proyecto-ci
cd mi-proyecto-ci

mkdir app
mkdir tests
mkdir .github
mkdir .github/workflows
```

---

# Paso 2: Crear código Python

## Archivo: `app/calculadora.py`

```python
def sumar(a, b):
    return a + b


def restar(a, b):
    return a - b
```

---

# Paso 3: Crear pruebas con Pytest

## Archivo: `tests/test_calculadora.py`

```python
from app.calculadora import sumar, restar


def test_sumar():
    assert sumar(2, 3) == 5


def test_restar():
    assert restar(10, 5) == 5
```

---

# Paso 4: Crear archivo requirements.txt

```txt
pytest
ruff
```

---

# Paso 5: Instalar dependencias

```bash
pip install -r requirements.txt
```

---

# Paso 6: Ejecutar pruebas

```bash
pytest
```

Resultado esperado:

```bash
2 passed
```

---

# Paso 7: Ejecutar Ruff

```bash
ruff check .
```

Resultado esperado:

```bash
All checks passed!
```

---

# Paso 8: Crear GitHub Actions

## Archivo: `.github/workflows/ci.yml`

```yaml
name: Python CI

on:
  push:
    branches:
      - main
      - byron

  pull_request:
    branches:
      - main
      - byron

jobs:
  build:

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
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Ejecutar Ruff
        run: |
          ruff check .

      - name: Ejecutar pruebas
        run: |
          pytest
```

---

# Paso 9: Inicializar Git

```bash
git init
```

---

# Paso 10: Crear repositorio en GitHub

```bash
mi-proyecto-ci
```

---

# Paso 11: Conectar repositorio

```bash
git remote add origin TU_URL
```

Ejemplo:

```bash
git remote add origin https://github.com/usuario/mi-proyecto-ci.git
```

---

# Paso 12: Crear rama

## Rama principal

```bash
git branch -M main
```

## Rama byron

```bash
git branch -M byron
```

---

# Paso 13: Subir cambios

```bash
git add .
git commit -m "Primer CI con GitHub Actions"
git push -u origin byron
```

---

# Paso 14: Ver GitHub Actions

Ir a:

```text
GitHub → Actions
```

---

# Flujo CI

```text
Push a GitHub
        ↓
GitHub Actions inicia
        ↓
Instala Python
        ↓
Instala dependencias
        ↓
Ejecuta Ruff
        ↓
Ejecuta Pytest
        ↓
Resultado OK o ERROR
```

---

# Ejercicios para estudiantes

## Actividad 1

Agregar función:

```python
def multiplicar(a, b):
    return a * b
```

Crear prueba correspondiente.

---

## Actividad 2

Provocar error:

```python
assert sumar(2, 2) == 5
```

---

## Actividad 3

Agregar error Ruff:

```python
x=1
```

---

# Conceptos importantes

| Concepto | Significado |
|---|---|
| CI | Integración Continua |
| Workflow | Flujo automatizado |
| Job | Tarea |
| Step | Paso |
| Runner | Máquina virtual |
| Pytest | Framework de pruebas |
| Ruff | Linter de Python |

---

# Comandos importantes

## Ejecutar pruebas

```bash
pytest
```

---

## Ejecutar Ruff

```bash
ruff check .
```

---

## Corregir errores automáticamente

```bash
ruff check . --fix
```

---

## Formatear código

```bash
ruff format .
```

---

## Ver ramas

```bash
git branch
```

---

## Cambiar rama

```bash
git checkout byron
```

---

# Buenas prácticas

- Hacer commits pequeños
- Ejecutar pruebas antes del push
- Mantener código limpio
- Automatizar validaciones
- Usar ramas de desarrollo

---

# Resultado esperado

El estudiante tendrá:

✅ Proyecto Python funcional  
✅ Pruebas automáticas  
✅ Validación de calidad  
✅ Pipeline CI automatizado  
✅ Conocimiento básico profesional de CI/CD  

---

# Próximo nivel

1. Coverage
2. Docker + Actions
3. Deploy automático
4. DockerHub
5. SonarQube
6. CD (Continuous Deployment)
7. Seguridad con Bandit
8. Testing avanzado

---

# Recursos oficiales

- Python: https://www.python.org
- Pytest: https://docs.pytest.org
- Ruff: https://docs.astral.sh/ruff/
- GitHub Actions: https://docs.github.com/actions
- Git: https://git-scm.com
