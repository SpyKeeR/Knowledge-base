# 🐚 Configuration de l'Environnement Shell

## 📋 Informations du Module

- **Examen** : LPIC-1 102
- **Objectif** : 105.1 - Customize and use the shell environment
- **Cours** : Shell environment variables + Login vs Non-login shell & Interactive vs Non-Interactive shell
- **Poids** : 4 points
- **Objectif** : Maîtriser les variables d'environnement, comprendre les types de shells, configurer les fichiers de profil appropriés

---

## 🎯 Concepts Fondamentaux

### 🌟 Variables d'Environnement

**Variable d'environnement** : Paire **nom=valeur** stockée dans la session shell, accessible par les processus enfants.

**Deux types** :
- **Variables shell** : Locales au shell actuel (non exportées)
- **Variables d'environnement** : Exportées, héritées par processus enfants

**Analogie** : Variables d'environnement = pense-bête global que tous les programmes enfants peuvent consulter.

### 🔄 Types de Shells : Diagramme Complet

```
┌─────────────────────────────────────────────────────────────┐
│                     TYPES DE SHELLS                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Critère 1 : LOGIN ?                                        │
│  ┌────────────────┐              ┌────────────────────┐    │
│  │  Login Shell   │              │  Non-Login Shell   │    │
│  │  (Connexion)   │              │  (Pas de login)    │    │
│  └────────────────┘              └────────────────────┘    │
│         │                                  │                │
│    SSH, TTY                           bash command          │
│    su - user                          Terminal GUI          │
│                                                             │
│  Critère 2 : INTERACTIF ?                                   │
│  ┌────────────────┐              ┌────────────────────┐    │
│  │  Interactive   │              │  Non-Interactive   │    │
│  │  (Prompt)      │              │  (Script)          │    │
│  └────────────────┘              └────────────────────┘    │
│         │                                  │                │
│    Dialogue user                    bash script.sh          │
│    Commandes                        Cron jobs               │
│                                                             │
│  Combinaisons (4 types possibles) :                         │
│  ┌──────────────────────────────────────────────────────┐  │
│  │                                                       │  │
│  │  1️⃣ Interactive + Login                              │  │
│  │     SSH login, su - user, TTY login                   │  │
│  │     Fichiers: /etc/profile → ~/.bash_profile          │  │
│  │                                                       │  │
│  │  2️⃣ Interactive + Non-Login                          │  │
│  │     bash command, Terminal GUI, su user (sans -)      │  │
│  │     Fichiers: /etc/bash.bashrc → ~/.bashrc            │  │
│  │                                                       │  │
│  │  3️⃣ Non-Interactive + Login                          │  │
│  │     ssh user@host "command"                           │  │
│  │     Fichiers: /etc/profile (+ $BASH_ENV si défini)    │  │
│  │                                                       │  │
│  │  4️⃣ Non-Interactive + Non-Login                      │  │
│  │     bash script.sh, cron, sous-shell commande         │  │
│  │     Fichiers: Seulement $BASH_ENV (si défini)         │  │
│  │                                                       │  │
│  └──────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

**Déterminer type de shell actuel** :
```bash
# Login shell ?
echo $0
# -bash  ou  -su    → Login shell (- en préfixe)
# bash   ou   sh    → Non-login shell

shopt -q login_shell && echo "Login shell" || echo "Non-login shell"

# Interactive ?
[[ $- == *i* ]] && echo "Interactive" || echo "Non-interactive"

# Combiné
case "$-" in
    *i*) echo "Interactive";;
    *) echo "Non-interactive";;
esac
```

---

## 📂 Séquence de Chargement des Fichiers de Configuration

### 1️⃣ Interactive Login Shell

**Exemples** : `ssh user@server`, `su - alice`, `Ctrl+Alt+F2` (TTY)

**Séquence d'exécution** (dans l'ordre) :

```
┌─────────────────────────────────────────────────────────────┐
│  INTERACTIVE LOGIN SHELL                                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. /etc/profile                                            │
│     ↓                                                       │
│     Généralement contient ligne appelant :                  │
│     for script in /etc/profile.d/*.sh; do                   │
│         source "$script"                                    │
│     done                                                    │
│                                                             │
│  2. /etc/profile.d/*.sh                                     │
│     ↓                                                       │
│     Scripts système (configurations globales)               │
│     - /etc/profile.d/lang.sh  (locale)                      │
│     - /etc/profile.d/bash_completion.sh                     │
│                                                             │
│  3. Premier fichier trouvé parmi (s'arrête au 1er) :       │
│     ↓                                                       │
│     ~/.bash_profile  ──┐                                    │
│                        │ SEULEMENT LE                       │
│     ~/.bash_login   ───┤ PREMIER                            │
│                        │ TROUVÉ                             │
│     ~/.profile      ───┘                                    │
│                                                             │
│     ⚠️  Si ~/.bash_profile existe, les 2 autres            │
│         ne seront JAMAIS lus                                │
│                                                             │
│  4. ~/.bashrc                                               │
│     ↓                                                       │
│     Configuration personnelle (toujours chargé)             │
│     ⚠️  Souvent appelé depuis ~/.bash_profile via :        │
│         if [ -f ~/.bashrc ]; then                           │
│             source ~/.bashrc                                │
│         fi                                                  │
│                                                             │
│  LOGOUT :                                                   │
│  5. ~/.bash_logout                                          │
│     ↓                                                       │
│     Nettoyage (clear, suppression historique, etc.)         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Exemple réel** :
```bash
# SSH login
ssh alice@server

# Fichiers chargés :
# 1. /etc/profile
# 2. /etc/profile.d/lang.sh, bash_completion.sh, etc.
# 3. /home/alice/.bash_profile (s'arrête ici si existe)
# 4. /home/alice/.bashrc

# Au logout
exit
# 5. /home/alice/.bash_logout exécuté
```

---

### 2️⃣ Interactive Non-Login Shell

**Exemples** : `bash` command, Terminal GUI (GNOME Terminal, Konsole), `su alice` (sans `-`)

**Séquence d'exécution** :

```
┌─────────────────────────────────────────────────────────────┐
│  INTERACTIVE NON-LOGIN SHELL                                │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. /etc/bash.bashrc  (ou /etc/bashrc sur RHEL/CentOS)     │
│     ↓                                                       │
│     Configuration système globale                           │
│                                                             │
│  2. ~/.bashrc                                               │
│     ↓                                                       │
│     Configuration personnelle utilisateur                   │
│     - Alias (alias ll='ls -la')                             │
│     - Fonctions personnalisées                              │
│     - PS1 (prompt)                                          │
│     - Variables locales                                     │
│                                                             │
│  ⚠️  /etc/profile et ~/.bash_profile NE SONT PAS lus       │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Exemple réel** :
```bash
# Terminal GUI dans GNOME
# Fichiers chargés :
# 1. /etc/bash.bashrc
# 2. /home/alice/.bashrc

# Commande bash depuis shell existant
bash
# 1. /etc/bash.bashrc
# 2. /home/alice/.bashrc
```

---

### 3️⃣ Non-Interactive Shell

**Exemples** : `bash script.sh`, commandes dans cron, sous-shells (`$(commande)`)

**Séquence d'exécution** :

```
┌─────────────────────────────────────────────────────────────┐
│  NON-INTERACTIVE SHELL                                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. Fichier pointé par variable $BASH_ENV (si définie)     │
│     ↓                                                       │
│     export BASH_ENV="/home/alice/.bash_env"                 │
│     → Charge /home/alice/.bash_env                          │
│                                                             │
│  ⚠️  Par défaut, $BASH_ENV est VIDE                        │
│      → AUCUN fichier de config n'est chargé                │
│                                                             │
│  ⚠️  /etc/profile, ~/.bashrc, etc. NE SONT PAS lus         │
│                                                             │
│  Environnement hérité du shell parent uniquement           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Exemple réel** :
```bash
# Script simple
cat > myscript.sh <<'EOF'
#!/bin/bash
echo "PATH: $PATH"
echo "CUSTOM_VAR: $CUSTOM_VAR"
EOF

chmod +x myscript.sh

# Exécution
export CUSTOM_VAR="valeur"
bash myscript.sh
# PATH: /usr/bin:/bin  (hérité du parent)
# CUSTOM_VAR: valeur  (hérité car exporté)

# Avec BASH_ENV
export BASH_ENV="/home/alice/.bash_env"
echo 'export SCRIPT_VAR="configured"' > ~/.bash_env

bash myscript.sh
# Charge ~/.bash_env → SCRIPT_VAR disponible
```

---

### 📊 Tableau Récapitulatif

| Type de Shell | Exemples | Fichiers Chargés (ordre) | Usage typique |
|---------------|----------|--------------------------|---------------|
| **Interactive Login** | SSH, TTY, `su -` | 1. `/etc/profile`<br>2. `/etc/profile.d/*.sh`<br>3. `~/.bash_profile` (ou `.bash_login` ou `.profile`)<br>4. `~/.bashrc`<br>Logout: `~/.bash_logout` | Connexion initiale serveur |
| **Interactive Non-Login** | `bash`, Terminal GUI, `su` | 1. `/etc/bash.bashrc`<br>2. `~/.bashrc` | Travail quotidien local |
| **Non-Interactive** | Scripts, cron, `$(cmd)` | Seulement `$BASH_ENV` (si défini)<br>Sinon **aucun** | Automatisation, scripts |

---

## 🛠️ Commandes de Gestion des Variables

### env - Afficher/Modifier Environnement

**Syntaxe** :
```bash
env [OPTIONS] [VAR=value...] [COMMAND [ARGS]]
```

**Usage** :
```bash
# Afficher toutes variables d'environnement
env
# PATH=/usr/bin:/bin
# HOME=/home/alice
# USER=alice
# ...

# Exécuter commande avec env modifié
env VAR1=value1 VAR2=value2 bash -c 'echo $VAR1 $VAR2'
# value1 value2

# Env vide (seulement VAR définie)
env -i VAR=test bash -c 'echo PATH=$PATH VAR=$VAR'
# PATH= VAR=test  (PATH vide !)

# Supprimer variable pour commande
env -u HOME bash -c 'echo HOME=$HOME'
# HOME=  (HOME non défini)

# Cas d'usage : Isolation environnement
env -i PATH=/usr/bin LANG=C myprogram
```

---

### printenv - Afficher Variables d'Environnement

**Syntaxe** :
```bash
printenv [VARIABLE...]
```

**Usage** :
```bash
# Toutes les variables d'environnement
printenv
# (même résultat que env)

# Variable spécifique
printenv PATH
# /usr/local/bin:/usr/bin:/bin

printenv HOME USER
# /home/alice
# alice

# Différence avec echo
echo $PATH
# /usr/local/bin:/usr/bin:/bin

printenv PATH
# /usr/local/bin:/usr/bin:/bin

# printenv n'affiche QUE les variables EXPORTÉES
MY_VAR="local"  # Non exportée
printenv MY_VAR
# (vide)

export MY_VAR
printenv MY_VAR
# local
```

---

### set - Afficher/Configurer Variables Shell

**Syntaxe** :
```bash
set [OPTIONS] [ARGS]
```

**Usage** :
```bash
# Afficher TOUTES variables (shell + environnement) et fonctions
set | head -20
# BASH=/bin/bash
# BASHOPTS=checkwinsize:cmdhist:...
# MY_LOCAL_VAR=value  ← Variable locale aussi
# PATH=/usr/bin:/bin
# my_function () { ... }  ← Fonctions aussi

# set affiche TOUT (variables locales + exportées + fonctions)
# env/printenv affichent seulement variables EXPORTÉES

# Options debugging (utile scripts)
set -x  # Activer trace (affiche commandes exécutées)
echo "test"
# + echo test  ← Préfixe +
# test

set +x  # Désactiver trace

# Options strictes scripts
set -e  # Arrêter si erreur
set -u  # Erreur si variable non définie
set -o pipefail  # Erreur si échec dans pipe

# Combiné (best practice scripts)
set -euo pipefail

# Arguments positionnels
set -- arg1 arg2 arg3
echo $1 $2 $3
# arg1 arg2 arg3
```

---

### unset - Supprimer Variable ou Fonction

**Syntaxe** :
```bash
unset [OPTIONS] NAME...
```

**Usage** :
```bash
# Créer variable
MY_VAR="valeur"
echo $MY_VAR
# valeur

# Supprimer
unset MY_VAR
echo $MY_VAR
# (vide)

# Supprimer fonction
my_func() { echo "hello"; }
my_func
# hello

unset -f my_func  # -f = fonction
my_func
# bash: my_func: command not found

# Supprimer variable exportée
export PATH_BACKUP=$PATH
printenv PATH_BACKUP
# /usr/bin:/bin

unset PATH_BACKUP
printenv PATH_BACKUP
# (vide)

# ⚠️ Certaines variables ne peuvent pas être unset
unset PATH
echo $PATH
# (vide mais bash peut dysfonctionner)
```

---

### export - Exporter Variable vers Processus Enfants

**Syntaxe** :
```bash
export [OPTIONS] [NAME[=VALUE]...]
```

**Principe** : Variable **locale** → variable **d'environnement** (héritée par enfants)

**Usage** :
```bash
# Créer variable locale
MY_VAR="valeur"

# Vérifier non exportée
bash -c 'echo $MY_VAR'
# (vide - bash enfant ne voit pas MY_VAR)

# Exporter
export MY_VAR

# Vérifier exportée
bash -c 'echo $MY_VAR'
# valeur  ✅ Enfant hérite

# Export + définition (une ligne)
export NEW_VAR="nouvelle valeur"

bash -c 'echo $NEW_VAR'
# nouvelle valeur

# Lister variables exportées
export -p
# declare -x HOME="/home/alice"
# declare -x MY_VAR="valeur"
# declare -x PATH="/usr/bin:/bin"

# Supprimer export (garder variable locale)
export -n MY_VAR  # MY_VAR existe mais non exportée

bash -c 'echo $MY_VAR'
# (vide - plus exportée)

echo $MY_VAR
# valeur  (existe localement)
```

**Schéma export** :
```
Parent Shell
├── MY_VAR="local"  (non exporté)
├── export MY_VAR   (exporté)
│
└─→ Enfant (bash -c 'echo $MY_VAR')
    └── MY_VAR="local"  ✅ Hérite si exporté
                        ❌ Vide si non exporté
```

---

### source (ou .) - Exécuter Script dans Shell Actuel

**Syntaxe** :
```bash
source FILENAME [ARGS]
. FILENAME [ARGS]  # Équivalent POSIX
```

**Principe** : Exécute script dans le **shell actuel** (pas de sous-shell)

**Usage** :
```bash
# Créer fichier config
cat > myconfig.sh <<'EOF'
export MY_VAR="configured"
MY_PATH="/opt/myapp/bin"
PATH="$MY_PATH:$PATH"
alias ll='ls -la'
EOF

# Méthode 1 : bash myconfig.sh (sous-shell)
bash myconfig.sh
echo $MY_VAR
# (vide - MY_VAR défini dans sous-shell, perdu après)

# Méthode 2 : source myconfig.sh (shell actuel)
source myconfig.sh
echo $MY_VAR
# configured  ✅ Variable définie dans shell actuel

alias ll
# alias ll='ls -la'  ✅ Alias défini

# Équivalent avec .
. myconfig.sh

# Recharger ~/.bashrc
source ~/.bashrc
# ou
. ~/.bashrc

# Cas d'usage : Activer virtualenv Python
source /path/to/venv/bin/activate
# Modifie PATH, PS1, etc. dans shell actuel
```

**Différence source vs exécution** :
```
bash script.sh          source script.sh
     │                       │
     ├─ Sous-shell          └─ Shell actuel
     │  Variables perdues      Variables conservées
     │  après exécution        après exécution
     └─ Isolation              Modification directe
```

---

### alias / unalias - Raccourcis Commandes

**Syntaxe** :
```bash
alias [NAME[=VALUE]]
unalias NAME
```

**Usage** :
```bash
# Créer alias
alias ll='ls -la'
alias grep='grep --color=auto'
alias update='sudo apt update && sudo apt upgrade'

# Utiliser
ll
# (équivalent à ls -la)

# Lister tous alias
alias
# alias grep='grep --color=auto'
# alias ll='ls -la'
# alias ls='ls --color=auto'

# Alias spécifique
alias ll
# alias ll='ls -la'

# Supprimer alias
unalias ll
ll
# bash: ll: command not found

# Supprimer tous alias
unalias -a

# Ignorer alias temporairement (exécuter commande réelle)
\ls  # Backslash ignore alias
# ou
command ls
# ou
/bin/ls

# Alias avec arguments
alias rmi='rm -i'  # rm interactif (confirmation)
rmi fichier.txt
# rm: remove regular file 'fichier.txt'? y

# ⚠️ Alias ne peuvent pas prendre arguments positionnels
# Pour ça, utiliser fonctions :
mygrep() { grep -r "$1" /var/log/; }
mygrep "error"
```

**Alias courants** :
```bash
# Navigation
alias ..='cd ..'
alias ...='cd ../..'
alias ~='cd ~'

# Sécurité
alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'

# Couleurs
alias ls='ls --color=auto'
alias grep='grep --color=auto'
alias diff='diff --color=auto'

# Raccourcis
alias h='history'
alias c='clear'
alias e='exit'

# Informations système
alias df='df -h'
alias du='du -h'
alias free='free -h'
```

---

### PS1 - Personnaliser Prompt

**Variable PS1** : Définit l'apparence du **prompt** (invite de commande)

**Codes d'échappement** :

| Code | Description | Exemple |
|------|-------------|---------|
| `\u` | Nom utilisateur | `alice` |
| `\h` | Hostname (court) | `server` |
| `\H` | Hostname (FQDN) | `server.example.com` |
| `\w` | Répertoire courant (complet) | `/home/alice/documents` |
| `\W` | Répertoire courant (basename) | `documents` |
| `\d` | Date (Jour Mois Date) | `Mon Feb 10` |
| `\t` | Heure (HH:MM:SS) | `14:35:20` |
| `\T` | Heure 12h (HH:MM:SS) | `02:35:20` |
| `\@` | Heure 12h (AM/PM) | `02:35 PM` |
| `\$` | `$` si user, `#` si root | `$` ou `#` |
| `\n` | Nouvelle ligne | |
| `\[` `\]` | Début/Fin séquence non-imprimable (couleurs) | |

**Couleurs** :
```bash
# Format couleur
\[\033[XXm\]texte\[\033[0m\]
#       ^^           ^^^^^
#       Code         Reset

# Codes couleurs texte
30 Noir     31 Rouge    32 Vert     33 Jaune
34 Bleu     35 Magenta  36 Cyan     37 Blanc

# Codes couleurs fond
40 Noir     41 Rouge    42 Vert     43 Jaune
44 Bleu     45 Magenta  46 Cyan     47 Blanc

# Attributs
0 Reset     1 Gras      4 Souligné  7 Inversé
```

**Exemples PS1** :
```bash
# Simple
PS1="\u@\h:\w\$ "
# alice@server:/home/alice$

# Avec couleurs (vert user@host, bleu chemin)
PS1="\[\033[32m\]\u@\h\[\033[0m\]:\[\033[34m\]\w\[\033[0m\]\$ "
# alice@server:/home/alice$  (coloré)

# Multi-ligne avec date
PS1="[\d \t] \u@\h\n\w\$ "
# [Mon Feb 10 14:35:20] alice@server
# /home/alice$

# Fancy (couleur différente si root)
if [ "$EUID" -eq 0 ]; then
    PS1="\[\033[31m\]\u@\h\[\033[0m\]:\[\033[34m\]\w\[\033[0m\]# "  # Rouge si root
else
    PS1="\[\033[32m\]\u@\h\[\033[0m\]:\[\033[34m\]\w\[\033[0m\]\$ "  # Vert si user
fi

# Avec code retour dernière commande
PS1="\[\033[32m\]\u@\h\[\033[0m\]:\[\033[34m\]\w\[\033[0m\] [\$?]\$ "
# alice@server:/home/alice [0]$  (0 = succès)
# alice@server:/home/alice [127]$  (erreur)

# Git branch (avancé)
parse_git_branch() {
    git branch 2>/dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/(\1)/'
}
PS1="\u@\h:\w \[\033[33m\]\$(parse_git_branch)\[\033[0m\]\$ "
# alice@server:/project (main)$
```

**Autres variables PS** :
```bash
PS2="> "  # Prompt continuation (commande multi-ligne)
PS3="#? "  # Prompt select (menus)
PS4="+ "  # Prompt debug (set -x)
```

**Persister PS1** :
```bash
# Ajouter à ~/.bashrc
echo 'PS1="\[\033[32m\]\u@\h\[\033[0m\]:\[\033[34m\]\w\[\033[0m\]\$ "' >> ~/.bashrc

source ~/.bashrc
```

---

## 📁 Fichiers de Configuration Importants

### /etc/profile - Configuration Système Globale (Login)

**Rôle** : Chargé pour **tous les utilisateurs** lors **login shells**

**Contenu typique** :
```bash
cat /etc/profile
```

```bash
# /etc/profile: system-wide .profile file for Bash

# Définir PATH système
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"

# Charger scripts dans /etc/profile.d/
if [ -d /etc/profile.d ]; then
    for script in /etc/profile.d/*.sh; do
        if [ -r "$script" ]; then
            . "$script"
        fi
    done
    unset script
fi

# Variables d'environnement système
export PATH
export EDITOR=nano
export PAGER=less

# umask par défaut
umask 022
```

**⚠️ Ne PAS modifier directement** : Créer fichier dans `/etc/profile.d/` à la place

---

### /etc/profile.d/ - Scripts de Configuration Modulaires

**Rôle** : Scripts chargés par `/etc/profile` (modularité)

**Exemples** :
```bash
ls /etc/profile.d/
# bash_completion.sh
# lang.sh
# myapp.sh
# vte.sh

# Créer configuration personnalisée système
cat > /etc/profile.d/custom.sh <<'EOF'
# Configuration personnalisée globale
export MYAPP_HOME="/opt/myapp"
export PATH="$MYAPP_HOME/bin:$PATH"

# Fonction disponible pour tous
hello() {
    echo "Hello from global profile"
}
EOF

# Tester
su - alice
hello
# Hello from global profile
```

---

### ~/.bash_profile, ~/.bash_login, ~/.profile - Config Utilisateur (Login)

**Rôle** : Configuration **personnelle** utilisateur pour **login shells**

**⚠️ Priorité** : Seulement le **premier trouvé** est chargé :
1. `~/.bash_profile`
2. `~/.bash_login`
3. `~/.profile`

**Contenu typique ~/.bash_profile** :
```bash
cat ~/.bash_profile
```

```bash
# ~/.bash_profile: Personal environment for login shells

# Charger ~/.bashrc (important !)
if [ -f ~/.bashrc ]; then
    . ~/.bashrc
fi

# Variables personnelles
export PATH="$HOME/bin:$HOME/.local/bin:$PATH"
export EDITOR=vim
export VISUAL=vim

# Configuration SSH agent
if [ -z "$SSH_AUTH_SOCK" ]; then
    eval "$(ssh-agent -s)"
    ssh-add ~/.ssh/id_rsa 2>/dev/null
fi

# Message de bienvenue
echo "Bienvenue $(whoami) sur $(hostname)"
date
```

**⚠️ Différence avec ~/.profile** :
- `~/.bash_profile` : Spécifique à **Bash**
- `~/.profile` : Compatible **POSIX** (sh, dash, bash)

---

### ~/.bashrc - Configuration Utilisateur (Toujours)

**Rôle** : Configuration **personnelle** pour **tous les shells interactifs** (login et non-login)

**⚠️ FICHIER LE PLUS IMPORTANT** : À personnaliser en priorité

**Contenu typique** :
```bash
cat ~/.bashrc
```

```bash
# ~/.bashrc: Personal configuration for interactive shells

# Si non-interactif, arrêter
[[ $- != *i* ]] && return

# Historique
HISTSIZE=10000
HISTFILESIZE=20000
HISTCONTROL=ignoredups:erasedups
shopt -s histappend  # Append (pas overwrite)

# Options shell
shopt -s checkwinsize  # Mettre à jour LINES et COLUMNS
shopt -s cdspell      # Corriger typos cd

# Alias
alias ls='ls --color=auto'
alias ll='ls -la'
alias grep='grep --color=auto'
alias ..='cd ..'
alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'

# Fonctions
mkcd() {
    mkdir -p "$1" && cd "$1"
}

extract() {
    case "$1" in
        *.tar.gz)  tar xzf "$1";;
        *.zip)     unzip "$1";;
        *.rar)     unrar x "$1";;
        *)         echo "Format non supporté";;
    esac
}

# Prompt personnalisé
PS1='\[\033[32m\]\u@\h\[\033[0m\]:\[\033[34m\]\w\[\033[0m\]\$ '

# Complétion bash
if [ -f /etc/bash_completion ]; then
    . /etc/bash_completion
fi

# Variables locales (non exportées)
MY_LOCAL_VAR="local value"
```

---

### ~/.bash_logout - Nettoyage au Déconnexion

**Rôle** : Exécuté lors de la **déconnexion** d'un **login shell**

**Contenu typique** :
```bash
cat ~/.bash_logout
```

```bash
# ~/.bash_logout: Personal cleanup on logout

# Effacer écran (confidentialité)
clear

# Supprimer historique (si nécessaire)
# history -c

# Déconnecter SSH agent
# if [ -n "$SSH_AGENT_PID" ]; then
#     eval "$(ssh-agent -k)"
# fi

# Message
echo "À bientôt, $(whoami) !"
```

---

### /etc/bash.bashrc - Configuration Système (Non-Login)

**Rôle** : Configuration **système** pour **shells non-login interactifs**

**Contenu typique** :
```bash
cat /etc/bash.bashrc
# (similaire à /etc/profile mais pour non-login)

# PS1 système
PS1='\u@\h:\w\$ '

# Alias système
alias ls='ls --color=auto'

# Chargement bash_completion
if [ -f /usr/share/bash-completion/bash_completion ]; then
    . /usr/share/bash-completion/bash_completion
fi
```

---

### /etc/skel/ - Template Nouveaux Utilisateurs

**Rôle** : Squelette copié vers `/home/newuser/` lors création utilisateur

**Contenu** :
```bash
ls -la /etc/skel/
# -rw-r--r-- 1 root root  220 .bash_logout
# -rw-r--r-- 1 root root 3526 .bashrc
# -rw-r--r-- 1 root root  807 .profile

# Créer utilisateur
useradd -m alice
# → Copie /etc/skel/* vers /home/alice/

ls -la /home/alice/
# -rw-r--r-- 1 alice alice  220 .bash_logout
# -rw-r--r-- 1 alice alice 3526 .bashrc
# -rw-r--r-- 1 alice alice  807 .profile
```

**Personnaliser skel** :
```bash
# Ajouter fichier template
cat > /etc/skel/.bash_aliases <<'EOF'
alias ll='ls -la'
alias update='sudo apt update && sudo apt upgrade'
EOF

# Nouveaux users auront .bash_aliases par défaut
```

---

## 🎓 10 Scénarios Pratiques Complets

### Scénario 1️⃣ : Configurer environnement développement Python

**Contexte** : Développeur Python, configurer PATH, virtualenv, alias.

```bash
# 1. Créer ~/.bash_profile (si n'existe pas)
cat > ~/.bash_profile <<'EOF'
# Charger ~/.bashrc
if [ -f ~/.bashrc ]; then
    . ~/.bashrc
fi

# Python personnalisé
export PATH="$HOME/.local/bin:$PATH"
export PYTHONPATH="$HOME/python_modules:$PYTHONPATH"

# Virtualenv par défaut
export WORKON_HOME="$HOME/.virtualenvs"
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3

# Charger virtualenvwrapper (si installé)
if [ -f /usr/local/bin/virtualenvwrapper.sh ]; then
    source /usr/local/bin/virtualenvwrapper.sh
fi
EOF

# 2. Ajouter alias dans ~/.bashrc
cat >> ~/.bashrc <<'EOF'

# Alias Python
alias py='python3'
alias pip='pip3'
alias venv='python3 -m venv'
alias activate='source venv/bin/activate'

# Fonction créer + activer venv
mkvenv() {
    python3 -m venv "$1"
    source "$1/bin/activate"
}
EOF

# 3. Recharger configuration
source ~/.bash_profile

# 4. Tester
py --version
# Python 3.11.7

which py
# alias py='python3'

# 5. Créer virtualenv
mkvenv myproject_env
# (venv activé automatiquement)

pip list
# Package    Version
# ---------- -------
# pip        23.0.1
# setuptools 65.5.0
```

---

### Scénario 2️⃣ : Différencier prompt user/root avec couleurs

**Contexte** : Éviter erreurs, prompt distinct user (vert) vs root (rouge).

```bash
# 1. Configuration ~/.bashrc (utilisateur)
cat >> ~/.bashrc <<'EOF'

# Prompt coloré utilisateur (vert)
if [ "$EUID" -ne 0 ]; then
    PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
fi
EOF

# 2. Configuration /root/.bashrc (root)
sudo bash -c 'cat >> /root/.bashrc <<EOF

# Prompt coloré root (rouge + #)
if [ "$EUID" -eq 0 ]; then
    PS1='\[\033[01;31m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]# '
fi
EOF'

# 3. Tester utilisateur
source ~/.bashrc
# alice@server:/home/alice$  (vert)

# 4. Tester root
sudo su -
# root@server:/root#  (rouge)

# 5. Version avancée avec code retour
cat >> ~/.bashrc <<'EOF'

# Prompt avec indicateur erreur
PROMPT_COMMAND='ret=$?; if [ $ret -ne 0 ]; then echo -e "\[\033[01;31m\]✗ Erreur: $ret\[\033[00m\]"; fi'
PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
EOF

source ~/.bashrc

# Commande échoue
ls /nonexistent
# ✗ Erreur: 2
# alice@server:/home/alice$
```

---

### Scénario 3️⃣ : Script nécessitant variables d'environnement spécifiques

**Contexte** : Script backup.sh nécessite config dans fichier séparé.

```bash
# 1. Créer fichier config
cat > ~/.backup_env <<'EOF'
# Configuration backup
export BACKUP_DIR="/mnt/backups"
export BACKUP_RETENTION=30
export BACKUP_EXCLUDE="/tmp /var/cache"
export BACKUP_LOG="/var/log/backup.log"
EOF

# 2. Créer script backup
cat > ~/backup.sh <<'EOF'
#!/bin/bash
set -euo pipefail

# Charger configuration
if [ -f "$HOME/.backup_env" ]; then
    source "$HOME/.backup_env"
else
    echo "Erreur: Config manquante (~/.backup_env)"
    exit 1
fi

# Vérifier variables
: ${BACKUP_DIR:?Variable BACKUP_DIR non définie}
: ${BACKUP_RETENTION:?Variable BACKUP_RETENTION non définie}

# Créer backup
echo "Backup vers $BACKUP_DIR..."
tar czf "$BACKUP_DIR/backup-$(date +%Y%m%d).tar.gz" \
    --exclude="$BACKUP_EXCLUDE" \
    /home/

# Nettoyer anciens backups
find "$BACKUP_DIR" -name "backup-*.tar.gz" -mtime +$BACKUP_RETENTION -delete

echo "Backup terminé : $BACKUP_DIR/backup-$(date +%Y%m%d).tar.gz"
EOF

chmod +x ~/backup.sh

# 3. Exécuter
~/backup.sh
# Backup vers /mnt/backups...
# Backup terminé : /mnt/backups/backup-20260210.tar.gz

# 4. Alternative : BASH_ENV pour non-interactive
export BASH_ENV="$HOME/.backup_env"
bash ~/backup.sh
# Variables chargées automatiquement

# 5. Cron (env isolé)
crontab -e
# 0 2 * * * bash -c 'source $HOME/.backup_env && $HOME/backup.sh'
```

---

### Scénario 4️⃣ : Personnaliser shell pour tous les utilisateurs

**Contexte** : Serveur multi-utilisateurs, alias et fonctions communs.

```bash
# 1. Créer script global
cat > /etc/profile.d/company-aliases.sh <<'EOF'
# Alias entreprise (tous utilisateurs)

# Sécurité
alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'

# Monitoring
alias checkdisk='df -h | grep -v tmpfs'
alias checkmem='free -h'
alias top='htop'

# Navigation
alias ll='ls -la --color=auto'
alias lh='ls -lh --color=auto'

# Fonction commune
serviceup() {
    systemctl is-active "$1" >/dev/null 2>&1 && echo "✓ $1 actif" || echo "✗ $1 inactif"
}

# Message du jour
export COMPANY_MSG="Serveur Production - Environnement critique"
EOF

chmod +x /etc/profile.d/company-aliases.sh

# 2. Tester nouveau login
su - alice
ll
# total 56
# drwxr-xr-x 5 alice alice 4096 Feb 10 15:00 .
# ...

serviceup ssh
# ✓ ssh actif

# 3. Personnaliser message login (/etc/motd)
cat > /etc/motd <<'EOF'
╔═══════════════════════════════════════╗
║   Serveur Production - Company XYZ   ║
║   Support: support@company.com       ║
╚═══════════════════════════════════════╝
EOF

# 4. Afficher à chaque login (dans /etc/profile)
echo 'cat /etc/motd' >> /etc/profile

# 5. Tester
ssh alice@server
# ╔═══════════════════════════════════════╗
# ║   Serveur Production - Company XYZ   ║
# ...
```

---

### Scénario 5️⃣ : Debugger quel fichier config est chargé

**Contexte** : Variable mystérieuse définie quelque part, trouver source.

```bash
# 1. Ajouter traces dans fichiers config
for file in /etc/profile /etc/bash.bashrc ~/.bash_profile ~/.bashrc; do
    if [ -f "$file" ]; then
        echo "echo '[TRACE] Chargement de $file'" | sudo tee -a "$file" >/dev/null
    fi
done

# 2. Nouveau login shell
su - alice
# [TRACE] Chargement de /etc/profile
# [TRACE] Chargement de /home/alice/.bash_profile
# [TRACE] Chargement de /home/alice/.bashrc

# 3. Nouveau non-login shell
bash
# [TRACE] Chargement de /etc/bash.bashrc
# [TRACE] Chargement de /home/alice/.bashrc

# 4. Chercher où variable est définie
grep -r "MY_MYSTERY_VAR" /etc/profile /etc/profile.d/ ~/.bash* 2>/dev/null
# /etc/profile.d/custom.sh:export MY_MYSTERY_VAR="found!"

# 5. Méthode alternative : set -x
bash -x -l
# + source /etc/profile
# ++ export MY_MYSTERY_VAR=found
# ++ MY_MYSTERY_VAR=found
# ...

# 6. Nettoyer traces
for file in /etc/profile /etc/bash.bashrc ~/.bash_profile ~/.bashrc; do
    if [ -f "$file" ]; then
        sudo sed -i '/\[TRACE\]/d' "$file"
    fi
done
```

---

### Scénario 6️⃣ : Configuration SSH avec ForwardAgent

**Contexte** : Utiliser clés SSH locales sur serveur distant (agent forwarding).

```bash
# 1. Configuration ~/.bash_profile (local)
cat >> ~/.bash_profile <<'EOF'

# SSH Agent automatique
if [ -z "$SSH_AUTH_SOCK" ]; then
    # Vérifier agent existant
    if [ -f ~/.ssh/ssh-agent.env ]; then
        source ~/.ssh/ssh-agent.env > /dev/null
        if ! kill -0 $SSH_AGENT_PID 2>/dev/null; then
            # Agent mort, en créer nouveau
            eval "$(ssh-agent -s)" > /dev/null
            echo "export SSH_AUTH_SOCK=$SSH_AUTH_SOCK" > ~/.ssh/ssh-agent.env
            echo "export SSH_AGENT_PID=$SSH_AGENT_PID" >> ~/.ssh/ssh-agent.env
        fi
    else
        # Pas d'agent, en créer
        eval "$(ssh-agent -s)" > /dev/null
        echo "export SSH_AUTH_SOCK=$SSH_AUTH_SOCK" > ~/.ssh/ssh-agent.env
        echo "export SSH_AGENT_PID=$SSH_AGENT_PID" >> ~/.ssh/ssh-agent.env
    fi
    
    # Ajouter clés
    ssh-add ~/.ssh/id_rsa 2>/dev/null
    ssh-add ~/.ssh/id_ed25519 2>/dev/null
fi
EOF

# 2. ~/.bash_logout (arrêter agent)
cat >> ~/.bash_logout <<'EOF'

# Arrêter SSH agent
if [ -n "$SSH_AGENT_PID" ]; then
    eval "$(ssh-agent -k)" > /dev/null
    rm -f ~/.ssh/ssh-agent.env
fi
EOF

# 3. Configuration SSH client (~/.ssh/config)
cat >> ~/.ssh/config <<'EOF'
Host production-server
    HostName 192.168.1.100
    User alice
    ForwardAgent yes
    IdentityFile ~/.ssh/id_rsa
EOF

# 4. Tester
source ~/.bash_profile
ssh-add -l
# 2048 SHA256:... /home/alice/.ssh/id_rsa (RSA)

# 5. Connexion avec forwarding
ssh production-server

# Sur serveur distant
ssh-add -l
# 2048 SHA256:... /home/alice/.ssh/id_rsa (RSA)
# ✅ Clés locales accessibles sur serveur distant

# Accéder à GitHub depuis serveur distant (sans copier clés)
git clone git@github.com:user/repo.git
# ✅ Utilise clés locales via agent forwarding
```

---

### Scénario 7️⃣ : Gérer multiples environnements (dev/staging/prod)

**Contexte** : Configurations différentes selon environnement.

```bash
# 1. Créer fichiers config par environnement
mkdir -p ~/.config/envs

cat > ~/.config/envs/dev.env <<'EOF'
export ENV_NAME="development"
export DB_HOST="localhost"
export DB_PORT="5432"
export API_URL="http://localhost:8000"
export LOG_LEVEL="DEBUG"
export PS1='\[\033[01;32m\][DEV]\[\033[00m\] \u@\h:\w\$ '
EOF

cat > ~/.config/envs/staging.env <<'EOF'
export ENV_NAME="staging"
export DB_HOST="staging-db.company.com"
export DB_PORT="5432"
export API_URL="https://staging-api.company.com"
export LOG_LEVEL="INFO"
export PS1='\[\033[01;33m\][STAGING]\[\033[00m\] \u@\h:\w\$ '
EOF

cat > ~/.config/envs/prod.env <<'EOF'
export ENV_NAME="production"
export DB_HOST="prod-db.company.com"
export DB_PORT="5432"
export API_URL="https://api.company.com"
export LOG_LEVEL="WARNING"
export PS1='\[\033[01;31m\][PROD]\[\033[00m\] \u@\h:\w\$ '
EOF

# 2. Créer fonction de bascule
cat >> ~/.bashrc <<'EOF'

# Fonction charger environnement
loadenv() {
    local env_file="$HOME/.config/envs/$1.env"
    
    if [ ! -f "$env_file" ]; then
        echo "Environnement '$1' non trouvé"
        echo "Disponibles: dev, staging, prod"
        return 1
    fi
    
    # Décharger ancien env
    unset ENV_NAME DB_HOST DB_PORT API_URL LOG_LEVEL
    
    # Charger nouveau
    source "$env_file"
    echo "✓ Environnement chargé: $ENV_NAME"
}

# Alias
alias dev='loadenv dev'
alias staging='loadenv staging'
alias prod='loadenv prod'
EOF

source ~/.bashrc

# 3. Utiliser
dev
# ✓ Environnement chargé: development
# [DEV] alice@laptop:~$

echo $DB_HOST
# localhost

echo $API_URL
# http://localhost:8000

# 4. Basculer prod
prod
# ✓ Environnement chargé: production
# [PROD] alice@laptop:~$

echo $DB_HOST
# prod-db.company.com

# 5. Script utilisant env
cat > deploy.sh <<'EOF'
#!/bin/bash
: ${ENV_NAME:?Erreur: Charger env d'abord (dev/staging/prod)}

echo "Déploiement vers $ENV_NAME"
echo "API: $API_URL"
echo "DB: $DB_HOST:$DB_PORT"
# ... logique déploiement
EOF

chmod +x deploy.sh

dev
./deploy.sh
# Déploiement vers development
# API: http://localhost:8000
# DB: localhost:5432
```

---

### Scénario 8️⃣ : Alias conditionnel selon OS

**Contexte** : ~/.bashrc partagé entre Linux et macOS (Dropbox/Git).

```bash
# ~/.bashrc portable
cat >> ~/.bashrc <<'EOF'

# Détection OS
case "$(uname -s)" in
    Linux*)
        OS_TYPE="Linux"
        ;;
    Darwin*)
        OS_TYPE="macOS"
        ;;
    CYGWIN*|MINGW*)
        OS_TYPE="Windows"
        ;;
    *)
        OS_TYPE="Unknown"
        ;;
esac

# Alias selon OS
if [ "$OS_TYPE" = "Linux" ]; then
    alias ls='ls --color=auto'
    alias ll='ls -la --color=auto'
    alias open='xdg-open'
    alias pbcopy='xclip -selection clipboard'
    alias pbpaste='xclip -selection clipboard -o'
    alias update='sudo apt update && sudo apt upgrade'
    
elif [ "$OS_TYPE" = "macOS" ]; then
    alias ls='ls -G'
    alias ll='ls -laG'
    # open déjà présent sur macOS
    # pbcopy/pbpaste déjà présents
    alias update='brew update && brew upgrade'
    
fi

# Alias universels
alias h='history'
alias c='clear'
alias grep='grep --color=auto'

# Fonction portable
extract() {
    if [ -f "$1" ]; then
        case "$1" in
            *.tar.gz)  tar xzf "$1";;
            *.tar.bz2) tar xjf "$1";;
            *.zip)     unzip "$1";;
            *.rar)     
                if [ "$OS_TYPE" = "macOS" ]; then
                    unar "$1"  # macOS
                else
                    unrar x "$1"  # Linux
                fi
                ;;
            *)         echo "Format non supporté: $1";;
        esac
    else
        echo "Fichier non trouvé: $1"
    fi
}

# Prompt avec OS
PS1="[$OS_TYPE] \u@\h:\w\$ "
EOF

# Tester
source ~/.bashrc

# Linux
# [Linux] alice@server:~$

# macOS
# [macOS] alice@macbook:~$

ll
# Fonctionne sur les deux (couleurs adaptées)

update
# Linux: apt update && apt upgrade
# macOS: brew update && brew upgrade
```

---

### Scénario 9️⃣ : Configuration pour scripts cron (non-interactive)

**Contexte** : Script cron nécessite variables d'environnement.

```bash
# 1. Créer fichier env pour cron
cat > ~/.cron_env <<'EOF'
# Variables pour scripts cron
PATH=/usr/local/bin:/usr/bin:/bin
SHELL=/bin/bash
HOME=/home/alice
USER=alice

# Variables personnalisées
BACKUP_DIR=/mnt/backups
LOG_DIR=/var/log/myapp
NOTIFY_EMAIL=alice@company.com
EOF

# 2. Script utilisant variables
cat > ~/cron_backup.sh <<'EOF'
#!/bin/bash
set -euo pipefail

# Charger env (car cron n'a pas env normal)
if [ -f "$HOME/.cron_env" ]; then
    set -a  # Auto-export toutes variables
    source "$HOME/.cron_env"
    set +a
fi

# Vérifier variables
: ${BACKUP_DIR:?BACKUP_DIR non défini}
: ${LOG_DIR:?LOG_DIR non défini}

# Logging
LOG_FILE="$LOG_DIR/backup-$(date +%Y%m%d).log"
exec > >(tee -a "$LOG_FILE") 2>&1

echo "[$(date)] Début backup"

# Backup
tar czf "$BACKUP_DIR/backup-$(date +%Y%m%d_%H%M%S).tar.gz" \
    /home/alice/documents/ \
    2>&1

echo "[$(date)] Backup terminé"

# Notification (si erreur)
if [ $? -ne 0 ]; then
    echo "Backup échoué" | mail -s "Erreur Backup" "$NOTIFY_EMAIL"
fi
EOF

chmod +x ~/cron_backup.sh

# 3. Tester manuellement
~/cron_backup.sh
# [Mon Feb 10 15:30:00 2026] Début backup
# [Mon Feb 10 15:30:15 2026] Backup terminé

# 4. Configurer cron
crontab -e

# Option 1 : Charger env dans crontab
# SHELL=/bin/bash
# 0 2 * * * source $HOME/.cron_env && $HOME/cron_backup.sh

# Option 2 : BASH_ENV (automatique pour bash)
# BASH_ENV=/home/alice/.cron_env
# 0 2 * * * $HOME/cron_backup.sh

# Option 3 : Dans script (déjà fait avec source)
# 0 2 * * * $HOME/cron_backup.sh

# 5. Vérifier exécution cron
grep CRON /var/log/syslog | tail
# Feb 10 02:00:01 server CRON[12345]: (alice) CMD (/home/alice/cron_backup.sh)

cat /var/log/myapp/backup-20260210.log
# [Tue Feb 10 02:00:01 2026] Début backup
# [Tue Feb 10 02:00:18 2026] Backup terminé
```

---

### Scénario 🔟 : Isoler environnement pour compilation

**Contexte** : Compiler programme avec toolchain spécifique (sans polluer env global).

```bash
# 1. Créer environnement compilation isolé
cat > ~/build_env.sh <<'EOF'
# Environnement de compilation isolé

# Sauvegarder env actuel
export OLD_PATH="$PATH"
export OLD_LD_LIBRARY_PATH="$LD_LIBRARY_PATH"
export OLD_PS1="$PS1"

# Toolchain personnalisé
export TOOLCHAIN_ROOT="/opt/toolchain"
export PATH="$TOOLCHAIN_ROOT/bin:$PATH"
export LD_LIBRARY_PATH="$TOOLCHAIN_ROOT/lib:$LD_LIBRARY_PATH"
export CC="$TOOLCHAIN_ROOT/bin/gcc"
export CXX="$TOOLCHAIN_ROOT/bin/g++"
export CFLAGS="-O2 -march=native"
export CXXFLAGS="$CFLAGS"

# Prompt différent
export PS1='[BUILD] \u@\h:\w\$ '

echo "✓ Environnement de compilation chargé"
echo "  CC: $CC"
echo "  PATH: $PATH"
EOF

# 2. Créer fonction de restauration
cat > ~/restore_env.sh <<'EOF'
# Restaurer environnement

export PATH="$OLD_PATH"
export LD_LIBRARY_PATH="$OLD_LD_LIBRARY_PATH"
export PS1="$OLD_PS1"

unset TOOLCHAIN_ROOT CC CXX CFLAGS CXXFLAGS
unset OLD_PATH OLD_LD_LIBRARY_PATH OLD_PS1

echo "✓ Environnement restauré"
EOF

# 3. Utiliser
source ~/build_env.sh
# ✓ Environnement de compilation chargé
#   CC: /opt/toolchain/bin/gcc
#   PATH: /opt/toolchain/bin:/usr/bin:/bin
# [BUILD] alice@server:~$

which gcc
# /opt/toolchain/bin/gcc

gcc --version
# gcc (Custom Toolchain) 13.2.0

# 4. Compiler
cd ~/myproject
./configure
make
# Utilise toolchain isolé

# 5. Restaurer
source ~/restore_env.sh
# ✓ Environnement restauré
# alice@server:~$

which gcc
# /usr/bin/gcc

# 6. Alternative : Sous-shell isolé (auto-restauration)
cat > ~/build_isolated.sh <<'EOF'
#!/bin/bash
# Sous-shell isolé (env perdu après)

export PATH="/opt/toolchain/bin:$PATH"
export CC="/opt/toolchain/bin/gcc"

echo "Building dans environnement isolé..."
cd ~/myproject
./configure
make

echo "Build terminé, env restauré automatiquement"
EOF

chmod +x ~/build_isolated.sh
./build_isolated.sh
# (env modifié seulement dans sous-shell)
```

---

## 🚨 10 Erreurs Courantes et Solutions

### Erreur 1️⃣ : Variable non exportée, invisible dans script enfant

**Symptôme** :
```bash
# Définir variable
MY_VAR="valeur"

# Script enfant
bash -c 'echo $MY_VAR'
# (vide)
```

**Cause** : Variable **locale** (non exportée) → non héritée par processus enfants.

**Solutions** :
```bash
# 1. Exporter variable
export MY_VAR="valeur"

bash -c 'echo $MY_VAR'
# valeur  ✅

# 2. Export + définition (une ligne)
export MY_VAR="valeur"

# 3. Passer variable directement
env MY_VAR="valeur" bash -c 'echo $MY_VAR'
# valeur

# 4. Dans script, toujours exporter
cat > script.sh <<'EOF'
#!/bin/bash
MY_VAR="test"  # Local
export MY_VAR  # Exporter pour enfants
./child_script.sh  # Héritera MY_VAR
EOF

# 5. set -a : Auto-export toutes variables
set -a
MY_VAR1="val1"
MY_VAR2="val2"
# Automatiquement exportées
set +a
```

---

### Erreur 2️⃣ : Modifications ~/.bashrc non appliquées

**Symptôme** :
```bash
# Modifier ~/.bashrc
echo 'alias ll="ls -la"' >> ~/.bashrc

# Tester
ll
# bash: ll: command not found
```

**Cause** : Modifications non rechargées dans session actuelle.

**Solutions** :
```bash
# 1. Recharger ~/.bashrc
source ~/.bashrc
# ou
. ~/.bashrc

ll
# total 48...  ✅

# 2. Nouveau shell (charge automatiquement)
bash

ll  # ✅ Fonctionne

# 3. Nouveau login (si login shell)
exit
# Re-login via SSH
ll  # ✅ Fonctionne

# 4. Vérifier syntaxe avant recharger
bash -n ~/.bashrc
# (aucune sortie = OK)

# Si erreur syntaxe :
# bash: ~/.bashrc: line 45: unexpected EOF

# 5. Debugger chargement
bash -x ~/.bashrc
# + alias ll='ls -la'
# ...
```

---

### Erreur 3️⃣ : ~/.bash_profile existe, ~/.profile ignoré

**Symptôme** :
```bash
# Ajouter config dans ~/.profile
echo 'export MY_VAR="test"' >> ~/.profile

# Login shell
su - alice
echo $MY_VAR
# (vide)
```

**Cause** : `~/.bash_profile` existe → `~/.profile` **jamais chargé** (priorité).

**Solutions** :
```bash
# 1. Vérifier fichiers existants
ls -la ~/ | grep -E "bash_profile|bash_login|profile"
# -rw-r--r-- 1 alice alice  220 .bash_profile  ← Existe
# -rw-r--r-- 1 alice alice  807 .profile        ← Ignoré !

# 2. Option A : Supprimer ~/.bash_profile (utiliser ~/.profile)
rm ~/.bash_profile

# 3. Option B : ~/.bash_profile charge ~/.profile
cat > ~/.bash_profile <<'EOF'
# Charger ~/.profile (compatibilité)
if [ -f ~/.profile ]; then
    . ~/.profile
fi

# Charger ~/.bashrc
if [ -f ~/.bashrc ]; then
    . ~/.bashrc
fi
EOF

# 4. Option C : Mettre config dans ~/.bash_profile directement
echo 'export MY_VAR="test"' >> ~/.bash_profile

# 5. Tester
su - alice
echo $MY_VAR
# test  ✅
```

---

### Erreur 4️⃣ : Alias non disponible dans script

**Symptôme** :
```bash
# Alias dans ~/.bashrc
alias ll='ls -la'

# Script
cat > test.sh <<'EOF'
#!/bin/bash
ll  # Utiliser alias
EOF

chmod +x test.sh
./test.sh
# ./test.sh: line 2: ll: command not found
```

**Cause** : Scripts = **non-interactive** → `~/.bashrc` non chargé, alias indisponibles.

**Solutions** :
```bash
# 1. Charger ~/.bashrc explicitement (si interactif)
cat > test.sh <<'EOF'
#!/bin/bash
if [ -f ~/.bashrc ]; then
    source ~/.bashrc
fi
ll
EOF

./test.sh
# total 48...  ✅

# 2. Utiliser commande réelle (recommandé scripts)
cat > test.sh <<'EOF'
#!/bin/bash
ls -la  # Pas d'alias
EOF

# 3. Définir alias dans script
cat > test.sh <<'EOF'
#!/bin/bash
shopt -s expand_aliases  # Activer alias dans script
alias ll='ls -la'
ll
EOF

# 4. Function à la place d'alias (meilleur)
cat > test.sh <<'EOF'
#!/bin/bash
ll() {
    ls -la "$@"
}
ll /home
EOF

# 5. BASH_ENV (pour tous scripts)
cat > ~/.bash_aliases <<'EOF'
alias ll='ls -la'
EOF

export BASH_ENV=~/.bash_aliases

cat > test.sh <<'EOF'
#!/bin/bash
ll
EOF

./test.sh
# (fonctionne si expand_aliases activé)
```

---

### Erreur 5️⃣ : PATH modifié perdu au reboot

**Symptôme** :
```bash
# Modifier PATH
export PATH="/opt/myapp/bin:$PATH"

which myapp
# /opt/myapp/bin/myapp  ✅

# Reboot
# ...

which myapp
# (non trouvé)
```

**Cause** : Modification **temporaire** (session uniquement), non persistante.

**Solutions** :
```bash
# 1. Ajouter à ~/.bash_profile (login shells)
echo 'export PATH="/opt/myapp/bin:$PATH"' >> ~/.bash_profile

# 2. Ajouter à ~/.bashrc (tous shells interactifs)
echo 'export PATH="/opt/myapp/bin:$PATH"' >> ~/.bashrc

# 3. Système global (/etc/profile.d/)
cat > /etc/profile.d/myapp.sh <<'EOF'
export PATH="/opt/myapp/bin:$PATH"
EOF

# 4. /etc/environment (système, tous utilisateurs)
echo 'PATH="/opt/myapp/bin:/usr/local/bin:/usr/bin:/bin"' | sudo tee -a /etc/environment

# 5. Vérifier après reboot
reboot
# ...
which myapp
# /opt/myapp/bin/myapp  ✅
```

---

### Erreur 6️⃣ : PS1 casse (prompt bizarre après commande)

**Symptôme** :
```bash
# Définir PS1 avec couleurs
export PS1='\033[32m\u@\h:\w\$ \033[0m'

# Taper commande longue, utiliser ← (flèche gauche)
# Curseur position incorrecte, texte écrasé
```

**Cause** : Séquences couleur **non-imprimables** comptées comme caractères → calcul largeur incorrect.

**Solutions** :
```bash
# 1. Entourer couleurs avec \[ \] (non-imprimables)
export PS1='\[\033[32m\]\u@\h:\w\$ \[\033[0m\]'
#            ^^          ^^      ^^ ^^        ^^
#            Marqueurs non-imprimables

# 2. Version lisible (variables)
COLOR_GREEN='\[\033[32m\]'
COLOR_RESET='\[\033[0m\]'
export PS1="${COLOR_GREEN}\u@\h:\w\$ ${COLOR_RESET}"

# 3. Tester
longcommandthatshouldwrapcorrectlynowwithproperescape
# (flèches fonctionnent correctement)

# 4. PS1 avancé (couleurs + Git)
cat >> ~/.bashrc <<'EOF'
# Couleurs
C_RED='\[\033[31m\]'
C_GREEN='\[\033[32m\]'
C_BLUE='\[\033[34m\]'
C_RESET='\[\033[0m\]'

# Prompt
PS1="${C_GREEN}\u@\h${C_RESET}:${C_BLUE}\w${C_RESET}\$ "
EOF

source ~/.bashrc
```

---

### Erreur 7️⃣ : Source d'un script au lieu de l'exécuter (pollution env)

**Symptôme** :
```bash
# Script modifiant variables
cat > script.sh <<'EOF'
#!/bin/bash
export TEMP_VAR="temporary"
cd /tmp
EOF

# Utiliser source au lieu de ./
source script.sh

# Variables et PWD modifiés dans shell actuel !
echo $TEMP_VAR
# temporary

pwd
# /tmp  ← Shell actuel changé !
```

**Cause** : `source` exécute dans **shell actuel** (pas de sous-shell) → modifications persistent.

**Solutions** :
```bash
# 1. Exécuter normalement (sous-shell)
./script.sh

echo $TEMP_VAR
# (vide)  ✅

pwd
# /home/alice  ✅ Inchangé

# 2. Utiliser source SEULEMENT pour config
# Fichiers config :
source ~/.bashrc  ✅
source ~/.bash_profile  ✅

# Scripts :
./script.sh  ✅
bash script.sh  ✅

# 3. Si script DOIT modifier env (activation venv)
source venv/bin/activate  ✅ Voulu
# Modifie PATH, PS1 dans shell actuel

# 4. Nettoyer après source accidentel
unset TEMP_VAR
cd ~
```

---

### Erreur 8️⃣ : Cron ne voit pas variables ~/.bashrc

**Symptôme** :
```bash
# Variable dans ~/.bashrc
echo 'export MY_APP_HOME="/opt/myapp"' >> ~/.bashrc

# Script cron
cat > cron_script.sh <<'EOF'
#!/bin/bash
echo "MY_APP_HOME: $MY_APP_HOME"
cd "$MY_APP_HOME/bin"
./myapp
EOF

chmod +x cron_script.sh

# Crontab
crontab -e
# */5 * * * * /home/alice/cron_script.sh >> /tmp/cron.log 2>&1

# Log après exécution
cat /tmp/cron.log
# MY_APP_HOME:
# ./cron_script.sh: line 3: cd: /bin: No such file or directory
```

**Cause** : Cron = **non-interactive**, `~/.bashrc` **non chargé** (seulement `$BASH_ENV`).

**Solutions** :
```bash
# 1. Charger ~/.bashrc dans script
cat > cron_script.sh <<'EOF'
#!/bin/bash
source $HOME/.bashrc  # Charger env
echo "MY_APP_HOME: $MY_APP_HOME"
cd "$MY_APP_HOME/bin"
./myapp
EOF

# 2. Définir variables dans script directement
cat > cron_script.sh <<'EOF'
#!/bin/bash
export MY_APP_HOME="/opt/myapp"
echo "MY_APP_HOME: $MY_APP_HOME"
cd "$MY_APP_HOME/bin"
./myapp
EOF

# 3. Utiliser BASH_ENV (fichier dédié cron)
cat > ~/.cron_env <<'EOF'
export MY_APP_HOME="/opt/myapp"
export PATH="/opt/myapp/bin:$PATH"
EOF

crontab -e
# BASH_ENV=/home/alice/.cron_env
# */5 * * * * /home/alice/cron_script.sh >> /tmp/cron.log 2>&1

# 4. Définir dans crontab directement
crontab -e
# MY_APP_HOME=/opt/myapp
# */5 * * * * /home/alice/cron_script.sh >> /tmp/cron.log 2>&1

# 5. Vérifier
cat /tmp/cron.log
# MY_APP_HOME: /opt/myapp  ✅
```

---

### Erreur 9️⃣ : Boucle infinie chargement config (source récursif)

**Symptôme** :
```bash
# ~/.bash_profile source ~/.bashrc
cat ~/.bash_profile
# source ~/.bashrc

# ~/.bashrc source ~/.bash_profile (erreur)
cat ~/.bashrc
# source ~/.bash_profile

# Login
su - alice
# (hang, CPU 100%, timeout)
```

**Cause** : **Boucle infinie** : `~/.bash_profile` → `~/.bashrc` → `~/.bash_profile` → ...

**Solutions** :
```bash
# 1. Corriger structure (standard)
cat > ~/.bash_profile <<'EOF'
# ~/.bash_profile charge ~/.bashrc (jamais l'inverse)
if [ -f ~/.bashrc ]; then
    source ~/.bashrc
fi

# Config spécifique login shell
export PATH="$HOME/bin:$PATH"
EOF

cat > ~/.bashrc <<'EOF'
# ~/.bashrc NE charge JAMAIS ~/.bash_profile

# Config tous shells interactifs
alias ll='ls -la'
PS1='\u@\h:\w\$ '
EOF

# 2. Règle : Hiérarchie claire
# ~/.bash_profile → ~/.bashrc  ✅
# ~/.bashrc → ~/.bash_profile  ❌ JAMAIS

# 3. Vérifier dépendances
grep -n "source\|^\." ~/.bash_profile ~/.bashrc
# Identifier sources récursifs

# 4. Garde contre récursion (avancé)
cat > ~/.bashrc <<'EOF'
# Garde contre chargement multiple
if [ -n "$BASHRC_LOADED" ]; then
    return
fi
export BASHRC_LOADED=1

# ... reste config
EOF
```

---

### Erreur 🔟 : Function masque commande système

**Symptôme** :
```bash
# Créer fonction ls
cat >> ~/.bashrc <<'EOF'
ls() {
    echo "Custom ls function"
}
EOF

source ~/.bashrc

ls
# Custom ls function

# Impossible utiliser vraie commande ls
```

**Cause** : Fonction **masque** commande système (priorité : fonction > alias > commande).

**Solutions** :
```bash
# 1. Utiliser commande réelle avec command
command ls
# file1.txt  file2.txt  ✅

# 2. Chemin complet
/bin/ls
# file1.txt  file2.txt

# 3. Backslash ignore fonction/alias
\ls
# file1.txt  file2.txt

# 4. Renommer fonction (meilleure pratique)
cat >> ~/.bashrc <<'EOF'
# Ne PAS masquer commandes système
myls() {  # Préfixe my
    echo "Custom listing"
    command ls -la "$@"
}
EOF

source ~/.bashrc

ls  # ✅ Commande système
myls  # ✅ Fonction personnalisée

# 5. Vérifier priorité
type ls
#
 is a function
# ls ()
# {
#     echo "Custom ls function"
# }

# 6. Supprimer fonction
unset -f ls

type ls
# ls is /bin/ls  ✅
```

---

## 📝 Antisèche (Cheat Sheet)

### Types de Shells

```bash
# Déterminer type shell actuel
echo $0                # -bash = login, bash = non-login
shopt -q login_shell && echo "Login" || echo "Non-login"
[[ $- == *i* ]] && echo "Interactive" || echo "Non-interactive"
```

### Séquence Chargement Fichiers

| Type Shell | Fichiers Chargés (ordre) |
|------------|--------------------------|
| **Interactive Login** | 1. `/etc/profile`<br>2. `/etc/profile.d/*.sh`<br>3. `~/.bash_profile` (ou `.bash_login` ou `.profile`)<br>4. `~/.bashrc`<br>Logout: `~/.bash_logout` |
| **Interactive Non-Login** | 1. `/etc/bash.bashrc`<br>2. `~/.bashrc` |
| **Non-Interactive** | `$BASH_ENV` (si défini)<br>Sinon aucun |

### Commandes Variables

```bash
# Afficher
env                    # Variables d'environnement
printenv VAR           # Variable spécifique
set                    # Toutes variables + fonctions
echo $VAR              # Variable spécifique

# Définir/Exporter
VAR="value"            # Variable locale
export VAR="value"     # Variable d'environnement
export VAR             # Exporter variable existante
export -p              # Lister variables exportées

# Supprimer
unset VAR              # Supprimer variable
unset -f func          # Supprimer fonction
export -n VAR          # Retirer export (garder local)

# Exécuter avec env modifié
env VAR=val cmd        # Ajouter variable
env -i cmd             # Env vide
env -u VAR cmd         # Supprimer variable
```

### Alias et Fonctions

```bash
# Alias
alias name='command'   # Créer
alias                  # Lister
unalias name           # Supprimer
unalias -a             # Supprimer tous

# Ignorer alias
\command               # Backslash
command command        # Builtin command
/bin/command           # Chemin complet

# Fonctions
func() { commands; }   # Définir
declare -f func        # Afficher définition
declare -F             # Lister noms fonctions
unset -f func          # Supprimer
```

### Source vs Exécution

```bash
source file            # Exécuter dans shell actuel
. file                 # Équivalent POSIX
bash file              # Exécuter dans sous-shell
./file                 # Exécuter dans sous-shell

# Cas d'usage
source ~/.bashrc       # Config (modifie shell actuel)
./script.sh            # Script (isolé, pas de pollution)
```

### PS1 - Prompt

```bash
# Codes courants
\u   # User
\h   # Hostname
\w   # Chemin complet
\W   # Basename
\$   # $ ou #
\t   # Heure HH:MM:SS

# Couleurs
\[\033[XXm\]text\[\033[0m\]
# XX: 31=rouge, 32=vert, 33=jaune, 34=bleu

# Exemple
PS1='\[\033[32m\]\u@\h\[\033[0m\]:\[\033[34m\]\w\[\033[0m\]\$ '
```

### Fichiers de Configuration

```bash
# Système (tous users)
/etc/profile           # Login shells
/etc/profile.d/*.sh    # Scripts modulaires
/etc/bash.bashrc       # Non-login interactive
/etc/environment       # Variables système

# Utilisateur (personnel)
~/.bash_profile        # Login (priorité 1)
~/.bash_login          # Login (priorité 2)
~/.profile             # Login (priorité 3)
~/.bashrc              # Tous interactive ⭐
~/.bash_logout         # Logout
~/.bash_history        # Historique

# Template nouveaux users
/etc/skel/             # Copié vers /home/newuser/
```

### Debugging

```bash
# Tracer chargement
bash -x                # Trace commandes
set -x                 # Activer trace
set +x                 # Désactiver

# Vérifier syntaxe
bash -n file           # Parse sans exécuter

# Type commande
type cmd               # Type (alias/function/builtin/file)
which cmd              # Chemin exécutable
whereis cmd            # Binaire + man + sources
```

---

## 🎯 Points Clés pour l'Examen LPIC-1

### ✅ Concepts à Maîtriser Absolument

1. **4 types de shells** :
   - **Interactive Login** : SSH, TTY, `su -` → `/etc/profile` + `~/.bash_profile` + `~/.bashrc`
   - **Interactive Non-Login** : Terminal GUI, `bash`, `su` → `/etc/bash.bashrc` + `~/.bashrc`
   - **Non-Interactive Login** : `ssh user@host "cmd"` → `/etc/profile` + `$BASH_ENV`
   - **Non-Interactive Non-Login** : Scripts, cron → Seulement `$BASH_ENV` (souvent vide)

2. **Séquence login shell** :
   - `/etc/profile` (tous users)
   - `/etc/profile.d/*.sh` (scripts modulaires)
   - **Premier trouvé** parmi : `~/.bash_profile` → `~/.bash_login` → `~/.profile`
   - `~/.bashrc` (si appelé depuis profil)
   - Logout : `~/.bash_logout`

3. **Différence env/printenv/set** :
   - `env` / `printenv` : Variables **exportées** uniquement
   - `set` : **Toutes** variables (locales + exportées + fonctions)

4. **export = héritage** :
   - Variable **locale** → sous-shell **ne voit pas**
   - Variable **exportée** → sous-shell **hérite**
   - `export VAR` ou `export VAR=value`

5. **source vs exécution** :
   - `source file` / `. file` : Exécute dans **shell actuel** (modifie env)
   - `./file` / `bash file` : Exécute dans **sous-shell** (isolé, env perdu après)

6. **Alias limitations** :
   - Disponibles seulement shells **interactifs**
   - Scripts **non-interactive** → alias indisponibles (sauf `shopt -s expand_aliases`)
   - Priorité : **fonction** > **alias** > **commande**
   - Ignorer : `\cmd`, `command cmd`, `/bin/cmd`

7. **PS1 couleurs** :
   - Entourer séquences couleur avec `\[` `\]` (non-imprimables)
   - Sinon calcul largeur prompt incorrect
   - `\[\033[32m\]text\[\033[0m\]`

8. **~/.bashrc = fichier principal** :
   - À personnaliser en priorité
   - Chargé par **tous shells interactifs** (login et non-login)
   - `~/.bash_profile` devrait **toujours** charger `~/.bashrc`

9. **Cron et env** :
   - Cron = **non-interactive** → `~/.bashrc` **NON chargé**
   - Solutions : `BASH_ENV`, définir vars dans script, ou `source ~/.bashrc` dans script

10. **Priorité ~/.bash_profile** :
    - Si `~/.bash_profile` existe → `~/.bash_login` et `~/.profile` **ignorés**
    - Seulement **premier trouvé** chargé

### 📚 Commandes à Connaître par Cœur

```bash
# Variables
env                    # Afficher variables exportées
printenv VAR           # Variable spécifique
set                    # Toutes variables + fonctions
export VAR=value       # Exporter variable
unset VAR              # Supprimer variable

# Source
source file            # Exécuter dans shell actuel
. file                 # Équivalent

# Alias
alias name='cmd'       # Créer alias
unalias name           # Supprimer
\cmd                   # Ignorer alias

# Type shell
echo $0                # -bash = login
shopt -q login_shell   # Test login
[[ $- == *i* ]]        # Test interactive

# Debugging
bash -x                # Trace
set -x                 # Activer trace
type cmd               # Type commande
```

### ⚠️ Pièges d'Examen Fréquents

1. **Variable non exportée invisible dans sous-shell**
   ```bash
   VAR=value              # Local
   bash -c 'echo $VAR'    # Vide ❌
   
   export VAR=value       # Exportée
   bash -c 'echo $VAR'    # value ✅
   ```

2. **~/.bash_profile masque ~/.profile**
   - Si `~/.bash_profile` existe, `~/.profile` jamais lu

3. **Scripts ne chargent pas ~/.bashrc**
   - Scripts = non-interactive → `~/.bashrc` non chargé
   - Alias indisponibles dans scripts (sauf `shopt -s expand_aliases`)

4. **source modifie shell actuel**
   ```bash
   source script.sh    # Modifie PWD, variables
   ./script.sh         # Isolé, aucune modification
   ```

5. **Cron env minimal**
   - Cron n'a **pas** `~/.bashrc`
   - PATH minimaliste
   - Charger env manuellement ou via `BASH_ENV`

6. **PS1 couleurs sans \[ \]**
   - Curseur position incorrecte
   - Toujours : `\[\033[32m\]text\[\033[0m\]`

7. **Ordre priorité fichiers login**
   - `~/.bash_profile` **OU** `~/.bash_login` **OU** `~/.profile`
   - PAS les 3, seulement premier trouvé

8. **set affiche fonctions aussi**
   - `env` / `printenv` : Variables seulement
   - `set` : Variables **+** fonctions

9. **Non-interactive → $BASH_ENV seulement**
   - Scripts : Aucun fichier config automatique
   - Sauf si `$BASH_ENV` défini (rare)

10. **Boucle source récursif**
    - `~/.bash_profile` → `~/.bashrc` ✅
    - `~/.bashrc` → `~/.bash_profile` ❌ Boucle infinie

### 🔑 Valeurs à Mémoriser

**Fichiers login shell (ordre)** :
```
1. /etc/profile
2. /etc/profile.d/*.sh
3. ~/.bash_profile (priorité 1)
   ou ~/.bash_login (priorité 2)
   ou ~/.profile (priorité 3)
4. ~/.bashrc (si appelé)
Logout: ~/.bash_logout
```

**Fichiers non-login interactive** :
```
1. /etc/bash.bashrc
2. ~/.bashrc
```

**Codes couleur PS1** :
```
31 Rouge    32 Vert    33 Jaune    34 Bleu
\[\033[32m\]text\[\033[0m\]
       ^^           ^^^^
       Code         Reset
```

**Tests type shell** :
```bash
echo $0              # -bash = login
shopt -q login_shell # return 0 si login
[[ $- == *i* ]]      # true si interactive
```

---

## 📖 Résumé Final

Ce cours couvre la **configuration de l'environnement shell** pour LPIC-1 102 :

1. **Variables d'environnement** :
   - `env`, `printenv` : Variables exportées
   - `set` : Toutes variables + fonctions
   - `export` : Exporter vers enfants
   - `unset` : Supprimer
   - `source` : Exécuter dans shell actuel

2. **Types de shells** :
   - **Interactive Login** : SSH, TTY → `/etc/profile` + `~/.bash_profile` + `~/.bashrc`
   - **Interactive Non-Login** : Terminal GUI → `/etc/bash.bashrc` + `~/.bashrc`
   - **Non-Interactive** : Scripts → Seulement `$BASH_ENV`

3. **Fichiers configuration** :
   - **Système** : `/etc/profile`, `/etc/profile.d/`, `/etc/bash.bashrc`
   - **Utilisateur** : `~/.bash_profile` (ou `.bash_login` ou `.profile`), `~/.bashrc`, `~/.bash_logout`
   - **Template** : `/etc/skel/`

4. **Alias et fonctions** :
   - `alias` : Raccourcis commandes (interactif seulement)
   - Fonctions : Plus flexibles (arguments)
   - Priorité : fonction > alias > commande

5. **PS1** : Personnaliser prompt avec codes et couleurs

6. **Bonnes pratiques** :
   - Personnaliser `~/.bashrc` (chargé par tous shells interactifs)
   - `~/.bash_profile` charge `~/.bashrc`
   - Exporter variables nécessaires aux enfants
   - Scripts : Définir env dans script ou charger fichier config
   - Cron : Env isolé, charger manuellement

La maîtrise de la configuration shell est **essentielle** pour l'administration Linux et l'examen LPIC-1 102.

---

*Documentation créée le 10 février 2026 - Formation LPIC-1 102 - Objectif 105.1*
