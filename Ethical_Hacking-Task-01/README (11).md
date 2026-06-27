# Ethical Hacking Task 01 — Information Gathering & Reconnaissance

**Name:** Sanjana Aneeshkumar  
**Internship:** White Band Associates  
**Date:** June 2026  
**Target:** Kristu Jayanti University — https://www.kristujayanti.edu.in/

---

## Part A — Target Selection

Chose **Kristu Jayanti University** as the target since it is my own college website. This makes it an ethical and appropriate choice for passive reconnaissance. No systems were accessed or exploited — purely educational.

---

## Part B — WHOIS Lookup

Performed using `who.is` and `whois.com`.

| Field | Value |
|-------|-------|
| Domain | kristujayanti.edu.in |
| Registered On | 2012-04-20 |
| Expires On | 2035-04-20 |
| Last Updated | 2026-03-08 |
| Registrar | ERNET India (IANA ID: 800068) |
| Registrant | Kristu Jayanti College, Karnataka, IN |
| Name Servers | ns-265.awsdns-33.com, ns-812.awsdns-37.net, ns-1333.awsdns-38.org, ns-1857.awsdns-40.co.uk |
| DNSSEC | Unsigned |
| Status | ok |

> Registrant contact details are redacted for privacy (ICANN compliance). `who.is` returned no data — expected behaviour for `.edu.in` domains managed by ERNET India.

---

## Part C — DNS Enumeration

Performed using `dnschecker.org`.

**A Records:**
| IP Address | Notes |
|-----------|-------|
| 13.235.5.4 | AWS EC2 — ap-south-1 (Mumbai) |
| 13.232.185.200 | AWS EC2 — ap-south-1 (Mumbai) |

**MX Record:**
| Priority | Mail Server |
|----------|------------|
| 10 | inbound-smtp.us-east-1.amazonaws.com (Amazon SES) |

**NS Records:** All four name servers are Amazon Route 53 (awsdns-*).

**TXT Records:**
- `v=spf1 include:_spf.google.com ~all` — Google Workspace authorized to send email
- `v=spf1 include:amazonses.com ~all` — Amazon SES authorized to send email
- Two `google-site-verification` tokens — Google Search Console ownership verified

---

## Part D — Technology Identification

Performed via HTML source analysis, URL patterns, and DNS data.

| Category | Technology | Evidence |
|----------|-----------|---------|
| Hosting | Amazon Web Services (AWS) | IPs belong to AWS ap-south-1 region |
| DNS | Amazon Route 53 | All name servers are awsdns-* |
| Backend | Custom PHP | All URLs end in .php |
| Analytics | Google Tag Manager | GTM-MTMBSPS9 found in HTML |
| Email (Staff) | Google Workspace | SPF includes _spf.google.com |
| Email (System) | Amazon SES | SPF includes amazonses.com, MX points to AWS |
| CDN | None detected | No Cloudflare/Akamai headers |

**Subdomains discovered:**
- `kjusys.kristujayanti.edu.in` — Student/Faculty portal
- `kjusys-applicant.kristujayanti.edu.in` — Admission applicant login
- `kristujayantijournal.com` — Academic journals site

---

## Part E — HTTP Security Headers

Checked via `securityheaders.com` and manual HEAD request. Server returns HTTP 403 to automated requests (bot protection active).

| Header | Present | Purpose |
|--------|---------|---------|
| Content-Security-Policy (CSP) | Not Detected | Prevents XSS by specifying trusted content sources |
| X-Frame-Options | Not Detected | Prevents clickjacking via iframe embedding |
| X-Content-Type-Options | Not Detected | Prevents MIME type sniffing by the browser |
| Strict-Transport-Security (HSTS) | Not Detected | Forces browsers to always use HTTPS |
| Referrer-Policy | Not Detected | Controls referrer info sent with requests |
| Permissions-Policy | Not Detected | Controls browser feature access (camera, mic, etc.) |

> HTTPS (TLS/SSL) is active — good baseline. However none of the major security headers are implemented.

---

## Part F — Robots.txt & Sitemap

**robots.txt** — `https://www.kristujayanti.edu.in/robots.txt` ✅ Present

Disallowed paths found:
- `/cgi-bin/` — server-side scripts directory
- `/SSR-III/login.php` — student login portal (reveals internal path)
- `/admission/mba_admissionnn.php` — MBA admission page
- `/admission.php/` — main admission page
- `/about/mitrotsava-2019.php` — old event page
- `/*/pop_up_admission/` — all popup admission pages
- `/*/pdf/media_room/` — media room PDFs
- `/*/images/admission/` — admission images

> Key finding: robots.txt revealed `/SSR-III/login.php` — an internal student portal path not meant to be publicly known.

**sitemap.xml** — `https://www.kristujayanti.edu.in/sitemap.xml` ✅ Present

- Generated using `mysitemapgenerator.com`
- Homepage priority: 1.0, all other pages: 0.8
- Last modified: 2025-09-30
- Indexed pages include: `/admission/`, `/doctoral-research/`, `/UN-SDG/`, `/Library/`, `/campus/hostel.php`, `/examination/` and more

---

## Part G — Conclusion

Kristu Jayanti University's website runs entirely on AWS infrastructure (EC2 + Route 53 + SES) with a custom PHP backend. Email is split between Google Workspace and Amazon SES. The server blocks automated requests (HTTP 403) showing some bot protection is active, and HTTPS is properly configured.

Key concerns found passively:
- No HTTP security headers implemented (CSP, HSTS, X-Frame-Options etc.)
- `robots.txt` exposes internal paths like `/SSR-III/login.php`
- No DNSSEC enabled on the domain

This exercise showed how much information is publicly available before touching a target's systems. Reconnaissance is the foundation of ethical hacking.

---

## Files in this Folder

| File | Description |
|------|-------------|
| `Reconnaissance_Report.docx` | Full formatted report with all findings and embedded screenshots |
| `Research_Notes.md` | Raw notes, commands used, and findings |
| `screenshots/` | All 10 screenshots taken during reconnaissance |
| `README.md` | This file |

---

> **Disclaimer:** All reconnaissance was performed passively using public tools only. No systems were accessed, no exploits were attempted. Submitted as part of White Band Associates Ethical Hacking Internship Task 01.
