Linux Installation
Linux package should work with most Linux distributions that use systemd. Currently x64 (x86_64) and ARM versions of the installer are available.

If you are using Ubuntu, check the Installation on DigitalOcean guide for more detailed installation instructions and a video.

Install
Download and extract the installer package
Execute traccar.run file
sudo ./traccar.run
Start systemd service
sudo systemctl start traccar
Uninstall
Stop systemd service
sudo systemctl stop traccar
Remove systemd service
sudo systemctl disable traccar
sudo rm /etc/systemd/system/traccar.service
sudo systemctl daemon-reload
Remove traccar directory
sudo rm -R /opt/traccar
By default Traccar is executed with root user privileges. If you want to run it as a regular user, check run as non-root documentation.

Run as non-root (systemd)
-------------------------
The installer creates a root-owned service. To drop privileges:
1) Create a dedicated user and group:
   `sudo useradd --system --home /opt/traccar --shell /usr/sbin/nologin traccar`
2) Move ownership of the install directories:
   `sudo chown -R traccar:traccar /opt/traccar`
3) Add a systemd drop-in at `/etc/systemd/system/traccar.service.d/override.conf`:
```
[Service]
User=traccar
Group=traccar
AmbientCapabilities=
CapabilityBoundingSet=
NoNewPrivileges=yes
```
4) Reload and restart:
   `sudo systemctl daemon-reload && sudo systemctl restart traccar`

Notes:
- Default ports (8082, 5000+) are above 1024, so no extra capabilities are required. If you bind to lower ports, add `AmbientCapabilities=CAP_NET_BIND_SERVICE`.
- Confirm logs and data directories remain writable after the ownership change.
