# IoT and OT Exploitation

## Module 18: Introduction to IoT Pentesting

---

# What is IoT?

**Internet of Things (IoT)** refers to a network of physical devices that are connected to the Internet and can collect, exchange, and process data automatically.

These devices contain:

- Sensors
- Software
- Network connectivity
- Embedded systems

IoT devices communicate with other devices, cloud services, and users without requiring constant human interaction.

## Common Examples

- Smart TVs
- Smart Cameras
- Smart Locks
- Smart Bulbs
- Smart Thermostats
- Fitness Bands
- Smart Refrigerators
- Wearable Devices

---

# What is OT?

**Operational Technology (OT)** refers to hardware and software systems used to monitor and control industrial processes.

Unlike traditional IT systems, OT directly interacts with physical equipment.

## Common OT Environments

- Manufacturing Plants
- Power Generation Facilities
- Water Treatment Plants
- Oil and Gas Industries
- Transportation Systems
- Building Automation Systems

---

# Difference Between IT and OT

| Feature | IT | OT |
|----------|----|----|
| Primary Goal | Data Processing | Physical Process Control |
| Security Priority | Confidentiality | Availability & Safety |
| Examples | PCs, Servers | PLCs, SCADA Systems |
| Downtime Impact | Business Disruption | Physical Damage |
| Patch Frequency | Regular | Rare |

---

# Important IoT Protocols

IoT devices use specialized communication protocols.

| Protocol | Port | Usage |
|-----------|------|-------|
| MQTT | 1883/8883 | Lightweight messaging |
| CoAP | 5683 | Device communication |
| HTTP/HTTPS | 80/443 | Web communication |
| Zigbee | N/A | Low-power wireless communication |
| Bluetooth LE | N/A | Short-range communication |
| AMQP | 5672 | Message queuing |
| Modbus | 502 | Industrial communication |

---

# Important OT Protocols

Industrial systems use specific protocols.

| Protocol | Port | Purpose |
|-----------|------|---------|
| Modbus TCP | 502 | Industrial communication |
| DNP3 | 20000 | Power systems |
| BACnet | 47808 | Building automation |
| OPC UA | 4840 | Industrial interoperability |
| Profinet | Various | Industrial Ethernet |
| EtherNet/IP | 44818 | PLC communication |

---

# Common OT Components

## PLC (Programmable Logic Controller)

Industrial computer responsible for controlling physical processes.

Examples:

- Opening valves
- Controlling motors
- Monitoring sensors

---

## SCADA (Supervisory Control and Data Acquisition)

Centralized system used to monitor and control industrial environments.

Functions:

- Data collection
- Monitoring
- Alarm generation
- Remote control

---

## HMI (Human Machine Interface)

Provides graphical interfaces for operators to interact with industrial processes.

Examples:

- Control room dashboards
- Touchscreen industrial panels

---

## RTU (Remote Terminal Unit)

Field devices responsible for collecting sensor data and communicating with control centers.

---

# Typical IoT Attack Surface

Attackers commonly target:

## Web Interfaces

Examples:

- Default credentials
- Command Injection
- Authentication bypass

---

## Mobile Applications

Examples:

- Hardcoded API keys
- Weak authentication
- Insecure APIs

---

## Cloud Backends

Examples:

- Exposed databases
- Broken access control
- Insecure APIs

---

## Firmware

Examples:

- Hardcoded passwords
- Hidden accounts
- Vulnerable libraries

---

## Hardware Interfaces

Examples:

- UART
- JTAG
- SPI
- I2C

---

# Firmware Analysis Process

Firmware analysis is a critical phase in IoT pentesting.

## Steps

### 1. Obtain Firmware

Sources:

- Vendor websites
- Device extraction
- OTA updates

### 2. Extract Firmware

Common tools:

- Binwalk
- Firmware Mod Kit

Example:

```bash
binwalk -e firmware.bin
```

### 3. Analyze File System

Look for:

- Passwords
- API Keys
- Certificates
- Scripts
- Configuration Files

### 4. Reverse Engineer Binaries

Common tools:

- Ghidra
- IDA Pro
- Radare2

---

# IoT Pentesting Tools

| Tool | Purpose |
|-------|---------|
| Nmap | Discovery & Enumeration |
| Wireshark | Traffic Analysis |
| Binwalk | Firmware Extraction |
| Firmware Mod Kit | Firmware Analysis |
| Burp Suite | Web/API Testing |
| Hydra | Credential Attacks |
| Ghidra | Reverse Engineering |
| Shodan | Internet-wide Device Discovery |
| Metasploit | Exploitation |
| RouterSploit | Router Exploitation |

---

# IoT Reconnaissance Checklist

- Identify devices.
- Enumerate open ports.
- Identify services.
- Determine protocols.
- Fingerprint operating systems.
- Identify firmware versions.
- Enumerate web interfaces.
- Capture network traffic.

---

# OT Security Principles

Industrial systems prioritize:

1. Safety
2. Availability
3. Reliability
4. Integrity
5. Confidentiality

> In OT environments, availability is usually more important than confidentiality.

---

# Network Segmentation in OT

One of the most important defensive controls.

## Why?

Segmentation prevents attackers from moving laterally between IT and OT networks.

Example Architecture:

```text
Internet
   |
Firewall
   |
Corporate IT Network
   |
DMZ
   |
Industrial Firewall
   |
OT Network
   |
PLC / SCADA / HMI
```

---

# Common IoT and OT Security Best Practices

## Device Security

- Change default credentials.
- Disable unnecessary services.
- Keep firmware updated.
- Use secure boot.

## Network Security

- Use VLAN segmentation.
- Restrict Internet access.
- Deploy firewalls.
- Monitor network traffic.

## Access Control

- Use MFA.
- Enforce least privilege.
- Audit privileged accounts.

## Monitoring

- Enable logging.
- Deploy IDS/IPS.
- Continuously monitor assets.

---

# Real-World Attacks

## Mirai Botnet (2016)

Attackers compromised thousands of IoT devices using default credentials.

Impact:

- Massive DDoS attacks.
- Major Internet outages.

---

## Stuxnet (2010)

Sophisticated malware targeting industrial control systems.

Target:

- Iranian nuclear facilities.

Significance:

- First malware specifically designed to damage physical infrastructure.

---

# Key Takeaways

- Most IoT attacks exploit weak configurations rather than advanced vulnerabilities.
- Default credentials remain one of the biggest risks.
- Firmware analysis is a crucial IoT pentesting skill.
- OT environments prioritize availability and safety.
- Network segmentation is essential in industrial environments.
- Security testing in OT environments should always follow safety-first principles.
