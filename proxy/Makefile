# Docker compose configuration for the Nginx proxy
DC_PROXY = docker compose --env-file .env.proxy -f docker-compose.proxy.yml

# Create the network
proxy-create-network:
	docker network create nginx-proxy-network

# Start the proxy
proxy-up:
	$(DC_PROXY) up -d

help:
	@echo "Available targets:"
	@echo " proxy-create-network - Create Docker network for Nginx"
	@echo " proxy-up             - Start Nginx-Docker proxy setup"

.PHONY: proxy-create-network proxy-up
