# 🐧 70+ Vital Linux Commands for Cybersecurity Analysts

> *This is a quick-reference survival guide bridging the gap between basic Linux administration and daily SOC operations. Curated for rapid incident response, log analysis, and system enumeration.*

---

## 📌 Overview
This cheat sheet contains essential Linux commands required for daily SOC operations, system administration, and rapid incident response. 

## 💻 Command Reference

## 📂 1. Directory & File Operations

### Navigation & Directory Creation
| Command | Description | Practical Example / Notes |
| :--- | :--- | :--- |
| `pwd` | Displays the present working directory. | `pwd` |
| `ls` | Lists directory contents. | `ls -la` (all files, hidden files, permissions), `ls -l` (long format). |
| `tree` | Shows all folders and subfolders within it in a tree structure. | `tree /var/log` |
| `cd` | Changes the current working directory. | `cd /var/log` (path), `cd ..` (up one level), `cd ~` (home folder). |
| `mkdir` | Creates a new directory. | `mkdir newdir`, `mkdir -p x/y/z` (creates parent directories too). |
| `rmdir` | Removes an empty directory. | `rmdir olddir` |
| `touch` | Creates an empty file or updates the timestamp. | `touch newfile.txt` |

### Advanced File Manipulations (Copy, Move, Remove)
| Command | Description | Practical Example / Notes |
| :--- | :--- | :--- |
| `cp` | Copies files or directories. | `cp file1 file2` (copies/overwrites).<br>`cp -r dir1 dir2` (Recursive copy).<br>`cp -rv dir1 dir2` (Recursive & Verbose).<br>`sudo cp -a -r dir1 dir2` (Archive: preserves permissions, ownership, timestamps).<br>`sudo cp -i -r dir1 dir2` (Interactive: asks before overwrite). |
| `mv` | Moves and/or renames files and directories. | `mv oldname newname` (renames).<br>`mv file1 /Desktop/` (moves). |
| `rm` | Removes files or directories. | `rm file.txt` (deletes file).<br>`rm -r directory` (deletes directory). |
| `shred` | Secure deletion by overwriting with garbage data. | `shred -u file.txt` (overwrites and deletes, preventing recovery). |
| `ln` | Creates hard and symbolic links. | `ln -s target linkname` |

### File Viewing, Editing & Text Processing
| Command | Description | Practical Example / Notes |
| :--- | :--- | :--- |
| `cat` | Concatenates and displays file content. | `cat file.txt` (view).<br>`cat > file.txt` (write text manually, press Ctrl+D to save). |
| `less` | Views file content one screen/page at a time. | `less /var/log/syslog` |
| `head` | Displays the first part of a file. | `head -n 10 file.txt` |
| `tail` | Displays the last part of a file. | `tail -n 10 file.txt`<br>`tail -f /var/log/auth.log` (**Crucial:** Live log monitoring). |
| `echo` | Displays a line of text or writes to a file. | `echo "msg" > file.txt` (overwrites).<br>`echo "msg" >> file.txt` (appends). |
| `printf` | Used to write formatted scripts. | `printf "Name: Rohit\n" > info.txt` |
| `nano` / `vim` | Terminal text editors. | `nano file.txt` or `vim file.txt` |
| `cmp` / `diff`| Compares files. | `cmp file1 file2` (byte by byte).<br>`diff file1 file2` (line by line). |
| `sort` | Sorts lines of text files. | `sort file.txt` |
| `awk` | Programming language for pattern scanning. | `awk '{print $1}' file.txt` (prints first field). |

---

## 🔐 2. Permissions & Ownership

### Changing Permissions (`chmod`)
*Format: Read (`r`=4), Write (`w`=2), Execute (`x`=1)*

| Command | Description | Practical Example / Notes |
| :--- | :--- | :--- |
| `chmod` | Changes file permissions. | `chmod 755 script.sh` (User=7, Group=5, Other=5).<br>`chmod u=rwx,g=rx,o=rx file.txt` (Explicit method).<br>`chmod g+w file.txt` (Adds write permission to group).<br>`chmod -R 766 dir/` (Recursive directory permission change). |

### Changing Ownership (`chown`)
| Command | Description | Practical Example / Notes |
| :--- | :--- | :--- |
| `chown` | Changes file owner and group. | `chown user:group file.txt` (changes both).<br>`chown -R user:group dir/` (Recursive).<br>`chown user file.txt` (user only).<br>`chown :group file.txt` (group only). |

---

## 👤 3. User & Group Management

### Users & Groups
| Command | Description | Practical Example / Notes |
| :--- | :--- | :--- |
| `whoami` | Displays current logged-in user. | `whoami` |
| `finger` | Displays information about system users. | `finger user` |
| `su` / `sudo` | Switch user / Execute as superuser. | `su - username` (switch user).<br>`sudo -su username` (force switch using your admin password). |
| `useradd` | Adds a new user. | `useradd username` (no home dir).<br>`useradd -m username` (with home dir). |
| `adduser` | Adds a new user interactively. | `sudo adduser newuser` |
| `userdel` | Deletes a user. | `userdel username` |
| `usermod` | Modifies a user account. | `usermod -aG group user` (adds user to group). |
| `groupadd` / `groupdel`| Adds / Deletes a group. | `sudo groupadd -r sysgrp` (creates system group). |
| `getent group` | See all groups on the system. | `getent group` |
| `compgen -u` | See all usernames on the system. | `compgen -u` |
| `exit` | Exits current shell. | `exit` |

### Password Aging & Policies (`passwd` & `chage`)
| Command | Description | Practical Example / Notes |
| :--- | :--- | :--- |
| `passwd` | Manages passwords. | `passwd username` (change/add).<br>`passwd -d username` (passwordless).<br>`passwd -l username` (locks account).<br>`passwd -u username` (unlocks account). |
| `chage` | Password expiry and aging policies. | `chage -d 0 username` (force change on next login).<br>`chage -M 60 username` (set 60-day expiry limit).<br>`chage -W 7 username` (7-day warning before expiry).<br>`sudo chage -d $(date +%Y-%m-%d) user` (**Pro Trick:** Resets countdown cycle to today without changing actual password). |

---

## ⚙️ 4. Package Management (Debian/Ubuntu)

| Command | Description | Practical Example / Notes |
| :--- | :--- | :--- |
| `apt` | Manages packages. | `sudo apt update` (fetches latest lists).<br>`sudo apt upgrade` (installs updates).<br>`sudo apt install package` (installs).<br>`sudo apt remove package` (removes).<br>`apt dist-upgrade` (comprehensive upgrade). |

---

## 🔄 5. Process Management & Services

### Processes
| Command | Description | Practical Example / Notes |
| :--- | :--- | :--- |
| `ps` | Displays active processes. | `ps aux` (ALL running processes).<br>`ps -f t pts/0` (processes for a specific terminal). |
| `top` / `htop` | Real-time system resource & process monitor. | `top` (exit with 'q'). `htop` is interactive. |
| `pgrep` | Gets Process IDs (PIDs). | `pgrep -l -u username` (gets PIDs for a specific user). |
| `kill` | Terminates process by PID. | `kill 1234`<br>`sudo kill -9 PID` (Force kill). |
| `pkill` | Terminates process by name/user. | `pkill firefox`<br>`sudo pkill -u username` (kicks a user completely). |

### Services (Systemd)
*Note: We are using `apache2` as the example web service.*

| Command | Description | Practical Example / Notes |
| :--- | :--- | :--- |
| `systemctl` | Manages systemd services. | `systemctl start apache2`<br>`systemctl stop apache2`<br>`systemctl enable apache2` (launches on boot).<br>`systemctl disable apache2`<br>`systemctl status apache2` (dashboard/troubleshooting).<br>`systemctl restart apache2` |

---

## 🌐 6. Networking & Advanced Searching

### Network Configuration & Troubleshooting
| Command | Description | Practical Example / Notes |
| :--- | :--- | :--- |
| `ip address` / `ifconfig` | Displays network interfaces and IPs. | `ip address show` |
| `ping` | Sends ICMP echo requests. | `ping google.com` |
| `netstat` | Displays network connections/ports. | `netstat -tuln` (shows listening ports). |
| `ss` | Socket statistics (modern netstat). | `ss -tuln`<br>`ss -l4p` (IPv4 sockets with processes). |
| `iptables` | Packet filtering rules. | `sudo iptables -L` |
| `ufw` | Uncomplicated Firewall manager. | `sudo ufw enable` |
| `hostname` / `hostnamectl`| Displays/sets system hostname. | `hostnamectl` (Shows OS, Kernel version, static name). |
| `sudo nmtui` | Text-based GUI for NetworkManager. | `sudo nmtui` |
| `resolvectl status` | Shows current DNS settings. | `resolvectl status` |
| `traceroute` | Traces route to a host. | `traceroute google.com` |
| `dig` / `host` | DNS lookups and queries. | `dig example.com`<br>`host example.com` |
| `arp` | Displays/modifies ARP table. | `arp -a` |
| `iwconfig` | Configures wireless interfaces. | `iwconfig wlan0` |
| `whois` | Queries domain WHOIS database. | `whois example.com` |
| `ssh` | Connects to a remote machine. | `ssh user@hostname` |

### Advanced Searching (`find` & `grep`)
| Command | Description | Practical Example / Notes |
| :--- | :--- | :--- |
| `find` | Searches for files in directory hierarchy. | `find / -name file.txt` (searches entire drive).<br>`find /home/user -name "*.txt"` |
| `grep` | Searches for patterns in files. | `grep "root" /etc/passwd` |
| `Pipeline Search` | Combines `find` and `grep` using `xargs`. | `find . -name "*.txt" \| xargs grep "password"` (Finds all text files and searches inside them for 'password'). |

---

## 💻 7. System Information & Utilities

| Command | Description | Practical Example / Notes |
| :--- | :--- | :--- |
| `uname` | Prints system information. | `uname -a` |
| `neofetch` | System info with aesthetic layout. | `neofetch` |
| `free` | Displays memory usage. | `free -h` (human-readable). |
| `df` | Displays disk space usage. | `df -h` |
| `cal` | Displays a calendar. | `cal` |
| `clear` | Clears terminal screen. | `clear` |
| `history` | Command history manager. | `history`<br>`history -d [PID]` (Deletes specific command).<br>`history -c` (Clears all history). |
| `alias` | Creates command shortcuts. | `alias update='sudo apt update'` |
| `man` / `whatis` | Manuals and descriptions. | `man ls`<br>`whatis ls` |
| `reboot` / `shutdown`| Restart or power off. | `sudo reboot`<br>`sudo shutdown -h now` |

### Archiving & Transfers
| Command | Description | Practical Example / Notes |
| :--- | :--- | :--- |
| `curl` | Transfers data from/to server. | `curl -O http://example.com/file.txt` |
| `zip` / `unzip` | Compresses/extracts archives. | `zip archive.zip file1 file2`<br>`unzip archive.zip` |

---

## 💡 Pro-Tips & Text Editor Shortcuts

### Getting Quick Help
If you don't want to read a full `man` page, you can append `--help` to almost any command to see its available options instantly.
- `ls --help`
- `cat --help`

### Changing Hostname Permanently
While `hostnamectl` shows system info, you can use it to rename your server permanently without editing config files manually:
- `sudo hostnamectl set-hostname new-server-name`

### 📝 Nano Editor Essential Keys
- **Start typing:** Just type directly.
- **Save file:** `Ctrl + O` -> `Enter` (to confirm filename)
- **Exit nano:** `Ctrl + X`
- **Cut/Paste line:** `Ctrl + K` (Cut), `Ctrl + U` (Paste)
- **Search text:** `Ctrl + W`
- **Undo:** `Alt + U`

### 📝 Vi / Vim Editor Essential Keys
- **Insert Mode:** `i` (Start typing)
- **Command Mode:** `Esc` (Stop typing and issue commands)
- **Save:** `:w`
- **Quit:** `:q`
- **Save & Quit:** `:wq`
