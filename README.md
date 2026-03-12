# monitoring-for-homelab

Docker Compose monitoring stack for a homelab running Ubuntu. Prometheus scrapes metrics from the host, Docker containers, and Caddy — Grafana visualizes everything and sends email alerts via Gmail.

## Stack

| Service | Image | Purpose |
|---|---|---|
| Prometheus | `prom/prometheus:v2.52.0` | Metrics collection, 90-day retention |
| Grafana | `grafana/grafana:10.2.2` | Dashboards & alerts |
| cAdvisor | `gcr.io/cadvisor/cadvisor:v0.47.2` | Docker container metrics |
| node-exporter | `prom/node-exporter:latest` | Host CPU, RAM, disk metrics |

Prometheus also scrapes Caddy (`caddy:2019/metrics`) if you're running it on the same `monitoring` network.

## Setup

**1. Create the external Docker network** (shared with other services on the host)

```bash
docker network create monitoring
```

**2. Create the metrics storage directory**

```bash
mkdir -p /mnt/data_storage/prometheus_data
```

Fix permissions for Prometheus (runs as UID 65534):

```bash
sudo chown -R 65534:65534 /mnt/data_storage/prometheus_data
```

**3. Configure Grafana alerts** — edit `grafana.ini` and fill in your Gmail details. Use an [App Password](https://myaccount.google.com/apppasswords), not your main password. The password must have no spaces.

```ini
user = you@gmail.com
password = yourapppassword
from_address = you@gmail.com
```

**4. Set Grafana admin password** — change `GF_SECURITY_ADMIN_PASSWORD` in `docker-compose.yml`.

> ⚠️ Hardcoding credentials in `docker-compose.yml` is not ideal. Better options include using a `.env` file (excluded from git) or Docker secrets with the `GF_SECURITY_ADMIN_PASSWORD__FILE` approach.

**5. Start everything**

```bash
docker compose up -d
```

Open `localhost:3535` → login with `admin` / your password.

## Dashboards

For Docker container metrics, import dashboard ID **893** from Grafana's dashboard library.

For custom panels, here are the PromQL queries I use:

```promql
# RAM usage %
(1 - (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes)) * 100

# CPU usage %
100 - (avg by(instance)(irate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
```

## Files in this repo

- `docker-compose.yml` — full stack definition
- `prometheus.yml` — scrape config for all targets
- `grafana.ini` — SMTP config for email alerts
- `JSON model` — export of the working Grafana dashboard
- `Export Admins rules` — export of the working alert rules

## Troubleshooting

**Prometheus keeps restarting with `permission denied` on queries.active**

```bash
sudo chown -R 65534:65534 /mnt/data_storage/prometheus_data
docker compose down && docker compose up -d
```
