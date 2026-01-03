Traccar Installation
====================

Supported approaches
--------------------
- **Docker (recommended for portability)** – Official images on Docker Hub (`traccar/traccar`) with Alpine, Debian, or Ubuntu bases. See `docker.md` for tags, volume mounts, and production database options.
- **Native installer (Linux/systemd)** – Download the `.run` package, execute it as root, and manage with `systemctl`. See `linux.md` for commands and removal steps.
- **Cloud reference** – A step-by-step Ubuntu installation example is in `install-digitalocean.md`.
- **From source** – Build the server with `./gradlew assemble` and the web app separately; swap the generated artifacts into an existing installation (`build.md`).

Prerequisites
-------------
- A public IP (or properly forwarded ports) so devices can reach the server on their protocol ports.
- Open ports: 8082 for HTTP/WS UI/API; device ports listed in `protocollist.md`.
- SQL database for production (H2 is bundled for testing only). MySQL and PostgreSQL/TimescaleDB are the common choices; configure via `conf/traccar.xml` (`mysqldatabase.md`, `postgresql.md`).

Docker quick start
------------------
```
docker run \
  --name traccar \
  --hostname traccar \
  --detach --restart unless-stopped \
  --publish 80:8082 \
  --publish 5000-5300:5000-5300 \
  --publish 5000-5300:5000-5300/udp \
  traccar/traccar:latest
```
For persistence, mount `logs`, `conf/traccar.xml`, and `data` from the host. Swap `latest` for a pinned tag (e.g., `6.11.1-alpine`) to align with your rollout policy.

Linux installer quick start
---------------------------
1) Download the installer for your architecture.  
2) Run `sudo ./traccar.run`.  
3) Start the service: `sudo systemctl start traccar`.  
4) Access the UI at `http://<server-ip>:8082`.  
See `linux.md` for uninstall steps and running as non-root.

Post-install checks
-------------------
- Create the first user (becomes admin on recent versions) or use provided credentials; see `quickstart.md`.
- Register a device using its unique identifier/IMEI; confirm data arrival in the server logs or via the web UI.
- Configure email/SMS/FCM credentials before enabling notifications (`notification.md`).
- Back up `conf/traccar.xml` and database credentials; ensure log/data volumes are persisted in container setups.
