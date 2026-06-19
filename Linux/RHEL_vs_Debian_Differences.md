# 🔴 RHEL vs Debian/Ubuntu: Key Differences for SOC Analysts

> *These notes provide us an enterprise-focused technical baseline detailing the operational, package management, and security-level differences between standard Debian distributions and Red Hat Enterprise infrastructure.*

---

## 📌 Overview of RHEL (Red Hat Enterprise Linux)
Red Hat Enterprise Linux (RHEL) is a premium, enterprise-grade, open-source operating system created by Red Hat. It is known for its extreme stability, rigorous testing, and Long-Term Support (LTS) lifecycles.

**Why Top Companies Use RHEL:**
- **Rock-Solid Stability:** It is designed for mission-critical workloads without unexpected crashes.
- **Enterprise Security:** Comes pre-configured with SELinux (Security-Enhanced Linux), providing military-grade Mandatory Access Control (MAC).
- **Official Support:** Large corporations (Banks, Healthcare, Government) prefer RHEL because it comes with guaranteed technical support and strict compliance certifications.

## 🔄 Core Commands Integration
Because all Linux distributions follow standard architecture rules (POSIX compliance), **over 80% of your daily command-line tasks remain exactly the same.** Core commands for navigation (`cd`, `pwd`), file manipulation (`grep`, `awk`, `cat`, `find`), and process management (`ps`, `top`, `kill`) do not change. However, the differences emerge when an analyst has to perform system administration, package management, or configure security policies.

## ⚖️ Command & Service Comparison Table

Below is a quick reference guide highlighting the operational differences between Debian-based systems (Ubuntu, Kali Linux) and RHEL-based systems (Red Hat, CentOS, Fedora).

| Feature / Task | Debian / Ubuntu (Kali) | RHEL / CentOS / Fedora | Description / Practical Note |
| :--- | :--- | :--- | :--- |
| **Package Manager** | `apt` / `apt-get` | `dnf` / `yum` | Used to install, remove, and manage software packages. (`dnf` is the modern replacement for `yum`). |
| **Update System** | `sudo apt update && sudo apt upgrade` | `sudo dnf update` | Fetches the latest package versions and applies updates. |
| **Install Package** | `sudo apt install <package>` | `sudo dnf install <package>` | Example: Installing Nmap or Wireshark. |
| **Firewall Service** | `ufw` (Uncomplicated Firewall) | `firewalld` | Manages network traffic. In RHEL, you use `firewall-cmd --add-port=80/tcp` to open ports. |
| **SSH Daemon Service** | `ssh` | `sshd` | To check SSH status: In Ubuntu it's `systemctl status ssh`, but in RHEL it's `systemctl status sshd`. |
| **Web Server Service** | `apache2` | `httpd` | If you are analyzing web server logs, the service name and folder paths differ (`/var/log/apache2/` vs `/var/log/httpd/`). |
| **Network Management** | `netplan` | `nmcli` (NetworkManager) | `nmcli` is heavily used in RHEL to configure IP addresses and network interfaces directly from the terminal. |
| **Kernel Security Module**| `AppArmor` | `SELinux` | RHEL uses SELinux by default. Commands like `sestatus` and `setenforce 0` are crucial during troubleshooting. |

## 🛡️ SELinux (Security-Enhanced Linux) Basics
Understanding SELinux is critical when dealing with enterprise RHEL servers. It operates on a **Mandatory Access Control (MAC)** architecture, meaning even if a user or process has `root` privileges, SELinux policies can still restrict their actions to prevent a total system compromise.

### Essential SELinux Commands for Troubleshooting:

| Command | Description | Practical SOC Use Case |
| :--- | :--- | :--- |
| `sestatus` | Displays the detailed status of SELinux. | Used to check if SELinux is enabled, the current mode, and the loaded policy. |
| `getenforce` | Prints only the current operational mode. | Quickly returns `Enforcing`, `Permissive`, or `Disabled`. |
| `sudo setenforce 0` | Temporarily switches SELinux to `Permissive` mode. | **Crucial for Troubleshooting:** If a service is failing, switch to permissive. If the service starts working, you know an SELinux policy is blocking it. (Note: Reverts to Enforcing on system reboot). |
| `sudo setenforce 1` | Switches SELinux back to `Enforcing` mode. | Immediately re-enables the strict security policies after your troubleshooting is complete. |
| `ls -Z` | Lists files along with their SELinux security contexts. | Used to see the specific user, role, and type contexts assigned to a file, which helps in writing custom allow rules. |
