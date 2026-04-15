# Envy — Self-Hosted Deployment Guide

**Envy** is an on-premise secret management platform. This repository contains everything you need to run Envy on your own infrastructure.

> **Note:** Docker images are private. You need a registry access token provided by Envy after purchase.

---

## Requirements

| Requirement | Version |
|-------------|---------|
| Docker | 24+ |
| Docker Compose | v2.20+ |
| RAM | 2 GB minimum |
| Disk | 10 GB minimum |

---

## Deployment Flow

```
1. Purchase → You receive a GHCR access token from Envy
2. Login to registry → Pull images
3. Start the application
4. Get your Installation ID from the UI
5. Send Installation ID to Envy support
6. Receive your LICENSE.key file
7. Place LICENSE.key and restart → Fully active
```

---

## Step 1 — Login to the Container Registry

After purchase, you will receive a registry access token from Envy. Use it to authenticate:

```bash
docker login ghcr.io -u <provided-username> -p <provided-token>
```

---

## Step 2 — Clone this repository

```bash
git clone https://github.com/envyapp/envy-deploy.git
cd envy-deploy
```

---

## Step 3 — Create your `.env` file

```bash
cp .env.example .env
```

Open `.env` and fill in all values:

```bash
# Generate a strong random secret key:
openssl rand -hex 32
```

| Variable | Description |
|----------|-------------|
| `POSTGRES_USER` | PostgreSQL username |
| `POSTGRES_PASSWORD` | PostgreSQL password — use a strong one |
| `POSTGRES_DB` | PostgreSQL database name |
| `SECRET_KEY` | JWT signing key — minimum 32 characters, random |
| `ENVY_SUPERADMIN_EMAIL` | First admin account email |
| `ENVY_SUPERADMIN_PASSWORD` | First admin account password |
| `ENVY_BASE_URL` | URL where the web UI will be accessible (e.g. `http://192.168.1.100:3000`) |
| `COOKIE_SECURE` | Set to `true` only if serving over HTTPS |
| `LICENSE_PUBLIC_KEY` | Provided by Envy — leave empty for now, fill in Step 6 |

---

## Step 4 — Start Envy (Bootstrap Mode)

At this stage `LICENSE.key` is not yet placed. The application will start in **bootstrap mode** — the UI will be accessible and your Installation ID will be shown.

```bash
touch LICENSE.key
docker compose up -d
```

Check that all containers are running:

```bash
docker compose ps
```

---

## Step 5 — Get your Installation ID

Open your browser and navigate to your `ENVY_BASE_URL`.

You will see your **Installation ID** on the screen. Copy it and send it to Envy support:

📧 **support@envyapp.io**

---

## Step 6 — Receive and activate your license

After verifying your purchase, Envy will send you two things:

- `LICENSE.key` — your license file
- `LICENSE_PUBLIC_KEY` — the public key value for your `.env`

**Activate:**

1. Place `LICENSE.key` in the same directory as `docker-compose.yml`
2. Open `.env` and set `LICENSE_PUBLIC_KEY=<value from Envy>`
3. Restart the application:

```bash
docker compose restart
```

Your Envy instance is now fully active.

---

## Directory structure

```
envy-deploy/
├── docker-compose.yml
├── nginx.conf
├── .env              ← created by you (not committed)
├── .env.example      ← template
└── LICENSE.key       ← received from Envy after purchase
```

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
```bash
docker compose logs envy-api
```
- Make sure `LICENSE.key` exists (even empty file) in the same directory
- After receiving your license, make sure `LICENSE_PUBLIC_KEY` is set in `.env`

### Cannot connect to the web UI
- Verify `ENVY_BASE_URL` matches the address you are accessing
- Confirm port `3000` is open in your firewall

### Database connection errors
- Check `POSTGRES_USER`, `POSTGRES_PASSWORD`, `POSTGRES_DB` are consistent in `.env`
- Wait a few seconds — API waits for the database to be healthy before starting

### Image pull errors (unauthorized)
- Make sure you ran `docker login ghcr.io` with the credentials provided by Envy
- Contact support if your token has expired: **support@envyapp.io**

---

## Security recommendations

- Use a strong, unique `SECRET_KEY` (minimum 32 random characters)
- Use a strong `POSTGRES_PASSWORD`
- Enable HTTPS and set `COOKIE_SECURE=true` in production
- Do not expose port `8000` (API) publicly — only Nginx (port `3000`) should be accessible
- Keep `LICENSE.key` private — do not commit it to version control

---

## Support

📧 **support@envyapp.io**
🐙 **[github.com/envyapp/envy-deploy](https://github.com/envyapp/envy-deploy)**
