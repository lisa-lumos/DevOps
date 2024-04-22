# 12. AWS Cloud for project set up: Lift & Shift
## Intro
Assume there are many servers running different services in your data center, services such as databases, application services, DNS services, etc. And we want to have a pay as you go model, and modernize the application. 

In this project, the vprofile project will be moved to AWS. The EC2 instances will be the VMs for Tomcat, RabbitMQ, Memcache and MySQL Servers. The NGINX service will be replaced by elastic load balancer. 

The EC2 instances will use the auto scaling service, to automatically scale in and out. 

For storage, will use S3 or EFS; and Route 53 for private DNS service. 

## Security Group & Keypairs
EC2 -> create security group -> Security group name: vprofile-ELB-SG; Description: Security group for vprofile prod Load Balancer; Add inbound rules, Type: HTTP, Source: Anywhere; Add rule, Type: HTTPS, Source: Anywhere -> Create security group. 

Next, create security group for the Tomcat instance. Security group name: vprofile-app-sg; Description: Security group for tomcat instances; Inbound rule: Type: Custom TCP; Port range: 8080; Source: Custom, vprofile-ELB-SG; Description: Allow traffic from Vprofile prod ELB -> Create security group. 















## EC2 Instances


## Build and Deploy Artifacts


## Load Balancer & DNS


## Autoscaling Group


## Validate & Summarize






















