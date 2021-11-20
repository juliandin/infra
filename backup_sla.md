# Backup SLA 

In this file i am going to describe backup approach for all services that are in our infrastructure.

## Ansible

The first and the most important backup is ansible code. Our entire infrastructure is built and configured with ansible. Ansible should be backed up for sure.

**Coverage:** Full code with all templates and roles.

**Backup RPO:** It is acceptable to lose 1 to 3 days of code. We are not changing our infrastructure and ansible everyday, which means one copy within 3-7 days is enough.

**Versioning:** We need to store our currently-running version and previous version of ansible code. In our case ansible version means any crucial and significant change in our infrastructure or configuration files.

**Retention:** Versions are stored usually for the period before the new version comes.

**Usability:** After every ansible code backup creation we need to ensure that this code works. We verify that by using our backed up version and running ansible playbook on our infrasctructure, nothing should change and everything should work as expected.

**Restoration criteria:** Backup should be restorad in case our infrastructure was damaged by some bad configurations or attacks and as a result some of our services, applications, networking are not working properly.

**Backup RTO:** Usually up to 40 minutes.


### MySQL

MySQL contains all data that was inserted by the user into the AGAMA application. Database may contain some important data for our user and it needs to be backed up. 

**Coverage:** MySQL table in database that is used by AGAMA needs to be backed up.

**Backup RPO:** It is okay to lose few hours of user data and information in app. But, we must ensure everyday backups.

**Versioning:** We store 14 versions (2 weeks of data).

**Retention:** Versions are stored for 2 weeks.

**Usability:** We have an analytical department in our company. They are analyzing workers achieved tasks and goals every week. They are checking backups usability.

**Restoration criteria:** In case application MySQL database was damaged or lost, we need to restore it.

**Backup RTO:** Backup takes up to 20 minutes, depending on users information amount.


## Prometheus

Prometheus is installed and configured with ansible. We only need to ensure backup of metrics and logs.

**Coverage:** All prometheus databases.

**Backup RPO:** It is okay to lose few hours of logs and information in app. But, we must ensure everyday backups for logs.

**Versioning:** We store 10 versions.

**Retention:** Versions are stored for 10 days.

**Usability:** We have an analytical department in our company. They are analyzing logs. They are checking also backups usability.

**Restoration criteria:** In case logs were deleted or some misbehavior and strange acting in logs is noticed.

**Backup RTO:** Backup takes up to 30 minutes.


## InfluxDB

InfluxDB contains a lot of important data like rsyslog, latency and other data about our machines.

**Coverage:** All tables in InfluxDB that we use for our monitorning.

**Backup RPO:** It is possible to lose some hours of data, but we must ensure everyday backups.

**Versioning:** We are storing 20 versions.

**Retention:** Version is stored for 20 days.

**Usability:** We have an analytical IT department in our company. They are analyzing logs and warnings for possible threats. They are checking backups usability.

**Restoration criteria:** In case InfluxDB database got damaged or broken or deleted.

**Backup RTO:** Backup takes up to 30 minutes.


## Grafana

Grafana is our main monitoring tools. It contains a lot of manually created graph for our databases and metrics and is very importnant to backup.

**Coverage:** All panels and database configurations we are using and have created.

**Backup RPO:** it is okay to do grafana backup once a month and whenever we do significant changes to our grafana environment we should do new backup aswell.

**Versioning:** We are storing last three versions of our Grafana configurations.

**Retention:** Versions are stored untill newer version comes.

**Usability:** Grafana is used by our IT specialists everyday and they all are checking its usability and integrity.

**Restoration criteria:** Just in case our Grafana panels get damaged or destroyed by wrong configurations, we are always able to backup them.

**Backup RTO:** Backup takes up to 20 minutes.
