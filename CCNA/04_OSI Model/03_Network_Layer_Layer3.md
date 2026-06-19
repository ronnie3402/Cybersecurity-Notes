# Network Layer (OSI Layer 3)

## Overview
The Network Layer is responsible for providing logical addressing and enabling communication between different networks. Its primary function is to determine the best path (routing) for data and ensure packets are delivered from the source network to the destination network.





## Responsibilities
* **Logical Addressing:** Assigning and managing IP addresses.
* **Best Path Selection:** Determining the most efficient route for packet delivery.
* **Routing:** Forwarding packets across different networks.
* **Fragmentation:** Breaking down packets if they exceed the Maximum Transmission Unit (MTU) of the network.

## Data Unit
| Layer | PDU |
| :--- | :--- |
| **Network Layer** | **Packet** |

*The Transport Layer passes segments to the Network Layer. The Network Layer encapsulates them by adding an IP header, creating a packet.*

---

## Layer 3 Devices

| Device | Role |
| :--- | :--- |
| **Router** | Forwards packets between different networks using routing tables. |
| **Layer 3 Switch** | Performs high-speed switching and routing by examining both MAC and IP addresses. |

---

## Protocols
* **Addressing:** IPv4 and IPv6.
* **Troubleshooting (ICMP):** Used for diagnostics (`ping`, `traceroute`).
* **Address Mapping (ARP):** Maps IPv4 addresses to MAC addresses for local delivery. *(Note: Works between Layer 2 and Layer 3).*
* **Routing Protocols:** Used to exchange network info and determine paths (e.g., OSPF, EIGRP, BGP, RIP).

---

## Key Concepts

### Routing
The process of selecting the best path for packet delivery between distinct networks.

### Fragmentation
If a packet exceeds the MTU of a link, it is divided into smaller pieces (fragments) to fit the network requirements.

### Time To Live (TTL)
* Prevents packets from circulating indefinitely (loops).
* Each router decreases TTL by 1.
* When TTL reaches 0, the packet is discarded, and an ICMP "Time Exceeded" message is sent back to the sender.

---

## Example: Packet Delivery
1. Application generates a request.
2. Network Layer adds Source and Destination IP addresses (Packet).
3. If destination is outside the local network, packet is forwarded to the **Default Gateway** (Router).
4. Routers forward the packet across hops until it reaches the destination.



---

## Technical Comparisons

### IP Address vs. MAC Address
| Feature | IP Address | MAC Address |
| :--- | :--- | :--- |
| **Type** | Logical | Physical |
| **Layer** | Layer 3 | Layer 2 |
| **Purpose** | Communication between networks | Communication within a LAN |
| **Stability** | Changeable | Usually fixed |

### Router vs. Switch
| Feature | Router | Switch |
| :--- | :--- | :--- |
| **Layer** | Layer 3 | Layer 2 |
| **Logic** | Uses IP addresses | Uses MAC addresses |
| **Scope** | Connects different networks | Connects devices within the same network |
| **Boundary** | Separates broadcast domains | Creates collision domains |

---

## Troubleshooting & Verification
* **Check Connectivity:** `ping <destination_ip>`
* **Trace Path (Windows):** `tracert <destination_ip>`
* **Trace Path (Linux):** `traceroute <destination_ip>`
* **View Routing Table (Windows):** `route print`
* **View Routing Table (Linux):** `ip route`

---

## Exam and Interview Notes

* **Q: Which OSI layer is responsible for routing?**
  * **A:** Layer 3 (Network Layer).
* **Q: What happens when TTL reaches zero?**
  * **A:** Router discards packet and sends ICMP "Time Exceeded" message.
* **Q: Why is a router required for communication between VLANs?**
  * **A:** Different VLANs are separate logical networks; Layer 3 routing is needed to cross boundaries.

---

## Public vs. Private IPs
* **Private IP Ranges (Non-routable on Internet):** * `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`.
* **Public IP Addresses:** Globally unique and routable over the Internet.
