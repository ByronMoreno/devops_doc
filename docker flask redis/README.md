# Clase práctica: Despliegue de Flask + Redis con Docker Swarm y Traefik

**Resumen:** en esta clase los estudiantes construirán, empaquetarán y desplegarán un microservicio *Flask* que utiliza *Redis* como almacén simple — todo orquestado con **Docker Swarm** y expuesto mediante **Traefik** en el subdominio `holaflask.byronrm.com`. El servicio está pensado para entornos con recursos limitados; las configuraciones incluyen límites de CPU y memoria para evitar saturar el VPS.

---

## Objetivo de la clase
Que los estudiantes comprendan y practiquen el ciclo completo de despliegue de una aplicación web ligera con Docker Swarm y Traefik, incluyendo:
- Contenerizar una aplicación Flask.
- Conectar la app a Redis como datastore ligero.
- Crear y desplegar un `stack.yml` para Docker Swarm con integración a Traefik.
- Verificar, depurar y optimizar despliegues en un VPS con recursos limitados (4 GB RAM / 2 vCPU).

### Resultado de aprendizaje
Al finalizar la sesión, el estudiante será capaz de:
1. Explicar la arquitectura básica: contenedor app ↔ Redis ↔ Traefik.  
2. Construir la imagen Docker de la aplicación Flask.  
3. Desplegar y gestionar un stack en Docker Swarm usando `docker stack deploy`.  
4. Verificar la conectividad, revisar logs y aplicar ajustes para reducir consumo de recursos.

---

## Duración y estructura (sugerida)
- Duración total: 90 minutos (1 sesión práctica)
  - 10 min: Introducción y objetivos
  - 15 min: Repaso rápido de Docker, Swarm y Traefik (teoría mínima)
  - 30 min: Laboratorio — construir imagen y desplegar stack
  - 20 min: Pruebas, ver logs y debugging
  - 15 min: Recomendaciones para optimización y cierre / preguntas

---

## Archivo: `app.py` (Flask + Redis)

```python
import os
from flask import Flask, jsonify
import redis
import time

REDIS_HOST = os.environ.get("REDIS_HOST", "redis")
REDIS_PORT = int(os.environ.get("REDIS_PORT", 6379))
REDIS_DB = int(os.environ.get("REDIS_DB", 0))
REDIS_PASSWORD = os.environ.get("REDIS_PASSWORD", None)

def make_redis_client():
    pool = redis.ConnectionPool(
        host=REDIS_HOST,
        port=REDIS_PORT,
        db=REDIS_DB,
        password=REDIS_PASSWORD,
        socket_connect_timeout=5,
        socket_timeout=5,
    )
    return redis.Redis(connection_pool=pool, decode_responses=True)

app = Flask(__name__)
r = make_redis_client()

@app.route("/")
def index():
    try:
        value = r.incr("visitas")
        return jsonify({
            "message": "¡Hola Mundo desde Flask + Redis! 🚀",
            "visitas": int(value),
            "redis_host": REDIS_HOST,
            "redis_port": REDIS_PORT
        })
    except redis.RedisError as e:
        return jsonify({
            "error": "No se pudo conectar a Redis",
            "details": str(e)
        }), 500

@app.route("/reset")
def reset():
    try:
        r.set("visitas", 0)
        return jsonify({"message": "contador reiniciado"})
    except redis.RedisError as e:
        return jsonify({"error": "No se pudo conectar a Redis", "details": str(e)}), 500

if __name__ == "__main__":
    try:
        r.exists("visitas") or r.set("visitas", 0)
    except Exception:
        pass
    app.run(host="0.0.0.0", port=5000)
```

---

## Archivo: `Dockerfile`

```Dockerfile
FROM python:3.11-slim

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app
COPY app.py /app

RUN apt-get update \
    && apt-get install -y --no-install-recommends gcc libssl-dev \
    && pip install --no-cache-dir flask redis \
    && apt-get purge -y --auto-remove gcc \
    && rm -rf /var/lib/apt/lists/*

EXPOSE 5000
CMD ["python", "app.py"]
```

---

## Archivo: `Makefile`

```makefile
STACK_NAME=holaflask
IMAGE_NAME=holaflask:latest
STACK_FILE=stack.yml

build:
	docker build -t $(IMAGE_NAME) .

deploy: build
	docker stack deploy -c $(STACK_FILE) $(STACK_NAME)

up: deploy

ps:
	docker stack services $(STACK_NAME)

tasks:
	docker service ps $(STACK_NAME)_holaflask

logs:
	docker service logs -f $(STACK_NAME)_holaflask

down:
	docker stack rm $(STACK_NAME)

clean:
	docker rmi $(IMAGE_NAME) || true

.PHONY: build deploy up ps tasks logs down clean
```

---

## Archivo: `stack.yml` (con integración Traefik)

> Nota: Traefik ya debe estar desplegado y conectado a la red `traefik-public`. Este `stack.yml` asume que Traefik maneja certificados (certresolver configurado).

```yaml
version: "3.8"

services:
  holaflask:
    image: holaflask:latest
    deploy:
      replicas: 1
      restart_policy:
        condition: any
      resources:
        limits:
          cpus: "0.50"
          memory: 512M
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
    environment:
      - REDIS_HOST=redis
      - REDIS_PORT=6379
    networks:
      - appnet
      - traefik-public

  redis:
    image: redis:7-alpine
    command: ["redis-server", "--save", "60", "1", "--appendonly", "no"]
    volumes:
      - redis_data:/data
    deploy:
      replicas: 1
      restart_policy:
        condition: any
      resources:
        limits:
          cpus: "0.25"
          memory: 256M
    networks:
      - appnet

volumes:
  redis_data:

networks:
  appnet:
    driver: overlay

  traefik-public:
    external: true
```

---

## Plan de actividades (laboratorio paso a paso)

1. **Preparación del entorno (5 min)**  
   - Asegúrate de que el servidor tenga Docker y Docker Engine (con Swarm) instalados.  
   - Traefik ya debe estar desplegado y conectado a la red `traefik-public`.  
   - Verificar recursos disponibles: `free -h` y `docker node ls`.

2. **Clonar código / copiar ficheros (5 min)**  
   - Copia `app.py`, `Dockerfile`, `stack.yml` y `Makefile` en una carpeta `holaflask/` en el VPS o en tu máquina local (si vas a desplegar ahí).

3. **Build de la imagen (5–8 min)**  
   - Ejecuta: `make build` o `docker build -t holaflask:latest .`

4. **Inicializar Swarm (si no está activo) (2 min)**  
   - `docker swarm init`

5. **Crear red overlay para la app (si no existe) (1 min)**  
   - `docker network create --driver=overlay appnet` (si el instructor la quiere crear explícitamente).

6. **Desplegar stack (3 min)**  
   - `make deploy` o `docker stack deploy -c stack.yml holaflask`

7. **Verificar servicios (3–5 min)**  
   - `docker stack services holaflask`  
   - `docker service ps holaflask_holaflask`

8. **Probar el servicio desde un navegador o curl (5 min)**  
   - `curl http://IP_DEL_SERVIDOR:5000/` (si usas puerto directo) o `https://holaflask.byronrm.com` si Traefik y DNS están configurados.

9. **Depuración / logs (10 min)**  
   - `make logs` para ver logs en tiempo real.  
   - `docker service ps <servicio>` para ver fallos en las tareas.  
   - Revisar `docker node ps` y `docker node ls` si hay problemas de scheduling.

10. **Optimización (5–10 min)**  
    - Ajustar `memory` y `cpus` en `stack.yml` si el nodo se queda sin recursos.  
    - Habilitar swap en el VPS si es necesario (no recomendado para producción).  
    - Si Redis consume mucho, usar `maxmemory` y políticas de expiración según el caso.

---

## Material de evaluación (sugerido)
- **Tarea corta (para entregar):** despliega el stack en una pequeña VM local o en el VPS del curso; enviar captura de `docker stack services` y `curl` exitoso.  
- **Pregunta técnica:** explicar por qué `mode: host` en `ports` cambia el comportamiento del balanceo en Swarm.  
- **Actividad extra (opcional):** modificar la app para guardar además la IP cliente y mostrar las últimas 5 IPs (usar lista en Redis).

---

## Troubleshooting (problemas comunes y soluciones rápidas)

- **502 Bad Gateway en Traefik:** verificar que el servicio tenga las labels correctamente y que `traefik.docker.network` apunte a la red donde Traefik está presente (`traefik-public`). Revisar logs de Traefik.  
- **Redis no accesible:** asegurarse que ambos servicios estén en la misma red overlay (`appnet`). Comprobar `docker service logs holaflask_redis`.  
- **OOM / servicio reiniciándose:** bajar `memory` o bajar la carga (réplicas = 1); agregar swap como mitigación.  
- **Imagen no encontrada al deploy:** recuerda ejecutar `docker build -t holaflask:latest .` antes de `docker stack deploy` (Swarm no soporta build en stack deploy).

---

## Requisitos previos para los estudiantes
- Conocimientos básicos de Linux (línea de comandos).  
- Conceptos básicos de Docker (images, containers).  
- Si la práctica se hace en el servidor del instructor, cada estudiante puede realizar el despliegue en su propia VM local para evitar colapsar el VPS.

---

## Recursos y lectura adicional
- Documentación Docker Swarm: https://docs.docker.com/engine/swarm/  
- Traefik docs: https://doc.traefik.io/traefik/  
- Redis: https://redis.io/  
- Flask: https://flask.palletsprojects.com/

---

## Notas finales para el docente
- Puedes adaptar el `stack.yml` para desplegar en modo `ingress` si quieres practicar balanceo entre réplicas (quita `mode: host` y usa `published` en modo por defecto).  
- Para un curso con muchos estudiantes, mi recomendación es usar DB gestionada o pedir a cada estudiante trabajar con SQLite local para prácticas individuales.

---

**Autor:** Byron Moreno — Material preparado para uso docente y laboratorio.
