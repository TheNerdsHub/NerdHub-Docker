# NerdHub-Docker

This repository contains the Docker and Docker Compose configurations for deploying the entire NerdHub application stack.

## Overview

The `docker-compose.yml` file orchestrates the deployment of the following services:

-   **`backend`**: The .NET 8 backend API.
-   **`frontend`**: The React-based frontend application.
-   **`discordbot`**: The Discord.js bot.
-   **`mongo`**: A MongoDB instance for data storage.

## Services

-   **Backend**: Exposes port `5000` for the API.
-   **Frontend**: Exposes port `80` for the web application.
-   **Discord Bot**: Runs the Discord bot service.
-   **MongoDB**: Uses a volume for persistent data storage.

## Getting Started

### Prerequisites

-   [Docker](https://www.docker.com/get-started)
-   [Docker Compose](https://docs.docker.com/compose/install/)

### Configuration

1.  Open the `stack.env` file in the root of the repository.
2.  Update the values for the environment variables with your specific settings (e.g., Steam API key, Discord bot token, MongoDB credentials). Refer to the `docker-compose.yml` file for a complete list of required variables.

### Running the Stack

To build and run the entire application stack, use the following command from the root of the repository:

```sh
docker-compose up --build -d
```

To stop the services, run:

```sh
docker-compose down
```