# Immich (Docker Compose)

```bash
docker compose up -d      # start
docker compose down       # stop
```

- `library/` – uploaded photos/videos (`UPLOAD_LOCATION`)
- `postgres/` – live database files (`DB_DATA_LOCATION`)
- `backups/` – database dumps (see below)

## Database backups

The `db-backup` service (`prodrigestivill/postgres-backup-local`) dumps the `immich` database:

- on container start, if today's backup doesn't exist yet
- on a daily cron schedule (`SCHEDULE`, container time = UTC unless `TZ` is set)

Dumps are written to `backups/daily/` as `immich-YYYYMMDD.sql.gz`. Old ones are deleted after
`BACKUP_KEEP_DAYS` days. Settings live in the `db-backup` section of `docker-compose.yml`.

Run a backup now:

```bash
MSYS_NO_PATHCONV=1 docker exec immich_db_backup /backup.sh
```

Restore: follow the [Immich restore guide](https://docs.immich.app/administration/backup-and-restore/),
using a `.sql.gz` file from `backups/daily/`.

Note: backups are on the same disk as the live data. For real protection, copy `backups/` and
`library/` somewhere else. I personally use [Kopia](https://github.com/kopia/kopia/)
for incremental backups with retention.

