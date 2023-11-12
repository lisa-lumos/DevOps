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

ifconfig   # get ip address of vm, then go to that address from the browser
```

Chat-GPT can generate a similar script with "Bash Script to install httpd package, start httpd service, download html template from tooplate.com and deploy to /var/www/html. At the end restart the httpd service and check the status of httpd service". 

If we put out script in the chat window and say "Enhance my script", it will add error handling etc. 

## Variables
Command line: 
```console
SKILL = "DevOps"  # define a variable
echo $SKILL       # retrieve the variable

PACKAGE = "httpd wget unzip"
yum install $PACKAGE -y         # use the variable
```

If hardcoded strings are saved in variables in prev script, then it is convenient to adapt to changes. 

"websetup.sh" to use variables:
```sh
#!/bin/bash

# Variable declaration
PACKAGE="httpd wget unzip"
SVC="httpd"
URL="https://www.tooplate.com/zip-templates/2098_health.zip"
ART_NAME="2098_health"
TEMPDIR="/tmp/webfiles"

# Installing packages
echo "####################################"
echo "Installing packages"
echo "####################################"
sudo yum install $PACKAGE -y > /dev/null
echo

# Start & Enable HTTPD Service
echo "####################################"
echo "Start & Enable HTTPD Service"
echo "####################################"
sudo systemctl start $SVC
sudo systemctl enable $SVC
echo

# Starting Artifact Deployment
echo "####################################"
echo "Starting Artifact Deployment"
echo "####################################"
mkdir -p $TEMPDIR
cd $TEMPDIR
echo

wget $URL > /dev/null
unzip $ART_NAME.zip > /dev/null
sudo cp -r $ART_NAME/* /var/www/html/
echo 

echo "####################################"
echo "Restarting HTTPD service"
echo "####################################"
systemctl restart $SVC
echo

echo "####################################"
echo "Removing tmp files"
echo "####################################"
rm -rf $TEMPDIR
echo

sudo systemctl status $SVC
ls /var/www/html/
```

## Command line arguments
Programmatically take arguments from script. 

In the command line, if you try to access an undefined variable, such as `echo $my_undefined_var`, you will get empty return.

In bash scripting, `$0` is the script name, `$1`, `$2`, ... are the names of arguments that are passed in when running the script, such as `./test.sh abc 123`. In this case, `$0` is `./test.sh`, `$1` is `abc`, and `$2` is `123`. 

"test.sh"
```sh
echo $0
echo $1
echo $2
echo $3
```

"websetup.sh" to use cmd arguments:
```sh
#!/bin/bash

# Variable declaration
PACKAGE="httpd wget unzip"
SVC="httpd"
# URL="https://www.tooplate.com/zip-templates/2098_health.zip"
# ART_NAME="2098_health"
TEMPDIR="/tmp/webfiles"

# Installing packages
echo "####################################"
echo "Installing packages"
echo "####################################"
sudo yum install $PACKAGE -y > /dev/null
echo

# Start & Enable HTTPD Service
echo "####################################"
echo "Start & Enable HTTPD Service"
echo "####################################"
sudo systemctl start $SVC
sudo systemctl enable $SVC
echo

# Starting Artifact Deployment
echo "####################################"
echo "Starting Artifact Deployment"
echo "####################################"
mkdir -p $TEMPDIR
cd $TEMPDIR
echo

wget $1 > /dev/null
unzip $2.zip > /dev/null
sudo cp -r $2/* /var/www/html/
echo 

echo "####################################"
echo "Restarting HTTPD service"
echo "####################################"
systemctl restart $SVC
echo

echo "####################################"
echo "Removing tmp files"
echo "####################################"
rm -rf $TEMPDIR
echo

sudo systemctl status $SVC
ls /var/www/html/
```

To run this, `./websetup.sh https://www.tooplate.com/zip-templates/2091_ziggy.zip 2091_ziggy`. 

In this way, this code can be used to deploy any websites. 

## System variables
Some system variables:
- `$0`: the name of the bash script
- `$1` to `$9`: the first 9 args to the Bash script
- `$#`: Num of args supplied to the Bash script
- `$@`: All the args supplied to the Bash script
- `$?`: The exist status of the most recently run process. Non-zero means last one failed
- `$$`: The process ID of the current script
- `$USER`: The username of the user running the script
- `$HOSTNAME`: The hostname of the machine the script os running on
- `$SECONDS`: Num of secs since the script was started. 
- `$RANDOM`: Returns a different random number each time it is referred to
- `LINENO`: Returns the current line number in the Bash script

```console
free -m
echo $?       # will return 0, meaning prv cmd was successful. 

freeeeee -m
echo $?       # will return 127, meaning prv cmd has failed

free -mabcdefg 
echo $?       # will return 1, meaning prv cmd has failed
```

## Quotes
You cannot refer to a variable's value using `$...` in single quotes. It will return the literal string you entered. So you need to use variable in double quotes, but put variable-like literal value after `\`, means to skip special character. 

```console
SKILL = "DevOps"
echo $SKILL     # will print: DevOps

SKILL = 'DevOps'
echo $SKILL     # will print: DevOps

echo "I have $SKILL skill."     # will print: I have DevOps skill.
echo 'I have $SKILL skill.'     # will print: I have $SKILL skill.

VIRUS = "covid19"
echo "Due to $VIRUS, companies have lost $9 million."
# will print: Due to covid19, companies have lost  million.
# this is because $9 in here actually means the 9th argument

echo 'Due to $VIRUS, companies have lost $9 million.'
# will print: Due to $VIRUS, companies have lost $9 million.

echo "Due to $VIRUS, companies have lost \$9 million."
# will print: Due to covid19, companies have lost $9 million.
```

## Command Substitution
To store command output to a variable. 
```console
# put a command into back ticks, or $(...), 
# it will take the output of the command, and store it into a variable
my_var1 = `ls` 
my_var2 = $(ls)

echo my_var1

free_ram_size = `free -m | grep Mem | awk '{print $4}'`
echo "Free RAM is $free_ram_size mb"
```

## Exporting variables
Variables defined in the script live with it and die after the script completes. To define a variable accessible to all the scripts from the current shell, make variables permanent for a user or every user in the system, we need to export it. 

Variables are temporary in the process. When the process is dead, the variable goes with it.

```console
SEASON="Monsoon"
echo $SEASON    # prints the value
exit

sudo -i
echo $SEASON    # the value is gone
```

"testvars.sh":
```sh
#!/bin/bash

echo "The $SEASON season is longer this time. "
```

In the command line:
```console
chmod +x testvars.sh
SEASON ="Monsoon"
echo $SEASON    # the value exists in the parent shell

./testvars.sh   # but not in the child shell here
# prints: The  season is longer this time. 

export SEASON
./testvars.sh   # The variable is now accessible from the child shell
# prints: The Monsoon season is longer this time. 

```

Exporting the variable will make the variable global for all the other child shell. But this is still temporary. If we log out, it will be gone again. 

To make this permanent for a user, need to add it to the ".bashrc", or ".profile", in that user's home dir. e.g., append `export SEASON="Monsoon"` to the ".bashrc" file, it will get executed when the user logs in (get sourced). 

To make this permanent for every user globally, you need to append the export command for this var to "/etc/profile" file. 

Note that ".bashrc" and ".profile" takes precedence over "/etc/profile" file, when var with same name is set to different values in these 2 files. Because ".bashrc" is more specific. 

## User Input



## Decision making


## Monitoring script


## loops


## While loops


## Remote command execution


## SSH key exchange


## Finale














