# 🌐 IP Addressing Fundamentals: Network Identification & Routing

> *These notes outline the mechanics of IP addressing, from the fundamentals of IPv4 and IPv6 to complex subnetting and NAT strategies. This baseline is essential for understanding how traffic is routed and is a foundational requirement for any security analysis or network troubleshooting.*

## 📌 What is an IP Address?
An **IP (Internet Protocol) Address** is a unique logical identifier assigned to every device connected to a computer network that uses the Internet Protocol for communication. 
* **Layer:** It operates at the **Network Layer (Layer 3)** of the OSI model.
* **Function:** It serves two main purposes: **Host/Network interface identification** (who it is) and **Location addressing** (where it is).

---

## 🔢 1. IPv4 vs. IPv6 (The Two Generations)

Because the internet grew massively, the original IP system (IPv4) ran out of unique addresses. This led to the creation of IPv6.

| Feature | IPv4 (Internet Protocol version 4) | IPv6 (Internet Protocol version 6) |
| :--- | :--- | :--- |
| **Address Length** | 32-bit | 128-bit |
| **Format** | Numeric, written in Decimal | Alphanumeric, written in Hexadecimal |
| **Notation** | Dotted-decimal (e.g., `192.168.1.1`) | Colon-separated (e.g., `2001:0db8:85a3::8a2e:0370:7334`) |
| **Address Space** | ~4.3 Billion unique addresses | ~340 Undecillion (Virtually unlimited) |
| **Security (IPsec)**| Optional | Built-in by default |
| **Configuration** | Manual or via DHCP | Supports Auto-configuration (SLAAC) |

---

## 🏗️ 2. Structure of an IPv4 Address

An IPv4 address consists of 32 bits, divided into 4 sections called **Octets** (each containing 8 bits). 
* Example: `192.168.10.50`
* Each octet can have a decimal value ranging from **0 to 255**.

Every IP address is split into two distinct parts:
1. **Network Portion (Network ID):** Identifies the specific network the device belongs to. (Like the street name).
2. **Host Portion (Host ID):** Identifies the specific device within that network. (Like the house number).

*The **Subnet Mask** is what determines which part of the IP is the Network ID and which part is the Host ID.*

### 💡 Practical Example: Understanding Network vs. Host
To understand how the **Subnet Mask** acts as a filter to divide the IP address, let's look at a common home network configuration:

* **IP Address:** `192.168.10.50`
* **Subnet Mask:** `255.255.255.0`



#### Breakdown:
| Portion | Value | Role | Analogy |
| :--- | :--- | :--- | :--- |
| **Network ID** | `192.168.10` | Identifies the specific network segment. | The **Street Name** |
| **Host ID** | `50` | Identifies the specific device on that network. | The **House Number** |

#### Why the Subnet Mask matters (Binary View):
The computer reads the Subnet Mask in binary to know where to draw the line between the Network and the Host portions.

* **Subnet Mask (Decimal):** `255.255.255.0`
* **Subnet Mask (Binary):** `11111111.11111111.11111111.00000000`

> **Key Rule:** Where the mask has **1s**, that part of the IP is the **Network ID**. Where the mask has **0s**, that part is the **Host ID**. 

Because our mask has 24 ones, this is often written in shorthand as **/24** (also called CIDR notation). This tells us that any device starting with `192.168.10` is on the same local "street" and can communicate directly without needing a router.

---

## 🏛️ 3. IPv4 Address Classes

To manage networks of different sizes, IPv4 addresses were originally divided into 5 classes. The class is determined by the value of the **first octet**.

### Class A (Large Networks)
Used for massive enterprise or government networks with millions of devices.
* **Range:** `1.0.0.0` to `126.255.255.255`
* **Default Subnet Mask:** `255.0.0.0` (Network.Host.Host.Host)
* **Hosts per Network:** ~16.7 Million

### Class B (Medium Networks)
Used for mid-sized organizations, universities, and large enterprise branches.
* **Range:** `128.0.0.0` to `191.255.255.255`
* **Default Subnet Mask:** `255.255.0.0` (Network.Network.Host.Host)
* **Hosts per Network:** 65,534

### Class C (Small Networks)
Used for small businesses and home networks (LANs).
* **Range:** `192.0.0.0` to `223.255.255.255`
* **Default Subnet Mask:** `255.255.255.0` (Network.Network.Network.Host)
* **Hosts per Network:** 254

### Class D (Multicast)
Used for broadcasting data to a specific group of devices at once (e.g., streaming, routing protocols).
* **Range:** `224.0.0.0` to `239.255.255.255`
* *Note: Not assigned to individual hosts. No subnet mask.*

### Class E (Experimental)
Reserved for research and development purposes.
* **Range:** `240.0.0.0` to `255.255.255.255`

---

## 🌍 4. Public vs. Private IP Addresses

Not every IP address is allowed to route traffic directly over the public internet. 

### 🏠 Private IP Addresses (RFC 1918)
These addresses are free to use by anyone within a local network (LAN) but **cannot be routed on the internet**. To access the internet, they must be translated into a Public IP using **NAT (Network Address Translation)** on the router.

| Class | Private IP Range | Typical Use Case |
| :--- | :--- | :--- |
| **A** | `10.0.0.0` – `10.255.255.255` | Large corporate networks, Cloud VPCs |
| **B** | `172.16.0.0` – `172.31.255.255` | Medium corporate networks, Docker containers |
| **C** | `192.168.0.0` – `192.168.255.255` | Home Wi-Fi networks, small office routers |

### 🌐 Public IP Addresses
These are globally unique addresses assigned by an ISP (Internet Service Provider) and controlled by IANA. They face the public internet directly.
* **Examples:** Web servers, external firewall interfaces, public APIs.

---

## 🚨 5. Special IP Addresses (Reserved)

Certain IP addresses are reserved for specific networking functions and cannot be assigned to standard devices:

* **Loopback Address (`127.0.0.1` to `127.255.255.255`):** Used to test the local machine's own network interface (localhost). If you can ping `127.0.0.1`, your TCP/IP stack is working correctly.
* **APIPA (`169.254.0.0` - `169.254.255.255`):** Automatic Private IP Addressing. If a device is configured to get an IP dynamically but the DHCP server is down, Windows automatically assigns an IP in this range. *(SOC Tip: If a device has an APIPA, it cannot reach the internet).*
* **Network ID (First IP in a subnet):** Represents the entire network itself. (e.g., `192.168.1.0`).
* **Broadcast ID (Last IP in a subnet):** Used to send a message to *all* hosts on that specific network simultaneously. (e.g., `192.168.1.255`).
