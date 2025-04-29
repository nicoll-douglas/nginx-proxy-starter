# Docker compose configuration for production/deployment
DC_DEPLOY = docker compose --env-file .env -f docker-compose.deploy.yml

# Docker compose configuration for build
DC_BUILD = docker compose --env-file .env -f docker-compose.build.yml

# Stop and destroy production containers
deploy-down:
	$(DC_DEPLOY) down

# Pull production images from Docker Hub
deploy-pull:
	$(DC_DEPLOY) pull

# Start up the production containers
deploy-up:
	$(DC_DEPLOY) up -d --remove-orphans

# Build application images
deploy-build:
	$(DC_BUILD) build

# Push application images to Docker Hub
deploy-push:
	set -a; . .env; set +a; docker push $$DOCKERHUB_IMAGE

help:
	@echo "Available targets:"
	@echo " deploy-down     - Stop and remove deployment"
	@echo " deploy-pull     - Pull production images"
	@echo " deploy-up       - Pull and start deployment"
	@echo " deploy-build    - Build production image(s)"
	@echo " deploy-push     - Push production image to Docker Hub"

.PHONY: deploy-down deploy-pull deploy-up deploy-build deploy-push
