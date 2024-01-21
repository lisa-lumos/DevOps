# 11. AWS
Will focus on AWS services related to  SysOps, and DevOps. 

## EC2
Can integrate with other services, such as S3, EFS, RDS, DynamoDB, Lambda, etc. 

Components in a ec2 instance:
- Amazon Machine Image(AMI). Like vagrant boxes. 
- Instance type. Size of your instance, in terms of hardware
- Elastic Block Store (EBS). For data storage. Virtual hard disks. 

AMI primarily have ~8GB of storage for Linux machines, and ~30GB of storage of Windows machines. EBS allow you to add more. 

To create a vm, Instances -> Launch instances. In the User data section, you can enter the command you need the vm to run when the instance comes up(like vagrant provisioning). Such as:
```bash
#!/bin/bash
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd
mkdir /tmp/test1
```

The instance will have a public IP, for accessing from the internet, and a private IP for internal communication. 

Clicking "Connect" button, go to "SSH client" tab, it will show you how to connect via ssh. The user name for most of the AWS AMIs are "ec2-user". After connecting to the vm, run
```console
sudo -i
systemctl status httpd      # should see it up and running
ss -tunlp | grep 80         # see httpd at port 80
curl http://localhost

# accessing the vm's public IP will not show httpd webpage
# because we access it on port 80, while firewall (security group) is only allowing access on port 22
# to set it up, go to Security tab, click on the security group name, 
# "Edit in bound rules", "Add rule", Port range: 80; Source: My IP. 
# You can also add any IPv4 and IPv6. "Save rules".
# Then you can access the httpd page via the public IP
```
Stop instance. 

EC2 instance creation steps:
1. Requirement gathering (os, storage, compute size, services/apps running, dev/QA/Staging/Prod env, login user/owner etc)
2. key pairs (do it before you launch the instance)
3. Security group
4. Instance launch

Below operations follows the steps above. 

In the left pane:
- Network & Security -> Key Pairs -> Create key pair -> Name: tween-dev-nvir (this is based on project name, env, region name), Private key file format: .pem -> Create key pair. This will download the private key to your local machine. 
- Network & Security -> Security Groups -> Create security group -> Security group name: tween-web-dev-sg (this is based on project name, service name, env), Description: tween-web-dev-sg. Add inbound rules, such as all traffic from ipv4 and ipv6. You can search by protocol (from the Type field), such as ssh, if the protocol name is not in the drop down list, you can directly give a port number (for the Port range field). Can set Source as MyIP -> Create security group. 
- Launch Instances -> Key: Name, Value: web01, Resource types: Instances, Volumes, Network interfaces -> Key: Project, value: tween -> Key: Environment, value: prod -> Key: Owner, value: LeadDevOps -> Key pair name: tween-dev-nvir -> Network settings, click Edit -> Firewall (security groups), Select existing security group -> Common security groups: tween-web-dev-sg -> Advance details, User data: (set scripts to run at launch) -> Summary pane -> Can set number of instances to launch, default to 1 -> Launch instance. 
- Go to the instance -> Connect -> see the connection instructions. 
- Open terminal on local machine, use the command in prv step to log in. Run below commands

```console
sudo apt update
sudo apt install apache2 wget unzip -y
wget https://tooplate.com/zip-templates/2128_tween_agency.zip
unzip 2128_tween_agency.zip
cp -r 2128_tween_agency/* /var/www/html/
systemctl restart apache2
```

- We know this service open up on port 80. So go to the instance -> Security pane -> open its security group -> Edit inbound rules -> Add rule -> Type: Custom TCP, Port range: 80, Source: My IP -> Save rules
- Now the website can be accessed via [vm_ip]:80. 

A security group is a virtual firewall, that controls the traffic for one or more instances. You can add rules to each security group, that allow traffic to, or from, it associated instances. Security groups are "stateful" - if you are allowing port 22 to do a ssh to anyone, the same outbound rule will be automatically updated, so you do not need to mention it. 

One key can be used for all the instances, or each instance can have their own keys. The best way is to divide your key, based on the environment. 

When you tag your instances, you can search for it based on the tag, in the UI. 

By default, the public ip of the ec2 instance will change every time you power on/off, but private ip will be the same. 

If you need a static public IP, 
1. In the left pane, Network & Security -> Elastic IPs -> Allocate Elastic IP address -> Allocate. 
2. Actions -> Associate Elastic IP address -> Instance: (choose the ec2 instance) -> Associate

Benefits of elastic IP: you can recreate the instance, and attach the same elastic IP. To the user, the IP remains the same, even though server has changed to behind the scene. 

When the instance gets created, along with it, a few other things gets created, such as the network interface, Volumes(virtual hard disks). The security group, the public IP are for the network interface, not for the instance. 

EC2 Dashboard shows a list of resource types, and how many of each are there. 

To change the size of instance: Actions -> Instance settings -> Change instance type.

If you are building your own images, and for some reason, it doesn't come up. To trouble shoot: Actions -> Monitor and troubleshoot -> Get system log. 

## AWS CLI
What we do through the GUI can also be done through the CLI. To use it, you need to install it on your local computer, using a command from PowerShell. Check the installation with `aws --version`. 

To configure the CLI, you need an IAM User. In the GUI, IAM -> Users -> Add users -> User name: awscli -> Next -> Attach policies directly; Permissions policies: AdministratorAccess -> Next -> Create user. 

Next, click on the user name -> Security credentials -> Create access key -> CLI -> Next -> Create access key -> Download .csv file

Important: do not reveal your access key and secret access key to anybody else. 

```console
aws configure 
# then enter the access key and secrete access key up on prompt
# then default region, such as us-east-1
# default output format: json
# all the above info will be stored as ~/.aws/

aws sts get-caller-identity

aws ec2 describe-instances
```

If you think some keys are compromised, you don't wan to take any chance, so instead of deleting the user, you can also deactivate/delete those access keys. 

Can find docs on all aws commands in their official docs. Can also ask Chat-GPT to generate commands, such as "aws command to create key pair, security group allows port 22 from my ip and launch ec2 instance with ami amazon linux in us-east-1". 

## EBS (Elastic Block Storage)
The virtual hard disks for your EC2 instance. It provides both the virtual hard disk, and the snapshot (the backup of the EBS volume). 

It is a block based storage, and runs EC2 OS, store data from db, file data, etc. 

When you create an EBS volume, you need to specify an availability zone, and your instance should/must be in the same zone where you have the EBS volume. 

EBS volume is automatically replicated within the availability zone, so you have protection at the data center level. But this is within the same availability zone, not in multiple availability zone. 

EBS types:
- General purpose (SSD): generally used for most of your workload.
- Provisioned IOPS: higher input output per secs, suitable for large databases
- Throughput optimized HD: For big data, data warehouses
- Cold HDD: file servers. Low cost. 
- Magnetic. For backups, archives. Low cost. 

Launch instances -> Name: web01, Browse more AMIs, AWS Marketplace AMIs, search for CentOS, Select CentOS Stream 9 -> Continue -> Instance type: t2.micro, Key pair: web-dev-key, Select existing security group -> Configure storage: 10GB of gp2 (general purpose 2) -> Launch instance. 

ssh to it from local machine by `ssh -i key-pair-path ec2-user@public-ip-address-of-vm`, then:
```console
sudo -i
yum install httpd -y
systemctl start httpd
systemctl enable httpd
cd /tmp
wget https://tooplate.com/zip-templates/2128_tween_agency.zip
unzip -o 2128_tween_agency.zip
cp -r 2128_tween_agency/* /var/www/html/
systemctl restart httpd

systemctl status httpd
ls /var/www/html/

```

Go to the instance in the GUI -> Storage tab -> Block devices section -> Can see Volume ID, Volume size -> Click on the Volume ID -> Can name the volume as "web01-ROOT-Volume". Always name your volumes, so it is easier to identify, especially if you have many instances. -> From the top pane, can see its availability zone, such as "us-east-1c". 

Now, assume we need to add extra volume, e.g., we need 5GB for the server images. In the GUI, click Volumes in the left pane, Create Volume -> Volume Type: gp2, Size: 5GB, Availability Zone: us-east-1c (must be in the same AZ as the ec2 instance), Add Tag: key: Name, Value: gymso-web01-Images -> Create Volume.

Actions -> Attach Volume -> Instance: (select the ec2 instance) -> Attach. 

```console
ssh -i key-pair-path ec2-user@public-ip-address-of-vm
sudo -i
cd /var/www/html/
ls    # see the image folder, which will be put in the new separate storage

fdisk -l      
# list all your disks
# you will see /dev/xvda, which is the root volume, who has a partition /dev/xvda1. 
# you will also see /dev/xvdf, which is around 5GB of size, and it has no partition. We need to create a partition for it

df -h
# this will show /dev/xvda1 is mounted to root directory /

fdisk /dev/xvdf
# use m to see all the options, can see n is add a new partition, so hit n and enter
# type p to create a primary partition, default to partition number 1, 
# hit enter so it finds free sector for you
# hit enter to set default last sector
# hit p to print the partition info
# hit w to write and save. 

fdisk -l      
# to see the new partition /dev/xvdf1

mkfs.ext4 /dev/xvdf1    # format the partition to ext4 format

# next step is to mount it, to images folder
cd /var/www/html/
ls images/    # see some data in there, so need to save it somewhere first
mkdir /tmp/img-backups
mv images/* /tmp/img-backups/

ls images/    # should be empty now

# this would do a temporary mount, which will be gone after a machine reboot
mount /dev/xvdf1 /var/www/html/images/
df -h         # can see it has been mounted there, and its size 
# to unmount it:
umount /var/www/html/images/

# to do a permanent mount:
vi /etc/fstab
# append this to the end of file, and save
# dev/xvdf1 /var/www/html/images ext4 defaults 0 0
mount -a      # will mount all entries from prv file
df -h         # can see it has been mounted there, and its size 

mv /tmp/img-backups/* /var/www/html/images/

systemctl restart httpd
systemctl status httpd  # check the status, because sometimes it doesn't allow the mount

ls /var/www/html/images/ # check if the files are there

# if you do not see images in the website, could be SELinux is preventing it
# disable it by:
vi /etc/selinux/config
# change SELINUX=enforcing to SELINUX=disabled
# save the file
# and reboot the machine, with 
reboot

# to unmount it, if not allowed, run lsof
unmount /var/www/html/images
```

Once it is unmounted, in the GUI, Actions -> Detach Volume. Then Actions -> Delete Volume. 

### EBS snapshot
Assume you have a db installed, and you messed up and lost the data. If you already have a snapshot of it, you can recover the db from it. Steps:
1. Unmount partition, so current data is preserved
2. Detach volume
3. Create new volume from snapshot
4. Attach the new volume, that is created from snapshot. 
5. Mount it back

To walk this through, create a new volume, and use it as a database. Create Volume -> Volume type: gp2, Size: 5GB, Availability Zone: us-east-1c (must be in the same AZ as the ec2 instance), Add Tag: key: Name, Value: db01-volume -> Create Volume.

Actions -> Attach Volume -> Instance: (select the ec2 instance) -> Attach. 

```console
fdisk -l      
# list all your disks
# see /dev/xvda, which is the root volume, who has a partition /dev/xvda1. 
# you will also see /dev/xvdf, which is around 5GB of size, and it has no partition. We need to create a partition for it

fdisk /dev/xvdf

fdisk -l      
# to see the new partition /dev/xvdf1

mkfs.ext4 /dev/xvdf1    # format the partition to ext4 format

mkdir -p /var/lib/mysql

vi /etc/fstab
# append this to the end of file, and save
# dev/xvdf1 /var/lib/mysql ext4 defaults 0 0
mount -a      # will mount all entries from prv file
df -h         # can see it has been mounted there, and its size 

cat /etc/os-release   # check linux version

yum install mariadb-server -y

systemctl start mariadb

systemctl status mariadb

ls /var/lib/mysql/      # can see the db data

```

To create a snapshot of the current block storage, Actions -> Create Snapshot -> Description: db volume snapshot, Key: Name, Value: db01-volume-db-SNAP -> Create Snapshot.

Can check the status in Snapshots in the left pane. Wait until it changes from pending to completed. 

```console
ls /var/lib/mysql/      # can see the db data
rm -rf *                # assume you deleted it by mistake
ls

systemctl stop mariadb  # need to first stop the service

unmount /var/lib/mysql/
fdisk -l
```

Detach the corrupted volume. Then, go to the snapshot, Actions -> Create Volume. 

Go to Volumes, and should see this newly created volume. Actions -> Attach Volume. 

```console
df -h                 # might be automatically mounted

mount -a

ls /var/lib/mysql/    # see the data recovered to there
```

## ELB (Elastic Load Balancer)
When you build a cluster of web services, with multiple servers, you will need a single endpoint to access them, which is usually provided through a load balancer. It is the AWS equivalent of Nginx, HAproxy, etc. 

Load balancer ports:
- Frontend port: Listens from the user requests on this port. e.g.: 80, 443, 25, ...
- Backend ports: Services running on OS listening on this port. e.g.: 80, 443, 8080 etc. For example, if you have a Tomcat server running on port 8080, then the load balancer backend port will be 8080. 

The load balancer receives the incoming application/network traffic, and distributes them on the back end to multiple targets, such as EC2 instances, containers, IP addresses, in multiple availability zones. 

3 Types of ELBs in AWS:
1. Classic load balancer. Simplest. Works at the network level in the OSI. Nowadays not used often. 
2. Application load balancer. Only for web traffic. Fits in the OSI layer 7. Routes traffic based on advanced application level info, that includes the content of the request, such as the route/path. 
3. Network load balancer. High performance, expensive. Fits in the OSI layer 4. Can handle millions of requests per second. Can have an elastic IP attached to it, so it got a static IP. 

### Example to use ELB
Create a EC2 instance, and get a website running. Create an AMI (which contains the snapshot and the metadata) from this instance, and launch 1 more instance from it. 

When launching instances from AMI, you can create a template to save all the options in there, so you can quickly launch instance in seconds, which is called "Launch Template". Create launch template -> (make sure to select your AMI in there) -> Select the instance type of t2.micro -> select the key-pair for it -> Select security group -> Add tags -> Create launch template. 

To use this template, select it -> Actions -> Launch instance from template -> can see all the config in the template is already filled up for you, and you can still make changes for them, such as changing tag value from web00 to web02 -> Launch instance. 

In the left pane, Load Balancing -> Target Groups. It is a group of instances, with health checks. Create target group -> Target type: Instances, Target group name: tg-health, Protocol: HTTP. Port: 80. Advanced health check settings: Health check protocol: HTTP. Health check path: / (this is similar to the route after domain name). Port: Traffic port. ..., Success codes: 200. -> Next -> Register targets: select the two instances. Include as pending below (to check the ports, etc of both of the instances) -> Create target group. 

Load Balancers in the left pane. 



Notes: 
- From a snapshot, you can create a volume, but from an AMI, you can create an instance. 
- You can copy an AMI to a different region. Which can be used for moving your EC2 instance from one region to another. 
- You can make you AMI public, or share with other accounts you specify, so other people can have access to it.
- You can create a pipeline to build images automatically, this service in AWS is called "EC2 Image Builder". 
- Target group can check the health of the instance, 

## Cloud watch


## EFS


## S3


## RDS














