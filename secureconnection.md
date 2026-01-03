Secure Connection
By default, Traccar serves its web interface and API over the standard HTTP protocol, which does not provide encryption. This guide explains how to configure Traccar to use HTTPS with SSL/TLS encryption for secure traffic. While the examples focus on Ubuntu Linux, the same approach applies to other platforms.

Traccar does not natively support secure connections, but you can enable them by running it behind a proxy server. In this guide, we will use the Caddy server.

To obtain a valid SSL certificate, you need a registered domain name. See our documentation on how to register and configure a custom domain name for Traccar. If you don't yet own a domain, we recommend Namesilo, which offers consistently low prices for both new registrations and renewals.

First, install the latest version of Caddy. The commands below are for Ubuntu and Debian. For other platforms, refer to the Caddy installation documentation.

sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https curl
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
chmod o+r /usr/share/keyrings/caddy-stable-archive-keyring.gpg
chmod o+r /etc/apt/sources.list.d/caddy-stable.list
sudo apt update
sudo apt install caddy
Next, update the Caddy configuration file:

sudo nano /etc/caddy/Caddyfile
Paste the following content into the file, replacing demo.traccar.org with your domain:

demo.traccar.org {
  reverse_proxy localhost:8082
}
:80 {
  reverse_proxy localhost:8082
}
Restart the Caddy service:

sudo systemctl restart caddy
If your domain is configured correctly, Caddy will automatically obtain and install an SSL certificate for it using Let's Encrypt.

Alternative reverse proxies
---------------------------
The key requirements for any reverse proxy are:
- Terminate TLS for `https://` on port 443 and redirect HTTP to HTTPS.
- Proxy both REST and WebSocket traffic to Traccar on port 8082.
- Preserve headers (`Host`, `Upgrade`, `Connection`) so WebSockets stay stable.

Nginx example
-------------
```nginx
server {
  listen 80;
  server_name demo.traccar.org;
  return 301 https://$host$request_uri;
}

server {
  listen 443 ssl http2;
  server_name demo.traccar.org;

  ssl_certificate /etc/letsencrypt/live/demo.traccar.org/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/demo.traccar.org/privkey.pem;

  location / {
    proxy_pass http://127.0.0.1:8082;
    proxy_http_version 1.1;
    proxy_set_header Host $host;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection $connection_upgrade;
  }
}
```
Generate certificates with Certbot (standalone or via DNS) before enabling the TLS server block.

Traefik (Docker) example
------------------------
```yaml
services:
  traefik:
    image: traefik:v3.1
    command:
      - "--entrypoints.web.address=:80"
      - "--entrypoints.websecure.address=:443"
      - "--providers.docker=true"
      - "--certificatesresolvers.le.acme.httpchallenge=true"
      - "--certificatesresolvers.le.acme.httpchallenge.entrypoint=web"
      - "--certificatesresolvers.le.acme.email=admin@example.com"
      - "--certificatesresolvers.le.acme.storage=/letsencrypt/acme.json"
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock:ro"
      - "letsencrypt:/letsencrypt"

  traccar:
    image: traccar/traccar:6.11.1-alpine
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.traccar.rule=Host(`demo.traccar.org`)"
      - "traefik.http.routers.traccar.entrypoints=websecure"
      - "traefik.http.routers.traccar.tls.certresolver=le"
      - "traefik.http.services.traccar.loadbalancer.server.port=8082"

volumes:
  letsencrypt:
```
Traefik handles HTTPS and ACME automatically; the Traccar container stays unchanged.

Hardening tips
--------------
- Keep 8082 unexposed to the internet when using a reverse proxy; publish only 80/443.
- Use HSTS after confirming HTTPS works: `add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload" always;`
- Rotate ACME contact email and ensure certificate storage is persisted (volumes).
- Limit `trusted-proxies` and allowed IP ranges in your reverse proxy if you expose device protocol ports separately.
- Consider basic rate limiting and IP allowlists on the proxy edges to protect API and device ports.

Nginx rate limit and allowlist example
--------------------------------------
```nginx
limit_req_zone $binary_remote_addr zone=api_limit:10m rate=10r/s;

server {
  listen 443 ssl http2;
  server_name demo.traccar.org;
  # ssl_* directives omitted for brevity

  # Allowlist a management IP range; drop early if not matched
  allow 203.0.113.0/24;
  deny all;

  location / {
    limit_req zone=api_limit burst=20 nodelay;
    proxy_pass http://127.0.0.1:8082;
    proxy_http_version 1.1;
    proxy_set_header Host $host;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection $connection_upgrade;
  }
}
```
Tune rates for your workload; use a separate zone if device ports are proxied through Nginx.

Firewall and Security Group examples
------------------------------------
- UFW (Ubuntu): allow HTTPS, block everything else by default, and open selected device ports:
  ```
  sudo ufw default deny incoming
  sudo ufw default allow outgoing
  sudo ufw allow 80,443/tcp
  sudo ufw allow 5000:5300/tcp
  sudo ufw allow 5000:5300/udp
  sudo ufw enable
  ```
- AWS Security Group: allow `0.0.0.0/0` on 80/443 for the proxy; restrict device protocol ports (e.g., 5000-5300 TCP/UDP) to known source CIDRs where possible instead of open internet. Keep SSH limited to trusted admin IPs.
- Secret rotation: when rotating DB or notification provider credentials, use your secret store and restart/redeploy Traccar with updated env/`traccar.xml` values; avoid embedding secrets directly in images or IaC outputs. Automate rotation via your cloud KMS/secret manager and propagate to containers through mounts/env files.
