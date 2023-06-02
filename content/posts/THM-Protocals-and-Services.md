---
title: "THM Protocols and Services"
date: 2023-06-02T15:02:53-07:00
draft: false
---

This is a THM room that i worked on last year

**Protocols and Servers**
---
Summary of some of the the most common attacked Protocols 

| Protocol | TCP Port | Application(s) | Data Security |
|:--------:|:--------:|:--------------:|:-------------:|
| FTP | 21 | File Transfer | Cleartext |
| HTTP | 80 | Worldwide Web | Cleartext |
| IMAP | 143 | Email (MDA) | Cleartext |
| POP3 | 110 | Email (MDA) | Cleartext |
| SMTP | 25 | Email (MTA) | Cleartext |
| Telnet | 23 | Remote Access | Cleartext |
 
 from THM [https://tryhackme.com/room/protocolsandservers]

**Tools** 
*Sniffing Attacks* 
 - Tcpdump
 - Wireshark
 - Tshark

*Man in the middle (MITM) Attack*
- [Ettercap](https://www.ettercap-project.org/)
- [Brttercap](https://www.bettercap.org/)

**Notes on Transport Layer Security (TLS)** 
![[Pasted image 20220623173903.png]]

| Protocol | Default Port | Secured Protocol | Default Port with TLS |
|:--------:|:------------:|:----------------:|:---------------------:|
| HTTP | 80 | HTTPS | 443 |
| FTP | 21 | FTPS | 990 | 
| SMTP | 25 | SMTPS | 465 |
| POP3 | 110 | POP3S | 995 |
| IMAP | 143 | IMAPS | 993 |

On Linux, macOS, and MS Windows builds after 2018, you can connect to an SSH server using the following command `ssh username@10.10.203.224`. This command will try to connect to the server of IP address `10.10.203.224` with the login name `username`. If an SSH server is listening on the default port, it will ask you to provide the password for `username`. Once authenticated, the user will have access to the target server’s terminal. The terminal output below is an example of using SSH to access a Debian Linux server.


We can use SSH to transfer files using SCP (Secure Copy Protocol) based on the SSH protocol. An example of the syntax is as follows: `scp mark@10.10.203.224:/home/mark/archive.tar.gz ~`. This command will copy a file named `archive.tar.gz` from the remote system located in the `/home/mark` directory to `~`, i.e., the root of the home directory of the currently logged-in user.

Another example syntax is `scp backup.tar.bz2 mark@10.10.203.224:/home/mark/`. This command will copy the file `backup.tar.bz2` from the local system to the directory `/home/mark/` on the remote system.

We want an automated way to try the common passwords or the entries from a word list; here comes [THC Hydra](https://github.com/vanhauser-thc/thc-hydra). Hydra supports many protocols, including FTP, POP3, IMAP, SMTP, SSH, and all methods related to HTTP. The general command-line syntax is: `hydra -l username -P wordlist.txt server service` where we specify the following options:

-   `-l username`: `-l` should precede the `username`, i.e. the login name of the target.
-   `-P wordlist.txt`: `-P` precedes the `wordlist.txt` file, which is a text file containing the list of passwords you want to try with the provided username.
-   `server` is the hostname or IP address of the target server.
-   `service` indicates the service which you are trying to launch the dictionary attack.
-   `hydra -l mark -P /usr/share/wordlists/rockyou.txt 10.10.203.224 ftp` will use `mark` as the username as it iterates over the provided passwords against the FTP server.
-   `hydra -l mark -P /usr/share/wordlists/rockyou.txt ftp://10.10.203.224` is identical to the previous example. `10.10.203.224 ftp` is the same as `ftp://10.10.203.224`.
-   `hydra -l frank -P /usr/share/wordlists/rockyou.txt 10.10.203.224 ssh` will use `frank` as the user name as it tries to login via SSH using the different passwords.

Summary

This room covered various protocols, their usage, and how they work under the hood. Three common attacks are:

1.  Sniffing Attack
2.  MITM Attack
3.  Password Attack

For each of the above, we focused both on the attack details and the mitigation steps.

Many other attacks can be conducted against specific servers and protocols. We will provide a list of some related modules.

-   [Vulnerability Research](https://tryhackme.com/module/vulnerability-research): This module provides more information about vulnerabilities and exploits.
-   [Metasploit](https://tryhackme.com/module/metasploit): This module trains you on how to use Metasploit to exploit target systems.
-   [Burp Suite](https://tryhackme.com/module/learn-burp-suite): This module teaches you how to use Burp Suite to intercept HTTP traffic and launch attacks related to the web.

It is good to remember the default port number for common protocols. For convenience, the services we covered are listed in the following table sorted by alphabetical order.

| Protocol | TCP Port | Application(s) | Data Security |
|:--------:|:--------:|:--------------:|:-------------:|
| FTP | 21 | File Transfer | Cleartext |
| FTPS | 990 | File Transfer | Encrypted |
| HTTP | 80 | Worldwide Web | Cleartext | 
| HTTPS | 443 | Worldwide Web | Encrypted |
| IMAP | 143 | Email (MDA) | Cleartext |
| IMAPS | 993 | Email (MDA) | Encrypted |
| POP3 | 110 | Email (MDA) | Cleartext |
| POP3S | 995 | Email (MDA) | Encrypted |
| SFTP | 22 | File Transfer | Encrypted |
| SSH | 22 | Remote Access and File Transfer | Encrypted |
| SMTP | 25 | Email (MTA) | Cleartext | 
| SMTPS  | 465 | Email (MTA) | Encrypted |
| Telnet | 23 | Remote Access | Cleartext |

Hydra remains a very efficient tool that you can launch from the terminal to try the different passwords. We summarize its main options in the following table.

| Option | Explanation |
|:------:|:-----------:|
| `-l username` | Provide the login name |
| `-P WordList.txt` | Specify the password list to use |
| `server service` | Set the server address and service to attack |
| `-s PORT` | Use in case of non-default service port number |
| `-V` or `-vV` | Show the username and password combinations being tried |
| `-d` | Display debugging output if the verbose output is not helping | 

