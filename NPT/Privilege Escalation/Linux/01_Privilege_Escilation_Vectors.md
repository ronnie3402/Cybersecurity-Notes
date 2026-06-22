# Linux Privilege Escalation (Module 10)

# Overview
> Privilege Escalation (PrivEsc) is the process of elevating standard user permissions to Administrator (`root`) access. In enterprise environments and penetration testing (e.g., OSCP), misconfigurations can occur at multiple layers. This module covers the 12 core industry-standard vectors for Linux Privilege Escalation.

---

## The 12 Core Privilege Escalation Vectors

### 1. Sudo Misconfiguration (`sudo -l`)
* **Concept:** System administrators often grant normal users the ability to run specific commands with root privileges (often without a password via `NOPASSWD` in the `/etc/sudoers` file).
* **Exploitation:** By running `sudo -l`, an attacker can list their allowed commands. If an allowed binary (e.g., `vim`, `awk`, `less`, `find`) has shell escape capabilities, the attacker can leverage it to spawn a root shell.
* **Reference:** **GTFOBins** is the definitive resource for bypassing local security restrictions using misconfigured sudo rights.

### 2. Service Exploits
* **Concept:** Background services and daemons (like MySQL, Apache, or custom internal applications) often run with root privileges.
* **Exploitation:** If a service running as root is outdated or misconfigured (e.g., allowing Remote Code Execution or arbitrary file writes), exploiting that specific service will grant a shell with the privileges of the service owner (root).

### 3. SUID, SGID, and Sticky Bit
* **Concept:** * **SUID (Set-User-ID):** Allows a file to be executed with the privileges of its owner (typically root).
  * **SGID (Set-Group-ID):** Executes with the privileges of the file's group.
  * **Sticky Bit:** Restricts file deletion within a directory to the file owner.
* **Exploitation:** Attackers search for non-standard SUID binaries. If a custom or vulnerable binary has the SUID bit set, it can be manipulated (via shell escapes, buffer overflows, or path hijacking) to grant root access.
* **Command:** `find / -perm -u=s -type f 2>/dev/null`

### 4. Weak File Permissions
* **Concept:** Core OS authentication and configuration files must be strictly protected.
* **Exploitation:** * **`/etc/passwd` (World-Writable):** An attacker can directly append a new user with root privileges (`UID 0`) and a known password hash.
  * **`/etc/shadow` (World-Readable/Writable):** An attacker can read root password hashes for offline cracking, or directly overwrite the root hash with their own.

### 5. Cron Jobs (Scheduled Tasks)
* **Concept:** Cron jobs automate background tasks at specific intervals. System-wide cron jobs often run as root.
* **Exploitation:**
  * **Writable Scripts:** If a cron job executes a script that is world-writable, an attacker can inject a reverse shell payload.
  * **Wildcard Injection:** If a cron job uses wildcards (e.g., `tar *`), attackers can create files with specific names (like `--checkpoint-action=exec=sh`) to execute arbitrary commands.
  * **$PATH Hijacking:** If the cron job does not use absolute paths, an attacker can place a malicious binary in a higher-priority directory.

### 6. SSH Keys, History Files, and Configs
* **Concept:** Administrators often leave sensitive data exposed in user directories or backups.
* **Exploitation:**
  * **History Files:** Users may type passwords in plaintext (e.g., `mysql -u root -pPassword123`). Check `.bash_history` or `.zsh_history`.
  * **SSH Keys:** Finding unprotected private keys (`id_rsa`) in `~/.ssh/` or backup folders allows direct login as the target user/root.
  * **Config Files:** Searching web directories (e.g., `wp-config.php`) or deployment scripts for plaintext database or root credentials.

### 7. NFS (Network File System) Root Squashing
* **Concept:** NFS allows systems to share directories over a network. By default, NFS strips root privileges from remote users (`root_squash`).
* **Exploitation:** If an NFS share is exported with `no_root_squash` in `/etc/exports`, a remote attacker can mount the share on their local machine, create a malicious C binary, set the SUID bit, and then execute it on the target machine to gain root access.

### 8. Kernel Exploits
* **Concept:** Exploiting a known public vulnerability in the core Linux kernel itself (e.g., Dirty COW, Dirty Pipe).
* **Exploitation:** After checking the kernel version (`uname -a`), an attacker compiles and executes a specific exploit code written in C. This interacts directly with kernel memory to grant immediate root access.
* **Status:** Highly critical but used as a last resort, as kernel exploits can cause system crashes (Kernel Panics).

### 9. Capabilities
* **Concept:** Linux capabilities divide root privileges into smaller, distinct units (e.g., `CAP_SETUID`, `CAP_DAC_OVERRIDE`).
* **Exploitation:** System administrators sometimes assign excessive capabilities to standard binaries (like `python`, `tar`, or `perl`) instead of using SUID. An attacker can abuse these capabilities to manipulate user IDs or read protected files.
* **Command:** `getcap -r / 2>/dev/null`

### 10. Vulnerable Software (Local Packages)
* **Concept:** Software packages installed on the system may contain known vulnerabilities (CVEs) that allow local privilege escalation.
* **Exploitation:** Enumerating installed software (e.g., `dpkg -l` or `rpm -qa`) to find outdated versions. Classic examples include **Polkit (PwnKit)** vulnerabilities or outdated `sudo` versions (e.g., CVE-2021-3156 "Baron Samedit").

### 11. Port Forwarding (Internal Pivoting)
* **Concept:** Some highly privileged services (like internal databases, administrative web panels, or root-level APIs) are bound only to the loopback interface (`127.0.0.1`) and are blocked from external access by a firewall.
* **Exploitation:** An attacker with standard SSH or shell access uses Local Port Forwarding (via SSH, Chisel, or Socat) to tunnel the internal service out to their attacking machine. They then exploit the vulnerable internal service to escalate privileges.

### 12. User Group Memberships
* **Concept:** Certain Linux groups grant permissions that are effectively equivalent to root access.
* **Exploitation:** An attacker checks their group memberships using the `id` command.
  * **`docker` group:** Allows mounting the entire host root filesystem (`/`) into a container, granting full read/write access to the host OS.
  * **`lxd` / `lxc` group:** Similar to Docker, allows creating a container to mount and manipulate the host filesystem.
  * **`disk` group:** Grants raw read/write access to hard drive block devices.
  * **`shadow` group:** Grants read access to `/etc/shadow` to extract password hashes.

---

## Certification & SOC Strategy
When approaching a Linux machine during an engagement or exam:
1. **Always start with low-hanging fruit:** `sudo -l`, `id` (User Groups), and `.bash_history`.
2. **Automate Enumeration:** Run scripts like `LinPEAS` or `Linux-Exploit-Suggester` to quickly identify SUID binaries, Capabilities, and Cron Jobs.
3. **Save Kernel Exploits for Last:** Due to the risk of crashing the target system, rely on misconfigurations (Vectors 1-7 and 12) before attempting complex Kernel or Software exploits.
