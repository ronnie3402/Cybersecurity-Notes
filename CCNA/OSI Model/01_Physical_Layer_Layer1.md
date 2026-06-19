# OSI Layer 1 - Physical Layer

## Overview
The Physical Layer is the first and lowest layer of the OSI model. It is responsible for transmitting raw bits (0s and 1s) over a physical medium such as copper cables, fiber optic cables, or wireless signals. Unlike higher layers, the Physical Layer does not interpret or process data. Its only responsibility is to move bits from one device to another.




## Responsibilities
The Physical Layer is responsible for:
* Defining hardware specifications.
* Defining cable types and connectors.
* Converting bits into electrical, optical, or radio signals.
* Establishing and terminating physical connections.
* Managing transmission rates and signal timing.

## Data Unit
| Layer | PDU |
| :--- | :--- |
| **Physical Layer** | **Bits** |

*The Data Link Layer passes frames to the Physical Layer. The Physical Layer converts those frames into signals for transmission.*

---

## Physical Layer Devices

| Device | Role |
| :--- | :--- |
| **Cables/Connectors** | Ethernet (CAT5e, CAT6, CAT6A), Fiber Optic, RJ-45, Console cables. |
| **Hub** | Repeats incoming electrical signals to all connected ports. Operates at Layer 1; does not examine MAC/IP addresses. |
| **Repeater** | Regenerates weak signals to extend transmission distance. |
| **NIC** | Hardware converting digital bits into physical signals (and vice versa). |

---

## Standards and Protocols

### Ethernet Standards
* `10BASE-T`
* `100BASE-TX`
* `1000BASE-T`
*(These standards define speed, cabling requirements, and maximum transmission distances.)*

### Wireless Standards
* **IEEE 802.11 (Wi-Fi):** Defines wireless communication frequencies and signaling methods.

### Interface Standards
* USB, Bluetooth, RS-232.

---

## Network Topologies & Transmission
Common physical topologies include: **Star, Bus, Ring, Mesh.**

### Transmission Modes
| Mode | Description | Example |
| :--- | :--- | :--- |
| **Simplex** | One-way communication | Radio broadcasting |
| **Half-Duplex** | Two-way, but alternating (one at a time) | Walkie-talkies |
| **Full-Duplex** | Simultaneous two-way communication | Modern Ethernet |

### Encoding and Modulation
The Physical Layer converts digital bits into signals suitable for transmission over a medium. Examples include:
* Electrical pulses (Copper)
* Light pulses (Fiber)
* Radio waves (Wireless)

---

## Example: Data Transmission Flow
When a user presses **Enter** in a web browser:
1. Data reaches the Network Interface Card (NIC).
2. The NIC converts bits into electrical or optical signals.
3. Signals travel through the transmission medium.
4. The receiving device converts the signals back into bits.

---

## Troubleshooting Layer 1 Issues
**Common Issues:** Damaged cables, loose connectors, faulty NICs, signal attenuation, or incorrect cable types.

**Quick Checks:**
* Verify link LEDs.
* Replace suspected cables.
* Check NIC status.
* Verify interface status.

**Cisco Command:** `show interfaces`
*(Displays physical interface status and errors)*

**Wireshark Note:** Wireshark does not display Physical Layer headers because it captures data starting from Layer 2. However, interface information and frame lengths can still be observed.

---

## Exam and Interview Notes

* **Q: Which OSI layer is affected if an Ethernet cable exceeds its maximum supported length?**
  * **A:** Layer 1 (Signal attenuation).
* **Q: Why is a hub considered a Layer 1 device?**
  * **A:** It only repeats electrical signals and does not process MAC or IP addresses.

### Physical vs. Data Link
| Physical Layer | Data Link Layer |
| :--- | :--- |
| Deals with signals, cables, and connectors | Deals with frames and MAC addresses |
| Transfers bits | Transfers frames |

---

## Additional Note: IPv6 "No Internet Access"
Sometimes Windows displays "No Internet Access" for IPv6 even though internet connectivity is working normally.

### Common Reasons
* **ISP Support:** Many ISPs still provide internet connectivity only through IPv4.
* **Router Configuration:** IPv6 may be disabled on the router, or the router may not receive IPv6 connectivity from the ISP.
* **IPv4 Dominance:** Most websites continue to work over IPv4, so users may not notice any issue.

### Connectivity Breakdown
* **Local Connectivity:** Devices can communicate within the local network using Link-Local IPv6 addresses (`fe80::/10`).
* **Global Connectivity:** Internet access requires a globally routable IPv6 address (starting with `2001:` or `2405:`) and a valid gateway from the ISP.

**Verification:**
Run `ipconfig` in Command Prompt.
* If only an address beginning with `fe80:` is present, IPv6 communication is limited to the local network.
* If an address beginning with `2001:` or `2405:` is present, global IPv6 connectivity is likely available.
