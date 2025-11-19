# 🚀 CI/CD con GitHub Actions + Docker Swarm (Deploy Limpio)

Este archivo explica el proceso **simple y estable** para desplegar servicios en un VPS usando:

- **GitHub Actions**
- **SCP para copiar archivos**
- **SSH para desplegar el stack**
- **Docker Swarm**
- **GitHub Container Registry (GHCR)**

Incluye la versión **recomendada**: eliminar siempre el stack antes de desplegar.

---

# 📦 1. Copiar stack.yml al VPS (SCP)

```yaml
- name: Copiar stack.yml al VPS (SCP)
  uses: appleboy/scp-action@v1
  with:
    host: ${{ secrets.VPS_HOST }}
    username: ${{ secrets.VPS_USER }}
    password: ${{ secrets.VPS_PASSWORD }}
    port: ${{ secrets.VPS_SSH_PORT }}
    source: "stack.yml"
    target: "/home/${{ secrets.VPS_USER }}/deploy"
```

📌 Esto envía tu archivo `stack.yml` a la carpeta `~/deploy` del servidor.

---

# ⚙️ 2. Desplegar con SSH (versión simple y robusta)

Esta versión **siempre borra el stack** antes de desplegar nuevamente, lo cual evita errores comunes en Docker Swarm.

```yaml
- name: 🚀 Deploy con eliminación del stack
  uses: appleboy/ssh-action@v1
  with:
    host: ${{ secrets.VPS_HOST }}
    username: ${{ secrets.VPS_USER }}
    password: ${{ secrets.VPS_PASSWORD }}
    port: ${{ secrets.VPS_SSH_PORT }}
    script: |
      cd ~/deploy

      # Login a GitHub Container Registry
      echo "${{ secrets.GHCR_PAT }}" | docker login ghcr.io -u byronmoreno --password-stdin

      # Descargar la nueva imagen
      docker pull ghcr.io/byronmoreno/pepito:1.0.1

      # Borrar el stack SIEMPRE (si no existe no falla)
      docker stack rm pepito || true

      # Pequeña espera mientras Swarm libera recursos
      sleep 10

      # Crear el nuevo stack limpio
      docker stack deploy --with-registry-auth -c stack.yml pepito

      # Estado final del despliegue
      docker service ls
      docker stack ps pepito
```

---

# 🎯 ¿Por qué es la mejor práctica?

- ❌ Docker Swarm a veces NO actualiza imágenes aunque hagas `docker pull`
- ❌ Las tareas viejas pueden quedar vivas
- ❌ Servicios anteriores pueden mantener conexiones o configuraciones viejas

✔️ **Eliminar el stack garantiza un deploy limpio 100% confiable**

✔️ Es ideal para CI/CD automatizado.

---

# 🔐 Secrets necesarios en GitHub

Agrega estos en **Settings → Secrets → Actions**:

| Nombre | Descripción |
|--------|-------------|
| `VPS_HOST` | IP del servidor |
| `VPS_USER` | Usuario SSH |
| `VPS_PASSWORD` | Contraseña del VPS |
| `VPS_SSH_PORT` | Puerto SSH |
| `GHCR_PAT` | Token personal para descargar imágenes desde GHCR |

---

# 🎉 ¡Deploy automático completado!

Cada vez que hagas `git push` a la rama `main`, GitHub Actions:

1. Construye la imagen Docker  
2. La sube a GHCR  
3. Copia `stack.yml` al VPS  
4. Borra el stack  
5. Despliega desde cero  
6. Muestra el estado final  

🚀 **CI/CD real, limpio y profesional.**  
