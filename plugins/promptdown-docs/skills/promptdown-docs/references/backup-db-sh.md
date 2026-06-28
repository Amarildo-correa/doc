Agendado via cron para rodar às 2h da manhã. Copia o arquivo com timestamp no nome e mantém apenas os 7 backups mais recentes.

```bash
TIMESTAMP=$(date +"%Y-%m-%d_%H-%M")
SOURCE="/var/lib/docker/volumes/promptdown_db-data/_data/database.json"
DEST="/var/backups/promptdown/database_$TIMESTAMP.json"

cp "$SOURCE" "$DEST"
ls -t /var/backups/promptdown/database_*.json | tail -n +8 | xargs rm -f
```

## Agendamento via cron

```bash
# roda às 2h da manhã todo dia
0 2 * * * /var/www/promptdown-ui-v3/scripts/backup-db.sh
```
