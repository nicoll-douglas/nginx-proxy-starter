# Nginx Proxy + Docker + SSL Starter Kit

A Docker + Nginx + Let's Encrypt starter kit / setup for deploying Dockerized web applications to a server with automatic SSL certificate management using Nginx Proxy and Let's Encrypt.

## Overview

This repository provides a configurable and extensible solution for:

- Setting up an Nginx reverse proxy with Docker
- Automatic SSL certificate generation and renewal via Let's Encrypt
- Simplified deployment of Dockerized web applications behind the proxy
- GitHub Actions workflow templates for CI/CD

## Repository Structure

```
nginx-proxy-starter/
├── proxy/                        # Nginx proxy configuration
│   ├── .env.proxy                # Environment variables for the proxy
│   ├── docker-compose.proxy.yml  # Docker Compose for Nginx and Let's Encrypt
│   └── Makefile                  # Commands for proxy management
│
├── site/                         # Files to be copied and configured for your project
│   ├── .env.production           # Environment variables for your site
│   ├── Dockerfile                # Dockerfile for your site
│   ├── docker-compose.build.yml  # Docker Compose for building your site's image(s)
│   ├── docker-compose.deploy.yml # Docker Compose for deployment
│   └── Makefile                  # Commands for site deployment
│
└── workflows/                    # GitHub Actions workflow templates
    ├── deploy.yml                # Automatic deployment workflow
    └── test-ssh.yml              # Test SSH workflow
```

## How It Works

1. You will have an Nginx proxy container listening on ports 80 and 443 on your server
2. You will have a container for your website also on your server
3. Your site container will connect to the same network and be accessible via its configured domain
4. When a request comes in for the configured domain, the proxy will route to the appropriate container (your site)
5. The Let's Encrypt companion container will automatically issue and renew SSL certificates for your site

## Guide

This guide walks you through setting up the Nginx proxy and manually deploying your site.

### Prerequisites

- A server or VPS
- [Docker Engine](https://docs.docker.com/engine/) installed on your server and locally
- [Git](https://git-scm.com/) installed on your server and locally
- [GNU Make](https://www.gnu.org/software/make/) installed on your server and locally
- Domain name(s) with DNS configured to point to your server
- Docker Hub account (for pushing/pulling images)

### Setup Instructions

#### 1. Build Your Application

Clone the repo to your local machine and switch to the site directory:

```bash
git clone https://github.com/nicoll-douglas/nginx-proxy-starter.git
cd nginx-proxy-starter/site
```

Copy the files to your project directory:

```bash
cp ./* ./.* /path/to/your/project
```

Next, configure `.env.production` and the Docker files as necessary for your project. See the next section for more details on environment configuration.

Then, login to docker with the following command:

```bash
docker login -u yourusername
```

Next, build and push your image to Docker Hub:

```bash
make deploy-build
make deploy-push
```

Your site is now built and pushed to Docker Hub, ready to be pulled to your server ✅.

#### 2. Set up the Nginx Proxy

On your server, clone the repository:

```bash
git clone https://github.com/nicoll-douglas/nginx-proxy-starter.git
cd nginx-proxy-starter/proxy
```

Next, edit `.env.proxy` with your email. This will be the default email used for Let's Encrypt notifications.

Then, run the commands to create the necessary Docker network and start the proxy:

```bash
make proxy-create-network
make proxy-up
```

#### 3. Deploy Your Application

Next, somewhere on your server, clone your site's repo which contains the Docker compose files and Makefile you configured and copied to it earlier:

```bash
git clone https://github.com/your-username/your-site.git
cd your-site
```

Make sure you have the `.env.production` file in your site's working directory and configure as necessary.

Next, pull your site's docker image:

```bash
make deploy-pull
```

Finally, deploy your site:

```bash
make deploy-up
```

Your site should now be deployed with SSL enabled ✅.

## Environment Variables

### Proxy Environment (.env.proxy)

- `DEFAULT_EMAIL`: Your default email for Let's Encrypt notifications

### Site Environment (.env.production)

- `APP_CONTAINER_NAME`: Container name for your application
- `DOCKERHUB_IMAGE`: Docker Hub image (e.g., username/repo:tag)
- `VIRTUAL_HOST`: Domain name for your site
- `LETSENCRYPT_HOST`: Domain for SSL certificate (usually same as `VIRTUAL_HOST`)
- `LETSENCRYPT_EMAIL`: Site-specific email for Let's Encrypt notifications

## Available Commands

### Proxy Commands

- `make proxy-create-network`: Create the Docker network for Nginx **(server use)**
- `make proxy-up`: Start the Nginx proxy and Let's Encrypt companion containers **(server use)**

### Site Commands

- `make deploy-build`: Build your site's Docker image **(local use)**
- `make deploy-push`: Push your image to Docker Hub **(local use)**
- `make deploy-pull`: Pull the latest image from Docker Hub **(server use)**
- `make deploy-up`: Deploy your site **(server use)**
- `make deploy-down`: Stop and remove your site's containers **(server use)**

## Best Practices

This starter kit provides a guide for manual deployment so you can get it up and running quickly. For production use, it is recommended to set up CI/CD pipelines using the provided GitHub Actions workflow templates.

## Troubleshooting

- Check Docker logs: `docker logs nginx-proxy`
- Verify network connectivity: `docker network inspect nginx-proxy-network`
- Ensure DNS is properly configured for your domains
- Check that your application container is running: `docker ps`
- You may need to run `make` commands with `sudo` access
- SSL errors in the browser may mean that the containers are still starting up. Check the logs if issues persist.

## License

[MIT](https://mit-license.org/)
