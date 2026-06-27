# Ethical Hacking Task 02 — Network Scanning & Service Enumeration

**Name:** Sanjana Aneeshkumar  
**Internship:** White Band Associates  
**Date:** June 27, 2026  
**Target:** localhost (127.0.0.1) — Personal Windows 11 Machine

---

## Part A — Nmap Installation
Installed Nmap 7.99 on Windows 11. Verified using `nmap --version`.

---

## Part B — Basic Scan
Ran `nmap localhost`. Found **2 open ports**:
| Port | Service |
|------|---------|
| 135/tcp | msrpc |
| 445/tcp | microsoft-ds |

---

## Part C — Service Version Detection
Ran `nmap -sV localhost`.

| Port | Service | Version |
|------|---------|---------|
| 135/tcp | msrpc | Microsoft Windows RPC |
| 445/tcp | microsoft-ds | Not fully detected |

---

## Part D — OS Detection
Ran `nmap -O localhost`.

- **OS Detected:** Yes
- **OS:** Microsoft Windows 11 24H2 – 25H2
- **Device Type:** General Purpose
- **Network Distance:** 0 hops

OS detection is useful in penetration testing to identify OS-specific vulnerabilities and choose appropriate exploits.

---

## Part E — Common Port Research
Researched 11 common ports: 20/21 (FTP), 22 (SSH), 23 (Telnet), 25 (SMTP), 53 (DNS), 80 (HTTP), 110 (POP3), 143 (IMAP), 443 (HTTPS), 445 (SMB), 3389 (RDP). Full details in `Research_Notes.md`.

---

## Part F — Scan Analysis
- **Running services:** msrpc (135) and SMB (445)
- **Necessary ports:** Port 135 is required by Windows. Port 445 only if file sharing is in use.
- **Security risk:** Port 445 (SMB) — used in WannaCry ransomware attack via EternalBlue exploit
- **Port to disable if not needed:** 445 (SMB)

---

## Part G — Conclusion
Scanning localhost with Nmap revealed 2 open ports — both default Windows services. OS was correctly identified as Windows 11 24H2–25H2. SMB (port 445) is the highest risk service and should be restricted if file sharing is not required. This exercise showed how quickly attack surfaces can be mapped using basic Nmap commands.

---

## Files
| File | Description |
|------|-------------|
| `Nmap_Scan_Report.docx` | Full professional scan report with embedded screenshots |
| `Research_Notes.md` | Raw notes, commands, findings, and port research |
| `README.md` | This file |
| `screenshots/` | All 4 screenshots from the scan |

---

> **Disclaimer:** All scans were performed only on my own local machine (localhost/127.0.0.1). No external systems were scanned or accessed. Submitted as part of White Band Associates Ethical Hacking Internship Task 02.
