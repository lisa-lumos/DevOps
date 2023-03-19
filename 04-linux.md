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
vagrant@vagrant:~$ whoami # prompt content: username@hostname, $ means normal user, ~ means home dir
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
vagrant@vagrant:~$ sudo -i # switch to root, $ means root user shell, ~ means home dir
root@vagrant:~# whoami
root
root@vagrant:~# pwd
/root
root@vagrant:~# ls
snap
root@vagrant:~# 
root@vagrant:~# cd / # go to root dir
root@vagrant:/# pwd
/
root@vagrant:/# ls
bin   cdrom  etc   lib         media  opt   root  sbin  srv       sys  usr      var
boot  dev    home  lost+found  mnt    proc  run   snap  swap.img  tmp  vagrant
root@vagrant:/# cd bin
root@vagrant:/bin# ls # show user executables in this folder
'['                                   hexdump                            scsi_start
 aa-enabled                           host                               scsi_stop
 aa-exec                              hostid                             scsi_temperature
 add-apt-repository                   hostname                           sdiff
 addpart                              hostnamectl                        sed
 apport-bug                           htop                               see
...
root@vagrant:/# cd /sbin
root@vagrant:/sbin# ls # show root user executables
aa-remove-unknown      fsck.cramfs                  lvreduce               shadowconfig
aa-status              fsck.ext2                    lvremove               shutdown
aa-teardown            fsck.ext3                    lvrename               sshd
accessdb               fsck.ext4                    lvresize               start-stop-daemon
addgnupghome           fsck.fat                     lvs                    sulogin
...
root@vagrant:/sbin# cd /etc
root@vagrant:/etc# ls # show configuration files
adduser.conf                   e2scrub.conf     libblockdev          os-release               shadow-
alternatives                   environment      libnl-3              overlayroot.conf         shells
apparmor                       ethertypes       locale.alias         PackageKit               skel
apparmor.d                     fonts            locale.gen           pam.conf                 sos
apport                         fstab            localtime            pam.d                    ssh
apt                            fuse.conf        logcheck             passwd                   ssl
at.deny                        fwupd            login.defs           passwd-                  subgid
bash.bashrc                    gai.conf         logrotate.conf       perl                     subgid-
bash_completion                groff            logrotate.d          pki                      subuid
bash_completion.d              group            lsb-release          pm                       subuid-
bindresvport.blacklist         group-           ltrace.conf          polkit-1                 sudoers
binfmt.d                       grub.d           lvm                  pollinate                sudoers.d
byobu                          gshadow          machine-id           popularity-contest.conf  sysctl.conf
ca-certificates                gshadow-         magic                profile                  sysctl.d
ca-certificates.conf           gss              magic.mime           profile.d                systemd
ca-certificates.conf.dpkg-old  hdparm.conf      mailcap              protocols                terminfo
calendar                       host.conf        mailcap.order        python3                  timezone
cloud                          hostname         manpath.config       python3.8                tmpfiles.d
console-setup                  hosts            mdadm                rc0.d                    ubuntu-advantage
cron.d                         hosts.allow      mime.types           rc1.d                    ucf.conf
cron.daily                     hosts.deny       mke2fs.conf          rc2.d                    udev
cron.hourly                    init.d           modprobe.d           rc3.d                    udisks2
cron.monthly                   initramfs-tools  modules              rc4.d                    ufw
crontab                        inputrc          modules-load.d       rc5.d                    update-manager
cron.weekly                    iproute2         mtab                 rc6.d                    update-motd.d
cryptsetup-initramfs           iscsi            multipath            rcS.d                    update-notifier
crypttab                       issue            multipath.conf       resolv.conf              vim
dbus-1                         issue.net        nanorc               rmt                      vmware-tools
dconf                          kernel           netplan              rpc                      vtrgb
debconf.conf                   landscape        network              rsyslog.conf             wgetrc
debian_version                 ldap             networkd-dispatcher  rsyslog.d                X11
default                        ld.so.cache      NetworkManager       screenrc                 xattr.conf
deluser.conf                   ld.so.conf       networks             security                 xdg
depmod.d                       ld.so.conf.d     newt                 selinux                  zsh_command_not_found
dhcp                           legal            nsswitch.conf        services
dpkg                           libaudit.conf    opt                  shadow
root@vagrant:/etc# cat /etc/hostname 
vagrant
root@vagrant:/etc# ls /tmp/ # show temp files
netplan_6ip8lq53
snap.lxd
snap-private-tmp
systemd-private-b35160ea30f149aa845bf94fee2ebf01-fwupd.service-S0cuhf
systemd-private-b35160ea30f149aa845bf94fee2ebf01-systemd-logind.service-F7FhEh
systemd-private-b35160ea30f149aa845bf94fee2ebf01-systemd-resolved.service-vcUohh
systemd-private-b35160ea30f149aa845bf94fee2ebf01-systemd-timesyncd.service-Ev3yui
tmp.aP8vA5U5gw
vmware-root_36827-1023324764
vmware-root_776-2965448177
root@vagrant:/# ls /boot/ # show kernel and bootloaders
config-5.4.0-144-generic  initrd.img                    lost+found                    vmlinuz-5.4.0-144-generic
config-5.4.0-89-generic   initrd.img-5.4.0-144-generic  System.map-5.4.0-144-generic  vmlinuz-5.4.0-89-generic
efi                       initrd.img-5.4.0-89-generic   System.map-5.4.0-89-generic   vmlinuz.old
grub                      initrd.img.old                vmlinuz
root@vagrant:/# ls /proc/ # show system info folder contents
1     117     1611   226   270   3     317    5570   77524  903        cpuinfo      kpageflags    sysrq-trigger
10    118     17     227   271   30    318    5571   78     91         crypto       loadavg       sysvipc
100   119     18     228   28    300   319    5574   79     914        devices      locks         thread-self
1001  12      184    23    284   301   320    5575   79366  92         device-tree  mdstat        timer_list
101   120     19     230   285   302   321    5576   8      93         diskstats    meminfo       tty
102   120860  19102  231   286   303   322    5577   80     937        driver       misc          uptime
103   121     19116  232   287   304   3478   567    80986  94         execdomains  modules       version
104   121046  19119  233   288   305   3555   5999   81     95         fb           mounts        version_signature
105   121068  19216  234   289   306   3556   6      82     96         filesystems  net           vmallocinfo
106   121074  2      235   29    307   357    6001   83     97         fs           pagetypeinfo  vmstat
107   121097  20     236   290   308   36189  720    84     974        interrupts   partitions    zoneinfo
108   121117  21     2385  291   309   366    721    85     98         iomem        pressure
109   122     2141   249   292   31    36826  722    87     99         ioports      sched_debug
11    124     219    25    293   310   36827  723    877    993        irq          schedstat
110   13      22     250   294   3103  373    743    878    999        kallsyms     scsi
111   133     220    2526  295   311   397    744    88     asound     kcore        self
112   136     221    26    296   312   4      745    890    buddyinfo  keys         slabinfo
113   14      222    261   297   313   441    746    897    bus        key-users    softirqs
114   143     223    262   2979  314   442    76578  9      cgroups    kmsg         stat
115   15      224    264   298   315   4472   76743  90     cmdline    kpagecgroup  swaps
116   16      225    27    299   316   46853  77     900    consoles   kpagecount   sys
root@vagrant:/# uptime
 19:18:19 up 36 min,  1 user,  load average: 0.00, 0.00, 0.09
root@vagrant:/# cat /proc/uptime  # not user readable here
2272.29 3941.59
root@vagrant:/# free -m
              total        used        free      shared  buff/cache   available
Mem:            719         175         162           1         381         451
Swap:          1437          54        1383



```



































