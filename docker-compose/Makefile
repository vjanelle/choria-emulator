.PHONY: all kill down 

all: up
	docker-compose exec shell bash

up: .build kill down
	# docker-compose build --parallel
	docker-compose up -d 

kill:
	docker-compose kill

down:
	docker-compose down -v

.build:
	docker-compose build --parallel
	touch .build

clean:
	rm .build || true

