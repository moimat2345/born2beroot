<div align="center">
  <img src="https://raw.githubusercontent.com/ayogun/42-project-badges/main/covers/cover-born2beroot.png" alt="born2beroot" />
</div>

# Born2beRoot - Administration Système & Virtualisation

Le projet `Born2beRoot` introduit les fondamentaux de l'**administration système** et de la **virtualisation**. L'objectif est de configurer un serveur Linux sécurisé dans une machine virtuelle, en respectant des politiques de sécurité et des règles de configuration strictes.

## Statut
<div align="center">

![42 Bangkok](https://img.shields.io/badge/42-Bangkok-000000?style=for-the-badge&logo=42&logoColor=white)
![Debian](https://img.shields.io/badge/Debian-A81D33?style=for-the-badge&logo=debian&logoColor=white)
![VirtualBox](https://img.shields.io/badge/VirtualBox-183A61?style=for-the-badge&logo=virtualbox&logoColor=white)

<br>

### 🌍 Language / Langue

[![English](https://img.shields.io/badge/🇬🇧_Read_in_English-blue?style=for-the-badge&logoColor=white)](README.md)

</div>

---

## Présentation

Ce projet est une introduction à :
- La **virtualisation** avec VirtualBox (ou UTM pour Apple Silicon)
- L'**administration système Linux** (Debian ou Rocky Linux)
- Les **politiques de sécurité** et le durcissement
- La **gestion des utilisateurs et des permissions**
- L'**automatisation avec cron**

Aucune interface graphique n'est autorisée — tout se fait en **ligne de commande**.

---

## 📋 Exigences du Projet

<table>
  <tr>
    <th>Système d'exploitation</th>
    <td>Debian (stable) ou Rocky Linux (stable)</td>
  </tr>
  <tr>
    <th>Virtualisation</th>
    <td>VirtualBox (ou UTM pour Apple Silicon)</td>
  </tr>
  <tr>
    <th>Interface graphique</th>
    <td>❌ Interdite (X.org ou équivalent = note 0)</td>
  </tr>
  <tr>
    <th>Pare-feu</th>
    <td>UFW (Debian) / firewalld (Rocky)</td>
  </tr>
  <tr>
    <th>Port SSH</th>
    <td>4242 uniquement</td>
  </tr>
  <tr>
    <th>Module de sécurité</th>
    <td>AppArmor (Debian) / SELinux (Rocky)</td>
  </tr>
  <tr>
    <th>Hostname</th>
    <td>Votre login + 42 (ex: <code>wil42</code>)</td>
  </tr>
  <tr>
    <th>Utilisateur requis</th>
    <td>Votre login (membre des groupes <code>user42</code> et <code>sudo</code>)</td>
  </tr>
  <tr>
    <th>Bonus</th>
    <td>❌ Non implémenté</td>
  </tr>
</table>

---

## 📂 Structure du Projet

```
born2beroot/
├── 📄 signature.txt    # Signature SHA1 du disque de la VM
└── 📄 README.md        # Documentation
```

> ⚠️ La machine virtuelle n'est **pas** incluse dans le dépôt.
> Seule la signature du disque (`signature.txt`) est requise pour la soumission.

---

## 🔧 Schéma de Partitionnement (Mandatory)

Le projet requiert au moins **2 partitions chiffrées** utilisant LVM :

```
sda
├── sda1              # /boot (non chiffré)
├── sda2              # (partition étendue)
└── sda5              # LVM chiffré
    └── LVMGroup_crypt
        ├── root      # /
        ├── swap      # [SWAP]
        └── home      # /home
```

<details>
<summary><b>Schéma de Partitionnement Bonus (7 partitions)</b></summary>

```
sda
├── sda1              # /boot (500M)
├── sda2              # (partition étendue)
└── sda5              # LVM chiffré
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

## 🔐 Configuration de Sécurité

<details open>
<summary><b>Cliquez pour voir les règles de sécurité</b></summary>

### Politique de Mot de Passe

| Règle | Valeur |
|:------|:-------|
| **Expiration** | Tous les 30 jours |
| **Jours minimum avant modification** | 2 jours |
| **Avertissement avant expiration** | 7 jours |
| **Longueur minimum** | 10 caractères |
| **Complexité** | Majuscule + minuscule + chiffre requis |
| **Caractères identiques consécutifs max** | 3 |
| **Nom d'utilisateur dans le mot de passe** | ❌ Interdit |
| **Différent de l'ancien mot de passe** | Au moins 7 caractères *(ne s'applique PAS à root)* |

### Configuration Sudo

| Règle | Description |
|:------|:------------|
| **Tentatives d'authentification** | 3 maximum |
| **Message d'erreur personnalisé** | Affiché en cas de mauvais mot de passe |
| **Journalisation entrée/sortie** | Toutes les commandes archivées dans `/var/log/sudo/` |
| **Mode TTY** | Requis |
| **Chemin sécurisé** | `/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin` |

### Configuration SSH

| Règle | Valeur |
|:------|:-------|
| **Port** | 4242 |
| **Connexion root** | ❌ Interdite |

### Pare-feu (UFW / firewalld)

| Règle | Description |
|:------|:------------|
| **Politique par défaut** | Refuser les connexions entrantes |
| **Port autorisé** | 4242 uniquement (SSH) |
| **État au démarrage** | Doit être actif |

</details>

---

## 📊 Script de Monitoring (`monitoring.sh`)

Un script Bash qui s'exécute automatiquement **toutes les 10 minutes** via `cron`, diffusant les informations système sur tous les terminaux avec `wall`.

<details open>
<summary><b>Informations affichées</b></summary>

| Métrique | Description |
|:---------|:------------|
| **#Architecture** | OS et version du noyau |
| **#CPU physical** | Nombre de processeurs physiques |
| **#vCPU** | Nombre de processeurs virtuels |
| **#Memory Usage** | RAM utilisée/totale (pourcentage) |
| **#Disk Usage** | Stockage utilisé/total (pourcentage) |
| **#CPU load** | Utilisation actuelle du processeur |
| **#Last boot** | Date et heure du dernier redémarrage |
| **#LVM use** | Actif ou non |
| **#Connections TCP** | Nombre de connexions établies |
| **#User log** | Nombre d'utilisateurs connectés |
| **#Network** | Adresse IPv4 et adresse MAC |
| **#Sudo** | Nombre de commandes sudo exécutées |

</details>

### Exemple de Sortie

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

## 🧪 Commandes Utiles

<details open>
<summary><b>Cliquez pour voir les commandes</b></summary>

### Gestion des Utilisateurs et Groupes

```bash
# Vérifier les groupes d'un utilisateur
groups <nom_utilisateur>

# Ajouter un utilisateur à un groupe
sudo usermod -aG <groupe> <nom_utilisateur>

# Créer un nouvel utilisateur
sudo adduser <nom_utilisateur>

# Créer un nouveau groupe
sudo groupadd <nom_groupe>

# Vérifier la politique de mot de passe d'un utilisateur
sudo chage -l <nom_utilisateur>
```

### Gestion des Services

```bash
# Vérifier le statut SSH
sudo systemctl status ssh

# Vérifier le statut UFW (Debian)
sudo ufw status

# Vérifier le statut firewalld (Rocky)
sudo firewall-cmd --state
sudo firewall-cmd --list-all

# Vérifier le statut AppArmor (Debian)
sudo aa-status

# Vérifier le statut SELinux (Rocky)
sestatus
```

### Gestion de Cron

```bash
# Voir les tâches cron
sudo crontab -l

# Modifier les tâches cron
sudo crontab -e

# Arrêter le monitoring sans modifier le script
sudo systemctl stop cron
# ou
sudo /etc/init.d/cron stop
```

### Hostname

```bash
# Vérifier le hostname
hostnamectl

# Changer le hostname
sudo hostnamectl set-hostname <nouveau_hostname>
# Mettre aussi à jour /etc/hosts
```

### Informations Système

```bash
# Vérifier les partitions
lsblk

# Vérifier LVM
sudo lvdisplay

# Vérifier la version de l'OS
head -n 2 /etc/os-release
```

</details>

---

## 🎁 Partie Bonus

<details>
<summary><b>Cliquez pour voir les exigences bonus</b></summary>

La partie bonus inclut :

1. **Schéma de partitionnement étendu** (7 partitions comme montré ci-dessus)
2. **Site WordPress fonctionnel** avec :
   - lighttpd (serveur web)
   - MariaDB (base de données)
   - PHP
3. **Service supplémentaire de votre choix** (NGINX/Apache2 exclus)

> ⚠️ Le bonus n'est évalué que si la partie mandatory est **PARFAITE**

</details>

---

## 🧠 Ce que j'ai appris

- Installer et configurer un serveur Linux depuis zéro
- Concepts de virtualisation avec VirtualBox/UTM
- Différences entre aptitude et apt
- Ce qu'est AppArmor/SELinux et comment ça fonctionne
- Gestion des utilisateurs, groupes et permissions
- Configuration sécurisée de SSH et du pare-feu
- Politiques de mot de passe et règles de sécurité sudo
- Écriture de scripts Bash pour la surveillance système
- Planification de tâches avec cron
- LVM et partitionnement de disque chiffré

---

## 📄 Soumission

Un seul fichier est soumis :

```bash
signature.txt
```

Ce fichier contient la **signature SHA1** du disque de la machine virtuelle.

### Générer la Signature

```bash
# Windows
certUtil -hashfile <disque.vdi> sha1

# Linux
sha1sum <disque.vdi>

# macOS (VirtualBox)
shasum <disque.vdi>

# macOS M1 (UTM)
shasum <nom_vm.utm>/Images/disk-0.qcow2
```

### Emplacements par Défaut des VM

| OS | Chemin |
|:---|:-------|
| Windows | `%HOMEDRIVE%%HOMEPATH%\VirtualBox VMs\` |
| Linux | `~/VirtualBox VMs/` |
| macOS | `~/VirtualBox VMs/` |
| macOS M1 (UTM) | `~/Library/Containers/com.utmapp.UTM/Data/Documents/` |

> ⚠️ La signature peut changer après l'évaluation. Dupliquez votre VM ou utilisez save state.
>
> ⚠️ Les snapshots sont **INTERDITS** (note = 0 si détectés).

---

## 🚨 Avertissement

**🇬🇧 This project has an educational purpose and you should under no circumstances copy and paste.**
**Cheat is bad. Don't cheat.**

**🇫🇷 Ce projet a un but éducatif et vous ne devez en aucun cas faire du copier-coller.**
**Tricher c'est mal. Ne trichez pas.**

---

<div align="center">

**Fait avec ❤️ pour 42 Bangkok**

[![Retour en haut](https://img.shields.io/badge/⬆-Retour_en_haut-red?style=for-the-badge)](#born2beroot---administration-système--virtualisation)

</div>
