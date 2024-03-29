# VProfile Project Setup
This project sets up a multi-tier web application stack, locally. This project is the baseline for other upcoming projects, and by setting up the lab locally, you can do all your R&D. 

## Project intro
Assume you are working on a project, with uses many different services. Maybe MySQL/PostgreSQL for database services, Nginx for web services, Tomcat/JBoss/GlassFish as application services, etc. 

You also runbook/setup doc to setup your project stack. 

But we still lack of confidence in making changes in the real systems. So you want to setup all that stack on your local machine, by using VMs. However, local setup is complex, time-consuming, and not repeatable. 

The solution would be, to have an automated local setup. It will be repeatable, because we will have infrastructure as code (IAAC). 

Therefore, we need a hypervisor, such as Oracle VM Virtualbox, and use Vagrant for automation. We will use Gib Bash for executing commands, and text editor. 

There will be Nginx, Tomcat, RabbitMQ, Memcached and MySQL in the architectural design. 

This web app is a social networking site written in Java. And the work of this project is to set up all the services in VMs, so users can use it. 

In their browser, the user opens the IP address, which is the IP of a load balancer, supported by Nginx service. Nginx is a web service like Apache hppd, and is very commonly used for load balancing. One VM will have Nginx service, and as soon as the request comes, it will route the request to the Tomcat server, which is supported by the Apache Tomcat service (a Java Web Application service). The Java application code sits in here. And if you app needs an external storage, you can use NFS (Network File System) servers. If you have a cluster of servers, and if you need a centralized storage, using NFS is very common in this scenario. 

Next, the user gets a webpage, to login, the login details will be stored in MySQL db. 

There is also RabbitMQ, which is not functional in this project, and is only intended to create more complexity for practice. RabbitMQ is a message broker, or a queueing agent to connect to applications together, so you can stream the data from here. 

When user logs in, before the app runs an sql query to access the user info stored in MySQL db, it first goes to the Memcache service, which is a database caching connected with the MySQL server. When the first time the request comes to the MySQL database, the info will be sent to the Tomcat, then it will be cached to the Memcache server. So the next time the same request comes, it will be accessing the data in the cache service. 

Note that it is very important for a DevOps engineer to understand the flow of the stack, so whenever any issue happens, they can understand where it is originating from. For example, if the webpage is showing, but user cannot login, it is the database service not connected to the app; if the webpage is not showing properly, it is the Tomcat having problem. 

## Manual VM Setup
Note that we need to install vagrant plugin called vagrant-hostmanager. So every vm's "/etc/hosts" file will have the matching name and ip address, same with the ones specified in the vagrant file. 

Multi-vm vagrantfile:
```ruby
Vagrant.configure("2") do |config|
  config.hostmanager.enabled = true 
  config.hostmanager.manage_host = true
  
### DB vm  ####
  config.vm.define "db01" do |db01|
    db01.vm.box = "eurolinux-vagrant/centos-stream-9"
    db01.vm.hostname = "db01"
    db01.vm.network "private_network", ip: "192.168.56.15"
    db01.vm.provider "virtualbox" do |vb|
     vb.memory = "600"
   end

  end
  
### Memcache vm  #### 
  config.vm.define "mc01" do |mc01|
    mc01.vm.box = "eurolinux-vagrant/centos-stream-9"
    mc01.vm.hostname = "mc01"
    mc01.vm.network "private_network", ip: "192.168.56.14"
    mc01.vm.provider "virtualbox" do |vb|
     vb.memory = "600"
   end
  end
  
### RabbitMQ vm  ####
  config.vm.define "rmq01" do |rmq01|
    rmq01.vm.box = "eurolinux-vagrant/centos-stream-9"
  rmq01.vm.hostname = "rmq01"
    rmq01.vm.network "private_network", ip: "192.168.56.13"
    rmq01.vm.provider "virtualbox" do |vb|
     vb.memory = "600"
   end
  end
  
### tomcat vm ###
   config.vm.define "app01" do |app01|
    app01.vm.box = "eurolinux-vagrant/centos-stream-9"
    app01.vm.hostname = "app01"
    app01.vm.network "private_network", ip: "192.168.56.12"
    app01.vm.provider "virtualbox" do |vb|
     vb.memory = "800"
   end
   end
   
  
### Nginx VM ###
  config.vm.define "web01" do |web01|
    web01.vm.box = "ubuntu/jammy64"
    web01.vm.hostname = "web01"
  web01.vm.network "private_network", ip: "192.168.56.11"
  web01.vm.provider "virtualbox" do |vb|
     vb.gui = true
     vb.memory = "800"
   end
end
  
end
```

Before you do `vagrant up` to bring up all the vms, do `vagrant global-status` to make sure no other vm is running. `vagrant status` to see all current machine states. 

```console
vagrant ssh db01    # login to this vm

cat /etc/hosts      # shows the ip address of all 5 vms, and their names
```

Note in a multi-machine env, where the connection between the machines are via IP address. However, IP address may change, so we always use the host name, not the IP addresses. 

To test whether the name resolves to the IP address or not:
```console
ping web01 -c 4     # 4 means 4 counts of ping packets

vagrant ssh web01
cat /etc/hosts 
ping app01 -c 4
exit

vagrant ssh app01
ping db01 -c 4
ping mc01 -c 4
ping rmq01 -c 4
exit
```

## Manual db, cache, queue setup
Note that: A stack means a combination of multiple services. A cluster means a group of systems running the same service. 

When we set up a stack, a good practice is to always do it from the database, or from the backend, so we can start validating, and then, to the frontend. When we stop a stack, it will be reverse this order - first stop the frontend services, such as app servers, web services, then to the backend and databases. 

### MySQL setup
```console
vagrant ssh db01
sudo -i
yum update -y
yum install epel-release -y   # gives access to more packages
yum install git mariadb-server -y
systemctl start mariadb       # package name and service name can be different
systemctl enable mariadb
systemctl status mariadb

# set up the db: 
mysql_secure_installation     # skip root pwd for db, switch to unix_socket authentication, change root pwd, and set the pwd. rmv anonymous users. Disallow root login remotely - should be yes, but set no for testing purposes. rmv test db and its access. reload privilege tables. 

mysql -u root -p[your_pwd_without_space]
> create database accounts;
> show databases
> grant all privileges on accounts.* to 'admin'@'%' identified by 'admin123';
> FLUSH PRIVILEGES;
> exit;

git clone -b main https://github.com/hkhcoder/vprofile-project.git 
cd vprofile-project
mysql -u root -padmin123 accounts < src/main/resources/db_backup.sql    # execute file against the accounts db
mysql -u root -padmin123 accounts   # login to the db
> show tables;    # see the tables already created
> exit;

systemctl restart mariadb     # restart the db service
systemctl status mariadb      # check its status
exit
exit
```

### Memcache setup
```console
vagrant ssh mc01
sudo -i
dnf install epel-release -y
dnf install memcached -y     # install memcache

systemctl start memcached
systemctl enable memcached
systemctl status memcached

# search and replace local ip to 0.0.0.0
# because some services, like Memcache, by default only listens to local connection
# This command allows for remote connection to communicate with it
# so it listens to all IPv4 ip addresses
sed -i 's/127.0.0.1/0.0.0.0/g' /etc/sysconfig/memcached
cat /etc/sysconfig/memcached      # review the changes
systemctl restart memcached       # apply the changes
systemctl status memcached        # check the status, make sure its running

exit
exit
```

### RabbitMQ setup
```console
vagrant ssh rmq01
sudo -i
yum update -y
yum install epel-release -y

dnf -y install centos-release-rabbitmq-38
dnf --enablerepo=centos-rabbitmq-38 -y install rabbitmq-server    # enable repo, and install RabbitMQ server

systemctl start rabbitmq-server
systemctl enable rabbitmq-server

# create config file, and add content
sh -c 'echo "[{rabbit, [{loopback_users, []}]}]." > /etc/rabbitmq/rabbitmq.config'
cat /etc/rabbitmq/rabbitmq.config

rabbitmqctl add_user test test      # add user test, with pwd as test
rabbitmqctl set_user_tags test administrator
systemctl restart rabbitmq-server   # apply config changes
systemctl status rabbitmq-server    # service will not start, if there were config syntax mistake
```

### App setup
Tomcat installation is different from other services that we install via yum/dnf, as they will extract their files in the proper Linux file system structure (log files will go to "/var/log", start up scripts will go to "systemd", configurations will go to "etc". ). For Tomcat service, we will need to set up systemctl for it.

```console
vagrant ssh app01
sudo -i

yum update -y
yum install epel-release -y

# install open jdk 11, because we need to build source code
dnf -y install java-11-openjdk java-11-openjdk-devel
dnf install git maven wget -y       # install git and maven

cd /tmp/
wget https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.75/bin/apache-tomcat-9.0.75.tar.gz
tar xzvf apache-tomcat-9.0.75.tar.gz

# add tomcat user
useradd --home-dir /usr/local/tomcat --shell /sbin/nologin tomcat
cp -r /tmp/apache-tomcat-9.0.75/* /usr/local/tomcat/
ls -ld /usr/local/tomcat/                     # see owner as root
chown -R tomcat.tomcat /usr/local/tomcat      # change owner, required for systemctl
ls -ld /usr/local/tomcat/                     # see new owner as tomcat

vi /etc/systemd/system/tomcat.service         # update tomcat config file to:

[Unit]
Description=Tomcat
After=network.target
[Service]
User=tomcat
WorkingDirectory=/usr/local/tomcat
Environment=JRE_HOME=/usr/lib/jvm/jre
Environment=JAVA_HOME=/usr/lib/jvm/jre
Environment=CATALINA_HOME=/usr/local/tomcat
Environment=CATALINE_BASE=/usr/local/tomcat
ExecStart=/usr/local/tomcat/bin/catalina.sh run
ExecStop=/usr/local/tomcat/bin/shutdown.sh
SyslogIdentifier=tomcat-%i
[Install]
WantedBy=multi-user.target

systemctl daemon-reload                       # reload service to use new config

systemctl start tomcat                        # now systemctl can be used directly
systemctl enable tomcat

# this folder will have bin, conf, lib, logs folders inside it, which is different with services installed via yum/apt, which have these folders in the proper Linux file system structure. 
ls /usr/local/tomcat/ 
```

Code build & deploy:
```console
git clone -b main https://github.com/hkhcoder/vprofile-project.git

# update file with backend server details, if needed
cd vprofile-project
vim src/main/resources/application.properties  

mvn install       # find the artifact "vprofile-v2.war" in "target" folder

systemctl stop tomcat
rm -rf /usr/local/tomcat/webapps/ROOT     # remove the Tomcat default app
cp target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war  # copy our app to loc
systemctl start tomcat        # Tomcat extracts from war file and creates "root" folder

systemctl status tomcat       # will see the app running. 

```

### Nginx setup
In Ubuntu, Nginx service gets started/enabled automatically. 
```console
vagrant ssh web01
sudo -i

cat /etc/hosts
apt update && apt upgrade -y

apt install nginx -y

vi /etc/nginx/sites-available/vproapp       # create config file with contents:
<!-- 
upstream vproapp {
  server app01:8080;
}
server {
  listen 80;
  location / {
    proxy_pass http://vproapp;
  }
} 
-->
# when you access any website from browser, by default, it goes on port 80 for http, and port 443 for https. 
# We set Nginx to listen to prot 80, and route the request to http://vproapp. The vproapp is the config give in the first part of config file. Note that if you have multiple cluster of Tomcat, the first section will have multiple entries, like app01, app02, ...

ls /etc/nginx/sites-available       # loc of config and sites
ls  -l /etc/nginx/sites-enabled     # link
rm -rf /etc/nginx/sites-enabled/default     # rmv default config link

# make our website the default link for Nginx
ln -s /etc/nginx/sites-available/vproapp /etc/nginx/sites-enabled/vproapp
ls  -l /etc/nginx/sites-enabled     # see vproapp links to .../sites-available/vproapp

systemctl restart nginx
systemctl status nginx    # if you make config error, service won't start, you see error here
```

### Validate
```console
vagrant ssh web01
sudo -i

ip addr show        # get ip of web01, which is 192.168.56.11, belongs to Nginx

# In the browser, enter http://192.168.56.11:80. 
# Note that both http and port 80 is the default, 
# so even if you just enter the ip, it will still work. 
# The login page is from Tomcat. 
# Login with admin_vp as user name and pwd. 
# The login details are stored in the db. 

# By far, Nginx, Tomcat and database are all validated. 

# Click the "RabbitMq" button, and see the queue info
# Click on the "All Users" button, and see the user list fetched from db
# Click on any user, see the message "data is from db and dat inserted in cache",
# so you know that the caching service is working fine. Click "Back".
# Now click on the same user id again, should see the message "data is from cache".
# This time is is loaded faster, because from the cache service

vagrant destroy --force       # go to the folder on local machine, destroy all vms. 
```

## Automated
### Intro
Similar design as the prv proj, except here we will have bash scripts got executed automatically. These bash scripts will provision all the services (entire stack). 

### Code
"Vagrantfile":
```ruby
Vagrant.configure("2") do |config|
  config.hostmanager.enabled = true 
  config.hostmanager.manage_host = true
  
  ### DB vm  ####
  config.vm.define "db01" do |db01|
    db01.vm.box = "geerlingguy/centos7"
    db01.vm.hostname = "db01"
    db01.vm.network "private_network", ip: "192.168.56.15"
    db01.vm.provision "shell", path: "mysql.sh"  # this is new
  end
  
  ### Memcache vm  #### 
  config.vm.define "mc01" do |mc01|
    mc01.vm.box = "geerlingguy/centos7"
    mc01.vm.hostname = "mc01"
    mc01.vm.network "private_network", ip: "192.168.56.14"
    mc01.vm.provision "shell", path: "memcache.sh"  # this is new
  end
  
  ### RabbitMQ vm  ####
  config.vm.define "rmq01" do |rmq01|
    rmq01.vm.box = "geerlingguy/centos7"
    rmq01.vm.hostname = "rmq01"
    rmq01.vm.network "private_network", ip: "192.168.56.16"
    rmq01.vm.provision "shell", path: "rabbitmq.sh"  # this is new
  end
  
  ### tomcat vm ###
  config.vm.define "app01" do |app01|
    app01.vm.box = "geerlingguy/centos7"
    app01.vm.hostname = "app01"
    app01.vm.network "private_network", ip: "192.168.56.12"
    app01.vm.provision "shell", path: "tomcat.sh"  # this is new
    app01.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
    end
  end
  
  ### Nginx VM ###
  config.vm.define "web01" do |web01|
    web01.vm.box = "ubuntu/xenial64"
    web01.vm.hostname = "web01"
    web01.vm.network "private_network", ip: "192.168.56.11"
    web01.vm.provision "shell", path: "nginx.sh"  # this is new
  end
  
end
```

"mysql.sh" lives in same dir as Vagrantfile in the local machine, and contains the commands that we manually executed during manual setup:
```sh
#!/bin/bash
DATABASE_PASS='admin123'
sudo yum update -y
sudo yum install epel-release -y
sudo yum install git zip unzip -y
sudo yum install mariadb-server -y

# starting & enabling mariadb-server
sudo systemctl start mariadb
sudo systemctl enable mariadb
cd /tmp/
git clone -b local-setup https://github.com/devopshydclub/vprofile-project.git

# restore the dump file for the application
# to run sql from shell, need to pass login detail, and use "-e" flag to execute. 
sudo mysqladmin -u root password "$DATABASE_PASS"
sudo mysql -u root -p"$DATABASE_PASS" -e "UPDATE mysql.user SET Password=PASSWORD('$DATABASE_PASS') WHERE User='root'"
sudo mysql -u root -p"$DATABASE_PASS" -e "DELETE FROM mysql.user WHERE User='root' AND Host NOT IN ('localhost', '127.0.0.1', '::1')"
sudo mysql -u root -p"$DATABASE_PASS" -e "DELETE FROM mysql.user WHERE User=''"
sudo mysql -u root -p"$DATABASE_PASS" -e "DELETE FROM mysql.db WHERE Db='test' OR Db='test\_%'"
sudo mysql -u root -p"$DATABASE_PASS" -e "FLUSH PRIVILEGES"
sudo mysql -u root -p"$DATABASE_PASS" -e "create database accounts"
sudo mysql -u root -p"$DATABASE_PASS" -e "grant all privileges on accounts.* TO 'admin'@'localhost' identified by 'admin123'"
sudo mysql -u root -p"$DATABASE_PASS" -e "grant all privileges on accounts.* TO 'admin'@'%' identified by 'admin123'"
sudo mysql -u root -p"$DATABASE_PASS" accounts < /tmp/vprofile-project/src/main/resources/db_backup.sql
sudo mysql -u root -p"$DATABASE_PASS" -e "FLUSH PRIVILEGES"

# Restart mariadb-server
sudo systemctl restart mariadb

# starting the firewall and allowing the mariadb to access from port no. 3306
sudo systemctl start firewalld
sudo systemctl enable firewalld
sudo firewall-cmd --get-active-zones
sudo firewall-cmd --zone=public --add-port=3306/tcp --permanent
sudo firewall-cmd --reload
sudo systemctl restart mariadb
```

"memcache.sh":
```sh
#!/bin/bash
sudo yum install epel-release -y
sudo yum install memcached -y
sudo systemctl start memcached
sudo systemctl enable memcached
sudo systemctl status memcached
sudo memcached -p 11211 -U 11111 -u memcached -d
```

"rabbitmq.sh":
```sh
#!/bin/bash
sudo yum install epel-release -y
sudo yum update -y
sudo yum install wget -y
cd /tmp/
wget http://packages.erlang-solutions.com/erlang-solutions-2.0-1.noarch.rpm
sudo rpm -Uvh erlang-solutions-2.0-1.noarch.rpm
sudo yum -y install erlang socat
curl -s https://packagecloud.io/install/repositories/rabbitmq/rabbitmq-server/script.rpm.sh | sudo bash
sudo yum install rabbitmq-server -y
sudo systemctl start rabbitmq-server
sudo systemctl enable rabbitmq-server
sudo systemctl status rabbitmq-server
sudo sh -c 'echo "[{rabbit, [{loopback_users, []}]}]." > /etc/rabbitmq/rabbitmq.config'
sudo rabbitmqctl add_user test test
sudo rabbitmqctl set_user_tags test administrator
sudo systemctl restart rabbitmq-server
```

"tomcat.sh":
```sh
TOMURL="https://archive.apache.org/dist/tomcat/tomcat-8/v8.5.37/bin/apache-tomcat-8.5.37.tar.gz"
yum install java-1.8.0-openjdk -y
yum install git maven wget -y
cd /tmp/
wget $TOMURL -O tomcatbin.tar.gz
EXTOUT=`tar xzvf tomcatbin.tar.gz`
TOMDIR=`echo $EXTOUT | cut -d '/' -f1`
useradd --shell /sbin/nologin tomcat
rsync -avzh /tmp/$TOMDIR/ /usr/local/tomcat8/
chown -R tomcat.tomcat /usr/local/tomcat8

rm -rf /etc/systemd/system/tomcat.service

cat <<EOT>> /etc/systemd/system/tomcat.service
[Unit]
Description=Tomcat
After=network.target

[Service]

User=tomcat
Group=tomcat

WorkingDirectory=/usr/local/tomcat8

#Environment=JRE_HOME=/usr/lib/jvm/jre
Environment=JAVA_HOME=/usr/lib/jvm/jre

Environment=CATALINA_PID=/var/tomcat/%i/run/tomcat.pid
Environment=CATALINA_HOME=/usr/local/tomcat8
Environment=CATALINE_BASE=/usr/local/tomcat8

ExecStart=/usr/local/tomcat8/bin/catalina.sh run
ExecStop=/usr/local/tomcat8/bin/shutdown.sh

RestartSec=10
Restart=always

[Install]
WantedBy=multi-user.target

EOT

systemctl daemon-reload
systemctl start tomcat
systemctl enable tomcat

git clone -b local-setup https://github.com/devopshydclub/vprofile-project.git
cd vprofile-project
mvn install
systemctl stop tomcat
sleep 60
rm -rf /usr/local/tomcat8/webapps/ROOT*
cp target/vprofile-v2.war /usr/local/tomcat8/webapps/ROOT.war
systemctl start tomcat
sleep 120
cp /vagrant/application.properties /usr/local/tomcat8/webapps/ROOT/WEB-INF/classes/application.properties
systemctl restart tomcat
```

"nginx.sh":
```sh
# adding repository and installing nginx		
apt update
apt install nginx -y
cat <<EOT > vproapp
upstream vproapp {
 server app01:8080;
}

server {
  listen 80;
  location / {
    proxy_pass http://vproapp;
  }
}
EOT

mv vproapp /etc/nginx/sites-available/vproapp
rm -rf /etc/nginx/sites-enabled/default
ln -s /etc/nginx/sites-available/vproapp /etc/nginx/sites-enabled/vproapp

#starting nginx service and firewall
systemctl start nginx
systemctl enable nginx
systemctl restart nginx
```

### Execution
Navigate to the project folder, and run `vagrant up`. 

To validate the setup, can login using the ip specified in Vagrantfile, or even just the vm name "web01". 

If you run `vagrant halt`, and validate that the `vagrant status` are in poweroff state, and then run `vagrant up`, it will just bring up all the VMs. Because vagrant will do provisioning only once. 

With this, we have a local setup that is completely automated, repeatable, and has IAC (infra as code). 
