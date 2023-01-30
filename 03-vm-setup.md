# 3. VM setup
Virtualization of OS - one computer that runs multiple OS at the same time. It partitions your physical resource into virtual resource. 

In old days before virtualization, for isolation, one service is hosted on one server, but servers are always over-provisioned. It also cause large maintenance overhead. 

Then came VMware, which allow one computer to run multiple OS. So instead of running multiple main services on one computer, we can run multiple OS in the computer and run all services on top of that, so they will still be isolated. These physical computers can be clustered together so you can distribute your VMs. 

Hardware -> Hypervisor -> different VMs with different OS

Host OS: the OS of the physical machine. 

Guest OS: the OS of the virtual machine. 

Snapshot: a way of taking backup of the VM. 

Hypervisor: the software that allows for creation of virtual machines. There are two type of hypervisors:
- Type 1 (bare metal): runs directly on the physical computer. It is only for production. E.g.: VMware ESXi, Xen Hypervisor, Hyper-V, ...
- Type 2: runs as a software, can install on any computer. Mostly for testing purposes. E.g.: Oracle virtualbox, VMware server, ...

`If you want to automate anything, you should know how to do it manually. `

## VM on MacOS M1 chip
The most popular VM creation tool is Oracle VM Virtual box, but it doesn't support Mac OS M1 chip. So we use VMware desktop to create VMs manually and automatically. 

```
1. Install rosetta
  /usr/sbin/softwareupdate --install-rosetta --agree-to-license
	
2. Install vagrant with homebrew
  brew install vagrant
	
3. Create an account on vmware
  https://customerconnect.vmware.com/
	
4. Download & Install VMWare Fusion Tech Preview
  https://customerconnect.vmware.com/downloads/get-download?downloadGroup=FUS-PUBTP-2021H1

5. Create link
  ln -s /Applications/VMWare\ Fusion\ Tech\ Preview.app /Applications/VMWare\ Fusion.app

6. Install vmware provider
  Vagrant vmware Utility
  https://releases.hashicorp.com/vagrant-vmware-utility/1.0.21/vagrant-vmware-utility_1.0.21_x86_64.dmg

7. Install Plugin
  vagrant plugin install vagrant-vmware-desktop

8. Create folder vms/ubuntu
  cd
  mkdir Desktop/vms/ubuntu
  cd Desktop/vms/ubuntu
  vim Vagrantfile

  Copy paste  below content in the Vagrantfile

  Vagrant.configure("2") do |config| 
  config.vm.box = "spox/ubuntu-arm" 
  config.vm.box_version = "1.0.0"
  config.vm.network "private_network", ip: "192.168.56.11"
  config.vm.provider "vmware_desktop" do |vmware|
    vmware.gui = true
    vmware.allowlist_verified = true
    end
  end
	
9. Bring up vm
  Open terminal, Go to the folder where you created Vagrantfile & issue below command.
  vagrant up
  vagrant ssh
          sudo -i
          ip addr show # shows the ip of the vm
  exit
          exit
          vagrant status # check status of vm
           vagrant halt # shut down the vm
  vagrant destroy # delete the vm

10. Create folder vms/fedora
  cd
  mkdir Desktop/vms/fedora
  cd Desktop/vms/fedora
  vim Vagrantfile
 
 Copy paste  below content in the Vagrantfile
  
    Vagrant.configure("2") do |config| 
    config.vm.box = "jacobw/fedora35-arm64" 
    config.vm.network "private_network", ip: "192.168.56.12"
    config.vm.provider "vmware_desktop" do |vmware|
      vmware.gui = true
      vmware.allowlist_verified = true
    end
  end
	
11. Bring up vm
  Open Terminal, go to the folder where you created Vagrantfile & issue below command.
  vagrant up
  vagrant ssh
          sudo -i
          ip addr show
  exit
          exit
           vagrant halt
  vagrant destroy --force # force means do not ask any questions
```
Windows user have option for many other boxes compared to Mac M series chips. 




