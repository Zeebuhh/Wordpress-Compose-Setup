# Wordpress-Compose-Setup

## Table of Contents

1. [Introduction](#introduction)
2. [Quickstart](#quickstart)
3. [Usage](#usage)
4. [Configuration Details](#configuration-details)
5. [Troubleshooting](#troubleshooting)

## Introduction

This repository provides a `docker-compose` setup for deploying a WordPress site with a MariaDB database. The purpose of this setup is to simplify the deployment process by using containerized services. This repository includes configuration files necessary to quickly launch a WordPress instance with persistent storage and database support.

## Quickstart

### Prerequisites

Before you begin, ensure you have the following installed:

- Docker
- Docker Compose
- Git

### Steps to Start

1. Clone the repository:
   ```sh
   #bash
   git clone <repository-url>
   cd <repository-folder>
   ```
2. Create a `.gitignore` file and add `.env` to exclude it from version control.
3. Create a `.env` file with necessary environment variables (see [Configuration Details](#configuration-details)).
4. Run the following command to start the containers:
   ```sh
   #bash
   docker-compose up --build
   ```
5. Access WordPress in your browser at `http://<your-ip>:8080`.

## Usage

### Docker Compose File

The `docker-compose.yaml` file defines the services:

```yaml
services:
  wordpress:
    image: wordpress:latest
    ports:
      - 8080:80
    env_file:
      - .env
    volumes:
      - wordpress_data:/var/www/html #official data-path
    depends_on:
      - db
  db:
    image: mariadb:10.6.4-focal
    volumes:
      - db_data:/var/lib/mysql #official data-path
    env_file:
      - .env
    restart: always

volumes:
  db_data:
  wordpress_data:
```

### Explanation of Services

- **WordPress Service (`wordpress`)**

  - Uses the latest official WordPress image.
  - Exposes port 8080 on the host, mapping it to port 80 inside the container.
  - Uses environment variables from the `.env` file.
  - Stores persistent data in `wordpress_data` volume.
  - Depends on the database service (`db`)and ensures to start db first.

- **Database Service (`db`)**
  - Uses MariaDB version `10.6.4-focal`.
  - Stores database data persistently in `db_data` volume.
  - Loads database credentials from the `.env` file.
  - Restarts automatically in case of failures.

## Configuration Details

Create a `.env` file to define environment variables required by WordPress and MariaDB:

```ini
WORDPRESS_DB_HOST=db
WORDPRESS_DB_USER=wordpress
WORDPRESS_DB_PASSWORD=wordpress
WORDPRESS_DB_NAME=wordpress

MYSQL_ROOT_PASSWORD=somewordpress
MYSQL_DATABASE=wordpress
MYSQL_USER=wordpress
MYSQL_PASSWORD=wordpress
```

If you need to copy the `.env` file to a remote VM, use:

```sh
#bash
scp /path/to/env/file/.env username@vm-ip:/destination/path/
```

## Troubleshooting

If you encounter issues, you can use the following commands to debug:

```sh
#bash
docker-compose ps         # Check running containers
docker-compose logs       # View logs for troubleshooting
docker-compose start      # Start stopped containers
docker-compose stop       # Stop running containers
docker-compose down       # Stop and remove containers
```

Ensure that:

- The `.env` file contains correct credentials.
- No other service is using port `8080`.
- The database service is running before WordPress starts.

If you need to reset the setup, remove all containers and volumes:

```sh
docker-compose down -v
```
