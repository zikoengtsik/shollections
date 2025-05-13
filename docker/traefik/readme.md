# Traefik

Modern HTTP reverse proxy and load balancer that makes deploying microservices easy.

## Overview

[Traefik](https://traefik.io/) is an open-source Application Proxy that makes publishing your services a fun and easy experience. It receives requests on behalf of your system and identifies which components are responsible for handling them, and routes them securely.

### Project Information

- **Project Name**: `traefik`
- **Root Directory**: `/opt/docker/traefik`
- **Ingress Network**: `ingress-traefik`
- **Service Images**:
  + traefik: `docker.io/traefik:v3.4.0`

### Directory Structure

```text
/opt/docker/traefik
├── data  # Data directory
|   ├── acme.json  # ACME configuration file
|   └── ...  # Other Data files
├── dynamic  # Dynamic configuration
│   ├── middlewares.yaml  # Middlewares configuration file
│   └── ...  # Other configuration files
├── logs  # Log directory
|   ├── access.log  # Access log file
│   └── traefik.log  # Traefik log file
|── .env  # Environment variables
└── compose.yaml  # Docker Compose file
```

## Getting Started

### Prerequisites

- Required ports are open on your server.
  + HTTP port: `80` (default)
  + HTTPS port: `443` (default)
  + Other ports as required by your needs.

### Deployment

1. Define the ingress network `ingress-traefik` for connections to all containers from the reverse proxy.

    ```shell
    docker network create ingress-traefik
    ```

2. Create the required directories for the project.

    ```shell
    mkdir -p /opt/docker/traefik
    mkdir -p /opt/docker/traefik/data
    mkdir -p /opt/docker/traefik/dynamic
    ```

3. Copy the compose file to `compose.yaml` in the root directory.

    + `compose.with-acme.yaml`: Configure Traefik to use an ACME provider for automatic certificate generation.

4. Copy the `.env.example` file to `.env` in the root directory, and update the environment variables.
5. Read the "Configuration" section to configure the project.
6. Run the command `docker compose -f /opt/docker/traefik/compose.yaml up -d` to start the project.

## Configuration

### HTTPS with ACME

> This section requires the `compose.with-acme.yaml` file.

Traefik supports the ACME protocol to automatically create SSL certificates for your services. The DNS challenge is the most reliable method to obtain SSL certificates.

I recommend using the Cloudflare DNS provider to create the DNS challenge. You need to create an API token with the permissions `Zone.Zone:Read` and `Zone.DNS:Edit`.

1. Create a new file `acme.json` in the directory `data` and set the permissions to `600`.

    ```shell
    touch /opt/docker/traefik/data/acme.json
    chmod 600 /opt/docker/traefik/data/acme.json
    ```

2. Update environment variables in the `.env` file.

    + `TRAEFIK_PROXY_ACME_EMAIL`: The email address for the ACME challenge.
    + `TRAEFIK_PROXY_ACME_DNS_PROVIDER`: The DNS provider for the ACME challenge. Set the value to `cloudflare`.

3. Add the Cloudflare API token to the `compose.yaml` file.

    ```yaml
    services:
      traefik:
        environment:
          - CF_DNS_API_TOKEN=<cloudflare-api-token>
    ```

### Logs

By default, logs are written to stdout, in json format. You can change the log configuration by updating the `.env` file.

If you want to write logs to files, follow the steps below.

1. Create a log directory in the root directory.

    ```shell
    mkdir -p /opt/docker/traefik/logs
    ```

2. Update the `compose.yaml` file to enable logging.

    ```yaml
    services:
    traefik:
      command:
        # Enable logging
        - --log.filePath=/logs/traefik.log
        - --accesslog.filePath=/logs/access.log
      volumes:
        - ${ROOT_DIR}/logs:/logs
    ```

3. Run the command `docker compose -f /opt/docker/traefik/compose.yaml up -d` to apply the changes.

### Dashboard

Traefik provides a web dashboard to monitor the status of the services.

If you want to access the dashboard, add the following lines to the `routers.yaml` file in the directory `dynamic`.

```yaml
http:
  routers:
    traefik-dashboard:
      rule: Host(`<traefik-dashboard-domain>`) && (PathPrefix(`/api`) || PathPrefix(`/dashboard`))
      entrypoints: [web, websecure]
      service: api@internal
```

### Basic Authentication

Enable basic authentication to grants access to services to authorized users.

> Passwords must be hashed using MD5, SHA1, or BCrypt. You can use the `htpasswd` command to generate the password.

1. Run the command `htpasswd -nbB <username> <password>` to generate the password.
2. Add the following lines to the `middlewares.yaml` file in the directory `dynamic`.

    ```yaml
    http:
      middlewares:
        basic-auth:
          basicAuth:
            users:
            - "<username>:<hashed-password>"
    ```

3. Enable basic authentication for the service by adding the following lines to the `routers.yaml` file in the directory `dynamic`.

    ```yaml
    http:
      routers:
        traefik-dashboard:
          middlewares:
            - basic-auth@file
    ```
