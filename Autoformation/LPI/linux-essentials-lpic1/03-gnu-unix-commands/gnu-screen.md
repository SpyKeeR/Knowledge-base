# 🖥️ GNU Screen : Multiplexeur Terminal

**Module** : GNU and Unix Commands  
**Cours** : GNU Screen  
**Objectif** : Maîtriser multiplexage terminal avec Screen

---

## 📺 Screen : Concepts

### Qu'est-ce que Screen ?

**GNU Screen** = **Multiplexeur terminal** (plusieurs terminaux virtuels dans une session)

**Analogie** 🏢 :
- **Session Screen** = Bureau avec plusieurs écrans
- **Windows** = Applications différentes (chaque écran)
- **Regions** = Split-screen (plusieurs apps visibles simultanément)
- **Détach/Attach** = Quitter bureau, revenir plus tard (tout reste actif)

---

### Hiérarchie Screen

```
Session Screen (exemple: 12345.my-session)
│
├─ Window 0: bash
├─ Window 1: vim document.txt
├─ Window 2: tail -f logs
│  ├─ Region 1 (haut): vim
│  └─ Region 2 (bas): logs
└─ Window 3: htop
```

---

### Pourquoi Utiliser Screen ?

**Cas d'usage** :

✅ **Persistance** : Processus continuent si connexion SSH coupée  
✅ **Multitâches** : Plusieurs terminaux dans une session  
✅ **Organisation** : Splits écran (horizontal/vertical)  
✅ **Mobilité** : Détacher session, réattacher ailleurs  
✅ **Backup** : Sessions survivent déconnexion réseau  

**Exemples** :
- Compilation longue (reste active si SSH déco)
- Surveillance logs + édition code simultané
- Administration serveur distant (sécurité déconnexion)

---

## 🚀 Démarrage Screen

### Première Utilisation

```bash
# Lancer Screen (première fois)
screen

# MOTD version affiché :
# Screen version 4.08.00 (GNU) 05-Feb-20
# 
# Copyright (c) 2018-2020 Alexander Naumov, Amadeusz Slawinski
# ...
# [Press Space or Return to end.]

# Appuyer ESPACE ou ENTRÉE
# → Terminal Screen actif
```

**Indicateur** : Aucun visuel par défaut (mais dans session Screen)

---

### Vérifier dans Screen

```bash
# Variable d'environnement
echo $STY
# 12345.pts-0.hostname  ← Dans Screen
# (vide)                 ← PAS dans Screen

# Ou
echo $TERM
# screen  ← Dans Screen
```

---

## ⌨️ Raccourcis Clavier : Préfixe Ctrl+A

### Préfixe Commande

**TOUS** raccourcis Screen commencent par **Ctrl+A**

**Syntaxe** : `Ctrl+A` puis `touche`

**Exemples** :
- `Ctrl+A` puis `c` → Créer window
- `Ctrl+A` puis `n` → Next window
- `Ctrl+A` puis `d` → Détacher session

**⚠️ Ne PAS maintenir Ctrl** : Relâcher après A

---

### Aide Intégrée

```bash
# Afficher aide (dans Screen)
Ctrl+A ?

# Liste TOUS raccourcis disponibles
# Navigation : Espace (page suivante), Entrée (quitter)
```

---

## 🪟 Gestion Windows

### Créer Window

```bash
# Créer nouvelle window
Ctrl+A c

# Window créée avec :
# - Numéro auto (0, 1, 2...)
# - Nom défaut (bash, zsh...)
# - Shell nouveau
```

---

### Lister Windows

```bash
# Afficher liste windows (bas écran)
Ctrl+A w

# Affichage :
# 0$ bash  1*$ vim  2$ htop
# 
# Légende :
# - 0, 1, 2 : Numéros windows
# - * : Window ACTIVE (visible)
# - $ : Aucun process actif (shell idle)
# - & : Process actif (vim, tail -f...)
```

---

### Naviguer Windows

```bash
# Window suivante (next)
Ctrl+A n

# Window précédente (previous)
Ctrl+A p

# Window spécifique (numéro 0-9)
Ctrl+A 0  # Window 0
Ctrl+A 1  # Window 1
Ctrl+A 5  # Window 5

# Menu sélection interactif
Ctrl+A "
# ↑↓ : Naviguer
# Entrée : Sélectionner
# Esc : Annuler
```

---

### Renommer Window

```bash
# Renommer window actuelle
Ctrl+A A

# Prompt bas écran :
# Set window's title to: [bash]
# 
# Taper nouveau nom
# vim-config
# 
# Entrée pour valider

# Vérifier
Ctrl+A w
# 0$ bash  1$ vim-config  2$ htop
```

---

### Créer Window avec Nom (Ligne Commande)

```bash
# Depuis HORS Screen : Lancer Screen avec windows nommées
screen -t "logs" tail -f /var/log/syslog

# Depuis DANS Screen : Créer window nommée
# Impossible direct Ctrl+A
# Utiliser : Créer puis renommer (Ctrl+A c, puis Ctrl+A A)
```

---

### Fermer Window

```bash
# Fermer window actuelle
Ctrl+A k

# Confirmation :
# Really kill this window [y/n]
# 
# y : Fermer
# n : Annuler

# Alternative : exit (ferme proprement)
exit  # Dans window, ferme sans confirmation
```

---

## 🔲 Gestion Régions (Splits)

### Concepts Régions

**Region** = Split écran (afficher **plusieurs windows simultanément**)

**Analogie** 📱 :
- **Window** = Application (une à la fois)
- **Region** = Split-screen (2+ apps visibles)

---

### Split Horizontal

```bash
# Diviser écran HORIZONTALEMENT (haut/bas)
Ctrl+A S

# Résultat :
# ┌──────────────────┐
# │   Region 1       │  ← Window actuelle
# ├──────────────────┤
# │   Region 2       │  ← Vide (noir)
# └──────────────────┘
```

**⚠️ Important** : Nouvelle région **vide** (faut afficher window)

---

### Split Vertical

```bash
# Diviser écran VERTICALEMENT (gauche/droite)
Ctrl+A |

# Résultat :
# ┌─────────┬─────────┐
# │ Region  │ Region  │
# │   1     │   2     │
# │         │ (vide)  │
# └─────────┴─────────┘
```

---

### Naviguer Régions

```bash
# Région suivante
Ctrl+A Tab

# Cycle : Region 1 → Region 2 → Region 3 → Region 1...
```

---

### Afficher Window dans Région

```bash
# 1. Créer split
Ctrl+A S

# 2. Aller région vide
Ctrl+A Tab

# 3. Afficher window (par exemple window 1)
Ctrl+A 1

# OU : Créer nouvelle window dans région
Ctrl+A c
```

---

### Fermer Régions

```bash
# Fermer TOUTES régions sauf actuelle
Ctrl+A Q

# Résultat : Retour plein écran window actuelle

# Fermer région ACTUELLE seulement
Ctrl+A X

# Région disparaît, autres régions agrandies
```

---

### Exemple Split Complet

```bash
# Configuration : 3 régions (top/logs/htop)

# 1. Créer windows
Ctrl+A c  # Window 0: bash
Ctrl+A c  # Window 1: (vim)
Ctrl+A c  # Window 2: (logs)

# 2. Window 1: Éditer fichier
vim /etc/nginx/nginx.conf

# 3. Créer split horizontal
Ctrl+A S

# 4. Aller région bas
Ctrl+A Tab

# 5. Afficher logs (window 2)
Ctrl+A 2
tail -f /var/log/nginx/access.log

# 6. Split vertical région bas
Ctrl+A |

# 7. Aller région droite
Ctrl+A Tab

# 8. Créer window htop
Ctrl+A c
htop

# Résultat :
# ┌─────────────────────────────┐
# │  vim nginx.conf             │
# ├──────────────┬──────────────┤
# │ tail logs    │ htop         │
# └──────────────┴──────────────┘
```

---

## 📋 Copier/Coller

### Mode Scroll (Copy Mode)

**Rôle** : Défiler historique + copier texte

```bash
# Entrer mode scroll
Ctrl+A [

# Indicateur bas écran :
# Copy mode - Column 0 Line 0

# Navigation :
# ↑↓←→ : Déplacer curseur
# PgUp/PgDown : Page haut/bas
# g : Début buffer
# G : Fin buffer
# / : Recherche (comme vim)
```

---

### Copier Texte

```bash
# 1. Entrer mode scroll
Ctrl+A [

# 2. Déplacer au DÉBUT texte à copier
# (↑↓←→)

# 3. Marquer DÉBUT copie
Espace

# Indicateur :
# Copy mode - Column 5 Line 10 (+1 bytes)

# 4. Déplacer à FIN texte
# (↑↓←→)

# 5. Marquer FIN copie
Espace

# Texte copié dans buffer Screen
# Mode scroll quitté automatiquement
```

---

### Coller Texte

```bash
# Coller buffer Screen
Ctrl+A ]

# Texte collé à curseur actuel
```

---

### Exemple Copier/Coller

```bash
# Window 0 : Logs avec erreur
tail /var/log/syslog
# ...
# Feb  2 12:30:45 server error: Connection refused
# ...

# Copier ligne erreur
Ctrl+A [           # Mode scroll
↑↑↑                # Remonter à ligne
Espace             # Marquer début
→→→ (fin ligne)    # Sélectionner
Espace             # Marquer fin

# Window 1 : Rapport
Ctrl+A c
vim rapport.txt

# Coller erreur
i                  # Mode insertion vim
Ctrl+A ]           # Coller
# Feb  2 12:30:45 server error: Connection refused
```

---

## 🔌 Détacher/Réattacher Sessions

### Détacher Session

**Détacher** = Quitter Screen **SANS** tuer processus

```bash
# Détacher session actuelle
Ctrl+A d

# Message :
# [detached from 12345.pts-0.hostname]

# Retour shell HORS Screen
# Processus Screen continuent en arrière-plan
```

**Usage** :
- Quitter SSH (processus continuent)
- Changer terminal (réattacher ailleurs)
- Pause travail (revenir plus tard)

---

### Lister Sessions

```bash
# Lister sessions Screen existantes
screen -ls

# Sortie :
# There are screens on:
#         12345.pts-0.hostname    (Detached)
#         67890.my-session        (Attached)
# 2 Sockets in /run/screen/S-alice.
```

**États** :
- **Detached** : Session détachée (disponible réattacher)
- **Attached** : Session attachée (utilisée actuellement)

---

### Réattacher Session

```bash
# Réattacher session (une seule existante)
screen -r

# Réattacher session spécifique (plusieurs sessions)
screen -r 12345

# OU : Par nom
screen -r my-session
```

---

### Créer Session Nommée

```bash
# Créer session avec nom custom
screen -S "dev-project"

# Lister
screen -ls
# There is a screen on:
#         23456.dev-project       (Attached)

# Détacher
Ctrl+A d

# Réattacher par nom
screen -r dev-project
```

---

## 🔧 Commandes screen (Ligne Commande)

### Options Création Session

```bash
# Session normale
screen

# Session nommée
screen -S "nom-session"

# Session avec window nommée
screen -t "logs" tail -f /var/log/syslog

# Session détachée dès départ
screen -d -m

# Session détachée avec commande
screen -d -m -S "backup" /scripts/backup.sh
```

---

### Options Réattacher

| Commande | Comportement |
|----------|-------------|
| `screen -r` | Réattacher (erreur si multiple ou attached) |
| `screen -r ID` | Réattacher session spécifique |
| `screen -d -r ID` | **Détacher** puis réattacher (si déjà attached) |
| `screen -d -R ID` | Comme `-d -r` mais **crée** si inexistante |
| `screen -d -RR` | Réattacher **première** session disponible |
| `screen -D -r ID` | **Force détacher** (autre user) puis réattacher |
| `screen -D -R ID` | **Multi-display**: Attaché OU créer + notifier |
| `screen -D -RR` | Comme `-D -R` mais première session |

---

### Explication Options Détaillées

#### -d -r : Détacher puis Réattacher

```bash
# Session déjà attachée (autre terminal)
screen -ls
# 12345.my-session        (Attached)

# Réattacher ici (détache automatiquement autre terminal)
screen -d -r 12345

# Autre terminal affiche :
# [remote detached from 12345.my-session]
```

**Usage** : Basculer session entre terminaux

---

#### -d -R : Détacher/Réattacher OU Créer

```bash
# Si session existe : Réattache (détache si besoin)
screen -d -R my-session

# Si session inexistante : CRÉE nouvelle session "my-session"
```

**Usage** : Script automatique (assure session existe)

---

#### -d -RR : Première Session Disponible

```bash
# Plusieurs sessions
screen -ls
# 12345.session-1         (Detached)
# 67890.session-2         (Detached)

# Réattacher PREMIÈRE (12345)
screen -d -RR
```

**Usage** : Peu importe quelle session (juste réattacher)

---

#### -D -r : Force Détacher Multi-User

```bash
# Session attachée par autre user
sudo screen -ls
# 12345.root-session      (Attached) (root)

# User normal tente réattacher
screen -D -r 12345
# Permission granted (force détach root)

# Root voit :
# [remote detached from 12345.root-session]
```

**⚠️ Attention** : Peut perturber autre utilisateur

---

### Options Gestion Sessions

```bash
# Lister sessions
screen -ls

# Tuer session spécifique (depuis HORS Screen)
screen -S 12345 -X quit

# OU
screen -S my-session -X quit

# Tuer TOUTES sessions
pkill screen  # ⚠️ Brutal

# Envoyer commande à session
screen -S my-session -X stuff "ls -la\n"
# Exécute "ls -la" dans session
```

---

### Options Démarrage Détaché

#### -d -m : Démarrer Détaché

```bash
# Démarrer session en background
screen -d -m -S "backup" /scripts/backup.sh

# Vérifier
screen -ls
# 12345.backup            (Detached)

# Session tourne, peut réattacher plus tard
screen -r backup
```

**Usage** : Scripts automatiques, cron jobs

---

#### -D -m : Démarrer Sans Fork

```bash
# Démarrer détaché SANS créer nouveau process
screen -D -m

# Différence vs -d -m :
# -d -m : Fork nouveau process (parent continue)
# -D -m : Pas fork (process actuel devient Screen)
```

**Usage** : Cas avancés (scripts systemd, docker)

---

## ⚙️ Configuration Screen

### Fichiers Configuration

**2 niveaux** :

1. **Global** : `/etc/screenrc`  
   → Configuration système (tous users)

2. **User** : `~/.screenrc`  
   → Configuration personnelle (prioritaire)

---

### Structure screenrc

```bash
# Sections principales :

# 1. SCREEN SETTINGS
# Configuration générale Screen

# 2. SCREEN KEYBINDINGS
# Raccourcis personnalisés

# 3. TERMINAL SETTINGS
# Configuration terminal

# 4. STARTUP SCREENS
# Windows créées au démarrage
```

---

### Exemples Configuration

#### ~/.screenrc Basique

```bash
# ~/.screenrc

# ===== SETTINGS =====

# Désactiver message démarrage
startup_message off

# Scrollback buffer (lignes historique)
defscrollback 10000

# Encoding UTF-8
defutf8 on

# Visual bell (pas beep sonore)
vbell on
vbell_msg "   Wuff  ----  Wuff!!  "

# ===== KEYBINDINGS =====

# Échapper Ctrl+A (envoyer à application)
# Ctrl+A A → Envoie Ctrl+A réel (utile bash/emacs)

# Split vertical (| difficile taper)
# Raccourci alternatif Ctrl+A v
bind v split -v

# Reload config
bind r source ~/.screenrc

# ===== TERMINAL =====

# Terminal type
term screen-256color

# Hardstatus line (barre bas)
hardstatus alwayslastline
hardstatus string '%{= kG}[ %{G}%H %{g}][%= %{= kw}%?%-Lw%?%{r}(%{W}%n*%f%t%?(%u)%?%{r})%{w}%?%+Lw%?%?%= %{g}][%{B} %m-%d %{W}%c %{g}]'

# ===== STARTUP SCREENS =====

# Window 0 : bash
screen -t "bash" 0

# Window 1 : logs
screen -t "logs" 1 tail -f /var/log/syslog

# Window 2 : monitoring
screen -t "htop" 2 htop
```

---

### Options Configuration Utiles

```bash
# Message démarrage
startup_message off           # Désactiver
startup_message on            # Activer (défaut)

# Scrollback (historique lignes)
defscrollback 10000           # 10000 lignes

# Échappement Ctrl+A
escape ^Aa                    # Défaut Ctrl+A, puis a
escape ^Bb                    # Changer Ctrl+B (comme tmux)

# Visual bell
vbell on                      # Activer
vbell_msg "BELL!"             # Message flash

# Hardstatus (barre info)
hardstatus alwayslastline     # Toujours afficher bas
hardstatus string "..."       # Format personnalisé

# Encoding
defutf8 on                    # UTF-8 (recommandé)

# Logging
logfile /tmp/screen-%t.log    # Fichiers logs
deflog on                     # Log automatique

# Monitor activité window
defmonitor on                 # Alerter si activité window background
```

---

### Keybindings Personnalisés

```bash
# Syntaxe : bind [touche] [commande]

# Split vertical facile
bind v split -v

# Reload config
bind r source ~/.screenrc

# Tuer window sans confirmation
bind k kill

# Créer window avec prompt nom
bind c screen

# Lockscreen (verrouiller)
bind x lockscreen

# Titre window custom
bind t title

# Naviguer windows avec F-keys
bind -k F1 select 0
bind -k F2 select 1
bind -k F3 select 2
bind -k F4 select 3
```

---

### Hardstatus Line (Barre Info)

**Hardstatus** = Barre information bas écran (windows, date, load...)

```bash
# Simple : Liste windows
hardstatus alwayslastline
hardstatus string '%{= kw} %-w%{= KW} %n %t %{-}%+w %= %c'

# Résultat :
# 0 bash  1 vim  2* logs  3 htop           12:34
```

**Codes format** :

| Code | Description |
|------|-------------|
| `%H` | Hostname |
| `%n` | Numéro window |
| `%t` | Titre window |
| `%c` | Heure (HH:MM) |
| `%d` | Date (DD/MM) |
| `%w` | Liste windows |
| `%-w` | Windows avant actuelle |
| `%+w` | Windows après actuelle |
| `%=` | Centrer/aligner droite |

**Couleurs** :

| Code | Couleur |
|------|---------|
| `%{k}` | Black |
| `%{r}` | Red |
| `%{g}` | Green |
| `%{y}` | Yellow |
| `%{b}` | Blue |
| `%{m}` | Magenta |
| `%{c}` | Cyan |
| `%{w}` | White |
| `%{K}` | Bright black |
| `%{= kw}` | Black bg, white fg |

---

## 📋 Antisèche - GNU Screen

### Raccourcis Essentiels

| Raccourci | Action |
|-----------|--------|
| `Ctrl+A ?` | **Aide** (liste raccourcis) |
| `Ctrl+A d` | **Détacher** session |
| `Ctrl+A c` | **Créer** window |
| `Ctrl+A k` | **Tuer** window (avec confirmation) |
| `Ctrl+A A` | **Renommer** window |
| `Ctrl+A w` | **Lister** windows (bas écran) |
| `Ctrl+A "` | **Menu** sélection window |
| `Ctrl+A n` | Window **suivante** (next) |
| `Ctrl+A p` | Window **précédente** (previous) |
| `Ctrl+A 0-9` | Aller window **numéro** |

---

### Raccourcis Régions (Splits)

| Raccourci | Action |
|-----------|--------|
| `Ctrl+A S` | Split **horizontal** |
| `Ctrl+A \|` | Split **vertical** |
| `Ctrl+A Tab` | Région **suivante** |
| `Ctrl+A Q` | Fermer **toutes** régions (sauf actuelle) |
| `Ctrl+A X` | Fermer région **actuelle** |

---

### Raccourcis Copier/Coller

| Raccourci | Action |
|-----------|--------|
| `Ctrl+A [` | Mode **scroll** (copie) |
| `Espace` | Marquer **début** copie (puis FIN) |
| `Ctrl+A ]` | **Coller** buffer |

---

### Commandes Ligne

| Commande | Action |
|----------|--------|
| `screen` | Démarrer Screen |
| `screen -S nom` | Session **nommée** |
| `screen -ls` | **Lister** sessions |
| `screen -r` | **Réattacher** session |
| `screen -r ID` | Réattacher session **spécifique** |
| `screen -d -r ID` | **Détacher** puis réattacher |
| `screen -d -R ID` | Détacher/réattacher OU **créer** |
| `screen -d -RR` | Réattacher **première** session |
| `screen -S ID -X quit` | **Tuer** session |
| `screen -d -m cmd` | Démarrer **détaché** |

---

## 💡 Scénarios Pratiques

### Scénario 1 : Compilation Longue SSH

```bash
# Se connecter serveur distant
ssh user@build-server

# Démarrer Screen (sécurité déconnexion)
screen -S "build-project"

# Compiler (3 heures)
cd /project
./configure
make -j8

# Détacher (peut fermer SSH)
Ctrl+A d

# --- Plus tard ou autre machine ---

# Réattacher voir progression
ssh user@build-server
screen -r build-project

# Compilation visible, continuer même si SSH coupé
```

---

### Scénario 2 : Monitoring Multi-Écrans

```bash
# Démarrer Screen
screen -S "monitoring"

# Window 0 : Logs système
Ctrl+A c
Ctrl+A A  # Renommer
logs-system
tail -f /var/log/syslog

# Window 1 : Logs application
Ctrl+A c
Ctrl+A A
logs-app
tail -f /var/log/app/error.log

# Window 2 : Monitoring ressources
Ctrl+A c
Ctrl+A A
htop
htop

# Split écran : Logs + htop simultané
Ctrl+A 0   # Aller window 0 (logs-system)
Ctrl+A S   # Split horizontal
Ctrl+A Tab # Région bas
Ctrl+A 2   # Afficher htop

# Résultat :
# ┌─────────────────────┐
# │ tail -f syslog      │
# ├─────────────────────┤
# │ htop                │
# └─────────────────────┘

# Naviguer windows : Ctrl+A n/p
# Naviguer régions : Ctrl+A Tab
```

---

### Scénario 3 : Session Persistante Développement

```bash
# Créer session dev projet
screen -S "webapp-dev"

# ~/.screenrc custom (créer windows auto)
cat > ~/.screenrc <<'EOF'
startup_message off
defscrollback 10000

# Window 0 : Éditeur
screen -t "vim" 0 vim

# Window 1 : Server dev
screen -t "server" 1 npm run dev

# Window 2 : Logs
screen -t "logs" 2 tail -f /var/log/app.log

# Window 3 : Shell
screen -t "shell" 3

# Hardstatus
hardstatus alwayslastline
hardstatus string '%{= kw}%-w%{= KW}%n %t%{-}%+w %= %c'
EOF

# Lancer (windows créées auto)
screen

# Workflow :
# - Window 0 : Coder (vim)
# - Window 1 : Voir server tourner
# - Window 2 : Surveiller logs
# - Window 3 : Commandes git/tests

# Fin journée : Détacher
Ctrl+A d

# Lendemain : Réattacher (tout comme laissé)
screen -r webapp-dev
```

---

### Scénario 4 : Copier Erreur Logs → Email

```bash
# Window 0 : Logs avec erreur
tail -f /var/log/app/error.log
# [ERROR] Database connection failed: timeout after 30s

# Copier erreur
Ctrl+A [           # Mode scroll
↑↑↑                # Remonter à ligne erreur
Espace             # Marquer début
Shift+→ (fin)      # Sélectionner ligne
Espace             # Marquer fin

# Window 1 : Composer email
Ctrl+A c
vim incident-report.txt

# Coller erreur
i                  # Insert mode
Ctrl+A ]           # Coller
Esc :wq            # Sauvegarder

# Envoyer email
mail -s "Error Report" admin@example.com < incident-report.txt
```

---

### Scénario 5 : Multi-Server Administration

```bash
# Screen avec connexions multiples serveurs

# Window 0 : Server 1 (web)
screen -S "admin-servers"
ssh web-01
Ctrl+A A  # Renommer
web-01

# Window 1 : Server 2 (db)
Ctrl+A c
ssh db-01
Ctrl+A A
db-01

# Window 2 : Server 3 (cache)
Ctrl+A c
ssh cache-01
Ctrl+A A
cache-01

# Window 3 : Local (commands)
Ctrl+A c
Ctrl+A A
local

# Lister windows
Ctrl+A w
# 0$ web-01  1$ db-01  2$ cache-01  3*$ local

# Basculer rapidement
Ctrl+A 0  # web-01
Ctrl+A 1  # db-01

# Split pour voir 2 serveurs simultanément
Ctrl+A 0   # web-01
Ctrl+A S   # Split
Ctrl+A Tab
Ctrl+A 1   # db-01 région bas
```

---

### Scénario 6 : Background Job Détaché

```bash
# Lancer backup long en Screen détaché
screen -d -m -S "backup-nightly" /scripts/backup-database.sh

# Vérifier démarré
screen -ls
# 12345.backup-nightly    (Detached)

# Continuer travailler (backup tourne background)

# Vérifier progression plus tard
screen -r backup-nightly

# Voir output backup
# Détacher à nouveau si besoin
Ctrl+A d
```

---

### Scénario 7 : Partage Session (Pair Programming)

```bash
# User 1 : Créer session partagée
screen -S "pair-coding"

# User 2 : Réattacher MÊME session (multi-attach)
screen -x pair-coding

# Les DEUX voient même écran en temps réel
# User 1 tape → User 2 voit immédiatement
# User 2 tape → User 1 voit immédiatement

# Usage :
# - Pair programming
# - Debug avec collègue
# - Formation/démonstration

# ⚠️ -x (multi-attach) vs -r (single)
# -x : Plusieurs users simultanés
# -r : Détache autres (un seul user)
```

---

### Scénario 8 : Logging Session Automatique

```bash
# ~/.screenrc : Logging automatique
cat > ~/.screenrc <<'EOF'
# Activer logs
deflog on
logfile /tmp/screen-%t-%Y%m%d.log
EOF

# Lancer Screen
screen

# TOUTES windows loggées automatiquement

# Fichiers créés :
# /tmp/screen-bash-20260202.log
# /tmp/screen-vim-20260202.log

# Vérifier
ls /tmp/screen-*.log

# Utile pour :
# - Audit commandes
# - Reproduction erreurs
# - Formation (rejouer session)
```

---

### Scénario 9 : Screen dans Cron Job

```bash
# Crontab : Backup quotidien dans Screen
crontab -e

# Ajouter :
0 2 * * * screen -d -m -S "backup-daily-$(date +\%Y\%m\%d)" /scripts/backup.sh

# Chaque nuit 2h :
# - Démarre Screen détaché
# - Nom session unique (date)
# - Lance backup

# Lendemain : Vérifier résultat
screen -ls
# 12345.backup-daily-20260202    (Detached)

screen -r backup-daily-20260202
# Voir logs backup

# Nettoyer vieilles sessions
for session in $(screen -ls | grep backup-daily | cut -d. -f1); do
    screen -S $session -X quit
done
```

---

### Scénario 10 : Workspace Automatique

```bash
# Script : Créer workspace dev complet
cat > ~/start-dev-workspace.sh <<'EOF'
#!/bin/bash

SESSION="dev-workspace"

# Tuer session si existe
screen -S $SESSION -X quit 2>/dev/null

# Créer session détachée
screen -d -m -S $SESSION

# Window 0 : Vim
screen -S $SESSION -X screen -t "vim" vim

# Window 1 : Server
screen -S $SESSION -X screen -t "server" bash -c "cd /project && npm start"

# Window 2 : Logs
screen -S $SESSION -X screen -t "logs" bash -c "tail -f /var/log/app.log"

# Window 3 : Git
screen -S $SESSION -X screen -t "git" bash

# Window 4 : Tests
screen -S $SESSION -X screen -t "tests" bash

# Réattacher
screen -r $SESSION
EOF

chmod +x ~/start-dev-workspace.sh

# Utiliser
~/start-dev-workspace.sh

# Workspace complet prêt en 1 commande !
```

---

## 🎓 Points Clés pour l'Examen

✅ **Screen** : Multiplexeur terminal (sessions persistantes)  
✅ **Préfixe** : Tous raccourcis commencent **Ctrl+A**  
✅ **Windows** : Terminaux virtuels (numérotés 0-9+)  
✅ **Regions** : Splits écran (horizontal S, vertical |)  
✅ **Détacher** : Ctrl+A d (session continue background)  
✅ **Réattacher** : screen -r (reprendre session)  
✅ **Navigation** : Ctrl+A n/p (next/previous window)  
✅ **Créer window** : Ctrl+A c  
✅ **Lister windows** : Ctrl+A w  
✅ **Copier** : Ctrl+A [ (mode scroll), Espace×2, Ctrl+A ] (coller)  
✅ **Sessions nommées** : screen -S "nom"  
✅ **Lister sessions** : screen -ls  
✅ **Options réattacher** : -r (normal), -d -r (détacher d'abord), -d -R (créer si besoin)  
✅ **Tuer session** : screen -S ID -X quit  
✅ **Config** : ~/.screenrc (settings, keybindings, startup windows)  
✅ **Démarrer détaché** : screen -d -m  

---

## ⚠️ Erreurs Courantes

### Erreur 1 : Oublier Relâcher Ctrl

```bash
# ❌ ERREUR : Maintenir Ctrl après A
Ctrl+A+c  # (Ctrl maintenu pour c)
# Peut ne pas fonctionner

# ✅ CORRECT : Relâcher Ctrl après A
Ctrl+A  (relâcher)  c
# Créer window fonctionne
```

---

### Erreur 2 : Région Vide Après Split

```bash
# Créer split
Ctrl+A S

# ❌ PROBLÈME : Région bas vide (noire)
# Oublier d'afficher window dedans

# ✅ SOLUTION :
Ctrl+A S       # Split
Ctrl+A Tab     # Aller région
Ctrl+A 1       # Afficher window 1
# OU
Ctrl+A c       # Créer nouvelle window
```

---

### Erreur 3 : Confusion Détacher vs Tuer

```bash
# ❌ ERREUR : Tuer au lieu détacher
exit  # Ferme window, pas session
# Si dernière window : Session meurt

# ✅ DÉTACHER (session survit)
Ctrl+A d

# ✅ TUER session (si vraiment voulu)
screen -S mysession -X quit
```

---

### Erreur 4 : Réattacher Session Déjà Attachée

```bash
# Session attachée (autre terminal)
screen -ls
# 12345.mysession         (Attached)

# Tenter réattacher
screen -r 12345
# There is a screen on:
#         12345.mysession (Attached)
# There is no screen to be resumed matching 12345.

# ✅ SOLUTION : Détacher d'abord
screen -d -r 12345
# Détache autre terminal, puis réattache ici
```

---

### Erreur 5 : Perdre Scrollback

```bash
# ❌ PROBLÈME : Historique limité
# Défaut scrollback 100 lignes (petit)

# Logs longs perdus
tail -f /var/log/syslog
# ... beaucoup de lignes ...
Ctrl+A [   # Mode scroll
↑↑↑        # Seulement 100 dernières lignes

# ✅ SOLUTION : Augmenter scrollback
echo "defscrollback 10000" >> ~/.screenrc

# Recharger
Ctrl+A : source ~/.screenrc
```

---

### Erreur 6 : Copier Entre Screens

```bash
# ❌ LIMITATION : Buffer Screen local
# Copier window 1 (session A)
Ctrl+A [
(copier texte)

# Nouvelle session Screen (session B)
screen -S autre

# Coller
Ctrl+A ]
# (vide) ← Buffer pas partagé entre sessions

# ✅ SOLUTION : Clipboard système
# Utiliser tmux (buffer partagé)
# OU : Copier via terminal (sélection souris)
```

---

### Erreur 7 : ~/.screenrc Syntaxe

```bash
# ❌ ERREUR : Syntaxe invalide
# ~/.screenrc
screen -t logs tail -f /var/log/syslog  # Manque guillemets

# Erreur démarrage :
# /root/.screenrc: screen: unknown command 'tail'

# ✅ CORRECT : Guillemets commandes
screen -t "logs" tail -f /var/log/syslog

# OU : Numéro window explicite
screen -t "logs" 1 tail -f /var/log/syslog
```

---

### Erreur 8 : Screen dans Screen

```bash
# ❌ CONFUSION : Screen imbriqué
# Déjà dans Screen
echo $STY
# 12345.session1

# Lancer nouveau Screen
screen

# Maintenant : Screen dans Screen
# Ctrl+A ambigu (lequel ?)

# ✅ ÉVITER : Pas Screen imbriqué
# Créer window au lieu nouvelle session
Ctrl+A c  # Nouvelle window (même session)

# Si vraiment besoin : Échapper Ctrl+A
Ctrl+A A  # Envoie Ctrl+A au Screen interne
```

---

### Erreur 9 : Oublier screen -ls

```bash
# ❌ PERTE : Sessions oubliées
# Déconnexion SSH, screens détachées

# Semaines plus tard : Screens zombies
screen -ls
# 12345.old-build         (Detached)  ← 3 semaines
# 67890.forgotten         (Detached)  ← 2 mois

# ✅ BONNE PRATIQUE : Nettoyer régulièrement
# Lister
screen -ls

# Tuer inutiles
screen -S 12345 -X quit
screen -S 67890 -X quit

# OU : Script nettoyage
for s in $(screen -ls | grep Detached | cut -d. -f1); do
    screen -S $s -X quit
done
```

---

### Erreur 10 : Pas Nommer Sessions

```bash
# ❌ CONFUSION : Sessions numéros
screen -ls
# 12345.pts-0.hostname    (Detached)
# 67890.pts-1.hostname    (Detached)
# 54321.pts-2.hostname    (Detached)
# Laquelle est laquelle ?

# ✅ CLARTÉ : Toujours nommer
screen -S "dev-webapp"
screen -S "backup-database"
screen -S "monitoring"

screen -ls
# 12345.dev-webapp        (Detached)
# 67890.backup-database   (Detached)
# 54321.monitoring        (Detached)
# Clair immédiatement !
```

---

## 🆚 Screen vs tmux

### Comparaison

| Critère | Screen | tmux |
|---------|--------|------|
| **Âge** | Ancien (1987) | Moderne (2007) |
| **Préfixe** | Ctrl+A | Ctrl+B |
| **Config** | ~/.screenrc | ~/.tmux.conf |
| **Popularité** | Déclin | Montée |
| **Split** | Basique | Avancé (resize facile) |
| **Plugins** | Peu | Nombreux |
| **Performance** | OK | Meilleure |
| **Learning** | Plus simple | Plus complexe |

---

### Quand Utiliser Screen ?

✅ **Screen** si :
- Serveurs anciens (Screen pré-installé souvent)
- Besoin simple (détach/reattach)
- Habitude Screen existante
- Pas admin (tmux pas installé)

---

### Quand Utiliser tmux ?

✅ **tmux** si :
- Projet nouveau (apprendre modern tool)
- Splits complexes (resize, layouts)
- Plugins (powerline, tmux-resurrect...)
- Performance critique

---

**🎯 Prochaine étape** : Perform Basic File Editing Operations Using vi/vim

*Dernière mise à jour: 2 février 2026*
