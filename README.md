# Prerequisites
#
- JDK 11 
- Maven 3 
- MySQL 8

# Technologies 
- Spring MVC
- Spring Security
- Spring Data JPA
- Maven
- JSP
- Tomcat
- MySQL
- Memcached
- Rabbitmq
- ElasticSearch
# Database
Here,we used Mysql DB 
sql dump file:
- /src/main/resources/db_backup.sql
- db_backup.sql file is a mysql dump file.we have to import this dump to mysql db server
- > mysql -u <user_name> -p accounts < db_backup.sql



<!-- My code -->
## Vagrant plugins:
- Vagrant plugins or extensions give extra feature to the Vagrant
- vagrant plugin install  vagrant-hostmanager
- vagrant plugin install  vagrant-vbguest




## Setup should be in below mentioned order
- MySqL (database SVC)
- Memcache (DB Cashing SVC)
- RabbitMQ (Broker/Queue SVC)
- Tomcat (Application SVC)
- Nginx (Web SVC)
- So the idea is to start by setup all related backend stack down to frontend stacks, and when we want to stop, we start from bottom to top

### MySql setup
- 1) Update OS with latest patches 
#### yum update -y

- 2) Set Repository
####  yum install epel-release -y

- 3) Install Maria DB Packages - 
#### yum install git mariadb-server -y

- 4) Starting and enabling mariadb-server
#### systemctl start mariadb
#### systemctl enable mariadb

- 5) Run mysql secure installation script
#### mysql_secure_installation
- Note: Set db root password, 

 1  yum update -y
    2  clear
    3  yum install epel-release -y
    4  yum install git mariadb-server -y 
    5  clear
    6  systemctl status mariadb
    7  systemctl enable  mariadb
    8  systemctl status mariadb
    9  :q!
   10  clear
   11  systemctl start  mariadb
   12  systemctl status mariadb
   13  clear
   14  mysql_secure_installation
   15  clear
   16  mysql_secure_installation
   17  history

### Set DB name and users
#### mysql -u root -padmin123
- mysql> create database accounts;
- mysql> grant all privileges on accounts.* TO 'admin'@'%' identified by 'admin123' ;
- This command mean we give admin user the privilege to manage accounts database remotely (%) means remotely by the password 'admin123' 
- mysql>FLUSH PRIVILEGES; (to reload)
- mysql> exit

### Download Source code & Initialize Database
#### git clone -b main https://github.com/hkhcoder/vprofile-project.git
#### cd vprofile-rpoject
#### mysql -u root -padmin123 accounts < src/main/resources/db_backup.sql (to deploy the database)
#### mysql -u root -padmin123 accounts
#### mysql> show tables