# Network Service Enumeration Notes (Module 04-Part 1)

## Introduction to Enumeration

Enumeration is the phase of a penetration test where detailed information is extracted from network services. The goal is to understand the attack surface, identify misconfigurations, and discover information that can lead to further access.



# SMB & NetBIOS (Ports 139, 445)

## Overview

- **NetBIOS (Network Basic Input/Output System)** provides naming and session services in Windows environments.
- **SMB (Server Message Block)** is the primary protocol used for file and printer sharing.

## Enumeration Goals

- Discover shared folders and printers.
- Enumerate users and their RIDs (Relative Identifiers).
- Identify security misconfigurations such as anonymous access.

## Common Tools

### Nmap

```bash
nmap -p 139,445 --script smb-enum-shares,smb-enum-users <IP>
```

### smbclient

```bash
smbclient -L //<IP> -N
```

`-N` attempts an anonymous login.

### enum4linux

```bash
enum4linux -a <IP>
```

Provides information about:

- Users
- Shares
- Operating System
- SID information

## Important Status Codes

| Status Code | Meaning |
|-------------|----------|
| `NT_STATUS_ACCESS_DENIED` | Permission denied. |
| `NT_STATUS_OBJECT_NAME_NOT_FOUND` | File or directory does not exist. |

## Accessing SMB Shares

**Syntax**

```bash
smbclient //[IP]/[SHARE] -U [USERNAME] -p [PORT]
```

**Example**

```bash
smbclient //10.10.10.10/secrets -U Anonymous -p 445
```

## Common SMB Attribute Codes

| Code | Meaning |
|------|----------|
| D | Directory |
| A | Archive |
| R | Read Only |
| H | Hidden |
| S | System |
| N | Normal |

Examples:

- `DH` → Hidden Directory
- `AHR` → Archive, Hidden, Read-Only
- `DHS` → Hidden System Directory

---

# SNMP (UDP Port 161)

## Overview

SNMP (Simple Network Management Protocol) is used to monitor and manage network devices.

Default community strings:

- `public` → Read-only
- `private` → Read-write

## Enumeration Goals

- Discover device information.
- Enumerate running processes and installed software.
- Identify network inventory.

## Workflow

### Discover the Service

```bash
nmap -sU -p 161 <IP>
```

### Query Using Default Community String

```bash
snmpwalk -v2c -c public <IP>
```

### Query Specific OID

```bash
snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.25.1.6.0
```

The above OID returns information about running processes.

---

# LDAP & DNS Enumeration

# LDAP (Port 389)

LDAP (Lightweight Directory Access Protocol) stores:

- Users
- Groups
- System information

## Tool

```bash
ldapsearch
```

## Basic Command

```bash
ldapsearch -x -H ldap://<IP> -s base
```

### Authentication Methods

- **SASL** → Advanced authentication framework.
- **Simple Bind (`-x`)** → Simple or anonymous authentication.

### Base DN

The root of the LDAP directory tree.

Example:

```text
dc=metasploitable,dc=local
```

---

# DNS (Port 53)

DNS translates domain names into IP addresses and can reveal internal network information.

## Zone Transfer (AXFR)

A misconfigured DNS server may allow full zone transfers.

```bash
dig axfr @<DNS-IP> <domain>
```

## Check DNS Version

```bash
dig @<DNS-IP> version.bind CHAOS TXT
```

## Verify Port 53

```bash
nmap -p 53 <IP>
```

---

# NFS (Port 2049)

NFS (Network File System) is commonly used on Linux and Unix systems for remote file sharing.

## Enumeration Workflow

### Discover Services

```bash
nmap -p 111,2049 <IP>
```

### List Exported Shares

```bash
showmount -e <IP>
```

If the output contains:

```text
/ *
```

it means the root directory is exported to everyone.

### Mount an Export

```bash
sudo mount -t nfs <IP>:/ <local_folder>
```

## Troubleshooting

### Device Busy Error

Leave the mounted directory before unmounting:

```bash
cd ..
```

### Lazy Unmount

```bash
sudo umount -l <folder>
```

---

# RPC (Port 111)

RPC allows programs to execute services remotely.

## Important Component

### Portmapper (rpcbind)

Maps RPC programs to their dynamic ports.

Example:

- NFS → Program Number `100003`

## Tools

### rpcinfo

```bash
rpcinfo -p <IP>
```

### rpcclient

```bash
rpcclient -U "" <IP>
```

Useful commands inside `rpcclient`:

```text
enumdomusers
srvinfo
```

---

# SMTP (Port 25)

SMTP is responsible for email delivery.

In penetration testing it is commonly used for **user enumeration**.
## SMTP User Enumeration (Nmap)

```bash
nmap -p 25 --script smtp-enum-users <IP>
```

Attempts to enumerate valid usernames from the SMTP server.
```
## Banner Grabbing

```bash
nc <IP> 25
```

or

```bash
telnet <IP> 25
```

## Useful Commands

### Verify User

```text
VRFY root
```

### Expand Mailing List

```text
EXPN
```

### Recipient Validation

```text
RCPT TO:
```

## SMTP Status Codes

| Code | Meaning |
|------|----------|
| 220 | Service Ready |
| 250 | Success |
| 252 | Cannot verify user but user may exist |
| 550 | User does not exist |

---

# SMTP vs RPC

| Feature | SMTP | RPC |
|----------|------|-----|
| Primary Goal | Email Transfer | Inter-process Communication |
| Port | 25 | 111 |
| Enumeration Benefit | User Enumeration | Service Discovery |
| Common Tools | Netcat, Telnet | rpcinfo, rpcclient |

---

# FTP Enumeration (Port 21)

## Overview

FTP is used to transfer files between systems and communicates in plaintext.

## Banner Grabbing

Connecting to the service often reveals its version.

Example:

```text
vsFTPd 2.3.4
```

## Anonymous Login

A common misconfiguration that allows anyone to access the server.

## Nmap Script

```bash
nmap -p 21 --script ftp-anon <IP>
```

## Useful FTP Commands

| Command | Purpose |
|----------|----------|
| `ls -la` | List files including hidden files |
| `cd <dir>` | Change directory |
| `get <file>` | Download file |
| `put <file>` | Upload file |
| `binary` | Switch to binary transfer mode |
| `bye` | Exit FTP |

---

# Enumeration Summary Table

| Service | Port | Tool | Command | Main Issue | Goal |
|----------|------|------|----------|-------------|------|
| SMB | 445 | enum4linux | `smbclient -L //IP` | Anonymous Shares | Users and Shares |
| SNMP | 161/UDP | snmpwalk | `snmpwalk -v2c -c public IP` | Default Community Strings | System Information |
| NFS | 2049 | showmount | `showmount -e IP` | World-Writable Shares | File Access |
| DNS | 53 | dig | `dig axfr @IP domain` | Zone Transfer | Topology Discovery |
| SMTP | 25 | nc/telnet | `VRFY root` | User Enumeration | Valid Accounts |
| FTP | 21 | ftp | `anonymous login` | Anonymous Access | File Access |

---

# Additional Lab Knowledge

# SSH Login Using RSA Key

## Set Proper Permissions

```bash
chmod 600 id_rsa
```

## Login

```bash
ssh -i id_rsa username@ip
```

Sometimes usernames can be extracted from public SSH keys.

---

# Hydra

## Basic Syntax

```bash
hydra -t 4 -l mike -P /usr/share/wordlists/rockyou.txt -vV <IP> ftp
```

## Common Flags

| Flag | Meaning |
|------|----------|
| `-l` | Single username |
| `-L` | Username list |
| `-p` | Single password |
| `-P` | Password list |
| `-vV` | Verbose output |
| `-t` | Number of parallel tasks |

## Website Brute Force Example

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt \
iicseh.com -s 2083 https-form-post \
"/login/:user=^USER^&pass=^PASS^:F=Invalid login"
```

---

# Enumerating NFS Using Nmap

```bash
nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount <IP>
```

---

# Netcat

```bash
nc <IP> <PORT>
```

---

# ProFTPD File Copy Commands

## SITE CPFR (Copy From)

```text
SITE CPFR <filename>
```

Example:

```text
SITE CPFR /home/kenobi/.ssh/id_rsa
```

---

## SITE CPTO (Copy To)

```text
SITE CPTO <new_filename>
```

Example:

```text
SITE CPTO /var/tmp/id_rsa
```

---

## Example Session

```bash
nc 10.48.141.10 21
220 ProFTPD 1.3.5 Server
SITE CPFR /home/kenobi/.ssh/id_rsa
350 File or directory exists
SITE CPTO /var/tmp/id_rsa
250 Copy successful
```

---

# Telnet vs SSH

## Telnet

- Remote administration and troubleshooting.
- Sends all data in plaintext.
- Mainly used for testing network services.

## SSH

- Encrypted remote access.
- Supports password and key-based authentication.
- Standard protocol for secure administration.

## Comparison

| Feature | SSH | Telnet |
|----------|-----|---------|
| Encryption | Yes | No |
| Credentials Protection | Yes | No |
| Port | 22 | 23 |
| Authentication | Passwords and Keys | Passwords |
| Primary Use | Secure Administration | Testing and Legacy Administration |

## Why Telnet Is Still Useful

Although Telnet is considered insecure and is rarely used for remote administration today, it still has several practical uses in networking and penetration testing.

### Common Use Cases

- **Basic Connectivity Testing**  
  Quickly verify whether a remote system is reachable over a specific TCP port.

- **Banner Grabbing**  
  Connect to services manually and identify server software, versions, or other information exposed in service banners.

- **Service Verification**  
  Check whether a service is actively listening on a particular port.

### Important Note

> Telnet transmits all data, including credentials, in plaintext and should **not** be used for remote administration on modern systems.

For secure remote access and administration, **SSH should always be preferred**.

---
