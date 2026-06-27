# Research Notes — Ethical Hacking Task 02
**Target:** localhost (127.0.0.1)  
**Analyst:** Sanjana Aneeshkumar  
**Date:** June 27, 2026  
**OS:** Microsoft Windows 11 24H2 – 25H2

---

## Part A — Nmap Installation & Version

**Command used:**
```
nmap --version
```

**Output:**
```
Nmap version 7.99 ( https://nmap.org )
Platform: i686-pc-windows-windows
Compiled with: nmap-liblua-5.4.8 openssl-3.0.16 nmap-libssh2-1.11.1 nmap-libz-1.3.2 nmap-libpcre2-10.47 Npcap-1.87 nmap-libdnet-1.18.0 ipv6
Available nsock engines: iocp poll select
```

---

## Part B — Basic Localhost Scan

**Command used:**
```
nmap localhost
```

**Output:**
```
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00019s latency).
Not shown: 998 closed tcp ports (reset)
PORT     STATE  SERVICE
135/tcp  open   msrpc
445/tcp  open   microsoft-ds
Nmap done: 1 IP address (1 host up) scanned in 0.33 seconds
```

**Findings:**
- Total open ports: 2
- Port 135 — msrpc (Microsoft Remote Procedure Call)
- Port 445 — microsoft-ds (Microsoft Directory Services / SMB)

---

## Part C — Service Version Detection

**Command used:**
```
nmap -sV localhost
```

**Output:**
```
PORT     STATE  SERVICE       VERSION
135/tcp  open   msrpc         Microsoft Windows RPC
445/tcp  open   microsoft-ds?
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

| Port | Service | Version |
|------|---------|---------|
| 135/tcp | msrpc | Microsoft Windows RPC |
| 445/tcp | microsoft-ds | Not fully detected (microsoft-ds?) |

---

## Part D — OS Detection

**Command used:**
```
nmap -O localhost
```

**Output:**
```
Device type: general purpose
Running: Microsoft Windows 11
OS CPE: cpe:/o:microsoft:windows_11
OS details: Microsoft Windows 11 24H2 – 25H2
Network Distance: 0 hops
```

**Findings:**
- OS detected: Yes
- OS identified: Microsoft Windows 11 24H2 – 25H2
- Why OS detection is useful: Knowing the OS helps a penetration tester identify OS-specific vulnerabilities, choose the right exploits, and understand what services are likely running by default.

---

## Part E — Common Port Research

| Port | Protocol | Purpose | Common Security Risks |
|------|----------|---------|----------------------|
| 20/21 | FTP | File Transfer Protocol — used to transfer files between client and server | Transmits data in plaintext (no encryption). Vulnerable to brute force, sniffing, and anonymous login attacks |
| 22 | SSH | Secure Shell — encrypted remote login and command execution | Brute force attacks on weak passwords. Should use key-based authentication |
| 23 | Telnet | Remote login protocol (unencrypted) | Transmits everything in plaintext including passwords. Highly insecure — should never be used |
| 25 | SMTP | Simple Mail Transfer Protocol — sends emails between servers | Open relays can be abused for spam. Vulnerable to email spoofing if SPF/DKIM not configured |
| 53 | DNS | Domain Name System — resolves domain names to IP addresses | DNS poisoning/spoofing attacks. DNS amplification DDoS attacks |
| 80 | HTTP | HyperText Transfer Protocol — serves web pages (unencrypted) | Traffic can be intercepted (MITM). No encryption — sensitive data exposed |
| 110 | POP3 | Post Office Protocol — downloads emails from server to client | Credentials sent in plaintext unless SSL used. Vulnerable to sniffing |
| 143 | IMAP | Internet Message Access Protocol — access emails on server | Same risks as POP3 — plaintext unless IMAPS (port 993) used |
| 443 | HTTPS | HTTP over TLS/SSL — encrypted web traffic | Misconfigured SSL/TLS (outdated ciphers). Certificate spoofing attacks |
| 445 | SMB | Server Message Block — file sharing and Windows network services | EternalBlue exploit (WannaCry ransomware). Should be blocked on public networks |
| 3389 | RDP | Remote Desktop Protocol — remote graphical access to Windows | Brute force attacks. BlueKeep vulnerability. Should never be exposed to the internet |

---

## Part F — Scan Analysis

**1. Which services are currently running?**
Two services are running on localhost:
- Port 135 — Microsoft RPC (msrpc) — used by Windows for inter-process communication
- Port 445 — SMB (microsoft-ds) — used for Windows file sharing and network services

**2. Are all open ports necessary?**
Port 135 (RPC) is required by Windows for core system functions and cannot easily be disabled. Port 445 (SMB) is needed if file sharing is in use, but if no file sharing is required on this machine, it could be restricted or firewalled.

**3. Which services could become security risks if misconfigured?**
Port 445 (SMB) is a significant risk if exposed to a network — it was the attack vector for the WannaCry ransomware attack via the EternalBlue exploit. Port 135 (RPC) has also had historical vulnerabilities (MS03-026 DCOM exploit).

**4. Which port would you disable if not required?**
Port 445 (SMB) — if file sharing is not needed, SMB should be disabled or restricted to trusted network segments only. Exposing SMB to the internet is extremely dangerous.

---

## Part G — Conclusion

The network scanning phase on my local Windows 11 machine using Nmap 7.99 revealed two open ports: 135 (Microsoft RPC) and 445 (SMB). The OS was correctly identified as Microsoft Windows 11 24H2–25H2. Both services are default Windows services — RPC is a core system requirement while SMB handles file sharing. From a security standpoint, SMB (port 445) should be monitored and restricted since it has been exploited in major attacks like WannaCry. This exercise demonstrated how quickly an attacker can map a machine's services and identify potential attack surfaces using simple Nmap commands.
