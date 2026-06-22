## 7. PRIVILEGE ESCALATION TOOLS

---

## pspy

A lightweight process monitoring tool that runs without root privileges.

### Monitors:

- cron jobs  
- background processes  
- system-triggered commands  
- hidden scheduled tasks  

### Why it is useful:

Helps detect:

- misconfigured cron jobs  
- scripts running as root  
- automatic privilege escalation opportunities  

---

## mkpasswd

Generates Linux password hashes.

### Used for:

- creating encrypted password strings  
- preparing entries for `/etc/shadow`  
- testing authentication setups  

---

## openssl passwd

Creates password hashes for `/etc/passwd`.

### Used for:

- generating hashed passwords  
- setting or modifying local user credentials  
- testing password hashing formats (MD5, SHA variants depending on config)  
