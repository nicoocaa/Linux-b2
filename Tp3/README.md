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

![SSH](./img/ssh.jpg)

🌞 **Chiffrement fort côté serveur**

- trouver une ressource de confiance (je veux le lien en compte-rendu)
- configurer le serveur SSH pour qu'il utilise des paramètres forts en terme de chiffrement (je veux le fichier de conf dans le compte-rendu)
  - conf dans le fichier de conf
  - regénérer des clés pour le serveur ?
  - regénérer les paramètres Diffie-Hellman ? (se renseigner sur Diffie-Hellman ?)

🌞 **Clés de chiffrement fortes pour le client**

- trouver une ressource de confiance (je veux le lien en compte-rendu)
- générez-vous une paire de clés qui utilise un chiffrement fort et une passphrase
- ne soyez pas non plus absurdes dans le choix du chiffrement quand je dis "fort" (genre pas de RSA avec une clé de taile 98789080932083209 bytes)
- vous n'utiliserez PAS l'algorithme RSA, trouvez autre chose

🌞 **Connectez-vous en SSH à votre VM avec cette paire de clés**

- prouvez en ajoutant `-vvvv` sur la commande `ssh` de connexion que vous utilisez bien cette clé là

## 4. DoT

Ca commence à faire quelques années maintenant que plusieurs acteurs poussent pour qu'on fasse du DNS chiffré, et qu'on arrête d'envoyer des requêtes DNS en clair dans tous les sens.

Le Dot est une techno qui va dans ce sens : DoT pour DNS over TLS. On fait nos requêtes DNS dans des tunnels chiffrés avec le protocole TLS.

🌞 **Configurer la machine pour qu'elle fasse du DoT**

- installez `systemd-resolved` sur la machine pour ça
- activez aussi DNSSEC tant qu'on y est
- [référez-vous à cette doc qui est cool par exemple](https://wiki.archlinux.org/title/systemd-resolved)
- utilisez le serveur public de CloudFlare : 1.1.1.1 (il supporte le DoT)

> Donc normalement : install du paquet, modif du fichier `/etc/systemd/resolved.conf` pour activer le DoT, activer DNSSEC et utiliser `1.1.1.1`, puis une commande pour modifier le contenu de `/etc/resolv.conf`, et enfin, redémarrer le service `systemd-resoved`.

🌞 **Prouvez que les requêtes DNS effectuées par la machine...**

- ont une réponse qui provient du serveur que vous avez conf (normalement c'est `127.0.0.1` avec `systemd-networkd` qui tourne)
  - quand on fait un `dig ynov.com` on voit en bas quel serveur a répondu
- mais qu'en réalité, la requête a été forward vers 1.1.1.1 avec du TLS
  - je veux une capture Wireshark à l'appui !

## 5. AIDE

Un truc demandé au point 1.3.1 du guide CIS c'est d'installer AIDE.

AIDE est un IDS ou *Intrusion Detection System*. Les IDS c'est un type de programme dont les fonctions peuvent être multiples.

Dans notre cas, AIDE, il surveille que certains fichiers du disque n'ont pas été modifiés. Des fichiers comme `/etc/shadow` par exemple.

🌞 **Installer et configurer AIDE**

- et bah incroyable mais [une très bonne ressource ici](https://www.it-connect.fr/aide-utilisation-et-configuration-dune-solution-de-controle-dintegrite-sous-linux/)
- configurez AIDE pour qu'il surveille (fichier de conf en compte-rendu)
  - le fichier de conf du serveur SSH
  - le fichier de conf du client chrony (le service qui gère le temps)
  - le fichier de conf de `systemd-networkd`

🌞 **Scénario de modification**

- introduisez une modification dans le fichier de conf du serveur SSH
- montrez que AIDE peut la détecter

🌞 **Timer et service systemd**

- créez un service systemd qui exécute un check AIDE
  - il faut créer un fichier `.service` dans le dossier `/etc/systemd/system/`
  - contenu du fichier à montrer dans le compte rendu
- créez un timer systemd qui exécute un check AIDE toutes les 10 minutes
  - il faut créer un fichier `.timer` dans le dossier `/etc/systemd/system/`
  - il doit porter le même nom que le service, genre `aide.service` et `aide.timer`
  - c'est complètement irréaliste 10 minutes, mais ça vous permettra de faire des tests (vous pouvez même raccourcir encore)