# ⏰ Gestion du Temps Système Linux

## 📋 Informations du Module

- **Examen** : LPIC-1 102
- **Objectif** : 108.1 - Maintain system time
- **Poids** : 3 points
- **Objectif** : Gérer l'heure système, l'horloge matérielle et la synchronisation NTP

---

## 🎯 Vue d'Ensemble

La gestion du temps sous Linux repose sur **deux horloges distinctes** :

```
┌──────────────────────────────────────────────────────┐
│  ARCHITECTURE DU TEMPS LINUX                         │
├──────────────────────────────────────────────────────┤
│                                                      │
│  ┌────────────────────────────────────┐             │
│  │  HARDWARE CLOCK (RTC)              │             │
│  │  Real-Time Clock                   │             │
│  │  - Horloge matérielle (BIOS/UEFI) │             │
│  │  - Fonctionne même PC éteint       │             │
│  │  - Batterie CMOS                   │             │
│  │  - UTC ou Local Time               │             │
│  └──────────┬─────────────────────────┘             │
│             │ Boot: Hardware → System               │
│             ↓                                        │
│  ┌────────────────────────────────────┐             │
│  │  SYSTEM CLOCK (Software)           │             │
│  │  - Maintenue par kernel Linux      │             │
│  │  - Tick après tick (interruptions) │             │
│  │  - Toujours en UTC en interne      │             │
│  │  - Ajuste avec timezone → locale   │             │
│  └──────────┬─────────────────────────┘             │
│             │ Synchronisation NTP/Chrony            │
│             ↓                                        │
│  ┌────────────────────────────────────┐             │
│  │  NTP SERVERS                       │             │
│  │  - pool.ntp.org                    │             │
│  │  - time.google.com                 │             │
│  │  - ntp.ubuntu.com                  │             │
│  └────────────────────────────────────┘             │
└──────────────────────────────────────────────────────┘
```

**Processus au démarrage :**
1. Linux lit **Hardware Clock (RTC)** au boot
2. Applique **timezone offset** (si RTC en UTC)
3. Initialise **System Clock**
4. Synchronise avec **serveurs NTP** (si configuré)
5. Ajuste périodiquement System Clock

**⚠️ Recommandation : RTC en UTC**
- ✅ Évite problèmes changement d'heure DST
- ✅ Cohérence multi-boot (Linux + BSD)
- ❌ Windows utilise local time (dual-boot problématique)

---

# 🕰️ PARTIE 1 : HARDWARE CLOCK & HORLOGE SYSTÈME

## 🔧 Commande hwclock - Gestion Hardware Clock

### 📊 Afficher l'Heure Matérielle

```bash
# Afficher l'heure de l'horloge matérielle (RTC)
hwclock

# Sortie exemple :
# 2026-02-11 14:35:42.123456+01:00

# Afficher avec plus de détails
hwclock --verbose

# Sortie :
# hwclock from util-linux 2.38.1
# System Time: 1770750942.123456
# Trying to open: /dev/rtc0
# Using the rtc interface to the clock.
# Assuming hardware clock is kept in UTC time.
# Waiting for clock tick...
# ...got clock tick
# Time read from Hardware Clock: 2026/02/11 13:35:42
# Hw clock time : 2026/02/11 13:35:42 = 1770750942 seconds since 1969
# Time since last adjustment is 1234567 seconds
# Needed adjustment is 0.123456 seconds
# 2026-02-11 13:35:42.123456+00:00

# Afficher en supposant RTC en UTC
hwclock --utc

# Afficher en supposant RTC en heure locale
hwclock --localtime
```

**Fichier device :**
```bash
# RTC est accessible via /dev/rtc (symlink)
ls -la /dev/rtc
# lrwxrwxrwx 1 root root 4 Feb 11 10:00 /dev/rtc -> rtc0

# Device réel
ls -la /dev/rtc0
# crw------- 1 root root 254, 0 Feb 11 10:00 /dev/rtc0
```

### ⚙️ Configurer l'Heure Matérielle

#### Méthode 1 : Définir Manuellement

```bash
# Définir heure matérielle en heure locale
sudo hwclock --localtime --set --date="01/05/2026 22:04:00"

# Vérifier
sudo hwclock --localtime
# 2026-01-05 22:04:05.123456+01:00

# Définir heure matérielle en UTC
sudo hwclock --utc --set --date="2026-02-11 13:35:00"

# Format de date accepté (plusieurs variantes)
sudo hwclock --set --date="2026-02-11 14:35:42"
sudo hwclock --set --date="11 Feb 2026 14:35:42"
sudo hwclock --set --date="02/11/2026 14:35:42"
```

**⚠️ Format date :**
```
MM/DD/YYYY HH:MM:SS    # Format US
YYYY-MM-DD HH:MM:SS    # Format ISO
DD Mon YYYY HH:MM:SS   # Format texte
```

#### Méthode 2 : Synchroniser depuis System Clock

**Le plus courant : synchroniser RTC depuis l'heure système.**

```bash
# Écrire l'heure système vers hardware clock (UTC)
sudo hwclock --utc --systohc

# Alias : -u = --utc, -w = --systohc
sudo hwclock -u -w

# Écrire en heure locale
sudo hwclock --localtime --systohc
sudo hwclock -l -w

# Processus :
# System Clock (corrigée via NTP) → Hardware Clock
```

**Cas d'usage :**
```bash
# 1. Synchroniser système via NTP
sudo systemctl start systemd-timesyncd

# 2. Attendre synchronisation
sleep 5

# 3. Écrire l'heure système (maintenant correcte) vers RTC
sudo hwclock -u -w

# RTC est maintenant à jour !
```

#### Méthode 3 : Synchroniser System Clock depuis RTC

**Au boot ou correction système depuis hardware.**

```bash
# Lire hardware clock et définir system clock (UTC)
sudo hwclock --utc --hctosys

# Alias : -u = --utc, -s = --hctosys
sudo hwclock -u -s

# Lire en heure locale
sudo hwclock --localtime --hctosys
sudo hwclock -l -s

# Processus :
# Hardware Clock → System Clock
```

**⚠️ Attention :** Cette commande modifie l'heure système immédiatement.  
→ Services, logs, cron peuvent être impactés.

### 📝 Options hwclock Résumé

| Option | Alias | Description |
|--------|-------|-------------|
| `--show` | - | Afficher heure hardware (défaut) |
| `--set --date="..."` | - | Définir heure hardware manuellement |
| `--systohc` | `-w` | System Clock → Hardware Clock |
| `--hctosys` | `-s` | Hardware Clock → System Clock |
| `--utc` | `-u` | RTC stockée en UTC |
| `--localtime` | `-l` | RTC stockée en heure locale |
| `--verbose` | - | Mode verbeux (détails) |
| `--adjust` | `-a` | Ajuster RTC selon drift (dérive) |

**Exemples combinés :**

```bash
# Afficher RTC (UTC)
sudo hwclock -u

# Définir RTC manuellement (UTC)
sudo hwclock -u --set --date="2026-02-11 14:00:00"

# Système → RTC (UTC)
sudo hwclock -u -w

# RTC → Système (UTC)
sudo hwclock -u -s
```

### 🗂️ Fichier /etc/adjtime

**`/etc/adjtime`** : Fichier stockant les informations sur l'horloge matérielle.

**Contenu :**
```bash
cat /etc/adjtime
# 0.123456 1770750942 0.000000
# 1770750942
# UTC
```

**Structure (3 lignes) :**

| Ligne | Contenu | Description |
|-------|---------|-------------|
| **1** | Correction drift | `drift_seconds last_adjustment_time adjustment_factor` |
| **2** | Dernier ajustement | Timestamp Unix de la dernière synchro |
| **3** | Mode horloge | `UTC` ou `LOCAL` |

**Ligne 3 - Mode horloge :**
```
UTC    → Hardware clock stockée en UTC
LOCAL  → Hardware clock stockée en heure locale
```

**Importance :**
- Si option `-u` ou `-l` NON spécifiée dans `hwclock`, le mode est lu depuis `/etc/adjtime`
- Créé/mis à jour automatiquement par `hwclock`, `timedatectl`

**Exemple :**

```bash
# Vérifier le mode actuel
cat /etc/adjtime | tail -1
# UTC

# Changer vers heure locale (via timedatectl)
sudo timedatectl set-local-rtc 1

# Vérifier modification
cat /etc/adjtime | tail -1
# LOCAL

# Revenir en UTC (recommandé)
sudo timedatectl set-local-rtc 0

cat /etc/adjtime | tail -1
# UTC
```

**⚠️ Ne JAMAIS éditer /etc/adjtime manuellement !**  
Utiliser `timedatectl` ou laisser `hwclock` le gérer.

### 🗺️ Fichiers Timezone (Rappel)

#### /etc/localtime

**Fichier principal** définissant le timezone système (vu dans 107.3).

```bash
# Symlink vers timezone
ls -la /etc/localtime
# lrwxrwxrwx 1 root root 33 Feb 11 10:00 /etc/localtime -> /usr/share/zoneinfo/Europe/Paris

# Au boot :
# 1. Linux lit RTC (ex: 13:35 UTC)
# 2. Applique offset depuis /etc/localtime (Europe/Paris = UTC+1)
# 3. Affiche 14:35 CET
```

#### /etc/timezone

**Fichier historique** (Debian/Ubuntu) contenant le nom du timezone.

```bash
cat /etc/timezone
# Europe/Paris

# ⚠️ Peu utilisé sur systèmes modernes
# /etc/localtime est prioritaire
# Maintenu pour compatibilité avec anciens scripts
```

**Relation entre fichiers :**
```
/etc/localtime       → Fichier de données timezone (binaire)
/etc/timezone        → Nom lisible (texte)
/etc/adjtime         → Mode RTC (UTC/LOCAL)
```

---

## 🕐 Commande date - Modification Système

### 🔧 Afficher et Définir l'Heure

```bash
# Afficher heure système (défaut)
date
# Tue Feb 11 14:35:42 CET 2026

# Définir heure système manuellement (root requis)
sudo date -s "2026-02-11 15:00:00"

# Vérifier
date
# Tue Feb 11 15:00:03 CET 2026

# Autres formats acceptés
sudo date -s "15:30:00"                    # Heure seulement (même date)
sudo date -s "2026-02-12"                  # Date seulement (minuit)
sudo date -s "next Friday"                 # Relatif
sudo date -s "11 Feb 2026 16:00:00"        # Format texte
```

**⚠️ ATTENTION - Impacts :**
```bash
# Modifier l'heure système affecte IMMÉDIATEMENT :
# - Logs (timestamps décalés)
# - Cron (peut exécuter ou manquer tâches)
# - Services avec timeouts
# - Bases de données (transactions)
# - Certificats SSL (validation date)
# - Systèmes de fichiers (mtime, atime)
```

**❌ NON RECOMMANDÉ en production :**
- Préférer synchronisation NTP/Chrony
- Ou `timedatectl set-time`
- `date -s` uniquement pour tests/urgence

### 📅 Utilisation Correcte de date

```bash
# ✅ Affichage formaté (vue dans 107.3)
date +"%F %T"
# 2026-02-11 14:35:42

# ✅ Calculs de dates
date -d "tomorrow"
date -d "next Monday"
date -d "+2 days"

# ✅ Timestamp Unix
date +%s
# 1770750942

# ❌ Modification heure (éviter)
sudo date -s "16:00:00"
```

**Alternative recommandée :**
```bash
# Utiliser timedatectl pour modifier l'heure
sudo timedatectl set-time "2026-02-11 15:00:00"
sudo timedatectl set-time "15:30:00"

# Plus sûr : met à jour RTC automatiquement
```

### 📅 Sélecteurs de Format de Date

**La commande `date` accepte des sélecteurs de format pour afficher l'heure de manière personnalisée.**

**Syntaxe :**
```bash
date +"%sélecteur"
date +"%sélecteur1 %sélecteur2"
```

**Sélecteurs d'heure :**

| Sélecteur | Description | Exemple | Format |
|-----------|-------------|---------|--------|
| **%H** | Heure (24h) | 14 | 00..23 |
| **%M** | Minutes | 35 | 00..59 |
| **%S** | Secondes | 42 | 00..60 |
| **%T** | Heure complète (24h) | 14:35:42 | %H:%M:%S |
| **%r** | Heure complète (12h) | 02:35:42 PM | 12h format |
| **%Z** | Fuseau horaire | CET | Zone name |

**Sélecteurs de date :**

| Sélecteur | Description | Exemple | Format |
|-----------|-------------|---------|--------|
| **%a** | Jour abrégé | lun | Mon, Tue, Wed |
| **%A** | Jour complet | lundi | Monday, Tuesday |
| **%b** | Mois abrégé | fév | Jan, Feb, Mar |
| **%B** | Mois complet | février | January, February |
| **%d** | Jour du mois | 11 | 01..31 |
| **%j** | Jour de l'année | 042 | 001..366 |
| **%m** | Numéro du mois | 02 | 01..12 |
| **%U** | Numéro de semaine | 06 | 00..53 |
| **%y** | Année (2 chiffres) | 26 | 00..99 |
| **%Y** | Année complète | 2026 | YYYY |

**Exemples pratiques :**

```bash
# Heure actuelle
date +"%H:%M:%S"
# 14:35:42

# Format 12h
date +"%r"
# 02:35:42 PM

# Date complète
date +"%A %d %B %Y"
# lundi 11 février 2026

# Date abrégée
date +"%a %d %b %y"
# lun 11 fév 26

# Format ISO 8601
date +"%Y-%m-%d %T"
# 2026-02-11 14:35:42

# Jour de l'année
date +"%j"
# 042

# Numéro de semaine
date +"%U"
# 06

# Fuseau horaire
date +"%Z"
# CET

# Combinaison personnalisée
date +"Nous sommes le %A %d %B %Y, il est %H:%M:%S (%Z)"
# Nous sommes le lundi 11 février 2026, il est 14:35:42 (CET)

# Timestamp Unix + lisible
date +"Timestamp: %s - Date: %F %T"
# Timestamp: 1770750942 - Date: 2026-02-11 14:35:42

# Pour logs
date +"%Y-%m-%d_%H-%M-%S"
# 2026-02-11_14-35-42
```

**💡 Astuce - Format pour fichiers de log :**
```bash
# Créer fichier de backup avec date
cp /etc/config /etc/config.$(date +"%Y%m%d_%H%M%S")
# /etc/config.20260211_143542

# Nom de fichier log
logfile="/var/log/app_$(date +"%Y-%m-%d").log"
# /var/log/app_2026-02-11.log
```

---

# 🌐 PARTIE 2 : SYNCHRONISATION TEMPS - NTP & CHRONY

## 🎯 Principe de la Synchronisation Temps

**Problème :** Les horloges ordinateurs dérivent (drift).
- Hardware imparfait (cristal oscillateur)
- Température, voltage
- **Dérive typique** : 1-50 secondes par jour

**Solution :** Synchroniser avec serveurs de temps de référence (NTP).

```
┌──────────────────────────────────────────────────────┐
│  HIÉRARCHIE NTP (Stratum)                            │
├──────────────────────────────────────────────────────┤
│  Stratum 0 : Horloge atomique, GPS                  │
│      ↓                                               │
│  Stratum 1 : Serveurs connectés à Stratum 0         │
│      ↓       (time.google.com, nist.gov...)          │
│  Stratum 2 : Serveurs synchronisés sur Stratum 1    │
│      ↓       (pool.ntp.org)                          │
│  Stratum 3 : Serveurs synchronisés sur Stratum 2    │
│      ↓                                               │
│  Stratum 4-15 : Clients finaux                      │
│               (Votre PC, serveur)                    │
└──────────────────────────────────────────────────────┘
```

**Protocole NTP :**
- ✅ Prend en compte latence réseau (aller-retour)
- ✅ Multiple échantillons (filtrage erreurs)
- ✅ Ajustement progressif (évite sauts temporels)
- ✅ Précision < 1 milliseconde (LAN)

---

## 🔍 ntpdate - Synchronisation Ponctuelle

### 📡 Principe

**`ntpdate`** : Outil simple pour synchroniser **ponctuellement** l'heure avec un serveur NTP.

**⚠️ OBSOLÈTE :** Remplacé par `ntpd -q` ou `chronyd -q`, mais encore utilisé.

**Fonctionnement :**
1. Envoie requête NTP au serveur
2. Reçoit timestamp serveur
3. Calcule offset (décalage)
4. Mesure latence (round-trip)
5. Ajuste system clock

```bash
# Installer ntpdate (si nécessaire)
sudo apt install ntpdate        # Debian/Ubuntu
sudo yum install ntpdate        # RedHat/CentOS

# Synchroniser avec serveur NTP
sudo ntpdate pool.ntp.org

# Sortie exemple :
# 11 Feb 14:35:42 ntpdate[12345]: adjust time server 192.0.2.1 offset 0.123456 sec

# Autres serveurs
sudo ntpdate time.google.com
sudo ntpdate ntp.ubuntu.com
sudo ntpdate 0.fr.pool.ntp.org
sudo ntpdate 1.europe.pool.ntp.org
```

### 📊 Comprendre la Sortie

```bash
sudo ntpdate -q pool.ntp.org

# Sortie :
# server 192.0.2.1, stratum 2, offset 0.123456, delay 0.02345
# server 192.0.2.2, stratum 2, offset 0.123789, delay 0.02567
# server 192.0.2.3, stratum 2, offset 0.123234, delay 0.02123
# 11 Feb 14:35:42 ntpdate[12345]: adjust time server 192.0.2.1 offset 0.123456 sec
```

**Champs :**

| Champ | Description | Exemple |
|-------|-------------|---------|
| **server** | Adresse IP serveur NTP contacté | 192.0.2.1 |
| **stratum** | Niveau hiérarchie (1-15, plus bas = mieux) | 2 |
| **offset** | Décalage système vs serveur (secondes) | 0.123456 |
| **delay** | Latence réseau aller-retour (secondes) | 0.02345 |

**Offset positif** : Votre horloge est EN RETARD  
**Offset négatif** : Votre horloge est EN AVANCE

**Précision avec itérations multiples :**

```bash
# Première synchronisation
sudo ntpdate pool.ntp.org
# offset 2.345678 sec  (gros écart)

# Attendre quelques secondes
sleep 5

# Deuxième synchronisation
sudo ntpdate pool.ntp.org
# offset 0.012345 sec  (correction plus fine)

# Troisième synchronisation
sudo ntpdate pool.ntp.org
# offset 0.000123 sec  (très précis)
```

### ⚙️ Options ntpdate

```bash
# Mode query (ne modifie PAS l'heure, affiche seulement)
ntpdate -q pool.ntp.org

# Mode debug
sudo ntpdate -d pool.ntp.org

# Utiliser port spécifique (par défaut 123)
sudo ntpdate -p 8 pool.ntp.org

# Mode verbeux
sudo ntpdate -v pool.ntp.org

# Utiliser IPv4 seulement
sudo ntpdate -4 pool.ntp.org

# Utiliser IPv6 seulement
sudo ntpdate -6 pool.ntp.org
```

### ⚠️ Conflit avec ntpd

**PROBLÈME :** `ntpdate` et `ntpd` utilisent le même port UDP 123.

```bash
# Si ntpd est actif
sudo ntpdate pool.ntp.org
# the NTP socket is in use, exiting

# Vérifier si ntpd est actif
systemctl status ntpd
systemctl status ntp

# Solution 1 : Arrêter ntpd temporairement
sudo systemctl stop ntpd
sudo ntpdate pool.ntp.org
sudo systemctl start ntpd

# Solution 2 : Utiliser ntpd directement
sudo ntpd -q

# Solution 3 : Utiliser chronyd (recommandé)
sudo chronyc makestep
```

---

## 🕐 NTP Daemon (ntpd) - Synchronisation Continue

### 🎯 Principe

**`ntpd`** : Daemon NTP maintenant l'heure système **continuellement synchronisée**.

**Avantages vs ntpdate :**
- ✅ Synchronisation continue (pas ponctuelle)
- ✅ Ajustement progressif (slew) évitant sauts temporels
- ✅ Peut servir de serveur NTP (relay)
- ✅ Algorithmes avancés (discipline d'horloge)
- ✅ Statistiques et monitoring

**Cas d'usage :**
- Serveurs de production
- Infrastructures nécessitant temps précis
- Systèmes devant servir de référence temps

### 🔧 Installation et Démarrage

```bash
# Vérifier si installé
which ntpd
dpkg -l | grep ntp          # Debian/Ubuntu
rpm -qa | grep ntp          # RedHat/CentOS

# Installer (Debian/Ubuntu)
sudo apt update
sudo apt install ntp

# Installer (RedHat/CentOS)
sudo yum install ntp

# Démarrer le service
sudo systemctl start ntpd
sudo systemctl start ntp    # Sur certaines distributions

# Activer au démarrage
sudo systemctl enable ntpd

# Vérifier statut
systemctl status ntpd

# Sortie :
# ● ntpd.service - Network Time Service
#      Loaded: loaded (/lib/systemd/system/ntpd.service; enabled)
#      Active: active (running) since Tue 2026-02-11 14:35:42 CET
#      ...

# Vérifier processus
ps aux | grep ntpd
# ntp      1234  0.0  0.1  12345  6789 ?  Ss  14:35  0:00 /usr/sbin/ntpd -p /var/run/ntpd.pid -g -u ntp:ntp
```

### 📝 Configuration - /etc/ntp.conf ou /etc/ntpd.conf

**Fichier de configuration principal de ntpd.**

**📂 Emplacements possibles :**
```bash
# La configuration de NTP peut se trouver dans :
/etc/ntp.conf       # Standard (Debian, Ubuntu, RHEL/CentOS anciennes versions)
/etc/ntpd.conf      # Certaines distributions OpenBSD, FreeBSD

# Vérifier quel fichier existe
ls -l /etc/ntp.conf /etc/ntpd.conf 2>/dev/null

# Éditer la configuration (selon votre système)
sudo vim /etc/ntp.conf
# OU
sudo vim /etc/ntpd.conf
```

**Exemple de configuration :**
```bash
# /etc/ntp.conf (ou /etc/ntpd.conf)

# Fichier de drift (dérive horloge)
driftfile /var/lib/ntp/ntp.drift

# Statistiques
statsdir /var/log/ntpstats/
statistics loopstats peerstats clockstats
filegen loopstats file loopstats type day enable
filegen peerstats file peerstats type day enable
filegen clockstats file clockstats type day enable

# Pool de serveurs NTP (avec iburst pour sync rapide au démarrage)
pool 0.debian.pool.ntp.org iburst
pool 1.debian.pool.ntp.org iburst
pool 2.debian.pool.ntp.org iburst
pool 3.debian.pool.ntp.org iburst

# Ou serveurs individuels
server 0.fr.pool.ntp.org iburst
server 1.fr.pool.ntp.org iburst
server time.google.com iburst

# Serveur de fallback local (si réseau down)
server 127.127.1.0
fudge 127.127.1.0 stratum 10

# Restrictions d'accès (sécurité)
# Par défaut : refuser tout
restrict default kod notrap nomodify nopeer noquery limited

# Autoriser localhost
restrict 127.0.0.1
restrict ::1

# Autoriser réseau local à query (optionnel, si serveur NTP local)
restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap

# Autoriser serveurs NTP pool à répondre
restrict source nomodify noquery notrap
```

**Directives importantes :**

| Directive | Description | Exemple |
|-----------|-------------|---------|
| **pool** | Pool de serveurs NTP (round-robin) | `pool pool.ntp.org iburst` |
| **server** | Déclarer un serveur sur lequel se synchroniser | `server time.google.com iburst` |
| **iburst** | Sync rapide au démarrage (8 requêtes) | `iburst` |
| **prefer** | Serveur préféré | `server ntp.local prefer` |
| **driftfile** | Fichier stockant dérive horloge | `/var/lib/ntp/ntp.drift` |
| **restrict** | Règles d'accès (sécurité) | `restrict 192.168.1.0 mask ...` |

**💡 Directive server - Synchronisation :**
```bash
# Déclarer un serveur NTP pour synchronisation
server <adresse> [options]

# Exemples :
server time.google.com iburst
server ntp.ubuntu.com iburst prefer
server 192.168.1.10

# Options courantes :
# - iburst : 8 requêtes rapides au démarrage (synchronisation plus rapide)
# - prefer : serveur préféré (priorité si plusieurs serveurs)
# - minpoll N : intervalle minimum polling (défaut 6 = 64s)
# - maxpoll N : intervalle maximum polling (défaut 10 = 1024s)
```

**Options restrict :**

| Option | Description |
|--------|-------------|
| `kod` | Kiss Of Death (bloquer clients abusifs) |
| `nomodify` | Empêcher modification config |
| `notrap` | Désactiver trap (remote logging) |
| `nopeer` | Empêcher peering |
| `noquery` | Empêcher requêtes status (ntpq) |
| `limited` | Rate limiting |

**Pools NTP recommandés :**

```bash
# Mondial
pool.ntp.org

# Par continent
europe.pool.ntp.org
north-america.pool.ntp.org
asia.pool.ntp.org

# Par pays (2-letter code)
fr.pool.ntp.org         # France
de.pool.ntp.org         # Allemagne
uk.pool.ntp.org         # Royaume-Uni
us.pool.ntp.org         # États-Unis

# Distribution-specific
debian.pool.ntp.org
ubuntu.pool.ntp.org
centos.pool.ntp.org

# Grands fournisseurs
time.google.com
time.cloudflare.com
time.apple.com
time.windows.com
```

### 🔍 Commande ntpq - Monitoring NTP

**`ntpq`** : Outil pour interroger et monitorer `ntpd`.

#### Afficher les Peers (Serveurs Synchronisés)

```bash
# Lister les serveurs NTP utilisés
ntpq -p

# Sortie exemple :
#      remote           refid      st t when poll reach   delay   offset  jitter
# ==============================================================================
# *time.google.com .GOOG.           1 u   32   64  377    12.345   0.123   0.456
# +ntp1.example.co .GPS.            1 u   28   64  377    23.456   0.234   0.567
# -ntp2.example.co .GPS.            2 u   45   64  377    34.567  -1.234   1.234
#  ntp3.example.co .GPS.            2 u   12   64  377    45.678   5.678   2.345
# xntp4.example.co .INIT.          16 u    -   64    0     0.000   0.000   0.000
```

**Colonnes :**

| Colonne | Description | Exemple |
|---------|-------------|---------|
| **remote** | Adresse serveur NTP | time.google.com |
| **refid** | Source référence du serveur | .GOOG., .GPS. |
| **st** | Stratum (1-16) | 1 (meilleur) |
| **t** | Type : u=unicast, b=broadcast, l=local | u |
| **when** | Secondes depuis dernière réponse | 32 |
| **poll** | Intervalle polling (secondes) | 64 |
| **reach** | Registre succès (octal, 377=parfait) | 377 |
| **delay** | Latence réseau (ms) | 12.345 |
| **offset** | Décalage temps (ms) | 0.123 |
| **jitter** | Variation offset (ms) | 0.456 |

**Préfixes (symboles) :**

| Symbole | État | Description |
|---------|------|-------------|
| `*` | **Sync** | Serveur de référence ACTUEL (synchronisé) |
| `+` | **Candidat** | Bon serveur, candidat pour sync |
| `-` | **Écarté** | Serveur valide mais écarté (précision moindre) |
| `x` | **Falseticker** | Serveur désynchronisé (ignoré) |
| `#` | **Bon** | Bon serveur mais pas sélectionné |
| ` ` | **Rejeté** | Serveur rejeté (trop d'erreurs) |

**Interprétation :**

```bash
# ✅ BON : Au moins un *
# *time.google.com   → Synchronisé avec ce serveur
# +ntp1.example.com  → Backup prêt

# ⚠️ MOYEN : Seulement +
# Pas de *, mais des + candidats → NTP cherche référence stable

# ❌ MAUVAIS : Tous x ou espaces
# Aucun serveur fiable → Vérifier réseau, config
```

#### Autres Commandes ntpq

```bash
# Mode interactif
ntpq
ntpq> help
ntpq> peers
ntpq> associations
ntpq> exit

# Afficher variables système
ntpq -c sysinfo

# Afficher version
ntpq -c version

# Verbose
ntpq -c "rv 0"    # Read variables association 0 (system)
```

### � Commande ntpstat - État de Synchronisation

**`ntpstat`** : Affiche l'état de synchronisation NTP de manière simple.

**Syntaxe :**
```bash
ntpstat
```

**Exemples de sortie :**

**✅ Synchronisé :**
```bash
ntpstat
# synchronised to NTP server (216.239.35.0) at stratum 2
#    time correct to within 42 ms
#    polling server every 64 s
```

**⏳ En cours de synchronisation :**
```bash
ntpstat
# unsynchronised
#    polling server every 64 s
```

**❌ Non synchronisé / Pas de serveur :**
```bash
ntpstat
# unsynchronised
#    time server re-starting
#       polling server every 64 s
```

**Codes de retour :**

| Code | État | Description |
|------|------|-------------|
| **0** | Synchronisé | Horloge synchronisée avec serveur NTP |
| **1** | Non synchronisé | En attente sync ou serveur inaccessible |
| **2** | Erreur | ntpd non démarré ou erreur config |

**Utilisation dans scripts :**
```bash
# Vérifier état sync
if ntpstat > /dev/null 2>&1; then
    echo "✅ Système synchronisé avec NTP"
else
    echo "❌ Système NON synchronisé"
fi

# Attendre synchronisation
echo "Attente synchronisation NTP..."
while ! ntpstat > /dev/null 2>&1; do
    sleep 2
done
echo "✅ Synchronisation établie"
```

**💡 Différence ntpstat vs ntpq -p :**

| Commande | Usage | Détails |
|----------|-------|---------|
| **ntpstat** | État global sync (simple) | Oui/Non synchronisé |
| **ntpq -p** | Liste serveurs (détaillé) | Tous les peers, offsets, jitter |

### �🔄 Recharger Configuration

```bash
# Modifier /etc/ntp.conf
sudo vim /etc/ntp.conf

# Recharger configuration (sans redémarrage)
sudo systemctl reload ntpd

# Ou redémarrer complètement
sudo systemctl restart ntpd

# Vérifier changements
ntpq -p
```

---

## 🐧 Chrony - Alternative Moderne à NTP

### 🎯 Principe et Avantages

**Chrony** : Implémentation moderne de NTP, remplaçant `ntpd` sur beaucoup de distributions.

**Avantages de Chrony :**

| Critère | ntpd | chronyd |
|---------|------|---------|
| **Synchronisation initiale** | Lente | Rapide |
| **Connexions intermittentes** | ❌ Mauvais | ✅ Excellent (laptops, mobiles) |
| **Précision** | < 1 ms (LAN) | < 1 µs (LAN) |
| **Grands changements temps** | Refuse (sécurité) | Ajuste progressivement |
| **Démarrage rapide** | Plusieurs minutes | Quelques secondes |
| **Ressources** | Plus lourdes | Plus légères |
| **Complexité config** | Moyenne | Simple |
| **Utilisation** | Serveurs stratum 1-2 | Clients, laptops, serveurs |

**Cas d'usage Chrony :**
- ✅ Laptops (connexion WiFi intermittente)
- ✅ Machines virtuelles (horloge instable)
- ✅ Systèmes arrêtés/redémarrés fréquemment
- ✅ Réseaux avec latence variable
- ✅ Desktop Linux moderne

**Distribution utilisant Chrony par défaut :**
- Fedora, RHEL 8+, CentOS 8+
- Ubuntu 18.04+
- Debian 10+
- openSUSE

### 🔧 Installation

```bash
# Vérifier si installé
which chronyd
systemctl status chronyd

# Installer (Debian/Ubuntu)
sudo apt install chrony

# Installer (RedHat/CentOS)
sudo yum install chrony

# Démarrer
sudo systemctl start chronyd
sudo systemctl enable chronyd

# Vérifier
systemctl status chronyd
```

**⚠️ Conflit ntpd/chronyd :**
```bash
# Ne PAS exécuter ntpd et chronyd simultanément !

# Désactiver ntpd
sudo systemctl stop ntpd
sudo systemctl disable ntpd

# Activer chronyd
sudo systemctl enable --now chronyd
```

### 📝 Configuration - /etc/chrony/chrony.conf

**Fichier de configuration Chrony.**

```bash
# Éditer config
sudo vim /etc/chrony/chrony.conf

# Exemple configuration
# /etc/chrony/chrony.conf

# Pool de serveurs NTP
pool 2.debian.pool.ntp.org iburst
pool 0.europe.pool.ntp.org iburst
pool 1.europe.pool.ntp.org iburst

# Ou serveurs individuels
server time.google.com iburst
server time.cloudflare.com iburst

# Fichier de drift
driftfile /var/lib/chrony/drift

# Autoriser ajustements système (sudo)
makestep 1.0 3

# RTC (horloge matérielle)
rtcsync

# Log
logdir /var/log/chrony
log measurements statistics tracking

# Autoriser clients locaux (si serveur NTP)
allow 192.168.1.0/24

# Deny par défaut
deny all
```

**Directives importantes :**

| Directive | Description | Exemple |
|-----------|-------------|---------|
| **server** | Serveur NTP spécifique à utiliser, avec ses options | `server time.google.com iburst` |
| **pool** | Pool de serveurs NTP (résolution fournit plusieurs serveurs) | `pool pool.ntp.org iburst` |
| **iburst** | 4 requêtes rapides au démarrage | `iburst` |
| **driftfile** | Fichier dérive horloge | `/var/lib/chrony/drift` |
| **makestep** | Contrôle vitesse rattrapage décalage temporel | `makestep 1.0 3` |
| **rtcsync** | L'heure système est régulièrement synchronisée avec l'heure RTC | `rtcsync` |
| **leapsectz** | Gestion secondes intercalaires selon fuseau horaire | `leapsectz right/UTC` |
| **allow** | Autoriser clients (si serveur) | `allow 192.168.1.0/24` |
| **deny** | Refuser clients | `deny all` |
| **logdir** | Répertoire logs | `/var/log/chrony` |

**server vs pool :**
```bash
# server : définit un serveur NTP UNIQUE
server time.google.com iburst
# → 1 seul serveur DNS : time.google.com

# pool : définit un POOL de serveurs (plusieurs adresses)
pool 2.debian.pool.ntp.org iburst
# → Résolution DNS fournit PLUSIEURS adresses de serveurs NTP
# → Chrony sélectionne automatiquement les meilleurs

# Avantage pool : redondance et répartition de charge
```

**makestep - Saut temporel :**
```bash
makestep <threshold> <limit>

# makestep 1.0 3
# Permet de contrôler la vitesse du rattrapage du décalage temporel
# Si offset > 1 seconde : forcer mise à jour (STEP/saut)
# Autorisé pour les 3 premières mises à jour seulement
# Après : slew progressif uniquement

# Cas d'usage : boot système avec RTC décalée
# → 1er ajustement rapide (step)
# → Ajustements suivants lents (slew)
```

**rtcsync - Synchronisation RTC :**
```bash
# rtcsync active
# L'heure système est régulièrement synchronisée avec l'heure RTC
# Chrony synchronise automatiquement /dev/rtc (hardware clock)
# Équivalent à : hwclock -w automatique périodique toutes les 11 minutes
```

**leapsectz - Secondes Intercalaires :**
```bash
# leapsectz : gestion des secondes intercalaires en fonction du fuseau horaire

leapsectz right/UTC

# Contexte :
# - Le temps universel TU1 (temps solaire moyen) n'est pas parfaitement 
#   identique à UTC (temps atomique)
# - Il s'ensuit un décalage dans le temps
# - Depuis 1972, 37 secondes ont été ajoutées afin de rester synchronisé
# - Ces ajustements sont appelés "secondes intercalaires" (leap seconds)

# right/UTC : fuseau horaire avec gestion des leap seconds
# Chrony ajuste automatiquement lors des annonces leap seconds

# Exemple de fichiers timezone avec leap seconds :
# /usr/share/zoneinfo/right/UTC
# /usr/share/zoneinfo/right/Europe/Paris
```

### 🔍 Commande chronyc - Client Chrony

**`chronyc`** : Outil pour interroger et contrôler `chronyd`.

**💡 Utilisation :**
La commande **chronyc s'utilise avec des arguments** (mode non-interactif) **ou de manière interactive** (CLI).

#### Mode Interactif

```bash
# Lancer CLI interactive
chronyc

chronyc> help
# Affiche toutes les commandes

chronyc> tracking
# Affiche état synchronisation (vérifier l'état de la synchronisation)

chronyc> sources
# Affiche serveurs NTP (affiche les sources de synchronisation)

chronyc> sourcestats
# Statistiques sur les sources (avoir des statistiques sur les sources)

chronyc> makestep
# Force une mise à jour immédiate (saut temporel)

chronyc> exit
```

#### Mode Non-Interactif (Commandes Directes)

**tracking - Vérifier État Synchronisation**

```bash
chronyc tracking

# Sortie :
# Reference ID    : C0A80101 (192.168.1.1)
# Stratum         : 3
# Ref time (UTC)  : Tue Feb 11 13:35:42 2026
# System time     : 0.000123456 seconds slow of NTP time
# Last offset     : +0.000234567 seconds
# RMS offset      : 0.000345678 seconds
# Frequency       : 12.345 ppm fast
# Residual freq   : +0.123 ppm
# Skew            : 0.456 ppm
# Root delay      : 0.012345678 seconds
# Root dispersion : 0.001234567 seconds
# Update interval : 64.5 seconds
# Leap status     : Normal
```

**Champs importants :**

| Champ | Description | Bon État |
|-------|-------------|----------|
| **Reference ID** | Serveur NTP de référence | IP ou nom |
| **Stratum** | Niveau hiérarchie | 1-4 |
| **System time** | Décalage vs NTP | < 0.001 s |
| **Last offset** | Dernier ajustement | < 0.01 s |
| **Frequency** | Dérive horloge (ppm) | < 100 ppm |
| **Leap status** | État secondes intercalaires | Normal |

**sources - Serveurs NTP**

```bash
chronyc sources

# Sortie :
# MS Name/IP address         Stratum Poll Reach LastRx Last sample               
# ===============================================================================
# ^* time.google.com               1   6   377    32   +123us[ +456us] +/-   12ms
# ^+ ntp1.example.com              1   6   377    28   +234us[ +567us] +/-   23ms
# ^- ntp2.example.com              2   6   377    45  -1234us[-1567us] +/-   34ms
# ^? ntp3.example.com              2   6     0     -      +0ns[   +0ns] +/-    0ns
```

**Préfixes (symboles) :**

| Symbole | État | Description |
|---------|------|-------------|
| `^*` | **Synced** | Serveur de référence actuel |
| `^+` | **Combiné** | Serveur combiné pour précision |
| `^-` | **Pas combiné** | Serveur valide mais non utilisé |
| `^?` | **Unreachable** | Serveur injoignable |
| `^x` | **Falseticker** | Serveur avec temps faux |

**Colonnes :**

| Colonne | Description |
|---------|-------------|
| **Name/IP** | Adresse serveur |
| **Stratum** | Niveau hiérarchie |
| **Poll** | Intervalle polling (log2 secondes) |
| **Reach** | Succès dernières 8 tentatives (octal) |
| **LastRx** | Secondes depuis dernière réponse |
| **Last sample** | Offset et jitter |

**activity - Activité Sources**

```bash
chronyc activity

# Sortie :
# 200 OK
# 4 sources online
# 0 sources offline
# 0 sources doing burst (return to online)
# 0 sources doing burst (return to offline)
# 0 sources with unknown address
```

**sourcestats - Statistiques Sources**

```bash
chronyc sourcestats

# Sortie :
# Name/IP Address            NP  NR  Span  Frequency  Freq Skew  Offset  Std Dev
# ===============================================================================
# time.google.com            12   7   193     +0.123      1.234  +123us   456us
# ntp1.example.com           10   5   128     -0.234      2.345  -234us   567us
```

### 🔧 Commandes Utiles chronyc

```bash
# Forcer synchronisation immédiate (saut temporel si nécessaire)
sudo chronyc makestep

# Ajouter serveur NTP temporairement
sudo chronyc add server time.apple.com

# Supprimer serveur
sudo chronyc delete time.apple.com

# Vider historique statistiques
sudo chronyc reset sources

# Dump configuration actuelle
chronyc dump

# Vérifier accès NTP (online/offline)
chronyc -a 'online'
chronyc -a 'offline'

# Afficher version
chronyc -v
```

### 🔄 Recharger Configuration

```bash
# Modifier configuration
sudo vim /etc/chrony/chrony.conf

# Relire configuration (recharge serveurs NTP)
sudo chronyc reload sources

# Ou redémarrer service
sudo systemctl restart chronyd

# Vérifier
chronyc sources
```

---

## ⏰ AJUSTEMENT TEMPS : Sauts vs Slew

### 🎯 Problème des Grands Changements Temporels

**Problème :** Que se passe-t-il si l'horloge système est décalée de plusieurs heures/jours ?

**Impacts potentiels :**

```
❌ Changement brutal (ex: 10:00 → 18:00 instantané)
   ↓
├─ Logs incohérents (timestamps décalés)
├─ Transactions bancaires foireuses
├─ Certificats SSL invalidés
├─ Bases de données corrompues
├─ Cron manque ou double exécution
├─ Makefiles rebuild tout
└─ Applications crash (timeouts négatifs)
```

**Solutions NTP/Chrony :**

### 🔧 NTP (ntpd) - Refus Sécurisé et Gestion de la Dérive

**Comportement ntpd :**

**📊 Gestion de la dérive - 3 seuils critiques :**

Si le service NTP détecte une dérive (delta entre horloge système et temps réel), il s'efforce de corriger progressivement le problème selon 3 règles :

**1. Delta < 128 millisecondes :**
```bash
# NTP ajuste AUTOMATIQUEMENT et IMMÉDIATEMENT l'horloge système
# Méthode : STEP (saut unique)
# Pas d'intervention nécessaire
```

**2. Delta ≥ 128 ms et < 1000 secondes :**
```bash
# NTP ajuste LENTEMENT l'horloge système
# Méthode : SLEW (ajustement progressif)
# Vitesse : +/- 0,5 millisecondes par seconde maximum
# Temps de correction : peut prendre plusieurs heures

# Exemple : Dérive de 500 secondes (8min20s)
# Correction progressive : 500s / 0.0005s/s = 1 000 000 secondes ≈ 11,5 jours
```

**3. Delta ≥ 1000 secondes (≈17 minutes) :**
```bash
# ❌ NTP REFUSE de synchroniser automatiquement
# Le service NTP ne démarre PAS ou s'arrête

# Log ntpd :
# ntpd[1234]: time correction of 3600 seconds exceeds sanity limit (1000); 
# set clock manually to the correct UTC time.
```

**💡 Solutions pour dérive > 1000 secondes :**

**Solution 1 : Correction manuelle via ntpdate**
```bash
# Arrêter ntpd
sudo systemctl stop ntpd

# Synchroniser manuellement (force saut temporel)
sudo ntpdate -b pool.ntp.org    # -b = force step (saut brutal)

# Redémarrer ntpd
sudo systemctl start ntpd
```

**Solution 2 : Utiliser ntpd -gq (RECOMMANDÉ)**
```bash
# L'administrateur peut remettre à l'heure le système
# si le service n'est pas actif, avec cette commande :

sudo ntpd -gq

# Options :
# -g : ignore le seuil de panique (panic threshold)
#      permet synchronisation même si delta > 1000s
# -q : quit après synchronisation (mode one-shot)
#      ntpd fait la sync puis s'arrête

# Ensuite démarrer le service normalement
sudo systemctl start ntpd
```

**Solution 3 : ntpdate AVANT démarrage ntpd**
```bash
# Synchroniser d'abord avec ntpdate
sudo ntpdate pool.ntp.org

# Puis démarrer ntpd normalement
sudo systemctl start ntpd
```

**📊 Récapitulatif des seuils ntpd :**

| Dérive (Delta) | Méthode | Comportement | Intervention |
|----------------|---------|--------------|--------------|
| **< 128 ms** | STEP (saut immédiat) | Ajustement automatique instantané | ✅ Aucune |
| **128 ms - 1000 s** | SLEW (progressif) | Ajustement lent à 0,5 ms/s | ✅ Aucune (patience) |
| **> 1000 s** | REFUS | Service refuse de démarrer | ❌ `ntpd -gq` requis |

**💡 Note importante :**
- **STEP** (saut) = changement brutal de l'heure
- **SLEW** (glissement) = accélération/ralentissement progressif de l'horloge
- SLEW préféré car moins d'impact sur les applications

### 🚀 Chrony - Slew Progressif

**Comportement chronyd : PLUS INTELLIGENT**

**Ajustement progressif (slew) :**
```bash
# Chrony "ralentit" ou "accélère" le temps graduellement
# Au lieu de faire un saut brutal

# Exemple : Horloge 10 minutes en retard
# → Chrony accélère le temps pour rattraper

# Mécanisme :
# 1. Calcule offset (ex: -600 secondes = 10 min retard)
# 2. Ajuste fréquence horloge (ex: ajoute 0.01% vitesse)
# 3. Sur plusieurs heures/jours, rattrape le retard
# 4. Applications ne voient PAS de saut temporel
```

**Directive makestep :**
```bash
# /etc/chrony/chrony.conf
makestep 1.0 3

# Signification :
# - Si offset > 1 seconde : autoriser SAUT (step)
# - Limité aux 3 premières mises à jour seulement
# - Après : SLEW uniquement (progressif)

# Cas d'usage :
# Boot système avec RTC très décalée
# → 1er ajustement = SAUT (rapide)
# → Ajustements suivants = SLEW (smooth)
```

**Forcer saut manuel :**
```bash
# Si besoin saut immédiat (urgence)
sudo chronyc makestep

# Chrony fait un saut PUIS revient en mode slew
```

**Avantage Chrony :**
```
✅ Pas de "voyage dans le temps"
✅ Logs cohérents
✅ Applications stables
✅ Synchronisation même gros écarts (sans intervention manuelle)
```

### 🌍 Leap Seconds (Secondes Intercalaires)

**Leap second** : Seconde ajoutée/retirée pour compenser rotation Terre.

**Status :**
```bash
# Vérifier leap status
chronyc tracking | grep "Leap status"
# Leap status     : Normal

# États possibles :
# Normal           → Pas de leap second prévu
# Insert second    → Seconde ajoutée à minuit UTC
# Delete second    → Seconde retirée à minuit UTC
# Not synchronised → Pas synchronisé (pas d'info leap)
```

**Gestion automatique :**
- NTP/Chrony gèrent automatiquement les leap seconds
- Annoncées des mois à l'avance
- Transparentes pour utilisateur

**Fichier leap seconds :**
```bash
# Certains systèmes utilisent fichier leap-seconds.list
ls /usr/share/zoneinfo/leap-seconds.list

# Chrony télécharge automatiquement
# ntpd peut nécessiter mise à jour manuelle
```

---

## 🎓 Scénarios Pratiques

### Scénario 1 : Synchroniser Serveur Production

```bash
# 1. Installer Chrony (moderne, fiable)
sudo apt install chrony

# 2. Configurer pool NTP
sudo vim /etc/chrony/chrony.conf
# pool 0.debian.pool.ntp.org iburst
# pool 1.debian.pool.ntp.org iburst
# rtcsync
# makestep 1.0 3

# 3. Démarrer service
sudo systemctl enable --now chronyd

# 4. Vérifier synchronisation
chronyc tracking
chronyc sources

# 5. Synchroniser RTC
sudo hwclock -u -w
```

### Scénario 2 : Laptop Voyage (Connexion Intermittente)

```bash
# Chrony idéal pour laptops !
sudo apt install chrony

# Config : /etc/chrony/chrony.conf
# pool pool.ntp.org iburst maxdelay 0.1
# rtcsync
# makestep 1.0 -1   # -1 = toujours autoriser step si besoin

sudo systemctl enable --now chronyd

# Chrony synchronise rapidement après connexion WiFi
```

### Scénario 3 : Correction Hardware Clock Décalée

```bash
# RTC décalée de 2 heures
hwclock
# 2026-02-11 16:35:42 (au lieu de 14:35)

# 1. Synchroniser système via NTP
sudo chronyc makestep
chronyc tracking
# System time : 0.000001 seconds slow

# 2. Écrire heure système vers RTC
sudo hwclock -u -w

# 3. Vérifier RTC
sudo hwclock -u
# 2026-02-11 13:35:45 (UTC correct)
```

### Scénario 4 : Dual-Boot Windows/Linux

```bash
# Problème : Windows modifie RTC en local time
# Linux attend RTC en UTC

# Solution 1 : Linux en local time (pas recommandé)
sudo timedatectl set-local-rtc 1

# Solution 2 : Windows en UTC (recommandé)
# Dans Windows (admin PowerShell) :
# reg add "HKLM\System\CurrentControlSet\Control\TimeZoneInformation" /v RealTimeIsUniversal /d 1 /t REG_DWORD /f

# Linux reste en UTC
sudo timedatectl set-local-rtc 0
```

### Scénario 5 : Serveur NTP Local (Relay)

```bash
# Configuration Chrony comme serveur relay

# /etc/chrony/chrony.conf
pool pool.ntp.org iburst
rtcsync
allow 192.168.1.0/24    # Autoriser clients LAN
deny all                # Refuser autres
local stratum 10        # Servir temps même si déconnecté

sudo systemctl restart chronyd

# Clients LAN utilisent :
# server 192.168.1.254 iburst
```

---

## 📝 Cheat Sheet

### hwclock

```bash
# Afficher RTC
hwclock
hwclock -u          # Supposer UTC
hwclock -l          # Supposer local time

# Définir RTC manuellement
sudo hwclock --set --date="2026-02-11 14:00:00"

# Système → RTC
sudo hwclock -u -w
sudo hwclock --utc --systohc

# RTC → Système
sudo hwclock -u -s
sudo hwclock --utc --hctosys

# Fichiers
/etc/adjtime        # Mode RTC (UTC/LOCAL)
/dev/rtc            # Device horloge matérielle
```

### ntpdate / ntpd

```bash
# Synchronisation ponctuelle
sudo ntpdate pool.ntp.org
sudo ntpdate -q pool.ntp.org    # Query only

# Service ntpd
sudo systemctl start ntpd
sudo systemctl enable ntpd

# Config
/etc/ntp.conf       # Configuration NTP

# Monitoring
ntpq -p             # Afficher peers
ntpq -c sysinfo     # Info système
```

### Chrony

```bash
# Service
sudo systemctl start chronyd
sudo systemctl enable chronyd

# Config
/etc/chrony/chrony.conf

# Commandes
chronyc tracking    # État synchronisation
chronyc sources     # Serveurs NTP
chronyc activity    # Activité
chronyc makestep    # Forcer step

# Directives config importantes
pool pool.ntp.org iburst
makestep 1.0 3
rtcsync
allow 192.168.1.0/24
```

### timedatectl

```bash
# Afficher config complète
timedatectl

# Définir heure
sudo timedatectl set-time "2026-02-11 15:00:00"

# NTP
sudo timedatectl set-ntp true

# RTC mode
sudo timedatectl set-local-rtc 0    # UTC
sudo timedatectl set-local-rtc 1    # Local
```

---

## 🎯 Points Clés pour l'Examen LPIC-1

### Connaissances Essentielles

✅ **Deux horloges :**
- Hardware Clock (RTC) : matérielle, batterie, survit arrêt PC
- System Clock : logicielle, kernel, temps courant système

✅ **hwclock :**
- `-u` / `--utc` : RTC en UTC (recommandé)
- `-l` / `--localtime` : RTC en heure locale
- `-w` / `--systohc` : Système → RTC
- `-s` / `--hctosys` : RTC → Système
- `--set --date="..."` : Définir RTC manuellement

✅ **Fichiers :**
- `/etc/adjtime` : Mode RTC (UTC/LOCAL), drift
- `/etc/localtime` : Timezone système
- `/etc/timezone` : Nom timezone (historique)
- `/dev/rtc` : Device hardware clock

✅ **NTP vs Chrony :**
- **ntpd** : Server-oriented, strict, logging avancé
- **chronyd** : Client-oriented, laptops, connexions instables

✅ **ntpdate :**
- Synchronisation ponctuelle
- Conflit avec ntpd (même socket)
- Obsolète (remplacé par `ntpd -q` ou `chronyc`)

✅ **ntpd :**
- Daemon : `ntpd`
- Config : `/etc/ntp.conf`
- Monitoring : `ntpq -p`
- Symboles : `*` (sync), `+` (candidat), `-` (écarté)

✅ **Chrony :**
- Daemon : `chronyd`
- Config : `/etc/chrony/chrony.conf`
- Client : `chronyc`
- Commandes : `tracking`, `sources`, `activity`, `makestep`
- Directive : `makestep 1.0 3`, `rtcsync`, `iburst`

✅ **Ajustement temps :**
- **Step** : Saut immédiat (brutal)
- **Slew** : Ajustement progressif (smooth)
- ntpd : Refuse > 1000s
- Chrony : Slew intelligent même gros écarts

✅ **Leap seconds :**
- Secondes intercalaires (ajustement rotation Terre)
- Gérées automatiquement par NTP/Chrony
- Status : Normal, Insert second, Delete second

### Commandes à Maîtriser

| Commande | Usage |
|----------|-------|
| `hwclock` | Afficher/configurer RTC |
| `hwclock -u -w` | Système → RTC (UTC) |
| `hwclock -u -s` | RTC → Système (UTC) |
| `ntpdate server` | Sync ponctuelle |
| `ntpq -p` | Monitoring ntpd |
| `chronyc tracking` | État sync Chrony |
| `chronyc sources` | Serveurs NTP Chrony |
| `timedatectl` | Config complète temps |

### Pièges Fréquents

❌ Confondre hwclock et system clock  
❌ Oublier `-u` pour UTC dans hwclock  
❌ Utiliser ntpdate pendant que ntpd tourne  
❌ Ne pas synchroniser RTC après correction système  
❌ Dual-boot : RTC en local pour Windows mais Linux attend UTC  
❌ Modifier directement `/etc/adjtime`  
❌ Faire tourner ntpd ET chronyd ensemble

---

**Formation LPIC-1 102 - Préparation Certification Linux**  
**Cours 108.1 - Gestion du Temps Système**  
**Maxime Chenaud - 11 Février 2026**
