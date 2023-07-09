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


















