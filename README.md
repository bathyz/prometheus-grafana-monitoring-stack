# Prometheus + Grafana Monitoring Stack

A self-contained observability stack — Prometheus, node-exporter,
Alertmanager, and Grafana — wired together with `docker-compose` so it comes
up pre-configured: Grafana already has Prometheus added as a datasource and
a dashboard provisioned, no manual clicking required.

## Stack

| Service | Port | Role |
|---|---|---|
| Prometheus | 9090 | Scrapes metrics, evaluates alert rules |
| node-exporter | 9100 | Exposes host-level metrics (CPU, memory, disk) |
| Alertmanager | 9093 | Routes/groups firing alerts |
| Grafana | 3000 | Dashboards, pre-provisioned datasource + dashboard |

## Running it

```bash
docker compose up -d
```

Then open:
- Grafana: http://localhost:3000 (`admin` / `admin`, change on first login)
- Prometheus: http://localhost:9090
- Alertmanager: http://localhost:9093

The **Node Health** dashboard in Grafana shows CPU %, memory %, disk free %,
and a count of healthy scrape targets — loaded automatically from
`grafana/dashboards/node-health.json` via the provisioning config in
`grafana/provisioning/`.

## Alerting

`prometheus/alert.rules.yml` defines four rules: `HighCpuUsage` (>85% for
5m), `HighMemoryUsage` (>90% for 5m), `LowDiskSpace` (<10% free for 10m),
and `InstanceDown` (any scrape target unreachable for 2m). They fire into
Alertmanager, which is configured with a placeholder receiver — swap in a
real Slack/PagerDuty/email receiver in `alertmanager/alertmanager.yml` for
actual notifications (see the commented example in that file).

## Why this project

Anyone can `docker run` Grafana. The point here is the provisioning layer —
datasources and dashboards defined as files and loaded automatically on
container start, so the whole stack is reproducible from git instead of
depending on manual UI setup that gets lost the moment the container is
recreated.

## License

MIT
