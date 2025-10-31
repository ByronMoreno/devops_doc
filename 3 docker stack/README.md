# 🧩 Clase: Despliegue de Servicios con Makefile y stack.yml

## 🎯 Objetivo de la clase
Comprender y aplicar el uso de los archivos **Makefile** y **stack.yml** para automatizar el despliegue de servicios utilizando **Docker Stack**, implementando una base de datos **PostgreSQL** y una herramienta de administración **pgAdmin**.

---

## 📘 Introducción

Cuando trabajamos con contenedores Docker, normalmente usamos comandos como `docker run` o `docker-compose`.  
Sin embargo, cuando queremos **automatizar despliegues en entornos más avanzados**, como **Docker Swarm**, utilizamos:

- 🧱 **stack.yml** → Define los servicios (contenedores) a desplegar.  
- ⚙️ **Makefile** → Permite automatizar tareas (por ejemplo, construir imágenes o desplegar el stack).

Ambos archivos juntos permiten una forma **profesional, reproducible y automatizada** de gestionar despliegues.

---

## 🗂️ Estructura del proyecto

```
proyecto-db/
│
├── Makefile
├── stack.yml
└── data/             # (opcional) Volumen para PostgreSQL
```

---

## 🐘 1. Archivo `stack.yml`

```yaml
version: "3.8"

services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: admin123
      POSTGRES_DB: ejemplo_db
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - db_net
    deploy:
      replicas: 1
      restart_policy:
        condition: on-failure

  pgadmin:
    image: dpage/pgadmin4:latest
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@admin.com
      PGADMIN_DEFAULT_PASSWORD: admin123
    ports:
      - "5050:80"
    depends_on:
      - postgres
    networks:
      - db_net
    deploy:
      replicas: 1
      restart_policy:
        condition: on-failure

volumes:
  postgres_data:

networks:
  db_net:
```

---

## ⚙️ 2. Archivo `Makefile`

```makefile
STACK_NAME=db_stack

deploy:
	docker stack deploy -c stack.yml $(STACK_NAME)

ps:
	docker stack ps $(STACK_NAME)

logs:
	docker service logs $(STACK_NAME)_postgres -f

rm:
	docker stack rm $(STACK_NAME)

networks:
	docker network ls

volumes:
	docker volume ls
```

---

## 🚀 3. Ejecución paso a paso

1. **Inicializa Docker Swarm (solo la primera vez):**
   ```bash
   docker swarm init
   ```

2. **Despliega los servicios:**
   ```bash
   make deploy
   ```

3. **Verifica que los servicios estén activos:**
   ```bash
   make ps
   ```

4. **Abre pgAdmin en el navegador:**
   ```
   http://localhost:5050
   ```
   - Usuario: `admin@admin.com`
   - Clave: `admin123`

5. **Conéctate a PostgreSQL desde pgAdmin:**
   - Host: `postgres`
   - Usuario: `admin`
   - Clave: `admin123`
   - Base de datos: `ejemplo_db`

6. **Para detener los servicios:**
   ```bash
   make rm
   ```

---

## 🧭 4. Resultado esperado

Después de desplegar:
- Los servicios estarán **corriendo en contenedores** gestionados por Docker Swarm.
- Podrás **administrar la base de datos PostgreSQL** desde la interfaz de **pgAdmin**.
- El proceso de despliegue será **automático y reutilizable** gracias al Makefile.

---

## 🧩 5. Beneficios de esta metodología

| Ventaja | Descripción |
|----------|-------------|
| 🔁 Automatización | Simplifica tareas repetitivas. |
| 🧱 Portabilidad | Facilita desplegar el mismo entorno en otros servidores. |
| 🔐 Organización | Mantiene la configuración centralizada. |
| ⚡ Productividad | Permite concentrarse en el desarrollo, no en la infraestructura. |

---

## 🧪 Ejercicio práctico

1. Modifica el `stack.yml` para que PostgreSQL use la versión 16.  
2. Agrega una variable de entorno `TZ` para configurar la zona horaria.  
3. Usa `make deploy` para probar los cambios.  
4. Verifica los logs con `make logs`.
