# 4. Linux
Open source software is software with source code that anyone can inspect, modify, and enhance, then redistribute. 

Linux is an open source OS. 

Some Linux principles:
- Everything is a file, including hardware
- Small single purpose programs, which can be chained together for complex operations
- Avoid captive user interface
- Configuration data stored in text file

Why Linux: 
- open source
- community support
- support wide variety of hardware
- allows for customization
- most servers runs on Linux
- automation is easy
- good security

Linux architecture: Hardware -> kernel -> shell -> apps, tools, ...

## List of linux distributions
Popular Linux distros for desktop: Ubuntu Linux, Linux Mint, Arch Linux, Fedora, Debian, OpenSuse, ...

Popular server Linux OS: Red Hat Enterprise Linux (most stable and secure, not open source), Ubuntu Server (open source and popular), CentOS (open source), SUSE Enterprise Linux, ...

RPM based and Debian based varies in packaging method of the software (.rpm vs .deb). 

RPM based: RHEL, CentOS, Oracle Linux

Debian based: Ubuntu Server, Kali Linux. Focus more on user friendliness. 

Prefer debian based for DevOps purpose and red hat based for server purposes. 

## important directories
- Home directories: `/root`, `/home/username`
- User executable: `/bin`, `/user/bin`, `user/local/bin`
- System executables: `/sbin`, `/user/sbin`, `/user/local/sbin`
- Other mount points: `/media`, `/mnt`
- Configuration: `/etc`
- Temporary files: `/tmp`
- Kernels and Bootloader: `/boot`
- Server data: `/var`, `/srv`
- System information: `/proc`, `/sys`
- Shared libraries: `/lib`, `/usr/lib`, `/usr/local/lib`

## Linux commands
```console
vagrant@vagrant:~$ whoami     # prompt content: username@hostname, $ means normal user, ~ means home dir
vagrant
vagrant@vagrant:~$ pwd
/home/vagrant
vagrant@vagrant:~$ ls

vagrant@vagrant:~$ cat /etc/os-release 
NAME="Ubuntu"
VERSION="20.04.3 LTS (Focal Fossa)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 20.04.3 LTS"
VERSION_ID="20.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=focal
UBUNTU_CODENAME=focal

vagrant@vagrant:~$ sudo -i    # switch to root, $ means root user shell, ~ means home dir
root@vagrant:~# whoami
root
root@vagrant:~# pwd
/root
root@vagrant:~# ls
snap
root@vagrant:~# cd /          # go to root dir
root@vagrant:/# pwd
/
root@vagrant:/# ls
bin   cdrom  etc   lib         media  opt   root  sbin  srv       sys  usr      var
boot  dev    home  lost+found  mnt    proc  run   snap  swap.img  tmp  vagrant

root@vagrant:/# cd bin
root@vagrant:/bin# ls         # show user executables in this folder
'['                                   hexdump                            scsi_start
 aa-enabled                           host                               scsi_stop
...

root@vagrant:/# cd /sbin
root@vagrant:/sbin# ls        # show root user executables
aa-remove-unknown      fsck.cramfs                  lvreduce               shadowconfig
aa-status              fsck.ext2                    lvremove               shutdown
...

root@vagrant:/sbin# cd /etc
root@vagrant:/etc# ls         # show configuration files
adduser.conf                   e2scrub.conf     libblockdev          os-release               shadow-
alternatives                   environment      libnl-3              overlayroot.conf         shells
...

root@vagrant:/etc# cat /etc/hostname 
vagrant
root@vagrant:/etc# ls /tmp/   # show temp files
netplan_6ip8lq53
snap.lxd
...

root@vagrant:/# ls /boot/     # show kernel and bootloaders
config-5.4.0-144-generic  initrd.img                    lost+found                    vmlinuz-5.4.0-144-generic
config-5.4.0-89-generic   initrd.img-5.4.0-144-generic  System.map-5.4.0-144-generic  vmlinuz-5.4.0-89-generic
...

root@vagrant:/# ls /proc/     # show system info folder contents
1     117     1611   226   270   3     317    5570   77524  903        cpuinfo      kpageflags    sysrq-trigger
10    118     17     227   271   30    318    5571   78     91         crypto       loadavg       sysvipc
...

root@vagrant:/# uptime
 19:18:19 up 36 min,  1 user,  load average: 0.00, 0.00, 0.09
root@vagrant:/# cat /proc/uptime    # not user readable here
2272.29 3941.59
root@vagrant:/# free -m             # info about the system memory usage
              total        used        free      shared  buff/cache   available
Mem:            719         175         162           1         381         451
Swap:          1437          54        1383

root@vagrant:~# whoami
root
root@vagrant:~# exit
logout
vagrant@vagrant:~$ whoami
vagrant
vagrant@vagrant:~$ ls
vagrant@vagrant:~$ pwd
/home/vagrant

vagrant@vagrant:~$ mkdir dev
vagrant@vagrant:~$ mkdir ops backupdir
vagrant@vagrant:~$ ls
backupdir  dev  ops

vagrant@vagrant:~$ touch testfile1.txt
vagrant@vagrant:~$ ls
backupdir  dev  ops  testfile1.txt
vagrant@vagrant:~$ touch devopsfile{1..10}.txt  # multiplication from 1 to 10
vagrant@vagrant:~$ ls
backupdir  devopsfile10.txt  devopsfile2.txt  devopsfile4.txt  devopsfile6.txt  devopsfile8.txt  ops
dev        devopsfile1.txt   devopsfile3.txt  devopsfile5.txt  devopsfile7.txt  devopsfile9.txt  testfile1.txt

vagrant@vagrant:~$ cp devopsfile1.txt dev/  # copy this file to this dir
vagrant@vagrant:~$ ls dev/
devopsfile1.txt
vagrant@vagrant:~$ ls /home/vagrant/dev/
devopsfile1.txt
vagrant@vagrant:~$ cd /tmp/
vagrant@vagrant:/tmp$ cp /home/vagrant/devopsfile2.txt /home/vagrant/dev/   # using absolute path is a good practice
vagrant@vagrant:/tmp$ ls /home/vagrant/dev/
devopsfile1.txt  devopsfile2.txt

vagrant@vagrant:~$ cp -r dev backupdir/ # copy a directory into a directory. "-r" is one of the options, "cp" is the command, and "dev backupdir/" are arguments
vagrant@vagrant:~$ ls backupdir/
dev

vagrant@vagrant:~$ cp --help

vagrant@vagrant:~$ mv devopsfile3.txt ops/    # move a file to a dir
vagrant@vagrant:~$ mv ops dev/                # move a dir into a dir
vagrant@vagrant:~$ mv testfile1.txt testfile22.txt     # rename this file

vagrant@vagrant:~$ mkdir textdir
vagrant@vagrant:~$ mv *.txt textdir/          # move all files ends with .txt to this dir
vagrant@vagrant:~$ ls
backupdir  dev  textdir
vagrant@vagrant:~$ cd textdir/
vagrant@vagrant:~/textdir$ ls
devopsfile10.txt  devopsfile2.txt  devopsfile5.txt  devopsfile7.txt  devopsfile9.txt
devopsfile1.txt   devopsfile4.txt  devopsfile6.txt  devopsfile8.txt  testfile22.txt

vagrant@vagrant:~/textdir$ rm devopsfile10.txt    # remove this file
vagrant@vagrant:~/textdir$ ls
devopsfile1.txt  devopsfile4.txt  devopsfile6.txt  devopsfile8.txt  testfile22.txt
devopsfile2.txt  devopsfile5.txt  devopsfile7.txt  devopsfile9.txt
vagrant@vagrant:~/textdir$ mkdir mobile
vagrant@vagrant:~/textdir$ rm -r mobile     # remove a dir
vagrant@vagrant:~/textdir$ rm -rf *         # dangerous, remove everything from current dir, cannot recover deleted data

```

## Editors
vim is enhanced vi editor. If not already installed, `sudo apt-get install vim`. 

`vim firstfile.txt` to create a new file, hit "i" to go to insert mode, type some text contents, and hit "esc" key to exit insert mode into command mode, type ":w" to save current edits. ":q" to quit vim, or ":wq" to save and quit at the same time. 

In vim, "o" can insert a new line at the bottom of the file. ":q!" to discard changes and quit. ":se nu" will show line numbers. "shift+j" goes to the bottom of file. "jj" goes to the top of file. "dd" to delete current line, "u" to undo. "/network" searches for network, "n" goes to the next search match. 

In command mode, `:%s/word1/word2` replaces the first word1 into word2 in each line in the file. `:%s/word1/word2/g` replaces all occurances of word1 into word2 in the file (g means global). `:%s/word1//g` removes all occurances of word1 in the file.

```console
vagrant@vagrant:~$ vim firstfile.txt
vagrant@vagrant:~$ cat firstfile.txt    # view the file
Welcome to Linux. 
This is a new line.
vagrant@vagrant:~$ vim firstfile.txt
vagrant@vagrant:~$ cat firstfile.txt 
Welcome to Linux. 
This is a new line. 
This is cool.
```

Types of files in Linux:
| Syntax      | First char in file listing | description
| ----------- | ----------- | ----------- |
| regular file | - | normal files such as text, data, or executable files |
| directory | d | files that are lists of other files |
| link | l | a shortcut that points to the location of the actual file |
| special file | c | mechanism used for input and output, such as files /dev |
| socket | s | a special file that provides inter-process networking protected by the file system's access control |
| pipe | p | a special file that allows processes to communicate with each other without using network socket semantics |

```console
vagrant@vagrant:~$ sudo -i
root@vagrant:~# whoami
root
root@vagrant:~# ls -l     # l means long listing
total 4
drwxr-xr-x 3 root root 4096 Oct 28  2021 snap
root@vagrant:~# mkdir devopsdir
root@vagrant:~# ls -l
total 8
drwxr-xr-x 2 root root 4096 Mar 23 23:01 devopsdir
drwxr-xr-x 3 root root 4096 Oct 28  2021 snap

root@vagrant:~# touch test.txt
root@vagrant:~# ls -l     # the last row starts with a dash, means it is an actual file; first two rows start with a d, means they are directories
total 8
drwxr-xr-x 2 root root 4096 Mar 23 23:01 devopsdir
drwxr-xr-x 3 root root 4096 Oct 28  2021 snap
-rw-r--r-- 1 root root    0 Mar 23 23:03 test.txt

root@vagrant:~# vim test.txt      # add some text into this file
root@vagrant:~# file test.txt     # shows it is ascii text file
test.txt: ASCII text
root@vagrant:~# cd /bin
root@vagrant:/bin# file apt-get   # result shows it is a binary file
apt-get: ELF 64-bit LSB shared object, ARM aarch64, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux-aarch64.so.1, BuildID[sha1]=14b73cbee4048e51aadefd2f632143b0962168dd, for GNU/Linux 3.7.0, stripped
root@vagrant:~# cd
root@vagrant:~# file devopsdir/
devopsdir/: directory

root@vagrant:~# cd /dev
root@vagrant:/dev# ls -l    # first row starts with c, means keyboard character device; third row starts with b, means it is the hard disk, b means block file; 4th row starts with l, means link, see the -> sign, which points to the original file
total 0
crw-r--r-- 1 root root     10, 235 Mar 23 03:31 autofs
drwxr-xr-x 2 root root         320 Mar 22 21:29 block
brw-rw---- 1 root disk    253,   0 Mar 23 03:31 dm-0
lrwxrwxrwx 1 root root           3 Mar 23 03:31 dvd -> sr0
...

root@vagrant:~# cd
root@vagrant:~# ls
devopsdir  snap  test.txt
root@vagrant:~# mkdir -p /opt/dev/ops/devops/test             # create a directory tree, whether already exists or not exists
root@vagrant:~# vim /opt/dev/ops/devops/test/commands.txt     # create a new file, and add contents inside
root@vagrant:~# cat /opt/dev/ops/devops/test/commands.txt
ls
pwd
whoami
cd
uptime
touch
mkdir

root@vagrant:~# ln -s /opt/dev/ops/devops/test/commands.txt cmds    # create a link, s means soft link
root@vagrant:~# ls -l
total 12
lrwxrwxrwx 1 root root   37 Mar 23 23:24 cmds -> /opt/dev/ops/devops/test/commands.txt
...

root@vagrant:~# mv /opt/dev/ops/devops/test/commands.txt /tmp/
root@vagrant:~# ls -l     # first row highlights in red, because it now points to a file that does not exist. 
total 12
lrwxrwxrwx 1 root root   37 Mar 23 23:24 cmds -> /opt/dev/ops/devops/test/commands.txt
...
root@vagrant:~# mv /tmp/commands.txt /opt/dev/ops/devops/test/
root@vagrant:~# ls -l     # moved it back, the highlights are gone
total 12
lrwxrwxrwx 1 root root   37 Mar 23 23:24 cmds -> /opt/dev/ops/devops/test/commands.txt
...
root@vagrant:~# unlink cmds     # removes the link file
root@vagrant:~# ls -l           # link is gone
total 12
...
root@vagrant:~# ln -s /opt/dev/ops/devops/test/commands.txt cmds

root@vagrant:~# ls -l     # by default, sort by file name
total 12
lrwxrwxrwx 1 root root   37 Mar 24 03:27 cmds -> /opt/dev/ops/devops/test/commands.txt
drwxr-xr-x 2 root root 4096 Mar 23 23:01 devopsdir
drwxr-xr-x 3 root root 4096 Oct 28  2021 snap
-rw-r--r-- 1 root root   22 Mar 23 23:05 test.txt

root@vagrant:~# ls -lt    # sort by timestamp, latest first
total 12
lrwxrwxrwx 1 root root   37 Mar 24 03:27 cmds -> /opt/dev/ops/devops/test/commands.txt
-rw-r--r-- 1 root root   22 Mar 23 23:05 test.txt
drwxr-xr-x 2 root root 4096 Mar 23 23:01 devopsdir
drwxr-xr-x 3 root root 4096 Oct 28  2021 snap

root@vagrant:~# ls -ltr   # sort by timestamp, newest first
total 12
drwxr-xr-x 3 root root 4096 Oct 28  2021 snap
drwxr-xr-x 2 root root 4096 Mar 23 23:01 devopsdir
-rw-r--r-- 1 root root   22 Mar 23 23:05 test.txt
lrwxrwxrwx 1 root root   37 Mar 24 03:27 cmds -> /opt/dev/ops/devops/test/commands.txt

root@vagrant:~# history   # list the history commands
```

## Filter
```console
root@vagrant:~# ls -l
total 16
-rw-r--r-- 1 root root 5014 Mar 25 02:22 test.txt
...
root@vagrant:~# grep my_keyword test.txt        # find lines contain this keyword in this file, grep is case-sensitive
root@vagrant:~# grep -i my_keyword test.txt     # -i means ignore case-sensitivity
root@vagrant:~# grep -i my_keyword *            # find keywords in all files in current dir, will ignore nested files in this directory
root@vagrant:~# root@vagrant:~# grep -iR my_keyword *     # find keywords in all files in current dir, R means will also search nested files in this directory
root@vagrant:~# grep -R SELINUX /etc/*          # find this keyword recursively (includes nested files) in all files in /etc/ directory. Can be used to find parameter names for a certain configuration in setting files. Great for sys admins, so they do not need to remember things. 
root@vagrant:~# grep -vi firewall test.txt      # reverse search: show all rows that do not contain this keyword in this file, case-insensitive

root@vagrant:~# less test.txt       # file reader, up and down arrow to navigate, q to quit
root@vagrant:~# more test.txt       # file reader, show percentage, use enter key to navigate, q to quit

root@vagrant:~# head test.txt       # show first 10 lines of this file
root@vagrant:~# head -20 test.txt   # show first 20 lines of this file
root@vagrant:~# tail test.txt       # show last 10 lines of this file
root@vagrant:~# tail -20 test.txt   # show last 20 lines of this file
root@vagrant:~# tail -f test.txt    # show dynamic contents of this file, control+c to quit. Useful to view log files for troubleshooting a server - you see errors in log files, such as /var/log/syslog file. Every service have their own log file. 

root@vagrant:~# cat /etc/passwd     # this file contain all users info, segregated into rows, and rows have cols separated by colon(:), as delimiters. First row first col is username
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
...

root@vagrant:~# cut -d: -f1 /etc/passwd     # extract first col of each row in this file. -d: means delimiter is colon, -f1 means field 1 (first column)
root
daemon
bin
sys
sync
...

root@vagrant:~# cut -d: -f3 /etc/passwd     # extract third col, which means userid in this file
0
1
2
3
4
...

root@vagrant:~# awk -F':' '{print $1}' /etc/passwd      # use awk command, if your file do not have proper delimiters, then can use regex. This command also gets the first column of the file. awk is much more powerful than cut command, with a lot more options. 
root
daemon
bin
sys
sync
...

root@vagrant:~# sed 's/word1/word2/g' test.txt      # replaces all occurrences of word1 to word2 in this file, print on screen the result, do not actually edit the file
root@vagrant:~# sed 's/word1/word2/g' *             # replaces all occurrences of word1 to word2 in all files in cur dir, print on screen the result, do not actually edit the files
root@vagrant:~# sed -i 's/word1/word2/g' test.txt   # replaces all occurrences of word1 to word2 in this file. Can be used without -i first to view the potential changes. Then actually apply the changes with -i. 
```

## Redirections

```console
[vagrant@bazinga ~]$ uptime > /tmp/sysinfo.txt    # redirect output into this file, if not exist, create it
[vagrant@bazinga ~]$ cat /tmp/sysinfo.txt
 11:22:02 up 6 min,  1 user,  load average: 0.09, 0.05, 0.01

[vagrant@bazinga ~]$ ls > /tmp/sysinfo.txt        # overwrite this file if exists
[vagrant@bazinga ~]$ cat /tmp/sysinfo.txt

[vagrant@bazinga ~]$ uptime >> /tmp/sysinfo.txt   # append to this file only
[vagrant@bazinga ~]$ cat /tmp/sysinfo.txt
 11:24:18 up 8 min,  1 user,  load average: 0.05, 0.07, 0.01

[vagrant@bazinga ~]$ df -h                        # shows disk partition utilization
Filesystem                        Size  Used Avail Use% Mounted on
devtmpfs                          4.0M     0  4.0M   0% /dev
tmpfs                             976M     0  976M   0% /dev/shm
...

[vagrant@bazinga ~]$ echo "Good morning! "        # print on the screen
Good morning! 
[vagrant@bazinga ~]$ echo "#####################" > /tmp/sysinfo.txt      # print to file
[vagrant@bazinga ~]$ cat /tmp/sysinfo.txt
#####################

[vagrant@bazinga ~]$ date > /tmp/sysinfo.txt 
[vagrant@bazinga ~]$ echo "#####################" >> /tmp/sysinfo.txt 
[vagrant@bazinga ~]$ uptime >> /tmp/sysinfo.txt 
[vagrant@bazinga ~]$ echo "#####################" >> /tmp/sysinfo.txt 
[vagrant@bazinga ~]$ free -m >> /tmp/sysinfo.txt 
[vagrant@bazinga ~]$ echo "#####################" >> /tmp/sysinfo.txt 
[vagrant@bazinga ~]$ df -h >> /tmp/sysinfo.txt 
[vagrant@bazinga ~]$ echo "#####################" >> /tmp/sysinfo.txt 
[vagrant@bazinga ~]$ cat /tmp/sysinfo.txt
Sun Apr  2 11:29:31 AM PDT 2023
#####################
 11:30:22 up 14 min,  1 user,  load average: 0.00, 0.02, 0.00
#####################
               total        used        free      shared  buff/cache   available
Mem:            1950         223        1536           0         190        1702
Swap:           1949           0        1949
#####################
Filesystem                        Size  Used Avail Use% Mounted on
devtmpfs                          4.0M     0  4.0M   0% /dev
tmpfs                             976M     0  976M   0% /dev/shm
tmpfs                             391M  708K  390M   1% /run
/dev/mapper/fedora_fedora35-root   15G  2.0G   13G  14% /
/dev/nvme0n1p2                   1014M  148M  867M  15% /boot
/dev/nvme0n1p1                    599M  5.9M  593M   1% /boot/efi
tmpfs                             196M     0  196M   0% /run/user/1000
#####################

[root@bazinga ~]# echo "This message goes into a black hole" > /dev/null 
[root@bazinga ~]# cat /dev/null                         # nothing is there

[root@bazinga ~]# cat /dev/null > /tmp/sysinfo.txt      # put content in black hole into this file, which clears this file contents. Handy in bash scripting
[root@bazinga ~]# cat /tmp/sysinfo.txt

[root@bazinga ~]# freeeeee -m > /dev/null               # if command contains error, it will not go to black hole, as error will be print on the screen
-bash: freeeeee: command not found

[root@bazinga ~]# freeeeee -m 2> /tmp/error.log         # 2 means redirect standard error
[root@bazinga ~]# cat /tmp/error.log 
-bash: freeeeee: command not found

[root@bazinga ~]# freeeeee -m &> /tmp/anything.log      # & means redirect any output, whether error or normal output

[root@bazinga ~]# ls /var/log/            # processes running behind the scene generates output here; handy in bash script because we run them in the background, and can view the output later
anaconda         dnf.log      lastlog  tallylog                vmware-vmsvc-root.log
...

[root@bazinga ~]# wc -l /etc/passwd       # return line number in this file
25 /etc/passwd
[root@bazinga ~]# wc -l < /etc/passwd     # same as above, the input to this wc cmd comes from this file
25

[root@bazinga ~]# cd /etc
[root@bazinga etc]# ls | wc -l            # result of ls cmd pipes into the wc cmd, returns total num of files in cur path
167

[root@bazinga etc]# ls | grep host        # grab all filenames in cur path start with host
host.conf
hostname
hosts

[root@bazinga etc]# tail /var/log/vmware-vmsvc-root.log | grep warning    # from last 10 lines of this file, search for text warning
[2023-04-02T18:35:12.419Z] [ warning] [vmsvc] [740] SimpleSock: Couldn't get VMCI socket family info.
[2023-04-02T18:40:12.234Z] [ warning] [vmsvc] [740] SimpleSock: Couldn't get VMCI socket family info.
...

[root@bazinga etc]# free -m
               total        used        free      shared  buff/cache   available
Mem:            1950         224        1309           0         416        1701
Swap:           1949           0        1949
[root@bazinga etc]# free -m | grep Mem
Mem:            1950         224        1309           0         416        1701

[root@bazinga etc]# ls -l | tail      # show last 10 files
-rw-r--r--.  1 root root       32 Feb 23  2022 vagrant_box_build_time
-rw-r--r--.  1 root root       28 Feb 23  2022 vconsole.conf
...

[root@bazinga etc]# ls -l | head      # show first 10 files
total 1064
-rw-r--r--.  1 root root       18 Feb 23  2022 adjtime
-rw-r--r--.  1 root root     1529 Jul 16  2021 aliases
...

[root@bazinga etc]# find /etc -name hostname    # find filename in this path
/etc/hostname
[root@bazinga etc]# find / -name hostname       # search in root dir, not recommended, it is realtime search
/proc/sys/kernel/hostname
/etc/hostname
...

[root@bazinga etc]# yum install mlocate -y
[root@bazinga etc]# updatedb
[root@bazinga etc]# locate host # not real time search, will not reflect changes unless you run updatedb command
/dev/vhost-net
/dev/vhost-vsock
...

```

## Users and groups

They are used to control access to files. 

Every file on the system is owned by a user and associated with a group. Every process has a owner and group affiliation, and can only access files its owner and group can access. 

Every user of the system is assigned a unique user ID number (UID); username and UID are stored in `etc/passwd`; user's password is stored in `etc/shadow` in encrypted form. Users are assigned a home directory and a program that is run when they login (usually a shell). Users cannot read, write or execute each other's files without permission. 

Types of users: 
- Root user. user id: 0; group id: 0; home dir: `/root`; shell: `/bin/bash`
- Regular user. user id: 1k to 10k; group id: 1k to 60k; home dir: `home/username`; shell: `/bin/bash`
- Service user. user id: 0 to 999; group id: 0 to 999; home dir: `/var/ftp` etc; shell: `/sbin/nologin`

```console
[root@bazinga ~]# head -1 /etc/passwd       # username:shadowfile:userid:groupid:comment:homedir:shell
root:x:0:0:root:/root:/bin/bash

[root@bazinga ~]# grep vagrant /etc/passwd
vagrant:x:1000:1000::/home/vagrant:/bin/bash

[root@bazinga ~]# cat /etc/group    # shows group name and group ids
root:x:0:
bin:x:1:
...

[root@bazinga ~]# id vagrant        # the id command
uid=1000(vagrant) gid=1000(vagrant) groups=1000(vagrant)

[root@bazinga ~]# useradd ansible
[root@bazinga ~]# useradd jenkins
[root@bazinga ~]# useradd aws

[root@bazinga ~]# tail -3 /etc/passwd
ansible:x:1001:1001::/home/ansible:/bin/bash
jenkins:x:1002:1002::/home/jenkins:/bin/bash
aws:x:1003:1003::/home/aws:/bin/bash

[root@bazinga ~]# tail -3 /etc/group
ansible:x:1001:
jenkins:x:1002:
aws:x:1003:

[root@bazinga ~]# id ansible
uid=1001(ansible) gid=1001(ansible) groups=1001(ansible)

[root@bazinga ~]# groupadd devops

[root@bazinga ~]# usermod -aG devops ansible      # G means secondary group, g means primary group
[root@bazinga ~]# id ansible                      # now this username is in devops groups
uid=1001(ansible) gid=1001(ansible) groups=1001(ansible),1004(devops)

[root@bazinga ~]# grep devops /etc/group          # see user ansible now in devops group
devops:x:1004:ansible

[root@bazinga ~]# vim /etc/group      # can also append users in the file directly like this: devops:x:1004:ansible,jenkins,aws

[root@bazinga ~]# id aws
uid=1003(aws) gid=1003(aws) groups=1003(aws),1004(devops)

[root@bazinga ~]# passwd ansible      # add/reset pwd for this user
Changing password for user ansible.
New password: 
BAD PASSWORD: The password is shorter than 8 characters
Retype new password: 
passwd: all authentication tokens updated successfully.

[root@bazinga ~]# su - ansible        # root user can directly access other users
[ansible@bazinga ~]$ whoami
ansible
[ansible@bazinga ~]$ su - aws         # regular user need pwd to switch to a diff user
Password: 
[aws@bazinga ~]$ exit
logout
[ansible@bazinga ~]$ exit             # go back to root user
logout

[root@bazinga ~]# last                # shows login info
vagrant  pts/0        192.168.211.1    Sun Apr  2 11:15    gone - no logout
reboot   system boot  5.16.9-200.fc35. Sun Apr  2 12:15   still running
reboot   system boot  5.16.9-200.fc35. Thu Feb 24 06:39 - 22:40  (-7:58)
...

[root@bazinga ~]# who                 # shows current user
vagrant  pts/0        2023-04-02 11:15 (192.168.211.1)

[root@bazinga ~]# lsof -u vagrant       # list all the open file by this user, can indicate what this user is doing
COMMAND   PID    USER   FD      TYPE             DEVICE SIZE/OFF       NODE NAME
systemd   908 vagrant  cwd       DIR              253,0      224        128 /
systemd   908 vagrant  rtd       DIR              253,0      224        128 /
...

[root@bazinga ~]# userdel aws           # delete the user, but its homedir still exits
[root@bazinga ~]# ls /home/
ansible  aws  jenkins  vagrant

[root@bazinga ~]# userdel -r jenkins    # delete the user and its home dir
[root@bazinga ~]# ls /home/
ansible  aws  vagrant

[root@bazinga ~]# groupdel devops       # delete the group

```

## File permissions
r means read, w means write, x means execute permission. 

When you do `ls -l`, in each result line, it starts with 1 + 3 + 3 + 3 characters, 1st character means file type, the next 3 sets of 3 chars means user permission, group permission and others permission (if there is a -, it means that permission is missing). 

For a directory, execute permission means you can do `cd`, and r means you can do `ls`, w means can make changes, such as deleting it. 

For a link file, the letters indicate your permissions for the link it self, not the actual file that it points to. 

```console
[vagrant@bazinga ~]$ ls -l /bin/login       # first root is the root user, and second root is the root group
-rwxr-xr-x. 1 root root 69848 Feb 14  2023 /bin/login       # permissions: rwx for root user, rx for root group, and rx for others

[root@bazinga ~]# useradd ansible
[root@bazinga ~]# useradd jenkins
[root@bazinga ~]# useradd aws
[root@bazinga ~]# useradd miles
[root@bazinga ~]# groupadd devops
[root@bazinga ~]# vim /etc/group      # append ansible,jenkins,aws
[root@bazinga ~]# id ansible
uid=1001(ansible) gid=1001(ansible) groups=1001(ansible),1004(devops)

[root@bazinga ~]# mkdir /opt/devopsdir
[root@bazinga ~]# ls -ld /opt/devopsdir
drwxr-xr-x. 2 root root 6 Apr 22 11:02 /opt/devopsdir

[root@bazinga ~]# chown ansible:devops /opt/devopsdir/      # change owner of this dir to ansible with group devops. could use -r flag to do recursively for all contents in this dir, but should be careful - hard to rollback. 
[root@bazinga ~]# ls -ld /opt/devopsdir
drwxr-xr-x. 2 ansible devops 6 Apr 22 11:02 /opt/devopsdir

[root@bazinga ~]# chmod o-x /opt/devopsdir/       # for others (o), remove x permission (-x)
[root@bazinga ~]# ls -ld /opt/devopsdir
drwxr-xr--. 2 ansible devops 6 Apr 22 11:02 /opt/devopsdir

[root@bazinga ~]# chmod o-r /opt/devopsdir/       # for others, remove r permission
[root@bazinga ~]# ls -ld /opt/devopsdir
drwxr-x---. 2 ansible devops 6 Apr 22 11:02 /opt/devopsdir

[root@bazinga ~]# chmod g+w /opt/devopsdir/       # for this group, add w permission
[root@bazinga ~]# ls -ld /opt/devopsdir
drwxrwx---. 2 ansible devops 6 Apr 22 11:02 /opt/devopsdir

[root@bazinga ~]# su - miles      # this user cannot read/execute/modify it

[miles@bazinga ~]$ ls /opt/devopsdir/
ls: cannot open directory '/opt/devopsdir/': Permission denied

[miles@bazinga ~]$ cd /opt/devopsdir/
-bash: cd: /opt/devopsdir/: Permission denied

[miles@bazinga ~]$ touch /opt/devopsdir/test.txt
touch: cannot touch '/opt/devopsdir/test.txt': Permission denied

[miles@bazinga ~]$ exit
logout

[root@bazinga ~]# su - aws      # this user is part of the devops group, can do rwx
[aws@bazinga ~]$ ls /opt/devopsdir/
[aws@bazinga ~]$ cd /opt/devopsdir/
[aws@bazinga devopsdir]$ touch test.txt
```

You can also use numbers to change permissions. Permissions are calculated by adding numbers for r/w/x. The sum is then used to specify all permissions for usr/group/others. This way is quick to grant permissions for all 3 of them. 

## Sudo
sudo gives power to a normal user to execute commands owned by root user. If a normal user already has full sudo privilege, it can become a root user anytime. 

Vagrant user has privilege to start a command with sudo, and can switch to root user using `sudo -i`. 

```console
[root@bazinga ~]# whoami
root
[root@bazinga ~]# passwd ansible    # set pwd for user ansible
Changing password for user ansible.
New password: 
Retype new password: 
passwd: all authentication tokens updated successfully.

[root@bazinga ~]# su - ansible      # switch to user ansible
Last login: Sun Apr  9 23:00:19 PDT 2023 on pts/0

[ansible@bazinga ~]$ whoami
ansible

[ansible@bazinga ~]$ sudo useradd test1       # cannot run sudo as normal usr
We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:
    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.
[sudo] password for ansible: 
ansible is not in the sudoers file.  This incident will be reported.

[ansible@bazinga ~]$ sudo -i      # cannot switch to root user
[sudo] password for ansible: 
ansible is not in the sudoers file.  This incident will be reported.

[ansible@bazinga ~]$ exit
logout

[root@bazinga ~]# ls -l /etc/sudoers      # the file to modify to add ansible to root, even root user do not have permission to write to this file. 
-r--r-----. 1 root root 4375 Feb 23  2022 /etc/sudoers

[root@bazinga ~]# visudo          # add a new line "ansible ALL=(ALL)       ALL" after "root    ALL=(ALL)       ALL"

[root@bazinga ~]# su - ansible
Last login: Sun Apr 23 11:57:14 PDT 2023 on pts/0

[ansible@bazinga ~]$ sudo -i      # note that it asks for pwd, so it is interactive
[sudo] password for ansible: 

[root@bazinga ~]# exit
logout
[ansible@bazinga ~]$ whoami
ansible
[ansible@bazinga ~]$ exit
logout
[root@bazinga ~]# whoami
root

[root@bazinga ~]# visudo          # add "NOPASSWD: " in front of the last ALL in the newly added line, so it becomes: "ansible ALL=(ALL)       NOPASSWD: ALL"

[root@bazinga ~]# su - ansible
Last login: Sun Apr 23 12:04:36 PDT 2023 on pts/0
[ansible@bazinga ~]$ sudo -i      # now no password asked

```

Note that most servers do not have root password set for security purpose. If you created an error in your sudoer file, you will be stuck. So the better alternative would be to create your own file in the `/etc/sudoer.d/` directory. 

To add all users in devops group into the sudoers file:
```console
[root@bazinga ~]# cd /etc/sudoers.d
[root@bazinga sudoers.d]# ls
vagrant
[root@bazinga sudoers.d]# cat vagrant 
Defaults:vagrant !fqdn
Defaults:vagrant !requiretty
vagrant ALL=(ALL) NOPASSWD: ALL

[root@bazinga sudoers.d]# cp vagrant devops
[root@bazinga sudoers.d]# vim devops      # note that % means group
[root@bazinga sudoers.d]# cat devops 
Defaults:vagrant !fqdn
Defaults:vagrant !requiretty
%devops ALL=(ALL) NOPASSWD: ALL
```

## software management
If we want to be able to use the `tree` command to see the file system tree, we could go to rmpfind.net and find the download link of the tree package. Then with root user, run `curl download-link -o file-name-to-save-as` to downlowd the package. `rpm -ivh file-name` to install the package.  To check whether tree is installed, use `rum -qa | grep tree`. To delete the tree package, `rpm -e tree-package-full-name`, where the package full name is the what the prv command returned. 

Sometimes, packages need many dependencies to be able to be installed. `yum` is a better option for this. To search for packages that contain "tree": `yum search tree`. To install a package that you found: `yum install tree.aarch64`, this will automatically install any dependencies you need. `yum remove tree.aarch64` removes the package. `ls /etc/yum.repos.d/` shows the links that yum have access to install packages. If your target package is not there, for example, Jenkins, then you refer to their official documentation `https://www.jenkins.io/doc/book/installing/`. Find the steps for your Linux distributions, I have Fedora. 

## Services
```console
[root@bazinga ~]# yum install httpd -y
[root@bazinga ~]# systemctl status httpd      # check status of this httpd, shows inactive

[root@bazinga ~]# systemctl start httpd       # start the service
[root@bazinga ~]# systemctl status httpd      # check the status again, shows active

[root@bazinga ~]# systemctl restart httpd     # restart after config changes
[root@bazinga ~]# systemctl reload httpd      # reload the config without restarting

[root@bazinga ~]# sudo reboot
(base) lisa@mac16 fedora % vagrant ssh

[root@bazinga ~]# systemctl enable httpd      # enable it at boot time
[root@bazinga ~]# systemctl start httpd       # start it after enabling it
[root@bazinga ~]# systemctl is-active httpd   # check if service is active, shows active
[root@bazinga ~]# systemctl is-enabled httpd  # check if service is enabled, shows enabled
```

## Processes
```console
[root@bazinga ~]# top

top - 09:51:24 up 18 min,  1 user,  load average: 0.00, 0.02, 0.00
Tasks: 207 total,   1 running, 206 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.2 us,  0.2 sy,  0.0 ni, 99.5 id,  0.0 wa,  0.0 hi,  0.2 si,  0.0 st
MiB Mem :   1950.8 total,   1264.0 free,    235.3 used,    451.6 buff/cache
MiB Swap:   1950.0 total,   1950.0 free,      0.0 used.   1690.3 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND         
     13 root      20   0       0      0      0 I   0.3   0.0   0:00.20 rcu_sched       
    705 systemd+  20   0   18372   7128   6320 S   0.3   0.4   0:01.94 systemd-oomd    
    ...
```
`top` shows the running processes etc. It sorts the processes based on CPU consumption. 1 running, 206 sleeping shows this is a lazy OS. 

Zombies are the processes whose operations are done, but their entry is still in the process table. `q` to quit the command. 

`ps aux` displays similar info on the screen, and quits automatically. 

`ps -ef` instead of cpu utilization, it will show you the parent process id (PPID). 

`ps -ef | grep httpd` shows all the httpd process. `kill 1420` asks process that has PID of 1420 to first kill its child processes, then kill the process itself. 

But sometimes processes becomes adamant and does not listen, so you have to forcefully kill them with `kill -9 1420`. But this only kills this parent process - all its child processes will keep running and become orphaned. 

Nowadays OS are smart and will automatically kill these orphaned processes. But you can manually kill them using `ps -ef | grep httpd | grep -v 'grep' | awk '{print $2} | xargs kill -9'`. 

## Archiving
Archiving is often used to make a backup, with a timestamp. 

The old, legacy, feature-rich command is `tar`: 
```console
[root@bazinga ~]# cd /var/log/
[root@bazinga log]# ls
anaconda         dnf.log      journal  sssd                  vmware-network.log
...
[root@bazinga log]# tar -czvf anaconda_05212023.tar.gz anaconda       # zip a folder
[root@bazinga log]# ls 
anaconda                  dnf.rpm.log  README                vmware-network.log
anaconda_05212023.tar.gz  firewalld    sa                    vmware-vgauthsvc.log.0
...

[root@bazinga log]# file anaconda_05212023.tar.gz 
anaconda_05212023.tar.gz: gzip compressed data, from Unix, original size modulo 2^32 3164160

[root@bazinga log]# mv anaconda_05212023.tar.gz /tmp/
[root@bazinga /]# cd /tmp/
[root@bazinga tmp]# tar -xzvf anaconda_05212023.tar.gz      # unzip it
[root@bazinga tmp]# ls
anaconda
anaconda_05212023.tar.gz
...
[root@bazinga tmp]# tar -xzvf anaconda_05212023.tar.gz -C /opt/       # unzip to a diff dir
```

An alternative is `zip` and `unzip`:
```console
[root@bazinga log]# yum install zip unzip -y
[root@bazinga log]# zip -r anaconda_05212023.zip anaconda/
[root@bazinga log]# mv anaconda_05212023.zip /opt/
[root@bazinga log]# cd /opt/
[root@bazinga opt]# ls
anaconda  anaconda_05212023.zip
[root@bazinga opt]# rm -rf anaconda
[root@bazinga opt]# ls
anaconda_05212023.zip
[root@bazinga opt]# unzip anaconda_05212023.zip 
```

## Ubuntu commands
Most of the commands above works for Ubuntu also, but some are Ubuntu specific. 
1. `adduser` instead of `useradd`
2. Default editor being `nano`, instead of `vim`
3. Package manager. 

```console
[root@bazinga ~]# useradd devops
[root@bazinga ~]# su - devops       # Error: no home direcotry. Ubuntu will not create home dir and mail spool for a new user
[root@bazinga ~]# userdel -r devops
[root@bazinga ~]# adduser devops    # this works in Ubuntu easily
[root@bazinga ~]# id devops         # will show uid, gid, groups

[root@bazinga ~]# visudo            # opens in nano editor
[root@bazinga ~]# export EDITOR=vim     # set var temporarily on current shell. clears after a logout.
[root@bazinga ~]# visudo            # opens in vim editor

[root@bazinga ~]# wget http://archive.ubuntu.com/ubuntu/pool/universe/t/tree/tree_1.7.0-3_amd64.deb
[root@bazinga ~]# dpkg -i tree_1.7.0-3_amd64.deb
[root@bazinga ~]# tree              # now it works
[root@bazinga ~]# dpkg -l           # list all the debian pkgs on your machine
[root@bazinga ~]# dpkg -l | grep tree       # find the tree package
[root@bazinga ~]# dpkg -r tree      # removes the tree package

[root@bazinga ~]# cd /etc/apt/
[root@bazinga ~]# ls                # there is a sources.list there, contains repo info
[root@bazinga ~]# apt update        # check all the latest repo, and create a list
[root@bazinga ~]# apt search tree   # search for packages
[root@bazinga ~]# apt install tree 
[root@bazinga ~]# apt install apache2       # can auto download dependencies, if any
[root@bazinga ~]# systemctl status apache2        # shows it is running
[root@bazinga ~]# systemctl is-enabled apache2    # show it is enabled
[root@bazinga ~]# apt upgrade       # upgrade all your packages
[root@bazinga ~]# apt remove apache2        # rmv a pkg, without removing the data and config
[root@bazinga ~]# apt purge apache2         # rmv a pkg, with all its config and data
```

If you run `visodu` in Ubuntu, it will open the default editor Nano. 

Whenever you want to install any software in Ubuntu, or any Debian machine, you need to run the `apt update` first. You do not need to do this with `yum`, because yum will always search first (create list), and it can refresh the list every 24 hrs. 

But this is where Ubuntu Debian beats RedHat system - because of its software access. It has many many software which is available from many many repos in the world. 

In Ubuntu, if you install any service, it will start it automatically. Such as apache2. 

When you install/uninstall services in Ubuntu, like apache2, which is a network service that sells webpages, it will also update the firewall rules. `ufw` means Ubuntu firewall.  

There are a few more different commands, which will be covered in bash scripting, and AWS cloud computing. 
