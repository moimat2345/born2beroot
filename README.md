# 🖥️ Born2beRoot

## 📌 Description

**Born2beRoot** is a system administration project from **42 School**.  
The goal is to set up a **secure Linux server** using virtualization, while following strict security and configuration rules.

This project introduces the basics of:
- Virtualization
- Linux system administration
- Security policies
- User and permission management
- Automation with cron

---

## 📌 Description (FR)

**Born2beRoot** est un projet d’administration système de **l’école 42**.  
L’objectif est de mettre en place un **serveur Linux sécurisé** via une machine virtuelle, en respectant des règles strictes de configuration et de sécurité.

Ce projet permet de découvrir :
- La virtualisation
- L’administration système Linux
- Les politiques de sécurité
- La gestion des utilisateurs et des permissions
- L’automatisation avec cron

---

## ⚙️ Technical Setup

- **Operating System**: Debian (stable)
- **Virtualization**: VirtualBox
- **Graphical interface**: ❌ None (CLI only)
- **Firewall**: UFW
- **SSH**: Enabled (port `4242`)
- **Security module**: AppArmor
- **Bonus**: ❌ Not implemented

---

## 🗂️ Project Structure

```bash
.
├── signature.txt
└── README.md
```

> ⚠️ The virtual machine is **not** included in the repository.  
> Only the disk signature is required for evaluation.

---

## 🔐 Security Rules Implemented

- Strong password policy
- Password expiration and warnings
- `sudo` configuration with:
  - Limited authentication attempts
  - Custom error message
  - Full input/output logging
- SSH access disabled for `root`
- Firewall allowing only required ports
- User added to `sudo` and `user42` groups

---

## 📊 Monitoring Script

A Bash script named `monitoring.sh` is executed automatically **every 10 minutes** using `cron`.

It displays system information on all terminals using `wall`.

### Information displayed:
- OS architecture and kernel version
- Physical and virtual CPU count
- RAM usage
- Disk usage
- CPU load
- Last reboot
- LVM status
- Active TCP connections
- Logged users
- Network information (IP & MAC)
- Number of sudo commands executed

---

## 🧪 Useful Commands

### Check cron jobs
```bash
crontab -l
```

### Check firewall status
```bash
sudo ufw status
```

### Check SSH service
```bash
sudo systemctl status ssh
```

### Check sudo logs
```bash
ls /var/log/sudo/
```

---

## 🧠 What I Learned

- Installing and configuring a Linux server from scratch
- Virtualization with VirtualBox
- User, group, and permission management
- Secure SSH and firewall configuration
- Password policies and sudo rules
- Writing automation scripts in Bash
- Scheduling tasks with cron
- Understanding system monitoring tools

---

## 📄 Submission

Only the following file is submitted:

```bash
signature.txt
```

This file contains the **SHA1 signature** of the virtual machine disk, as required by the subject.

---

## 👤 Author

**Matéo Nuskovski**  
42 School — Born2beRoot project
