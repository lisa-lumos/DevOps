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
```

## ELB


## Cloud watch


## EFS


## S3


## RDS














