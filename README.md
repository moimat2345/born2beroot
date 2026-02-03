<div align="center">
  <img src="https://raw.githubusercontent.com/ayogun/42-project-badges/main/covers/cover-born2beroot.png" alt="born2beroot" />
</div>

# Born2beRoot - System Administration & Virtualization

The `Born2beRoot` project introduces the fundamentals of **system administration** and **virtualization**. The goal is to set up a secure Linux server inside a virtual machine, following strict security policies and configuration requirements.

## Status
<div align="center">

![42 Bangkok](https://img.shields.io/badge/42-Bangkok-000000?style=for-the-badge&logo=42&logoColor=white)
![Debian](https://img.shields.io/badge/Debian-A81D33?style=for-the-badge&logo=debian&logoColor=white)
![VirtualBox](https://img.shields.io/badge/VirtualBox-183A61?style=for-the-badge&logo=virtualbox&logoColor=white)

<br>

### 🌍 Language / Langue

[![Français](https://img.shields.io/badge/🇫🇷_Lire_en_Français-blue?style=for-the-badge&logoColor=white)](README.fr.md)

</div>

---

## Overview

This project is an introduction to:
- **Virtualization** with VirtualBox (or UTM for Apple Silicon)
- **Linux system administration** (Debian or Rocky Linux)
- **Security policies** and hardening
- **User and permission management**
- **Automation with cron**

No graphical interface is allowed — everything is done via **command line**.

---

## 📋 Project Requirements

<table>
  <tr>
    <th>Operating System</th>
    <td>Debian (stable) or Rocky Linux (stable)</td>
  </tr>
  <tr>
    <th>Virtualization</th>
    <td>VirtualBox (or UTM for Apple Silicon)</td>
  </tr>
  <tr>
    <th>Graphical Interface</th>
    <td>❌ Forbidden (X.org or equivalent = grade 0)</td>
  </tr>
  <tr>
    <th>Firewall</th>
    <td>UFW (Debian) / firewalld (Rocky)</td>
  </tr>
  <tr>
    <th>SSH Port</th>
    <td>4242 only</td>
  </tr>
  <tr>
    <th>Security Module</th>
    <td>AppArmor (Debian) / SELinux (Rocky)</td>
  </tr>
  <tr>
    <th>Hostname</th>
    <td>Your login + 42 (e.g., <code>wil42</code>)</td>
  </tr>
  <tr>
    <th>Required User</th>
    <td>Your login (member of <code>user42</code> and <code>sudo</code> groups)</td>
  </tr>
  <tr>
    <th>Bonus</th>
    <td>❌ Not implemented</td>
  </tr>
</table>

---

## 📂 Project Structure

```
born2beroot/
├── 📄 signature.txt    # SHA1 signature of the VM disk
└── 📄 README.md        # Documentation
```

> ⚠️ The virtual machine is **not** included in the repository.
> Only the disk signature (`signature.txt`) is required for submission.

---

## 🔧 Partition Scheme (Mandatory)

The project requires at least **2 encrypted partitions** using LVM:

```
sda
├── sda1              # /boot (unencrypted)
├── sda2              # (extended partition)
└── sda5              # Encrypted LVM
    └── LVMGroup_crypt
        ├── root      # /
        ├── swap      # [SWAP]
        └── home      # /home
```

<details>
<summary><b>Bonus Partition Scheme (7 partitions)</b></summary>

```
sda
├── sda1              # /boot (500M)
├── sda2              # (extended partition)
└── sda5              # Encrypted LVM
    └── LVMGroup
        ├── root      # /
        ├── swap      # [SWAP]
        ├── home      # /home
        ├── var       # /var
        ├── srv       # /srv
        ├── tmp       # /tmp
        └── var-log   # /var/log
```

</details>

---

## 🔐 Security Configuration

<details open>
<summary><b>Click to see security rules</b></summary>

### Password Policy

| Rule | Value |
|:-----|:------|
| **Expiration** | Every 30 days |
| **Minimum days before change** | 2 days |
| **Warning before expiration** | 7 days |
| **Minimum length** | 10 characters |
| **Complexity** | Uppercase + lowercase + digit required |
| **Max consecutive identical chars** | 3 |
| **Username in password** | ❌ Forbidden |
| **Different from old password** | At least 7 characters *(does NOT apply to root)* |

### Sudo Configuration

| Rule | Description |
|:-----|:------------|
| **Authentication attempts** | 3 maximum |
| **Custom error message** | Displayed on wrong password |
| **Input/Output logging** | All commands archived in `/var/log/sudo/` |
| **TTY mode** | Required |
| **Secure path** | `/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin` |

### SSH Configuration

| Rule | Value |
|:-----|:------|
| **Port** | 4242 |
| **Root login** | ❌ Forbidden |

### Firewall (UFW / firewalld)

| Rule | Description |
|:-----|:------------|
| **Default policy** | Deny incoming |
| **Allowed port** | 4242 only (SSH) |
| **Status at boot** | Must be active |

</details>

---

## 📊 Monitoring Script (`monitoring.sh`)

A Bash script that runs automatically **every 10 minutes** via `cron`, broadcasting system information to all terminals using `wall`.

<details open>
<summary><b>Information displayed</b></summary>

| Metric | Description |
|:-------|:------------|
| **#Architecture** | OS and kernel version |
| **#CPU physical** | Number of physical processors |
| **#vCPU** | Number of virtual processors |
| **#Memory Usage** | Used/Total RAM (percentage) |
| **#Disk Usage** | Used/Total storage (percentage) |
| **#CPU load** | Current processor utilization |
| **#Last boot** | Date and time of last reboot |
| **#LVM use** | Active or not |
| **#Connections TCP** | Number of established connections |
| **#User log** | Number of logged users |
| **#Network** | IPv4 address and MAC address |
| **#Sudo** | Number of sudo commands executed |

</details>

### Example Output

```
Broadcast message from root@wil (tty1) (Sun Apr 25 15:45:00 2021):

#Architecture: Linux wil 4.19.0-16-amd64 #1 SMP Debian 4.19.181-1 (2021-03-19) x86_64 GNU/Linux
#CPU physical : 1
#vCPU : 1
#Memory Usage: 74/987MB (7.50%)
#Disk Usage: 1009/2Gb (49%)
#CPU load: 6.7%
#Last boot: 2021-04-25 14:45
#LVM use: yes
#Connections TCP : 1 ESTABLISHED
#User log: 1
#Network: IP 10.0.2.15 (08:00:27:51:9b:a5)
#Sudo : 42 cmd
```

---

## 🧪 Useful Commands

<details open>
<summary><b>Click to see commands</b></summary>

### User & Group Management

```bash
# Check user groups
groups <username>

# Add user to group
sudo usermod -aG <group> <username>

# Create new user
sudo adduser <username>

# Create new group
sudo groupadd <groupname>

# Check password policy for user
sudo chage -l <username>
```

### Service Management

```bash
# Check SSH status
sudo systemctl status ssh

# Check UFW status (Debian)
sudo ufw status

# Check firewalld status (Rocky)
sudo firewall-cmd --state
sudo firewall-cmd --list-all

# Check AppArmor status (Debian)
sudo aa-status

# Check SELinux status (Rocky)
sestatus
```

### Cron Management

```bash
# View cron jobs
sudo crontab -l

# Edit cron jobs
sudo crontab -e

# Stop monitoring without modifying script
sudo systemctl stop cron
# or
sudo /etc/init.d/cron stop
```

### Hostname

```bash
# Check hostname
hostnamectl

# Change hostname
sudo hostnamectl set-hostname <new_hostname>
# Also update /etc/hosts
```

### System Info

```bash
# Check partitions
lsblk

# Check LVM
sudo lvdisplay

# Check OS version
head -n 2 /etc/os-release
```

</details>

---

## 🎁 Bonus Part

<details>
<summary><b>Click to see bonus requirements</b></summary>

The bonus part includes:

1. **Extended partition scheme** (7 partitions as shown above)
2. **WordPress website** with:
   - lighttpd (web server)
   - MariaDB (database)
   - PHP
3. **Additional service of your choice** (NGINX/Apache2 excluded)

> ⚠️ Bonus is only evaluated if mandatory part is **PERFECT**

</details>

---

## 🧠 What I Learned

- Installing and configuring a Linux server from scratch
- Virtualization concepts with VirtualBox/UTM
- Differences between aptitude and apt
- What AppArmor/SELinux is and how it works
- User, group, and permission management
- Secure SSH and firewall configuration
- Password policies and sudo security rules
- Writing Bash scripts for system monitoring
- Scheduling tasks with cron
- LVM and encrypted disk partitioning

---

## 📄 Submission

Only one file is submitted:

```bash
signature.txt
```

This file contains the **SHA1 signature** of the virtual machine disk.

### Generate Signature

```bash
# Windows
certUtil -hashfile <disk.vdi> sha1

# Linux
sha1sum <disk.vdi>

# macOS (VirtualBox)
shasum <disk.vdi>

# macOS M1 (UTM)
shasum <vm_name.utm>/Images/disk-0.qcow2
```

### Default VM Locations

| OS | Path |
|:---|:-----|
| Windows | `%HOMEDRIVE%%HOMEPATH%\VirtualBox VMs\` |
| Linux | `~/VirtualBox VMs/` |
| macOS | `~/VirtualBox VMs/` |
| macOS M1 (UTM) | `~/Library/Containers/com.utmapp.UTM/Data/Documents/` |

> ⚠️ The signature may change after evaluation. Duplicate your VM or use save state.
>
> ⚠️ Snapshots are **FORBIDDEN** (grade = 0 if detected).

---

## 🚨 Disclaimer

**🇬🇧 This project has an educational purpose and you should under no circumstances copy and paste.**
**Cheat is bad. Don't cheat.**

**🇫🇷 Ce projet a un but éducatif et vous ne devez en aucun cas faire du copier-coller.**
**Tricher c'est mal. Ne trichez pas.**

---

<div align="center">

**Made with ❤️ for 42 Bangkok**

[![Back to top](https://img.shields.io/badge/⬆-Back_to_top-red?style=for-the-badge)](#born2beroot---system-administration--virtualization)

</div>
