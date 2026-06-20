# Scanning Networks (Module 03)

## Overview
This module covers the methodologies and techniques for scanning networks using Nmap (Network Mapper). It focuses on host discovery, port scanning, service enumeration, and advanced firewall evasion tactics.



## 1. Nmap (Network Mapper) Introduction
Nmap is an open-source tool used for network discovery and security auditing.

* **Basic Syntax:** `nmap <Scan_Type> <Options> <Target>`
* **Default Behavior:** If no port is specified, Nmap scans the top 1000 common ports.
* **Targeting:** You can scan a single IP, a range (`192.168.1.1-50`), or an entire subnet (`/24`).
* **Verbosity (`-v`, `-vv`, `-vvv`):** Increases the detail level of the output, which is crucial for debugging and real-time monitoring.
* **Bulk Scanning (`-iL`):** Reads target IPs from a file.
  * *Example:* `nmap -p80 -iL targets.txt`

---

## 2. Host Discovery (Finding Live Hosts)
Before port scanning, it is essential to determine which hosts are online.

* **Ping Scan (`-sn`):** Performs host discovery only; disables port scanning.
* **No Ping (`-Pn`):** Skips host discovery and treats all hosts as online. Crucial when firewalls block ICMP ping requests.
* **List Scan (`-sL`):** Lists the hosts that Nmap will scan without actually sending any packets to the targets.
* **ARP Ping (`-PR -sn`):** Uses ARP requests to discover live hosts on a local network (Ethernet).
* **ICMP Echo Ping (`-PE -sn`):** Sends ICMP Type 8 (Echo) requests.
* **TCP SYN Ping (`-PS<port>`):** Sends a TCP SYN packet to specified ports.
* **TCP ACK Ping (`-PA<port>`):** Sends a TCP ACK packet to specified ports.
* **UDP Ping (`-PU<port>`):** Sends a UDP packet to specified ports.

### Default Host Discovery Behavior
* **Privileged User (Local Network):** Uses ARP requests.
* **Privileged User (External Network):** Uses ICMP Echo, TCP ACK to port 80, TCP SYN to port 443, and ICMP timestamp requests.
* **Unprivileged User:** Resorts to a standard TCP 3-way handshake by sending SYN packets to ports 80 and 443.

### Host Discovery Commands Summary
| Scan Type | Example Command |
| :--- | :--- |
| **ARP Scan** | `sudo nmap -PR -sn MACHINE_IP/24` |
| **ICMP Echo Scan** | `sudo nmap -PE -sn MACHINE_IP/24` |
| **ICMP Timestamp** | `sudo nmap -PP -sn MACHINE_IP/24` |
| **ICMP Address Mask** | `sudo nmap -PM -sn MACHINE_IP/24` |
| **TCP SYN Ping** | `sudo nmap -PS22,80,443 -sn MACHINE_IP/30` |
| **TCP ACK Ping** | `sudo nmap -PA22,80,443 -sn MACHINE_IP/30` |
| **UDP Ping** | `sudo nmap -PU53,161,162 -sn MACHINE_IP/30` |

### DNS Resolution Options
| Option | Purpose |
| :--- | :--- |
| `-n` | Disables DNS resolution (speeds up scans). |
| `-R` | Forces reverse-DNS resolution for all hosts. |

---

## 3. Port Scanning Techniques

* **SYN Stealth Scan (`-sS`):** Known as a half-open scan. It is fast and stealthy because it never completes the TCP handshake. Nmap sends a SYN packet, receives a SYN/ACK, and immediately sends an RST packet to tear down the connection. Requires root privileges.
* **TCP Connect Scan (`-sT`):** Completes the full TCP 3-way handshake. Used when root access is unavailable. It is slower and highly visible in target logs.
* **UDP Scan (`-sU`):** Used to discover UDP services like DNS or SNMP. If a UDP port is closed, the target responds with an ICMP "Port Unreachable" message.

### Understanding Port States
1. **Open:** A service is actively listening and accepting connections.
2. **Closed:** The port is reachable, but no service is listening.
3. **Filtered:** A firewall is dropping packets; Nmap cannot determine if the port is open or closed.
4. **Unfiltered:** The port is accessible, but Nmap cannot determine its state (typically seen during ACK scans).

---

## 4. Enumeration & Identification

* **Service Version Detection (`-sV`):** Probes open ports to determine the exact service and application version running.
* **OS Fingerprinting (`-O`):** Attempts to identify the target's operating system by analyzing network packets.
* **Aggressive Scan (`-A`):** Combines OS detection, version detection, script scanning, and traceroute. *Note: Highly intrusive and noisy.*

---

## 5. Port Selection & Timing

* **Port Specification:**
  * `-p-` (Scans all 65,535 ports).
  * `-p 80,443` (Scans specific comma-separated ports).
* **Timing Templates (`-T`):** Ranges from `-T0` (Paranoid) to `-T5` (Insane). For real-world engagements, `-T4` offers the best balance of speed and accuracy without overwhelming the network.

---

## 6. Nmap Scripting Engine (NSE)
NSE uses Lua scripts to automate advanced tasks like vulnerability detection, brute-forcing, and advanced enumeration.

* **Default Scripts (`-sC`):** Runs a collection of common, safe scripts.
* **Specific Scripts:** `--script http-enum` (enumerates web directories) or `--script smb-os-discovery`.

---

## 7. Firewall Evasion & Stealth (Advanced)

* **ACK Scan (`-sA`):** Used to map out firewall rulesets and determine if a firewall is stateful or stateless.
* **Decoy Scan (`-D`):** Masks your actual IP address by mixing it with spoofed IP addresses during the scan.
* **Fragmentation (`-f`):** Fragments the IP packets into smaller pieces to bypass rudimentary packet filters and older firewalls.
* **Source Port Manipulation (`-g` or `--source-port`):** Sends traffic from trusted source ports (like DNS port 53) to bypass simplistic firewall rules.
  * *Example:* `nmap -sS -p80,443 -g 53 <Target_IP>`
* **Idle Scan (`-sI`):** The stealthiest scan. It utilizes a third-party "Zombie" machine to scan the target, ensuring your IP is never recorded in the target's logs.
  * *To find a Zombie:* `nmap --script ipidseq <Potential_Zombie_IP>`

*(Note: Firewalls can be configured to reject packets with a TCP RST. In Linux iptables: `iptables -I INPUT -p tcp --dport <port> -j REJECT --reject-with tcp-reset`)*

---

## 8. Practical Operations & Pro-Commands

### Tactical Scan Profiles
* **Stealth Reconnaissance:** `sudo nmap -sS -Pn -T2 <Target>`
* **Comprehensive Enumeration:** `sudo nmap -sS -sV -sC -p- -T4 -oN scan_results.txt <Target>`
* **Zombie (Idle) Scan:** `sudo nmap -Pn -sI <Zombie_IP> <Target_IP>`

### Operational Lab Insights
1. Adjusting timing templates (`-T`) is critical for bypassing threshold-based firewall alerts.
2. To quickly generate a list of IPs that will be scanned without performing DNS resolution or actual scanning: `nmap -sL -n <Target_Subnet>`
