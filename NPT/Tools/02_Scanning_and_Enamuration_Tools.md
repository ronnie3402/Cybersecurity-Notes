## 2. SCANNING & ENUMERATION

---

## 2.1 SMB / NetBIOS (Ports 139, 445)

---

## Nmap SMB scripts


nmap --script smb-enum-shares,smb-enum-users target


---

## enum4linux

Used for:

- user enumeration  
- share listing  
- group enumeration  
- SID extraction  

---

## smbclient

Used to access SMB shares manually.

---

## 2.2 SNMP (Port 161 UDP)

---

## snmpwalk

Extracts system data:

- processes  
- running services  
- network info  

---

## 2.3 LDAP (Port 389)

---

## ldapsearch

Used for Active Directory enumeration:

- users  
- groups  
- domain structure  

---

## 2.4 DNS (Port 53)

- `dig` → DNS queries  
- `nslookup` → basic DNS resolution  

---

## 2.5 NFS (Port 2049) & RPC (Port 111)

---

## showmount

Lists exported shares.

---

## mount / umount

Used to attach/detach remote shares.

---

## 2.6 SMTP (Port 25)

---

## netcat / telnet

Used for:

- banner grabbing  
- email relay testing  

---

## 2.7 RPC (Port 111)

---

## rpcinfo

Lists RPC services.

---

## rpcclient

Used for Samba/Windows enumeration.

---

## 2.8 FTP (Port 21)

---

## ftp

Used for:

- file upload/download  
- directory listing  

---

## 2.9 SSH (Port 22)

---

## ssh

Secure remote login using:

- password authentication  
- private key authentication (`id_rsa`)  
