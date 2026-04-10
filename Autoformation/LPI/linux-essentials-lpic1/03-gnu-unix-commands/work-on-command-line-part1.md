# 💻 Travailler sur la Ligne de Commande - Partie 1

## 🎯 Commandes Shell Simples et Séquences

---

## 🔌 Modes de Connexion à un Système Linux

### Vue d'Ensemble

**4 méthodes principales** pour interagir avec un système Linux :

| Mode | Type | Exemple | Interface |
|------|------|---------|-----------|
| **Local text-mode** | Console texte locale | TTY1-6 (Ctrl+Alt+F1-F6) | Texte uniquement |
| **Local graphical-mode** | Console graphique locale | Bureau GNOME/KDE (TTY7) | Interface graphique |
| **Remote text-mode** | Console texte distante | SSH, ancien Telnet | Texte sur réseau |
| **Remote graphical-mode** | Console graphique distante | VNC, RDP, X11 | Graphique sur réseau |

**Analogie** 🚗 :
- **Local** = Conduire votre voiture directement
- **Remote** = Contrôler voiture à distance

**Analogie** 📺 :
- **Text-mode** = Radio (audio uniquement)
- **Graphical-mode** = Télévision (visuel)

---

### Schéma Connexions

```
┌─────────────────────────────────────────────┐
│         SERVEUR LINUX                       │
│                                             │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐ │
│  │   TTY1   │  │   TTY2   │  │   TTY7   │ │ ← Consoles locales
│  │  (text)  │  │  (text)  │  │  (GUI)   │ │
│  └──────────┘  └──────────┘  └──────────┘ │
│                                             │
│  ┌──────────┐  ┌──────────┐               │
│  │   sshd   │  │  vncserver│               │ ← Services réseau
│  │ (port 22)│  │(port 5901)│               │
│  └──────────┘  └──────────┘               │
└─────────────────┬───────────────────────────┘
                  │
                  │ Réseau
                  │
┌─────────────────┴───────────────────────────┐
│         CLIENT (Utilisateur)                │
│                                             │
│  ┌──────────────┐  ┌──────────────┐       │
│  │  SSH Client  │  │  VNC Viewer  │       │
│  │ (text remote)│  │(graphical rem)│       │
│  └──────────────┘  └──────────────┘       │
└─────────────────────────────────────────────┘
```

---

## 🖥️ Console : Affichage OS Standard

### Définition Console

**Console** = Interface **physique** directe avec le système

**Caractéristiques** :
- ✅ Connexion **directe** au matériel
- ✅ Disponible **avant** chargement OS complet
- ✅ Affiche messages **boot** (chargement noyau, services...)
- ✅ Accès **même si** interface graphique plante

**Historique** 🕰️ :
- **Années 1960-70** : Terminal physique (clavier + écran cathodique)
- **Aujourd'hui** : Émulé mais conserve concept

---

### TTY : TeleTYpewriter

**TTY** = **T**ele**TY**pewriter (téléimprimeur)

**Origine** 📠 :
```
Années 1960 :
┌─────────────────┐
│  Téléimprimeur  │ ← Machine à écrire électrique
│   (TTY33/ASR)   │   connectée au mainframe
│  Clavier + Papier │
└─────────────────┘
        │
        │ Ligne série
        │
┌─────────────────┐
│   Mainframe     │
│     (serveur)   │
└─────────────────┘
```

**Aujourd'hui** :
- TTY = **Terminal virtuel** (émulation logicielle)
- Conserve terminologie historique

---

### Messages Console au Boot

**Console affiche** :

```
[    0.000000] Linux version 6.2.0-39-generic (buildd@ubuntu) ...
[    0.000000] Command line: BOOT_IMAGE=/boot/vmlinuz-6.2.0-39-generic ...
[    0.123456] PCI: Using configuration type 1 for base access
[    0.234567] Memory: 16384000K/16777216K available
[    1.000000] systemd[1]: Detected architecture x86-64
[    1.123456] systemd[1]: Starting systemd-journald.service...
[  OK  ] Started systemd-journald.service
[  OK  ] Reached target Local File Systems
[  OK  ] Started OpenSSH server daemon
```

**Informations** :
- ⚙️ Chargement noyau (kernel)
- 🔧 Détection matériel (PCI, RAM, CPU)
- 🚀 Démarrage services (systemd units)
- ✅ État services (OK, FAILED)

**💡 Utilité** : Diagnostiquer problèmes démarrage

---

## 🔀 Virtual Terminal (VT) : Multiples Consoles

### Concept

**Virtual Terminal** = Console **virtuelle** (plusieurs terminaux sur même écran)

**Linux fournit** : **6 consoles texte** + **1 console graphique**

---

### Table Virtual Terminals

| TTY | Type | Accès | Usage |
|-----|------|-------|-------|
| **TTY1** | Texte | Ctrl+Alt+F1 | Console texte principale |
| **TTY2** | Texte | Ctrl+Alt+F2 | Console texte secondaire |
| **TTY3** | Texte | Ctrl+Alt+F3 | Console texte 3 |
| **TTY4** | Texte | Ctrl+Alt+F4 | Console texte 4 |
| **TTY5** | Texte | Texte | Ctrl+Alt+F5 | Console texte 5 |
| **TTY6** | Texte | Ctrl+Alt+F6 | Console texte 6 |
| **TTY7** | Graphique | Ctrl+Alt+F7 | Interface graphique (X11/Wayland) |

**💡 Distribution moderne** : Interface graphique peut être sur TTY1 ou TTY2 selon config

---

### Changer de Virtual Terminal

**Raccourcis clavier** :

```bash
# Passer en mode texte (TTY2)
Ctrl + Alt + F2

# Passer en mode texte (TTY3)
Ctrl + Alt + F3

# Retourner interface graphique (généralement TTY7 ou TTY1)
Ctrl + Alt + F7
# Ou
Ctrl + Alt + F1
```

**Depuis terminal graphique** :
```bash
# Changer VT sans Ctrl
Alt + F2  # TTY2
Alt + F3  # TTY3
```

---

### Utilisation Virtual Terminals

**Scénarios pratiques** :

#### 1️⃣ Interface graphique plantée
```
Problème : Bureau GNOME/KDE ne répond plus

Solution :
Ctrl + Alt + F2  → TTY2 (texte)
Connexion : username / password
kill -9 $(pidof gnome-shell)  → Tuer process graphique
systemctl restart gdm  → Redémarrer gestionnaire affichage
Ctrl + Alt + F7  → Retour interface graphique
```

#### 2️⃣ Tâche en arrière-plan
```
TTY1 : Compilation longue (make -j8)
TTY2 : Surveillance (htop)
TTY3 : Logs (journalctl -f)
TTY7 : Travail graphique (navigateur, IDE)
```

#### 3️⃣ Diagnostic serveur
```
Serveur sans interface graphique (headless)
TTY1 : Logs système (dmesg -w)
TTY2 : Session admin (root)
TTY3 : Monitoring (top, iotop)
```

---

### Analogie Virtual Terminals 📺

**Imaginez** : 7 chaînes TV différentes

```
F1 → Chaîne Info (console texte)
F2 → Chaîne Sport (autre console texte)
F3 → Chaîne Météo (encore console texte)
...
F7 → Chaîne Netflix (interface graphique)
```

**Zapper** : Ctrl+Alt+F[1-7]

---

### Lister TTY Actifs

```bash
# Voir terminal actuel
tty
# /dev/tty2

# Lister utilisateurs connectés avec TTY
who
# user1  tty1   2026-02-02 10:30
# user2  tty2   2026-02-02 10:35
# user3  pts/0  2026-02-02 10:40 (192.168.1.100)

# Voir tous TTY avec processus
ps aux | grep tty
```

**Types TTY** :
- `tty1-6` → Virtual Terminals texte
- `tty7` → Interface graphique
- `pts/0` → Pseudo-terminal (SSH, Terminal Emulator)

---

## 🪟 Terminal Emulator : Terminal dans GUI

### Définition

**Terminal Emulator** = Application **graphique** émulant terminal texte

**Rôle** : Terminal **dans** environnement graphique (sans quitter GUI)

**Analogie** 🎮 : Émulateur console (PlayStation sur PC)

---

### Exemples Terminal Emulators

| Environnement | Terminal Emulator | Commande |
|---------------|-------------------|----------|
| **GNOME** | GNOME Terminal | `gnome-terminal` |
| **KDE Plasma** | Konsole | `konsole` |
| **XFCE** | XFCE Terminal | `xfce4-terminal` |
| **MATE** | MATE Terminal | `mate-terminal` |
| **Universel** | xterm | `xterm` |
| **Avancé** | Terminator | `terminator` |
| **Moderne** | Alacritty | `alacritty` |
| **Windows** | Windows Terminal | `wt.exe` |
| **macOS** | Terminal.app | Terminal |

---

### Caractéristiques Terminal Emulators

**Avantages** :
- ✅ **Multiples onglets** (plusieurs shells dans une fenêtre)
- ✅ **Copier-coller** graphique (souris)
- ✅ **Split screen** (diviser fenêtre)
- ✅ **Couleurs** configurables
- ✅ **Polices** personnalisées
- ✅ **Transparence** fenêtre
- ✅ **Raccourcis clavier** custom

**vs TTY virtuel** :
- TTY : Pas de souris, noir/blanc, police fixe
- Terminal Emulator : Souris, couleurs, config riche

---

### Terminal Emulator → PTY/PTS

**PTY** = **P**seudo-**T**erminal **slav**e**Y**

**Différence TTY vs PTY** :

| Type | Connexion | Exemple | Fichier périphérique |
|------|-----------|---------|----------------------|
| **TTY** | Physique/Virtuel | Console directe | `/dev/tty1`, `/dev/tty2` |
| **PTY** | Émulé (software) | Terminal Emulator, SSH | `/dev/pts/0`, `/dev/pts/1` |

**Vérifier** :
```bash
# Dans terminal emulator
tty
# /dev/pts/0  ← Pseudo-terminal

# Dans TTY2 (Ctrl+Alt+F2)
tty
# /dev/tty2  ← Virtual terminal
```

---

### Schéma Architecture Terminal Emulator

```
┌────────────────────────────────────────┐
│   Bureau Graphique (GNOME/KDE)        │
│                                        │
│  ┌──────────────────────────────────┐ │
│  │  GNOME Terminal (Application)    │ │
│  │                                  │ │
│  │  ┌────────┐  ┌────────┐        │ │
│  │  │ Onglet1│  │ Onglet2│        │ │
│  │  │ pts/0  │  │ pts/1  │        │ │
│  │  │ bash   │  │ bash   │        │ │
│  │  └────────┘  └────────┘        │ │
│  └──────────────────────────────────┘ │
│                                        │
└────────────────────────────────────────┘
          ↓
    /dev/pts/0, /dev/pts/1
          ↓
       Shell (bash)
```

---

### Lancer Terminal Emulator

**Méthodes** :

```bash
# 1. Menu Applications → Terminal

# 2. Raccourci clavier (selon distro)
Ctrl + Alt + T  # Ubuntu/GNOME
Super + Enter   # i3, Sway

# 3. Commande (depuis terminal existant)
gnome-terminal &
konsole &
xterm &

# 4. Lancer avec commande directe
gnome-terminal -- bash -c "htop"
```

---

### Multiples Onglets/Splits

**GNOME Terminal** :
```
Ctrl + Shift + T  → Nouvel onglet
Ctrl + Shift + W  → Fermer onglet
Ctrl + PageUp/Down → Naviguer onglets
```

**Terminator** (split avancé) :
```
Ctrl + Shift + E  → Split vertical
Ctrl + Shift + O  → Split horizontal
Ctrl + Tab        → Naviguer splits
```

---

## 🌐 Connexion Graphique Distante

### Vue d'Ensemble

**Objectif** : Accéder **interface graphique** système distant

**Protocoles principaux** :

| Protocole | Type | Port | Plateformes | Chiffré |
|-----------|------|------|-------------|---------|
| **VNC** | Virtual Network Computing | 5900+ | Multi-plateforme | ⚠️ Non (par défaut) |
| **RDP** | Remote Desktop Protocol | 3389 | Windows (Linux via xrdp) | ✅ Oui |
| **X11 Forwarding** | X Window System | 22 (SSH) | Linux/Unix | ✅ Oui (via SSH) |
| **XRDP** | RDP pour Linux | 3389 | Linux | ✅ Oui |
| **SPICE** | Simple Protocol for Independent Computing Environments | 5900 | Linux (KVM/QEMU) | ✅ Oui |

---

### VNC : Virtual Network Computing

**Fonctionnement** :

```
┌─────────────────┐               ┌─────────────────┐
│  Serveur Linux  │               │   Client PC     │
│                 │               │                 │
│  ┌───────────┐  │               │  ┌───────────┐  │
│  │ VNC Server│  │←──── VNC ────→│  │VNC Viewer │  │
│  │(port 5901)│  │   (réseau)    │  │(TightVNC) │  │
│  └───────────┘  │               │  └───────────┘  │
│       ↓         │               │        ↓        │
│  ┌───────────┐  │               │  ┌───────────┐  │
│  │ Bureau    │  │               │  │Affichage  │  │
│  │ GNOME/KDE │  │               │  │bureau     │  │
│  └───────────┘  │               │  │distant    │  │
└─────────────────┘               └─────────────────┘
```

**Serveurs VNC** :
- **TigerVNC** (moderne, performant)
- **TightVNC** (compression efficace)
- **x11vnc** (partage session X11 existante)
- **RealVNC** (commercial)

**Clients VNC** :
- **TigerVNC Viewer** (Linux/Windows/macOS)
- **RealVNC Viewer** (multi-plateforme)
- **Remmina** (Linux, supporte VNC/RDP/SSH)
- **VNC Viewer** (navigateur web)

---

### Installation VNC (Exemple TigerVNC)

**Serveur Ubuntu/Debian** :
```bash
# Installer serveur VNC
sudo apt-get install tigervnc-standalone-server tigervnc-common

# Démarrer serveur VNC (display :1 = port 5901)
vncserver :1
# Password: ******
# Verify: ******

# Configurer bureau
echo "exec startxfce4" > ~/.vnc/xstartup
chmod +x ~/.vnc/xstartup

# Redémarrer VNC
vncserver -kill :1
vncserver :1 -geometry 1920x1080 -depth 24

# Vérifier port
ss -tlnp | grep 5901
# LISTEN  0.0.0.0:5901
```

**Client Windows/Linux** :
```bash
# Linux : Installer viewer
sudo apt-get install tigervnc-viewer

# Connexion
vncviewer 192.168.1.100:5901
# Password: ******

# Windows : TigerVNC Viewer
# Télécharger depuis https://tigervnc.org
# Remote Host: 192.168.1.100:5901
```

---

### RDP : Remote Desktop Protocol

**Microsoft RDP** (Windows natif) :

**Serveur Windows** :
```
Paramètres → Système → Bureau à distance
Activer "Autoriser les connexions à distance"
Port 3389 (TCP)
```

**Client Windows** :
```
mstsc.exe  (Microsoft Terminal Services Client)
Ordinateur : 192.168.1.100
Utilisateur : domain\username
```

---

### XRDP : RDP pour Linux

**Serveur Linux** :
```bash
# Installer XRDP
sudo apt-get install xrdp

# Démarrer service
sudo systemctl start xrdp
sudo systemctl enable xrdp

# Vérifier port 3389
sudo ss -tlnp | grep 3389
# LISTEN  0.0.0.0:3389

# Firewall
sudo ufw allow 3389/tcp
```

**Client Windows** :
```
mstsc.exe
Ordinateur : 192.168.1.100:3389
Connexion avec user/password Linux
```

**Avantage** : Client RDP natif Windows (pas install nécessaire)

---

### X11 Forwarding via SSH

**Concept** : Afficher applications graphiques distantes **via SSH**

**Serveur Linux** :
```bash
# Activer X11Forwarding dans SSH
sudo nano /etc/ssh/sshd_config
# X11Forwarding yes

sudo systemctl restart sshd
```

**Client Linux/macOS** :
```bash
# Connexion SSH avec X11 forwarding
ssh -X user@192.168.1.100

# Lancer application graphique
firefox &
# Firefox s'affiche localement, exécute sur serveur distant !

# Haute fidélité (trusted)
ssh -Y user@192.168.1.100
gedit &
```

**Client Windows** :
```bash
# Installer X Server Windows (VcXsrv, Xming)
# 1. Lancer VcXsrv
# 2. SSH avec X11
ssh -X user@192.168.1.100
# 3. Exporter DISPLAY
export DISPLAY=localhost:0.0
firefox &
```

---

### Comparaison Protocoles Graphiques

| Aspect | VNC | RDP | X11 Forwarding |
|--------|-----|-----|----------------|
| **Chiffrement** | ⚠️ Non (tunnel SSH recommandé) | ✅ Oui | ✅ Oui (via SSH) |
| **Performance** | 🟡 Moyen | 🟢 Bon | 🟢 Bon (apps légères) |
| **Compression** | ✅ Oui | ✅ Oui | ⚠️ Limitée |
| **Audio** | ⚠️ Extension | ✅ Oui | ⚠️ Extension |
| **Presse-papier** | ✅ Oui | ✅ Oui | ✅ Oui |
| **Multi-utilisateur** | ✅ Sessions séparées | ✅ Sessions séparées | ✅ Oui |
| **Usage** | Accès bureau complet | Accès bureau complet | Applications isolées |

---

## 🔐 Connexion Texte Distante : SSH

### SSH : Secure SHell

**SSH** = **S**ecure **SH**ell

**Rôle** : Connexion **sécurisée** (chiffrée) à système distant en mode texte

**vs Telnet** (ancien, **obsolète**) :

| Aspect | Telnet | SSH |
|--------|--------|-----|
| **Chiffrement** | ❌ **Non** (texte clair) | ✅ **Oui** (AES, ChaCha20) |
| **Port** | 23 | 22 |
| **Authentification** | Mot de passe clair | Mot de passe chiffré + clés publiques |
| **Sécurité** | 🔴 **Dangereuse** | 🟢 **Sécurisée** |
| **Usage moderne** | ⛔ **Obsolète** | ✅ **Standard** |

**💡 Règle** : **JAMAIS** utiliser Telnet (mots de passe interceptables)

---

### Architecture SSH : Client/Serveur

**Daemon SSH** (serveur) :

```
┌─────────────────────────────────┐
│      Serveur Linux              │
│                                 │
│  ┌──────────────────────────┐  │
│  │   sshd (daemon)          │  │ ← Service SSH
│  │   Port: 22               │  │
│  │   /etc/ssh/sshd_config   │  │ ← Configuration serveur
│  └──────────────────────────┘  │
│              ↓                  │
│  Authentification user          │
│  Shell bash/zsh                 │
└─────────────────────────────────┘
          ↑
          │ Connexion chiffrée
          │ (port 22)
          ↓
┌─────────────────────────────────┐
│      Client (PC utilisateur)    │
│                                 │
│  ┌──────────────────────────┐  │
│  │   ssh (client)           │  │ ← Client SSH
│  │   ~/.ssh/config          │  │ ← Configuration client
│  │   ~/.ssh/id_rsa          │  │ ← Clé privée
│  └──────────────────────────┘  │
└─────────────────────────────────┘
```

---

### SSH Server : Daemon sshd

**Installation serveur** (Ubuntu/Debian) :

```bash
# Installer OpenSSH Server
sudo apt-get update
sudo apt-get install openssh-server

# Démarrer service
sudo systemctl start sshd
sudo systemctl enable sshd

# Vérifier statut
sudo systemctl status sshd
# ● ssh.service - OpenSSH server daemon
#    Active: active (running)

# Vérifier port 22
sudo ss -tlnp | grep :22
# LISTEN  0.0.0.0:22  users:(("sshd",pid=1234,fd=3))
```

**RHEL/CentOS** :
```bash
sudo dnf install openssh-server
sudo systemctl start sshd
sudo systemctl enable sshd
```

**openSUSE** :
```bash
sudo zypper install openssh
sudo systemctl start sshd
sudo systemctl enable sshd
```

---

### Fichier Configuration Serveur

**Emplacement** : `/etc/ssh/sshd_config`

**Options importantes** :

```bash
# Port SSH (défaut: 22, changeable pour sécurité)
Port 22

# Autoriser root à se connecter (déconseillé)
PermitRootLogin no

# Authentification par mot de passe
PasswordAuthentication yes

# Authentification par clé publique (recommandé)
PubkeyAuthentication yes

# X11 Forwarding (GUI via SSH)
X11Forwarding yes

# Timeout inactivité
ClientAliveInterval 300
ClientAliveCountMax 2

# Banner personnalisé
Banner /etc/ssh/banner.txt
```

**Recharger après modification** :
```bash
sudo systemctl reload sshd
```

---

### SSH Client : Disponibilité Plateformes

#### Linux / macOS : Client Natif ✅

**Linux** :
```bash
# Client SSH inclus par défaut (OpenSSH)
which ssh
# /usr/bin/ssh

ssh --version
# OpenSSH_9.0p1 Ubuntu-1ubuntu7, OpenSSL 3.0.2

# Connexion
ssh user@192.168.1.100
```

**macOS** :
```bash
# Client SSH natif (BSD OpenSSH)
ssh user@serveur.example.com
```

---

#### Windows : Évolution Clients

**Avant Windows 10** (historique) :

```
❌ Pas de client SSH natif
Solution : PuTTY

PuTTY = Client SSH tiers (open-source)
- Télécharger : https://www.putty.org
- putty.exe → Interface graphique
  * Host Name: 192.168.1.100
  * Port: 22
  * Connection Type: SSH
- plink.exe → Client ligne de commande
```

**Windows 10+ (1809+)** : Client SSH Intégré ✅

```powershell
# Client OpenSSH inclus (optionnel, souvent pré-installé)
# Vérifier installation
Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH.Client*'
# Name  : OpenSSH.Client~~~~0.0.1.0
# State : Installed

# Si non installé
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0

# Utilisation (PowerShell/CMD)
ssh user@192.168.1.100
# Password: ******
```

**Windows Terminal** (moderne, recommandé) :
```powershell
# Windows 11 : Pré-installé
# Windows 10 : Microsoft Store → Windows Terminal

# Onglets multiples, thèmes, profils SSH
# Fichier settings.json :
{
  "profiles": {
    "list": [
      {
        "name": "Serveur Prod",
        "commandline": "ssh user@prod.example.com"
      }
    ]
  }
}
```

---

### Comparaison Clients SSH Windows

| Client | Époque | Type | Avantages | Inconvénients |
|--------|--------|------|-----------|---------------|
| **PuTTY** | 1999-présent | Tiers | ✅ GUI simple, portable | ⚠️ Pas natif, config séparée |
| **OpenSSH (Win10+)** | 2018-présent | Natif | ✅ Natif, compatible Linux | ⚠️ Windows 10 1809+ requis |
| **Windows Terminal** | 2019-présent | Natif | ✅ Moderne, onglets, UTF-8 | ⚠️ Windows 10+ |
| **WSL2 + SSH** | 2019-présent | Subsystem | ✅ OpenSSH Linux complet | ⚠️ WSL2 requis |

**💡 Recommandation 2026** : **Windows Terminal** + **OpenSSH natif**

---

### Connexion SSH Basique

**Syntaxe** :
```bash
ssh utilisateur@serveur
```

**Exemples** :

```bash
# Par adresse IP
ssh john@192.168.1.100
# Password: ******

# Par nom d'hôte
ssh admin@web-server.example.com

# Port non-standard
ssh user@serveur -p 2222

# Verbose (debug)
ssh -v user@serveur
```

**Première connexion** (fingerprint) :
```
The authenticity of host '192.168.1.100 (192.168.1.100)' can't be established.
ED25519 key fingerprint is SHA256:abcd1234...xyz9876.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.1.100' (ED25519) to the list of known hosts.
```

**Fichier connus** : `~/.ssh/known_hosts`

---

### Exécuter Commande Distante

```bash
# Commande unique (sans session interactive)
ssh user@serveur 'uptime'
# 10:30:45 up 42 days, 3:15, 2 users, load average: 0.05, 0.10, 0.08

# Plusieurs commandes
ssh user@serveur 'df -h; free -m'

# Script distant
ssh user@serveur 'bash -s' < script-local.sh

# Copie fichier (alternative scp)
cat fichier.txt | ssh user@serveur 'cat > /tmp/fichier-distant.txt'
```

---

### Authentification Clé Publique

**Avantages** :
- ✅ **Pas de mot de passe** à taper
- ✅ **Plus sécurisé** (clé 2048-4096 bits)
- ✅ **Automatisation** scripts/cron

**Génération clé** :

```bash
# Générer paire clés RSA 4096 bits
ssh-keygen -t rsa -b 4096 -C "mon-email@example.com"
# Generating public/private rsa key pair.
# Enter file in which to save the key (~/.ssh/id_rsa): [Enter]
# Enter passphrase (empty for no passphrase): [Enter ou mot de passe]
# Your public key has been saved in ~/.ssh/id_rsa.pub

# Fichiers générés
ls -l ~/.ssh/
# -rw------- 1 user user 3243 Feb  2 10:00 id_rsa        ← Clé PRIVÉE (secret)
# -rw-r--r-- 1 user user  743 Feb  2 10:00 id_rsa.pub    ← Clé PUBLIQUE

# Copier clé publique sur serveur
ssh-copy-id user@serveur
# Password: ******  (dernière fois !)
# Now try logging into the machine...

# Connexion sans mot de passe
ssh user@serveur
# (connecté directement)
```

**Manuel** :
```bash
# Afficher clé publique
cat ~/.ssh/id_rsa.pub
# ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQC... mon-email@example.com

# Sur serveur : Ajouter à authorized_keys
echo "ssh-rsa AAAAB3Nz..." >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

---

### Fichier Configuration Client SSH

**Emplacement** : `~/.ssh/config`

**Exemple** :

```bash
# Serveur production
Host prod
  HostName prod.example.com
  User admin
  Port 22
  IdentityFile ~/.ssh/id_rsa_prod

# Serveur dev (raccourci)
Host dev
  HostName 192.168.1.100
  User developer
  Port 2222

# Bastion/Jump host
Host bastion
  HostName bastion.example.com
  User jumpuser
  
Host internal-server
  HostName 10.0.1.50
  User admin
  ProxyJump bastion
```

**Usage** :
```bash
# Au lieu de
ssh admin@prod.example.com -i ~/.ssh/id_rsa_prod

# Simplement
ssh prod

# Accès via bastion (jump)
ssh internal-server
# (connecte via bastion automatiquement)
```

---

## 📋 Antisèche - Connexions et Terminaux

| Concept | Type | Accès | Exemple |
|---------|------|-------|---------|
| **Console** | Physique/Local | Écran+clavier | Messages boot |
| **TTY1-6** | Virtual Terminal texte | Ctrl+Alt+F1-6 | Login texte |
| **TTY7** | Virtual Terminal graphique | Ctrl+Alt+F7 | Bureau GNOME/KDE |
| **Terminal Emulator** | Application GUI | Menu/Raccourci | GNOME Terminal, Konsole |
| **PTY/PTS** | Pseudo-terminal | Émulé | `/dev/pts/0` |
| **VNC** | Graphique distant | Viewer VNC | Port 5900+ |
| **RDP** | Graphique distant | Client RDP | Port 3389 |
| **X11 Forwarding** | Apps graphiques SSH | `ssh -X` | Firefox distant |
| **SSH** | Texte distant | Client SSH | Port 22 |
| **Telnet** | ⛔ Obsolète | ⛔ Non sécurisé | Port 23 |

---

## 🎓 Points Clés pour l'Examen

✅ **4 modes connexion** : Local text, Local graphical, Remote text, Remote graphical  
✅ **Console** : Affichage physique direct, messages boot/kernel  
✅ **TTY** : TeleTYpewriter, terminal physique/virtuel  
✅ **Virtual Terminals** : TTY1-6 (texte), TTY7 (graphique), accès Ctrl+Alt+F1-F7  
✅ **Terminal Emulator** : Application GUI émulant terminal (GNOME Terminal, Konsole, xterm)  
✅ **PTY/PTS** : Pseudo-terminal (Terminal Emulator, SSH) vs TTY (console directe)  
✅ **VNC** : Virtual Network Computing, accès bureau distant, port 5900+  
✅ **RDP** : Remote Desktop Protocol (Windows), port 3389, Linux via XRDP  
✅ **X11 Forwarding** : Apps graphiques distantes via SSH (`ssh -X`)  
✅ **SSH** : Secure Shell, connexion texte chiffrée, port 22  
✅ **Telnet** : Obsolète, non chiffré (⛔ ne jamais utiliser)  
✅ **SSH daemon** : `sshd` serveur, `/etc/ssh/sshd_config`  
✅ **SSH client** : Natif Linux/macOS, Windows 10+ intégré, ancien PuTTY  
✅ **ssh-keygen** : Générer clés RSA/ED25519 pour auth sans mot de passe  
✅ **ssh-copy-id** : Copier clé publique sur serveur distant  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Interface Graphique Plantée

```bash
Problème : Bureau GNOME figé, souris ne bouge plus

# 1. Basculer TTY2
Ctrl + Alt + F2

# 2. Connexion
username: john
password: ******

# 3. Identifier processus graphique
ps aux | grep gnome-shell
# john  1234  5.0  2.3  /usr/bin/gnome-shell

# 4. Tuer processus
kill -9 1234

# 5. Redémarrer gestionnaire affichage
sudo systemctl restart gdm

# 6. Retour interface graphique
Ctrl + Alt + F7

# (Bureau redémarre, session perdue mais système sauvé)
```

---

### Scénario 2 : Surveillance Multi-TTY

```bash
# Serveur web avec forte charge

# TTY1 : Logs Apache temps réel
Ctrl + Alt + F1
tail -f /var/log/apache2/access.log

# TTY2 : Monitoring CPU/RAM
Ctrl + Alt + F2
htop

# TTY3 : Monitoring réseau
Ctrl + Alt + F3
iftop -i eth0

# TTY4 : Session admin maintenance
Ctrl + Alt + F4
# Disponible pour commandes urgentes

# Naviguer entre consoles selon besoin
```

---

### Scénario 3 : Connexion SSH depuis Windows

```powershell
# Windows 10/11 (PowerShell)

# Vérifier client SSH
ssh -V
# OpenSSH_for_Windows_8.1p1

# Première connexion serveur
ssh admin@192.168.1.100
# The authenticity of host '192.168.1.100' can't be established.
# ED25519 key fingerprint is SHA256:abc123...
# Are you sure? yes
# Password: ******

# Générer clé SSH Windows
ssh-keygen -t ed25519 -C "windows-pc@example.com"
# Clés dans C:\Users\Username\.ssh\

# Copier clé sur serveur (manuel car pas ssh-copy-id Windows)
type C:\Users\Username\.ssh\id_ed25519.pub | ssh admin@192.168.1.100 "cat >> ~/.ssh/authorized_keys"
# Password: ******

# Connexion sans mot de passe
ssh admin@192.168.1.100
# (connecté directement)
```

---

### Scénario 4 : VNC Accès Bureau Distant

```bash
# Serveur Ubuntu (installer VNC)

# 1. Installer TigerVNC
sudo apt-get install tigervnc-standalone-server tigervnc-common

# 2. Configurer mot de passe VNC
vncpasswd
# Password: ******
# Verify: ******
# Would you like to enter a view-only password (y/n)? n

# 3. Configuration bureau XFCE
mkdir -p ~/.vnc
echo "exec startxfce4" > ~/.vnc/xstartup
chmod +x ~/.vnc/xstartup

# 4. Démarrer VNC display :1 (port 5901)
vncserver :1 -geometry 1920x1080 -depth 24
# Desktop 'TigerVNC' started on display :1

# 5. Vérifier
ss -tlnp | grep 5901
# LISTEN  0.0.0.0:5901

# 6. Firewall
sudo ufw allow 5901/tcp

# Client (Windows/Linux)
# Télécharger TigerVNC Viewer
# Remote: 192.168.1.100:5901
# Password: ******
# (Bureau XFCE distant s'affiche)
```

---

### Scénario 5 : X11 Forwarding Application Graphique

```bash
# Serveur Linux : Activer X11 Forwarding

# 1. Config SSH serveur
sudo nano /etc/ssh/sshd_config
# X11Forwarding yes
# X11DisplayOffset 10

sudo systemctl restart sshd

# Client Linux : Lancer app graphique distante

# 2. Connexion SSH avec X11
ssh -X user@serveur.example.com

# 3. Vérifier DISPLAY
echo $DISPLAY
# localhost:10.0

# 4. Lancer application graphique
firefox &
# (Firefox s'ouvre localement mais exécute sur serveur)

gedit ~/fichier-distant.txt &
# (Éditeur texte graphique distant)

# 5. Haute fidélité (trusted X11)
ssh -Y user@serveur.example.com
gimp &
# (GIMP distant avec accélération complète)
```

---

### Scénario 6 : Configuration SSH Client Avancée

```bash
# ~/.ssh/config : Simplifier connexions

cat > ~/.ssh/config << 'EOF'
# Serveur production
Host prod
  HostName prod.example.com
  User admin
  Port 22
  IdentityFile ~/.ssh/id_rsa_prod
  ServerAliveInterval 60
  
# Serveur dev local
Host dev
  HostName 192.168.1.100
  User developer
  Port 2222
  
# GitHub
Host github
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_github
  
# Bastion + serveurs internes
Host bastion
  HostName bastion.example.com
  User jumpuser
  IdentityFile ~/.ssh/id_rsa_bastion
  
Host web-internal
  HostName 10.0.1.10
  User webadmin
  ProxyJump bastion
  
Host db-internal
  HostName 10.0.1.20
  User dbadmin
  ProxyJump bastion
EOF

chmod 600 ~/.ssh/config

# Usage simplifié
ssh prod           # Au lieu de ssh admin@prod.example.com -i ~/.ssh/id_rsa_prod
ssh dev            # Au lieu de ssh developer@192.168.1.100 -p 2222
ssh web-internal   # Passe automatiquement par bastion
```

---

### Scénario 7 : Diagnostic Serveur Multi-Console

```bash
# Serveur production sans GUI (headless)

# Console 1 (TTY1) : Logs système temps réel
journalctl -f -u nginx -u mysql
# Feb 02 10:30:01 server nginx[1234]: Connection from 203.0.113.50
# Feb 02 10:30:05 server mysql[5678]: Query OK, 1 row affected

# Console 2 (TTY2) : Monitoring processus
htop
# (Vue CPU/RAM temps réel)

# Console 3 (TTY3) : Session root maintenance
sudo -i
# Disponible pour interventions urgentes

# Console 4 (TTY4) : Monitoring réseau
iftop -i ens33
# (Bande passante temps réel)

# Basculer selon besoin :
Alt + F1  # Logs
Alt + F2  # htop
Alt + F3  # Root
Alt + F4  # Réseau
```

---

### Scénario 8 : Tunnel SSH pour Sécuriser VNC

```bash
# Problème : VNC non chiffré par défaut

# Serveur : VNC écoute localhost uniquement
vncserver :1 -localhost yes
# (Port 5901 accessible uniquement en local)

# Client : Tunnel SSH
ssh -L 5901:localhost:5901 user@serveur.example.com
# (Laisse terminal ouvert)

# Nouveau terminal : Connexion VNC via tunnel
vncviewer localhost:5901
# (Trafic VNC chiffré via SSH tunnel)
```

**Avantages** :
- ✅ VNC chiffré (via SSH)
- ✅ Pas d'exposition port VNC sur Internet
- ✅ Authentification SSH (clés)

---

### Scénario 9 : Terminal Emulator Multi-Split

```bash
# Terminator : Split écran puissant

# 1. Installer
sudo apt-get install terminator

# 2. Lancer
terminator &

# 3. Splits
Ctrl + Shift + E  # Split vertical
Ctrl + Shift + O  # Split horizontal

# Layout exemple :
┌──────────────┬──────────────┐
│              │              │
│   htop       │   tail -f    │
│              │   syslog     │
├──────────────┴──────────────┤
│                             │
│   Session SSH serveur       │
│                             │
└─────────────────────────────┘

# Navigation
Ctrl + Shift + Tab  # Pane suivante
Ctrl + Shift + W    # Fermer pane

# Broadcast (même commande tous panes)
Ctrl + Shift + G  # Toggle broadcast
# Taper commande → Exécute dans tous panes
```

---

### Scénario 10 : Dépannage SSH Connexion Refusée

```bash
# Problème : ssh user@serveur
# ssh: connect to host serveur port 22: Connection refused

# Diagnostic serveur (console locale ou TTY)

# 1. Vérifier service SSH actif
sudo systemctl status sshd
# ○ ssh.service - OpenSSH server daemon
#    Loaded: loaded
#    Active: inactive (dead)  ← PROBLÈME !

# 2. Démarrer SSH
sudo systemctl start sshd
sudo systemctl enable sshd

# 3. Vérifier port écoute
sudo ss -tlnp | grep :22
# LISTEN  0.0.0.0:22  users:(("sshd",pid=1234))

# 4. Vérifier firewall
sudo ufw status
# Status: active
# To           Action
# 22/tcp       DENY  ← PROBLÈME !

sudo ufw allow 22/tcp
sudo ufw reload

# 5. Tester depuis client
ssh user@serveur
# (Connexion réussie)

# Logs debug serveur
sudo journalctl -u sshd -f
# (Voir tentatives connexion temps réel)
```

---

## ⚠️ Erreurs Courantes

### Erreur 1 : TTY Bloqué (Aucune Réaction)

```
Symptôme : TTY2 affiche login, mais clavier ne répond pas

Cause : Terminal gelé (Ctrl+S accidentel)

Solution :
Ctrl + Q  # Déverrouiller (toggle Ctrl+S freeze)
```

---

### Erreur 2 : Permission Denied (publickey)

```
ssh user@serveur
# Permission denied (publickey).

Causes possibles :
1. Clé publique pas sur serveur
2. Permissions ~/.ssh incorrectes
3. PasswordAuthentication désactivé

Solutions :

# Autoriser mot de passe temporairement (serveur)
sudo nano /etc/ssh/sshd_config
# PasswordAuthentication yes
sudo systemctl restart sshd

# Vérifier permissions (serveur)
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys

# Copier clé (client)
ssh-copy-id user@serveur
```

---

### Erreur 3 : VNC Écran Noir

```
Symptôme : Connexion VNC réussie mais écran noir

Cause : Bureau pas configuré dans xstartup

Solution :
# Serveur
cat > ~/.vnc/xstartup << 'EOF'
#!/bin/bash
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
exec startxfce4
EOF

chmod +x ~/.vnc/xstartup

vncserver -kill :1
vncserver :1
```

---

### Erreur 4 : WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED

```
ssh user@serveur
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!

Causes :
- Serveur réinstallé (nouvelle clé)
- IP réutilisée par autre machine
- ⚠️ Attaque Man-in-the-Middle (rare)

Solution (si légitime) :
ssh-keygen -R serveur.example.com
# Host serveur.example.com found: line 12
# /home/user/.ssh/known_hosts updated.

ssh user@serveur
# (Nouvelle fingerprint à valider)
```

---

### Erreur 5 : X11 Forwarding Ne Fonctionne Pas

```
ssh -X user@serveur
firefox &
# Error: Can't open display

Solutions :

# 1. Vérifier DISPLAY
echo $DISPLAY
# (vide) → PROBLÈME

# 2. Serveur : Activer X11Forwarding
sudo nano /etc/ssh/sshd_config
# X11Forwarding yes
# X11UseLocalhost yes
sudo systemctl restart sshd

# 3. Client : Installer X11 (si manquant)
# macOS
brew install xquartz  # Relancer session

# Linux
sudo apt-get install xauth

# 4. Reconnexion
ssh -X user@serveur
echo $DISPLAY
# localhost:10.0  ✓
```

---

## 📜 Historique des Commandes (Command History)

### Concept History

**Historique** = Journal automatique de **toutes les commandes** tapées

**Rôle** :
- ✅ **Retrouver** commandes précédentes
- ✅ **Réexécuter** sans retaper
- ✅ **Audit** activités (qui a fait quoi)
- ✅ **Apprentissage** (revoir syntaxe)

**Analogie** 🕰️ :
- **History** = Historique navigateur web (pages visitées)
- **Prompt** = Barre d'adresse (suggestions basées sur historique)

---

### Architecture Historique Bash

```
┌─────────────────────────────────────────┐
│         Session Bash Active             │
│                                         │
│  Commande tapée                         │
│       ↓                                 │
│  ┌──────────────────────────────────┐  │
│  │  Mémoire RAM (buffer)            │  │ ← Historique session actuelle
│  │  1. ls -la                       │  │
│  │  2. cd /var/log                  │  │
│  │  3. tail -f syslog               │  │
│  └──────────────────────────────────┘  │
│                                         │
└─────────────────────────────────────────┘
                  ↓
         (À la fin de session)
                  ↓
┌─────────────────────────────────────────┐
│  Fichier ~/.bash_history                │ ← Sauvegarde permanente
│                                         │
│  ls -la                                 │
│  cd /var/log                            │
│  tail -f syslog                         │
│  systemctl status nginx                 │
│  ... (500 dernières commandes)          │
└─────────────────────────────────────────┘
```

**⚠️ Important** : Historique sauvegardé à la **fermeture** session !

---

## 🔍 Commande history

### Syntaxe Basique

```bash
history
```

**Résultat** :
```
    1  ls -la
    2  cd /var/log
    3  tail -f syslog
    4  sudo systemctl restart nginx
    5  grep error /var/log/nginx/error.log
    6  df -h
    7  free -m
    8  history
```

**Format** : `NuméroLigne  Commande`

---

### Afficher N Dernières Commandes

```bash
# 10 dernières commandes
history 10

# 5 dernières commandes
history 5
```

**Résultat** :
```
  496  cd /var/www
  497  ls -l
  498  git status
  499  git pull
  500  history 5
```

---

### Rechercher dans Historique avec grep

**Syntaxe** :
```bash
history | grep motif
```

**Exemples** :

```bash
# Toutes commandes SSH
history | grep ssh
# 45  ssh user@192.168.1.100
# 67  ssh-keygen -t ed25519
# 89  ssh-copy-id user@serveur

# Toutes commandes sudo
history | grep sudo
# 23  sudo apt-get update
# 45  sudo systemctl restart nginx
# 78  sudo tail -f /var/log/syslog

# Commandes avec fichier spécifique
history | grep nginx.conf
# 34  sudo nano /etc/nginx/nginx.conf
# 56  sudo nginx -t
# 67  cat /etc/nginx/nginx.conf

# Recherche insensible casse
history | grep -i DOCKER
# 12  docker ps
# 34  sudo systemctl start docker
# 56  DOCKER_HOST=tcp://localhost:2375
```

---

### Combiner Recherches

```bash
# Commandes apt install
history | grep "apt.*install"
# 23  sudo apt install nginx
# 45  sudo apt install docker.io

# Commandes systemctl start/restart
history | grep "systemctl.*start\|restart"
# 12  sudo systemctl start nginx
# 34  sudo systemctl restart mysql

# Exclure commandes history
history | grep docker | grep -v history
# (Évite ligne "history | grep docker")
```

---

## 📂 Fichier .bash_history

### Emplacement

**Fichier** : `~/.bash_history` (home utilisateur)

**Chemin complet** :
```bash
/home/username/.bash_history
```

**Variable** :
```bash
echo $HISTFILE
# /home/john/.bash_history
```

---

### Visualiser Fichier

```bash
# Afficher contenu
cat ~/.bash_history

# Dernières 20 lignes
tail -20 ~/.bash_history

# Rechercher dans fichier
grep ssh ~/.bash_history

# Compter commandes
wc -l ~/.bash_history
# 500 /home/john/.bash_history
```

---

### Moment Sauvegarde

**⚠️ Critique** : Historique sauvegardé à la **fermeture session**

**Démo** :

```bash
# Terminal 1 : Session active
$ echo "Test historique"
$ ls /tmp
$ pwd
# (3 nouvelles commandes)

# Vérifier fichier (autre terminal)
$ tail -3 ~/.bash_history
# (Ne contient PAS les 3 commandes récentes)

# Terminal 1 : Forcer sauvegarde
$ history -a  # Append (ajoute) historique actuel au fichier

# Vérifier fichier
$ tail -3 ~/.bash_history
# echo "Test historique"
# ls /tmp
# pwd
# (Maintenant présent !)

# OU fermer session (exit/logout)
$ exit

# Rouvrir terminal
$ tail -3 ~/.bash_history
# (Contient maintenant les commandes)
```

---

### Situations Multiples Sessions

**Problème** : Plusieurs terminaux ouverts simultanément

```
Terminal 1 (ouvert 10h00) :
- ls /var
- cd /tmp
(Historique en RAM)

Terminal 2 (ouvert 10h30) :
- ps aux
- top
(Historique en RAM séparé)

Fermeture Terminal 1 (10h45) :
→ Sauvegarde dans ~/.bash_history (écrase fichier)

Fermeture Terminal 2 (11h00) :
→ Sauvegarde dans ~/.bash_history (écrase fichier)
⚠️ Historique Terminal 1 PERDU !
```

**Solution** : Variables `HISTCONTROL` (voir section suivante)

---

## ⬆️⬇️ Navigation Flèches Clavier

### Flèches Haut/Bas

**Raccourcis** :

```bash
# Flèche HAUT (↑)
# → Commande précédente

# Flèche BAS (↓)
# → Commande suivante (si remonté avec ↑)
```

**Usage** :

```
Prompt vide :
$ _

↑ (une fois) :
$ history                    ← Dernière commande

↑ (deux fois) :
$ grep ssh ~/.bash_history   ← Avant-dernière

↑ (trois fois) :
$ cat /var/log/syslog        ← Avant-avant-dernière

↓ (une fois) :
$ grep ssh ~/.bash_history   ← Redescendre

↓ (deux fois) :
$ history                    ← Revenir

↓ (jusqu'au bout) :
$ _                          ← Prompt vide (ligne actuelle)
```

**💡 Pratique** : Remonter historique sans `history | grep`

---

### Éditer Commande Récupérée

```bash
# Récupérer commande
↑ (plusieurs fois)
$ ssh user@192.168.1.100

# Éditer avec flèches ←→ et touches
Début ligne : Home
Fin ligne   : End
← → : Naviguer caractères
Ctrl+A : Début ligne
Ctrl+E : Fin ligne
Backspace : Effacer caractère

# Modifier
$ ssh user@192.168.1.200  ← Changer IP
Enter → Exécuter
```

---

## 🔧 Variables Environnement Historique

### HISTSIZE : Taille Historique en Mémoire

**Rôle** : Nombre commandes en **RAM** (session active)

```bash
# Voir valeur actuelle
echo $HISTSIZE
# 1000  (défaut Ubuntu)

# Modifier (session actuelle)
export HISTSIZE=5000

# Permanent (~/.bashrc)
echo "export HISTSIZE=5000" >> ~/.bashrc
source ~/.bashrc
```

**Effet** : Commande `history` affiche 5000 lignes max

---

### HISTFILESIZE : Taille Fichier .bash_history

**Rôle** : Nombre commandes dans **fichier** ~/.bash_history

```bash
# Voir valeur
echo $HISTFILESIZE
# 2000  (défaut Ubuntu)

# Modifier
export HISTFILESIZE=10000

# Permanent
echo "export HISTFILESIZE=10000" >> ~/.bashrc
```

**Différence HISTSIZE vs HISTFILESIZE** :

| Variable | Portée | Exemple |
|----------|--------|---------|
| **HISTSIZE** | Mémoire RAM (session) | 1000 commandes affichées par `history` |
| **HISTFILESIZE** | Fichier ~/.bash_history | 2000 commandes sauvegardées sur disque |

---

### HISTCONTROL : Contrôle Doublons

**Rôle** : Filtrer commandes enregistrées

**Valeurs** :

| Valeur | Effet | Exemple |
|--------|-------|---------|
| `ignorespace` | Ignore commandes débutant par **espace** | ` rm -rf /tmp/secret` |
| `ignoredups` | Ignore **doublons** consécutifs | `ls` (répété) → Une seule entrée |
| `ignoreboth` | `ignorespace` **+** `ignoredups` | Combinaison |
| `erasedups` | Efface **tous** doublons (pas que consécutifs) | Historique sans répétition |

---

**Exemples** :

```bash
# Configuration par défaut (Ubuntu)
echo $HISTCONTROL
# ignoredups

# Ignorer commandes avec espace au début
export HISTCONTROL=ignorespace

# Commande avec espace (non enregistrée)
$  cat /etc/shadow  ← Espace au début
# (Ne sera PAS dans history)

# Combiner ignorespace + ignoredups
export HISTCONTROL=ignoreboth

# Permanent
echo "export HISTCONTROL=ignoreboth" >> ~/.bashrc
```

**💡 Sécurité** : `ignorespace` pour commandes sensibles (mots de passe)

---

### HISTIGNORE : Ignorer Patterns

**Rôle** : Liste commandes à **ne jamais** enregistrer

```bash
# Ignorer commandes courantes
export HISTIGNORE="ls:ll:pwd:history:clear:exit"

# Ignorer avec wildcards
export HISTIGNORE="ls*:cd*:pwd:exit"

# Permanent
echo 'export HISTIGNORE="ls:ll:pwd:history:clear:exit"' >> ~/.bashrc
```

**Effet** :
```bash
$ ls -la     # Non enregistré (dans HISTIGNORE)
$ pwd        # Non enregistré
$ history    # Non enregistré
$ df -h      # Enregistré (pas dans HISTIGNORE)
```

---

### HISTTIMEFORMAT : Timestamps

**Rôle** : Ajouter **date/heure** à chaque commande

```bash
# Activer timestamps
export HISTTIMEFORMAT="%F %T "

# Voir historique avec dates
history
# 1  2026-02-02 10:30:45 ssh user@serveur
# 2  2026-02-02 10:35:12 sudo systemctl restart nginx
# 3  2026-02-02 11:00:03 docker ps

# Permanent
echo 'export HISTTIMEFORMAT="%F %T "' >> ~/.bashrc
```

**Formats** :
- `%F` : Date (YYYY-MM-DD)
- `%T` : Heure (HH:MM:SS)
- `%d/%m/%Y %H:%M` : Format français

---

### Configuration Complète ~/.bashrc

```bash
# Ajouter à ~/.bashrc
cat >> ~/.bashrc << 'EOF'

# Historique Bash optimisé
export HISTSIZE=10000              # 10k commandes en mémoire
export HISTFILESIZE=20000          # 20k commandes sur disque
export HISTCONTROL=ignoreboth:erasedups  # Pas de doublons + ignore espace
export HISTIGNORE="ls:ll:pwd:history:clear:exit:bg:fg"  # Ignorer commandes triviales
export HISTTIMEFORMAT="%F %T "     # Timestamps
shopt -s histappend                # Append (pas écrase) historique
export PROMPT_COMMAND="history -a" # Sauvegarde après chaque commande

EOF

# Recharger config
source ~/.bashrc
```

---

## 🚀 Raccourcis Expansion Historique

### !! : Dernière Commande

```bash
# Répéter dernière commande
$ apt-get update
# Permission denied

$ sudo !!
# Équivalent à : sudo apt-get update
# (Exécute avec sudo)
```

---

### !n : Commande Numéro N

```bash
# Voir numéros
$ history
# 345  ls -la
# 346  cd /var/log
# 347  tail -f syslog

# Réexécuter commande 345
$ !345
# ls -la
# (Exécute ls -la)
```

---

### !-n : N Commandes Avant

```bash
# Historique
$ echo "Commande 1"
$ echo "Commande 2"
$ echo "Commande 3"

# 1 commande avant
$ !-1
# echo "Commande 3"

# 2 commandes avant
$ !-2
# echo "Commande 2"

# 3 commandes avant
$ !-3
# echo "Commande 1"
```

---

### !string : Dernière Commande Commençant par String

```bash
$ ssh user@serveur
$ cd /tmp
$ ls -la

# Réexécuter dernière commande ssh
$ !ssh
# ssh user@serveur
# (Reconnexion automatique)

$ systemctl status nginx
$ docker ps
$ systemctl restart mysql

# Dernière commande systemctl
$ !systemctl
# systemctl restart mysql
```

---

### !?string : Dernière Commande Contenant String

```bash
$ grep -r "error" /var/log
$ cd /tmp
$ ls

# Dernière commande contenant "error"
$ !?error
# grep -r "error" /var/log
```

---

### !$ : Dernier Argument Commande Précédente

```bash
$ cat /etc/nginx/nginx.conf
# (Contenu fichier)

$ sudo nano !$
# Équivalent à : sudo nano /etc/nginx/nginx.conf

# Autre exemple
$ ls -la /var/www/html/index.php
$ chmod 644 !$
# chmod 644 /var/www/html/index.php
```

---

### !* : Tous Arguments Commande Précédente

```bash
$ ls /etc/nginx /var/log /tmp

$ echo !*
# echo /etc/nginx /var/log /tmp
# /etc/nginx /var/log /tmp
```

---

### !^ : Premier Argument Commande Précédente

```bash
$ cat fichier1.txt fichier2.txt fichier3.txt

$ nano !^
# nano fichier1.txt
```

---

### Tableau Raccourcis Expansion

| Raccourci | Signification | Exemple |
|-----------|---------------|---------|
| `!!` | Dernière commande | `sudo !!` |
| `!n` | Commande numéro N | `!345` |
| `!-n` | N commandes avant | `!-2` |
| `!string` | Dernière commande débutant par string | `!ssh` |
| `!?string` | Dernière commande contenant string | `!?error` |
| `!$` | Dernier argument | `nano !$` |
| `!*` | Tous arguments | `echo !*` |
| `!^` | Premier argument | `cat !^` |
| `^old^new` | Remplacer dans dernière commande | `^http^https` |

---

### Modificateurs Expansion

```bash
# :p : Afficher sans exécuter (preview)
$ !ssh:p
# ssh user@serveur  (affiché, pas exécuté)

# :s/old/new : Substituer
$ echo hello world
$ !!:s/hello/goodbye
# echo goodbye world

# :gs/old/new : Substituer toutes occurrences
$ echo test test test
$ !!:gs/test/ok
# echo ok ok ok
```

---

## 🔄 Ctrl+R : Recherche Inversée Interactive

### Utilisation

**Raccourci** : `Ctrl + R`

**Fonctionnement** :

```bash
# Appuyer Ctrl+R
(reverse-i-search)`_': 

# Taper début commande (ex: ssh)
(reverse-i-search)`ssh': ssh user@192.168.1.100

# Résultats :
# - Appuyer Enter : Exécuter
# - Appuyer Ctrl+R : Résultat précédent
# - Appuyer ← ou → : Éditer commande
# - Appuyer Ctrl+G : Annuler recherche

# Continuer recherche
(reverse-i-search)`ssh': ssh admin@prod.example.com  ← Ctrl+R

# Résultat plus ancien
(reverse-i-search)`ssh': ssh-keygen -t ed25519  ← Ctrl+R
```

**💡 Très efficace** : Chercher sans `history | grep`

---

### Navigation Ctrl+R

```
Ctrl+R : Lancer recherche
Taper texte : Filtrer
Ctrl+R : Match précédent (remonter)
Ctrl+S : Match suivant (descendre, nécessite stty -ixon)
Enter : Exécuter commande
← → : Éditer sans exécuter
Ctrl+G : Annuler recherche
Esc : Annuler et garder commande affichée
```

---

### Activer Ctrl+S (Recherche Forward)

```bash
# Par défaut, Ctrl+S freeze terminal (flow control)
# Désactiver pour permettre recherche forward

# Session actuelle
stty -ixon

# Permanent
echo "stty -ixon" >> ~/.bashrc
source ~/.bashrc

# Maintenant :
# Ctrl+R : Recherche backward (ancien → récent)
# Ctrl+S : Recherche forward (récent → ancien)
```

---

## ⚙️ Commandes Avancées Historique

### history -c : Effacer Historique Mémoire

```bash
# Effacer historique session actuelle (RAM)
history -c

# Vérifier
history
# (Vide)

# ⚠️ Fichier ~/.bash_history pas effacé
cat ~/.bash_history
# (Contient toujours anciennes commandes)
```

---

### history -d N : Supprimer Ligne N

```bash
# Historique
history
# 345  cat /etc/shadow
# 346  ls -la
# 347  pwd

# Supprimer ligne 345
history -d 345

# Vérifier
history
# 346  ls -la
# 347  pwd
# (Ligne 345 supprimée)
```

---

### history -a : Append Historique au Fichier

```bash
# Forcer sauvegarde sans fermer session
history -a

# Effet : Ajoute commandes actuelles à ~/.bash_history
```

**Usage** : Multiples sessions simultanées (éviter perte)

---

### history -n : Lire Nouvelles Lignes Fichier

```bash
# Autre terminal a ajouté commandes à .bash_history
# Charger ces nouvelles commandes en mémoire

history -n

# Historique actuel enrichi avec nouvelles lignes
```

---

### history -r : Lire Historique Fichier

```bash
# Remplacer historique mémoire par contenu fichier
history -r

# Effet : Historique actuel = ~/.bash_history
```

---

### history -w : Écrire Historique vers Fichier

```bash
# Sauvegarder historique actuel (écrase fichier)
history -w

# Équivalent à fermeture session (mais sans fermer)
```

---

### fc : Edit and Execute Commands

**fc** = **F**ix **C**ommand

**Rôle** : Éditer commande historique dans éditeur puis exécuter

```bash
# Éditer dernière commande
fc

# Éditeur s'ouvre avec dernière commande
# Modifier, sauvegarder, fermer
# → Commande modifiée exécutée automatiquement

# Éditer commande spécifique
fc 345

# Éditer plage commandes
fc 340 345

# Utiliser éditeur spécifique
fc -e nano

# Lister commandes (comme history)
fc -l
fc -l 300 310
```

---

## 📋 Antisèche - Historique Commandes

| Commande/Raccourci | Je veux... | Exemple |
|--------------------|-----------|---------|
| `history` | Afficher historique | `history` |
| `history 10` | 10 dernières commandes | `history 10` |
| `history \| grep ssh` | Chercher dans historique | `history \| grep docker` |
| `↑` | Commande précédente | ↑ (flèche) |
| `↓` | Commande suivante | ↓ (flèche) |
| `Ctrl+R` | Recherche interactive | Ctrl+R puis taper |
| `!!` | Répéter dernière commande | `sudo !!` |
| `!n` | Exécuter commande N | `!345` |
| `!-n` | N commandes avant | `!-2` |
| `!ssh` | Dernière commande ssh | `!systemctl` |
| `!$` | Dernier argument | `nano !$` |
| `!*` | Tous arguments | `rm !*` |
| `history -c` | Effacer historique RAM | `history -c` |
| `history -d N` | Supprimer ligne N | `history -d 345` |
| `history -a` | Sauvegarder maintenant | `history -a` |
| `history -w` | Écraser fichier | `history -w` |
| `fc` | Éditer dernière commande | `fc` |

---

## 🎓 Points Clés pour l'Examen

✅ **history** : Affiche historique commandes numérotées  
✅ **history | grep** : Chercher pattern dans historique  
✅ **~/.bash_history** : Fichier sauvegarde historique (home utilisateur)  
✅ **Sauvegarde** : Historique écrit à la **fermeture session** (pas en temps réel)  
✅ **Flèches ↑↓** : Naviguer historique directement dans prompt  
✅ **HISTSIZE** : Nombre commandes en mémoire (RAM)  
✅ **HISTFILESIZE** : Nombre commandes dans fichier  
✅ **HISTCONTROL** : Contrôle doublons (ignoredups, ignorespace, ignoreboth)  
✅ **HISTIGNORE** : Liste patterns commandes à ignorer  
✅ **HISTTIMEFORMAT** : Ajouter timestamps historique  
✅ **Ctrl+R** : Recherche inversée interactive  
✅ **!!** : Répéter dernière commande (ex: `sudo !!`)  
✅ **!n** : Exécuter commande numéro N  
✅ **!string** : Dernière commande débutant par string  
✅ **!$** : Dernier argument commande précédente  
✅ **history -c** : Effacer historique session (RAM)  
✅ **history -a** : Append historique au fichier (forcer sauvegarde)  
✅ **fc** : Éditer commande dans éditeur puis exécuter  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Retrouver Commande SSH Ancienne

```bash
# Besoin : Retrouver IP serveur SSH utilisée il y a 2 semaines

# Méthode 1 : history + grep
history | grep ssh
# 234  ssh admin@192.168.1.100
# 567  ssh-copy-id admin@192.168.1.100
# 789  ssh admin@prod.example.com

# Méthode 2 : Ctrl+R
Ctrl+R
(reverse-i-search)`ssh': ssh admin@192.168.1.100
# (Appuyer Ctrl+R pour autres résultats)

# Méthode 3 : Recherche fichier
grep ssh ~/.bash_history | grep -v "ssh-keygen"
```

---

### Scénario 2 : Répéter Commande avec sudo

```bash
# Oubli sudo
$ systemctl restart nginx
# Failed to restart nginx.service: Access denied

# Répéter avec sudo
$ sudo !!
# sudo systemctl restart nginx
# (Succès)

# Autre exemple
$ apt-get install htop
# E: Could not open lock file - open (13: Permission denied)

$ sudo !!
# sudo apt-get install htop
# (Installation OK)
```

---

### Scénario 3 : Réutiliser Chemin Fichier Long

```bash
# Éditer fichier avec chemin long
$ cat /var/www/html/application/config/database.php
# (Contenu affiché)

# Éditer même fichier
$ sudo nano !$
# sudo nano /var/www/html/application/config/database.php

# Sauvegarder backup
$ sudo cp !$ !$.bak
# sudo cp /var/www/html/application/config/database.php /var/www/html/application/config/database.php.bak
```

---

### Scénario 4 : Audit Activités Utilisateur

```bash
# Admin veut voir commandes exécutées par user

# Voir son propre historique avec dates
export HISTTIMEFORMAT="%F %T "
history | tail -20
# 980  2026-02-02 09:30:15 sudo systemctl stop mysql
# 981  2026-02-02 09:30:30 sudo rm -rf /var/lib/mysql/*
# 982  2026-02-02 09:35:00 sudo systemctl start mysql

# Voir historique autre user (root requis)
sudo cat /home/john/.bash_history | tail -20

# Chercher commandes critiques
sudo grep -E "rm -rf|shutdown|reboot" /home/*/.bash_history
```

---

### Scénario 5 : Nettoyer Historique Sensible

```bash
# Problème : Mot de passe tapé dans historique
$ mysql -u root -p MyP@ssw0rd  # ERREUR ! Mot de passe visible

# Voir numéro commande
history | grep mysql
# 456  mysql -u root -p MyP@ssw0rd

# Supprimer ligne
history -d 456

# Forcer sauvegarde (écraser fichier)
history -w

# Vérifier suppression
history | grep MyP@ssw0rd
# (Vide)

# Bonne pratique future : Espace au début
export HISTCONTROL=ignorespace
$  mysql -u root -p  # Espace au début
# Enter password: ******
# (Non enregistré dans history)
```

---

### Scénario 6 : Configuration Historique Optimisée

```bash
# ~/.bashrc : Configuration complète

# Augmenter taille historique
export HISTSIZE=10000
export HISTFILESIZE=20000

# Éviter doublons et commandes avec espace
export HISTCONTROL=ignoreboth:erasedups

# Ignorer commandes triviales
export HISTIGNORE="ls:ll:pwd:clear:history:exit"

# Timestamps
export HISTTIMEFORMAT="%F %T "

# Append au lieu d'écraser (multiples sessions)
shopt -s histappend

# Sauvegarde après chaque commande
export PROMPT_COMMAND="history -a; history -n"

# Recharger
source ~/.bashrc

# Test
echo "Test"
history | tail -1
# 1234  2026-02-02 14:30:45 echo "Test"  ✓
```

---

### Scénario 7 : Recherche Interactive Complexe

```bash
# Chercher commande docker avec Ctrl+R

# Appuyer Ctrl+R
(reverse-i-search)`_': 

# Taper "docker"
(reverse-i-search)`docker': docker ps -a

# Continuer recherche (Ctrl+R)
(reverse-i-search)`docker': docker run -d -p 8080:80 nginx

# Encore précédent (Ctrl+R)
(reverse-i-search)`docker': docker exec -it web-server bash

# Éditer sans exécuter (→)
$ docker exec -it web-server bash
# Modifier
$ docker exec -it db-server bash
Enter → Exécuter
```

---

### Scénario 8 : Multiples Sessions - Éviter Perte

```bash
# Problème : 3 terminaux ouverts simultanément
# Terminal 1, 2, 3 ont historiques séparés en RAM
# À la fermeture, dernier écrase fichier → Perte historique autres

# Solution : Append + sauvegarde continue

# ~/.bashrc (tous utilisateurs)
shopt -s histappend                # Append au lieu d'écraser
export PROMPT_COMMAND="history -a; history -n"  # Sauvegarde après chaque commande

# Effet :
# Terminal 1 : Commande tapée → Append à .bash_history
# Terminal 2 : Charge nouvelles lignes automatiquement
# Terminal 3 : Idem
# → Historique partagé entre sessions !
```

---

### Scénario 9 : Éditer Commande Complexe avec fc

```bash
# Longue commande avec erreur
$ docker run -d -p 8080:80 --name web-server --restart=always -v /var/www:/usr/share/nginx/html nginx:latst
# Error: No such image: nginx:latst  (faute frappe)

# Éditer avec fc au lieu de ↑ et éditer
fc
# (Éditeur s'ouvre avec commande)

# Corriger latst → latest
docker run -d -p 8080:80 --name web-server --restart=always -v /var/www:/usr/share/nginx/html nginx:latest

# Sauvegarder et fermer éditeur
# → Commande corrigée exécutée automatiquement
```

---

### Scénario 10 : Audit Sécurité Historique

```bash
# Admin vérifie activités suspectes

# Chercher tentatives accès root
sudo grep -E "su -|sudo -i|sudo su" /home/*/.bash_history

# Chercher modifications fichiers système
sudo grep -E "/etc/passwd|/etc/shadow|/etc/sudoers" /home/*/.bash_history

# Chercher suppressions massives
sudo grep -E "rm -rf" /home/*/.bash_history
# /home/john/.bash_history:456:rm -rf /tmp/*
# /home/alice/.bash_history:789:sudo rm -rf /var/log/*.log  ← Suspect !

# Chercher avec timestamps (si HISTTIMEFORMAT activé)
# Analyser période suspecte (ex: 3h du matin)
grep "2026-02-02 03:" ~/.bash_history
```

---

## ⚠️ Erreurs Courantes

### Erreur 1 : Historique Pas Sauvegardé

```
Problème : Commandes récentes pas dans history

Cause : Historique sauvegardé à fermeture session

Solution :
# Forcer sauvegarde
history -a

# Ou automatique (PROMPT_COMMAND)
export PROMPT_COMMAND="history -a"
```

---

### Erreur 2 : !! Ne Fonctionne Pas

```bash
$ echo "test"
$ !!
bash: !!: event not found

Cause : set +H (expansion historique désactivée)

Solution :
set -H  # Activer expansion historique

# Vérifier
shopt | grep histexpand
# histexpand      on  ✓
```

---

### Erreur 3 : Ctrl+S Freeze Terminal

```
Symptôme : Ctrl+S fige terminal (rien ne s'affiche)

Cause : Flow control XON/XOFF (ancien comportement)

Solution :
Ctrl+Q  # Débloquer (unfreeze)

# Désactiver flow control (permanent)
echo "stty -ixon" >> ~/.bashrc
source ~/.bashrc
```

---

### Erreur 4 : Historique Partagé Entre Users

```
Problème : User1 voit commandes User2

Cause : Variable $HISTFILE pointant même fichier

Solution :
# Vérifier
echo $HISTFILE
# /home/user1/.bash_history  ← Doit être home utilisateur

# Corriger si nécessaire
export HISTFILE=~/.bash_history

# Vérifier permissions
ls -l ~/.bash_history
# -rw------- 1 user1 user1  ← Seulement user1 (600)

chmod 600 ~/.bash_history
```

---

### Erreur 5 : Historique Effacé Accidentellement

```bash
# Fichier .bash_history supprimé
$ rm ~/.bash_history  # Erreur !

# Conséquence : Historique perdu à fermeture session

# Solutions préventives :

# 1. Backup régulier
crontab -e
# 0 2 * * * cp ~/.bash_history ~/.bash_history.bak

# 2. Git historique (avancé)
mkdir ~/history-backup
cd ~/history-backup
git init
cp ~/.bash_history .
git add .bash_history
git commit -m "Backup history $(date)"

# Script automatique
echo "cp ~/.bash_history ~/history-backup/.bash_history && \
      cd ~/history-backup && \
      git add .bash_history && \
      git commit -m 'Backup $(date)' 2>/dev/null" >> ~/.bash_logout
```

---

## 🔐 Bonnes Pratiques Sécurité Historique

### 1. Ignorer Commandes Sensibles

```bash
# ~/.bashrc
export HISTCONTROL=ignorespace

# Usage : Espace avant commandes sensibles
$  mysql -u root -p  # Espace au début
$  export API_KEY=secret123
$  ssh-keygen -p  # Changer passphrase

# Vérifier non enregistré
history | tail -3
# (Ne contient pas commandes avec espace)
```

---

### 2. Effacer Historique Serveurs Partagés

```bash
# Serveur jump/bastion où plusieurs users se connectent
# Effacer historique à chaque déconnexion

# ~/.bash_logout
history -c
history -w

# Ou ne pas sauvegarder du tout
unset HISTFILE  # Désactive sauvegarde
```

---

### 3. Historique Centralisé (Audit)

```bash
# Pour serveurs production : Envoyer historique syslog

# ~/.bashrc
export PROMPT_COMMAND='history -a; tail -1 ~/.bash_history | logger -t "BASH_HISTORY[$$]"'

# Logs centralisés dans /var/log/syslog
sudo tail /var/log/syslog | grep BASH_HISTORY
# Feb 02 14:30:45 server BASH_HISTORY[12345]: sudo systemctl restart nginx
# Feb 02 14:31:00 server BASH_HISTORY[12345]: docker ps
```

---

### 4. Permissions Strictes

```bash
# Vérifier permissions historique
ls -l ~/.bash_history
# -rw------- 1 user user  ← OK (600)

# Corriger si nécessaire
chmod 600 ~/.bash_history

# Empêcher modification accidentelle
chattr +a ~/.bash_history  # Append-only (root)
# (Nécessite root, fichier append-only)
```

---

## 🌍 Variables d'Environnement et Configuration Shell

### Variables d'Environnement : Concepts

**Variable d'environnement** = Paramètre système accessible par **tous les processus**

**Rôle** :
- ✅ **Configuration** applications (PATH, LANG, EDITOR...)
- ✅ **Personnalisation** comportement shell
- ✅ **Communication** entre processus parent/enfant
- ✅ **Informations** système (HOME, USER, HOSTNAME...)

**Analogie** 🏢 :
- **Variable locale** = Note personnelle (bureau uniquement)
- **Variable d'environnement** = Affiche entreprise (visible tous services)

---

### Différence Variables Locales vs Environnement

```bash
# Variable LOCALE (shell actuel uniquement)
MA_VAR="test"
echo $MA_VAR
# test

# Vérifier si environnement
printenv | grep MA_VAR
# (Vide - pas dans environnement)

# Processus enfant ne voit PAS variable locale
bash -c 'echo $MA_VAR'
# (Vide)

# EXPORTER en variable d'environnement
export MA_VAR="test"

# Maintenant processus enfant voit variable
bash -c 'echo $MA_VAR'
# test

printenv | grep MA_VAR
# MA_VAR=test  ✓
```

**Règle** : `export` pour rendre variable accessible processus enfants

---

## 📋 Commandes env et printenv

### printenv : Afficher Variables d'Environnement

**Syntaxe** :
```bash
printenv
```

**Résultat** (extrait) :
```
USER=john
HOME=/home/john
PATH=/usr/local/bin:/usr/bin:/bin
SHELL=/bin/bash
LANG=en_US.UTF-8
PWD=/home/john
HOSTNAME=server01
EDITOR=nano
```

**Afficher variable spécifique** :
```bash
printenv HOME
# /home/john

printenv PATH
# /usr/local/bin:/usr/bin:/bin

printenv USER
# john
```

---

### env : Similaire à printenv

**Syntaxe** :
```bash
env
```

**Résultat** : Identique à `printenv`

```bash
# Afficher toutes variables
env

# Afficher variable spécifique (syntaxe différente)
env | grep HOME
# HOME=/home/john
```

---

### Différences printenv vs env

| Aspect | printenv | env |
|--------|----------|-----|
| **Affichage toutes variables** | `printenv` | `env` |
| **Afficher variable spécifique** | `printenv HOME` | `env \| grep HOME` |
| **Usage principal** | ✅ Affichage variables | ✅ Exécuter commande avec env modifié |
| **Syntaxe** | Plus simple | Plus flexible |

**Usage env avancé** :
```bash
# Exécuter commande avec variable temporaire
env MY_VAR=test bash -c 'echo $MY_VAR'
# test

# Exécuter avec environnement vide
env -i bash
# (Shell sans variables héritées)

# Exécuter avec PATH modifié temporairement
env PATH=/custom/bin:$PATH which mycommand
```

**💡 Pratique** :
- `printenv` → Affichage simple
- `env` → Exécution avec environnement modifié

---

### Variables Environnement Courantes

| Variable | Signification | Exemple |
|----------|---------------|---------|
| **HOME** | Répertoire home utilisateur | `/home/john` |
| **USER** | Nom utilisateur | `john` |
| **SHELL** | Shell par défaut | `/bin/bash` |
| **PATH** | Chemins binaires | `/usr/bin:/bin` |
| **PWD** | Répertoire actuel | `/var/log` |
| **OLDPWD** | Répertoire précédent | `/home/john` |
| **LANG** | Langue système | `en_US.UTF-8` |
| **EDITOR** | Éditeur par défaut | `nano`, `vim` |
| **HOSTNAME** | Nom machine | `server01` |
| **TERM** | Type terminal | `xterm-256color` |
| **DISPLAY** | Serveur X11 | `:0` |
| **LD_LIBRARY_PATH** | Chemins bibliothèques | `/usr/local/lib` |

---

## 🏠 ~/.bashrc : Variables Utilisateur Spécifique

### Fichier .bashrc

**Emplacement** : `~/.bashrc` (home utilisateur)

**Rôle** : Configuration personnelle shell **Bash** (chargé à chaque nouveau shell interactif)

**Contenu type** :
```bash
# ~/.bashrc

# Aliases
alias ll='ls -la'
alias update='sudo apt-get update && sudo apt-get upgrade'

# Variables d'environnement personnelles
export EDITOR=vim
export VISUAL=vim

# PATH personnalisé
export PATH="$HOME/bin:$PATH"

# Historique
export HISTSIZE=10000
export HISTFILESIZE=20000
export HISTCONTROL=ignoreboth

# Prompt personnalisé
PS1='\[\e[32m\]\u@\h\[\e[0m\]:\[\e[34m\]\w\[\e[0m\]\$ '

# Fonctions personnelles
function mkcd() {
    mkdir -p "$1" && cd "$1"
}
```

---

### Ajouter Variable Personnelle

```bash
# Éditer .bashrc
nano ~/.bashrc

# Ajouter en fin de fichier
export MY_PROJECT_DIR="/home/john/projects"
export DOCKER_HOST="tcp://localhost:2375"
export JAVA_HOME="/usr/lib/jvm/java-11-openjdk-amd64"

# Sauvegarder et recharger
source ~/.bashrc

# Vérifier
echo $MY_PROJECT_DIR
# /home/john/projects

printenv | grep MY_PROJECT
# MY_PROJECT_DIR=/home/john/projects
```

---

### Ordre Chargement Fichiers Bash

**Login shell** (SSH, TTY) :
```
1. /etc/profile
2. /etc/profile.d/*.sh
3. ~/.bash_profile (si existe)
   OU ~/.bash_login (si existe)
   OU ~/.profile (si existe)
4. ~/.bashrc (généralement appelé par ~/.bash_profile)
```

**Non-login shell** (Terminal Emulator) :
```
1. ~/.bashrc
```

**Logout** :
```
1. ~/.bash_logout
```

---

### ~/.bashrc vs ~/.bash_profile

| Fichier | Chargé quand | Usage |
|---------|--------------|-------|
| **~/.bashrc** | Chaque nouveau shell interactif | Aliases, fonctions, prompt |
| **~/.bash_profile** | Login shell uniquement (SSH, TTY) | Variables d'environnement, PATH |

**Bonne pratique** : `~/.bash_profile` appelle `~/.bashrc`

```bash
# ~/.bash_profile
if [ -f ~/.bashrc ]; then
    source ~/.bashrc
fi

# Variables login shell
export PATH="$HOME/bin:$PATH"
```

---

## 🌐 /etc/environment : Variables Globales Système

### Fichier /etc/environment

**Emplacement** : `/etc/environment`

**Rôle** : Variables d'environnement **globales** pour **tous les utilisateurs**

**Caractéristiques** :
- ✅ Chargé au **login PAM** (avant shell)
- ✅ **Tous utilisateurs** (root, john, alice...)
- ✅ **Indépendant du shell** (bash, zsh, fish...)
- ✅ Format **simple** (pas de scripts)

**⚠️ Important** : **PAS de syntaxe shell** (pas `export`, pas `$` expansions)

---

### Syntaxe /etc/environment

**Format** : `VARIABLE="valeur"` (une par ligne)

```bash
# /etc/environment
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
LANG="en_US.UTF-8"
LC_ALL="en_US.UTF-8"
EDITOR="nano"
```

**⚠️ INTERDIT** :
```bash
# ❌ PAS de export
export PATH="/usr/bin"

# ❌ PAS d'expansion variables
PATH="$HOME/bin:$PATH"

# ❌ PAS de commandes shell
PATH=$(echo /usr/bin)
```

---

### Ajouter Variable Globale

```bash
# Éditer (root requis)
sudo nano /etc/environment

# Ajouter variable
JAVA_HOME="/usr/lib/jvm/java-11-openjdk-amd64"
DOCKER_HOST="tcp://localhost:2375"
MY_COMPANY_VAR="production"

# Sauvegarder

# Recharger (logout/login ou)
source /etc/environment

# Vérifier (tous utilisateurs)
printenv JAVA_HOME
# /usr/lib/jvm/java-11-openjdk-amd64
```

---

### Modifier PATH Global

**Problème** : Ajouter `/opt/bin` au PATH de tous les utilisateurs

**❌ Mauvaise méthode** (expansion impossible) :
```bash
# /etc/environment
PATH="/opt/bin:$PATH"  # ❌ $PATH pas évalué !
```

**✅ Bonne méthode** : Réécrire PATH complet
```bash
# /etc/environment
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin:/opt/bin"
```

**💡 Alternative** : Utiliser `/etc/profile.d/` pour syntaxe shell

---

### Exemple Configuration Production

```bash
# /etc/environment - Serveur production

# Langue
LANG="en_US.UTF-8"
LC_ALL="en_US.UTF-8"

# Éditeur par défaut
EDITOR="vim"
VISUAL="vim"

# Java
JAVA_HOME="/usr/lib/jvm/java-11-openjdk-amd64"

# Node.js
NODE_ENV="production"

# Application custom
APP_ENV="production"
APP_LOG_LEVEL="info"
DATABASE_HOST="db.internal.example.com"
```

---

## 📁 /etc/profile.d/ : Scripts Login Globaux

### Répertoire /etc/profile.d/

**Emplacement** : `/etc/profile.d/`

**Rôle** : Scripts shell exécutés au **login de tous les utilisateurs**

**Caractéristiques** :
- ✅ **Syntaxe shell complète** (export, if, variables...)
- ✅ **Tous utilisateurs** au login
- ✅ **Fichiers .sh** exécutés par `/etc/profile`
- ✅ **Pas de shebang nécessaire** (sourcé par bash)

---

### Pourquoi /etc/profile.d/ ?

**Avantages vs /etc/environment** :

| Aspect | /etc/environment | /etc/profile.d/*.sh |
|--------|------------------|---------------------|
| **Syntaxe shell** | ❌ Non | ✅ Oui (`export`, `if`, `$`...) |
| **Expansion variables** | ❌ Non | ✅ Oui (`$PATH`, `$HOME`...) |
| **Scripts complexes** | ❌ Non | ✅ Oui |
| **Modification PATH** | ⚠️ Difficile | ✅ Facile |
| **Conditions** | ❌ Non | ✅ Oui (`if`, `case`) |

---

### Créer Script /etc/profile.d/

**Exemple** : Ajouter `/opt/bin` au PATH

```bash
# Créer script (root)
sudo nano /etc/profile.d/custom-path.sh
```

**Contenu** :
```bash
# /etc/profile.d/custom-path.sh
# Ajouter /opt/bin au PATH de tous les utilisateurs

# ⚠️ PAS DE SHEBANG NÉCESSAIRE (sourcé par bash)

# Ajouter répertoire si existe
if [ -d /opt/bin ]; then
    export PATH="/opt/bin:$PATH"
fi
```

**Rendre exécutable** (optionnel mais recommandé) :
```bash
sudo chmod +x /etc/profile.d/custom-path.sh
```

**Tester** :
```bash
# Logout/login ou
bash -l  # Login shell

echo $PATH
# /opt/bin:/usr/local/bin:/usr/bin:/bin  ✓
```

---

### Exemples Scripts /etc/profile.d/

#### 1️⃣ Java Global

```bash
# /etc/profile.d/java.sh
# Configuration Java pour tous utilisateurs

JAVA_HOME="/usr/lib/jvm/java-11-openjdk-amd64"
export JAVA_HOME

if [ -d "$JAVA_HOME/bin" ]; then
    export PATH="$JAVA_HOME/bin:$PATH"
fi

export CLASSPATH=".:$JAVA_HOME/lib"
```

---

#### 2️⃣ Proxy Entreprise

```bash
# /etc/profile.d/proxy.sh
# Configuration proxy réseau entreprise

export HTTP_PROXY="http://proxy.company.com:8080"
export HTTPS_PROXY="http://proxy.company.com:8080"
export NO_PROXY="localhost,127.0.0.1,*.internal.company.com"

# Variables lowercase (certaines apps)
export http_proxy="$HTTP_PROXY"
export https_proxy="$HTTPS_PROXY"
export no_proxy="$NO_PROXY"
```

---

#### 3️⃣ Variables Application Custom

```bash
# /etc/profile.d/myapp.sh
# Configuration application métier

export MYAPP_HOME="/opt/myapp"
export MYAPP_CONFIG="/etc/myapp/config.yml"
export MYAPP_LOG_DIR="/var/log/myapp"

# Ajouter binaires app au PATH
if [ -d "$MYAPP_HOME/bin" ]; then
    export PATH="$MYAPP_HOME/bin:$PATH"
fi

# Alias utiles
alias myapp-start='sudo systemctl start myapp'
alias myapp-stop='sudo systemctl stop myapp'
alias myapp-logs='tail -f /var/log/myapp/app.log'
```

---

#### 4️⃣ Configuration Conditionnelle (Utilisateur)

```bash
# /etc/profile.d/dev-tools.sh
# Outils développement uniquement pour groupe "developers"

if groups | grep -q developers; then
    # Activer outils dev
    export NODE_ENV="development"
    export RAILS_ENV="development"
    
    # PATH outils dev
    export PATH="$HOME/.local/bin:$PATH"
    
    # Alias dev
    alias dc='docker-compose'
    alias dps='docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"'
fi
```

---

### Pas de Shebang Nécessaire

**Question** : Pourquoi pas de `#!/bin/bash` ?

**Réponse** : Scripts **sourcés** (pas exécutés)

```bash
# /etc/profile contient :
for i in /etc/profile.d/*.sh ; do
    if [ -r "$i" ]; then
        . "$i"  # ← Source (pas execute)
    fi
done
```

**Source (`.` ou `source`)** :
- Code exécuté dans **shell actuel** (pas sous-processus)
- Variables exportées **persistent**
- Pas besoin shebang

**Execute** :
- Code exécuté dans **sous-shell**
- Variables **perdues** à la fin
- Nécessite shebang

---

### Ordre Exécution Scripts

```bash
# Ordre alphabétique
ls /etc/profile.d/
# 00-custom.sh
# apps.sh
# java.sh
# proxy.sh
# z-last.sh

# Exécution : 00-custom.sh → apps.sh → java.sh → proxy.sh → z-last.sh
```

**💡 Astuce** : Préfixer numéros pour contrôler ordre
- `00-base.sh` (premier)
- `50-apps.sh` (milieu)
- `99-last.sh` (dernier)

---

## 📂 /etc/skel : Template Nouveaux Utilisateurs

### Répertoire /etc/skel

**Emplacement** : `/etc/skel/`

**Rôle** : **Template** copié dans home des **nouveaux utilisateurs**

**Utilisation** : Commande `useradd -m`

```bash
# Créer utilisateur avec home
sudo useradd -m john

# Processus :
# 1. Créer /home/john
# 2. Copier TOUT /etc/skel/* vers /home/john/
# 3. Changer propriétaire → john:john
```

---

### Contenu par Défaut /etc/skel

```bash
# Lister contenu
ls -la /etc/skel/
# .bash_logout
# .bashrc
# .profile
```

**Fichiers copiés** :
- `.bashrc` → Configuration bash
- `.profile` → Login shell
- `.bash_logout` → Exécuté à logout

---

### Créer Template Personnalisé

**Objectif** : Tous nouveaux utilisateurs ont fichiers pré-configurés

```bash
# Créer fichier bienvenue
sudo nano /etc/skel/README.txt
```

**Contenu** :
```
Bienvenue sur le serveur !

Règles importantes :
- Respecter politique sécurité entreprise
- Sauvegarder données importantes dans ~/backups/
- Contacter support : support@company.com

Documentation : https://docs.company.com
```

**Créer structure répertoires** :
```bash
# Créer dossiers template
sudo mkdir -p /etc/skel/projects
sudo mkdir -p /etc/skel/backups
sudo mkdir -p /etc/skel/bin

# Créer fichier .gitconfig
sudo nano /etc/skel/.gitconfig
```

```ini
[user]
    # Utilisateur devra configurer
    name = Your Name
    email = your.email@company.com

[core]
    editor = vim
    autocrlf = input

[alias]
    st = status
    co = checkout
    br = branch
    ci = commit
```

---

### Personnaliser .bashrc Template

```bash
# Éditer bashrc template
sudo nano /etc/skel/.bashrc
```

**Ajouter en fin** :
```bash
# Configuration entreprise

# Aliases standards
alias ll='ls -lah'
alias la='ls -A'
alias update='sudo apt-get update && sudo apt-get upgrade'

# Prompt personnalisé entreprise
PS1='\[\e[32m\][\u@company]\[\e[0m\]:\[\e[34m\]\w\[\e[0m\]\$ '

# Variables projet
export PROJECT_HOME="$HOME/projects"
export BACKUP_DIR="$HOME/backups"

# PATH personnel
export PATH="$HOME/bin:$PATH"

# Historique optimisé
export HISTSIZE=10000
export HISTFILESIZE=20000
export HISTCONTROL=ignoreboth

# Message bienvenue
if [ -f ~/README.txt ]; then
    cat ~/README.txt
fi
```

---

### Test Template

```bash
# Créer nouvel utilisateur
sudo useradd -m alice

# Vérifier home créé avec template
sudo ls -la /home/alice/
# .bash_logout
# .bashrc
# .gitconfig
# .profile
# README.txt
# backups/
# bin/
# projects/

# Vérifier contenu
sudo cat /home/alice/README.txt
# Bienvenue sur le serveur !
# ...

# Vérifier bashrc
sudo grep "company" /home/alice/.bashrc
# PS1='\[\e[32m\][\u@company]\[\e[0m\]:\[\e[34m\]\w\[\e[0m\]\$ '
```

---

### Option -k : Skeleton Alternatif

**Syntaxe** :
```bash
useradd -m -k /path/to/custom/skel username
```

**Usage** : Template différent selon type utilisateur

---

**Exemple** : Profils développeurs vs utilisateurs standard

```bash
# Créer skeleton développeurs
sudo mkdir -p /etc/skel-dev
sudo cp -r /etc/skel/* /etc/skel-dev/

# Ajouter outils dev
sudo mkdir -p /etc/skel-dev/.config/git
sudo mkdir -p /etc/skel-dev/projects/{python,nodejs,docker}

# .bashrc développeurs
sudo nano /etc/skel-dev/.bashrc
# (Ajouter aliases dev, Docker, Git...)

# Créer utilisateur développeur
sudo useradd -m -k /etc/skel-dev bob

# Vérifier structure
sudo ls -la /home/bob/projects/
# docker/
# nodejs/
# python/
```

---

### Synchroniser Utilisateurs Existants

**Problème** : Template modifié, mais utilisateurs existants n'ont pas nouvelles configs

**⚠️ Attention** : Pas de synchronisation automatique !

**Solutions** :

#### 1️⃣ Copie Manuelle Sélective

```bash
# Copier nouveau fichier pour user existant
sudo cp /etc/skel/README.txt /home/john/
sudo chown john:john /home/john/README.txt

# Script pour tous users
for user in john alice bob; do
    sudo cp /etc/skel/README.txt /home/$user/
    sudo chown $user:$user /home/$user/README.txt
done
```

---

#### 2️⃣ Message Notification

```bash
# Créer script notification
sudo nano /etc/profile.d/skel-update.sh
```

```bash
# /etc/profile.d/skel-update.sh
# Notifier utilisateurs de nouveaux templates

if [ ! -f "$HOME/.skel-synced-2026-02" ]; then
    cat << 'EOF'
╔═══════════════════════════════════════════════════════╗
║  NOUVEAUX FICHIERS TEMPLATE DISPONIBLES              ║
║  Exécuter : cp /etc/skel/.gitconfig ~/.gitconfig     ║
║  Voir : ls /etc/skel/                                ║
╚═══════════════════════════════════════════════════════╝
EOF
fi
```

---

#### 3️⃣ Script Opt-in Utilisateur

```bash
# Créer script synchronisation
sudo nano /usr/local/bin/sync-skel
```

```bash
#!/bin/bash
# sync-skel : Synchroniser home avec /etc/skel

echo "Synchronisation templates /etc/skel..."

# Sauvegarder configs actuelles
BACKUP_DIR="$HOME/.skel-backup-$(date +%Y%m%d-%H%M%S)"
mkdir -p "$BACKUP_DIR"

# Copier fichiers skel (sans écraser)
for file in /etc/skel/.*; do
    filename=$(basename "$file")
    
    # Ignorer . et ..
    if [[ "$filename" == "." || "$filename" == ".." ]]; then
        continue
    fi
    
    # Si fichier existe, sauvegarder
    if [ -f "$HOME/$filename" ]; then
        cp "$HOME/$filename" "$BACKUP_DIR/"
        echo "Sauvegarde : $filename → $BACKUP_DIR/"
    fi
    
    # Copier nouveau template
    cp "$file" "$HOME/"
    echo "Copie : $filename"
done

echo "Synchronisation terminée. Backup dans : $BACKUP_DIR"
echo "Relancer shell : exec bash"
```

```bash
# Rendre exécutable
sudo chmod +x /usr/local/bin/sync-skel

# Utilisateur peut synchroniser
sync-skel
# Synchronisation templates /etc/skel...
# Sauvegarde : .bashrc → /home/john/.skel-backup-20260202-143000/
# Copie : .bashrc
# ...
```

---

## 🛣️ Modifier Variable PATH

### Variable PATH : Concepts

**PATH** = Liste **répertoires** où shell cherche commandes exécutables

**Format** : Chemins séparés par **`:`** (délimiteur)

```bash
echo $PATH
# /usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/usr/sbin:/sbin
```

**Ordre important** : Recherche **gauche → droite** (premier trouvé exécuté)

---

### Ajouter Répertoire au PATH

#### Méthode 1 : Début PATH (Prioritaire)

```bash
# Ajouter /opt/bin au DÉBUT
export PATH="/opt/bin:$PATH"

# Résultat
echo $PATH
# /opt/bin:/usr/local/bin:/usr/bin:/bin
```

**Effet** : `/opt/bin` cherché **avant** `/usr/bin`

---

#### Méthode 2 : Fin PATH (Secours)

```bash
# Ajouter /opt/bin à la FIN
export PATH="$PATH:/opt/bin"

# Résultat
echo $PATH
# /usr/local/bin:/usr/bin:/bin:/opt/bin
```

**Effet** : `/opt/bin` cherché **après** autres répertoires

---

### Délimiteur : (deux-points)

**⚠️ Important** : Délimiteur = **`:`** (pas `;` ni `,`)

```bash
# ✅ CORRECT
export PATH="/opt/bin:/custom/bin:$PATH"

# ❌ INCORRECT
export PATH="/opt/bin;/custom/bin;$PATH"  # Bash interprète ; comme fin commande
export PATH="/opt/bin,/custom/bin,$PATH"  # Virgule pas reconnue
```

---

### Exemples Modification PATH

#### 1️⃣ Binaires Personnels Utilisateur

```bash
# ~/.bashrc
# Créer ~/bin si existe
if [ -d "$HOME/bin" ]; then
    export PATH="$HOME/bin:$PATH"
fi
```

---

#### 2️⃣ Applications /opt

```bash
# /etc/profile.d/opt-apps.sh
# Ajouter applications /opt au PATH

# /opt/bin
if [ -d /opt/bin ]; then
    export PATH="/opt/bin:$PATH"
fi

# Application Java custom
if [ -d /opt/java/bin ]; then
    export PATH="/opt/java/bin:$PATH"
fi

# Node.js global
if [ -d /opt/nodejs/bin ]; then
    export PATH="/opt/nodejs/bin:$PATH"
fi
```

---

#### 3️⃣ Multiples Répertoires

```bash
# ~/.bashrc
# Ajouter plusieurs répertoires

CUSTOM_PATHS=(
    "$HOME/bin"
    "$HOME/.local/bin"
    "/opt/bin"
    "/opt/custom/bin"
)

for dir in "${CUSTOM_PATHS[@]}"; do
    if [ -d "$dir" ]; then
        export PATH="$dir:$PATH"
    fi
done
```

---

#### 4️⃣ PATH Global /etc/environment

**⚠️ Pas d'expansion variables** → Réécrire PATH complet

```bash
# /etc/environment
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin:/opt/bin"
```

**Ordre** :
1. `/usr/local/sbin` (admin local prioritaire)
2. `/usr/local/bin` (programmes locaux)
3. `/usr/sbin` (admin système)
4. `/usr/bin` (programmes système)
5. `/sbin` (admin basique)
6. `/bin` (commandes basiques)
7. `/snap/bin` (Snap packages)
8. `/opt/bin` (applications tierces) ← **AJOUTÉ**

---

### Vérifier Ordre PATH

```bash
# Afficher PATH ligne par ligne
echo $PATH | tr ':' '\n'
# /opt/bin
# /usr/local/bin
# /usr/bin
# /bin

# Vérifier quel binaire sera exécuté
which python
# /usr/bin/python

# Tous chemins d'une commande
which -a python
# /usr/local/bin/python  ← Exécuté (premier)
# /usr/bin/python
```

---

### Précédence PATH

**Exemple conflit** : Deux versions Python

```bash
# PATH actuel
echo $PATH
# /usr/local/bin:/usr/bin

ls /usr/local/bin/python
# python -> python3.11

ls /usr/bin/python
# python -> python3.9

# Quelle version exécutée ?
which python
# /usr/local/bin/python  ← Premier dans PATH

python --version
# Python 3.11.0
```

**💡 Ordre PATH = Ordre priorité**

---

## 📋 Antisèche - Variables et Configuration

| Commande/Fichier | Je veux... | Exemple |
|------------------|-----------|---------|
| `printenv` | Afficher variables environnement | `printenv` |
| `printenv VAR` | Afficher variable spécifique | `printenv HOME` |
| `env` | Afficher variables (similaire printenv) | `env` |
| `export VAR=val` | Créer variable environnement | `export EDITOR=vim` |
| `~/.bashrc` | Config personnelle utilisateur | Variables, aliases, fonctions |
| `/etc/environment` | Variables globales tous users | `EDITOR="nano"` |
| `/etc/profile.d/*.sh` | Scripts login globaux | Scripts avec syntaxe shell |
| `/etc/skel/` | Template nouveaux utilisateurs | Fichiers copiés lors `useradd -m` |
| `useradd -m -k /path` | Skeleton alternatif | `useradd -m -k /etc/skel-dev bob` |
| `export PATH="/opt/bin:$PATH"` | Ajouter au PATH (début) | Priorité haute |
| `export PATH="$PATH:/opt/bin"` | Ajouter au PATH (fin) | Priorité basse |
| `echo $PATH \| tr ':' '\n'` | PATH ligne par ligne | Voir ordre |
| `which cmd` | Chemin commande exécutée | `which python` |

---

## 🎓 Points Clés pour l'Examen

✅ **printenv** et **env** : Afficher variables environnement (similaires)  
✅ **~/.bashrc** : Configuration shell utilisateur (variables, aliases, fonctions)  
✅ **/etc/environment** : Variables globales tous utilisateurs (format simple, pas `export`)  
✅ **/etc/profile.d/*.sh** : Scripts exécutés au login tous users (syntaxe shell complète)  
✅ **Pas de shebang** dans `/etc/profile.d/` (scripts sourcés par bash)  
✅ **/etc/skel/** : Template copié lors `useradd -m` (nouveaux utilisateurs)  
✅ **useradd -m** : Crée home + copie `/etc/skel/*`  
✅ **useradd -k /path** : Skeleton alternatif  
✅ **PATH** : Variable chemins binaires, délimiteur **`:`**  
✅ **Ajouter PATH** : `/opt/bin:$PATH` (début) ou `$PATH:/opt/bin` (fin)  
✅ **Ordre PATH** : Gauche → droite (premier trouvé exécuté)  
✅ **export** : Rendre variable accessible processus enfants  
✅ **source ~/.bashrc** : Recharger config sans logout  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Configurer Éditeur par Défaut

```bash
# Problème : Commandes utilisent nano, préférer vim

# Solution 1 : Personnel (~/.bashrc)
echo "export EDITOR=vim" >> ~/.bashrc
echo "export VISUAL=vim" >> ~/.bashrc
source ~/.bashrc

# Solution 2 : Global (/etc/environment)
sudo nano /etc/environment
# EDITOR="vim"
# VISUAL="vim"

# Vérifier
printenv EDITOR
# vim

# Tester
crontab -e
# (Ouvre vim au lieu de nano)

git commit
# (Utilise vim pour message commit)
```

---

### Scénario 2 : Ajouter Binaires Custom au PATH

```bash
# Application installée dans /opt/myapp/bin

# Méthode 1 : Personnel
echo 'export PATH="/opt/myapp/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

# Méthode 2 : Global tous users
sudo nano /etc/profile.d/myapp.sh
```

```bash
# /etc/profile.d/myapp.sh
if [ -d /opt/myapp/bin ]; then
    export PATH="/opt/myapp/bin:$PATH"
fi

export MYAPP_HOME="/opt/myapp"
export MYAPP_CONFIG="/etc/myapp/config.yml"
```

```bash
# Rendre exécutable
sudo chmod +x /etc/profile.d/myapp.sh

# Tester (nouveau shell)
bash -l
which myapp-cli
# /opt/myapp/bin/myapp-cli  ✓
```

---

### Scénario 3 : Template Développeurs /etc/skel

```bash
# Créer skeleton développeurs

# Copier base
sudo cp -r /etc/skel /etc/skel-dev

# Structure projets
sudo mkdir -p /etc/skel-dev/projects/{python,nodejs,docker,scripts}
sudo mkdir -p /etc/skel-dev/.config/{git,vim}

# .bashrc développeurs
sudo nano /etc/skel-dev/.bashrc
```

```bash
# Ajouter en fin .bashrc
# === Configuration Développeur ===

# Aliases dev
alias dc='docker-compose'
alias dps='docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"'
alias gst='git status'
alias gco='git checkout'
alias glog='git log --oneline --graph --all'

# PATH personnel + Node.js
export PATH="$HOME/bin:$HOME/.local/bin:$PATH"
export PATH="$HOME/.npm-global/bin:$PATH"

# Variables dev
export NODE_ENV="development"
export DOCKER_BUILDKIT=1
export COMPOSE_DOCKER_CLI_BUILD=1

# Prompt développeur
PS1='\[\e[36m\][DEV]\[\e[32m\]\u@\h\[\e[0m\]:\[\e[34m\]\w\[\e[0m\]\$ '

echo "Environnement développeur chargé. Projets : ~/projects/"
```

```bash
# .gitconfig template
sudo nano /etc/skel-dev/.gitconfig
```

```ini
[user]
    name = Developer Name
    email = dev@company.com

[core]
    editor = vim
    autocrlf = input

[alias]
    st = status
    co = checkout
    br = branch
    ci = commit
    lg = log --oneline --graph --all
    unstage = reset HEAD --

[color]
    ui = auto
```

```bash
# Créer utilisateur développeur
sudo useradd -m -k /etc/skel-dev -s /bin/bash -G docker,sudo alice

# Définir mot de passe
sudo passwd alice

# Vérifier structure
sudo ls -la /home/alice/
# .bashrc (custom dev)
# .gitconfig
# projects/docker/
# projects/nodejs/
# projects/python/
# projects/scripts/

# Tester connexion
su - alice
# [DEV]alice@server:~$ 
# Environnement développeur chargé. Projets : ~/projects/
```

---

### Scénario 4 : Proxy Entreprise Global

```bash
# Configurer proxy pour tous users

# /etc/profile.d/proxy.sh
sudo nano /etc/profile.d/proxy.sh
```

```bash
# /etc/profile.d/proxy.sh
# Configuration proxy réseau entreprise

PROXY_HOST="proxy.company.com"
PROXY_PORT="8080"

export HTTP_PROXY="http://${PROXY_HOST}:${PROXY_PORT}"
export HTTPS_PROXY="http://${PROXY_HOST}:${PROXY_PORT}"
export FTP_PROXY="http://${PROXY_HOST}:${PROXY_PORT}"

# Exceptions (réseau interne)
export NO_PROXY="localhost,127.0.0.1,.company.com,.internal,10.0.0.0/8"

# Variables lowercase (certaines apps)
export http_proxy="$HTTP_PROXY"
export https_proxy="$HTTPS_PROXY"
export ftp_proxy="$FTP_PROXY"
export no_proxy="$NO_PROXY"

# Git proxy
git config --global http.proxy "$HTTP_PROXY"
git config --global https.proxy "$HTTPS_PROXY"

# Apt proxy
if [ -w /etc/apt/apt.conf.d/ ]; then
    echo "Acquire::http::Proxy \"$HTTP_PROXY\";" | sudo tee /etc/apt/apt.conf.d/95proxies > /dev/null
fi
```

```bash
# Permissions
sudo chmod +x /etc/profile.d/proxy.sh

# Tester
bash -l
printenv | grep -i proxy
# HTTP_PROXY=http://proxy.company.com:8080
# HTTPS_PROXY=http://proxy.company.com:8080
# ...

# Test connexion
curl -I https://google.com
# (Passe par proxy)
```

---

### Scénario 5 : Variables Application Multi-Environnement

```bash
# Serveur avec environnements dev/staging/prod

# /etc/profile.d/app-env.sh
sudo nano /etc/profile.d/app-env.sh
```

```bash
# /etc/profile.d/app-env.sh
# Détection environnement automatique

HOSTNAME=$(hostname)

case "$HOSTNAME" in
    dev-server*)
        export APP_ENV="development"
        export APP_DEBUG="true"
        export DATABASE_HOST="dev-db.internal"
        export LOG_LEVEL="debug"
        ;;
    staging-server*)
        export APP_ENV="staging"
        export APP_DEBUG="false"
        export DATABASE_HOST="staging-db.internal"
        export LOG_LEVEL="info"
        ;;
    prod-server*)
        export APP_ENV="production"
        export APP_DEBUG="false"
        export DATABASE_HOST="prod-db.internal"
        export LOG_LEVEL="warning"
        ;;
    *)
        export APP_ENV="unknown"
        ;;
esac

# Variables communes
export APP_HOME="/opt/myapp"
export APP_CONFIG="/etc/myapp/config.yml"
export APP_LOG_DIR="/var/log/myapp"

# Prompt avec environnement
case "$APP_ENV" in
    production)
        PS1='\[\e[31m\][PROD]\[\e[0m\] \u@\h:\w\$ '  # Rouge
        ;;
    staging)
        PS1='\[\e[33m\][STAGING]\[\e[0m\] \u@\h:\w\$ '  # Jaune
        ;;
    development)
        PS1='\[\e[32m\][DEV]\[\e[0m\] \u@\h:\w\$ '  # Vert
        ;;
esac
```

---

### Scénario 6 : Synchroniser Config Existants

```bash
# Nouveau template créé, synchroniser users existants

# Script synchronisation
sudo nano /usr/local/bin/skel-update
```

```bash
#!/bin/bash
# skel-update : Proposer mise à jour configs depuis /etc/skel

SKEL_DIR="/etc/skel"
TARGET_FILE="$1"

if [ -z "$TARGET_FILE" ]; then
    echo "Usage: skel-update <fichier>"
    echo "Fichiers disponibles :"
    ls -1 "$SKEL_DIR"
    exit 1
fi

SKEL_FILE="$SKEL_DIR/$TARGET_FILE"
HOME_FILE="$HOME/$TARGET_FILE"

if [ ! -f "$SKEL_FILE" ]; then
    echo "Erreur : $SKEL_FILE n'existe pas"
    exit 1
fi

# Sauvegarder ancien fichier
if [ -f "$HOME_FILE" ]; then
    BACKUP_DIR="$HOME/.config-backups"
    mkdir -p "$BACKUP_DIR"
    BACKUP_FILE="$BACKUP_DIR/${TARGET_FILE}.bak-$(date +%Y%m%d-%H%M%S)"
    
    cp "$HOME_FILE" "$BACKUP_FILE"
    echo "Sauvegarde : $BACKUP_FILE"
fi

# Copier nouveau template
cp "$SKEL_FILE" "$HOME_FILE"
echo "Copie : $SKEL_FILE → $HOME_FILE"
echo "Relancer shell : exec bash"
```

```bash
sudo chmod +x /usr/local/bin/skel-update

# Utilisateur synchronise
skel-update .bashrc
# Sauvegarde : /home/john/.config-backups/.bashrc.bak-20260202-150000
# Copie : /etc/skel/.bashrc → /home/john/.bashrc
# Relancer shell : exec bash

exec bash
# (Nouveau .bashrc chargé)
```

---

### Scénario 7 : PATH Priorité Application

```bash
# Problème : Deux versions Python installées
# /usr/bin/python (3.9 système)
# /opt/python/bin/python (3.11 custom)

# Vérifier PATH actuel
echo $PATH
# /usr/local/bin:/usr/bin:/bin

which python
# /usr/bin/python

python --version
# Python 3.9.0

# Solution : Ajouter /opt/python/bin AVANT /usr/bin
echo 'export PATH="/opt/python/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

# Vérifier nouveau PATH
echo $PATH
# /opt/python/bin:/usr/local/bin:/usr/bin:/bin

which python
# /opt/python/bin/python  ✓

python --version
# Python 3.11.0  ✓

# Version système toujours accessible
/usr/bin/python --version
# Python 3.9.0
```

---

### Scénario 8 : Debugging Variables Environnement

```bash
# Problème : Variable pas définie/mauvaise valeur

# 1. Vérifier variable existe
printenv MY_VAR
# (Vide) → Pas définie

# 2. Vérifier définition
grep -r "MY_VAR" ~/.bashrc /etc/profile.d/ /etc/environment
# (Rien) → Pas configurée

# 3. Définir temporairement (test)
export MY_VAR="test-value"
printenv MY_VAR
# test-value  ✓

# 4. Définir permanent
echo 'export MY_VAR="production-value"' >> ~/.bashrc
source ~/.bashrc

# 5. Vérifier dans nouveau shell
bash -l -c 'printenv MY_VAR'
# production-value  ✓

# 6. Vérifier processus enfant voit variable
bash -c 'echo $MY_VAR'
# production-value  ✓ (export fonctionne)
```

---

### Scénario 9 : Configuration Java Globale

```bash
# Installer Java et configurer JAVA_HOME pour tous

# 1. Installer OpenJDK
sudo apt-get install openjdk-11-jdk

# 2. Trouver chemin Java
update-alternatives --list java
# /usr/lib/jvm/java-11-openjdk-amd64/bin/java

# 3. Configurer global
sudo nano /etc/profile.d/java.sh
```

```bash
# /etc/profile.d/java.sh
# Configuration Java

export JAVA_HOME="/usr/lib/jvm/java-11-openjdk-amd64"
export JRE_HOME="$JAVA_HOME/jre"

# Ajouter binaires Java au PATH
if [ -d "$JAVA_HOME/bin" ]; then
    export PATH="$JAVA_HOME/bin:$PATH"
fi

# CLASSPATH
export CLASSPATH=".:$JAVA_HOME/lib:$JRE_HOME/lib"
```

```bash
sudo chmod +x /etc/profile.d/java.sh

# Tester
bash -l
echo $JAVA_HOME
# /usr/lib/jvm/java-11-openjdk-amd64

java -version
# openjdk version "11.0.18"

javac -version
# javac 11.0.18
```

---

### Scénario 10 : Template Utilisateurs Serveur Web

```bash
# Créer skeleton pour développeurs web

# Structure /etc/skel-web
sudo mkdir -p /etc/skel-web/{public_html,logs,backups,scripts}
sudo cp /etc/skel/.bashrc /etc/skel-web/

# .bashrc web
sudo nano /etc/skel-web/.bashrc
```

```bash
# Ajouter config web
# === Configuration Serveur Web ===

# Chemins
export WEB_ROOT="$HOME/public_html"
export WEB_LOGS="$HOME/logs"
export WEB_BACKUPS="$HOME/backups"

# Aliases web
alias wwwlogs='tail -f ~/logs/access.log'
alias wwwerr='tail -f ~/logs/error.log'
alias wwwreload='sudo systemctl reload nginx'
alias wwwstatus='sudo systemctl status nginx'
alias wwwtest='sudo nginx -t'

# Permissions web
umask 022  # Fichiers 644, dossiers 755 par défaut

# Message bienvenue
cat << 'EOF'
╔══════════════════════════════════════╗
║  Serveur Web - Environnement activé  ║
║  Public HTML : ~/public_html/        ║
║  Logs        : ~/logs/               ║
║  Alias       : wwwlogs, wwwerr       ║
╚══════════════════════════════════════╝
EOF
```

```bash
# index.html template
sudo nano /etc/skel-web/public_html/index.html
```

```html
<!DOCTYPE html>
<html>
<head>
    <title>Site Web</title>
    <meta charset="utf-8">
</head>
<body>
    <h1>Site en construction</h1>
    <p>Cette page sera bientôt disponible.</p>
</body>
</html>
```

```bash
# .htaccess template
sudo nano /etc/skel-web/public_html/.htaccess
```

```apache
# .htaccess
Options -Indexes
DirectoryIndex index.html index.php

# PHP settings
php_value upload_max_filesize 10M
php_value post_max_size 10M
```

```bash
# Créer utilisateur web
sudo useradd -m -k /etc/skel-web -s /bin/bash -G www-data webuser1
sudo passwd webuser1

# Configurer permissions
sudo chown -R webuser1:www-data /home/webuser1/public_html
sudo chmod -R 750 /home/webuser1/public_html

# Tester
su - webuser1
# ╔══════════════════════════════════════╗
# ║  Serveur Web - Environnement activé  ║
# ║  Public HTML : ~/public_html/        ║
# ...

ls ~/public_html/
# index.html  .htaccess
```

---

## ⚠️ Erreurs Courantes

### Erreur 1 : export dans /etc/environment

```bash
# ❌ ERREUR
# /etc/environment
export JAVA_HOME="/usr/lib/jvm/java-11"

# Résultat : Variable pas chargée
printenv JAVA_HOME
# (Vide)

# ✅ CORRECTION
# /etc/environment (sans export)
JAVA_HOME="/usr/lib/jvm/java-11"
```

---

### Erreur 2 : Expansion Variables dans /etc/environment

```bash
# ❌ ERREUR
# /etc/environment
PATH="/opt/bin:$PATH"

# Résultat : Littéral "$PATH" dans variable
echo $PATH
# /opt/bin:$PATH  ← Pas expansion !

# ✅ CORRECTION : Réécrire PATH complet
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/opt/bin"

# OU utiliser /etc/profile.d/ (supporte expansion)
# /etc/profile.d/custom-path.sh
export PATH="/opt/bin:$PATH"
```

---

### Erreur 3 : Shebang dans /etc/profile.d/

```bash
# /etc/profile.d/myapp.sh
#!/bin/bash  # ← PAS NÉCESSAIRE (sourcé)

export MYAPP_HOME="/opt/myapp"

# Pas d'erreur mais inutile (scripts sourcés, pas exécutés)
```

---

### Erreur 4 : Oublier Recharger Config

```bash
# Modifier .bashrc
echo 'export EDITOR=vim' >> ~/.bashrc

# Tester immédiatement
echo $EDITOR
# (Vide) ← Pas rechargé !

# ✅ RECHARGER
source ~/.bashrc

echo $EDITOR
# vim  ✓
```

---

### Erreur 5 : PATH Délimiteur Incorrect

```bash
# ❌ ERREUR : Point-virgule
export PATH="/opt/bin;$PATH"

# Bash interprète ; comme séparateur commandes
echo $PATH
# /opt/bin  ← $PATH pas ajouté

# ✅ CORRECTION : Deux-points
export PATH="/opt/bin:$PATH"
```

---

## 🔤 Filtres de Traitement de Texte

### Filtres : Concepts

**Filtre** = Programme qui lit **stdin**, transforme, et écrit **stdout**

**Caractéristiques** :
- ✅ Lecture standard input (stdin)
- ✅ Écriture standard output (stdout)
- ✅ **Chaînables** via pipes (`|`)
- ✅ Pas de modification fichiers (sauf options)

**Analogie** 🏭 :
- **Filtre** = Étape chaîne production (reçoit matière, transforme, passe suivant)
- **Pipe** = Tapis roulant (connecte étapes)

---

## 🐱 cat et tac : Affichage Fichiers

### cat : Concatenate

**Rôle** : Afficher contenu fichier(s) **dans l'ordre**

```bash
# Fichier exemple
cat file.txt
# Ligne 1
# Ligne 2
# Ligne 3
# Ligne 4
```

**Usages** :
```bash
# Afficher fichier
cat /etc/hosts

# Concaténer plusieurs fichiers
cat file1.txt file2.txt file3.txt

# Créer fichier
cat > newfile.txt
# (Taper contenu, Ctrl+D pour terminer)

# Numéroter lignes
cat -n file.txt
# 1  Ligne 1
# 2  Ligne 2
```

---

### tac : Reverse de cat

**Rôle** : Afficher contenu fichier **dans l'ordre inverse** (dernière ligne → première)

**Nom** : **tac** = **cat** à l'envers 🔄

```bash
# Fichier original
cat file.txt
# Ligne 1
# Ligne 2
# Ligne 3
# Ligne 4

# Ordre inversé
tac file.txt
# Ligne 4
# Ligne 3
# Ligne 2
# Ligne 1
```

---

### Comparaison cat vs tac

| Commande | Ordre | Exemple |
|----------|-------|---------|
| `cat file.txt` | Normal (↓) | 1, 2, 3, 4 |
| `tac file.txt` | Inversé (↑) | 4, 3, 2, 1 |

**Usage tac** :
```bash
# Logs : Afficher derniers événements en premier
tac /var/log/syslog | head -20

# Historique inversé
tac ~/.bash_history | head -10
```

---

## 📊 head et tail : Début/Fin Fichier

### head : Premières Lignes

**Rôle** : Afficher **début** fichier (10 lignes par défaut)

```bash
# 10 premières lignes
head file.txt

# N premières lignes
head -n 20 file.txt
head -20 file.txt  # Syntaxe courte

# Premiers 100 octets
head -c 100 file.txt
```

---

### tail : Dernières Lignes

**Rôle** : Afficher **fin** fichier (10 lignes par défaut)

```bash
# 10 dernières lignes
tail file.txt

# N dernières lignes
tail -n 50 file.txt
tail -50 file.txt  # Syntaxe courte

# Suivi temps réel (logs)
tail -f /var/log/syslog
# (Affiche nouvelles lignes en temps réel, Ctrl+C pour arrêter)

# Suivre avec retry (fichier peut disparaître/recréer)
tail -F /var/log/app.log

# N dernières lignes + suivi
tail -n 100 -f /var/log/nginx/access.log
```

---

### Combinaisons head/tail

```bash
# Lignes 10 à 20
head -20 file.txt | tail -10

# Lignes 50 à 60
head -60 file.txt | tail -10

# Ligne spécifique (ex: ligne 15)
head -15 file.txt | tail -1
```

---

## ✂️ cut : Découper Colonnes

### Rôle cut

**cut** = Extraire **colonnes/champs** depuis lignes texte

**Usages** :
- ✅ CSV/TSV parsing
- ✅ Logs structurés
- ✅ Fichiers délimités

---

### Option -d : Délimiteur

**Syntaxe** :
```bash
cut -d 'délimiteur' -f numéro_champ fichier
```

**Exemple** :

```bash
# Fichier users.csv
cat users.csv
# john,doe,john@example.com
# alice,smith,alice@example.com
# bob,jones,bob@example.com

# Extraire 1er champ (prénom) - délimiteur virgule
cut -d ',' -f 1 users.csv
# john
# alice
# bob

# Extraire 3e champ (email)
cut -d ',' -f 3 users.csv
# john@example.com
# alice@example.com
# bob@example.com
```

---

### Option -f : Numéro Field

**Syntaxe** :

```bash
-f N         # Champ N
-f N,M       # Champs N et M
-f N-M       # Champs N à M
-f N-        # Champs N jusqu'à fin
-f -M        # Champs 1 à M
```

**Exemples** :

```bash
# Fichier /etc/passwd (délimiteur :)
head -3 /etc/passwd
# root:x:0:0:root:/root:/bin/bash
# daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
# bin:x:2:2:bin:/bin:/usr/sbin/nologin

# Extraire username (champ 1)
cut -d ':' -f 1 /etc/passwd | head -3
# root
# daemon
# bin

# Extraire username + home (champs 1 et 6)
cut -d ':' -f 1,6 /etc/passwd | head -3
# root:/root
# daemon:/usr/sbin
# bin:/bin

# Extraire champs 1 à 3
cut -d ':' -f 1-3 /etc/passwd | head -3
# root:x:0
# daemon:x:1
# bin:x:2

# Extraire username + tout après UID (1,3-)
cut -d ':' -f 1,3- /etc/passwd | head -3
# root:0:0:root:/root:/bin/bash
# daemon:1:1:daemon:/usr/sbin:/usr/sbin/nologin
```

---

### cut avec Espace comme Délimiteur

```bash
# Fichier avec espaces
cat data.txt
# John Doe 35
# Alice Smith 28
# Bob Jones 42

# Délimiteur espace
cut -d ' ' -f 1 data.txt
# John
# Alice
# Bob

# Nom complet (champs 1-2)
cut -d ' ' -f 1-2 data.txt
# John Doe
# Alice Smith
# Bob Jones
```

---

### cut avec Tabulations

```bash
# Fichier TSV (tab-separated)
cat data.tsv
# Name	Age	City
# John	35	Paris
# Alice	28	London

# Délimiteur tabulation (défaut cut)
cut -f 1 data.tsv
# Name
# John
# Alice

# Ou explicite
cut -d $'\t' -f 1 data.tsv
```

---

## 🔄 sed : Stream Editor

### Rôle sed

**sed** = **S**tream **ED**itor (éditeur flux)

**Fonctions** :
- ✅ **Substitution** texte
- ✅ **Suppression** lignes
- ✅ **Insertion** texte
- ✅ **Transformation** flux

**Analogie** 📝 : Rechercher/remplacer Word, mais en ligne de commande

---

### Substitution Basique : s/pattern/replace/

**Syntaxe** :
```bash
sed 's/pattern/replacement/' fichier
```

**Exemple** :

```bash
# Fichier
cat file.txt
# Hello world
# Hello universe
# Goodbye world

# Remplacer "world" par "earth" (1ère occurrence par ligne)
sed 's/world/earth/' file.txt
# Hello earth
# Hello universe
# Goodbye earth
```

**⚠️ Important** : Sans option, sed affiche sur **stdout** (pas de modification fichier)

---

### Flag /g : Global (Toutes Occurrences)

**Problème** : Substitution par défaut = **1ère occurrence** par ligne uniquement

```bash
# Fichier
cat file.txt
# foo bar foo baz foo

# Sans /g : 1ère occurrence seulement
sed 's/foo/XXX/' file.txt
# XXX bar foo baz foo  ← Seulement 1er foo remplacé

# Avec /g : Toutes occurrences
sed 's/foo/XXX/g' file.txt
# XXX bar XXX baz XXX  ← Tous foo remplacés
```

---

### Quotes Simples vs Doubles

**⚠️ Critique** : Toujours utiliser **quotes simples** pour pattern sed

**Raison** : Éviter interprétation **métacaractères** par bash

```bash
# ✅ CORRECT : Quotes simples
sed 's/old/new/g' file.txt

# ❌ RISQUÉ : Quotes doubles
sed "s/$USER/admin/g" file.txt
# Bash remplace $USER AVANT que sed ne le voie

# Exemple problème
PATTERN="*.txt"
sed "s/$PATTERN/file.txt/g" file.txt
# Bash expande $PATTERN (wildcards) → Erreur

# ✅ Solution : Quotes simples
sed 's/$PATTERN/file.txt/g' file.txt
# sed voit littéralement "$PATTERN"
```

**Règle** : **Quotes simples** sauf si besoin expansion variable

---

### Option -i : In-Place Editing

**Syntaxe** :
```bash
sed -i 's/pattern/replacement/g' fichier
```

**Effet** : Modifier fichier **directement** (pas stdout)

---

**Exemple** :

```bash
# Fichier original
cat config.txt
# SERVER=192.168.1.100
# PORT=8080
# DEBUG=true

# Sans -i : Affiche seulement
sed 's/DEBUG=true/DEBUG=false/g' config.txt
# SERVER=192.168.1.100
# PORT=8080
# DEBUG=false

# Fichier pas modifié
cat config.txt
# DEBUG=true  ← Inchangé

# Avec -i : Modifie fichier
sed -i 's/DEBUG=true/DEBUG=false/g' config.txt

# Vérifier modification
cat config.txt
# DEBUG=false  ← Modifié !
```

---

### Backup avec -i

**Syntaxe** :
```bash
sed -i.bak 's/pattern/replacement/g' fichier
```

**Effet** : Crée backup `fichier.bak` avant modification

```bash
# Modifier avec backup
sed -i.bak 's/old/new/g' file.txt

# Vérifier backup créé
ls
# file.txt      ← Modifié
# file.txt.bak  ← Original sauvegardé

# Restaurer si erreur
mv file.txt.bak file.txt
```

---

### Exemples sed Avancés

#### 1️⃣ Remplacer IP

```bash
# Fichier config
cat /etc/app/config.conf
# database_host=192.168.1.100
# cache_host=192.168.1.100

# Remplacer IP
sed -i 's/192\.168\.1\.100/10.0.0.50/g' /etc/app/config.conf
# ⚠️ Échapper points : \. (sinon . = n'importe quel caractère)

# Vérifier
cat /etc/app/config.conf
# database_host=10.0.0.50
# cache_host=10.0.0.50
```

---

#### 2️⃣ Remplacer Ligne Complète

```bash
# Remplacer ligne contenant "old_setting"
sed -i '/old_setting/c\new_setting=value' config.txt
```

---

#### 3️⃣ Supprimer Lignes

```bash
# Supprimer lignes vides
sed '/^$/d' file.txt

# Supprimer lignes commentaires (#)
sed '/^#/d' config.txt

# Supprimer lignes 2 à 5
sed '2,5d' file.txt

# Supprimer dernière ligne
sed '$d' file.txt
```

---

#### 4️⃣ Insérer Lignes

```bash
# Insérer avant ligne 3
sed '3i\Nouvelle ligne' file.txt

# Ajouter après ligne 5
sed '5a\Ligne ajoutée' file.txt

# Ajouter à la fin
sed '$a\Dernière ligne' file.txt
```

---

#### 5️⃣ Substitution Conditionnelle

```bash
# Remplacer seulement lignes contenant "server"
sed '/server/s/old/new/g' file.txt

# Remplacer entre lignes 10-20
sed '10,20s/old/new/g' file.txt
```

---

## 🔢 sort : Trier Lignes

### Rôle sort

**sort** = Trier lignes fichier

**Ordre par défaut** : **Alphabétique** (ASCII)

```bash
# Fichier non trié
cat names.txt
# bob
# alice
# charlie
# alice

# Trier alphabétiquement
sort names.txt
# alice
# alice
# bob
# charlie
```

---

### Options sort Courantes

```bash
# Trier en ordre inverse
sort -r names.txt
# charlie
# bob
# alice
# alice

# Trier numériquement (pas alphabétique)
sort -n numbers.txt
# 1
# 10
# 2  ← Alphabétique ❌
# 20

sort -n numbers.txt
# 1
# 2
# 10  ← Numérique ✅
# 20

# Trier par colonne (délimiteur)
sort -t ',' -k 2 data.csv  # Trier par 2e colonne

# Trier et supprimer doublons
sort -u names.txt
# alice  ← Une seule fois
# bob
# charlie

# Ignorer casse
sort -f names.txt
# Alice
# alice  ← Traitées pareil
# Bob
```

---

## 🔷 uniq : Supprimer Doublons

### Rôle uniq

**uniq** = Supprimer lignes **dupliquées consécutives**

**⚠️ Important** : Seulement lignes **consécutives** !

```bash
# Fichier avec doublons
cat file.txt
# apple
# apple
# banana
# apple  ← Pas consécutif avec premiers apple
# banana

# uniq seul (doublons consécutifs seulement)
uniq file.txt
# apple   ← 2 apple consécutifs → 1
# banana
# apple   ← Pas consécutif → Gardé
# banana  ← Doublon avec précédent → Gardé (car séparé)
```

**❌ Problème** : Doublons non-consécutifs **pas supprimés**

---

### Combinaison sort + uniq

**Solution** : **sort** avant **uniq** pour vraie unicité

```bash
# Fichier
cat file.txt
# apple
# apple
# banana
# apple
# banana

# sort PUIS uniq
sort file.txt | uniq
# apple   ← Toutes occurrences groupées → 1
# banana  ← Toutes occurrences groupées → 1

# OU : sort -u (équivalent)
sort -u file.txt
# apple
# banana
```

**💡 Règle** : **Toujours** `sort` avant `uniq` pour unicité complète

---

### Options uniq

```bash
# Compter occurrences
uniq -c file.txt
# 2 apple
# 1 banana
# 1 apple

# Avec sort
sort file.txt | uniq -c
# 3 apple  ← Total occurrences
# 2 banana

# Afficher seulement doublons
uniq -d file.txt

# Afficher seulement lignes uniques (non dupliquées)
uniq -u file.txt

# Ignorer N premiers caractères
uniq -s 2 file.txt  # Ignore 2 premiers chars

# Ignorer casse
uniq -i file.txt
```

---

### Workflow sort + uniq Typique

```bash
# Logs : Compter IPs uniques
cat access.log | cut -d ' ' -f 1 | sort | uniq -c | sort -rn
# 1234 192.168.1.100
# 567  203.0.113.50
# 89   10.0.0.25

# Étapes :
# 1. cut : Extraire IPs
# 2. sort : Trier IPs (grouper identiques)
# 3. uniq -c : Compter occurrences
# 4. sort -rn : Trier par nombre (reverse numeric)
```

---

## 🔍 diff : Comparer Fichiers

### Rôle diff

**diff** = Comparer **deux fichiers** et afficher différences

**Usage** :
- ✅ Vérifier modifications
- ✅ Comparer versions
- ✅ Créer patches

---

### Syntaxe Basique

```bash
diff fichier1 fichier2
```

**Fichiers exemple** :

```bash
# file1.txt
cat file1.txt
# Line 1
# Line 2 original
# Line 3
# Line 4 old

# file2.txt
cat file2.txt
# Line 1
# Line 2 modified
# Line 3
# Line 4 new
```

---

### Format Sortie par Défaut

```bash
diff file1.txt file2.txt
# 2c2
# < Line 2 original
# ---
# > Line 2 modified
# 4c4
# < Line 4 old
# ---
# > Line 4 new
```

**Signification** :

| Code | Signification | Exemple |
|------|---------------|---------|
| **NcM** | Ligne N de file1 **modifiée** (change) → ligne M de file2 | `2c2` |
| **NdM** | Ligne N de file1 **supprimée** (delete) | `3d2` |
| **NaM** | Ligne M **ajoutée** (add) dans file2 | `2a3` |
| **<** | Ligne de **file1** | `< Line 2 original` |
| **>** | Ligne de **file2** | `> Line 2 modified` |

---

### Format Codes diff

```bash
# Modification
2c2
# Ligne 2 file1 changée (c) → ligne 2 file2

# Suppression
3d2
# Ligne 3 file1 supprimée (d), correspondance ligne 2 file2

# Ajout
2a3
# Après ligne 2 file1, ajout (a) ligne 3 dans file2
```

---

### Option -c : Context (Contexte)

**Rôle** : Afficher lignes **autour** des différences (contexte)

```bash
diff -c file1.txt file2.txt
```

**Résultat** :
```
*** file1.txt   2026-02-02 10:30:00
--- file2.txt   2026-02-02 10:35:00
***************
*** 1,4 ****
  Line 1
! Line 2 original
  Line 3
! Line 4 old
--- 1,4 ----
  Line 1
! Line 2 modified
  Line 3
! Line 4 new
```

**Symboles** :
- `!` → Ligne modifiée
- `-` → Ligne supprimée
- `+` → Ligne ajoutée
- (espace) → Ligne identique (contexte)

---

### Option -y : Side by Side (Côte à Côte)

**Rôle** : Affichage **visuel** deux fichiers côte à côte

**Alias** : `sdiff`

```bash
# Option -y
diff -y file1.txt file2.txt

# OU commande sdiff (équivalent)
sdiff file1.txt file2.txt
```

**Résultat** :
```
Line 1                                                  Line 1
Line 2 original                    |                    Line 2 modified
Line 3                                                  Line 3
Line 4 old                         |                    Line 4 new
```

**Symboles** :
- `|` → Ligne **différente**
- `<` → Ligne seulement dans **file1**
- `>` → Ligne seulement dans **file2**
- (espace) → Ligne **identique**

---

### Option -y --suppress-common-lines

**Rôle** : Masquer lignes identiques (seulement différences)

```bash
diff -y --suppress-common-lines file1.txt file2.txt
# Line 2 original                    |                    Line 2 modified
# Line 4 old                         |                    Line 4 new
```

**💡 Très utile** : Fichiers longs avec peu de différences

---

### Option -u : Unified Format (Git)

**Rôle** : Format **patch** (utilisé par Git)

```bash
diff -u file1.txt file2.txt
```

**Résultat** :
```
--- file1.txt   2026-02-02 10:30:00
+++ file2.txt   2026-02-02 10:35:00
@@ -1,4 +1,4 @@
 Line 1
-Line 2 original
+Line 2 modified
 Line 3
-Line 4 old
+Line 4 new
```

**Symboles** :
- `-` → Ligne supprimée (file1)
- `+` → Ligne ajoutée (file2)
- (espace) → Ligne identique

---

### Comparaison Formats diff

| Format | Option | Usage | Lisibilité |
|--------|--------|-------|------------|
| **Défaut** | (aucune) | Script automatique | ⭐⭐ |
| **Context** | `-c` | Contexte détaillé | ⭐⭐⭐ |
| **Side by side** | `-y` ou `sdiff` | **Visuel humain** | ⭐⭐⭐⭐⭐ |
| **Unified** | `-u` | Patches Git | ⭐⭐⭐⭐ |

**💡 Recommandation** : `-y` (side by side) pour lecture humaine

---

### diff Répertoires

```bash
# Comparer deux dossiers
diff -r dir1/ dir2/

# Avec side by side
diff -ry dir1/ dir2/

# Seulement fichiers différents
diff -rq dir1/ dir2/
# Files dir1/config.txt and dir2/config.txt differ
# Only in dir1: old_file.txt
# Only in dir2: new_file.txt
```

---

## 📋 Antisèche - Filtres Texte

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `cat file` | Afficher fichier | `cat /etc/hosts` |
| `tac file` | Afficher inversé | `tac logs.txt` |
| `head -n N file` | N premières lignes | `head -20 file.txt` |
| `tail -n N file` | N dernières lignes | `tail -50 file.txt` |
| `tail -f file` | Suivre fichier temps réel | `tail -f /var/log/syslog` |
| `cut -d ',' -f 1` | Extraire colonne CSV | `cut -d ',' -f 1 data.csv` |
| `sed 's/old/new/g'` | Remplacer texte | `sed 's/foo/bar/g' file.txt` |
| `sed -i 's/old/new/g'` | Remplacer in-place | `sed -i 's/old/new/g' file.txt` |
| `sort file` | Trier alphabétiquement | `sort names.txt` |
| `sort -n file` | Trier numériquement | `sort -n numbers.txt` |
| `sort -u file` | Trier + unicité | `sort -u names.txt` |
| `uniq file` | Supprimer doublons consécutifs | `sort file.txt \| uniq` |
| `uniq -c file` | Compter occurrences | `sort file.txt \| uniq -c` |
| `diff file1 file2` | Comparer fichiers | `diff old.txt new.txt` |
| `diff -y file1 file2` | Comparaison visuelle | `diff -y old.txt new.txt` |
| `sdiff file1 file2` | Side by side (alias -y) | `sdiff old.txt new.txt` |
| `diff -c file1 file2` | Avec contexte | `diff -c old.txt new.txt` |

---

## 🎓 Points Clés pour l'Examen

✅ **cat** : Afficher fichier dans l'ordre  
✅ **tac** : Afficher fichier en ordre **inverse** (dernière → première ligne)  
✅ **head -n N** : N premières lignes  
✅ **tail -n N** : N dernières lignes  
✅ **tail -f** : Suivi temps réel (logs)  
✅ **cut -d 'delim' -f N** : Extraire champ N avec délimiteur  
✅ **sed 's/pattern/replace/g'** : Substituer texte (toutes occurrences avec `/g`)  
✅ **Quotes simples sed** : Éviter interprétation bash métacaractères  
✅ **sed -i** : Modification in-place (modifie fichier directement)  
✅ **sort** : Trier lignes alphabétiquement (ou `-n` numériquement)  
✅ **uniq** : Supprimer doublons **consécutifs** uniquement  
✅ **sort + uniq** : Combinaison obligatoire pour unicité complète  
✅ **uniq -c** : Compter occurrences  
✅ **diff** : Comparer fichiers (format NcM, NdM, NaM)  
✅ **diff -c** : Format avec contexte  
✅ **diff -y** ou **sdiff** : Side by side (visuel)  
✅ **diff -u** : Format unified (patches Git)  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Extraire IPs Uniques Logs Apache

```bash
# Fichier access.log
cat access.log
# 192.168.1.100 - - [02/Feb/2026:10:30:00] "GET /" 200
# 203.0.113.50 - - [02/Feb/2026:10:30:05] "GET /api" 200
# 192.168.1.100 - - [02/Feb/2026:10:30:10] "POST /login" 302
# 10.0.0.25 - - [02/Feb/2026:10:30:15] "GET /" 200

# Pipeline extraction IPs
cat access.log | cut -d ' ' -f 1 | sort | uniq
# 10.0.0.25
# 192.168.1.100
# 203.0.113.50

# Avec compteur occurrences
cat access.log | cut -d ' ' -f 1 | sort | uniq -c | sort -rn
# 2 192.168.1.100
# 1 203.0.113.50
# 1 10.0.0.25
```

---

### Scénario 2 : Modifier Configuration Production

```bash
# Fichier config.ini
cat /etc/app/config.ini
# [database]
# host=localhost
# port=3306
# debug=true
#
# [cache]
# host=localhost
# port=6379

# Remplacer localhost par IP production (avec backup)
sed -i.bak 's/localhost/192.168.1.100/g' /etc/app/config.ini

# Vérifier backup créé
ls /etc/app/
# config.ini      ← Modifié
# config.ini.bak  ← Original

# Vérifier modifications
cat /etc/app/config.ini
# host=192.168.1.100
# ...

# Désactiver debug
sed -i 's/debug=true/debug=false/g' /etc/app/config.ini

# Comparer versions
diff -y /etc/app/config.ini.bak /etc/app/config.ini
# host=localhost                     |    host=192.168.1.100
# debug=true                         |    debug=false
```

---

### Scénario 3 : Analyser Logs Erreurs

```bash
# Logs application
cat app.log
# 2026-02-02 10:00:00 INFO Starting application
# 2026-02-02 10:00:05 ERROR Database connection failed
# 2026-02-02 10:00:10 INFO Retrying connection
# 2026-02-02 10:00:15 ERROR Database connection failed
# 2026-02-02 10:00:20 WARN High memory usage
# 2026-02-02 10:00:25 ERROR API request timeout

# Extraire seulement erreurs
grep ERROR app.log
# 2026-02-02 10:00:05 ERROR Database connection failed
# 2026-02-02 10:00:15 ERROR Database connection failed
# 2026-02-02 10:00:25 ERROR API request timeout

# Compter types erreurs (enlever timestamps)
grep ERROR app.log | cut -d ' ' -f 4- | sort | uniq -c
# 2 Database connection failed
# 1 API request timeout

# Pipeline complet : Erreurs par type, triées par fréquence
grep ERROR app.log | cut -d ' ' -f 4- | sort | uniq -c | sort -rn
# 2 Database connection failed
# 1 API request timeout
```

---

### Scénario 4 : CSV Manipulation

```bash
# Fichier employees.csv
cat employees.csv
# John,Doe,Engineering,75000
# Alice,Smith,Marketing,65000
# Bob,Jones,Engineering,80000
# Charlie,Brown,Sales,70000

# Extraire département (3e colonne)
cut -d ',' -f 3 employees.csv
# Engineering
# Marketing
# Engineering
# Sales

# Départements uniques
cut -d ',' -f 3 employees.csv | sort -u
# Engineering
# Marketing
# Sales

# Compter employés par département
cut -d ',' -f 3 employees.csv | sort | uniq -c
# 2 Engineering
# 1 Marketing
# 1 Sales

# Extraire noms (colonnes 1-2)
cut -d ',' -f 1-2 employees.csv
# John,Doe
# Alice,Smith
# Bob,Jones
# Charlie,Brown
```

---

### Scénario 5 : Comparer Configs Avant/Après

```bash
# Configuration avant mise à jour
cat config-old.txt
# server_name=web01
# port=8080
# ssl_enabled=false
# max_connections=100

# Configuration après mise à jour
cat config-new.txt
# server_name=web01
# port=443
# ssl_enabled=true
# max_connections=200
# backup_enabled=true

# Comparaison visuelle
diff -y config-old.txt config-new.txt
# server_name=web01                              server_name=web01
# port=8080                          |           port=443
# ssl_enabled=false                  |           ssl_enabled=true
# max_connections=100                |           max_connections=200
#                                    >           backup_enabled=true

# Seulement différences
diff -y --suppress-common-lines config-old.txt config-new.txt
# port=8080                          |           port=443
# ssl_enabled=false                  |           ssl_enabled=true
# max_connections=100                |           max_connections=200
#                                    >           backup_enabled=true

# Format contexte
diff -c config-old.txt config-new.txt
```

---

### Scénario 6 : Inverser Logs Chronologie

```bash
# Logs chronologiques (ancien → récent)
cat /var/log/app.log
# 2026-02-01 10:00:00 App started
# 2026-02-01 11:00:00 User login
# 2026-02-01 12:00:00 API call
# 2026-02-02 09:00:00 App started

# Afficher récent → ancien (tac)
tac /var/log/app.log
# 2026-02-02 09:00:00 App started
# 2026-02-01 12:00:00 API call
# 2026-02-01 11:00:00 User login
# 2026-02-01 10:00:00 App started

# 10 événements plus récents en premier
tac /var/log/app.log | head -10
```

---

### Scénario 7 : Nettoyage Doublons Liste

```bash
# Liste emails avec doublons
cat emails.txt
# john@example.com
# alice@example.com
# bob@example.com
# john@example.com
# alice@example.com
# charlie@example.com

# Trier + supprimer doublons
sort emails.txt | uniq
# alice@example.com
# bob@example.com
# charlie@example.com
# john@example.com

# Ou directement
sort -u emails.txt

# Compter occurrences (trouver spam)
sort emails.txt | uniq -c | sort -rn
# 2 alice@example.com
# 2 john@example.com
# 1 charlie@example.com
# 1 bob@example.com

# Extraire seulement doublons
sort emails.txt | uniq -d
# alice@example.com
# john@example.com
```

---

### Scénario 8 : Modifier Hosts File

```bash
# /etc/hosts actuel
cat /etc/hosts
# 127.0.0.1 localhost
# 192.168.1.100 server01
# 192.168.1.101 server02

# Remplacer subnet (avec backup)
sudo sed -i.bak 's/192\.168\.1\./10.0.0./g' /etc/hosts

# Vérifier
cat /etc/hosts
# 127.0.0.1 localhost
# 10.0.0.100 server01
# 10.0.0.101 server02

# Comparer avant/après
diff -y /etc/hosts.bak /etc/hosts
# 127.0.0.1 localhost                            127.0.0.1 localhost
# 192.168.1.100 server01            |           10.0.0.100 server01
# 192.168.1.101 server02            |           10.0.0.101 server02
```

---

### Scénario 9 : Extraire URLs Logs Nginx

```bash
# Logs nginx
cat /var/log/nginx/access.log
# 192.168.1.100 - - [02/Feb/2026] "GET /index.html HTTP/1.1" 200
# 203.0.113.50 - - [02/Feb/2026] "POST /api/users HTTP/1.1" 201
# 192.168.1.100 - - [02/Feb/2026] "GET /about.html HTTP/1.1" 200
# 10.0.0.25 - - [02/Feb/2026] "GET /api/users HTTP/1.1" 200

# Extraire URLs (7e champ)
cut -d '"' -f 2 /var/log/nginx/access.log | cut -d ' ' -f 2
# /index.html
# /api/users
# /about.html
# /api/users

# URLs uniques
cut -d '"' -f 2 /var/log/nginx/access.log | cut -d ' ' -f 2 | sort -u
# /about.html
# /api/users
# /index.html

# URLs les plus demandées
cut -d '"' -f 2 /var/log/nginx/access.log | cut -d ' ' -f 2 | sort | uniq -c | sort -rn
# 2 /api/users
# 1 /index.html
# 1 /about.html
```

---

### Scénario 10 : Comparer Versions Code Source

```bash
# Version 1.0
cat app-v1.0.py
# def hello():
#     print("Hello World")
#
# def add(a, b):
#     return a + b

# Version 1.1
cat app-v1.1.py
# def hello(name="World"):
#     print(f"Hello {name}")
#
# def add(a, b):
#     return a + b
#
# def multiply(a, b):
#     return a * b

# Comparaison visuelle
diff -y app-v1.0.py app-v1.1.py
# def hello():                       |           def hello(name="World"):
#     print("Hello World")           |               print(f"Hello {name}")
#                                                
# def add(a, b):                                 def add(a, b):
#     return a + b                                   return a + b
#                                    >
#                                    >           def multiply(a, b):
#                                    >               return a * b

# Format unified (patch Git)
diff -u app-v1.0.py app-v1.1.py
# --- app-v1.0.py
# +++ app-v1.1.py
# @@ -1,4 +1,7 @@
# -def hello():
# -    print("Hello World")
# +def hello(name="World"):
# +    print(f"Hello {name}")
#  
#  def add(a, b):
#      return a + b
# +
# +def multiply(a, b):
# +    return a * b
```

---

## ⚠️ Erreurs Courantes

### Erreur 1 : uniq Sans sort

```bash
# ❌ ERREUR : uniq sans sort
cat file.txt
# apple
# banana
# apple  ← Pas consécutif avec premier
# banana

uniq file.txt
# apple
# banana
# apple  ← Toujours présent !
# banana

# ✅ CORRECTION : sort avant uniq
sort file.txt | uniq
# apple  ← Unicité complète
# banana
```

---

### Erreur 2 : Quotes Doubles sed

```bash
# ❌ RISQUE : Quotes doubles
USER="john"
sed "s/$USER/admin/g" file.txt
# Bash remplace $USER AVANT sed → OK si volontaire

# Problème : Métacaractères bash
sed "s/*.txt/file.txt/g" file.txt
# bash: no match: *.txt

# ✅ SÉCURITÉ : Quotes simples
sed 's/*.txt/file.txt/g' file.txt
# sed voit littéralement *
```

---

### Erreur 3 : sed Sans /g

```bash
# Fichier
cat file.txt
# foo bar foo baz

# ❌ Sans /g : 1ère occurrence seulement
sed 's/foo/XXX/' file.txt
# XXX bar foo baz  ← Deuxième foo pas remplacé

# ✅ Avec /g : Toutes occurrences
sed 's/foo/XXX/g' file.txt
# XXX bar XXX baz
```

---

### Erreur 4 : Oublier Échapper Points sed

```bash
# ❌ ERREUR : Point non échappé
sed 's/192.168.1.100/10.0.0.50/' file.txt
# . = N'importe quel caractère !
# Matche 192X168X1X100 aussi

# ✅ CORRECT : Échapper points
sed 's/192\.168\.1\.100/10.0.0.50/' file.txt
# \. = Point littéral
```

---

### Erreur 5 : cut Délimiteur Incorrect

```bash
# Fichier CSV
cat data.csv
# John,Doe,30
# Alice,Smith,25

# ❌ ERREUR : Délimiteur espace (fichier CSV)
cut -d ' ' -f 1 data.csv
# John,Doe,30  ← Toute ligne (pas de délimiteur espace)

# ✅ CORRECT : Délimiteur virgule
cut -d ',' -f 1 data.csv
# John
# Alice
```

---

**🎯 Prochaine étape** : Work on the Command Line - Part 3 (Commandes shell avancées)

*Dernière mise à jour: 2 février 2026*
