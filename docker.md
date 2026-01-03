Docker
Traccar provides official Docker images that are available directly on Docker Hub.

Recommended tags:

6.11.1, 6.11, 6, 6.11.1-alpine, 6.11-alpine, 6-alpine, latest
6.11.1-debian, 6.11-debian, 6-debian, debian
6.11.1-ubuntu, 6.11-ubuntu, 6-ubuntu, ubuntu
Multi-platform images available:

Alpine: linux/amd64, linux/arm64
Debian: linux/amd64, linux/arm64
Ubuntu: linux/amd64, linux/arm64
Container creation example
Command to create a container using the default database and configuration:

docker run \
--name traccar \
--hostname traccar \
--detach --restart unless-stopped \
--publish 80:8082 \
--publish 5000-5300:5000-5300 \
--publish 5000-5300:5000-5300/udp \
traccar/traccar:latest
It is usually a good idea to mount the logs, data, and configuration file from the host. Make sure to create the directories and the configuration file first.

--volume /opt/traccar/logs:/opt/traccar/logs:rw
--volume /opt/traccar/traccar.xml:/opt/traccar/conf/traccar.xml:ro
--volume /opt/traccar/data:/opt/traccar/data:rw
Production deployment
The default H2 database is not recommended for production use. For non-testing deployments, we recommend TimescaleDB for large installations or MySQL for smaller servers.

The following Docker Compose examples can serve as a starting point for your deployment with a production-grade database. Align `conf/traccar.xml` JDBC settings with the service you pick and pin images to your rollout policy.

Docker Compose: Traccar + MySQL
-------------------------------
```yaml
version: "3.9"
services:
  traccar:
    image: traccar/traccar:6.11.1-alpine
    container_name: traccar
    restart: unless-stopped
    ports:
      - "80:8082"
      - "5000-5300:5000-5300"
      - "5000-5300:5000-5300/udp"
    depends_on:
      - mysql
    volumes:
      - traccar-logs:/opt/traccar/logs
      - ./conf/traccar.xml:/opt/traccar/conf/traccar.xml:ro
      - traccar-data:/opt/traccar/data

  mysql:
    image: mysql:8.0
    container_name: traccar-mysql
    restart: unless-stopped
    environment:
      MYSQL_DATABASE: traccar
      MYSQL_USER: traccar
      MYSQL_PASSWORD: change-me
      MYSQL_ROOT_PASSWORD: change-root
    command: --default-authentication-plugin=mysql_native_password
    volumes:
      - mysql-data:/var/lib/mysql

volumes:
  traccar-logs:
  traccar-data:
  mysql-data:
```
Update `traccar.xml` database block to use the MySQL JDBC URL (e.g., `jdbc:mysql://mysql:3306/traccar`). Minimal matching snippet:
```xml
<entry key='database.driver'>com.mysql.cj.jdbc.Driver</entry>
<entry key='database.url'>jdbc:mysql://mysql:3306/traccar?serverTimezone=UTC</entry>
<entry key='database.user'>traccar</entry>
<entry key='database.password'>change-me</entry>
```

Docker Compose: Traccar + TimescaleDB (PostgreSQL)
--------------------------------------------------
```yaml
version: "3.9"
services:
  traccar:
    image: traccar/traccar:6.11.1-alpine
    container_name: traccar
    restart: unless-stopped
    ports:
      - "80:8082"
      - "5000-5300:5000-5300"
      - "5000-5300:5000-5300/udp"
    depends_on:
      - timescaledb
    volumes:
      - traccar-logs:/opt/traccar/logs
      - ./conf/traccar.xml:/opt/traccar/conf/traccar.xml:ro
      - traccar-data:/opt/traccar/data

  timescaledb:
    image: timescale/timescaledb:2.14.0-pg15
    container_name: traccar-timescaledb
    restart: unless-stopped
    environment:
      POSTGRES_DB: traccar
      POSTGRES_USER: traccar
      POSTGRES_PASSWORD: change-me
    volumes:
      - timescaledb-data:/var/lib/postgresql/data

volumes:
  traccar-logs:
  traccar-data:
  timescaledb-data:
```
Update `traccar.xml` to use a PostgreSQL JDBC URL (e.g., `jdbc:postgresql://timescaledb:5432/traccar`). Minimal matching snippet:
```xml
<entry key='database.driver'>org.postgresql.Driver</entry>
<entry key='database.url'>jdbc:postgresql://timescaledb:5432/traccar</entry>
<entry key='database.user'>traccar</entry>
<entry key='database.password'>change-me</entry>
```
For high-ingest workloads, enable TimescaleDB hypertables per the database guide.

Other JDBC examples (without Compose)
-------------------------------------
For environments using managed databases, update `traccar.xml` with the appropriate driver and URL:
- Microsoft SQL Server:
```xml
<entry key='database.driver'>com.microsoft.sqlserver.jdbc.SQLServerDriver</entry>
<entry key='database.url'>jdbc:sqlserver://mssql-host:1433;databaseName=traccar;encrypt=true;trustServerCertificate=true</entry>
<entry key='database.user'>traccar</entry>
<entry key='database.password'>change-me</entry>
```
- Oracle:
```xml
<entry key='database.driver'>oracle.jdbc.OracleDriver</entry>
<entry key='database.url'>jdbc:oracle:thin:@//oracle-host:1521/ORCLCDB</entry>
<entry key='database.user'>traccar</entry>
<entry key='database.password'>change-me</entry>
```
Ensure the corresponding JDBC drivers are available to the server runtime.

Validating Liquibase migrations on managed databases
----------------------------------------------------
When pointing a managed database at a new Traccar version:
- Take a snapshot/backup first.
- Run Liquibase in dry-run mode (`updateSQL`) from the exact Traccar version/image you plan to deploy to ensure permissions and changelog compatibility.
- Run `liquibase status` (or start a staging instance) against a replica to confirm pending changes are applied cleanly before touching production.
- Deploy only after validation succeeds; keep backups until post-deploy verification passes.
