Upgrading Traccar
If you are running Traccar version 4.0 or later, you can upgrade directly to the latest release. If you are on an earlier version, please review the notes at the bottom of this page first.

Upgrade steps:

Back up the database.
Back up the traccar.xml configuration file (if you have modified it).
Back up the media folder (if it exists).
Uninstall the previous version of Traccar.
Install the new version of Traccar.
Restore the media folder (if applicable).
Restore the traccar.xml configuration file (if applicable).
Start the Traccar service.
If you are using versions 3.3-3.17, upgrade to version 4.0 first, and then to the latest release.

If you are using versions 3.0-3.2, upgrade to version 3.3 first, and then to the latest release.

If you are on version 2.x, you must migrate the database schema manually. See Upgrading from 2.x to 3.0 for details.

These instructions assume that only the main configuration file has been modified. If you have changed other files (for example, templates), re-apply those changes to the new version. Older templates may not be compatible with newer releases.

Backup and restore examples
---------------------------
- MySQL:
  - Backup: `mysqldump -h <host> -u traccar -p traccar > traccar.sql`
  - Restore: `mysql -h <host> -u traccar -p traccar < traccar.sql`
- PostgreSQL:
  - Backup: `pg_dump --format=custom --file=traccar.dump --dbname="postgresql://traccar:<password>@<host>:5432/traccar"`
  - Restore: `pg_restore --clean --dbname="postgresql://traccar:<password>@<host>:5432/traccar" traccar.dump`
For large databases, run backups from a replica when available and store them with the same rotation policy as `conf/traccar.xml`.

Upgrade checklist
-----------------
- Confirm backups: database dump, `conf/traccar.xml`, and any media/asset directories.
- Note current version and target version; pin installer or Docker tag (avoid `latest`).
- Review database migration notes if skipping major versions; plan maintenance window.
- Verify service health after upgrade: logs are clean, API responds, devices reconnect, notifications fire.

Post-upgrade validation
-----------------------
- API/UI: load the web UI, perform an authenticated API call (e.g., `/api/server`), and verify WebSocket connects.
- Device ingestion: confirm fresh position updates arrive for a test device.
- Notifications: trigger a test notification channel (email/SMS/push) where configured.
- Jobs: ensure retention/cleanup cron jobs still run as scheduled after the upgrade.
