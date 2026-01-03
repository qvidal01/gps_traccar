Operations Runbook
==================

Purpose
-------
Quick reference for common operational alerts and where to investigate in this documentation set.

Common alerts and actions
-------------------------
- **HTTP/API down or 5xx spike**  
  - Check reverse proxy/TLS (`secureconnection.md`), confirm container/service is running, inspect logs in `/opt/traccar/logs`.
- **Devices not connecting**  
  - Verify protocol ports open/forwarded (firewall/SG guidance in `secureconnection.md`), see `servertroubleshooting.md` (`severtroubleshooting.md` legacy) for decoding and port issues.
- **No positions or decoding errors**  
  - Inspect logs for unknown device or decode errors; confirm identifiers and protocol port (`servertroubleshooting.md`, `protocollist.md`).
- **High CPU/memory or connection exhaustion**  
  - Apply tuning in `optimization.md` (nofile limits, timeouts), scale vertically or add replicas (`horizontalscaling.md`).
- **Notification failures (email/SMS/push)**  
  - Verify credentials and provider reachability, review `notification.md` for channel configuration.
- **Database growth/storage pressure**  
  - Apply retention guidance in `optimization.md`, consider TimescaleDB policies; validate Liquibase migrations before upgrades (`docker.md`).

Observability (logs/metrics/alerts)
-----------------------------------
- Logs: ingest `/opt/traccar/logs` into your log system; alert on ERROR spikes and decode/DB connectivity failures.
- Metrics: collect host/container metrics (CPU, memory, disk, file descriptors) plus database health; alert on abnormal connection counts and disk growth.
- Uptime: monitor HTTP 8082 (or behind proxy 80/443) and WebSocket connect success; alert on elevated latency or handshake failures.

Prometheus/Grafana examples
---------------------------
- Scrape host/container metrics (node exporter/cAdvisor) and database exporters.
- Scrape config example (Prometheus):
  ```yaml
  scrape_configs:
    - job_name: node
      static_configs: [{ targets: ['node-exporter:9100'] }]
    - job_name: cadvisor
      static_configs: [{ targets: ['cadvisor:8080'] }]
    - job_name: db
      static_configs: [{ targets: ['db-exporter:9187'] }]
  ```
- Sample Grafana panels: HTTP 5xx rate, WebSocket failure rate, JVM/container memory, fd usage, DB connections/lag, disk forecast, ingest rate (derived from logs/metrics).
- Suggested alerts (tune to your scale):
  - HTTP 5xx rate > 1% for 5m on UI/API endpoint.
  - WebSocket handshake failures > 5/min for 5m.
  - JVM/container memory > 85% for 10m; fd usage > 80%.
  - Database connection saturation > 80%; replication lag (if used) > 30s.
  - Disk fill prediction < 7 days.
- Dashboards: include device ingest rate, protocol port error counts (from logs), API latency, notification success/fail, DB connections, disk IO.

Exporter configs (Docker)
-------------------------
- node exporter:
  ```yaml
  node-exporter:
    image: prom/node-exporter:v1.8.1
    network_mode: host
    pid: host
    restart: unless-stopped
    command:
      - "--path.rootfs=/host"
    volumes:
      - "/:/host:ro,rslave"
  ```
- cAdvisor:
  ```yaml
  cadvisor:
    image: gcr.io/cadvisor/cadvisor:v0.47.2
    restart: unless-stopped
    ports: ["8080:8080"]
    volumes:
      - "/:/rootfs:ro"
      - "/var/run:/var/run:ro"
      - "/sys:/sys:ro"
      - "/var/lib/docker/:/var/lib/docker:ro"
  ```
- Database exporter: use `postgres_exporter` or `mysqld_exporter` and point Prometheus to the exposed port.

Prometheus alert rule samples
-----------------------------
```yaml
groups:
  - name: traccar
    rules:
      - alert: TraccarHttp5xx
        expr: sum(rate(http_requests_total{job="traefik",code=~"5.."}[5m])) / sum(rate(http_requests_total{job="traefik"}[5m])) > 0.01
        for: 5m
        labels: { severity: page }
        annotations:
          summary: "High 5xx rate on Traccar"
      - alert: TraccarWsFailures
        expr: rate(traccar_ws_handshake_failures_total[5m]) > 5
        for: 5m
        labels: { severity: page }
        annotations:
          summary: "WebSocket handshakes failing"
      - alert: TraccarHighFDUsage
        expr: (process_open_fds / process_max_fds) > 0.8
        for: 10m
        labels: { severity: warn }
        annotations:
          summary: "File descriptor usage high"
      - alert: TraccarDbConnectionsHigh
        expr: avg_over_time(db_max_connections_used[5m]) / db_max_connections > 0.8
        for: 5m
        labels: { severity: warn }
        annotations:
          summary: "DB connections near saturation"
```

Grafana dashboard JSON
----------------------
Store dashboard JSON in VCS for reproducibility. Example skeleton:
```json
{
  "title": "Traccar Overview",
  "panels": [
    { "type": "graph", "title": "HTTP 5xx %", "targets": [{ "expr": "sum(rate(http_requests_total{job='traefik',code=~'5..'}[5m])) / sum(rate(http_requests_total{job='traefik'}[5m]))" }] },
    { "type": "graph", "title": "WS failures", "targets": [{ "expr": "rate(traccar_ws_handshake_failures_total[5m])" }] },
    { "type": "graph", "title": "Ingest rate", "targets": [{ "expr": "rate(traccar_positions_ingested_total[5m])" }] },
    { "type": "graph", "title": "DB connections", "targets": [{ "expr": "db_max_connections_used" }] },
    { "type": "stat",  "title": "Disk fill (days)", "targets": [{ "expr": "predict_linear(node_filesystem_free_bytes[6h], 86400) / 1e9" }] }
  ]
}
```
Adjust metrics/labels to match your exporters; version dashboards with your IaC.

Sample alert rules file (Prometheus)
------------------------------------
```yaml
groups:
  - name: traccar-sli
    rules:
      - alert: TraccarHttpLatencyP95High
        expr: histogram_quantile(0.95, sum(rate(http_request_duration_seconds_bucket{job="traefik"}[5m])) by (le)) > 1
        for: 5m
        labels: { severity: warn }
        annotations:
          summary: "P95 HTTP latency > 1s"
      - alert: TraccarPositionIngestDrop
        expr: rate(traccar_positions_ingested_total[5m]) < 1
        for: 10m
        labels: { severity: page }
        annotations:
          summary: "No positions ingested in the last 10m"
      - alert: TraccarNotificationFailures
        expr: rate(traccar_notification_failures_total[5m]) > 5
        for: 5m
        labels: { severity: warn }
        annotations:
          summary: "Notification failures elevated"
```

SLI/SLO guidance and tuning
---------------------------
- Suggested starting SLOs (adjust per scale and use case):
  - Availability: 99.5% monthly for API/UI (HTTP 2xx/3xx success).
  - Latency: p95 HTTP < 1s, p99 < 2s for core endpoints.
  - Ingest freshness: 99% of device positions arrive within 2x expected reporting interval.
  - Notification delivery: 99% success within 1 minute for configured channels.
- Tuning alerts by scale:
  - Small deployments: raise thresholds (e.g., 5xx > 5%) to avoid flapping on low volume; lengthen `for` durations.
  - Large deployments: lower thresholds and tighten windows; consider multi-window, multi-burn-rate alerts for SLOs.
  - Use burn-rate alerts for availability/latency SLOs to balance fast detection and noise.

Full artifacts (store in VCS)
-----------------------------
- Prometheus rules file: use `prometheus-rules/traccar.yml` with the alert groups above and any custom labels.
- Grafana dashboards: store JSON (e.g., `grafana/dashboards/traccar-overview.json`) matching exporter metric names used here.
- Keep metric naming consistent: if using traefik/nginx ingress metrics, align `job`/`service` labels to match the alert queries.
- Example Prometheus burn-rate alerts (multi-window):
  ```yaml
  groups:
    - name: traccar-slo-burn
      rules:
        - alert: TraccarAvailabilityFastBurn
          expr: (sum(rate(http_requests_total{code=~"5..",job="traefik"}[5m])) / sum(rate(http_requests_total{job="traefik"}[5m]))) > (0.01 * 14.4)
          for: 5m
          labels: { severity: page }
          annotations: { summary: "Fast burn on availability SLO" }
        - alert: TraccarAvailabilitySlowBurn
          expr: (sum(rate(http_requests_total{code=~"5..",job="traefik"}[1h])) / sum(rate(http_requests_total{job="traefik"}[1h]))) > (0.01 * 2)
          for: 2h
          labels: { severity: warn }
          annotations: { summary: "Slow burn on availability SLO" }
  ```
  Adjust error budget (0.5% monthly -> 0.01) and factors per your SLO.

CI/CD health checks
-------------------
- Linting equivalent: run `curl -f http://localhost:8082/api/server` against a staging instance after deploy; expect 200 and valid JSON.
- WebSocket probe: open a WebSocket to `/api/socket` from a script and verify handshake succeeds.
- Smoke device ingest: post a known-good protocol sample to a staging protocol port and assert it is decoded (parse logs or API).

References
----------
- Install/deploy: `INSTALL.md`, `docker.md`, `linux.md`
- Security/TLS: `secureconnection.md`
- Troubleshooting: `clienttroubleshooting.md`, `servertroubleshooting.md` (`severtroubleshooting.md`)
- Upgrades: `upgradingtraccar.md`
