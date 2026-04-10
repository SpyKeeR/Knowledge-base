# 🌍 Localisation et Internationalisation Linux

## 📋 Informations du Module

- **Examen** : LPIC-1 102
- **Objectif** : 107.3 - Localisation and internationalisation
- **Poids** : 3 points
- **Objectif** : Configurer les paramètres de localisation, timezone et encodage des caractères

---

## 🎯 Vue d'Ensemble

**Localisation (l10n)** et **Internationalisation (i18n)** permettent d'adapter un système Linux pour différentes langues, régions et conventions culturelles.

**Composants principaux :**

```
┌─────────────────────────────────────────────────────────┐
│  Localisation & Internationalisation                    │
├─────────────────────────────────────────────────────────┤
│  1. TIMEZONE (Fuseau horaire)                           │
│     → Heure locale vs UTC                               │
│     → /etc/localtime, TZ, timedatectl                   │
│                                                         │
│  2. LOCALE (Conventions régionales)                     │
│     → Langue, formats date/nombre/monnaie               │
│     → Variables LC_*, LANG, LC_ALL                      │
│     → /etc/locale.conf, localectl                       │
│                                                         │
│  3. CHARACTER ENCODING (Encodage)                       │
│     → ASCII, ISO-8859, UTF-8                            │
│     → Conversion avec iconv                             │
└─────────────────────────────────────────────────────────┘
```

---

## 📅 Commande DATE - Formats de Sortie

### 🕐 Rappel Rapide

La commande `date` affiche la date/heure système et permet de formater la sortie avec des clés de format.

**Syntaxe de formatage :**
```bash
date +FORMAT
```

**Clés de format principales :**

| Clé | Description | Exemple |
|-----|-------------|---------|
| `%Y` | Année (4 chiffres) | 2026 |
| `%y` | Année (2 chiffres) | 26 |
| `%m` | Mois (01-12) | 02 |
| `%B` | Mois (nom complet) | February |
| `%b` | Mois (nom abrégé) | Feb |
| `%d` | Jour du mois (01-31) | 11 |
| `%D` | Date format US (mm/dd/yy) | 02/11/26 |
| `%F` | Date ISO 8601 (yyyy-mm-dd) | 2026-02-11 |
| `%H` | Heure (00-23) | 14 |
| `%I` | Heure (01-12) | 02 |
| `%M` | Minute (00-59) | 35 |
| `%S` | Seconde (00-59) | 42 |
| `%T` | Heure complète (HH:MM:SS) | 14:35:42 |
| `%R` | Heure:Minute (HH:MM) | 14:35 |
| `%A` | Jour semaine (nom complet) | Tuesday |
| `%a` | Jour semaine (abrégé) | Tue |
| `%u` | Jour semaine (1-7, 1=lundi) | 2 |
| `%w` | Jour semaine (0-6, 0=dimanche) | 2 |
| `%s` | Timestamp Unix (secondes depuis epoch) | 1770750942 |
| `%Z` | Timezone (nom) | CET |
| `%z` | Timezone (offset UTC) | +0100 |

**Exemples pratiques :**

```bash
# Date actuelle par défaut
date
# Tue Feb 11 14:35:42 CET 2026

# Format ISO 8601
date +%F
# 2026-02-11

# Date et heure complète
date +"%F %T"
# 2026-02-11 14:35:42

# Format personnalisé
date +"%A %d %B %Y - %H:%M:%S"
# Tuesday 11 February 2026 - 14:35:42

# Pour nom de fichier (sans espaces/caractères spéciaux)
date +%Y%m%d_%H%M%S
# 20260211_143542

# Backup avec timestamp
tar -czf backup_$(date +%Y%m%d).tar.gz /home
# backup_20260211.tar.gz

# Timestamp Unix
date +%s
# 1770750942

# Date avec timezone
date +"%F %T %Z (%z)"
# 2026-02-11 14:35:42 CET (+0100)
```

**⚠️ Dans crontab, échapper les % :**
```bash
# ❌ ERREUR dans crontab
0 2 * * * backup_$(date +%Y%m%d).sh

# ✅ CORRECT
0 2 * * * backup_$(date +\%Y\%m\%d).sh
```

---

## 🌐 TIMEZONE - Gestion des Fuseaux Horaires

### 🕰️ Concepts Fondamentaux

**UTC (Coordinated Universal Time)** : Temps universel de référence (ancien GMT).

**Timezone** : Décalage par rapport à UTC + règles de changement d'heure (DST - Daylight Saving Time).

```
┌──────────────────────────────────────────────────────┐
│  Horloge Matérielle (RTC - Real Time Clock)         │
│  → Stockée dans le BIOS/UEFI                         │
│  → Peut être en UTC ou heure locale                  │
└──────────────┬───────────────────────────────────────┘
               │
               ↓
┌──────────────────────────────────────────────────────┐
│  Horloge Système (System Clock)                      │
│  → Maintenue par le noyau Linux                      │
│  → Généralement en UTC                               │
└──────────────┬───────────────────────────────────────┘
               │
               ↓ Applique Timezone
┌──────────────────────────────────────────────────────┐
│  Heure Locale Affichée                               │
│  → UTC + Timezone offset + DST                       │
└──────────────────────────────────────────────────────┘
```

### 📊 timedatectl - Affichage et Configuration (systemd)

**`timedatectl`** : Outil systemd pour gérer l'heure, la date et le timezone.

#### Afficher la Configuration

```bash
# Afficher toutes les informations
timedatectl

# Sortie exemple :
               Local time: Tue 2026-02-11 14:35:42 CET
           Universal time: Tue 2026-02-11 13:35:42 UTC
                 RTC time: Tue 2026-02-11 13:35:42
                Time zone: Europe/Paris (CET, +0100)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

**Informations affichées :**

| Champ | Description |
|-------|-------------|
| **Local time** | Heure locale avec timezone appliquée |
| **Universal time** | Heure UTC |
| **RTC time** | Heure de l'horloge matérielle |
| **Time zone** | Timezone configurée (nom + offset) |
| **System clock synchronized** | Synchronisation NTP active ou non |
| **NTP service** | Service NTP actif (systemd-timesyncd, chrony...) |
| **RTC in local TZ** | Horloge matérielle en heure locale (no = UTC) |

#### Lister les Timezones Disponibles

```bash
# Lister tous les timezones
timedatectl list-timezones

# Sortie (extrait) :
# Africa/Abidjan
# Africa/Accra
# ...
# America/New_York
# ...
# Asia/Tokyo
# ...
# Europe/Paris
# Europe/London
# ...

# Filtrer par région
timedatectl list-timezones | grep Europe

# Filtrer par ville
timedatectl list-timezones | grep Paris
```

#### Configurer le Timezone

```bash
# Changer le timezone (root requis)
sudo timedatectl set-timezone Europe/Paris

# Vérifier le changement
timedatectl
# Time zone: Europe/Paris (CET, +0100)

# Autres exemples
sudo timedatectl set-timezone America/New_York
sudo timedatectl set-timezone Asia/Tokyo
sudo timedatectl set-timezone UTC

# ⚠️ Le nom doit être EXACT (respecter la casse)
sudo timedatectl set-timezone europe/paris
# Failed to set time zone: Invalid or not installed time zone 'europe/paris'
```

#### Configurer l'Horloge Matérielle

```bash
# Mettre RTC en UTC (recommandé)
sudo timedatectl set-local-rtc 0

# Mettre RTC en heure locale (dual-boot Windows)
sudo timedatectl set-local-rtc 1

# Vérifier
timedatectl | grep "RTC in local TZ"
# RTC in local TZ: no
```

**⚠️ Pourquoi UTC pour RTC ?**
- ✅ Évite les problèmes avec changement d'heure DST
- ✅ Cohérence multi-boot (Linux + BSD)
- ❌ Windows utilise heure locale par défaut (problème dual-boot)

#### Synchronisation NTP

```bash
# Activer la synchronisation NTP
sudo timedatectl set-ntp true

# Désactiver la synchronisation NTP
sudo timedatectl set-ntp false

# Vérifier le statut NTP
timedatectl show-timesync --all
```

### 🗂️ Configuration Système - /etc/localtime

**Le timezone système est défini par `/etc/localtime`.**

**📄 Format et Contenu du Fichier**

Le fichier **`/etc/localtime`** est au **format binaire** et contient les informations liées au fuseau horaire :

- **Décalage par rapport à l'heure UTC** (offset en secondes)
- **Règles pour le changement d'heure d'été ou d'hiver** (DST - Daylight Saving Time)
- **Transitions historiques** des changements d'heure
- **Abréviations** des fuseaux (CET, CEST, EST, EDT, etc.)

**⚠️ Fichier binaire :** Ne peut PAS être édité directement avec un éditeur de texte.

```bash
# Vérifier le type de fichier
file /etc/localtime
# /etc/localtime: timezone data, version 2, 8 gmt time flags, 8 std time flags, no leap seconds, 139 transition times, 8 abbreviation chars

# Tenter de lire (données binaires)
cat /etc/localtime
# TZif2^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@... (illisible)

# Plus d'infos avec zdump (afficher les transitions DST)
zdump -v /etc/localtime | head -20
# /etc/localtime  Sun Mar 25 00:59:59 1917 UT = Sun Mar 25 01:59:59 1917 CET isdst=0
# /etc/localtime  Sun Mar 25 01:00:00 1917 UT = Sun Mar 25 03:00:00 1917 CEST isdst=1
# /etc/localtime  Mon Oct  8 00:59:59 1917 UT = Mon Oct  8 02:59:59 1917 CEST isdst=1
# /etc/localtime  Mon Oct  8 01:00:00 1917 UT = Mon Oct  8 02:00:00 1917 CET isdst=0
# ...
```

**Mécanisme :**
```bash
# /etc/localtime est un lien symbolique vers un fichier timezone
ls -la /etc/localtime
# lrwxrwxrwx 1 root root 33 Feb 11 10:00 /etc/localtime -> /usr/share/zoneinfo/Europe/Paris
```

**Base de données des timezones - /usr/share/zoneinfo :**

Les fichiers de configuration des fuseaux horaires sont placés dans **`/usr/share/zoneinfo`**.

**`/etc/localtime` est une copie (ou un lien symbolique) du fichier correspondant au fuseau horaire.**

```bash
# Tous les timezones disponibles
ls /usr/share/zoneinfo/
# Africa/  America/  Antarctica/  Arctic/  Asia/  Atlantic/  Australia/  
# Europe/  Indian/  Pacific/  CET  CST6CDT  EET  EST  EST5EDT  GB  GMT  
# GMT+0  GMT-0  GMT0  Greenwich  HST  MET  MST  MST7MDT  NZ  NZ-CHAT  
# Poland  Portugal  PRC  PST8PDT  ROC  ROK  Singapore  Turkey  UCT  UTC  
# Universal  W-SU  WET  Zulu  iso3166.tab  posix/  right/  zone.tab

# Exemple de structure
ls /usr/share/zoneinfo/Europe/
# Amsterdam  Andorra  Athens  Belgrade  Berlin  Brussels  Bucharest  
# Budapest  Copenhagen  Dublin  Gibraltar  Helsinki  Istanbul  Kaliningrad  
# Kiev  Lisbon  London  Luxembourg  Madrid  Malta  Monaco  Moscow  Oslo  
# Paris  Prague  Riga  Rome  Samara  Sarajevo  Stockholm  Tallinn  Tirane  
# Vienna  Vilnius  Warsaw  Zurich

# Chaque fichier contient les règles DST pour cette ville/region
file /usr/share/zoneinfo/Europe/Paris
# /usr/share/zoneinfo/Europe/Paris: timezone data, version 2, 8 gmt time flags, 8 std time flags, no leap seconds, 139 transition times, 8 abbreviation chars
```

**📑 Fichiers spéciaux dans /usr/share/zoneinfo :**

- **`iso3166.tab`** : Codes pays ISO 3166
- **`zone.tab`** : Correspondance pays/timezones
- **`posix/`** : Timezones POSIX standard
- **`right/`** : Timezones avec secondes intercalaires (leap seconds)

#### Modifier le Timezone Manuellement

**Méthode 1 : Lien symbolique (manuelle)**
```bash
# Supprimer l'ancien lien
sudo rm /etc/localtime

# Créer le nouveau lien
sudo ln -s /usr/share/zoneinfo/Asia/Tokyo /etc/localtime

# Vérifier
ls -la /etc/localtime
date
# Tue Feb 11 22:35:42 JST 2026
```

**Méthode 2 : timedatectl (recommandée)**
```bash
# Plus simple et sûr
sudo timedatectl set-timezone Asia/Tokyo
```

**📄 Fichier /etc/timezone (Debian/Ubuntu)**

Sur les distributions **Debian** et **Ubuntu**, le fichier **`/etc/timezone`** contient le **nom du fuseau horaire** au format texte simple.

**Format :** `Continent/Ville`

```bash
# Afficher le contenu (nom du timezone)
cat /etc/timezone
# Europe/Paris

# Format standard : Continent/Ville
# Exemples valides :
# Europe/Paris
# America/New_York
# Asia/Tokyo
# Australia/Sydney
# Africa/Cairo
# UTC

# ⚠️ Ce fichier contient UNIQUEMENT le nom, pas les règles DST
# Les règles sont dans /etc/localtime (format binaire)
```

**Relation /etc/timezone ↔ /etc/localtime :**

```
/etc/timezone          →  Nom lisible (texte)     →  "Europe/Paris"
                          ↓
/etc/localtime         →  Données binaires (DST)  →  Lien vers /usr/share/zoneinfo/Europe/Paris
```

**Modification manuelle :**

```bash
# Méthode 1 : Modification manuelle (Debian/Ubuntu)
# 1. Mettre à jour /etc/localtime
sudo rm /etc/localtime
sudo ln -s /usr/share/zoneinfo/Asia/Tokyo /etc/localtime

# 2. Mettre à jour /etc/timezone
echo "Asia/Tokyo" | sudo tee /etc/timezone
# Asia/Tokyo

# Vérifier
cat /etc/timezone
# Asia/Tokyo

date
# Tue Feb 11 22:35:42 JST 2026

# Méthode 2 : Utiliser timedatectl (met à jour les deux automatiquement)
sudo timedatectl set-timezone Asia/Tokyo
# Met à jour /etc/localtime ET /etc/timezone automatiquement
```

**⚠️ Note importante :**

- **Red Hat/CentOS** n'utilisent PAS `/etc/timezone`
- Ils utilisent uniquement `/etc/localtime`
- Utilisez `timedatectl` pour la portabilité entre distributions

### 🔧 Variable d'Environnement TZ

**La variable `TZ` permet de définir un timezone temporaire** pour l'utilisateur ou une commande.

**Priorité :**
```
TZ (variable d'environnement) > /etc/localtime (système)
```

#### Utilisation Temporaire

```bash
# Afficher la date avec timezone par défaut
date
# Tue Feb 11 14:35:42 CET 2026

# Définir TZ pour une commande spécifique
env TZ='Asia/Tokyo' date
# Tue Feb 11 22:35:42 JST 2026

# Autre syntaxe (équivalent)
TZ='America/New_York' date
# Tue Feb 11 08:35:42 EST 2026

# Appliquer à plusieurs commandes
TZ='UTC' date
# Tue Feb 11 13:35:42 UTC 2026
TZ='UTC' ls -la /tmp
# (ls s'exécute avec TZ=UTC pour timestamps)
```

**Exemples pratiques :**

```bash
# Comparer plusieurs timezones
echo "Paris    : $(TZ='Europe/Paris' date)"
echo "New York : $(TZ='America/New_York' date)"
echo "Tokyo    : $(TZ='Asia/Tokyo' date)"
echo "Sydney   : $(TZ='Australia/Sydney' date)"

# Sortie :
# Paris    : Tue Feb 11 14:35:42 CET 2026
# New York : Tue Feb 11 08:35:42 EST 2026
# Tokyo    : Tue Feb 11 22:35:42 JST 2026
# Sydney   : Wed Feb 12 00:35:42 AEDT 2026

# Logs avec timezone spécifique
TZ='UTC' tail -f /var/log/syslog
```

#### Export Permanent (Session Shell)

```bash
# Exporter TZ pour le shell courant et ses enfants
export TZ='Asia/Tokyo'

# Vérifier
date
# Tue Feb 11 22:35:42 JST 2026

echo $TZ
# Asia/Tokyo

# La variable reste active dans les sous-shells
bash -c 'date'
# Tue Feb 11 22:35:42 JST 2026
```

#### Configuration Permanente (Utilisateur)

**Fichiers de profil :**

| Fichier | Scope | Quand Chargé |
|---------|-------|--------------|
| `~/.profile` | Utilisateur | Login shells (sh, bash, dash) |
| `~/.bash_profile` | Utilisateur | Login shells bash uniquement |
| `~/.bashrc` | Utilisateur | Shells interactifs non-login |
| `/etc/profile` | Système | Login shells (tous utilisateurs) |
| `/etc/bash.bashrc` | Système | Shells interactifs bash (tous) |

**Ajouter TZ permanent pour l'utilisateur :**

```bash
# Éditer ~/.profile
vim ~/.profile

# Ajouter à la fin
export TZ='Asia/Tokyo'

# Recharger la configuration
source ~/.profile

# Ou redémarrer le shell
exit
# (se reconnecter)

# Vérifier
echo $TZ
# Asia/Tokyo
```

**Pour tous les utilisateurs (système) :**

```bash
# Éditer /etc/profile (root requis)
sudo vim /etc/profile

# Ajouter
export TZ='UTC'

# Tous les utilisateurs auront TZ=UTC par défaut
```

**⚠️ Différence avec /etc/localtime :**
- `/etc/localtime` : Timezone SYSTÈME (toutes commandes, services, cron...)
- `TZ` : Timezone UTILISATEUR (session shell uniquement)

### 🔍 tzselect - Sélection Interactive (Ancienne Méthode)

**`tzselect`** : Outil interactif pour déterminer le timezone approprié.

**⚠️ Important : `tzselect` NE CHANGE PAS le timezone système !**  
Elle affiche seulement la valeur `TZ` à utiliser.

```bash
# Lancer tzselect
tzselect

# Script interactif :
# Please identify a location so that time zone rules can be set correctly.
# Please select a continent, ocean, "coord", or "TZ".
#  1) Africa
#  2) Americas
#  3) Antarctica
#  4) Asia
#  5) Atlantic Ocean
#  6) Australia
#  7) Europe
#  8) Indian Ocean
#  9) Pacific Ocean
# 10) coord - I want to use geographical coordinates.
# 11) TZ - I want to specify the timezone using a Posix.1 TZ format.
# #? 7   (sélectionner Europe)

# Please select a country whose clocks agree with yours.
#  1) Andorra       15) Greece         29) Poland
#  2) Austria       16) Guernsey       30) Portugal
#  ...
# 18) France        ...
# #? 18   (sélectionner France)

# The following information has been given:
#         France
# Therefore TZ='Europe/Paris' will be used.
# Selected time is now:   Tue Feb 11 14:35:42 CET 2026.
# Is the above information OK?
# 1) Yes
# 2) No
# #? 1

# Sortie finale :
# You can make this change permanent for yourself by appending the line
#         TZ='Europe/Paris'; export TZ
# to the file '.profile' in your home directory; then log out and log in again.
```

**Appliquer la recommandation :**

```bash
# Ajouter à ~/.profile
echo "TZ='Europe/Paris'; export TZ" >> ~/.profile

# Ou export temporaire
export TZ='Europe/Paris'
```

### 🗑️ tzconfig - Obsolète

**`tzconfig`** : Ancien outil Debian pour configurer le timezone.

**❌ Non supporté sur les systèmes modernes.**

**Remplacé par :**
- `timedatectl` (systemd)
- `dpkg-reconfigure tzdata` (Debian/Ubuntu)

```bash
# ❌ tzconfig n'existe plus
tzconfig
# bash: tzconfig: command not found

# ✅ Utiliser à la place
sudo dpkg-reconfigure tzdata    # Debian/Ubuntu
sudo timedatectl set-timezone Europe/Paris
```

### 🔧 dpkg-reconfigure tzdata - Configuration Interactive Timezone

**Pour reconfigurer le fuseau horaire (timezone), utilisez les commandes suivantes selon votre distribution :**

| Distribution | Commande Principale | Commande Alternative |
|--------------|---------------------|----------------------|
| **Debian/Ubuntu** | `dpkg-reconfigure tzdata` | `timedatectl` |
| **Red Hat/CentOS** | `timedatectl` | - |

**dpkg-reconfigure tzdata** : Outil interactif pour configurer le fuseau horaire sur Debian et Ubuntu.

```bash
# Lancer la reconfiguration du timezone (root)
sudo dpkg-reconfigure tzdata

# ┌──────────────┤ Configuring tzdata ├───────────────┐
# │ Please select the geographic area in which you    │
# │ live. Subsequent configuration questions will     │
# │ narrow this down by presenting a list of cities,  │
# │ representing the time zones in which they are     │
# │ located.                                           │
# │                                                     │
# │        Africa                                      │
# │        America                                     │
# │        Antarctica                                  │
# │        Australia                                   │
# │        Arctic                                      │
# │        Asia                                        │
# │   →    Europe                          ←           │  
# │        Atlantic                                    │
# │        Indian                                      │
# │        Pacific                                     │
# │        US                                          │
# │        Etc                                         │
# │                                                     │
# │           <Ok>               <Cancel>              │
# └─────────────────────────────────────────────────────┘

# Après sélection de "Europe" :
# ┌──────────────┤ Configuring tzdata ├───────────────┐
# │ Please select the city or region corresponding    │
# │ to your time zone.                                 │
# │                                                     │
# │        Amsterdam                                   │
# │        Andorra                                     │
# │        Athens                                      │
# │        Belgrade                                    │
# │        Berlin                                      │
# │        Brussels                                    │
# │   →    Paris                           ←           │
# │        Prague                                      │
# │        Rome                                        │
# │        Stockholm                                   │
# │        Vienna                                      │
# │        Warsaw                                      │
# │                                                     │
# │           <Ok>               <Cancel>              │
# └─────────────────────────────────────────────────────┘

# Après sélection valide :
# Current default time zone: 'Europe/Paris'
# Local time is now:      Tue Feb 11 14:35:42 CET 2026.
# Universal Time is now:  Tue Feb 11 13:35:42 UTC 2026.
```

**Ce que fait dpkg-reconfigure tzdata :**

1. **Met à jour `/etc/timezone`** avec le nom du fuseau horaire
2. **Met à jour `/etc/localtime`** (copie ou lien symbolique)
3. **Affiche l'heure locale et UTC** pour confirmation

**Vérification après configuration :**

```bash
# Vérifier /etc/timezone
cat /etc/timezone
# Europe/Paris

# Vérifier /etc/localtime (lien symbolique ou copie)
ls -la /etc/localtime
# lrwxrwxrwx 1 root root 33 Feb 11 14:35 /etc/localtime -> /usr/share/zoneinfo/Europe/Paris

# Vérifier avec timedatectl
timedatectl
#       Local time: Tue 2026-02-11 14:35:42 CET
#   Universal time: Tue 2026-02-11 13:35:42 UTC
#         Time zone: Europe/Paris (CET, +0100)

# Vérifier avec date
date
# Tue Feb 11 14:35:42 CET 2026
```

**💡 Note importante :**

Le fuseau horaire se détermine sous la forme **Continent/Ville**.

```
Format : Continent/Ville

Exemples :
- Europe/Paris
- America/New_York
- Asia/Tokyo
- Australia/Sydney
- Africa/Cairo
```

---

## 🗣️ LOCALE - Conventions Régionales

### 🌍 Principe des Locales

**Locale** : Ensemble de paramètres définissant les conventions culturelles et linguistiques :
- Langue d'affichage
- Format des dates
- Format des nombres (virgule vs point décimal)
- Symbole monétaire
- Format des adresses/téléphones
- Ordre de tri (collation)

**Architecture :**
```
┌────────────────────────────────────────────────────────┐
│  Locale = Langue + Région + Encodage                  │
│                                                        │
│  Exemple : en_US.UTF-8                                 │
│            │   │   └─ Encodage (UTF-8)                 │
│            │   └───── Région (United States)           │
│            └───────── Langue (English)                 │
└────────────────────────────────────────────────────────┘
```

### 📊 Variables de Locale

**Commande `locale`** : Affiche les variables de locale actives.

```bash
# Afficher toutes les variables de locale
locale

# Sortie exemple :
LANG=en_US.UTF-8
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=
```

**📂 Fichiers d'Initialisation des Locales**

Les paramètres déterminant le pays, la langue ou la monnaie utilisés sont positionnés dans différentes **variables d'environnement du shell**. La principale se nomme **LANG**.

**Fichiers de configuration selon les distributions :**

| Distribution | Fichier de Configuration | Exemple |
|--------------|--------------------------|---------|
| **Debian/Ubuntu** | `/etc/default/locale` | `LANG="en_US.UTF-8"` |
| **Red Hat/CentOS** | `/etc/locale.conf` | `LANG=en_US.UTF-8` |
| **SuSE** | `/etc/sysconfig/language` | `RC_LANG="en_US.UTF-8"` |

**Exemples pratiques :**

```bash
# Debian/Ubuntu
cat /etc/default/locale
# LANG="en_US.UTF-8"
# LANGUAGE="en_US:en"

# Red Hat/CentOS/Fedora
cat /etc/locale.conf
# LANG=en_US.UTF-8

# SuSE/openSUSE
cat /etc/sysconfig/language
# RC_LANG="en_US.UTF-8"
# RC_LC_MESSAGES="en_US.UTF-8"
```

**📁 Support Système - /usr/share/locale**

Les fichiers de traduction et support des locales sont stockés dans **`/usr/share/locale`**.

```bash
# Lister les locales supportées par le système
ls /usr/share/locale/

# Sortie exemple :
# af  am  ar  as  ast  az  be  bg  bn  bn_IN  br  bs  ca  cs  cy  da  de  
# el  en_AU  en_CA  en_GB  en_US  eo  es  et  eu  fa  fi  fr  ga  gl  gu  
# he  hi  hr  hu  hy  id  is  it  ja  ka  kk  km  kn  ko  ku  ky  lt  lv  
# mk  ml  mn  mr  ms  nb  nds  ne  nl  nn  oc  or  pa  pl  pt  pt_BR  ro  
# ru  si  sk  sl  sq  sr  sv  ta  te  th  tr  uk  ur  vi  zh_CN  zh_TW

# Voir les fichiers pour une locale spécifique (exemple : français)
ls /usr/share/locale/fr/
# LC_MESSAGES/

ls /usr/share/locale/fr/LC_MESSAGES/
# apt.mo  bash.mo  coreutils.mo  dpkg.mo  grep.mo  sed.mo  tar.mo  ...

# Fichiers .mo : Messages Object (traductions compilées)
```

**Description des variables :**

| Variable | Fonction | Exemple d'Impact |
|----------|----------|------------------|
| **LANG** | Locale par défaut (fallback) | Langue générale si LC_* non défini |
| **LC_CTYPE** | Classe des caractères et conversion | Majuscules/minuscules, types de caractères |
| **LC_NUMERIC** | Format numérique par défaut (autre que monnaie) | `1,234.56` (US) vs `1 234,56` (FR) |
| **LC_TIME** | Format par défaut de la date et de l'heure | `02/11/2026` vs `11/02/2026` |
| **LC_COLLATE** | Règles de comparaison et de tri | Ordre alphabétique (ex: é après e ou à la fin), caractères accentués |
| **LC_MONETARY** | Format monétaire | `$1,234.56` vs `1 234,56 €` |
| **LC_MESSAGES** | Format des messages informatifs, interactifs et de diagnostic | Langue des messages d'erreur/aide |
| **LC_PAPER** | Format de papier par défaut | Letter (US) vs A4 (Europe) |
| **LC_NAME** | Format du nom d'une personne | Prénom Nom vs Nom Prénom |
| **LC_ADDRESS** | Format d'une adresse | Ordre des champs |
| **LC_TELEPHONE** | Format d'un téléphone | `(555) 123-4567` vs `05 55 12 34 67` |
| **LC_MEASUREMENT** | Système de mesure | Impérial vs Métrique (métrique en France) |
| **LC_IDENTIFICATION** | Métadonnées supplémentaires concernant la spécification locale | Informations sur la locale |
| **LC_ALL** | **Règles pour toutes les autres variables LC** | Force une locale pour tout (override) |

**Hiérarchie des variables :**
```
LC_ALL  >  LC_*  >  LANG

LC_ALL si définie → ignore tout le reste
Sinon LC_CATEGORY spécifique (ex: LC_TIME)
Sinon LANG (valeur par défaut)
```

### 🔤 Format des Locales

**Structure :**
```
langue_PAYS.ENCODAGE

Exemples :
en_US.UTF-8     English - United States - UTF-8
fr_FR.UTF-8     French - France - UTF-8
de_DE.UTF-8     German - Germany - UTF-8
es_ES.UTF-8     Spanish - Spain - UTF-8
ja_JP.UTF-8     Japanese - Japan - UTF-8
zh_CN.UTF-8     Chinese - China - UTF-8
```

**Composants :**

| Partie | Description | Exemples |
|--------|-------------|----------|
| **langue** | Code ISO 639 (2 lettres) | `en`, `fr`, `de`, `es`, `ja`, `zh` |
| **PAYS** | Code ISO 3166 (2 lettres) | `US`, `FR`, `DE`, `ES`, `JP`, `CN` |
| **ENCODAGE** | Table de caractères | `UTF-8`, `ISO-8859-1`, `ASCII` |

**Variantes régionales :**
```bash
en_US.UTF-8    # Anglais américain
en_GB.UTF-8    # Anglais britannique
en_CA.UTF-8    # Anglais canadien
en_AU.UTF-8    # Anglais australien

fr_FR.UTF-8    # Français France
fr_BE.UTF-8    # Français Belgique
fr_CA.UTF-8    # Français Canada

es_ES.UTF-8    # Espagnol Espagne
es_MX.UTF-8    # Espagnol Mexique
```

### 🔍 Locale "C" - Le Standard de Programmation

**`LANG=C`** ou **`LC_ALL=C`** : Locale "POSIX" ou "C".

**Caractéristiques :**
- ✅ Basée sur le langage de programmation C
- ✅ Proche de l'anglais (mais minimaliste)
- ✅ Comportement prévisible et cohérent
- ✅ Pas de traductions (messages en anglais)
- ✅ ASCII pur (7 bits)
- ✅ Tri simple (ordre ASCII)

**Cas d'usage :**

```bash
# 1. Scripting - Comportement prévisible
LC_ALL=C sort fichier.txt
# Tri strictement en ordre ASCII (A-Z puis a-z)

# vs locale avec accents
LC_ALL=fr_FR.UTF-8 sort fichier.txt
# Tri alphabétique français (é traité comme e)

# 2. Parsing de commandes - Format fixe
LC_ALL=C df -h
# Colonnes en anglais : "Filesystem  Size  Used  Avail  Use%  Mounted on"

# vs locale française
LC_ALL=fr_FR.UTF-8 df -h
# Colonnes traduites (problème pour scripts)

# 3. Debugging - Messages en anglais original
LC_ALL=C apt update
# Messages d'erreur en anglais (plus facile à chercher sur internet)

# 4. Performance - Pas de traduction
LC_ALL=C grep pattern fichier_énorme.txt
# Légèrement plus rapide (pas de gestion accents)
```

**Exemple concret :**

```bash
# Avec locale française
LANG=fr_FR.UTF-8 ls -l /nonexistent
# ls: impossible d'accéder à '/nonexistent': Aucun fichier ou dossier de ce type

# Avec locale C
LANG=C ls -l /nonexistent
# ls: cannot access '/nonexistent': No such file or directory
```

**⚠️ Dans les scripts :**
```bash
#!/bin/bash
# Forcer locale C pour comportement prévisible
export LC_ALL=C

# Tri, regex, parsing sont maintenant cohérents
sort fichier.txt
```

### 📋 Lister les Locales Disponibles

```bash
# Lister toutes les locales disponibles sur le système
locale -a

# Sortie exemple :
# C
# C.UTF-8
# en_US.utf8
# fr_FR.utf8
# POSIX

# Chercher une locale spécifique
locale -a | grep fr
# fr_BE.utf8
# fr_CA.utf8
# fr_CH.utf8
# fr_FR.utf8

locale -a | grep en_US
# en_US.utf8
```

### ⚙️ Configuration des Locales

#### Modifier Temporairement

```bash
# Changer LANG pour le shell courant
export LANG=fr_FR.UTF-8

# Vérifier
locale
# LANG=fr_FR.UTF-8
# LC_CTYPE="fr_FR.UTF-8"
# ...

# Changer une variable spécifique
export LC_TIME=en_GB.UTF-8

# Forcer une locale pour TOUT (override)
export LC_ALL=de_DE.UTF-8

# Tester l'impact
date
# Di 11. Feb 14:35:42 CET 2026   (Allemand)

# Exécuter une commande avec locale spécifique
LANG=es_ES.UTF-8 date
# mar 11 feb 2026 14:35:42 CET   (Espagnol)
```

#### Configuration Permanente - /etc/locale.conf

**Fichier `/etc/locale.conf`** : Configuration système des locales (systemd).

```bash
# Voir la configuration actuelle
cat /etc/locale.conf
# LANG=en_US.UTF-8

# Éditer manuellement (root)
sudo vim /etc/locale.conf

# Exemple de contenu
LANG=fr_FR.UTF-8
LC_TIME=en_GB.UTF-8
LC_MONETARY=fr_FR.UTF-8

# ⚠️ Redémarrage ou relogin nécessaire
```

**⚠️ Fichier alternatif (Debian/Ubuntu) : `/etc/default/locale`**
```bash
cat /etc/default/locale
# LANG="en_US.UTF-8"
# LANGUAGE="en_US:en"
```

#### localectl - Configuration via systemd

**`localectl`** : Outil systemd pour gérer les locales.

```bash
# Afficher la configuration actuelle
localectl

# Sortie :
   System Locale: LANG=en_US.UTF-8
       VC Keymap: us
      X11 Layout: us

# Lister les locales disponibles
localectl list-locales
# C.UTF-8
# en_US.utf8
# fr_FR.utf8
# ...

# Changer la locale système (root)
sudo localectl set-locale LANG=fr_FR.UTF-8

# Vérifier
localectl
# System Locale: LANG=fr_FR.UTF-8

cat /etc/locale.conf
# LANG=fr_FR.UTF-8

# Changer plusieurs variables
sudo localectl set-locale LANG=en_US.UTF-8 LC_TIME=fr_FR.UTF-8

# ⚠️ Prend effet au prochain login
# Pour appliquer immédiatement :
source /etc/locale.conf
export LANG LC_TIME
```

#### dpkg-reconfigure locales (Debian/Ubuntu)

**Debian/Ubuntu** : Outil interactif pour activer et configurer les locales.

```bash
# Lancer la reconfiguration (root)
sudo dpkg-reconfigure locales

# Interface interactive :
# 1. Sélectionner les locales à GÉNÉRER (Espace pour cocher)
#    [*] en_US.UTF-8 UTF-8
#    [ ] en_GB.UTF-8 UTF-8
#    [*] fr_FR.UTF-8 UTF-8
#    [ ] de_DE.UTF-8 UTF-8
# 
# 2. Sélectionner la locale PAR DÉFAUT
#    Default locale for the system environment:
#      1. None
#      2. en_US.UTF-8
#      3. fr_FR.UTF-8
#    → Choisir 2 ou 3

# Les locales sélectionnées sont générées
# Generating locales (this might take a while)...
#   en_US.UTF-8... done
#   fr_FR.UTF-8... done
# Generation complete.

# Vérifier
locale -a
# C
# C.UTF-8
# en_US.utf8
# fr_FR.utf8
# POSIX
```

**Fichier de configuration : `/etc/locale.gen`**
```bash
# Voir les locales à générer
cat /etc/locale.gen

# Décommenter les locales voulues
sudo vim /etc/locale.gen

# Exemple :
# en_US.UTF-8 UTF-8
# fr_FR.UTF-8 UTF-8
# de_DE.UTF-8 UTF-8

# Régénérer les locales
sudo locale-gen

# Generating locales (this might take a while)...
#   en_US.UTF-8... done
#   fr_FR.UTF-8... done
#   de_DE.UTF-8... done
# Generation complete.
```

### 🧪 Exemples Pratiques d'Impact des Locales

#### Format des Dates

```bash
# Anglais US
LC_TIME=en_US.UTF-8 date
# Tue Feb 11 02:35:42 PM CET 2026

# Français
LC_TIME=fr_FR.UTF-8 date
# mar. 11 févr. 2026 14:35:42 CET

# Allemand
LC_TIME=de_DE.UTF-8 date
# Di 11. Feb 14:35:42 CET 2026

# Japonais
LC_TIME=ja_JP.UTF-8 date
# 2026年 2月11日 火曜日 14:35:42 CET
```

#### Format des Nombres

```bash
# Anglais US (point décimal)
LC_NUMERIC=en_US.UTF-8 printf "%'d\n" 1234567
# 1,234,567

# Français (virgule décimale, espace milliers)
LC_NUMERIC=fr_FR.UTF-8 printf "%'d\n" 1234567
# 1 234 567

# Allemand (point milliers, virgule décimale)
LC_NUMERIC=de_DE.UTF-8 printf "%'d\n" 1234567
# 1.234.567
```

#### Messages Système

```bash
# Anglais
LC_MESSAGES=en_US.UTF-8 ls /nonexistent
# ls: cannot access '/nonexistent': No such file or directory

# Français
LC_MESSAGES=fr_FR.UTF-8 ls /nonexistent
# ls: impossible d'accéder à '/nonexistent': Aucun fichier ou dossier de ce type

# Allemand
LC_MESSAGES=de_DE.UTF-8 ls /nonexistent
# ls: Zugriff auf '/nonexistent' nicht möglich: Datei oder Verzeichnis nicht gefunden
```

#### Tri (Collation)

```bash
# Créer fichier test
cat > test_sort.txt <<EOF
zoo
église
éléphant
apple
École
EOF

# Tri ASCII strict (C)
LC_COLLATE=C sort test_sort.txt
# École
# apple
# zoo
# église
# éléphant
# (Majuscules avant minuscules, accents à la fin)

# Tri alphabétique français
LC_COLLATE=fr_FR.UTF-8 sort test_sort.txt
# apple
# École
# église
# éléphant
# zoo
# (é traité comme e, ordre alphabétique naturel)
```

---

## 🔡 ENCODAGE DES CARACTÈRES

### 📚 Histoire et Évolution

**Encodage** : Représentation numérique des caractères.

```
┌──────────────────────────────────────────────────────┐
│  Évolution des Encodages                             │
├──────────────────────────────────────────────────────┤
│  1. ASCII (1963)                                     │
│     → 128 caractères, 7 bits                         │
│     → Anglais uniquement                             │
│                                                      │
│  2. ISO-8859 (1987)                                  │
│     → Extensions ASCII, 8 bits (256 caractères)      │
│     → ISO-8859-1 (Latin-1) : Europe Ouest            │
│     → ISO-8859-15 : Latin-1 + € et œ                 │
│     → ISO-8859-X : Variantes (grec, cyrillique...)   │
│                                                      │
│  3. UTF-8 (1993)                                     │
│     → Encodage universel (Unicode)                   │
│     → 1-4 octets par caractère                       │
│     → Rétrocompatible ASCII                          │
│     → Standard actuel                                │
└──────────────────────────────────────────────────────┘
```

### 🔤 ASCII - American Standard Code for Information Interchange

**Caractéristiques :**
- **128 caractères** (0-127)
- **7 bits** par caractère
- Créé en 1963 pour le télégraphe
- Anglais uniquement

**Contenu :**
- 0-31 : Caractères de contrôle (non imprimables)
- 32 : Espace
- 33-126 : Caractères imprimables (A-Z, a-z, 0-9, ponctuation)
- 127 : DEL (delete)

**Exemple :**
```
Caractère  Décimal  Binaire     Hexadécimal
   A         65     01000001       0x41
   a         97     01100001       0x61
   0         48     00110000       0x30
   Space     32     00100000       0x20
```

**Limitation :** Pas d'accents (é, à, ö...), pas de symboles spéciaux (€, ©...).

### 🌍 ISO-8859 - Extensions ASCII Régionales

**Caractéristiques :**
- **256 caractères** (0-255)
- **8 bits** par caractère
- 0-127 : Identique à ASCII
- 128-255 : Caractères additionnels (régionaux)

**Variantes principales :**

| Encodage | Nom | Région/Langues |
|----------|-----|----------------|
| **ISO-8859-1** | Latin-1 | Europe Ouest (français, allemand, espagnol...) |
| **ISO-8859-2** | Latin-2 | Europe Centrale (polonais, tchèque...) |
| **ISO-8859-3** | Latin-3 | Europe Sud (turc, maltais...) |
| **ISO-8859-5** | Cyrillique | Russe, bulgare, serbe... |
| **ISO-8859-6** | Arabe | Langues arabes |
| **ISO-8859-7** | Grec | Grec moderne |
| **ISO-8859-8** | Hébreu | Hébreu |
| **ISO-8859-9** | Latin-5 | Turc |
| **ISO-8859-11** | Thaï | Thaï |
| **ISO-8859-15** | Latin-9 | Latin-1 + Euro (€) + œ |

**Problème :** Un fichier ne peut utiliser qu'UN seul encodage ISO-8859-X.  
→ Impossible de mélanger français + russe dans le même document.

### 🌐 UTF-8 - Universal Character Set Transformation Format 8-bit

**Caractéristiques :**
- **Encodage universel** (tous les caractères du monde)
- **Longueur variable** : 1 à 4 octets par caractère
- **Rétrocompatible ASCII** (caractères ASCII = 1 octet identique)
- **Standard actuel** (web, Linux, macOS...)

**Avantages :**
- ✅ Tous les alphabets (latin, cyrillique, arabe, chinois, japonais...)
- ✅ Symboles et emojis (©, €, 😊, 🚀...)
- ✅ Compatible ASCII (économie d'espace pour texte anglais)
- ✅ Interopérabilité maximale

**Structure :**

| Plage Unicode | Octets UTF-8 | Exemple |
|---------------|--------------|---------|
| U+0000 - U+007F | 1 octet | A (ASCII) |
| U+0080 - U+07FF | 2 octets | é, à, ñ |
| U+0800 - U+FFFF | 3 octets | 中, 日, €, ✓ |
| U+10000 - U+10FFFF | 4 octets | 😊, 🚀, 𝕳 |

**Exemples d'encodage :**

```
Caractère  Unicode   UTF-8 (hex)        Nombre octets
   A       U+0041    41                 1
   é       U+00E9    C3 A9              2
   €       U+20AC    E2 82 AC           3
   😊      U+1F60A   F0 9F 98 8A        4
```

**⚠️ UTF-8 vs UTF-16 vs UTF-32 :**
- **UTF-8** : Longueur variable (1-4 octets), compatible ASCII → **Standard Linux/Web**
- **UTF-16** : Longueur variable (2 ou 4 octets) → Windows, Java
- **UTF-32** : Longueur fixe (4 octets) → Rare (gaspillage mémoire)

### 🔍 Vérifier l'Encodage d'un Fichier

```bash
# Commande file (détecte encodage)
file fichier.txt
# fichier.txt: UTF-8 Unicode text

file ancien_fichier.txt
# ancien_fichier.txt: ISO-8859 text

# Commande chardet (plus précis, nécessite installation)
chardet fichier.txt
# fichier.txt: utf-8 with confidence 0.99
```

---

## 🔄 ICONV - Conversion d'Encodage

### 🛠️ Principe

**`iconv`** : Convertit des fichiers d'un encodage vers un autre.

**Syntaxe :**
```bash
iconv [OPTIONS] -f ENCODAGE_SOURCE -t ENCODAGE_CIBLE fichier
```

**Options principales :**

| Option | Description | Exemple |
|--------|-------------|---------|
| `-f FROM` | Encodage source (FROM) | `-f ISO-8859-1` |
| `-t TO` | Encodage cible (TO) | `-t UTF-8` |
| `-l` | Lister tous les encodages supportés | `iconv -l` |
| `-o FILE` | Fichier de sortie | `-o output.txt` |
| `-c` | Ignorer les caractères invalides | `-c` |
| `--verbose` | Mode verbeux | `--verbose` |

### 📋 Lister les Encodages Supportés

```bash
# Lister tous les encodages
iconv -l

# Sortie (extrait) :
# UTF-8
# UTF-16
# UTF-32
# ISO-8859-1
# ISO-8859-15
# WINDOWS-1252
# ASCII
# ...

# Filtrer pour encodage spécifique
iconv -l | grep -i iso-8859
# ISO-8859-1
# ISO-8859-2
# ISO-8859-3
# ...

iconv -l | grep -i windows
# WINDOWS-1250
# WINDOWS-1251
# WINDOWS-1252
# ...
```

### ✏️ Conversions Pratiques

#### Exemple 1 : ISO-8859-1 vers UTF-8

```bash
# Fichier en ISO-8859-1 (Latin-1)
file ancien.txt
# ancien.txt: ISO-8859 text

# Convertir vers UTF-8
iconv -f ISO-8859-1 -t UTF-8 ancien.txt > nouveau.txt

# Vérifier
file nouveau.txt
# nouveau.txt: UTF-8 Unicode text

# Ou écrire directement dans le fichier de sortie
iconv -f ISO-8859-1 -t UTF-8 ancien.txt -o nouveau.txt
```

#### Exemple 2 : Windows-1252 vers UTF-8

```bash
# Fichier créé sous Windows (CP1252)
file document_windows.txt
# document_windows.txt: Non-ISO extended-ASCII text

# Convertir
iconv -f WINDOWS-1252 -t UTF-8 document_windows.txt -o document_linux.txt

# Vérifier
file document_linux.txt
# document_linux.txt: UTF-8 Unicode text
```

#### Exemple 3 : UTF-8 vers ISO-8859-15

```bash
# Convertir UTF-8 vers ISO-8859-15 (pour ancien système)
iconv -f UTF-8 -t ISO-8859-15 moderne.txt -o ancien.txt

# ⚠️ Caractères non supportés par ISO-8859-15 seront perdus !
# (ex: emojis, caractères chinois...)

# Utiliser -c pour ignorer les erreurs
iconv -f UTF-8 -t ISO-8859-15 -c moderne.txt -o ancien.txt
```

#### Exemple 4 : Conversion en Place (Overwrite)

```bash
# ⚠️ iconv ne supporte PAS la modification en place directe

# ❌ ERREUR
iconv -f ISO-8859-1 -t UTF-8 fichier.txt > fichier.txt
# Résultat : fichier vide (stdout écrit avant lecture)

# ✅ CORRECT : Utiliser fichier temporaire
iconv -f ISO-8859-1 -t UTF-8 fichier.txt > fichier_temp.txt
mv fichier_temp.txt fichier.txt

# ✅ Ou sponge (moreutils)
iconv -f ISO-8859-1 -t UTF-8 fichier.txt | sponge fichier.txt
```

#### Exemple 5 : Conversion Batch (Plusieurs Fichiers)

```bash
# Convertir tous les .txt d'un répertoire
for file in *.txt; do
    iconv -f ISO-8859-1 -t UTF-8 "$file" > "${file%.txt}_utf8.txt"
done

# Ou en place avec boucle
for file in *.txt; do
    iconv -f ISO-8859-1 -t UTF-8 "$file" > temp.txt
    mv temp.txt "$file"
done

# Ou avec find
find . -name "*.txt" -exec bash -c 'iconv -f ISO-8859-1 -t UTF-8 "$0" > "$0.new" && mv "$0.new" "$0"' {} \;
```

### 🔧 Gestion des Erreurs

**Problème : Caractère illégal dans encodage cible**

```bash
# Fichier UTF-8 avec emoji
echo "Hello 😊 World" > test.txt

# Convertir vers ISO-8859-1 (ne supporte pas emoji)
iconv -f UTF-8 -t ISO-8859-1 test.txt
# iconv: illegal input sequence at position 6

# Solution 1 : Ignorer caractères invalides (-c)
iconv -f UTF-8 -t ISO-8859-1 -c test.txt
# Hello  World   (emoji supprimé)

# Solution 2 : Remplacer par ? (//TRANSLIT)
iconv -f UTF-8 -t ISO-8859-1//TRANSLIT test.txt
# Hello ? World

# Solution 3 : Ignorer complètement (//IGNORE)
iconv -f UTF-8 -t ISO-8859-1//IGNORE test.txt
# Hello  World
```

**Options avancées :**

| Suffixe | Description | Exemple |
|---------|-------------|---------|
| `//TRANSLIT` | Translittération (remplace caractères proches) | `UTF-8//TRANSLIT` |
| `//IGNORE` | Ignore caractères non convertibles | `ISO-8859-1//IGNORE` |
| `//TRANSLIT//IGNORE` | Combinaison | `UTF-8//TRANSLIT//IGNORE` |

**Exemple translittération :**
```bash
# Fichier avec accents
echo "Café résumé" > test.txt

# Conversion vers ASCII (pas d'accents)
iconv -f UTF-8 -t ASCII//TRANSLIT test.txt
# Cafe resume   (é → e)
```

---

## 🎓 Scénarios Pratiques

### Scénario 1 : Serveur Multi-Régions

**Besoin :** Serveur web pour utilisateurs français, affichage dates en français, mais logs en anglais (parsing).

```bash
# Configuration système en français
sudo localectl set-locale LANG=fr_FR.UTF-8

# Dans script de log (forcer anglais)
#!/bin/bash
export LC_ALL=C
echo "$(date) - Server started" >> /var/log/myapp.log
# Tue Feb 11 14:35:42 UTC 2026 - Server started
```

### Scénario 2 : Migration Ancien Système

**Besoin :** Importer fichiers CSV encodés ISO-8859-1 (Windows) vers base UTF-8.

```bash
# Convertir tous les CSV
for csv in /import/*.csv; do
    iconv -f WINDOWS-1252 -t UTF-8 "$csv" > "/tmp/$(basename "$csv")"
done

# Importer dans base
mysql -u root -p database < /tmp/data.csv
```

### Scénario 3 : Laptop Voyage International

**Besoin :** Laptop configuré en heure locale française, afficher heure New York temporairement.

```bash
# Système reste en Europe/Paris
timedatectl
# Time zone: Europe/Paris (CET, +0100)

# Terminal 1 : Heure Paris (défaut)
date
# Tue Feb 11 14:35:42 CET 2026

# Terminal 2 : Heure New York (temporaire)
export TZ='America/New_York'
date
# Tue Feb 11 08:35:42 EST 2026

# Comparer facilement
echo "Paris: $(date)"
echo "NY   : $(TZ='America/New_York' date)"
# Paris: Tue Feb 11 14:35:42 CET 2026
# NY   : Tue Feb 11 08:35:42 EST 2026
```

### Scénario 4 : Script Portable Multi-Langue

**Besoin :** Script devant fonctionner quel que soit la locale utilisateur.

```bash
#!/bin/bash
# Forcer locale C pour comportement prévisible
export LC_ALL=C

# Parsing date prévisible
today=$(date +%Y-%m-%d)
echo "Backup du $today"

# Tri alphabétique ASCII
sort /tmp/list.txt

# Messages d'erreur en anglais (facilite debug)
if [ ! -f /tmp/file ]; then
    echo "Error: File not found" >&2
fi
```

### Scénario 5 : Dual-Boot Linux/Windows

**Besoin :** Horloge matérielle désynchronisée entre Linux (UTC) et Windows (local time).

```bash
# Linux : Vérifier RTC
timedatectl
# RTC in local TZ: no   (UTC)

# Problème : Windows change RTC en heure locale
# Solution 1 : Configurer Linux en local time (pas recommandé)
sudo timedatectl set-local-rtc 1

# Solution 2 : Configurer Windows en UTC (recommandé)
# (dans Windows)
# reg add "HKLM\System\CurrentControlSet\Control\TimeZoneInformation" /v RealTimeIsUniversal /d 1 /t REG_DWORD /f

# Revenir Linux en UTC
sudo timedatectl set-local-rtc 0
```

---

## ⚠️ Erreurs Courantes

### Erreur 1 : Confusion TZ vs /etc/localtime

**Problème :**
```bash
# TZ définie en Asie
export TZ='Asia/Tokyo'
date
# Tue Feb 11 22:35:42 JST 2026

# Mais cron utilise /etc/localtime (système)
ls -la /etc/localtime
# lrwxrwxrwx 1 root root 33 Feb 11 10:00 /etc/localtime -> /usr/share/zoneinfo/Europe/Paris

# Tâche cron s'exécute en heure Paris, pas Tokyo !
```

**Solution :** Configurer timezone système avec `timedatectl`.

### Erreur 2 : LC_ALL bloque tout

**Problème :**
```bash
# Utilisateur définit LC_ALL
export LC_ALL=en_US.UTF-8

# Essaie de changer langue messages
export LC_MESSAGES=fr_FR.UTF-8

# Mais LC_ALL override tout !
ls /nonexistent
# ls: cannot access '/nonexistent': No such file or directory
# (toujours en anglais)
```

**Solution :** Ne PAS utiliser LC_ALL sauf besoin spécifique. Utiliser LANG ou LC_*.

### Erreur 3 : Encodage UTF-8 non généré

**Problème :**
```bash
# Essayer de configurer UTF-8
sudo localectl set-locale LANG=fr_FR.UTF-8

# Erreur
Failed to set locale: Locale fr_FR.UTF-8 not available

# La locale n'est pas générée !
locale -a | grep fr_FR
# (vide)
```

**Solution :**
```bash
# Générer la locale (Debian/Ubuntu)
sudo dpkg-reconfigure locales
# Cocher fr_FR.UTF-8

# Ou manuellement
sudo vim /etc/locale.gen
# Décommenter : fr_FR.UTF-8 UTF-8

sudo locale-gen
# Generating locales...
#   fr_FR.UTF-8... done
```

### Erreur 4 : iconv écrase fichier source

**Problème :**
```bash
# ❌ Tentative conversion en place
iconv -f ISO-8859-1 -t UTF-8 fichier.txt > fichier.txt
# Résultat : fichier vide !
```

**Solution :**
```bash
# ✅ Fichier temporaire
iconv -f ISO-8859-1 -t UTF-8 fichier.txt > temp.txt
mv temp.txt fichier.txt

# ✅ Ou option -o
iconv -f ISO-8859-1 -t UTF-8 fichier.txt -o new_fichier.txt
```

### Erreur 5 : Timezone inexistant

**Problème :**
```bash
sudo timedatectl set-timezone Europe/paris   # Minuscule
# Failed to set time zone: Invalid or not installed time zone 'Europe/paris'
```

**Solution :**
```bash
# Respecter la casse EXACTE
sudo timedatectl set-timezone Europe/Paris

# Lister pour vérifier le nom exact
timedatectl list-timezones | grep Paris
# Europe/Paris
```

---

## 📝 Cheat Sheet

### Timezone

```bash
# Afficher configuration
timedatectl

# Lister timezones
timedatectl list-timezones
timedatectl list-timezones | grep Europe

# Changer timezone système
sudo timedatectl set-timezone Europe/Paris

# Fichiers
/etc/localtime          # Symlink vers /usr/share/zoneinfo/...
/usr/share/zoneinfo/    # Base de données timezones

# Variable TZ (temporaire)
export TZ='Asia/Tokyo'
TZ='America/New_York' date

# tzselect (interactive, ne modifie PAS le système)
tzselect
```

### Locale

```bash
# Afficher locales
locale
locale -a               # Locales disponibles

# Changer locale temporaire
export LANG=fr_FR.UTF-8
export LC_ALL=C         # Override tout

# Configuration système
sudo localectl set-locale LANG=en_US.UTF-8
cat /etc/locale.conf

# Générer locales (Debian/Ubuntu)
sudo dpkg-reconfigure locales
sudo locale-gen

# Variables importantes
LANG                    # Locale par défaut
LC_ALL                  # Override toutes les LC_*
LC_TIME                 # Format date/heure
LC_MESSAGES             # Langue messages
LC_NUMERIC              # Format nombres
```

### Encodage

```bash
# Vérifier encodage
file fichier.txt

# Lister encodages
iconv -l

# Convertir
iconv -f ISO-8859-1 -t UTF-8 input.txt -o output.txt
iconv -f WINDOWS-1252 -t UTF-8 -c input.txt > output.txt

# Options
-f FROM                 # Encodage source
-t TO                   # Encodage cible
-c                      # Ignorer caractères invalides
//TRANSLIT              # Translittération
//IGNORE                # Ignorer incompatibles
```

### Commande date

```bash
# Formats utiles
date +%F                # 2026-02-11 (ISO)
date +%Y%m%d            # 20260211
date +"%F %T"           # 2026-02-11 14:35:42
date +%s                # 1770750942 (timestamp Unix)

# Dans scripts cron
backup_$(date +\%Y\%m\%d).tar.gz
```

---

## 🎯 Points Clés pour l'Examen LPIC-1

### Connaissances Essentielles

✅ **Timezone :**
- Différence UTC vs heure locale
- `timedatectl` : affichage, configuration, list-timezones
- `/etc/localtime` : symlink vers `/usr/share/zoneinfo/`
- Variable `TZ` : temporaire, ne modifie pas système
- `tzselect` : interactive, affiche valeur TZ à utiliser (ne change rien)

✅ **Locale :**
- Format : `langue_PAYS.ENCODAGE` (ex: `en_US.UTF-8`)
- `LANG` : locale par défaut
- `LC_ALL` : override toutes les `LC_*`
- Variables `LC_*` : TIME, MESSAGES, NUMERIC, COLLATE, MONETARY...
- `locale` : afficher configuration
- `locale -a` : locales disponibles
- `localectl` : configuration systemd
- `/etc/locale.conf` : fichier de config système
- `LANG=C` : locale POSIX (scripting, comportement prévisible)

✅ **Encodage :**
- **ASCII** : 7 bits, 128 caractères
- **ISO-8859** : 8 bits, variantes régionales (ISO-8859-1 = Latin-1)
- **UTF-8** : universel, rétrocompatible ASCII, standard actuel
- `iconv` : conversion encodage
  - `-f FROM -t TO`
  - `-l` : lister encodages
  - `-c` : ignorer erreurs
  - `//TRANSLIT`, `//IGNORE`

### Commandes à Maîtriser

| Commande | Usage Principal |
|----------|-----------------|
| `timedatectl` | Afficher/configurer timezone |
| `timedatectl list-timezones` | Lister timezones |
| `tzselect` | Sélection interactive (affiche TZ) |
| `locale` | Afficher variables locale |
| `locale -a` | Lister locales disponibles |
| `localectl` | Configuration locale (systemd) |
| `iconv` | Conversion encodage |
| `date +FORMAT` | Formatage date personnalisé |

### Fichiers Importants

| Fichier | Rôle |
|---------|------|
| `/etc/localtime` | Symlink timezone système |
| `/usr/share/zoneinfo/` | Base de données timezones |
| `/etc/locale.conf` | Configuration locale système |
| `/etc/default/locale` | Config locale (Debian/Ubuntu) |
| `/etc/locale.gen` | Locales à générer (Debian) |
| `/etc/timezone` | Nom timezone (Debian/Ubuntu) |

### Pièges Fréquents

❌ Confondre `TZ` (variable utilisateur) et `/etc/localtime` (système)  
❌ Utiliser `LC_ALL` sans comprendre qu'elle override tout  
❌ Oublier de générer la locale avant de la configurer  
❌ Conversion `iconv` en place (écrase fichier source)  
❌ Casse incorrecte dans nom timezone (`europe/paris` vs `Europe/Paris`)  
❌ Croire que `tzselect` change le timezone système (elle affiche seulement)

---

**Formation LPIC-1 102 - Préparation Certification Linux**  
**Cours 107.3 - Localisation et Internationalisation**  
**Maxime Chenaud - 11 Février 2026**
