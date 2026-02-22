# Plausible Analytics

A lightweight, privacy-focused web analytics tool as an alternative to Google Analytics. This deployment uses the Plausible Community Edition v2 with PostgreSQL.

## Features

- 📊 Privacy-focused analytics
- ⚡ Fast and lightweight
- 🔐 No data selling - your data stays private
- 🌐 Web UI dashboard and analytics
- 🔗 Subsonic API compatible
- 📈 Real-time stats

## Quick Start

1. Generate a secret key:
   ```bash
   openssl rand -base64 48
   ```

2. Copy the environment template:
   ```bash
   cp .env.dist .env
   ```

3. Edit `.env` and fill in your configuration:
   - `VIRTUAL_HOST`: Your domain (e.g., analytics.example.com)
   - `LETSENCRYPT_HOST`: Same as VIRTUAL_HOST
   - `LETSENCRYPT_EMAIL`: Your email for Let's Encrypt certificates
   - `POSTGRES_PASSWORD`: Strong random password for PostgreSQL
   - `SECRET_KEY_BASE`: Output from the openssl command above

4. Start the services:
   ```bash
   docker compose up -d
   ```

5. Access the web UI at `https://VIRTUAL_HOST` and create your admin account

## Updating

To update to the latest images:

```bash
./updateDockerImages.sh
```

## Architecture

- **Containers**:
  - `plausible_db` (PostgreSQL 16 Alpine)
  - `plausible_app` (Plausible Community Edition v2, port 8000)
- **Volumes**:
  - `./data/db:/var/lib/postgresql/data` (PostgreSQL data)
- **Network**: Connected to `reverse-proxy` network for HTTPS via nginx-proxy

## Database

- PostgreSQL 16 Alpine
- Health check enabled to ensure DB is ready before Plausible starts
- Data persisted in `./data/db/`

## Configuration

See `.claude/plans/init.md` for full setup instructions and Docker Compose details.

## Links

- [Plausible Analytics GitHub](https://github.com/plausible/community-edition)
- [Plausible Documentation](https://plausible.io/docs)
