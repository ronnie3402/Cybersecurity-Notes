# TCP/IP Model

## Overview

The TCP/IP (Transmission Control Protocol/Internet Protocol) model is the practical networking model used by the modern Internet. Unlike the OSI model, which was designed as a theoretical framework, the TCP/IP model was built around existing protocols and real-world networking requirements.

Today, nearly all network communication on the Internet operates using the TCP/IP protocol suite.

---

## OSI vs TCP/IP: Theory vs Practical Implementation

The OSI model was created as a conceptual framework to standardize network communication. Protocols were developed later to fit within its seven-layer architecture.

In contrast, the TCP/IP model was developed by studying how networking protocols actually worked in practice. As a result, it is simpler and more closely aligned with real-world implementations.

### Key Idea

- **OSI Model:** Primarily used for learning and troubleshooting.
- **TCP/IP Model:** Used in real-world networking and Internet communication.

---

# Layer Mapping: OSI vs TCP/IP

| OSI Model | TCP/IP Model | Notes |
| :--- | :--- | :--- |
| Application | Application | Combined into a single layer |
| Presentation | Application | Combined into a single layer |
| Session | Application | Combined into a single layer |
| Transport | Transport | Functions remain unchanged |
| Network | Internet | Responsible for routing and logical addressing |
| Data Link | Network Access | Handles local delivery and framing |
| Physical | Network Access | Handles transmission of bits and signals |

> **Note:** Some textbooks combine the Data Link and Physical layers into a single **Network Interface Layer**.

---

# TCP/IP Layers

## 1. Application Layer

### Responsibilities

- Provides an interface between applications and the network.
- Handles application-level protocols and services.
- Performs functions such as data formatting, session management, encryption, and compression through software implementations.

### Common Protocols

- HTTP
- HTTPS
- FTP
- DNS
- SSH
- SMTP
- SNMP

### Examples

- Web Browsing
- Email Services
- Remote Administration
- File Transfer

---

## 2. Transport Layer

### Responsibilities

- Provides end-to-end communication.
- Ensures reliable or fast delivery depending on application requirements.
- Performs segmentation and reassembly.
- Uses port numbers to identify applications.

### Protocols

#### TCP (Transmission Control Protocol)

- Connection-oriented
- Reliable communication
- Error detection and retransmission
- Ordered delivery

#### UDP (User Datagram Protocol)

- Connectionless
- Faster communication
- No delivery guarantee
- Lower overhead

### Common Port Examples

| Service | Port |
| :--- | :--- |
| HTTP | 80 |
| HTTPS | 443 |
| SSH | 22 |
| DNS | 53 |

---

## 3. Internet Layer

### Responsibilities

- Provides logical addressing.
- Determines the best path for packet delivery.
- Routes packets between networks.

### Common Protocols

- IPv4
- IPv6
- ICMP
- ARP

### Primary Device

**Router**

Routers operate at this layer and forward packets between different networks.

---

## 4. Network Access Layer

The Network Access Layer combines the responsibilities of the OSI Data Link and Physical layers.

### Responsibilities

- Frame creation and processing.
- Physical transmission of bits.
- MAC addressing.
- Media access control.
- Communication over physical media.

### Common Technologies

- Ethernet (IEEE 802.3)
- Wi-Fi (IEEE 802.11)

### Devices

- Switches
- NICs
- Hubs
- Cables

---

# Key Differences Between OSI and TCP/IP

| Feature | OSI Model | TCP/IP Model |
| :--- | :--- | :--- |
| Number of Layers | 7 | 4 or 5 |
| Nature | Theoretical | Practical |
| Development Approach | Model first, protocols later | Protocols first, model later |
| Real-world Usage | Primarily educational | Used across the Internet |

---

# Cybersecurity Perspective

Understanding the TCP/IP model is essential for cybersecurity professionals because attacks often target specific layers.

| Attack | Target Layer |
| :--- | :--- |
| IP Spoofing | Internet Layer |
| SYN Flood | Transport Layer |
| SQL Injection | Application Layer |
| DNS Poisoning | Application Layer |
| ARP Spoofing | Network Access Layer |

---

# Practical and Lab Examples

## Packet Capture

When capturing traffic in Wireshark, packets are typically displayed according to the TCP/IP stack:

```text
Ethernet → IP → TCP/UDP → HTTP/DNS
```

---

## ICMP Testing

```bash
ping google.com
```

The `ping` command uses the **ICMP protocol** at the Internet Layer.

---

## Viewing Active Connections

```bash
netstat -ano
```

Displays active TCP and UDP connections along with associated processes.

---

# Exam and Interview Notes

### Q: Which model is used by the Internet?

**Answer:** TCP/IP Model.

---

### Q: Which TCP/IP layer is responsible for routing?

**Answer:** Internet Layer.

---

### Q: Which TCP/IP layer corresponds to OSI Layer 3?

**Answer:** Internet Layer.

---

### Q: Why is the TCP/IP model considered more practical than the OSI model?

**Answer:** Because it was built around real-world networking protocols and is widely implemented on the Internet.

---

# Key Takeaways

- The TCP/IP model forms the foundation of modern networking.
- Most Internet communication uses the TCP/IP protocol suite.
- The model consists of four or five layers depending on implementation.
- The Application layer combines the OSI Application, Presentation, and Session layers.
- Understanding TCP/IP is essential for networking and cybersecurity professionals.
