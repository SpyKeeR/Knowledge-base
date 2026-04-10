# 📋 Journalisation Système Linux

## 📋 Informations du Module

- **Examen** : LPIC-1 102
- **Objectif** : 108.2 - System logging
- **Poids** : 4 points
- **Objectif** : Comprendre et configurer la journalisation système (rsyslog, journald, logrotate)

---

## 🎯 Vue d'Ensemble de la Journalisation

**Journalisation (Logging)** : Enregistrement des événements système, services et applications.

**Évolution historique :**

```
┌──────────────────────────────────────────────────────┐
│  HISTOIRE DE LA JOURNALISATION LINUX                 │
├──────────────────────────────────────────────────────┤
│  1. syslog (1980s)                                   │
│     → Protocole original BSD                         │
│     → Centralisation basique                         │
│                                                      │
│  2. syslog-ng (1998)                                 │
│     → Syslog "Next Generation"                       │
│     → Filtrage avancé                                │
│                                                      │
│  3. rsyslog (2004)                                   │
│     → "Rocket-fast syslog"                           │
│     → Haute performance, modules                     │
│     → Standard actuel (non-systemd)                  │
│                                                      │
│  4. systemd-journald (2012)                          │
│     → Intégration systemd                            │
│     → Logs binaires indexés                          │
│     → Remplace rsyslog sur systemd                   │
└──────────────────────────────────────────────────────┘
```

**Architecture moderne :**

```
┌─────────────────────────────────────────────────────────┐
│  FLUX DE JOURNALISATION                                 │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  Applications / Services / Kernel                       │
│         │                                               │
│         ↓                                               │
│  ┌──────────────────────────────┐                       │
│  │  BUS DE JOURNALISATION       │                       │
│  │  /dev/log (syslog socket)    │                       │
│  │  /dev/kmsg (kernel messages) │                       │
│  └──────────┬───────────────────┘                       │
│             │                                           │
│       ┌─────┴──────┐                                    │
│       │            │                                    │
│       ↓            ↓                                    │
│  ┌─────────┐  ┌──────────┐                             │
│  │ rsyslog │  │ journald │                             │
│  └────┬────┘  └────┬─────┘                             │
│       │            │                                    │
│       ↓            ↓                                    │
│  /var/log/*.log    /var/log/journal/                   │
│  (texte)           (binaire)                            │
│       │                                                 │
│       ↓                                                 │
│  Rotation (logrotate)                                   │
│  Archivage / Compression                                │
└─────────────────────────────────────────────────────────┘
```

---

# 📖 PARTIE 1 : CONCEPTS GÉNÉRAUX & OUTILS

## 🔌 Bus de Journalisation

**Devices centraux** pour la journalisation :

```bash
# Socket syslog (userspace)
ls -la /dev/log
# srw-rw-rw- 1 root root 0 Feb 11 10:00 /dev/log

# Kernel message buffer
ls -la /dev/kmsg
# crw-rw---- 1 root kmem 1, 11 Feb 11 10:00 /dev/kmsg
```

**Fonctionnement :**
- Applications écrivent dans `/dev/log` (socket Unix)
- Kernel écrit dans `/dev/kmsg`
- Daemons (rsyslog/journald) écoutent ces devices
- Redirection vers fichiers de log selon configuration

---

## 🗂️ Fichiers de Log Standards

### 📂 Emplacements Principaux

**Répertoire central : `/var/log/`**

```bash
ls -la /var/log/

# Structure typique :
# drwxr-xr-x  auth.log           # Authentification, sudo, SSH
# drwxr-xr-x  syslog             # Log central (Debian/Ubuntu)
# drwxr-xr-x  messages           # Log central (RedHat/CentOS)
# drwxr-xr-x  kern.log           # Messages kernel
# drwxr-xr-x  daemon.log         # Services/daemons
# drwxr-xr-x  boot.log           # Messages boot
# drwxr-xr-x  dpkg.log           # APT/dpkg (Debian)
# drwxr-xr-x  yum.log            # Yum (RedHat)
# drwxr-xr-x  apache2/           # Apache logs
# drwxr-xr-x  nginx/             # Nginx logs
# drwxr-xr-x  mysql/             # MySQL logs
# drwxr-xr-x  journal/           # systemd-journald (binaire)
# -rw-rw-r--  wtmp               # Connexions réussies
# -rw-rw-r--  btmp               # Connexions échouées
# -rw-rw-r--  lastlog            # Dernière connexion par user
```

### 📋 Fichiers de Log Importants

| Fichier | Distribution | Contenu |
|---------|--------------|---------|
| **auth.log** | Debian/Ubuntu | Authentification, sudo, SSH, login |
| **secure** | RedHat/CentOS | Authentification (équivalent auth.log) |
| **syslog** | Debian/Ubuntu | Log central (si pas redirigé ailleurs) |
| **messages** | RedHat/CentOS | Log central (équivalent syslog) |
| **kern.log** | Debian/Ubuntu | Messages kernel |
| **dmesg** | Toutes | Kernel ring buffer (au boot) |
| **daemon.log** | Debian/Ubuntu | Services système |
| **boot.log** | Toutes | Messages boot |
| **dpkg.log** | Debian/Ubuntu | Installation/suppression packages |
| **yum.log** | RedHat/CentOS | Yum package manager |
| **apache2/access.log** | Toutes | Accès HTTP Apache |
| **apache2/error.log** | Toutes | Erreurs Apache |
| **nginx/access.log** | Toutes | Accès HTTP Nginx |
| **mysql/error.log** | Toutes | Erreurs MySQL |

### 🔐 Fichiers de Connexion

**Fichiers binaires** (nécessitent commandes spéciales) :

```bash
# wtmp : Connexions réussies (successful logins)
last
# user1    pts/0   192.168.1.10   Tue Feb 11 14:35   still logged in
# user2    tty1                   Tue Feb 11 10:00 - 12:00  (02:00)

last -f /var/log/wtmp

# btmp : Connexions échouées (failed logins)
lastb
# root     ssh:notty   203.0.113.5   Tue Feb 11 14:30 - 14:30  (00:00)
# admin    ssh:notty   203.0.113.5   Tue Feb 11 14:25 - 14:25  (00:00)

sudo lastb -f /var/log/btmp

# lastlog : Dernière connexion par utilisateur
lastlog
# Username         Port     From             Latest
# root             pts/0    192.168.1.10     Tue Feb 11 14:35:42 +0100 2026
# alice            pts/1    192.168.1.20     Mon Feb 10 09:12:34 +0100 2026

lastlog -u alice
```

**utmp** : Connexions actuelles (current logins)
```bash
# Fichier : /var/run/utmp (volatile, RAM)
who
# alice    pts/0        2026-02-11 14:35 (192.168.1.20)
# bob      pts/1        2026-02-11 12:00 (192.168.1.30)

w
# 14:35:42 up 5 days,  2:34,  2 users,  load average: 0.05, 0.10, 0.15
# USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
# alice    pts/0    192.168.1.20     14:35    0.00s  0.05s  0.00s w
# bob      pts/1    192.168.1.30     12:00    2:35m  0.10s  0.10s vim
```

---

## 📖 Outils de Lecture de Logs

### 📝 Commandes de Base

```bash
# Lire log texte
cat /var/log/syslog

# Lire avec pagination
less /var/log/auth.log
# Navigation : Espace (page suivante), b (page précédente), / (recherche), q (quitter)

# Dernières lignes
tail /var/log/syslog
tail -n 50 /var/log/auth.log     # 50 dernières lignes

# Suivi temps réel
tail -f /var/log/syslog
# Ctrl+C pour arrêter

# Premières lignes
head /var/log/kern.log
head -n 20 /var/log/messages

# Filtrer avec grep
grep "error" /var/log/syslog
grep -i "failed" /var/log/auth.log     # Case insensitive
grep "SSH" /var/log/auth.log | tail -20

# Compter occurrences
grep -c "error" /var/log/syslog
# 42
```

### 🗜️ Logs Compressés

**Les anciens logs sont souvent compressés (gzip).**

```bash
# Fichiers compressés
ls -lh /var/log/
# -rw-r----- syslog
# -rw-r----- syslog.1           # Rotation précédente (non compressé)
# -rw-r----- syslog.2.gz        # Compressé
# -rw-r----- syslog.3.gz
# -rw-r----- syslog.4.gz

# Lire log compressé avec zcat
zcat /var/log/syslog.2.gz

# Pagination log compressé avec zless
zless /var/log/auth.log.3.gz

# Rechercher dans log compressé avec zgrep
zgrep "error" /var/log/syslog.*.gz

# Équivalents pour bzip2
bzcat /var/log/messages.2.bz2
bzless /var/log/messages.2.bz2
bzgrep "failed" /var/log/auth.log.*.bz2

# Équivalents pour xz
xzcat /var/log/syslog.2.xz
xzless /var/log/syslog.2.xz
xzgrep "error" /var/log/syslog.*.xz
```

**Exemples pratiques :**

```bash
# Chercher "failed password" dans tous les auth.log (compressés ou non)
grep "Failed password" /var/log/auth.log
zgrep "Failed password" /var/log/auth.log.*.gz

# Combiner
{ grep "Failed password" /var/log/auth.log; \
  zgrep "Failed password" /var/log/auth.log.*.gz; } | less

# Chercher dans tous les syslog
zgrep -h "error" /var/log/syslog* | wc -l
# 1234 (total erreurs)

# Dernières erreurs dans logs rotationnés
zcat /var/log/syslog.2.gz | tail -50
```

---

## 🔔 Kernel Ring Buffer & dmesg

### 🎯 Principe

**Kernel Ring Buffer** : Zone mémoire circulaire stockant messages kernel.

**Caractéristiques :**
- Taille limitée (typiquement 256 KB - 1 MB)
- **Circulaire** : anciens messages écrasés par nouveaux
- Disponible **AVANT** démarrage rsyslog/journald
- Utilisé principalement au **boot**

**Cas d'usage :**
- Messages boot kernel
- Détection hardware
- Drivers chargés
- Erreurs kernel
- Debugging système

### 📋 Commande dmesg

```bash
# Afficher tout le ring buffer
dmesg

# Avec horodatage lisible
dmesg -T

# Sortie exemple :
# [Tue Feb 11 10:00:01 2026] Linux version 6.1.0-10-amd64 ...
# [Tue Feb 11 10:00:01 2026] DMI: Dell Inc. PowerEdge R740/...
# [Tue Feb 11 10:00:02 2026] Memory: 128GB RAM
# [Tue Feb 11 10:00:03 2026] eth0: link up, 1000Mbps, full-duplex

# Dernières lignes
dmesg | tail -20
dmesg --tail=50

# Suivi temps réel
dmesg -w
dmesg --follow

# Filtrage par niveau (priorité)
dmesg -l err        # Erreurs seulement
dmesg -l warn       # Avertissements
dmesg -l info       # Info

# Niveaux disponibles : emerg, alert, crit, err, warn, notice, info, debug

# Filtrer par facility (subsystem)
dmesg -f kern       # Kernel
dmesg -f daemon     # Daemons

# Temps depuis boot (secondes)
dmesg -T
# [    0.000000] Linux version ...
# [    2.345678] USB device detected

# Format JSON
dmesg -J

# Format humain (couleurs selon niveau)
dmesg -H

# Vider le ring buffer (root)
sudo dmesg -C

# Lire puis vider
sudo dmesg -c
```

**Exemples pratiques :**

```bash
# Voir matériel détecté
dmesg | grep -i "detected"
dmesg | grep -i "usb"
dmesg | grep -i "eth"

# Voir erreurs récentes
dmesg -l err,warn | tail -50

# Voir tout depuis boot avec time
dmesg -T | less

# Chercher problèmes disque
dmesg | grep -i "I/O error"
dmesg | grep -i "sd"

# Voir modules chargés
dmesg | grep "module"

# Debug problème réseau
dmesg | grep -iE 'eth|network|link'

# Sauvegarder dmesg au boot (utile pour debug)
dmesg > /root/dmesg-$(date +%Y%m%d-%H%M%S).log
```

**⚠️ dmesg vs journalctl -k :**
```bash
# Équivalent moderne (systemd)
journalctl -k       # Kernel messages (dmesg)
journalctl -kb      # Kernel messages du boot actuel
```

---

## 🔄 LOGROTATE - Rotation des Logs

### 🎯 Principe

**Problème :** Les logs grandissent indéfiniment → saturation disque.

**Solution :** **Logrotate** - rotation périodique des logs.

**Fonctionnement :**
1. Renomme log actuel (`syslog` → `syslog.1`)
2. Compresse ancien (`syslog.1` → `syslog.2.gz`)
3. Supprime les plus vieux (selon rétention)
4. Crée nouveau fichier vide (`syslog`)

```
Rotation exemple :
syslog         → syslog.1      (renommé)
syslog.1       → syslog.2.gz   (compressé)
syslog.2.gz    → syslog.3.gz   (renommé)
syslog.3.gz    → syslog.4.gz
syslog.4.gz    → (supprimé si rotate 4)
```

### 📝 Configuration

#### Fichier Principal - /etc/logrotate.conf

```bash
# Configuration globale
cat /etc/logrotate.conf

# Exemple :
# /etc/logrotate.conf

# Rotation hebdomadaire par défaut
weekly

# Conserver 4 semaines de logs
rotate 4

# Créer nouveau fichier après rotation
create

# Compresser anciens logs
compress

# Inclure configurations spécifiques
include /etc/logrotate.d

# Logs système
/var/log/wtmp {
    monthly
    create 0664 root utmp
    minsize 1M
    rotate 1
}

/var/log/btmp {
    missingok
    monthly
    create 0600 root utmp
    rotate 1
}
```

**Directives globales :**

| Directive | Description | Exemple |
|-----------|-------------|---------|
| **rotate N** | Nombre de rotations à conserver (fichiers numérotés en séquence) | `rotate 7` (garde 7 précédents) |
| **daily** | La commande s'exécute chaque jour (minuit) | `daily` |
| **weekly** | Rotation hebdomadaire (lundi) | `weekly` |
| **monthly** | Rotation mensuelle (1er du mois) | `monthly` |
| **yearly** | Rotation annuelle | `yearly` |
| **missingok** | L'absence du fichier n'est pas une erreur | `missingok` |
| **notifempty** | Ne pas effectuer de rotation si le fichier est vide | `notifempty` |
| **create** | Créer nouveau fichier vide après rotation | `create 0644 root root` |
| **nocreate** | Aucun nouveau fichier n'est créé par logrotate (à la charge du service) | `nocreate` |
| **compress** | Les fichiers traités sont compressés (gzip) | `compress` |
| **delaycompress** | La compression du fichier sera effectuée à la prochaine passe (syslog.1 n'est pas compressé) | `delaycompress` |
| **dateext** | Extension date (YYYYMMDD) | `dateext` |
| **maxage N** | Supprimer logs > N jours | `maxage 365` |
| **size N** | Rotation si taille > N | `size 100M` |
| **minsize N** | Taille minimale du fichier nécessitant une rotation (en dessous : pas de rotation avant intervalle) | `minsize 10M` |
| **maxsize N** | Si la taille maximale est atteinte avant l'intervalle donné, une rotation a tout de même lieu | `maxsize 500M` |
| **postrotate** | La commande spécifiée est exécutée par le shell après rotation (ligne de commande ou script) | `postrotate...endscript` |

#### Répertoire /etc/logrotate.d/

**Configurations spécifiques par service.**

```bash
ls /etc/logrotate.d/
# apache2  nginx  mysql  rsyslog  apt  dpkg  ...

# Exemple : /etc/logrotate.d/apache2
cat /etc/logrotate.d/apache2

/var/log/apache2/*.log {
    daily
    missingok
    rotate 14
    compress
    delaycompress
    notifempty
    create 0640 www-data adm
    sharedscripts
    postrotate
        if [ -f /var/run/apache2.pid ]; then
            /etc/init.d/apache2 reload > /dev/null
        fi
    endscript
}
```

**Directives spécifiques :**

| Directive | Description | Cas d'usage |
|-----------|-------------|-------------|
| **missingok** | Pas d'erreur si fichier absent | Logs optionnels |
| **notifempty** | Ne pas rotationner si vide | Économiser espace |
| **sharedscripts** | Exécuter scripts une fois (pas par fichier) | Reload service |
| **postrotate** | Script après rotation | Recharger service |
| **prerotate** | Script avant rotation | Backup |
| **endscript** | Fin bloc script | Obligatoire |
| **copytruncate** | Copier puis tronquer (pas renommer) | Fichiers ouverts |
| **nocreate** | Ne pas créer nouveau fichier | Applications le créent |
| **ifempty** | Rotationner même si vide (défaut) | Force rotation |
| **nocompress** | Ne pas compresser | Logs récents |

### 📄 Exemples de Configuration

#### Exemple 1 : Apache / Nginx

```bash
# /etc/logrotate.d/httpd
/var/log/httpd/*log {
    missingok           # OK si fichier absent
    notifempty          # Ne pas rotationner si vide
    weekly              # Rotation hebdomadaire
    rotate 4            # Conserver 4 semaines
    compress            # Compresser
    delaycompress       # Pas compresser la 1ère rotation
    sharedscripts       # Script commun (pas par fichier)
    postrotate
        /usr/bin/systemctl reload httpd.service > /dev/null 2>&1 || true
    endscript
}
```

**delaycompress** : La version `.1` reste non compressée.
```
access.log        (actuel)
access.log.1      (dernière rotation, NON compressé)
access.log.2.gz   (compressé)
access.log.3.gz
access.log.4.gz
```

**Pourquoi ?** Outils de monitoring peuvent encore lire `.1` facilement.

#### Exemple 2 : Rsyslog

```bash
# /etc/logrotate.d/rsyslog
/var/log/syslog {
    daily
    rotate 7
    missingok
    notifempty
    delaycompress
    compress
    postrotate
        /usr/lib/rsyslog/rsyslog-rotate
    endscript
}

/var/log/mail.info
/var/log/mail.warn
/var/log/mail.err
/var/log/mail.log
/var/log/daemon.log
/var/log/kern.log
/var/log/auth.log
/var/log/user.log
/var/log/lpr.log
/var/log/cron.log
/var/log/debug
/var/log/messages {
    weekly
    rotate 4
    missingok
    notifempty
    compress
    delaycompress
    sharedscripts
    postrotate
        /usr/lib/rsyslog/rsyslog-rotate
    endscript
}
```

#### Exemple 3 : MySQL

```bash
# /etc/logrotate.d/mysql-server
/var/log/mysql/mysql.log
/var/log/mysql/mysql-slow.log
/var/log/mysql/error.log {
    daily
    rotate 7
    missingok
    create 640 mysql adm
    compress
    sharedscripts
    postrotate
        test -x /usr/bin/mysqladmin || exit 0
        if /usr/bin/mysqladmin ping &>/dev/null; then
            /usr/bin/mysqladmin flush-logs
        fi
    endscript
}
```

#### Exemple 4 : Configuration Personnalisée

```bash
# /etc/logrotate.d/myapp
/var/log/myapp/*.log {
    # Rotation quotidienne
    daily
    
    # Rotation aussi si > 100MB (même pas fin journée)
    size 100M
    
    # Conserver 30 jours
    rotate 30
    
    # Extension date au lieu numéro
    dateext
    dateformat -%Y%m%d
    
    # Compression bzip2 (meilleur ratio)
    compress
    compresscmd /bin/bzip2
    compressext .bz2
    
    # Permissions nouveau fichier
    create 0640 myapp myapp
    
    # Script après rotation
    postrotate
        /usr/bin/systemctl reload myapp.service
    endscript
}
```

**Résultat nommage :**
```
myapp.log                 (actuel)
myapp.log-20260211        (hier)
myapp.log-20260210.bz2    (avant-hier compressé)
myapp.log-20260209.bz2
...
```

### ⚙️ Exécution Logrotate

**Logrotate est exécuté via cron.**

```bash
# Script cron quotidien
ls -la /etc/cron.daily/logrotate
# -rwxr-xr-x 1 root root 372 Dec 10 08:30 /etc/cron.daily/logrotate

cat /etc/cron.daily/logrotate
#!/bin/sh
test -x /usr/sbin/logrotate || exit 0
/usr/sbin/logrotate /etc/logrotate.conf
```

**Exécution manuelle (test/debug) :**

```bash
# Exécuter rotation manuellement
sudo logrotate /etc/logrotate.conf

# Mode verbose (afficher détails)
sudo logrotate -v /etc/logrotate.conf

# Mode debug (simuler, sans effectuer)
sudo logrotate -d /etc/logrotate.conf

# Forcer rotation (même si pas période)
sudo logrotate -f /etc/logrotate.conf

# Tester config spécifique
sudo logrotate -d /etc/logrotate.d/apache2

# État rotation (dernière exécution)
cat /var/lib/logrotate/status
# /var/log/apache2/access.log 2026-02-11-06:25:01
# /var/log/syslog 2026-02-11-06:25:01
```

---

# 🔧 PARTIE 2 : RSYSLOG - Configuration Avancée

## 🎯 Architecture rsyslog

**rsyslog** : Daemon de journalisation haute performance.

```bash
# Service
systemctl status rsyslog

# Processus
ps aux | grep rsyslogd
# root  1234  0.0  0.1  12345  6789 ?  Ssl  10:00  0:00 /usr/sbin/rsyslogd -n

# Ports (optionnel, si reception remote)
ss -tulnp | grep rsyslog
# udp   0.0.0.0:514   (syslog)
# tcp   0.0.0.0:514   (syslog)
```

## 📝 Configuration rsyslog

### 📂 Fichiers de Configuration

```bash
# Configuration principale
/etc/rsyslog.conf

# Configuration précoce (avant activation couches réseau)
/etc/rsyslog.early.conf

# Configurations modulaires
/etc/rsyslog.d/*.conf
```

**Structure de configuration :**

| Fichier | Rôle | Utilisation |
|---------|------|-------------|
| `/etc/rsyslog.conf` | Configuration principale | Règles de routage, modules, directives globales |
| `/etc/rsyslog.early.conf` | Configuration précoce | Chargée **avant** activation des couches réseau |
| `/etc/rsyslog.d/*.conf` | Configurations additionnelles | Règles spécifiques par service/application |

**Ordre de chargement :**
```
1. /etc/rsyslog.early.conf  (si existe)
2. /etc/rsyslog.conf
3. /etc/rsyslog.d/*.conf     (si $IncludeConfig présent)
```

### 🗂️ Structure /etc/rsyslog.conf

**3 sections principales :**

```bash
cat /etc/rsyslog.conf

# ===================================================================
# SECTION 1 : MODULES
# ===================================================================
module(load="imuxsock")     # Support sockets Unix (/dev/log)
module(load="imklog")       # Support kernel logging (/dev/kmsg)
#module(load="imudp")       # Reception UDP (remote)
#input(type="imudp" port="514")
#module(load="imtcp")       # Reception TCP (remote)
#input(type="imtcp" port="514")

# ===================================================================
# SECTION 2 : GLOBAL DIRECTIVES
# ===================================================================
$WorkDirectory /var/spool/rsyslog
$ActionFileDefaultTemplate RSYSLOG_TraditionalFileFormat
$FileOwner root
$FileGroup adm
$FileCreateMode 0640
$DirCreateMode 0755
$Umask 0022
$PrivDropToUser syslog
$PrivDropToGroup syslog

# ===================================================================
# SECTION 3 : RULES (Règles de routage)
# ===================================================================
# facility.priority    action

# Tout sauf auth vers syslog
*.*;auth,authpriv.none          -/var/log/syslog

# Auth vers auth.log
auth,authpriv.*                 /var/log/auth.log

# Kernel vers kern.log
kern.*                          -/var/log/kern.log

# Mail vers mail.log
mail.*                          -/var/log/mail.log

# Cron vers cron.log
cron.*                          -/var/log/cron.log

# Urgences vers tous les utilisateurs
*.emerg                         :omusrmsg:*

# Include configs
$IncludeConfig /etc/rsyslog.d/*.conf
```

### 🔍 Section MODULES

**Modules étendent fonctionnalités rsyslog.**

| Module | Description | Usage |
|--------|-------------|-------|
| **imuxsock** | Input Unix socket | Écoute `/dev/log` |
| **imklog** | Input kernel | Écoute `/dev/kmsg` |
| **imudp** | Input UDP | Réception remote (514/udp) |
| **imtcp** | Input TCP | Réception remote (514/tcp) |
| **imfile** | Input fichier | Surveiller fichiers texte |
| **ommail** | Output mail | Envoyer logs par email |
| **ommysql** | Output MySQL | Logs vers base MySQL |
| **omelasticsearch** | Output Elasticsearch | Centralisation logs |

**Activer réception remote (serveur centralisé) :**

```bash
# /etc/rsyslog.conf
module(load="imudp")
input(type="imudp" port="514")

module(load="imtcp")
input(type="imtcp" port="514")

# Redémarrer
sudo systemctl restart rsyslog

# Vérifier ports
ss -tulnp | grep 514
```

### ⚙️ Section GLOBAL DIRECTIVES

**Configuration globale rsyslog.**

| Directive | Description | Exemple |
|-----------|-------------|---------|
| `$WorkDirectory` | Répertoire de travail | `/var/spool/rsyslog` |
| `$FileOwner` | Propriétaire fichiers logs | `root` |
| `$FileGroup` | Groupe fichiers logs | `adm` |
| `$FileCreateMode` | Permissions fichiers | `0640` |
| `$DirCreateMode` | Permissions répertoires | `0755` |
| `$Umask` | Umask création fichiers | `0022` |
| `$PrivDropToUser` | User pour rsyslog | `syslog` |
| `$ActionFileDefaultTemplate` | Format logs | `RSYSLOG_TraditionalFileFormat` |

### 📋 Section RULES - Règles de Routage

**Format :**
```
facility.priority    action
```

## 🏷️ Facilities (Sources)

**Facility** : Catégorie/source du message.

| Facility | Code | Description |
|----------|------|-------------|
| **kern** | 0 | Messages kernel |
| **user** | 1 | Messages user-level |
| **mail** | 2 | Système mail |
| **daemon** | 3 | Daemons système |
| **auth** | 4 | Sécurité/authentification |
| **syslog** | 5 | Messages rsyslog interne |
| **lpr** | 6 | Imprimantes |
| **news** | 7 | Serveur news (NNTP) |
| **uucp** | 8 | UUCP |
| **cron** | 9 | Cron/at |
| **authpriv** | 10 | Authentification privée (SSH) |
| **ftp** | 11 | FTP |
| **ntp** | 12 | NTP |
| **security** | 13 | Audit système |
| **console** | 14 | Messages console |
| **local0-local7** | 16-23 | Réservées usage local |

**Wildcards :**
- `*` : Toutes les facilities
- `kern,mail` : Multiple facilities (virgule)

## 🚨 Priorities (Niveaux de Gravité)

**Priority** : Niveau de gravité du message.

| Priority | Code | Description | Usage |
|----------|------|-------------|-------|
| **emerg** / **panic** | 0 | Urgence (système inutilisable) | Kernel panic |
| **alert** | 1 | Action immédiate requise | Perte RAID |
| **crit** | 2 | Conditions critiques | Température CPU |
| **err** / **error** | 3 | Erreurs | Erreur daemon |
| **warn** / **warning** | 4 | Avertissements | Disque 90% |
| **notice** | 5 | Normal mais significatif | Changement config |
| **info** | 6 | Informationnel | Service démarré |
| **debug** | 7 | Messages debug | Debugging app |

**⚠️ Hiérarchie :** Spécifier une priority inclut TOUTES celles supérieures.

```
kern.err    → Capture err, crit, alert, emerg (PAS warn, notice, info, debug)
```

**Modificateurs de priority :**

| Syntaxe | Description | Exemple |
|---------|-------------|---------|
| `facility.priority` | Priority et supérieures | `kern.err` |
| `facility.=priority` | Priority EXACTE uniquement | `cron.=alert` |
| `facility.!priority` | Exclure priority et inférieures | `mail.!info` |
| `facility.*` | Toutes priorities | `auth.*` |
| `facility.none` | Aucune (exclusion) | `mail.none` |

## 🎯 Actions (Destinations)

**Action** : Destination du log.

| Type | Syntaxe | Exemple | Description |
|------|---------|---------|-------------|
| **Fichier** | `/path/to/file` | `/var/log/syslog` | Écriture fichier (avec sync) |
| **Fichier async** | `-/path/to/file` | `-/var/log/syslog` | Écriture sans sync disque |
| **Console** | `/dev/console` | `/dev/console` | Affichage sur console système |
| **Terminal user** | `username` | `root` | Message vers utilisateur connecté |
| **Plusieurs users** | `user1,user2,userN` | `root,admin,alice` | Message vers liste utilisateurs |
| **Tous users** | `*` | `*` | Broadcast tous users |
| **Module omusrmsg** | `:omusrmsg:*` | `:omusrmsg:admin` | Message utilisateurs (module) |
| **Remote UDP** | `@ip[:port]` | `@192.168.1.10:514` | Serveur syslog distant (UDP) |
| **Remote TCP** | `@@ip[:port]` | `@@192.168.1.10:514` | Serveur syslog distant (TCP) |
| **Pipe** | `|/path/to/pipe` | `|/var/log/pipe` | Named pipe |
| **Programme** | `^nom_programme` | `^/usr/local/bin/process-log.sh` | Passer message à programme |
| **Ignorer** | `~` | `~` | Ignorer/rejeter le message |
| **Actions multiples** | `action1 & action2 & ...` | `/var/log/msg & @@remote` | Plusieurs destinations |

**Symbole `-` (pas de sync) :**

```bash
# AVEC sync (par défaut)
auth.*    /var/log/auth.log
# Chaque log = write() + fsync() → I/O intense

# SANS sync (async, recommandé logs non-critiques)
auth.*    -/var/log/auth.log
# Bufferisé, fsync périodique → Meilleures performances

# Critique (finances, audit) : AVEC sync
# Non-critique (debug, info) : SANS sync (-)
```

**Exemples d'actions avancées :**

```bash
# Console système
kern.crit    /dev/console

# Envoyer mail à admin et root
mail.err    root,admin

# Message vers tous utilisateurs connectés (module)
*.emerg    :omusrmsg:*

# Programme custom pour traitement
local0.*    ^/usr/local/bin/process-app-logs.sh

# Ignorer tous les messages debug de daemon
daemon.debug    ~

# Actions multiples : fichier local + serveur distant
auth.*    /var/log/auth.log & @@192.168.1.100:514

# Fichier + console pour urgences
*.emerg    /var/log/emergency.log & /dev/console
```

## 📐 Exemples de Règles

### Règles de Base

```bash
# Tout vers syslog
*.*    /var/log/syslog

# Kernel vers kern.log
kern.*    -/var/log/kern.log

# Auth vers auth.log
auth,authpriv.*    /var/log/auth.log

# Mail vers mail.log
mail.*    -/var/log/mail.log

# Cron vers cron.log
cron.*    -/var/log/cron.log

# Erreurs kern vers fichier critique (avec sync)
kern.err    /var/log/kernel-errors.log
```

### Règles avec Exclusions

```bash
# Tout SAUF auth/authpriv vers syslog
*.*;auth,authpriv.none    -/var/log/syslog

# Décomposition :
# *.* = toutes facilities, toutes priorities
# ; = SAUF
# auth,authpriv.none = exclure auth ET authpriv
```

```bash
# Mail SAUF info et debug
mail.*;mail.!info    /var/log/mail-important.log

# Équivalent
mail.notice;mail.warn;mail.err;mail.crit;mail.alert;mail.emerg    /var/log/mail-important.log
```

### Règles avec Priority Exacte

```bash
# Cron UNIQUEMENT alert (pas emerg, pas crit...)
cron.=alert    /var/log/cron-alerts.log

# Debug SEULEMENT pour daemon
daemon.=debug    /var/log/daemon-debug.log
```

### Règles avec Utilisateurs

```bash
# Urgences (emerg) vers TOUS les utilisateurs connectés
*.emerg    :omusrmsg:*

# Alertes vers root
*.alert    root

# Critiques vers admin et root
*.crit    root,admin
```

### Règles Remote (Centralisation)

```bash
# Envoyer TOUT vers serveur syslog central (UDP)
*.*    @192.168.1.100:514

# Envoyer TOUT vers serveur syslog central (TCP, fiable)
*.*    @@syslog.example.com:514

# Envoyer seulement erreurs vers remote
*.err    @@192.168.1.100

# Local ET remote
auth.*    /var/log/auth.log
auth.*    @@192.168.1.100
```

### Règles Avancées

```bash
# Local0 vers fichier custom (app custom)
local0.*    /var/log/myapp.log

# Local1 debug vers fichier séparé
local1.=debug    /var/log/myapp-debug.log

# Combiner plusieurs facilities
auth,authpriv,cron.*    /var/log/security.log

# Multiple actions (même log → plusieurs destinations)
mail.*    /var/log/mail.log
mail.*    @@backup-server.local
mail.err    /var/log/mail-errors.log
mail.err    root
```

## 🔧 Commande logger - Générer Logs

**`logger`** : Envoyer manuellement message vers syslog.

**Cas d'usage :**
- Scripts shell
- Tests configuration rsyslog
- Génération logs custom
- Monitoring

### Syntaxe

```bash
# Message simple (facility user, priority notice)
logger "Ceci est un message test"

# Vérifier dans syslog
grep "Ceci est un message test" /var/log/syslog
# Feb 11 14:35:42 server alice: Ceci est un message test

# Spécifier priority
logger -p info "Message informatif"
logger -p warn "Avertissement"
logger -p err "Erreur simulée"

# Spécifier facility.priority
logger -p local0.notice "Application démarrée"
logger -p auth.alert "Tentative accès suspect"
logger -p cron.info "Tâche cron exécutée"

# Tag (identifier source)
logger -t myapp "Erreur application"
# Feb 11 14:35:42 server myapp: Erreur application

logger -t backup -p local1.info "Backup démarré"
# Feb 11 14:35:42 server backup: Backup démarré

# Depuis fichier
logger -f /tmp/messages.txt

# Depuis stdin
echo "Message depuis pipeline" | logger -t test

# Remote syslog
logger -n 192.168.1.100 -P 514 "Message vers remote"
logger -n syslog.example.com --tcp -P 514 "TCP remote"
```

### Exemples Pratiques

```bash
# Script backup
#!/bin/bash
logger -t backup -p local0.info "Backup démarré"
tar -czf /backup/data.tar.gz /data
if [ $? -eq 0 ]; then
    logger -t backup -p local0.notice "Backup réussi"
else
    logger -t backup -p local0.err "Backup échoué"
fi

# Test rsyslog config
logger -p local1.debug "Test debug"
logger -p local1.info "Test info"
logger -p local1.warn "Test warning"
logger -p local1.err "Test error"

# Puis vérifier
tail /var/log/syslog | grep local1

# Monitoring script
if ! ping -c 1 8.8.8.8 &>/dev/null; then
    logger -t network -p daemon.alert "Réseau injoignable"
fi

# Cron job logging
# /etc/cron.daily/cleanup
#!/bin/bash
logger -t cleanup -p cron.info "Nettoyage démarré"
find /tmp -mtime +7 -delete
logger -t cleanup -p cron.notice "Nettoyage terminé"
```

---

# 📘 PARTIE 3 : SYSTEMD-JOURNALD

## 🎯 Principe de journald

**systemd-journald** : Système de journalisation moderne intégré à systemd.

**Avantages vs rsyslog :**

| Critère | rsyslog | journald |
|---------|---------|----------|
| **Format** | Texte | Binaire indexé |
| **Recherche** | grep (lent) | Index (rapide) |
| **Structuration** | Lignes texte | Champs structurés |
| **Persistance** | Toujours | Optionnelle (volatile) |
| **Intégration systemd** | Externe | Native |
| **Centralisation** | Excellent | Possible (forwarding) |
| **Filtrage** | Regex | Champs précis |

**Coexistence :**
```bash
# Possible : journald + rsyslog
# journald collecte → forward vers rsyslog

# /etc/systemd/journald.conf
ForwardToSyslog=yes
```

## 📝 Configuration journald

### 📂 Fichier /etc/systemd/journald.conf

```bash
cat /etc/systemd/journald.conf

[Journal]
# Stockage des logs
#Storage=auto
#Compress=yes
#Seal=yes

# Limites système (persistent)
#SystemMaxUse=
#SystemKeepFree=
#SystemMaxFileSize=
#SystemMaxFiles=100

# Limites runtime (volatile)
#RuntimeMaxUse=
#RuntimeKeepFree=
#RuntimeMaxFileSize=
#RuntimeMaxFiles=100

# Forwarding
#ForwardToSyslog=yes
#ForwardToKMsg=no
#ForwardToConsole=no
#ForwardToWall=yes

# Ratelimit
#RateLimitIntervalSec=30s
#RateLimitBurst=10000

# Taille max message
#MaxLevelStore=debug
#MaxLevelSyslog=debug
```

### ⚙️ Directives Principales journald.conf

**Directive `Storage`** : Définit le lieu de stockage des traces.

| Valeur | Description | Emplacement |
|--------|-------------|-------------|
| **auto** | Écriture dans `/var/log/journal` s'il existe, `/run/log/journal` sinon | Conditionnel (défaut) |
| **persistent** | journald utilise le répertoire `/var/log/journal` (créé si absent) | Disque permanent |
| **volatile** | journald utilise le répertoire `/run/log/journal` (RAM uniquement) | RAM (perdu au reboot) |
| **none** | Aucun stockage des logs | Nulle part |

```bash
# Par défaut : auto
Storage=auto

# Si /var/log/journal existe
ls -ld /var/log/journal
# drwxr-sr-x+ 3 root systemd-journal 4096 Feb 11 10:00 /var/log/journal
# → Logs persistents (survie reboot)

# Si /var/log/journal N'EXISTE PAS
# → Logs dans /run/log/journal (RAM, perdus au reboot)

# Activer persistance
sudo mkdir -p /var/log/journal
sudo systemd-tmpfiles --create --prefix /var/log/journal
sudo systemctl restart systemd-journald

# Vérifier
journalctl --disk-usage
```

### 💾 Gestion de l'Espace Disque

**Directives de limitation :**

| Directive | Description | Défaut |
|-----------|-------------|--------|
| **SystemMaxUse** | Taux d'occupation maximum de l'espace disque avant recyclage (persistent) | 10% du FS |
| **SystemKeepFree** | Espace libre minimum à garder sur le disque | 15% du FS |
| **SystemMaxFileSize** | Taille maximum d'un fichier journal individuel | 1/8 de SystemMaxUse |
| **SystemMaxFiles** | Nombre maximum de fichiers journaux | 100 |
| **RuntimeMaxUse** | Taux d'occupation maximum pour logs volatile (RAM) | 10% de /run |
| **RuntimeKeepFree** | Espace libre minimum à garder dans /run | 15% de /run |

**⚠️ Note :** `SystemMaxUse` et `RunMaxUse` définissent le **taux d'occupation maximum** (par défaut **10%**) de l'espace disque avant que journald commence le recyclage des anciens logs.

**Exemples :**

```bash
# /etc/systemd/journald.conf

[Journal]
# Limiter à 500MB max
SystemMaxUse=500M

# Garder 2GB libres sur /var
SystemKeepFree=2G

# Fichiers individuels max 100MB
SystemMaxFileSize=100M

# Conserver max 50 fichiers
SystemMaxFiles=50

# Pour volatile (RAM)
RuntimeMaxUse=100M
RuntimeKeepFree=50M
```

**Appliquer changements :**
```bash
sudo systemctl restart systemd-journald

# Vérifier usage disque
journalctl --disk-usage
# Archived and active journals take up 256.0M in the file system.
```

**💡 Option `--disk-usage` :** Affiche l'espace disque utilisé par les fichiers de journald.

### 🔀 Directives de Transfert et Filtrage

**Directive `ForwardToSyslog`** : Transfert des logs vers rsyslog.

```bash
# /etc/systemd/journald.conf
[Journal]
ForwardToSyslog=yes    # Par défaut à yes

# journald collecte les messages puis les transfère à rsyslog
# → Double stockage : journald + rsyslog
```

**Cas d'usage :**
- Transition rsyslog → journald
- Centralisation logs (rsyslog remote)
- Compatibilité outils existants

**Directive `MaxRetentionSec`** : Durée maximale de rétention des messages.

```bash
# /etc/systemd/journald.conf
[Journal]
MaxRetentionSec=0    # Par défaut 0 (illimité)

# Exemples :
MaxRetentionSec=1month    # Conserver 1 mois
MaxRetentionSec=90d       # Conserver 90 jours
MaxRetentionSec=2year     # Conserver 2 ans
```

**⚠️ Note :** La valeur par défaut **0 est suffisante**, car c'est alors la **taille d'occupation du disque** (SystemMaxUse) qui régit la rétention.

**Directive `MaxLevelStore`** : Niveau de criticité minimum des traces à traiter.

```bash
# /etc/systemd/journald.conf
[Journal]
MaxLevelStore=debug    # Par défaut à debug (7)

# Niveaux possibles (du plus critique au moins) :
# emerg (0), alert (1), crit (2), err (3), warning (4), notice (5), info (6), debug (7)
```

| Niveau | Valeur | Description | Exemple configuration |
|--------|--------|-------------|----------------------|
| **debug** | 7 | Tout (défaut) | `MaxLevelStore=debug` |
| **info** | 6 | Info et plus critique | `MaxLevelStore=info` |
| **notice** | 5 | Notice et plus critique | `MaxLevelStore=notice` |
| **warning** | 4 | Avertissements et plus | `MaxLevelStore=warning` |
| **err** | 3 | Erreurs et plus critique | `MaxLevelStore=err` |

**Exemple complet :**

```bash
# /etc/systemd/journald.conf
[Journal]
Storage=persistent
SystemMaxUse=500M
MaxRetentionSec=30d
ForwardToSyslog=yes
MaxLevelStore=debug

# Signification :
# - Stockage persistent (/var/log/journal)
# - Max 500MB avant recyclage (10% par défaut)
# - Conserver 30 jours maximum
# - Transférer vers rsyslog
# - Stocker tous les niveaux (debug à emerg)
```

## 🔍 Commande journalctl

### 📖 Affichage de Base

```bash
# Afficher TOUS les logs
journalctl

# Pager : less
# Navigation : Espace, b, /, q

# Dernières lignes
journalctl -n 20
journalctl --lines=50

# Suivre temps réel (tail -f)
journalctl -f
journalctl --follow

# Afficher depuis la fin
journalctl -e
# Ouvre directement à la fin (sans scroller)

# Inverser ordre (plus récents en premier)
journalctl -r
journalctl --reverse
```

### 🔧 Filtrage par Boot

```bash
# Logs du boot actuel
journalctl -b
journalctl --boot

# Logs boot précédent
journalctl -b -1

# Lister tous les boots
journalctl --list-boots
# -2 1234abcd... Tue 2026-02-09 10:00:00 CET - Tue 2026-02-10 18:00:00 CET
# -1 5678efgh... Wed 2026-02-10 18:05:00 CET - Wed 2026-02-10 22:30:00 CET
#  0 9012ijkl... Thu 2026-02-11 10:00:00 CET - Thu 2026-02-11 14:35:42 CET

# Boot spécifique (par ID)
journalctl -b 1234abcd...

# Deux boots précédents
journalctl -b -2
```

### 🚨 Filtrage par Priority

```bash
# Seulement erreurs (err et supérieur : crit, alert, emerg)
journalctl -p err
journalctl --priority=err

# Avertissements
journalctl -p warning

# Informations
journalctl -p info

# Debug (TOUT)
journalctl -p debug

# Priorities disponibles :
# emerg (0), alert (1), crit (2), err (3), warning (4), notice (5), info (6), debug (7)

# Plage de priorities
journalctl -p warning..emerg   # warning à emerg
journalctl -p 3..0             # err à emerg (codes numériques)
```

### 🔧 Filtrage par Unit (Service)

```bash
# Logs d'un service systemd
journalctl -u ssh
journalctl -u apache2.service
journalctl -u nginx.service

# Multiple units
journalctl -u ssh -u apache2

# Unit + follow
journalctl -u mysql -f

# Unit + boot + priority
journalctl -u nginx -b -p err
```

### 📅 Filtrage Temporel

```bash
# Depuis une heure
journalctl --since "1 hour ago"
journalctl --since "60 minutes ago"

# Depuis hier
journalctl --since yesterday

# Depuis aujourd'hui minuit
journalctl --since today

# Depuis date/heure précise
journalctl --since "2026-02-11 10:00:00"
journalctl --since "2026-02-11"

# Jusqu'à une date
journalctl --until "2026-02-11 14:00:00"
journalctl --until "1 hour ago"

# Plage temporelle
journalctl --since "2026-02-10" --until "2026-02-11"
journalctl --since "10:00" --until "12:00"
journalctl --since "2 hours ago" --until "30 minutes ago"

# Aujourd'hui entre 14h et 15h
journalctl --since "14:00" --until "15:00"
```

### 🐧 Filtrage par Kernel

```bash
# Messages kernel (équivalent dmesg)
journalctl -k
journalctl --dmesg

# Kernel du boot actuel
journalctl -k -b

# Kernel boot précédent
journalctl -k -b -1
```

### 🔍 Filtrage Avancé (Champs)

**journald stocke logs structurés avec champs.**

```bash
# Par binaire/programme
journalctl /usr/bin/sshd
journalctl /usr/sbin/apache2

# Par PID
journalctl _PID=1234

# Par UID
journalctl _UID=1000

# Par GID
journalctl _GID=33   # www-data

# Par champ personnalisé
journalctl PRIORITY=4
journalctl SYSLOG_FACILITY=10   # authpriv

# Combiner plusieurs champs
journalctl _PID=1234 PRIORITY=3
journalctl _UID=1000 -u ssh

# Lister champs disponibles
journalctl -o json-pretty | less
# _PID, _UID, _GID, _COMM, _EXE, _CMDLINE, _SYSTEMD_UNIT, ...
```

### 📊 Formats de Sortie

```bash
# Format court (défaut)
journalctl -o short

# Format verbeux (tous les champs)
journalctl -o verbose

# JSON
journalctl -o json
journalctl -o json-pretty

# Export binaire
journalctl -o export

# Cat (sans métadonnées)
journalctl -o cat
```

### 💾 Gestion de l'Espace

```bash
# Vérifier usage disque
journalctl --disk-usage
# Archived and active journals take up 512.0M in the file system.

# Nettoyer logs > 3 mois
sudo journalctl --vacuum-time=3months
sudo journalctl --vacuum-time=2weeks
sudo journalctl --vacuum-time=7d

# Réduire à taille max
sudo journalctl --vacuum-size=500M
sudo journalctl --vacuum-size=1G

# Conserver N fichiers max
sudo journalctl --vacuum-files=10

# Vérifier après nettoyage
journalctl --disk-usage
# Archived and active journals take up 256.0M in the file system.
```

### 📂 Logs Externes (Autre Système)

```bash
# Lire logs depuis répertoire externe
sudo journalctl -D /mnt/backup/var/log/journal

# Lire fichier journal spécifique
sudo journalctl --file /mnt/backup/var/log/journal/abc123.../system.journal

# Système monté
sudo journalctl --root /mnt/rescue

# Fusionner avec logs actuels
sudo journalctl --merge -D /mnt/backup/var/log/journal
```

### 🔧 Exemples Pratiques

```bash
# Erreurs SSH dernière heure
journalctl -u ssh -p err --since "1 hour ago"

# Tout Apache aujourd'hui
journalctl -u apache2 --since today

# Kernel errors boot actuel
journalctl -k -p err -b

# Suivi temps réel nginx
journalctl -u nginx -f

# Authentifications échouées
journalctl -u ssh | grep "Failed password"

# Logs d'un user
journalctl _UID=$(id -u alice)

# Boot problématique
journalctl -b -1 -p err

# Chercher pattern
journalctl | grep -i "out of memory"

# Derniers 100 logs
journalctl -n 100 --no-pager
```

## 🔧 Commande systemd-cat

**`systemd-cat`** : Envoyer logs vers journald (alternative `logger`).

```bash
# Message simple
echo "Test message" | systemd-cat

# Avec priority
echo "Erreur application" | systemd-cat -p err
echo "Info debug" | systemd-cat -p debug

# Avec identifier
echo "Backup terminé" | systemd-cat -t backup

# Priority + identifier
echo "Alerte système" | systemd-cat -p alert -t monitoring

# Exécuter commande et logger sortie
systemd-cat -p info uname -r
# 6.1.0-10-amd64 (envoyé dans journal)

systemd-cat -t myapp /usr/local/bin/myapp.sh

# Script entier
systemd-cat -p debug bash -c '
    echo "Démarrage script"
    ls -la /tmp
    echo "Fin script"
'

# Vérifier
journalctl -t backup
journalctl -t myapp
```

## 📂 Stockage des Logs journald

### 🗂️ Emplacements

```bash
# Persistent (survit reboot)
/var/log/journal/

# Volatile (RAM, perdu reboot)
/run/log/journal/

# Structure
/var/log/journal/<machine-id>/
/run/log/journal/<machine-id>/

# Machine ID
cat /etc/machine-id
# a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6

# Exemple
ls -la /var/log/journal/a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6/
# -rw-r-----+ system.journal
# -rw-r-----+ system@1234abcd.journal
# -rw-r-----+ user-1000.journal
```

**Types de fichiers :**
- `system.journal` : Logs système actuels
- `system@<id>.journal` : Logs système archivés
- `user-<uid>.journal` : Logs utilisateur

### 🔄 Migration Volatile → Persistent

```bash
# 1. Créer répertoire
sudo mkdir -p /var/log/journal

# 2. Permissions correctes
sudo systemd-tmpfiles --create --prefix /var/log/journal

# 3. Copier logs volatile actuels (optionnel)
sudo cp -r /run/log/journal/* /var/log/journal/

# 4. Redémarrer journald
sudo systemctl restart systemd-journald

# 5. Vérifier
journalctl --disk-usage
ls -la /var/log/journal/
```

---

## 🎓 Scénarios Pratiques

### Scénario 1 : Debug Service Qui Ne Démarre Pas

```bash
# Voir erreurs service
journalctl -u myapp.service -p err

# Logs complets dernière tentative démarrage
journalctl -u myapp.service --since "5 minutes ago"

# Avec plus de contexte
journalctl -u myapp.service -n 100 --no-pager
```

### Scénario 2 : Analyser Tentatives Connexion SSH

```bash
# Toutes tentatives aujourd'hui
journalctl -u ssh --since today

# Échecs seulement
journalctl -u ssh | grep "Failed password"

# Compter tentatives
journalctl -u ssh --since today | grep -c "Failed password"
# 152

# Par IP source
journalctl -u ssh | grep "Failed password" | awk '{print $(NF-3)}' | sort | uniq -c | sort -rn
```

### Scénario 3 : Rotation Logs Apache

```bash
# Config logrotate
sudo vim /etc/logrotate.d/apache2

/var/log/apache2/*.log {
    daily
    rotate 14
    compress
    delaycompress
    notifempty
    create 0640 www-data adm
    sharedscripts
    postrotate
        systemctl reload apache2 > /dev/null
    endscript
}

# Tester
sudo logrotate -d /etc/logrotate.d/apache2

# Forcer rotation
sudo logrotate -f /etc/logrotate.d/apache2
```

### Scénario 4 : Centralisation Logs (rsyslog)

**Serveur central :**
```bash
# /etc/rsyslog.conf
module(load="imudp")
input(type="imudp" port="514")

# Logs clients dans fichiers séparés
$template RemoteLogs,"/var/log/remote/%HOSTNAME%/%PROGRAMNAME%.log"
*.* ?RemoteLogs

sudo systemctl restart rsyslog
```

**Clients :**
```bash
# /etc/rsyslog.conf
*.*    @@syslog-server.local:514

sudo systemctl restart rsyslog
```

### Scénario 5 : Debugging Kernel Panic

```bash
# Logs kernel boot précédent
journalctl -k -b -1 -p err

# Tout kernel boot problématique
journalctl -k -b -1 > /tmp/kernel-crash.log

# Ou dmesg si système redémarré
dmesg -T > /tmp/dmesg-$(date +%Y%m%d).log
```

---

## 📝 Cheat Sheet

### rsyslog

```bash
# Config
/etc/rsyslog.conf
/etc/rsyslog.d/*.conf

# Service
systemctl restart rsyslog

# Format règles
facility.priority    action

# Exemples
*.*;auth.none    -/var/log/syslog
auth.*    /var/log/auth.log
*.emerg    *
*.*    @@remote-server

# logger
logger "message"
logger -p local0.info "app log"
logger -t myapp "tagged"
```

### logrotate

```bash
# Config
/etc/logrotate.conf
/etc/logrotate.d/service

# Directives
weekly, daily, monthly
rotate 4
compress, delaycompress
create 0640 user group
missingok, notifempty
postrotate ... endscript

# Exécution
sudo logrotate -f /etc/logrotate.conf
sudo logrotate -d /etc/logrotate.d/apache2
```

### journalctl

```bash
# Affichage
journalctl                   # Tout
journalctl -n 50             # 50 dernières
journalctl -f                # Follow
journalctl -r                # Reverse
journalctl -e                # End

# Filtres
journalctl -b                # Boot actuel
journalctl -k                # Kernel
journalctl -u service        # Service
journalctl -p err            # Priority
journalctl --since "1h ago"  # Temporel

# Maintenance
journalctl --disk-usage
journalctl --vacuum-time=2weeks
journalctl --vacuum-size=500M
```

### dmesg

```bash
# Affichage
dmesg
dmesg -T                     # Timestamps
dmesg -w                     # Follow
dmesg -l err                 # Errors
dmesg | grep -i usb         # Filter
```

---

## 🎯 Points Clés pour l'Examen LPIC-1

### Connaissances Essentielles

✅ **Architecture :**
- Bus logs : `/dev/log` (syslog), `/dev/kmsg` (kernel)
- rsyslog : texte, `/var/log/*.log`
- journald : binaire, `/var/log/journal/`

✅ **Fichiers logs :**
- `auth.log` / `secure` : Authentification
- `syslog` / `messages` : Central
- `kern.log` : Kernel
- `wtmp`, `btmp`, `lastlog` : Connexions

✅ **rsyslog :**
- Config : `/etc/rsyslog.conf`, `/etc/rsyslog.d/`
- Facilities : kern, user, mail, daemon, auth, cron...
- Priorities : emerg, alert, crit, err, warn, notice, info, debug
- Règles : `facility.priority action`
- Actions : fichier, `-fichier` (async), `@ip` (UDP), `@@ip` (TCP)

✅ **logrotate :**
- Config : `/etc/logrotate.conf`, `/etc/logrotate.d/`
- Rotation : daily, weekly, monthly
- Directives : rotate, compress, create, missingok, notifempty
- Scripts : postrotate ... endscript

✅ **journald :**
- Service : `systemd-journald`
- Commande : `journalctl`
- Config : `/etc/systemd/journald.conf`
- Storage : auto, persistent, volatile, none
- Stockage : `/var/log/journal/` ou `/run/log/journal/`

✅ **journalctl options :**
- `-b` (boot), `-k` (kernel), `-u` (unit), `-p` (priority)
- `-f` (follow), `-r` (reverse), `-e` (end), `-n` (lines)
- `--since`, `--until`
- `--disk-usage`, `--vacuum-time`, `--vacuum-size`

✅ **Outils :**
- `logger` : Générer logs rsyslog
- `systemd-cat` : Générer logs journald
- `dmesg` : Kernel ring buffer
- `last`, `lastb`, `lastlog` : Connexions

### Commandes à Maîtriser

| Commande | Usage |
|----------|-------|
| `journalctl` | Voir logs journald |
| `journalctl -u service` | Logs service |
| `journalctl -b` | Logs boot actuel |
| `dmesg` | Kernel ring buffer |
| `logger` | Générer log syslog |
| `last` | Connexions réussies |
| `lastb` | Connexions échouées |
| `zgrep` | Chercher logs compressés |
| `logrotate` | Rotation logs |

### Pièges Fréquents

❌ Confondre auth.log (Debian) et secure (RedHat)  
❌ Confondre syslog (Debian) et messages (RedHat)  
❌ Oublier `-` pour async dans rsyslog  
❌ Croire que journald est toujours persistent  
❌ Ne pas créer `/var/log/journal` pour persistance  
❌ Utiliser `cat` sur logs compressés (utiliser `zcat`)  
❌ Oublier `sudo` pour `lastb` (btmp accessible root seulement)

---

**Formation LPIC-1 102 - Préparation Certification Linux**  
**Cours 108.2 - Journalisation Système**  
**Maxime Chenaud - 11 Février 2026**
