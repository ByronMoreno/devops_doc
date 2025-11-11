APP_NAME=pgmoreno
STACK_FILE=stack.yml

build:
	docker build -t $(APP_NAME):latest .

deploy:
	docker stack deploy --with-registry-auth -c $(STACK_FILE) $(APP_NAME)

logs:
	docker service logs -f $(APP_NAME)_$(APP_NAME)

rm:
	docker stack rm $(APP_NAME)

ps:
	docker service ls

restart:
	make rm
	sleep 5
	make build
	make deploy
