# 📁 Filesystem Hierarchy Standard (FHS)

## 📋 Informations du Module

- **Module** : 4 - Devices, Linux Filesystems, Filesystem Hierarchy Standard
- **Cours** : Find System Files and Place Files in the Correct Location Part 1 - FHS
- **Objectif** : Maîtriser la structure FHS, localiser fichiers système, comprendre le rôle de chaque répertoire racine, utiliser find/locate/whereis/which

---

## 🎯 Concepts Fondamentaux

### 🏛️ Qu'est-ce que le FHS ?

**FHS (Filesystem Hierarchy Standard)** : Standard définissant l'organisation des répertoires et fichiers sous Linux/Unix.

**Objectifs** :
- **Cohérence** : Même structure sur toutes les distributions
- **Prévisibilité** : Savoir où trouver les fichiers
- **Interopérabilité** : Applications fonctionnent sur toutes distros
- **Simplicité** : Administration facilitée

**Maintenu par** : Linux Foundation (anciennement Free Standards Group)

**Version actuelle** : FHS 3.0 (3 juin 2015)

### 📊 Principe d'Organisation

**Arborescence inversée** : Racine `/` en haut, branches vers le bas

```
/
├── bin/          → Binaires essentiels (commandes de base)
├── boot/         → Noyau et fichiers de démarrage
├── dev/          → Périphériques (devices)
├── etc/          → Configuration système
├── home/         → Répertoires personnels utilisateurs
├── lib/          → Bibliothèques partagées essentielles
├── media/        → Points de montage amovibles (USB, CD)
├── mnt/          → Points de montage temporaires
├── opt/          → Applications tierces optionnelles
├── proc/         → Système de fichiers virtuel (processus)
├── root/         → Répertoire personnel de root
├── run/          → Données runtime (PID, sockets)
├── sbin/         → Binaires système (admin)
├── srv/          → Données servies (web, ftp)
├── sys/          → Système de fichiers virtuel (hardware)
├── tmp/          → Fichiers temporaires
├── usr/          → Hiérarchie secondaire (programmes utilisateur)
│   ├── bin/      → Commandes utilisateur
│   ├── lib/      → Bibliothèques
│   ├── local/    → Logiciels locaux
│   ├── share/    → Données partagées (doc, man)
│   └── src/      → Code source
└── var/          → Données variables (logs, caches, spools)
    ├── log/      → Journaux système
    ├── tmp/      → Temporaires préservés entre reboots
    ├── cache/    → Caches applicatifs
    └── spool/    → Files d'attente (mail, impression)
```

**Classification** :

| Type | Description | Exemples |
|------|-------------|----------|
| **Statique** | Ne change pas sans admin | `/bin`, `/boot`, `/etc`, `/lib`, `/sbin` |
| **Variable** | Change pendant fonctionnement | `/var`, `/tmp`, `/run`, `/home` |
| **Partageable** | Peut être partagé en réseau (NFS) | `/usr`, `/opt`, `/home` |
| **Non-partageable** | Spécifique à la machine | `/etc`, `/boot`, `/var/lock` |
| **Virtuel** | Généré par noyau (pas sur disque) | `/proc`, `/sys`, `/dev` |

---

## 📂 Répertoires Racine : Guide Détaillé

### /bin - Binaires Essentiels

**Rôle** : Commandes **essentielles** disponibles pour **tous les utilisateurs**, nécessaires en **mode mono-utilisateur** (single-user mode).

**Contenu** :
- Commandes de base : `ls`, `cp`, `mv`, `rm`, `mkdir`, `cat`, `echo`
- Shells : `bash`, `sh`, `dash`
- Utilitaires système : `ps`, `grep`, `sed`, `awk`, `tar`, `gzip`

**Exemples** :
```bash
ls -lh /bin/
# lrwxrwxrwx  1 root root    7 Jan 15 10:00 /bin -> usr/bin  (symlink moderne)

# Ou sur systèmes anciens :
ls /bin/ | head -10
# bash  cat  chmod  chown  cp  date  dd  df  dmesg  echo

# Commandes critiques
ls -lh /bin/bash /bin/ls /bin/cat
# -rwxr-xr-x 1 root root 1.4M Jan 15 10:00 /bin/bash
# -rwxr-xr-x 1 root root 143K Jan 15 10:00 /bin/ls
# -rwxr-xr-x 1 root root  43K Jan 15 10:00 /bin/cat

# Tester disponibilité mode mono-utilisateur
which bash ls cp mv rm
# /bin/bash
# /bin/ls
# /bin/cp
# /bin/mv
# /bin/rm
```

**⚠️ Évolution moderne** : Debian/Ubuntu/Fedora modernes fusionnent `/bin` → `/usr/bin` (symlink)

```bash
ls -ld /bin
# lrwxrwxrwx 1 root root 7 Jan 15 10:00 /bin -> usr/bin

# Raison : Simplification (historiquement /bin = critique, /usr/bin = additionnel)
# Maintenant : Tout dans /usr/bin
```

---

### /boot - Fichiers de Démarrage

**Rôle** : Fichiers nécessaires au **démarrage du système** (bootloader, noyau).

**Contenu** :
- **Noyau Linux** : `vmlinuz-*`
- **initramfs** : `initrd.img-*` ou `initramfs-*` (système de fichiers initial)
- **Configuration bootloader** : GRUB (`grub/`)
- **System.map** : Table symboles noyau

**Exemples** :
```bash
ls -lh /boot/
# -rw-r--r-- 1 root root  10M Jan 15 10:00 vmlinuz-6.1.0-17-amd64  ← Noyau
# -rw-r--r-- 1 root root  30M Jan 15 10:00 initrd.img-6.1.0-17-amd64  ← initramfs
# -rw-r--r-- 1 root root 256K Jan 15 10:00 config-6.1.0-17-amd64  ← Config noyau
# -rw-r--r-- 1 root root 8.5M Jan 15 10:00 System.map-6.1.0-17-amd64  ← Symboles
# drwxr-xr-x 5 root root 4.0K Jan 15 10:00 grub/  ← GRUB bootloader

# Configuration GRUB
ls /boot/grub/
# grub.cfg  grubenv  fonts/  locale/  themes/

# Taille partition /boot (souvent séparée)
df -h /boot
# Filesystem      Size  Used Avail Use% Mounted on
# /dev/sda1       512M  150M  362M  30% /boot

# ⚠️ Ne jamais remplir /boot (empêche mises à jour noyau)
```

**Partition séparée** : Souvent `/boot` sur partition dédiée (compatibilité BIOS ancien, chiffrement système).

---

### /dev - Périphériques (Devices)

**Rôle** : Fichiers spéciaux représentant **périphériques matériels** et **pseudo-périphériques**.

**Contenu** :
- **Block devices** : `sda`, `nvme0n1` (disques)
- **Character devices** : `tty`, `console` (terminaux)
- **Pseudo-devices** : `null`, `zero`, `random`, `urandom`

**Exemples** :
```bash
ls -l /dev/ | head -20
# brw-rw---- 1 root disk      8,   0 Feb  3 10:00 sda        ← Disque SATA
# brw-rw---- 1 root disk      8,   1 Feb  3 10:00 sda1       ← Partition 1
# brw-rw---- 1 root disk      8,   2 Feb  3 10:00 sda2       ← Partition 2
# crw-rw-rw- 1 root tty       5,   0 Feb  3 10:00 tty        ← Console
# crw--w---- 1 root tty       4,   1 Feb  3 10:00 tty1       ← Terminal 1
# crw-rw-rw- 1 root root      1,   3 Feb  3 10:00 null       ← /dev/null
# crw-rw-rw- 1 root root      1,   8 Feb  3 10:00 random     ← Random
# crw-rw-rw- 1 root root      1,   9 Feb  3 10:00 urandom    ← urandom
# lrwxrwxrwx 1 root root         15 Feb  3 10:00 stdin -> /proc/self/fd/0
# lrwxrwxrwx 1 root root         15 Feb  3 10:00 stdout -> /proc/self/fd/1

# Disques et partitions
ls -l /dev/sd* /dev/nvme*
# brw-rw---- 1 root disk 8,  0 Feb  3 10:00 /dev/sda
# brw-rw---- 1 root disk 8, 16 Feb  3 10:00 /dev/sdb
# brw-rw---- 1 root disk 259, 0 Feb  3 10:00 /dev/nvme0n1

# Pseudo-périphériques utiles
# /dev/null : Puits sans fond (discard output)
echo "test" > /dev/null  # Sortie jetée

# /dev/zero : Source infinie de zéros
dd if=/dev/zero of=file.img bs=1M count=100  # Créer fichier 100MB de zéros

# /dev/random : Nombres aléatoires (bloque si entropie insuffisante)
head -c 16 /dev/random | base64

# /dev/urandom : Nombres pseudo-aléatoires (ne bloque jamais)
head -c 16 /dev/urandom | base64
```

**udev** : Gestionnaire dynamique de périphériques (crée/supprime fichiers dans `/dev`)

```bash
# Informations udev
udevadm info /dev/sda1
# P: /devices/pci0000:00/0000:00:1f.2/ata1/host0/target0:0:0/0:0:0:0/block/sda/sda1
# N: sda1
# S: disk/by-uuid/a1b2c3d4-...
```

---

### /etc - Configuration Système

**Rôle** : Fichiers de **configuration système** et **services**.

**Contenu** :
- **Utilisateurs/groupes** : `passwd`, `shadow`, `group`, `gshadow`
- **Réseau** : `hostname`, `hosts`, `resolv.conf`, `network/`
- **Services** : `ssh/sshd_config`, `apache2/`, `nginx/`
- **Système** : `fstab`, `crontab`, `sudoers`

**Exemples** :
```bash
ls -l /etc/ | head -15
# -rw-r--r--  1 root root     3028 Feb  3 10:00 passwd
# -rw-r-----  1 root shadow   1753 Feb  3 10:00 shadow
# -rw-r--r--  1 root root     1087 Feb  3 10:00 group
# -rw-r--r--  1 root root      604 Feb  3 10:00 fstab
# -rw-r--r--  1 root root      220 Feb  3 10:00 hosts
# -rw-r--r--  1 root root       13 Feb  3 10:00 hostname
# drwxr-xr-x  3 root root     4096 Feb  3 10:00 ssh/
# drwxr-xr-x  2 root root     4096 Feb  3 10:00 systemd/

# Fichiers critiques
cat /etc/hostname
# serveur-prod

cat /etc/hosts
# 127.0.0.1   localhost
# 127.0.1.1   serveur-prod
# 192.168.1.10 db-server

cat /etc/resolv.conf
# nameserver 8.8.8.8
# nameserver 1.1.1.1

# Configuration SSH
cat /etc/ssh/sshd_config | grep -v "^#" | grep -v "^$"
# Port 22
# PermitRootLogin no
# PasswordAuthentication yes

# Montages automatiques
cat /etc/fstab
# UUID=a1b2c3d4-...  /       ext4  defaults  0  1
# UUID=e5f6g7h8-...  /home   ext4  defaults  0  2
```

**⚠️ Permissions** : Fichiers sensibles (`shadow`, `sudoers`) protégés (600, 640, 440)

```bash
ls -l /etc/shadow /etc/sudoers
# -rw-r----- 1 root shadow 1753 Feb  3 10:00 /etc/shadow
# -r--r----- 1 root root    755 Feb  3 10:00 /etc/sudoers
```

---

### /home - Répertoires Personnels Utilisateurs

**Rôle** : Répertoires personnels des **utilisateurs normaux**.

**Structure** : `/home/username/`

**Contenu** :
- Documents utilisateur
- Configuration personnelle (`.bashrc`, `.profile`, `.config/`)
- Données applicatives (`.mozilla/`, `.ssh/`)

**Exemples** :
```bash
ls -la /home/
# drwxr-xr-x  5 root   root   4096 Feb  3 10:00 .
# drwxr-xr-x 18 root   root   4096 Feb  3 10:00 ..
# drwxr-x--- 25 alice  alice  4096 Feb  3 10:00 alice/
# drwxr-x--- 18 bob    bob    4096 Feb  3 10:00 bob/
# drwxr-x--- 12 charlie charlie 4096 Feb  3 10:00 charlie/

# Contenu typique home utilisateur
ls -la /home/alice/
# drwxr-xr-x  3 alice alice 4096 Feb  3 10:00 Documents/
# drwxr-xr-x  2 alice alice 4096 Feb  3 10:00 Downloads/
# drwxr-xr-x  2 alice alice 4096 Feb  3 10:00 Pictures/
# -rw-r--r--  1 alice alice  220 Feb  3 10:00 .bashrc
# -rw-r--r--  1 alice alice  807 Feb  3 10:00 .profile
# drwx------  2 alice alice 4096 Feb  3 10:00 .ssh/
# drwxr-xr-x  3 alice alice 4096 Feb  3 10:00 .config/

# Quota utilisateur
du -sh /home/alice
# 5.2G  /home/alice

# Permissions (700 = privé)
ls -ld /home/alice
# drwxr-x--- 25 alice alice 4096 Feb  3 10:00 /home/alice
#  │││││││││
#  │││││││└┴─ Groupe: r-x (lecture + accès)
#  │││└┴┴──── User: rwx (contrôle total)
#  └┴┴─────── Type: directory
```

**Variable $HOME** : Pointe vers home utilisateur actuel

```bash
echo $HOME
# /home/alice

cd ~
# → /home/alice

cd ~/Documents
# → /home/alice/Documents
```

---

### /lib - Bibliothèques Partagées Essentielles

**Rôle** : Bibliothèques partagées (shared libraries) essentielles pour `/bin` et `/sbin`.

**Contenu** :
- **Libraries .so** : `libc.so.6`, `libm.so.6`, `libpthread.so.0`
- **Modules noyau** : `/lib/modules/`

**Exemples** :
```bash
ls -l /lib/ | head -10
# lrwxrwxrwx  1 root root    9 Jan 15 10:00 /lib -> usr/lib  (symlink moderne)

# Ou sur systèmes anciens :
ls /lib/x86_64-linux-gnu/ | head
# ld-linux-x86-64.so.2  ← Linker dynamique
# libc.so.6              ← Bibliothèque C standard
# libm.so.6              ← Bibliothèque mathématique
# libpthread.so.0        ← Threads POSIX

# Modules noyau
ls /lib/modules/$(uname -r)/
# kernel/  modules.alias  modules.dep  modules.symbols

# Vérifier dépendances binaire
ldd /bin/bash
# linux-vdso.so.1 (0x00007ffc123ab000)
# libtinfo.so.6 => /lib/x86_64-linux-gnu/libtinfo.so.6
# libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6
# /lib64/ld-linux-x86-64.so.2

# Chemins recherche bibliothèques
cat /etc/ld.so.conf
# include /etc/ld.so.conf.d/*.conf

ldconfig -p | head
# 1234 libs found in cache `/etc/ld.so.cache'
#     libc.so.6 (libc6,x86-64) => /lib/x86_64-linux-gnu/libc.so.6
```

**⚠️ Évolution** : `/lib` → `/usr/lib` (symlink)

---

### /media - Points de Montage Amovibles

**Rôle** : Points de montage pour **médias amovibles** (USB, CD/DVD) **gérés automatiquement**.

**Contenu** :
- Clés USB : `/media/user/USB_LABEL/`
- CD/DVD : `/media/user/CDROM/`

**Exemples** :
```bash
ls -l /media/
# drwxr-x---+ 3 root root 4096 Feb  3 10:00 alice/
# drwxr-x---+ 2 root root 4096 Feb  3 10:00 bob/

# Clé USB montée automatiquement
ls /media/alice/
# USB_DRIVE/

ls /media/alice/USB_DRIVE/
# Documents/  Photos/  Videos/

# Informations montage
mount | grep media
# /dev/sdb1 on /media/alice/USB_DRIVE type vfat (rw,nosuid,nodev,uid=1000,gid=1000)

# Démonter (via GUI ou commande)
umount /media/alice/USB_DRIVE
```

**Gestionnaires** : `udisks2`, `gvfs` (GNOME), `kio` (KDE)

---

### /mnt - Points de Montage Temporaires

**Rôle** : Points de montage pour **filesystems temporaires** montés **manuellement** par admin.

**Usage** :
- Montage manuel disques réseau (NFS, CIFS)
- Partitions de test
- Montages ponctuels

**Exemples** :
```bash
ls -l /mnt/
# drwxr-xr-x 2 root root 4096 Feb  3 10:00 backup/
# drwxr-xr-x 2 root root 4096 Feb  3 10:00 nfs_share/

# Monter NFS temporairement
mount -t nfs 192.168.1.100:/export/data /mnt/nfs_share

# Vérifier
ls /mnt/nfs_share/
# project_files/  documents/

# Démonter
umount /mnt/nfs_share

# Créer point de montage temporaire
mkdir /mnt/test_disk
mount /dev/sdc1 /mnt/test_disk
ls /mnt/test_disk
umount /mnt/test_disk
```

**Différence /media vs /mnt** :
- **/media** : Automatique (udisks, utilisateurs)
- **/mnt** : Manuel (admin, temporaire)

---

### /opt - Applications Tierces Optionnelles

**Rôle** : Applications tierces **auto-contenues** (non gérées par gestionnaire de paquets).

**Structure** : `/opt/application_name/`

**Exemples** :
```bash
ls -l /opt/
# drwxr-xr-x  3 root root 4096 Feb  3 10:00 google/
# drwxr-xr-x  5 root root 4096 Feb  3 10:00 teamviewer/
# drwxr-xr-x  8 root root 4096 Feb  3 10:00 pycharm/

# Application dans /opt (ex: Google Chrome)
ls /opt/google/chrome/
# chrome*  chrome_sandbox  locales/  resources/

# Liens symboliques vers /usr/local/bin
ls -l /usr/local/bin/pycharm
# lrwxrwxrwx 1 root root 25 Feb  3 10:00 /usr/local/bin/pycharm -> /opt/pycharm/bin/pycharm.sh

# Installation manuelle
tar xzf myapp.tar.gz -C /opt/
ln -s /opt/myapp/bin/myapp /usr/local/bin/myapp
```

**Cas d'usage** :
- Logiciels commerciaux
- Installations manuelles (non-deb/rpm)
- Applications isolées

---

### /proc - Système de Fichiers Virtuel (Processus)

**Rôle** : Interface vers **noyau** et **processus** (généré dynamiquement, **pas sur disque**).

**Contenu** :
- **Répertoires numérotés** : PID processus (`/proc/1234/`)
- **Fichiers système** : `cpuinfo`, `meminfo`, `version`

**Exemples** :
```bash
ls -l /proc/
# dr-xr-xr-x  9 root   root             0 Feb  3 10:00 1/       ← PID 1 (systemd)
# dr-xr-xr-x  9 alice  alice            0 Feb  3 10:00 12345/   ← PID 12345
# -r--r--r--  1 root   root             0 Feb  3 10:00 cpuinfo
# -r--r--r--  1 root   root             0 Feb  3 10:00 meminfo
# -r--r--r--  1 root   root             0 Feb  3 10:00 version

# Informations CPU
cat /proc/cpuinfo | head -20
# processor       : 0
# vendor_id       : GenuineIntel
# cpu family      : 6
# model           : 142
# model name      : Intel(R) Core(TM) i7-8550U CPU @ 1.80GHz

# Informations mémoire
cat /proc/meminfo | head
# MemTotal:       16384000 kB
# MemFree:         8192000 kB
# MemAvailable:   12288000 kB

# Version noyau
cat /proc/version
# Linux version 6.1.0-17-amd64 (debian-kernel@lists.debian.org) (gcc-12 ...) #1 SMP PREEMPT_DYNAMIC Debian 6.1.69-1

# Informations processus
ls /proc/1/
# cmdline  cwd@  environ  exe@  fd/  maps  status

cat /proc/1/cmdline
# /sbin/init

cat /proc/1/status | head
# Name:   systemd
# State:  S (sleeping)
# Pid:    1
# PPid:   0

# Ligne de commande processus
cat /proc/12345/cmdline | tr '\0' ' '
# python3 myapp.py --config /etc/myapp.conf
```

**Utilité** :
- Debugging processus
- Monitoring système
- Scripts système

---

### /root - Répertoire Personnel de Root

**Rôle** : Répertoire personnel du super-utilisateur **root**.

**Raison** : Séparé de `/home` (peut être sur partition différente, disponible en mode rescue).

**Exemples** :
```bash
# En tant que root
sudo su -
cd ~
pwd
# /root

ls -la /root/
# drwx------  5 root root 4096 Feb  3 10:00 .
# drwxr-xr-x 18 root root 4096 Feb  3 10:00 ..
# -rw-------  1 root root  123 Feb  3 10:00 .bash_history
# -rw-r--r--  1 root root  570 Feb  3 10:00 .bashrc
# -rw-r--r--  1 root root  161 Feb  3 10:00 .profile
# drwx------  2 root root 4096 Feb  3 10:00 .ssh/

# Permissions strictes (700)
ls -ld /root
# drwx------ 5 root root 4096 Feb  3 10:00 /root
```

**⚠️ Sécurité** : Ne jamais partager `/root` en réseau, permissions 700

---

### /run - Données Runtime Variables

**Rôle** : Données **runtime** (générées au boot, **tmpfs en RAM**, effacées au reboot).

**Contenu** :
- **PID files** : `nginx.pid`, `sshd.pid`
- **Sockets Unix** : `systemd/`, `dbus/`
- **Lock files** : Verrous processus

**Exemples** :
```bash
ls -l /run/
# drwxr-xr-x  2 root     root       60 Feb  3 10:00 lock/
# -rw-r--r--  1 root     root        4 Feb  3 10:00 nginx.pid
# srw-rw-rw-  1 root     root        0 Feb  3 10:00 systemd/notify
# drwxr-xr-x  3 postgres postgres   80 Feb  3 10:00 postgresql/

# Fichier PID
cat /run/nginx.pid
# 1234

# Vérifier processus
ps -p $(cat /run/nginx.pid)
# PID TTY          TIME CMD
# 1234 ?        00:00:05 nginx

# Sockets systemd
ls /run/systemd/
# notify  private/  system/  users/

# Type filesystem (tmpfs = RAM)
df -h /run
# Filesystem      Size  Used Avail Use% Mounted on
# tmpfs           1.6G  1.2M  1.6G   1% /run
```

**⚠️ Tmpfs** : Contenu perdu au reboot (intentionnel)

---

### /sbin - Binaires Système (Administration)

**Rôle** : Commandes système pour **administration** (généralement root uniquement).

**Contenu** :
- Gestion disques : `fdisk`, `mkfs`, `fsck`, `mount`
- Réseau : `ip`, `ifconfig`, `route`
- Boot : `init`, `shutdown`, `reboot`
- Services : `systemctl`, `service`

**Exemples** :
```bash
ls -l /sbin/ | head -10
# lrwxrwxrwx  1 root root    8 Jan 15 10:00 /sbin -> usr/sbin  (symlink moderne)

# Ou systèmes anciens :
ls /sbin/ | head
# fdisk  fsck  init  ip  iptables  mkfs  mount  reboot  shutdown

# Commandes admin
which fdisk mkfs mount ip
# /sbin/fdisk
# /sbin/mkfs
# /sbin/mount
# /sbin/ip

# Utilisateur normal n'a pas /sbin dans PATH
echo $PATH | grep sbin
# (vide pour utilisateur normal)

# Root a /sbin dans PATH
sudo su -
echo $PATH
# /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

**⚠️ Évolution** : `/sbin` → `/usr/sbin` (symlink)

---

### /srv - Données Servies

**Rôle** : Données **servies par le système** (web, ftp, etc.).

**Structure** : `/srv/service_name/`

**Exemples** :
```bash
ls -l /srv/
# drwxr-xr-x 3 www-data www-data 4096 Feb  3 10:00 www/
# drwxr-xr-x 2 ftp      ftp      4096 Feb  3 10:00 ftp/
# drwxr-xr-x 5 git      git      4096 Feb  3 10:00 git/

# Serveur web
ls /srv/www/
# example.com/  blog.example.com/

ls /srv/www/example.com/
# index.html  css/  js/  images/

# Configuration Apache/Nginx pointe vers /srv/www
cat /etc/apache2/sites-available/example.com.conf
# DocumentRoot /srv/www/example.com

# Serveur Git
ls /srv/git/
# project1.git/  project2.git/

# Alternative : /var/www (plus courant)
```

**⚠️ Usage** : Peu utilisé en pratique, préférence pour `/var/www`, `/var/ftp`

---

### /sys - Système de Fichiers Virtuel (Hardware)

**Rôle** : Interface vers **matériel** et **drivers** (généré dynamiquement par noyau).

**Contenu** :
- Informations hardware
- Configuration périphériques
- Classes de devices

**Exemples** :
```bash
ls -l /sys/
# drwxr-xr-x  2 root root 0 Feb  3 10:00 block/
# drwxr-xr-x  8 root root 0 Feb  3 10:00 bus/
# drwxr-xr-x 75 root root 0 Feb  3 10:00 class/
# drwxr-xr-x 12 root root 0 Feb  3 10:00 devices/
# drwxr-xr-x  5 root root 0 Feb  3 10:00 module/

# Périphériques blocs
ls /sys/block/
# sda  sdb  nvme0n1  loop0

# Informations disque
cat /sys/block/sda/size
# 1953525168  (secteurs de 512 octets = 1TB)

cat /sys/block/sda/removable
# 0  (non amovible)

# Classes périphériques
ls /sys/class/
# backlight/  input/  net/  power_supply/  thermal/

# Interfaces réseau
ls /sys/class/net/
# eth0  lo  wlan0

cat /sys/class/net/eth0/address
# 00:1a:2b:3c:4d:5e  (MAC address)

cat /sys/class/net/eth0/speed
# 1000  (Mbps)

# Batterie
cat /sys/class/power_supply/BAT0/capacity
# 85  (%)

cat /sys/class/power_supply/BAT0/status
# Charging
```

**Utilité** : Scripts monitoring, debugging hardware

---

### /tmp - Fichiers Temporaires

**Rôle** : Fichiers **temporaires** accessibles à **tous** (effacés périodiquement).

**Caractéristiques** :
- Permissions : `1777` (sticky bit)
- Nettoyage : Au boot ou via `systemd-tmpfiles`
- Tmpfs : Peut être en RAM (selon config)

**Exemples** :
```bash
ls -ld /tmp
# drwxrwxrwt 15 root root 4096 Feb  3 10:00 /tmp
#         │
#         └─ Sticky bit (seul proprio peut supprimer)

# Créer fichier temporaire
mktemp
# /tmp/tmp.Ab12Cd34Ef

# Script créant fichiers temporaires
TMPFILE=$(mktemp)
echo "data" > "$TMPFILE"
cat "$TMPFILE"
rm "$TMPFILE"

# Vérifier si tmpfs (RAM)
df -h /tmp
# Filesystem      Size  Used Avail Use% Mounted on
# tmpfs           3.2G  150M  3.0G   5% /tmp

# Ou disque
# /dev/sda1        50G   20G   30G  40% /

# Nettoyage automatique
systemctl status systemd-tmpfiles-clean.timer
# Active: active (waiting) since ...
# Next elapse: ...

# Configuration nettoyage
cat /usr/lib/tmpfiles.d/tmp.conf
# d /tmp 1777 root root 10d
#                       ^^^
#                       Fichiers > 10 jours supprimés
```

**⚠️ Sécurité** : Ne jamais stocker données sensibles dans `/tmp` (accessible à tous)

---

### /usr - Hiérarchie Secondaire

**Rôle** : **User System Resources** = Programmes et données **utilisateur** (partageable en réseau).

**Structure** :
```
/usr/
├── bin/       → Commandes utilisateur (non essentielles)
├── sbin/      → Commandes admin (non essentielles)
├── lib/       → Bibliothèques
├── include/   → Headers C/C++ (développement)
├── local/     → Logiciels installés localement
│   ├── bin/
│   ├── lib/
│   └── share/
├── share/     → Données partagées (architecture indépendante)
│   ├── man/   → Pages manuel
│   ├── doc/   → Documentation
│   └── locale/ → Traductions
└── src/       → Code source (noyau, etc.)
```

**Exemples** :
```bash
# Commandes dans /usr/bin
ls /usr/bin/ | wc -l
# 2843  (beaucoup plus que /bin)

ls /usr/bin/ | head
# vim  gcc  python3  git  wget  curl  ssh  apt  dpkg

# Bibliothèques
ls /usr/lib/x86_64-linux-gnu/ | head
# libssl.so.3  libcrypto.so.3  libcurl.so.4

# Headers développement
ls /usr/include/ | head
# stdio.h  stdlib.h  string.h  unistd.h

# Pages manuel
ls /usr/share/man/man1/ | head
# ls.1.gz  cat.1.gz  grep.1.gz  vim.1.gz

man ls
# → Lit /usr/share/man/man1/ls.1.gz

# Documentation
ls /usr/share/doc/ | head
# bash  vim  git  python3.11

# Logiciels locaux
ls /usr/local/bin/
# custom_script.sh  myapp
```

**⚠️ /usr/local** : Pour installations **manuelles** (non gérées par apt/yum)

---

### /var - Données Variables

**Rôle** : Données **variables** (logs, caches, spools, bases de données).

**Structure** :
```
/var/
├── log/       → Logs système et applications
├── cache/     → Caches applicatifs
├── tmp/       → Temporaires (préservés entre reboots)
├── spool/     → Files d'attente (mail, impression)
├── lib/       → État persistant applications (bases de données)
├── mail/      → Boîtes mail utilisateurs
└── www/       → Données serveur web (alternatif à /srv/www)
```

**Exemples** :
```bash
# Logs système
ls -lh /var/log/
# -rw-r--r-- 1 root   root   1.2M Feb  3 10:00 syslog
# -rw-r--r-- 1 root   root   856K Feb  3 10:00 auth.log
# -rw-r----- 1 syslog adm    2.1M Feb  3 10:00 kern.log
# drwxr-xr-x 2 root   root   4.0K Feb  3 10:00 apache2/
# drwxr-xr-x 2 root   root   4.0K Feb  3 10:00 nginx/

tail /var/log/syslog
# Feb  3 10:15:23 server systemd[1]: Started Session 123 of user alice.

# Caches APT
ls -lh /var/cache/apt/archives/ | head
# -rw-r--r-- 1 root root 1.2M Feb  1 10:00 vim_9.0.1234-1_amd64.deb
# -rw-r--r-- 1 root root 856K Feb  1 10:00 git_2.39.0-1_amd64.deb

du -sh /var/cache/
# 2.5G  /var/cache

# Spools impression
ls /var/spool/cups/
# tmp/  (jobs d'impression en attente)

# Bases de données
ls /var/lib/
# dpkg/  mysql/  postgresql/  docker/

du -sh /var/lib/mysql
# 15G  /var/lib/mysql

# Web (alternative à /srv/www)
ls /var/www/
# html/

ls /var/www/html/
# index.html  css/  js/
```

**⚠️ /var/tmp vs /tmp** :
- **/tmp** : Effacé au boot
- **/var/tmp** : Préservé entre reboots (nettoyage après 30 jours)

---

## 🔍 Commandes de Localisation de Fichiers

### find - Recherche Avancée

**Syntaxe** :
```bash
find [path] [options] [expression]
```

**Exemples** :
```bash
# Trouver fichier par nom
find /etc -name "*.conf"
find /home -name "resume.pdf"

# Insensible à la casse
find /usr/share -iname "README*"

# Par type
find /dev -type c  # Character devices
find /home -type d  # Répertoires
find /tmp -type l  # Liens symboliques

# Par taille
find /var/log -size +100M  # > 100MB
find /home -size -1k  # < 1KB

# Par date modification
find /etc -mtime -7  # Modifiés derniers 7 jours
find /tmp -mtime +30  # Modifiés il y a > 30 jours

# Par permissions
find /usr/bin -perm -4000  # SUID
find /home -perm 777  # Exactement 777

# Par propriétaire
find /home -user alice
find /var -group www-data

# Exécuter commande sur résultats
find /var/log -name "*.log" -exec ls -lh {} \;
find /tmp -type f -mtime +7 -delete

# Combinaisons
find /etc -name "*.conf" -type f -mtime -30
```

### locate - Recherche Rapide (Base de Données)

**Syntaxe** :
```bash
locate [options] pattern
```

**Principe** : Recherche dans **base de données** (rapide, mais peut être obsolète)

**Exemples** :
```bash
# Recherche simple
locate passwd
# /etc/passwd
# /etc/pam.d/passwd
# /usr/bin/passwd

# Insensible casse
locate -i README

# Compter résultats
locate -c "*.pdf"
# 1234

# Limiter résultats
locate -l 10 config

# Mettre à jour base de données
updatedb  # (root uniquement)

# Statistiques base
locate -S
# Database: /var/lib/mlocate/mlocate.db
# File names: 3,456,789
# File sizes: 123 MB
```

**⚠️ Limitations** :
- Base mise à jour quotidiennement (cron)
- Fichiers récents peuvent ne pas apparaître
- Fichiers supprimés peuvent encore être listés

### whereis - Localiser Binaires, Sources, Manuels

**Syntaxe** :
```bash
whereis [options] command
```

**Exemples** :
```bash
# Recherche complète
whereis bash
# bash: /bin/bash /etc/bash.bashrc /usr/share/man/man1/bash.1.gz

# Seulement binaires
whereis -b ls
# ls: /bin/ls

# Seulement manuels
whereis -m vim
# vim: /usr/share/man/man1/vim.1.gz

# Seulement sources
whereis -s python3
# python3: /usr/src/python3.11

# Plusieurs commandes
whereis ls cat grep
# ls: /bin/ls /usr/share/man/man1/ls.1.gz
# cat: /bin/cat /usr/share/man/man1/cat.1.gz
# grep: /bin/grep /usr/share/man/man1/grep.1.gz
```

### which - Localiser Exécutable dans PATH

**Syntaxe** :
```bash
which [options] command
```

**Principe** : Trouve **premier exécutable** dans `$PATH`

**Exemples** :
```bash
# Commande simple
which python3
# /usr/bin/python3

# Afficher tous (si multiples)
which -a python3
# /usr/bin/python3
# /usr/local/bin/python3

# Plusieurs commandes
which bash ls cat
# /bin/bash
# /bin/ls
# /bin/cat

# Tester disponibilité
which nonexistent_command
# (aucune sortie, exit code 1)

if which docker > /dev/null 2>&1; then
    echo "Docker installé"
else
    echo "Docker non installé"
fi
```

### type - Identifier Type de Commande

**Syntaxe** :
```bash
type [options] command
```

**Exemples** :
```bash
# Commande simple
type ls
# ls is aliased to 'ls --color=auto'

type -t ls
# alias

# Commande builtin
type cd
# cd is a shell builtin

# Exécutable externe
type grep
# grep is /bin/grep

# Afficher tout
type -a echo
# echo is a shell builtin
# echo is /bin/echo

# Fonction shell
myfunc() { echo "test"; }
type myfunc
# myfunc is a function
```

### Comparaison des Commandes

| Commande | Recherche | Vitesse | Cas d'usage |
|----------|-----------|---------|-------------|
| `find` | Filesystem réel | Lent (parcours disque) | Recherche précise, récente, avec critères |
| `locate` | Base de données | Rapide | Recherche rapide nom fichier |
| `whereis` | Emplacements standards | Rapide | Trouver binaire + man + source |
| `which` | $PATH | Rapide | Trouver commande exécutable |
| `type` | Shell | Instantané | Identifier type (alias, builtin, fonction) |

---

## 🎓 10 Scénarios Pratiques Complets

### Scénario 1️⃣ : Diagnostic espace disque critique sur /var/log

**Contexte** : Serveur en production, `/var/log` plein à 95%.

```bash
# 1. Vérifier espace disque
df -h
# Filesystem      Size  Used Avail Use% Mounted on
# /dev/sda1        50G   20G   30G  40% /
# /dev/sda2       100G   95G    5G  95% /var
#                                   ^^^
#                                   Critique !

# 2. Identifier plus gros répertoires dans /var
du -sh /var/* | sort -rh | head -10
# 80G   /var/log
# 10G   /var/cache
# 3G    /var/lib
# 1.5G  /var/tmp

# 3. Analyser /var/log
du -sh /var/log/* | sort -rh | head
# 50G   /var/log/application.log
# 20G   /var/log/syslog
# 5G    /var/log/apache2/access.log
# 3G    /var/log/kern.log

# 4. Vérifier fichiers énormes
find /var/log -type f -size +1G -exec ls -lh {} \;
# -rw-r--r-- 1 root root 50G Feb  3 10:00 /var/log/application.log

# 5. Solution immédiate : Tronquer fichier log
> /var/log/application.log
# ou
truncate -s 0 /var/log/application.log

# 6. Vérifier espace libéré
df -h /var
# Filesystem      Size  Used Avail Use% Mounted on
# /dev/sda2       100G   45G   55G  45% /var

# 7. Solution permanente : Rotation logs (logrotate)
cat > /etc/logrotate.d/application <<'EOF'
/var/log/application.log {
    daily
    rotate 7
    compress
    delaycompress
    missingok
    notifempty
    create 0644 root root
    postrotate
        systemctl reload application
    endscript
}
EOF

# 8. Tester configuration
logrotate -d /etc/logrotate.d/application

# 9. Forcer rotation
logrotate -f /etc/logrotate.d/application

# 10. Vérifier logs archivés
ls -lh /var/log/application.log*
# -rw-r--r-- 1 root root  10M Feb  3 15:00 application.log
# -rw-r--r-- 1 root root 2.1M Feb  2 23:59 application.log.1.gz
# -rw-r--r-- 1 root root 1.9M Feb  1 23:59 application.log.2.gz
```

---

### Scénario 2️⃣ : Localiser fichier de configuration spécifique

**Contexte** : Trouver configuration SSH personnalisée.

```bash
# 1. Recherche simple (trop de résultats)
find / -name "*ssh*" 2>/dev/null | wc -l
# 1234

# 2. Limiter à /etc
find /etc -name "*ssh*"
# /etc/ssh
# /etc/ssh/sshd_config
# /etc/ssh/ssh_config

# 3. Fichiers de configuration seulement
find /etc -name "*ssh*.conf" -o -name "sshd_config"
# /etc/ssh/sshd_config

# 4. Avec locate (rapide)
locate sshd_config
# /etc/ssh/sshd_config
# /usr/share/man/man5/sshd_config.5.gz

# 5. Vérifier binaire SSH
which ssh
# /usr/bin/ssh

whereis ssh
# ssh: /usr/bin/ssh /etc/ssh /usr/share/man/man1/ssh.1.gz

# 6. Configuration active (processus en cours)
ps aux | grep sshd
# root      1234  ... /usr/sbin/sshd -D

# Vérifier fichier config utilisé
/usr/sbin/sshd -T | head
# port 22
# addressfamily any
# ...

# 7. Fichiers récemment modifiés
find /etc/ssh -type f -mtime -30
# /etc/ssh/sshd_config  (modifié il y a 5 jours)

# 8. Comparer avec backup
diff /etc/ssh/sshd_config /etc/ssh/sshd_config.bak

# 9. Chercher directive spécifique
grep -r "PermitRootLogin" /etc/ssh/
# /etc/ssh/sshd_config:PermitRootLogin no

# 10. Documentation
man sshd_config
whereis -m sshd_config
# sshd_config: /usr/share/man/man5/sshd_config.5.gz
```

---

### Scénario 3️⃣ : Nettoyer /tmp et /var/tmp

**Contexte** : Libérer espace, supprimer anciens fichiers temporaires.

```bash
# 1. Vérifier espace /tmp
df -h /tmp
# Filesystem      Size  Used Avail Use% Mounted on
# tmpfs           3.2G  2.8G  400M  88% /tmp

# 2. Identifier gros fichiers
du -sh /tmp/* 2>/dev/null | sort -rh | head
# 1.5G  /tmp/large_backup.tar.gz
# 500M  /tmp/tmpfiles123
# 200M  /tmp/download.iso

# 3. Fichiers anciens (> 7 jours)
find /tmp -type f -mtime +7 -ls
# ... (liste)

# 4. Supprimer fichiers anciens
find /tmp -type f -mtime +7 -delete

# 5. Vérifier propriétaires
find /tmp -type f -exec ls -lh {} \; | awk '{print $3}' | sort | uniq -c
#  123 alice
#   45 bob
#   12 www-data

# 6. Nettoyer /var/tmp (préservé entre reboots)
df -h /var/tmp
# Filesystem      Size  Used Avail Use% Mounted on
# /dev/sda2       100G   45G   55G  45% /

du -sh /var/tmp
# 10G   /var/tmp

# Fichiers > 30 jours
find /var/tmp -type f -mtime +30 -delete

# 7. Configuration systemd-tmpfiles
cat /usr/lib/tmpfiles.d/tmp.conf
# d /tmp 1777 root root 10d
# d /var/tmp 1777 root root 30d

# 8. Forcer nettoyage systemd
systemd-tmpfiles --clean

# 9. Vérifier résultat
df -h /tmp /var/tmp
# Filesystem      Size  Used Avail Use% Mounted on
# tmpfs           3.2G  800M  2.4G  25% /tmp
# /dev/sda2       100G   38G   62G  38% /

# 10. Automatisation : Script cron
cat > /etc/cron.daily/cleanup-tmp <<'EOF'
#!/bin/bash
# Nettoyer fichiers > 7 jours dans /tmp
find /tmp -type f -mtime +7 -delete
# Nettoyer fichiers > 30 jours dans /var/tmp
find /var/tmp -type f -mtime +30 -delete
EOF

chmod +x /etc/cron.daily/cleanup-tmp
```

---

### Scénario 4️⃣ : Installer application dans /opt avec intégration

**Contexte** : Installer PyCharm manuellement dans `/opt`.

```bash
# 1. Télécharger archive
wget https://download.jetbrains.com/python/pycharm-community-2024.1.tar.gz

# 2. Extraire dans /opt
tar xzf pycharm-community-2024.1.tar.gz -C /opt/

# 3. Renommer (optionnel)
mv /opt/pycharm-community-2024.1 /opt/pycharm

# 4. Vérifier structure
ls -l /opt/pycharm/
# drwxr-xr-x 2 root root 4096 Feb  3 10:00 bin/
# drwxr-xr-x 2 root root 4096 Feb  3 10:00 lib/
# drwxr-xr-x 2 root root 4096 Feb  3 10:00 plugins/
# -rw-r--r-- 1 root root  123 Feb  3 10:00 Install-Linux-tar.txt

# 5. Créer lien symbolique vers /usr/local/bin
ln -s /opt/pycharm/bin/pycharm.sh /usr/local/bin/pycharm

# 6. Vérifier PATH
which pycharm
# /usr/local/bin/pycharm

# 7. Tester lancement
pycharm &

# 8. Créer raccourci desktop (optionnel)
cat > /usr/share/applications/pycharm.desktop <<'EOF'
[Desktop Entry]
Version=1.0
Type=Application
Name=PyCharm Community
Icon=/opt/pycharm/bin/pycharm.png
Exec=/opt/pycharm/bin/pycharm.sh %f
Comment=Python IDE
Categories=Development;IDE;
Terminal=false
StartupWMClass=jetbrains-pycharm
EOF

# 9. Permissions
chown -R root:root /opt/pycharm
chmod -R 755 /opt/pycharm

# 10. Documentation utilisateurs
cat > /opt/pycharm/README.local <<'EOF'
PyCharm Community Edition
Installed: Feb 3, 2026
Location: /opt/pycharm
Symlink: /usr/local/bin/pycharm
Desktop: /usr/share/applications/pycharm.desktop

To launch: Run 'pycharm' from terminal
To update: Download new version, extract to /opt/, update symlink
EOF
```

---

### Scénario 5️⃣ : Analyser /proc pour monitoring processus

**Contexte** : Débugger processus consommant trop de mémoire.

```bash
# 1. Identifier processus gourmand
ps aux --sort=-%mem | head
# USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
# alice    12345  0.5 25.3 4123456 4096000 ?    Sl   10:00   0:15 /usr/bin/chrome

PID=12345

# 2. Informations dans /proc
ls /proc/$PID/
# cmdline  cwd@  environ  exe@  fd/  maps  status  ...

# 3. Ligne de commande
cat /proc/$PID/cmdline | tr '\0' ' '
# /usr/bin/chrome --type=renderer --enable-features=...

# 4. Statut processus
cat /proc/$PID/status | grep -E "Name|State|Pid|PPid|VmSize|VmRSS"
# Name:   chrome
# State:  S (sleeping)
# Pid:    12345
# PPid:   1234
# VmSize: 4123456 kB  (Virtual Memory)
# VmRSS:  4096000 kB  (Resident Set Size = RAM physique)

# 5. Mappings mémoire
cat /proc/$PID/maps | head
# 00400000-00500000 r-xp 00000000 08:01 131072    /usr/bin/chrome
# 00700000-00800000 rw-p 00100000 08:01 131072    /usr/bin/chrome
# 7f1234567000-7f1234568000 r--p ... /usr/lib/x86_64-linux-gnu/libc.so.6

# 6. Fichiers ouverts
ls -l /proc/$PID/fd/
# lr-x------ 1 alice alice 64 Feb  3 10:00 0 -> /dev/null
# lrwx------ 1 alice alice 64 Feb  3 10:00 1 -> /dev/pts/0
# lr-x------ 1 alice alice 64 Feb  3 10:00 3 -> /home/alice/data.db

# 7. Variables environnement
cat /proc/$PID/environ | tr '\0' '\n' | grep PATH
# PATH=/usr/local/bin:/usr/bin:/bin

# 8. Limites processus
cat /proc/$PID/limits
# Limit                     Soft Limit           Hard Limit           Units
# Max open files            1024                 1048576              files
# Max address space         unlimited            unlimited            bytes

# 9. Statistiques CPU
cat /proc/$PID/stat | awk '{print "utime: "$14" stime: "$15}'
# utime: 123456 stime: 78901  (jiffies)

# 10. Répertoire courant
readlink /proc/$PID/cwd
# /home/alice/projects/myapp
```

---

### Scénario 6️⃣ : Organiser /srv pour serveur multi-sites

**Contexte** : Serveur hébergeant plusieurs sites web et services.

```bash
# 1. Structure /srv
mkdir -p /srv/{www,ftp,git}

# 2. Sites web
mkdir -p /srv/www/{example.com,blog.example.com,api.example.com}

# 3. Contenu site principal
mkdir -p /srv/www/example.com/{public,logs,backups}

cat > /srv/www/example.com/public/index.html <<'EOF'
<!DOCTYPE html>
<html>
<head><title>Example</title></head>
<body><h1>Welcome to Example.com</h1></body>
</html>
EOF

# 4. Permissions
chown -R www-data:www-data /srv/www/
chmod -R 755 /srv/www/

# 5. Configuration Apache
cat > /etc/apache2/sites-available/example.com.conf <<'EOF'
<VirtualHost *:80>
    ServerName example.com
    ServerAdmin admin@example.com
    DocumentRoot /srv/www/example.com/public
    ErrorLog /srv/www/example.com/logs/error.log
    CustomLog /srv/www/example.com/logs/access.log combined

    <Directory /srv/www/example.com/public>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
EOF

# 6. Activer site
a2ensite example.com
systemctl reload apache2

# 7. Serveur FTP
mkdir -p /srv/ftp/{public,upload}
chown -R ftp:ftp /srv/ftp/
chmod 755 /srv/ftp/public
chmod 733 /srv/ftp/upload  # Write-only pour upload

# 8. Dépôts Git
mkdir -p /srv/git/{project1.git,project2.git}

cd /srv/git/project1.git
git init --bare
chown -R git:git /srv/git/

# 9. Structure complète
tree -L 3 /srv/
# /srv/
# ├── www/
# │   ├── example.com/
# │   │   ├── public/
# │   │   ├── logs/
# │   │   └── backups/
# │   ├── blog.example.com/
# │   └── api.example.com/
# ├── ftp/
# │   ├── public/
# │   └── upload/
# └── git/
#     ├── project1.git/
#     └── project2.git/

# 10. Documentation
cat > /srv/README.txt <<'EOF'
/srv - Service Data Directory

/srv/www/          - Web sites
  example.com/     - Main site (Apache vhost)
  blog.*/          - Blog (Apache vhost)
  api.*/           - API (Apache vhost)

/srv/ftp/          - FTP server data
  public/          - Public downloads
  upload/          - User uploads (write-only)

/srv/git/          - Git repositories
  *.git/           - Bare repositories

Permissions:
  www: www-data:www-data (755)
  ftp: ftp:ftp (755/733)
  git: git:git (755)
EOF
```

---

### Scénario 7️⃣ : Débugger boot avec /boot et /proc

**Contexte** : Système ne boot pas, analyser noyau et initramfs.

```bash
# === Mode rescue (boot USB Live) ===

# 1. Monter partition /boot
mount /dev/sda1 /mnt/boot

ls -lh /mnt/boot/
# -rw-r--r-- 1 root root  10M Jan 15 10:00 vmlinuz-6.1.0-17-amd64
# -rw-r--r-- 1 root root  30M Jan 15 10:00 initrd.img-6.1.0-17-amd64
# -rw-r--r-- 1 root root 8.5M Jan 15 10:00 vmlinuz-6.1.0-16-amd64  ← Ancien
# drwxr-xr-x 5 root root 4.0K Jan 15 10:00 grub/

# 2. Vérifier configuration GRUB
cat /mnt/boot/grub/grub.cfg | grep "menuentry "
# menuentry 'Debian GNU/Linux' ...
# menuentry 'Debian GNU/Linux, with Linux 6.1.0-17-amd64' ...

# 3. Problème : Nouveau noyau corrompu
# Solution : Booter ancien noyau
# Éditer GRUB (redémarrer, appuyer 'e' au boot)
# Changer : linux /vmlinuz-6.1.0-17-amd64 ...
#       → linux /vmlinuz-6.1.0-16-amd64 ...

# 4. Après boot réussi avec ancien noyau
uname -r
# 6.1.0-16-amd64

# 5. Vérifier noyau disponibles
ls -lh /boot/vmlinuz*
# -rw-r--r-- 1 root root  10M Jan 15 10:00 vmlinuz-6.1.0-17-amd64  ← Problème
# -rw-r--r-- 1 root root 8.5M Jan 10 10:00 vmlinuz-6.1.0-16-amd64  ← OK

# 6. Réinstaller noyau corrompu
apt install --reinstall linux-image-6.1.0-17-amd64

# 7. Vérifier initramfs
ls -lh /boot/initrd.img*
# -rw-r--r-- 1 root root 30M Feb  3 15:30 initrd.img-6.1.0-17-amd64  ← Nouveau
# -rw-r--r-- 1 root root 28M Jan 10 10:00 initrd.img-6.1.0-16-amd64

# 8. Mettre à jour GRUB
update-grub
# Generating grub configuration file ...
# Found linux image: /boot/vmlinuz-6.1.0-17-amd64
# Found initrd image: /boot/initrd.img-6.1.0-17-amd64
# done

# 9. Après boot OK, vérifier noyau actuel
cat /proc/version
# Linux version 6.1.0-17-amd64 (debian-kernel@lists.debian.org) ...

# 10. Nettoyer anciens noyaux
apt autoremove --purge
```

---

### Scénario 8️⃣ : Trouver binaires avec find et which

**Contexte** : Localiser toutes versions Python installées.

```bash
# 1. Avec which (seulement PATH)
which python python3 python3.11
# /usr/bin/python3
# /usr/bin/python3.11

# 2. Toutes versions dans PATH
which -a python3
# /usr/bin/python3
# /usr/local/bin/python3

# 3. Recherche globale avec find
find /usr -name "python*" -type f -executable 2>/dev/null
# /usr/bin/python3
# /usr/bin/python3.11
# /usr/bin/python3.10
# /usr/local/bin/python3.12

# 4. Avec whereis (binaires + sources + man)
whereis python3
# python3: /usr/bin/python3 /usr/lib/python3.11 /etc/python3.11 /usr/share/man/man1/python3.1.gz

# 5. Vérifier liens symboliques
ls -l /usr/bin/python*
# lrwxrwxrwx 1 root root    9 Feb  3 10:00 /usr/bin/python3 -> python3.11
# -rwxr-xr-x 1 root root 5.9M Feb  3 10:00 /usr/bin/python3.11
# -rwxr-xr-x 1 root root 5.7M Jan 15 10:00 /usr/bin/python3.10

# 6. Versions
/usr/bin/python3.11 --version
# Python 3.11.7

/usr/bin/python3.10 --version
# Python 3.10.12

# 7. locate (rapide)
locate -b '\python3'
# /usr/bin/python3
# /usr/bin/python3.11

# 8. Bibliothèques associées
find /usr/lib -name "python*" -type d
# /usr/lib/python3.11
# /usr/lib/python3.10

# 9. Trouver scripts Python système
find /usr/bin -type f -exec file {} \; | grep "Python script"
# /usr/bin/pip3: Python script, ASCII text executable

# 10. Récapitulatif
cat > /tmp/python_inventory.txt <<EOF
Python installations found:

Executables:
$(find /usr -name "python*" -type f -executable 2>/dev/null)

Libraries:
$(find /usr/lib -maxdepth 1 -name "python*" -type d)

Current default (python3):
$(readlink -f /usr/bin/python3)

Version: $(python3 --version)
EOF

cat /tmp/python_inventory.txt
```

---

### Scénario 9️⃣ : Gérer /home avec quotas utilisateurs

**Contexte** : Limiter espace disque par utilisateur.

```bash
# 1. Vérifier /home actuel
df -h /home
# Filesystem      Size  Used Avail Use% Mounted on
# /dev/sda3       200G  150G   50G  75% /home

# 2. Usage par utilisateur
du -sh /home/*
# 50G   /home/alice
# 40G   /home/bob
# 30G   /home/charlie
# 20G   /home/david

# 3. Installer quotas
apt install quota

# 4. Activer quotas sur /home (fstab)
grep /home /etc/fstab
# /dev/sda3  /home  ext4  defaults  0  2

# Modifier avec usrquota,grpquota
sed -i 's|/dev/sda3  /home  ext4  defaults|/dev/sda3  /home  ext4  defaults,usrquota,grpquota|' /etc/fstab

cat /etc/fstab | grep /home
# /dev/sda3  /home  ext4  defaults,usrquota,grpquota  0  2

# 5. Remonter /home
mount -o remount /home

# 6. Créer fichiers quotas
quotacheck -cugm /home
# Creates: /home/aquota.user, /home/aquota.group

ls -l /home/aquota.*
# -rw------- 1 root root 8192 Feb  3 15:00 /home/aquota.user
# -rw------- 1 root root 8192 Feb  3 15:00 /home/aquota.group

# 7. Activer quotas
quotaon /home

# 8. Définir quotas (20GB soft, 25GB hard)
setquota -u alice 20G 25G 0 0 /home
setquota -u bob 15G 20G 0 0 /home
setquota -u charlie 10G 12G 0 0 /home

# Format: setquota -u USER SOFT_BLOCKS HARD_BLOCKS SOFT_INODES HARD_INODES FILESYSTEM

# 9. Vérifier quotas
quota -u alice
# Disk quotas for user alice (uid 1000):
#      Filesystem  blocks   quota   limit   grace   files   quota   limit   grace
#       /dev/sda3   50000000 20000000 25000000   7days       0       0       0

repquota /home
# *** Report for user quotas on device /dev/sda3
# Block grace time: 7days; Inode grace time: 7days
#                         Block limits                File limits
# User            used    soft    hard  grace    used  soft  hard  grace
# alice     --   50000   20000   25000   7days      0     0     0
# bob       --   40000   15000   20000   7days      0     0     0

# 10. Notification utilisateurs (mail automatique)
# Alice dépasse soft limit → grace period 7 jours
# Si hard limit atteint → impossible d'écrire
```

---

### Scénario 🔟 : Sauvegarder et restaurer /etc

**Contexte** : Créer backup configuration avant changements majeurs.

```bash
# 1. Vérifier taille /etc
du -sh /etc
# 25M  /etc

# 2. Créer backup complet
DATE=$(date +%Y%m%d_%H%M%S)
tar czf /var/backups/etc-backup-$DATE.tar.gz /etc/

ls -lh /var/backups/etc-backup*
# -rw-r--r-- 1 root root 8.5M Feb  3 15:30 /var/backups/etc-backup-20260203_153000.tar.gz

# 3. Vérifier contenu archive
tar tzf /var/backups/etc-backup-$DATE.tar.gz | head
# etc/
# etc/passwd
# etc/group
# etc/fstab
# etc/hostname

# 4. Sauvegarder fichiers critiques seulement
tar czf /var/backups/etc-critical-$DATE.tar.gz \
    /etc/passwd \
    /etc/shadow \
    /etc/group \
    /etc/fstab \
    /etc/hostname \
    /etc/network/ \
    /etc/ssh/ \
    /etc/apt/

# 5. Versioning avec rsync
mkdir -p /var/backups/etc-versions/$DATE
rsync -av /etc/ /var/backups/etc-versions/$DATE/

# Hard links pour fichiers inchangés (économie espace)
LAST=$(ls -1 /var/backups/etc-versions/ | tail -2 | head -1)
rsync -av --link-dest=/var/backups/etc-versions/$LAST \
    /etc/ /var/backups/etc-versions/$DATE/

# 6. Comparer avec backup précédent
diff -r /etc /var/backups/etc-versions/$LAST | head
# Only in /etc/: newfile.conf
# diff /etc/ssh/sshd_config /var/backups/etc-versions/20260202_100000/ssh/sshd_config
# 15c15
# < PermitRootLogin no
# > PermitRootLogin yes

# 7. Restaurer fichier spécifique
tar xzf /var/backups/etc-backup-$DATE.tar.gz -C / etc/ssh/sshd_config

# Ou depuis rsync
cp /var/backups/etc-versions/$LAST/ssh/sshd_config /etc/ssh/

# 8. Restauration complète (mode rescue)
# Boot en rescue mode
mount /dev/sda1 /mnt
cd /mnt
tar xzf /var/backups/etc-backup-20260203_153000.tar.gz

# 9. Automatisation : Backup quotidien
cat > /etc/cron.daily/backup-etc <<'EOF'
#!/bin/bash
DATE=$(date +%Y%m%d)
tar czf /var/backups/etc-backup-$DATE.tar.gz /etc/

# Garder 30 derniers jours
find /var/backups -name "etc-backup-*.tar.gz" -mtime +30 -delete
EOF

chmod +x /etc/cron.daily/backup-etc

# 10. Script de restauration interactive
cat > /usr/local/bin/restore-etc <<'EOF'
#!/bin/bash

echo "Backups disponibles :"
ls -lh /var/backups/etc-backup-* | nl

read -p "Numéro du backup à restaurer : " NUM
BACKUP=$(ls -1 /var/backups/etc-backup-* | sed -n "${NUM}p")

echo "Restaurer depuis : $BACKUP"
read -p "Continuer ? (yes/no) : " CONFIRM

if [ "$CONFIRM" = "yes" ]; then
    tar xzf "$BACKUP" -C /
    echo "Restauration terminée"
    echo "Redémarrez les services : systemctl daemon-reload"
else
    echo "Annulé"
fi
EOF

chmod +x /usr/local/bin/restore-etc
```

---

## 🚨 10 Erreurs Courantes et Solutions

### Erreur 1️⃣ : Confusion /bin vs /usr/bin (systèmes modernes)

**Symptôme** :
```bash
# Documentation ancienne dit : "Copier dans /bin"
cp myscript /bin/

ls -ld /bin
# lrwxrwxrwx 1 root root 7 Jan 15 10:00 /bin -> usr/bin

# Fichier finit dans /usr/bin
ls -l /usr/bin/myscript
# -rwxr-xr-x 1 root root 256 Feb  3 10:00 /usr/bin/myscript
```

**Cause** : Distributions modernes fusionnent `/bin` → `/usr/bin` (symlink).

**Solutions** :

```bash
# 1. Vérifier si fusion
ls -ld /bin /sbin /lib
# lrwxrwxrwx 1 root root 7 Jan 15 10:00 /bin -> usr/bin
# lrwxrwxrwx 1 root root 8 Jan 15 10:00 /sbin -> usr/sbin
# lrwxrwxrwx 1 root root 7 Jan 15 10:00 /lib -> usr/lib

# 2. Utiliser directement /usr/bin
cp myscript /usr/bin/

# 3. Ou /usr/local/bin (recommandé pour scripts locaux)
cp myscript /usr/local/bin/

# 4. Scripts d'installation portables
if [ -L /bin ] && [ "$(readlink /bin)" = "usr/bin" ]; then
    BINDIR="/usr/bin"
else
    BINDIR="/bin"
fi
cp myscript "$BINDIR/"
```

---

### Erreur 2️⃣ : /tmp plein empêche création fichiers temporaires

**Symptôme** :
```bash
mktemp
# mktemp: failed to create file via template '/tmp/tmp.XXXXXXXXXX': No space left on device
```

**Cause** : `/tmp` plein (surtout si tmpfs avec limite de taille).

**Solutions** :

```bash
# 1. Vérifier espace
df -h /tmp
# Filesystem      Size  Used Avail Use% Mounted on
# tmpfs           3.2G  3.2G     0 100% /tmp

# 2. Identifier gros fichiers
du -sh /tmp/* | sort -rh | head
# 2.0G  /tmp/largefile.iso
# 500M  /tmp/backup.tar.gz

# 3. Supprimer fichiers inutiles
rm /tmp/largefile.iso

# 4. Si tmpfs trop petit, augmenter
mount -o remount,size=10G /tmp

# Permanent (fstab)
echo "tmpfs /tmp tmpfs defaults,size=10G 0 0" >> /etc/fstab

# 5. Alternative : Utiliser /var/tmp (disque)
TMPFILE=$(mktemp -p /var/tmp)

# 6. Variable TMPDIR
export TMPDIR=/var/tmp
mktemp  # Utilise /var/tmp au lieu de /tmp

# 7. Nettoyer automatiquement
systemd-tmpfiles --clean
```

---

### Erreur 3️⃣ : Permissions /root trop ouvertes

**Symptôme** :
```bash
ls -ld /root
# drwxr-xr-x 5 root root 4096 Feb  3 10:00 /root
#       │  │
#       └──┴─ Groupe et others peuvent lire !
```

**Cause** : Permissions incorrectes sur répertoire root (risque sécurité).

**Solutions** :

```bash
# 1. Vérifier contenu
ls -la /root/
# -rw------- 1 root root  123 Feb  3 10:00 .bash_history
# -rw-r--r-- 1 root root  570 Feb  3 10:00 .bashrc
# drwx------ 2 root root 4096 Feb  3 10:00 .ssh/
# -rw------- 1 root root 3243 Feb  3 10:00 private_key.pem  ← Sensible !

# 2. Corriger permissions /root
chmod 700 /root

ls -ld /root
# drwx------ 5 root root 4096 Feb  3 10:00 /root

# 3. Vérifier fichiers sensibles
chmod 600 /root/.bash_history
chmod 700 /root/.ssh
chmod 600 /root/.ssh/*

# 4. Audit complet
find /root -type f -perm /077 -ls
# Fichiers lisibles par groupe ou others

# Corriger tous
find /root -type f -exec chmod 600 {} \;
find /root -type d -exec chmod 700 {} \;

# 5. Script de vérification
cat > /usr/local/bin/check-root-perms <<'EOF'
#!/bin/bash
PERMS=$(stat -c '%a' /root)
if [ "$PERMS" != "700" ]; then
    echo "⚠️  /root permissions: $PERMS (should be 700)"
    exit 1
fi
echo "✓ /root permissions OK"
EOF

chmod +x /usr/local/bin/check-root-perms
```

---

### Erreur 4️⃣ : /boot plein empêche mises à jour noyau

**Symptôme** :
```bash
apt upgrade
# The following packages have been kept back:
#   linux-image-amd64

df -h /boot
# Filesystem      Size  Used Avail Use% Mounted on
# /dev/sda1       512M  510M    2M 100% /boot
```

**Cause** : Anciens noyaux non supprimés.

**Solutions** :

```bash
# 1. Lister noyaux installés
dpkg --list | grep linux-image
# ii  linux-image-6.1.0-15-amd64
# ii  linux-image-6.1.0-16-amd64
# ii  linux-image-6.1.0-17-amd64
# ii  linux-image-6.1.0-18-amd64  ← Actuel
# ii  linux-image-6.1.0-19-amd64

# 2. Noyau actuel
uname -r
# 6.1.0-18-amd64

# 3. Supprimer anciens (garder actuel + 1 ancien)
apt remove --purge linux-image-6.1.0-15-amd64
apt remove --purge linux-image-6.1.0-16-amd64

# 4. Nettoyer automatiquement
apt autoremove --purge

# 5. Vérifier espace libéré
df -h /boot
# Filesystem      Size  Used Avail Use% Mounted on
# /dev/sda1       512M  250M  262M  49% /boot

# 6. Mettre à jour GRUB
update-grub

# 7. Automatisation : Garder seulement 2 noyaux
cat > /etc/apt/apt.conf.d/50unattended-upgrades <<'EOF'
Unattended-Upgrade::Remove-Unused-Kernel-Packages "true";
Unattended-Upgrade::Remove-Unused-Dependencies "true";
EOF

# 8. Script manuel
cat > /usr/local/bin/clean-old-kernels <<'EOF'
#!/bin/bash
CURRENT=$(uname -r | sed 's/-amd64//')
dpkg --list | grep linux-image | grep -v "$CURRENT" | awk '{print $2}' | grep -v "$(uname -r)" | xargs apt remove --purge -y
update-grub
EOF

chmod +x /usr/local/bin/clean-old-kernels
```

---

### Erreur 5️⃣ : Fichiers dans /usr/local non trouvés (PATH)

**Symptôme** :
```bash
# Installation script dans /usr/local/bin
cp myscript /usr/local/bin/
chmod +x /usr/local/bin/myscript

# Tenter exécution
myscript
# bash: myscript: command not found
```

**Cause** : `/usr/local/bin` pas dans `$PATH` (utilisateur).

**Solutions** :

```bash
# 1. Vérifier PATH
echo $PATH
# /usr/bin:/bin  ← Manque /usr/local/bin

# 2. Exécuter avec chemin complet
/usr/local/bin/myscript

# 3. Ajouter à PATH temporairement
export PATH="/usr/local/bin:$PATH"
myscript  # ✅ Fonctionne

# 4. Permanent (utilisateur) : ~/.bashrc
echo 'export PATH="/usr/local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

# 5. Permanent (système) : /etc/environment
echo 'PATH="/usr/local/bin:/usr/bin:/bin"' | sudo tee /etc/environment

# 6. Vérifier configuration par défaut
cat /etc/profile | grep PATH
# PATH="/usr/local/bin:/usr/bin:/bin"

# 7. Si manquant, ajouter
cat >> /etc/profile <<'EOF'
if [ -d /usr/local/bin ]; then
    PATH="/usr/local/bin:$PATH"
fi
EOF

# 8. Recharger profil
source /etc/profile

# 9. Vérifier utilisateurs
su - alice
echo $PATH
# /usr/local/bin:/usr/bin:/bin  ✅
```

---

### Erreur 6️⃣ : /var/log énorme ralentit système

**Symptôme** :
```bash
df -h
# Filesystem      Size  Used Avail Use% Mounted on
# /dev/sda2       100G   95G    5G  95% /var

du -sh /var/*
# 90G   /var/log
```

**Cause** : Logs non rotés, accumulation.

**Solutions** :

```bash
# 1. Identifier gros fichiers
du -sh /var/log/* | sort -rh | head
# 50G   /var/log/application.log
# 20G   /var/log/syslog
# 10G   /var/log/apache2/access.log

# 2. Vérifier logrotate
systemctl status logrotate
# Active: active (exited) since ...

# 3. Configuration logrotate
ls /etc/logrotate.d/
# apache2  apt  dpkg  rsyslog

# 4. Créer rotation pour application
cat > /etc/logrotate.d/application <<'EOF'
/var/log/application.log {
    daily
    rotate 7
    compress
    delaycompress
    missingok
    notifempty
    create 0644 root root
}
EOF

# 5. Tester
logrotate -d /etc/logrotate.d/application

# 6. Forcer rotation immédiate
logrotate -f /etc/logrotate.conf

# 7. Vérifier résultat
ls -lh /var/log/application.log*
# -rw-r--r-- 1 root root   10M Feb  3 16:00 application.log
# -rw-r--r-- 1 root root  2.1M Feb  2 23:59 application.log.1.gz
# -rw-r--r-- 1 root root  1.9M Feb  1 23:59 application.log.2.gz

# 8. Nettoyer anciens logs manuellement
find /var/log -name "*.log" -mtime +30 -exec gzip {} \;
find /var/log -name "*.gz" -mtime +90 -delete

# 9. Vérifier espace libéré
df -h /var
# Filesystem      Size  Used Avail Use% Mounted on
# /dev/sda2       100G   20G   80G  20% /var

# 10. Monitoring futur
cat > /etc/cron.daily/check-var-log-size <<'EOF'
#!/bin/bash
SIZE=$(du -s /var/log | awk '{print $1}')
LIMIT=$((10 * 1024 * 1024))  # 10GB en KB

if [ $SIZE -gt $LIMIT ]; then
    echo "/var/log dépasse 10GB: $(du -sh /var/log)" | mail -s "Alert: /var/log size" root
fi
EOF

chmod +x /etc/cron.daily/check-var-log-size
```

---

### Erreur 7️⃣ : /proc non monté (mode rescue)

**Symptôme** :
```bash
# En mode rescue
ps aux
# Error: /proc must be mounted

ls /proc/
# (vide ou n'existe pas)
```

**Cause** : `/proc` non monté (filesystem virtuel).

**Solutions** :

```bash
# 1. Monter /proc
mount -t proc proc /proc

# 2. Vérifier
ls /proc/
# 1/  123/  cpuinfo  meminfo  version  ...

ps aux
# ✅ Fonctionne

# 3. Si en chroot
mount -t proc proc /mnt/proc
mount -t sysfs sys /mnt/sys
mount -t devtmpfs dev /mnt/dev
mount -t devpts devpts /mnt/dev/pts

chroot /mnt
# Maintenant /proc accessible dans chroot

# 4. Montage automatique (fstab)
grep proc /etc/fstab
# proc  /proc  proc  defaults  0  0

# Si manquant, ajouter
echo "proc /proc proc defaults 0 0" >> /etc/fstab

# 5. Monter tous (fstab)
mount -a

# 6. Script boot rescue
cat > /root/mount-virtual-fs.sh <<'EOF'
#!/bin/bash
mount -t proc proc /proc
mount -t sysfs sys /sys
mount -t devtmpfs dev /dev
mount -t devpts devpts /dev/pts
mount -t tmpfs tmpfs /run
EOF

chmod +x /root/mount-virtual-fs.sh
```

---

### Erreur 8️⃣ : locate obsolète (base non mise à jour)

**Symptôme** :
```bash
# Créer fichier
echo "test" > /tmp/newfile.txt

# locate ne le trouve pas
locate newfile.txt
# (aucun résultat)

# find fonctionne
find /tmp -name "newfile.txt"
# /tmp/newfile.txt
```

**Cause** : Base de données `locate` mise à jour quotidiennement (cron).

**Solutions** :

```bash
# 1. Mettre à jour manuellement
updatedb

# 2. Chercher à nouveau
locate newfile.txt
# /tmp/newfile.txt  ✅

# 3. Vérifier cron updatedb
ls -l /etc/cron.daily/
# -rwxr-xr-x 1 root root 256 Jan 15 10:00 mlocate

cat /etc/cron.daily/mlocate
# #!/bin/sh
# /usr/bin/updatedb

# 4. Forcer exécution immédiate
/etc/cron.daily/mlocate

# 5. Statistiques base
locate -S
# Database: /var/lib/mlocate/mlocate.db
# File names: 3,456,789
# Directories: 234,567
# File sizes: 123 MB
# Timestamp: Feb  3 03:00:00 2026  ← Dernière mise à jour

# 6. Si updatedb lent, exclure chemins
cat /etc/updatedb.conf
# PRUNEPATHS="/tmp /var/tmp /var/cache /var/spool"
# PRUNEFS="NFS nfs nfs4 rpc_pipefs"

# 7. Alternative : find (temps réel)
find / -name "fichier.txt" 2>/dev/null
```

---

### Erreur 9️⃣ : /media mount échoue (permissions)

**Symptôme** :
```bash
# Insérer clé USB
# Pas de montage automatique

dmesg | tail
# [123.456] usb 1-1: new high-speed USB device number 2 using xhci_hcd
# [123.789] sd 2:0:0:0: [sdb] 15728640 512-byte logical blocks
# [124.123] sdb: sdb1

# Monter manuellement
mount /dev/sdb1 /media/alice/USB
# mount: /media/alice/USB: mount point does not exist
```

**Cause** : Point de montage inexistant, permissions.

**Solutions** :

```bash
# 1. Créer point de montage
mkdir -p /media/alice/USB

# 2. Permissions
chown alice:alice /media/alice/USB

# 3. Monter
mount /dev/sdb1 /media/alice/USB

ls /media/alice/USB/
# Documents/  Photos/

# 4. Démonter proprement
umount /media/alice/USB

# 5. Montage automatique (udisks2)
apt install udisks2

systemctl start udisks2
systemctl enable udisks2

# 6. Vérifier automount
udisksctl mount -b /dev/sdb1
# Mounted /dev/sdb1 at /media/alice/USB_DRIVE

# 7. Informations device
udisksctl info -b /dev/sdb1

# 8. Démonter avec udisks
udisksctl unmount -b /dev/sdb1

# 9. Règles udev (montage automatique personnalisé)
cat > /etc/udev/rules.d/99-usb-automount.rules <<'EOF'
ACTION=="add", KERNEL=="sd[a-z][0-9]", SUBSYSTEMS=="usb", RUN+="/usr/local/bin/usb-mount.sh %k"
ACTION=="remove", KERNEL=="sd[a-z][0-9]", SUBSYSTEMS=="usb", RUN+="/usr/local/bin/usb-umount.sh %k"
EOF

# 10. Script montage
cat > /usr/local/bin/usb-mount.sh <<'EOF'
#!/bin/bash
DEVICE=$1
MOUNTPOINT="/media/$DEVICE"
mkdir -p "$MOUNTPOINT"
mount "/dev/$DEVICE" "$MOUNTPOINT"
EOF

chmod +x /usr/local/bin/usb-mount.sh
```

---

### Erreur 🔟 : /opt app ne démarre pas (bibliothèques manquantes)

**Symptôme** :
```bash
/opt/myapp/bin/myapp
# error while loading shared libraries: libfoo.so.1: cannot open shared object file: No such file or directory
```

**Cause** : Bibliothèques dans `/opt` non trouvées (pas dans path standard).

**Solutions** :

```bash
# 1. Vérifier dépendances
ldd /opt/myapp/bin/myapp
# linux-vdso.so.1 (0x00007ffc123ab000)
# libfoo.so.1 => not found  ← Problème
# libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6

# 2. Localiser bibliothèque
find /opt/myapp -name "libfoo.so*"
# /opt/myapp/lib/libfoo.so.1

# 3. Solution 1 : Variable LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/opt/myapp/lib:$LD_LIBRARY_PATH
/opt/myapp/bin/myapp  # ✅ Fonctionne

# 4. Solution 2 : Configuration ld.so
cat > /etc/ld.so.conf.d/myapp.conf <<'EOF'
/opt/myapp/lib
EOF

ldconfig  # Recharger cache

# Vérifier
ldconfig -p | grep libfoo
# libfoo.so.1 (libc6,x86-64) => /opt/myapp/lib/libfoo.so.1

ldd /opt/myapp/bin/myapp
# libfoo.so.1 => /opt/myapp/lib/libfoo.so.1  ✅

# 5. Solution 3 : Script wrapper
cat > /usr/local/bin/myapp <<'EOF'
#!/bin/bash
export LD_LIBRARY_PATH=/opt/myapp/lib:$LD_LIBRARY_PATH
exec /opt/myapp/bin/myapp "$@"
EOF

chmod +x /usr/local/bin/myapp

# 6. Solution 4 : RPATH (compilation)
# (si vous compilez l'app)
gcc -Wl,-rpath,/opt/myapp/lib -o myapp myapp.c -L/opt/myapp/lib -lfoo

# 7. Vérifier RPATH
readelf -d /opt/myapp/bin/myapp | grep RPATH
# 0x000000000000000f (RPATH) Library rpath: [/opt/myapp/lib]

# 8. Tester lancement
/opt/myapp/bin/myapp
# ✅ Fonctionne sans LD_LIBRARY_PATH
```

---

## 📝 Antisèche (Cheat Sheet)

### Structure FHS - Répertoires Racine

| Répertoire | Type | Rôle | Contenu typique |
|------------|------|------|-----------------|
| `/bin` | Statique | Binaires essentiels | `ls`, `cat`, `bash`, `cp`, `mv` |
| `/boot` | Statique | Démarrage système | `vmlinuz`, `initrd.img`, `grub/` |
| `/dev` | Virtuel | Périphériques | `sda`, `tty`, `null`, `random` |
| `/etc` | Statique | Configuration | `passwd`, `fstab`, `hostname` |
| `/home` | Variable | Répertoires utilisateurs | `/home/alice/`, `/home/bob/` |
| `/lib` | Statique | Bibliothèques essentielles | `libc.so.6`, `libm.so.6` |
| `/media` | Variable | Montages amovibles auto | `/media/alice/USB_DRIVE/` |
| `/mnt` | Variable | Montages temporaires manuels | `/mnt/nfs_share/` |
| `/opt` | Statique | Applications tierces | `/opt/google/`, `/opt/pycharm/` |
| `/proc` | Virtuel | Processus et noyau | `/proc/cpuinfo`, `/proc/1/` |
| `/root` | Variable | Home de root | `.bashrc`, `.ssh/` |
| `/run` | Virtuel | Runtime (tmpfs) | `nginx.pid`, `systemd/` |
| `/sbin` | Statique | Binaires admin | `fdisk`, `mount`, `ip` |
| `/srv` | Variable | Données servies | `/srv/www/`, `/srv/ftp/` |
| `/sys` | Virtuel | Hardware et drivers | `/sys/block/`, `/sys/class/` |
| `/tmp` | Variable | Temporaires (effacés) | Fichiers temporaires |
| `/usr` | Statique | Programmes utilisateur | `/usr/bin/`, `/usr/lib/`, `/usr/share/` |
| `/var` | Variable | Données variables | `/var/log/`, `/var/cache/` |

### Hiérarchie /usr

```
/usr/
├── bin/       Commandes utilisateur (non essentielles)
├── sbin/      Commandes admin (non essentielles)
├── lib/       Bibliothèques
├── include/   Headers C/C++
├── local/     Installations locales
│   ├── bin/
│   ├── lib/
│   └── share/
├── share/     Données partagées
│   ├── man/   Pages manuel
│   ├── doc/   Documentation
│   └── locale/ Traductions
└── src/       Code source
```

### Hiérarchie /var

```
/var/
├── log/       Logs système et applications
├── cache/     Caches applicatifs
├── tmp/       Temporaires (préservés entre reboots)
├── spool/     Files d'attente (mail, impression)
├── lib/       État applications (bases de données)
├── mail/      Boîtes mail
└── www/       Données web (alternative /srv/www)
```

### Commandes de Localisation

```bash
# find - Recherche filesystem réel
find /etc -name "*.conf"
find /home -user alice -type f
find /var/log -size +100M
find /usr/bin -perm -4000

# locate - Recherche base de données (rapide)
locate passwd
updatedb  # Mettre à jour base

# whereis - Binaire + sources + man
whereis bash
whereis -b ls  # Seulement binaire
whereis -m vim  # Seulement manuel

# which - Exécutable dans PATH
which python3
which -a python3  # Tous

# type - Type commande shell
type ls
type cd  # Builtin
```

### Vérifications Système

```bash
# Espace disque
df -h
du -sh /var/*

# Filesystems virtuels
mount | grep -E "proc|sys|dev|run"

# Fichiers récents
find /etc -mtime -7

# Gros fichiers
find /var -size +1G

# SUID/SGID
find /usr/bin -perm -4000
find /usr/bin -perm -2000
```

### Maintenance

```bash
# Nettoyer /tmp
find /tmp -type f -mtime +7 -delete
systemd-tmpfiles --clean

# Rotation logs
logrotate -f /etc/logrotate.conf

# Quotas
quota -u username
repquota /home

# Anciens noyaux
apt autoremove --purge
```

---

## 🎯 Points Clés pour l'Examen LPIC-1

### ✅ Concepts à Maîtriser Absolument

1. **FHS = Standard d'organisation**
   - Cohérence entre distributions
   - Emplacements prévisibles
   - `/` racine, branches thématiques

2. **Répertoires essentiels (objectif 104.7)**
   - **/bin** : Commandes de base (ls, cat, bash)
   - **/boot** : Noyau, initramfs, GRUB
   - **/dev** : Périphériques (sda, tty, null)
   - **/etc** : Configuration système
   - **/home** : Répertoires utilisateurs
   - **/lib** : Bibliothèques essentielles
   - **/opt** : Applications tierces
   - **/proc** : Virtuel (processus, noyau)
   - **/root** : Home de root
   - **/sbin** : Commandes admin
   - **/tmp** : Temporaires (effacés)
   - **/usr** : Programmes utilisateur
   - **/var** : Données variables (logs, caches)

3. **Filesystems virtuels**
   - **/proc** : Processus et noyau (PID, cpuinfo, meminfo)
   - **/sys** : Hardware et drivers (block, class, devices)
   - **/dev** : Périphériques (udev dynamique)
   - **/run** : Runtime tmpfs (PIDs, sockets)

4. **Fusion moderne /bin → /usr/bin**
   - Debian, Ubuntu, Fedora : symlinks
   - `/bin -> usr/bin`, `/sbin -> usr/sbin`, `/lib -> usr/lib`
   - Simplification, historiquement séparés

5. **/usr structure**
   - `/usr/bin` : Commandes non essentielles
   - `/usr/sbin` : Admin non essentiel
   - `/usr/lib` : Bibliothèques
   - `/usr/local` : Installations locales manuelles
   - `/usr/share` : Données partagées (man, doc)

6. **/var structure**
   - `/var/log` : Logs système et apps
   - `/var/cache` : Caches
   - `/var/tmp` : Temporaires préservés (vs /tmp)
   - `/var/lib` : État applications (bases de données)
   - `/var/spool` : Files d'attente

7. **Différences clés**
   - **/tmp** : Effacé au boot, tmpfs
   - **/var/tmp** : Préservé, disque
   - **/media** : Montages auto (USB)
   - **/mnt** : Montages manuels temporaires
   - **/opt** : Apps tierces
   - **/usr/local** : Apps locales

8. **Commandes find**
   - `find /path -name "pattern"`
   - `find /path -type f` (fichier)
   - `find /path -size +100M`
   - `find /path -mtime -7` (modifiés derniers 7j)
   - `find /path -perm -4000` (SUID)

9. **Commandes locate**
   - `locate filename` (rapide, base de données)
   - `updatedb` (mettre à jour base)
   - Base obsolète si fichiers récents

10. **Commandes whereis/which**
    - `whereis cmd` : Binaire + sources + man
    - `which cmd` : Premier dans PATH
    - `type cmd` : Type (alias, builtin, fonction)

### 📚 Commandes à Connaître par Cœur

```bash
# Navigation FHS
ls -l /bin /sbin /lib  # Vérifier symlinks
ls -l /usr/bin /usr/sbin
ls -l /var/log /var/cache /var/tmp

# Recherche
find /etc -name "*.conf"
find /var/log -size +100M
locate passwd
whereis bash
which python3

# Filesystems virtuels
cat /proc/cpuinfo
cat /proc/meminfo
ls /sys/block/
ls /dev/

# Espace disque
df -h
du -sh /var/*
du -sh /home/*

# Montages
mount | grep -E "proc|sys|dev|run|tmp"
cat /etc/fstab

# Logs
ls -lh /var/log/
tail /var/log/syslog
journalctl -xe

# Temporaires
ls -ld /tmp /var/tmp
df -h /tmp
systemd-tmpfiles --clean
```

### ⚠️ Pièges d'Examen Fréquents

1. **/bin vs /usr/bin fusion moderne**
   - Systèmes récents : `/bin` → `/usr/bin` (symlink)
   - Scripts anciens peuvent référencer `/bin`

2. **/tmp vs /var/tmp**
   - `/tmp` : Effacé au boot
   - `/var/tmp` : Préservé entre reboots

3. **/media vs /mnt**
   - `/media` : Montages automatiques (USB, udisks)
   - `/mnt` : Montages manuels temporaires (admin)

4. **/opt vs /usr/local**
   - `/opt` : Apps tierces auto-contenues
   - `/usr/local` : Installations locales manuelles

5. **/proc et /sys sont VIRTUELS**
   - Générés par noyau, pas sur disque
   - Contenu disparaît à l'arrêt
   - Taille 0 avec `du` mais `df` montre usage tmpfs

6. **locate base obsolète**
   - Mise à jour quotidienne (cron)
   - Fichiers récents absents
   - Utiliser `updatedb` ou `find`

7. **/root ≠ /home/root**
   - Home de root : `/root` (pas dans /home)
   - Permissions strictes : 700

8. **/boot plein**
   - Anciens noyaux non supprimés
   - Empêche mises à jour
   - `apt autoremove --purge`

9. **which vs whereis vs type**
   - `which` : Premier dans PATH
   - `whereis` : Binaire + man + sources
   - `type` : Type (alias, builtin, fonction, fichier)

10. **find -perm modes**
    - `-perm 644` : Exactement 644
    - `-perm -644` : Au moins 644
    - `-perm /644` : N'importe quel bit

### 🔑 Valeurs à Mémoriser

**Permissions par défaut** :
```
/tmp        1777  (sticky bit)
/root       700   (privé)
/home/user  755   (ou 700)
/etc        755
/var/log    755
```

**Tailles typiques** :
```
/boot       512MB - 1GB  (partition dédiée)
/tmp        tmpfs (RAM, 50% par défaut)
/var        Variable (logs, caches)
```

**Nettoyage automatique** :
```
/tmp        10 jours  (systemd-tmpfiles)
/var/tmp    30 jours  (systemd-tmpfiles)
```

**Filesystems virtuels** :
```
/proc       proc
/sys        sysfs
/dev        devtmpfs ou udev
/run        tmpfs
```

---

## 📖 Résumé Final

Ce cours couvre le **Filesystem Hierarchy Standard (FHS)** :

1. **Structure standardisée** : Cohérence entre distributions Linux/Unix

2. **Répertoires racine essentiels** :
   - **/bin, /sbin, /lib** : Système de base (fusionnés avec /usr sur systèmes modernes)
   - **/boot** : Démarrage (noyau, GRUB)
   - **/etc** : Configuration
   - **/home** : Utilisateurs
   - **/opt** : Applications tierces
   - **/usr** : Programmes utilisateur (hiérarchie secondaire)
   - **/var** : Données variables (logs, caches, spools)

3. **Filesystems virtuels** :
   - **/proc** : Processus et noyau
   - **/sys** : Hardware
   - **/dev** : Périphériques
   - **/run** : Runtime (tmpfs)

4. **Différences critiques** :
   - `/tmp` vs `/var/tmp` (effacé vs préservé)
   - `/media` vs `/mnt` (auto vs manuel)
   - `/opt` vs `/usr/local` (tiers vs local)

5. **Commandes de localisation** :
   - `find` : Recherche filesystem réel (précis, lent)
   - `locate` : Recherche base de données (rapide, obsolète)
   - `whereis` : Binaire + sources + man
   - `which` : Premier dans PATH
   - `type` : Type commande shell

6. **Maintenance** :
   - Nettoyer `/tmp` et `/var/tmp`
   - Rotation logs (`logrotate`)
   - Gérer `/boot` (anciens noyaux)
   - Quotas `/home`

La maîtrise du FHS est **fondamentale** pour l'administration Linux et la certification LPIC-1. Savoir où trouver et placer les fichiers est essentiel au quotidien.

---

*Documentation créée le 3 février 2026 - Formation Linux Essentials / LPIC-1 - Module 4 - FIN*

**🎉 Félicitations ! Vous avez terminé le Module 4 et la section LPIC-1 101 KodeKloud Course !**
