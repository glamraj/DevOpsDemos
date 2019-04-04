# SonarQube Installation

SonarQube provides the capability to not only show health of an application but also to highlight issues newly introduced. With a Quality Gate in place, you can fix the leak and therefore improve code quality systematically.
### Follow this Article in **[YouTube](https://www.youtube.com/watch?v=zRQrcAi9UdU)**

### Prerequisites
1. EC2 instance with Java installed
1. MySQL Database Server or MyQL RDS instance. **[Get help here](https://www.youtube.com/watch?v=vLaW6b441x0)**

### Installation

Install MySQL client version 

 ```sh
  # yum install mysql
 ```
Download stable SonarQube version from below website. 
- Website: https://www.sonarqube.org/downloads/
- Note: This Article written for SonarQube6.0  

#######################Non RDS version.. MySQL Community version installation#############
Step 1: Download Mysql with this link to your destination folder.

# wget http://dev.mysql.com/get/mysql57-community-release-el7-7.noarch.rpm


Step 2: Execute the rpm in your server with command.

# rpm -ivh mysql57-community-release-el7-7.noarch.rpm

Step 3: Install MySQL with.

# yum install mysql-community-server

Step 4: Start MySQL with.

# service mysqld start

Step 5: Check status of your  MySQL with 

# service mysqld status

                  You can check MySql Status wih # mysql --version

Step 6: You have to secure your  MySQL with 

# grep 'temporary password' /var/log/mysqld.log

 after generating complex password you need reset it with your own password with
 
 [root@ip-172-31-17-253 opt]# grep 'temporary password' /var/log/mysqld.log
2019-03-11T13:38:51.635253Z 1 [Note] A temporary password is generated for root@localhost: mYsql234567?-


# mysql_secure_installation
mysql server root password: 	mYsql234567?-

[root@ip-172-31-17-253 opt]# mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 5.7.25 MySQL Community Server (GPL)

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>


#######################Non RDS version.. MySQL Community version installation#############

Download & unzip SonarQube 6.0
```sh
# cd /opt
# wget https://sonarsource.bintray.com/Distribution/sonarqube/sonarqube-6.0.zip
# unzip sonarqube-6.0.zip
# mv /opt/sonarqube-6.0 /opt/sonar
```
Allow RDS instance security group to access SonarQube server 

Connect to RDS instance with database credentials
```sh 
mysql -h <RDS_Instance_endpoint>:3306 -u <DB_USER_NAME> -p <DB_PASSWORD> 
```
Create a new sonar database
```sh
CREATE DATABASE sonar CHARACTER SET utf8 COLLATE utf8_general_ci;
```

Create a local and a remote user
```sh
CREATE USER sonar@localhost IDENTIFIED BY 'Sonarl234567?-';
CREATE USER sonar@'%' IDENTIFIED BY 'Sonarl234567?-';
```

###########in case need to delete user######################

mysql> DROP USER sonar@localhost;
Query OK, 0 rows affected (0.00 sec)

mysql> DROP USER sonar@'%';
Query OK, 0 rows affected (0.00 sec)

############################################################

Grant database access permissions to users 
```sh
GRANT ALL ON sonar.* TO sonar@localhost;
GRANT ALL ON sonar.* TO sonar@'%';
```

check users and databases 
```sh
use mysql
show databases;
SELECT User FROM mysql.user;
FLUSH PRIVILEGES;
QUIT
```
So for you have configured required database information on RDS. Let’s Jump back to your EC2 instance and enable SonarQube properties file to connect his Database.

### ON EC2 Instance
Edit sonar properties file to uncomment and provide required information for below properties. 

- File Name: /opt/sonar/conf/sonar.properties
  - sonar.jdbc.username=`sonar`
  - sonar.jdbc.password=`sonar`
  - sonar.jdbc.url=jdbc:mysql://`<RDS_DATABAE_ENDPOINT>:3306`/sonar?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&useConfigs=maxPerformance&useSSL=false
  - sonar.web.host=`0.0.0.0`
  - sonar.web.context=`/sonar`

Start SonarQube service 
```sh
# cd /opt/sonar/bin/linux-x86-64/
# ./sonar.sh start
```

##### Run SonarQube as a default service 

Implement SonarQube server as a service
```sh
Copy sonar.sh to etc/init.d/sonar and modify it according to your platform.
# sudo cp /opt/sonar/bin/linux-x86-64/sonar.sh /etc/init.d/sonar
# sudo vi /etc/init.d/sonar
```

Add below values to your /etc/init.d/sonar file
```sh
Insert/modify below values
SONAR_HOME=/opt/sonar
PLATFORM=linux-x86-64

WRAPPER_CMD="${SONAR_HOME}/bin/${PLATFORM}/wrapper"
WRAPPER_CONF="${SONAR_HOME}/conf/wrapper.conf"
PIDDIR="/var/run"
```

Start SonarQube server
```sh
# service sonar start
```
SonarQube application uses port 9000. access SonarQube from browser
```sh
  http://<EC2_PUBLIC_IP>:9000/sonar
```
###  Troubleshooting 

1. Check whether you enabled port 9000 in EC2 instance security group
2. Check whether you enabled EC2 instance IP range in RDS security group

### Next Step
- [x] [Integrate SonarQube with Jenkins](https://www.youtube.com/watch?v=k-3krTRuAFA)
