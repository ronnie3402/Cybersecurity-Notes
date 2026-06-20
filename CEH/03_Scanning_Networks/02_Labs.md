# Lab Notes: Mastering Nmap for Penetration Testing

## Overview
A structured guide to unlocking the full potential of Nmap for comprehensive network reconnaissance and vulnerability assessment. These techniques are essential for ethical penetration testing and security auditing.



## 1. Fundamentals: Host Discovery & Port Scanning
Identifying live hosts and open ports is the foundational step in any penetration test.

### Host Discovery
* **Ping Scan:** Performs host discovery only, identifying live IPs without scanning ports.
  * `nmap -sn 192.168.1.0/24`
* **No Ping (Treat as Online):** Skips ping checks. Essential when ICMP is blocked by a firewall.
  * `nmap -Pn 192.168.1.10`

### Port Scanning Techniques
* **SYN Scan (Stealth):** Half-open TCP scan. It is fast, stealthy, and requires root privileges.
  * `nmap -sS 192.168.1.10`
* **TCP Connect Scan:** Completes the full TCP handshake. Used when root privileges are not available.
  * `nmap -sT 192.168.1.10`
* **UDP Scan:** Targets UDP services such as DNS, SNMP, and NTP.
  * `nmap -sU 192.168.1.10`

---

## 2. Deep Scan: Comprehensive Port Enumeration
Granular control over port selection ensures no potential entry points are missed.

* **Scan All 65535 Ports:** Uncovers less obvious services.
  * `nmap -p- 192.168.1.10`
* **Scan Specific Ports:** Focuses on a predefined list to reduce scan time.
  * `nmap -p 22,80,443 192.168.1.10`
* **Scan Top Ports:** Scans the most commonly used ports (default is 1000). Offers a balance of speed and coverage.
  * `nmap --top-ports 100 192.168.1.10`

---

## 3. Identification: Service, Version & OS Detection
Determining the specific services and operating systems provides crucial intelligence for targeted attacks.

* **Service Version Detection (`-sV`):** Detects the exact service and version running on open ports. Critical for vulnerability mapping.
  * `nmap -sV 192.168.1.10`
* **OS Fingerprinting (`-O`):** Attempts to identify the target's operating system to aid in exploit selection.
  * `nmap -O 192.168.1.10`
* **Aggressive Scan (`-A`):** Combines OS detection, version detection, script scanning, and traceroute. *(Note: Highly noisy; not recommended for stealth operations).*
  * `nmap -A 192.168.1.10`

---

## 4. Optimization: Timing, Speed & Reporting
Adjusting timing and saving output are key for efficient, well-documented engagements.

### Timing & Speed Control
* **Fast Scan (`-T4`):** Optimizes for speed; suitable for most modern networks.
  * `nmap -T4 192.168.1.10`
* **Paranoid Scan (`-T0`):** Extremely slow scan designed to evade IDS detection.
  * `nmap -T0 192.168.1.10`

### Output & Reporting Formats
* **Normal Output:** `nmap -oN scan.txt 192.168.1.10`
* **XML Output:** `nmap -oX scan.xml 192.168.1.10`
* **Grepable Output:** `nmap -oG scan.gnmap 192.168.1.10`

---

## 5. Nmap Scripting Engine (NSE)
The NSE allows Nmap to perform advanced tasks like vulnerability detection, brute-forcing, and sophisticated enumeration.

### Standard NSE Execution
* **Default Safe Scripts:** Runs a set of common checks for basic enumeration.
  * `nmap -sC 192.168.1.10`
* **Vulnerability Scripts:** Detects known vulnerabilities on the target.
  * `nmap --script vuln 192.168.1.10`
* **HTTP Enumeration:** Discovers common web directories and misconfigurations.
  * `nmap --script http-enum -p80 192.168.1.10`

### Advanced NSE Script Applications
* **SMB Share Enumeration:** Lists accessible Server Message Block (SMB) shares.
  * `nmap --script smb-enum-shares -p445 192.168.1.10`
* **SMB OS Discovery:** Identifies the exact Windows version via SMB.
  * `nmap --script smb-os-discovery -p445 192.168.1.10`
* **FTP Anonymous Login Check:** Checks for anonymous FTP access, a common misconfiguration.
  * `nmap --script ftp-anon -p21 192.168.1.10`

---

## 6. Defense Bypass & Evasion Techniques
Bypassing firewalls and Intrusion Detection Systems (IDS) is critical for realistic testing.

* **Fragment Packets (`-f`):** Splits packets into smaller fragments to evade simple firewalls and basic packet inspection.
  * `nmap -f 192.168.1.10`
* **Decoy Scan (`-D`):** Obfuscates the true source of the scan by mixing your IP with random decoy IP addresses.
  * `nmap -D RND:5 192.168.1.10`

---

## 7. Real-World Execution: The Pentester's Staple Command
This combination is highly effective and frequently used during the in-depth enumeration phase of a real-world penetration test.

`nmap -sS -sV -p- -T4 -oN full_scan.txt 192.168.1.10`

**Breakdown:**
* `-sS`: SYN stealth scan for efficiency and discretion.
* `-sV`: Detects service versions for vulnerability mapping.
* `-p-`: Scans all 65,535 TCP ports to ensure full coverage.
* `-T4`: Applies aggressive timing for faster results.
* `-oN full_scan.txt`: Saves the output in a readable text format.

