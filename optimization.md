Optimization
(Alias of `optimzation.md`; use this normalized filename going forward.)
This page provides information on configuring Traccar and operating system to work with a large number of devices and users.

In addition to the server optimization, we recommend a regular clean up of old location history and logs. Check clear history page for more details on how to configure it.

Increasing connection limit on Linux
You can check hard and soft limits for current user using the following command:

cat /proc/$(pidof java)/limits | grep "open files"
On most systems by default the limit is 1024 connections.

To increase the limit add the following lines to /etc/security/limits.conf:

* soft  nofile 50000
* hard  nofile 50000
On some versions of Ubuntu there is an issue and you need to do the following:

Edit /etc/systemd/user.conf and add DefaultLimitNOFILE=50000
Edit /etc/systemd/system.conf and add DefaultLimitNOFILE=50000
Make sure you use a number higher than your number of devices because when device reconnects it might consume two or even more connection for some period of time.

System restart is required for the chagnes to take affect.

Scaling beyond 65k connections
After following instructions above you might find that connection limit doesn't increase past 65k or even smaller number. This is because of the vm.max_map_count variable.

To fix the issue you need to modify "/etc/sysctl.conf":

vm.max_map_count = 250000
fs.file-max = 250000
net.ipv4.ip_local_port_range = 1024 65535
The first two parameters allow to increase total limit of connections to 250k. The last parameter is important to increase the local port range, which would allow more connections on a single port. By default the range is usually around 32k.

Don't forget to restart the system after modifying the file.

Setting connection timeout in Traccar
Operating systems have a timeout for all TCP connections, but it's usually very high. For example, on Linux it's common to have a 2 hours timeout by default. It means that if your device re-connects without gracefully closing connection, then it will leave a stale connection on the server that consumes server resources and is counted against the total connection limit. When network connection is poor, a single device can easily create tens or even hundreds of connections within 2 hour period.

To avoid the problem, it is recommended to set the connection timeout in Traccar server. You can use server.timeout or protocol.timeout option in the config file. It is recommended to set the value (in seconds) to slightly higher than your device reporting interval.

Data retention and archiving
----------------------------
- Define retention by workload: for high-frequency devices, consider 30–90 days online; archive older history to cheaper storage (e.g., S3/object storage) if needed for compliance.
- MySQL/PostgreSQL: periodically delete or move old `positions` rows by date; run during low-traffic windows and batch in small chunks to avoid locks.
- TimescaleDB: use hypertables and drop/retain chunks with `add_retention_policy` for predictable disk usage.
- Always back up before bulk deletes; monitor disk growth and index bloat after retention jobs.

MySQL chunked delete example
----------------------------
Delete in batches to avoid long locks:
```sql
DELETE FROM positions
WHERE id IN (
  SELECT id FROM positions
  WHERE fixtime < NOW() - INTERVAL 90 DAY
  ORDER BY fixtime
  LIMIT 1000
);
```
Run repeatedly (cron) until no rows are removed. Adjust the limit for your workload.

PostgreSQL chunked delete example
---------------------------------
```sql
WITH cte AS (
  SELECT id FROM positions
  WHERE fixtime < NOW() - INTERVAL '90 days'
  ORDER BY fixtime
  LIMIT 1000
)
DELETE FROM positions p USING cte WHERE p.id = cte.id;
```

TimescaleDB retention policy
----------------------------
Assuming `positions` is a hypertable with `fixtime` as the time column:
```sql
SELECT add_retention_policy('positions', INTERVAL '90 days');
```
For rolling historical archives, use `add_continuous_aggregate_policy` or export old chunks before dropping. Validate policies in staging before applying to production.

Automation examples
-------------------
- Retention: run the chunked delete SQL via cron during low-traffic windows, e.g., `/usr/bin/psql ... -f prune_positions.sql` or `/usr/bin/mysql ... < prune_positions.sql`.
- Backups: schedule nightly backups with rotation, e.g., a systemd timer or cron entry:
  ```
  0 2 * * * /usr/bin/mysqldump -h mysql -u traccar -p"$MYSQL_PASSWORD" traccar | gzip > /backups/traccar-$(date +\%F).sql.gz
  find /backups -name "traccar-*.sql.gz" -mtime +7 -delete
  ```
- Monitor job success: send logs to your central system and alert on non-zero exit codes.

Terraform/IaC starter snippets
------------------------------
- AWS Security Group (restrict protocol ports, open 80/443):
  ```hcl
  resource "aws_security_group" "traccar" {
    name        = "traccar-sg"
    description = "Traccar SG"
    vpc_id      = var.vpc_id

    ingress { from_port = 80   to_port = 80   protocol = "tcp" cidr_blocks = ["0.0.0.0/0"] }
    ingress { from_port = 443  to_port = 443  protocol = "tcp" cidr_blocks = ["0.0.0.0/0"] }
    ingress { from_port = 5000 to_port = 5300 protocol = "tcp" cidr_blocks = var.device_cidrs }
    ingress { from_port = 5000 to_port = 5300 protocol = "udp" cidr_blocks = var.device_cidrs }
    egress  { from_port = 0    to_port = 0    protocol = "-1"  cidr_blocks = ["0.0.0.0/0"] }
  }
  ```
- Scheduled backup (AWS Backup targeting an RDS instance):
  ```hcl
  resource "aws_backup_vault" "traccar" { name = "traccar-backup" }

  resource "aws_backup_plan" "traccar" {
    name = "traccar-plan"
    rule {
      rule_name         = "daily"
      target_vault_name = aws_backup_vault.traccar.name
      schedule          = "cron(0 3 * * ? *)"
      lifecycle { delete_after = 14 }
    }
  }

  resource "aws_backup_selection" "traccar" {
    name         = "traccar-selection"
    plan_id      = aws_backup_plan.traccar.id
    iam_role_arn = var.backup_role_arn
    resources    = ["arn:aws:rds:region:account:db:traccar-db"]
  }
  ```
Adapt these snippets to your cloud/provider; ensure creds/secrets are managed via your secret store.

Secret store wiring (containers)
--------------------------------
- AWS SSM Parameter Store -> env var:
  - Task definition or Compose: `ValueFrom` (ECS) or `secrets` in Compose pulling from SSM/Secrets Manager.
  - Example Compose snippet:
    ```yaml
    services:
      traccar:
        image: traccar/traccar:6.11.1-alpine
        secrets:
          - db_password
        environment:
          DATABASE_PASSWORD_FILE: /run/secrets/db_password
    secrets:
      db_password:
        external: true
    ```
    Create the secret externally (e.g., `aws secretsmanager create-secret --name traccar-db-password --secret-string '...'`).
- Kubernetes: mount secrets as files or env; point `DATABASE_PASSWORD_FILE` to the mounted path to avoid inline values in manifests.
- Azure Key Vault (Linux container):
  - Use `Azure.Identity`-based sidecar or `azure-keyvault-secrets-provider` CSI driver in AKS to mount secrets as files (e.g., `/mnt/secrets/db-password`).
  - Point `DATABASE_PASSWORD_FILE` to the mounted secret file; keep Key Vault access via managed identity.
- GCP Secret Manager (GKE/containers):
  - Use `gke-secrets-store-csi-driver` to mount secrets as files, or pull via workload identity and write to a temp file.
  - Example K8s env-from-file:
    ```yaml
    env:
      - name: DATABASE_PASSWORD_FILE
        value: /var/secrets/db-password
    volumeMounts:
      - name: db-secret
        mountPath: /var/secrets
        readOnly: true
    volumes:
      - name: db-secret
        secret:
          secretName: traccar-db-password
    ```
    Populate the K8s secret from Secret Manager via CSI or pipeline sync.
- Direct env injection examples:
  - Azure Container Apps: set `secrets` from Key Vault and map to `DATABASE_PASSWORD` env; switch the app config to `DATABASE_PASSWORD_FILE` with a mounted path when supported.
  - GCP Cloud Run: mount Secret Manager secrets as volumes (`/secrets/db-password`) and set `DATABASE_PASSWORD_FILE=/secrets/db-password`.
  - Cloud Run: use `--set-secrets DATABASE_PASSWORD=traccar-db-password:latest` with `_FILE` pattern not directly supported; instead mount to `/var/secrets` and set `DATABASE_PASSWORD_FILE=/var/secrets/DATABASE_PASSWORD`.

Non-AWS IaC starters
--------------------
- Azure NSG (80/443 open, device ports restricted):
  ```hcl
  resource "azurerm_network_security_group" "traccar" {
    name                = "traccar-nsg"
    location            = azurerm_resource_group.rg.location
    resource_group_name = azurerm_resource_group.rg.name

    security_rule {
      name                       = "http"
      priority                   = 100
      direction                  = "Inbound"
      access                     = "Allow"
      protocol                   = "Tcp"
      source_port_range          = "*"
      destination_port_range     = "80"
      source_address_prefix      = "*"
      destination_address_prefix = "*"
    }

    security_rule {
      name                       = "https"
      priority                   = 110
      direction                  = "Inbound"
      access                     = "Allow"
      protocol                   = "Tcp"
      source_port_range          = "*"
      destination_port_range     = "443"
      source_address_prefix      = "*"
      destination_address_prefix = "*"
    }

    security_rule {
      name                       = "device-tcp"
      priority                   = 120
      direction                  = "Inbound"
      access                     = "Allow"
      protocol                   = "Tcp"
      source_port_range          = "*"
      destination_port_ranges    = ["5000-5300"]
      source_address_prefix      = var.device_cidrs
      destination_address_prefix = "*"
    }
  }
  ```
- GCP firewall (ingress for 80/443 and device ports):
  ```hcl
  resource "google_compute_firewall" "traccar" {
    name    = "traccar-fw"
    network = var.network

    allow { protocol = "tcp" ports = ["80", "443"] }
    allow { protocol = "tcp" ports = ["5000-5300"] }
    allow { protocol = "udp" ports = ["5000-5300"] }
    source_ranges = ["0.0.0.0/0"]
  }
  ```

Credential rotation playbooks
-----------------------------
- Databases:
  1) Rotate the password in your secret store (Secrets Manager/Key Vault/Secret Manager).
  2) Update DB user credential.
  3) Redeploy/restart Traccar with updated secret mount/env (`docker compose restart traccar` or `systemctl restart traccar`).
  4) Validate API and ingest health post-rotation.
- Notification providers:
  1) Rotate provider API keys in the secret store.
  2) Update `traccar.xml` (or env) to reference the new secret path/file.
  3) Restart service; send a test notification to confirm.
- Service reload/restart templates:
  - systemd: `sudo systemctl daemon-reload && sudo systemctl restart traccar`
  - Docker Compose: `docker compose up -d traccar` (picks up new secrets/mounts)
  - Kubernetes: `kubectl rollout restart deploy/traccar` (ensure secrets are updated)
  - Windows service: `net stop Traccar && net start Traccar`

Cloud Run / Container Apps deployment hints
-------------------------------------------
- Cloud Run: mount secrets from Secret Manager as volumes and set `_FILE` env paths; include exporters via sidecar is not supported—run exporters externally (e.g., node exporter on the host or GKE). Use `--set-secrets` for env-based secrets when `_FILE` is unnecessary.
- Azure Container Apps: Key Vault references can populate secrets; for exporters/sidecars, run them as separate Container Apps or in an AKS deployment if co-location is required. Map secrets to env or volume mounts and set `_FILE` paths in the container args/env.
- Exporter placement when sidecars aren't supported: deploy exporters on the host (VM), in a separate VM/cluster, or use managed metrics solutions; scrape through Prometheus remote targets.
- Cloud Run external exporter pattern:
  - Run node exporter/cAdvisor on a small GCE instance; allow Prometheus (self-hosted or managed) to scrape it over a private network/VPC connector.
  - For Cloud Run service metrics, rely on Cloud Monitoring and bridge into Prometheus via the managed Prometheus integration.
- Azure Container Apps external exporter pattern:
  - Run node exporter/cAdvisor on an Azure VM or AKS node pool and scrape via Prometheus.
  - Use Azure Monitor metrics for the Container App itself and federate to Prometheus/Grafana if needed.
- Example Cloud Run deployment (secrets + managed metrics):
  - Deploy: `gcloud run deploy traccar --image=gcr.io/PROJECT/traccar:tag --set-secrets=DATABASE_PASSWORD=traccar-db-password:latest --vpc-connector=projects/PROJECT/locations/REGION/connectors/CONNECTOR --allow-unauthenticated`
  - Mount secret to file with `--update-secrets DATABASE_PASSWORD_FILE=traccar-db-password:latest:/var/secrets/db-password` and set env `DATABASE_PASSWORD_FILE=/var/secrets/db-password`.
  - Exporters run externally (e.g., GCE) and are scraped by managed Prometheus; ingest Cloud Run metrics via Cloud Monitoring integration.
- Example Azure Container Apps deployment (secrets + external exporters):
  - Bicep snippet:
    ```bicep
    resource aca 'Microsoft.App/containerApps@2023-05-01' = {
      name: 'traccar'
      location: resourceGroup().location
      properties: {
        environmentId: appEnv.id
        configuration: {
          ingress: { external: true, targetPort: 8082 }
          secrets: [
            { name: 'db-password', value: keyVault.getSecret('traccar-db-password') }
          ]
          registries: []
        }
        template: {
          containers: [
            {
              name: 'traccar'
              image: 'ghcr.io/org/traccar:tag'
              env: [
                { name: 'DATABASE_PASSWORD_FILE', value: '/var/secrets/db-password' }
              ]
              volumeMounts: [{ volumeName: 'kv', mountPath: '/var/secrets' }]
            }
          ]
          volumes: [{ name: 'kv', secret: { secrets: [{ name: 'db-password' }] } }]
        }
      }
    }
    ```
  - Exporters run in AKS or a VM; scrape via Prometheus/Grafana; use Azure Monitor for Container Apps metrics.

Service configuration parameters
Traccar uses Java virtual machine, so it has restrictions on the amount of memory it can use on the system.

You can change Java heap size by adding following parameters to the service config:

-Xmx to specify the maximum heap size
-Xms to specify the initial Java heap size
On Linux the service config located at /etc/systemd/system/traccar.service:

ExecStart=/opt/traccar/jre/bin/java -Xmx1G -jar tracker-server.jar conf/traccar.xml
In above example the maximum heap size is set to 1GB.

Selecting database engine
By default Traccar uses embedded H2 database system. It's used to simplify initial set up and configuration of the server software, but for any production environment it's strongly recommended to use a fully-featured database engine. One of the best results in terms of performance are observed with MySQL database. Traccar also supports other popular database systems (Microsoft SQL Server, PostgreSQL and others).

Make sure that the database is configured appropriately to handle the amount of data and traffic you plan to have. Default cache size and other configuration parameters might not be the best for your use case. For MySQL tips you can check our MySQL optimization documentation page.
