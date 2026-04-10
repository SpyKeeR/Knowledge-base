# 📝 Éditeurs de Texte Linux : vi/vim, nano, emacs

**Module** : GNU and Unix Commands  
**Cours** : Basic File Editing Operations  
**Objectif** : Maîtriser éditeurs texte essentiels Linux

---

## 📖 Pagers : less et more

### Concepts Pagers

**Pager** = Visualiser fichier texte (lecture seule, navigation)

**Analogie** 📰 :
- **cat** = Affiche journal entier d'un coup (scroll rapide)
- **less/more** = Tourne pages journal (contrôle lecture)

---

### more : Pager Basique

```bash
# Visualiser fichier
more fichier.txt

# Navigation :
# Espace : Page suivante
# Entrée : Ligne suivante
# q : Quitter
# /pattern : Rechercher
# n : Recherche suivante
```

**Limitations** :
- ❌ Pas scroll arrière (seulement avant)
- ❌ Moins fonctionnalités que less

---

### less : Pager Moderne

```bash
# Visualiser fichier
less fichier.txt

# Navigation :
# ↑↓ : Ligne haut/bas
# PgUp/PgDown : Page haut/bas
# g : Début fichier
# G : Fin fichier
# /pattern : Rechercher AVANT
# ?pattern : Rechercher ARRIÈRE
# n : Recherche suivante
# N : Recherche précédente
# q : Quitter
# h : Aide
```

---

### less : Options Utiles

```bash
# Recherche insensible casse
less -i fichier.txt
# /ERROR → trouve "error", "Error", "ERROR"

# Numéros lignes
less -N fichier.txt

# Ne pas wrapper lignes longues
less -S fichier.txt
# ←→ : Scroll horizontal

# Suivre fichier (comme tail -f)
less +F /var/log/syslog
# Ctrl+C : Arrêter suivi
# F : Reprendre suivi

# Ouvrir fin fichier
less +G fichier.txt
```

---

### Recherche Insensible Casse

```bash
# Option -i (insensitive)
less -i /var/log/syslog

# Recherche :
# /error
# Trouve : "Error", "ERROR", "error"

# OU : Dans less, activer interactivement
# -i (pendant visualisation)
# Active/désactive insensibilité casse
```

---

## 📝 vi/vim : Éditeur Modal

### Concepts vi/vim

**vi** = Visual editor (1976, Bill Joy)  
**vim** = Vi IMproved (1991, moderne)

**Modal** = 3 modes principaux

| Mode | Rôle | Entrée |
|------|------|--------|
| **Normal** | Navigation, commandes | Esc |
| **Insert** | Saisie texte | i, a, o |
| **Visual** | Sélection texte | v, V, Ctrl+V |
| **Command** | Commandes ex | : |

---

### Modes vi/vim

```bash
# Mode NORMAL (défaut)
# - Naviguer (hjkl, ↑↓←→)
# - Copier/Coller (yy, p)
# - Supprimer (dd, x)
# - Chercher (/pattern)

# → Mode INSERT (saisie)
i     # Insert avant curseur
a     # Append après curseur
o     # Nouvelle ligne dessous
O     # Nouvelle ligne dessus

# → Mode VISUAL (sélection)
v     # Visual caractères
V     # Visual lignes
Ctrl+V # Visual bloc

# → Mode COMMAND (commandes)
:     # Commandes ex (:w, :q, :wq)

# Retour NORMAL depuis n'importe quel mode
Esc
```

---

### Navigation vim

```bash
# Caractères
h     # Gauche
j     # Bas
k     # Haut
l     # Droite

# Mots
w     # Word suivant (début)
b     # Back word précédent (début)
e     # End word suivant (fin)

# Lignes
0     # Début ligne (colonne 0)
^     # Premier caractère non-blanc
$     # Fin ligne

# Écran
H     # Haut écran (High)
M     # Milieu écran (Middle)
L     # Bas écran (Low)

# Fichier
gg    # Début fichier
G     # Fin fichier
:42   # Aller ligne 42

# Scroll
Ctrl+F # Forward page
Ctrl+B # Back page
Ctrl+D # Down demi-page
Ctrl+U # Up demi-page
```

---

### Édition vim

```bash
# Insérer
i     # Insert avant curseur
I     # Insert début ligne
a     # Append après curseur
A     # Append fin ligne
o     # Open ligne dessous
O     # Open ligne dessus

# Supprimer
x     # Supprimer caractère (comme Del)
X     # Supprimer caractère avant (comme Backspace)
dd    # Delete ligne
D     # Delete fin ligne
dw    # Delete word

# Copier/Coller
yy    # Yank ligne (copier)
yw    # Yank word
p     # Put après curseur (coller)
P     # Put avant curseur

# Changer (delete + insert)
cw    # Change word
cc    # Change ligne
C     # Change fin ligne

# Undo/Redo
u     # Undo
Ctrl+R # Redo
```

---

### Recherche vim

```bash
# Recherche avant
/pattern

# Recherche arrière
?pattern

# Résultat suivant
n

# Résultat précédent
N

# Recherche mot sous curseur
*     # Suivant
#     # Précédent

# Recherche insensible casse (dans pattern)
/error\c
# Trouve "error", "Error", "ERROR"

# OU : Activer globalement
:set ignorecase
:set ic

# Smart case (insensible si minuscules, sensible si majuscule)
:set smartcase
:set sic
```

---

### Commandes ex (:)

```bash
# Fichier
:w          # Write (sauvegarder)
:w fichier  # Sauvegarder sous nom
:q          # Quit
:q!         # Quit sans sauvegarder (force)
:wq         # Write + Quit
:x          # Write + Quit (si modifié)
ZZ          # Write + Quit (mode normal)

# Édition
:5,10d      # Delete lignes 5-10
:5,10y      # Yank lignes 5-10
:5,10s/old/new/g  # Substitute lignes 5-10

# Rechercher/Remplacer
:%s/old/new/g       # Remplacer TOUT fichier
:%s/old/new/gc      # Avec confirmation
:5,10s/old/new/g    # Lignes 5-10

# Fichiers multiples
:e fichier2.txt     # Edit autre fichier
:bn                 # Buffer next
:bp                 # Buffer previous
:ls                 # Lister buffers

# Aide
:help
:help dd
```

---

### Options vim Utiles

```bash
# Numéros lignes
:set number
:set nu

# Désactiver
:set nonumber
:set nonu

# Syntax highlighting
:syntax on

# Indentation auto
:set autoindent
:set ai

# Tab = espaces
:set expandtab
:set et

# Taille tab
:set tabstop=4
:set ts=4

# Search highlighting
:set hlsearch
:set hls

# Désactiver highlight actuel
:noh

# Configuration permanente : ~/.vimrc
```

---

### ~/.vimrc Configuration

```bash
# ~/.vimrc

" Général
set number              " Numéros lignes
set relativenumber      " Numéros relatifs
set ruler               " Position curseur
set showcmd             " Affiche commande
set showmode            " Affiche mode

" Recherche
set ignorecase          " Insensible casse
set smartcase           " Smart case
set hlsearch            " Highlight résultats
set incsearch           " Recherche incrémentale

" Indentation
set autoindent          " Auto-indent
set expandtab           " Tab → espaces
set tabstop=4           " Tab = 4 espaces
set shiftwidth=4        " Indent = 4 espaces

" Apparence
syntax on               " Syntax highlighting
set background=dark     " Fond sombre
colorscheme desert      " Theme

" Divers
set mouse=a             " Support souris
set clipboard=unnamedplus " Clipboard système
set wildmenu            " Menu complétion commandes
```

---

## 🖊️ nano : Éditeur Simple

### Concepts nano

**nano** = Éditeur **simple**, **intuitif** (pas modal comme vi)

**Avantages** :
- ✅ Courbe apprentissage **facile**
- ✅ Aide **visible** (bas écran)
- ✅ Pas modes (saisie directe)

**Inconvénients** :
- ❌ Moins **puissant** que vim
- ❌ Pas scripting avancé

---

### Interface nano

```bash
# Ouvrir fichier
nano fichier.txt

# Interface :
# ┌─────────────────────────────────┐
# │  GNU nano 5.4     fichier.txt   │ ← Header
# ├─────────────────────────────────┤
# │ Contenu fichier...              │
# │                                 │
# │                                 │
# ├─────────────────────────────────┤
# │ ^G Help  ^O Write ^W Where Is  │ ← Aide (bas)
# │ ^X Exit  ^R Read  ^\ Replace   │
# └─────────────────────────────────┘

# Notation :
# ^ = Ctrl
# M- = Alt
```

---

### Raccourcis nano Essentiels

**Navigation** :

```bash
# Curseur
↑↓←→              # Flèches

# Page
Ctrl+Y            # Page haut
Ctrl+V            # Page bas

# Ligne
Ctrl+A            # Début ligne (Home)
Ctrl+E            # Fin ligne (End)

# Fichier
Alt+\             # Début fichier
Alt+/             # Fin fichier

# Aller ligne
Ctrl+_            # Prompt numéro ligne
```

---

**Édition** :

```bash
# Copier/Coller
Ctrl+6            # Marquer début sélection (Alt+A aussi)
# (déplacer curseur pour sélectionner)
Alt+6             # Copier sélection
Ctrl+K            # Couper sélection (ou ligne si pas sélection)
Ctrl+U            # Coller

# Undo/Redo
Alt+U             # Undo (annuler)
Alt+E             # Redo (refaire)

# Supprimer
Ctrl+K            # Couper ligne actuelle
Ctrl+D            # Delete caractère (Del)
Backspace         # Delete caractère avant
```

---

**Recherche/Remplacement** :

```bash
# Rechercher
Ctrl+W            # Where Is (rechercher)
# Taper pattern, Entrée
Alt+W             # Recherche suivante (répéter)
Alt+Q             # Recherche précédente

# Remplacer
Ctrl+\            # Replace
# Enter search term:
# Replace with:
# Y : Oui (remplacer cette occurrence)
# N : Non (passer)
# A : All (remplacer toutes)
# Ctrl+C : Annuler
```

---

**Fichier** :

```bash
# Sauvegarder
Ctrl+O            # Write Out (sauvegarder)
# File Name to Write: fichier.txt
# Entrée : Confirmer
# Ctrl+T : Browser fichiers

# Quitter
Ctrl+X            # Exit
# Si modifié :
#   Save modified buffer? (Y/N)
#   Y : Sauvegarder puis quitter
#   N : Quitter sans sauvegarder

# Insérer fichier
Ctrl+R            # Read File (insérer contenu autre fichier)
```

---

**Vérification Orthographique** :

```bash
# Spell check (si aspell installé)
Ctrl+T            # To Spell

# Installer aspell :
sudo apt install aspell aspell-fr

# Utiliser :
# - Mots mal orthographiés surlignés
# - Suggestions proposées
# - Ctrl+C : Ignorer
# - 1-9 : Choisir suggestion
```

---

**Autres** :

```bash
# Aide
Ctrl+G            # Get Help

# Afficher position
Ctrl+C            # Affiche ligne/colonne actuelle

# Justifier paragraphe
Ctrl+J            # Justify

# Indenter
Alt+}             # Indenter ligne/sélection
Alt+{             # Dé-indenter

# Commenter (si syntax highlighting actif)
Alt+3             # Commenter ligne/sélection
```

---

### Options nano (Ligne Commande)

```bash
# Numéros lignes
nano -l fichier.txt
nano --linenumbers fichier.txt

# Backup automatique
nano -B fichier.txt
# Crée fichier~ backup

# Smooth scroll
nano -S fichier.txt

# Syntaxe spécifique
nano -Y python script.py
nano -Y sh script.sh

# Readonly
nano -v fichier.txt
```

---

### ~/.nanorc Configuration

```bash
# ~/.nanorc

# Numéros lignes
set linenumbers

# Backup auto
set backup
set backupdir "~/.nano/backups"

# Smooth scroll
set smooth

# Mouse support
set mouse

# Tabsize
set tabsize 4
set tabstospaces

# Syntax highlighting
include "/usr/share/nano/*.nanorc"

# Couleurs
set titlecolor brightwhite,blue
set statuscolor brightwhite,green
set errorcolor brightwhite,red
set selectedcolor brightwhite,magenta
set numbercolor cyan
```

---

## 🎯 emacs : IDE Puissant

### Concepts emacs

**emacs** = Editor MACroS (1976, Richard Stallman)

**Philosophie** : "Operating system déguisé en éditeur"

**Capacités** :
- ✅ Éditeur texte complet
- ✅ IDE programmation
- ✅ Client email (mu4e, gnus)
- ✅ Lecteur news/RSS
- ✅ Agenda/TODO (org-mode)
- ✅ Shell intégré
- ✅ Git interface (magit)
- ✅ Extensible Elisp (Emacs Lisp)

---

### Navigation emacs

**Notation** :
- `C-x` = Ctrl+x
- `M-x` = Alt+x (ou Esc puis x)

```bash
# Curseur
C-f   # Forward caractère (→)
C-b   # Backward caractère (←)
C-n   # Next ligne (↓)
C-p   # Previous ligne (↑)

# Mots
M-f   # Forward mot
M-b   # Backward mot

# Lignes
C-a   # Début ligne
C-e   # End ligne

# Écran
C-v   # Page bas (scroll down)
M-v   # Page haut (scroll up)

# Fichier
M-<   # Début fichier (Alt+Shift+,)
M->   # Fin fichier (Alt+Shift+.)

# Aller ligne
M-g g # Prompt numéro ligne
```

---

### Édition emacs

```bash
# Insérer
# (taper directement, pas de mode Insert)

# Supprimer
C-d       # Delete caractère (Del)
Backspace # Delete caractère avant
C-k       # Kill ligne (couper fin ligne)
M-d       # Kill mot suivant
M-Backspace # Kill mot précédent

# Copier/Coller
C-Espace  # Marquer début sélection
# (déplacer curseur)
C-w       # Kill sélection (couper)
M-w       # Copy sélection (copier)
C-y       # Yank (coller)
M-y       # Yank précédent (cycle kill ring)

# Undo
C-/       # Undo
C-x u     # Undo aussi
```

---

### Fichiers emacs

```bash
# Ouvrir fichier
C-x C-f   # Find file
# Prompt : ~/
# Taper chemin : fichier.txt
# Tab : Complétion
# Entrée : Ouvrir

# Sauvegarder
C-x C-s   # Save buffer actuel
C-x s     # Save tous buffers modifiés (prompt)

# Sauvegarder sous
C-x C-w   # Write file (nouveau nom)

# Quitter
C-x C-c   # Quit emacs
# Si modifiés :
#   Save file ~/fichier.txt? (y, n, !, ...)
```

---

### Buffers/Windows emacs

```bash
# Buffers (fichiers ouverts)
C-x b     # Switch buffer
C-x C-b   # List buffers
C-x k     # Kill buffer (fermer)

# Windows (splits)
C-x 2     # Split horizontal (haut/bas)
C-x 3     # Split vertical (gauche/droite)
C-x o     # Other window (cycle)
C-x 0     # Delete window actuelle
C-x 1     # Delete autres windows (garde actuelle)

# Resize windows
C-x ^     # Agrandir hauteur
C-x }     # Agrandir largeur
C-x {     # Réduire largeur
```

---

### Recherche emacs

```bash
# Recherche incrémentale avant
C-s       # Search
# Taper pattern (recherche pendant frappe)
# C-s : Occurrence suivante
# C-r : Occurrence précédente
# Entrée : Arrêter recherche

# Recherche incrémentale arrière
C-r       # Reverse search

# Recherche/Remplacement
M-%       # Query replace
# Query replacing old with new:
# Espace/y : Oui (remplacer)
# n : Non (passer)
# ! : Remplacer tout (rest)
# q : Quit
```

---

### Commandes M-x

```bash
# Exécuter commande par nom
M-x
# M-x
# Taper commande : replace-string
# Entrée

# Commandes utiles :
M-x package-install         # Installer package
M-x shell                   # Shell intégré
M-x eshell                  # Emacs shell
M-x term                    # Terminal émulé
M-x calendar                # Calendrier
M-x doctor                  # Easter egg (psychothérapeute)
M-x tetris                  # Jeu tetris
M-x zone                    # Screensaver animations
```

---

### Modes emacs

**Major Mode** : Type fichier (un seul actif)
- `python-mode` : Fichiers .py
- `text-mode` : Fichiers texte
- `markdown-mode` : Fichiers .md
- `org-mode` : Fichiers .org

**Minor Modes** : Fonctionnalités (plusieurs actifs)
- `auto-fill-mode` : Wrap lignes auto
- `line-number-mode` : Numéros lignes
- `flyspell-mode` : Vérification orthographe temps réel

```bash
# Activer/Désactiver mode
M-x auto-fill-mode
M-x line-number-mode
```

---

### ~/.emacs Configuration

```bash
# ~/.emacs

;; Apparence
(menu-bar-mode -1)         ; Désactiver menu bar
(tool-bar-mode -1)         ; Désactiver tool bar
(scroll-bar-mode -1)       ; Désactiver scroll bar
(setq inhibit-startup-message t) ; Pas message démarrage

;; Numéros lignes
(global-linum-mode t)      ; Numéros lignes

;; Backup
(setq backup-directory-alist '(("." . "~/.emacs.d/backups")))

;; Indentation
(setq-default indent-tabs-mode nil) ; Espaces pas tabs
(setq-default tab-width 4)

;; Parenthèses matching
(show-paren-mode 1)

;; Syntax highlighting
(global-font-lock-mode t)

;; Theme
(load-theme 'tango-dark t)

;; Package repos
(require 'package)
(add-to-list 'package-archives
             '("melpa" . "https://melpa.org/packages/"))
(package-initialize)
```

---

## ⚙️ Configurer Éditeur par Défaut

### Variable EDITOR

**EDITOR** = Variable environnement (éditeur par défaut)

**Utilisé par** :
- `crontab -e` : Éditer crontab
- `visudo` : Éditer sudoers
- `git commit` : Message commit
- `systemctl edit` : Éditer units

---

### Méthode 1 : export (Temporaire)

```bash
# Session actuelle seulement
export EDITOR=nano

# Vérifier
echo $EDITOR
# nano

# Utiliser
crontab -e
# Ouvre avec nano

# ⚠️ Perdu après fermeture terminal
```

---

### Méthode 2 : ~/.bash_profile (Permanent User)

```bash
# Éditer ~/.bash_profile
nano ~/.bash_profile

# Ajouter ligne :
export EDITOR=nano

# Sauvegarder, quitter

# Recharger
source ~/.bash_profile

# OU : Se reconnecter

# Vérifier
echo $EDITOR
# nano

# Appliqué utilisateur courant seulement
```

---

### Méthode 3 : ~/.bashrc (Alternative)

```bash
# Éditer ~/.bashrc (plus commun)
nano ~/.bashrc

# Ajouter fin fichier :
export EDITOR=vim

# Sauvegarder

# Recharger
source ~/.bashrc

# Vérifier
echo $EDITOR
# vim
```

---

### Méthode 4 : update-alternatives (Système)

**update-alternatives** = Gestion alternatives système (Debian/Ubuntu)

```bash
# Lister éditeurs disponibles
update-alternatives --list editor

# Sortie :
# /bin/nano
# /usr/bin/vim.basic
# /usr/bin/vim.tiny

# Configurer interactivement
sudo update-alternatives --config editor

# Menu :
# Selection    Path                Priority   Status
# ------------------------------------------------------------
#   0            /bin/nano            40        auto mode
#   1            /bin/nano            40        manual mode
# * 2            /usr/bin/vim.basic   30        manual mode
#   3            /usr/bin/vim.tiny    15        manual mode
# 
# Press <enter> to keep the current choice[*], or type selection number:

# Taper numéro (par exemple 1 pour nano)
# 1
# Entrée

# Vérifier
update-alternatives --display editor
# editor - auto mode
#   link currently points to /bin/nano

# OU : Variable
echo $EDITOR
# /bin/nano  (si configuré dans profile)
```

---

### Méthode 5 : select-editor (User Debian)

```bash
# Choisir éditeur user (Debian/Ubuntu)
select-editor

# Menu :
# Select an editor.  To change later, run 'select-editor'.
#   1. /bin/nano        <---- easiest
#   2. /usr/bin/vim.basic
#   3. /usr/bin/vim.tiny
# 
# Choose 1-3 [1]:

# Taper numéro
# 2
# Entrée

# Crée ~/.selected_editor
cat ~/.selected_editor
# SELECTED_EDITOR="/usr/bin/vim.basic"

# Appliqué user courant
```

---

### Priorité Variables

```bash
# Ordre priorité (premier trouvé gagne) :

1. VISUAL       # Éditeur visuel (priorité haute)
2. EDITOR       # Éditeur par défaut
3. Système      # Éditeur système (/etc/alternatives/editor)

# Exemple :
export VISUAL=vim
export EDITOR=nano

# git commit utilise vim (VISUAL prioritaire)
# crontab -e peut utiliser nano (si VISUAL ignoré)
```

---

## 📋 Antisèche - Éditeurs

### less

| Commande | Action |
|----------|--------|
| `less -i fichier` | Recherche **insensible** casse |
| `less -N fichier` | **Numéros** lignes |
| `less +F fichier` | **Suivre** fichier (tail -f) |
| `/pattern` | Rechercher **avant** |
| `?pattern` | Rechercher **arrière** |
| `n / N` | Suivant / Précédent |
| `g / G` | Début / Fin fichier |

---

### vim Essentiel

| Commande | Action |
|----------|--------|
| `i / a / o` | Insert / Append / Open ligne |
| `Esc` | Retour mode Normal |
| `hjkl` | ←↓↑→ |
| `w / b` | Word suivant / précédent |
| `0 / $` | Début / Fin ligne |
| `gg / G` | Début / Fin fichier |
| `dd / yy / p` | Delete / Yank / Put ligne |
| `/pattern` | Rechercher |
| `/pattern\c` | Rechercher **insensible** casse |
| `:w` | Sauvegarder |
| `:q` | Quitter |
| `:wq` | Sauvegarder + Quitter |
| `:q!` | Quitter SANS sauvegarder |

---

### nano Essentiel

| Commande | Action |
|----------|--------|
| `Ctrl+6` | Marquer **sélection** |
| `Alt+6` | **Copier** sélection |
| `Ctrl+K` | **Couper** sélection/ligne |
| `Ctrl+U` | **Coller** |
| `Alt+U` | **Undo** |
| `Alt+E` | **Redo** |
| `Ctrl+W` | **Rechercher** |
| `Ctrl+\` | **Remplacer** |
| `Ctrl+T` | **Spell check** |
| `Ctrl+O` | **Sauvegarder** (Write Out) |
| `Ctrl+X` | **Quitter** |

---

### emacs Essentiel

| Commande | Action |
|----------|--------|
| `C-x C-f` | Ouvrir fichier |
| `C-x C-s` | Sauvegarder |
| `C-x C-c` | Quitter |
| `C-Espace` | Marquer sélection |
| `C-w / M-w` | Couper / Copier |
| `C-y` | Coller (Yank) |
| `C-/` | Undo |
| `C-s / C-r` | Rechercher avant/arrière |
| `M-%` | Remplacer |
| `C-x 2 / 3` | Split horizontal/vertical |
| `C-x o` | Other window |

---

### Configurer EDITOR

| Méthode | Portée | Commande |
|---------|--------|----------|
| `export EDITOR=nano` | **Temporaire** (session) | Immediate |
| `~/.bashrc` | **Permanent** (user) | `echo 'export EDITOR=vim' >> ~/.bashrc` |
| `update-alternatives` | **Système** (tous users) | `sudo update-alternatives --config editor` |
| `select-editor` | **User** (Debian) | `select-editor` |

---

## 🎓 Points Clés pour l'Examen

✅ **less -i** : Recherche insensible casse  
✅ **vim /pattern\c** : Recherche insensible casse dans pattern  
✅ **vim modes** : Normal (Esc), Insert (i/a/o), Command (:)  
✅ **vim :w :q :wq :q!** : Sauvegarder, Quitter, Les deux, Force quit  
✅ **nano Ctrl+O** : Sauvegarder (Write Out)  
✅ **nano Ctrl+X** : Quitter  
✅ **nano Ctrl+6** : Marquer sélection  
✅ **nano Alt+6 / Ctrl+K / Ctrl+U** : Copier / Couper / Coller  
✅ **nano Alt+U / Alt+E** : Undo / Redo  
✅ **nano Ctrl+\** : Remplacer  
✅ **nano Ctrl+T** : Spell check  
✅ **emacs C- / M-** : Ctrl / Alt notation  
✅ **emacs IDE** : Email, news, RSS, agenda, shell intégré  
✅ **EDITOR variable** : export EDITOR=nano (temporaire)  
✅ **~/.bash_profile** : export EDITOR=vim (permanent user)  
✅ **update-alternatives --config editor** : Système (meilleur outil)  
✅ **Priorité** : VISUAL > EDITOR > système  

---

**🎯 Prochaine étape** : Module 4 - Devices, Linux Filesystems, FHS

*Dernière mise à jour: 2 février 2026*
