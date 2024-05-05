# 12. AWS Cloud for project set up: Lift & Shift
## Intro
Assume there are many servers running different services in your data center, services such as databases, application services, DNS services, etc. And we want to have a pay as you go model, and modernize the application. 

In this project, the vprofile project will be moved to AWS. The EC2 instances will be the VMs for Tomcat, RabbitMQ, Memcache and MySQL Servers. The NGINX service will be replaced by elastic load balancer. 

The EC2 instances will use the auto scaling service, to automatically scale in and out. 

For storage, will use S3 or EFS; and Route 53 for private DNS service. 

## Security Group & Keypairs
EC2 -> create security group -> Security group name: vprofile-ELB-SG; Description: Security group for vprofile prod Load Balancer; Add inbound rules, Type: HTTP, Source: Anywhere; Add rule, Type: HTTPS, Source: Anywhere -> Create security group. 

Next, create security group for the Tomcat instance. Security group name: vprofile-app-sg; Description: Security group for tomcat instances; Inbound rule: Type: Custom TCP; Port range: 8080; Source: Custom, vprofile-ELB-SG; Description: Allow traffic from Vprofile prod ELB -> Create security group. 

Next, create security group for the backend services, including RabbitMQ, memcache and MySQL. Security group name: vprofile-backend-sg; Description: Security Group for VProfile Backend Services; Add rule -> Inbound rules, Type: MYSQL/Aurora, Source: Custom, vprofile-app-sg, Description: Allow 3306 from application servers; Add rule -> Inbound rules, Type: Custom TCP, Port range: 11211, Source: Custom, vprofile-app-sg, Description: Allow tomcat to connect memcache; Add rule -> Inbound rules, Type: Custom TCP, Port range: 5672, Source: Custom, vprofile-app-sg, Description: Allow tomcat to connect RabbitMQ; Add rule -> Inbound rules, Type: All traffic, Source: Custom, vprofile-backend-sg (itself), Description: Allow internal traffic to flow on all ports.  

Also add your own IP into inbound rules into both vprofile-app-sg, and vprofile-backend-sg, for ssh access. (port range of 22, from My IP; 8080 from My IP to access the app server from the browser)

Next, create Key Pairs. In the EC2 page, Key Pairs in the left pane, Create key pair -> Name: vprofile-prod-key; File format: pem (for git bash login); 

## EC2 Instances











## Build and Deploy Artifacts


## Load Balancer & DNS


## Autoscaling Group


## Validate & Summarize






















