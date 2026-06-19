# Data Link Layer (OSI Layer 2)

## Overview
The Data Link Layer provides reliable communication between directly connected devices on the same network segment. It is responsible for framing, physical addressing (MAC), media access control, and error detection.




## Responsibilities
* **Framing:** Encapsulating packets from the Network Layer into frames.
* **Physical Addressing:** Using MAC addresses to identify source and destination devices.
* **Media Access Control:** Managing when and how devices transmit data on the shared medium.
* **Error Detection:** Ensuring data integrity using a trailer (FCS/CRC).

## Data Unit
| Layer | PDU |
| :--- | :--- |
| **Data Link Layer** | **Frame** |

*A frame consists of a **Header** (Source/Dest MAC addresses) and a **Trailer** (FCS for error detection).*

---

## Layer 2 Devices

| Device | Role |
| :--- | :--- |
| **Switch** | Maintains a MAC address table to forward frames to the correct port. |
| **Bridge** | Connects multiple LAN segments and filters traffic based on MAC addresses. |
| **NIC** | Hardware containing the burned-in MAC address; determines whether to accept/discard frames. |

---

## Ethernet and Key Protocols
* **Ethernet (IEEE 802.3):** Defines the frame format and rules for wired networks.
* **VLAN (Virtual LAN):** Logically divides a switch into multiple networks. Improves security, reduces broadcast traffic, and separates user groups.
* **STP (Spanning Tree Protocol):** Prevents switching loops by blocking redundant paths.
* **IEEE 802.1Q (Dot1Q):** Standard for tagging frames across trunk links to identify VLAN membership.
* **ARP (Address Resolution Protocol):** Maps IPv4 addresses to MAC addresses for local delivery.

---

## Collision vs. Broadcast Domains

### Collision Domain
* A segment where data collisions can occur.
* Each switch port is a separate collision domain. Modern full-duplex switches effectively eliminate collisions.

### Broadcast Domain
* The boundary within which broadcast traffic is forwarded.
* Switches forward broadcasts; Routers do not.
* Each VLAN is a separate broadcast domain.

---

## Switching Methods

| Method | Mechanism | Trade-off |
| :--- | :--- | :--- |
| **Store-and-Forward** | Receives full frame, checks FCS (errors), then forwards. | High reliability / Slower. |
| **Cut-Through** | Forwards frame as soon as destination MAC is read. | Very fast / Risk of forwarding corrupted frames. |
| **Fragment-Free** | Checks the first 64 bytes before forwarding. | Balance of speed and reliability. |

---

## Operational Examples

### Frame Forwarding Flow
1. Computer creates a frame with destination Printer's MAC address.
2. Switch consults its **MAC Address Table**.
3. Frame is forwarded exclusively to the port connected to the Printer.

### Inter-VLAN Communication
Devices in different VLANs cannot communicate directly. This requires a Layer 3 device (Router or Multilayer Switch) to route traffic between VLANs.

---

## Troubleshooting & Lab Commands

* **View Local MAC Address:** `getmac`
* **View ARP Cache:** `arp -a` (IP-to-MAC mappings).
* **View MAC Table (Cisco):** `show mac address-table`
* **Network Details:** `ipconfig /all`

---

## Exam and Interview Notes

* **Q: Does a switch forward traffic based on IP?**
  * **A:** No. Traditional L2 switches use MAC addresses.
* **Q: Why create VLANs?**
  * **A:** To create separate broadcast domains and isolate traffic.
* **Q: What is a broadcast storm?**
  * **A:** Result of a switching loop; excessive traffic that can crash the network.

### Data Link vs. Network Layer
| Data Link Layer (L2) | Network Layer (L3) |
| :--- | :--- |
| Uses MAC addresses | Uses IP addresses |
| Local delivery (LAN) | End-to-end delivery (WAN) |
| Transfers frames | Transfers packets |
