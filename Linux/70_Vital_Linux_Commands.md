# 🐧 70+ Vital Linux Commands for Cybersecurity Analysts

> *This is a quick-reference survival guide bridging the gap between basic Linux administration and daily SOC operations. Curated for rapid incident response, log analysis, and system enumeration.*

---

## 📌 Overview
This cheat sheet contains essential Linux commands required for daily SOC operations, system administration, and rapid incident response. 

## 💻 Command Reference

| S.No | Command | Description | Practical Example |
| :--- | :--- | :--- | :--- |
| 1 | `pwd` | Prints the current working directory. | `pwd` displays the full path of the current directory. |
| 2 | `ls` | Lists directory contents. | `ls -l` lists files in long format, showing permissions, owner, size, and modification date.<br><br>`ls -a` lists files including hidden files (those starting with a dot).<br><br>`ls -la` list directory contents in a long format, including hidden files. |
| 3 | `cd` | Changes the current directory. | `cd /home/user` changes the directory to /home/user. |
| 4 | `touch` | Creates an empty file or updates the timestamp of an existing file. | `touch newfile.txt` creates an empty file named newfile.txt. |
| 5 | `echo` | Displays a line of text or a variable value. | `echo "Hello, World!"` prints Hello, World! to the terminal.<br><br>`echo "Hello, World!" > filename.txt` creates a new text file named "filename.txt" (or overwrites it if it already exists) and writes the phrase "Hello, World!" into it.<br><br>`echo "Hello, World!" >> filename.txt` appends the phrase "Hello, World!" to the end of the existing file named "filename.txt" (or creates the file if it doesn't exist). |
| 6 | `rm` | Removes files or directories. | `rm file.txt` deletes file.txt. |
| 7 | `cp` | Copies files or directories. | `cp file1.txt file2.txt` copies file1.txt to file2.txt.<br><br>`cp file1.txt ./Desktop` copies the file named "file1.txt" from the current directory to the Desktop folder. |
| 8 | `mv` | Used for moving and renaming files and directories. | `mv oldname.txt newname.txt` renames oldname.txt to newname.txt in the current directory.<br><br>`mv file1.txt ./Desktop` moves the file named "file1.txt" from the current directory to the Desktop folder. |
| 9 | `cat` | Concatenates and displays file content. | `cat file.txt` displays the content of file.txt. |
| 10 | `nano` | Open the Nano text editor. | `nano file.txt` opens file.txt in the Nano editor. |
| 11 | `vim` | Open the Vim text editor. | `vim file.txt` opens file.txt in the Vim editor. |
| 12 | `shred` | Overwrites a file to hide its contents and optionally deletes it. | `shred -u file.txt` overwrites and deletes file.txt. |
| 13 | `mkdir` | Creates a new directory. | `mkdir newdir` creates a directory named newdir. |
| 14 | `rmdir` | Removes an empty directory. | `rmdir olddir` removes the empty directory olddir. |
| 15 | `ln` | Creates hard and symbolic links. | `ln -s target linkname` creates a symbolic link named linkname pointing to target. |
| 16 | `clear` | Clears the terminal screen. | `clear` clears the terminal display. |
| 17 | `whoami` | Displays the current logged-in user. | `whoami` shows the username of the current user. |
| 18 | `useradd` | Adds a new user. | `sudo useradd newuser` adds a new user named newuser. |
| 19 | `sudo` | Executes a command as another user, typically the superuser. | `sudo apt-get update` runs the `apt-get update` command with superuser privileges. |
| 20 | `adduser` | Adds a new user with a more interactive interface. | `sudo adduser newuser` interactively adds a new user named newuser. |
| 21 | `su` | Switch to another user account. | `su - user` switches to the `user` account. |
| 22 | `exit` | Exits the current shell or session. | `exit` logs out of the current session. |
| 23 | `passwd` | Changes a user's password. | `passwd` prompts to change the current user's password. |
| 24 | `apt` | Manages packages on Debian-based systems. | `sudo apt install package` installs the specified package.<br><br>`sudo apt remove package` removes the specified package.<br><br>`apt update` update the package list<br><br>`apt upgrade` upgrade installed packages to their latest versions<br><br>`apt dist-upgrade` perform a comprehensive system upgrade |
| 25 | `ssh` | Connects to a remote machine via SSH. | `ssh user@hostname` connects to the remote machine `hostname` as `user`. |
| 26 | `finger` | Displays information about system users. | `finger user` shows details about `user`. |
| 27 | `man` | Displays the manual page for a command. | `man ls` shows the manual for the `ls` command. |
| 28 | `whatis` | Displays a brief description of a command. | `whatis ls` provides a short description of the `ls` command. |
| 29 | `curl` | Transfers data from or to a server. | `curl -O http://example.com/file.txt` downloads `file.txt` from the specified URL. |
| 30 | `zip` | Compresses files into a zip archive. | `zip archive.zip file1 file2` compresses `file1` and `file2` into `archive.zip`. |
| 31 | `unzip` | Extracts files from a zip archive. | `unzip archive.zip` extracts files from `archive.zip`. |
| 32 | `less` | Views file content one screen at a time. | `less file.txt` displays `file.txt` content one screen at a time. |
| 33 | `head` | Displays the first part of a file. | `head -n 10 file.txt` shows the first 10 lines of `file.txt`. |
| 34 | `tail` | Displays the last part of a file. | `tail -n 10 file.txt` shows the last 10 lines of `file.txt`. |
| 35 | `cmp` | Compare two files byte by byte. | `cmp file1 file2` compares `file1` and `file2`. |
| 36 | `diff` | Compares files line by line. | `diff file1 file2` shows the differences between `file1` and `file2`. |
| 37 | `sort` | Sorts lines of text files. | `sort file.txt` sorts the lines in `file.txt`. |
| 38 | `find` | Searches for files in a directory hierarchy. | `find /home -name "*.txt"` finds all `.txt` files in the `/home` directory. |
| 39 | `chmod` | Changes file permissions. | `chmod 755 script.sh` sets the permissions of `script.sh` to `rwxr-xr-x`. |
| 40 | `chown` | Changes file owner and group. | `chown user:group file.txt` changes the owner and group of `file.txt` to `user` and `group`. |
| 41 | `ifconfig` | Display network interface information. Configures network interfaces. | `ifconfig eth0` displays the configuration of the `eth0` interface. |
| 42 | `ip address` | Displays IP addresses and interfaces. | `ip address show` shows all IP addresses and network interfaces. |
| 43 | `grep` | Searches for patterns in files. | `grep "pattern" file.txt` searches for "pattern" in `file.txt`. |
| 44 | `awk` | A programming language for pattern scanning and processing. | `awk '{print $1}' file.txt` prints the first field of each line in `file.txt`. |
| 45 | `resolvectl status` | Shows the current DNS settings. | `resolvectl status` displays the DNS configuration and status. |
| 46 | `ping` | Sends ICMP ECHO_REQUEST packets to network hosts. | `ping google.com` sends ping requests to `google.com`. |
| 47 | `netstat` | Displays network connections, routing tables, and interface statistics. | `netstat -tuln` shows listening ports and their status. |
| 48 | `ss` | Displays socket statistics. | `ss -tuln` shows listening sockets.<br><br>`ss -l4p` displays all listening IPv4 sockets along with the associated processes |
| 49 | `iptables` | Configures packet filtering rules. | `sudo iptables -L` lists all current iptables rules. |
| 50 | `ufw` | Manages firewall with Uncomplicated Firewall. | `sudo ufw enable` enables the firewall. |
| 51 | `uname` | Prints system information. | `uname -a` displays all system information. |
| 52 | `neofetch` | Displays system information with an aesthetic layout. | `neofetch` shows system information in a visually appealing format. |
| 53 | `cal` | Displays a calendar. | `cal` shows the current month's calendar. |
| 54 | `free` | Displays memory usage. | `free -h` shows memory usage in a human-readable format. |
| 55 | `df` | Displays disk space usage of file systems. | `df -h` shows disk space usage in a human-readable format. |
| 56 | `ps` | Displays information about active processes. | `ps aux` shows detailed information about all running processes. |
| 57 | `top` | Displays real-time system resource usage. | `top` shows real-time processes and system resource usage. |
| 58 | `htop` | An interactive process viewer. | `htop` provides an interactive view of system processes. |
| 59 | `kill` | Terminates a process by PID. | `kill 1234` terminates the process with PID 1234. |
| 60 | `pkill` | Terminates processes by name. | `pkill firefox` terminates all processes named `firefox`. |
| 61 | `systemctl` | Manages systemd services. | `systemctl status nginx` shows the status of the `nginx` service. |
| 62 | `history` | Displays the command history. | `history` shows the list of previously executed commands. |
| 63 | `reboot` | Reboots the system. | `sudo reboot` restarts the system. |
| 64 | `shutdown` | Shuts down or reboots the system. | `sudo shutdown -h now` shuts down the system immediately. |
| 65 | `traceroute` | Traces the route packets take to a network host. | `traceroute google.com` shows the route to `google.com`. |
| 66 | `dig` | Queries DNS servers. | `dig example.com` retrieves DNS information for `example.com`. |
| 67 | `host` | Performs DNS lookups. | `host example.com` shows the IP address of `example.com`. |
| 68 | `arp` | Displays and modifies the ARP table. | `arp -a` shows the current ARP table. |
| 69 | `iwconfig` | Configures wireless network interfaces. | `iwconfig wlan0` shows the configuration of the `wlan0` wireless interface. |
| 70 | `hostname` | Displays or sets the system's hostname. | `hostname` shows the current hostname. |
| 71 | `whois` | Queries the WHOIS database for domain information. | `whois example.com` retrieves WHOIS information for `example.com`. |
