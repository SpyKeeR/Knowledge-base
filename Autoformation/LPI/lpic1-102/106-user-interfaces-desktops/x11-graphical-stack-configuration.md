# 🖥️ Stack Graphique Linux et Configuration X11

## 📋 Informations du Module

- **Examen** : LPIC-1 102
- **Objectif** : 106.1 - Install and configure X11
- **Cours** : Understand Linux Graphical Stack and Install and Configure X11
- **Poids** : 2 points
- **Objectif** : Comprendre l'architecture graphique Linux, configurer X11, gérer l'accès distant

---

## 🎯 Architecture Graphique Linux - Couches d'Abstraction

### 📊 Stack Complet (Bas en Haut)

```
┌──────────────────────────────────────────────────────────┐
│                     UTILISATEUR                          │
│              (Interactions clavier/souris)               │
└────────────────────────┬─────────────────────────────────┘
                         │
┌────────────────────────▼─────────────────────────────────┐
│           APPLICATIONS GRAPHIQUES                        │
│     (Firefox, GIMP, Terminal, LibreOffice...)           │
└────────────────────────┬─────────────────────────────────┘
                         │
        ┌────────────────┴────────────────┐
        │                                 │
┌───────▼────────────┐          ┌────────▼──────────────┐
│  DESKTOP           │          │  WINDOW MANAGER       │
│  ENVIRONMENT       │          │  (Léger)              │
│                    │          │                       │
│  Gnome, KDE, XFCE  │    OU    │  i3, dwm, OpenBox,    │
│  Cinnamon, MATE    │          │  Fluxbox, Awesome     │
│                    │          │                       │
│  + Bibliothèques:  │          │  Juste gestion        │
│  - GTK+            │          │  fenêtres             │
│  - Qt              │          │                       │
└────────────────────┘          └───────────────────────┘
        │                                 │
        └────────────────┬────────────────┘
                         │
┌────────────────────────▼─────────────────────────────────┐
│            DISPLAY SERVER (Serveur d'affichage)          │
│                                                          │
│  X11 (X.org)              OU          Wayland            │
│  - Depuis 1980                        - Moderne          │
│  - Complexe                           - Sécurisé         │
│  - Déprécié                           - Simple           │
│  - Fallback                           - Par défaut       │
└────────────────────────┬─────────────────────────────────┘
                         │
┌────────────────────────▼─────────────────────────────────┐
│                      KERNEL                              │
│              (Gestion matériel, drivers)                 │
└────────────────────────┬─────────────────────────────────┘
                         │
┌────────────────────────▼─────────────────────────────────┐
│                     HARDWARE                             │
│   GPU, Écran, Clavier, Souris, Carte graphique          │
└──────────────────────────────────────────────────────────┘
```

**Rôle de chaque couche** :

1. **Hardware** : Composants physiques (GPU, écrans, périphériques d'entrée)

2. **Kernel** : 
   - Pilotes matériels (DRM - Direct Rendering Manager)
   - KMS (Kernel Mode Setting) pour résolution/refresh rate
   - Interface entre hardware et userspace

3. **Display Server** :
   - **Serveur d'affichage** gérant le rendu graphique
   - Communication avec applications (protocole)
   - Gestion fenêtres, input devices, compositing
   - **X11** ou **Wayland**

4. **Desktop Environment / Window Manager** :
   - **DE** : Environnement complet (suite logiciels + WM)
   - **WM** : Gestionnaire fenêtres seul (léger)

5. **Applications** : Programmes graphiques utilisateur

6. **Utilisateur** : Interactions finales

---

## 🖼️ Desktop Environment vs Window Manager

### 🏢 Desktop Environment (DE)

**Définition** : Environnement de bureau **complet** avec suite logicielle intégrée.

**Composants** :
```
Desktop Environment = Window Manager + Suite Applicative + Bibliothèques
```

**Exemples populaires** :

| DE | Bibliothèques | Caractéristiques | Ressources |
|----|---------------|------------------|------------|
| **GNOME** | GTK+ | Moderne, épuré, extensions | Élevées |
| **KDE Plasma** | Qt | Personnalisable, riche features | Élevées |
| **XFCE** | GTK+ | Léger, traditionnel, stable | Moyennes |
| **Cinnamon** | GTK+ | Intuitif, familier Windows | Moyennes |
| **MATE** | GTK+ | Fork GNOME 2, classique | Faibles |
| **LXQt** | Qt | Ultra-léger, moderne | Faibles |
| **LXDE** | GTK+ | Très léger, simple | Très faibles |

**Inclus dans DE** :
- Window Manager intégré (Mutter pour GNOME, KWin pour KDE)
- Gestionnaire fichiers (Nautilus, Dolphin)
- Éditeur texte (gedit, Kate)
- Terminal (GNOME Terminal, Konsole)
- Paramètres système
- Barre de tâches/Dock
- Menu applications
- Gestionnaire réseau
- Notifications
- Économiseur d'écran
- Thèmes/Icônes

**Avantages** :
- ✅ Expérience cohérente et intégrée
- ✅ User-friendly (débutants)
- ✅ Tout inclus (batteries included)
- ✅ Personnalisation graphique (GUI)

**Inconvénients** :
- ❌ Consommation ressources élevée
- ❌ Bloatware (logiciels inutiles)
- ❌ Moins de contrôle avancé

---

### 🪟 Window Manager (WM)

**Définition** : Gestionnaire de fenêtres **seul**, sans suite logicielle.

**Rôle unique** :
- Affichage et positionnement des fenêtres
- Bordures, titres, boutons (fermer/minimiser)
- Gestion focus clavier
- Raccourcis clavier
- **PAS** d'applications intégrées

**Types** :

| Type | Description | Exemples |
|------|-------------|----------|
| **Stacking** | Fenêtres superposées (traditionnel) | Openbox, Fluxbox, IceWM |
| **Tiling** | Fenêtres mosaïque (auto-organisation) | i3, dwm, awesome, bspwm, xmonad |
| **Dynamic** | Mix stacking + tiling | awesome, dwm |

**Exemples populaires** :

| WM | Type | Configuration | Utilisation |
|----|------|---------------|-------------|
| **i3** | Tiling | Fichier texte | Power users |
| **dwm** | Dynamic | Code C (recompile) | Minimalistes |
| **Openbox** | Stacking | XML | Flexibilité |
| **awesome** | Dynamic | Lua | Programmeurs |
| **bspwm** | Tiling | Scripts | Hackers |
| **Fluxbox** | Stacking | Simple | Léger |

**Avantages** :
- ✅ Extrêmement léger (RAM < 50MB)
- ✅ Performances élevées
- ✅ Contrôle total (configuration fichier texte)
- ✅ Productivité (raccourcis clavier)
- ✅ Personnalisation infinie

**Inconvénients** :
- ❌ Courbe d'apprentissage raide
- ❌ Configuration manuelle (fichiers texte)
- ❌ Pas d'applications intégrées (à installer séparément)
- ❌ Moins user-friendly

**Configuration typique avec WM** :
```bash
# Stack léger sans DE
Window Manager:  i3
Terminal:        urxvt / alacritty
File Manager:    ranger (CLI) ou pcmanfm (GUI)
Launcher:        dmenu / rofi
Status Bar:      i3status / polybar
Compositor:      picom (transparence, ombres)
Wallpaper:       feh
Lock Screen:     i3lock
Notifications:   dunst
```

---

## 🔧 Display Servers - X11 vs Wayland

### 🖥️ X11 (X Window System)

**Histoire** :
```
1984  → X Window System créé (MIT)
1987  → Version 11 (X11) - actuelle
1992  → XFree86 (implémentation libre)
2004  → Fork → X.org (Foundation)
2026  → Déprécié mais toujours utilisé (fallback)
```

**Caractéristiques X11** :
- **Ancien** : Depuis 1984 (42 ans !)
- **Architecture client-serveur** : Apps = clients, X.org = serveur
- **Network transparent** : Affichage distant natif
- **Code legacy** : Très complexe à maintenir
- **Sécurité** : Faible (apps voient tout)
- **Performances** : Overhead important

**Problèmes X11** :
```
┌──────────────────────────────────────────────┐
│  Application                                 │
└──────────────┬───────────────────────────────┘
               │ Draw requests
┌──────────────▼───────────────────────────────┐
│  X Server (X.org)                            │
│  - Gestion fenêtres                          │
│  - Rendu                                     │
│  - Compositing (via extension)               │
└──────────────┬───────────────────────────────┘
               │
┌──────────────▼───────────────────────────────┐
│  Kernel (DRM/KMS)                            │
└──────────────┬───────────────────────────────┘
               │
┌──────────────▼───────────────────────────────┐
│  GPU                                         │
└──────────────────────────────────────────────┘

Problèmes:
❌ Trop de couches (latence)
❌ X Server fait trop de choses
❌ Extensions empilées (complexité)
❌ Sécurité (apps keylogging possibles)
❌ Code ancien (maintenance difficile)
```

**Avantages X11** :
- ✅ Mature et stable
- ✅ Compatible avec tout (legacy apps)
- ✅ Affichage distant natif (ssh -X)
- ✅ Bien documenté
- ✅ Drivers disponibles

**Inconvénients X11** :
- ❌ Complexité code (maintenance)
- ❌ Sécurité faible
- ❌ Performances sous-optimales
- ❌ Tearing (déchirure image)
- ❌ Développement ralenti

---

### 🌊 Wayland

**Histoire** :
```
2008  → Projet lancé (Kristian Høgsberg)
2012  → Version 1.0
2016  → Adoption croissante (Fedora, Ubuntu)
2020+ → Par défaut sur nombreuses distros
2026  → Standard moderne (X11 en fallback)
```

**Principe Wayland** :
```
┌──────────────────────────────────────────────┐
│  Application (Wayland client)                │
└──────────────┬───────────────────────────────┘
               │ Buffers graphiques
┌──────────────▼───────────────────────────────┐
│  Compositor Wayland (Mutter, KWin, Sway)     │
│  - Window Manager                            │
│  - Compositing                               │
│  - Direct au Kernel                          │
└──────────────┬───────────────────────────────┘
               │
┌──────────────▼───────────────────────────────┐
│  Kernel (DRM/KMS)                            │
└──────────────┬───────────────────────────────┘
               │
┌──────────────▼───────────────────────────────┐
│  GPU                                         │
└──────────────────────────────────────────────┘

Avantages:
✅ Plus simple (moins de couches)
✅ Compositor fait tout (cohérence)
✅ Direct kernel → GPU (performances)
✅ Sécurité (isolation apps)
✅ Code moderne (maintenance facile)
```

**Avantages Wayland** :
- ✅ **Moderne** : Architecture propre
- ✅ **Sécurité** : Apps isolées (pas de keylogging)
- ✅ **Performances** : Direct rendering, moins de latence
- ✅ **Pas de tearing** : Vsync natif
- ✅ **Maintenance** : Code plus simple
- ✅ **HiDPI** : Support natif écrans haute résolution
- ✅ **Multi-monitor** : Gestion améliorée

**Inconvénients Wayland** :
- ❌ Apps legacy (compatibilité XWayland)
- ❌ Screen sharing complexe
- ❌ Affichage distant pas natif
- ❌ Nvidia drivers problématiques (en amélioration)
- ❌ Certains WM pas encore portés

**Compositeurs Wayland** :

| Compositor | DE/WM | Description |
|------------|-------|-------------|
| **Mutter** | GNOME | Compositor GNOME |
| **KWin** | KDE Plasma | Compositor KDE |
| **Sway** | WM (i3-like) | Tiling WM Wayland |
| **wlroots** | Base | Bibliothèque pour compositor |
| **Weston** | Référence | Compositor démo |

**Transition X11 → Wayland** :
```bash
# Vérifier session actuelle
echo $XDG_SESSION_TYPE
# x11  ou  wayland

echo $WAYLAND_DISPLAY
# wayland-0 (si Wayland actif)
# vide (si X11)

# Lister clients X11 actifs
xlsclients
# Liste toutes les applications utilisant X11
# Si une app graphique est absente : elle utilise Wayland natif
# Utile pour détecter les apps Wayland vs XWayland

# Depuis GDM/SDDM : Choix à la connexion
# ⚙️ → GNOME (Wayland)
# ⚙️ → GNOME on Xorg

# XWayland : Compatibilité apps X11 sur Wayland
ps aux | grep -i xwayland
# Serveur X11 tournant dans Wayland pour apps legacy
```

**État actuel (2026)** :
- **Par défaut** : Fedora, Ubuntu (GNOME), Arch, openSUSE
- **Fallback X11** : Si problèmes drivers Nvidia, apps legacy
- **X11 déprécié** : Maintenance minimale, migration encouragée

---

## 🔧 Configuration X11

### 📄 Fichiers de Configuration

**Hiérarchie** :
```
/etc/X11/
├── xorg.conf                    # Configuration principale (MONOLITHIQUE)
├── xorg.conf.d/                 # Configuration modulaire (MODERNE)
│   ├── 00-keyboard.conf
│   ├── 10-monitor.conf
│   ├── 20-nvidia.conf
│   └── ...
└── Xsession.d/                  # Scripts démarrage session
    ├── 50x11-common_determine-startup
    └── ...
```

**Priorité** :
1. `/etc/X11/xorg.conf` (si existe, **prioritaire**)
2. `/etc/X11/xorg.conf.d/*.conf` (fichiers modulaires, ordre alphabétique)
3. Auto-détection X.org (si aucun fichier config)

**⚠️ Moderne** : Utiliser `/etc/X11/xorg.conf.d/` (modulaire, packages-friendly).

**⚠️ Legacy** : `/etc/X11/xorg.conf` (monolithique, rarement nécessaire).

---

### 🛠️ Génération Configuration

**Générer configuration de base** :
```bash
# Générer xorg.conf.new (à copier ensuite)
sudo Xorg -configure

# OU (selon distro)
sudo X -configure

# Depuis une console texte (aucun serveur X actif) :
sudo X -configure :0
# Génère xorg.conf.new avec détection matériel

# Résultat
ls ~/xorg.conf.new
# Configuration générée (à déplacer)

sudo mv ~/xorg.conf.new /etc/X11/xorg.conf

# Tester avant de valider
sudo X -config ~/xorg.conf.new
# Si écran s'affiche : OK ✅
# Ctrl+Alt+F2 pour sortir si bloqué
```

**Outils de configuration historiques** :

**xorgcfg** (outil graphique, legacy) :
```bash
# Détecte automatiquement tous les paramètres de configuration du serveur X
# Crée un fichier xorg.conf
# Lance X avec un gestionnaire de fenêtres de base (twm)
# Affiche un outil graphique pour modifier/affiner la configuration
sudo xorgcfg
```

**xorgconfig** (outil mode texte, legacy) :
```bash
# Fonctionne en mode texte (questions interactives)
# Guide l'utilisateur pour créer xorg.conf
sudo xorgconfig
```

**⚠️ Note** : Ces outils sont **obsolètes** sur distributions modernes. Utiliser `Xorg -configure` préférablement.

**Tester configuration générée** :

**Versions antérieures à Xorg 7.2** :
```bash
# Tester sans lancer X complet
sudo X -probeonly
```

**Versions Xorg 7.3 et supérieures** :
```bash
# Autoconfiguration complète
sudo Xorg -configure
# Génère xorg.conf.new dans le répertoire de root

# Tester la configuration
sudo Xorg -config xorg.conf.new
# Si ça fonctionne, copier dans /etc/X11/xorg.conf
sudo mv xorg.conf.new /etc/X11/xorg.conf
```

**Tester serveur X minimal (sans Display Manager/Window Manager)** :
```bash
# Démarrer serveur X seul (écran noir avec curseur)
sudo X
# Ctrl+Alt+F2 pour sortir
# Utile pour vérifier que le serveur X démarre correctement
```

**⚠️ Rarement nécessaire** : X.org auto-détecte généralement tout.

**Quand générer config manuelle ?** :
- Multiples GPU (sélectionner carte)
- Configurations exotiques (résolutions custom)
- Drivers spécifiques (Nvidia proprietary)
- Multi-écrans complexes
- Debugging problèmes graphiques

---

### 📐 Structure xorg.conf

**Fichier monolithique** : `/etc/X11/xorg.conf`

**Sections principales** :

```ini
# 1. Section "Files" - Chemins ressources
Section "Files"
    ModulePath   "/usr/lib/xorg/modules"
    FontPath     "/usr/share/fonts/X11/misc"
    FontPath     "/usr/share/fonts/X11/100dpi"
    FontPath     "/usr/share/fonts/X11/75dpi"
    FontPath     "/usr/share/fonts/X11/Type1"
    InputDevices "/dev/gpmdata"         # Périphérique d'entrée (GPM)
    InputDevices "/dev/input/mice"      # Périphériques souris
EndSection

# 2. Section "Module" - Modules à charger
Section "Module"
    Load  "glx"        # OpenGL
    Load  "dri"        # Direct Rendering
    Load  "dri2"
    Load  "extmod"     # Extensions
EndSection

# 3. Section "InputDevice" ou "InputClass" - Périphériques entrée
# 
# ⚠️ InputDevice (legacy) : configuration spécifique par périphérique
# ✅ InputClass (moderne) : configuration par classe de périphériques
#
# Périphériques supportés :
# - Clavier (keyboard)
# - Souris (mouse)
# - Touchpad
# - Tablette graphique
#
# ⚠️ Il faut une section pour chaque périphérique d'entrée (si InputDevice)
#
# Composants InputDevice :
# - Identifier : identifiant unique
# - Driver : pilote (ex: kbd, mouse, evdev, synaptics, libinput, etc.)
# - Option : diverses options liées au pilote
#
# Note : Les chemins des fichiers de périphériques sont généralement 
#        placés dans la section Files sous les entrées InputDevice.

Section "InputDevice"
    Identifier  "Keyboard0"
    Driver      "kbd"
    Option      "CoreKeyboard"
    Option      "XkbLayout"  "us"     # Disposition clavier
    Option      "XkbVariant" "intl"
    Option      "XkbOptions" "compose:ralt"
EndSection

Section "InputDevice"
    Identifier  "Mouse0"
    Driver      "mouse"
    Option      "Protocol"    "auto"
    Option      "Device"      "/dev/input/mice"
    Option      "Emulate3Buttons" "no"
    Option      "ZAxisMapping" "4 5"  # Molette
EndSection

# 4. Section "Device" - Carte graphique
Section "Device"
    Identifier  "Card0"
    Driver      "radeon"               # Driver: radeon, nvidia, intel, nouveau, vesa
    BusID       "PCI:1:0:0"           # Bus PCI de la carte
    Option      "DynamicClocks" "on"
    Option      "AccelMethod"   "glamor"
EndSection

# Driver VESA (fallback universel)
Section "Device"
    Identifier  "VesaFallback"
    Driver      "vesa"                 # Low-res, compatible partout
EndSection

# 5. Section "Monitor" - Configuration écran
Section "Monitor"
    Identifier   "Monitor0"
    VendorName   "Samsung"
    ModelName    "SyncMaster"
    HorizSync    30.0 - 81.0          # kHz
    VertRefresh  56.0 - 75.0          # Hz
    Option       "DPMS"                # Display Power Management Signaling
EndSection

# 6. Section "Screen" - Association Device + Monitor + Résolutions
Section "Screen"
    Identifier   "Screen0"
    Device       "Card0"
    Monitor      "Monitor0"
    DefaultDepth 24                    # Profondeur couleur (bits)
    
    SubSection "Display"
        Depth     1
        Modes     "1024x768"
    EndSubSection
    
    SubSection "Display"
        Depth     4
        Modes     "1024x768"
    EndSubSection
    
    SubSection "Display"
        Depth     8
        Modes     "1024x768"
    EndSubSection
    
    SubSection "Display"
        Depth     15
        Modes     "1920x1080" "1600x1200" "1280x1024" "1024x768"
    EndSubSection
    
    SubSection "Display"
        Depth     16
        Modes     "1920x1080" "1600x1200" "1280x1024" "1024x768"
    EndSubSection
    
    SubSection "Display"
        Depth     24                   # Profondeur par défaut
        Modes     "1920x1080" "1600x1200" "1280x1024" "1024x768" "800x600"
    EndSubSection
EndSection

# 7. Section "ServerLayout" - Agencement global
Section "ServerLayout"
    Identifier     "DefaultLayout"
    Screen      0  "Screen0"  0 0      # Écran principal
    InputDevice    "Keyboard0" "CoreKeyboard"
    InputDevice    "Mouse0"    "CorePointer"
    Option         "Xinerama"  "off"   # Multi-écrans (désactivé si un seul)
EndSection

# 8. Section "DRI" - Direct Rendering Infrastructure (OpenGL)
Section "DRI"
    Mode 0666                          # Permissions
EndSection

# 9. Section "Extensions" - Extensions X11
Section "Extensions"
    Option "Composite" "Enable"        # Compositing (transparence, effets)
EndSection

# 10. Section "ServerFlags" - Options globales du serveur X Window
Section "ServerFlags"
    # Séquences clavier et gestion consoles
    Option "DontZap"        "false"    # Si true : désactive Alt-Ctrl-Backspace (kill serveur X)
    Option "DontVTSwitch"   "false"    # Si true : désactive accès consoles via Alt-Ctrl-Fn
    Option "DontZoom"       "false"    # Si true : interdit changement résolution par Alt-Ctrl-+/-
    
    # Périphériques
    Option "AllowMouseOpenFail" "false"  # Si true : X se charge même sans souris détectée
    Option "XkbDisable"     "false"    # Si true : désactive le clavier (⚠️ dangereux)
    
    # Économie d'énergie (DPMS - Display Power Management Signaling)
    Option "BlankTime"      "10"       # Durée en minutes avant mode Stand by (économiseur d'écran)
    Option "StandbyTime"    "20"       # Durée en minutes de la phase Stand by
    Option "SuspendTime"    "30"       # Durée en minutes avant Suspend
    Option "OffTime"        "40"       # Durée en minutes avant extinction complète
EndSection
```

---

### 📝 Exemple Complet xorg.conf (Configuration Réelle)

Voici un exemple complet de configuration fonctionnelle avec carte Nvidia GeForce 8600 GT et écran LG :

```ini
Section "Monitor"
    Identifier   "L1915S"
    ModelName    "L1915S"
    VendorName   "LG"
EndSection

Section "Device"
    Identifier   "Device[0]"
    Driver       "nvidia"
    VendorName   "NVidia"
    BoardName    "GeForce 8600 GT"
    Option       "NoLogo" "0"
    Option       "DPI" "86 x 86"
    Option       "RenderAccel" "True"
    Option       "AddARGBGLXVisuals" "True"
EndSection

Section "Screen"
    Identifier    "Screen[0]"
    Device        "Device[0]"
    Monitor       "Monitor[0]"
    DefaultDepth   24
    
    SubSection    "Display"
        Depth        15
        Modes       "1280x1024" "1024x768" "800x600" "640x480"
    EndSubSection
    
    SubSection    "Display"
        Depth        16
        Modes       "1280x1024" "1024x768" "800x600" "640x480"
    EndSubSection
    
    SubSection    "Display"
        Depth        24
        Modes       "1280x1024" "1024x768" "800x600" "640x480"
    EndSubSection
    
    SubSection    "Display"
        Depth        8
        Modes       "1280x1024" "1024x768" "800x600" "640x480"
    EndSubSection
EndSection

Section "ServerLayout"
    Identifier   "Layout[all]"
    Screen       "Screen[0]" 0 0
    InputDevice  "Keyboard[0]" "CoreKeyboard"
    InputDevice  "Mouse[1]" "CorePointer"
EndSection

Section "Files"
    InputDevices    "/dev/gpmdata"
    InputDevices    "/dev/input/mice"
    FontPath      "/usr/share/fonts/misc:unscaled"
    FontPath      "/usr/share/fonts/75dpi:unscaled"
    # ... autres FontPath
EndSection
```

**Points clés de cet exemple** :
- **Monitor** : Simple identification (pas de paramètres complexes nécessaires avec matériel moderne)
- **Device** : Options Nvidia spécifiques (RenderAccel, AddARGBGLXVisuals, DPI)
- **Screen** : Multiples profondeurs couleur (8, 15, 16, 24 bits) avec résolutions par profondeur
- **ServerLayout** : Liaison de tous les composants (écran + périphériques d'entrée)
- **Files** : Chemins InputDevices pour GPM et souris, plus FontPath

---

### 🔧 Configuration Modulaire (Moderne)

**Fichiers dans `/etc/X11/xorg.conf.d/`** :

**Exemple 1** : Clavier AZERTY
```bash
# /etc/X11/xorg.conf.d/00-keyboard.conf
Section "InputClass"
    Identifier "system-keyboard"
    MatchIsKeyboard "on"
    Option "XkbLayout" "fr"           # Français AZERTY
    Option "XkbVariant" "azerty"
    Option "XkbOptions" "compose:ralt"
EndSection
```

**Exemple 2** : Nvidia proprietary
```bash
# /etc/X11/xorg.conf.d/20-nvidia.conf
Section "Device"
    Identifier "Nvidia Card"
    Driver "nvidia"
    VendorName "NVIDIA Corporation"
    BoardName "GeForce GTX 1080"
    Option "NoLogo" "true"
EndSection
```

**Exemple 3** : Touchpad
```bash
# /etc/X11/xorg.conf.d/30-touchpad.conf
Section "InputClass"
    Identifier "touchpad"
    MatchIsTouchpad "on"
    Driver "libinput"
    Option "Tapping" "on"
    Option "NaturalScrolling" "true"
    Option "DisableWhileTyping" "true"
EndSection
```

**Exemple 4** : Multi-écrans
```bash
# /etc/X11/xorg.conf.d/10-monitor.conf
Section "Monitor"
    Identifier "HDMI-0"
    Option "Primary" "true"
    Option "Position" "0 0"
EndSection

Section "Monitor"
    Identifier "DP-1"
    Option "RightOf" "HDMI-0"
EndSection
```

**Avantages modularité** :
- ✅ Packages peuvent ajouter configs sans écraser
- ✅ Facilité maintenance (un fichier = une fonction)
- ✅ Override facile (créer fichier custom)
- ✅ Pas de risque casser config globale

---

### 🎨 Drivers Graphiques

**Drivers disponibles** :

| Driver | Type | Cartes | Performance | Utilisation |
|--------|------|--------|-------------|-------------|
| **intel** | Open-source | Intel HD/Iris | Excellente | Par défaut Intel |
| **nouveau** | Open-source | Nvidia | Moyenne | Nvidia libre |
| **nvidia** | Propriétaire | Nvidia | Excellente | Nvidia officiel |
| **amdgpu** | Open-source | AMD (recent) | Excellente | AMD moderne |
| **radeon** | Open-source | AMD (ancien) | Bonne | AMD legacy |
| **vesa** | Fallback | Toutes | Faible | Dépannage |
| **fbdev** | Fallback | Toutes | Très faible | Framebuffer |
| **modesetting** | Générique | Toutes | Moyenne | KMS générique |

**Driver VESA** :
```
┌─────────────────────────────────────────────┐
│  VESA (Video Electronics Standards Assoc.) │
├─────────────────────────────────────────────┤
│  Rôle: Driver FALLBACK universel           │
│                                             │
│  Résolution: Basse (souvent 1024x768)       │
│  Accélération: AUCUNE                       │
│  3D: NON                                    │
│                                             │
│  Usage:                                     │
│  - Dépannage driver principal cassé         │
│  - Recovery mode                            │
│  - Installation système (live CD)           │
│  - Carte non reconnue                       │
│                                             │
│  Performances: MINIMALES                    │
│  → Juste afficher écran basique ✅          │
│  → PAS pour utilisation normale ❌          │
└─────────────────────────────────────────────┘
```

**Installation drivers Nvidia (exemple)** :
```bash
# Debian/Ubuntu
apt search nvidia-driver
apt install nvidia-driver

# Arch
pacman -S nvidia nvidia-utils

# Reboot requis
reboot

# Vérifier driver chargé
lspci -k | grep -A 3 VGA
# Kernel driver in use: nvidia
```

---

## 🌐 Variable DISPLAY

### 📺 Principe

**DISPLAY** : Variable d'environnement indiquant **où afficher** les applications graphiques.

**Format** :
```
DISPLAY=[hostname]:display[.screen]
```

**Composants** :

| Partie | Description | Exemple |
|--------|-------------|---------|
| `hostname` | Serveur X (vide = local) | `192.168.1.100` ou vide |
| `display` | Numéro display | `:0` (premier) |
| `screen` | Numéro écran (optionnel) | `.0` (premier écran) |

**Exemples** :
```bash
# LOCAL : Premier display, premier écran
DISPLAY=:0
DISPLAY=:0.0

# LOCAL : Deuxième display
DISPLAY=:1

# DISTANT : Serveur X sur 192.168.52.3
DISPLAY=192.168.52.3:0

# DISTANT : Localhost via TCP
DISPLAY=localhost:10.0
```

---

### 🔌 Affichage Local

**Cas standard** : Applications sur machine locale, affichage local.

```bash
# Vérifier DISPLAY actuelle
echo $DISPLAY
# :0  ou  :0.0

# Lancer application graphique
firefox
# S'ouvre sur display :0

# Changer temporairement display
DISPLAY=:1 xterm
# xterm s'affiche sur display :1 (si existe)
```

**Multi-displays locaux** :
```bash
# Utilisateur 1 : Display :0 (TTY7)
# Ctrl+Alt+F7
echo $DISPLAY
# :0

# Utilisateur 2 : Display :1 (TTY8)
# Ctrl+Alt+F8
echo $DISPLAY
# :1

# Chaque utilisateur a son propre serveur X
```

---

### 🌍 Affichage Distant (X11 Forwarding)

**Principe** : Application **s'exécute** sur serveur distant, **s'affiche** sur client local.

```
┌─────────────────┐                    ┌─────────────────┐
│  CLIENT Local   │                    │  SERVEUR Distant│
│  (Laptop)       │                    │  (Server)       │
│                 │                    │                 │
│  ┌───────────┐  │                    │  ┌───────────┐  │
│  │ X Server  │◄─┼────────SSH────────┼──┤   xeyes   │  │
│  │ (Display) │  │   (Tunnel port)    │  │   App     │  │
│  └───────────┘  │                    │  └───────────┘  │
│                 │                    │                 │
│  Affichage ici  │                    │  Calculs ici    │
└─────────────────┘                    └─────────────────┘
```

**Configuration SSH X11 Forwarding** :

**Sur serveur** (`/etc/ssh/sshd_config`) :
```bash
X11Forwarding yes
X11DisplayOffset 10        # Displays virtuels commencent à :10
X11UseLocalhost yes        # Sécurité (bind localhost)
```

**Redémarrer SSH** :
```bash
sudo systemctl restart sshd
```

**Connexion depuis client** :
```bash
# Option -X (X11 forwarding)
ssh -X user@server.example.com

# OU -Y (trusted X11, moins sécurisé)
ssh -Y user@server.example.com

# Vérifier DISPLAY
echo $DISPLAY
# localhost:10.0  ← Display virtuel via tunnel SSH

# Lancer app graphique (s'affiche localement)
xeyes &
firefox &

# Application s'exécute sur serveur
# Affichage redirigé vers client via tunnel SSH
```

**Sans SSH, manuellement** (⚠️ DANGEREUX) :
```bash
# Sur CLIENT : Autoriser serveur distant (xhost)
xhost +192.168.1.100
# access control disabled, clients can connect from any host

# Sur SERVEUR : Définir DISPLAY vers client
export DISPLAY=192.168.1.50:0

# Lancer app
xeyes
# S'affiche sur 192.168.1.50 (client)
```

---

### 👁️ xeyes - Application de Test

**xeyes** : Petite application graphique affichant des yeux suivant le curseur.

**Utilité** :
- Tester DISPLAY fonctionne
- Vérifier X11 forwarding SSH
- Démo affichage distant

**Utilisation** :
```bash
# Local
xeyes &

# Distant via SSH
ssh -X user@server
xeyes &
# Yeux s'affichent sur machine locale ✅

# Changer DISPLAY explicitement
DISPLAY=:0 xeyes &       # Display :0
DISPLAY=10.0.0.5:0 xeyes &  # Serveur distant

# Avec options
xeyes -geometry 200x200+100+100  # Taille et position
xeyes -fg blue -outline red      # Couleurs
```

---

## 🔒 Contrôle d'Accès X11

### 🚪 xhost - Contrôle par Hôte

**xhost** : Gère **liste d'accès** au serveur X basée sur **hostname/IP**.

**⚠️ SÉCURITÉ FAIBLE** : Toute app sur hôte autorisé peut se connecter.

**Syntaxe** :
```bash
xhost [+|-][hostname|ip]
```

**Commandes** :

```bash
# Lister accès actuels
xhost
# access control enabled, only authorized clients can connect
# INET:192.168.1.100
# SI:localuser:alice

# Désactiver contrôle (TOUT LE MONDE autorisé)
xhost +
# access control disabled, clients can connect from any host
# ⚠️ DANGEREUX : N'importe qui peut se connecter !

# Réactiver contrôle (bloquer tout par défaut)
xhost -
# access control enabled, only authorized clients can connect

# Autoriser IP spécifique
xhost +192.168.1.100
# 192.168.1.100 being added to access control list

# Autoriser hostname
xhost +server.example.com

# Révoquer IP
xhost -192.168.1.100
# 192.168.1.100 being removed from access control list

# Autoriser utilisateur local (SI = Server Interpreted)
xhost +SI:localuser:alice

# Autoriser famille inet (IPv4)
xhost +inet:192.168.1.0/24
```

**Modes** :

| Commande | Signification |
|----------|---------------|
| `xhost` | Lister accès actuels |
| `xhost +` | Désactiver contrôle (autorise TOUS) |
| `xhost -` | Activer contrôle (bloque TOUS) |
| `xhost +HOST` | Autoriser HOST spécifique |
| `xhost -HOST` | Révoquer HOST |
| `xhost +SI:localuser:USER` | Autoriser utilisateur local |

**Par défaut** :
```bash
xhost
# access control enabled, only authorized clients can connect
# SI:localuser:bob
# → Seul utilisateur local bob autorisé
```

**Exemple affichage distant** :
```bash
# CLIENT (affiche)
xhost +192.168.50.24
# Autoriser serveur 192.168.50.24

# SERVEUR (exécute app)
export DISPLAY=192.168.1.50:0  # IP du client
xeyes
# S'affiche sur client ✅
```

**⚠️ Problème xhost** :
- Basé sur hostname/IP (facile à spoofing)
- Toutes apps sur hôte autorisé peuvent se connecter
- Pas d'authentification forte

**➡️ Solution moderne** : `xauth` (authentification par token)

---

### 🔐 xauth - Authentification par Token

**xauth** : Mécanisme **authentification forte** via cookie/token partagé.

**Principe** :
```
┌──────────────────────────────────────────────────────┐
│  1. Client génère TOKEN (cookie magique)            │
│     → Stocké dans ~/.Xauthority                     │
│                                                      │
│  2. Serveur distant obtient TOKEN                   │
│     → Copie ~/.Xauthority ou ajoute manuellement    │
│                                                      │
│  3. Serveur distant tente connexion X11             │
│     → Envoie TOKEN au serveur X du client           │
│                                                      │
│  4. Serveur X vérifie TOKEN                         │
│     → Si correspond : OK ✅                          │
│     → Sinon : Refusé ❌                              │
└──────────────────────────────────────────────────────┘
```

**Fichier** : `~/.Xauthority`
- Fichier binaire contenant cookies X11
- Format : DISPLAY + Protocol + Cookie (hexadécimal)
- Permissions : `600` (user seulement)

**Commandes xauth** :

```bash
# Lister cookies actuels
xauth list
# laptop/unix:0  MIT-MAGIC-COOKIE-1  a1b2c3d4e5f6...
# laptop/unix:10 MIT-MAGIC-COOKIE-1  f6e5d4c3b2a1...

# Afficher fichier utilisé
xauth info
# Authority file:       /home/user/.Xauthority
# File new:             no
# File locked:          no
# Number of entries:    2
# Changes honored:      yes
# Changes made:         no

# Générer nouveau cookie pour display
xauth generate :0 .
# → Ajoute cookie pour display :0

# Ajouter cookie manuellement
xauth add :0 . a1b2c3d4e5f6789012345678abcdef00

# Format complet
xauth add hostname/unix:0 MIT-MAGIC-COOKIE-1 a1b2c3d4e5f6...

# Extraire cookie pour display
xauth extract /tmp/cookie.xauth :0
# → Sauvegarde cookie dans fichier

# Merger cookies depuis fichier
xauth merge /tmp/cookie.xauth

# Supprimer cookie
xauth remove :0
```

---

### 🔄 Utilisation xauth pour Affichage Distant

**Méthode 1 : SSH (automatique)** ✅ :
```bash
# SSH configure automatiquement xauth !
ssh -X user@server
echo $DISPLAY
# localhost:10.0

xauth list
# server/unix:10  MIT-MAGIC-COOKIE-1  ...
# → Cookie déjà configuré par SSH ✅

xeyes  # Fonctionne directement
```

**Méthode 2 : Manuel (sans SSH)** :

**Sur CLIENT** :
```bash
# Extraire cookie pour display local
xauth extract /tmp/x11cookie :0

# Transférer fichier vers serveur
scp /tmp/x11cookie user@server:/tmp/
```

**Sur SERVEUR** :
```bash
# Importer cookie
xauth merge /tmp/x11cookie

# Vérifier
xauth list
# client.domain/unix:0  MIT-MAGIC-COOKIE-1  ...

# Définir DISPLAY vers client
export DISPLAY=client.domain:0

# Lancer app
xeyes
# ✅ Autorisé (cookie valide)
```

**Méthode 3 : Copie directe** :

**CLIENT** :
```bash
# Activer TCP listening (désactivé par défaut sécurité)
# /etc/X11/xinit/xserverrc
exec /usr/bin/X -nolisten tcp  # Enlever -nolisten tcp

# Redémarrer X
```

**SERVEUR** :
```bash
# Copier .Xauthority du client
scp user@client:~/.Xauthority ~/.Xauthority

export DISPLAY=client:0
xeyes
```

---

### 🆚 xhost vs xauth

| Critère | xhost | xauth |
|---------|-------|-------|
| **Sécurité** | ❌ Faible (IP/hostname) | ✅ Forte (token cryptographique) |
| **Granularité** | Hôte entier | Par display/par app |
| **Spoofing** | ❌ Facile | ✅ Difficile |
| **Configuration** | Rapide (1 commande) | Plus complexe (cookies) |
| **SSH** | Manuelle | ✅ Automatique (-X) |
| **Recommandé** | ❌ Non (legacy) | ✅ Oui (moderne) |

**Recommandation** :
- ✅ Toujours utiliser **SSH avec -X** (xauth automatique)
- ❌ Éviter `xhost +` (ouverture totale dangereuse)
- ✅ Si sans SSH : `xauth` (manuel mais sécurisé)

---

## 📁 Fichiers et Logs X11

### 📝 ~/.xsession-errors

**Rôle** : Fichier de log contenant **erreurs et avertissements** du démarrage de session X11.

**Localisation** :
```bash
~/.xsession-errors
```

**Contenu** :
- Erreurs applications lancées au démarrage
- Warnings bibliothèques manquantes
- Erreurs configuration window manager/DE
- Messages debug session X

**Utilisation** :
```bash
# Visualiser erreurs récentes
tail -f ~/.xsession-errors

# Rechercher erreur spécifique
grep -i error ~/.xsession-errors
grep -i warning ~/.xsession-errors

# Vider fichier (peut devenir gros)
> ~/.xsession-errors

# Exemples d'erreurs courantes
cat ~/.xsession-errors
# (gnome-settings-daemon:2345): GLib-GIO-WARNING **: 
# Settings schema 'org.gnome.desktop.interface' does not contain a key named 'gtk-theme'
#
# ** (process:3456): WARNING **: Could not load theme icon 'missing.png'
#
# Gtk-WARNING **: Unable to locate theme engine in module_path: "adwaita"
```

**Debugger problèmes démarrage** :
```bash
# Session X ne démarre pas ou se bloque
# 1. Basculer TTY (Ctrl+Alt+F2)

# 2. Lire erreurs
less ~/.xsession-errors

# 3. Chercher dernières lignes (moment crash)
tail -n 50 ~/.xsession-errors

# 4. Corriger (désactiver app problématique, installer package manquant)

# 5. Retenter connexion graphique (Ctrl+Alt+F7)
```

---

### 📊 Autres Fichiers Importants

**Logs système X11** :
```bash
# Log serveur X (Xorg)
/var/log/Xorg.0.log
/var/log/Xorg.1.log  # Si multiples displays

# Contient:
# - Détection matériel
# - Chargement drivers
# - Résolutions détectées
# - Erreurs serveur X

journalctl -u gdm       # GNOME Display Manager
journalctl -u sddm      # KDE Display Manager
journalctl -u lightdm   # Lightweight Display Manager
```

**Configuration utilisateur X** :
```bash
~/.xinitrc              # Utilisé par startx (pas display managers)
~/.xserverrc            # Config serveur X local
~/.Xresources           # Config apps X11 (couleurs, fonts)
~/.Xmodmap              # Remapping clavier/souris
```

**Exemple ~/.xinitrc** :
```bash
#!/bin/bash
# Lancé par startx (sans display manager)

# Charger ressources
xrdb -merge ~/.Xresources

# Fond d'écran
feh --bg-scale ~/wallpaper.jpg

# Compositor
picom -b

# Status bar
polybar &

# Window Manager (dernière ligne, remplace script)
exec i3
```

---

## 🎓 10 Scénarios Pratiques Complets

### Scénario 1️⃣ : Configurer clavier AZERTY dans X11

**Contexte** : Système installé avec clavier US, passer en AZERTY français.

```bash
# Méthode 1 : Fichier de config (PERMANENT)
sudo nano /etc/X11/xorg.conf.d/00-keyboard.conf

# Contenu
Section "InputClass"
    Identifier "system-keyboard"
    MatchIsKeyboard "on"
    Option "XkbLayout" "fr"
    Option "XkbVariant" "azerty"
    Option "XkbOptions" "terminate:ctrl_alt_bksp"
EndSection

# Sauvegarder et redémarrer X
sudo systemctl restart gdm
# Ou Ctrl+Alt+Backspace si terminate activé

# Méthode 2 : Commande temporaire (session courante)
setxkbmap fr azerty

# Méthode 3 : localectl (systemd)
localectl set-x11-keymap fr azerty

# Vérifier
setxkbmap -query
# model:      pc105
# layout:     fr
# variant:    azerty
```

---

### Scénario 2️⃣ : Résoudre écran noir au démarrage X11

**Symptômes** : Écran noir après login, curseur clignotant.

**Debugging** :
```bash
# 1. Basculer TTY texte
# Ctrl+Alt+F2

# 2. Login en console

# 3. Vérifier logs X
sudo tail -100 /var/log/Xorg.0.log

# Chercher lignes (EE) = Erreur, (WW) = Warning
grep -E "\(EE\)|\(WW\)" /var/log/Xorg.0.log

# 4. Vérifier erreurs session utilisateur
tail -50 ~/.xsession-errors

# 5. Identifier problème courant

# Cas A : Driver manquant
# (EE) Failed to load module "nvidia" (module does not exist)
sudo apt install nvidia-driver
sudo reboot

# Cas B : Resolution invalide
# (WW) NVIDIA(0): Unable to get display device for DPI computation
# → Enlever résolution custom dans xorg.conf

# Cas C : Desktop Environment corrompu
# Tester avec window manager minimal
echo "exec openbox" > ~/.xinitrc
startx  # Depuis TTY

# Si fonctionne → Problème DE (GNOME/KDE)
# Réinstaller
sudo apt install --reinstall gnome-shell

# 6. Fallback driver VESA (dépannage)
sudo nano /etc/X11/xorg.conf.d/20-fallback.conf

Section "Device"
    Identifier "Fallback"
    Driver "vesa"
EndSection

# Redémarrer
sudo systemctl restart gdm
```

---

### Scénario 3️⃣ : Affichage distant via SSH avec X11 forwarding

**Contexte** : Utiliser GUI sur serveur distant depuis laptop.

**Configuration** :

**Sur SERVEUR** :
```bash
# 1. Activer X11Forwarding dans SSH
sudo nano /etc/ssh/sshd_config

X11Forwarding yes
X11DisplayOffset 10
X11UseLocalhost yes

# 2. Redémarrer SSH
sudo systemctl restart sshd

# 3. Installer apps X11 de base (si serveur headless)
sudo apt install xauth x11-apps
```

**Sur CLIENT** :
```bash
# 1. Connexion SSH avec forwarding
ssh -X user@server.example.com

# Ou trusted (moins sécurisé mais plus compatible)
ssh -Y user@server.example.com

# 2. Vérifier DISPLAY
echo $DISPLAY
# localhost:10.0  ✅ Forwarding actif

# 3. Vérifier xauth configuré
xauth list
# server/unix:10  MIT-MAGIC-COOKIE-1  ...

# 4. Tester avec xeyes
xeyes &
# Yeux s'affichent sur laptop ✅

# 5. Lancer app réelle
firefox &
gedit /etc/hosts &

# Apps s'exécutent sur serveur
# Affichage redirigé vers laptop via tunnel SSH chiffré
```

**Troubleshooting** :
```bash
# Erreur : "cannot open display"
echo $DISPLAY
# (vide) → Forwarding non actif

# Vérifier SSH config client
cat ~/.ssh/config
# ForwardX11 yes
# ForwardX11Trusted yes

# Ou forcer
ssh -vvv -X user@server 2>&1 | grep -i x11
# debug1: Requesting X11 forwarding with authentication spoofing.
# debug1: Remote: X11 forwarding disabled in user configuration file.
# → Problème config serveur

# Erreur : "X11 forwarding request failed"
# Serveur : installer xauth
sudo apt install xauth
```

---

### Scénario 4️⃣ : Configurer dual-screen (multi-monitors)

**Contexte** : Laptop + écran externe HDMI.

**Détection** :
```bash
# Lister sorties vidéo disponibles
xrandr
# Screen 0: minimum 320 x 200, current 3840 x 1080, maximum 16384 x 16384
# eDP-1 connected primary 1920x1080+0+0 (laptop)
# HDMI-1 connected 1920x1080+1920+0 (external)
# DP-1 disconnected

# Informations verboses
xrandr --verbose
```

**Configuration temporaire (xrandr)** :
```bash
# Activer externe à droite du laptop
xrandr --output HDMI-1 --auto --right-of eDP-1

# Positionner au-dessus
xrandr --output HDMI-1 --auto --above eDP-1

# Résolution spécifique
xrandr --output HDMI-1 --mode 2560x1440

# Rotation
xrandr --output HDMI-1 --rotate left

# Définir principal
xrandr --output HDMI-1 --primary

# Désactiver écran
xrandr --output HDMI-1 --off

# Mirror (même affichage)
xrandr --output HDMI-1 --same-as eDP-1
```

**Configuration permanente (xorg.conf.d)** :
```bash
sudo nano /etc/X11/xorg.conf.d/10-monitor.conf

Section "Monitor"
    Identifier "eDP-1"
    Option "Primary" "true"
    Option "Position" "0 0"
EndSection

Section "Monitor"
    Identifier "HDMI-1"
    Option "RightOf" "eDP-1"
    Option "PreferredMode" "1920x1080"
EndSection

# Redémarrer X
sudo systemctl restart gdm
```

**Script démarrage automatique** :
```bash
# ~/.config/autostart/monitors.sh
#!/bin/bash
xrandr --output HDMI-1 --auto --right-of eDP-1
```

**GUI (plus simple)** :
```bash
# GNOME Settings
gnome-control-center display

# KDE System Settings
systemsettings5

# XFCE Display
xfce4-display-settings

# arandr (GUI pour xrandr)
sudo apt install arandr
arandr
# → Interface graphique drag-and-drop
# → Save layout → Script généré
```

---

### Scénario 5️⃣ : Passer de X11 à Wayland (et vice-versa)

**Contexte** : Tester Wayland ou revenir à X11 si problèmes.

**Vérifier session courante** :
```bash
echo $XDG_SESSION_TYPE
# wayland  ou  x11

# Ou
loginctl show-session $(loginctl | grep $(whoami) | awk '{print $1}') -p Type
# Type=wayland
```

**Changer session (GNOME GDM)** :
```bash
# 1. Logout

# 2. À l'écran de connexion :
#    Cliquer icône engrenage ⚙️  (en bas à droite)
#    
#    Options disponibles :
#    - GNOME (Wayland par défaut)
#    - GNOME on Xorg (X11)

# 3. Sélectionner "GNOME on Xorg"

# 4. Login

# 5. Vérifier
echo $XDG_SESSION_TYPE
# x11  ✅
```

**Forcer X11 par défaut (désactiver Wayland)** :
```bash
# GDM : Éditer config
sudo nano /etc/gdm3/custom.conf

# Décommenter ligne
WaylandEnable=false

# Redémarrer GDM
sudo systemctl restart gdm3

# Ou reboot
sudo reboot
```

**Forcer Wayland (si était désactivé)** :
```bash
sudo nano /etc/gdm3/custom.conf

# Commenter ou supprimer
#WaylandEnable=false

sudo systemctl restart gdm3
```

**KDE SDDM** :
```bash
# À l'écran login :
# Session → Plasma (Wayland) ou Plasma (X11)

# Défaut système
sudo nano /etc/sddm.conf

[General]
DisplayServer=wayland   # ou x11
```

**Vérifier compatibilité Wayland** :
```bash
# Apps nécessitant X11 (tourneront via XWayland)
# - OBS Studio (version ancienne)
# - certaines apps Wine
# - screen recording tools anciens

# Vérifier XWayland actif
ps aux | grep -i xwayland
# /usr/bin/Xwayland :0 ...  ✅
```

---

### Scénario 6️⃣ : Installer environnement graphique minimal (Window Manager)

**Contexte** : Serveur sans GUI, ajouter WM léger (pas DE complet).

```bash
# 1. Installer serveur X
sudo apt install xorg

# 2. Installer Window Manager (choix)
sudo apt install i3              # Tiling WM
# OU
sudo apt install openbox         # Stacking WM

# 3. Installer terminal et outils de base
sudo apt install rxvt-unicode    # Terminal léger
sudo apt install dmenu           # Lanceur applications
sudo apt install feh             # Fond d'écran

# 4. Créer ~/.xinitrc
nano ~/.xinitrc

#!/bin/bash
# Fond d'écran
feh --bg-scale ~/wallpaper.jpg &

# Éventuellement compositor (transparence)
# picom -b &

# Lancer WM (remplace script)
exec i3

# Rendre exécutable
chmod +x ~/.xinitrc

# 5. Tester depuis TTY
startx

# 6. Configuration i3
# Au premier lancement : Assistant config
# - Touche Mod : Alt ou Win
# - Générer config : Yes

# 7. Raccourcis i3 de base
# Mod+Enter : Terminal
# Mod+d : dmenu (lancer app)
# Mod+Shift+q : Fermer fenêtre
# Mod+Shift+e : Quitter i3

# 8. Installer apps supplémentaires
sudo apt install firefox         # Navigateur
sudo apt install pcmanfm         # File manager GUI
sudo apt install nitrogen        # Wallpaper manager
sudo apt install lxappearance    # Thèmes GTK

# 9. Optional : Display Manager (login graphique)
sudo apt install lightdm
sudo systemctl enable lightdm
sudo systemctl start lightdm
# Au login, sélectionner session "i3"
```

**Résultat** :
- RAM utilisée : ~200 MB (vs 1-2 GB pour GNOME)
- Démarrage rapide
- Contrôle total

---

### Scénario 7️⃣ : Diagnostiquer ralentissements graphiques

**Symptômes** : Interface lente, fenêtres saccadées, animations laggy.

**Diagnostic** :

```bash
# 1. Vérifier driver graphique actuel
lspci -k | grep -A 3 VGA
# Kernel driver in use: nouveau  ← Driver open-source Nvidia (lent)

# 2. Installer driver propriétaire si Nvidia
sudo apt install nvidia-driver
sudo reboot

# 3. Vérifier accélération 3D active
glxinfo | grep "direct rendering"
# direct rendering: Yes  ✅

# Si non installé
sudo apt install mesa-utils

# 4. Tester performances OpenGL
glxgears
# ~60 FPS (vsync) : Normal
# <30 FPS : Problème accélération

# 5. Vérifier compositing (effets) actif
# Si trop lent, désactiver

# GNOME
gsettings set org.gnome.desktop.interface enable-animations false

# KDE
# System Settings → Display → Compositor → Disable

# XFCE
# Settings → Window Manager Tweaks → Compositor → Disable

# 6. Vérifier résolution trop élevée pour GPU
xrandr
# 3840x2160 sur vieille carte → Baisser

xrandr --output HDMI-1 --mode 1920x1080

# 7. Monitorer usage GPU
nvidia-smi  # Nvidia

watch -n 1 nvidia-smi

# AMD
radeontop

# Intel
intel_gpu_top

# 8. Vérifier température GPU (throttling?)
sensors
# Requires lm-sensors
sudo apt install lm-sensors
sudo sensors-detect

# 9. Désactiver effets inutiles
# GNOME : Extensions
gnome-extensions-app
# Désactiver animations, effets

# 10. Fallback : Mode logiciel (lent mais stable)
# Forcer rendu logiciel (debug seulement)
LIBGL_ALWAYS_SOFTWARE=1 glxgears
```

---

### Scénario 8️⃣ : Créer session X11 virtuelle (VNC/Xorg headless)

**Contexte** : Accès GUI serveur sans écran physique via VNC.

```bash
# 1. Installer serveur VNC
sudo apt install tigervnc-standalone-server
# Ou
sudo apt install x11vnc

# 2. Configurer mot de passe VNC
vncpasswd
# Password: ********
# Verify: ********

# Fichier créé : ~/.vnc/passwd

# 3. Démarrer serveur VNC (crée display virtuel)
vncserver :1 -geometry 1920x1080 -depth 24
# New 'server:1 (user)' desktop is server:1

# 4. Vérifier display
echo $DISPLAY
# :1  ✅

# 5. Depuis client, connexion VNC
# Windows : TightVNC Viewer, RealVNC
# Linux : vncviewer, Remmina

vncviewer server.example.com:1

# 6. Lancer apps dans session VNC
# Sur serveur
DISPLAY=:1 firefox &

# S'affiche dans VNC ✅

# 7. Tuer session VNC
vncserver -kill :1

# 8. Autostart VNC (systemd)
sudo nano /etc/systemd/system/vncserver@.service

[Unit]
Description=Remote desktop service (VNC)
After=syslog.target network.target

[Service]
Type=forking
User=YOUR_USER
ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill :%i > /dev/null 2>&1 || :'
ExecStart=/usr/bin/vncserver :%i -geometry 1920x1080 -depth 24
ExecStop=/usr/bin/vncserver -kill :%i

[Install]
WantedBy=multi-user.target

# Activer
sudo systemctl enable vncserver@1.service
sudo systemctl start vncserver@1.service
```

**Alternative x11vnc (partage display existant)** :
```bash
# Partager display :0 actuel
x11vnc -display :0 -auth ~/.Xauthority

# Avec mot de passe
x11vnc -display :0 -auth ~/.Xauthority -passwd secret

# En arrière-plan
x11vnc -display :0 -auth ~/.Xauthority -passwd secret -forever -bg
```

---

### Scénario 9️⃣ : Configurer résolution custom (mode non détecté)

**Contexte** : Écran supporte 1920x1200 mais X11 ne propose que 1920x1080.

```bash
# 1. Générer mode avec cvt
cvt 1920 1200
# Modeline "1920x1200_60.00"  193.25  1920 2056 2256 2592  1200 1203 1209 1245 -hsync +vsync

# 2. Créer nouveau mode dans xrandr
xrandr --newmode "1920x1200_60.00" 193.25  1920 2056 2256 2592  1200 1203 1209 1245 -hsync +vsync

# 3. Ajouter mode à la sortie
xrandr --addmode HDMI-1 1920x1200_60.00

# 4. Activer mode
xrandr --output HDMI-1 --mode 1920x1200_60.00

# 5. Vérifier
xrandr | grep 1920x1200
#    1920x1200_60.00  59.95*  ✅

# 6. Rendre permanent
sudo nano /etc/X11/xorg.conf.d/10-monitor.conf

Section "Monitor"
    Identifier "HDMI-1"
    Modeline "1920x1200_60.00" 193.25 1920 2056 2256 2592 1200 1203 1209 1245 -hsync +vsync
    Option "PreferredMode" "1920x1200_60.00"
EndSection

Section "Screen"
    Identifier "Screen0"
    Monitor "HDMI-1"
    DefaultDepth 24
    SubSection "Display"
        Depth 24
        Modes "1920x1200_60.00" "1920x1080" "1600x1200"
    EndSubSection
EndSection

# 7. Redémarrer X
sudo systemctl restart gdm
```

---

### Scénario 🔟 : Sécuriser accès X11 distant avec SSH + xauth

**Contexte** : Accès GUI distant sécurisé (meilleures pratiques).

**Configuration complète** :

**Serveur** :
```bash
# 1. SSH config
sudo nano /etc/ssh/sshd_config

# Activer X11 forwarding
X11Forwarding yes
X11DisplayOffset 10
X11UseLocalhost yes

# Sécurité SSH (bonus)
PermitRootLogin no
PasswordAuthentication no  # Clés seulement
PubkeyAuthentication yes

sudo systemctl restart sshd

# 2. Installer xauth
sudo apt install xauth x11-apps

# 3. Désactiver xhost (sécurité)
# Ne PAS faire xhost +

# Vérifier contrôle actif
xhost
# access control enabled  ✅

# 4. Firewall : Autoriser SSH seulement (pas port 6000 X11)
sudo ufw allow 22/tcp
sudo ufw deny 6000:6063/tcp  # Ports X11 (bloquer accès direct)
sudo ufw enable
```

**Client** :
```bash
# 1. Générer clés SSH (si pas déjà fait)
ssh-keygen -t ed25519 -C "user@client"

# 2. Copier clé publique vers serveur
ssh-copy-id user@server.example.com

# 3. Config SSH client
nano ~/.ssh/config

Host server
    HostName server.example.com
    User myuser
    ForwardX11 yes
    ForwardX11Trusted yes
    Compression yes

# 4. Connexion
ssh server

# Ou explicite
ssh -X server.example.com

# 5. Vérifier
echo $DISPLAY
# localhost:10.0  ✅

xauth list
# server.example.com/unix:10  MIT-MAGIC-COOKIE-1  ...  ✅

# 6. Tester
xeyes &
firefox &

# 7. Vérifier tunnel SSH
ss -tulpen | grep 6010
# tcp   LISTEN 0   128   127.0.0.1:6010   *:*   users:(("sshd",pid=1234))
# → Tunnel local pour X11
```

**Sécurité vérifiée** :
- ✅ Authentification SSH (clés, pas passwords)
- ✅ Chiffrement tunnel SSH
- ✅ xauth tokens (pas xhost +)
- ✅ X11UseLocalhost (pas d'exposition réseau)
- ✅ Firewall bloque ports X11 directs

---

## 🚨 10 Erreurs Courantes et Solutions

### Erreur 1️⃣ : "cannot open display" après ssh

**Symptôme** :
```bash
ssh user@server
firefox &
# Error: cannot open display:
```

**Cause** : X11 forwarding non actif.

**Solutions** :
```bash
# 1. Vérifier DISPLAY définie
echo $DISPLAY
# (vide) ❌

# 2. Reconnecter avec -X
exit
ssh -X user@server
echo $DISPLAY
# localhost:10.0  ✅

# 3. Vérifier config serveur SSH
# /etc/ssh/sshd_config
X11Forwarding yes  # Doit être yes

# 4. Installer xauth sur serveur
sudo apt install xauth

# 5. Vérifier client SSH
# ~/.ssh/config
ForwardX11 yes

# 6. Debug verbose
ssh -vvv -X user@server 2>&1 | grep -i x11
# debug1: Requesting X11 forwarding
# debug1: X11 forwarding enabled
```

---

### Erreur 2️⃣ : Écran noir après mise à jour drivers Nvidia

**Symptôme** : Après `apt upgrade`, écran noir au boot.

**Cause** : Drivers Nvidia incompatibles avec nouveau kernel.

**Solution** :
```bash
# 1. Basculer TTY (Ctrl+Alt+F2)

# 2. Login console

# 3. Vérifier driver chargé
lspci -k | grep -A 3 VGA
# Kernel driver in use: nouveau  ← Nvidia pas chargé

# 4. Réinstaller drivers Nvidia
sudo apt install --reinstall nvidia-driver

# Ou purge + réinstall
sudo apt purge nvidia-*
sudo apt install nvidia-driver

# 5. Reconstruire initramfs
sudo update-initramfs -u

# 6. Reboot
sudo reboot

# 7. Si toujours problème : Blacklist nouveau
sudo nano /etc/modprobe.d/blacklist-nouveau.conf

blacklist nouveau
options nouveau modeset=0

sudo update-initramfs -u
sudo reboot

# 8. Dernier recours : Driver VESA (temporaire)
sudo nano /etc/X11/xorg.conf.d/20-vesa.conf

Section "Device"
    Identifier "Fallback"
    Driver "vesa"
EndSection

sudo reboot
# Écran fonctionne (basse résolution)
# Diagnostiquer driver Nvidia ensuite
```

---

### Erreur 3️⃣ : xhost + ouvre accès à tous (sécurité)

**Symptôme** :
```bash
xhost +
# access control disabled, clients can connect from any host
```

**Problème** : N'importe qui sur réseau peut afficher sur votre écran, keylogger possible.

**Solution** :
```bash
# 1. Réactiver contrôle IMMÉDIATEMENT
xhost -
# access control enabled

# 2. Autoriser hosts spécifiques seulement
xhost +192.168.1.100   # IP précise
xhost +server.local    # Hostname

# 3. Préférer xauth (sécurisé)
# Via SSH -X (automatique)

# 4. Vérifier accès actuels
xhost
# SI:localuser:bob
# 192.168.1.100
# → Liste restrictive ✅

# 5. Nettoyer accès
xhost -192.168.1.100  # Révoquer IP

# ⚠️ NE JAMAIS laisser xhost + en production
```

---

### Erreur 4️⃣ : Résolution bloquée à 1024x768 (driver vesa)

**Symptôme** : Impossible sélectionner 1920x1080, seulement 1024x768.

**Cause** : Driver VESA chargé (fallback basse résolution).

**Solution** :
```bash
# 1. Vérifier driver actuel
lspci -k | grep -A 3 VGA
# Kernel driver in use: vesa  ← Problème

# 2. Identifier carte graphique
lspci | grep VGA
# 01:00.0 VGA compatible controller: NVIDIA Corporation GP107 [GeForce GTX 1050]

# 3. Installer driver approprié
# Nvidia
sudo apt install nvidia-driver

# AMD
sudo apt install firmware-amd-graphics xserver-xorg-video-amdgpu

# Intel
sudo apt install xserver-xorg-video-intel

# 4. Supprimer config vesa si existe
sudo rm /etc/X11/xorg.conf.d/20-vesa.conf

# 5. Reboot
sudo reboot

# 6. Vérifier driver utilisé
lspci -k | grep -A 3 VGA
# Kernel driver in use: nvidia  ✅

# 7. Vérifier résolutions disponibles
xrandr
# 1920x1080  60.00*+  ✅
```

---

### Erreur 5️⃣ : ~/.Xauthority permission denied

**Symptôme** :
```bash
startx
# /usr/bin/startx: line 123: /home/user/.Xauthority: Permission denied
```

**Cause** : Fichier `.Xauthority` appartient à root ou permissions incorrectes.

**Solution** :
```bash
# 1. Vérifier propriétaire
ls -la ~/.Xauthority
# -rw------- 1 root root  ← Problème (devrait être user)

# 2. Corriger propriétaire
sudo chown $USER:$USER ~/.Xauthority

# 3. Vérifier permissions
chmod 600 ~/.Xauthority

# 4. Si toujours problème : Recréer
rm ~/.Xauthority
xauth generate :0 .

# 5. Ou laisser X le recréer
rm ~/.Xauthority
startx
# Fichier recréé automatiquement

# 6. Prévenir (ne jamais sudo startx)
# ❌ sudo startx  → Crée .Xauthority en root
# ✅ startx       → Fichier en user
```

---

### Erreur 6️⃣ : Wayland incompatible avec app (screen sharing)

**Symptôme** : OBS Studio, Discord screen sharing ne fonctionne pas sous Wayland.

**Cause** : Apps legacy utilisent API X11 pour capture écran (incompatible Wayland).

**Solution** :
```bash
# Option 1 : Utiliser session X11 (temporaire)
# Logout → Sélectionner "GNOME on Xorg" au login

# Option 2 : Forcer app en XWayland
# (fonctionnne parfois)

# Option 3 : Passer X11 par défaut
sudo nano /etc/gdm3/custom.conf
WaylandEnable=false

sudo systemctl restart gdm3

# Option 4 : Attendre version Wayland de l'app
# OBS Studio 28+ supporte Pipewire (Wayland)
sudo apt install obs-studio

# Discord : Utiliser version web avec partage fenêtre

# Option 5 : Utiliser alternatives Wayland-native
# Kazam → SimpleScreenRecorder (X11)
# ou
# wlroots-based screen recording
sudo apt install wf-recorder  # Wayland screen recorder
```

---

### Erreur 7️⃣ : Tearing (déchirure image) pendant vidéos

**Symptôme** : Bandes horizontales lors défilement/vidéo.

**Cause** : Vsync désactivé ou drivers problématiques.

**Solutions** :
```bash
# Intel : TearFree
sudo nano /etc/X11/xorg.conf.d/20-intel.conf

Section "Device"
    Identifier "Intel Graphics"
    Driver "intel"
    Option "TearFree" "true"
EndSection

# AMD : TearFree
sudo nano /etc/X11/xorg.conf.d/20-amdgpu.conf

Section "Device"
    Identifier "AMD Graphics"
    Driver "amdgpu"
    Option "TearFree" "true"
EndSection

# Nvidia : ForceFullCompositionPipeline
sudo nano /etc/X11/xorg.conf.d/20-nvidia.conf

Section "Device"
    Identifier "Nvidia Card"
    Driver "nvidia"
    Option "metamodes" "nvidia-auto-select +0+0 {ForceFullCompositionPipeline=On}"
EndSection

# Redémarrer X
sudo systemctl restart gdm

# Ou Wayland (pas de tearing natif)
# Passer à session Wayland
```

---

### Erreur 8️⃣ : Multiple .Xauthority fichiers (confusion)

**Symptôme** : Connexions X11 aléatoirement refusées.

**Cause** : Multiples fichiers `.Xauthority` (versions backup, user vs root).

**Solution** :
```bash
# 1. Trouver tous fichiers
find ~ -name ".Xauthority*"
# /home/user/.Xauthority
# /home/user/.Xauthority.old
# /root/.Xauthority  ← Si sudo utilisé

# 2. Vérifier contenu actuel
xauth list
# Lister cookies display actuel

# 3. Nettoyer vieux fichiers
rm ~/.Xauthority.old
rm ~/.Xauthority.backup*

# 4. Supprimer root (si existe)
sudo rm /root/.Xauthority

# 5. Recréer propre
rm ~/.Xauthority
startx
# Ou relogin

# 6. Vérifier nouveau fichier
ls -la ~/.Xauthority
# -rw------- 1 user user  ✅

xauth list
# server/unix:0  MIT-MAGIC-COOKIE-1  ...  ✅
```

---

### Erreur 9️⃣ : X11 démarre sur mauvais GPU (laptop avec Nvidia Optimus)

**Symptôme** : Laptop avec GPU Intel + Nvidia, X11 sur Nvidia (batterie vide rapidement).

**Cause** : Nvidia utilisé par défaut au lieu d'Intel économe.

**Solution** :
```bash
# 1. Vérifier GPU actuel
glxinfo | grep "OpenGL renderer"
# OpenGL renderer string: GeForce GTX 1050  ← Nvidia actif

# 2. Installer gestion hybride
sudo apt install nvidia-prime

# 3. Basculer sur Intel (économie)
sudo prime-select intel

# 4. Reboot
sudo reboot

# 5. Vérifier switch
prime-select query
# intel  ✅

glxinfo | grep "OpenGL renderer"
# OpenGL renderer string: Mesa DRI Intel(R) HD Graphics  ✅

# 6. Lancer app spécifique sur Nvidia (ponctuel)
__NV_PRIME_RENDER_OFFLOAD=1 __GLX_VENDOR_LIBRARY_NAME=nvidia glxgears

# 7. Basculer vers Nvidia (performances)
sudo prime-select nvidia
sudo reboot

# Alternative : Bumblebee (ancien)
sudo apt install bumblebee primus
optirun glxgears  # Lance sur Nvidia
primusrun steam   # Jeux sur Nvidia
```

---

### Erreur 🔟 : startx échoue "Server is already active for display 0"

**Symptôme** :
```bash
startx
# Fatal server error:
# Server is already active for display 0
```

**Cause** : Serveur X déjà lancé sur display :0.

**Solution** :
```bash
# 1. Vérifier serveurs X actifs
ps aux | grep Xorg
# /usr/lib/xorg/Xorg :0  ← Déjà lancé

# 2. Option A : Utiliser display existant
# Retourner au display :0
# Ctrl+Alt+F7  (ou F1, F2 selon config)

# 3. Option B : Lancer sur autre display
startx -- :1
# Lance nouveau serveur X sur :1

# 4. Option C : Tuer serveur existant (DANGEREUX)
sudo pkill Xorg
# Tue toutes sessions graphiques !

# 5. Option D : Display Manager actif (normal)
systemctl status gdm
# active  ← GDM gère X automatiquement

# Pas besoin de startx si Display Manager actif
# Juste login graphique normal

# 6. Désactiver DM si besoin console pure
sudo systemctl disable gdm
sudo systemctl stop gdm

# Puis startx fonctionne
startx
```

---

## 📝 Antisèche (Cheat Sheet)

### Architecture Graphique
```
User → Apps → DE/WM → Display Server (X11/Wayland) → Kernel → Hardware
```

### Display Server
```bash
# X11
- Ancien (1984), complexe, déprécié
- Fallback universel
- Network transparent natif

# Wayland
- Moderne, sécurisé, performant
- Par défaut distros récentes
- XWayland pour compatibilité
```

### Desktop Environments
```bash
GNOME        # GTK+, moderne, lourd
KDE Plasma   # Qt, personnalisable, lourd
XFCE         # GTK+, léger, stable
MATE         # GTK+, classique, moyen
Cinnamon     # GTK+, intuitif, moyen
```

### Window Managers
```bash
i3           # Tiling, populaire
dwm          # Tiling, minimaliste
Openbox      # Stacking, léger
awesome      # Dynamic, scriptable
```

### Fichiers X11
```bash
/etc/X11/xorg.conf                # Config monolithique
/etc/X11/xorg.conf.d/*.conf       # Config modulaire (préféré)
~/.Xauthority                     # Tokens xauth
~/.xsession-errors                # Logs session
/var/log/Xorg.0.log               # Logs serveur X
```

### Configuration xorg.conf Sections
```bash
Section "Files"         # Fonts, modules
Section "Module"        # Modules chargés (glx, dri)
Section "InputDevice"   # Clavier, souris
Section "Device"        # Carte graphique, driver
Section "Monitor"       # Écran, résolutions
Section "Screen"        # Association Device+Monitor
Section "ServerLayout"  # Agencement global
```

### Drivers Graphiques
```bash
intel        # Intel HD/Iris
nouveau      # Nvidia open-source
nvidia       # Nvidia propriétaire
amdgpu       # AMD récent
radeon       # AMD ancien
vesa         # Fallback universel (low-res)
modesetting  # Générique KMS
```

### Variable DISPLAY
```bash
:0                    # Local, display 0
:0.0                  # Local, display 0, screen 0
192.168.1.100:0       # Distant, serveur 192.168.1.100
localhost:10.0        # SSH X11 forwarding

# Vérifier
echo $DISPLAY

# Définir
export DISPLAY=:0
```

### xhost (Contrôle accès)
```bash
xhost                 # Lister accès
xhost +               # ❌ Désactiver contrôle (DANGER)
xhost -               # Activer contrôle
xhost +192.168.1.100  # Autoriser IP
xhost -192.168.1.100  # Révoquer IP
xhost +SI:localuser:bob  # Autoriser user local
```

### xauth (Tokens)
```bash
xauth list                    # Lister cookies
xauth add :0 . COOKIE         # Ajouter cookie
xauth generate :0 .           # Générer cookie
xauth extract /tmp/x11 :0     # Exporter cookie
xauth merge /tmp/x11          # Importer cookie
xauth remove :0               # Supprimer cookie
```

### SSH X11 Forwarding
```bash
# Serveur: /etc/ssh/sshd_config
X11Forwarding yes
X11DisplayOffset 10
X11UseLocalhost yes

# Client
ssh -X user@server            # Forwarding X11
ssh -Y user@server            # Trusted forwarding

# Vérifier
echo $DISPLAY                 # localhost:10.0
xauth list                    # Cookie présent
```

### xrandr (Multi-écrans)
```bash
xrandr                        # Lister sorties
xrandr --output HDMI-1 --auto  # Activer écran
xrandr --output HDMI-1 --right-of eDP-1  # Positionner
xrandr --output HDMI-1 --mode 1920x1080  # Résolution
xrandr --output HDMI-1 --rotate left     # Rotation
xrandr --output HDMI-1 --off             # Désactiver
```

### Génération Configuration
```bash
sudo Xorg -configure           # Générer xorg.conf.new
sudo X -config ~/xorg.conf.new # Tester config
cvt 1920 1080                  # Générer modeline
```

### Tests X11
```bash
xeyes                         # App test (yeux)
glxinfo | grep "direct rendering"  # Vérifier 3D
glxgears                      # Test OpenGL FPS
xdpyinfo                      # Infos display
```

### Session Management
```bash
echo $XDG_SESSION_TYPE        # x11 ou wayland
startx                        # Démarrer X (sans DM)
systemctl restart gdm         # Redémarrer display manager
```

**Démarrage serveur X avec environnement (via startx)** :

Quand X est démarré via `startx`, il cherche à exécuter **dans cet ordre** :

1. `$HOME/.startxrc` ← **PRIORITÉ 1**
2. Si absent : `/usr/lib64/sys.startxrc`  
3. Si absent : `$HOME/.xinitrc`  
4. Si absent : `/etc/X11/xinit/xinitrc`

**Exemple ~/.xinitrc** :
```bash
#!/bin/bash
# Configuration session X lancée par startx

# Définir fond d'écran
xsetroot -solid gray

# Charger ressources X11
xrdb ~/.Xresources

# Lancer window manager (doit être en dernier avec exec)
exec i3
# OU exec openbox
# OU exec dwm
```

**⚠️ Note** : Ces fichiers sont pour `startx` uniquement (sans Display Manager). Si vous utilisez GDM/SDDM/LightDM, utilisez `~/.xsession` à la place.

---

### Émulateurs de Terminaux

Il existe de nombreux émulateurs de terminaux pour X11, avec différents niveaux de fonctionnalités :

| Terminal | Type | Caractéristiques |
|----------|------|------------------|
| **xterm** | Standard | Terminal X11 de référence, léger, basique |
| **rxvt** | Léger | Très léger, ressemble à xterm mais sans menus de configuration |
| **aterm** | Léger | Proche de rxvt, léger |
| **eterm** | Standard | Prévu pour remplacer xterm |
| **konsole** | KDE | Terminal livré avec KDE, riche en fonctionnalités |
| **gnome-terminal** | GNOME | Terminal livré avec GNOME, intégré dans l'environnement |
| **urxvt** | Léger | rxvt-unicode, supporte Unicode, très léger |
| **alacritty** | Moderne | Accéléré GPU, très rapide |
| **kitty** | Moderne | Accéléré GPU, riche en fonctionnalités |
| **terminator** | Avancé | Multiplexeur terminal (splits, tabs) |

**Installation exemples** :
```bash
# xterm (standard)
sudo apt install xterm

# rxvt (léger)
sudo apt install rxvt-unicode

# aterm
sudo apt install aterm

# eterm
sudo apt install eterm

# konsole (KDE)
sudo apt install konsole

# gnome-terminal (GNOME)
sudo apt install gnome-terminal

# alacritty (moderne)
sudo apt install alacritty

# kitty (moderne)
sudo apt install kitty
```

---

## 🎓 Points Clés pour l'Examen LPIC-1

### ✅ Concepts à Maîtriser Absolument

1. **Architecture en couches** :
   - Hardware → Kernel → Display Server → DE/WM → Apps → User
   - Display Server = X11 ou Wayland
   - DE = Environnement complet (GNOME, KDE)
   - WM = Gestionnaire fenêtres seul (i3, Openbox)

2. **X11 vs Wayland** :
   - X11 : Ancien, complexe, déprécié, fallback
   - Wayland : Moderne, sécurisé, performant
   - XWayland : Compatibilité apps X11 sur Wayland

3. **Configuration X11** :
   - `/etc/X11/xorg.conf` : Monolithique (legacy)
   - `/etc/X11/xorg.conf.d/*.conf` : Modulaire (moderne)
   - Sections : Files, Module, InputDevice, Device, Monitor, Screen, ServerLayout

4. **Drivers graphiques** :
   - intel, nvidia, nouveau, amdgpu, radeon
   - **vesa** : Fallback universel low-res (dépannage)
   - modesetting : Générique KMS

5. **Variable DISPLAY** :
   - Format : `[hostname]:display[.screen]`
   - Local : `:0`, `:0.0`
   - Distant : `192.168.1.100:0`
   - SSH forwarding : `localhost:10.0`

6. **xhost** :
   - Contrôle accès par IP/hostname
   - ⚠️ xhost + = DANGEREUX (tout le monde)
   - xhost - = Activer contrôle
   - Sécurité FAIBLE (legacy)

7. **xauth** :
   - Authentification par token (cookie)
   - Fichier : `~/.Xauthority`
   - Sécurité FORTE (moderne)
   - SSH -X configure automatiquement

8. **SSH X11 Forwarding** :
   - Serveur : `X11Forwarding yes`
   - Client : `ssh -X user@server`
   - DISPLAY : `localhost:10.0`
   - Tunnel chiffré automatique

9. **Fichiers logs** :
   - `~/.xsession-errors` : Erreurs session utilisateur
   - `/var/log/Xorg.0.log` : Logs serveur X
   - Debugging avec `grep -E "\(EE\)|\(WW\)"`

10. **Commandes essentielles** :
    - `xrandr` : Gestion écrans/résolutions
    - `xeyes` : Test affichage
    - `glxinfo` : Infos OpenGL
    - `Xorg -configure` : Générer config
    - `startx` : Démarrer X manuellement

### 📚 Commandes à Connaître par Cœur

```bash
# Configuration
Xorg -configure
xrandr
setxkbmap

# Contrôle accès
xhost
xauth list
xauth add

# Tests
xeyes
glxinfo
glxgears

# Affichage distant
ssh -X user@server
export DISPLAY=:0

# Logs
~/.xsession-errors
/var/log/Xorg.0.log
```

### ⚠️ Pièges d'Examen Fréquents

1. **xhost + vs xauth** :
   - xhost + = Désactive contrôle (DANGER)
   - xauth = Tokens sécurisés (RECOMMANDÉ)

2. **DISPLAY format** :
   - `:0` = Local display 0
   - `server:0` = Distant
   - `localhost:10` = SSH forwarding

3. **Driver vesa** :
   - = Fallback universel
   - = Low resolution (1024x768 max souvent)
   - = Pas d'accélération 3D
   - Usage : Dépannage seulement

4. **xorg.conf vs xorg.conf.d/** :
   - xorg.conf = Monolithique (prioritaire si existe)
   - xorg.conf.d/ = Modulaire (moderne, préféré)

5. **X11 vs Wayland session** :
   - Vérifier : `echo $XDG_SESSION_TYPE`
   - GDM : Sélection au login (engrenage ⚙️ )
   - Wayland = Défaut moderne
   - X11 = Fallback si problèmes

6. **SSH X11 forwarding requis** :
   - Serveur : `X11Forwarding yes` dans sshd_config
   - Client : `ssh -X` (ou ForwardX11 yes)
   - Package : xauth installé sur serveur

7. **~/.Xauthority permissions** :
   - Doit être 600 (user seulement)
   - Propriétaire user (pas root)
   - ❌ Ne jamais `sudo startx`

8. **Section xorg.conf ordre** :
   - Device = Carte graphique
   - Monitor = Écran
   - Screen = Device + Monitor
   - ServerLayout = Agencement global

9. **Logs debug X11** :
   - `~/.xsession-errors` = Apps session
   - `/var/log/Xorg.0.log` = Serveur X
   - Chercher (EE) = Erreur, (WW) = Warning

10. **DE vs WM distinction** :
    - DE = Complet (WM + apps + libs)
    - WM = Fenêtres seulement (léger)
    - Exemples DE : GNOME, KDE, XFCE
    - Exemples WM : i3, Openbox, dwm

### 🔑 Valeurs à Mémoriser

**Chemins fichiers** :
```
/etc/X11/xorg.conf
/etc/X11/xorg.conf.d/*.conf
~/.Xauthority
~/.xsession-errors
/var/log/Xorg.0.log
```

**Ports X11** :
```
Display :0  → Port 6000
Display :1  → Port 6001
SSH forward → Port 6010+ (offset 10)
```

**Drivers graphiques** :
```
intel     Intel
nvidia    Nvidia propriétaire
nouveau   Nvidia open-source
amdgpu    AMD récent
radeon    AMD ancien
vesa      Fallback universel
```

**Display Managers** :
```
gdm       GNOME Display Manager
sddm      KDE Display Manager
lightdm   Lightweight DM
```

**Desktop Environments** :
```
GNOME     GTK+, moderne
KDE       Qt, personnalisable
XFCE      GTK+, léger
```

---

## 📖 Résumé Final

Ce cours couvre le **système graphique Linux et configuration X11** pour LPIC-1 102 :

1. **Architecture graphique** :
   - Couches : Hardware → Kernel → Display Server → DE/WM → Apps
   - Display Server : X11 (ancien) ou Wayland (moderne)
   - DE (complet) vs WM (léger)

2. **X11** :
   - XFree86 → X.org → X11 (version 11)
   - Complexe, déprécié, mais fallback universel
   - Network transparent natif

3. **Wayland** :
   - Moderne, sécurisé, performant
   - Moins de couches, direct rendering
   - XWayland pour compatibilité

4. **Configuration** :
   - `/etc/X11/xorg.conf` (monolithique)
   - `/etc/X11/xorg.conf.d/*.conf` (modulaire moderne)
   - Sections : Files, Module, InputDevice, Device, Monitor, Screen, ServerLayout
   - Génération : `Xorg -configure`

5. **Drivers** :
   - intel, nvidia, nouveau, amdgpu, radeon
   - **vesa** : Fallback low-res (dépannage)

6. **DISPLAY** :
   - `:0` local, `server:0` distant
   - SSH forwarding : `localhost:10.0`

7. **Contrôle accès** :
   - **xhost** : Par IP/hostname (faible sécurité)
   - **xauth** : Par tokens (forte sécurité)
   - SSH -X : xauth automatique ✅

8. **Logs** :
   - `~/.xsession-errors` : Session utilisateur
   - `/var/log/Xorg.0.log` : Serveur X

9. **Commandes** :
   - xrandr, xhost, xauth, xeyes
   - glxinfo, startx, Xorg -configure

10. **Meilleures pratiques** :
    - Wayland par défaut (X11 fallback)
    - SSH -X pour affichage distant
    - xauth (pas xhost +)
    - Config modulaire xorg.conf.d/

La compréhension du système graphique Linux est essentielle pour l'administration et le dépannage GUI.

---

*Documentation créée le 10 février 2026 - Formation LPIC-1 102 - Objectif 106.1*
