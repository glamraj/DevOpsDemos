######################Linux Installation######################################

##Install dependencies

[root@ip-172-31-9-245 ec2-user]# yum install httpd php gcc* glib* gd* -y


[root@ip-172-31-9-245 ec2-user]# /usr/sbin/useradd -m nagios

[root@ip-172-31-9-245 ec2-user]# passwd nagios
Changing password for user nagios.
New password:
BAD PASSWORD: The password is shorter than 8 characters
Retype new password:
passwd: all authentication tokens updated successfully.

[root@ip-172-31-9-245 ec2-user]# /usr/sbin/groupadd nagcmd


[root@ip-172-31-9-245 ec2-user]# usermod -a -G nagcmd nagios
[root@ip-172-31-9-245 ec2-user]# usermod -a -G nagcmd apache


[root@ip-172-31-9-245 ec2-user]# grep nagios /etc/passwd
nagios:x:1002:1003::/home/nagios:/bin/bash

[root@ip-172-31-9-245 ec2-user]# id nagios
uid=1002(nagios) gid=1003(nagios) groups=1003(nagios),1004(nagcmd)

##
[root@ip-172-31-9-245 opt]# wget https://assets.nagios.com/downloads/nagioscore/releases/nagios-4.4.3.tar.gz

[root@ip-172-31-9-245 opt]# ls
docker-lemp  go-server  maven  nagios-4.4.3.tar.gz

[root@ip-172-31-9-245 nagios]# ./configure --with-command-group=nagcmd

Creating sample config files in sample-config/ ...


*** Configuration summary for nagios 4.4.3 2019-01-15 ***:

 General Options:
 -------------------------
        Nagios executable:  nagios
        Nagios user/group:  nagios,nagios
       Command user/group:  nagios,nagcmd
             Event Broker:  yes
        Install ${prefix}:  /usr/local/nagios
    Install ${includedir}:  /usr/local/nagios/include/nagios
                Lock file:  /run/nagios.lock
   Check result directory:  /usr/local/nagios/var/spool/checkresults
           Init directory:  /lib/systemd/system
  Apache conf.d directory:  /etc/httpd/conf.d
             Mail program:  /usr/sbin/sendmail
                  Host OS:  linux-gnu
          IOBroker Method:  epoll

 Web Interface Options:
 ------------------------
                 HTML URL:  http://localhost/nagios/
                  CGI URL:  http://localhost/nagios/cgi-bin/
 Traceroute (used by WAP):


Review the options above for accuracy.  If they look okay,
type 'make all' to compile the main program and CGIs.

[root@ip-172-31-9-245 nagios]# make all

[root@ip-172-31-9-245 nagios]# make install

[root@ip-172-31-9-245 nagios]# make install-init

[root@ip-172-31-9-245 nagios]# make install-commandmode

[root@ip-172-31-9-245 nagios]# make install-config

[root@ip-172-31-9-245 nagios]# make install-webconf
/usr/bin/install -c -m 644 sample-config/httpd.conf /etc/httpd/conf.d/nagios.conf
if [ 0 -eq 1 ]; then \
        ln -s /etc/httpd/conf.d/nagios.conf /etc/apache2/sites-enabled/nagios.conf; \
fi

*** Nagios/Apache conf file installed ***

####

[root@ip-172-31-9-245 nagios]# vi /usr/local/nagios/etc/objects/contacts.cfg
~~change email or other information

[root@ip-172-31-9-245 nagios]# htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin
New password:
Re-type new password:
Adding password for user nagiosadmin

[root@ip-172-31-9-245 nagios]# service httpd start
Redirecting to /bin/systemctl start httpd.service

Check portal: Service started but nagios is still not running.
http://13.233.62.205/nagios/

##Configure nagios plugins####

[root@ip-172-31-9-245 nagios-plugins]# ./configure --with-nagios-user=nagios --with-nagios-group=nagios

[root@ip-172-31-9-245 nagios-plugins]# make

[root@ip-172-31-9-245 nagios-plugins]# make install

#To verify configuration
[root@ip-172-31-9-245 nagios-plugins]# /usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg

Nagios Core 4.4.3
Copyright (c) 2009-present Nagios Core Development Team and Community Contributors
Copyright (c) 1999-2009 Ethan Galstad
Last Modified: 2019-01-15
License: GPL

Website: https://www.nagios.org
Reading configuration data...
   Read main config file okay...
   Read object config files okay...

Running pre-flight check on configuration data...

Checking objects...
        Checked 8 services.
        Checked 1 hosts.
        Checked 1 host groups.
        Checked 0 service groups.
        Checked 1 contacts.
        Checked 1 contact groups.
        Checked 24 commands.
        Checked 5 time periods.
        Checked 0 host escalations.
        Checked 0 service escalations.
Checking for circular paths...
        Checked 1 hosts
        Checked 0 service dependencies
        Checked 0 host dependencies
        Checked 5 timeperiods
Checking global event handlers...
Checking obsessive compulsive processor commands...
Checking misc settings...

Total Warnings: 0
Total Errors:   0

Things look okay - No serious problems were detected during the pre-flight check

[root@ip-172-31-9-245 nagios-plugins]# service nagios start
Redirecting to /bin/systemctl start nagios.service

[root@ip-172-31-9-245 nagios-plugins]# systemctl enable nagios
Created symlink from /etc/systemd/system/multi-user.target.wants/nagios.service to /usr/lib/systemd/system/nagios.service.

[root@ip-172-31-9-245 etc]# vi nagios.cfg
[root@ip-172-31-9-245 etc]# service nagios restart
Redirecting to /bin/systemctl restart nagios.service

[root@ip-172-31-9-245 etc]# ls
cgi.cfg  htpasswd.users  nagios.cfg  objects  resource.cfg
[root@ip-172-31-9-245 etc]# cd objects
[root@ip-172-31-9-245 objects]# ls
commands.cfg  localhost.cfg  switch.cfg     timeperiods.cfg
contacts.cfg  printer.cfg    templates.cfg  windows.cfg
[root@ip-172-31-9-245 objects]# pwd
/usr/local/nagios/etc/objects
[root@ip-172-31-9-245 objects]#

[root@ip-172-31-9-245 objects]# vi localhost.cfg
[root@ip-172-31-9-245 objects]# service nagios restart
Redirecting to /bin/systemctl restart nagios.service

[root@ip-172-31-9-245 objects]# pwd
/usr/local/nagios/etc/objects

[root@ip-172-31-9-245 objects]# vi localhost.cfg
#EC2 Linux Host-1

define service {

    use                     local-service           ; Name of service template to use
    host_name               Host Server-1
    service_description     PING
    check_command           check_ping!1.0,20%!100.0,60%
}

#SYSTEM DOWN issue/PING Issue
https://support.nagios.com/forum/viewtopic.php?f=7&t=52538
https://stackoverflow.com/questions/21981796/cannot-ping-aws-ec2-instance

Add a new EC2 security group inbound rule:

Type: Custom ICMP rule
Protocol: Echo Request
Port: N/A
Source: your choice (I would select Anywhere to be able to ping from any machine)



#################LOCALHOST.CFG - configuration file#######################################################
[root@ip-172-31-9-245 sbin]# cd /usr/local/nagios/etc/objects
[root@ip-172-31-9-245 objects]# cat localhost.cfg
        ###############################################################################
# LOCALHOST.CFG - SAMPLE OBJECT CONFIG FILE FOR MONITORING THIS MACHINE
#
#
# NOTE: This config file is intended to serve as an *extremely* simple
#       example of how you can create configuration entries to monitor
#       the local (Linux) machine.
#
###############################################################################



###############################################################################
#
# HOST DEFINITION
#
###############################################################################

# Define a host for the local machine

define host {

    use                     linux-server            ; Name of host template to use
                                                    ; This host definition will inherit all variables that are defined
                                                    ; in (or inherited by) the linux-server host template definition.
    host_name               localhost
    alias                   localhost
    address                 127.0.0.1
}

#EC2 Linux Host-1

define host {

    use                     linux-server            ; Name of host template to use
                                                    ; This host definition will inherit all variables that are defined
                                                    ; in (or inherited by) the linux-server host template definition.
    host_name               Host-Server-1
    alias                   Host-Server-1
    address                 172.31.9.248
}



###############################################################################
#
# HOST GROUP DEFINITION
#
###############################################################################

# Define an optional hostgroup for Linux machines

define hostgroup {

    hostgroup_name          linux-servers           ; The name of the hostgroup
    alias                   Linux Servers           ; Long name of the group
    members                 localhost               ; Comma separated list of hosts that belong to this group
}



###############################################################################
#
# SERVICE DEFINITIONS
#
###############################################################################

# Define a service to "ping" the local machine

define service {

    use                     local-service           ; Name of service template to use
    host_name               localhost
    service_description     PING
    check_command           check_ping!100.0,20%!500.0,60%
}

#EC2 server-1

define service {

    use                     local-service           ; Name of service template to use
    host_name               Host-Server-1
    service_description     PING
    check_command           check_ping!100.0,20%!500.0,60%
}


# Define a service to check the disk space of the root partition
# on the local machine.  Warning if < 20% free, critical if
# < 10% free space on partition.

define service {

    use                     local-service           ; Name of service template to use
    host_name               localhost
    service_description     Root Partition
    check_command           check_local_disk!20%!10%!/
}

#EC2 server-1

define service {

    use                     local-service           ; Name of service template to use
    host_name               Host-Server-1
    service_description     Root Partition
    check_command           check_local_disk!20%!10%!/
}



# Define a service to check the number of currently logged in
# users on the local machine.  Warning if > 20 users, critical
# if > 50 users.

define service {

    use                     local-service           ; Name of service template to use
    host_name               localhost
    service_description     Current Users
    check_command           check_local_users!20!50
}

#EC2 server-1

define service {

    use                     local-service           ; Name of service template to use
    host_name               Host-Server-1
    service_description     Current Users
    check_command           check_local_users!20!50
}


# Define a service to check the number of currently running procs
# on the local machine.  Warning if > 250 processes, critical if
# > 400 processes.

define service {

    use                     local-service           ; Name of service template to use
    host_name               localhost
    service_description     Total Processes
    check_command           check_local_procs!250!400!RSZDT
}

#EC2 server-1

define service {

    use                     local-service           ; Name of service template to use
    host_name               Host-Server-1
    service_description     Total Processes
    check_command           check_local_procs!250!400!RSZDT
}


# Define a service to check the load on the local machine.

define service {

    use                     local-service           ; Name of service template to use
    host_name               localhost
    service_description     Current Load
    check_command           check_local_load!5.0,4.0,3.0!10.0,6.0,4.0
}

#EC2 server-1

define service {

    use                     local-service           ; Name of service template to use
    host_name               Host-Server-1
    service_description     Current Load
    check_command           check_local_load!5.0,4.0,3.0!10.0,6.0,4.0
}


# Define a service to check the swap usage the local machine.
# Critical if less than 10% of swap is free, warning if less than 20% is free

define service {

    use                     local-service           ; Name of service template to use
    host_name               localhost
    service_description     Swap Usage
    check_command           check_local_swap!20%!10%
}

#EC2 server-1

define service {

    use                     local-service           ; Name of service template to use
    host_name               Host-Server-1
    service_description     Swap Usage
    check_command           check_local_swap!20%!10%
}



# Define a service to check SSH on the local machine.
# Disable notifications for this service by default, as not all users may have SSH enabled.

define service {

    use                     local-service           ; Name of service template to use
    host_name               localhost
    service_description     SSH
    check_command           check_ssh
    notifications_enabled   0
}

#EC2 server-1

define service {

    use                     local-service           ; Name of service template to use
    host_name               Host-Server-1
    service_description     SSH
    check_command           check_ssh
    notifications_enabled   0
}


# Define a service to check HTTP on the local machine.
# Disable notifications for this service by default, as not all users may have HTTP enabled.

define service {

    use                     local-service           ; Name of service template to use
    host_name               localhost
    service_description     HTTP
    check_command           check_http
    notifications_enabled   0
}

#EC2 server-1

define service {

    use                     local-service           ; Name of service template to use
    host_name               Host-Server-1
    service_description     HTTP
    check_command           check_http
    notifications_enabled   0
}
#####################################################################################################



######################Ubuntu Installation######################################
Step 1 – Prerequisite
We assume that you have fresh installed Ubuntu/Debian systems, So our first requirement is to install Apache and PHP first. Use the following commands to complete it. You can find detailed LAMP setup instruction here.

Let’s use following commands to install required packages for Nagios.

sudo apt-get update
sudo apt-get install wget build-essential unzip openssl libssl-dev
sudo apt-get install apache2 php libapache2-mod-php php-gd libgd-dev 
2Step 2 – Create Nagios User
First of all, create a new user account for Nagios in your system and assign a password.

sudo adduser nagios
Now create a group for Nagios setup “nagcmd” and add nagios user to this group. Also, add nagios user in the Apache group.

sudo groupadd nagcmd
sudo usermod -a -G nagcmd nagios
sudo usermod -a -G nagcmd www-data
Step 3 – Install Nagios Core Service
After installing required dependencies and adding user accounts. Let’s start with Nagios core installation. Download latest Nagios core service from the official site.

cd /opt/
wget https://assets.nagios.com/downloads/nagioscore/releases/nagios-4.4.3.tar.gz
tar xzf nagios-4.4.3.tar.gz
After extracting naviate to nagios source directory and install using make command.

cd nagios-4.4.3
sudo ./configure --with-command-group=nagcmd
sudo make all
sudo make install
sudo make install-init
sudo make install-daemoninit
sudo make install-config
sudo make install-commandmode
sudo make install-exfoliation
Now copy event handlers scripts under libexec directory. These binaries provides multiple events triggers for your Nagios web interface.

cp -R contrib/eventhandlers/ /usr/local/nagios/libexec/
chown -R nagios:nagios /usr/local/nagios/libexec/eventhandlers
Step 4 – Setup Apache with Authentication
Now create an Apache configuration file for your Nagios server as below:

sudo vi /etc/apache2/conf-available/nagios.conf
/etc/apache2/conf-available/nagios.conf
ScriptAlias /nagios/cgi-bin "/usr/local/nagios/sbin"

<Directory "/usr/local/nagios/sbin">
   Options ExecCGI
   AllowOverride None
   Order allow,deny
   Allow from all
   AuthName "Restricted Area"
   AuthType Basic
   AuthUserFile /usr/local/nagios/etc/htpasswd.users
   Require valid-user
</Directory>

Alias /nagios "/usr/local/nagios/share"

<Directory "/usr/local/nagios/share">
   Options None
   AllowOverride None
   Order allow,deny
   Allow from all
   AuthName "Restricted Area"
   AuthType Basic
   AuthUserFile /usr/local/nagios/etc/htpasswd.users
   Require valid-user
</Directory>
We need to setup apache authentication for user nagiosadmin. Until more specific reason use this username as “nagiosadmin” else you would require more changes in configuration.

htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin
Now enable Apache configuration and restart Apache service to make the new settings take effect.

sudo a2enconf nagios
sudo a2enmod cgi rewrite
sudo service apache2 restart
Step 5 – Installing Nagios Plugins
After installing and configuring Nagios core service, Download latest nagios-plugins source and install using following commands.

cd /opt
wget http://www.nagios-plugins.org/download/nagios-plugins-2.2.1.tar.gz
tar xzf nagios-plugins-2.2.1.tar.gz
cd nagios-plugins-2.2.1
Now compile and install Nagios plugins

sudo ./configure --with-nagios-user=nagios --with-nagios-group=nagios --with-openssl
sudo make
sudo make install
Step 6 – Verify Settings
Use the Nagios commands to verify the Nagios installation and configuration file. After successfully verify start the Nagios core service.

/usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg
service nagios start
Also configure Nagios to auto start on system boot.

sudo systemctl enable nagios
Step 7 – Access Nagios Web Interface
Access your nagios setup by access nagios server using hostname or ip address followed by /nagios.
[change domain name with your domain or ip]

 http://svr1.tecadmin.net/nagios/
Prompting for Apache Authentication Password –

Install Nagios 1

Nagios After login screen –

Install Nagios - Dashboard

Finally, You have successfully installed and configured Nagios Monitoring Server core service in your system. Now visit net article to monitor Linux host and Windows host using Nagios server.


------------------
NRPE (Nagios Remote Plugin Executor) is used for executing Nagios plugins on remote client systems. In previous article we had described about installation of Nagios Server on Ubuntu operating system. This article will help you to install NRPE on Ubuntu 18.04 LTS, 16.04 LTS & LinuxMint 19/18 systems.

Install NRPE

Step 1 – Install NRPE on Ubuntu
NRPE is available under default apt repositories of Ubuntu systems. Execute the following command to install it

sudo apt-get update
sudo apt-get install nagios-nrpe-server nagios-plugins
Step 2 – Configure NRPE
In NRPE configuration, first we need to nrpe to which nagios servers it accepts requests, For example your nagios server ip is 192.168.1.100, then add this ip to allowed hosts list. Edit NRPE configuration file /etc/nagios/nrpe.cfg and make changes like

/etc/nagios/nrpe.cfg
 allowed_hosts=127.0.0.1, 192.168.1.100
we can add more Nagios servers in allowed hosts by comma separated list.

Now restart NRPE service. Now its ready to listen to requests from Nagios server

sudo /etc/init.d/nagios-nrpe-server restart
Step 3 – Verify Connection
Let’s verify the connection between the Nagios server and NRPE client machine. Login to your Nagios server and check the Nagios server can communicate with NRPE service properly.

Use check_nrpe command on Nagios server under plugins directory. The command will be like as below here 192.168.1.11 is the IP address of client machine.

check_nrpe -H 192.168.1.11

NRPE v2.15
The output “NRPE v2.15” shows that the Nagios server successfully communicated with NRPE.

Step 4 – Add Check Commands in NRPE
All the services check commands with the nagios plugins packages, which is by default installed in /usr/lib/nagios/plugins/ for 32 bit systems. Default installation adds few commands in configuration file. Add more commands as per your requirements like below

vim /etc/nagios/nrpe.cfg
/etc/nagios/nrpe.cfg
command[check_users]=/usr/lib/nagios/plugins/check_users -w 5 -c 10
command[check_load]=/usr/lib/nagios/plugins/check_load -w 15,10,5 -c 30,25,20
command[check_hda1]=/usr/lib/nagios/plugins/check_disk -w 20% -c 10% -p /dev/hda1
command[check_zombie_procs]=/usr/lib/nagios/plugins/check_procs -w 5 -c 10 -s Z
command[check_total_procs]=/usr/lib/nagios/plugins/check_procs -w 150 -c 200
Step 5 – Start/Stop NRPE Service
Use following commands to start, stop or restart NRPE service. Each time we make any changes in configuration file required to restart service

sudo /etc/init.d/nagios-nrpe-server stop
sudo /etc/init.d/nagios-nrpe-server start
sudo /etc/init.d/nagios-nrpe-server restart

-------------------------------------------------------------------------------------------

