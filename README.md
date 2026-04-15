# Envy — Self-Hosted Deployment Guide

**Envy** is an on-premise secret management platform. This repository contains everything you need to run Envy on your own infrastructure.

---

## Requirements

| Requirement | Version |
|-------------|---------|
| Docker | 24+ |
| Docker Compose | v2.20+ |
| RAM | 2 GB minimum |
| Disk | 10 GB minimum |

---

## Quick Start

### 1. Clone this repository

```bash
git clone https://github.com/envyapp/envy-deploy.git
cd envy-deploy
```

### 2. Create your `.env` file

```bash
cp .env.example .env
```

Open `.env` and fill in all required values:

```bash
# Generate a strong random secret key:
openssl rand -hex 32
```

| Variable | Description |
|----------|-------------|
| `POSTGRES_USER` | PostgreSQL username |
| `POSTGRES_PASSWORD` | PostgreSQL password (use a strong one) |
| `POSTGRES_DB` | PostgreSQL database name |
| `SECRET_KEY` | JWT signing key — minimum 32 characters, random |
| `ENVY_SUPERADMIN_EMAIL` | First admin account email |
| `ENVY_SUPERADMIN_PASSWORD` | First admin account password |
| `ENVY_BASE_URL` | URL where the web UI will be accessible (e.g. `http://192.168.1.100:3000`) |
| `COOKIE_SECURE` | Set to `true` only if serving over HTTPS |
| `LICENSE_PUBLIC_KEY` | Provided by Envy after purchase |

### 3. Place your license file

Copy the `LICENSE.key` file you received into the same directory as `docker-compose.yml`:

```
envy-deploy/
├── docker-compose.yml
├── nginx.conf
├── .env
└── LICENSE.key   ← here
```

### 4. Start Envy

```bash
docker compose up -d
```

That's it. Envy will be available at the URL you set in `ENVY_BASE_URL` (default port: `3000`).

---

## Verify the installation

```bash
# Check all containers are running
docker compose ps

# Check API logs
docker compose logs envy-api

# Check for license errors
docker compose logs envy-api | grep -i license
```

---

## Accessing the dashboard

Open your browser and navigate to your `ENVY_BASE_URL`. Log in with the `ENVY_SUPERADMIN_EMAIL` and `ENVY_SUPERADMIN_PASSWORD` you set.

---

## Stopping and updating

```bash
# Stop
docker compose down

# Update to a new version
docker compose pull
docker compose up -d
```

---

## Troubleshooting

### API container exits immediately
Check logs for license errors:
```bash
docker compose logs envy-api
```
- Make sure `LICENSE.key` is present in the same directory as `docker-compose.yml`
- Make sure `LICENSE_PUBLIC_KEY` is correctly set in `.env`

### Cannot connect to the web UI
- Verify `ENVY_BASE_URL` matches the address you are accessing
- Confirm port `3000` is open in your firewall

### Database connection errors
- Check `POSTGRES_USER`, `POSTGRES_PASSWORD`, and `POSTGRES_DB` are identical in `.env`
- Wait a few seconds — the API waits for the database to be healthy before starting

---

## Security recommendations

- Use a strong, unique `SECRET_KEY` (minimum 32 random characters)
- Use a strong `POSTGRES_PASSWORD`
- Enable HTTPS and set `COOKIE_SECURE=true` in production
- Restrict port `8000` (API) — it should not be publicly accessible; only Nginx (port `3000`) should be exposed
- Keep your `LICENSE.key` file private

---

## Support

For license issues or support, contact: **support@envyapp.io**
