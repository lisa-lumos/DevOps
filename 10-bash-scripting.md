# 10. Bash Scripting
To automate day to day tasks, sys admins write bash scripts in Linux system. It is just commands in a text file, that can be executed whenever we want. 

## First script
"Vagrantfile":
```ruby
Vagrant.configure("2") do |config|
  config.vm.define "scriptbox" do |scriptbox|
    scriptbox.vm.box = "geerlingguy/centos7"
    scriptbox.vm.network "private_network", ip: "192.168.10.12"
    scriptbox.vm.hostname = "scriptbox"
    scriptbox.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
    end
  end

  config.vm.define "web01" do |web01|
    web01.vm.box = "geerlingguy/centos7"
    web01.vm.network "private_network", ip: "192.168.10.13"
    web01.vm.hostname = "web01"
  end
  
  config.vm.define "web02" do |web02|
    web02.vm.box = "geerlingguy/centos7"
    web02.vm.network "private_network", ip: "192.168.10.14"
    web02.vm.hostname = "web02"
  end

  config.vm.define "web03" do |web03|
    web03.vm.box = "ubuntu/bionic64"
    web03.vm.network "private_network", ip: "192.168.10.15"
    web03.vm.hostname = "web03"
  end
end
```

Commands:
```console
vagrant up scriptbox
vagrant ssh scriptbox

vi /etc/hostname  # open this file, save "scriptbox" inside as hostname
hostname scriptbox
logout
logout

vagrant ssh scriptbox
hostname          # should return "scriptbox"
sudo -i

mkdir /opt/scripts
cd /opt/scripts
yum install vim -y
vim firstscript.sh
```

"firstscript.sh":
```sh
#!/bin/bash     # the shebang character, path of the interpreter to execute the script

# This script prints system info
echo "Welcome to bash script"
echo

echo "###########################################"
echo "The uptime of the system is: "
uptime
echo

echo "###########################################"
echo "Memory Utilization: "
free -m
echo

echo "###########################################"
echo "Disk Utilization: "
df -h
```

To run a script:
```console
ls -l                     # shows permissions
chmod +x firstscript.sh   # give root executable permission
./firstscript.sh
/opt/scripts/firstscript.sh  # absolute path also work
```

## Sample Script
"websetup.sh"
```sh
#!/bin/bash

# Installing packages
echo "####################################"
echo "Installing packages"
echo "####################################"
sudo yum install wget unzip httpd -y > /dev/null
echo

# Start & Enable HTTPD Service
echo "####################################"
echo "Start & Enable HTTPD Service"
echo "####################################"
sudo systemctl start httpd
sudo systemctl enable httpd
echo

# Starting Artifact Deployment
echo "####################################"
echo "Starting Artifact Deployment"
echo "####################################"
mkdir -p /tmp/webfiles
cd /tmp/webfiles
echo

wget https://www.tooplate.com/zip-templates/2098_health.zip > /dev/null
unzip 2098_health.zip > /dev/null
sudo cp -r 2098_health/* /var/www/html/
echo 

echo "####################################"
echo "Restarting HTTPD service"
echo "####################################"
systemctl restart httpd
echo

echo "####################################"
echo "Removing tmp files"
echo "####################################"
rm -rf /tmp/webfiles
echo

sudo systemctl status httpd
ls /var/www/html/
```

To execute it:
```console
chmod +x /opt/scripts/websetup.sh
/opt/scripts/websetup.sh

ipconfig   # get ip address of vm, then go to that address from the browser
```

Chat-GPT can generate a similar script with "Bash Script to install httpd package, start httpd service, download html template from tooplate.com and deploy to /var/www/html. At the end restart the httpd service and check the status of httpd service". 

If we put out script in the chat window and say "Enhance my script", it will add error handling etc. 

## Variables



















