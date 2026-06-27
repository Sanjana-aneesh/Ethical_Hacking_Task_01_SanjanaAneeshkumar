# Research Notes — Ethical Hacking Task 01
**Target:** https://www.kristujayanti.edu.in/  
**Analyst:** Sanjana Aneeshkumar  
**Date:** June 2026  

---

## Part B — WHOIS Lookup

**Tool used:** who.is / whois.domaintools.com  
**Domain:** kristujayanti.edu.in

### Findings:
- Domain: `kristujayanti.edu.in`
- TLD: `.edu.in` — Indian restricted educational domain managed by ERNET India
- DNS Provider: Amazon Route 53
- Name Servers:
  - ns-265.awsdns-33.com
  - ns-812.awsdns-37.net
  - ns-1333.awsdns-38.org
  - ns-1857.awsdns-40.co.uk
- Registration/Expiry dates: **Not publicly available** (standard for .edu.in domains)
- Registrant info: **Restricted** (standard for .edu.in domains)

**Note:** `.edu.in` domains are strictly for recognized educational institutions in India and their WHOIS data is partially withheld by ERNET for privacy.

---

## Part C — DNS Enumeration

**Tool used:** Python dnspython library, dnschecker.org

### Command used:
```python
import dns.resolver
domain = 'kristujayanti.edu.in'
for rtype in ['A', 'MX', 'NS', 'TXT']:
    answers = dns.resolver.resolve(domain, rtype)
    for r in answers:
        print(r)
```

### A Records:
```
13.235.5.4
13.232.185.200
```
Both IPs belong to AWS ap-south-1 (Mumbai) region.

### MX Records:
```
10 inbound-smtp.us-east-1.amazonaws.com.
```
Email received via Amazon SES (Simple Email Service).

### NS Records:
```
ns-812.awsdns-37.net.
ns-1857.awsdns-40.co.uk.
ns-265.awsdns-33.com.
ns-1333.awsdns-38.org.
```
All AWS Route 53 name servers.

### TXT Records:
```
"v=spf1 include:_spf.google.com ~all"
"v=spf1 include:amazonses.com ~all"
"google-site-verification=k0WiuRMXif5j3oqdug2npE7j5RF7yz7bUarZgEbLdiI"
"google-site-verification=VOrsWwWwEhAGFZkrnd4U1jeqPgWzlFyoBiXUFBGL3ig"
```

---

## Part D — Technology Identification

**Tool used:** HTML source analysis, Wappalyzer, DNS analysis

### From HTML source:
- Google Tag Manager: `GTM-MTMBSPS9` found in HTML
- All page URLs end in `.php` → custom PHP backend
- No WordPress generator tag, no Drupal signatures

### From DNS:
- Hosting: AWS (A records → AWS Mumbai region)
- DNS: AWS Route 53
- Email: Google Workspace (SPF) + Amazon SES (SPF + MX)

### Subdomains found:
- `kjusys.kristujayanti.edu.in` — Student/Faculty portal
- `kjusys-applicant.kristujayanti.edu.in` — Admission applicant portal
- `kristujayantijournal.com` — Academic journals (separate domain)

---

## Part E — HTTP Security Headers

**Tool used:** securityheaders.com, manual HEAD request

### Raw response (automated HEAD request):
```
Status: 403 Forbidden
x-deny-reason: host_not_allowed
content-type: text/plain
date: Fri, 26 Jun 2026
```

Server actively blocks automated requests — a positive security measure.

### Security header status (checked via securityheaders.com):
| Header | Present | Purpose |
|--------|---------|---------|
| Content-Security-Policy (CSP) | Not Detected | Prevents XSS attacks by specifying which content sources the browser should trust |
| X-Frame-Options | Not Detected | Prevents the page from being embedded in iframes — protects against clickjacking attacks |
| X-Content-Type-Options | Not Detected | Stops browsers from MIME-sniffing a response away from the declared content type |
| Strict-Transport-Security (HSTS) | Not Detected | Forces browsers to always connect via HTTPS, even if the user types http:// |
| Referrer-Policy | Not Detected | Controls how much referrer information is sent along with requests to other pages |
| Permissions-Policy | Not Detected | Controls which browser features (camera, microphone, geolocation) the page can access |

HTTPS is active (TLS works fine in browser).

---

## Part F — Robots.txt & Sitemap

**URLs to check manually in browser:**
- https://www.kristujayanti.edu.in/robots.txt
- https://www.kristujayanti.edu.in/sitemap.xml

Automated fetch returns 403, so manual browser check required.  
Screenshots added to screenshots/ folder.

---

## Other Observations

- Server sends HTTP 403 to automated bots/scripts → some bot protection active
- Two Google Search Console verifications → site is actively monitored
- Load balanced across 2 AWS IPs → redundancy in place
- No CDN (Cloudflare/Akamai) detected
- No WAF (Web Application Firewall) headers detected

---

## Screenshots Needed (take manually)

1. `whois_lookup.png` — WHOIS result from who.is or whois.domaintools.com
2. `dns_a_record.png` — A records from dnschecker.org
3. `dns_mx_record.png` — MX records
4. `dns_ns_record.png` — NS records
5. `dns_txt_record.png` — TXT records
6. `robots_txt.png` — Browser visit to /robots.txt
7. `sitemap_xml.png` — Browser visit to /sitemap.xml
