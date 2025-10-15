
# Curso rápido de Docker — Guía completa para clase

**Propósito:** Documento para una clase completa sobre Docker. Incluye comandos básicos, explicación de `Dockerfile`, ejemplos prácticos con la imagen `hello-world`, creación de imágenes propias, ejemplo con Nginx, `.dockerignore`, y buenas prácticas.

---

## Índice
1. Introducción rápida
2. Comandos básicos de Docker (detallado)
3. ¿Qué es un Dockerfile? — explicación y ejemplos
4. Ejecutar la imagen oficial `hello-world`
5. Crear tu propia imagen "mi-hola-mundo"
6. Ejemplo práctico: pequeña web con Nginx
7. Archivo `.dockerignore`
8. Buenas prácticas y consejos para clase
9. Resolución de problemas comunes
10. Recursos adicionales

---

## 1. Introducción rápida

Docker es una plataforma para **empaquetar, distribuir y ejecutar aplicaciones en contenedores**. Un *contenedor* es un entorno ligero y aislado que contiene todo lo necesario para ejecutar una aplicación (bibliotecas, configuración, ejecutable).

---

## 2. Comandos básicos de Docker (detallado)

### Gestión de imágenes
- `docker images` — lista imágenes locales.
- `docker pull <imagen>` — descarga una imagen (ej: `docker pull alpine:latest`).
- `docker build -t nombre:tag <ruta>` — construye una imagen desde Dockerfile.
- `docker rmi <imagen>` — elimina una imagen.
- `docker tag <imagen> <repo/imagen:tag>` — etiqueta una imagen.
- `docker push <repo/imagen:tag>` — sube la imagen a un registry.

### Gestión de contenedores
- `docker run [opciones] <imagen> [comando]` — crea y ejecuta un contenedor.
  - `-d` → ejecuta en background (detached).
  - `--rm` → elimina contenedor al terminar.
  - `-it` → interactivo + pseudo-TTY.
  - `-p host:contenedor` → mapear puertos.
  - `--name nombre` → asignar nombre.
  - `-v host:contenedor` → montar volúmenes.
- `docker ps` — muestra contenedores activos.
- `docker ps -a` — muestra todos los contenedores.
- `docker stop/start/restart <contenedor>` — detener, iniciar, reiniciar.
- `docker rm <contenedor>` — elimina contenedor detenido.
- `docker logs <contenedor>` — muestra logs.
- `docker exec -it <contenedor> <comando>` — ejecutar comando dentro del contenedor.
- `docker inspect <contenedor|imagen>` — muestra metadata detallada.
- `docker cp <origen> <destino>` — copiar archivos entre host y contenedor.

### Volúmenes y datos
- `docker volume create <nombre>` — crea un volumen.
- `docker volume ls` — lista volúmenes.
- `docker volume rm <nombre>` — elimina un volumen.

### Redes
- `docker network ls` — listar redes.
- `docker network create <nombre>` — crear red.
- `docker run --network <nombre> ...` — unir contenedor a red.

### Limpieza
- `docker system prune` — eliminar recursos no usados.
- `docker system prune -a` — elimina también imágenes no referenciadas.
- `docker volume prune` — eliminar volúmenes no usados.

---

## 3. ¿Qué es un Dockerfile?

Un **Dockerfile** es un archivo de texto con instrucciones que Docker ejecuta para **construir una imagen**. Cada instrucción crea una capa en la imagen final.

### Instrucciones comunes

- `FROM <imagen>` — establece la imagen base.
- `LABEL key="value"` — metadatos.
- `RUN <comando>` — ejecuta un comando durante la construcción.
- `COPY <origen> <destino>` — copia archivos.
- `WORKDIR <ruta>` — establece directorio de trabajo.
- `ENV KEY=value` — define variables de entorno.
- `EXPOSE <puerto>` — documenta puerto usado.
- `CMD ["ejecutable", "arg"]` — comando por defecto.
- `ENTRYPOINT ["programa"]` — define proceso principal.

### Ejemplo
```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "index.js"]
```

---

## 4. Ejecutar la imagen oficial `hello-world`

Esta imagen verifica que Docker está instalado y funcionando:

```bash
docker run --rm hello-world
```

- `--rm`: elimina el contenedor al finalizar.
- Si la imagen no está local, Docker la descargará automáticamente.

---

## 5. Crear tu propia imagen "mi-hola-mundo"

### Paso 1: Estructura mínima
```bash
mkdir mi-hola-mundo
cd mi-hola-mundo
```

### Paso 2: Dockerfile
```dockerfile
FROM alpine:latest
LABEL maintainer="Profesor <tu@correo>"
CMD ["echo", "Hola Mundo desde mi imagen Docker!"]
```

### Paso 3: Construir y ejecutar
```bash
docker build -t mi-hola-mundo:1.0 .
docker run --rm mi-hola-mundo:1.0
```

### Variante con script
`app.sh`:
```bash
#!/bin/sh
echo "Hola Mundo desde mi imagen Docker (script)!"
```
`Dockerfile`:
```dockerfile
FROM alpine:latest
COPY app.sh /app.sh
RUN chmod +x /app.sh
CMD ["/app.sh"]
```

---

## 6. Ejemplo práctico: pequeña web con Nginx

`index.html`
```html
<!doctype html>
<html>
  <body>
    <h1>Hola Mundo desde Nginx en Docker!</h1>
  </body>
</html>
```

`Dockerfile`
```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
```

### Ejecutar
```bash
docker build -t mi-hola-web:1.0 .
docker run --rm -p 8080:80 mi-hola-web:1.0
```
Abrir en el navegador: `http://localhost:8080`

---

## 7. Archivo `.dockerignore`

Evita enviar archivos innecesarios al contexto de build.

Ejemplo:
```
.git
node_modules
*.log
dist
.env
Dockerfile*
```

---

## 8. Buenas prácticas

- Usar imágenes pequeñas (`alpine`).
- Incluir `.dockerignore`.
- Evitar credenciales en Dockerfile.
- Etiquetar versiones (`:1.0`, `:1.1`).
- Usar `--rm` para ejercicios temporales.
- Usar `docker logs` y `docker exec -it` para depurar.

---

## 9. Problemas comunes

- **Permission denied** → revisar permisos (`chmod +x app.sh`).
- **Puerto ocupado** → cambiar puerto o detener servicio.
- **Imagen no encontrada** → usar `docker pull`.
- **Build lento** → agregar `.dockerignore`.
- **Contenedor se detiene** → verificar el comando `CMD`.

---

## 10. Recursos adicionales

- Documentación: https://docs.docker.com  
- Docker Hub: https://hub.docker.com  
- Tutorial oficial: https://docs.docker.com/get-started/

---
**Autor:** Guía preparada para clase completa de Docker.
