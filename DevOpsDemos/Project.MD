### Simple DevOps Project ###

# 1. Create Two AWS EC2 Linux instance for the project
------------------------------------------------
a) Jenkins Server
b) Linux Host

# 2. Setup Java on Jenkins Server. Using MobaXTerm to login to EC2 instances.
------------------------------------------------
[ec2-user@ip-172-31-25-207 ~]$ sudo passwd root

[ec2-user@ip-172-31-25-207 ~]$ su

[root@ip-172-31-25-207 ec2-user]# yum update

[root@ip-172-31-25-207 ec2-user]# yum -y install java-1.8*

# Error: Package: 1:java-1.8.0-openjdk-headless-1.8.0.201.b09-0.el7_6.x86_64 (rhui-REGION-rhel-server-releases)
           Requires: pcsc-lite-devel(x86-64)
 You could try using --skip-broken to work around the problem
 You could try running: rpm -Va --nofiles --nodigest

# 3. Need to enable/add repos:
------------------------------------------------
[root@ip-172-31-25-207 ec2-user]# yum-config-manager --enable rhui-REGION-rhel-server-extras rhui-REGION-rhel-server-optional

[root@ip-172-31-25-207 ec2-user]# yum -y install java-1.8*

[root@ip-172-31-25-207 ec2-user]# java -version
openjdk version "1.8.0_201"
OpenJDK Runtime Environment (build 1.8.0_201-b09)
OpenJDK 64-Bit Server VM (build 25.201-b09, mixed mode)

# 4. Setup  java home:
------------------------------------------------

[root@ip-172-31-25-207 ec2-user]# echo $PATH
/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/ec2-user/.local/bin:/home/ec2-user/bin

[root@ip-172-31-25-207 ec2-user]# find /usr/lib/jvm/java-1.8* | head -n 3
/usr/lib/jvm/java-1.8.0
/usr/lib/jvm/java-1.8.0-openjdk
/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.201.b09-0.el7_6.x86_64

# Set $JAVA_HOME
[root@ip-172-31-25-207 ec2-user]# JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.201.b09-0.el7_6.x86_64
[root@ip-172-31-25-207 ec2-user]# echo $JAVA_HOME
/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.201.b09-0.el7_6.x86_64

[root@ip-172-31-25-207 ec2-user]# export JAVA_HOME
[root@ip-172-31-25-207 ec2-user]# PATH=$PATH:$JAVA_HOME
[root@ip-172-31-25-207 ec2-user]# echo $PATH
/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/ec2-user/.local/bin:/home/ec2-user/bin:/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.201.b09-0.el7_6.x86_64

# To set it permanently update your .bash_profile
[root@ip-172-31-25-207 ec2-user]# source ~/.bash_profile

4. Now install Jenkins on Jenkins Server:
------------------------------------------------

## Install Jenkins
You can install jenkins using the rpm or by setting up the repo. We will setup the repo so that we can update it easily in future.
Get latest version of jenkins from https://pkg.jenkins.io/redhat-stable/

[root@ip-172-31-25-207 ec2-user]# yum -y install wget

[root@ip-172-31-25-207 ec2-user]# wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo


[root@ip-172-31-25-207 ec2-user]# cd /etc/yum.repos.d/
[root@ip-172-31-25-207 yum.repos.d]# ls
jenkins.repo  redhat.repo  redhat-rhui-client-config.repo  redhat-rhui.repo  rhui-load-balancers.conf


[root@ip-172-31-25-207 yum.repos.d]# rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
[root@ip-172-31-25-207 yum.repos.d]# yum -y install jenkins
Installed:
  jenkins.noarch 0:2.150.3-1.1

Complete!

# Start jenkins service
[root@ip-172-31-25-207 yum.repos.d]# systemctl start jenkins
# Setup Jenkins to start at boot
[root@ip-172-31-25-207 yum.repos.d]# systemctl enable jenkins
jenkins.service is not a native service, redirecting to /sbin/chkconfig.
Executing /sbin/chkconfig jenkins on


# 5. Enable port for JEnkins Server in EC2 instance:
------------------------------------------------
http://13.126.118.157:8080/

#Secret password:
[root@ip-172-31-25-207 yum.repos.d]# cat /var/lib/jenkins/secrets/initialAdminPassword

# 6. GIT installation
------------------------------------------------
[root@ip-172-31-17-253 ec2-user]# yum install git
[root@ip-172-31-17-253 ec2-user]# git --version
git version 1.8.3.1

#Install git pugin in JEnkins

# 6. Maven installation
------------------------------------------------

### Install Maven on Jenkins
[root@ip-172-31-25-207 opt]# mkdir /opt/maven
[root@ip-172-31-25-207 opt]# ls
hello  maven
[root@ip-172-31-25-207 opt]# cd maven
[root@ip-172-31-25-207 maven]# wget http://mirrors.fibergrid.in/apache/maven/maven-3/3.6.0/binaries/apache-maven-3.6.0-bin.zip
[root@ip-172-31-25-207 maven]# ls
apache-maven-3.6.0-bin.zip
[root@ip-172-31-25-207 maven]# yum install unzip
[root@ip-172-31-25-207 maven]# unzip apache-maven-3.6.0-bin.zip
[root@ip-172-31-25-207 maven]# ls
apache-maven-3.6.0  apache-maven-3.6.0-bin.zip
[root@ip-172-31-25-207 maven]# vi ~/.bash_profile
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi

# User specific environment and startup programs
M2_HOME=/opt/maven/apache-maven-3.6.0
M2=$M2_HOME/bin

PATH=$PATH:$HOME/bin:$M2_HOME:$M2

export PATH
[root@ip-172-31-25-207 maven]# source ~/.bash_profile

[root@ip-172-31-25-207 maven]# mvn -v
Apache Maven 3.6.0 (97c98ec64a1fdfee7767ce5ffb20918da4f719f3; 2018-10-24T18:41:47Z)
Maven home: /opt/maven/apache-maven-3.6.0
Java version: 1.8.0_201, vendor: Oracle Corporation, runtime: /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.201.b09-0.el7_6.x86_64/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "3.10.0-862.el7.x86_64", arch: "amd64", family: "unix"

#Install Maven Invoker plugin in JEnkins

##Setup Global Tools in Jenkins
JDK installations
JAVA_HOME =	/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.201.b09-0.el7_6.x86_64

Git Installations
Default
Git

Maven installations
MAVEN_HOME = /opt/maven/apache-maven-3.6.0


### Part-01 : Adding steps for Integration

### Steps to create Jenkin job
1. Login to Jenkins console
1. Create *Jenkins job*, Fill the following details,
   - *Source Code Management:*
      - Repository: `https://github.com/glamraj/world.git`
      - Branches to build : `*/master`  
   - *Build:*
     - Root POM:`pom.xml`
     - Goals and options : `clean install package`
	 



#------------------------------------------
AWS Linux Host Machine setup
#------------------------------------------


# 7. Tomcat installation on Linux Host machine:
------------------------------------------------

[root@ip-172-31-17-253 bin]# cd /opt
[root@ip-172-31-17-253 opt]# yum install wget
[root@ip-172-31-17-253 opt]# wget http://mirrors.estointernet.in/apache/tomcat/tomcat-8/v8.5.38/bin/apache-tomcat-8.5.38.tar.gz
[root@ip-172-31-17-253 opt]# ls
apache-tomcat-8.5.38.tar.gz
[root@ip-172-31-17-253 opt]# tar -xvzf apache-tomcat-8.5.38.tar.gz
[root@ip-172-31-17-253 opt]# cd apache-tomcat-8.5.38/
[root@ip-172-31-17-253 apache-tomcat-8.5.38]# ls
bin  BUILDING.txt  conf  CONTRIBUTING.md  lib  LICENSE  logs  NOTICE  README.md  RELEASE-NOTES  RUNNING.txt  temp  webapps  work
[root@ip-172-31-17-253 apache-tomcat-8.5.38]# cd bin
[root@ip-172-31-17-253 bin]# ls
bootstrap.jar  catalina-tasks.xml            configtest.bat  digest.bat        setclasspath.sh  startup.bat      tomcat-native.tar.gz  version.bat
catalina.bat   commons-daemon.jar            configtest.sh   digest.sh         shutdown.bat     startup.sh       tool-wrapper.bat      version.sh
catalina.sh    commons-daemon-native.tar.gz  daemon.sh       setclasspath.bat  shutdown.sh      tomcat-juli.jar  tool-wrapper.sh
[root@ip-172-31-17-253 bin]# chmod +x *.sh
[root@ip-172-31-17-253 bin]# ls
bootstrap.jar  catalina-tasks.xml            configtest.bat  digest.bat        setclasspath.sh  startup.bat      tomcat-native.tar.gz  version.bat
catalina.bat   commons-daemon.jar            configtest.sh   digest.sh         shutdown.bat     startup.sh       tool-wrapper.bat      version.sh
catalina.sh    commons-daemon-native.tar.gz  daemon.sh       setclasspath.bat  shutdown.sh      tomcat-juli.jar  tool-wrapper.sh
[root@ip-172-31-17-253 bin]# ln -s /opt/apache-tomcat-8.5.38/bin/startup.sh /usr/local/bin/tomcatup
[root@ip-172-31-17-253 bin]# ln -s /opt/apache-tomcat-8.5.38/bin/shutdown.sh /usr/local/bin/tomcatdown
[root@ip-172-31-17-253 bin]# tomcatup
Using CATALINA_BASE:   /opt/apache-tomcat-8.5.38
Using CATALINA_HOME:   /opt/apache-tomcat-8.5.38
Using CATALINA_TMPDIR: /opt/apache-tomcat-8.5.38/temp
Using JRE_HOME:        /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.201.b09-0.el7_6.x86_64
Using CLASSPATH:       /opt/apache-tomcat-8.5.38/bin/bootstrap.jar:/opt/apache-tomcat-8.5.38/bin/tomcat-juli.jar
Tomcat started.

#Using unique ports for each application is a best practice in an environment. But tomcat and Jenkins runs on ports number 8080. Hence lets change tomcat port number to 8090. Change port number in conf/server.xml file under tomcat home

##  cd /opt/apache-tomcat-8.5.35/conf

tomcat application doesnt allow to login from browser. changing a default parameter in context.xml does address this issue
```sh
#search for context.xml
find / -name context.xml
```
#


[root@ip-172-31-17-253 conf]# find / -name context.xml
/opt/apache-tomcat-8.5.38/conf/context.xml
/opt/apache-tomcat-8.5.38/webapps/host-manager/META-INF/context.xml
/opt/apache-tomcat-8.5.38/webapps/manager/META-INF/context.xml


```sh 
tomcatdown
tomcatup

[root@ip-172-31-17-253 conf]# vi /opt/apache-tomcat-8.5.38/webapps/host-manager/META-INF/context.xml
[root@ip-172-31-17-253 conf]# vi /opt/apache-tomcat-8.5.38/webapps/manager/META-INF/context.xml
[root@ip-172-31-17-253 conf]# tomcatdown
Using CATALINA_BASE:   /opt/apache-tomcat-8.5.38
Using CATALINA_HOME:   /opt/apache-tomcat-8.5.38
Using CATALINA_TMPDIR: /opt/apache-tomcat-8.5.38/temp
Using JRE_HOME:        /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.201.b09-0.el7_6.x86_64
Using CLASSPATH:       /opt/apache-tomcat-8.5.38/bin/bootstrap.jar:/opt/apache-tomcat-8.5.38/bin/tomcat-juli.jar
[root@ip-172-31-17-253 conf]# tomcatup
Using CATALINA_BASE:   /opt/apache-tomcat-8.5.38
Using CATALINA_HOME:   /opt/apache-tomcat-8.5.38
Using CATALINA_TMPDIR: /opt/apache-tomcat-8.5.38/temp
Using JRE_HOME:        /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.201.b09-0.el7_6.x86_64
Using CLASSPATH:       /opt/apache-tomcat-8.5.38/bin/bootstrap.jar:/opt/apache-tomcat-8.5.38/bin/tomcat-juli.jar
Tomcat started.
[root@ip-172-31-17-253 conf]# ps aux | grep tomcat
root     17184  0.0  0.0 113312   700 pts/0    S    13:23   0:00 /bin/sh /opt/apache-tomcat-8.5.38/bin/catalina.sh start
root     17185 16.8  7.7 2320384 78920 pts/0   Sl   13:23   0:02 /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.201.b09-0.el7_6.x86_64/bin/java -Djava.util.logging.config.file=/opt/apache-tomcat-8.5.38/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Dorg.apache.catalina.security.SecurityListener.UMASK=0027 -Dignore.endorsed.dirs= -classpath /opt/apache-tomcat-8.5.38/bin/bootstrap.jar:/opt/apache-tomcat-8.5.38/bin/tomcat-juli.jar -Dcatalina.base=/opt/apache-tomcat-8.5.38 -Dcatalina.home=/opt/apache-tomcat-8.5.38 -Djava.io.tmpdir=/opt/apache-tomcat-8.5.38/temp org.apache.catalina.startup.Bootstrap start
root     17242  0.0  0.0 112708   976 pts/0    S+   13:23   0:00 grep --color=auto tomcat

[root@ip-172-31-17-253 conf]# vi tomcat-users.xml
	<role rolename="manager-gui"/>
	<role rolename="manager-script"/>
	<role rolename="manager-jmx"/>
	<role rolename="manager-status"/>
	<user username="admin" password="admin" roles="manager-gui, manager-script, manager-jmx, manager-status"/>
	<user username="deployer" password="deployer" roles="manager-script"/>
	<user username="tomcat" password="s3cret" roles="manager-gui"/>
	
[root@ip-172-31-17-253 conf]# tomcatdown
Using CATALINA_BASE:   /opt/apache-tomcat-8.5.38
Using CATALINA_HOME:   /opt/apache-tomcat-8.5.38
Using CATALINA_TMPDIR: /opt/apache-tomcat-8.5.38/temp
Using JRE_HOME:        /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.201.b09-0.el7_6.x86_64
Using CLASSPATH:       /opt/apache-tomcat-8.5.38/bin/bootstrap.jar:/opt/apache-tomcat-8.5.38/bin/tomcat-juli.jar
[root@ip-172-31-17-253 conf]# ps aux | grep tomcat
root     17452  0.0  0.0 112708   976 pts/0    R+   13:27   0:00 grep --color=auto tomcat
[root@ip-172-31-17-253 conf]# tomcatup
Using CATALINA_BASE:   /opt/apache-tomcat-8.5.38
Using CATALINA_HOME:   /opt/apache-tomcat-8.5.38
Using CATALINA_TMPDIR: /opt/apache-tomcat-8.5.38/temp
Using JRE_HOME:        /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.201.b09-0.el7_6.x86_64
Using CLASSPATH:       /opt/apache-tomcat-8.5.38/bin/bootstrap.jar:/opt/apache-tomcat-8.5.38/bin/tomcat-juli.jar
Tomcat started.

### Part-02: Adding Deployment Steps 
in this port we are going to install 'deploy to container' plugin. this is need to deploy on tomcat server which we are using. 

### Port-03 : Continuous Integration & Continuous Deployment (CI/CD)
Now job is running fine but to make this as Continuous Integration and Continuous Deployment Tod do that go back and modify job as below. 
  - Build Triggers
    - Poll SCM
      - schedule `*/2 * * * *`

Save the job and modify the code in GitHub. Then you could see your job get trigger a build without any manual intervention.

above command gives 3 context.xml files. comment (<!-- & -->) `Value ClassName` field on files which are under webapp directory. 


After that restart tomcat services to effect these changes##

```sh 
tomcatdown
tomcatup

[root@ip-172-31-17-253 conf]# vi /opt/apache-tomcat-8.5.38/webapps/host-manager/META-INF/context.xml
[root@ip-172-31-17-253 conf]# vi /opt/apache-tomcat-8.5.38/webapps/manager/META-INF/context.xml
[root@ip-172-31-17-253 conf]# tomcatdown
Using CATALINA_BASE:   /opt/apache-tomcat-8.5.38
Using CATALINA_HOME:   /opt/apache-tomcat-8.5.38
Using CATALINA_TMPDIR: /opt/apache-tomcat-8.5.38/temp
Using JRE_HOME:        /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.201.b09-0.el7_6.x86_64
Using CLASSPATH:       /opt/apache-tomcat-8.5.38/bin/bootstrap.jar:/opt/apache-tomcat-8.5.38/bin/tomcat-juli.jar
[root@ip-172-31-17-253 conf]# tomcatup
Using CATALINA_BASE:   /opt/apache-tomcat-8.5.38
Using CATALINA_HOME:   /opt/apache-tomcat-8.5.38
Using CATALINA_TMPDIR: /opt/apache-tomcat-8.5.38/temp
Using JRE_HOME:        /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.201.b09-0.el7_6.x86_64
Using CLASSPATH:       /opt/apache-tomcat-8.5.38/bin/bootstrap.jar:/opt/apache-tomcat-8.5.38/bin/tomcat-juli.jar
Tomcat started.
[root@ip-172-31-17-253 conf]# ps aux | grep tomcat
root     17184  0.0  0.0 113312   700 pts/0    S    13:23   0:00 /bin/sh /opt/apache-tomcat-8.5.38/bin/catalina.sh start
root     17185 16.8  7.7 2320384 78920 pts/0   Sl   13:23   0:02 /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.201.b09-0.el7_6.x86_64/bin/java -Djava.util.logging.config.file=/opt/apache-tomcat-8.5.38/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Dorg.apache.catalina.security.SecurityListener.UMASK=0027 -Dignore.endorsed.dirs= -classpath /opt/apache-tomcat-8.5.38/bin/bootstrap.jar:/opt/apache-tomcat-8.5.38/bin/tomcat-juli.jar -Dcatalina.base=/opt/apache-tomcat-8.5.38 -Dcatalina.home=/opt/apache-tomcat-8.5.38 -Djava.io.tmpdir=/opt/apache-tomcat-8.5.38/temp org.apache.catalina.startup.Bootstrap start
root     17242  0.0  0.0 112708   976 pts/0    S+   13:23   0:00 grep --color=auto tomcat

[root@ip-172-31-17-253 conf]# vi tomcat-users.xml
	<role rolename="manager-gui"/>
	<role rolename="manager-script"/>
	<role rolename="manager-jmx"/>
	<role rolename="manager-status"/>
	<user username="admin" password="admin" roles="manager-gui, manager-script, manager-jmx, manager-status"/>
	<user username="deployer" password="deployer" roles="manager-script"/>
	<user username="tomcat" password="s3cret" roles="manager-gui"/>
	
[root@ip-172-31-17-253 conf]# tomcatdown
Using CATALINA_BASE:   /opt/apache-tomcat-8.5.38
Using CATALINA_HOME:   /opt/apache-tomcat-8.5.38
Using CATALINA_TMPDIR: /opt/apache-tomcat-8.5.38/temp
Using JRE_HOME:        /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.201.b09-0.el7_6.x86_64
Using CLASSPATH:       /opt/apache-tomcat-8.5.38/bin/bootstrap.jar:/opt/apache-tomcat-8.5.38/bin/tomcat-juli.jar
[root@ip-172-31-17-253 conf]# ps aux | grep tomcat
root     17452  0.0  0.0 112708   976 pts/0    R+   13:27   0:00 grep --color=auto tomcat
[root@ip-172-31-17-253 conf]# tomcatup
Using CATALINA_BASE:   /opt/apache-tomcat-8.5.38
Using CATALINA_HOME:   /opt/apache-tomcat-8.5.38
Using CATALINA_TMPDIR: /opt/apache-tomcat-8.5.38/temp
Using JRE_HOME:        /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.201.b09-0.el7_6.x86_64
Using CLASSPATH:       /opt/apache-tomcat-8.5.38/bin/bootstrap.jar:/opt/apache-tomcat-8.5.38/bin/tomcat-juli.jar
Tomcat started.

### Part-02: Adding Deployment Steps 
in this port we are going to install 'deploy to container' plugin. this is need to deploy on tomcat server which we are using. 

### Port-03 : Continuous Integration & Continuous Deployment (CI/CD)
Now job is running fine but to make this as Continuous Integration and Continuous Deployment Tod do that go back and modify job as below. 
  - Build Triggers
    - Poll SCM
      - schedule `H/2 * * * *`

Save the job and modify the code in GitHub. Then you could see your job get trigger a build without any manual intervention.



# 8. Ansible installation on Linux Host machine:
------------------------------------------------

##Create A new EC2 instance for Ansible Server. Install Ansible in both Ansible Server & Linux Host Machine(Slave/Client)

#Add EPEL
[root@ip-172-31-23-26 ec2-user]# rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

[root@ip-172-31-23-26 ec2-user]# yum install ansible -y

[root@ip-172-31-23-26 ec2-user]# ansible --version
ansible 2.7.8
  config file = /etc/ansible/ansible.cfg
  configured module search path = [u'/root/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python2.7/site-packages/ansible
  executable location = /usr/bin/ansible
  python version = 2.7.5 (default, Feb 20 2018, 09:19:12) [GCC 4.8.5 20150623 (Red Hat 4.8.5-28)]

[root@ip-172-31-23-26 ec2-user]# useradd ansadmin
[root@ip-172-31-23-26 ec2-user]# passwd ansadmin
Changing password for user ansadmin.
New password:
BAD PASSWORD: The password contains the user name in some form
Retype new password:
passwd: all authentication tokens updated successfully.
[root@ip-172-31-23-26 ec2-user]# vi /etc/sudoers
[root@ip-172-31-23-26 ec2-user]# echo "ansadmin ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
[root@ip-172-31-23-26 ec2-user]# vi /etc/sudoers
[root@ip-172-31-23-26 ec2-user]# sed -ie 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
[ansadmin@ip-172-31-23-26 ec2-user]$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ansadmin/.ssh/id_rsa):
Created directory '/home/ansadmin/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ansadmin/.ssh/id_rsa.
Your public key has been saved in /home/ansadmin/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:ovL1HTbXJgSFSp9K+mFwlmZE8hGBaSM8lLbi5hLH0LM ansadmin@ip-172-31-23-26.ap-south-1.compute.internal
The key's randomart image is:
+---[RSA 2048]----+
|   o...+=o ..    |
|    * =oo...     |
| . . = +.+..     |
|. + . . O o.     |
| + +  .OS.  .    |
|. E  ...+  . .   |
| =. . .o .+ o o  |
|. .o . ..o + o   |
| .  .   . .      |
+----[SHA256]-----+

[ansadmin@ip-172-31-23-26 ec2-user]$ service sshd restart
Redirecting to /bin/systemctl restart sshd.service

[root@ip-172-31-23-26 ec2-user]# su ansadmin
[ansadmin@ip-172-31-23-26 ec2-user]$ ssh-copy-id 172.31.17.253
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/ansadmin/.ssh/id_rsa.pub"
The authenticity of host '172.31.17.253 (172.31.17.253)' can't be established.
ECDSA key fingerprint is SHA256:nfk+xUZo9MQXTN/9wqjALiM1ZLCmEdOTSGHLfltTEcs.
ECDSA key fingerprint is MD5:31:3d:0b:db:7d:eb:7e:5a:56:d5:62:3a:e7:8d:26:50.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
Permission denied (publickey,gssapi-keyex,gssapi-with-mic).


#INstall ansible on Host machine as well. Then follow below commands...


[ansadmin@ip-172-31-23-26 ec2-user]$ ssh-copy-id 172.31.17.253
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/ansadmin/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
ansadmin@172.31.17.253's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh '172.31.17.253'"
and check to make sure that only the key(s) you wanted were added.

[root@ip-172-31-17-253 ansadmin]# echo 172.31.17.253 > /etc/ansible/hosts
[root@ip-172-31-17-253 ansadmin]# su ansadmin
[ansadmin@ip-172-31-17-253 ~]$ ssh '172.31.17.253'
The authenticity of host '172.31.17.253 (172.31.17.253)' can't be established.
ECDSA key fingerprint is SHA256:nfk+xUZo9MQXTN/9wqjALiM1ZLCmEdOTSGHLfltTEcs.
ECDSA key fingerprint is MD5:31:3d:0b:db:7d:eb:7e:5a:56:d5:62:3a:e7:8d:26:50.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '172.31.17.253' (ECDSA) to the list of known hosts.
ansadmin@172.31.17.253's password:
Last login: Mon Mar  4 19:23:30 2019
[ansadmin@ip-172-31-17-253 ~]$ pwd
/home/ansadmin
[ansadmin@ip-172-31-17-253 ~]$ cd /opt
[ansadmin@ip-172-31-17-253 opt]$ ls
apache-tomcat-8.5.38
[ansadmin@ip-172-31-17-253 opt]$ exit
logout
Connection to 172.31.17.253 closed.
[ansadmin@ip-172-31-17-253 ~]$

[root@ip-172-31-23-26 target]# cat /opt/playbooks/copywarfile.yml
---
- hosts: all
  become: true
  tasks:
    - name: copy war file onto tomcat servers
      copy:
          src: /opt/playbooks/webapp/target/webapp.war
          dest: /opt/apache-tomcat-8.5.38/webapps

 
 Create *Jenkins job*, Fill the following details,

   - *Source Code Management:*
      - Repository: `https://github.com/ValaxyTech/hello-world.git`
      - Branches to build : `*/master`  
   - *Build:*
     - Root POM:`pom.xml`
     - Goals and options : `clean install package`
	 
- *Add post-build steps*
    - Send files or execute commands over SSH
      - SSH Server : `ansible_server`
      - Source fiels: `webapp/target/*.war`
      - Remote directory:  `//opt//playbooks`
- *Add post-build steps*
    - Send files or execute commands over ssH
      - SSH Server : `ansible_server`
      - Exec command ansible-playbook /opt/playbooks/copywarfile.yml
		
Execute job and you should be able to seen build has been deployed on Tomcat server. 


# 9. Installing Docker on Amazon Linux server
---------------------------------------------

1.Update packages
```sh 
yum update -y
```
2.Install docker
```sh 
yum install docker -y

# It will throw error
Error downloading packages:
  1:containers-common-0.1.31-8.gitb0b750d.el7.x86_64: [Errno 256] No more mirrors to try.
  1:oci-register-machine-0-6.git2b44233.el7.x86_64: [Errno 256] No more mirrors to try.
  2:docker-client-1.13.1-91.git07f3374.el7.x86_64: [Errno 256] No more mirrors to try.
  2:docker-common-1.13.1-91.git07f3374.el7.x86_64: [Errno 256] No more mirrors to try.
  2:oci-umount-2.3.4-2.git87f9237.el7.x86_64: [Errno 256] No more mirrors to try.
  2:docker-1.13.1-91.git07f3374.el7.x86_64: [Errno 256] No more mirrors to try.
  2:container-selinux-2.77-1.el7_6.noarch: [Errno 256] No more mirrors to try.
  2:docker-rhel-push-plugin-1.13.1-91.git07f3374.el7.x86_64: [Errno 256] No more mirrors to try.
  1:oci-systemd-hook-0.1.18-3.git8787307.el7_6.x86_64: [Errno 256] No more mirrors to try.
  1:atomic-registries-1.22.1-26.gitb507039.el7.x86_64: [Errno 256] No more mirrors to try.
  container-storage-setup-0.11.0-2.git5eaf76c.el7.noarch: [Errno 256] No more mirrors to try.
  python-pytoml-0.1.14-1.git7dea353.el7.noarch: [Errno 256] No more mirrors to try.


Re: RHEL7 - No package docker available. Extras Repo?
https://forums.aws.amazon.com/thread.jspa?messageID=555865

Solution:

sudo yum install yum-utils
sudo yum-config-manager --enable rhui-REGION-rhel-server-extras
sudo yum install docker

[root@ip-172-31-17-253 webapps]# docker --version
Docker version 1.13.1, build 07f3374/1.13.1
[root@ip-172-31-17-253 webapps]# service docker start
Redirecting to /bin/systemctl start docker.service


'sudo groupadd docker' creates the group docker.

usermod -aG docker dockeradmin

[root@ip-172-31-17-253 ec2-user]# sudo service docker restart
Redirecting to /bin/systemctl restart docker.service

[root@ip-172-31-17-253 webapps]# mkdir /opt/docker
[root@ip-172-31-17-253 webapps]# cd /opt/docker
[root@ip-172-31-17-253 docker]# ls
[root@ip-172-31-17-253 docker]# vi Dockerfile
[root@ip-172-31-17-253 docker]# ls
Dockerfile
[root@ip-172-31-17-253 docker]# cat Dockerfile
# Pull base image
FROM tomcat:8-jre8

# Maintainer
MAINTAINER "Rajib"

# copy war file on to container
COPY ./webapp.war /usr/local/tomcat/webapps


# Login to Jenkins console and add Docker server to execute commands from Jenkins  
Manage Jenkins --> Configure system -->  Publish over SSH --> add Docker server and credentials

# Create Jenkins job 

A) Source Code Management  
 Repository : https://github.com/ValaxyTech/hello-world.git  
 Branches to build : */master  

B) Build
 Root POM: pom.xml  
 Goals and options : clean install package  
 
C) send files or execute commands over SSH
 Name: docker_host  
 Source files	: webapp/target/*.war  
 Remove prefix	: webapp/target  
 Remote directory	: //opt//docker  
 Exec command	: docker stop docker_demo; docker rm -f docker_demo; docker rmi -f docker_demo; cd /opt/docker; docker build -t docker_demo .  

D) send files or execute commands over SSH  
  Name: docker_host  
  Exec command	: docker run -d --name docker_demo -p 8090:8080 docker_demo
  
  
  
####Docker COmmands

sudo systemctl stop docker
sudo service docker restart
sudo systemctl start docker
sudo systemctl status docker
sudo systemctl enable docker