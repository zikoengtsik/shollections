# Opengist

Self-hosted pastebin powered by Git, open-source alternative to Github Gist.

## Overview

[Opengist](https://opengist.io/) is a self-hosted Pastebin powered by Git. All snippets are stored in a Git repository and can be read and/or modified using standard Git commands, or with the web interface. It is similar to [GitHub Gist](https://gist.github.com/), but open-source and could be self-hosted.

### Project Infomation

- **Project Name**: `opengist`
- **Root Directory**: `/opt/docker/opengist`
- **Service Images**:
  + opengist: `ghcr.io/thomiceli/opengist:1.10.0`

### Directory Structure

```text
/opt/docker/opengist
├── data  # Data directory
├── .env  # Environment variables
└── compose.yaml  # Docker Compose file
```

## Getting Started

### Deployment

1. Create the required directories for the project.

    ```shell
    mkdir -p /opt/docker/opengist
    mkdir -p /opt/docker/opengist/data
    ```

2. Copy the compose file to `compose.yaml` in the root directory.

    + `compose.with-traefik.yaml`: Deploy the project with Traefik.

3. Copy the `.env.example` file to `.env` in the root directory, and update the environment variables.
4. Run the command `docker compose -f /opt/docker/opengist/compose.yaml up -d` to start the project.
