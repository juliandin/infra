# Backups restoration process

## Ansible

Ansible is also stored on our backup server and can be downloaded from there:

*command:*  **scp -r juliandin@backup.julian.exam:ica0002 ansible_code**

Command will be inside ansible_code directory. Write **cd ansible_code** to enter directory with code.


In case we need ansible code and backup server is not available for some reason, code can be download from admins repository:

*command:*  **git clone https://github.com/juliandin/ica0002** (admin will provide credentails via secure voice communication or chat)


### AGAMA / MySQL

MySQL database for our agama applciation can be restored from backup server.

*command_as_backup_user:* **duplicity --no-encryption restore rsync://juliandin@backup.julian.exam//home/juliandin/ /home/backup/restore/agama.sql**

*command_as_backup_user:* **mysql agama < /home/backup/restore/agama.sql**

## Prometheus

Prometheus database and service can be restored from directory, downlodaed from backup server.

*command_as_backup_user:* **duplicity --no-encryption restore rsync://juliandin@backup.julian.exam//home/juliandin/prometheus /home/backup/restore/prometheus**

*command_as_backup_user:* cp -r /home/backup/restore/prometheus/* /var/lib/prometheus/

*command_as_admin:* service **sudo service prometheus restart**

## InfluxDB

InfluxDB database and service can be restored from directory, downlodaed from backup server.

*command_as_backup_user:* **duplicity --no-encryption restore rsync://juliandin@backup.julian.exam//home/juliandin/influxdb /home/backup/restore/influxdb**

*command_as_backup_user:* cp -r /home/backup/restore/influxdb/* /var/lib/influxdb/

*command_as_admin:* service **sudo service influxdb restart**

## Grafana-Docker

Grafana databases and tables can be restored from .json files, if qualified person is present. Panels can be find inside ansible code. Check step 1 for ansible restoration.

## MySQL replication

MySQL replication can be restored by rewriting commands below. Must ensure that have altest backups!

*command_as_root_on_sql_server:* **sudo mysql**

*Commands inside MySQL:*\
STOP SLAVE;\
CHANGE MASTER TO MASTER_HOST='mysql-master-host-ip', MASTER_USER='replication', MASTER_PASSWORD='repl-password';\
RESET SLAVE;\
START SLAVE;\
SHOW SLAVE STATUS\G; # should show no errors
 
