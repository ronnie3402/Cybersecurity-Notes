# 🔁 Cybersecurity Workflow Pipeline (End-to-End)

A complete penetration testing flow from initial information gathering to maintaining access.

---

## 1. Recon (Reconnaissance)

Goal: Gather maximum public information about the target.

- OSINT collection  
- Domain discovery  
- Employee/email harvesting  
- Infrastructure mapping  

### Common tools:

- :contentReference[oaicite:0]{index=0}  
- :contentReference[oaicite:1]{index=1}  
- :contentReference[oaicite:2]{index=2}  

---

## 2. OSINT (Open Source Intelligence)

Goal: Extract publicly available sensitive data.

- Google dorking  
- Email discovery  
- Leaked credentials lookup  
- Social footprinting  

---

## 3. DNS Enumeration

Goal: Map domain infrastructure.

- Subdomains  
- Mail servers (MX)  
- Name servers (NS)  
- Zone transfer testing  

### Tools:

- :contentReference[oaicite:3]{index=3}  
- :contentReference[oaicite:4]{index=4}  

---

## 4. Network Scanning

Goal: Identify live hosts and open ports.

- Port scanning  
- Service detection  
- OS fingerprinting  
- Firewall detection  

### Tools:

- :contentReference[oaicite:5]{index=5}  
- :contentReference[oaicite:6]{index=6}  

---

## 5. Web Enumeration

Goal: Discover hidden web assets.

- Directories  
- Files  
- Admin panels  
- Virtual hosts  

### Tools:

- gobuster  
- ffuf  
- feroxbuster  

---

## 6. Services Enumeration

Goal: Interact with exposed services.

- SMB (Windows shares)  
- FTP  
- SSH  
- SMTP  
- LDAP  

### Tools:

- smbclient  
- rpcclient  
- enum4linux  
- snmpwalk  

---

## 7. Vulnerability Scanning

Goal: Identify known security weaknesses.

- CVE matching  
- Misconfiguration detection  
- Exploit database lookup  

### Tools:

- :contentReference[oaicite:7]{index=7}  
- :contentReference[oaicite:8]{index=8}  

---

## 8. Exploitation

Goal: Gain initial access.

- Password attacks  
- Service exploitation  
- Web exploits  
- Misconfiguration abuse  

### Tools:

- Hydra  
- Metasploit Framework  

---

## 9. Privilege Escalation

Goal: Move from low privilege → root/admin.

- SUID abuse  
- misconfigured groups  
- kernel exploits  
- cron jobs  

### Tools:

- LinPEAS  
- LinEnum  
- Linux Exploit Suggester  

---

## 10. Persistence

Goal: Maintain long-term access.

- SSH backdoors  
- cron persistence  
- authorized_keys injection  
- service manipulation  

---

# 🧠 Final Flow Summary
