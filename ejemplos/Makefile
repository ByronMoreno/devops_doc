build-image:
	@ docker build -f Dockerfile -t titulacionbackend:1 .
network:
	@ docker network create --driver overlay --scope swarm desarrollo_net || true

imprimir:
	@ echo "Ingrese la contraseña para los secretos: moodle_db_pass, moodle_db_root_pass respectivamente"
secrets: imprimir
	@ printf $(shell stty -echo; read -p "Password: " pwd; stty echo; echo $$pwd) | docker secret create moodle_db_pass -
	@ printf $(shell stty -echo; read -p "Password: " pwd; stty echo; echo $$pwd) | docker secret create moodle_db_root_pass -

volumes:
	@ docker volume create pg_tendencia_data

deploy:
	@ docker stack deploy --with-registry-auth -c stack_pg_ventas.yml titulacion
rm:
	@ docker stack rm titulacion

rm-secret:
	@ docker secret rm moodle_db_pass
	@ docker secret rm moodle_db_root_pass
rm-volume:
	@ docker volume rm pg_tendencia_data
