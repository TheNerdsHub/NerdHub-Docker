# NerdHub-Docker

This repository contains the Docker and Docker Compose configurations for deploying the entire NerdHub application stack.

## Overview

The `docker-compose.yml` file orchestrates the deployment of the following services:

- **`backend`**: The .NET 8 backend API.
- **`frontend`**: The React-based frontend application.
- **`discordbot`**: The Discord.js bot.
- **`imgproxy`**: An on-the-fly image processing server that converts Steam JPEGs to AVIF.
- **`mongo`**: A MongoDB instance for data storage.

All services communicate over an internal Docker network (`172.20.0.0/16`) using static IPs.

## Services

| Service | Container Port | Default Host Port | IP |
|---|---|---|---|---|
| Backend | 80 | `9003` | `172.20.0.2` |
| Frontend | 80 | `9004` | `172.20.0.3` |
| Discord Bot | — | — | `172.20.0.4` |
| MongoDB | 27017 | `9002` | `172.20.0.5` |
| imgproxy | 8080 | `8080`¹ | `172.20.0.6` |

> ¹ imgproxy port is optional — not needed for normal operation as nginx proxies requests internally.

## Environment Variables

Variables use a prefix convention so you know which service owns each one:

- `BACKEND_*` — consumed by the backend API
- `FRONTEND_*` / `API_URL_EXTERNAL` — consumed by the frontend
- `DISCORD_*` / `API_URL_INTERNAL` — consumed by the Discord bot
- `MONGO_*` — consumed by MongoDB

### All Variables

| Variable | Service | Purpose | Internal / External | `stack.env` Default |
|---|---|---|---|---|
| `BACKEND_PORT` | Docker | Host port mapped to backend container port 80 | Host | `9003` |
| `BACKEND_MONGO_URI` | Backend | MongoDB connection string for the .NET API | Internal | `mongodb://BACKEND_MONGO_URI_HERE` |
| `BACKEND_STEAM_API_KEY` | Backend | Steam API key for fetching game data | Secret | `API_KEY_HERE` |
| `BACKEND_CORS_ORIGINS` | Backend | Comma-separated list of allowed CORS origins | External | `http://localhost:3000,http://localhost:5173,https://your-production-frontend.com` |
| `FRONTEND_PORT` | Docker | Host port mapped to frontend container port 80 | Host | `9004` |
| `API_URL_EXTERNAL` | Frontend | Backend URL the browser uses (public/proxy address) | External | `http://172.20.0.2:80` |
| `IMGPROXY_URL` | Frontend | Path prefix proxied to imgproxy via nginx (default: `/images`) | Internal | `/images` |
| `DISCORD_BOT_TOKEN` | Discord Bot | Discord bot authentication token | Secret | `YOUR_TOKEN_HERE` |
| `API_URL_INTERNAL` | Discord Bot | Backend URL the bot uses (Docker-internal hostname) | Internal | `http://172.20.0.2:80` |
| `MONGODB_PORT` | Docker | Host port mapped to MongoDB container port 27017 | Host | `9002` |
| `IMGPROXY_PORT` | Docker | Optional host port to expose imgproxy directly (not needed for normal operation) | Host | `8080` |
| `MONGO_INITDB_ROOT_USERNAME` | MongoDB | MongoDB root username | Internal | `root` |
| `MONGO_INITDB_ROOT_PASSWORD` | MongoDB | MongoDB root password | Internal | `root` |

### External vs Internal

- **External** — Must be reachable from outside Docker (your browser, public internet). Set these to your server's public IP, domain, or reverse proxy URL.
- **Internal** — Only needs to resolve inside the Docker network. Use Docker service names (`backend`, `mongo`) or static IPs.
- **Host** — Controls which ports on the Docker host the containers bind to.
- **Secret** — Sensitive values that should be kept secure.

## Getting Started

### Prerequisites

- [Docker](https://www.docker.com/get-started)
- [Docker Compose](https://docs.docker.com/compose/install/)

### Configuration

1. Copy `stack.env` to `.env` (or use it directly with `--env-file`).
2. Update the values for your environment. At minimum you need:
   - `BACKEND_MONGO_URI` — point to your MongoDB instance
   - `BACKEND_STEAM_API_KEY` — your Steam API key
   - `DISCORD_BOT_TOKEN` — your Discord bot token
   - `API_URL_EXTERNAL` — your server's public URL or IP where the frontend can reach the backend

### Running the Stack

```sh
docker compose --env-file stack.env up -d
```

To stop the services:

```sh
docker compose down
```

### Portainer CE Deployment

This stack is designed for git-based deployment in Portainer CE v2.39.0+.

1. Create a new stack from your git repository.
2. Set **Compose path** to `docker-compose.yml`.
3. Add all required variables as **inline environment variables** in Portainer's UI (do not use `env_file:` — see below).

> **Note:** `env_file:` is avoided in this compose file because Portainer CE v2.39.0 cannot register files from git repos as config resources. All variables are injected via `${VAR}` substitution from Portainer's inline environment variables UI.

See the [stack.env](./stack.env) file for the full list and default values.
