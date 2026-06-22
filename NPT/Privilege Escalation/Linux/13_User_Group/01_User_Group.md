# Linux Privilege Escalation: Exploiting User Groups

## 1. Core Concept: How Linux Groups Work

Linux uses a permission system based on three identities:

User (Owner) → file creator or assigned owner  
Group → set of users sharing same permissions  
Others → everyone else on the system  

Every file and process is controlled using these three layers.

---

## Permission Model

Each file has permissions like:
-rw-r--r--

Breakdown:

rw- → owner permissions  
r-- → group permissions  
r-- → others  

So if a user belongs to a powerful group, they inherit whatever that group can access.

---

## Why Groups Matter in Privilege Escalation

Linux administrators often add users to special groups for convenience.  
If misconfigured, these groups can indirectly allow:

- Root filesystem access  
- Password hash access  
- Disk-level manipulation  
- Log file inspection  
- Container escape  

---

## 2. Enumeration: Finding Group Privileges

Before exploiting anything, check current group membership.

### Command 1
id

### Command 2
groups


---

## What to look for

High-risk groups:

- docker  
- disk  
- shadow  
- adm  
- lxd  
- video  

Even a single match can indicate a privilege escalation path.

## 3. Exploitation Techniques by Group

---

## 3.1 Docker Group → Instant Root Access

### Why it works

Docker daemon runs with root-level privileges on the host.  
Anyone in the docker group can control containers.

This becomes dangerous because:

- Containers can mount host filesystem  
- Root filesystem (`/`) can be attached inside container  
- Escape from container = root on host  

---

### Step 1: Check images
docker images

Look for available base images like:

- ubuntu  
- alpine  
- debian  

---

### Step 2: Mount host filesystem
docker run -v /:/mnt --rm -it ubuntu chroot /mnt bash


---

### What happens internally

- `-v /:/mnt` → mounts host root filesystem inside container  
- `chroot /mnt` → changes root directory to host system  
- `bash` → spawns shell in that environment  

---

### Result
root@host#


Full root access to the system.

---

## 3.2 Disk Group → Raw Disk Access

### Why it works

Members of disk group can access:

- `/dev/sda1`  
- `/dev/sda`  
- `/dev/sdb`  

These are raw block devices representing the hard drive.

This bypasses:

- File permissions  
- OS-level restrictions  
- User-level isolation  

---

### Step 1: Open disk interface
debugfs /dev/sda1

---

### Step 2: Extract sensitive files

Inside `debugfs`:
cat /etc/shadow
cat /root/.ssh/id_rsa

---

### What this enables

- Read any file on disk  
- Recover deleted files  
- Extract SSH private keys  
- Dump password hashes  

---

## 3.3 Shadow Group → Password Hash Access

### Why it matters

Linux stores password hashes in:
/etc/shadow

Normally:

- `/etc/passwd` → user info with placeholder (`x`)  
- `/etc/shadow` → actual password hashes  

---

### Step: Read shadow file
cat /etc/shadow

---

### Attack flow

- Extract root hash  
- Save it locally  
- Use offline cracking tools:
  - John the Ripper  
  - Hashcat  

---

### Impact

- Password recovery  
- Offline brute force attacks  
- Full system compromise if weak password  

---

## 3.4 Adm Group → Log File Intelligence

### Why it matters

The adm group provides access to system logs:
/var/log/

Logs often contain:

- Authentication attempts  
- Service errors  
- Application debug info  
- Accidental password leaks  

---

### Step 1: Search logs
grep -i "password" /var/log/syslog
grep -i "pass" /var/log/auth.log

---

### What can be found

- Plaintext credentials  
- API keys  
- Cron job commands  
- Misconfigured services  
- Internal IP addresses  

---

### Why it is powerful

Even without root access, logs can lead to:

- Credential reuse attacks  
- SSH login compromise  
- Lateral movement inside network  

---

## 3.5 Video Group (Rare Case)

### Potential risk

Access to hardware-level interfaces may allow:

- framebuffer access  
- device-level manipulation  
- sometimes privilege escalation via drivers  

This depends on system configuration.
## 4. Session Issue: Group Not Updating

Sometimes after being added to a group, changes do not apply immediately.

---

### Fix
newgrp docker

---

### What it does

- Refreshes group session context  
- Applies new privileges instantly  
- Avoids logout/login requirement  

---

## 5. Common Problems & Fixes

---

## Issue 1: Docker image not found

### Problem

No internet → cannot pull images

---

### Fix
docker images

Use existing local image instead of pulling new one.

---

## Issue 2: debugfs not available

### Problem

Tool missing on system

---

### Alternative
tar -cvf /tmp/backup.tar /dev/sda1

(Used for raw disk extraction fallback methods)

---

## Issue 3: Group exists but no permission

### Problem

User added to group but access denied

---

### Cause

Session not refreshed

---

### Fix
newgrp <groupname>

---

## 6. Summary Table

| Group   | Impact |
|--------|--------|
| docker | Full root via container escape |
| disk   | Raw disk access, file extraction |
| shadow | Password hash exposure |
| adm    | Log-based credential leakage |
| video  | Hardware-level access (rare exploitation paths) |

---

## Final Insight

Group-based privilege escalation is one of the most dangerous misconfigurations in Linux because:

- No exploit is needed in many cases  
- Access is granted purely by misconfiguration  
- Impact can directly lead to full root compromise  
