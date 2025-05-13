# Vaultwarden

Unofficial Bitwarden compatible server written in Rust.

## Overview

[Vaultwarden](https://github.com/dani-garcia/vaultwarden) is an unofficial Bitwarden server implementation written in Rust. It is compatible with the official Bitwarden clients, and is ideal for self-hosted deployments where running the official resource-heavy service is undesirable.

### Project Infomation

- **Project Name**: `vaultwarden`
- **Root Directory**: `/opt/docker/vaultwarden`
- **Service Images**:
  + vaultwarden: `ghcr.io/dani-garcia/vaultwarden:1.33.2`

### Directory Structure

```text
/opt/docker/vaultwarden
├── data  # Data directory
├── .env  # Environment variables
└── compose.yaml  # Docker Compose file
```

## Getting Started

### Prerequisites

- Have a SMTP server to send emails.

### Deployment

1. Create the required directories for the project.

    ```shell
    mkdir -p /opt/docker/vaultwarden
    mkdir -p /opt/docker/vaultwarden/data
    ```

2. Copy the compose file to `compose.yaml` in the root directory.

    + `compose.with-traefik.yaml`: Deploy the project with Traefik.

3. Copy the `.env.example` file to `.env` in the root directory, and update the environment variables.
4. Read the "Configuration" section to configure the project.
5. Run the command `docker compose -f /opt/docker/vaultwarden/compose.yaml up -d` to start the project.

## Configuration

### Registration & Invitation

By default, registration is enabled in Vaultwarden. To disable registration, set the `VAULTWARDEN_SIGNUPS_ALLOWED` environment variable to `false` in the `.env` file.

Even when registration is disabled, you can still invite users to join your organization. You can disable invitations by setting the `VAULTWARDEN_INVITATIONS_ALLOWED` environment variable to `false` in the `.env` file.

### SMTP Configuration

Vaultwarden requires an SMTP server to send emails. Update the relevant environment variables in the `.env` file with your SMTP server details.
