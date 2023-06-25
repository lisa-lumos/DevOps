# 5. Vagrant and Linux Servers
## Vagrant IP, RAM, and CPU
To see the status of all vms:
```console
(base) lisa@mac16 fedora % vagrant global-status
id       name    provider       state       directory                           
--------------------------------------------------------------------------------
3a48b5a  default vmware_desktop not running /Users/lisa/Desktop/vms/centos7     
9840f6c  default vmware_desktop suspended   /Users/lisa/derivedData/vms/ubuntu  
87484d8  default vmware_desktop running     /Users/lisa/derivedData/vms/fedora  
 
The above shows information about all known Vagrant environments
on this machine. This data is cached and may not be completely
up-to-date (use "vagrant global-status --prune" to prune invalid
entries). To interact with any of the machines, you can go to that
directory and run Vagrant, or you can use the ID directly with
Vagrant commands from any directory. For example:
"vagrant destroy 1a2b3c4d"

(base) lisa@mac16 fedora % vagrant global-status --prune      # prune invalid entries
id       name    provider       state   directory                           
----------------------------------------------------------------------------
87484d8  default vmware_desktop running /Users/lisa/derivedData/vms/fedora  
 
The above shows information about all known Vagrant environments
on this machine. This data is cached and may not be completely
up-to-date (use "vagrant global-status --prune" to prune invalid
entries). To interact with any of the machines, you can go to that
directory and run Vagrant, or you can use the ID directly with
Vagrant commands from any directory. For example:
"vagrant destroy 1a2b3c4d"

(base) lisa@mac16 fedora % vagrant status     # status of vm in cur dir
Current machine states:

default                   running (vmware_desktop)

The VM is running. To stop this VM, you can run `vagrant halt` to
shut it down, or you can run `vagrant suspend` to simply suspend
the virtual machine. In either case, to restart it again, run
`vagrant up`.

```

Current dir has not only the "Vagrantfile", but also a hidden dir, which was created when running `vagrant up` for the first time. This is from where `vagrant status` etc commands are pulling data from. 
```console
(base) lisa@mac16 fedora % ls -la
total 8
drwxr-xr-x@ 4 lisa  staff  128 Mar 19 12:32 .
drwxr-xr-x@ 6 lisa  staff  192 Mar 19 12:52 ..
drwxr-xr-x@ 5 lisa  staff  160 Mar 19 12:32 .vagrant
-rw-r--r--@ 1 lisa  staff  311 Jan 29 20:30 Vagrantfile
```

The "Vagrantfile" is a Ruby script, and all lines that start with "# " will be ignored. This is same for bash/Python scripts. 
- `config.vm.network "private_network", ip: "192.168.56.12"` means set a static IP. Host-only Adapter. 
- `config.vm.network "public_network"` means fetch an IP address from a wireless router, which will be dynamic. Bridge interface. 
- The default is the NAT adapter. It will have the same IP address for every VM. 
- Inside `config.vm.provider`, you can change the amount of memory and num of CPUs the vm has, which then overrides the default vals. 

After updating the "Vagrantfile", run `vagrant reload` to apply the changes. 

In Ubuntu, run `ifconfig` to show all the network interfaces. 

## Vagrant Sync Directories
When you create a VM with vagrant, it will sync this vagrant dir, in this case, `vms/fedora` of your machine, with `/vagrant` dir inside the vm. This happens by default. 

Inside the VM, if you create new files in this folder, it will sync to the vm directory of your machine. Same happens vise versa.

This is called Sync directory in Vagrant. 2 use cases for it:
- To preserve files that lives in the VM, in case the VM is corrupted/deleted. 
- You can use IDEs such as IntelliJ on your machine to edit these files, and no need to login to this vm and use the vim editor etc. Then you can use these files in the vm. 

For example, on my machine, in the shared folder, using your preferred text editor, create a file "script1.sh":
```sh
#!/bin/bash
echo "Welcome to Bash Scripting. "
sudo apt update

echo "Completed successfully. "
```

And to run this shellscript in vm, run:
```console
[root@bazinga devops]# chmod +x *.sh
[root@bazinga devops]# ./script1.sh 
```

Note: this works natively for Windows. For Mac, will need extra config, by adding `config.vm.synced_folder "/Users/lisa/derivedData/vms/fedora/shared-dir",  "/home/vagrant/vagrant-data"` to the config file, for example. After editing the Vagrantfile, run `vagrant reload` to apply new changes. 

## Provisioning/Bootstrapping
Executing your commands/scripts to configure something, when your VM comes up. 

You can put all these commands in the Vagrantfile config section, and vagrant will execute them for you. You need to make sure these commands are not interactive - they do not ask questions. "Vagranfile" example:
```ruby
Vagrant.configure("2") do |config| 
  config.vm.box = "jacobw/fedora35-arm64" 
  config.vm.network "private_network", ip: "192.168.56.12"
  config.vm.provider "vmware_desktop" do |vmware|
    vmware.gui = true
    vmware.allowlist_verified = true
  end
  config.vm.synced_folder "/Users/lisa/derivedData/vms/fedora/shared-dir",  "/home/vagrant/vagrant-data"
  config.vm.provision "shell", inline: <<-SHELL
    yum install httpd wget unzip -y
    mkdir /opt/devopsdir
    free -m
    uptime
  SHELL
end
```

Run `vagrant reload --provision` if the vm already exists:
```console
(base) lisa@mac16 fedora % vagrant reload --provision
...
==> default: Running provisioner: shell...
    default: Running: inline script
    default: Last metadata expiration check: 2:38:44 ago on Sun 11 Jun 2023 07:56:38 AM PDT.
    default: Package httpd-2.4.54-1.fc35.aarch64 is already installed.
    default: Package wget-1.21.2-2.fc35.aarch64 is already installed.
    default: Package unzip-6.0-53.fc35.aarch64 is already installed.
    default: Dependencies resolved.
    default: Nothing to do.
    default: Complete!
    default:                total        used        free      shared  buff/cache   available
    default: Mem:            1950         220        1258           1         471        1705
    default: Swap:           1949           0        1949
    default:  10:35:22 up 0 min,  0 users,  load average: 0.06, 0.02, 0.00
...
```

## Website Setup (static)
Hosting a service/server on Linux. 

When httpd is enabled and running, get the static/dynamic ip:
```console
[root@bazinga ~]# ip addr show
```
and paste in the browser of your machine. If nothing shows up, stop the firewall:
```console
[root@bazinga ~]# systemctl stop firewalld
[root@bazinga ~]# systemctl disable firewalld
Removed /etc/systemd/system/multi-user.target.wants/firewalld.service.
Removed /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.
```

Note that the server data is located in `/var/www/html`: 
```console
[root@bazinga ~]# cd /var/www/html
[root@bazinga html]# touch index.html
[root@bazinga html]# ls
index.html
[root@bazinga html]# vi index.html 
[root@bazinga html]# cat index.html 
Hello world! 
```

As a best practice, when you make config changes to the server, you restart/reload: 
```console
[root@bazinga html]# systemctl restart httpd
```
Refresh the ip page on your local machine, and you can see the new website. 

To get a template from other website, navigate to the `/tmp` folder, then use `wget your-download-link` to download the file to the current folder. `unzip your-file-name.zip` to unzip the file. Navigate to the unzipped folder, `cp -r * /var/www/html` to copy all of the contents to the httpd's website folder. Restart the httpd service, and you should see the website in your machine's browser. 

## Website Setup, Wordpress (with a database)
Create a new vm dir `vms/wordpress`, navigate to the dir, run `vagrant init your-vm-version` to create the vagrant file. Uncomment `config.vm.network "private_network", ip: "192.168.56.13"` to create a static ip (make sure the ip is different from previous vms). Give it a bridge adapter - uncomment `config.vm.network "public_network"`. 

`vagrant up` to bring up the vm. Refer to "wordpress setup on ubuntu site:ubuntu.com" search result to set up. 

References: https://discourse.ubuntu.com/t/install-and-configure-wordpress/13959. 

## Automate Website setup
Infrastructure as code (IAC): Will put the entire infrastructure of the website in the Vagrantfile: Basically, put the commands in the prv chapter into the `config.vm.provision` section, and make then not interactive. 

"Vagranfile":
```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "jacobw/fedora35-arm64" 
  config.vm.network "private_network", ip: "192.168.56.91"
  config.vm.provider "vmware_desktop" do |vmware|
    vmware.gui = true
    vmware.allowlist_verified = true
   # vmware.memory = "1024"
   end

   config.vm.provision "shell", inline: <<-SHELL
   mv /etc/yum.repos.d/fedora-updates.repo /tmp/
   mv /etc/yum.repos.d/fedora-updates-modular.repo /tmp/
   yum clean all
   yum update
   yum install httpd wget unzip -y
	 systemctl start httpd
	 systemctl enable httpd
   mkdir -p /tmp/finance
	 cd /tmp/finance
	 wget https://www.tooplate.com/zip-templates/2119_gymso_fitness.zip
	 unzip -o 2119_gymso_fitness.zip # -o means overwrite existing file
	 cp -r 2119_gymso_fitness/* /var/www/html/
	 systemctl restart httpd
   systemctl stop firewalld
   cd /tmp/ # jump out of the to-be-deleted dir
   rm -rf /tmp/finance # del the dir
   SHELL
end
```

This script has re-usability. You can destroy the vm, but as long as you have the Vagrantfile, you can provision it again anytime you want. 

## Automate Wordpress Setup
Similar as the prv chapter, you can do the same to Wordpress. 



## Multi VM Vagrant file









