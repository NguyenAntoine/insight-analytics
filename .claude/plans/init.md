# Plausible Community Edition Setup

Plausible is a lightweight, privacy-focused web analytics tool. The community edition v2 uses PostgreSQL for storage (ClickHouse dependency was removed).

## Docker Compose Configuration

```yaml
services:
  db:
    image: postgres:16-alpine
    container_name: plausible_db
    restart: always
    volumes:
      - ./data/db:/var/lib/postgresql/data
    environment:
      - POSTGRES_DB=plausible
      - POSTGRES_USER=plausible
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U plausible"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - default

  plausible:
    image: ghcr.io/plausible/community-edition:v2
    container_name: plausible_app
    restart: always
    expose:
      - 8000
    depends_on:
      db:
        condition: service_healthy
    environment:
      - BASE_URL=https://${VIRTUAL_HOST}
      - SECRET_KEY_BASE=${SECRET_KEY_BASE}
      - DATABASE_URL=postgres://plausible:${POSTGRES_PASSWORD}@db:5432/plausible
      - VIRTUAL_HOST=${VIRTUAL_HOST}
      - LETSENCRYPT_HOST=${LETSENCRYPT_HOST}
      - LETSENCRYPT_EMAIL=${LETSENCRYPT_EMAIL}
      - VIRTUAL_PORT=8000
    networks:
      - default
      - reverse-proxy

networks:
  reverse-proxy:
    external: true
```

## Setup Instructions

1. Generate a secret key:
   ```bash
   openssl rand -base64 48
   ```

2. Copy the environment template:
   ```bash
   cp .env.dist .env
   ```

3. Edit `.env` and fill in:
   - `VIRTUAL_HOST`: Your domain (e.g., analytics.example.com)
   - `LETSENCRYPT_HOST`: Same as VIRTUAL_HOST
   - `LETSENCRYPT_EMAIL`: Your email for Let's Encrypt
   - `POSTGRES_PASSWORD`: Strong random password
   - `SECRET_KEY_BASE`: Output from openssl command above

4. Transfer to VPS:
   ```bash
   rsync -av /Users/antoine/Perso/insight-analytics/ vps:/path/to/insight-analytics/
   ```

5. On VPS, start the containers:
   ```bash
   cd /path/to/insight-analytics
   docker compose up -d
   ```

## Features
- Privacy-focused analytics alternative to Google Analytics
- PostgreSQL-based storage (v2 simplified architecture)
- Web UI for configuration and dashboard
- Lightweight and fast
