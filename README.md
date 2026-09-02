Traccar Server Documentation (Operations & Architecture)
========================================================

This repository is a curated, production-focused set of notes for running and operating the Traccar GPS tracking platform. It summarizes how the platform works, how to install it, and where to find more detailed guidance for day-to-day operations and troubleshooting.

Traccar itself is an open-source GPS tracking stack with a Java server (Netty/Jetty), a React web app, and mobile clients. It supports a large catalog of hardware GPS trackers plus the Traccar Client mobile app, and can be deployed on-prem or in the cloud.

Attribution and license
-----------------------
Most of the per-topic pages in this repository are copied from the official
[Traccar documentation](https://www.traccar.org/documentation/), Copyright (c)
Anton Tananaev and Traccar contributors, Apache License 2.0. Credit for that
material belongs entirely to the [Traccar project](https://github.com/traccar/traccar).
The architecture summary, install guide, runbook, and MkDocs site are my own
additions and are released under the same Apache License 2.0. See `NOTICE` for
the file-level breakdown and `LICENSE` for the license text. This is an
unofficial companion and is not affiliated with Traccar.

What this repository covers
---------------------------
- High-level architecture: Netty pipeline, protocol handling, database layer, API, and web app (see `ARCHITECTURE.md`).
- Installation options: native packages, Docker images, and platform notes (see `INSTALL.md`).
- Configuration and operations: server configuration (`configurationfile.md`), databases (`mysqldatabase.md`, `postgresql.md`), and scaling (`horizontalscaling.md`).
- Troubleshooting and platform guides: device support, notifications, security, and more (see the documents in this directory).

Quick start
-----------
- Docker: `docker run --name traccar --restart unless-stopped -p 80:8082 -p 5000-5300:5000-5300 -p 5000-5300:5000-5300/udp traccar/traccar:latest` (see `docker.md` for volumes and production database options).
- Native installer (Linux/systemd): download the OS package, run `sudo ./traccar.run`, then `sudo systemctl start traccar` (see `linux.md`).
- First login: open `http://<server-ip>:8082` in a browser or Traccar Manager. Register the first user (becomes admin on new versions) and add a device using its unique identifier/IMEI (`quickstart.md`).

Technology and entry points
---------------------------
- Server: Java application built on Netty (protocol servers), Jetty (HTTP/WS), Liquibase (database migrations), and JDBC drivers for the selected SQL database.
- Web: React + Material UI single-page app served as static assets by the server.
- Ports: 8082 for HTTP/websocket by default; device protocol ports vary by hardware (`protocollist.md`).
- Build: `./gradlew assemble` for the server, plus a separate build for the modern or legacy web app (`build.md`).

Repository map
--------------
- `ARCHITECTURE.md` – end-to-end architecture and data flow.
- `INSTALL.md` – practical install paths (installer, Docker, cloud).
- `configurationfile.md` – primary server configuration reference.
- `docker.md`, `linux.md`, `install-digitalocean.md` – platform-specific install notes.
- `secureconnection.md` – reverse proxy and TLS termination options.
- `mysqldatabase.md`, `postgresql.md`, `microsql.md` – database configuration.
- `quickstart.md`, `clienttroubleshooting.md`, `severtroubleshooting.md` – onboarding and troubleshooting.
- `servertroubleshooting.md` – normalized name for server troubleshooting (legacy `severtroubleshooting.md` kept as an alias).
- `supporteddevices.md`, `protocollist.md` – hardware and protocol coverage.
- `optimization.md` – normalized name for optimization guide (legacy `optimzation.md` kept as an alias).
- `OPERATIONS_RUNBOOK.md` – quick operational responses mapped to relevant guides.
  - Includes observability and CI/CD health-check suggestions; see `optimization.md` for automation and IaC snippets.
  - Grafana/Prometheus alert samples and dashboard skeletons included.
  - Prometheus burn-rate alerts and paths for rule/dashboard artifacts.
- `CHANGELOG.md` – documented changes to this repository.

Use cases
---------
- Commercial and personal fleet tracking, public transit, delivery/taxi, agriculture, marine/aviation, personal and animal tracking, alarms, and driver/fuel monitoring.
