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














