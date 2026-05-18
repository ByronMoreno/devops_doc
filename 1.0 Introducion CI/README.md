# ⚡ GitHub Actions
## Automatización CI/CD Moderna

![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-Automatización-blue?logo=githubactions)
![DevOps](https://img.shields.io/badge/DevOps-CI%2FCD-success)
![Automation](https://img.shields.io/badge/Automation-Workflows-orange)

---

# 📘 ¿Qué es GitHub Actions?

GitHub Actions es una herramienta de automatización integrada en GitHub que permite ejecutar tareas automáticamente cuando ocurren eventos dentro de un repositorio.

Se utiliza principalmente para:

- CI (Continuous Integration)
- CD (Continuous Deployment)
- Automatización de procesos
- Testing automático
- Compilación automática
- Despliegues automáticos

---

# 🧠 Concepto General

GitHub Actions funciona mediante:

Evento → Workflow → Jobs → Steps

---

# 🔄 Funcionamiento General

Developer realiza cambios
            ↓
Hace git push
            ↓
GitHub detecta evento
            ↓
GitHub Actions inicia workflow
            ↓
Ejecuta tareas automáticas
            ↓
Genera resultados

---

# 🚀 ¿Qué se puede automatizar?

Con GitHub Actions se puede automatizar:

✅ Compilación de proyectos  
✅ Ejecución de pruebas  
✅ Análisis de código  
✅ Construcción de imágenes Docker  
✅ Despliegue a servidores  
✅ Publicación de aplicaciones  
✅ Notificaciones automáticas  
✅ Integración con la nube  

---

# 📂 Ubicación de los Workflows

Los workflows se almacenan dentro de:

.github/workflows/

Ejemplo:

.github/workflows/ci-cd.yml

---

# 📄 ¿Qué es un Workflow?

Un workflow es un archivo YAML que define tareas automatizadas.

Ejemplo:

```yaml
name: Mi Workflow

on:
  push:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Hola Mundo
        run: echo "Hola Mundo"
```

---

# ⚙️ Componentes de GitHub Actions

## 🔹 Workflow

Archivo principal de automatización.

## 🔹 Event

Evento que activa el workflow.

## 🔹 Job

Conjunto de tareas.

## 🔹 Step

Paso individual dentro de un Job.

## 🔹 Runner

Máquina virtual que ejecuta el workflow.

---

# 📌 Eventos Más Utilizados

| Evento | Descripción |
|---|---|
| push | Cuando se sube código |
| pull_request | Cuando se crea PR |
| release | Cuando se publica release |
| workflow_dispatch | Ejecución manual |
| schedule | Ejecución programada |

---

# 🧪 Ejemplo Básico

## 📄 hola.yml

```yaml
name: Hola Mundo

on:
  push:

jobs:

  saludo:

    runs-on: ubuntu-latest

    steps:

      - name: Mostrar mensaje
        run: echo "Hola Mundo desde GitHub Actions"
```

---

# 🚀 Flujo Real de CI/CD

Desarrollador
      ↓
Git Push
      ↓
GitHub
      ↓
GitHub Actions
      ↓
Tests
      ↓
Build
      ↓
Deploy
      ↓
Producción

---

# 🐳 Integración con Docker

Ejemplo:

```yaml
- name: Construir Docker
  run: docker build -t miapp .
```

---

# ☁️ Integración con la Nube

GitHub Actions puede integrarse con:

- AWS
- Azure
- Google Cloud
- Kubernetes
- VPS Linux

---

# 🔒 Ventajas de GitHub Actions

✅ Integrado en GitHub  
✅ Fácil de usar  
✅ Compatible con múltiples lenguajes  
✅ Automatización completa  
✅ Integración con Docker y Cloud  

---

# 📚 Extensiones y Marketplace

https://github.com/marketplace?type=actions

Ejemplo:

```yaml
uses: actions/checkout@v4
```

---

# 📊 GitHub Actions vs Jenkins

| Característica | GitHub Actions | Jenkins |
|---|---|---|
| Integración GitHub | Excelente | Manual |
| Facilidad | Alta | Media |
| Configuración | YAML | Plugins |

---

# 🎯 Objetivos Académicos

✅ Comprender CI/CD  
✅ Automatizar workflows  
✅ Integrar proyectos con GitHub  
✅ Ejecutar pruebas automáticas  
✅ Desplegar aplicaciones automáticamente  

---

# 📖 Recursos Oficiales

- https://docs.github.com/actions
- https://github.com/features/actions

---

# 🏁 Conclusión

GitHub Actions es una de las herramientas más utilizadas actualmente para automatizar procesos DevOps y CI/CD.

Permite construir pipelines modernos y profesionales directamente desde GitHub.

---
