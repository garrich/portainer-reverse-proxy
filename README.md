# Traefik-Portainer Setup with httpbin Example

This repository contains a Docker Compose configuration for setting up Traefik as a reverse proxy, Portainer for container management, and instructions for adding a httpbin container as an example service.

## Prerequisites

- Docker and Docker Compose installed on your system
- A machine with a fixed IP address and port 9443 open

## Setup Instructions

1. Clone this repository:
   ```
   git clone https://your-repo-url.git
   cd your-repo-directory
   ```

2. Create a Docker network for Traefik:
   ```
   docker network create traefik-public
   ```

3. Start the Traefik and Portainer services:
   ```
   docker-compose up -d
   ```

4. Access Portainer:
   Open your web browser and navigate to `http://your-external-ip:9443/portainer`

5. Set up Portainer:
   - Create an admin user when prompted
   - Choose "Docker" as the environment type
   - Select "Local" and connect to the local Docker environment

## Adding httpbin Container

To add the httpbin container as an example service:

1. In Portainer, go to "Containers" and click "+ Add container"

2. Use the following settings:
   - Name: httpbin
   - Image: kennethreitz/httpbin
   - Network: Select the "traefik-public" network

3. In "Advanced container settings", add these labels:
   ```
   traefik.enable: true
   traefik.http.routers.httpbin.rule: PathPrefix(`/httpbin`)
   traefik.http.services.httpbin.loadbalancer.server.port: 80
   traefik.http.middlewares.httpbin-stripprefix.stripprefix.prefixes: /httpbin
   traefik.http.routers.httpbin.middlewares: httpbin-stripprefix
   ```

4. Click "Deploy the container"

## Testing the Setup

Access the httpbin service at:
```
http://your-external-ip:9443/httpbin
```

Try these endpoints:
- `http://your-external-ip:9443/httpbin/get`
- `http://your-external-ip:9443/httpbin/headers`
- `http://your-external-ip:9443/httpbin/ip`

## Notes

- This setup uses HTTP. For production use, configure HTTPS.
- The external port 9443 is mapped to internal port 80 in the Traefik service.
- Ensure your firewall allows incoming traffic on port 9443.

## Troubleshooting

If you encounter issues:
1. Check that all containers are running: `docker ps`
2. View Traefik logs: `docker logs traefik`
3. Ensure the "traefik-public" network exists: `docker network ls`

For more help, please open an issue in this repository.
