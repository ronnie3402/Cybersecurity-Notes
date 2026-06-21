# Cryptography Lab Notes

---

# 1. Cryptography Fundamentals

Cryptography is the science of protecting information by transforming it into a secure format so that only authorized parties can access or modify it.

## Goals of Cryptography (CIA + Authenticity)

| Goal | Purpose | Technique |
|-------|---------|-----------|
| Confidentiality | Keep data secret from unauthorized users | Encryption |
| Integrity | Ensure data has not been modified | Hashing |
| Authenticity | Verify the identity of the sender | Digital Signatures |
| Non-Repudiation | Prevent sender from denying an action | Digital Signatures |

---

# 2. Encoding vs Encryption vs Hashing

Understanding the differences between these concepts is extremely important.

| Feature | Encoding | Encryption | Hashing |
|----------|------------|------------|---------|
| Purpose | Data representation/compatibility | Confidentiality | Integrity verification |
| Reversible? | Yes | Yes (with key) | No |
| Key Required? | No | Yes | No |
| Security Provided? | No | Yes | Integrity only |
| Examples | Base64, ASCII | AES, RSA | SHA-256, MD5 |

## Key Points

### Encoding
- Converts data into another format for compatibility.
- Does **not** provide security.
- Easily reversible.

### Encryption
- Converts plaintext into ciphertext.
- Requires a cryptographic key.
- Protects confidentiality.

### Hashing
- Produces a fixed-length fingerprint of data.
- One-way operation.
- Used for integrity checking and password storage.

---

# 3. Base64 Encoding

> **Important:** Base64 is NOT encryption.

## Purpose
Converts binary data into ASCII text for safe transmission across text-based protocols.

## Common Uses

- Email attachments (MIME)
- HTTP Headers
- X.509 Certificates
- Malware obfuscation
- CTF challenges

## Characteristics

- Easily reversible
- Requires no key
- Provides zero confidentiality

### Example

```text
Hello -> SGVsbG8=
```

⚠️ Never use Base64 to protect sensitive information.

---

# 4. Symmetric Encryption Algorithms

Symmetric encryption uses a single shared key for both encryption and decryption.

## Advantages

- Fast
- Efficient
- Suitable for bulk data encryption

## Disadvantages

- Key distribution is difficult

---

## AES (Advanced Encryption Standard)

### Features

- Block Size: 128 bits
- Key Sizes:
  - 128-bit
  - 192-bit
  - 256-bit

### Advantages

- Fast
- Secure
- Industry standard

### Uses

- HTTPS/TLS
- VPNs
- Disk encryption
- Wi-Fi security (WPA2/WPA3)

---

## DES (Data Encryption Standard)

### Features

- Block Size: 64 bits
- Key Size: 56 bits

### Status

❌ Deprecated

### Weakness

- Vulnerable to brute-force attacks.

---

## 3DES (Triple DES)

### Features

- Applies DES three times.
- Key Sizes:
  - 112 bits
  - 168 bits

### Status

⚠️ Being phased out.

### Comparison

More secure than DES but slower and weaker than AES.

---

# 5. Asymmetric Encryption Algorithms

Asymmetric cryptography uses two keys:

- Public Key
- Private Key

## Advantages

- Secure key exchange
- Digital signatures

## Disadvantages

- Slower than symmetric encryption

---

## RSA

### Based On

Prime factorization.

### Uses

- Key exchange
- Digital signatures
- TLS certificates

### Common Key Sizes

- 2048 bits
- 3072 bits
- 4096 bits

---

## Diffie-Hellman (DH)

### Purpose

Securely exchange keys over insecure networks.

### Important

DH does **not** encrypt data.

### Weakness

Vulnerable to Man-in-the-Middle attacks if authentication is absent.

---

## ECC (Elliptic Curve Cryptography)

### Features

- Smaller key sizes.
- Faster than RSA.
- High security.

### Ideal For

- Mobile devices
- IoT devices

---

# 6. Hashing Algorithms

Hashing generates a fixed-length digital fingerprint.

## Characteristics

- One-way function
- Fixed output size
- Small input changes produce completely different hashes

---

## SHA-256

### Output Length

256 bits

### Uses

- Password storage
- File integrity
- Digital signatures
- Blockchain

### Status

✅ Secure

---

## MD5

### Output Length

128 bits

### Status

❌ Broken

### Weakness

Susceptible to collisions.

---

# 7. HMAC (Hash-Based Message Authentication Code)

HMAC combines:

```text
Secret Key + Hash Function
```

## Provides

- Integrity
- Authenticity

## Common Uses

- APIs
- SSL/TLS
- IPSec
- JWT Tokens

---

# 8. Digital Signatures

Digital signatures verify:

1. Authenticity
2. Integrity
3. Non-Repudiation

---

## How Digital Signatures Work

### Signing

```text
Private Key → Sign
```

### Verification

```text
Public Key → Verify
```

---

## Workflow

```text
Message
   ↓
Hash Generated
   ↓
Hash encrypted using sender's Private Key
   ↓
Digital Signature created
```

Receiver:

```text
Decrypt signature using Public Key
Compare hashes
```

If hashes match:

```text
✓ Authentic
✓ Unmodified
```

---

## Real-World Uses

- SSL/TLS Certificates
- Secure Email (S/MIME)
- Software Code Signing
- Document Signing

---

# 9. Steganography vs Cryptography

| Feature | Steganography | Cryptography |
|----------|--------------|--------------|
| Goal | Hide existence | Hide contents |
| Visibility | Secret remains hidden | Encrypted data visible |
| Carrier Required | Yes | No |
| Example | Image hiding | AES |

---

## Steganography

Hides secret data inside:

- Images
- Audio files
- Videos

### Common Formats

- JPG
- PNG
- MP3
- MP4

### Example

Least Significant Bit (LSB) technique.

---

# 10. CyberChef (https://cyberchef.org)

CyberChef is a web-based cryptographic toolkit. 

## Capabilities

- Encoding/Decoding
- Encryption/Decryption
- Hashing
- Data Conversion
- Malware String Analysis

## Common Uses

- Decode Base64
- Crack hashes
- Analyze malware
- Convert formats

---

# 11. Online Hash Cracking Platforms

Online hash cracking platforms use pre-computed databases (rainbow tables) and previously cracked hashes to identify or recover passwords from hashes.

> ⚠️ **Note:** These tools should only be used for authorized security testing, educational purposes, or CTF challenges.

---

## CrackStation

Website:

https://crackstation.net

### Features

- Uses large pre-computed rainbow tables.
- Extremely fast for cracking common MD5 and SHA-1 hashes.
- Can instantly recover weak or commonly used passwords.

### Common Uses

- CTF challenges
- Password auditing
- Weak password identification

### Limitations

- Cannot crack strong or salted hashes effectively.
- Less powerful than offline tools like Hashcat or John the Ripper.

---

## Hashes.com

Website:

https://hashes.com

### Features

- Identifies hash types.
- Maintains a community-driven cracked hash database.
- Supports lookup for multiple hash algorithms.

### Supported Functions

- Hash identification
- Hash lookup
- Online cracking requests
- Community-based cracking results

### Common Uses

- Identifying unknown hash types.
- Searching previously cracked hashes.
- Quick verification during labs and CTFs.

---

## Comparison Table

| Feature | CrackStation | Hashes.com |
|----------|-------------|------------|
| Rainbow Table Lookup | Yes | Yes |
| Hash Identification | No | Yes |
| Community Database | No | Yes |
| Online Cracking Requests | No | Yes |
| Best Use Case | Fast lookup of common hashes | Hash identification and community lookup |

---

## Important Limitations

Online hash cracking platforms generally fail against:

- Strong passwords
- Salted hashes
- Long random passwords
- Modern password hashing algorithms such as:
  - bcrypt
  - scrypt
  - Argon2

For these cases, offline cracking tools such as **Hashcat** and **John the Ripper** are typically required.

---

## Recommended Workflow

1. Identify the hash type.
2. Search online databases (CrackStation, Hashes.com).
3. If the hash is not found, use offline tools like Hashcat or John the Ripper.
4. Analyze password complexity and security posture.

---

## Cybersecurity Tip

During penetration tests and CTFs, always check online databases first before launching computationally expensive offline attacks. This can save significant time and resources.


# 12. Practical Commands

---

## OpenSSL AES Encryption

### Encrypt File

```bash
openssl enc -aes-256-cbc -salt -in p.txt -out p.txt.aes
```

### Decrypt File

```bash
openssl enc -d -aes-256-cbc -in p.txt.aes -out p.txt
```

---

## Encryption with Password

### Encrypt

```bash
openssl enc -aes-256-cbc -salt -in p.txt -out p.txt.aes -pass pass:jordon
```

### Decrypt

```bash
openssl enc -d -aes-256-cbc -in p.txt.aes -out p2.txt -pass pass:jordon
```

---

## RSA Private Key Generation

```bash
openssl genrsa -des3 -out ca.key 4096
```

### Explanation

| Option | Meaning |
|---------|---------|
| genrsa | Generate RSA key |
| -des3 | Encrypt key with 3DES |
| -out | Output file |
| 4096 | Key length |

---

# 13. GPG (GNU Privacy Guard)

GPG is used for:

- Email encryption
- Digital signatures
- Key management

---

## Generate Key Pair

```bash
gpg --full-generate-key
```

---

## Sign File

```bash
gpg --sign password.txt
```

---

## Verify Signature

```bash
gpg --verify password.txt.gpg
```

---

# 14. Steganography Tools

## Steghide

### Install

```bash
sudo apt-get install steghide
```

### Embed File

```bash
steghide embed -cf image.jpeg -ef secret.txt
```

### Extract Hidden Data

```bash
steghide extract -sf image.jpeg
```

---

## Stegseek

Fast password cracker for Steghide files.

### Crack Using Wordlist

```bash
stegseek image.jpg rockyou.txt
```

### Seed Cracking

```bash
stegseek --seed image.jpg
```

---

# 15. Hashing Commands

## MD5

```bash
md5sum file.txt
```

## SHA256

```bash
sha256sum file.txt
```

---

## Identify Hash Type

```bash
hash-identifier
```

---

# 16. CEH Exam Quick Revision

## Security Hierarchy

```text
AES > 3DES > DES
```

## Remember

- Base64 ≠ Encryption
- Hashing is one-way.
- RSA uses Public/Private keys.
- Diffie-Hellman exchanges keys only.
- Digital signatures:
  - Sign → Private Key
  - Verify → Public Key

---

# 17. Cybersecurity Relevance

## Offensive Security

- Cracking weak encryption.
- Extracting hidden files.
- Identifying insecure cryptographic implementations.

## Defensive Security

- Secure communications.
- File integrity monitoring.
- Certificate management.
- Secure password storage.

---

# 18. Recommended Tools

| Tool | Purpose |
|-------|---------|
| OpenSSL | Encryption & Key Generation |
| GPG | Digital Signatures |
| CyberChef | Encoding, Hashing, Analysis |
| Steghide | Hide Data |
| Stegseek | Crack Steghide Passwords |
| Hash-Identifier | Identify Hash Types |
