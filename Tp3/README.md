# TP3 Sécu : Linux Hardening

**Dans ce TP, vous allez renforcer la sécurité d'un OS Linux.**


## Sommaire

- [TP3 Sécu : Linux Hardening](#tp3-sécu--linux-hardening)
  - [Sommaire](#sommaire)
  - [1. Guides CIS](#1-guides-cis)
  - [2. Conf SSH](#2-conf-ssh)
  - [4. DoT](#4-dot)
  - [5. AIDE](#5-aide)

## 1. Guides CIS


🌞 **Suivre un guide CIS**

2.1:  

```bash
[root@localhost ~]$ rpm -q  autofs
package autofs is not installed
[root@localhost ~]$ rpm -q avahi
package avahi is not installed
[root@localhost ~]$ rpm -q dhcp-server
package dhcp-server is not installed
[root@localhost ~]$ rpm -q bind
package bind is not installed
[root@localhost ~]$ rpm -q dnsmasq
package dnsmasq is not installed
[root@localhost ~]$ rpm -q samba
package samba is not installed
[root@localhost ~]$ rpm -q vsftpd
package vsftpd is not installed
[root@localhost ~]$ rpm -q dovecot cyrus-imap
package dovecot is not installed
package cyrus-imap is not installed
[root@localhost ~]$ rpm -q nfs-utils
package nfs-utils is not installed
[root@localhost ~]$ rpm -q ypserv
package ypserv is not installed
[root@localhost ~]$ rpm -q cups
package cups is not installed
[root@localhost ~]$ rpm -q rpcbind
package rpcbind is not installed
[root@localhost ~]$ rpm -q rsync-daemon
package rsync-daemon is not installed
[root@localhost ~]$ rpm -q net-snmp
package net-snmp is not installed
[root@localhost ~]$ rpm -q tftp-server
package tftp-server is not installed
[root@localhost ~]$ rpm -q squid
package squid is not installed
[root@localhost ~]$ rpm -q httpd nginx
package httpd is not installed
package nginx is not installed
[root@localhost ~]$ rpm -q xinetd
package xinetd is not installed
[root@localhost ~]$ rpm -q xorg-x11-server-common
package xorg-x11-server-common is not installed
[root@localhost ~]$ bash script.sh
script.sh: line 5: postconf: command not found
Usage: grep [OPTION]... PATTERNS [FILE]...
Try 'grep --help' for more information.
script.sh: line 8: \h+(127\.0\.0\.1|\[?::1\]?):25\b: command not found
Usage: grep [OPTION]... PATTERNS [FILE]...
Try 'grep --help' for more information.
script.sh: line 8: \h+(127\.0\.0\.1|\[?::1\]?):465\b: command not found
Usage: grep [OPTION]... PATTERNS [FILE]...
Try 'grep --help' for more information.
script.sh: line 8: \h+(127\.0\.0\.1|\[?::1\]?):587\b: command not found

- Audit Result:
 ** PASS **

 - Port "25" is not listening on a non-loopback network
interface
 - Port "465" is not listening on a non-loopback network
interface
 - Port "587" is not listening on a non-loopback network
interface

[root@localhost ~]$ ss -plntu
Netid State  Recv-Q Send-Q Local Address:Port Peer Address:PortProcess
udp   UNCONN 0      0          127.0.0.1:323       0.0.0.0:*
udp   UNCONN 0      0              [::1]:323          [::]:*
tcp   LISTEN 0      128          0.0.0.0:22        0.0.0.0:*
tcp   LISTEN 0      128             [::]:22           [::]:*
```

3.1:  

```bash
[nico@localhost ~]$ bash script.sh

- Audit Result:
 ** PASS **

 - System has no wireless NICs installed
[nico@localhost ~]$ rpm -q bluez
package bluez is not installed
[nico@localhost ~]$ systemctl is-active bluetooth.service 2>/dev/null | grep '^active'
[nico@localhost ~]$ sudo vi script.sh
[nico@localhost ~]$ bash script.sh

- Audit Result:
 ** PASS **
 - kernel module: "dccp" doesn't exist in "/usr/lib/modules/5.14.0-362.8.1.el9_3.x86_64/kernel/net"
[nico@localhost ~]$ sudo vi script.sh
[nico@localhost ~]$ bash script.sh
script.sh: line 34: /usr/lib/modules/5.14.0-362.8.1.el9_3.x86_64/kernel/net/tipc: Is a directory


 -- INFO --
 - module: "tipc" exists in:
 - "/usr/lib/modules/5.14.0-362.8.1.el9_3.x86_64/kernel/net"

- Audit Result:
 ** FAIL **
 - Reason(s) for audit failure:
 - kernel module: "tipc" is loadable
 - kernel module: "tipc" is not deny listed
- Correctly set:
 - kernel module: "tipc" is not loaded
[nico@localhost ~]$ sudo vi script.sh
[nico@localhost ~]$ bash script.sh
script.sh: line 30: /usr/lib/modules/5.14.0-362.8.1.el9_3.x86_64/kernel/net/tipc: Is a directory
script.sh: line 21: /etc/modprobe.d/tipc.conf: Permission denied
script.sh: line 25: /etc/modprobe.d/tipc.conf: Permission denied


 -- INFO --
 - module: "tipc" exists in:
 - "/usr/lib/modules/5.14.0-362.8.1.el9_3.x86_64/kernel/net"
 - setting kernel module: "tipc" to "/bin/false"
 - denylisting kernel module: "tipc"

 - remediation of kernel module: "tipc" complete

[nico@localhost ~]$ sudo vi script.sh
[nico@localhost ~]$ bash script.sh

- Audit Result:
 ** PASS **
 - kernel module: "rds" doesn't exist in "/usr/lib/modules/5.14.0-362.8.1.el9_3.x86_64/kernel/net"
[nico@localhost ~]$ sudo vi script.sh
[nico@localhost ~]$ bash script.sh
script.sh: line 34: /usr/lib/modules/5.14.0-362.8.1.el9_3.x86_64/kernel/net/sctp: Is a directory


 -- INFO --
 - module: "sctp" exists in:
 - "/usr/lib/modules/5.14.0-362.8.1.el9_3.x86_64/kernel/net"

- Audit Result:
 ** FAIL **
 - Reason(s) for audit failure:
 - kernel module: "sctp" is loadable
 - kernel module: "sctp" is not deny listed
- Correctly set:
 - kernel module: "sctp" is not loaded
[nico@localhost ~]$ sudo vi script.sh
[nico@localhost ~]$ bash script.sh
script.sh: line 30: /usr/lib/modules/5.14.0-362.8.1.el9_3.x86_64/kernel/net/sctp: Is a directory
script.sh: line 21: /etc/modprobe.d/sctp.conf: Permission denied
script.sh: line 25: /etc/modprobe.d/sctp.conf: Permission denied


 -- INFO --
 - module: "sctp" exists in:
 - "/usr/lib/modules/5.14.0-362.8.1.el9_3.x86_64/kernel/net"
 - setting kernel module: "sctp" to "/bin/false"
 - denylisting kernel module: "sctp"

 - remediation of kernel module: "sctp" complete

[nico@localhost ~]$ sudo vi script.sh
[root@localhost ~]# bash script.sh 

- Audit Result:
 ** PASS **
 - kernel module: "sctp" doesn't exist in "/usr/lib/modules/5.14.0-427.13.1.el9_4.x86_64/kernel/net"
 - kernel module: "sctp" doesn't exist in "/usr/lib/modules/5.14.0-427.40.1.el9_4.x86_64/kernel/net"

[nico@localhost ~]$ sudo vi script.sh
[nico@localhost ~]$ bash script.sh
Usage: grep [OPTION]... PATTERNS [FILE]...
Try 'grep --help' for more information.
script.sh: line 20: ^\h*([^#\n\r]+|#\h*\/[^#\n\r\h]+\.conf\b): No such file or directory
Usage: grep [OPTION]... PATTERNS [FILE]...
Try 'grep --help' for more information.
script.sh: line 11: ^\h*net\.ipv6\.conf\.all\.disable_ipv6\h*=\h*1\b: command not found
script.sh: line 13: ^\h*net\.ipv6\.conf\.default\.disable_ipv6\h*=\h*1\b: command not found
Usage: grep [OPTION]... PATTERNS [FILE]...
Try 'grep --help' for more information.
script.sh: line 20: ^\h*([^#\n\r]+|#\h*\/[^#\n\r\h]+\.conf\b): No such file or directory

- Audit Result:
 ** FAIL **
 - Reason(s) for audit failure:

 - "net.ipv4.ip_forward" is not set in an included file
 ** Note: "net.ipv4.ip_forward" May
be set in a file that's ignored by load procedure **

 - "net.ipv6.conf.all.forwarding" is not set in an included file
 ** Note: "net.ipv6.conf.all.forwarding" May
be set in a file that's ignored by load procedure **



- Correctly set:

 - "net.ipv4.ip_forward" is correctly set to "0" in the running configuration
 - "net.ipv6.conf.all.forwarding" is correctly set to "0" in the running configuration

script.sh: line 80: Page: command not found
[nico@localhost ~]$ sudo vi /etc/sysctl.conf
[nico@localhost ~]$ sudo vi script.sh
[nico@localhost ~]$ bash script.sh
sysctl: permission denied on key "net.ipv4.ip_forward"
sysctl: permission denied on key "net.ipv4.route.flush"
[nico@localhost ~]$ sudo bash script.sh
net.ipv4.ip_forward = 0
net.ipv4.route.flush = 1
[nico@localhost ~]$ sudo vi /etc/sysctl.conf
[nico@localhost ~]$ sudo bash script.sh
net.ipv4.ip_forward = 0
net.ipv4.route.flush = 1
[nico@localhost ~]$ sudo vi script.sh
[nico@localhost ~]$ sudo bash script.sh
net.ipv6.conf.all.forwarding = 0
net.ipv6.route.flush = 1
[root@localhost ~]# bash script.sh 

- Audit Result:
 ** FAIL **
 - Reason(s) for audit failure:

 - "net.ipv4.conf.all.send_redirects" is not set in an included file
 ** Note: "net.ipv4.conf.all.send_redirects" may be set in a file that's ignored by the load procedure **

 - "net.ipv4.conf.default.send_redirects" is not set in an included file
 ** Note: "net.ipv4.conf.default.send_redirects" may be set in a file that's ignored by the load procedure **



- Correctly set:

 - "net.ipv4.conf.all.send_redirects" is correctly set to "0" in the running configuration
 - "net.ipv4.conf.default.send_redirects" is correctly set to "0" in the running configuration

[root@localhost ~]# bash script.sh 

- Audit Result:
 ** FAIL **
 - Reason(s) for audit failure:

 - "net.ipv4.icmp_ignore_bogus_error_responses" is not set in an included file
 ** Note: "net.ipv4.icmp_ignore_bogus_error_responses" may be set in a file that's ignored by the load procedure **



- Correctly set:

 - "net.ipv4.icmp_ignore_bogus_error_responses" is correctly set to "1" in the running configuration

[root@localhost ~]# bash script.sh 

- Audit Result:
 ** FAIL **
 - Reason(s) for audit failure:

 - "net.ipv4.icmp_echo_ignore_broadcasts" is not set in an included file
 ** Note: "net.ipv4.icmp_echo_ignore_broadcasts" may be set in a file that's ignored by the load procedure **



- Correctly set:

 - "net.ipv4.icmp_echo_ignore_broadcasts" is correctly set to "1" in the running configuration

[root@localhost ~]# bash test.sh 
net.ipv6.conf.all.accept_redirects = 0
net.ipv6.conf.default.accept_redirects = 0
net.ipv6.route.flush = 1
[root@localhost ~]# bash script.sh 

- Audit Result:
 ** FAIL **
 - Reason(s) for audit failure:

 - "net.ipv4.conf.all.accept_redirects" is incorrectly set to "1" in the running configuration and should have a value of: "0"
 - "net.ipv4.conf.all.accept_redirects" is not set in an included file
 ** Note: "net.ipv4.conf.all.accept_redirects" may be set in a file that's ignored by the load procedure **

 - "net.ipv4.conf.default.accept_redirects" is incorrectly set to "1" in the running configuration and should have a value of: "0"
 - "net.ipv4.conf.default.accept_redirects" is not set in an included file
 ** Note: "net.ipv4.conf.default.accept_redirects" may be set in a file that's ignored by the load procedure **

 - "net.ipv6.conf.all.accept_redirects" is not set in an included file
 ** Note: "net.ipv6.conf.all.accept_redirects" may be set in a file that's ignored by the load procedure **

 - "net.ipv6.conf.default.accept_redirects" is not set in an included file
 ** Note: "net.ipv6.conf.default.accept_redirects" may be set in a file that's ignored by the load procedure **



- Correctly set:

 - "net.ipv6.conf.all.accept_redirects" is correctly set to "0" in the running configuration
 - "net.ipv6.conf.default.accept_redirects" is correctly set to "0" in the running configuration

[root@localhost ~]# bash test.sh 
net.ipv4.conf.all.secure_redirects = 0
net.ipv4.conf.default.secure_redirects = 0
net.ipv4.route.flush = 1
[root@localhost ~]# bash script.sh 

- Audit Result:
 ** FAIL **
 - Reason(s) for audit failure:

 - "net.ipv4.conf.all.secure_redirects" is not set in an included file
 ** Note: "net.ipv4.conf.all.secure_redirects" may be set in a file that's ignored by the load procedure **

 - "net.ipv4.conf.default.secure_redirects" is not set in an included file
 ** Note: "net.ipv4.conf.default.secure_redirects" may be set in a file that's ignored by the load procedure **



- Correctly set:

 - "net.ipv4.conf.all.secure_redirects" is correctly set to "0" in the running configuration
 - "net.ipv4.conf.default.secure_redirects" is correctly set to "0" in the running configuration

root@localhost ~]# bash script.sh 

- Audit Result:
 ** FAIL **
 - Reason(s) for audit failure:

 - "net.ipv4.conf.all.rp_filter" is incorrectly set to "0" in the running configuration and should have a value of: "1"
 - "net.ipv4.conf.all.rp_filter" is not set in an included file
 ** Note: "net.ipv4.conf.all.rp_filter" may be set in a file that's ignored by the load procedure **



- Correctly set:

 - "net.ipv4.conf.default.rp_filter" is correctly set to "1" in the running configuration
 - "net.ipv4.conf.default.rp_filter" is correctly set to "1" in "/usr/lib/sysctl.d/50-redhat.conf"

[root@localhost ~]# bash script.sh 

- Audit Result:
 ** FAIL **
 - Reason(s) for audit failure:

 - "net.ipv4.conf.all.log_martians" is not set in an included file
 ** Note: "net.ipv4.conf.all.log_martians" may be set in a file that's ignored by load procedure **

 - "net.ipv4.conf.default.log_martians" is not set in an included file
 ** Note: "net.ipv4.conf.default.log_martians" may be set in a file that's ignored by load procedure **



- Correctly set:

 - "net.ipv4.conf.all.log_martians" is correctly set to "1" in the running configuration
 - "net.ipv4.conf.default.log_martians" is correctly set to "1" in the running configuration

[root@localhost ~]# bash script.sh 

- Audit Result:
 ** FAIL **
 - Reason(s) for audit failure:

 - "net.ipv4.tcp_syncookies" is not set in an included file
 ** Note: "net.ipv4.tcp_syncookies" May be set in a file that's ignored by load procedure **



- Correctly set:

 - "net.ipv4.tcp_syncookies" is correctly set to "1" in the running configuration
[nico@localhost ~]$ bash script.sh 

- Audit Result:
 *** PASS ***
- * Correctly set * :

 - File: "/etc/ssh/sshd_config":
 - Correct: mode (0600), owner (root), and group owner (root) configured
[nico@localhost ~]$ bash script.sh 

- Audit Result:
 ** PASS **
 - * Correctly configured * :
 - No openSSH private keys found
[nico@localhost ~]$ bash script.sh 

- Audit Result:
 ** PASS **
 - * Correctly configured * :
 - File: "/etc/ssh/ssh_host_ed25519_key.pub"
 - Correct: mode: "0644", owner: "root", and group owner: "root" configured
 - File: "/etc/ssh/ssh_host_ecdsa_key.pub"
 - Correct: mode: "0644", owner: "root", and group owner: "root" configured
 - File: "/etc/ssh/ssh_host_rsa_key.pub"
 - Correct: mode: "0644", owner: "root", and group owner: "root" configured
[nico@localhost ~]$ sudo sshd -T | grep -Pi -- '^ciphers\h+\"?([^#\n\r]+,)?((3des|blowfish|cast128|aes(128|192|256))-
cbc|arcfour(128|256)?|rijndael-cbc@lysator\.liu\.se|chacha20-poly1305@openssh\.com)\b'
grep: the -P option only supports a single pattern
[nico@localhost ~]$ sudo !!
sudo sshd -T | grep -Pi -- 'kexalgorithms\h+([^#\n\r]+,)?(diffie-hellman-group1-sha1|diffie-hellman-group14-
sha1|diffie-hellman-group-exchange-sha1)\b'
grep: the -P option only supports a single pattern
[nico@localhost ~]$ sudo sshd -T | grep -Pi -- 'macs\h+([^#\n\r]+,)?(hmac-md5|hmac-md5-96|hmac-ripemd160|hmac-sha1-96|umac-
64@openssh\.com|hmac-md5-etm@openssh\.com|hmac-md5-96-etm@openssh\.com|hmac-ripemd160-etm@openssh\.com|hmac-
sha1-96-etm@openssh\.com|umac-64-etm@openssh\.com|umac-128-etm@openssh\.com)\b'
grep: the -P option only supports a single pattern
[nico@localhost ~]$ sudo sshd -T | grep -Pi -- '^\h*(allow|deny)(users|groups)\h+\H+'
[nico@localhost ~]$ sudo sshd -T | grep -Pi -- '^banner\h+\/\H+'
[nico@localhost ~]$ sudo sshd -T | grep -Pi -- '(clientaliveinterval|clientalivecountmax)'
clientaliveinterval 0
clientalivecountmax 3
[nico@localhost ~]$ sudo sshd -T | grep -i disableforwarding
disableforwarding no

Edit the /etc/ssh/sshd_config file to set the DisableForwarding parameter to yes
above any Include entry as follows:

DisableForwarding yes

[nico@localhost ~]$ sudo sshd -T | grep -i disableforwarding
disableforwarding yes
[nico@localhost ~]$ sudo sshd -T | grep gssapiauthentication
gssapiauthentication yes
[nico@localhost ~]$ sudo sshd -T | grep hostbasedauthentication
hostbasedauthentication no
[nico@localhost ~]$ sudo sshd -T | grep ignorerhosts
ignorerhosts yes
[nico@localhost ~]$ sudo sshd -T | grep logingracetime
logingracetime 60
[nico@localhost ~]$ sudo sshd -T | grep loglevel
loglevel INFO
[nico@localhost ~]$ sudo sshd -T | grep maxauthtries
maxauthtries 4
[nico@localhost ~]$ sudo sshd -T | awk '$1 ~ /^\s*maxstartups/{split($2, a, ":");{if(a[1] > 10 || a[2] > 30 || a[3] > 60) print $0}}'
maxstartups 10:30:100
[nico@localhost ~]$ sudo sshd -T | grep -i maxsessions
maxsessions 10
[nico@localhost ~]$ sudo sshd -T | grep permitemptypasswords
permitemptypasswords no
[nico@localhost ~]$ sudo sshd -T | grep permitrootlogin
permitrootlogin no
[nico@localhost ~]$ sudo sshd -T | grep permituserenvironment
permituserenvironment no
[nico@localhost ~]$ sudo sshd -T | grep -i usepam
usepam yes
[nico@localhost ~]$ stat -Lc 'Access: (%#a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/passwd
Access: (0644/-rw-r--r--) Uid: ( 0/ root) Gid: ( 0/ root)
[nico@localhost ~]$ stat -Lc 'Access: (%#a/%A) Uid: ( %u/ %U) Gid: { %g/ %G)' /etc/passwd-
Access: (0644/-rw-r--r--) Uid: ( 0/ root) Gid: { 0/ root)
[nico@localhost ~]$ stat -Lc 'Access: (%#a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/group
Access: (0644/-rw-r--r--) Uid: ( 0/ root) Gid: ( 0/ root)
[nico@localhost ~]$ stat -Lc 'Access: (%#a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/group-
Access: (0644/-rw-r--r--) Uid: ( 0/ root) Gid: ( 0/ root)
[nico@localhost ~]$ stat -Lc 'Access: (%#a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/shadow
Access: (0/----------) Uid: ( 0/ root) Gid: ( 0/ root)
[nico@localhost ~]$ stat -Lc 'Access: (%#a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/shadow-
Access: (0/----------) Uid: ( 0/ root) Gid: ( 0/ root)
[nico@localhost ~]$ stat -Lc 'Access: (%#a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/gshadow
Access: (0/----------) Uid: ( 0/ root) Gid: ( 0/ root)
[nico@localhost ~]$ stat -Lc 'Access: (%#a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/gshadow-
Access: (0/----------) Uid: ( 0/ root) Gid: ( 0/ root)
[nico@localhost ~]$ stat -Lc 'Access: (%#a/%A) Uid: ( %u/ %U) Gid: ( %g/ %G)' /etc/shells
Access: (0644/-rw-r--r--) Uid: ( 0/ root) Gid: ( 0/ root)
[nico@localhost ~]$ sudo [ -e "/etc/security/opasswd.old" ] && stat -Lc
[nico@localhost ~]$ rpm -q libselinux
libselinux-3.6-1.el9.x86_64
[nico@localhost ~]$ sudo grubby --info=ALL | grep -Po '(selinux|enforcing)=0\b'
[nico@localhost ~]$ grep -E '^\s*SELINUXTYPE=(targeted|mls)\b' /etc/selinux/config
SELINUXTYPE=targeted
[nico@localhost ~]$ getenforce
Permissive
[nico@localhost ~]$ sudo bash script.sh 

- Audit Result:
 ** PASS **
 - All audit configuration files are mode: "640" or more restrictive
[nico@localhost ~]$ sudo find /etc/audit/ -type f \( -name '*.conf' -o -name '*.rules' \) ! -user root
[nico@localhost ~]$ sudo find /etc/audit/ -type f \( -name '*.conf' -o -name '*.rules' \) ! -group root
[nico@localhost ~]$ bash script.sh 

- Audit Result:
 ** PASS **
 - * Correctly configured * :
 - Audit tool "/sbin/auditctl" is correctly configured to mode: "0755"
 - Audit tool "/sbin/aureport" is correctly configured to mode: "0755"
 - Audit tool "/sbin/ausearch" is correctly configured to mode: "0755"
 - Audit tool "/sbin/autrace" is correctly configured to mode: "0750"
 - Audit tool "/sbin/auditd" is correctly configured to mode: "0755"
 - Audit tool "/sbin/augenrules" is correctly configured to mode: "0755"
[nico@localhost ~]$ stat -Lc "%n %U" /sbin/auditctl /sbin/aureport /sbin/ausearch /sbin/autrace /sbin/auditd /sbin/augenrules | awk '$2 != "root" {print}'
[nico@localhost ~]$ stat -Lc "%n %G" /sbin/auditctl /sbin/aureport /sbin/ausearch /sbin/autrace /sbin/auditd /sbin/augenrules |
awk '$2 != "root" {print}'

```

## 2. Conf SSH

## 4. DoT

🌞 **Configurer la machine pour qu'elle fasse du DoT**

```bash
[nico@localhost ~]$ sudo dnf install systemd-resolved
[sudo] password for nico:
AlmaLinux 9 - AppStream                     6.6 kB/s | 4.2 kB     00:00
AlmaLinux 9 - AppStream                     1.4 MB/s |  11 MB     00:08
AlmaLinux 9 - BaseOS                        5.1 kB/s | 3.8 kB     00:00
AlmaLinux 9 - BaseOS                        1.4 MB/s | 9.1 MB     00:06
AlmaLinux 9 - Extras                        2.9 kB/s | 3.3 kB     00:01
AlmaLinux 9 - Extras                         10 kB/s |  13 kB     00:01
Package systemd-resolved-252-46.el9_5.2.alma.1.x86_64 is already installed.
Dependencies resolved.
Nothing to do.
Complete!
[nico@localhost ~]$ sudo systemctl start systemd-resolved
[nico@localhost ~]$ sudo nano /etc/systemd/resolved.conf
sudo: nano: command not found
[nico@localhost ~]$ sudo vi /etc/systemd/resolved.conf
[nico@localhost ~]$ sudo cat /etc/systemd/resolved.conf
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it under the
#  terms of the GNU Lesser General Public License as published by the Free
#  Software Foundation; either version 2.1 of the License, or (at your option)
#  any later version.
#
# Entries in this file show the compile time defaults. Local configuration
# should be created by either modifying this file, or by creating "drop-ins" in
# the resolved.conf.d/ subdirectory. The latter is generally recommended.
# Defaults can be restored by simply deleting this file and all drop-ins.
#
# Use 'systemd-analyze cat-config systemd/resolved.conf' to display the full config.
#
# See resolved.conf(5) for details.

[Resolve]
DNS=1.1.1.1
DNSOverTLS=yes
DNSSEC=yes

# Some examples of DNS servers which may be used for DNS= and FallbackDNS=:
# Cloudflare: 1.1.1.1#cloudflare-dns.com 1.0.0.1#cloudflare-dns.com 2606:4700:4700::1111#cloudflare-dns.com 2606:4700:4700::1001#cloudflare-dns.com
# Google:     8.8.8.8#dns.google 8.8.4.4#dns.google 2001:4860:4860::8888#dns.google 2001:4860:4860::8844#dns.google
# Quad9:      9.9.9.9#dns.quad9.net 149.112.112.112#dns.quad9.net 2620:fe::fe#dns.quad9.net 2620:fe::9#dns.quad9.net
#DNS=
#FallbackDNS=
#Domains=
#DNSSEC=no
#DNSOverTLS=no
#MulticastDNS=no
#LLMNR=resolve
#Cache=yes
#CacheFromLocalhost=no
#DNSStubListener=yes
#DNSStubListenerExtra=
#ReadEtcHosts=yes
#ResolveUnicastSingleLabel=no
[nico@localhost ~]$ sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf
[nico@localhost ~]$
```

🌞 **Prouvez que les requêtes DNS effectuées par la machine...**

```bash
[nico@localhost ~]$ dig ynov.com

; <<>> DiG 9.16.23-RH <<>> ynov.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 43593
;; flags: qr rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;ynov.com.                      IN      A

;; ANSWER SECTION:
ynov.com.               300     IN      A       104.26.10.233
ynov.com.               300     IN      A       104.26.11.233
ynov.com.               300     IN      A       172.67.74.226

;; Query time: 220 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Wed Jan 29 10:03:10 CET 2025
;; MSG SIZE  rcvd: 85

[nico@localhost ~]$ resolvectl query ynov.com
ynov.com: 104.26.10.233                        -- link: enp0s8
          104.26.11.233                        -- link: enp0s8
          172.67.74.226                        -- link: enp0s8
          2606:4700:20::681a:ae9               -- link: enp0s8
          2606:4700:20::681a:be9               -- link: enp0s8
          2606:4700:20::ac43:4ae2              -- link: enp0s8

-- Information acquired via protocol DNS in 104.4ms.
-- Data is authenticated: no; Data was acquired via local or encrypted transport: yes
-- Data from: cache network
[nico@localhost ~]$

```

## 5. AIDE

🌞 **Installer et configurer AIDE**

```bash
[nico@localhost ~]$ sudo yum install aide
Last metadata expiration check: 0:04:15 ago on Wed Jan 29 10:01:45 2025.
Dependencies resolved.
============================================================================
 Package      Architecture   Version                Repository         Size
============================================================================
Installing:
 aide         x86_64         0.16-102.el9           appstream         146 k

Transaction Summary
============================================================================
Install  1 Package

Total download size: 146 k
Installed size: 353 k
Is this ok [y/N]: y
Downloading Packages:
[MIRROR] aide-0.16-102.el9.x86_64.rpm: Curl error (6): Couldn't resolve host name for http://mirror.ibcp.fr/pub/almalinux/9.5/AppStream/x86_64/os/Packages/aide-0.16-102.el9.x86_64.rpm [Could not resolve host: mirror.ibcp.fr]
aide-0.16-102.el9.x86_64.rpm                211 kB/s | 146 kB     00:00
----------------------------------------------------------------------------
Total                                       109 kB/s | 146 kB     00:01
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                    1/1
  Installing       : aide-0.16-102.el9.x86_64                           1/1
  Running scriptlet: aide-0.16-102.el9.x86_64                           1/1
  Verifying        : aide-0.16-102.el9.x86_64                           1/1

Installed:
  aide-0.16-102.el9.x86_64

Complete!
[nico@localhost ~]$ sudo aide --init
Start timestamp: 2025-01-29 10:06:14 +0100 (AIDE 0.16)
AIDE initialized database at /var/lib/aide/aide.db.new.gz

Number of entries:      33557

---------------------------------------------------
The attributes of the (uncompressed) database(s):
---------------------------------------------------

/var/lib/aide/aide.db.new.gz
  MD5      : 13hiYPEY7KPNR/A86GeAgQ==
  SHA1     : Vw43iLqcL0xaZ4zxkTsG59b4aEM=
  RMD160   : vtT75UGZWDzV3zbt6PpzOcN8qpY=
  TIGER    : m9baJauVhrlkH4IqUWY+bk8v/O7OuanG
  SHA256   : oY3rIgWxL0q72MABNqq3OyRY6ECKj0pM
             gsdnsJNRnaI=
  SHA512   : FFJ7q7hMk9F5Uqhu5mnXc1L5RcwEFi0R
             ofk74x02qTOQnX2PxLR3HO1JTxhbvmLM
             eBoMOvxBiEg8rcocPdpLWA==


End timestamp: 2025-01-29 10:06:59 +0100 (run time: 0m 45s)
[nico@localhost ~]$ sudo mv /var/lib/aide/aide.db.new.gz /var/lib/aide/aide.db.gz
[nico@localhost ~]$ sudo aide --check
Start timestamp: 2025-01-29 10:07:18 +0100 (AIDE 0.16)
AIDE found NO differences between database and filesystem. Looks okay!!

Number of entries:      33557

---------------------------------------------------
The attributes of the (uncompressed) database(s):
---------------------------------------------------

/var/lib/aide/aide.db.gz
  MD5      : 13hiYPEY7KPNR/A86GeAgQ==
  SHA1     : Vw43iLqcL0xaZ4zxkTsG59b4aEM=
  RMD160   : vtT75UGZWDzV3zbt6PpzOcN8qpY=
  TIGER    : m9baJauVhrlkH4IqUWY+bk8v/O7OuanG
  SHA256   : oY3rIgWxL0q72MABNqq3OyRY6ECKj0pM
             gsdnsJNRnaI=
  SHA512   : FFJ7q7hMk9F5Uqhu5mnXc1L5RcwEFi0R
             ofk74x02qTOQnX2PxLR3HO1JTxhbvmLM
             eBoMOvxBiEg8rcocPdpLWA==


End timestamp: 2025-01-29 10:07:29 +0100 (run time: 0m 11s)
[nico@localhost ~]$
```