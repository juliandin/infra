# Backup SLA 

In this file i am going to describe backup approach for all services that are in our infrastructure.

Currently our infrastructure contains these services and applications:

![](lab-9-infra.png)


## Ansible

The first and the most important backup is ansible code. Our entire infrastructure is built and configured with ansible and there is a lot of small static configuration files that we can backup with ansible aswell. Ansible should be backed up for sure.

**Coverage:** Full code with all templates and roles. No need to pick any specific ansible code parts, because they all work together - installations and configurations.

**Backup RPO:** It is acceptable to lose 1 to 3 days of code. Usually to write changes or add something in ansible takes some days, we are not doing entire work in one day and losing some small parts for one-two days is not critical and can be done again. We are not changing our infrastructure and ansible everyday, which means one copy within 3-7 days is enough.

**Versioning:** We need to store our currently-running version and previous two versions of ansible code. Previous versions need to be stored jsut in case our current code version completely breaks, so we can always get back to our previous versions and take some parts from here, and if the previos version is damaged aswell, we have one more step back. In our case ansible version means any crucial and significant change in our infrastructure or configuration files.

**Retention:** Versions are stored usually for the period before the new version comes. If we do big changes during this week, then we remove the oldest version and backup or new version. Which means that our currently created version will be deleted after three new versions. Usually it takes three weeks up to month.

**Usability:** After every ansible code backup creation we need to ensure that this code works. We verify that by using our backed up version and running ansible playbook on our infrasctructure, nothing should change and everything should work as expected. We check this with monitoring tools.

**Restoration criteria:** Backup should be restorad in case our infrastructure was damaged by some bad configurations or attacks and as a result some of our services, applications, networking are not working properly. In this case we need to restore our infrastructure for sure.

**Backup RTO:** Recovery period of our infrastructure really depends on damaged inflicted to it. But usually it is around 5 minutes to 20 minutes.

We store three copies of our ansible code. One is our currently running infrastructure and two older versions of ansible code, just in case of some currently-running system misconfigurations, so that we are always capable of going back to working DNS for example. One copy is stored in git repository and one copy is stored in our infrastructure administartors machine. Finally, ne more copy is aswell stored on flash media.

## Nginx

Nginx is used on both machines. 

All configuration files are created during machines installation and configuration via ansible. We don't have any manual changes and are not 
planning to do them manually on our machines, everything is configured and changed in Nginx with ansible. And if we need to add something, we simply change it inside ansible.

The most important part for us is to have this ansible code backed up and always available. 
So basically, Nginx backup is covered by ansible backup and we don't need to do any specific backups for Nginx configurations and etc.

## App server

Next is an application server, where we have AGAMA app installed for our worker.

Server uses uWSGI for serving our python application on Nginx server (HTTP client ↔ Nginx ↔ uWSGI ↔ Python app).

AGAMA application itself is downloaded from the network and uses MySQL database to store our workers application data.

### uWSGI

Our uWSGI service does not contain and dynamic configuration files, that ever need to be changed manually and were never changed manually. It is static configuration file 
for our application and database cooperative work. Everything connected with uWSGI is installed and configured with ansible. 

Which means that uWSGI backup is fully covered within ansible backup and we do not need to do any specific and additional backups.

### AGAMA

AGAMA application itself is downloaded from network. And it is very reccomended to have this application backed up. 

The application version installed on sever should be backed up. And later on if any new application version comes out and we plan to implement it, we should always 
have backed up version of our currently running and installed application on our server. 

It is suitable to store last three versions of applciation, just in case we install new version and for some reason it is not working in our enviroment, 
we are always able to go back to our previous versions.

After application was backed up we need to verify that this .py file and application from it is working properly. In case something was damaged during downloading or 
backup was created after application configuration and again, something is broken because of that.

Different configuration files connected with our application are covered by other services backups, mainly by ansible backup.

### MySQL

MySQL contains all data that was inserted by the user into the AGAMA application. Database may contain some important data for our user and it needs to be backed up. 

**Coverage:** MySQL table in database that is used by AGAMA needs to be backed up.

**Backup RPO:** It is okay to lose everyday user tasks and goals in app. But. they have some monthly or weekly tasks, we need to have backup for them. Users insert main data and tasks on Mondays and small tasks everyday during the week. We must do backups every Monday.

**Versioning:** Users asked us to store last four versions of their tasks. Table backups do not weigh too much and we are okay with that.

**Retention:** Versions are stored for 4 weeks.

**Usability:** We have an analytical department in our company. They are analyzing workers achieved tasks and goals every week. They are checing backups usability.

**Restoration criteria:** In case MySQL database was somehow damaged, we need to restore our Monday version of it.

**Backup RTO:** Backup takes 1 to 5 minutes, depending on users information.

MySQL service restoration and installation itself is covered with ansible backup. In MySQL backup we only ensure that users data is backed up.

## Prometheus

Prometheus is installed and configured with ansible. All configuration files here are static nothing is configured and changed manually, in case we need to change anything, we change it with ansible. Prometheus backup is covered with ansible backup.

## InfluxDB

InfluxDB contains a lot of important data like rsyslog. latency and other data about our machines. Wee should backup influx database for sure.

**Coverage:** All tables in InfluxDB that we use for our monitorning.

**Backup RPO:** It is possible to lose data for one day, but more than that it is bad for us, because based on these logs we can recover and understand problems in our system. Backup must be done everyday.

**Versioning:** We are storing last month of logs, to be exact last 30 versions.

**Retention:** Version is stored for 30 days.

**Usability:** We have an analytical IT department in our company. They are analyzing logs and warnings for possible threats. They are checing backups usability.

**Restoration criteria:** In case InfluxDB database got damaged or broken, we can restore it from our backup if we are in need for old logs.

**Backup RTO:** Backup takes 1 to 20 minutes, depending on logs amount.

## Telegraf

Telegraf does not have any specific data we need to backup and telgraf installation and configuration is fully covered by influxDB and ansible backups.

## Grafana

Grafana is our main monitoring tools. It contains a lot of manually created graph for our databases and metrics and is very importnant to backup.

**Coverage:** All panels we are using and have created.

**Backup RPO:** Depends on our changes in our infrastructure and monitoring, we are not changing things too often. Is is okay to do grafana backup once a month and whenever we do significant changes to our environment we should do new backup aswell.

**Versioning:** We are storing last three versions of our Grafana configurations.

**Retention:** Versions are stored for 30 days.

**Usability:** Grafana is used by our IT specialists everyday and they all are checking its usability and integrity.

**Restoration criteria:** Just in case our Grafana panels get damaged or destroyed by wrong configurations, we are always able to backup them.

**Backup RTO:** Backup takes 1 to 5 minutes, depending on Frafana .json file length.

## DNS

Last but not least, DNS. Probably, the most important part of our infrastructure and network. Entire DNS configuration and installation is covered by ansible code. Nothing really is added or changed manually, in case of new domain name or hosts, we change them inside ansible. This means that DNS backup is fully covered by an ansible backup. The important part here that we need to ensure ansible code availability and integrity in case of network loss.
