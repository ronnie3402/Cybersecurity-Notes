# Lab Notes: Email Footprinting & Network Reconnaissance

## Overview
Essential OSINT techniques and tools for security analysts conducting passive and active reconnaissance operations.

---

## 1. Email OSINT & Footprinting
Email footprinting reveals organizational structure, employee information, and potential security exposures. It combines public data mining with breach databases to build comprehensive target profiles.

### Web-Based Email Intelligence Platforms
| Platform | Description |
| :--- | :--- |
| **Hunter.io** | Domain-based email discovery and verification. Identifies email patterns and locates addresses. |
| **EmailRep** | Analyzes email reputation scores and risk indicators for threat assessment workflows. |
| **Have I Been Pwned** | Checks emails against known data breaches to validate credential exposure. |
| **DeHashed** | Premium service providing access to leaked credentials from historical breaches. |
| **Skymem** | Indexes emails extracted from public documents and web archives to uncover legacy addresses. |

### Command-Line Email Reconnaissance
*Automates email discovery and validation for efficient large-scale operations.*

* **theHarvester:** Aggregates emails from multiple search engines and professional networks.
  * `theHarvester -d example.com -b google,bing,linkedin`
* **holehe:** Determines which platforms and services an email address is registered with.
  * `holehe test@example.com`

---

## 2. WHOIS Intelligence

### Online Platforms
* **ICANN WHOIS, ViewDNS, Who.is, ARIN:** Provide registrant details, administrative contacts, and IP allocation data via web interfaces.

### WHOIS Command-Line Techniques
* **Domain WHOIS Query:** Retrieves complete registration info including registrar, creation date, and nameservers.
  * `whois example.com`
* **IP Address Lookup:** Identifies IP block ownership, geolocation data, and network operator details.
  * `whois 8.8.8.8`
* **Filtered Extraction:** Extracts specific fields for streamlined analysis using `grep`.
  * `whois example.com | grep -E "OrgName|Country|Email"`

---

## 3. DNS Intelligence & Enumeration

### Online Resources
* **DNSDumpster:** Visual DNS mapping and subdomain discovery.
* **SecurityTrails:** Historical DNS records and infrastructure change tracking over time.
* **MXToolbox:** Mail server configuration and email delivery path analysis.
* **VirusTotal:** Passive DNS intelligence from malware analysis submissions.

### DNS Command-Line Arsenal
| Tool/Technique | Command | Function |
| :--- | :--- | :--- |
| **dig** | `dig example.com ANY` | Queries all available DNS record types for zone information gathering. |
| **nslookup** | `nslookup example.com` | Basic DNS resolution and nameserver interrogation. |
| **dnsenum** | `dnsenum example.com` | Automated subdomain enumeration with AXFR testing and reverse lookups. |
| **fierce** | `fierce --domain example.com` | Aggressive subdomain brute-forcing and DNS zone discovery. |
| **amass** | `amass enum -d example.com` | Enterprise-grade OSINT aggregation combining multiple data sources. |
| **Zone Transfer** | `dig axfr example.com @ns1.example.com` | Tests for misconfigured DNS servers allowing full zone data extraction. |

---

## 4. Firewall and WAF Identification
*Understanding defensive infrastructure helps penetration testers craft evasion strategies and security analysts assess protection posture.*

### Detection Platforms
* **WAFW00F Online:** Identifies web application firewalls through HTTP fingerprinting.
* **Cloudflare Radar:** Provides infrastructure intelligence and DDoS protection identification.

### Advanced Firewall Detection Commands
* **WAF Fingerprinting:** Automated WAF detection through response header and behavior analysis.
  * `wafw00f https://example.com`
* **Nmap Ping Bypass:** Skips host discovery when ICMP is blocked by perimeter defenses.
  * `nmap -Pn example.com`
* **ACK Scan:** Determines firewall rulesets and identifies filtered versus unfiltered ports.
  * `nmap -sA example.com`
* **Port Filtering:** Distinguishes between dropped packets and actively rejected connections for firewall rule mapping.
  * `nmap -p 1-1000 example.com`
* **Traceroute Analysis:** Maps network paths and identifies filtering devices along the route.
  * `nmap --traceroute example.com`
* **Packet Behavior Analysis:** Analyzes TTL values and ICMP responses to detect stateful inspection mechanisms.
  * `hping3 -S example.com -p 80 -c 5`
