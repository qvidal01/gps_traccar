Changelog
=========

Unreleased
----------
- Added production-oriented README with quick start, architecture summary, and documentation map.
- Created INSTALL guide consolidating Docker, native installer, and post-install validation steps.
- Refined architecture overview to clarify Netty pipelines, notification channels, data layer, and web stack.
- Documented repository maintenance notes and outstanding follow-ups in `IMPLEMENTATION_NOTES.md`.
- Added Docker Compose templates for MySQL and TimescaleDB deployments in `docker.md`.
- Extended TLS and reverse-proxy guidance with Nginx and Traefik examples in `secureconnection.md`.
- Documented a systemd non-root service override for Linux installations in `linux.md`.
- Included matching `traccar.xml` JDBC snippets for the Compose stacks in `docker.md`.
- Added reverse-proxy rate limiting, allowlist, and firewall/SG examples to `secureconnection.md`.
- Normalized misspelled docs (`optimization.md`, `servertroubleshooting.md`) while retaining legacy filenames for compatibility.
- Added JDBC snippets for MSSQL/Oracle to align with managed DB deployments in `docker.md`.
- Added an upgrade checklist to `upgradingtraccar.md`.
- Added Liquibase validation notes for managed databases in `docker.md`.
- Added data retention/archival guidance to `optimization.md`.
- Created `OPERATIONS_RUNBOOK.md` mapping common alerts to troubleshooting steps.
- Added chunked retention SQL and Timescale policies to `optimization.md`.
- Documented MySQL/PostgreSQL backup/restore workflows and post-upgrade validation steps in `upgradingtraccar.md`.
- Added observability guidance, CI/CD health checks, and automation examples to `OPERATIONS_RUNBOOK.md` and `optimization.md`.
- Added Prometheus/Grafana alert ideas, IaC starters for SG/backups, and secret rotation notes.
- Added secret-store wiring examples (Compose/Kubernetes) and Azure/GCP IaC snippets to `optimization.md`.
- Added Prometheus scrape config and Grafana panel guidance to `OPERATIONS_RUNBOOK.md`.
- Added cloud secret-store wiring (Azure Key Vault, GCP Secret Manager), alert rules, and rotation guidance references.
- Added exporter configs (node exporter, cAdvisor), direct secret wiring examples, and credential rotation playbooks.
- Added Cloud Run/Container Apps secret-mount specifics, exporter deployment hints, and SLI alert rule examples.
- Added SLI/SLO tuning guidance and exporter placement notes for managed runtimes.
- Added service reload templates, VCS locations for Prometheus/Grafana artifacts, and managed-runtime exporter patterns.
- Added Cloud Run/Azure Container Apps deployment snippets with secrets and managed metrics integration, plus burn-rate alert examples.
