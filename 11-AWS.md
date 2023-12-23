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

## AWS CLI


## EBS


## ELB


## Cloud watch


## EFS


## S3


## RDS














