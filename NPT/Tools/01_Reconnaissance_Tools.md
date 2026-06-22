# 🔍 Cybersecurity Toolkit Notes (Reconnaissance → PrivEsc)

A structured reference sheet for tools used in penetration testing workflows

---

## 1. RECONNAISSANCE PHASE

---

## 1.1 OSINT (Open Source Intelligence)

### Google Dorking

Advanced search operators used to find hidden or sensitive data indexed by search engines.

### Examples:

- login panels  
- exposed directories  
- misconfigured files  
- backups  

### Common operators:

- `site:`  
- `inurl:`  
- `intitle:`  
- `filetype:`  

---

## Shodan

Search engine for internet-connected devices.

### Used for:

- Open ports  
- Server versions  
- IoT devices  
- Misconfigured services  

---

## Censys

Internet-wide scanning platform used to analyze:

- SSL certificates  
- exposed services  
- infrastructure mapping  
- host metadata  

---

## 1.2 Domain & DNS Enumeration

---

## WHOIS

Retrieves domain registration details:

- registrar  
- owner info (if not private)  
- creation & expiry dates  
- name servers  

---

## nslookup

DNS lookup utility.

### Used for:

- A records (IP resolution)  
- MX records (mail servers)  
- NS records (name servers)  

### Example:


nslookup example.com


---

## dig (Domain Information Groper)

More advanced DNS query tool.

### Provides:

- detailed DNS responses  
- query time  
- TTL values  
- zone transfer testing  

### Example:


dig example.com ANY


---

## Online DNS Tools

- DNSDumpster → subdomain mapping visualization  
- SecurityTrails → historical DNS tracking  
- MXToolbox → email infrastructure analysis  
- VirusTotal → passive DNS + malware intelligence  
- dnsenum / fierce → subdomain brute force + zone transfer checks  
- Amass → large-scale subdomain enumeration (multi-source OSINT)  

---

## 1.3 Network Scanning & Firewall Detection

---

## Nmap

Core network scanning tool.

### Used for:

- port scanning  
- service detection  
- OS fingerprinting  
- firewall bypass techniques  

### Example:


nmap -sV -A target


---

## Netcat / Telnet

Used for manual interaction with services.

- banner grabbing  
- raw port testing  
- service validation  

---

## hping3

Packet crafting tool.

### Used for:

- firewall evasion testing  
- TTL manipulation  
- custom TCP/UDP packets  

---

## WAFW00F

Detects Web Application Firewalls:

- Cloudflare  
- AWS WAF  
- ModSecurity  

---

## Online Tools

- HackerTarget → online scanning platform  
- CORTEX IP Intel → custom intelligence & footprinting tool  

---

## 1.4 Web Footprinting & Directory Enumeration

---

## gobuster

Brute-force hidden directories/files using wordlists.


gobuster dir -u http://site -w wordlist.txt


---

## dirbuster

GUI-based directory brute-forcer.

---

## ffuf (Fuzz Faster U Fool)

Fast fuzzing engine for:

- directories  
- parameters  
- virtual hosts  

---

## feroxbuster

Recursive directory brute forcing tool.

### Key feature:

Automatically scans discovered subdirectories

### Example flow:

- `/admin`  
- `/admin/config`  
- `/admin/config/backup`  

---

## Web Tech Detection Tools

- Wappalyzer → browser-based tech detection  
- BuiltWith → backend technology profiling  

---

## 1.5 Email & Identity Intelligence

---

## theHarvester

Collects:

- emails  
- subdomains  
- employee names  

### Sources:

- search engines  
- PGP servers  
- LinkedIn  

---

## holehe

Checks where an email is registered.

---

## GHunt

Google account intelligence tool:

- Gmail owner info  
- Google Maps activity  
- YouTube history  
- account metadata  

---

## Online Intelligence Tools

- Hunter.io → email pattern discovery  
- Skymem → email lookup database  
- Have I Been Pwned → breach detection  
- DeHashed → credential search engine  
- EmailRep → email reputation scoring  
- Sherlock → username OSINT across platforms  
- Maigret → advanced username enumeration  
- ExifTool → image metadata extraction  
