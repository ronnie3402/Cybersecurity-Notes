# 🐧 70+ Vital Linux Commands for Cybersecurity Analysts

## 📌 Overview
This cheat sheet contains essential Linux commands required for daily SOC operations, system administration, and rapid incident response. 

## 💻 Command Reference

| S.No | Command | Description | Practical Example |
| :--- | :--- | :--- | :--- |
| 1 | `pwd` | Prints the full path of the current working directory. | `pwd` |
| 2 | `ls` | Lists directory contents. | `ls -la` (lists contents in long format, including hidden files). |
| 3 | `cd` | Changes the current directory. | `cd /home/user` (changes the directory to /home/user). |
| 4 | `touch` | Creates an empty file or updates the timestamp of an existing file. | `touch newfile.txt` (creates an empty file named newfile.txt). |
| 5 | `echo` | Displays a line of text or a variable value. | `echo "Hello, World!" > file.txt` (creates file and writes phrase). |
| 6 | `rm` | Removes files or directories. | `rm file.txt` (deletes file.txt). |
| 7 | `cp` | Copies files or directories. | `cp file1.txt ./Desktop` (copies file to Desktop). |
| 8 | `mv` | Used for moving and renaming files and directories. | `mv oldname.txt newname.txt` (renames file in current directory). |
| 9 | `nano` | Opens the Nano text editor. | `nano file.txt` (opens file.txt in the Nano editor). |
| 10 | `vim` | Opens the Vim text editor. | `vim file.txt` (opens file.txt in the Vim editor). |
| 11 | `cat` | Concatenates and displays file content. | `cat file.txt` (displays the content of file.txt). |
| 12 | `shred` | Overwrites a file to hide its contents and optionally deletes it. | `shred -u file.txt` (overwrites and deletes file.txt). |
| 13 | `mkdir` | Creates a new directory. | `mkdir newdir` (creates a directory named newdir). |
| 14 | `rmdir` | Removes an empty directory. | `rmdir olddir` (removes the empty directory olddir). |
| 15 | `ln` | Creates hard and symbolic links. | `ln -s target linkname` (creates a symbolic link to target). |
| 16 | `clear` | Clears the terminal screen. | `clear` |
| 17 | `whoami` | Displays the current logged-in user. | `whoami` |
| 18 | `useradd` | Adds a new user. | `sudo useradd newuser` |
| 19 | `sudo` | Executes a command as another user, typically the superuser. | `sudo apt-get update` |
| 20 | `adduser` | Adds a new user with a more interactive interface. | `sudo adduser newuser` |
| 21 | `su` | Switch to another user account. | `su user` |
| 22 | `exit` | Exits the current shell or session. | `exit` |
| 23 | `passwd` | Changes a user's password. | `passwd` |
| 24 | `apt` | Manages packages on Debian-based systems. | `sudo apt install package` (installs the specified package). |
| 25 | `ssh` | Connects to a remote machine via SSH. | `ssh user@hostname` (connects to remote machine hostname as user). |
| 26 | `finger` | Displays information about system users. | `finger user` |
| 27 | `man` | Displays the manual page for a command. | `man ls` (shows the manual for the ls command). |
| 28 | `whatis` | Displays a brief description of a command. | `whatis ls` |
| 29 | `curl` | Transfers data from or to a server. | `curl -O http://example.com/file.txt` (downloads file). |
| 30 | `zip` | Compresses files into a zip archive. | `zip archive.zip file1 file2` |
| 31 | `unzip` | Extracts files from a zip archive. | `unzip archive.zip` |
| 32 | `less` | Views file content one screen at a time. | `less file.txt` |
| 33 | `head` | Displays the first part of a file. | `head -n 10 file.txt` (shows the first 10 lines). |
| 34 | `tail` | Displays the last part of a file. | `tail -n 10 file.txt` (shows the last 10 lines). |
| 35 | `cmp` | Compare two files byte by byte. | `cmp file1 file2` (compares file1 and file2). |
| 36 | `diff` | Compares files line by line. | `diff file1 file2` (shows the differences between file1 and file2). |
| 37 | `sort` | Sorts lines of text files. | `sort file.txt` (sorts the lines in file.txt). |
| 38 | `find` | Searches for files in a directory hierarchy. | `find /home -name "*.txt"` (finds all .txt files in /home). |
| 39 | `chmod` | Changes file permissions. | `chmod 755 script.sh` (sets permissions to rwxr-xr-x). |
| 40 | `chown` | Changes file owner and group. | `chown user:group file.txt` |
| 41 | `ifconfig` | Displays network interface information. | `ifconfig eth0` |
| 42 | `ip address` | Displays IP addresses and interfaces. | `ip address show` |
| 43 | `grep` | Searches for patterns in files. | `grep "pattern" file.txt` |
| 44 | `awk` | A programming language for pattern scanning and processing. | `awk '{print $1}' file.txt` |
| 45 | `resolvectl status`| Shows the current DNS settings. | `resolvectl status` |
| 46 | `ping` | Sends ICMP ECHO_REQUEST packets to network hosts. | `ping google.com` |
| 47 | `netstat` | Displays network connections, routing tables, and interface statistics. | `netstat -tuln` (shows listening ports and their status). |
| 48 | `ss` | Displays socket statistics. | `ss -l4p` (displays all listening IPv4 sockets along with processes). |
| 49 | `iptables` | Configures packet filtering rules. | `sudo iptables -L` (lists all current iptables rules). |
| 50 | `ufw` | Manages firewall with Uncomplicated Firewall. | `sudo ufw enable` (enables the firewall). |
| 51 | `uname` | Prints system information. | `uname -a` (displays all system information). |
| 52 | `neofetch` | Displays system information with an aesthetic layout. | `neofetch` |
| 53 | `cal` | Displays a calendar. | `cal` (shows the current month's calendar). |
| 54 | `free` | Displays memory usage. | `free -h` (shows memory usage in human-readable format). |
| 55 | `df` | Displays disk space usage of file systems. | `df -h` (shows disk space usage in human-readable format). |
| 56 | `ps` | Displays information about active processes. | `ps aux` (shows detailed information about all running processes). |
| 57 | `top` | Displays real-time system resource usage. | `top` |
| 58 | `htop` | An interactive process viewer. | `htop` |
| 59 | `kill` | Terminates a process by PID. | `kill 1234` |
| 60 | `pkill` | Terminates processes by name. | `pkill firefox` |
| 61 | `systemctl` | Manages systemd services. | `systemctl status nginx` |
| 62 | `history` | Displays the command history. | `history` |
| 63 | `reboot` | Reboots the system. | `sudo reboot` |
| 64 | `shutdown` | Shuts down or reboots the system. | `sudo shutdown -h now` |
| 65 | `traceroute`| Traces the route packets take to a network host. | `traceroute google.com` |
| 66 | `dig` | Queries DNS servers. | `dig example.com` |
| 67 | `host` | Performs DNS lookups. | `host example.com` |
| 68 | `arp` | Displays and modifies the ARP table. | `arp -a` |
| 69 | `iwconfig` | Configures wireless network interfaces. | `iwconfig wlan0` |
| 70 | `hostname` | Displays or sets the system's hostname. | `hostname` |
| 71 | `whois` | Queries the WHOIS database for domain information. | `whois example.com` |
