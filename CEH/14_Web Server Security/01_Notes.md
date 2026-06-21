# Exploiting Web Servers: Vulnerabilities & Defense (Module 14)

Web servers sit right at the edge of an organization's network — publicly reachable, always-on, and often running a mix of software that's easy to misconfigure. That combination makes them one of the most heavily targeted pieces of infrastructure on the internet. This module covers how attackers approach web servers — through misconfigurations, outdated software, DNS weaknesses, and missed patches — and the defensive techniques that counter each of those angles.

---

## 1: How Web Servers Operate

Before attacking (or defending) a web server, it helps to understand what it's actually doing: acting as the **middleman** between a client (a browser) and the application/data behind it. A web server receives an HTTP/HTTPS request, processes it according to its configuration, and sends back the appropriate response. That behavior is controlled through configuration files — `httpd.conf` for Apache, `nginx.conf` for Nginx, and so on.

**Simple way to picture it:** think of a web server as a restaurant's waiter. You (the browser) place an order (a request). The waiter doesn't cook the food themselves — they pass your order to the kitchen (the application/database), then bring back whatever the kitchen prepares (the response). If the waiter mishandles your order or has bad habits (misconfiguration), that's where things go wrong — even if the kitchen itself is fine.

### The Major Players

| Web Server | Known For |
|---|---|
| **Apache** | Open-source, extremely flexible and customizable |
| **Nginx** | High performance, commonly used as a reverse proxy / load balancer |
| **Microsoft IIS** | Deep integration with Windows environments |

---

## 2: Common Web Server Attack Vectors

These are the four most frequent ways attackers actually get a foothold on a web server — notice that none of them require a fancy zero-day exploit; they almost all come down to human error or neglect.

| Attack Vector | What It Means | Why It Happens |
|---|---|---|
| **Misconfiguration Attacks** | Exploiting improperly configured settings, or accounts still using default credentials | Admins leave default/test settings in place after setup |
| **Outdated Vulnerabilities** | Targeting unpatched server software with publicly known exploits | Patches are delayed or skipped entirely |
| **Directory Listing** | Unauthorized browsing of a server's file structure, exposing files never meant to be public | Directory browsing isn't disabled in the server config |
| **Improper Permissions** | Weak file/folder permissions that let attackers modify content they shouldn't be able to touch | Files given broader read/write access than necessary |

**In plain terms:** most of these aren't "hacking" in the dramatic sense — they're closer to walking through a door someone forgot to lock, or finding a filing cabinet left unlocked with sensitive papers in plain view.

---

## 3: DNS Server Hijacking

DNS is what translates a domain name (`example.com`) into the IP address browsers actually use to connect. If an attacker can tamper with that translation step, they can quietly redirect a victim's traffic anywhere they want — without the victim ever noticing the URL bar look "wrong."

### Attack Methods

1. **DNS Cache Poisoning** — corrupting a DNS resolver's cache with false records, so it serves up incorrect (attacker-controlled) IP addresses for legitimate domain names.
2. **Unauthorized Modification** — directly altering DNS records (often via stolen admin credentials) to redirect traffic at the source.
3. **Traffic Redirection** — the end result of the above: users land on phishing pages or malicious servers while believing they're on the real site.

> **Analogy:** Imagine someone secretly swapping the street signs in your neighborhood. You follow the sign exactly as always, but it now leads you to a fake building that looks identical to your destination. You'd have no reason to suspect anything was wrong.

### Defense Strategies

| Defense | How It Helps |
|---|---|
| **DNSSEC Implementation** | Adds cryptographic signatures to DNS responses, so forged or tampered records can be detected and rejected |
| **Secure Credentials** | Strong authentication around DNS management consoles, preventing unauthorized record changes |
| **Continuous Monitoring** | Actively tracking DNS records for unexpected changes, so tampering is caught quickly rather than discovered after damage is done |

---

## 4: Enhancing Web Server Security

No single tool secures a web server on its own — real-world hardening relies on layering several types of protection together.

| Tool / Practice | Purpose |
|---|---|
| **Nessus & OpenVAS** | Professional vulnerability assessment platforms that scan for known weaknesses across the server |
| **CIS Benchmarks** | Industry-standard configuration guidelines for hardening server settings (closing the kind of gaps described in Module 2) |
| **Web Application Firewall (WAF)** | Filters malicious traffic in real time, blocking common web attack patterns before they reach the application |

> **Key Takeaway:** Combine multiple security layers — regular patching, vulnerability scanning, and WAF deployment — to build a defense that doesn't rely on any single control catching everything. This is the classic **defense-in-depth** principle.

---

## 5: Website Defacement Prevention

"Defacement" refers to an attacker altering a website's visible content — replacing a homepage with their own message, for example. It's one of the more visible (and embarrassing) outcomes of poor server security.

### Root Causes

Defacement usually traces back to one of three things: **weak file permissions, vulnerable plugins, or compromised admin credentials** — any of which gives an attacker the ability to write to content they shouldn't be able to touch.

### Detection

**File integrity monitoring (FIM)** systems track the state of web content over time and immediately alert administrators when something changes unexpectedly — catching a defacement within minutes instead of when a visitor reports it.

### Prevention Strategy

- **Least privilege access control** — accounts and processes should only have the minimum access they actually need, so even a compromised account has limited reach.
- **Regular security audits** — periodically reviewing all web assets (plugins, permissions, accounts) rather than assuming a "set it and forget it" configuration stays safe.

---

## 6: Web Server Attack Methodology

This is the structured process a penetration tester (or attacker) follows when targeting a web server — essentially a mini "kill chain" specific to web infrastructure.

| Stage | What Happens |
|---|---|
| **1. Information Gathering** | Reconnaissance to identify which technologies the target server runs and where potential weaknesses might exist |
| **2. Server Fingerprinting** | Pinning down exact server versions, the underlying OS, and which services are actively running |
| **3. Vulnerability Scanning** | Automated tools sweep the server for known weaknesses and misconfigurations |
| **4. Exploitation Phase** | Actually attempting to exploit identified weaknesses — this is where hands-on lab work and real attack scenarios come in |
| **5. Privilege Escalation** | Turning initial, limited access into higher-level control — the goal of compromising the entire server, not just one component |
| **6. Reporting & Mitigation** | Documenting every finding clearly and implementing the fixes needed to close the gaps discovered |

**Why this order matters:** each stage builds directly on the last. You can't meaningfully scan for vulnerabilities (stage 3) without first knowing what software is running (stage 2), and you can't fix anything properly (stage 6) without a clear record of exactly what was found and how it was exploited.

---

## 7: The Critical Importance of Patch Management

### The Risk of Staying Unpatched

Outdated systems remain vulnerable to **known** exploits — and these aren't obscure threats. Attackers run automated scanning campaigns across the internet constantly, specifically hunting for systems that haven't applied a patch for a vulnerability that's already public knowledge. An unpatched server isn't a hidden risk; it's often found within hours of being exposed (the same dynamic discussed with honeypot discovery times).

### Building a Patch Management Routine

| Practice | Purpose |
|---|---|
| **Regular Updates** | Schedule OS and server software patches proactively, closing security gaps *before* they're exploited rather than reacting after a breach |
| **Automated Tools** | WSUS handles consistent patching across Windows environments; Linux package managers (`apt`, `yum`, etc.) do the equivalent on Linux |
| **Vulnerability Scanners** | Continuous scanning identifies which systems are missing patches and helps prioritize the most critical ones first |

---

## 8: Essential Tools for Web Server Assessment

These are the standard tools used during the reconnaissance and scanning stages of the attack methodology (Module 6).

### Nmap — Network Reconnaissance

Used for service discovery and version detection — identifying exactly what's running on which ports.

```bash
nmap -sV -p 80,443 target.local
```

### WhatWeb — Technology Fingerprinting

Identifies the underlying platforms, frameworks, and technologies a website is built on (e.g., WordPress, a specific CMS version, JavaScript libraries in use).

### Nikto — Web Server Vulnerability Scanner

A comprehensive scanner purpose-built for identifying server misconfigurations and known vulnerabilities.

```bash
nikto -h http://target.local
```

### Metasploit — Exploitation Framework

A full-featured exploitation framework with dedicated modules for web server attacks — used in the exploitation stage (Module 6, Stage 4) once a vulnerability has actually been identified and confirmed.

---

## Key Takeaways / Exam Summary

- A web server is fundamentally a **middleman** — request in, processed response out — governed entirely by its configuration files. Most attack vectors target *misconfiguration* of that process, not the underlying software itself.
- The four core attack vectors to remember: **misconfiguration, outdated software, directory listing, and improper permissions** — none require advanced exploits, just neglected basics.
- **DNS hijacking** (cache poisoning, record modification, traffic redirection) redirects users without any visible warning sign — defended primarily through **DNSSEC**, credential security, and monitoring.
- Strong web server security is always **layered**: vulnerability scanning (Nessus/OpenVAS) + hardened configuration (CIS Benchmarks) + active traffic filtering (WAF).
- **Defacement** is usually a symptom of weak permissions, vulnerable plugins, or stolen credentials — caught quickly via file integrity monitoring, and prevented via least-privilege access and regular audits.
- The standard **attack methodology** flows in order: Recon → Fingerprinting → Vulnerability Scanning → Exploitation → Privilege Escalation → Reporting. This order matters and shows up frequently in exam scenarios.
- **Patch management** isn't optional housekeeping — unpatched, known vulnerabilities are actively and automatically hunted across the internet, making them one of the fastest paths to compromise.
- Standard toolkit to know cold: **Nmap** (service/version discovery), **WhatWeb** (technology fingerprinting), **Nikto** (server vulnerability scanning), **Metasploit** (exploitation).
