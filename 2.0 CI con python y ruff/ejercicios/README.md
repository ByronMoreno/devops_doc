# CI con GitHub Actions + Python + Pytest + Ruff

## Objetivos de la clase

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

## Crear carpetas

```bash
mkdir mi-proyecto-ci
cd mi-proyecto-ci

mkdir app
mkdir tests
mkdir .github
mkdir .github/workflows
```

---

# Paso 2: Crear el código Python

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

## Archivo: `requirements.txt`

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

# Paso 6: Ejecutar pruebas manualmente

## Ejecutar Pytest

```bash
pytest
```

Resultado esperado:

```bash
2 passed
```

---

# Paso 7: Ejecutar Ruff

## Validar calidad de código

```bash
ruff check .
```

Si no hay errores:

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

Crear un repositorio llamado:

```bash
mi-proyecto-ci
```

---

# Paso 11: Conectar repositorio local

```bash
git remote add origin TU_URL
```

Ejemplo:

```bash
git remote add origin https://github.com/usuario/mi-proyecto-ci.git
```

---

# Paso 12: Subir proyecto

## Crear rama principal

```bash
git branch -M main
```

## O usar tu rama byron

```bash
git branch -M byron
```

---

## Subir cambios

```bash
git add .
git commit -m "Primer CI con GitHub Actions"
git push -u origin byron
```

---

# Paso 13: Ver GitHub Actions

Ir a:

```text
GitHub → Actions
```

Ahí se verá:

- Ruff ejecutándose
- Pytest ejecutándose
- Estado del pipeline

---

# Explicación del flujo CI

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

# Ejercicio para estudiantes

## Actividad 1

Agregar una nueva función:

```python
def multiplicar(a, b):
    return a * b
```

Crear su prueba correspondiente.

---

## Actividad 2

Provocar un error intencional:

```python
assert sumar(2, 2) == 5
```

Observar cómo falla el pipeline.

---

## Actividad 3

Agregar validaciones Ruff incorrectas:

```python
x=1
```

Verificar cómo Ruff detecta problemas de estilo.

---

# Conceptos importantes

| Concepto | Significado |
|---|---|
| CI | Integración Continua |
| Workflow | Flujo automatizado |
| Job | Tarea dentro del workflow |
| Step | Paso individual |
| Runner | Máquina que ejecuta el workflow |
| Pytest | Framework de pruebas |
| Ruff | Linter de Python |

---

# Comandos importantes

## Ejecutar pruebas

```bash
pytest
```

## Ejecutar Ruff

```bash
ruff check .
```

## Ver ramas

```bash
git branch
```

## Cambiar rama

```bash
git checkout byron
```

---

# Buenas prácticas CI

- Hacer commits pequeños
- Ejecutar pruebas antes del push
- Mantener código limpio
- Automatizar validaciones
- Usar ramas para desarrollo

---

# Resultado esperado

El estudiante tendrá:

✅ Proyecto Python funcional  
✅ Pruebas automáticas  
✅ Validación de calidad  
✅ Pipeline CI en GitHub Actions  
✅ Automatización básica profesional  

---

# Próximo nivel (continuación)

Después de esta clase puedes enseñar:

1. Coverage
2. Docker + Actions
3. Deploy automático
4. Publicación en DockerHub
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
