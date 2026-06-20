# Network Attacks & Packet Analysis (Module 09)

# Overview
This module covers the core tools and methodologies used for network traffic analysis, Layer-2 attacks (DHCP, STP, ARP), and modern Man-in-the-Middle (MitM) frameworks. Understanding both offensive techniques and defensive configurations is critical for network security.



## 1. Packet Analysis: Wireshark vs. tcpdump
Security professionals rarely rely on a single tool; instead, they use a combination of tools depending on the environment (GUI vs. Headless).

### Wireshark: The Analysis Engine
* **Interface:** Graphical User Interface (GUI).
* **Core Use:** Deep Packet Inspection (DPI), visual protocol debugging, and security auditing.
* **Context:** Used on desktop environments to visually dissect packet structures, filter specific protocols (HTTP, DNS), and analyze attack signatures.

### tcpdump: The Capture Engine
* **Interface:** Purely Command Line (CLI).
* **Core Use:** Quick packet capturing, automation via shell scripts, and monitoring headless systems.
* **Context:** Extremely lightweight. Ideal for remote Linux/Unix servers lacking a GUI.

> **Pro-Workflow (Real-World Tactic):** > Run `tcpdump` on a remote server to capture traffic without consuming high CPU/RAM, save the output to a `.pcap` file, download it locally, and open it in **Wireshark** for deep visual analysis.

| Feature | Wireshark | tcpdump |
| :--- | :--- | :--- |
| **Interface** | GUI | CLI |
| **Resource Usage** | Heavy (High RAM/CPU) | Extremely Lightweight |
| **Platform** | Windows, macOS, Linux | Unix/Linux standard |
| **Primary Role** | Detailed Analysis & Decoding | Remote Capture & Automation |

---

## 2. DHCP Threats & Mitigations



### Rogue DHCP Server
An unauthorized DHCP server connected to the network. Since DHCP operates on a "First Come, First Served" basis, a rogue server attempts to reply to `DHCP Discover` messages faster than the legitimate server.
* **The Exploit:** The rogue server assigns victims a fake IP, fake DNS, and sets its own IP as the Default Gateway.
* **Impact:** * **Man-in-the-Middle (MitM):** All outbound traffic routes through the attacker's machine.
  * **DNS Poisoning:** Redirects users to phishing sites.
  * **DoS:** Assigns an invalid subnet/gateway, dropping the victim from the network.

### DHCP Snooping (The Defense)
A Layer-2 switch security feature that filters untrusted DHCP messages.
* **Trusted Ports:** Connected to the legitimate DHCP server. The switch allows all DHCP traffic (Offers, ACKs) through these ports.
* **Untrusted Ports:** Connected to standard users/devices. If a `DHCP Offer` originates from an untrusted port, the switch immediately drops the packet.
* **Binding Database:** The switch builds a table matching MAC addresses, assigned IP addresses, and port numbers to prevent IP Spoofing.
* **Rate Limiting:** Prevents **DHCP Starvation** by limiting untrusted ports to a specific number of DHCP requests per second (blocking tools like *Yersinia*).

**Implementation (Cisco IOS):**
```bash
# Enable globally
ip dhcp snooping

# Enable for specific VLAN
ip dhcp snooping vlan 10

# Configure the uplink to the real DHCP server as trusted
interface GigabitEthernet0/1
ip dhcp snooping trust

```

---

# 3. Network Sniffing Frameworks: dsniff vs. Bettercap

## The Legacy Suite: dsniff

Created in the 1990s, dsniff is a collection of individual tools. It remains highly relevant for understanding basic attack mechanics and is frequently tested in CEH exams.

- **arpspoof**: Redirects traffic between the victim and router (MitM setup).
- **dsniff**: The core password sniffer. Extracts credentials from unencrypted protocols (FTP, Telnet, HTTP).
- **urlsnarf**: Logs all URLs visited by the victim.
- **filesnarf**: Captures files transmitted over NFS.

## The Modern Framework: Bettercap

Considered the "Swiss Army Knife" of modern penetration testing. It consolidates spoofing, sniffing, and SSL stripping into a single interactive framework.

- **All-in-One**: Replaces the need to run arpspoof and dsniff in separate terminals.
- **Caplets**: Allows automation of complex attacks using `.cap` script files.
- **Modern Targets**: Supports WiFi, Bluetooth Low Energy (BLE), HID attacks, and modern HSTS/HTTPS bypass techniques.

## Critical Prerequisite (IP Forwarding)

Before running any MitM spoofing tool, Linux IP Forwarding must be enabled. If left disabled, intercepted packets are dropped, cutting off the victim's internet and alerting them to the attack.

```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```

### Comparison

| Feature | dsniff Suite | Bettercap |
|---|---|---|
| Architecture | Collection of individual, single-purpose CLI tools | Unified, modular framework with Interactive UI |
| Capability | Basic Spoofing & Sniffing | Spoofing, Sniffing, SSL Strip, WiFi, BLE |
| Automation | Bash scripting required | Built-in JavaScript "Caplets" |

---

# 4. Spanning Tree Protocol (STP) Attacks

## STP Logic

STP prevents catastrophic network loops (Broadcast Storms) in redundant switch topologies by electing a Root Bridge and blocking redundant paths. The switch with the lowest Bridge Priority becomes the Root Bridge.

## The Attack (Root Bridge Takeover)

1. The attacker connects to an access port and launches a tool like Yersinia.
2. The attacker injects malicious BPDU (Bridge Protocol Data Unit) packets claiming a Bridge Priority of 0 (the lowest possible).
3. The legitimate switches accept the fake BPDUs and restructure the network topology, electing the attacker's machine as the new Root Bridge.

**Impact**: All inter-switch traffic is routed through the attacker, enabling massive-scale sniffing or Denial of Service (DoS).

## The Defense

- **BPDU Guard**: Applied to user-facing access ports. If a BPDU packet is received on that port, the switch immediately places the port into an err-disable (shutdown) state, neutralizing the attacker.
- **Root Guard**: Enforces the root bridge placement. It ignores any superior BPDUs received on specific ports, ensuring the attacker cannot seize the Root Bridge role.

---

# 5. Layer-2 Attack & Defense Cheat Sheet

| Attack Vector | Primary Tool(s) | Enterprise Detection / Mitigation |
|---|---|---|
| Packet Sniffing | Wireshark, tcpdump | Force HTTPS, Enterprise VPN, E2E Encryption. |
| MAC Flooding | macof, Yersinia | Enable Port Security on switches. |
| DHCP Attacks (Starvation/Rogue) | Yersinia, Metasploit | Enable DHCP Snooping & Rate Limiting. |
| ARP Spoofing/Poisoning | Bettercap, Ettercap, arpspoof | arpwatch, Dynamic ARP Inspection (DAI). |
| MAC Spoofing | macchanger | 802.1X Port-Based Authentication (NAC). |
| STP Attack | Yersinia | BPDU Guard, Root Guard. |
| DNS Poisoning | dnsspoof, Responder | DNSSEC, Trusted internal DNS resolution. |
