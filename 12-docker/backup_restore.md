# Backups restoration process

All commands are run as backup user on server 1 or server 2. Ansible code runs from administrators machine as root.

## Ansible

Ansible also stored on our backup server and can be downloaded from there:

*command:*  **scp -r juliandin@backup.julprom.jd:ica0002 ansible_code**

Command will be inside ansible_code directory. Write **cd ansible_code** to enter directory.

In case we need ansible code and backup server is not available for some reason, code can be download from admins repository:

*command:*  **git clone https://github.com/juliandin/ica0002**

(admin will provide password and username via voice communication or chat etc. etc.)

## Nginx

Nginx can be backed up by running ansible code from earlier downloaded repository (it is **ansible_code** if ansible downloaded from backup server or **ica0002** if downloaded from repo).

*command_on_admins_machine:*  **ansible-playbook infra.yaml**

## App server

### uWSGI

uWSGI can be backed up by running ansible code from earlier downloaded repository (it is **ansible_code** if ansible downloaded from backup server or **ica0002** if downloaded from repo).

*command_on_admins_machine:* **ansible-playbook infra.yaml**

### AGAMA

AGAMA can be backed up by running ansible code from earlier downloaded repository (it is **ansible_code** if ansible downloaded from backup server or **ica0002** if downloaded from repo).

*command_on_admins_machine:* **ansible-playbook infra.yaml**
#  
In case we have problems with our application or newer version for some reason is not working, we can insntall previous working version.

*command_as_backup_user:* **cd /home/backup/restore**

*command_as_backup_user:* **rsync -v juliandin@backup.julprom.jd:agama.py /home/backup/restore/**

*command_as_backup_user:* **mv agama.py /opt/agama/agama.py (and press enter for overwrite)**
#  
If version above is not working, we can download one month older application version:

*command_as_backup_user:* **cd /home/backup/restore**

*command_as_backup_user:* **rsync -v juliandin@backup.julprom.jd:agama.py~ /home/backup/restore/**

*command_as_backup_user:* **cp agama.py~ /opt/agama/agama.py (and press enter for overwrite)**

### MySQL

MySQL database for our agama applciation can be restored from backup server.

*command_as_backup_user:* **duplicity --no-encryption restore rsync://juliandin@backup.julprom.jd//home/juliandin/ /home/backup/restore/agama.sql**

*command_as_backup_user:* **mysql agama < home/backup/restore/agama.sql**
#
In case MySQL service is damaged or not configured properly we need to run ansible:

*command_on_admins_machine:* **ansible-playbook infra.yaml**

## Prometheus

Prometheus database and service can be restored from directory, downlodaed from backup server.

*command_as_backup_user:* **duplicity --no-encryption restore rsync://juliandin@backup.julprom.jd//home/juliandin/prometheus /home/backup/restore/prometheus**

*command_as_backup_user:* **mv /home/backup/restore/prometheus /var/lib/prometheus (and press enter for overwrite)**
#
In case Prometheus service is damaged or not configured properly we need to run ansible:

*command_on_admins_machine:* **ansible-playbook infra.yaml**

## InfluxDB

InfluxDB database and service can be restored from directory, downlodaed from backup server.

*command_as_backup_user:* **duplicity --no-encryption restore rsync://juliandin@backup.julprom.jd//home/juliandin/influxdb /home/backup/restore/influxdb**

*command_as_backup_user:* **mv /home/backup/restore/influxdb /var/lib/influxdb (and press enter for overwrite)**
#
In case InfluxDB service is damaged or not configured properly we need to run ansible:

*command_on_admins_machine:* **ansible-playbook infra.yaml**

## Telegraf

Telegraf databases and logs are covered by InfluxDB backup.

In case Telegraf service is damaged or not configured properly we need to run ansible:

*command_on_admins_machine:* **ansible-playbook infra.yaml**

## Grafana

Grafana installation and configuration can be restored by running ansible code.

*command_on_admins_machine:* **ansible-playbook infra.yaml**
#  
Grafana database and tables also can be restored from database backup, if qualified person is present.

*command_as_backup_user:* **duplicity --no-encryption restore rsync://juliandin@backup.julprom.jd//home/juliandin/grafana_db/ /home/backup/restore/grafana.db**

*command_as_backup_user:* **mv grafana.db /var/log/grafana/grafana.db (requires grafana service restart)**
#
Another way of restoration is to run ansible code and after that add needed panels from admins repository.

Fits only if specialist present.

## Grafana-Docker

*command_as_admin:* **duplicity --no-encryption restore rsync://juliandin@backup.julprom.jd//home/juliandin/grafana_db/ /home/backup/restore/grafana.db**

*command_as_admin:* **mv /home/backup/restore/grafana.db /opt/docker/grafana/**

*command_as_admin:* **chown -R 472:472 /opt/docker/grafana**

(requires grafana docker restart)

## DNS

DNS can be restored by ansible code run. *Just hope nothing serious happened to it*.

*command_on_admins_machine:* **ansible-playbook infra.yaml**


## MySQL replication

mySQL replication can be restored by reisntalling MySQL. MUST ENSURE THAT WE HAVE LATEST BACKUPS!


*command_as_root_on_sql_server:* **apt-get purge mysql-* **
*command_as_root_on_sql_server:* **rm -rf /etc/mysql**

*command_on_admins_machine:* **ansible-playbook infra.yaml**

*command_as_root_on_sql_server:* **sudo mysql**

Commands inside MySQL:
**
STOP SLAVE;
CHANGE MASTER TO MASTER_HOST='<mysql-master-host>',
                 MASTER_USER='replication',
                 MASTER_PASSWORD='<replication-password>'
RESET SLAVE;
START SLAVE;
SHOW SLAVE STATUS\G # should contain no errors
**

*command_as_backup_user:* **duplicity --no-encryption restore rsync://juliandin@backup.julprom.jd//home/juliandin/ /home/backup/restore/agama.sql**

*command_as_backup_user:* **mysql agama < home/backup/restore/agama.sql**
