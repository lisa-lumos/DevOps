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

## EBS


## ELB


## Cloud watch


## EFS


## S3


## RDS














