# 🖥️ Environnements de Bureau Linux et Accès Distant

## 📋 Informations du Module

- **Examen** : LPIC-1 102
- **Objectif** : 106.2 - Graphical desktops
- **Cours** : GNU/Linux Graphical Desktops & Remote Desktop Access
- **Poids** : 1 point
- **Objectif** : Connaître les principaux environnements de bureau, Display Managers et protocoles d'accès distant

---

## 🎯 Display Managers - Gestionnaires de Connexion

### 🚪 Concept de Display Manager

**Display Manager (DM)** : Programme gérant les **connexions graphiques** (login screen GUI).

**Rôle** :
```
┌──────────────────────────────────────────────────────┐
│  1. Démarrage automatique au boot (systemd)         │
│     → systemctl enable gdm                           │
│                                                      │
│  2. Initialisation serveur X11 ou Wayland           │
│     → Lance Xorg ou Wayland compositor               │
│                                                      │
│  3. Affichage écran de connexion (login screen)     │
│     → Interface graphique login/password             │
│                                                      │
│  4. Authentification utilisateur (PAM)              │
│     → Vérifie credentials contre /etc/passwd, LDAP   │
│                                                      │
│  5. Lancement session utilisateur                   │
│     → Démarre DE/WM sélectionné (GNOME, KDE...)      │
│     → Initialise environnement (~/.xsession)         │
│                                                      │
│  6. Gestion multi-sessions                          │
│     → Plusieurs users simultanés (VT switching)      │
│                                                      │
│  7. Connexions distantes (XDMCP - optionnel)        │
│     → Clients réseau peuvent se connecter            │
└──────────────────────────────────────────────────────┘
```

**Différence avec startx** :
```bash
# Sans Display Manager (manuel)
startx
# - User démarre session depuis TTY
# - Pas de login graphique
# - ~/.xinitrc exécuté

# Avec Display Manager (automatique)
# systemctl enable gdm
# - Démarrage automatique au boot
# - Login screen graphique
# - Multi-user support
# - Session management
```

---

### 🔐 XDMCP - X Display Manager Control Protocol

**XDMCP** : Protocole permettant connexions **distantes** via Display Manager.

**Principe** :
```
┌──────────────────┐                    ┌──────────────────┐
│  CLIENT Distant  │                    │  SERVEUR X       │
│                  │                    │  (Display Mgr)   │
│  ┌────────────┐  │  ─── XDMCP ───>   │  ┌────────────┐  │
│  │ X Server   │  │  (Port 177 UDP)    │  │    GDM     │  │
│  │ (Display)  │◄─┼────────────────────┼──│  Session   │  │
│  └────────────┘  │  X11 Protocol      │  └────────────┘  │
│                  │  (Port 6000+ TCP)  │                  │
│  Affichage local │                    │  Apps exécutées  │
└──────────────────┘                    └──────────────────┘
```

**Flux de connexion** :
```
1. Client XDMCP envoie broadcast Query (UDP 177)
   → "Cherche serveurs X disponibles"

2. Serveur(s) répondent Willing (infos disponibilité)
   → "Je suis disponible, voici mes capacités"

3. Client envoie Request (demande connexion)
   → "Je veux me connecter"

4. Serveur répond Accept (accepte connexion)
   → "Connexion acceptée, session ID: XXX"

5. Client envoie Manage (demande affichage login)
   → "Affiche-moi l'écran de login"

6. Serveur affiche login screen sur client distant
   → Interface graphique login apparaît

7. Utilisateur entre credentials, session démarre
   → Desktop Environment lancé
   → Applications s'exécutent sur serveur
   → Affichage redirigé vers client
```

**Ports utilisés** :
```
UDP 177   → XDMCP (queries, control)
TCP 6000+ → X11 (display data)
TCP 6001  → Display :1
...
```

**⚠️ SÉCURITÉ** :
```
❌ Trafic NON CHIFFRÉ (passwords en clair !)
❌ Pas d'authentification forte
❌ Vulnérable Man-in-the-Middle
❌ UDP broadcast (révèle serveurs réseau)

→ NE PAS utiliser sur réseaux non sécurisés
→ Préférer SSH X11 Forwarding (chiffré)
→ Ou VPN + XDMCP si nécessaire
→ Ou SSH tunneling pour XDMCP
```

**Configuration XDMCP** :

**GDM (GNOME)** :
```bash
# /etc/gdm3/custom.conf
[xdmcp]
Enable=true
Port=177
MaxSessions=10
MaxSessionsPerDisplay=2

# Redémarrer GDM
sudo systemctl restart gdm3

# Firewall
sudo ufw allow 177/udp
sudo ufw allow 6000:6063/tcp
```

**LightDM** :
```bash
# /etc/lightdm/lightdm.conf
[XDMCPServer]
enabled=true
port=177

sudo systemctl restart lightdm
```

**SDDM (KDE)** :
```bash
# /etc/sddm.conf
[XDisplay]
EnableXDMCP=true
XDMCPPort=177

sudo systemctl restart ssdm
```

**Client XDMCP (connexion)** :
```bash
# X :1 -query 192.168.1.100
# → Affiche login screen du serveur 192.168.1.100 sur display local :1

# Ou via Xnest (X server imbriqué)
Xnest :1 -query 192.168.1.100 -geometry 1280x1024

# Ou avec connexion indirecte (chooser)
X :1 -indirect 192.168.1.100
# → Affiche liste des serveurs disponibles
```

**État actuel XDMCP (2026)** :
```
⚠️  OBSOLÈTE et DÉPRÉCIÉ
→ Remplacé par :
  - SSH X11 Forwarding (chiffré)
  - VNC over SSH
  - RDP (Remote Desktop Protocol)
  - SPICE (Virtual Machines)

→ Raisons abandon :
  - Sécurité (trafic clair)
  - Complexité (multiples ports)
  - Remplacé par meilleures alternatives

→ Encore mentionné dans LPIC-1 (historique)
```

---

### 🎨 Display Managers Modernes

**Comparaison des Display Managers** :

| Display Manager | DE Associé | Technologie | Caractéristiques | Utilisation |
|-----------------|------------|-------------|------------------|-------------|
| **GDM** | GNOME | Wayland/X11 | Moderne, GNOME intégré | Ubuntu, Fedora |
| **SDDM** | KDE Plasma | Qt, Wayland/X11 | KDE natif, thèmes Qt | KDE distros |
| **LightDM** | Universel | X11 | Léger, multi-greeters | Xubuntu, Mint |
| **XDM** | Universel | X11 | Minimaliste, basique | Legacy/Minimal |
| **LXDM** | LXDE/LXQt | GTK/Qt | Ultra-léger | LXDE distros |
| **Ly** | Universel | TUI | Terminal UI (pas GUI) | Minimalistes |

---

#### 🟦 GDM - GNOME Display Manager

**Description** : Display Manager officiel de GNOME.

**Caractéristiques** :
- Support Wayland natif (prioritaire)
- Fallback X11 automatique
- Intégration GNOME complète
- Accessibilité (screen reader, high contrast)
- Session chooser (GNOME, GNOME on Xorg)
- Multi-user simultané

**Installation** :
```bash
# Debian/Ubuntu
sudo apt install gdm3

# Arch
sudo pacman -S gdm

# Enable/Start
sudo systemctl enable gdm
sudo systemctl start gdm
```

**Configuration** :
```bash
# Fichier principal
/etc/gdm3/custom.conf

[daemon]
AutomaticLoginEnable=false
AutomaticLogin=username          # Autologin (optionnel)
TimedLoginEnable=false
TimedLogin=username
TimedLoginDelay=10

[security]
AllowRoot=false                  # Bloquer login root

[xdmcp]
Enable=false                     # XDMCP (déprécié)

[greeter]
IncludeAll=true                  # Tous users dans liste
```

**Changer DE par défaut** :
```bash
# Fichier de session par défaut
/var/lib/AccountsService/users/USERNAME

[User]
XSession=gnome                   # ou kde-plasma, xfce
Session=gnome                    # Wayland session
```

**Logs** :
```bash
journalctl -u gdm
~/.xsession-errors
/var/log/Xorg.0.log
```

---

#### 🟦 SSDM - Simple Desktop Display Manager

**Description** : Display Manager moderne pour KDE Plasma.

**Caractéristiques** :
- Qt natif (cohérence KDE)
- Wayland + X11 support
- Thèmes customisables QML
- Léger et rapide
- Multi-seat support

**Installation** :
```bash
# Debian/Ubuntu
sudo apt install sddm

# Arch
sudo pacman -S sddm

# Enable
sudo systemctl enable sddm
sudo systemctl start sddm
```

**Configuration** :
```bash
# Générer config par défaut
sddm --example-config | sudo tee /etc/sddm.conf

# Éditer
sudo nano /etc/sddm.conf

[General]
DisplayServer=wayland            # ou x11
Numlock=on
InputMethod=

[Theme]
Current=breeze                   # Thème KDE par défaut
CursorTheme=breeze_cursors

[Users]
MaximumUid=60000
MinimumUid=1000
HideUsers=                       # Users à cacher
HideShells=/sbin/nologin,/bin/false

[Autologin]
User=                            # Username pour autologin
Session=plasma.desktop

[XDisplay]
ServerPath=/usr/bin/X
MinimumVT=7
```

**Thèmes** :
```bash
# Lister thèmes disponibles
ls /usr/share/sddm/themes/

# Installer nouveaux thèmes
git clone https://github.com/user/sddm-theme.git
sudo mv sddm-theme /usr/share/sddm/themes/

# Sélectionner thème
sudo nano /etc/sddm.conf
[Theme]
Current=sddm-theme
```

---

#### 🟦 LightDM - Lightweight Display Manager

**Description** : Display Manager léger et universel.

**Caractéristiques** :
- Très léger (minimal resources)
- Multi-greeter support (interfaces swap)
- Indépendant DE (fonctionne avec tous)
- Scriptable
- Session chooser

**Installation** :
```bash
# Debian/Ubuntu
sudo apt install lightdm

# Greeters (interfaces)
sudo apt install lightdm-gtk-greeter        # GTK (standard)
sudo apt install slick-greeter              # Moderne
sudo apt install lightdm-webkit2-greeter    # Thèmes web

# Enable
sudo systemctl enable lightdm
sudo systemctl start lightdm
```

**Configuration** :
```bash
# /etc/lightdm/lightdm.conf

[Seat:*]
greeter-session=lightdm-gtk-greeter
user-session=xfce                # Session par défaut
autologin-user=
autologin-user-timeout=0
allow-guest=false
greeter-hide-users=false

[XDMCPServer]
enabled=false                    # XDMCP
```

**Greeter GTK** :
```bash
# /etc/lightdm/lightdm-gtk-greeter.conf

[greeter]
theme-name=Arc-Dark
icon-theme-name=Papirus
background=/usr/share/backgrounds/default.jpg
font-name=Sans 11
indicators=~host;~spacer;~clock;~spacer;~session;~a11y;~power
```

**Commandes utiles** :
```bash
# Tester greeter (sans redémarrer DM)
sudo lightdm --test-mode --debug

# Logs
journalctl -u lightdm
/var/log/lightdm/lightdm.log
/var/log/lightdm/x-0.log
```

---

#### 🟦 XDM - X Display Manager

**Description** : Display Manager historique (original X.org).

**Caractéristiques** :
- Minimaliste (texte noir/blanc basique)
- Très léger
- Pas de features modernes
- Legacy

**Installation** :
```bash
sudo apt install xdm
sudo systemctl enable xdm
```

**Configuration principale** :
```bash
/etc/X11/xdm/              # Répertoire de configuration XDM
/etc/X11/xdm/xdm-config    # Fichier de configuration principal (1er fichier lu)
```

---

**Fichiers de configuration XDM détaillés** :

**1️⃣ Xsetup** (`/etc/X11/xdm/Xsetup` ou `DisplayManager.*.setup`)

**Moment d'exécution** : **AVANT** l'affichage de la boîte de login.

**Rôle** : Modification présentation de l'affichage et de XDM.

**Utilisations courantes** :
```bash
# Modifier le fond d'écran
xsetroot -solid steelblue

# Modifier les couleurs avec ressources X
xrdb -merge /etc/X11/xdm/my-resources

# Changer géométrie (positions/tailles) des boîtes de dialogue
# (via ressources X dans my-resources)

# Afficher une console système
xconsole -geometry 480x130-0-0 &

# Activer le clavier numérique
numlockx on

# Modifier le clavier (mapping touches)
xmodmap /etc/X11/xdm/Xmodmap
```

**2️⃣ RunChooser** (`/etc/X11/xdm/RunChooser` ou `DisplayManager.*.chooser`)

**Rôle** : Afficher la boîte de dialogue des **serveurs X distants** pour s'y connecter.

**Fonction** :
- Permet le paramétrage au lancement du programme `chooser`
  - `/usr/X11R6/bin/chooser`
  - `/usr/lib/X11/chooser`
  - Ou tout autre programme ayant le même rôle

**⚠️ Condition** : Ne sert que si le serveur X distant accepte les connexions via **XDMCP**.

**3️⃣ Xstartup** (`/etc/X11/xdm/Xstartup` ou `DisplayManager.*.startup`)

**Moment d'exécution** : Premier script exécuté **APRÈS** la réussite de l'authentification.

**Droits** : Exécuté avec les **droits de root** 🔑.

**Rôle** :
```bash
# Effacer l'écran
clear

# Écrire les informations de connexion dans fichiers journaux
logger "User $USER logged in from $DISPLAY"

# Vérifier si connexion locale ou distante
if [ "$DISPLAY" = ":0" ]; then
    echo "Connexion locale"
fi

# Vérifier si l'utilisateur est autorisé à se connecter
# (vérifications supplémentaires de sécurité)
```

**4️⃣ Xsession** (`/etc/X11/xdm/Xsession` ou `DisplayManager.*.session`)

**Moment d'exécution** : Premier script exécuté **APRÈS** l'authentification.

**Droits** : Exécuté avec les **droits de l'utilisateur authentifié** 👤.

**⚠️ Si absent** : X exécute une commande `xterm` qui sera le processus de session de l'utilisateur.

**Ordre de lancement des fichiers** :

Après une configuration de base, `Xsession` tente de lancer **dans cet ordre** :

1. `$HOME/.xsession` (propre à l'utilisateur) ← **PRIORITÉ 1**
2. Si absent : `$HOME/.xinitrc` (propre à l'utilisateur)
3. Si absent : `/etc/X11/xdm/sys.xsession`
4. Si absent : `/etc/X11/xinit/xinitrc`

**5️⃣ Xreset** (`/etc/X11/xdm/Xreset` ou `DisplayManager.*.reset`)

**Moment d'exécution** : Lorsque l'utilisateur **ferme sa session**.

**Rôle** :
```bash
# Messages de logs de déconnexion
logger "User $USER logged out from $DISPLAY"

# Nettoyage temporaire
rm -rf /tmp/.X${DISPLAY}-lock
```

**6️⃣ Xresources** (`/etc/X11/xdm/Xresources` ou `DisplayManager*resources`)

**Format** : Interprété par XDM.

**Rôle** : Définitions des **ressources visuelles** utilisées par XDM.

**Contenu** :
```bash
# Polices de caractères
xlogin*font: -*-helvetica-medium-r-normal--14-*
xlogin*promptFont: -*-helvetica-bold-r-normal--14-*

# Messages d'accueil
xlogin*greeting: Bienvenue sur HOSTNAME

# Message d'échec
xlogin*fail: Échec de connexion

# Couleurs
xlogin*background: #2C3E50
xlogin*foreground: #ECF0F1

# Adaptation affichage selon nombre de couleurs
xlogin*logoFileName: /usr/share/pixmaps/logo.xpm

# Adaptation taille écran
xlogin*geometry: 400x300+100+100
```

---

**Configuration GDM et KDM** :

**🟢 GDM (GNOME Display Manager)** :

Par défaut, **GDM utilise les mêmes fichiers de configuration que XDM**.

**Chemins configuration spécifiques GDM** :
```bash
/etc/X11/gdm/gdm.conf
# OU
/etc/gdm/gdm.conf
# OU
/usr/share/gdm/gdm.conf
```

**Configuration avancée** :
- Configuration avancée de GDM passe par la configuration des fichiers de XDM
- Configuration utilise également des fichiers spécifiques à GDM
- Peut aussi s'effectuer via l'interface graphique associée

**🔵 KDM (KDE Display Manager)** :

Par défaut, **KDM utilise les mêmes fichiers de configuration que XDM**.

**Chemins configuration spécifiques KDM** :
```bash
/etc/X11/kdm/kdmrc
# OU
/etc/KDE5/kdm/kdmrc
# OU
/etc/kde/kdm/kdmrc
```

**Configuration avancée** :
- Configuration avancée de KDM passe par la configuration des fichiers de XDM
- Configuration utilise également des fichiers spécifiques à KDM
- Peut aussi s'effectuer via l'interface graphique associée

---

**Démarrage automatique avec init (legacy /etc/inittab)** :

Sur les systèmes utilisant encore **SysVinit** (anciens systèmes) :

```bash
# /etc/inittab
xdm:5:respawn:/usr/X11R6/bin/xdm

# OU (auto-détection Display Manager préféré)
x:5:respawn:/etc/X11/prefdm -nodaemon
```

**Format** :
- `xdm` : identifiant unique
- `5` : runlevel 5 (mode graphique)
- `respawn` : redémarrer si le processus se termine
- `/usr/X11R6/bin/xdm` : commande à exécuter

**⚠️ Note** : `/etc/inittab` est **obsolète** sur systèmes systemd modernes.

---

**Session précédente (mémorisation du choix utilisateur)** :

**Ancien système** : `~/.dmrc`
```bash
# ~/.dmrc
[Desktop]
Session=gnome
Language=fr_FR.UTF-8
```

**Système récent** : `/var/lib/ServiceAccounts/users/$USER`

Le fichier porte le **nom de l'utilisateur** :
```bash
/var/lib/ServiceAccounts/users/alice
/var/lib/ServiceAccounts/users/bob
```

**Fichiers de description des sessions** :

**Emplacement** : `/usr/share/xsessions/*.desktop`

**Format** : Fichiers `.desktop` décrivant chaque session disponible.

**Exemple** (`/usr/share/xsessions/gnome.desktop`) :
```ini
[Desktop Entry]
Name=GNOME
Comment=GNOME Desktop Environment
Exec=gnome-session
TryExec=gnome-session
Type=Application
DesktopNames=GNOME
```

**Exemple** (`/usr/share/xsessions/xfce.desktop`) :
```ini
[Desktop Entry]
Name=Xfce Session
Comment=Xfce Desktop Environment
Exec=startxfce4
TryExec=startxfce4
Type=Application
DesktopNames=XFCE
```

**⚠️ Utilisation** : Systèmes minimaux seulement (XDM déprécié).


---

### 🔄 Changer de Display Manager

**Méthode 1 : dpkg-reconfigure (Debian/Ubuntu)** :
```bash
# Change default DM
sudo dpkg-reconfigure gdm3
# OU
sudo dpkg-reconfigure lightdm

# Interface graphique de sélection apparaît
# → Choisir DM souhaité
```

**Méthode 2 : Systemd** :
```bash
# Vérifier DM actuel
systemctl status display-manager.service

# Désactiver actuel
sudo systemctl disable gdm

# Activer nouveau
sudo systemctl enable sddm

# Redémarrer
sudo reboot
```

**Méthode 3 : Lien symbolique (manuel)** :
```bash
# Le service display-manager est un lien symbolique
ls -l /etc/systemd/system/display-manager.service
# lrwxrwxrwx ... /lib/systemd/system/gdm.service

# Changer manuellement
sudo rm /etc/systemd/system/display-manager.service
sudo ln -s /lib/systemd/system/sddm.service /etc/systemd/system/display-manager.service

sudo reboot
```

---

## 🏢 Desktop Environments - Environnements de Bureau

### 📊 Comparaison des Principaux DE

| DE | Début | Toolkit | RAM (idle) | Philosophie | Utilisation |
|----|-------|---------|------------|-------------|-------------|
| **GNOME** | 1999 | GTK+ | ~1.5 GB | Productivité, accessibilité | Ubuntu, Fedora |
| **KDE Plasma** | 1996 | Qt | ~800 MB | Personnalisation, features | Kubuntu, Manjaro |
| **XFCE** | 1999 | GTK+ | ~500 MB | Léger, modulaire, stable | Xubuntu, Mint XFCE |
| **Cinnamon** | 2011 | GTK+ | ~800 MB | Familier, intuitif | Linux Mint |
| **MATE** | 2011 | GTK+ | ~400 MB | GNOME 2 legacy, classique | Ubuntu MATE |
| **LXQt** | 2013 | Qt | ~300 MB | Ultra-léger, moderne | Lubuntu |
| **LXDE** | 2006 | GTK+ | ~250 MB | Très léger, simple | Anciennes machines |

---

### 🟣 GNOME - GNU Network Object Model Environment

**Histoire** :
```
1999  → GNOME 1.0 (fork de KDE pour licence libre)
2002  → GNOME 2.0 (stable, populaire)
2011  → GNOME 3.0 (Shell moderne, controverse)
2021  → GNOME 40+ (numérotation changée)
2026  → GNOME 47+ (actuel)
```

**Philosophie** :
- **Productivité** : Interface épurée, focus contenu
- **Accessibilité** : Screen readers, high contrast, zoom
- **Simplicité** : Moins d'options, choix opinionés
- **Workflow moderne** : Activities, workspaces dynamiques

**Composants GNOME** :

```
┌────────────────────────────────────────────────┐
│  GNOME Desktop Environment                     │
├────────────────────────────────────────────────┤
│                                                │
│  Window Manager:                               │
│  - Mutter (Wayland/X11 compositor)             │
│                                                │
│  Shell:                                        │
│  - GNOME Shell (interface principale)          │
│  - Activities Overview                         │
│  - Top Bar, Dash                               │
│                                                │
│  Applications:                                 │
│  - Nautilus (Files)                            │
│  - GNOME Terminal                              │
│  - gedit / GNOME Text Editor                   │
│  - Evince (PDF)                                │
│  - Eye of GNOME (Images)                       │
│  - GNOME Calendar, Contacts, Photos            │
│  - Software (App Store)                        │
│                                                │
│  Settings:                                     │
│  - GNOME Control Center                        │
│  - GNOME Tweaks (customization avancée)        │
│                                                │
│  Session:                                      │
│  - gnome-session (gestion démarrage)           │
│  - GDM (Display Manager)                       │
│                                                │
│  Framework:                                    │
│  - GTK+ 4 (toolkit graphique)                  │
│  - GLib (bibliothèques core)                   │
│  - D-Bus (communication inter-processus)       │
└────────────────────────────────────────────────┘
```

**Installation** :
```bash
# Debian/Ubuntu
sudo apt install gnome-core              # Minimal
sudo apt install gnome                   # Complet
sudo apt install ubuntu-gnome-desktop    # Ubuntu GNOME

# Arch
sudo pacman -S gnome
sudo pacman -S gnome-extra

# Enable GDM
sudo systemctl enable gdm
```

**Personnalisation** :
```bash
# GNOME Tweaks (requis pour personnalisation avancée)
sudo apt install gnome-tweaks

# Extensions
# https://extensions.gnome.org/
sudo apt install gnome-shell-extensions
sudo apt install chrome-gnome-shell      # Intégration navigateur

# Extensions populaires :
# - Dash to Dock
# - User Themes
# - Clipboard Indicator
# - Caffeine
```

**Configuration (gsettings / dconf)** :
```bash
# Lister schémas
gsettings list-schemas | grep desktop

# Wallpaper
gsettings set org.gnome.desktop.background picture-uri file:///path/to/image.jpg

# Thème
gsettings set org.gnome.desktop.interface gtk-theme 'Adwaita-dark'
gsettings set org.gnome.desktop.interface icon-theme 'Papirus'

# Favoris (Dash)
gsettings set org.gnome.shell favorite-apps "['firefox.desktop', 'org.gnome.Terminal.desktop']"

# Workspaces dynamiques
gsettings set org.gnome.mutter dynamic-workspaces true

# Animations
gsettings set org.gnome.desktop.interface enable-animations false

# Button layout
gsettings set org.gnome.desktop.wm.preferences button-layout 'close,minimize,maximize:'
```

**Avantages** :
- ✅ Moderne et élégant
- ✅ Support Wayland natif excellent
- ✅ Accessibilité de pointe
- ✅ Intégration GNOME Online Accounts (Google, etc.)
- ✅ Application Store bien fourni

**Inconvénients** :
- ❌ Consommation RAM élevée
- ❌ Peu d'options natives (requiert extensions)
- ❌ Workflow différent (courbe apprentissage)
- ❌ Performance sur vieux hardware faible

---

### 🔵 KDE Plasma - K Desktop Environment

**Histoire** :
```
1996  → KDE 1.0 (premier DE Qt)
2008  → KDE 4.0 (refonte complète)
2014  → Plasma 5.0 (séparation Plasma / Applications)
2024  → Plasma 6.0 (Qt 6, Wayland par défaut)
2026  → Plasma 6.x (actuel)
```

**Nom actuel** : **KDE Plasma** (desktop), KDE Applications (apps), KDE Frameworks (libs).

**Philosophie** :
- **Personnalisation** : Tout configurable (GUI)
- **Features riches** : Widgets, effets, intégrations
- **Performance** : Relativement léger malgré features
- **Traditionnel** : Familier users Windows

**Composants KDE Plasma** :

```
┌────────────────────────────────────────────────┐
│  KDE Plasma Desktop                            │
├────────────────────────────────────────────────┤
│                                                │
│  Window Manager:                               │
│  - KWin (compositor Wayland/X11)               │
│                                                │
│  Workspace:                                    │
│  - Plasma Desktop                              │
│  - Panel(s), Widgets (Plasmoids)               │
│  - KRunner (launcher)                          │
│  - Activities                                  │
│                                                │
│  Applications:                                 │
│  - Dolphin (File Manager)                      │
│  - Konsole (Terminal)                          │
│  - Kate (Text Editor)                          │
│  - Okular (PDF)                                │
│  - Gwenview (Images)                           │
│  - Spectacle (Screenshots)                     │
│  - Discover (App Store)                        │
│  - KMail, Kontact, Krita...                    │
│                                                │
│  Settings:                                     │
│  - System Settings (configuration centralisée) │
│                                                │
│  Session:                                      │
│  - SSDM (Display Manager)                      │
│  - Plasma Session                              │
│                                                │
│  Framework:                                    │
│  - Qt 6 (toolkit)                              │
│  - KDE Frameworks 6 (bibliothèques)            │
│  - D-Bus                                       │
└────────────────────────────────────────────────┘
```

**Installation** :
```bash
# Debian/Ubuntu
sudo apt install kde-plasma-desktop    # Minimal
sudo apt install kde-standard          # Standard
sudo apt install kde-full              # Complet (toutes apps KDE)

# Arch
sudo pacman -S plasma
sudo pacman -S kde-applications

# Enable SSDM
sudo systemctl enable ssdm
```

**Personnalisation** :
```bash
# Tout via GUI : System Settings
systemsettings5

# Thèmes Plasma
System Settings → Appearance → Global Theme
# Breeze, Breeze Dark, Oxygen...

# Widgets (Plasmoids)
Right-click panel/desktop → Add Widgets
# Centaines de widgets disponibles

# Layout
Right-click panel → Edit Panel
# Hauteur, position, widgets, etc.

# Effets fenêtres (KWin)
System Settings → Workspace Behavior → Desktop Effects
# Wobbly Windows, Magic Lamp, Cube...
```

**Configuration (fichiers)** :
```bash
~/.config/plasma-org.kde.plasma.desktop-appletsrc
~/.config/kwinrc
~/.config/kdeglobals
~/.config/plasmarc

# CLI configuration
kwriteconfig5 --file kwinrc --group Compositing --key Enabled true
```

**Avantages** :
- ✅ Extrêmement personnalisable (GUI)
- ✅ Features riches (widgets, effets)
- ✅ Performant (consomme moins que GNOME)
- ✅ Applications KDE de qualité
- ✅ Familier users Windows

**Inconvénients** :
- ❌ Peut sembler complexe (trop d'options)
- ❌ Design peut paraître chargé
- ❌ Support Wayland en amélioration (Plasma 6+)

---

### 🟢 XFCE - XForms Common Environment

**Histoire** :
```
1996  → XFCE 1.0 (basé XForms)
1999  → XFCE 3.0 (migration GTK+)
2007  → XFCE 4.4 (version stable populaire)
2020  → XFCE 4.16
2023  → XFCE 4.18 (actuel)
```

**Philosophie** :
- **Léger** : Faible utilisation ressources
- **Modulaire** : Composants indépendants swappables
- **Stable** : Pas de changements radicaux
- **Traditionnel** : Interface classique familière

**Composants XFCE** :

```
┌────────────────────────────────────────────────┐
│  XFCE Desktop Environment                      │
├────────────────────────────────────────────────┤
│                                                │
│  Window Manager:                               │
│  - Xfwm4 (compositor intégré)                  │
│                                                │
│  Desktop:                                      │
│  - Xfdesktop (desktop manager)                 │
│  - Panel (xfce4-panel)                         │
│  - Application Finder                          │
│                                                │
│  Applications:                                 │
│  - Thunar (File Manager)                       │
│  - XFCE Terminal                               │
│  - Mousepad (Text Editor)                      │
│  - Ristretto (Image Viewer)                    │
│  - Parole (Media Player)                       │
│  - XFCE Task Manager                           │
│                                                │
│  Components:                                   │
│  - xfce4-settings (Settings Manager)           │
│  - xfce4-session (Session Manager)             │
│  - xfce4-power-manager                         │
│  - xfce4-notifyd (Notifications)               │
│                                                │
│  Plugins/Panel:                                │
│  - Applets nombreux (calculatrice, météo...)   │
│                                                │
│  Framework:                                    │
│  - GTK+ 3 (migration GTK+ 4 en cours)          │
│  - Xfconf (configuration backend)              │
└────────────────────────────────────────────────┘
```

**Installation** :
```bash
# Debian/Ubuntu
sudo apt install xfce4                      # Base
sudo apt install xfce4-goodies              # Plugins additionnels

# Arch
sudo pacman -S xfce4
sudo pacman -S xfce4-goodies

# Display Manager recommandé
sudo apt install lightdm
sudo systemctl enable lightdm
```

**Personnalisation** :
```bash
# Settings Manager (GUI)
xfce4-settings-manager

# Modules disponibles :
# - Appearance (thème, icônes, fonts)
# - Window Manager (bordures, comportement)
# - Window Manager Tweaks (compositor, placement)
# - Panel (configuration panneaux)
# - Desktop (wallpaper, icônes desktop)
# - Session and Startup (autostart apps)

# Panel
xfce4-panel --preferences

# Plugins panel populaires
sudo apt install xfce4-battery-plugin
sudo apt install xfce4-cpugraph-plugin
sudo apt install xfce4-weather-plugin
```

**Configuration (fichiers)** :
```bash
~/.config/xfce4/
~/.config/xfce4/xfconf/xfce-perchannel-xml/
    xfce4-panel.xml
    xfwm4.xml
    xfdesktop.xml
    thunar.xml
```

**Avantages** :
- ✅ Léger (parfait vieux hardware)
- ✅ Stable et fiable
- ✅ Interface traditionnelle intuitive
- ✅ Modulaire (swap composants)
- ✅ Personnalisable sans complexité

**Inconvénients** :
- ❌ Design moins moderne
- ❌ Pas de Wayland (X11 seulement)
- ❌ Développement plus lent
- ❌ Moins de features modernes

---

### 🔄 Autres Desktop Environments

**Cinnamon** :
- Fork GNOME 3 (Linux Mint)
- Interface traditionnelle familière
- GTK+, Muffin window manager
- Bon compromis moderne/classique

**MATE** :
- Fork GNOME 2 (continuation legacy)
- Classique et léger
- GTK+, Marco window manager
- Utilisateurs GNOME 2 nostalgiques

**LXQt** :
- Fusion LXDE + Razor-qt
- Ultra-léger Qt
- Openbox window manager
- Machines modestes

**Budgie** :
- Moderne, élégant
- GTK+, Magpie window manager
- Solus Linux
- Familier Windows/macOS users

---

## 🌐 Protocoles d'Accès Distant Graphique

### 🔐 X11 Forwarding (SSH Tunnel)

**Principe** : Tunneliser trafic X11 dans connexion SSH **chiffrée**.

```
┌──────────────────┐                    ┌──────────────────┐
│  CLIENT Local    │                    │  SERVEUR Distant │
│                  │                    │                  │
│  ┌────────────┐  │   SSH Tunnel      │  ┌────────────┐  │
│  │ X Server   │◄─┼────────────────────┼──│ Firefox    │  │
│  │ (Display)  │  │   (Port 22 TCP)    │  │ gedit      │  │
│  └────────────┘  │   Chiffrement      │  │ xeyes      │  │
│                  │                    │  └────────────┘  │
│  Affichage ici   │                    │  Calculs ici     │
└──────────────────┘                    └──────────────────┘

Avantages:
✅ Chiffrement SSH (sécurité)
✅ Authentification SSH (clés)
✅ Compression possible
✅ Facile configuration
```

**Configuration Serveur** :
```bash
# /etc/ssh/sshd_config
X11Forwarding yes
X11DisplayOffset 10
X11UseLocalhost yes

# Packages requis
sudo apt install xauth x11-apps

# Redémarrer SSH
sudo systemctl restart sshd
```

**Utilisation Client** :
```bash
# Connexion avec X11 forwarding
ssh -X user@server.example.com

# Forwarding trusted (moins sécurisé, plus compatible)
ssh -Y user@server.example.com

# Avec compression (réseau lent)
ssh -XC user@server.example.com

# Vérifier DISPLAY
echo $DISPLAY
# localhost:10.0  ✅

# Vérifier xauth cookie
xauth list
# server/unix:10  MIT-MAGIC-COOKIE-1  ...

# Lancer apps
firefox &
gedit document.txt &
xeyes &

# Apps s'exécutent sur serveur
# Affichage sur client via tunnel SSH chiffré
```

**Configuration SSH Client persistante** :
```bash
# ~/.ssh/config
Host myserver
    HostName server.example.com
    User myuser
    ForwardX11 yes
    ForwardX11Trusted yes
    Compression yes

# Connexion simplifiée
ssh myserver
```

**Performance** :
```bash
# Compression SSH (améliore latence réseau lent)
ssh -XC user@server

# Priorité compression (niveau 1-9)
ssh -X -o CompressionLevel=6 user@server

# Désactiver features SSH inutiles (optimisation)
ssh -X -o "Ciphers=aes128-ctr" user@server
```

**Limitations X11 Forwarding** :
```
❌ Latence réseau (apps lourdes lentes)
❌ Pas d'accélération 3D (OpenGL limité)
❌ Audio non transmis (requiert PulseAudio over network)
❌ Clipboard sync basique
❌ Une seule app à la fois (pas desktop complet)
```

**Cas d'usage** :
- ✅ Lancer apps graphiques ponctuelles sur serveur
- ✅ Administration système (GUI tools)
- ✅ Développement distant (IDEs)
- ❌ PAS pour desktop complet (utiliser VNC/RDP)
- ❌ PAS pour gaming/multimedia (latence)

---

### 📺 VNC - Virtual Network Computing

**VNC** : Protocole de **partage de bureau** complet (Remote Frame Buffer).

**Principe** :
```
┌──────────────────┐                    ┌──────────────────┐
│  CLIENT (Viewer) │                    │  SERVEUR VNC     │
│                  │                    │                  │
│  ┌────────────┐  │   RFB Protocol    │  ┌────────────┐  │
│  │ VNC Viewer │◄─┼────────────────────┼──│ VNC Server │  │
│  │            │  │   Port 5900+N      │  │ (Display)  │  │
│  └────────────┘  │                    │  └────────────┘  │
│                  │                    │                  │
│  Voit TOUT       │                    │  Desktop complet │
│  le desktop      │                    │  (Session X11)   │
└──────────────────┘                    └──────────────────┘

Types VNC :
1. Partage display existant (x11vnc)
2. Display virtuel dédié (TigerVNC, TightVNC)
```

**Ports** :
```
5900  → Display :0
5901  → Display :1
5902  → Display :2
...

Protocole RFB (Remote Frame Buffer) sur TCP
```

**Installation VNC Server** :

**TigerVNC (moderne, performant)** :
```bash
# Installation
sudo apt install tigervnc-standalone-server tigervnc-common

# Configuration mot de passe
vncpasswd
# Password: ********
# Verify:   ********
# Would you like to enter a view-only password (y/n)? n

# Fichier créé : ~/.vnc/passwd

# Configuration serveur
nano ~/.vnc/xstartup

#!/bin/bash
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
exec startxfce4  # Ou gnome-session, startkde...

chmod +x ~/.vnc/xstartup

# Démarrer serveur VNC
vncserver :1 -geometry 1920x1080 -depth 24
# New 'hostname:1 (user)' desktop is hostname:1

# Lister sessions VNC
vncserver -list
# TigerVNC server sessions:
# X DISPLAY #     PROCESS ID
# :1              12345

# Arrêter session
vncserver -kill :1
```

**x11vnc (partage display existant)** :
```bash
# Installation
sudo apt install x11vnc

# Partager display actuel :0
x11vnc -display :0 -auth ~/.Xauthority

# Avec mot de passe
x11vnc -display :0 -auth ~/.Xauthority -passwd mysecret

# En arrière-plan persistant
x11vnc -display :0 -auth ~/.Xauthority -passwd secret -forever -bg -o /tmp/x11vnc.log

# Allow multiple connexions
x11vnc -display :0 -auth ~/.Xauthority -shared

# Port personnalisé
x11vnc -display :0 -rfbport 5901
```

**Autostart VNC (systemd)** :
```bash
# /etc/systemd/system/vncserver@.service
[Unit]
Description=Remote desktop service (VNC)
After=syslog.target network.target

[Service]
Type=forking
User=%i
ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill :%i > /dev/null 2>&1 || :'
ExecStart=/usr/bin/vncserver :%i -geometry 1920x1080 -depth 24 -localhost no
ExecStop=/usr/bin/vncserver -kill :%i

[Install]
WantedBy=multi-user.target

# Enable pour user alice, display :1
sudo systemctl enable vncserver@1
sudo systemctl start vncserver@1
```

**Client VNC** :

**Linux** :
```bash
# TigerVNC Viewer
sudo apt install tigervnc-viewer
vncviewer server.example.com:1

# Remmina (GUI multi-protocoles)
sudo apt install remmina remmina-plugin-vnc

# Vinagre (GNOME)
sudo apt install vinagre
```

**Windows** :
```
TightVNC Viewer
RealVNC Viewer
UltraVNC Viewer
```

**macOS** :
```
Built-in Screen Sharing (vnc://)
RealVNC Viewer
```

**Tunnel SSH pour VNC (sécurité)** :
```bash
# VNC sans chiffrement = DANGER
# → Tunneliser dans SSH

# Serveur : VNC en localhost seulement
vncserver :1 -localhost yes

# Client : Créer tunnel SSH
ssh -L 5901:localhost:5901 user@server

# Connexion VNC via tunnel
vncviewer localhost:1

# Schéma :
# Client VNC → localhost:5901 → SSH Tunnel → server:5901 → VNC Server
#              (local)                        (distant)
```

**Avantages VNC** :
- ✅ Desktop complet partagé
- ✅ Multi-plateforme (Windows/Mac/Linux)
- ✅ Interopérable (standards ouverts)
- ✅ Clipboard partagé
- ✅ Plusieurs viewers simultanés possibles

**Inconvénients VNC** :
- ❌ **Pas de chiffrement natif** (requiert SSH tunnel)
- ❌ Performances variables (bande passante)
- ❌ Latence réseau visible
- ❌ Qualité image (compression)
- ❌ Pas d'audio natif

**Optimisations VNC** :
```bash
# Compression
vncviewer -quality 5 server:1  # 0-9 (0=best, 9=worst)

# Profondeur couleur réduite
vncserver :1 -depth 16  # Au lieu de 24

# Résolution réduite
vncserver :1 -geometry 1280x720  # Au lieu de 1920x1080

# Encodage optimisé
vncviewer -encoding tight server:1  # Tight = meilleur compression
```

---

### 🎮 SPICE - Simple Protocol for Independent Computing Environments

**SPICE** : Protocole **accès distant** optimisé pour **machines virtuelles**.

**Histoire** :
```
2008  → Développé par Qumranet (closed source)
2008  → Red Hat acquiert Qumranet
2009  → Open-source par Red Hat
2026  → Standard pour KVM/QEMU VMs
```

**Principe** :
```
┌──────────────────┐                    ┌──────────────────┐
│  CLIENT (Viewer) │                    │  VM KVM/QEMU     │
│                  │                    │                  │
│  ┌────────────┐  │   SPICE Protocol  │  ┌────────────┐  │
│  │ Virt-Viewer│◄─┼────────────────────┼──│SPICE Server│  │
│  │ Remote-    │  │   Port 5900 TCP    │  │ (in VM)    │  │
│  │ Viewer     │  │                    │  └────────────┘  │
│  └────────────┘  │                    │                  │
│                  │                    │  Desktop VM      │
└──────────────────┘                    └──────────────────┘

Composants :
- SPICE Server : Dans VM (qemu-kvm)
- SPICE Client : Virt-Viewer, Remote-Viewer
- SPICE Agent : Dans VM guest (clipboard, resize)
```

**Caractéristiques** :
- ✅ **Faible latence** (optimisé VM)
- ✅ **Faible CPU** (client et serveur)
- ✅ Audio bidirectionnel (playback + recording)
- ✅ USB redirection (devices USB locaux dans VM)
- ✅ Clipboard partagé
- ✅ Multi-monitor support
- ✅ Qualité vidéo excellente
- ✅ Chiffrement possible (TLS)

**Configuration SPICE (VM KVM/QEMU)** :

**Avec virt-manager (GUI)** :
```bash
# Installation
sudo apt install virt-manager

# Créer/éditer VM
virt-manager

# VM Settings → Display:
# - Type: Spice server
# - Listen type: Address (localhost ou All interfaces)
# - Port: Auto ou manuel (5900)
# - TLS Port: 5901 (si chiffrement)

# VM Settings → Video:
# - Model: QXL (optimal SPICE)

# VM Settings → Channel:
# - Name: com.redhat.spice.0
# - Type: spicevmc
```

**Configuration manuelle (libvirt XML)** :
```xml
<domain type='kvm'>
  ...
  <devices>
    <!-- Display SPICE -->
    <graphics type='spice' port='5900' autoport='no' listen='0.0.0.0'>
      <listen type='address' address='0.0.0.0'/>
      <image compression='auto_glz'/>
      <jpeg compression='auto'/>
      <zlib compression='auto'/>
      <playback compression='on'/>
      <streaming mode='filter'/>
    </graphics>
    
    <!-- Video QXL (driver SPICE) -->
    <video>
      <model type='qxl' ram='65536' vram='65536' vgamem='16384' heads='1'/>
    </video>
    
    <!-- SPICE Agent (clipboard, resize) -->
    <channel type='spicevmc'>
      <target type='virtio' name='com.redhat.spice.0'/>
    </channel>
  </devices>
</domain>
```

**Client SPICE** :

**virt-viewer** :
```bash
# Installation
sudo apt install virt-viewer

# Connexion à VM locale (libvirt)
virt-viewer myvm

# Connexion SPICE directe
remote-viewer spice://server.example.com:5900

# Avec fichier .vv (SPICE descriptor)
remote-viewer /path/to/vm.vv

# Fullscreen
remote-viewer --full-screen spice://server:5900
```

**SPICE Agent (dans VM guest)** :
```bash
# Linux guest
sudo apt install spice-vdagent

# Service démarre automatiquement
systemctl status spice-vdagentd

# Features activées :
# - Clipboard partagé (copier/coller host ↔ guest)
# - Auto-resize (résolution VM suit fenêtre viewer)
# - Multi-monitor
```

**Windows guest** :
```
Télécharger spice-guest-tools.exe
Installer → SPICE Agent + QXL drivers
```

**Sécurité SPICE (TLS)** :
```xml
<!-- Chiffrement TLS -->
<graphics type='spice' port='5900' tlsPort='5901'>
  <listen type='address' address='0.0.0.0'/>
  <cert dir='/etc/pki/libvirt-spice'/>
</graphics>
```

**Avantages SPICE** :
- ✅ **Performances excellentes** (latence minimale)
- ✅ **Audio bidirectionnel** (microphone + haut-parleurs)
- ✅ **USB redirection** (clé USB, imprimante...)
- ✅ **Multi-monitor** natif
- ✅ **Faible CPU** (efficace)
- ✅ **Clipboard** transparent
- ✅ **Résolution dynamique** (auto-resize)

**Inconvénients SPICE** :
- ❌ **Spécifique VM KVM** (pas pour machines physiques)
- ❌ Moins universel que VNC/RDP
- ❌ Configuration plus complexe (VM stack required)

**Cas d'usage** :
- ✅ Accès desktop machines virtuelles KVM
- ✅ Infrastructure virtualisée (oVirt, Proxmox)
- ✅ VDI (Virtual Desktop Infrastructure)
- ❌ Accès machines physiques (utiliser VNC/RDP)

---

### 🪟 RDP - Remote Desktop Protocol (xrdp)

**RDP** : Protocole Microsoft **Remote Desktop** implémenté sur Linux via **xrdp**.

**Principe** :
```
┌──────────────────┐                    ┌──────────────────┐
│  CLIENT          │                    │  SERVEUR Linux   │
│                  │                    │                  │
│  ┌────────────┐  │   RDP Protocol    │  ┌────────────┐  │
│  │ rdesktop   │◄─┼────────────────────┼──│   xrdp     │  │
│  │ Remmina    │  │   Port 3389 TCP    │  │            │  │
│  │ Windows RD │  │   Chiffré (TLS)    │  └──────┬─────┘  │
│  └────────────┘  │                    │         │        │
│                  │                    │  ┌──────▼─────┐  │
│  Multi-platform  │                    │  │ VNC/X11    │  │
│  Windows/Linux   │                    │  │ Backend    │  │
│                  │                    │  └────────────┘  │
└──────────────────┘                    └──────────────────┘

xrdp = Serveur RDP pour Linux
     → Convertit RDP ↔ VNC/X11
```

**Installation xrdp** :
```bash
# Debian/Ubuntu
sudo apt install xrdp

# Packages additionnels
sudo apt install xorgxrdp  # X11 backend optimisé

# Enable/Start
sudo systemctl enable xrdp
sudo systemctl start xrdp

# Vérifier status
sudo systemctl status xrdp
# Port 3389 listening

ss -tuln | grep 3389
# tcp   LISTEN   0.0.0.0:3389
```

**Configuration** :
```bash
# Fichier principal
/etc/xrdp/xrdp.ini

[Globals]
port=3389
tcp_nodelay=true
tcp_keepalive=true
security_layer=negotiate    # auto, tls, rdp
certificate=
key_file=
ssl_protocols=TLSv1.2, TLSv1.3
max_bpp=32
fork=true

[Xorg]
name=Xorg
lib=libxup.so
username=ask
password=ask
ip=127.0.0.1
port=-1
code=20

[Xvnc]
name=Xvnc
lib=libvnc.so
username=ask
password=ask
ip=127.0.0.1
port=-1
```

**Backend configuration** :
```bash
# /etc/xrdp/sesman.ini (Session Manager)

[Globals]
ListenAddress=127.0.0.1
ListenPort=3350
EnableUserWindowManager=true
UserWindowManager=startwm.sh

[Security]
AllowRootLogin=false
MaxLoginRetry=4
TerminalServerUsers=tsusers
TerminalServerAdmins=tsadmins

[Sessions]
MaxSessions=50
KillDisconnected=false
DisconnectedTimeLimit=0

[Xorg]
param=-screen 1920x1080x24
```

**Session startup** :
```bash
# ~/.xsession (session utilisateur)
#!/bin/bash

# Choix DE/WM
# GNOME
exec gnome-session

# KDE
#exec startkde

# XFCE
#exec startxfce4

# i3
#exec i3

chmod +x ~/.xsession
```

**Firewall** :
```bash
# Autoriser RDP
sudo ufw allow 3389/tcp

# Limiter aux IPs spécifiques (sécurité)
sudo ufw allow from 192.168.1.0/24 to any port 3389
```

**Client RDP** :

**Linux** :
```bash
# rdesktop (classique)
sudo apt install rdesktop
rdesktop -g 1920x1080 -u username server.example.com

# xfreerdp (moderne, features riches)
sudo apt install freerdp2-x11
xfreerdp /v:server.example.com /u:username /size:1920x1080 /cert:ignore

# Remmina (GUI multi-protocoles)
sudo apt install remmina remmina-plugin-rdp
# Interface graphique complète
```

**Windows** :
```
mstsc.exe (Remote Desktop Connection)
- Built-in Windows
- Computer: server.example.com
- Username: user
```

**macOS** :
```
Microsoft Remote Desktop (App Store)
```

**Options connexion avancées** :
```bash
# xfreerdp
xfreerdp \
  /v:server.example.com \
  /u:username \
  /p:password \
  /size:1920x1080 \
  /cert:ignore \
  /compression \
  /clipboard \
  /sound:sys:alsa \
  /microphone:sys:alsa \
  /usb:id:046d:c52b \  # Redirection USB
  /drive:share,/home/user/shared  # Partage dossier
```

**Chiffrement TLS** :
```bash
# Générer certificat SSL pour xrdp
sudo apt install ssl-cert
sudo openssl req -x509 -newkey rsa:2048 -nodes \
  -keyout /etc/xrdp/key.pem \
  -out /etc/xrdp/cert.pem \
  -days 365

# Permissions
sudo chown xrdp:xrdp /etc/xrdp/key.pem /etc/xrdp/cert.pem
sudo chmod 640 /etc/xrdp/key.pem

# /etc/xrdp/xrdp.ini
certificate=/etc/xrdp/cert.pem
key_file=/etc/xrdp/key.pem
security_layer=tls

sudo systemctl restart xrdp
```

**Avantages RDP (xrdp)** :
- ✅ **Chiffrement TLS** natif
- ✅ **Compatible Windows** clients (mstsc)
- ✅ **Audio** bidirectionnel
- ✅ **Clipboard** partagé
- ✅ **Printer redirection** (impression)
- ✅ **Drive sharing** (partage fichiers)
- ✅ **USB redirection**
- ✅ **Multi-session** (plusieurs users simultanés)
- ✅ **Performances** correctes

**Inconvénients RDP** :
- ❌ xrdp = Layer supplémentaire (RDP → VNC/X11)
- ❌ Compatibilité DE variable (XFCE meilleur)
- ❌ Configuration parfois complexe
- ❌ Moins optimisé que RDP natif Windows

**Cas d'usage** :
- ✅ Accès distant Linux depuis Windows clients
- ✅ Environnement mixte Windows/Linux
- ✅ Users familiers Remote Desktop Windows
- ✅ Chiffrement requis (TLS)

---

### 📊 Comparaison Protocoles Accès Distant

| Protocole | Port(s) | Chiffrement | Audio | Performance | Complexité | Cas d'usage |
|-----------|---------|-------------|-------|-------------|------------|-------------|
| **X11 Forwarding** | 22 (SSH) | ✅ SSH | ❌ | Moyen | Facile | Apps ponctuelles |
| **VNC** | 5900+ | ❌ (SSH tunnel) | ❌ | Moyen | Facile | Desktop complet |
| **SPICE** | 5900 | ✅ TLS (opt) | ✅ | Excellent | Moyen | VMs KVM |
| **RDP (xrdp)** | 3389 | ✅ TLS | ✅ | Bon | Moyen | Windows clients |

**Recommandations** :

```
┌─────────────────────────────────────────────────────────┐
│  Besoin                    → Solution                   │
├─────────────────────────────────────────────────────────┤
│  App graphique ponctuelle  → X11 Forwarding (ssh -X)    │
│  Desktop complet léger     → VNC + SSH Tunnel           │
│  VM KVM/QEMU               → SPICE                      │
│  Clients Windows           → xrdp (RDP)                 │
│  Maximum sécurité          → X11 Forwarding ou VNC+SSH  │
│  Audio requis              → SPICE ou xrdp              │
│  USB devices               → SPICE ou xrdp              │
│  Faible latence            → SPICE                      │
└─────────────────────────────────────────────────────────┘
```

---

## 🎓 10 Scénarios Pratiques Complets

### Scénario 1️⃣ : Installer et configurer environnement GNOME complet

**Contexte** : Serveur Ubuntu Server → Desktop GNOME.

```bash
# 1. Update système
sudo apt update && sudo apt upgrade -y

# 2. Installer GNOME Desktop
sudo apt install ubuntu-desktop -y
# Ou minimal
sudo apt install gnome-core -y

# 3. Installer GDM (si pas déjà)
sudo apt install gdm3

# 4. Configurer Default Display Manager
sudo dpkg-reconfigure gdm3
# Sélectionner gdm3

# 5. Enable GDM
sudo systemctl enable gdm
sudo systemctl set-default graphical.target

# 6. Reboot
sudo reboot

# 7. Login graphique (GNOME)
# → Interface GDM apparaît
# → Login avec credentials

# 8. Post-configuration GNOME
# Installer tweaks
sudo apt install gnome-tweaks gnome-shell-extensions

# 9. Extensions Browser
sudo apt install chrome-gnome-shell

# 10. Vérifier session
echo $XDG_SESSION_TYPE
# wayland  (ou x11 si fallback)

echo $DESKTOP_SESSION
# gnome

# 11. Personnalisation
gsettings set org.gnome.desktop.interface gtk-theme 'Adwaita-dark'
gsettings set org.gnome.desktop.background picture-uri 'file:///usr/share/backgrounds/ubuntu-default.png'

# 12. Applications additionnelles
sudo apt install firefox libreoffice gimp
```

---

### Scénario 2️⃣ : Passer de GNOME à KDE Plasma

**Contexte** : Système avec GNOME, switch vers KDE.

```bash
# 1. Installer KDE Plasma (conserve GNOME)
sudo apt install kde-plasma-desktop

# 2. Installer SSDM
sudo apt install sddm

# 3. Changer Display Manager par défaut
sudo dpkg-reconfigure sddm
# Sélectionner SDDM

# 4. Reboot
sudo reboot

# 5. À l'écran SSDM login
# Cliquer icône session (bas gauche)
# Sélectionner "Plasma (X11)" ou "Plasma (Wayland)"

# 6. Login

# 7. Vérifier session
echo $DESKTOP_SESSION
# plasmax11  ou  plasma

# 8. Configuration KDE
systemsettings5

# 9. (Optionnel) Désinstaller GNOME si non utilisé
sudo apt remove --purge ubuntu-desktop gnome-shell
sudo apt autoremove

# 10. Cleanup GDM
sudo apt remove --purge gdm3

# 11. Vérifier Display Manager actif
systemctl status display-manager
# sddm.service
```

---

### Scénario 3️⃣ : Configuration environnement léger XFCE sur vieux PC

**Contexte** : Vieille machine (2GB RAM, CPU modeste).

```bash
# 1. Installation minimale
sudo apt install xfce4 xfce4-goodies

# 2. Display Manager léger
sudo apt install lightdm lightdm-gtk-greeter

# 3. Enable LightDM
sudo systemctl enable lightdm

# 4. Reboot
sudo reboot

# 5. Login XFCE

# 6. Désactiver compositing (performances)
xfce4-settings-manager
# Window Manager Tweaks → Compositor → Disable

# 7. Réduire animations
# Settings → Window Manager → Style
# Theme: Default (simple)

# 8. Panel minimal
xfce4-panel --preferences
# Enlever plugins inutiles

# 9. Applications légères
sudo apt install --no-install-recommends \
  firefox-esr \       # Navigateur
  thunar \            # File manager (déjà inclus)
  mousepad \          # Editeur texte
  xfce4-terminal \    # Terminal
  xarchiver \         # Archive manager
  ristretto           # Image viewer

# 10. Éviter apps lourdes
# ❌ LibreOffice → ✅ AbiWord, Gnumeric
sudo apt install abiword gnumeric

# 11. Vérifier utilisation RAM
free -h
# Objectif : < 600 MB idle
```

---

### Scénario 4️⃣ : Configurer accès distant VNC sécurisé (SSH tunnel)

**Contexte** : Accéder desktop distant via VNC chiffré.

**Serveur** :
```bash
# 1. Installer VNC server
sudo apt install tigervnc-standalone-server

# 2. Configurer mot de passe VNC
vncpasswd
# Password: ********

# 3. Créer xstartup
mkdir -p ~/.vnc
nano ~/.vnc/xstartup

#!/bin/bash
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
exec startxfce4

chmod +x ~/.vnc/xstartup

# 4. Démarrer VNC en LOCALHOST ONLY (sécurité)
vncserver :1 -localhost yes -geometry 1920x1080 -depth 24

# 5. Vérifier
ss -tuln | grep 5901
# tcp   LISTEN   127.0.0.1:5901  ✅ Localhost seulement

# 6. Firewall : NE PAS ouvrir 5901 (SSH tunnel uniquement)
sudo ufw deny 5901
sudo ufw allow 22
```

**Client** :
```bash
# 1. Créer tunnel SSH (forward port local 5901 vers distant 5901)
ssh -L 5901:localhost:5901 user@server.example.com

# Laisser connexion SSH ouverte

# 2. Nouvelle fenêtre terminal → Connexion VNC en local
vncviewer localhost:1

# Ou
remmina
# Protocol: VNC
# Server: localhost:5901

# 3. Session VNC s'ouvre ✅
# Trafic chiffré par tunnel SSH
```

**Automatisation (script)** :
```bash
# ~/bin/vnc-connect.sh
#!/bin/bash
SERVER="user@server.example.com"
DISPLAY=":1"

echo "Creating SSH tunnel..."
ssh -f -L 5901:localhost:5901 $SERVER sleep 10

echo "Launching VNC viewer..."
vncviewer localhost$DISPLAY

# Utilisation
chmod +x ~/bin/vnc-connect.sh
./vnc-connect.sh
```

---

### Scénario 5️⃣ : Accès distant RDP (xrdp) depuis Windows client

**Serveur Linux** :
```bash
# 1. Installation xrdp
sudo apt install xrdp

# 2. Ajouter user au groupe ssl-cert (permissions)
sudo usermod -aG ssl-cert $USER

# 3. Configurer session XFCE (meilleure compatibilité xrdp)
echo "startxfce4" > ~/.xsession
chmod +x ~/.xsession

# 4. Générer certificat TLS
sudo openssl req -x509 -newkey rsa:2048 -nodes \
  -keyout /etc/xrdp/key.pem \
  -out /etc/xrdp/cert.pem \
  -days 365 \
  -subj "/CN=server.example.com"

sudo chown xrdp:xrdp /etc/xrdp/*.pem
sudo chmod 640 /etc/xrdp/key.pem

# 5. Configurer xrdp.ini
sudo nano /etc/xrdp/xrdp.ini

certificate=/etc/xrdp/cert.pem
key_file=/etc/xrdp/key.pem
security_layer=tls

# 6. Redémarrer xrdp
sudo systemctl restart xrdp

# 7. Firewall
sudo ufw allow 3389/tcp

# 8. Vérifier
sudo systemctl status xrdp
ss -tuln | grep 3389
# tcp   LISTEN   0.0.0.0:3389  ✅
```

**Client Windows** :
```
1. Ouvrir "Remote Desktop Connection" (mstsc.exe)
   → Démarrer → Rechercher "Remote Desktop"

2. Computer: server.example.com
   Username: myuser

3. Connect

4. Certificat warning → Accept (si auto-signé)

5. Login screen xrdp
   → Username: myuser
   → Password: ********

6. Session XFCE démarre ✅
```

**Client Linux** :
```bash
# xfreerdp
xfreerdp /v:server.example.com /u:myuser /cert:ignore /size:1920x1080

# Remmina
remmina
# Protocol: RDP
# Server: server.example.com
# Username: myuser
```

---

### Scénario 6️⃣ : Configurer autologin (GDM)

**Contexte** : Machine personnelle, login automatique sans password.

**⚠️ Sécurité** : Ne jamais utiliser sur machines multi-users ou publiques.

```bash
# GDM
sudo nano /etc/gdm3/custom.conf

[daemon]
AutomaticLoginEnable=true
AutomaticLogin=myusername

# Redémarrer GDM
sudo systemctl restart gdm

# Au reboot : Login automatique sans password

# SDDM
sudo nano /etc/sddm.conf

[Autologin]
User=myusername
Session=plasma.desktop

sudo systemctl restart sddm

# LightDM
sudo nano /etc/lightdm/lightdm.conf

[Seat:*]
autologin-user=myusername
autologin-user-timeout=0

sudo systemctl restart lightdm
```

---

### Scénario 7️⃣ : Diagnostiquer Display Manager qui ne démarre pas

**Symptômes** : Boot système, écran noir, pas de login screen.

```bash
# 1. Basculer TTY text (Ctrl+Alt+F2)

# 2. Login console

# 3. Vérifier Default Target systemd
systemctl get-default
# Should be: graphical.target

# Si multi-user.target (text mode)
sudo systemctl set-default graphical.target

# 4. Vérifier Display Manager service
systemctl status display-manager
# OU spécifique
systemctl status gdm
systemctl status sddm
systemctl status lightdm

# Si failed/inactive
journalctl -u gdm -n 50
# Lire erreurs

# 5. Erreurs courantes

# A. Pas de Display Manager installé
sudo apt install gdm3

# B. X11 configuration cassée
sudo mv /etc/X11/xorg.conf /etc/X11/xorg.conf.backup
# Reboot (auto-detection)

# C. Driver graphique manquant
lspci | grep VGA
sudo apt install nvidia-driver  # Ou amdgpu, intel

# D. Permissions .Xauthority
ls -la ~/.Xauthority
# Si root owned
sudo chown $USER:$USER ~/.Xauthority
# Ou supprimer (recrée)
rm ~/.Xauthority

# 6. Tester startx manuel (debug)
startx
# Si fonctionne → Problème Display Manager
# Si échoue → Problème X11/drivers

# 7. Logs détaillés
cat /var/log/Xorg.0.log | grep -E "\(EE\)|\(WW\)"
cat ~/.xsession-errors

# 8. Fallback VESA (temporaire)
sudo nano /etc/X11/xorg.conf.d/20-vesa.conf

Section "Device"
    Identifier "Fallback"
    Driver "vesa"
EndSection

sudo reboot
```

---

### Scénario 8️⃣ : Créer session VNC systemd multi-user

**Contexte** : Plusieurs users avec sessions VNC dédiées persistantes.

```bash
# 1. Service template systemd
sudo nano /etc/systemd/system/vncserver@.service

[Unit]
Description=VNC Server for User %i
After=syslog.target network.target

[Service]
Type=forking
User=%i
Group=%i
WorkingDirectory=/home/%i

PIDFile=/home/%i/.vnc/%H:%i.pid
ExecStartPre=-/usr/bin/vncserver -kill :%i > /dev/null 2>&1
ExecStart=/usr/bin/vncserver :%i -geometry 1920x1080 -depth 24 -localhost yes
ExecStop=/usr/bin/vncserver -kill :%i

[Install]
WantedBy=multi-user.target

# 2. Chaque user configure VNC
# User alice
su - alice
vncpasswd
mkdir -p ~/.vnc
nano ~/.vnc/xstartup
#!/bin/bash
exec startxfce4
chmod +x ~/.vnc/xstartup
exit

# User bob
su - bob
vncpasswd
# ... même config
exit

# 3. Enable services
# alice → display :1
sudo systemctl enable vncserver@alice:1
sudo systemctl start vncserver@alice:1

# bob → display :2
sudo systemctl enable vncserver@bob:2
sudo systemctl start vncserver@bob:2

# 4. Vérifier
sudo systemctl status vncserver@alice:1
ss -tuln | grep 5901  # alice
ss -tuln | grep 5902  # bob

# 5. Connexion via SSH tunnel
# Alice
ssh -L 5901:localhost:5901 server
vncviewer localhost:1

# Bob
ssh -L 5902:localhost:5902 server
vncviewer localhost:2
```

---

### Scénario 9️⃣ : Configuration VM KVM avec SPICE

**Contexte** : Créer VM avec accès SPICE optimal.

```bash
# 1. Installer KVM + tools
sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager

# 2. Ajouter user à groupe libvirt
sudo usermod -aG libvirt $USER
newgrp libvirt

# 3. Lancer virt-manager
virt-manager

# 4. Créer nouvelle VM
# File → New Virtual Machine
# - Installation media: ISO
# - OS: Ubuntu/Debian/Fedora...
# - RAM: 4096 MB
# - Disk: 40 GB

# 5. Avant finaliser: Customize configuration

# 6. Display:
# - Type: Spice server
# - Listen: Localhost
# - OpenGL: Yes (si GPU disponible)

# 7. Video:
# - Model: QXL
# - Heads: 1

# 8. Sound:
# - Model: ich9

# 9. Add Hardware → Channel:
# - Type: spicevmc
# - Name: com.redhat.spice.0

# 10. Begin Installation

# 11. Après install OS, dans guest:
# Linux guest
sudo apt install spice-vdagent
sudo systemctl enable spice-vdagentd

# 12. Connexion depuis host
# Via virt-manager: Double-clic VM (console SPICE intégrée)

# Ou remote-viewer
remote-viewer spice://localhost:5900

# 13. Features testées
# - Clipboard: Copier texte host → guest ✅
# - Auto-resize: Redimensionner fenêtre → résolution guest suit ✅
# - Audio: Jouer vidéo dans guest → son sort sur host ✅
# - USB: Devices → Redirect USB device ✅
```

---

### Scénario 🔟 : Accès graphique distant via X11 Forwarding (apps lourdes)

**Contexte** : Exécuter Firefox, LibreOffice sur serveur puissant, afficher sur laptop.

**Serveur** :
```bash
# 1. Configuration SSH
sudo nano /etc/ssh/sshd_config

X11Forwarding yes
X11DisplayOffset 10
X11UseLocalhost yes
Compression yes

sudo systemctl restart sshd

# 2. Installer xauth + apps X11
sudo apt install xauth x11-apps firefox libreoffice
```

**Client** :
```bash
# 1. Connexion SSH avec X11 + compression
ssh -XC user@server.example.com

# 2. Vérifier DISPLAY
echo $DISPLAY
# localhost:10.0  ✅

xauth list
# server/unix:10  MIT-MAGIC-COOKIE-1  ...  ✅

# 3. Tester
xeyes &
# Yeux s'affichent ✅

# 4. Lancer apps lourdes
firefox &
# Navigateur s'ouvre (s'exécute sur serveur, affiché localement)

libreoffice --writer &
# LibreOffice démarre

# 5. Performance
# Compression SSH aide latence réseau
# Apps s'exécutent sur serveur (CPU/RAM serveur)
# Transfert affichage seulement (bande passante)

# 6. Config SSH persistante
nano ~/.ssh/config

Host server
    HostName server.example.com
    User myuser
    ForwardX11 yes
    ForwardX11Trusted yes
    Compression yes
    CompressionLevel 6

# Connexion simplifiée
ssh server
firefox &
```

---

## 🚨 10 Erreurs Courantes et Solutions

### Erreur 1️⃣ : Login loop GDM (login → écran noir → retour login)

**Symptômes** : Password correct, écran noir 2 secondes, retour écran login.

**Causes** :
- Permissions .Xauthority mauvaises
- Erreur configuration GNOME
- Disk full (home partition pleine)

**Solutions** :
```bash
# 1. TTY (Ctrl+Alt+F2)

# 2. Vérifier espace disque
df -h /home
# Si 100% → Nettoyer

# 3. Permissions .Xauthority
ls -la ~/.Xauthority
# Si root owned
sudo chown $USER:$USER ~/.Xauthority

# Ou recréer
rm ~/.Xauthority

# 4. Reconfigurer GNOME
# Renommer config (backup)
mv ~/.config ~/.config.backup
mv ~/.local ~/.local.backup

# 5. Lire erreurs session
tail -50 ~/.xsession-errors

# 6. Tester avec autre session
# Au login GDM, sélectionner "GNOME on Xorg" ou "Ubuntu"

# 7. Si toujours problème : Réinstaller gnome-shell
sudo apt install --reinstall gnome-shell ubuntu-desktop

# 8. Reboot
sudo reboot
```

---

### Erreur 2️⃣ : VNC "Authentication failed" malgré bon password

**Cause** : Fichier ~/.vnc/passwd corrompu ou permissions mauvaises.

**Solution** :
```bash
# 1. Supprimer ancien password
rm ~/.vnc/passwd

# 2. Régénérer
vncpasswd
# Password: ********
# Verify: ********

# 3. Permissions
chmod 600 ~/.vnc/passwd

# 4. Redémarrer VNC
vncserver -kill :1
vncserver :1

# 5. Tester connexion
vncviewer localhost:1
```

---

### Erreur 3️⃣ : xrdp écran gris vide après login

**Cause** : Session ~/.xsession mal configurée ou manquante.

**Solution** :
```bash
# 1. Créer/corriger ~/.xsession
nano ~/.xsession

#!/bin/bash
# Choisir DE/WM (un seul exec)
exec startxfce4
# OU
#exec gnome-session
# OU
#exec startkde

chmod +x ~/.xsession

# 2. Alternative : /etc/xrdp/startwm.sh
sudo nano /etc/xrdp/startwm.sh

# Add avant "test -x ..."
startxfce4

# 3. Logs xrdp
tail -f /var/log/xrdp-sesman.log
# Voir erreurs démarrage session

# 4. Tester session localement
startx

# Si fonctionne local mais pas xrdp → Config xrdp
# Si échoue local → Problème DE/X11

# 5. Redémarrer xrdp
sudo systemctl restart xrdp
```

---

### Erreur 4️⃣ : SPICE clipboard ne fonctionne pas (guest ↔ host)

**Cause** : spice-vdagent non installé ou non démarré dans guest.

**Solution** :

**Linux Guest** :
```bash
# 1. Installer agent
sudo apt install spice-vdagent

# 2. Vérifier service
systemctl status spice-vdagentd
# Si inactive
sudo systemctl start spice-vdagentd
sudo systemctl enable spice-vdagentd

# 3. Tester clipboard
# Host: Copier texte
# Guest: Coller (Ctrl+V)
# Guest: Copier texte
# Host: Coller ✅
```

**Windows Guest** :
```
1. Télécharger spice-guest-tools.exe
   https://www.spice-space.org/download.html

2. Installer (SPICE Agent + QXL drivers)

3. Reboot guest

4. Tester clipboard
```

---

### Erreur 5️⃣ : X11 Forwarding "cannot open display"

**Cause** : X11Forwarding désactivé côté serveur ou xauth manquant.

**Solution** :

**Serveur** :
```bash
# 1. Vérifier SSH config
sudo nano /etc/ssh/sshd_config

X11Forwarding yes
X11DisplayOffset 10
X11UseLocalhost yes

# 2. Redémarrer SSH
sudo systemctl restart sshd

# 3. Installer xauth
sudo apt install xauth

# 4. Vérifier permissions
ls -la /usr/bin/xauth
# -rwxr-xr-x
```

**Client** :
```bash
# 1. Déconnecter/reconnecter avec -X
exit
ssh -X user@server

# 2. Debug verbose
ssh -vvv -X user@server 2>&1 | grep -i x11
# Chercher erreurs X11

# 3. Vérifier DISPLAY définie
echo $DISPLAY
# localhost:10.0  (doit être défini)

# 4. Forcer X11 forwarding
ssh -o ForwardX11=yes -o ForwardX11Trusted=yes user@server
```

---

### Erreur 6️⃣ : GNOME extensions ne s'activent pas

**Cause** : chrome-gnome-shell manquant ou version GNOME Shell incompatible.

**Solution** :
```bash
# 1. Installer connector
sudo apt install chrome-gnome-shell gnome-shell-extensions

# 2. Vérifier version GNOME Shell
gnome-shell --version
# GNOME Shell 45.2

# 3. Extensions doivent être compatibles version
# https://extensions.gnome.org/
# → Vérifier compatibilité extension

# 4. Enable extensions via Tweaks
gnome-tweaks
# Extensions → Toggle ON

# 5. Restart GNOME Shell
# Wayland: Logout/login
# X11: Alt+F2 → 'r' → Enter

# 6. CLI enable extension
gnome-extensions enable dash-to-dock@micxgx.gmail.com

# 7. Lister enabled
gnome-extensions list --enabled
```

---

### Erreur 7️⃣ : VNC latence énorme (réseau lent)

**Cause** : Compression insuffisante, qualité trop élevée.

**Solution** :

**Serveur** :
```bash
# 1. Réduire profondeur couleur
vncserver :1 -depth 16  # Au lieu de 24

# 2. Résolution réduite si possible
vncserver :1 -geometry 1280x720
```

**Client** :
```bash
# 1. Compression maximale
vncviewer -quality 3 server:1  # 0-9 (0=best, 9=worst quality/best compression)

# 2. Encodage optimisé
vncviewer -encoding tight server:1

# 3. Désactiver compression JPEG locale (si déjà compressé)
vncviewer -nojpeg server:1

# 4. Limiter couleurs
vncviewer -depth 8 server:1

# 5. Tunnel SSH avec compression
ssh -C -L 5901:localhost:5901 user@server
vncviewer -quality 5 localhost:1
```

---

### Erreur 8️⃣ : KDE Plasma freeze après mise à jour

**Cause** : Cache Plasma corrompu après upgrade.

**Solution** :
```bash
# 1. TTY (Ctrl+Alt+F2)

# 2. Nettoyer cache Plasma
rm -rf ~/.cache/plasma*
rm -rf ~/.config/plasma*
rm -rf ~/.local/share/kactivitymanagerd

# 3. Reconfigurer plasmashell
kquitapp5 plasmashell
kstart5 plasmashell

# 4. Ou depuis TTY (si freeze complet)
killall plasmashell
plasmashell &

# 5. Si toujours problème : Reset config complète (BACKUP avant!)
mv ~/.config ~/.config.backup
mv ~/.local ~/.local.backup

# 6. Relogin
# Config par défaut Plasma

# 7. Si résolu : Restaurer configs une par une pour identifier coupable
```

---

### Erreur 9️⃣ : Display Manager boucle infinie (crash au démarrage)

**Cause** : Driver graphique incompatible ou xorg.conf cassé.

**Solution** :
```bash
# 1. Boot en mode rescue/recovery
# Grub menu → Advanced → Recovery mode

# 2. Drop to root shell

# 3. Supprimer xorg.conf (forcer auto-detect)
mv /etc/X11/xorg.conf /etc/X11/xorg.conf.broken
rm /etc/X11/xorg.conf.d/*.conf  # Ou mv pour backup

# 4. Fallback driver VESA
nano /etc/X11/xorg.conf.d/10-vesa-fallback.conf

Section "Device"
    Identifier "Card0"
    Driver "vesa"
EndSection

# 5. Reboot
reboot

# Si démarre avec VESA (basse résolution)
# → Problème driver principal

# 6. Identifier carte
lspci | grep VGA

# 7. Réinstaller driver approprié
sudo apt install --reinstall nvidia-driver
# OU
sudo apt install --reinstall xserver-xorg-video-amdgpu
# OU
sudo apt install --reinstall xserver-xorg-video-intel

# 8. Reboot
sudo reboot
```

---

### Erreur 🔟 : xrdp audio ne fonctionne pas

**Cause** : PulseAudio source module non configuré pour xrdp.

**Solution** :
```bash
# 1. Installer PulseAudio module xrdp
sudo apt install pulseaudio-module-xrdp

# 2. Redémarrer xrdp
sudo systemctl restart xrdp

# 3. Dans session RDP: Vérifier PulseAudio
pulseaudioctl status
# Doit montrer xrdp sink

# 4. Tester audio
paplay /usr/share/sounds/alsa/Front_Center.wav

# 5. Si toujours pas d'audio

# Client Windows: Vérifier "Remote audio"
# RDP settings → Experience → Remote audio → Play on this computer

# Client Linux (xfreerdp):
xfreerdp /v:server /u:user /sound:sys:alsa

# 6. Logs PulseAudio
journalctl --user -u pulseaudio
```

---

## 📝 Antisèche (Cheat Sheet)

### Display Managers
```bash
# Installation
sudo apt install gdm3        # GNOME
sudo apt install sddm        # KDE
sudo apt install lightdm     # Universal

# Changer default DM
sudo dpkg-reconfigure gdm3

# Systemd
sudo systemctl enable gdm
sudo systemctl restart gdm

# Vérifier actif
systemctl status display-manager
```

### Desktop Environments
```bash
# Installation
sudo apt install ubuntu-desktop      # GNOME
sudo apt install kde-plasma-desktop  # KDE
sudo apt install xfce4               # XFCE

# Vérifier session
echo $DESKTOP_SESSION
echo $XDG_SESSION_TYPE  # x11 ou wayland
```

### X11 Forwarding
```bash
# Serveur: /etc/ssh/sshd_config
X11Forwarding yes

# Client
ssh -X user@server
ssh -Y user@server      # Trusted
ssh -XC user@server     # Avec compression

# Vérifier
echo $DISPLAY           # localhost:10.0
xauth list
```

### VNC
```bash
# Serveur
vncserver :1 -geometry 1920x1080 -depth 24
vncserver -list
vncserver -kill :1

# Client
vncviewer server:1
vncviewer localhost:1   # Via SSH tunnel

# SSH Tunnel
ssh -L 5901:localhost:5901 user@server

# Ports
5900 + N  # Display :N
```

### SPICE
```bash
# VM Configuration (virt-manager)
# Display: Spice server
# Video: QXL

# Guest Agent
sudo apt install spice-vdagent

# Client
virt-viewer myvm
remote-viewer spice://server:5900
```

### xrdp
```bash
# Installation
sudo apt install xrdp

# Configuration
/etc/xrdp/xrdp.ini
~/.xsession

# Service
sudo systemctl enable xrdp
sudo systemctl restart xrdp

# Client
xfreerdp /v:server /u:user
mstsc.exe  # Windows

# Port
3389  # RDP
```

### XDMCP (Obsolète)
```bash
# GDM: /etc/gdm3/custom.conf
[xdmcp]
Enable=true

# Client
X :1 -query server

# Port
177 UDP  # XDMCP
6000+ TCP  # X11

⚠️ NON sécurisé (déprécié)
```

### Configuration Files
```bash
# Display Manager
/etc/gdm3/custom.conf
/etc/sddm.conf
/etc/lightdm/lightdm.conf

# Session
~/.xsession
~/.xinitrc

# GNOME
~/.config/dconf/
gsettings

# KDE
~/.config/plasma*
~/.config/kwin*

# XFCE
~/.config/xfce4/
```

### Troubleshooting
```bash
# Logs
journalctl -u gdm
~/.xsession-errors
/var/log/Xorg.0.log

# Vérifier Display Manager
systemctl status display-manager

# Changer target
sudo systemctl set-default graphical.target

# Permissions
ls -la ~/.Xauthority
sudo chown $USER:$USER ~/.Xauthority
```

---

## 🎓 Points Clés pour l'Examen LPIC-1

### ✅ Concepts à Maîtriser Absolument

1. **Display Managers** :
   - Rôle : Login graphique, gestion sessions
   - Principaux : GDM (GNOME), SDDM (KDE), LightDM (universel), XDM (legacy)
   - Configuration via `/etc/gdm3/custom.conf`, `/etc/ssdm.conf`, `/etc/lightdm/lightdm.conf`
   - Changer DM : `dpkg-reconfigure` ou `systemctl enable`

2. **XDMCP** (Important examen) :
   - Protocole connexions distantes via Display Manager
   - Port 177 UDP (queries)
   - **NON sécurisé** (trafic clair, passwords visibles)
   - **Obsolète/Déprécié** (remplacé SSH X11, VNC, RDP)
   - Configuration : `Enable=true` dans DM config
   - Encore mentionné LPIC-1 pour **historique**

3. **Desktop Environments** :
   - **GNOME** : 1999, GTK+, productivité/accessibilité, Mutter WM, ~1.5GB RAM
   - **KDE Plasma** : 1996 (Plasma depuis 2014), Qt, personnalisation, KWin WM, ~800MB RAM
   - **XFCE** : 1999, GTK+, léger/modulaire, Xfwm4 WM, ~500MB RAM
   - Toolkit : GTK+ (GNOME/XFCE) vs Qt (KDE)

4. **X11 Forwarding** :
   - Tunneliser X11 dans SSH (chiffrement)
   - Serveur : `X11Forwarding yes` dans `/etc/ssh/sshd_config`
   - Client : `ssh -X user@server`
   - DISPLAY : `localhost:10.0`
   - Requis : xauth installé serveur
   - Usage : Apps graphiques ponctuelles

5. **VNC** :
   - Protocole partage bureau complet (RFB)
   - Port 5900+N (N=display number)
   - **Pas de chiffrement natif** (SSH tunnel requis)
   - Types : TigerVNC (display virtuel), x11vnc (partage existant)
   - Interopérable multi-plateforme
   - Tunnel SSH : `ssh -L 5901:localhost:5901`

6. **SPICE** :
   - Protocole optimisé VMs (KVM/QEMU)
   - Port 5900 TCP
   - Audio bidirectionnel, USB redirection
   - Faible latence, faible CPU
   - Agent guest : spice-vdagent (clipboard, resize)
   - Client : virt-viewer, remote-viewer

7. **RDP (xrdp)** :
   - Remote Desktop Protocol pour Linux
   - Port 3389 TCP
   - Chiffrement TLS natif
   - Compatible clients Windows (mstsc.exe)
   - Configuration : ~/.xsession (choix DE)
   - Audio, clipboard, drive sharing

8. **Commandes essentielles** :
   - Display Manager : `systemctl status display-manager`
   - Changer DM : `sudo dpkg-reconfigure gdm3`
   - X11 Forward : `ssh -X user@server`
   - VNC : `vncserver :1`, `vncviewer`
   - SPICE : `virt-viewer`, `remote-viewer`
   - xrdp : `xfreerdp /v:server`

9. **Sécurité** :
   - ❌ XDMCP : Trafic clair (déprécié)
   - ❌ VNC : Pas chiffré (SSH tunnel requis)
   - ✅ X11 Forwarding : SSH chiffré
   - ✅ SPICE : TLS optionnel
   - ✅ xrdp : TLS natif

10. **Fichiers configuration** :
    - `/etc/gdm3/custom.conf` : GDM
    - `/etc/ssdm.conf` : SSDM
    - `/etc/lightdm/lightdm.conf` : LightDM
    - `/etc/ssh/sshd_config` : SSH X11Forwarding
    - `~/.vnc/passwd` : VNC password
    - `~/.xsession` : Session xrdp

### 📚 Commandes à Connaître par Cœur

```bash
# Display Manager
systemctl status display-manager
sudo dpkg-reconfigure gdm3
sudo systemctl enable ssdm

# Session info
echo $DESKTOP_SESSION
echo $XDG_SESSION_TYPE

# X11 Forwarding
ssh -X user@server
ssh -Y user@server
ssh -XC user@server

# VNC
vncserver :1
vncserver -kill :1
vncviewer server:1

# SPICE
virt-viewer myvm
remote-viewer spice://server:5900

# xrdp
xfreerdp /v:server /u:user
```

### ⚠️ Pièges d'Examen Fréquents

1. **XDMCP sécurité** :
   - Port 177 UDP
   - **Trafic NON chiffré** (passwords clairs)
   - **Déprécié** (ne pas recommander production)
   - Usage moderne : SSH X11 Forwarding

2. **X11 Forwarding vs XDMCP** :
   - X11 Forward : Apps individuelles, SSH chiffré ✅
   - XDMCP : Desktop complet, non chiffré ❌

3. **VNC chiffrement** :
   - VNC natif = **PAS chiffré**
   - Tunnel SSH obligatoire : `ssh -L 5901:localhost:5901`
   - Port 5900+N (N=display)

4. **Display Manager config files** :
   - GDM : `/etc/gdm3/custom.conf`
   - SDDM : `/etc/sddm.conf`
   - LightDM : `/etc/lightdm/lightdm.conf`
   - XDM : `/etc/X11/xdm/xdm-config`

5. **Desktop Environment Toolkits** :
   - GNOME : GTK+ ✅
   - KDE : Qt ✅
   - XFCE : GTK+ ✅
   - Ne pas confondre !

6. **Ports protocoles** :
   - XDMCP : 177 UDP
   - X11 : 6000+ TCP
   - VNC : 5900+N TCP
   - SPICE : 5900 TCP
   - RDP : 3389 TCP

7. **SPICE spécificité** :
   - **Spécifique VMs KVM/QEMU**
   - PAS pour machines physiques
   - Agent guest requis features avancées

8. **xrdp session startup** :
   - `~/.xsession` définit DE lancé
   - `exec startxfce4` (XFCE meilleure compatibilité)
   - Pas `~/.xinitrc` (pour startx)

9. **Autologin Display Manager** :
   - GDM : `AutomaticLogin=user` dans custom.conf
   - SDDM : `[Autologin]` section
   - LightDM : `autologin-user=user`

10. **X11Forwarding SSH** :
    - Serveur : `X11Forwarding yes` sshd_config
    - Client : ssh -X
    - Package requis serveur : xauth
    - DISPLAY : `localhost:10.0`

### 🔑 Valeurs à Mémoriser

**Ports** :
```
177 UDP    XDMCP
3389 TCP   RDP
5900 TCP   VNC/SPICE display :0
5901 TCP   VNC display :1
6000 TCP   X11 display :0
```

**Display Managers** :
```
GDM       GNOME
SSDM      KDE
LightDM   Universal
XDM       Legacy
```

**Desktop Environments** :
```
GNOME     GTK+, 1999, Mutter
KDE       Qt, 1996, KWin
XFCE      GTK+, 1999, Xfwm4
```

**Fichiers config** :
```
/etc/gdm3/custom.conf
/etc/ssdm.conf
/etc/lightdm/lightdm.conf
/etc/ssh/sshd_config
~/.xsession
~/.vnc/passwd
```

---

## 📖 Résumé Final

Ce cours couvre les **environnements de bureau Linux et accès distant** pour LPIC-1 102 :

1. **Display Managers** :
   - Gestion login graphique et sessions
   - GDM (GNOME), SSDM (KDE), LightDM (universel)
   - **XDMCP** : Protocole connexions distantes (obsolète, non sécurisé)

2. **Desktop Environments** :
   - **GNOME** : Moderne, GTK+, productivité (lourd)
   - **KDE Plasma** : Personnalisable, Qt, features riches
   - **XFCE** : Léger, GTK+, modulaire, stable

3. **Protocoles accès distant** :
   - **X11 Forwarding** : SSH tunnel, apps individuelles, chiffré ✅
   - **VNC** : Desktop complet, non chiffré (SSH tunnel requis)
   - **SPICE** : VMs KVM, audio/USB, faible latence
   - **RDP (xrdp)** : Compatible Windows, TLS, features riches

4. **Sécurité** :
   - XDMCP : ❌ Non sécurisé (déprécié)
   - VNC : ❌ Requiert tunnel SSH
   - X11 Forward, SPICE, xrdp : ✅ Chiffrement

5. **Configuration** :
   - Display Managers : `/etc/gdm3/custom.conf`, `/etc/ssdm.conf`
   - SSH X11 : `X11Forwarding yes` dans sshd_config
   - VNC : `vncpasswd`, `~/.vnc/xstartup`
   - xrdp : `~/.xsession` définit DE

La compréhension des environnements de bureau et protocoles d'accès distant est essentielle pour l'administration graphique Linux.

---

*Documentation créée le 10 février 2026 - Formation LPIC-1 102 - Objectif 106.2*
