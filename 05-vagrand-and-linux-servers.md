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

## Provisioning















## Website Setup


## Website Setup, Wordpress


## Automate Website setup


## Automate Wordpress Setup


## Multi VM Vagrant file









