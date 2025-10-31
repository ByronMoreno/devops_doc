# 🚀 Servicio Hola Mundo con Flask y Traefik

Este proyecto despliega un servicio **Flask "Hola Mundo"** accesible desde el subdominio  
**https://holaflask.byronrm.com**, utilizando **Docker Compose** y **Traefik** como proxy inverso.

---

## 📁 Estructura del Proyecto

```
holaflask/
├── app.py
├── Dockerfile
├── docker-compose.yml
├── Makefile
└── README.md
```

---

## 🐍 app.py

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return "¡Hola Mundo desde Flask con Traefik! 🚀"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

---

## 🐳 Dockerfile

```Dockerfile
# Imagen base oficial de Python
FROM python:3.11-slim

# Establecer directorio de trabajo
WORKDIR /app

# Copiar dependencias y código
COPY app.py /app

# Instalar Flask
RUN pip install --no-cache-dir flask

# Exponer el puerto donde corre Flask
EXPOSE 5000

# Comando por defecto
CMD ["python", "app.py"]
```

---

## ⚙️ docker-compose.yml

```yaml
version: '3.8'

services:
  holaflask:
    build: .
    container_name: holaflask
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.holaflask.entrypoints=http"
      - "traefik.http.routers.holaflask.rule=Host(`holaflask.byronrm.com`)"
      - "traefik.http.middlewares.holaflask-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.holaflask.middlewares=holaflask-https-redirect"
      - "traefik.http.routers.holaflask-secure.entrypoints=https"
      - "traefik.http.routers.holaflask-secure.rule=Host(`holaflask.byronrm.com`)"
      - "traefik.http.routers.holaflask-secure.tls=true"
      - "traefik.http.routers.holaflask-secure.tls.certresolver=http"
      - "traefik.http.routers.holaflask-secure.service=holaflask"
      - "traefik.http.services.holaflask.loadbalancer.server.port=5000"
      - "traefik.docker.network=traefik-public"
    networks:
      - traefik-public

networks:
  traefik-public:
    external: true
```

---

## 🧰 Makefile

```makefile
# Variables
SERVICE_NAME=holaflask

# Construir la imagen
build:
	docker compose build

# Levantar el servicio
up:
	docker compose up -d

# Ver logs del servicio
logs:
	docker compose logs -f $(SERVICE_NAME)

# Detener los contenedores
down:
	docker compose down

# Reconstruir completamente el servicio
rebuild: down build up

# Limpiar imágenes huérfanas
clean:
	docker system prune -f
```

---

## 🚀 Instrucciones de Despliegue

1. Clona o copia este proyecto en tu servidor:
   ```bash
   git clone https://github.com/tuusuario/holaflask.git
   cd holaflask
   ```

2. Verifica que la red `traefik-public` exista:
   ```bash
   docker network ls | grep traefik-public
   ```
   Si no existe:
   ```bash
   docker network create traefik-public
   ```

3. Despliega el contenedor Flask:
   ```bash
   make up
   ```

4. Abre tu navegador en:
   ```
   https://holaflask.byronrm.com
   ```

5. Deberías ver el mensaje:
   ```
   ¡Hola Mundo desde Flask con Traefik! 🚀
   ```

---

## 🧹 Comandos útiles del Makefile

| Comando | Descripción |
|----------|--------------|
| `make build` | Construye la imagen del contenedor |
| `make up` | Levanta el servicio en segundo plano |
| `make logs` | Muestra los logs en tiempo real |
| `make down` | Detiene y elimina los contenedores |
| `make rebuild` | Reconstruye todo el servicio |
| `make clean` | Limpia imágenes y contenedores no usados |

---

## ✅ Requisitos Previos

- Docker y Docker Compose instalados  
- Un contenedor **Traefik** en ejecución conectado a la red `traefik-public`  
- DNS configurado apuntando `holaflask.byronrm.com` al servidor

---

## 🧩 Verificación Rápida

- Ver contenedores activos:
  ```bash
  docker ps
  ```
- Ver logs de Flask:
  ```bash
  make logs
  ```
- Ver si el contenedor está conectado a la red correcta:
  ```bash
  docker network inspect traefik-public
  ```

---

## 📜 Licencia
Este proyecto puede utilizarse con fines educativos y de demostración.  
Creado con ❤️ por **Byron Moreno**.
