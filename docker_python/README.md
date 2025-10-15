# 🚀 Despliegue de un programa *Hola Mundo* en Python con Docker

Este documento guía paso a paso cómo crear y ejecutar un contenedor Docker que muestre el mensaje **"Hola mundo"** utilizando **Python**.

---

## 🧩 1. ¿Qué vamos a hacer?
Crearemos un pequeño programa en Python (`app.py`) que imprime `"Hola mundo"` y lo ejecutaremos dentro de un contenedor Docker.

---

## 🧱 2. Crear el archivo `app.py`

Primero, creamos un archivo llamado **`app.py`** con el siguiente contenido:

```python
# app.py
print("Hola mundo desde Docker 🐳")
```

Este programa simplemente imprimirá el texto cuando sea ejecutado.

---

## 🐋 3. Crear el archivo `Dockerfile`

Ahora crearemos un archivo llamado **`Dockerfile`** (sin extensión) en el mismo directorio.

Este archivo le indica a Docker **cómo construir la imagen**.

```dockerfile
# Utilizamos una imagen base oficial de Python
FROM python:3.10-slim

# Establecemos el directorio de trabajo dentro del contenedor
WORKDIR /app

# Copiamos el archivo app.py al contenedor
COPY app.py /app

# Definimos el comando que se ejecutará al iniciar el contenedor
CMD ["python", "app.py"]
```

🔹 **Explicación línea por línea:**
- `FROM python:3.10-slim` → Usa una versión ligera de Python 3.10.  
- `WORKDIR /app` → Crea (si no existe) y entra en el directorio `/app`.  
- `COPY app.py /app` → Copia nuestro archivo al contenedor.  
- `CMD ["python", "app.py"]` → Ejecuta el programa cuando se inicie el contenedor.

---

## ⚙️ 4. Construir la imagen Docker

Ejecutamos el siguiente comando en la terminal (dentro del directorio donde están `Dockerfile` y `app.py`):

```bash
docker build -t hola-mundo-python .
```

📦 **Explicación:**
- `docker build` → Crea una imagen Docker.
- `-t hola-mundo-python` → Asigna el nombre `hola-mundo-python` a la imagen.
- `.` → Indica que el Dockerfile está en el directorio actual.

Durante este proceso, Docker descargará la imagen base de Python (solo la primera vez).

---

## ▶️ 5. Ejecutar el contenedor

Una vez construida la imagen, ejecutamos el contenedor con:

```bash
docker run --rm hola-mundo-python
```

🖥️ **Salida esperada:**

```text
Hola mundo desde Docker 🐳
```

🔹 **Explicación:**
- `docker run` → Inicia un nuevo contenedor.
- `--rm` → Elimina el contenedor automáticamente al finalizar.
- `hola-mundo-python` → Nombre de la imagen a ejecutar.

---

## 📋 6. Verificar imágenes y contenedores

Puedes listar tus imágenes creadas con:

```bash
docker images
```

Y tus contenedores en ejecución (si los hay) con:

```bash
docker ps
```

Para ver también los contenedores detenidos:

```bash
docker ps -a
```

---

## 🧹 7. Eliminar imágenes o contenedores (opcional)

Si quieres limpiar espacio puedes eliminar contenedores o imágenes viejas:

```bash
docker rm <id_contenedor>
docker rmi <nombre_imagen>
```

---

## 🧠 8. Resumen visual del flujo

```
+-----------------------------+
| Archivo app.py              |
| print("Hola mundo")         |
+-------------+---------------+
              |
              ▼
+-----------------------------+
| Dockerfile                  |
| FROM python:3.10-slim       |
| COPY app.py /app            |
| CMD ["python", "app.py"]    |
+-------------+---------------+
              |
              ▼
+-----------------------------+
| docker build -t hola-mundo  |
| docker run hola-mundo       |
+-----------------------------+
              |
              ▼
       💬 Hola mundo 🐳
```

---

## 🎓 9. Ejercicio para los estudiantes

1. Modifica el código de `app.py` para imprimir tu **nombre completo**.  
2. Cambia el nombre de la imagen a `hola-tu-nombre`.  
3. Ejecuta tu contenedor y comprueba la salida.  
4. Usa el comando `docker ps -a` para verificar que se creó correctamente.  

---

✅ **Con esto tus estudiantes habrán creado, construido y ejecutado su primer contenedor Docker con Python.**

---

**Autor:** Profesor Byron Moreno  
**Materia:** DevOps / Contenedores con Docker  
