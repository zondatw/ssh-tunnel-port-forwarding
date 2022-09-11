DOCKER_CMD := docker
IMG_NAME := pf_con 
CONTAINER_NAME := pf_con
CURRENT_PATH := $(PWD)
PROJECT_PATH := $(CURRENT_PATH)/../

all: build run

build:
	$(DOCKER_CMD) build -t $(IMG_NAME) .

run:
	$(DOCKER_CMD) run --name $(CONTAINER_NAME) -it -p 2212:2212 -p 8088:8088 -p 8099:8099 -v $(PROJECT_PATH):/app $(IMG_NAME) /bin/bash

attach:
	$(DOCKER_CMD) exec -it $(CONTAINER_NAME) /bin/bash

start:
	$(DOCKER_CMD) start $(CONTAINER_NAME)

stop:
	$(DOCKER_CMD) stop $(CONTAINER_NAME)

.PHONY: clean

clean: stop clear

clear:
	$(DOCKER_CMD) rm $(CONTAINER_NAME)
	$(DOCKER_CMD) rmi $(IMG_NAME)
