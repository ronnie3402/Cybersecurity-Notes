# Wireshark: Wireless Traffic Analysis & Sniffing Notes

---

# 1. Wi-Fi Sniffing Basics: Managed Mode vs Monitor Mode

Initially, Wireshark only captured broadcast traffic because the wireless adapter was operating in **Managed Mode**.

## Managed Mode
- The default mode used by most Wi-Fi adapters.
- The adapter only receives packets specifically addressed to its own MAC address.
- Suitable for normal internet usage.

## Monitor Mode
- Allows the adapter to capture **all wireless packets** present in the air, regardless of destination.
- Essential for wireless penetration testing, packet analysis, and handshake capture.

### VMware Configuration for Monitor Mode
When using Kali Linux inside VMware:

- A USB Wi-Fi adapter must be connected directly to the VM.
- In VMware settings, select:

  ```
  VM → Removable Devices → USB Adapter → Connect (Disconnect from Host)
  ```

- This disconnects the adapter from the host OS and gives Kali direct hardware control.

---

# 2. Monitor Mode Setup (Manual Configuration)

The following commands were used to manually enable monitor mode:

```bash
sudo su
systemctl stop NetworkManager
systemctl stop wpa_supplicant
ifconfig wlan0 down
iwconfig wlan0 mode monitor
ifconfig wlan0 up
```

## Explanation

### `sudo su`
- Switches to the root user.

### `systemctl stop NetworkManager`
- Stops NetworkManager to avoid conflicts with monitor mode.

### `systemctl stop wpa_supplicant`
- Stops Wi-Fi authentication services that may interfere with packet capture.

### `ifconfig wlan0 down`
- Disables the wireless interface before changing its mode.

### `iwconfig wlan0 mode monitor`
- Changes the interface mode to monitor mode.

### `ifconfig wlan0 up`
- Re-enables the interface.

## Verification

Check whether monitor mode was successfully enabled:

```bash
iwconfig
```

Expected output:

```bash
Mode:Monitor
```

---

# 3. Target Discovery and Channel Locking

After enabling monitor mode, the next step is to identify nearby wireless networks.

## Discover Networks

Use:

```bash
airodump-ng wlan0
```

This displays:

- SSID
- BSSID (Router MAC Address)
- Channel Number
- Signal Strength
- Encryption Type

## Channel Matching

Wireless adapters can only listen effectively on one channel at a time.

If the target AP is operating on **Channel 1**, lock your adapter to the same channel:

```bash
airodump-ng -c 1 --bssid XX:XX:XX:XX:XX:XX wlan0
```

## Why Channel Locking Matters

Without channel locking:

- Packets may be missed.
- Handshakes may not be captured.
- WPA/WPA2 decryption will fail.

---

# 4. WPA2 Wi-Fi Decryption Setup in Wireshark

Wireless traffic is encrypted by default.

Wireshark requires the Wi-Fi password to decrypt packets.

## Steps

Navigate to:

```text
Edit → Preferences → Protocols → IEEE 802.11
```

Then:

- Enable:

  ```text
  Enable Decryption
  ```

- Add the key in the following format:

  ```text
  password:SSID
  ```

Example:

```text
12345678:Rohit_1831
```

---

# 5. The Importance of the 4-Way Handshake (EAPOL)

Providing only the Wi-Fi password is **not sufficient**.

Wireshark must also capture the WPA/WPA2 **4-Way Handshake**.

## Why?

When a client connects to a router:

- The router and client generate temporary session keys.
- These keys are exchanged during the handshake process.

Without this exchange, decryption cannot occur.

## Capturing a Handshake

While capturing traffic:

1. Disconnect the client from Wi-Fi.
2. Reconnect it.

This forces a new handshake.

## Verification

Use the following display filter:

```text
eapol
```

You should observe:

- Message 1
- Message 2
- Message 3
- Message 4

Once all four messages are captured, Wireshark can decrypt the session.

---

# 6. Packet Analysis and Important Protocols

After successful decryption, Wireshark displays higher-layer protocols.

## Common Protocols

### TCP
- Connection-oriented transport protocol.

### DNS
- Resolves domain names into IP addresses.

### HTTP
- Web communication protocol.

When these protocols appear instead of **802.11**, decryption is working correctly.

---

## QUIC Protocol

Many modern services such as:

- Google
- YouTube

use **QUIC (HTTP/3 over UDP)**.

Characteristics:

- Encrypted by default.
- Wireshark often displays:

  ```text
  Protected Payload
  ```

- Contents cannot be easily viewed.

---

## Destination IP Lookup

Security analysts frequently investigate destination IP addresses.

Methods include:

- WHOIS lookups
- Reverse DNS lookups
- Threat intelligence platforms

Purpose:

- Identify contacted websites.
- Detect suspicious communications.

---

## Server Name Indication (SNI)

During the TLS handshake, the destination hostname may appear in clear text.

Examples:

```text
linkedin.com
google.com
github.com
```

This helps analysts identify accessed websites even when traffic is encrypted.

---

# 7. SOC Analyst / Blue Team Perspective

Wireless traffic analysis is widely used by SOC analysts.

## Traffic Analysis

Analysts inspect:

- Source IPs
- Destination IPs
- Port Numbers
- Packet Sizes
- Timing Patterns

to identify suspicious behavior.

---

## Device Fingerprinting

Information such as:

- MAC Address
- Signal Strength (PWR)

can reveal:

- Device manufacturer
- Physical proximity
- Device type

---

## Indicators of Compromise (IOCs)

Analysts look for:

- Connections to malicious IP addresses
- Unusual traffic patterns
- Unknown destinations
- Suspicious DNS queries

---

## Deauthentication Attacks

Attackers may force devices to reconnect by sending deauthentication frames.

Purpose:

- Capture WPA/WPA2 handshakes.

Common tool:

```bash
aireplay-ng
```

---

# 8. Troubleshooting Guide

If packet capture or decryption fails, verify the following:

## Checklist

1. Is the adapter in Monitor Mode?

```bash
iwconfig
```

2. Does the adapter channel match the target AP channel?

3. Is **Enable Decryption** enabled in Wireshark?

4. Was the complete 4-Way Handshake captured?

5. Is the USB adapter connected directly to the VM?

---

# 9. Passive vs Active Sniffing

Understanding the distinction between passive and active sniffing is critical for both attackers and defenders.

---

## Wireshark (Passive Sniffing)

### Characteristics

- Operates outside the network.
- No need to join the Wi-Fi network.
- Simply listens to wireless traffic.

### Advantages

- Highly stealthy.
- Difficult to detect.
- Generates no additional network traffic.

### Detection Difficulty

Very difficult for defenders to detect.

---

## Bettercap (Active Sniffing)

### Characteristics

- Requires joining the target network.
- Often uses ARP spoofing or MITM techniques.

### Example Techniques

- ARP Spoofing
- DNS Spoofing
- HTTPS Proxying

### Advantages

- Allows interception and manipulation of traffic.

### Disadvantages

- Generates numerous forged packets.
- Easier for IDS/IPS and SOC teams to detect.

---

# 10. Key Differences: Passive vs Active Sniffing

| Feature | Passive Sniffing (Wireshark) | Active Sniffing (Bettercap) |
|----------|-----------------------------|-----------------------------|
| Network Access Required | No | Yes |
| Sends Packets | No | Yes |
| Stealth Level | High | Low |
| Detection Probability | Very Low | High |
| Traffic Manipulation | No | Yes |
| Typical Technique | Monitor Mode Capture | ARP Spoofing / MITM |
| Example Tool | Wireshark | Bettercap |

---

# 11. Important Wireshark Filters

| Filter | Purpose |
|---------|---------|
| `eapol` | Display WPA/WPA2 handshakes |
| `dns` | Show DNS packets |
| `http` | Show HTTP traffic |
| `tcp` | Show TCP packets |
| `udp` | Show UDP packets |
| `ip.addr == x.x.x.x` | Filter by IP address |
| `wlan.fc.type_subtype == 0x0c` | Show Deauthentication frames |
| `tls` | Show TLS traffic |
| `quic` | Display QUIC traffic |

---

# 12. Common Wireless Tools Used Alongside Wireshark

| Tool | Purpose |
|-------|---------|
| Wireshark | Packet capture and analysis |
| Airodump-ng | Wireless discovery and packet capture |
| Aireplay-ng | Packet injection and deauthentication |
| Bettercap | MITM and active network attacks |
| Aircrack-ng | WPA/WPA2 password cracking |
| Kismet | Wireless IDS and monitoring |

---

# 13. Security Considerations

- Capturing wireless traffic without authorization may violate laws and organizational policies.
- Always obtain explicit permission before conducting packet captures.
- Use isolated lab environments such as:
  - Home Lab
  - TryHackMe
  - Hack The Box
  - Authorized Security Assessments

---

# 14. Key Takeaways

- Monitor Mode is essential for wireless packet capture.
- Channel locking significantly improves capture quality.
- WPA/WPA2 decryption requires both the password and a valid 4-Way Handshake.
- Wireshark performs passive analysis and is highly stealthy.
- Bettercap performs active interception and is easier to detect.
- SOC analysts rely heavily on packet analysis for threat detection and incident response.
- Proper troubleshooting greatly improves successful wireless analysis.
