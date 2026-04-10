# 🔷 tmux : Terminal Multiplexer Moderne

**Module** : GNU and Unix Commands  
**Cours** : tmux  
**Objectif** : Maîtriser tmux (multiplexeur moderne client/serveur)

---

## 🎯 tmux : Concepts

### Qu'est-ce que tmux ?

**tmux** = **Terminal Multiplexer** (moderne, successeur Screen)

**Analogie** 🏗️ :
- **tmux Server** = Immeuble (gère tout)
- **Sessions** = Appartements (espaces travail isolés)
- **Windows** = Pièces (différentes tâches)
- **Panes** = Zones pièce (splits écran)
- **Client** = Vous (connectez/déconnectez)

---

### Architecture Client/Serveur

**Différence vs Screen** : tmux = **client/serveur**

```
┌─────────────────────────────────┐
│     tmux Server (background)    │
│  ┌────────────┬────────────┐    │
│  │ Session 1  │ Session 2  │    │
│  │  Window 0  │  Window 0  │    │
│  │  Window 1  │  Window 1  │    │
│  └────────────┴────────────┘    │
└─────────────┬───────────────────┘
              │
    ┌─────────┴─────────┐
    │                   │
┌───▼────┐         ┌────▼───┐
│Client 1│         │Client 2│
│Terminal│         │Terminal│
└────────┘         └────────┘
```

**Avantages** :
- ✅ Server unique (partage sessions entre clients)
- ✅ Détach/attach transparent
- ✅ Plusieurs clients même session simultanément
- ✅ Persistance automatique (server tourne background)

---

### Hiérarchie tmux

```
Server tmux
│
├─ Session 0: "dev"
│  ├─ Window 0: "vim"
│  │  ├─ Pane 0 (gauche): vim code.py
│  │  └─ Pane 1 (droite): terminal
│  ├─ Window 1: "logs"
│  └─ Window 2: "htop"
│
└─ Session 1: "admin"
   ├─ Window 0: "monitoring"
   └─ Window 1: "ssh"
```

**Terminologie** :
- **Server** : Process tmux background (un seul)
- **Session** : Ensemble windows (workspace)
- **Window** : Terminal virtuel (comme onglet navigateur)
- **Pane** : Split window (plusieurs terminaux visibles)

---

## 🚀 Démarrage tmux

### Première Session

```bash
# Lancer tmux (session anonyme)
tmux

# Barre état affichée (bas écran) :
# [0] 0:bash*                                    12:34 02-Feb-26
# 
# Légende :
# [0]        : Numéro session
# 0:bash*    : Window 0, nom "bash", active (*)
# 12:34      : Heure
# 02-Feb-26  : Date
```

---

### Session Nommée

```bash
# Session avec nom custom
tmux new -s "LPI"

# Barre :
# [LPI] 0:bash*                                  12:34 02-Feb-26

# Session + window nommée
tmux new -s "LPI" -n "window-zero"

# Barre :
# [LPI] 0:window-zero*                           12:34 02-Feb-26
```

**⚠️ Nom window auto-update** : Change selon dernière commande

```bash
# Window nommée "window-zero"
# Lancer vim
vim test.txt

# Barre après quelques secondes :
# [LPI] 0:vim*                                   12:34 02-Feb-26
# Nom changé automatiquement → "vim"

# Désactiver auto-rename (voir section config)
```

---

## ⌨️ Raccourcis Clavier : Préfixe Ctrl+B

### Préfixe Commande

**TOUS** raccourcis tmux commencent par **Ctrl+B**

**Syntaxe** : `Ctrl+B` puis `touche`

**Exemples** :
- `Ctrl+B` puis `c` → Créer window
- `Ctrl+B` puis `n` → Next window
- `Ctrl+B` puis `d` → Détacher session

**⚠️ Différent Screen** : Screen = Ctrl+A, tmux = Ctrl+B

---

### Aide Intégrée

```bash
# Afficher aide (dans tmux)
Ctrl+B ?

# Liste TOUS raccourcis
# q : Quitter aide
```

---

## 🪟 Gestion Windows

### Créer Window

```bash
# Créer nouvelle window
Ctrl+B c

# Window créée :
# - Numéro suivant (0, 1, 2...)
# - Nom = "bash" (ou shell par défaut)
# - Shell nouveau

# Barre après création :
# [LPI] 0:bash  1:bash*                          12:34 02-Feb-26
#               ↑ Window 1 active
```

---

### Renommer Window

```bash
# Renommer window actuelle
Ctrl+B ,

# Prompt barre état :
# (rename-window) bash█
# 
# Effacer nom actuel, taper nouveau
# monitoring
# 
# Entrée valider

# Barre :
# [LPI] 0:bash  1:monitoring*                    12:34 02-Feb-26
```

---

### Naviguer Windows

```bash
# Window suivante (next)
Ctrl+B n

# Window précédente (previous)
Ctrl+B p

# Window numéro spécifique
Ctrl+B 0  # Window 0
Ctrl+B 1  # Window 1
Ctrl+B 5  # Window 5

# Menu interactif sélection
Ctrl+B w
# Arborescence sessions/windows :
# (0)  - Session: LPI
# (0)    0: bash-
# (1)    1: monitoring*  "hostname"
# (2)    2: logs         "hostname"
# 
# ↑↓ : Naviguer
# Entrée : Sélectionner
# q : Quitter
```

---

### Trouver Window par Nom

```bash
# Recherche window
Ctrl+B f

# Prompt :
# (find-window) █
# 
# Taper partie nom
# mon
# 
# Résultats :
# 1: monitoring*

# Entrée : Aller window trouvée
```

---

### Changer Numéro Window

```bash
# Réorganiser windows (changer index)
Ctrl+B .

# Prompt :
# (move-window) 1█
# 
# Taper nouveau numéro
# 5
# 
# Entrée : Window 1 devient Window 5

# Barre avant :
# [LPI] 0:bash  1:monitoring*  2:logs

# Barre après :
# [LPI] 0:bash  2:logs  5:monitoring*
```

---

### Fermer Window

```bash
# Fermer window actuelle
Ctrl+B &

# Confirmation :
# kill-window 1:monitoring? (y/n)
# 
# y : Fermer
# n : Annuler

# Alternative : exit (ferme sans confirmation)
exit  # Ferme window proprement
```

---

## 🔲 Gestion Panes (Splits)

### Split Horizontal

```bash
# Diviser HORIZONTALEMENT (haut/bas)
Ctrl+B "

# Résultat :
# ┌──────────────────┐
# │   Pane 0         │  ← Pane actuel
# ├──────────────────┤
# │   Pane 1         │  ← Nouveau pane (actif)
# └──────────────────┘
```

---

### Split Vertical

```bash
# Diviser VERTICALEMENT (gauche/droite)
Ctrl+B %

# Résultat :
# ┌─────────┬─────────┐
# │ Pane 0  │ Pane 1  │
# │         │ (actif) │
# └─────────┴─────────┘
```

---

### Naviguer Panes

```bash
# Pane suivant (direction flèche)
Ctrl+B ↑  # Pane haut
Ctrl+B ↓  # Pane bas
Ctrl+B ←  # Pane gauche
Ctrl+B →  # Pane droite

# Dernier pane actif
Ctrl+B ;

# Cycle panes (ordre numérique)
Ctrl+B o
```

---

### Redimensionner Panes

```bash
# Redimensionner (1 ligne/colonne à la fois)
Ctrl+B Ctrl+↑  # Agrandir haut
Ctrl+B Ctrl+↓  # Agrandir bas
Ctrl+B Ctrl+←  # Agrandir gauche
Ctrl+B Ctrl+→  # Agrandir droite

# Redimensionner (5 lignes/colonnes)
Ctrl+B Alt+↑   # Agrandir haut ×5
Ctrl+B Alt+↓   # Agrandir bas ×5
Ctrl+B Alt+←   # Agrandir gauche ×5
Ctrl+B Alt+→   # Agrandir droite ×5
```

**⚠️ Maintenir Ctrl** après B pour Ctrl+flèches

---

### Déplacer Panes

```bash
# Déplacer pane vers position précédente
Ctrl+B {

# Déplacer pane vers position suivante
Ctrl+B }

# Exemple :
# Avant : [Pane 0] [Pane 1*] [Pane 2]
# Ctrl+B { (depuis Pane 1)
# Après : [Pane 1*] [Pane 0] [Pane 2]
```

---

### Zoom Pane

```bash
# Zoomer pane actuel (plein écran temporaire)
Ctrl+B z

# Pane remplit fenêtre
# Z affiché barre état (indicateur zoom)

# Re-zoomer : Ctrl+B z (toggle)
# Retour layout normal
```

**Usage** : Focus temporaire sur pane (édition, lecture)

---

### Transformer Pane → Window

```bash
# Convertir pane en window indépendante
Ctrl+B !

# Avant :
# Window 1 avec 3 panes

# Après :
# Window 1 avec 2 panes (pane transformé retiré)
# Window 2 créée avec ancien pane (maintenant seul)
```

---

### Fermer Pane

```bash
# Fermer pane actuel
Ctrl+B x

# Confirmation :
# kill-pane 1? (y/n)
# 
# y : Fermer
# n : Annuler

# Alternative : exit
exit  # Ferme pane proprement
```

---

### Horloge (Fun)

```bash
# Afficher horloge ASCII art (pane entier)
Ctrl+B t

# Horloge affichée :
#   ██  ██████      ██████  ██   ██
#  ███     ██            ██ ██   ██
#   ██    ██      ██     ██ ███████
#   ██   ██             ██      ██
# ████ ██████           ██      ██
# 
# q : Quitter horloge
```

---

## 🗂️ Gestion Sessions

### Créer Session

```bash
# Depuis HORS tmux : Nouvelle session
tmux new -s "admin"

# Depuis DANS tmux : Mode commande
Ctrl+B :
# Prompt :
# :█
# 
# Taper commande
:new -s "backup"
# 
# Entrée : Nouvelle session créée (switch automatique)
```

---

### Lister Sessions

```bash
# Menu sélection sessions (dans tmux)
Ctrl+B s

# Arborescence :
# (0)  + dev: 3 windows (attached)
# (1)  + admin: 2 windows
# (2)  + backup: 1 windows
# 
# ↑↓ : Naviguer
# Entrée : Switch session
# q : Annuler

# Développer session (voir windows)
# ↑↓ sur session, puis →
# (0)  - dev: 3 windows (attached)
# (0)    0: vim*
# (1)    1: logs
# (2)    2: htop
```

---

### Renommer Session

```bash
# Renommer session actuelle
Ctrl+B $

# Prompt :
# (rename-session) LPI█
# 
# Taper nouveau nom
# formation
# 
# Entrée

# Barre :
# [formation] 0:bash*                            12:34 02-Feb-26
```

---

### Naviguer Sessions

```bash
# Via menu
Ctrl+B s
# (voir section Lister Sessions)

# Session suivante
Ctrl+B )

# Session précédente
Ctrl+B (

# Dernière session
Ctrl+B L
```

---

### Détacher Session

```bash
# Détacher session actuelle
Ctrl+B d

# Message :
# [detached (from session formation)]

# Retour terminal HORS tmux
# Server tmux continue background

# Sélectionner client à détacher (si plusieurs)
Ctrl+B D

# Liste clients :
# 1: 0 [180x50 pts/0] (attached)
# 2: 0 [180x50 pts/1] (attached)
# 
# ↑↓ : Sélectionner
# Entrée : Détacher client sélectionné
```

---

## 📋 Copier/Coller

### Mode Scrollback/Copy

**Mode scrollback** = Défiler historique + copier texte

```bash
# Entrer mode scrollback
Ctrl+B [

# Indicateur coin haut-droit :
# [0/123]
# (ligne 0 sur 123 buffer)

# Navigation :
# ↑↓ : Ligne par ligne
# PgUp/PgDown : Page
# g : Début buffer
# G : Fin buffer
# / : Recherche avant
# ? : Recherche arrière
# n : Occurrence suivante
# N : Occurrence précédente

# q ou Esc : Quitter mode
```

---

### Copier Texte

```bash
# 1. Entrer mode scrollback
Ctrl+B [

# 2. Naviguer au DÉBUT texte
# (↑↓←→)

# 3. Marquer DÉBUT sélection
Espace

# Indicateur :
# [0/123] (visual)

# 4. Naviguer à FIN texte
# (↑↓←→)
# Texte sélectionné visuellement

# 5. Copier (marquer FIN)
Espace

# OU : Enter (copier + quitter mode)

# Texte copié dans buffer tmux
```

---

### Coller Texte

```bash
# Coller buffer tmux
Ctrl+B ]

# Texte collé à curseur

# ⚠️ Différent Screen : Screen = Ctrl+A ]
# tmux = Alt+W (selon config) OU Ctrl+B ]
```

**Note utilisateur** : "Alt+W pour coller"  
→ Dépend config (par défaut = Ctrl+B ])

---

### Mode vi Copy (Avancé)

**Par défaut** : Mode emacs (Espace début/fin)

**Alternative** : Mode vi (v début, y copier)

```bash
# Activer mode vi (dans tmux.conf)
set-window-option -g mode-keys vi

# Raccourcis mode vi :
# v : Visual mode (début sélection)
# y : Yank (copier)
# Esc : Quitter mode
```

---

## 🔧 Commandes tmux (Ligne Commande)

### Lister Sessions

```bash
# Lister toutes sessions
tmux ls

# Sortie :
# dev: 3 windows (created Sun Feb  2 10:30:00 2026) (attached)
# admin: 2 windows (created Sun Feb  2 11:00:00 2026)
# backup: 1 windows (created Sun Feb  2 11:30:00 2026)

# Alias
tmux list-sessions  # Équivalent tmux ls
```

---

### Réattacher Session

```bash
# Réattacher (une seule session)
tmux attach
tmux a      # Alias
tmux att    # Alias

# Réattacher session spécifique
tmux attach -t dev
tmux a -t admin

# Créer si inexistante
tmux attach -t mysession || tmux new -s mysession
```

---

### Tuer Session

```bash
# Tuer session spécifique
tmux kill-session -t "backup"

# Tuer TOUTES sessions (sauf actuelle si dans tmux)
tmux kill-server

# Tuer sessions sauf "dev"
tmux kill-session -a -t dev
# -a : All (toutes sauf)
```

---

### Autres Commandes Utiles

```bash
# Créer session détachée dès départ
tmux new -d -s "background-task"

# Envoyer commande à session
tmux send-keys -t mysession "ls -la" Enter

# Lister windows session
tmux list-windows -t dev

# Lister panes window
tmux list-panes -t dev:1

# Capturer contenu pane (vers fichier)
tmux capture-pane -t dev:0.0 -p > pane-output.txt
```

---

## ⚙️ Configuration tmux

### Fichiers Configuration

**2 niveaux** :

1. **Global** : `/etc/tmux.conf`  
   → Configuration système (tous users)

2. **User** : `~/.tmux.conf`  
   → Configuration personnelle (prioritaire)

---

### Recharger Configuration

```bash
# Dans tmux (mode commande)
Ctrl+B :
:source-file ~/.tmux.conf

# Ou : Ajouter raccourci dans config
# bind r source-file ~/.tmux.conf \; display "Config reloaded!"
# Puis : Ctrl+B r (recharge config)
```

---

### Exemples Configuration

#### ~/.tmux.conf Basique

```bash
# ~/.tmux.conf

# ===== SETTINGS =====

# Changer préfixe Ctrl+B → Ctrl+A (comme Screen)
unbind C-b
set-option -g prefix C-a
bind-key C-a send-prefix

# Numérotation windows/panes depuis 1 (pas 0)
set -g base-index 1
setw -g pane-base-index 1

# Historique scrollback
set -g history-limit 10000

# Mode souris (activer)
set -g mouse on

# Pas délai Esc (vim)
set -s escape-time 0

# Renumbering automatique windows (si fermeture)
set -g renumber-windows on

# ===== KEYBINDINGS =====

# Reload config
bind r source-file ~/.tmux.conf \; display "Config reloaded!"

# Split windows (plus intuitif)
bind | split-window -h  # Vertical (|)
bind - split-window -v  # Horizontal (-)

# Naviguer panes (vim-like)
bind h select-pane -L   # Gauche
bind j select-pane -D   # Bas
bind k select-pane -U   # Haut
bind l select-pane -R   # Droite

# Resize panes (vim-like)
bind -r H resize-pane -L 5
bind -r J resize-pane -D 5
bind -r K resize-pane -U 5
bind -r L resize-pane -R 5

# Mode vi copy
setw -g mode-keys vi
bind-key -T copy-mode-vi 'v' send -X begin-selection
bind-key -T copy-mode-vi 'y' send -X copy-selection-and-cancel

# ===== APPARENCE =====

# Couleurs 256
set -g default-terminal "screen-256color"

# Barre état (style)
set -g status-style bg=black,fg=white

# Window actuelle (surbrillance)
setw -g window-status-current-style bg=red,fg=white,bold

# Barre gauche
set -g status-left-length 40
set -g status-left "#[fg=green]Session: #S #[fg=yellow]#I #[fg=cyan]#P"

# Barre droite
set -g status-right "#[fg=cyan]%d %b %R"

# Centrer liste windows
set -g status-justify centre

# Désactiver auto-rename (garder noms custom)
setw -g automatic-rename off

# ===== AUTRES =====

# Activity monitoring
setw -g monitor-activity on
set -g visual-activity on
```

---

### Options Configuration Populaires

#### Général

```bash
# Historique
set -g history-limit 50000

# Souris
set -g mouse on              # Activer
set -g mouse off             # Désactiver

# Renumbering automatique
set -g renumber-windows on

# Index départ (1 au lieu 0)
set -g base-index 1
setw -g pane-base-index 1

# Délai Esc (vim users)
set -s escape-time 0
```

---

#### Keybindings

```bash
# Changer préfixe
unbind C-b
set -g prefix C-a

# Split intuitifs
bind | split-window -h
bind - split-window -v

# Reload config
bind r source-file ~/.tmux.conf

# Mode vi
setw -g mode-keys vi

# Synchronize panes (taper même chose tous panes)
bind S setw synchronize-panes
```

---

#### Apparence

```bash
# Couleurs
set -g default-terminal "screen-256color"

# Barre état
set -g status-bg black
set -g status-fg white

# Window actuelle
setw -g window-status-current-bg red
setw -g window-status-current-fg white

# Formats barre
set -g status-left "#S #I #P"
set -g status-right "%d %b %R"

# Centrer windows
set -g status-justify centre

# Position barre
set -g status-position top  # Haut écran
set -g status-position bottom  # Bas (défaut)
```

---

## 📋 Antisèche - tmux

### Raccourcis Sessions

| Raccourci | Action |
|-----------|--------|
| `Ctrl+B d` | **Détacher** session |
| `Ctrl+B D` | **Sélectionner** client détacher |
| `Ctrl+B s` | **Lister** sessions (menu) |
| `Ctrl+B $` | **Renommer** session |
| `Ctrl+B (` | Session **précédente** |
| `Ctrl+B )` | Session **suivante** |
| `Ctrl+B L` | **Dernière** session |

---

### Raccourcis Windows

| Raccourci | Action |
|-----------|--------|
| `Ctrl+B c` | **Créer** window |
| `Ctrl+B ,` | **Renommer** window |
| `Ctrl+B w` | **Lister** windows (menu) |
| `Ctrl+B n` | Window **suivante** (next) |
| `Ctrl+B p` | Window **précédente** (previous) |
| `Ctrl+B 0-9` | Aller window **numéro** |
| `Ctrl+B f` | **Trouver** window (recherche) |
| `Ctrl+B .` | Changer **index** window |
| `Ctrl+B &` | **Fermer** window |

---

### Raccourcis Panes

| Raccourci | Action |
|-----------|--------|
| `Ctrl+B "` | Split **horizontal** |
| `Ctrl+B %` | Split **vertical** |
| `Ctrl+B ↑↓←→` | **Naviguer** panes |
| `Ctrl+B ;` | **Dernier** pane actif |
| `Ctrl+B o` | **Cycle** panes |
| `Ctrl+B x` | **Fermer** pane |
| `Ctrl+B z` | **Zoom** pane (toggle) |
| `Ctrl+B !` | Pane → **window** |
| `Ctrl+B {` | **Déplacer** pane gauche |
| `Ctrl+B }` | **Déplacer** pane droite |
| `Ctrl+B Ctrl+↑↓←→` | **Resize** pane (1) |
| `Ctrl+B Alt+↑↓←→` | **Resize** pane (5) |
| `Ctrl+B t` | **Horloge** |

---

### Raccourcis Copier/Coller

| Raccourci | Action |
|-----------|--------|
| `Ctrl+B [` | Mode **scrollback** |
| `Espace` | Marquer **début/fin** sélection |
| `Ctrl+B ]` | **Coller** buffer |
| `q` ou `Esc` | **Quitter** mode scrollback |

---

### Commandes Ligne

| Commande | Action |
|----------|--------|
| `tmux` | Démarrer tmux |
| `tmux new -s nom` | Session **nommée** |
| `tmux ls` | **Lister** sessions |
| `tmux a` ou `tmux attach` | **Réattacher** session |
| `tmux a -t nom` | Réattacher session **spécifique** |
| `tmux kill-session -t nom` | **Tuer** session |
| `tmux kill-server` | **Tuer** toutes sessions |

---

### Mode Commande

| Commande | Action |
|----------|--------|
| `Ctrl+B :` | Mode **commande** |
| `:new -s nom` | Créer session |
| `:kill-session -t nom` | Tuer session |
| `:setw synchronize-panes` | Sync panes (toggle) |
| `:source-file ~/.tmux.conf` | Reload config |

---

## 💡 Scénarios Pratiques

### Scénario 1 : Workspace Développement Complet

```bash
#!/bin/bash
# Script : Créer workspace dev automatique

SESSION="webapp-dev"

# Tuer session si existe
tmux has-session -t $SESSION 2>/dev/null && tmux kill-session -t $SESSION

# Créer session + window 0
tmux new-session -d -s $SESSION -n "editor"

# Window 0 : Éditeur (split vertical)
tmux send-keys -t $SESSION:0 "cd ~/projects/webapp" C-m
tmux send-keys -t $SESSION:0 "vim app.py" C-m
tmux split-window -h -t $SESSION:0
tmux send-keys -t $SESSION:0.1 "cd ~/projects/webapp" C-m

# Window 1 : Server dev
tmux new-window -t $SESSION:1 -n "server"
tmux send-keys -t $SESSION:1 "cd ~/projects/webapp" C-m
tmux send-keys -t $SESSION:1 "python manage.py runserver" C-m

# Window 2 : Logs (split horizontal)
tmux new-window -t $SESSION:2 -n "logs"
tmux send-keys -t $SESSION:2 "tail -f /var/log/webapp/error.log" C-m
tmux split-window -v -t $SESSION:2
tmux send-keys -t $SESSION:2.1 "tail -f /var/log/webapp/access.log" C-m

# Window 3 : Database
tmux new-window -t $SESSION:3 -n "database"
tmux send-keys -t $SESSION:3 "mysql -u root -p webapp_db" C-m

# Window 4 : Git
tmux new-window -t $SESSION:4 -n "git"
tmux send-keys -t $SESSION:4 "cd ~/projects/webapp" C-m

# Sélectionner window 0 (editor)
tmux select-window -t $SESSION:0

# Attacher session
tmux attach-session -t $SESSION
```

**Usage** :
```bash
chmod +x ~/start-dev.sh
~/start-dev.sh

# Workspace complet créé :
# Window 0 : Vim + terminal
# Window 1 : Server Django/Flask
# Window 2 : Logs error/access
# Window 3 : MySQL client
# Window 4 : Git commands
```

---

### Scénario 2 : Monitoring Multi-Serveurs

```bash
# Session monitoring
tmux new -s "monitoring" -n "overview"

# Window 0 : Vue ensemble (htop local)
htop

# Window 1 : Server 1 (web-01) - Split 4 quadrants
Ctrl+B c
Ctrl+B ,
web-01
ssh web-01
Ctrl+B %          # Split vertical
ssh web-01
Ctrl+B →
Ctrl+B "          # Split horizontal droite
ssh web-01
Ctrl+B ←
Ctrl+B "          # Split horizontal gauche
ssh web-01

# Layout :
# ┌──────────┬──────────┐
# │ ssh web  │ ssh web  │
# │ htop     │ tail logs│
# ├──────────┼──────────┤
# │ ssh web  │ ssh web  │
# │ free -h  │ df -h    │
# └──────────┴──────────┘

# Commandes chaque pane
Ctrl+B ↑ (haut gauche)
htop
Ctrl+B → (haut droite)
tail -f /var/log/syslog
Ctrl+B ↓ (bas droite)
df -h
Ctrl+B ← (bas gauche)
free -h -t 1

# Window 2 : Server 2 (db-01) - Même layout
Ctrl+B c
(répéter splits + ssh db-01)
```

---

### Scénario 3 : Pair Programming Remote

```bash
# User 1 : Créer session partagée
tmux new -s "pair-coding"

# Donner accès SSH User 2
# (User 2 se connecte même serveur)

# User 2 : Attacher MÊME session
tmux attach -t pair-coding

# Les DEUX voient écran identique temps réel
# User 1 tape → User 2 voit
# User 2 tape → User 1 voit

# Workflow :
# - Window 0 : Éditeur partagé (vim)
# - Window 1 : Terminal tests
# - Window 2 : Recherche doc (man, --help)

# Détacher proprement
Ctrl+B d  # User 1 ou 2 (pas tuer session)

# Session reste active pour autre user
```

---

### Scénario 4 : Compilation Longue SSH

```bash
# Se connecter serveur build
ssh user@build-server

# Démarrer tmux (sécurité déconnexion)
tmux new -s "compile-kernel"

# Lancer compilation (6 heures)
cd /usr/src/linux
make -j$(nproc)

# Détacher (peut fermer SSH tranquillement)
Ctrl+B d

# Déconnecter SSH
exit

# --- 3 heures plus tard, autre machine ---

# Reconnecter
ssh user@build-server

# Réattacher voir progression
tmux a -t compile-kernel

# Compilation continue, voir output temps réel

# Re-détacher si besoin
Ctrl+B d
```

---

### Scénario 5 : Administration Multi-Tâches

```bash
# Session admin quotidienne
tmux new -s "daily-admin"

# Window 0 : Monitoring système
# Pane 0 : htop
htop
Ctrl+B "  # Split horizontal
# Pane 1 : iostat
iostat -x 2
Ctrl+B %  # Split vertical (depuis pane 1)
# Pane 2 : vmstat
vmstat 2

# Window 1 : Logs surveillance
Ctrl+B c
Ctrl+B ,
logs
tail -f /var/log/syslog
Ctrl+B "
tail -f /var/log/auth.log

# Window 2 : Backup vérification
Ctrl+B c
Ctrl+B ,
backups
ls -lh /backup/daily/
Ctrl+B "
du -sh /backup/*

# Window 3 : Docker
Ctrl+B c
Ctrl+B ,
docker
docker ps
Ctrl+B "
docker stats

# Fin journée : Détacher (garde monitoring actif)
Ctrl+B d

# Lendemain : Réattacher
tmux a -t daily-admin
# Voir ce qui s'est passé nuit (logs, monitoring)
```

---

### Scénario 6 : Copier Config Entre Serveurs

```bash
# Window 0 : Server source (web-01)
ssh web-01
cat /etc/nginx/sites-available/mysite

# Copier config
Ctrl+B [           # Mode scrollback
/server {          # Chercher début config
n n n              # Trouver bonne occurrence
Espace             # Marquer début
G                  # Fin buffer
Espace             # Marquer fin (copier)

# Window 1 : Server destination (web-02)
Ctrl+B c
ssh web-02
sudo vim /etc/nginx/sites-available/mysite

# Coller config
i                  # Insert mode vim
Ctrl+B ]           # Coller
Esc :wq            # Sauvegarder

# Tester config
sudo nginx -t
sudo systemctl reload nginx
```

---

### Scénario 7 : Debugging Application (Splits)

```bash
# Window debugging
tmux new -s "debug-app" -n "debug"

# Layout 3 panes :
# ┌─────────────────────┐
# │   Code (vim)        │  ← Pane 0
# ├──────────┬──────────┤
# │ App run  │ Logs     │  ← Pane 1 & 2
# └──────────┴──────────┘

# Pane 0 : Éditeur
vim app.py

# Split horizontal
Ctrl+B "

# Pane 1 : Lancer app
python app.py

# Split vertical (pane 1)
Ctrl+B %

# Pane 2 : Logs temps réel
tail -f /var/log/app/error.log

# Workflow debugging :
# 1. Voir erreur (Pane 2 logs)
# 2. Corriger code (Pane 0 vim)
# 3. Relancer app (Pane 1)
#    Ctrl+B ↓ (aller pane 1)
#    Ctrl+C (stop app)
#    ↑ (historique)
#    Enter (relancer)
# 4. Vérifier logs (Pane 2)
# 5. Répéter

# Zoom pane si besoin focus
Ctrl+B ↑ (pane vim)
Ctrl+B z (zoom vim plein écran)
# Éditer
Ctrl+B z (dézoom retour layout)
```

---

### Scénario 8 : Session Background Persistante

```bash
# Lancer backup nuit (détaché dès départ)
tmux new -d -s "backup-nightly" \
  "cd /scripts && ./backup-full.sh"

# Vérifier démarré
tmux ls
# backup-nightly: 1 windows (created ...)

# Continuer travailler (backup tourne background)

# Vérifier progression après 1h
tmux a -t backup-nightly
# Voir output backup

# Détacher à nouveau
Ctrl+B d

# Attendre fin (script termine → session meurt auto)

# OU : Script long, garder logs
tmux new -d -s "backup-nightly" \
  "cd /scripts && ./backup-full.sh 2>&1 | tee backup.log"

# Lendemain : Consulter résultat
tmux a -t backup-nightly
# OU lire log si session morte
cat /scripts/backup.log
```

---

### Scénario 9 : Synchronize Panes (Admin Multiple Serveurs)

```bash
# Configuration nécessaire :
# ~/.tmux.conf : bind S setw synchronize-panes

# Créer layout 4 serveurs
tmux new -s "multi-server" -n "all"

# Pane 0 : web-01
ssh web-01

# Split vertical
Ctrl+B %
# Pane 1 : web-02
ssh web-02

# Split horizontal (pane 0)
Ctrl+B ←
Ctrl+B "
# Pane 2 : web-03
ssh web-03

# Split horizontal (pane 1)
Ctrl+B →
Ctrl+B "
# Pane 3 : web-04
ssh web-04

# Layout :
# ┌─────────┬─────────┐
# │ web-01  │ web-02  │
# ├─────────┼─────────┤
# │ web-03  │ web-04  │
# └─────────┴─────────┘

# Activer synchronize panes
Ctrl+B S
# Indicateur orange barre : [sync]

# Taper MÊME commande TOUS serveurs simultanément
sudo apt update
# Exécuté sur web-01, web-02, web-03, web-04 simultanément

sudo apt upgrade -y
# Upgrade TOUS serveurs

systemctl status nginx
# Vérifier TOUS serveurs

# Désactiver sync
Ctrl+B S
# Retour normal (commandes individuelles)
```

---

### Scénario 10 : Workspace Custom Layouts

```bash
# ~/.tmux.conf : Définir layout custom
cat >> ~/.tmux.conf <<'EOF'
# Layout "coding" : Editor + terminal + logs
bind C-c new-window -n "coding" \; \
  send-keys "vim" C-m \; \
  split-window -v -p 30 \; \
  split-window -h \; \
  select-pane -t 0

# Layout "monitoring" : 4 quadrants htop/iostat/df/free
bind C-m new-window -n "monitoring" \; \
  send-keys "htop" C-m \; \
  split-window -h \; \
  send-keys "iostat -x 2" C-m \; \
  split-window -v \; \
  send-keys "df -h" C-m \; \
  select-pane -t 0 \; \
  split-window -v \; \
  send-keys "free -h -t 1" C-m
EOF

# Recharger config
Ctrl+B :
:source-file ~/.tmux.conf

# Utiliser layouts
Ctrl+B Ctrl+C  # Créer window "coding" layout auto
Ctrl+B Ctrl+M  # Créer window "monitoring" layout auto

# Layouts créés instantanément !
```

---

## 🎓 Points Clés pour l'Examen

✅ **tmux** : Multiplexeur moderne client/serveur  
✅ **Préfixe** : **Ctrl+B** (vs Screen Ctrl+A)  
✅ **Server** : Background unique, clients multiples  
✅ **Session** : Workspace (ensemble windows)  
✅ **Window** : Terminal virtuel (onglet)  
✅ **Pane** : Split window (plusieurs terminaux visibles)  
✅ **Créer session** : tmux new -s "nom"  
✅ **Détacher** : Ctrl+B d  
✅ **Lister** : tmux ls  
✅ **Réattacher** : tmux a ou tmux attach -t nom  
✅ **Windows** : Ctrl+B c (créer), n/p (naviguer), , (renommer)  
✅ **Panes** : Ctrl+B " (horizontal), % (vertical), flèches (naviguer)  
✅ **Resize** : Ctrl+B Ctrl+flèches (×1), Alt+flèches (×5)  
✅ **Zoom** : Ctrl+B z (toggle plein écran pane)  
✅ **Copier** : Ctrl+B [, Espace×2, Ctrl+B ]  
✅ **Config** : ~/.tmux.conf (settings, keybindings, apparence)  
✅ **Mode commande** : Ctrl+B :  
✅ **Barre état** : Auto (session, windows, heure, date)  

---

## ⚠️ Erreurs Courantes

### Erreur 1 : Confusion Préfixe Screen/tmux

```bash
# ❌ ERREUR : Utiliser Ctrl+A (Screen) dans tmux
Ctrl+A c
# (rien) → tmux préfixe = Ctrl+B

# ✅ CORRECT : Ctrl+B dans tmux
Ctrl+B c
# Créer window

# OU : Changer préfixe (dans ~/.tmux.conf)
unbind C-b
set -g prefix C-a
bind C-a send-prefix
```

---

### Erreur 2 : Oublier Enter Mode Scrollback

```bash
# ❌ PROBLÈME : Tenter défiler avec molette sans mode
# Molette souris : (ne scrolle pas buffer, scrolle terminal)

# ✅ SOLUTION : Mode scrollback
Ctrl+B [   # Entrer mode
↑↓         # Défiler buffer tmux
q          # Quitter mode

# OU : Activer souris (dans ~/.tmux.conf)
set -g mouse on
# Molette scrolle automatiquement (sans Ctrl+B [)
```

---

### Erreur 3 : Panes Trop Petits Resize

```bash
# ❌ PROBLÈME : Resize 1 par 1 très lent
Ctrl+B Ctrl+→
Ctrl+B Ctrl+→
Ctrl+B Ctrl+→
# ... (20 fois pour agrandir)

# ✅ SOLUTION : Resize ×5
Ctrl+B Alt+→
Ctrl+B Alt+→
Ctrl+B Alt+→
# (4× plus rapide)

# OU : Layouts prédéfinis
Ctrl+B Alt+1  # Even horizontal
Ctrl+B Alt+2  # Even vertical
Ctrl+B Alt+3  # Main horizontal
Ctrl+B Alt+4  # Main vertical
Ctrl+B Alt+5  # Tiled
```

---

### Erreur 4 : Perdre Pane (Zoom Oublié)

```bash
# Zoomer pane
Ctrl+B z

# Travailler...

# ❌ CONFUSION : Où sont autres panes ?
# Oublier zoom actif (Z dans barre)

# ✅ VÉRIFIER : Indicateur barre
# [mysession] 0:bash* Z                          12:34 02-Feb-26
#                     ↑ Z = Zoom actif

# Dézoom
Ctrl+B z
# Retour layout normal
```

---

### Erreur 5 : Session Morte (Tous Panes Fermés)

```bash
# Session avec 1 seule window, 1 pane
# Fermer pane
exit

# ❌ RÉSULTAT : Session MEURT (plus aucun pane)
# [exited]

# tmux ls
# (session disparue)

# ✅ ÉVITER : Détacher au lieu fermer
Ctrl+B d  # Détacher (session vit)

# OU : Créer windows multiples (redondance)
```

---

### Erreur 6 : Config Pas Appliquée

```bash
# Éditer ~/.tmux.conf
vim ~/.tmux.conf
# Ajouter : set -g mouse on

# ❌ PROBLÈME : Souris marche pas (config pas chargée)

# ✅ SOLUTION : Recharger config
Ctrl+B :
:source-file ~/.tmux.conf

# OU : Détacher/réattacher
Ctrl+B d
tmux a

# OU : Raccourci reload (ajouter config)
# bind r source-file ~/.tmux.conf
# Puis : Ctrl+B r
```

---

### Erreur 7 : Synchronize Panes Oublié Actif

```bash
# Activer sync panes (admin multiple serveurs)
Ctrl+B :
:setw synchronize-panes on

# Taper commandes...

# ❌ DANGER : Oublier désactiver
# Commande suivante exécutée TOUS serveurs

rm -rf /tmp/*
# TOUS serveurs nettoient /tmp (peut-être pas voulu)

# ✅ SÉCURITÉ : Vérifier indicateur
# Barre affiche : [sync] ou [synchronize-panes]

# Désactiver
Ctrl+B :
:setw synchronize-panes off

# OU : Toggle avec raccourci (ajouter config)
# bind S setw synchronize-panes
```

---

### Erreur 8 : Attacher Session Déjà Attachée

```bash
# Session attachée (terminal 1)
tmux a -t dev

# Tenter attacher (terminal 2)
tmux a -t dev
# sessions should be nested with care, unset $TMUX to force

# ❌ ERREUR : tmux refuse (éviter nested)

# ✅ SOLUTION 1 : Partager session (multi-attach)
tmux a -t dev
# (accepte, 2 clients même session)

# ✅ SOLUTION 2 : Forcer détach autres
tmux a -d -t dev
# -d : Détache autres clients avant attacher
```

---

### Erreur 9 : Copier Texte Long (Sélection Difficile)

```bash
# Texte 500 lignes à copier

# ❌ PROBLÈME : Sélection manuelle pénible
Ctrl+B [
↓↓↓↓↓... (500 fois)

# ✅ SOLUTION : Commandes recherche
Ctrl+B [
/Début texte    # Chercher début
Enter
Espace          # Marquer début
/Fin texte      # Chercher fin
Enter
Espace          # Marquer fin (copier)

# OU : Marks (mode vi)
# v : Visual
# gg : Début buffer
# G : Fin buffer
# y : Yank (copier tout)
```

---

### Erreur 10 : Pas Nommer Sessions/Windows

```bash
# ❌ CONFUSION : Tout par défaut
tmux ls
# 0: 5 windows (attached)
# 1: 3 windows
# 2: 1 windows

tmux a -t 0
Ctrl+B w
# 0: bash-
# 1: bash*
# 2: bash-

# Quelle window fait quoi ???

# ✅ CLARTÉ : Toujours nommer
tmux new -s "dev-webapp" -n "editor"
Ctrl+B c
Ctrl+B ,
server
Ctrl+B c
Ctrl+B ,
logs

tmux ls
# dev-webapp: 3 windows (attached)

Ctrl+B w
# 0: editor-
# 1: server-
# 2: logs*

# Clair immédiatement !
```

---

## 🆚 tmux vs Screen

### Comparaison Détaillée

| Critère | Screen | tmux |
|---------|--------|------|
| **Architecture** | Monolithique | Client/serveur |
| **Préfixe** | Ctrl+A | Ctrl+B |
| **Config** | ~/.screenrc | ~/.tmux.conf |
| **Année** | 1987 | 2007 |
| **Popularité** | Déclin | ⬆️ Montée |
| **Souris** | ❌ Non | ✅ Oui |
| **Panes resize** | Basique | Facile (Ctrl+flèches) |
| **Layouts** | Manuels | Prédéfinis (Alt+1-5) |
| **Status bar** | Custom complexe | Intégrée jolie |
| **Plugins** | Rares | Nombreux (TPM) |
| **Scripting** | Moyen | Excellent |
| **Performance** | OK | Meilleure |
| **Partage session** | -x (basique) | Multi-attach natif |
| **Copy/paste** | Ctrl+A [ ] | Ctrl+B [ ] (+ mode vi) |

---

### Migration Screen → tmux

**Cheatsheet conversion** :

| Action | Screen | tmux |
|--------|--------|------|
| Préfixe | Ctrl+A | Ctrl+B |
| Créer window | Ctrl+A c | Ctrl+B c |
| Next window | Ctrl+A n | Ctrl+B n |
| Split horizontal | Ctrl+A S | Ctrl+B " |
| Split vertical | Ctrl+A \| | Ctrl+B % |
| Détacher | Ctrl+A d | Ctrl+B d |
| Lister sessions | screen -ls | tmux ls |
| Réattacher | screen -r | tmux a |

**Transition douce** : Changer préfixe tmux → Ctrl+A
```bash
# ~/.tmux.conf
unbind C-b
set -g prefix C-a
bind C-a send-prefix
```

---

### Quand Utiliser Screen ?

✅ **Screen** si :
- Serveur ancien (Screen préinstallé, tmux absent)
- Habitude forte Screen (années usage)
- Pas admin (impossible installer tmux)
- Script legacy dépend Screen

---

### Quand Utiliser tmux ?

✅ **tmux** si :
- **Projet nouveau** (apprendre tool moderne)
- Besoin **souris** (scroll, resize, select pane)
- **Plugins** (tmux-resurrect, tmux-continuum...)
- **Layouts** complexes (resize facile)
- **Scripting** avancé (automation workspace)
- **Partage** session (pair programming)

**Recommandation 2026** : **tmux** (standard actuel)

---

**🎯 Prochaine étape** : Perform Basic File Editing Operations Using vi/vim

*Dernière mise à jour: 2 février 2026*
