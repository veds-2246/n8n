# n8n — Local Docker Compose

Run a self-hosted n8n instance locally with Docker Compose and PostgreSQL.

- n8n editor: http://localhost:5678
- PostgreSQL is available only on the internal Compose network.
- n8n and PostgreSQL data are stored in named Docker volumes.
- The n8n web interface is bound to `127.0.0.1` and is not exposed to your LAN.

## Requirements

- Docker Engine
- Docker Compose v2 (`docker compose`)

Check your installation:

```bash
docker --version
docker compose version
```

## 1. Clone the repository

```bash
git clone https://github.com/veds-2246/n8n.git
cd n8n
```

If you already cloned it, enter the repository and pull the latest commit:

```bash
git pull
```

## 2. Create your local environment file

```bash
cp .env.example .env
```

Generate secrets:

```bash
openssl rand -hex 32
openssl rand -hex 32
```

Use the first output as `POSTGRES_PASSWORD` and the second as `N8N_ENCRYPTION_KEY` in `.env`. Keep `.env` private. Never commit it or share the encryption key. If you already have n8n data, retain the original encryption key so saved credentials remain decryptable.

## 3. Start n8n

```bash
docker compose pull
docker compose up -d
```

Check container status:

```bash
docker compose ps
```

Follow startup logs:

```bash
docker compose logs -f n8n
```

Open http://localhost:5678 and complete the initial owner-account setup.

## Useful commands

```bash
# Stop containers (preserves volumes and data)
docker compose down

# Start again
docker compose up -d

# Restart n8n
docker compose restart n8n

# Follow all logs
docker compose logs -f

# Pull updated images and recreate containers
docker compose pull
docker compose up -d
```

## Data and backups

Named volumes `n8n_data` and `postgres_data` persist across container recreation. Back up your workflows/database and preserve `N8N_ENCRYPTION_KEY`.

**Never run `docker compose down -v` unless you intentionally want to delete the persisted volumes and all data stored in them.**

## Configuration notes

- The editor is exposed on localhost port `5678` only.
- The default timezone is `Asia/Kolkata`; change `GENERIC_TIMEZONE` and `TZ` in `.env` if required.
- This is a local-development setup. For public webhooks or remote access, configure a domain, HTTPS reverse proxy, and the correct `WEBHOOK_URL` before exposing the service.
- The n8n image uses the `latest` tag. For repeatable deployments, pin it to a tested n8n release in `docker-compose.yml` and update deliberately.
