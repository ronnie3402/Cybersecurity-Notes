# Footprinting and Reconnaissance (Module 02)

## Overview
Reconnaissance (or Footprinting) is the first and most critical phase of the ethical hacking methodology. The objective is to gather as much intelligence about the target as possible to accurately map the attack surface before planning an engagement.

---

## The 4 Systematic Phases of Reconnaissance

| Phase | Type | Target Focus | Risk of Detection |
| :--- | :--- | :--- | :--- |
| **1. OSINT** | Passive | Public Records, Search Engines, Social Media | Very Low |
| **2. Network** | Passive/Active | IP Ranges, DNS, Infrastructure | Low to Medium |
| **3. Web** | Active | Websites, Web Servers, Hidden Directories | Medium |
| **4. Social** | Active | Human Assets (Employees, Vendors) | High |

---

## Phase 1: OSINT & Passive Footprinting
Open Source Intelligence (OSINT) involves gathering data from publicly available sources without directly interacting with the target's internal infrastructure.

### Company vs. Individual Footprinting
* **Company Footprinting:** Maps the organizational attack surface (Domains, IP ranges, Tech stacks, Cloud assets).
* **Individual Footprinting:** Maps personal digital traces for background verification or threat intelligence (Username enumeration, breached credentials, metadata analysis).

| Factor | Company Footprinting | Individual Footprinting |
| :--- | :--- | :--- |
| **Focus** | IT Infrastructure & Organization | Personal digital traces |
| **Data Type** | IPs, Domains, Servers, ASNs | Usernames, Social profiles, Emails |
| **Risk** | Technical exposure | Privacy exposure |

### Google Dorking (Advanced Search Operators)
Using advanced operators to uncover hidden files, misconfigurations, and sensitive data indexed by search engines.

| Operator | Function | Example |
| :--- | :--- | :--- |
| `site:` | Restricts search to a specific domain/subdomain. | `site:example.com` or `site:example.com -www` |
| `""` | Exact phrase match. | `"internal use only"` |
| `OR` | Searches multiple possibilities. | `site:example.com (login OR admin)` |
| `filetype:`| Searches for specific file extensions. | `site:example.com (filetype:pdf OR filetype:xlsx)` |
| `inurl:` | Finds specific text within the URL. | `site:example.com inurl:admin` |
| `intitle:` | Finds specific text within the page title. | `site:example.com intitle:"index of"` |
| `cache:` | Displays Google's cached version of a page. | `cache:example.com` |

### WHOIS Lookup
A standard protocol used to query databases that store the registered users or assignees of an Internet resource (Domain name or IP address).
* **Key Output Data:** Creation/Expiry dates, Name Servers (NS), Registrar info, and Domain Status (e.g., `clientTransferProhibited`).
* **Privacy Controls:** Services like "WhoisGuard" mask the actual registrant's details.
* **Commands:** `whois example.com` or `whois 8.8.8.8` | `whois example.com | grep -E "OrgName|Country"`

### Email & Credential Intelligence
* **Online Platforms:** `Hunter.io` (Email discovery), `HaveIBeenPwned` (Breach exposure), `DeHashed` (Leaked credentials).
* **CLI Tools:**
  * `theHarvester -d example.com -b google,bing,linkedin` (Aggregates emails from multiple sources).
  * `holehe test@example.com` (Checks which platforms an email is registered with).

---

## Phase 2: Network Footprinting & DNS Enumeration
Mapping the internal and external network infrastructure, identifying IP ranges, and resolving hostnames.

### Core DNS Records
1. **A Record (Address):** Maps a domain name (e.g., google.com) to its IPv4 address.
2. **MX Record (Mail Exchanger):** Specifies the mail servers responsible for accepting email on behalf of a domain. Critical for planning phishing engagements.
3. **TXT Record (Text):** Used for domain verification and email security policies.

### Email Authentication Protocols (SOC Analyst Essentials)
These DNS TXT records are critical for preventing email spoofing:
* **SPF (Sender Policy Framework):** Lists the IP addresses/servers authorized to send emails on behalf of the domain.
* **DKIM (DomainKeys Identified Mail):** Adds a cryptographic signature to emails to ensure the content wasn't altered in transit.
* **DMARC:** Specifies how the receiving server should handle emails that fail SPF or DKIM checks (e.g., Reject or Quarantine).

### CLI Tools: NSLOOKUP vs. DIG
Both tools are used to query DNS servers, but they serve different operational needs.

| Feature | `nslookup` | `dig` (Domain Information Groper) |
| :--- | :--- | :--- |
| **Output** | Basic, easy to read. | Detailed (includes flags, headers, query time). |
| **Usage** | Quick manual queries. | Advanced scripting, automation, zone transfers. |

**Command Examples:**
* `nslookup -type=mx example.com`
* `dig example.com ANY`
* `dig @8.8.8.8 example.com` (Queries a specific nameserver).

#### Understanding DNS Output (Practical Analysis)
When analyzing output from a DNS query:
* **Local Gateway (`192.168.168.2#53`):** Indicates the local router/resolver handling the request via UDP Port 53.
* **Non-authoritative answer:** The response came from a local cache, not directly from the domain's primary nameserver.
* **TTL (Time to Live):** The time (in seconds) the DNS record remains in the resolver's cache before requiring a refresh. (Low TTLs often indicate the use of Load Balancers or WAFs like Cloudflare).
* **DIG Flags (`qr rd ra`):**
  * `qr`: Query Response.
  * `rd`: Recursion Desired (Client asked server to resolve the query fully).
  * `ra`: Recursion Available (Server supports recursive queries).

### Advanced Network Footprinting
* **DNS Zone Transfer (`dig axfr example.com @ns1.example.com`):** If misconfigured, allows an attacker to copy the entire DNS database (all subdomains).
* **Firewall/WAF Detection:**
  * `wafw00f https://example.com` (Automated WAF fingerprinting).
  * `nmap -sA example.com` (ACK scan to map firewall rulesets and filtered ports).

---

## Phase 3: Web Footprinting & Directory Enumeration
Analyzing the target's web applications, server technologies, and hidden structures.

* **Tech-Stack Identification:** Using tools like Wappalyzer or BuiltWith to identify languages (PHP, Python) and CMS platforms.
* **Banner Grabbing:** Extracting software and version details directly from service ports (e.g., `Server: Apache/2.4.41`).
* **Robots.txt Analysis:** Checking `/robots.txt` for disallowed paths and hidden directories.

### Directory & File Enumeration
The automated process of brute-forcing hidden directories and files (admin panels, backups, `.git` folders) that are not linked publicly.
* **Mechanism:** The tool sends requests using a wordlist and analyzes HTTP Status Codes.
  * `200 OK`: Directory/File exists.
  * `403 Forbidden`: Exists, but access is denied (High-value target for privilege escalation).
  * `404 Not Found`: Does not exist.
* **Standard Tools:** `Gobuster`, `FFUF`, `Dirb`.
* **Mitigation:** Disable directory listing, remove legacy backup files, and implement IP-based restrictions for admin panels.

---

## Phase 4: Social Engineering (Active Recon)
Exploiting human psychology rather than technical flaws. This is an active phase requiring direct interaction.
* **Phishing:** Fraudulent emails designed to steal credentials or deploy malware.
* **Vishing (Voice Phishing):** Telephone scams impersonating IT support or executives.
* **Tailgating:** Following an authorized person into a secure physical area without swiping an ID.
* **Shoulder Surfing:** Observing someone entering sensitive information (passwords, PINs) directly.
