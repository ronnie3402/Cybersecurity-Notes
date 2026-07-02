## **Windows Privilege Escalation (PrivEsc)** 

Windows Privilege Escalation is a critical milestone in offensive security. While it is core to red teaming, understanding these techniques is equally vital for Blue Team defense. To build robust detection alerts for registry modifications or suspicious service executions, one must understand how an attacker elevates their privileges within a system. 

**Definition:** Privilege Escalation is the process of exploiting a bug, design flaw, or configuration oversight in an operating system or software application to gain elevated access to resources that are normally protected. 

## **Core Concepts** 

- **Vertical Privilege Escalation:** Occurs when a user with lower privileges accesses functions or content reserved for higher-privilege users (e.g., escalating from a standard user to `NT AUTHORITY\SYSTEM` ). This is the primary objective of PrivEsc. 

- **Horizontal Privilege Escalation:** Occurs when a user accesses the data or functions of another user at the same privilege level. 

## **Common Attack Vectors** 

After achieving initial access, you typically operate within a limited shell. To reach `SYSTEM` level, investigate these common misconfigurations: 

1. **Unquoted Service Paths:** If a system service executable path is saved in the registry without quotes ( `""` ) and the path contains spaces, Windows may misinterpret the path. An attacker can place a malicious executable in the directory corresponding to the space. Upon system restart or service trigger, the malicious binary executes with `SYSTEM` privileges. 

2. **Weak Service or Registry Permissions:** If a low-privileged user is granted `Modify` (M) or `Write` (W) access to a service or its registry keys, an attacker can edit the service's `binPath` to point to a custom payload. 

3. **Stored Credentials:** Administrators often leave credentials in `unattend.xml` files, registry keys, or plaintext files. Use `cmdkey /list` or inspect PowerShell history ( `ConsoleHost_history.txt` ) to locate stored credentials that may provide administrative access. 

4. **Token Impersonation (Potato Attacks):** If `whoami /priv` reveals `SeImpersonatePrivilege` or `SeAssignPrimaryTokenPrivilege` enabled, you can utilize exploits like "Juicy Potato" or "Rotten Potato" to steal system-level tokens and gain the highest privileges. 

## **Enumeration Methodology** 

Privilege Escalation is 90% enumeration. Use a systematic approach: 

- **Manual Enumeration:** Start with baseline commands. Use `whoami /all` to check current groups and privileges, `systeminfo` to identify missing patches and potential kernel exploits, and `netstat -ano` to inspect listening internal ports. 

- **WinPEAS:** A script/executable that scans the system and provides color-coded output. Red text indicates high-probability PrivEsc vectors. 

- **PowerUp:** A PowerShell script that finds vulnerabilities and offers built-in functions to exploit them, such as generating malicious `.msi` installers. 

## **Automated vs. Manual Enumeration** 

**Question:** If we primarily use tools like WinPEAS for misconfigurations in Windows— unlike Linux where we rely more on manual enumeration—is WinPEAS the standard for Windows? 

**Answer:** Automated enumeration tools like WinPEAS and PowerUp are essential for Windows; however, relying solely on them is a tactical error. 

**Definition:** Automated Enumeration Scripts are diagnostic tools that rapidly query system configurations, file permissions, and registry settings to highlight potential privilege escalation vectors, often outputting color-coded results for quick analysis. 

**Why WinPEAS Dominates Windows:** Unlike Linux, where the "everything is a file" philosophy allows for fast manual checks (e.g., `find / -perm -4000` or `sudo -l` ), Windows architecture is highly complex: 

- **The Windows Registry:** Contains thousands of keys; checking each service's registry permission manually is impractical. 

- **Complex ACLs:** Permissions (Full Control, Modify, Read/Execute) are granular and applied at both file and folder levels. 

- **Services and Tasks:** Identifying unquoted service paths or vulnerable scheduled tasks requires a deep system scan. 

## **Risks of Relying Only on WinPEAS:** 

1. **Detection Risk:** WinPEAS is noisy. EDR and SOC systems frequently flag it because it performs thousands of registry queries and file reads in seconds. 

2. **Output Overload:** The tool generates massive amounts of data. Without manual understanding, you risk pursuing false positives. 

3. **Missing Custom Applications:** Automated tools focus on common misconfigurations. They often miss logic flaws in custom, third-party, or internal software. 

## **The Ideal Workflow:** 

1. **Manual Sweep (Stealthy):** Execute `whoami /priv` , `cmdkey /list` , `net user` , `net localgroup administrators` , and check `%userprofile%` 

   - `\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_ history.txt` . 

2. **Automated Deep Dive:** If manual checks fail, utilize WinPEAS or PowerUp to scan deep registry and service permissions. 

## **Windows CMD Essential Commands** 

## **1. File & Directory Operations** 

- `dir /a` : Lists directory contents, including hidden and system files. Use `/s` to scan subdirectories. 

- `type <filename>` : Prints the contents of a text or configuration file. Useful for reading `.xml` or `.ini` files that may contain credentials. 

- `more <filename>` : Displays long output one screen at a time. 

- `findstr /si <string> <files>` : Searches for a specific string within files. Use `/s` for subdirectories and `/i` for case-insensitive matching. (e.g., `findstr /si password *.txt *.xml *.ini` ). 

## **2. Query Utility** 

Used to gather information about system state, specifically in Remote Desktop/Terminal Services environments: 

- `query user` (or `qwinsta` ): Lists currently logged-in users. 

- `query session` : Displays the state (Active, Disconnected, Listen) of all sessions. 

- `query process /session:<id>` : Lists processes running in a specific session. 

## **3. The** **`/f` Switch** 

- **Force:** `taskkill /f /im <process_name>` terminates a process; `del /f <filename>` deletes read-only files. 

- **Find:** `reg query HKLM /f "password" /t REG_SZ /s` searches for the string "password" throughout the registry. 

- **File Parsing:** `for /f %i in (ips.txt) do ping -n 1 %i` reads a file line-by-line to execute commands, useful for manual network sweeps. 

## **4. System & User Enumeration** 

- `whoami /priv` : Displays enabled privileges. 

- `systeminfo` : Shows OS version, architecture, and installed patches. 

- `net user <username>` : Displays user details and group memberships. 

- `net localgroup administrators` : Lists local administrators. 

## **5. Services, Processes & Scheduled Tasks** 

- `tasklist /v` : Lists processes with verbose user account information. 

- `sc query` : Lists services. 

- `sc qc <ServiceName>` : Retrieves service configuration, essential for checking `BINARY_PATH_NAME` for unquoted paths. 

- `schtasks /query /fo LIST /v` : Lists scheduled tasks. 

## **6. Network & Connectivity** 

- `netstat -ano` : Shows listening ports and associated PIDs. 

- `ipconfig /all` : Displays network configuration and domain details. 

- `arp -a` : Shows the ARP cache, identifying machines communicating on the local network to aid in pivoting. 

