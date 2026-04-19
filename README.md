# LibreTranslate Stack

This stack provides a self-hosted LibreTranslate service with optional HTTPS and reverse proxy via Caddy. The setup is managed with Docker Compose and supports both internal and Cloudflare DNS-based TLS.

---

## Contents
- [Overview](#overview)
- [Directory Structure](#directory-structure)
- [Environment Variables](#environment-variables)
- [Build & Run Instructions](#build--run-instructions)
- [Caddy Configuration](#caddy-configuration)
- [Docker Compose Services](#docker-compose-services)
- [Notes](#notes)

---

## Overview
- **LibreTranslate**: Open-source machine translation API and web UI.
- **Caddy**: Handles HTTPS, reverse proxy, and optional Cloudflare DNS-based TLS.
- **Docker Compose**: Orchestrates containers and networks.

---

## Directory Structure
- `run.sh` – Main entry script to configure and run the stack.
- `docker-compose.yml` – Defines the LibreTranslate service and networks.
- `docker-compose.root-caddy.yml` – Defines the Caddy service and volumes.
- `.env.example` – Example environment variables for configuration.
- `.gitignore` – Files and directories to ignore in git.
- `caddy_global/Caddyfile` – Caddy reverse proxy configuration.
- `caddy_global/Dockerfile` – Dockerfile for building Caddy with Cloudflare DNS plugin.

---

## Environment Variables
Copy `.env.example` to `.env` and adjust as needed:

- `MULTIPLE_CADDY_GLOBAL`: Set to `true` for multiple global Caddy services
- `TRANSLATE_SERVER_ADDRESS`: Domain for Caddy HTTPS access
- `LT_LOAD_ONLY`: Comma-separated list of language models to load (e.g., `en,fr,es`)
- `CF_API_TOKEN`: Cloudflare API token for DNS-based TLS
- `HTTP_PORT`, `HTTPS_PORT`: Ports for Caddy (default: 80/443)

---

## Build & Run Instructions

1. Copy and edit environment variables:
   ```sh
   cp .env.example .env
   # Edit .env as needed
   ```
2. Start the stack:
   ```sh
   ./run.sh up -d
   ```
   - The script ensures required Docker networks exist and configures Compose files and TLS based on your environment.
3. To stop the stack:
   ```sh
   ./run.sh down
   ```

---

## Caddy Configuration
- The Caddyfile (`caddy_global/Caddyfile`) sets up a reverse proxy for the LibreTranslate API/web UI.
- TLS is configured based on whether the server address is an IP (internal TLS) or domain (Cloudflare DNS-based TLS).
- The Caddy Docker image is built with the Cloudflare DNS plugin (see `caddy_global/Dockerfile`).

---

## Docker Compose Services
### docker-compose.yml
- **libretranslate**: Machine translation API and web UI
  - Image: `libretranslate/libretranslate:latest`
  - Networks: `caddy_net`
  - Volumes: Persistent API keys and language models
  - Environment: API keys, model loading, web UI toggle

### docker-compose.root-caddy.yml
- **caddy**: Reverse proxy
  - Build context: `./caddy_global`
  - Ports: 80 (HTTP), 443 (HTTPS)
  - Volumes: Caddyfile, data, config
  - Networks: `caddy_net`

---

## Notes
- `.gitignore` excludes `.env` and `caddy_certs`.
- For production, ensure secrets are set securely and not committed to version control.
- For more details, see comments in `run.sh` and the Compose files.

---

## License
See upstream LibreTranslate and Caddy repositories for license details.
