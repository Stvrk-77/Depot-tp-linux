# Installation de nginx 
```bash

root@debian:~# apt update
Hit:1 http://deb.debian.org/debian bookworm InRelease
Get:2 http://deb.debian.org/debian bookworm-updates InRelease [55.4 kB]
Get:3 http://security.debian.org/debian-security bookworm-security InRelease [48.0 kB]
Get:4 http://security.debian.org/debian-security bookworm-security/main Sources [130 kB]
Get:5 http://security.debian.org/debian-security bookworm-security/main amd64 Packages [216 kB]
Fetched 450 kB in 0s (1037 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
All packages are up to date.
root@debian:~# apt install nginx
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  nginx-common
  ```

  # Installation et configuration d'un pare-feu

```bash

  root@debian:~# apt-get install ufw
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  iptables libip6tc2 libnetfilter-conntrack3 libnfnetlink0
Suggested packages:
  firewalld rsyslog
The following NEW packages will be installed:
  iptables libip6tc2 libnetfilter-conntrack3 libnfnetlink0 ufw
0 upgraded, 5 newly installed, 0 to remove and 0 not upgraded.
Need to get 603 kB of archives.
After this operation, 3606 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://deb.debian.org/debian bookworm/main amd64 libip6tc2 amd64 1.8.9-2 [19.4 kB]
Get:2 http://deb.debian.org/debian bookworm/main amd64 libnfnetlink0 amd64 1.0.2-2 [15.1 kB]
Get:3 http://deb.debian.org/debian bookworm/main amd64 libnetfilter-conntrack3 amd64 1.0.9-3 [40.7 kB]
Get:4 http://deb.debian.org/debian bookworm/main amd64 iptables amd64 1.8.9-2 [360 kB]
Get:5 http://deb.debian.org/debian bookworm/main amd64 ufw all 0.36.2-1 [168 kB]
Fetched 603 kB in 0s (5614 kB/s)
Preconfiguring packages ...
Selecting previously unselected package libip6tc2:amd64.
(Reading database ... 37547 files and directories currently installed.)
Preparing to unpack .../libip6tc2_1.8.9-2_amd64.deb ...
Unpacking libip6tc2:amd64 (1.8.9-2) ...
Selecting previously unselected package libnfnetlink0:amd64.
Preparing to unpack .../libnfnetlink0_1.0.2-2_amd64.deb ...
Unpacking libnfnetlink0:amd64 (1.0.2-2) ...
Selecting previously unselected package libnetfilter-conntrack3:amd64.
Preparing to unpack .../libnetfilter-conntrack3_1.0.9-3_amd64.deb ...
Unpacking libnetfilter-conntrack3:amd64 (1.0.9-3) ...
Selecting previously unselected package iptables.
Preparing to unpack .../iptables_1.8.9-2_amd64.deb ...
Unpacking iptables (1.8.9-2) ...
Selecting previously unselected package ufw.
Preparing to unpack .../archives/ufw_0.36.2-1_all.deb ...
Unpacking ufw (0.36.2-1) ...
Setting up libip6tc2:amd64 (1.8.9-2) ...
Setting up libnfnetlink0:amd64 (1.0.2-2) ...
Setting up libnetfilter-conntrack3:amd64 (1.0.9-3) ...
Setting up iptables (1.8.9-2) ...
update-alternatives: using /usr/sbin/iptables-legacy to provide /usr/sbin/iptables (iptables) in auto mode
update-alternatives: using /usr/sbin/ip6tables-legacy to provide /usr/sbin/ip6tables (ip6tables) in auto mode
update-alternatives: using /usr/sbin/iptables-nft to provide /usr/sbin/iptables (iptables) in auto mode
update-alternatives: using /usr/sbin/ip6tables-nft to provide /usr/sbin/ip6tables (ip6tables) in auto mode
update-alternatives: using /usr/sbin/arptables-nft to provide /usr/sbin/arptables (arptables) in auto mode
update-alternatives: using /usr/sbin/ebtables-nft to provide /usr/sbin/ebtables (ebtables) in auto mode
Setting up ufw (0.36.2-1) ...

Creating config file /etc/ufw/before.rules with new version

Creating config file /etc/ufw/before6.rules with new version

Creating config file /etc/ufw/after.rules with new version

Creating config file /etc/ufw/after6.rules with new version
Created symlink /etc/systemd/system/multi-user.target.wants/ufw.service -> /lib/systemd/system/ufw.service.
Processing triggers for libc-bin (2.36-9+deb12u9) ...
Processing triggers for man-db (2.11.2-2) ...
root@debian:~# ufw default deny incoming
Default incoming policy changed to 'deny'
(be sure to update your rules accordingly)
root@debian:~# ufw allow outgoing
ERROR: Could not find a profile matching 'outgoing'
root@debian:~# ufw default allow outgoing
Default outgoing policy changed to 'allow'
(be sure to update your rules accordingly)
root@debian:~# ufw allow ssh
Rules updated
Rules updated (v6)
root@debian:~# ufw allow 6222/tcp
Rules updated
Rules updated (v6)
root@debian:~# ufw enable
Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
Firewall is active and enabled on system startup
root@debian:~# ufw status numbered
Status: active

     To                         Action      From
     --                         ------      ----
[ 1] 22/tcp                     ALLOW IN    Anywhere
[ 2] 6222/tcp                   ALLOW IN    Anywhere
[ 3] 22/tcp (v6)                ALLOW IN    Anywhere (v6)
[ 4] 6222/tcp (v6)              ALLOW IN    Anywhere (v6)

root@debian:~# ufw deny 22/tcp
Rule updated
Rule updated (v6)
root@debian:~# ufw status
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     DENY        Anywhere
6222/tcp                   ALLOW       Anywhere
22/tcp (v6)                DENY        Anywhere (v6)
6222/tcp (v6)              ALLOW       Anywhere (v6)
```

# automatisation des maj de sécurité

```bash 

root@debian:~# sudo apt install unattended-upgrades
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  gir1.2-glib-2.0 libgirepository-1.0-1 python3-dbus python3-distro-info python3-gi
Suggested packages:
  python-dbus-doc bsd-mailx default-mta | mail-transport-agent needrestart powermgmt-base
The following NEW packages will be installed:
  gir1.2-glib-2.0 libgirepository-1.0-1 python3-dbus python3-distro-info python3-gi
  unattended-upgrades
0 upgraded, 6 newly installed, 0 to remove and 0 not upgraded.
Need to get 645 kB of archives.
After this operation, 2544 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://deb.debian.org/debian bookworm/main amd64 libgirepository-1.0-1 amd64 1.74.0-3 [101 kB]
Get:2 http://deb.debian.org/debian bookworm/main amd64 gir1.2-glib-2.0 amd64 1.74.0-3 [159 kB]
Get:3 http://deb.debian.org/debian bookworm/main amd64 python3-dbus amd64 1.3.2-4+b1 [95.1 kB]
Get:4 http://deb.debian.org/debian bookworm/main amd64 python3-distro-info all 1.5+deb12u1 [6772 B]
Get:5 http://deb.debian.org/debian bookworm/main amd64 python3-gi amd64 3.42.2-3+b1 [219 kB]
Get:6 http://deb.debian.org/debian bookworm/main amd64 unattended-upgrades all 2.9.1+nmu3 [63.3 kB]
Fetched 645 kB in 0s (8030 kB/s)
Preconfiguring packages ...
Selecting previously unselected package libgirepository-1.0-1:amd64.
(Reading database ... 37873 files and directories currently installed.)
Preparing to unpack .../0-libgirepository-1.0-1_1.74.0-3_amd64.deb ...
Unpacking libgirepository-1.0-1:amd64 (1.74.0-3) ...
Selecting previously unselected package gir1.2-glib-2.0:amd64.
Preparing to unpack .../1-gir1.2-glib-2.0_1.74.0-3_amd64.deb ...
Unpacking gir1.2-glib-2.0:amd64 (1.74.0-3) ...
Selecting previously unselected package python3-dbus.
Preparing to unpack .../2-python3-dbus_1.3.2-4+b1_amd64.deb ...
Unpacking python3-dbus (1.3.2-4+b1) ...
Selecting previously unselected package python3-distro-info.
Preparing to unpack .../3-python3-distro-info_1.5+deb12u1_all.deb ...
Unpacking python3-distro-info (1.5+deb12u1) ...
Selecting previously unselected package python3-gi.
Preparing to unpack .../4-python3-gi_3.42.2-3+b1_amd64.deb ...
Unpacking python3-gi (3.42.2-3+b1) ...
Selecting previously unselected package unattended-upgrades.
Preparing to unpack .../5-unattended-upgrades_2.9.1+nmu3_all.deb ...
Unpacking unattended-upgrades (2.9.1+nmu3) ...
Setting up python3-dbus (1.3.2-4+b1) ...
Setting up libgirepository-1.0-1:amd64 (1.74.0-3) ...
Setting up python3-distro-info (1.5+deb12u1) ...
Setting up unattended-upgrades (2.9.1+nmu3) ...

Creating config file /etc/apt/apt.conf.d/50unattended-upgrades with new version
Created symlink /etc/systemd/system/multi-user.target.wants/unattended-upgrades.service -> /lib/systemd/system/unattended-upgrades.service.
Synchronizing state of unattended-upgrades.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable unattended-upgrades
Setting up gir1.2-glib-2.0:amd64 (1.74.0-3) ...
Setting up python3-gi (3.42.2-3+b1) ...
Processing triggers for man-db (2.11.2-2) ...
Processing triggers for libc-bin (2.36-9+deb12u9) ...
root@debian:~# dpkg-reconfigure --priority=low unattended-upgrades
```
## création d'un nouvel utilisateur non-root

```bash

root@debian:~# adduser stark
Adding user `stark' ...
Adding new group `stark' (1000) ...
Adding new user `stark' (1000) with group `stark (1000)' ...
Creating home directory `/home/stark' ...
Copying files from `/etc/skel' ...
New password:
Retype new password:
passwd: password updated successfully
Changing the user information for stark
Enter the new value, or press ENTER for the default
        Full Name []:
        Room Number []:
        Work Phone []:
        Home Phone []:
        Other []:
Is the information correct? [Y/n]
Adding new user `stark' to supplemental / extra groups `users' ...
Adding user `stark' to group `users' ...
root@debian:~# usermod -aG sudo stark
``` 
# gestion des perm nginx
```bash
root@debian:~#  chmod 644 /etc/nginx/sites-enabled/
root@debian:~#  chmod 644 /etc/nginx/sites-available/
root@debian:~#  chmod 644 /etc/nginx/nginx.conf
root@debian:~# ls -alh /etc/nginx/nginx.conf
-rw-r--r-- 1 root root 1.5K Mar 14  2023 /etc/nginx/nginx.conf
```

 





