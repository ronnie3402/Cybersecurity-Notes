# Wireless Network Security & Exploitation (Module 17)

## 1. Introduction

Wireless networks are an essential part of modern organizations. Their flexibility and ease of deployment also introduce security risks. Weak encryption, misconfigured access points, and insecure protocols can expose networks to unauthorized access.

The primary objectives of wireless security assessments are:

- Identify wireless infrastructure.
- Evaluate encryption and authentication mechanisms.
- Detect vulnerabilities and misconfigurations.
- Identify possible attack vectors.

---

## 2. Wireless Network Fundamentals

### Core Terminology

| Term | Description |
|-------|-------------|
| SSID | Service Set Identifier (wireless network name). |
| BSSID | MAC address of the Access Point. |
| Access Point (AP) | Device that provides wireless connectivity to clients. |
| Client/Station | Device connected to the wireless network. |
| Channel | Radio frequency channel used by the AP. |
| MAC Filtering | Allows only approved MAC addresses to access the network. |

### IEEE 802.11 Standards

| Standard | Frequency | Maximum Speed |
|-----------|------------|---------------|
| 802.11a | 5 GHz | 54 Mbps |
| 802.11b | 2.4 GHz | 11 Mbps |
| 802.11g | 2.4 GHz | 54 Mbps |
| 802.11n | 2.4/5 GHz | 600 Mbps |
| 802.11ac | 5 GHz | Several Gbps |
| 802.11ax (Wi-Fi 6) | 2.4/5 GHz | Higher Throughput |

### Network Modes

#### Infrastructure Mode

Clients communicate through an Access Point.

#### Ad-Hoc Mode

Devices communicate directly without using an Access Point.

---

## 3. Wireless Encryption Standards

### WEP (Wired Equivalent Privacy)

- Oldest wireless encryption standard.
- Uses the RC4 encryption algorithm.
- Vulnerable due to weak 24-bit Initialization Vectors (IVs).
- Can often be cracked within minutes if enough packets are captured.

### WPA (Wi-Fi Protected Access)

- Introduced as a replacement for WEP.
- Uses TKIP encryption.
- Vulnerable to dictionary attacks and weak passwords.

### WPA2 (IEEE 802.11i)

- Widely deployed standard.
- Uses AES encryption.
- Vulnerable to weak passwords, PMKID attacks, and KRACK attacks.

### WPA3

- Latest wireless security standard.
- Uses SAE (Simultaneous Authentication of Equals).
- Research has identified Dragonblood vulnerabilities.

### Encryption Comparison

| Standard | Status | Main Weakness | Common Attack |
|-----------|--------|---------------|----------------|
| WEP | Obsolete | Weak IVs | Packet Capture |
| WPA | Legacy | Weak Passwords | Dictionary Attack |
| WPA2 | Widely Used | PMKID, Weak Passwords | Handshake Cracking |
| WPA3 | Current | Dragonblood | SAE Exploitation |

---

## 4. Common Wireless Threats

### Passive Reconnaissance

Attackers silently monitor wireless traffic to collect information such as:

- SSID
- BSSID
- Channel
- Encryption type
- Connected clients

### Man-in-the-Middle (MITM)

An attacker intercepts communication between the client and the legitimate Access Point.

Possible outcomes include:

- Credential theft
- Session hijacking
- Traffic manipulation

### Rogue Access Point

An unauthorized Access Point deployed within an organization's network.

### Evil Twin Attack

An attacker creates a fake Access Point that mimics a legitimate wireless network.

### Denial of Service (DoS)

Attackers disrupt wireless communications by generating interference or flooding management frames.

---

## 5. Wireless Hacking Methodology

### Step 1: Discovery and Reconnaissance

Identify target wireless networks.

Common tools:

- Airodump-ng
- Kismet
- WiFi Explorer

Collected information includes:

- SSID
- BSSID
- Channel
- Encryption type
- Signal strength

### Step 2: Traffic Monitoring

Switch the wireless adapter to Monitor Mode.

```bash
airmon-ng start wlan0
```

### Step 3: Credential Capture

Capture:

- WPA/WPA2 Handshakes
- PMKID Hashes

### Step 4: Password Cracking

Perform offline cracking using:

- Aircrack-ng
- Hashcat
- John the Ripper

### Step 5: Network Access

Use recovered credentials to gain network access.

### Step 6: Post Exploitation

After obtaining access, an attacker may perform:

- Internal enumeration
- Device discovery
- Traffic interception
- Pivoting

---

## 6. WiFi Password Cracking Techniques

### Dictionary Attack

Uses predefined wordlists to guess passwords.

Common wordlists:

- `rockyou.txt`
- SecLists

### Brute Force Attack

Attempts every possible password combination.

### PMKID Attack

Extracts PMKID directly from the Access Point.

Advantages:

- No client is required.
- No deauthentication attack is necessary.

### Rainbow Tables

Uses precomputed hashes to speed up password cracking.

---

## 7. Advanced Wireless Attacks

### Deauthentication Attack

Sends forged deauthentication frames to disconnect clients from the network.

Common uses:

- Capturing WPA handshakes.
- Supporting Evil Twin attacks.

Tool:

```bash
aireplay-ng
```

### Evil Twin Attack

Creates a fake Access Point with the same SSID as the legitimate network.

Objectives:

- Credential harvesting
- Traffic interception

### Jamming Attack

Creates radio interference to disrupt wireless communications.

---

## 8. Bluetooth Security

Bluetooth is a short-range wireless communication technology commonly used in mobile and IoT devices.

### Common Bluetooth Attacks

#### Bluejacking

Sending unsolicited messages to nearby Bluetooth devices.

#### Bluesnarfing

Unauthorized extraction of sensitive information such as:

- Contacts
- SMS messages
- Files

#### Bluebugging

Provides extensive control over the victim device.

### Important Bluetooth Vulnerabilities

| Vulnerability | Description |
|--------------|-------------|
| BlueBorne | Remote Code Execution vulnerability |
| KNOB Attack | Encryption downgrade attack |
| Weak Pairing | Insecure pairing mechanisms and weak PINs |

### Bluetooth Tools

- Btlejack
- Ubertooth
- BlueHydra

---

## 9. Hands-On Lab Exercises

### Lab 1: Wireless Discovery

**Objectives:**

- Discover wireless networks.
- Identify encryption mechanisms.

**Tool:**

```bash
airodump-ng
```

### Lab 2: WPA/WPA2 Handshake Capture and Cracking

**Objectives:**

- Capture WPA handshakes.
- Perform offline password cracking.

**Tools:**

```bash
aireplay-ng
hashcat
```

### Lab 3: Evil Twin and Bluetooth Assessment

**Objectives:**

- Deploy a rogue Access Point.
- Enumerate Bluetooth devices.

---

## 10. Common Wireless Security Tools

| Tool | Purpose |
|-------|---------|
| Aircrack-ng | Wireless auditing suite |
| Airodump-ng | Packet capture and analysis |
| Aireplay-ng | Packet injection |
| Hashcat | Password cracking |
| John the Ripper | Password cracking |
| Kismet | Wireless reconnaissance and IDS |
| Ubertooth | Bluetooth assessment |
| BlueHydra | Bluetooth discovery |

---

## 11. Defensive Measures

- Use strong WPA3 or WPA2 passphrases.
- Change default SSIDs and credentials.
- Disable WPS.
- Detect and remove rogue Access Points.
- Regularly update firmware.
- Deploy Wireless IDS/IPS solutions.
- Implement network segmentation.

---

## 12. Quick Revision

- WEP is obsolete and insecure.
- WPA2 is vulnerable to handshake and PMKID attacks if weak passwords are used.
- Evil Twin and MITM attacks are commonly used for credential theft.
- Deauthentication attacks are useful for handshake capture.
- Common Bluetooth attacks include Bluejacking, Bluesnarfing, and Bluebugging.
- Aircrack-ng and Hashcat are widely used during wireless security assessments.

> **Ethical Note:** Perform wireless security testing only in authorized environments or with explicit written permission.
