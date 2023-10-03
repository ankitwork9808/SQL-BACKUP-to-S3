# SQL BACKUP to S3
 Backup your local sql to S3

#!/bin/bash

# MySQL credentials
MYSQL_USER="root"
MYSQL_PASSWORD="password"

CURRENT_DATE=$(date +'%d_%B_%Y_%H%M%S')

BACKUP_DIR="/var/www/sqlbackup/backup"

CLIENT_DATABASES=$(mysql -u "$MYSQL_USER" -p"$MYSQL_PASSWORD" -e "SHOW DATABASES LIKE 'client_%';" | grep -v Database)

for DB in $CLIENT_DATABASES; do
  mysqldump -u "$MYSQL_USER" -p"$MYSQL_PASSWORD" "$DB" > "$BACKUP_DIR/${DB}.sql"
done

zip "$BACKUP_DIR/sql_${CURRENT_DATE}.zip" "$BACKUP_DIR"/*.sql

aws s3 cp "$BACKUP_DIR/sql_${CURRENT_DATE}.zip" s3://cs-tips-client/SQLBackup/
#echo "Done"