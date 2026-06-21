# Cryptography: Comprehensive Study Notes

---

# 1. The Core Pillars (CIA Triad)

Cryptography aur Information Security ka foundation **CIA Triad** par based hota hai.

## Confidentiality (Encryption)

Data ko secret rakhna taaki unauthorized log use padh na sakein.

- Sirf authorized users hi data access kar saken.
- Encryption ka primary goal confidentiality provide karna hota hai.

**Examples:**
- AES
- RSA
- HTTPS

---

## Integrity (Hashing)

Yeh ensure karta hai ki data ke saath koi tampering ya modification nahi hui hai.

- Agar data change ho jaye, toh hash bhi change ho jata hai.
- Data ki originality aur correctness verify karne ke liye use hota hai.

**Examples:**
- SHA-256
- SHA-3

---

## Authenticity (Digital Signatures)

Yeh prove karta hai ki message ya data asli sender ne hi bheja hai.

- Sender ki identity verify karta hai.
- Non-repudiation bhi provide karta hai (sender message bhejne se mana nahi kar sakta).

**Examples:**
- Digital Signatures
- PKI Certificates

---

# 2. Encoding vs Encryption vs Hashing

| Feature | Encoding | Encryption | Hashing |
|----------|-----------|------------|---------|
| **Purpose** | Data compatibility & representation | Secrecy / Privacy | Integrity Verification |
| **Reversible?** | Yes (No key required) | Yes (Key required) | No (One-way) |
| **Key Required?** | No | Yes | No |
| **Security Purpose** | No | Yes | Yes |
| **Examples** | ASCII, Base64, URL Encoding | AES, RSA | SHA-256, MD5 |

### Quick Summary

- **Encoding** → Data ko kisi dusre format mein convert karna.
- **Encryption** → Data ko secret banana.
- **Hashing** → Data ka fingerprint generate karna.

---

# 3. Hashing & Collisions

## What is Hashing?

Hashing ek process hai jisme kisi bhi input data ka ek fixed-length output generate hota hai jise **Hash Value** ya **Message Digest** kehte hain.

Chahe input kitna bhi bada ho, output ki length fixed rehti hai.

### Example

```text
Input: Hello World

SHA-256:
a591a6d40bf420404a011733cfb7b190...
```

---

## Properties of a Good Hash Function

- Deterministic (same input → same output)
- Fast computation
- Fixed-length output
- One-way function
- Collision resistant

---

## Collision

Jab do different inputs ka same hash output generate ho jaye toh use **Collision** kehte hain.

```text
Input A → Hash X
Input B → Hash X
```

Yahan:

```text
Input A ≠ Input B
```

Lekin:

```text
Hash(A) = Hash(B)
```

---

## Security Risks of Collisions

Agar attacker collision generate kar le:

- Original file ko malicious file se replace kar sakta hai.
- Digital signatures bypass kiye ja sakte hain.
- Integrity checks fail ho sakte hain.

---

## Safe Hash Algorithms

✅ SHA-256

✅ SHA-384

✅ SHA-512

✅ SHA-3

---

## Unsafe / Broken Algorithms

❌ MD5

❌ SHA-1

In algorithms mein practical collision attacks exist karte hain.

---

# 4. Symmetric vs Asymmetric Encryption

---

## Symmetric Encryption (Private Key Cryptography)

Ek hi secret key encryption aur decryption dono ke liye use hoti hai.

```text
Plaintext + Secret Key → Ciphertext

Ciphertext + Same Secret Key → Plaintext
```

### Advantages

- Fast
- Efficient for large data

### Disadvantages

- Key distribution difficult hota hai.

### Examples

- AES
- DES
- 3DES
- ChaCha20

---

## Asymmetric Encryption (Public Key Cryptography)

Isme do alag keys hoti hain:

- Public Key
- Private Key

### Encryption Process

```text
Public Key → Encrypt

Private Key → Decrypt
```

### Digital Signature Process

```text
Private Key → Sign

Public Key → Verify
```

---

## Advantages

- Secure key exchange
- Digital signatures possible

## Disadvantages

- Symmetric encryption se slower hota hai.

---

## Examples

- RSA
- ECC
- ElGamal

---

## Symmetric vs Asymmetric Comparison

| Feature | Symmetric | Asymmetric |
|----------|------------|------------|
| Keys Used | One Key | Two Keys |
| Speed | Fast | Slow |
| Key Distribution | Difficult | Easy |
| Main Use | Bulk Encryption | Key Exchange & Signatures |
| Examples | AES | RSA |

---

# 5. Diffie-Hellman Key Exchange

## Purpose

Do parties insecure network (Internet) par bina secret key share kiye ek common secret key generate kar sakti hain.

---

## Working Principle

Dono users:

1. Public parameters choose karte hain.
2. Apni private key select karte hain.
3. Public values exchange karte hain.
4. Independently same secret key derive kar lete hain.

---

## Simple Analogy

Imagine:

- Public Color = Yellow
- Alice Secret Color = Red
- Bob Secret Color = Blue

Dono apna secret color public color mein mix karte hain aur exchange karte hain.

Final mixture dono ke paas same hota hai.

Attacker sirf exchanged colors dekh sakta hai, original secret colors nahi.

---

## Mathematical Security

Diffie-Hellman security **Discrete Logarithm Problem (DLP)** par based hoti hai.

Is problem ko solve karna computationally infeasible mana jata hai.

---

## Uses

- HTTPS
- TLS
- VPNs
- SSH

---

# 6. Public Key Infrastructure (PKI)

PKI ek framework hai jo public key cryptography ko securely manage karta hai.

---

## Digital Certificate

Ek electronic identity card jo:

```text
Identity + Public Key
```

ko bind karta hai.

Certificates X.509 format mein hote hain.

---

## Certificate Authority (CA)

Trusted organization jo certificates issue aur digitally sign karti hai.

### Examples

- DigiCert
- GlobalSign
- Let's Encrypt

---

## Trust Chain

```text
Root CA
   ↓
Intermediate CA
   ↓
End User Certificate
```

Browser certificate chain verify karta hai.

Agar chain trusted ho:

🔒 Green Lock icon display hota hai.

---

## PKI Components

- Public Key
- Private Key
- Digital Certificate
- Certificate Authority
- Certificate Revocation List (CRL)

---

# 7. Practical Tools & Commands

---

## CyberChef

CyberChef ko aksar:

> "The Cyber Swiss Army Knife"

kaha jata hai.

### Uses

- Decoding
- Encoding
- Hashing
- Encryption
- Data Analysis

---

## GPG (GNU Privacy Guard)

Email encryption aur digital signatures ke liye use hota hai.

### Import Key

```bash
gpg --import keyfile
```

### Decrypt File

```bash
gpg --decrypt file.gpg
```

### Encrypt File

```bash
gpg --encrypt --recipient user file.txt
```

---

## OpenSSL

Cryptographic operations aur certificate management ke liye standard toolkit.

### Generate RSA Private Key

```bash
openssl genrsa -des3 -out ca.key 4096
```

### Generate CSR

```bash
openssl req -new -key ca.key -out request.csr
```

### View Certificate

```bash
openssl x509 -in cert.pem -text -noout
```

---

## Linux Extraction Commands

### ZIP Files

```bash
unzip file.zip
```

### GZIP Files

```bash
gunzip file.gz
```

### TAR.GZ Files

```bash
tar -xzvf file.tar.gz
```

---

# 8. XOR Cipher

XOR Cipher sabse basic symmetric encryption techniques mein se ek hai.

---

## Working Logic

```text
Plaintext ⊕ Key = Ciphertext

Ciphertext ⊕ Key = Plaintext
```

Jahan:

```text
⊕ = XOR Operation
```

---

## Example

```text
Plaintext: A

Binary: 01000001

Key:      00001111

Cipher:   01001110
```

Decryption:

```text
Cipher ⊕ Key = Original Plaintext
```

---

## Properties

- Fast
- Simple
- Easily reversible
- Same key encryption aur decryption ke liye use hoti hai

---

# One-Time Pad (OTP)

One-Time Pad XOR cipher ka sabse secure implementation mana jata hai.

## Conditions for Perfect Security

- Key completely random honi chahiye.
- Key ki length plaintext ke equal honi chahiye.
- Key sirf ek baar use honi chahiye.
- Key secret rehni chahiye.

Agar ye conditions satisfy ho jayein, toh One-Time Pad theoretically **unbreakable** mana jata hai.

---

# 📝 Quick Revision

| Concept | Main Purpose |
|----------|-------------|
| Encryption | Confidentiality |
| Hashing | Integrity |
| Digital Signature | Authenticity |
| Symmetric Encryption | Fast Data Encryption |
| Asymmetric Encryption | Secure Key Exchange |
| Diffie-Hellman | Shared Secret Generation |
| PKI | Trust Management |
| XOR | Basic Symmetric Encryption |
| One-Time Pad | Theoretically Unbreakable Encryption |

---
