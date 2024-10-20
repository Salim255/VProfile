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
#### mysql -u root -padmin123 accounts (to login to database named accounts)
#### mysql> show tables




### MEMCACHE SETUP
#### instal, start & enable memcache on port 11211
#### sudo dnf install epel-release -y  (dnf or yum are the same thing, but we use dnf in latest operating systems)
#### sudo dnf install memcached -y
#### sudo systemctl start memcached 
#### sudo systemctl enable memcached
#### sudo systemctl status memcached
#### sed -i 's/127.0.0.1/0.0.0.0/g' /etc/sysconfig/memcached (search for the local ip:127.0.0.0 and replace it by 0.0.0.0 ) (so we need to do the replacement because some services listen only to local connection, and to change this behavior we make this change so other services from other machine can connect to this services inside other machine (to allow remote connection) (0.0.0.0 in networking means all the ipV4))
#### sudo systemctl restart memcached



### RABBITMQ SETUP

#### yum install epel-release -y 
#### dnf -y install centos-release-rabbitmq-38 (to install rabbimq repo)
#### dnf --enablerepo=centos-rabbitmq-38 -y install rabbitmq-server (enable rabbitmq repo and install rabbitmq server)
#### systemctl start rabbitmq-server
#### system enable --now rabbitmq-server

#### firewall-cmd --add-port=5672/tcp
#### firewall-cmd --runtime-to-permanent

#### systemctl start rabbitmq-server
#### systemctl enable rabbitmq-server
#### systemctl status rabbitmq-server

#### sh -c 'echo "[{rabbit, [{loopback_users, []}]}]." > /etc/rabbitmq.config' (for vproifle application)
#### rabbitmqctl add_user test test (add user test with password test)
#### rabbitmqctl set_user_tags test administrator (set a tag to test user as administrator, willbe used by vprofile application to connect rabbitmq service)
#### systemctl restart rabbitmq-server

### TOMCAT SETUP
- Login to the tomcat vm
#### vagrant ssh app01
- Verify Host entry, if entires missing update it with IP and hosnames
#### cat /etc/hosts
- Update OS with latest patches
#### yum update -y
- Set Repository
#### yum install epel-release -y
- Install Dependencies
#### dnf -y install  java-11-openjdk java-11-openjdk-devel
#### dnf install git maven wget -y (git to clone source code, maven use to build our artifact )
- Change dir to /tmp
#### cd/tmp/

- Download & Tomcat Package
#### wget https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.75/bin/apache-tomcat-9.0.75.tar.gz (This tar contain everything we need for our tomcat application, configuration files, startup script...)

#### tar xzvf apache-tomcat-9.0.75.tar.gz

- Add tomcat user
#### useradd --home-dir /usr/local/tomcat --shell /sbin/nologin tomcat (add user as tomcat, and create home dir as /user/local/tomcat and this--shell /sbin/nologin to tell that we dont need to have login access with this service )

- Copy data to tomcat home dir
#### cp -r /tmp/apache-tomcat-9.0.75/* /usr/local/tomcat/

- Make tomcat user owner of tomcat home dir
#### chown -R tomcat.tomcat /usr/local/tomcat

#### Setup systemctl command for tomcat

- Update file with following content
#### vi /etc/systemd/system/tomcat.service

#### [Unit]
#### Description=Tomcat
#### After=network.target

#### [Service]
User=tomcat
WorkingDirectory=/usr/local/tomcat
Environment=JRE_HOME=/user/lib/jvm/jre
Environment=JAVA_HOME=/user/lib/jvm/jre
Environment=CATALINA_HOME=/usr/local/tomcat
Environment=CATALINA_BASE=/usr/local/tomcat
ExecStart=/usr/local/tomcat/bin/catalina.sh run
ExecStop=/usr/local/tomcat/bin/shutdown.sh
SyslogIdentifier=tomcat-%i

#### [Install]
WantedBy=multi-user.target

- Reload systemd files
#### systemctl daemon-reload

- Start & Enable service
#### systemctl start tomcat
#### systemctl enable tomcat