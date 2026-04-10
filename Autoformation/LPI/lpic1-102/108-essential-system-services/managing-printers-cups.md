# 🖨️ Gestion des Imprimantes Linux avec CUPS

## 📋 Informations du Module

- **Examen** : LPIC-1 102
- **Objectif** : 108.4 - Manage printers and printing
- **Poids** : 2 points
- **Objectif** : Gérer imprimantes et impressions avec CUPS et outils legacy

---

## 🎯 Vue d'Ensemble CUPS

### 🖨️ CUPS - Common Unix Printing System

**CUPS** : Système d'impression moderne pour Unix/Linux.

**Développement :**
- Créé par Apple (racheté en 2007)
- Standard de facto Linux
- Successeur LPD (Line Printer Daemon)
- Support IPP (Internet Printing Protocol)

**Architecture :**

```
┌─────────────────────────────────────────────────────┐
│  ARCHITECTURE CUPS                                  │
├─────────────────────────────────────────────────────┤
│                                                     │
│  Application (LibreOffice, Firefox...)             │
│         │                                           │
│         ↓                                           │
│  ┌──────────────┐                                   │
│  │ CUPS Client  │                                   │
│  │ (lp, lpr)    │                                   │
│  └──────┬───────┘                                   │
│         │ IPP (Internet Printing Protocol)          │
│         ↓                                           │
│  ┌──────────────────────────────┐                   │
│  │      CUPS Daemon (cupsd)     │                   │
│  │  - Queue management          │                   │
│  │  - Job scheduling            │                   │
│  │  - Filter pipeline           │                   │
│  └──────┬───────────────────────┘                   │
│         │                                           │
│         ↓                                           │
│  ┌──────────────┐     ┌──────────────┐             │
│  │   Filtres    │ --> │   Backend    │             │
│  │ (PDF, PS...) │     │ (USB, IPP...)│             │
│  └──────────────┘     └──────┬───────┘             │
│                              │                     │
│                              ↓                     │
│                      🖨️ Imprimante                  │
│                                                     │
└─────────────────────────────────────────────────────┘
```

### 📦 Installation

```bash
# Installation CUPS (Debian/Ubuntu)
sudo apt update
sudo apt install cups

# Paquets installés (nombreux) :
# - cups (daemon principal)
# - cups-client (commandes lp, lpstat)
# - cups-common (fichiers communs)
# - cups-daemon (daemon et filtres)
# - cups-filters (filtres impression)
# - cups-ppdc (compilateur PPD)
# - libcups2 (bibliothèques)

# Installation RedHat/CentOS
sudo yum install cups

# Vérifier installation
dpkg -l | grep cups
rpm -qa | grep cups

# Version
cupsd --version
# cupsd 2.4.2
```

### 🔧 Service systemd

```bash
# Service
systemctl status cups
systemctl status cups.service

# Démarrer
sudo systemctl start cups

# Arrêter
sudo systemctl stop cups

# Redémarrer
sudo systemctl restart cups

# Recharger config
sudo systemctl reload cups

# Activer au boot
sudo systemctl enable cups

# Désactiver
sudo systemctl disable cups

# Vérifier processus
ps aux | grep cupsd
# root  1234  0.0  0.1 ... /usr/sbin/cupsd -l

# Ports écoute
ss -tlnp | grep cups
# tcp   LISTEN  0.0.0.0:631   (cupsd)
```

---

## 📂 Fichiers de Configuration CUPS

### 🗂️ Répertoire /etc/cups/

```bash
# Structure
ls -la /etc/cups/

# Fichiers principaux
# -rw-r-----  cupsd.conf          # Config principale
# -rw-r-----  cups-files.conf     # Chemins fichiers/répertoires
# -rw-r--r--  printers.conf       # Déclaration imprimantes
# -rw-r--r--  classes.conf        # Classes imprimantes
# drwxr-xr-x  ppd/                # PostScript Printer Descriptions
# drwxr-xr-x  ssl/                # Certificats SSL
# -rw-------  subscriptions.conf  # Notifications
# -rw-r--r--  snmp.conf           # SNMP (découverte réseau)
```

### 📝 Fichier Principal - /etc/cups/cupsd.conf

**Configuration daemon CUPS.**

```bash
cat /etc/cups/cupsd.conf

# Exemple configuration
# /etc/cups/cupsd.conf

# Niveau log (warn, info, debug, debug2)
LogLevel warn

# Logs
ErrorLog /var/log/cups/error_log
AccessLog /var/log/cups/access_log
PageLog /var/log/cups/page_log

# Écoute sur localhost seulement (défaut)
Listen localhost:631
# Ou interface spécifique
#Listen 192.168.1.10:631
# Ou toutes interfaces (serveur impression)
#Listen 0.0.0.0:631

# Socket Unix local
Listen /run/cups/cups.sock

# Interface web activée
WebInterface Yes

# Accès web (localhost seulement)
<Location />
  Order allow,deny
  Allow localhost
</Location>

# Admin interface
<Location /admin>
  Order allow,deny
  Allow localhost
</Location>

# Configuration imprimantes
<Location /admin/conf>
  AuthType Default
  Require user @SYSTEM
  Order allow,deny
</Location>

# Temps max job (secondes)
MaxJobTime 3600

# Taille max job (0 = illimité)
MaxJobSize 0

# Préserver jobs terminés
PreserveJobHistory Yes
PreserveJobFiles 7d

# Partage imprimantes (Bonjour/Avahi)
Browsing Yes
```

**Directives importantes :**

| Directive | Description | Exemple |
|-----------|-------------|---------|
| **Listen** | Interface/port écoute | `Listen 0.0.0.0:631` |
| **WebInterface** | Activer interface web | `WebInterface Yes` |
| **LogLevel** | Niveau verbosité logs | `LogLevel info` |
| **MaxJobTime** | Temps max job (secondes) | `MaxJobTime 3600` |
| **MaxJobSize** | Taille max job (bytes, 0=∞) | `MaxJobSize 10485760` |
| **Browsing** | Découverte réseau | `Browsing On` |
| **BrowseAddress** | Serveur impression remote | `BrowseAddress 192.168.1.10` |

**Sections `<Location>` (contrôle accès) :**

```bash
# Autoriser local + sous-réseau
<Location />
  Order allow,deny
  Allow localhost
  Allow 192.168.1.0/24
</Location>

# Admin seulement localhost
<Location /admin>
  Order allow,deny
  Allow localhost
  Require user @SYSTEM
</Location>

# Authentification HTTP basic
<Location /printers>
  AuthType Basic
  Require valid-user
</Location>
```

**Appliquer changements :**

```bash
# Vérifier syntaxe
sudo cupsd -t

# Recharger config
sudo systemctl reload cups

# Ou redémarrer
sudo systemctl restart cups
```

### 🖨️ Fichier Imprimantes - /etc/cups/printers.conf

**Déclaration imprimantes configurées.**

```bash
# /etc/cups/printers.conf

# Exemple imprimante
<Printer HP_LaserJet>
  UUID urn:uuid:12345678-1234-1234-1234-123456789abc
  Info HP LaserJet 1020
  Location Bureau étage 2
  MakeModel HP LaserJet 1020, hpcups 3.21.12
  DeviceURI usb://HP/LaserJet%201020?serial=ABC123
  State Idle
  StateTime 1707654321
  ConfigTime 1707654000
  Type 8425484
  Accepting Yes
  Shared Yes
  JobSheets none none
  QuotaPeriod 0
  PageLimit 0
  KLimit 0
  OpPolicy default
  ErrorPolicy stop-printer
</Printer>

# Imprimante réseau
<Printer Canon_Network>
  Info Canon ImageRunner
  Location Salle impression
  DeviceURI ipp://192.168.1.50:631/printers/canon
  State Idle
  Accepting Yes
  Shared Yes
</Printer>
```

**⚠️ Ne PAS éditer manuellement !**
Utiliser commandes CUPS (`lpadmin`, interface web).

### 📄 Répertoire PPD - /etc/cups/ppd/

**PPD** : PostScript Printer Description

**Rôle :**
- Décrit capacités imprimante
- Résolutions, formats papier
- Options (recto-verso, agrafeuse...)
- Commandes PostScript

```bash
# Liste fichiers PPD
ls -la /etc/cups/ppd/

# -rw-r--r-- HP_LaserJet.ppd
# -rw-r--r-- Canon_Network.ppd

# Contenu PPD (extrait)
cat /etc/cups/ppd/HP_LaserJet.ppd

*PPD-Adobe: "4.3"
*FormatVersion: "4.3"
*FileVersion: "3.21.12"
*LanguageVersion: English
*Manufacturer: "HP"
*ModelName: "HP LaserJet 1020"
*ShortNickName: "HP LaserJet 1020 hpcups"
*ColorDevice: False

*PageSize Letter: "..."
*PageSize A4: "..."
*PageRegion Letter: "..."

*Resolution 600dpi: "..."
*DefaultResolution: 600dpi
```

**Sources PPD :**
- Inclus avec CUPS (pilotes génériques)
- Fournis par constructeur
- OpenPrinting.org (base de données)
- Foomatic (système abstraction pilotes)

```bash
# Chercher PPD disponibles
lpinfo -m | grep -i "HP LaserJet"

# Trouver tous PPD
lpinfo -m | less

# Pilotes disponibles
lpinfo -v
# network http
# network ipp
# network ipps
# direct usb://HP/LaserJet%201020
```

### ⚙️ Ajout Manuel d'Imprimante via printers.conf

**Contexte avancé :**
Bien que la méthode recommandée soit `lpadmin`, il est possible d'ajouter manuellement une imprimante en éditant directement `/etc/cups/printers.conf`.

**⚠️ Attention :**
- Méthode réservée aux cas avancés
- Risque de conflit avec interface web
- Nécessite redémarrage CUPS

**Procédure :**

1. **Arrêter le service CUPS**
```bash
sudo systemctl stop cups
```

2. **Éditer /etc/cups/printers.conf**
```bash
sudo nano /etc/cups/printers.conf
```

Ajouter une entrée `<Printer>` :

```
<Printer MonImprimante>
  Info Ma nouvelle imprimante
  Location Bureau
  DeviceURI usb://HP/LaserJet%201020
  State Idle
  Accepting Yes
  Shared No
  JobSheets none none
  QuotaPeriod 0
  PageLimit 0
  KLimit 0
  OpPolicy default
  ErrorPolicy stop-printer
</Printer>
```

**Directives importantes :**
- `DeviceURI` : URI périphérique (obligatoire)
- `State` : Idle, Processing, Stopped
- `Accepting` : Yes/No (accepte nouvelles tâches)
- `Shared` : Yes/No (partagée réseau)
- `ErrorPolicy` : abort-job, retry-job, retry-current-job, stop-printer

3. **Créer le lien symbolique PPD**

**🔑 Point crucial :** Le nom du lien symbolique **DOIT** correspondre au nom de la section `<Printer>`.

```bash
# Syntaxe générale
sudo ln -s /usr/share/cups/model/<fichier_ppd_source>.ppd \
           /etc/cups/ppd/<NomImprimante>.ppd

# Exemple concret
sudo ln -s /usr/share/cups/model/hp-laserjet_1020.ppd \
           /etc/cups/ppd/MonImprimante.ppd
```

**Vérification :**
```bash
# Lister PPD disponibles
lpinfo -m | grep -i laserjet

# drv:///sample.drv/laserjet.ppd HP LaserJet Series CUPS
```

**Explication du lien symbolique :**
```
Source    : /usr/share/cups/model/hp-laserjet_1020.ppd  (PPD originel)
            ↓
Lien symb : /etc/cups/ppd/MonImprimante.ppd            (nom = <Printer>)
```

**Règle absolue :**
```
Nom section printers.conf = <Printer MonImprimante>
                           → PPD DOIT s'appeler MonImprimante.ppd
```

4. **Redémarrer CUPS**
```bash
sudo systemctl start cups
```

5. **Vérifier l'imprimante**
```bash
lpstat -p -d
# printer MonImprimante is idle.  enabled since...

# Vérifier PPD
ls -l /etc/cups/ppd/
# lrwxrwxrwx MonImprimante.ppd -> /usr/share/cups/model/hp-laserjet_1020.ppd
```

**Cas d'usage :**
- Script automatisation ajout masse imprimantes
- Configuration serveur sans interface web
- Environnement sécurisé (pas d'accès web)
- Dépannage après corruption fichiers

**⚠️ Limitations :**
- Interface web peut ne pas afficher correctement l'imprimante
- Modifications ultérieures via web peuvent écraser config manuelle
- Nécessite redémarrage CUPS à chaque modification

**💡 Recommandation :**
Pour usage normal, préférer **`lpadmin`** qui gère automatiquement printers.conf ET les PPD.

### 📊 Logs CUPS - /var/log/cups/

```bash
# Répertoire logs
ls -la /var/log/cups/

# -rw-r-----  error_log       # Erreurs daemon
# -rw-r-----  access_log      # Accès HTTP (web interface)
# -rw-r-----  page_log        # Pages imprimées (comptabilité)

# Voir erreurs
sudo tail -f /var/log/cups/error_log

# Accès web interface
sudo tail -f /var/log/cups/access_log
# 127.0.0.1 - - [11/Feb/2026:14:35:42 +0100] "GET / HTTP/1.1" 200 12345

# Pages imprimées (statistiques)
sudo cat /var/log/cups/page_log
# HP_LaserJet alice 1 [11/Feb/2026:14:30:00 +0100] 1 1 - localhost document.pdf
# Printer    User   Job  Date                      Pages Copies Billing Host    Title
```

---

## 🌐 Interface Web CUPS

### 🖥️ Accès Interface

**URL : http://localhost:631**

```bash
# Activer interface web
# /etc/cups/cupsd.conf
WebInterface Yes

sudo systemctl reload cups

# Ouvrir navigateur
firefox http://localhost:631
chromium http://localhost:631

# Ou depuis réseau (si Listen 0.0.0.0:631)
http://192.168.1.10:631
```

**Sections interface :**

```
┌─────────────────────────────────────────────┐
│  CUPS Web Interface (localhost:631)         │
├─────────────────────────────────────────────┤
│                                             │
│  🏠 Home                                     │
│  📋 Administration                          │
│     - Add Printer                           │
│     - Manage Printers                       │
│     - Classes                               │
│     - Jobs                                  │
│  🖨️ Printers                                 │
│  📄 Jobs                                     │
│  ❓ Help                                     │
│                                             │
└─────────────────────────────────────────────┘
```

**Fonctionnalités :**
- ✅ Ajouter/supprimer imprimantes
- ✅ Configurer options imprimantes
- ✅ Gérer jobs impression
- ✅ Voir logs
- ✅ Tester impression
- ✅ Configurer classes (groupes imprimantes)

**Authentification :**
```bash
# Administration nécessite authentification
# Utilisateur groupe lpadmin

# Ajouter user au groupe
sudo usermod -aG lpadmin alice

# Ou dans /etc/cups/cupsd.conf
<Location /admin>
  AuthType Default
  Require user @SYSTEM
</Location>
```

### 🔐 Gestion des Utilisateurs Autorisés

#### 🔑 Commande lppasswd - Gestion Mots de Passe CUPS

**Rôle :**
`lppasswd` gère les utilisateurs autorisés à administrer CUPS via authentification HTTP.

**Syntaxe :**
```bash
lppasswd -a utilisateur    # Ajouter utilisateur
lppasswd -x utilisateur    # Supprimer utilisateur
lppasswd -g groupe         # Gérer groupe
```

**Exemples :**

```bash
# Ajouter alice à l'authentification CUPS
sudo lppasswd -a alice
# Enter password: ********
# Enter password again: ********
# Password for user alice added successfully

# Ajouter bob
sudo lppasswd -a bob

# Supprimer alice
sudo lppasswd -x alice
# User alice removed successfully
```

**Fichier de stockage :**
```bash
# Mots de passe stockés dans
/etc/cups/passwd

# Format (MD5)
alice:$1$abc123def$...
bob:$1$xyz789ghi$...
```

**💡 Différence avec lpadmin groupe :**
- **lpadmin groupe** : Authentification système (PAM)
- **lppasswd** : Authentification HTTP CUPS (indépendante)

#### 📜 Directives Require - Contrôle d'Accès dans cupsd.conf

**Rôle :**
Les directives `Require` dans `/etc/cups/cupsd.conf` contrôlent l'accès aux différentes sections de CUPS.

**Syntaxe générale :**
```
<Location /chemin>
  <Limit opération>
    Require type valeur
  </Limit>
</Location>
```

**Types de Require :**

| Directive | Signification | Exemple |
|-----------|---------------|---------|
| `Require user alice bob` | Utilisateurs spécifiques | Seuls alice et bob |
| `Require group @lpadmin` | Groupe système | Membres de lpadmin |
| `Require valid-user` | Tout utilisateur authentifié | N'importe qui |
| `Require user @SYSTEM` | Utilisateur système (root) | Administrateur système |
| `Require user @OWNER` | Propriétaire du job | Utilisateur ayant lancé l'impression |

**Opérations Limit :**
- `ALL` : Toutes opérations
- `GET` : Lecture/consultation
- `POST` : Soumission jobs
- `PUT` : Modification configuration
- `DELETE` : Suppression
- `CUPS-Add-Modify-Printer` : Ajout/modification imprimante
- `CUPS-Delete-Printer` : Suppression imprimante

**Exemples pratiques :**

**1. Administration réservée à root et lpadmin**
```bash
sudo nano /etc/cups/cupsd.conf
```

```
<Location /admin>
  AuthType Default
  <Limit CUPS-Add-Modify-Printer CUPS-Delete-Printer CUPS-Add-Modify-Class CUPS-Delete-Class CUPS-Set-Default>
    Require user @SYSTEM
    Require group @lpadmin
  </Limit>
</Location>
```

**2. Impression autorisée pour alice et bob uniquement**
```
<Location /printers/HP_LaserJet>
  AuthType Default
  <Limit All>
    Order deny,allow
    Require user alice bob
  </Limit>
</Location>
```

**3. Consultation jobs autorisée pour propriétaire ou admin**
```
<Location /jobs>
  AuthType Default
  <Limit All>
    Require user @OWNER @SYSTEM
  </Limit>
</Location>
```

**4. Configuration imprimante : groupe lpadmin ou utilisateurs spécifiques**
```
<Location /admin/conf>
  AuthType Default
  <Limit GET POST PUT>
    Require group @lpadmin
    Require user alice
  </Limit>
</Location>
```

**5. Accès racine CUPS : tout utilisateur authentifié**
```
<Location />
  Order allow,deny
  Allow localhost
  <Limit All>
    Require valid-user
  </Limit>
</Location>
```

**Combinaison avec lppasswd :**

```bash
# Scénario : Autoriser charlie via lppasswd + cupsd.conf

# 1. Ajouter charlie à lppasswd
sudo lppasswd -a charlie

# 2. Configurer cupsd.conf
sudo nano /etc/cups/cupsd.conf
```

```
<Location /admin>
  AuthType Default
  <Limit CUPS-Add-Modify-Printer>
    Require user charlie
  </Limit>
</Location>
```

```bash
# 3. Redémarrer CUPS
sudo systemctl restart cups

# 4. Charlie peut maintenant administrer via web
# URL: http://localhost:631/admin
# Login: charlie / mot de passe lppasswd
```

**⚠️ Points d'attention :**

- `Require user @SYSTEM` = root uniquement
- `Require group @lpadmin` = membres du groupe système lpadmin
- **lppasswd** crée authentification HTTP séparée du système
- Pour combiner : utilisateur doit être dans lpadmin **ET** dans lppasswd
- Redémarrage CUPS nécessaire après modification cupsd.conf

**Vérification configuration :**
```bash
# Tester syntaxe cupsd.conf
sudo cupsd -t
# cupsd: OK

# Voir config active
sudo cupsctl | grep -i auth

# Logs authentification
sudo tail -f /var/log/cups/error_log | grep -i auth
```

---

## 🔧 Commandes CUPS Modernes

### 📋 Commande lp - Imprimer

```bash
# Imprimer fichier
lp document.pdf
lp image.jpg
lp /etc/hosts

# Spécifier imprimante (-d)
lp -d HP_LaserJet document.pdf

# Nombre de copies (-n)
lp -n 3 document.pdf

# Titre job (-t)
lp -t "Rapport mensuel" report.pdf

# Options imprimante (-o)
lp -o media=A4 document.pdf
lp -o sides=two-sided-long-edge document.pdf
lp -o orientation-requested=3 landscape.pdf
lp -o fit-to-page document.pdf

# Combiner options
lp -d HP_LaserJet -n 2 -o media=A4 -o sides=two-sided-long-edge report.pdf

# Imprimer depuis stdin
cat file.txt | lp
echo "Test impression" | lp

# Plage pages (-P)
lp -P 1-5 document.pdf
lp -P 1,3,5-10 document.pdf

# Priorité (-q, 1-100, défaut 50)
lp -q 100 urgent.pdf
lp -q 1 low-priority.pdf

# Mail notification fin job (-m)
lp -m document.pdf
```

**Options courantes :**

| Option | Description | Exemple |
|--------|-------------|---------|
| **-d printer** | Imprimante destination | `lp -d HP_LaserJet file.pdf` |
| **-n copies** | Nombre copies | `lp -n 5 file.pdf` |
| **-t title** | Titre job | `lp -t "Invoice" inv.pdf` |
| **-o option** | Option imprimante | `lp -o media=A4 file.pdf` |
| **-P pages** | Plage pages | `lp -P 1-10 doc.pdf` |
| **-q priority** | Priorité (1-100) | `lp -q 100 file.pdf` |
| **-m** | Email notification | `lp -m file.pdf` |

### 📊 Commande lpstat - État Impression

```bash
# État général
lpstat

# Imprimantes disponibles
lpstat -p
# printer HP_LaserJet is idle.  enabled since Tue 11 Feb 2026 10:00:00 AM CET
# printer Canon_Network is idle.  enabled since Tue 11 Feb 2026 10:00:00 AM CET

# Imprimante par défaut
lpstat -d
# system default destination: HP_LaserJet

# Jobs actifs
lpstat -o
# HP_LaserJet-12      alice           1024   Tue 11 Feb 2026 02:35:00 PM CET

# Tous les jobs (actifs + récents)
lpstat -W all

# Jobs spécifique imprimante
lpstat -o HP_LaserJet

# Détails complets
lpstat -l -p
lpstat -l -o

# Tout afficher (verbose)
lpstat -t

# Classes imprimantes
lpstat -c

# Pilotes disponibles
lpstat -m

# Périphériques disponibles
lpstat -v
# device for HP_LaserJet: usb://HP/LaserJet%201020
# device for Canon_Network: ipp://192.168.1.50:631/printers/canon
```

### ❌ Commande cancel - Annuler Job

```bash
# Annuler par job ID
cancel 12
cancel HP_LaserJet-12

# Annuler tous jobs utilisateur
cancel -a

# Annuler tous jobs imprimante
cancel -a HP_LaserJet

# Annuler tous jobs (root)
sudo cancel -a -a

# Annuler job utilisateur spécifique (root)
sudo cancel -u alice

# Annuler avec confirmation
cancel -x 12
```

### 🔧 Commande lpadmin - Administration

```bash
# Ajouter imprimante USB
sudo lpadmin -p HP_LaserJet \
  -v usb://HP/LaserJet%201020 \
  -m drv:///hp/hpcups.drv/hp-laserjet_1020.ppd \
  -E

# Ajouter imprimante réseau IPP
sudo lpadmin -p Canon_Network \
  -v ipp://192.168.1.50:631/printers/canon \
  -m everywhere \
  -E

# Définir imprimante par défaut
lpadmin -d HP_LaserJet

# Modifier emplacement/description
sudo lpadmin -p HP_LaserJet \
  -L "Bureau étage 2" \
  -D "HP LaserJet 1020"

# Modifier options
sudo lpadmin -p HP_LaserJet \
  -o media=A4 \
  -o sides=two-sided-long-edge

# Supprimer imprimante
sudo lpadmin -x HP_LaserJet

# Partager imprimante réseau
sudo lpadmin -p HP_LaserJet -o printer-is-shared=true

# Ne pas partager
sudo lpadmin -p HP_LaserJet -o printer-is-shared=false
```

**Options lpadmin :**

| Option | Description | Exemple |
|--------|-------------|---------|
| **-p name** | Nom imprimante | `-p HP_LaserJet` |
| **-v URI** | Device URI | `-v usb://HP/...` |
| **-m model** | Modèle/PPD | `-m everywhere` |
| **-E** | Activer imprimante | `-E` |
| **-L location** | Localisation | `-L "Bureau 2"` |
| **-D description** | Description | `-D "HP LaserJet"` |
| **-o option=value** | Option imprimante | `-o media=A4` |
| **-d printer** | Défaut système | `-d HP_LaserJet` |
| **-x printer** | Supprimer | `-x HP_LaserJet` |

### 🎛️ Contrôle État Imprimante

**Accepter/Rejeter jobs dans queue :**

```bash
# Accepter nouveaux jobs
sudo cupsaccept HP_LaserJet

# Rejeter nouveaux jobs
sudo cupsreject HP_LaserJet

# Rejeter avec raison
sudo cupsreject -r "Maintenance en cours" HP_LaserJet

# Vérifier
lpstat -p HP_LaserJet
# printer HP_LaserJet disabled since Tue 11 Feb 2026 02:35:00 PM CET -
#         Maintenance en cours
```

**Activer/Désactiver impression :**

```bash
# Activer impression (traiter queue)
sudo cupsenable HP_LaserJet

# Désactiver impression (pause queue)
sudo cupsdisable HP_LaserJet

# Désactiver avec raison
sudo cupsdisable -r "Bourrage papier" HP_LaserJet

# Vérifier
lpstat -p HP_LaserJet
# printer HP_LaserJet is idle.  enabled since Tue 11 Feb 2026 02:40:00 PM CET
```

**Combinaisons état :**

| cupsaccept | cupsenable | Résultat |
|------------|------------|----------|
| ✅ Accept | ✅ Enable | Normal (accepte + imprime) |
| ❌ Reject | ✅ Enable | Traite queue, refuse nouveaux |
| ✅ Accept | ❌ Disable | Accepte nouveaux, pause impression |
| ❌ Reject | ❌ Disable | Bloqué (refuse tout) |

```bash
# Scénario maintenance :
# 1. Refuser nouveaux jobs
sudo cupsreject -r "Maintenance" HP_LaserJet

# 2. Laisser finir queue actuelle
# (cupsenable actif)

# Jobs actuels terminent...

# 3. Désactiver impression
sudo cupsdisable -r "Maintenance" HP_LaserJet

# Maintenance physique...

# 4. Réactiver
sudo cupsenable HP_LaserJet
sudo cupsaccept HP_LaserJet
```

---

## 🔙 Outils Legacy BSD (cups-bsd)

### 📦 Installation cups-bsd

**Rétrocompatibilité scripts anciens.**

```bash
# Installation
sudo apt install cups-bsd

# RedHat/CentOS
sudo yum install cups-lpd

# Commandes fournies :
# - lpr  (imprimer)
# - lpq  (queue)
# - lprm (remove job)
# - lpc  (control)
```

### 📄 Commande lpr - Imprimer (BSD)

**Alternative `lp` (syntaxe différente).**

```bash
# Imprimer fichier
lpr document.pdf
lpr image.jpg

# Spécifier imprimante (-P)
lpr -P HP_LaserJet document.pdf

# ⚠️ Différence lp vs lpr :
# lp : -d printer
# lpr : -P printer

# Nombre copies (-#)
lpr -#3 document.pdf

# Titre job (-J)
lpr -J "Rapport mensuel" report.pdf

# Format (-o)
lpr -o media=A4 document.pdf

# Depuis stdin
cat file.txt | lpr -P HP_LaserJet

# Mail notification fin job (-m)
lpr -m document.pdf
```

**Options courantes :**

| Option | Description | Équivalent lp |
|--------|-------------|---------------|
| **-P printer** | Imprimante | `-d printer` |
| **-# num** | Copies | `-n num` |
| **-J title** | Titre job | `-t title` |
| **-o option** | Options | `-o option` |
| **-m** | Email notification | `-m` |
| **-l** | Binaire (pas filtrage) | N/A |
| **-h** | Pas banner page | N/A |

### 📊 Commande lpq - Queue (BSD)

**Voir jobs en attente.**

```bash
# Queue imprimante par défaut
lpq

# Queue imprimante spécifique
lpq -P HP_LaserJet

# Output exemple
# HP_LaserJet is ready
# Rank    Owner   Job     File(s)                         Total Size
# active  alice   12      document.pdf                    1024 bytes
# 1st     bob     13      report.pdf                      2048 bytes

# Tous les utilisateurs (-a)
lpq -a

# Long format (-l)
lpq -l

# Rafraîchir toutes N secondes
lpq -P HP_LaserJet +5
# Rafraîchit toutes les 5 secondes (Ctrl+C pour arrêter)
```

**Équivalent moderne :**
```bash
lpstat -o HP_LaserJet
```

### ❌ Commande lprm - Remove Job (BSD)

**Supprimer jobs queue.**

```bash
# Supprimer job
lprm 12

# Supprimer tous mes jobs
lprm -

# Supprimer tous jobs imprimante
lprm -P HP_LaserJet -

# Supprimer job utilisateur (root)
sudo lprm -P HP_LaserJet -U alice

# Supprimer job spécifique imprimante
lprm -P HP_LaserJet 12
```

**Équivalent moderne :**
```bash
cancel 12
cancel -a HP_LaserJet
```

### 🎛️ Commande lpc - Control (BSD)

**Contrôle imprimantes (admin).**

```bash
# Mode interactif (root)
sudo lpc
lpc> help
lpc> status
lpc> status HP_LaserJet
lpc> stop HP_LaserJet
lpc> start HP_LaserJet
lpc> disable HP_LaserJet
lpc> enable HP_LaserJet
lpc> quit

# Mode ligne commande
sudo lpc status
sudo lpc status HP_LaserJet

# Arrêter impression
sudo lpc stop HP_LaserJet

# Reprendre impression
sudo lpc start HP_LaserJet

# Désactiver queue
sudo lpc disable HP_LaserJet

# Activer queue
sudo lpc enable HP_LaserJet

# Tout afficher
sudo lpc status all
```

**⚠️ Limité avec CUPS :**
CUPS émule `lpc` mais fonctionnalités limitées.

**Équivalents modernes :**
```bash
# lpc stop      → cupsdisable
# lpc start     → cupsenable
# lpc disable   → cupsreject
# lpc enable    → cupsaccept
# lpc status    → lpstat -p
```

---

## 🔍 Comparaison Commandes CUPS vs BSD

### 📊 Tableau Équivalences

| Action | CUPS moderne | BSD legacy |
|--------|--------------|------------|
| **Imprimer** | `lp -d printer file` | `lpr -P printer file` |
| **État imprimantes** | `lpstat -p` | `lpc status` |
| **Jobs queue** | `lpstat -o` | `lpq -P printer` |
| **Annuler job** | `cancel jobid` | `lprm jobid` |
| **Accepter jobs** | `cupsaccept printer` | `lpc enable printer` |
| **Rejeter jobs** | `cupsreject printer` | `lpc disable printer` |
| **Activer impression** | `cupsenable printer` | `lpc start printer` |
| **Désactiver impression** | `cupsdisable printer` | `lpc stop printer` |
| **Défaut système** | `lpadmin -d printer` | `lpoptions -d printer` |
| **Add imprimante** | `lpadmin -p ... -v ... -m ...` | N/A (config manuelle) |

### ⚡ Différences Syntaxe

```bash
# Spécifier imprimante
lp -d HP_LaserJet file.pdf      # CUPS
lpr -P HP_LaserJet file.pdf     # BSD

# Nombre copies
lp -n 3 file.pdf                # CUPS
lpr -#3 file.pdf                # BSD

# Titre job
lp -t "Title" file.pdf          # CUPS
lpr -J "Title" file.pdf         # BSD
```

---

## 🎓 Scénarios Pratiques

### Scénario 1 : Installer Imprimante USB

```bash
# 1. Connecter imprimante USB
# Vérifier détection
dmesg | tail
lsusb | grep -i printer

# 2. Voir périphériques disponibles
lpinfo -v
# device for usb://HP/LaserJet%201020?serial=ABC123

# 3. Chercher pilote
lpinfo -m | grep -i "HP LaserJet 1020"

# 4. Ajouter imprimante
sudo lpadmin -p HP_LaserJet \
  -v "usb://HP/LaserJet%201020?serial=ABC123" \
  -m drv:///hp/hpcups.drv/hp-laserjet_1020.ppd \
  -L "Bureau direction" \
  -D "HP LaserJet 1020" \
  -E

# 5. Définir par défaut
lpadmin -d HP_LaserJet

# 6. Vérifier
lpstat -p HP_LaserJet
lpstat -d

# 7. Test impression
echo "Test page" | lp -d HP_LaserJet
```

### Scénario 2 : Imprimante Réseau IPP

```bash
# 1. Trouver imprimante (nmap, avahi...)
avahi-browse -rt _ipp._tcp

# 2. Ajouter imprimante IPP
sudo lpadmin -p Canon_Network \
  -v "ipp://192.168.1.50/ipp/print" \
  -m everywhere \
  -L "Salle impression" \
  -D "Canon ImageRunner" \
  -o printer-is-shared=true \
  -E

# 3. Activer
sudo cupsaccept Canon_Network
sudo cupsenable Canon_Network

# 4. Test
lpstat -p Canon_Network
lp -d Canon_Network test.pdf
```

### Scénario 3 : Serveur Impression (Partage)

```bash
# 1. Config cupsd.conf
sudo vim /etc/cupsd.conf

# Écouter toutes interfaces
Listen 0.0.0.0:631

# Autoriser sous-réseau
<Location />
  Order allow,deny
  Allow localhost
  Allow 192.168.1.0/24
</Location>

<Location /admin>
  Order allow,deny
  Allow localhost
  Allow 192.168.1.0/24
</Location>

# 2. Recharger
sudo systemctl reload cups

# 3. Firewall
sudo firewall-cmd --permanent --add-service=ipp
sudo firewall-cmd --reload
# Ou ufw
sudo ufw allow 631/tcp

# 4. Partager imprimantes
sudo lpadmin -p HP_LaserJet -o printer-is-shared=true

# 5. Clients se connectent
# http://192.168.1.10:631
# Ajouter imprimante réseau ipp://192.168.1.10:631/printers/HP_LaserJet
```

### Scénario 4 : Maintenance Imprimante

```bash
# 1. Annoncer maintenance
sudo cupsreject -r "Maintenance programmée 14:00-15:00" HP_LaserJet

# 2. Attendre fin jobs en cours
lpstat -o HP_LaserJet
# Surveiller jusqu'à vide

# 3. Désactiver impression
sudo cupsdisable -r "Maintenance en cours" HP_LaserJet

# 4. Maintenance physique
# Chargement papier, nettoyage, toner...

# 5. Réactiver
sudo cupsenable HP_LaserJet
sudo cupsaccept HP_LaserJet

# 6. Test
echo "Test maintenance OK" | lp -d HP_LaserJet

# 7. Vérifier
lpstat -p HP_LaserJet
# printer HP_LaserJet is idle.  enabled since ...
```

### Scénario 5 : Gestion Queue Bloquée

```bash
# 1. Identifier problème
lpstat -o
# HP_LaserJet-15 alice 2048 ... (bloqué depuis 2h)

# 2. Voir détails
lpstat -l -o

# 3. Désactiver impression temporairement
sudo cupsdisable HP_LaserJet

# 4. Annuler job problématique
cancel 15

# 5. Purger queue si nécessaire
cancel -a HP_LaserJet

# 6. Vérifier logs
sudo tail -f /var/log/cups/error_log

# 7. Réactiver
sudo cupsenable HP_LaserJet

# 8. Relancer jobs si sauvegardés
lp -d HP_LaserJet saved-document.pdf
```

---

## 🛠️ Diagnostic et Dépannage

### 🔍 Vérifications Basiques

```bash
# Service actif ?
systemctl status cups

# Daemon tourne ?
ps aux | grep cupsd

# Port écoute ?
ss -tlnp | grep 631

# Imprimante configurée ?
lpstat -p

# Imprimante activée ?
lpstat -p HP_LaserJet
# Chercher "enabled" et "accepting"

# Jobs bloqués ?
lpstat -o

# Logs erreurs
sudo tail -50 /var/log/cups/error_log
journalctl -u cups -n 50
```

### ⚠️ Problèmes Courants

**Imprimante non détectée (USB) :**
```bash
# Vérifier USB
lsusb
dmesg | grep -i usb

# Permissions
ls -la /dev/usb/lp*
sudo chmod 666 /dev/usb/lp0

# Ou ajouter user au groupe
sudo usermod -aG lp alice
```

**Jobs bloqués :**
```bash
# Voir queue
lpstat -o -l

# Annuler
cancel -a printer

# Redémarrer CUPS
sudo systemctl restart cups
```

**Permission denied :**
```bash
# Ajouter au groupe lpadmin
sudo usermod -aG lpadmin alice

# Ou lp
sudo usermod -aG lp alice

# Re-login
su - alice
```

**Communication réseau échoue :**
```bash
# Firewall
sudo ufw status
sudo ufw allow 631/tcp

# Ping imprimante
ping 192.168.1.50

# Test port IPP
telnet 192.168.1.50 631

# Ou nc
nc -zv 192.168.1.50 631
```

---

## 📝 Cheat Sheet

### Commandes CUPS Modernes

```bash
# Impression
lp file.pdf                    # Défaut
lp -d printer file.pdf         # Spécifier imprimante
lp -n 3 file.pdf              # 3 copies
lp -o media=A4 file.pdf       # Options

# État
lpstat -p                      # Imprimantes
lpstat -d                      # Défaut
lpstat -o                      # Jobs
lpstat -t                      # Tout

# Annuler
cancel jobid                   # Job spécifique
cancel -a printer              # Tous jobs imprimante

# Administration
sudo lpadmin -p name -v URI -m model -E    # Ajouter
sudo lpadmin -x printer                     # Supprimer
lpadmin -d printer                          # Défaut

# Contrôle
sudo cupsaccept printer        # Accepter nouveaux jobs
sudo cupsreject printer        # Rejeter nouveaux jobs
sudo cupsenable printer        # Activer impression
sudo cupsdisable printer       # Désactiver impression
```

### Commandes BSD Legacy

```bash
# Impression
lpr -P printer file.pdf        # Imprimer
lpr -#3 file.pdf              # 3 copies

# Queue
lpq                            # Voir queue
lpq -P printer                 # Imprimante spécifique

# Annuler
lprm jobid                     # Supprimer job
lprm -                         # Mes jobs

# Contrôle
sudo lpc status                # État
sudo lpc stop printer          # Arrêter
sudo lpc start printer         # Démarrer
```

### Fichiers Importants

```bash
# Configuration
/etc/cups/cupsd.conf           # Principal
/etc/cups/printers.conf        # Imprimantes
/etc/cups/ppd/                 # Pilotes PPD

# Logs
/var/log/cups/error_log        # Erreurs
/var/log/cups/access_log       # Accès web
/var/log/cups/page_log         # Pages imprimées

# Web interface
http://localhost:631

# Service
systemctl status cups
systemctl reload cups
```

---

## 🎯 Points Clés pour l'Examen LPIC-1

### Connaissances Essentielles

✅ **CUPS (Common Unix Printing System) :**
- Paquet `cups` (+ dépendances nombreuses)
- Service `cups.service` (systemd)
- Standard moderne Linux

✅ **Configuration :**
- `/etc/cups/cupsd.conf` : Config principale
- Directive `Listen` : IP/port écoute (localhost, 0.0.0.0)
- `WebInterface Yes` : Activer interface web
- Interface web : `http://localhost:631`
- `/etc/cups/printers.conf` : Déclaration imprimantes (auto-géré)
- `/etc/cups/ppd/` : PostScript Printer Descriptions

✅ **Logs :**
- `/var/log/cups/error_log` : Erreurs
- `/var/log/cups/access_log` : Accès HTTP
- `/var/log/cups/page_log` : Statistiques

✅ **Commandes CUPS modernes :**
- `lp` : Imprimer (`-d printer`, `-n copies`, `-o options`)
- `lpstat` : État (`-p` printers, `-d` default, `-o` jobs, `-t` all)
- `cancel` : Annuler job
- `lpadmin` : Administration (`-p name -v URI -m model -E`)
- `cupsaccept/cupsreject` : Accepter/rejeter nouveaux jobs (`-r` raison)
- `cupsenable/cupsdisable` : Activer/désactiver impression (`-r` raison)

✅ **Outils BSD (cups-bsd) :**
- `lpr` : Imprimer (`-P printer`, `-#` copies)
- `lpq` : Voir queue (`-P printer`)
- `lprm` : Supprimer job
- `lpc` : Contrôle (limité avec CUPS)

✅ **Différences syntaxe :**
- CUPS : `lp -d printer`
- BSD : `lpr -P printer`

✅ **Pilotes :**
- CUPS a pilotes pré-embarqués
- PPD : PostScript Printer Description
- `lpinfo -m` : Lister modèles
- `lpinfo -v` : Lister périphériques

### Commandes à Maîtriser

| Commande | Usage |
|----------|-------|
| `lp -d printer file` | Imprimer fichier |
| `lpstat -p` | État imprimantes |
| `lpstat -o` | Jobs queue |
| `cancel jobid` | Annuler job |
| `cupsaccept printer` | Accepter jobs |
| `cupsreject printer` | Rejeter jobs |
| `cupsenable printer` | Activer impression |
| `cupsdisable printer` | Désactiver impression |
| `lpr -P printer file` | Imprimer (BSD) |
| `lpq` | Queue (BSD) |

### Pièges Fréquents

❌ Confondre `lp -d` (CUPS) et `lpr -P` (BSD)  
❌ Oublier `-E` à la fin de `lpadmin` (activer imprimante)  
❌ Éditer `/etc/cups/printers.conf` manuellement (utiliser lpadmin)  
❌ `cupsreject` rejette nouveaux jobs (pas impression queue actuelle)  
❌ `cupsdisable` pause impression (pas rejet nouveaux jobs)  
❌ Interface web nécessite `WebInterface Yes` + reload  
❌ Port 631 bloqué firewall → Pas accès réseau  
❌ Oublier paquet `cups-bsd` pour commandes legacy

### Exemples Examen

**Q : Afficher état toutes imprimantes ?**
```bash
lpstat -p
```

**Q : Imprimer 3 copies sur imprimante HP_LaserJet ?**
```bash
lp -d HP_LaserJet -n 3 file.pdf
# ou BSD
lpr -P HP_LaserJet -#3 file.pdf
```

**Q : Désactiver imprimante avec raison "Maintenance" ?**
```bash
sudo cupsdisable -r "Maintenance" HP_LaserJet
```

**Q : Fichier config principal CUPS ?**
```
/etc/cups/cupsd.conf
```

**Q : Activer interface web CUPS ?**
```bash
# /etc/cups/cupsd.conf
WebInterface Yes

sudo systemctl reload cups
```

**Q : Annuler tous jobs imprimante ?**
```bash
cancel -a HP_LaserJet
```

---

**Formation LPIC-1 102 - Préparation Certification Linux**  
**Cours 108.4 - Gestion Imprimantes avec CUPS**  
**Maxime Chenaud - 11 Février 2026**
