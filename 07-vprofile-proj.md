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
> 











```































