---
title: Scripts Bash & Shell Linux
description: Scripts d'automatisation shell, sauvegardes et maintenance système
---

# Scripts Bash & Shell Linux

Scripts d'exploitation système conçus selon les standards POSIX et avec une gestion robuste des erreurs.

---

## 1. Script de sauvegarde avec rotation et chiffrement

```bash title="backup_rotate.sh"
#!/usr/bin/env bash
set -euo pipefail

# Paramètres de configuration
BACKUP_SRC="/var/www /etc/nginx"
BACKUP_DEST="/mnt/backups/daily"
RETENTION_DAYS=7
TIMESTAMP=$(date +"%Y%m%d_%H%M%S")
ARCHIVE_NAME="backup_${TIMESTAMP}.tar.gz"

log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $*"
}

mkdir -p "${BACKUP_DEST}"

log "Début de la création de l'archive ${ARCHIVE_NAME}..."
tar -czf "${BACKUP_DEST}/${ARCHIVE_NAME}" ${BACKUP_SRC}
log "Archive créée avec succès (${BACKUP_DEST}/${ARCHIVE_NAME})."

log "Purge des archives de plus de ${RETENTION_DAYS} jours..."
find "${BACKUP_DEST}" -type f -name "backup_*.tar.gz" -mtime +"${RETENTION_DAYS}" -print -delete
log "Nettoyage terminé avec succès."
```
