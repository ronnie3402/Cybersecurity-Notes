# OSI Layer 4 - Transport Layer

## Overview

The Transport Layer is the fourth layer of the OSI model. It provides end-to-end communication between applications running on different devices.

This layer ensures that data reaches the correct application by using port numbers. It is also responsible for reliability, segmentation, flow control, and error recovery.

---

## Responsibilities

The Transport Layer is responsible for:

- Providing end-to-end communication.
- Segmenting large amounts of data into smaller units.
- Reassembling received segments.
- Performing flow control.
- Providing error detection and recovery.
- Delivering data to the appropriate application using port numbers.

---

## Data Unit

| Layer | PDU |
| :--- | :--- |
| **Transport Layer** | **Segment** |

The upper layers pass data to the Transport Layer, which encapsulates it by adding a header containing source and destination port numbers.

---

## Layer 4 Devices

| Device | Role |
| :--- | :--- |
| **Firewall** | Filters traffic based on port numbers, protocols, and security policies. |
| **Load Balancer** | Distributes application traffic across multiple servers to improve performance and availability. |

---

## Transport Layer Protocols

### TCP (Transmission Control Protocol)

TCP is a connection-oriented and reliable protocol.

**Key Features:**

- Connection-oriented communication
- Error detection and recovery
- Sequence numbering
- Acknowledgments (ACK)
- Retransmission of lost segments
- Flow control

**Common Applications:**

- HTTP / HTTPS
- FTP
- SSH
- SMTP

---

### UDP (User Datagram Protocol)

UDP is a connectionless and lightweight protocol designed for speed.

**Key Features:**

- No connection establishment
- No acknowledgments
- Lower overhead
- Faster transmission

**Common Applications:**

- DNS
- VoIP
- Video Streaming
- Online Gaming

---

## Port Numbers

Port numbers identify specific applications or services running on a device.

| Service | Default Port |
| :--- | :--- |
| HTTP | 80 |
| HTTPS | 443 |
| SSH | 22 |
| DNS | 53 |
| FTP | 21 |

Ports act as logical communication endpoints for applications.

---

## Key Concepts

### TCP Three-Way Handshake

TCP establishes a connection using a three-step process:

1. **SYN** → The client requests a connection.
2. **SYN-ACK** → The server acknowledges the request and responds.
3. **ACK** → The client acknowledges the server's response.

Once this process is complete, the connection is established.

---

### Flow Control and Windowing

Flow control prevents a sender from overwhelming the receiver.

TCP uses a windowing mechanism to adjust the amount of data transmitted based on the receiver's capacity.

---

### Socket

A socket uniquely identifies a communication session.

A socket consists of:

```text
IP Address + Port Number
```

Example:

```text
192.168.1.10:443
```

Networking applications use sockets to establish communication.

---

## Example: Application Communication Flow

Consider an IP Threat Intelligence application querying a remote API.

1. The application generates a request.
2. The Transport Layer assigns source and destination port numbers.
3. The request is sent to the remote server using TCP or UDP.
4. If the server does not respond, the connection may eventually time out.

---

## Troubleshooting and Lab Commands

### View Active Connections (Windows)

```bash
netstat -ano
```

Displays:

- Active connections
- Listening ports
- Associated process IDs

---

### View Active Connections (Linux)

```bash
ss -tulnp
```

Shows active TCP and UDP sockets.

---

### Wireshark Analysis

Useful fields to examine:

- TCP Flags (`SYN`, `ACK`, `FIN`, `RST`)
- Source and Destination Ports
- Sequence Numbers
- Retransmissions

**Example:**

Repeated `SYN` packets without receiving a response may indicate:

- A blocked port
- A closed port
- Firewall filtering

---

## Exam and Interview Notes

### Q: Does the Transport Layer contain IP addresses?

**Answer:** No. The Transport Layer header contains port numbers. IP addresses belong to the Network Layer.

---

### Q: Which protocol has higher overhead: TCP or UDP?

**Answer:** TCP, because it uses acknowledgments, sequence numbers, retransmissions, and additional control mechanisms.

---

### Q: Which protocol is faster: TCP or UDP?

**Answer:** UDP is generally faster because it does not establish connections or provide reliability mechanisms.

---

### Q: What is the primary purpose of port numbers?

**Answer:** To deliver data to the correct application or process.

---

## TCP vs UDP

| TCP | UDP |
| :--- | :--- |
| Connection-oriented | Connectionless |
| Reliable | Unreliable |
| Uses acknowledgments | No acknowledgments |
| Higher overhead | Lower overhead |
| Slower | Faster |

---

## Key Takeaways

- The Transport Layer provides end-to-end communication between applications.
- Port numbers identify applications and services.
- TCP provides reliable communication.
- UDP prioritizes speed over reliability.
- Firewalls commonly filter traffic based on ports and protocols.
- Sockets are fundamental to network communication.
