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




























