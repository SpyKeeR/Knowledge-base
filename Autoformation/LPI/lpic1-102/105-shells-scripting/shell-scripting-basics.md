# 📜 Bases du Scripting Shell

## 📋 Informations du Module

- **Examen** : LPIC-1 102
- **Objectif** : 105.2 - Customize or write simple scripts
- **Cours** : Shell scripting - shebang, arguments, conditions, boucles
- **Poids** : 4 points
- **Objectif** : Écrire scripts Bash robustes avec conditions, boucles, gestion d'entrées/sorties

---

## 🎯 Concepts Fondamentaux

### 🔀 Commandes Combinées (Rappel)

**Opérateurs de contrôle** : Enchaîner commandes avec logique conditionnelle.

```
┌─────────────────────────────────────────────────────────────┐
│  OPÉRATEURS DE COMBINAISON                                  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ;   (Point-virgule)                                        │
│      Exécution séquentielle INCONDITIONNELLE                │
│      cmd1 ; cmd2 ; cmd3                                     │
│      → cmd2 exécuté même si cmd1 échoue                     │
│                                                             │
│  &&  (ET logique)                                           │
│      Exécution SI succès précédent                          │
│      cmd1 && cmd2 && cmd3                                   │
│      → cmd2 exécuté SEULEMENT si cmd1 réussit (exit 0)      │
│      → Court-circuit si échec (arrêt chaîne)                │
│                                                             │
│  ||  (OU logique)                                           │
│      Exécution SI échec précédent                           │
│      cmd1 || cmd2 || cmd3                                   │
│      → cmd2 exécuté SEULEMENT si cmd1 échoue (exit ≠ 0)     │
│      → Court-circuit si succès (arrêt chaîne)               │
│                                                             │
│  Combinaisons :                                             │
│  cmd1 && cmd2 || cmd3                                       │
│  → Si cmd1 OK : exécute cmd2                                │
│  → Si cmd1 KO ou cmd2 KO : exécute cmd3                     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Exemples** :
```bash
# ; (séquentiel) - Toujours exécuter
mkdir /tmp/test ; cd /tmp/test ; touch file.txt
# Même si mkdir échoue, cd et touch exécutés

# && (succès) - Chaîne de succès
mkdir /tmp/test && cd /tmp/test && touch file.txt
# Si mkdir échoue → cd et touch PAS exécutés

# || (échec) - Fallback
cd /nonexistent || echo "Répertoire introuvable"
# Si cd échoue → echo exécuté

# Combiné (pattern if-then-else compact)
test -f file.txt && echo "Fichier existe" || echo "Fichier absent"
# Si test OK → premier echo
# Si test KO → second echo
```

---

### 🔨 Shebang (Rappel)

**Shebang** : Ligne `#!` en début de script spécifiant l'**interpréteur** à utiliser.

**Syntaxe** :
```bash
#!/chemin/vers/interpreteur [options]
```

**Exemples courants** :
```bash
#!/bin/bash              # Bash (chemin absolu)
#!/bin/sh                # Shell POSIX (souvent dash)
#!/usr/bin/env bash      # Bash (portable, cherche dans PATH)
#!/usr/bin/env python3   # Python 3
#!/usr/bin/env perl      # Perl
```

**Importance** :
```bash
# Sans shebang : Exécution avec shell par défaut
./script
# → Utilise $SHELL actuel (peut échouer si syntaxe Bash dans sh)

# Avec shebang : Exécution avec interpréteur spécifié
#!/bin/bash
./script
# → Toujours exécuté avec Bash (fiable)
```

**⚙️ `/usr/bin/env` pour Portabilité** :

**Rôle** : Permet de rechercher l'interpréteur dans le `PATH` au lieu d'utiliser un chemin absolu.

**Avantages** :
- ✅ **Portable** : Fonctionne même si interpréteur dans chemin différent
- ✅ **Flexible** : Trouve version dans environnement virtuel/conda
- ✅ **Standard** : Recommandé pour Python/Perl/Ruby

**Comparaison** :
```bash
#!/bin/bash
# ❌ Problème : Si bash dans /usr/local/bin/, script échoue

#!/usr/bin/env bash
# ✅ OK : Cherche bash dans PATH (trouve /bin/bash, /usr/local/bin/bash, etc.)
```

**Cas d'usage** :
```bash
#!/usr/bin/env python3
# ✅ Trouve python3 dans conda/venv activé
# ✅ Fonctionne sur macOS (python3 dans /usr/bin/), Linux (/usr/local/bin/)

#!/usr/bin/env perl
# ✅ Trouve perl installé localement par utilisateur

#!/usr/bin/env bash
# ✅ Fonctionme même si bash installé via homebrew/package manager
```

**Quand utiliser** :
- ✅ Scripts **distribués** (multi-plateformes)
- ✅ Scripts Python/Perl/**langages interprétés**
- ❌ Scripts systèmes critiques (préférer chemin absolu `/bin/bash` pour sécurité)

---

### 🚀 Exécution de Scripts

**Plusieurs méthodes pour exécuter un script** :

#### 1️⃣ Avec Permission d'Exécution (`+x`)

**Standard** : Script **exécutable** directement.

```bash
# Rendre script exécutable
chmod +x script.sh

# Exécuter
./script.sh
# → Utilise interpréteur défini dans shebang
```

**Conditions requises** :
- ✅ Permission `+x` activée
- ✅ Shebang valide en première ligne
- ✅ Chemin relatif `./` ou absolu `/path/to/script.sh`

#### 2️⃣ Sans Permission d'Exécution

**Alternative** : Passer script **en argument** à l'interpréteur.

```bash
# Script SANS chmod +x
ls -l script.sh
# -rw-r--r--  ← Pas de 'x'

# Exécution en spécifiant interpréteur
bash script.sh
# ✅ Fonctionne même sans +x

sh script.sh
# ✅ Fonctionne (shell POSIX)

python3 script.py
# ✅ Fonctionne pour Python

perl script.pl
# ✅ Fonctionne pour Perl
```

**Avantages** :
- ✅ Pas besoin modifier permissions
- ✅ Utile pour scripts temporaires/tests
- ✅ Fonctionne sur systèmes lecture seule

**Inconvénients** :
- ⚠️ Shebang **ignoré** (utilise interpréteur spécifié en ligne de commande)
- ⚠️ Doit connaître type du script

**Exemples** :
```bash
# Test rapide script Bash
bash mon_script.sh arg1 arg2

# Script POSIX strict
sh mon_script.sh

# Script Python sans shebang/sans +x
python3 analyse.py data.csv
```

**Différence shebang vs interpréteur manuel** :
```bash
#!/bin/bash
echo $BASH_VERSION

# Méthode 1 : Avec +x (utilise shebang)
chmod +x script.sh
./script.sh
# → Bash version X.X.X

# Méthode 2 : Sans +x (force sh)
sh script.sh
# → Erreur si syntaxe Bash ! (sh ≠ bash)

# Méthode 3 : Sans +x (force bash)
bash script.sh
# → Bash version X.X.X ✅
```

---

### 📦 Variables d'Arguments (Rappel)

**Variables positionnelles** : Accès aux **arguments** passés au script.

| Variable | Description | Exemple |
|----------|-------------|---------|
| `$0` | Nom du script | `myscript.sh` |
| `$1`, `$2`, ... `$9` | Arguments 1 à 9 | `$1` = premier arg |
| `${10}`, `${11}`, ... | Arguments ≥ 10 (accolades obligatoires) | `${10}` |
| `$#` | Nombre d'arguments | `3` si 3 args |
| `$@` | Tous arguments (liste séparée) | `"arg1" "arg2" "arg3"` |
| `$*` | Tous arguments (chaîne unique) | `"arg1 arg2 arg3"` |
| `$$` | PID du script | `12345` |
| `$?` | Code retour dernière commande | `0` (succès) ou `1-255` (erreur de 1 à 255) |

**Exemples** :
```bash
#!/bin/bash
# script.sh

echo "Script: $0"
echo "Premier argument: $1"
echo "Deuxième argument: $2"
echo "Nombre d'arguments: $#"
echo "Tous arguments (\$@): $@"
echo "PID: $$"

# Exécution
./script.sh arg1 arg2 arg3
# Script: ./script.sh
# Premier argument: arg1
# Deuxième argument: arg2
# Nombre d'arguments: 3
# Tous arguments ($@): arg1 arg2 arg3
# PID: 12345
```

**Différence $@ vs $\*** :

**Différence critique** : Comportement **avec guillemets doubles**.

#### Sans Guillemets : Identiques

```bash
#!/bin/bash
# test_args.sh

for arg in $@; do
    echo "Arg (\$@): [$arg]"
done

echo "---"

for arg in $*; do
    echo "Arg (\$*): [$arg]"
done

# Exécution
$ bash test_args.sh "arg 1" "arg 2" "arg 3"
Arg ($@): [arg]
Arg ($@): [1]
Arg ($@): [arg]
Arg ($@): [2]
Arg ($@): [arg]
Arg ($@): [3]
---
Arg ($*): [arg]
Arg ($*): [1]
Arg ($*): [arg]
Arg ($*): [2]
Arg ($*): [arg]
Arg ($*): [3]
# ← Expansion en mots séparés (word splitting)
```

#### Avec Guillemets : **DIFFÉRENTS**

**`"$@"`** : Chaque argument **conserve guillemets** (liste de chaînes séparées).

```bash
#!/bin/bash
for arg in "$@"; do
    echo "Arg: [$arg]"
done

# Exécution
$ bash test.sh "arg 1" "arg 2" "arg 3"
Arg: [arg 1]
Arg: [arg 2]
Arg: [arg 3]
# ✅ Chaque argument préservé avec espaces
```

**`"$*"`** : Tous arguments en **UNE seule chaîne** (séparés par IFS, espace par défaut).

```bash
#!/bin/bash
for arg in "$*"; do
    echo "Arg: [$arg]"
done

# Exécution
$ bash test.sh "arg 1" "arg 2" "arg 3"
Arg: [arg 1 arg 2 arg 3]
# ← Tous arguments fusionnés en une chaîne
```

**Tableau récapitulatif** :

| Expression | Arguments | Résultat |
|------------|-----------|----------|
| `$@` | `"a 1" "a 2" "a 3"` | `a 1 a 2 a 3` (word splitting) |
| `"$@"` | `"a 1" "a 2" "a 3"` | `"a 1"` `"a 2"` `"a 3"` ✅ |
| `$*` | `"a 1" "a 2" "a 3"` | `a 1 a 2 a 3` (word splitting) |
| `"$*"` | `"a 1" "a 2" "a 3"` | `"a 1 a 2 a 3"` (chaîne unique) |

**Utilisation recommandée** :
```bash
# ✅ CORRECT : Préserver arguments avec espaces
for arg in "$@"; do
    process "$arg"
done

# ❌ ERREUR : Perd espaces dans arguments
for arg in $@; do
    process "$arg"
done

# Passer tous arguments à commande
my_command "$@"    # ✅ Préserve guillemets
my_command "$*"    # ❌ Fusionne en une chaîne
my_command $@      # ❌ Word splitting
```

**Cas d'usage `"$*"`** :
```bash
# Créer message avec tous arguments
echo "Arguments reçus: $*"
# → "Arguments reçus: arg1 arg2 arg3"

# Journalisation
logger "Script exécuté avec: $*"
```

---

### 🔄 Commande `set` - Redéfinir Paramètres

**Rôle** : Modifier **paramètres de position** après lancement du script.

**Syntaxe** :
```bash
set arg1 arg2 arg3
```

**Fonctionnement** :
```bash
#!/bin/bash
# script.sh

echo "Arguments initiaux:"
echo "\$1 = $1"
echo "\$2 = $2"
echo "\$3 = $3"
echo "\$# = $#"

# Redéfinir paramètres
set Nouveau Param Défini

echo "\nAprès 'set Nouveau Param Défini':"
echo "\$1 = $1"
echo "\$2 = $2"
echo "\$3 = $3"
echo "\$# = $#"

# Exécution
$ bash script.sh ancien arg test
Arguments initiaux:
$1 = ancien
$2 = arg
$3 = test
$# = 3

Après 'set Nouveau Param Défini':
$1 = Nouveau
$2 = Param
$3 = Défini
$# = 3
```

**Cas d'usage** :
```bash
# Parser sortie commande en paramètres
DATE_OUTPUT=$(date +"%Y %m %d %H %M %S")
set $DATE_OUTPUT

echo "Année: $1"
echo "Mois: $2"
echo "Jour: $3"
echo "Heure: $4:$5:$6"

# Diviser chaîne en mots
CHAINE="element1 element2 element3"
set $CHAINE
echo "Premier élément: $1"  # element1
echo "Second élément: $2"   # element2

# Définir paramètres depuis fichier
set $(cat params.txt)
```

**Options `set`** (gestion erreurs) :

| Option | Description | Utilisation |
|--------|-------------|-------------|
| `set -e` | Arrêter script si commande échoue | Scripts critiques |
| `set -u` | Erreur si variable non définie | Détecter typos |
| `set -x` | Afficher commandes exécutées | Debug |
| `set -o pipefail` | Erreur si commande dans pipe échoue | Pipelines |

```bash
#!/bin/bash
set -e  # Arrêt si erreur
set -u  # Erreur si variable non définie
set -x  # Mode debug

commande1
commande2  # Si échoue, script s'arrête
```

---

### ⬅️ Commande `shift` - Déplacer Paramètres

**Rôle** : **Décaler paramètres** vers la gauche (supprime `$1`, tous autres décalent).

**Syntaxe** :
```bash
shift       # Décale de 1 position
shift N     # Décale de N positions
```

**Fonctionnement standard** :
```bash
#!/bin/bash
# shift_demo.sh

echo "Avant shift:"
echo "\$1 = $1, \$2 = $2, \$3 = $3, \$# = $#"

shift  # Décale de 1

echo "\nAprès shift:"
echo "\$1 = $1, \$2 = $2, \$3 = $3, \$# = $#"

# Exécution
$ bash shift_demo.sh arg1 arg2 arg3 arg4
Avant shift:
$1 = arg1, $2 = arg2, $3 = arg3, $# = 4

Après shift:
$1 = arg2, $2 = arg3, $3 = arg4, $# = 3
# arg1 supprimé, tous autres décalés
```

**Décalage multiple** :
```bash
#!/bin/bash
echo "Arguments initiaux: $@"
echo "\$1=$1, \$2=$2, \$3=$3, \$4=$4"

shift 2  # Décale de 2 positions

echo "\nAprès 'shift 2':"
echo "Arguments restants: $@"
echo "\$1=$1, \$2=$2"

# Exécution
$ bash test.sh A B C D E F
Arguments initiaux: A B C D E F
$1=A, $2=B, $3=C, $4=D

Après 'shift 2':
Arguments restants: C D E F
$1=C, $2=D
# A et B supprimés, $3→$1, $4→$2, etc.
```

**Cas d'usage classique** : Traiter **options** puis **fichiers**.

```bash
#!/bin/bash
# backup.sh -v -d /destination fichier1 fichier2

# Parser options
while [ $# -gt 0 ]; do
    case "$1" in
        -v)
            VERBOSE=1
            shift  # Supprimer -v de $@
            ;;
        -d)
            DEST="$2"
            shift 2  # Supprimer -d ET /destination
            ;;
        *)
            break  # Premier argument non-option = début fichiers
            ;;
    esac
done

# $@ contient maintenant uniquement fichiers
echo "Fichiers à sauvegarder: $@"
echo "Destination: $DEST"
echo "Mode verbose: $VERBOSE"
```

**Boucle avec shift** :
```bash
#!/bin/bash
# Traiter tous arguments un par un

while [ $# -gt 0 ]; do
    echo "Traitement: $1"
    process_file "$1"
    shift  # Passer au suivant
done

# Équivalent à
for arg in "$@"; do
    echo "Traitement: $arg"
    process_file "$arg"
done
```

**Erreur courante** :
```bash
# ❌ ERREUR : shift sans vérifier $#
shift 5  # Si seulement 3 arguments → Erreur

# ✅ CORRECT : Vérifier avant shift
if [ $# -ge 5 ]; then
    shift 5
else
    echo "Pas assez d'arguments"
    exit 1
fi
```

---

### 💱 Substitution de Commande (Rappel)

**Substitution de commande** : Exécuter commande et **remplacer par sa sortie**.

**Syntaxes** :
```bash
$(commande)   # Moderne (recommandé)
`commande`    # Ancienne (backticks)
```

**Exemples** :
```bash
# Moderne
DATE=$(date +%Y%m%d)
echo "Aujourd'hui: $DATE"
# Aujourd'hui: 20260210

# Ancienne (éviter)
DATE=`date +%Y%m%d`

# Nested (imbrication)
FILES=$(ls $(pwd))  # Fonctionne
FILES=`ls \`pwd\``  # Requiert échappement (compliqué)

# Cas d'usage
BACKUP_FILE="backup-$(date +%Y%m%d_%H%M%S).tar.gz"
# backup-20260210_153045.tar.gz

USERS=$(cat /etc/passwd | wc -l)
echo "Nombre d'utilisateurs: $USERS"

FREE_SPACE=$(df -h / | awk 'NR==2 {print $4}')
echo "Espace libre: $FREE_SPACE"
```

---

### 🔄 exec - Remplacer Shell Actuel

**exec** : Remplace le **processus actuel** par une nouvelle commande (pas de sous-shell).

**Principe** :
```
Normal (sous-shell) :           exec (remplacement) :
┌──────────────┐                ┌──────────────┐
│ Bash Parent  │                │ Bash Parent  │
│ PID: 1000    │                │ PID: 1000    │
└──────┬───────┘                └──────┬───────┘
       │                               │
       ├─ Fork                          │ exec → remplace
       │                               │
       v                               v
┌──────────────┐                ┌──────────────┐
│ Bash Enfant  │                │  Nouvelle    │
│ PID: 1001    │                │  Commande    │
│ Exécute cmd  │                │ PID: 1000    │ ← Même PID !
└──────┬───────┘                └──────────────┘
       │                               
       │ Retourne                    ⚠️ Pas de retour !
       v                               Shell remplacé
┌──────────────┐                
│ Bash Parent  │                
│ PID: 1000    │                
└──────────────┘                
```

**Syntaxe** :
```bash
exec [OPTIONS] COMMANDE [ARGS]
```

**Options importantes** :
```bash
-c         # Environnement vide (propre)
-a name    # Définir argv[0]
-l         # Login shell (- en préfixe $0)
```

**Exemples** :
```bash
# 1. Exécution normale (sous-shell)
#!/bin/bash
echo "Avant date"
date
echo "Après date"  # ✅ Exécuté (retour au script)

# 2. Avec exec (remplacement)
#!/bin/bash
echo "Avant date"
exec date
echo "Après date"  # ❌ JAMAIS exécuté (shell remplacé)

# Exécution
./script.sh
# Avant date
# Mon Feb 10 15:30:00 2026
# (script terminé, pas de "Après date")

# 3. Remplacer shell par autre programme
exec vim file.txt
# Shell actuel remplacé par vim
# Après :q dans vim → retour terminal parent (pas bash actuel)

# 4. Environnement propre (-c)
exec -c bash
# Nouveau bash avec env vide (PATH, HOME, etc. vides)
echo $PATH
# (vide)

# 5. Changer $0 (-a)
exec -a "custom_name" bash
echo $0
# custom_name
```

**Cas d'usage exec** :
```bash
# 1. Relancer script après mise à jour
#!/bin/bash
if [ -f /tmp/script_updated ]; then
    rm /tmp/script_updated
    exec "$0" "$@"  # Relancer script (même PID)
fi

# 2. Wrapper script (lancer vrai programme)
#!/bin/bash
# /usr/local/bin/myapp-wrapper
export MYAPP_CONFIG=/etc/myapp/config.conf
export LD_LIBRARY_PATH=/opt/myapp/lib
exec /opt/myapp/bin/myapp "$@"
# Script devient myapp (PID conservé, pas de processus wrapper restant)

# 3. Redirection persistante (moins courant)
exec > /var/log/script.log 2>&1
# Toutes sorties suivantes → fichier log
echo "Logged"  # Va dans log

# 4. Login shell personnalisé
exec -l bash
# Bash en mode login (charge /etc/profile, etc.)
```

**⚠️ Attention** : `exec` **remplace** le shell → impossible de revenir. Utiliser avec précaution.

---

## 🔀 Structures Conditionnelles

### 🎯 if / then / else / fi

**Syntaxe de base** :
```bash
if CONDITION; then
    COMMANDES
fi

# Avec else
if CONDITION; then
    COMMANDES_SI_VRAI
else
    COMMANDES_SI_FAUX
fi

# Avec elif (else if)
if CONDITION1; then
    COMMANDES_SI_CONDITION1
elif CONDITION2; then
    COMMANDES_SI_CONDITION2
else
    COMMANDES_SINON
fi
```

**Condition = Code retour commande** :
```bash
# 0 = Vrai (succès)
# ≠ 0 = Faux (échec)

if grep "error" /var/log/syslog; then
    echo "Erreurs trouvées"
fi
# Si grep trouve "error" (exit 0) → commande exécutée
```

**Exemples** :
```bash
#!/bin/bash

# Simple if
if [ -f /etc/passwd ]; then
    echo "Fichier /etc/passwd existe"
fi

# if / else
if ping -c 1 8.8.8.8 > /dev/null 2>&1; then
    echo "Connexion Internet OK"
else
    echo "Pas de connexion Internet"
fi

# if / elif / else
LOAD=$(uptime | awk '{print $(NF-2)}' | tr -d ',')
if [ "$(echo "$LOAD > 2.0" | bc)" -eq 1 ]; then
    echo "Charge haute: $LOAD"
elif [ "$(echo "$LOAD > 1.0" | bc)" -eq 1 ]; then
    echo "Charge moyenne: $LOAD"
else
    echo "Charge normale: $LOAD"
fi

# if imbriqués
if [ -d /var/log ]; then
    if [ -w /var/log ]; then
        echo "Répertoire /var/log accessible en écriture"
    else
        echo "Répertoire /var/log en lecture seule"
    fi
fi
```

---

### 🧪 test et [ ] - Tests Conditionnels

**test** : Commande évaluant des **conditions** (retourne 0 si vrai, 1 si faux).

**Trois syntaxes équivalentes** :
```bash
test CONDITION       # Commande test
[ CONDITION ]        # Alias (espaces obligatoires !)
[[ CONDITION ]]      # Bash étendu (recommandé)
```

**⚠️ Espaces importants** :
```bash
[ -f file.txt ]      # ✅ Correct (espaces autour [ et ])
[-f file.txt]        # ❌ Erreur (pas d'espaces)
[ -f file.txt]       # ❌ Erreur (manque espace avant ])
```

---

### 📊 Opérateurs de Comparaison

#### Chaînes de caractères

| Opérateur | Description | Exemple |
|-----------|-------------|---------|
| `=` ou `==` | Égalité | `[ "$VAR" = "value" ]` |
| `!=` | Différence | `[ "$VAR" != "value" ]` |
| `<` | Inférieur (alphabétique) | `[[ "$VAR" < "value" ]]` ⚠️ Seulement `[[` |
| `>` | Supérieur (alphabétique) | `[[ "$VAR" > "value" ]]` |
| `-z` | Chaîne vide | `[ -z "$VAR" ]` |
| `-n` | Chaîne non vide | `[ -n "$VAR" ]` |

**Exemples chaînes** :
```bash
NAME="Alice"

# Égalité
if [ "$NAME" = "Alice" ]; then
    echo "Bonjour Alice"
fi

# Différence
if [ "$NAME" != "Bob" ]; then
    echo "Vous n'êtes pas Bob"
fi

# Vide ou non
VAR=""
if [ -z "$VAR" ]; then
    echo "Variable vide"
fi

VAR="contenu"
if [ -n "$VAR" ]; then
    echo "Variable non vide"
fi

# Alphabétique (seulement [[)
if [[ "apple" < "banana" ]]; then
    echo "apple avant banana"
fi
```

**⚠️ Toujours quoter les variables** :
```bash
VAR="valeur avec espaces"

# Sans quotes (ERREUR)
[ $VAR = "test" ]
# Interprété : [ valeur avec espaces = "test" ]
# bash: [: too many arguments

# Avec quotes (CORRECT)
[ "$VAR" = "test" ]
# Interprété : [ "valeur avec espaces" = "test" ]
```

---

#### Nombres (Integers)

| Opérateur | Description | Exemple |
|-----------|-------------|---------|
| `-eq` | Égal (equal) | `[ "$NUM" -eq 5 ]` |
| `-ne` | Différent (not equal) | `[ "$NUM" -ne 5 ]` |
| `-lt` | Inférieur (less than) | `[ "$NUM" -lt 10 ]` |
| `-le` | Inférieur ou égal (less or equal) | `[ "$NUM" -le 10 ]` |
| `-gt` | Supérieur (greater than) | `[ "$NUM" -gt 5 ]` |
| `-ge` | Supérieur ou égal (greater or equal) | `[ "$NUM" -ge 5 ]` |

**Exemples nombres** :
```bash
AGE=25

# Comparaisons
if [ "$AGE" -ge 18 ]; then
    echo "Majeur"
else
    echo "Mineur"
fi

SCORE=85
if [ "$SCORE" -gt 90 ]; then
    echo "Excellent"
elif [ "$SCORE" -ge 70 ]; then
    echo "Bien"
elif [ "$SCORE" -ge 50 ]; then
    echo "Moyen"
else
    echo "Insuffisant"
fi

# $# (nombre d'arguments)
if [ $# -eq 0 ]; then
    echo "Aucun argument fourni"
    exit 1
fi

if [ $# -lt 2 ]; then
    echo "Usage: $0 arg1 arg2"
    exit 1
fi
```

**⚠️ Ne PAS utiliser = pour nombres** :
```bash
NUM=10

# INCORRECT (comparaison chaîne)
[ "$NUM" = "10" ]    # Fonctionne mais compare strings

# CORRECT (comparaison numérique)
[ "$NUM" -eq 10 ]    # Compare comme entiers
```

---

#### Fichiers et Répertoires

| Opérateur | Description | Exemple |
|-----------|-------------|---------|
| `-e` | Existe (fichier ou répertoire) | `[ -e /path ]` |
| `-f` | Fichier régulier existe | `[ -f /etc/passwd ]` |
| `-d` | Répertoire existe | `[ -d /var/log ]` |
| `-L` | Lien symbolique | `[ -L /bin ]` |
| `-s` | Fichier existe et non vide (size > 0) | `[ -s file.txt ]` |
| `-b` | Fichier bloc (block device) | `[ -b /dev/sda ]` |
| `-c` | Fichier caractère (character device) | `[ -c /dev/tty ]` |
| `-p` | Tube nommé (named pipe / FIFO) | `[ -p /tmp/mypipe ]` |
| `-r` | Lisible (readable) | `[ -r file.txt ]` |
| `-w` | Modifiable (writable) | `[ -w file.txt ]` |
| `-x` | Exécutable | `[ -x script.sh ]` |
| `-O` | Propriétaire actuel | `[ -O file.txt ]` |
| `-G` | Groupe actuel | `[ -G file.txt ]` |
| `f1 -nt f2` | f1 plus récent que f2 (newer than) | `[ file1 -nt file2 ]` |
| `f1 -ot f2` | f1 plus vieux que f2 (older than) | `[ file1 -ot file2 ]` |

**Exemples fichiers** :
```bash
# Fichier existe
if [ -f /etc/passwd ]; then
    echo "Fichier passwd existe"
fi

# Répertoire existe
if [ -d /var/log ]; then
    echo "Répertoire log existe"
fi

# Fichier existe et non vide
if [ -s /var/log/syslog ]; then
    echo "Syslog contient des données"
else
    echo "Syslog vide"
fi

# Fichiers spéciaux
if [ -b /dev/sda ]; then
    echo "/dev/sda est un périphérique bloc (disque)"
fi

if [ -c /dev/tty ]; then
    echo "/dev/tty est un périphérique caractère (terminal)"
fi

if [ -p /tmp/mypipe ]; then
    echo "/tmp/mypipe est un tube nommé (FIFO)"
fi

# Permissions
if [ -r file.txt ]; then
    cat file.txt
else
    echo "Impossible de lire file.txt"
fi

if [ ! -x script.sh ]; then
    echo "Script non exécutable, ajout de +x"
    chmod +x script.sh
fi

# Comparaison dates
if [ backup.tar.gz -nt source/ ]; then
    echo "Backup à jour"
else
    echo "Backup obsolète, mise à jour nécessaire"
fi

# Vérifications combinées
if [ -f "$FILE" ] && [ -r "$FILE" ]; then
    echo "Fichier existe et lisible"
fi
```

**`\( ... \)` - Groupement de Tests** :

**Rôle** : Grouper tests avec parenthèses échappées pour priorité opérations.

```bash
# Groupement avec \( \)
if [ \( "$A" = "val1" -o "$A" = "val2" \) -a "$B" = "test" ]; then
    echo "A vaut val1 OU val2, ET B vaut test"
fi

# Sans groupement (comportement différent)
if [ "$A" = "val1" -o "$A" = "val2" -a "$B" = "test" ]; then
    # Évaluation : (A=val1) OU ((A=val2) ET (B=test))
    # → Priorité à -a (AND)
fi

# Avec [[ ]], pas besoin échappement
if [[ ( "$A" = "val1" || "$A" = "val2" ) && "$B" = "test" ]]; then
    echo "Plus lisible avec [[  ]]"
fi
```

---

#### Opérateurs Logiques

| Opérateur | Description | Exemple |
|-----------|-------------|---------|
| `!` | NON logique | `[ ! -f file ]` |
| `-a` | ET logique (and) | `[ -f file -a -r file ]` |
| `-o` | OU logique (or) | `[ -f file1 -o -f file2 ]` |
| `\( ... \)` | Groupement (priorité) | `[ \( cond1 -o cond2 \) -a cond3 ]` |
| `&&` | ET (entre commandes) | `[ -f f1 ] && [ -f f2 ]` |
| `\|\|` | OU (entre commandes) | `[ -f f1 ] \|\| [ -f f2 ]` |

**Exemples logiques** :
```bash
# NON (!)
if [ ! -f file.txt ]; then
    echo "Fichier n'existe pas"
fi

# ET (-a ou &&)
if [ -f file.txt -a -r file.txt ]; then
    echo "Fichier existe et lisible"
fi

# Préférer && (plus lisible)
if [ -f file.txt ] && [ -r file.txt ]; then
    echo "Fichier existe et lisible"
fi

# OU (-o ou ||)
if [ "$USER" = "root" -o "$USER" = "admin" ]; then
    echo "Utilisateur privilégié"
fi

# Préférer || (plus lisible)
if [ "$USER" = "root" ] || [ "$USER" = "admin" ]; then
    echo "Utilisateur privilégié"
fi

# Combinaisons complexes
if [ -d /backup ] && [ -w /backup ] && [ $# -ge 1 ]; then
    echo "Backup possible"
else
    echo "Conditions backup non remplies"
fi

# Groupement pour priorité
if [ \( -f file1 -o -f file2 \) -a -d /backup ]; then
    echo "(file1 OU file2 existe) ET /backup est un répertoire"
fi
```

---

### 🎯 Bonnes Pratiques pour Tests

#### Toujours Quoter Variables dans `[ ]`

**Problème** : Variable vide ou avec espaces provoque **erreur de syntaxe**.

```bash
VAR=""

# ❌ ERREUR : Sans quotes
if [ $VAR = "test" ]; then
    echo "OK"
fi
# bash: [: =: unary operator expected
# → Devient: if [  = "test" ]  (syntaxe invalide)

# ✅ CORRECT : Avec quotes
if [ "$VAR" = "test" ]; then
    echo "OK"
fi
# → Devient: if [ "" = "test" ]  (OK, faux mais valide)

# Exemple avec espaces
VAR="multi word"

# ❌ ERREUR
if [ $VAR = "multi word" ]; then
    echo "Match"
fi
# bash: [: too many arguments
# → Devient: if [ multi word = "multi word" ]  (3 args au lieu de 1)

# ✅ CORRECT
if [ "$VAR" = "multi word" ]; then
    echo "Match"
fi
```

**Règle** : **Toujours** mettre variables entre guillemets dans `[ ]`.

#### Utiliser `[[ ]]` pour Éviter Problèmes

**Avantages `[[ ]]` sur `[ ]`** :

| Caractéristique | `[ ]` (test POSIX) | `[[ ]]` (Bash étendu) |
|-----------------|--------------------|-----------------------|
| **Variables vides** | ❌ Erreur si non quotées | ✅ Gère automatiquement |
| **Variables préfix `$`** | ✅ Requis | ✅ Optionnel |
| **Pattern matching** | ❌ Non supporté | ✅ Glob `*.txt` fonctionne |
| **Regex** | ❌ Non | ✅ `=~` supporté |
| **< > comparaison** | ⚠️ Nécessite `\<` `\>` | ✅ Direct `<` `>` |
| **Variables avec espaces** | ⚠️ Doit quoter | ✅ Quotes optionnelles |
| **Portabilité** | ✅ Tous shells POSIX | ❌ Bash/Zsh/Ksh seulement |

**`[[ ]]` gère variables vides sans erreur** :

```bash
#!/bin/bash
VAR=""

# [ ] : ERREUR si pas de quotes
if [ $VAR = "test" ]; then
    echo "OK"
fi
# bash: [: =: unary operator expected

# [[ ]] : OK même sans quotes
if [[ $VAR = "test" ]]; then
    echo "OK"
fi
# ✅ Fonctionne (faux, mais pas d'erreur syntaxe)

# Variables avec espaces
FILE="my file.txt"

# [ ] nécessite quotes
if [ "$FILE" = "my file.txt" ]; then
    echo "Match"
fi

# [[ ]] fonctionne sans quotes (mais recommandé quand même)
if [[ $FILE = "my file.txt" ]]; then
    echo "Match"
fi

# Pattern matching (seulement [[  ]])
if [[ $FILE = *.txt ]]; then
    echo "Fichier texte"
fi
# Ne fonctionne PAS avec [ ]

# Regex (seulement [[  ]])
if [[ $EMAIL =~ ^[a-z]+@[a-z]+\.[a-z]+$ ]]; then
    echo "Email valide"
fi
```

**Pas besoin `$` devant variables dans `[[ ]]`** (optionnel) :

```bash
#!/bin/bash
NUM=10

# Les deux fonctionnent
if [[ $NUM -gt 5 ]]; then
    echo "Supérieur à 5"
fi

if [[ NUM -gt 5 ]]; then  # Sans $
    echo "Supérieur à 5"
fi
# ⚠️ Mais confusant, préférer toujours $NUM
```

**Recommandations** :

```bash
# ✅ Scripts Bash : Utiliser [[  ]]
if [[ -f "$FILE" && "$USER" = "root" ]]; then
    process_file "$FILE"
fi

# ✅ Scripts POSIX (sh) : Utiliser [ ] avec quotes obligatoires
if [ -f "$FILE" ] && [ "$USER" = "root" ]; then
    process_file "$FILE"
fi

# ❌ ÉVITER : [ ] sans quotes
if [ $FILE = "test" ]; then  # Dangereux
    echo "Bad"
fi

# ✅ PRÉFÉRER : [[  ]] ou [ ] avec quotes
if [[ $FILE = "test" ]]; then  # Bash
    echo "Good"
fi

if [ "$FILE" = "test" ]; then  # POSIX
    echo "Good"
fi
```

---

### 🔍 `grep` dans Conditions

**Utiliser `grep` pour tester présence de pattern dans fichier** :

```bash
# Option -q : Quiet (pas de sortie, retourne juste 0 ou 1)
if grep -q "^root" /etc/passwd; then
    echo "Utilisateur root existe"
fi

# Sans -q (affiche résultat)
if grep "^admin" /etc/passwd; then
    echo "Utilisateur admin existe"
fi

# Exemples pratiques
if grep -q "error" /var/log/syslog; then
    echo "Erreurs détectées dans syslog"
    send_alert
fi

if ! grep -q "^pba" /etc/passwd; then
    echo "Utilisateur pba n'existe pas, création..."
    useradd pba
fi

# Combiner avec test fichier
if [ -f /var/log/app.log ] && grep -q "FATAL" /var/log/app.log; then
    echo "Erreur fatale détectée"
fi
```

**Codes retour `grep`** :
- `0` : Pattern trouvé
- `1` : Pattern non trouvé
- `2` : Erreur (fichier introuvable, etc.)

---

**[ ] vs [[ ]] - Différences** :
```bash
# [ ] : POSIX, compatible tous shells
[ "$VAR" = "value" ]

# [[ ]] : Bash étendu, plus de features
[[ "$VAR" = "value" ]]

# Avantages [[ ]] :
# 1. Pas besoin quoter variables (gère espaces/vides automatiquement)
[[ $VAR = "value" ]]  # OK même si VAR vide ou avec espaces

# 2. Pattern matching
[[ "$FILE" = *.txt ]]  # Glob fonctionne

# 3. Regex
[[ "$EMAIL" =~ ^[a-z]+@[a-z]+\.[a-z]+$ ]]

# 4. < > sans échappement
[[ "abc" < "def" ]]  # OK
[ "abc" \< "def" ]   # [ ] requiert \<

# Recommandation : Utiliser [[ ]] pour scripts Bash
```

---

### ❓ $? - Code Retour de Commande

**$?** : Variable contenant le **code de sortie** de la dernière commande exécutée.

**Codes retour standards** :

| Code | Signification |
|------|---------------|
| `0` | Succès (OK) |
| `1` | Erreur générale |
| `2` | Utilisation incorrecte (arguments invalides) |
| `126` | Commande trouvée mais non exécutable |
| `127` | Commande non trouvée |
| `128+N` | Signal N (ex: 130 = Ctrl+C = SIGINT = 128+2) |
| `255` | Code sortie hors limites |

**Exemples** :
```bash
# Succès
ls /tmp
echo $?
# 0  ✅

# Échec
ls /nonexistent
echo $?
# 2  ❌ (erreur ls)

# Utiliser dans condition
grep "error" /var/log/syslog
if [ $? -eq 0 ]; then
    echo "Erreurs trouvées"
else
    echo "Pas d'erreurs"
fi

# Plus idiomatique (directement)
if grep "error" /var/log/syslog; then
    echo "Erreurs trouvées"
fi

# Sauvegarder code retour
backup_data
RETVAL=$?
if [ $RETVAL -ne 0 ]; then
    echo "Backup échoué avec code $RETVAL"
    send_alert "Backup failed"
fi

# Script avec exit code
#!/bin/bash
if [ $# -eq 0 ]; then
    echo "Usage: $0 <filename>"
    exit 2  # Code erreur arguments
fi

if [ ! -f "$1" ]; then
    echo "Fichier '$1' introuvable"
    exit 1  # Erreur générale
fi

# Traitement...
exit 0  # Succès
```

**Fonction retournant code** :
```bash
check_service() {
    systemctl is-active "$1" > /dev/null 2>&1
    return $?  # Retourne code systemctl
}

if check_service sshd; then
    echo "SSH actif"
else
    echo "SSH inactif"
fi
```

---

## 📥 Lecture d'Entrées Utilisateur

### 🎤 read - Lire Entrée Standard

**Syntaxe** :
```bash
read [OPTIONS] VARIABLE(S)
```

**Options importantes** :

| Option | Description | Exemple |
|--------|-------------|---------|
| `-p "prompt"` | Afficher prompt avant lecture | `read -p "Nom: " NAME` |
| `-t SECONDES` | Timeout (secondes) | `read -t 5 RESPONSE` |
| `-s` | Silent (masquer saisie, mot de passe) | `read -s PASSWORD` |
| `-n N` | Lire N caractères seulement | `read -n 1 KEY` |
| `-r` | Raw (désactiver backslash échappement) | `read -r LINE` |
| `-a ARRAY` | Lire dans array | `read -a WORDS` |
| `-d DELIM` | Délimiteur personnalisé (défaut: newline) | `read -d ':' FIELD` |

**Exemples de base** :
```bash
# Simple
echo -n "Votre nom: "
read NAME
echo "Bonjour $NAME"

# Avec prompt (-p)
read -p "Votre nom: " NAME
echo "Bonjour $NAME"

# Mot de passe (-s silent)
read -sp "Mot de passe: " PASSWORD
echo  # Newline après saisie masquée
echo "Mot de passe saisi (${#PASSWORD} caractères)"

# Timeout (-t)
if read -t 5 -p "Continuer? (y/n): " RESPONSE; then
    echo "Réponse: $RESPONSE"
else
    echo "Timeout - abandon"
fi

# Un caractère (-n)
read -n 1 -p "Appuyez sur une touche pour continuer..."
echo  # Newline

# Confirmation
read -p "Supprimer fichier? (y/n): " -n 1 CONFIRM
echo
if [ "$CONFIRM" = "y" ]; then
    rm file.txt
fi
```

**Variables multiples** :
```bash
# Séparer par espaces (IFS par défaut)
read -p "Prénom Nom: " FIRSTNAME LASTNAME
echo "Prénom: $FIRSTNAME, Nom: $LASTNAME"

# Saisie: "John Doe"
# FIRSTNAME=John
# LASTNAME=Doe

# Plus de mots que variables → dernier variable = reste
read -p "Mots: " WORD1 WORD2 REST
# Saisie: "one two three four"
# WORD1=one
# WORD2=two
# REST=three four

# Pas de variable → $REPLY par défaut
read -p "Entrez quelque chose: "
echo "Vous avez saisi: $REPLY"
```

**IFS (Internal Field Separator)** :
```bash
# IFS par défaut : espace, tab, newline
IFS=' ' read -p "Mots: " WORD1 WORD2 WORD3
# Sépare sur espaces

# IFS personnalisé (ex: CSV)
IFS=',' read -p "CSV: " COL1 COL2 COL3
# Saisie: "Alice,30,Paris"
# COL1=Alice
# COL2=30
# COL3=Paris

# Lire ligne fichier
while IFS=':' read -r USERNAME PASSWORD UID GID GECOS HOME SHELL; do
    echo "User: $USERNAME, UID: $UID, Shell: $SHELL"
done < /etc/passwd

# IFS temporaire (une commande)
IFS=',' read -r -a FIELDS <<< "un,deux,trois"
echo "${FIELDS[0]}"  # un
echo "${FIELDS[1]}"  # deux
```

**Lire fichier ligne par ligne** :
```bash
# Méthode 1 : Redirection
while read -r LINE; do
    echo "Ligne: $LINE"
done < file.txt

# Méthode 2 : Pipe (sous-shell)
cat file.txt | while read -r LINE; do
    echo "Ligne: $LINE"
done

# Méthode 3 : With fields
while IFS=',' read -r NAME AGE CITY; do
    echo "$NAME a $AGE ans et vit à $CITY"
done < users.csv
```

**Array avec -a** :
```bash
read -p "Entrez mots séparés par espaces: " -a WORDS
echo "Nombre de mots: ${#WORDS[@]}"
echo "Premier mot: ${WORDS[0]}"
echo "Tous les mots: ${WORDS[@]}"

# Saisie: "apple banana cherry"
# Nombre de mots: 3
# Premier mot: apple
# Tous les mots: apple banana cherry
```

---

## 🔁 Boucles

### 🔄 for - Boucle sur Liste

**Syntaxe** :
```bash
for VARIABLE in LISTE; do
    COMMANDES
done
```

**Exemples - Liste explicite** :
```bash
# Liste de mots
for FRUIT in apple banana cherry; do
    echo "Fruit: $FRUIT"
done
# Fruit: apple
# Fruit: banana
# Fruit: cherry

# Liste de fichiers
for FILE in *.txt; do
    echo "Traitement de $FILE"
    wc -l "$FILE"
done

# Liste de nombres
for NUM in 1 2 3 4 5; do
    echo "Numéro: $NUM"
done
```

**Exemples - Séquence** :
```bash
# Brace expansion {début..fin}
for i in {1..10}; do
    echo "Iteration $i"
done

# Avec pas (step)
for i in {0..100..10}; do
    echo "$i"  # 0, 10, 20, ..., 100
done

# Lettres
for LETTER in {a..z}; do
    echo "$LETTER"
done

# Commande seq (ancienne méthode)
for i in $(seq 1 10); do
    echo "$i"
done

# seq avec pas
for i in $(seq 0 5 100); do
    echo "$i"  # 0, 5, 10, 15, ..., 100
done
```

**Exemples - Sortie de commande** :
```bash
# Fichiers
for FILE in $(ls /var/log/*.log); do
    echo "Log: $FILE"
done

# Utilisateurs
for USER in $(cat /etc/passwd | cut -d: -f1); do
    echo "User: $USER"
done

# Mieux : Éviter substitution commande pour fichiers (globbing)
for FILE in /var/log/*.log; do
    [ -f "$FILE" ] || continue  # Skip si pas fichier
    echo "Log: $FILE"
done
```

**Exemples - Ligne par ligne (fichier)** :
```bash
# INCORRECT (problème espaces/glob)
for LINE in $(cat file.txt); do
    echo "$LINE"
done

# CORRECT : while read
while IFS= read -r LINE; do
    echo "$LINE"
done < file.txt

# for avec array (si fichier petit)
mapfile -t LINES < file.txt
for LINE in "${LINES[@]}"; do
    echo "$LINE"
done
```

**Exemples - Arguments ($@)** :
```bash
#!/bin/bash
# process_files.sh

for FILE in "$@"; do
    if [ -f "$FILE" ]; then
        echo "Traitement de $FILE"
        wc -l "$FILE"
    else
        echo "ERREUR: $FILE introuvable"
    fi
done

# Utilisation
./process_files.sh file1.txt file2.txt file3.txt
```

**Boucle for SANS liste** (utilise `$@` automatiquement) :

```bash
#!/bin/bash
# for_sans_liste.sh

# Sans spécifier "in LISTE" → utilise paramètres de position
for ARG; do
    echo "Argument: $ARG"
done

# Équivalent à
for ARG in "$@"; do
    echo "Argument: $ARG"
done

# Utilisation
$ bash for_sans_liste.sh fichier1 fichier2 fichier3
Argument: fichier1
Argument: fichier2
Argument: fichier3
```

**Cas d'usage** :
```bash
#!/bin/bash
# backup.sh - Sans liste explicite

for FILE; do  # Automatiquement = for FILE in "$@"
    if [ -f "$FILE" ]; then
        cp "$FILE" "$FILE.bak"
        echo "Backup: $FILE → $FILE.bak"
    else
        echo "Avertissement: $FILE introuvable"
    fi
done

# Utilisation
$ bash backup.sh config. txt data.csv notes.md
Backup: config.txt → config.txt.bak
Backup: data.csv → data.csv.bak
Backup: notes.md → notes.md.bak
```

**Style C (pour compatibilité)** :
```bash
# Bash supporte aussi syntaxe C
for ((i=0; i<10; i++)); do
    echo "i = $i"
done

# Avec multiples variables
for ((i=0, j=10; i<10; i++, j--)); do
    echo "i=$i, j=$j"
done
```

---

### ⏳ while - Boucle Tant Que

**Syntaxe** :
```bash
while CONDITION; do
    COMMANDES
done
```

**Principe** : Tant que **CONDITION vraie** (exit 0), répéter.

**Exemples** :
```bash
# Boucle simple
COUNT=1
while [ $COUNT -le 5 ]; do
    echo "Count: $COUNT"
    COUNT=$((COUNT + 1))
done
# Count: 1
# Count: 2
# ...
# Count: 5

# Boucle infinie (Ctrl+C pour arrêter)
while true; do
    echo "Boucle infinie..."
    sleep 1
done

# Équivalent
while :; do
    echo "Boucle infinie..."
    sleep 1
done

# Monitoring
while systemctl is-active myservice > /dev/null; do
    echo "Service actif, vérification dans 5s"
    sleep 5
done
echo "Service arrêté !"

# Attendre fichier
while [ ! -f /tmp/ready.flag ]; do
    echo "Attente du fichier..."
    sleep 2
done
echo "Fichier détecté, suite du traitement"
```

**Lire fichier ligne par ligne** :
```bash
# Méthode standard (recommandée)
while IFS= read -r LINE; do
    echo "Ligne: $LINE"
done < file.txt

# Avec compteur
LINENUM=0
while IFS= read -r LINE; do
    LINENUM=$((LINENUM + 1))
    echo "$LINENUM: $LINE"
done < file.txt

# CSV avec fields
while IFS=',' read -r NAME AGE CITY; do
    echo "$NAME ($AGE ans) - $CITY"
done < users.csv
```

**Redirection après `done`** ⚠️ :

**Important** : Redirection fichier se fait **après `done`**, pas sur ligne `while`.

```bash
# ✅ CORRECT : Redirection après done
while read -r LINE; do
    echo "Ligne: $LINE"
done < fichier.txt

# ❌ INCORRECT : Redirection sur while
while read -r LINE < fichier.txt; do
    echo "Ligne: $LINE"
done
# Ne fonctionne PAS comme attendu (boucle infinie ou rien)
```

**Pourquoi** : La redirection après `done` s'applique à **toute la boucle**, permettant lecture séquentielle.

**Exploitation** :
```bash
# Traiter fichier avec variables cumulatives
TOTAL=0
COUNT=0

while read -r NUM; do
    TOTAL=$((TOTAL + NUM))
    COUNT=$((COUNT + 1))
done < nombres.txt

echo "Total: $TOTAL"
echo "Moyenne: $((TOTAL / COUNT))"

# Validation syntaxe
while read -r LINE; do
    if [[ ! "$LINE" =~ ^[0-9]+$ ]]; then
        echo "Ligne invalide: $LINE"
    fi
done < data.txt

# Multiples redirections possibles
while read -r INPUT; do
    echo "Traité: $INPUT"
done < input.txt > output.txt 2> errors.log
# input depuis input.txt
# stdout vers output.txt
# stderr vers errors.log
```

**Lire sortie de commande** :
```bash
# Ligne par ligne
ps aux | while read -r LINE; do
    echo "Processus: $LINE"
done

# Avec extraction de champs
df -h | tail -n +2 | while read -r FS SIZE USED AVAIL PERC MOUNT; do
    echo "Montage $MOUNT : $PERC utilisé"
done
```

**Menu interactif** :
```bash
#!/bin/bash

while true; do
    echo "===== MENU ====="
    echo "1. Afficher date"
    echo "2. Lister fichiers"
    echo "3. Quitter"
    echo "================"
    read -p "Choix: " CHOICE
    
    case $CHOICE in
        1)
            date
            ;;
        2)
            ls -la
            ;;
        3)
            echo "Au revoir"
            break  # Sortir de boucle
            ;;
        *)
            echo "Choix invalide"
            ;;
    esac
    
    echo
    read -p "Appuyez sur Entrée pour continuer..."
done
```

---

### 🔃 until - Boucle Jusqu'à

**Syntaxe** :
```bash
until CONDITION; do
    COMMANDES
done
```

**Principe** : Répéter **jusqu'à ce que CONDITION vraie** (exit 0).
- Inverse de `while` : Boucle tant que condition **fausse**

**Exemples** :
```bash
# Simple
COUNT=1
until [ $COUNT -gt 5 ]; do
    echo "Count: $COUNT"
    COUNT=$((COUNT + 1))
done
# Count: 1
# Count: 2
# ...
# Count: 5

# Équivalent while
COUNT=1
while [ $COUNT -le 5 ]; do
    echo "Count: $COUNT"
    COUNT=$((COUNT + 1))
done

# Attendre que fichier existe
until [ -f /tmp/data.txt ]; do
    echo "Attente de data.txt..."
    sleep 2
done
echo "Fichier trouvé !"

# Attendre arrêt de service
until ! systemctl is-active myservice > /dev/null; do
    echo "Attente arrêt service..."
    sleep 5
done
echo "Service arrêté"

# Ping jusqu'à succès
until ping -c 1 google.com > /dev/null 2>&1; do
    echo "Pas de connexion, nouvelle tentative..."
    sleep 5
done
echo "Connexion établie"
```

**Différence while vs until** :
```bash
# while : Tant que VRAI
while [ condition ]; do
    # Boucle si condition vraie
done

# until : Jusqu'à VRAI (= tant que FAUX)
until [ condition ]; do
    # Boucle si condition fausse
done

# Exemple
COUNT=1

# while
while [ $COUNT -le 5 ]; do  # Tant que ≤ 5
    echo $COUNT
    COUNT=$((COUNT + 1))
done

# until (équivalent)
COUNT=1
until [ $COUNT -gt 5 ]; do  # Jusqu'à > 5
    echo $COUNT
    COUNT=$((COUNT + 1))
done
```

---

### 🛑 break et continue - Contrôle de Boucles

**break** : Sortir de la boucle immédiatement.

**continue** : Passer à l'itération suivante.

**Exemples** :
```bash
# break
for i in {1..10}; do
    if [ $i -eq 5 ]; then
        echo "Arrêt à 5"
        break  # Sort de la boucle
    fi
    echo $i
done
# 1, 2, 3, 4, Arrêt à 5

# continue
for i in {1..10}; do
    if [ $((i % 2)) -eq 0 ]; then
        continue  # Skip nombres pairs
    fi
    echo $i  # Affiche seulement impairs
done
# 1, 3, 5, 7, 9

# break avec niveau (boucles imbriquées)
for i in {1..3}; do
    for j in {1..3}; do
        if [ $j -eq 2 ]; then
            break 2  # Sort des 2 boucles
        fi
        echo "i=$i, j=$j"
    done
done
# i=1, j=1
# (break 2 sort complètement)

# Recherche et arrêt
while read -r LINE; do
    if echo "$LINE" | grep -q "ERROR"; then
        echo "Erreur trouvée : $LINE"
        break
    fi
done < /var/log/syslog
```

---

## 🔀 Structure `case` - Sélection Multiple

### Syntaxe

**`case`** : Tester variable contre **plusieurs motifs** (patterns).

```bash
case $VARIABLE in
    MOTIF1)
        COMMANDES
        ;;
    MOTIF2)
        COMMANDES
        ;;
    *)
        COMMANDES_PAR_DEFAUT
        ;;
esac
```

### Caractères Wildcards

| Pattern | Description | Exemple motif | Match |
|---------|-------------|---------------|-------|
| `*` | N'importe quels caractères | `*.txt` | `file.txt`, `doc.txt` |
| `?` | Un seul caractère | `file?.txt` | `file1.txt`, `fileA.txt` |
| `[...]` | Un caractère parmi liste | `file[123].txt` | `file1.txt`, `file2.txt`, `file3.txt` |
| `[!...]` | Un caractère PAS dans liste | `file[!0-9].txt` | `fileA.txt` (pas `file1.txt`) |
| `\|` | OU (alternative) | `*.txt\|*.md` | `doc.txt`, `readme.md` |

### Exemples

**Exemple basique** :
```bash
#!/bin/bash
read -p "Entrez un choix (a/b/c): " CHOIX

case $CHOIX in
    a)
        echo "Vous avez choisi A"
        ;;
    b)
        echo "Vous avez choisi B"
        ;;
    c)
        echo "Vous avez choisi C"
        ;;
    *)
        echo "Choix invalide"
        ;;
esac
```

**Avec wildcards `*`** :
```bash
#!/bin/bash
read -p "Nom de fichier: " FILE

case $FILE in
    *.txt)
        echo "Fichier texte"
        ;;
    *.jpg | *.png | *.gif)
        echo "Fichier image"
        ;;
    *.sh)
        echo "Script shell"
        ;;
    *)
        echo "Type de fichier inconnu"
        ;;
esac
```

**Avec wildcards `?`** :
```bash
#!/bin/bash
read -p "Code (2 caractères): " CODE

case $CODE in
    ??)
        echo "Code valide: $CODE"
        ;;
    *)
        echo "Code doit avoir exactement 2 caractères"
        ;;
esac
```

**Avec `[...]` et `[!...]`** :
```bash
#!/bin/bash
read -p "Entrez une lettre: " LETTRE

case $LETTRE in
    [aeiouAEIOU])
        echo "Voyelle"
        ;;
    [!aeiouAEIOU])
        echo "Consonne (ou symbole)"
        ;;
    *)
        echo "Entrée invalide"
        ;;
esac

# Plages
read -p "Note (A-F): " NOTE

case $NOTE in
    [A-B])
        echo "Excellent"
        ;;
    [C-D])
        echo "Passable"
        ;;
    [E-F])
        echo "Insuffisant"
        ;;
    *)
        echo "Note invalide"
        ;;
esac
```

**Alternatives avec `|`** :
```bash
#!/bin/bash
read -p "Continuer? (oui/non): " REPONSE

case $REPONSE in
    oui | o | yes | y)
        echo "Continuation..."
        ;;
    non | n | no)
        echo "Arrêt"
        exit 0
        ;;
    *)
        echo "Réponse non comprise"
        ;;
esac
```

**Menu complet avec case** :
```bash
#!/bin/bash

while true; do
    echo "===== MENU PRINCIPAL ====="
    echo "1) Afficher date et heure"
    echo "2) Lister fichiers courants"
    echo "3) Afficher disque"
    echo "q) Quitter"
    echo "=========================="
    read -p "Votre choix: " CHOIX
    
    case $CHOIX in
        1)
            date
            ;;
        2)
            ls -lh
            ;;
        3)
            df -h
            ;;
        q | Q | quitter | exit)
            echo "Au revoir!"
            break
            ;;
        *)
            echo "Choix invalide"
            ;;
    esac
    
    echo
    read -p "Appuyez sur Entrée..."
done
```

**Gestion d'options script** :
```bash
#!/bin/bash
# script.sh -v -h -f fichier

while [ $# -gt 0 ]; do
    case $1 in
        -h | --help)
            echo "Usage: $0 [-v] [-h] [-f FILE]"
            exit 0
            ;;
        -v | --verbose)
            VERBOSE=1
            shift
            ;;
        -f | --file)
            FILE="$2"
            shift 2
            ;;
        -*)
            echo "Option inconnue: $1"
            exit 1
            ;;
        *)
            echo "Argument inattendu: $1"
            exit 1
            ;;
    esac
done

[ -n "$VERBOSE" ] && echo "Mode verbose activé"
[ -n "$FILE" ] && echo "Fichier: $FILE"
```

**Patterns complexes** :
```bash
#!/bin/bash
# Validation email simplifiée

read -p "Email: " EMAIL

case $EMAIL in
    *@*.*)
        echo "Format email acceptable"
        ;;
    *)
        echo "Format email invalide"
        ;;
esac

# Validation adresse IP
read -p "IP: " IP

case $IP in
    [0-9]*.[0-9]*.[0-9]*.[0-9]*)
        echo "Format IP valide (vérification basique)"
        ;;
    *)
        echo "Format IP invalide"
        ;;
esac
```

---

## 🎯 Structure `select` - Menu Interactif

### Syntaxe

**`select`** : Générer **menu numéroté** automatiquement.

```bash
select VARIABLE in LISTE; do
    COMMANDES
done
```

**Fonctionnement** :
1. Bash affiche **liste numérotée** automatiquement
2. Affiche prompt **`PS3`** (personnalisable)
3. Lit choix utilisateur
4. Affecte valeur choisie à `$VARIABLE`
5. Exécute commandes dans corps de boucle
6. Reboucle (menu réaffiché) sauf `break`

### Variable PS3

**`PS3`** : Prompt affiché pour demander choix (défaut: `#?`).

```bash
PS3="Votre choix : "
```

### Exemples

**Menu simple** :
```bash
#!/bin/bash

PS3="Sélectionnez une option : "

select CHOIX in "Date" "Heure" "Calendrier" "Quitter"; do
    case $CHOIX in
        "Date")
            date +%Y-%m-%d
            ;;
        "Heure")
            date +%H:%M:%S
            ;;
        "Calendrier")
            cal
            ;;
        "Quitter")
            echo "Au revoir!"
            break
            ;;
        *)
            echo "Option invalide"
            ;;
    esac
done

# Affichage automatique :
# 1) Date
# 2) Heure
# 3) Calendrier
# 4) Quitter
# Sélectionnez une option : _
```

**Menu avec validation** :
```bash
#!/bin/bash

PS3="Quelle donnée ? "

echo "Veuillez choisir une information :"
select REPONSE in Jules Romain Francois quitter; do
    case $REPONSE in
        Jules | Romain | Francois)
            echo "Vous avez sélectionné : $REPONSE"
            echo "Numéro de sélection : $REPLY"  # $REPLY = numéro saisi
            break
            ;;
        quitter)
            echo "Abandon"
            break
            ;;
        *)
            echo "Choix invalide, réessayez"
            ;;
    esac
done
```

**Menu d'administration** :
```bash
#!/bin/bash

PS3="Commande à exécuter : "

select ACTION in "Liste utilisateurs" "Espace disque" "Processus" "Logs erreurs" "Quitter"; do
    case $ACTION in
        "Liste utilisateurs")
            cut -d: -f1 /etc/passwd | column
            ;;
        "Espace disque")
            df -h
            ;;
        "Processus")
            ps aux | head -20
            ;;
        "Logs erreurs")
            grep -i error /var/log/syslog | tail -10
            ;;
        "Quitter")
            echo "Fermeture du menu"
            break
            ;;
        *)
            echo "Sélection invalide"
            ;;
    esac
    echo  # Ligne vide
done
```

**Sélection de fichiers** :
```bash
#!/bin/bash

PS3="Fichier à traiter : "

echo "Fichiers disponibles :"
select FICHIER in *.txt "Annuler"; do
    if [ "$FICHIER" = "Annuler" ]; then
        echo "Opération annulée"
        break
    elif [ -n "$FICHIER" ]; then
        echo "Traitement de $FICHIER..."
        wc -l "$FICHIER"
        break
    else
        echo "Choix invalide"
    fi
done
```

**Menu imbriqué** :
```bash
#!/bin/bash

PS3="Menu principal : "

select MENU_PRINCIPAL in "Gestion fichiers" "Gestion système" "Quitter"; do
    case $MENU_PRINCIPAL in
        "Gestion fichiers")
            PS3="Action fichier : "
            select ACTION_FICHIER in "Lister" "Créer" "Retour"; do
                case $ACTION_FICHIER in
                    "Lister")
                        ls -lh
                        ;;
                    "Créer")
                        read -p "Nom fichier : " NOM
                        touch "$NOM"
                        echo "Fichier $NOM créé"
                        ;;
                    "Retour")
                        break
                        ;;
                esac
            done
            PS3="Menu principal : "  # Restaurer prompt parent
            ;;
        "Gestion système")
            PS3="Info système : "
            select INFO in "Uptime" "Charge" "Retour"; do
                case $INFO in
                    "Uptime")
                        uptime
                        ;;
                    "Charge")
                        w
                        ;;
                    "Retour")
                        break
                        ;;
                esac
            done
            PS3="Menu principal : "
            ;;
        "Quitter")
            echo "Au revoir"
            break
            ;;
    esac
done
```

**Avec variable `$REPLY`** :

**`$REPLY`** : Contient **numéro** saisi par utilisateur (pas le texte).

```bash
#!/bin/bash

PS3="Votre choix (numéro) : "

select LANG in "Bash" "Python" "Perl" "Quitter"; do
    echo "Vous avez saisi le numéro : $REPLY"
    echo "Qui correspond à : $LANG"
    
    if [ "$LANG" = "Quitter" ]; then
        break
    fi
done

# Exemple interaction :
# 1) Bash
# 2) Python
# 3) Perl
# 4) Quitter
# Votre choix (numéro) : 2
# Vous avez saisi le numéro : 2
# Qui correspond à : Python
```

---

## 🔧 Fonctions

### Syntaxe

**Déclarer fonction** :
```bash
# Syntaxe 1 (POSIX)
nom_fonction() {
    COMMANDES
}

# Syntaxe 2 (Bash)
function nom_fonction {
    COMMANDES
}

# Syntaxe 3 (Bash, avec parenthèses function)
function nom_fonction() {
    COMMANDES
}
```

**Appeler fonction** :
```bash
nom_fonction           # Sans arguments
nom_fonction arg1 arg2 # Avec arguments
```

### Exemples

**Fonction simple** :
```bash
#!/bin/bash

# Déclaration
saluer() {
    echo "Bonjour depuis la fonction!"
}

# Appel
saluer

# Output:
# Bonjour depuis la fonction!
```

**Fonction avec arguments** :
```bash
#!/bin/bash

saluer_personne() {
    echo "Bonjour $1 !"
}

saluer_personne "Alice"
# Bonjour Alice !

saluer_personne "Bob"
# Bonjour Bob !

# Arguments multiples
afficher_info() {
    echo "Nom: $1"
    echo "Age: $2"
    echo "Ville: $3"
}

afficher_info "Jules" 30 "Paris"
# Nom: Jules
# Age: 30
# Ville: Paris
```

**Variables dans fonctions** :
```bash
#!/bin/bash

test_variables() {
    GLOBALE="Je suis globale"  # Accessible partout
    local LOCALE="Je suis locale"  # Seulement dans fonction
    
    echo "Dans fonction:"
    echo "  GLOBALE: $GLOBALE"
    echo "  LOCALE: $LOCALE"
}

test_variables

echo "Hors fonction:"
echo "  GLOBALE: $GLOBALE"  # Accessible
echo "  LOCALE: $LOCALE"    # Vide (pas accessible)

# Output:
# Dans fonction:
#   GLOBALE: Je suis globale
#   LOCALE: Je suis locale
# Hors fonction:
#   GLOBALE: Je suis globale
#   LOCALE: 
```

### Commande `return` - Code de Retour

**`return`** : Terminer fonction avec **code de sortie** (0-255).

**Syntaxe** :
```bash
return [CODE]
```

- **Sans code** : Retourne code dernière commande
- **Avec code** : Retourne code spécifié (0 = succès, 1-255 = erreur)

**Exemples** :
```bash
#!/bin/bash

# Fonction avec return
verifier_fichier() {
    if [ -f "$1" ]; then
        return 0  # Succès
    else
        return 1  # Échec
    fi
}

# Utilisation
if verifier_fichier "/etc/passwd"; then
    echo "Fichier existe"
else
    echo "Fichier introuvable"
fi
# Fichier existe

# Codes retour personnalisés
check_age() {
    AGE=$1
    
    if [ $AGE -lt 0 ]; then
        return 10  # Age invalide
    elif [ $AGE -lt 18 ]; then
        return 1   # Mineur
    else
        return 0   # Majeur
    fi
}

check_age 25
CODE=$?

case $CODE in
    0)
        echo "Majeur"
        ;;
    1)
        echo "Mineur"
        ;;
    10)
        echo "Age invalide"
        ;;
esac
# Majeur
```

**Return vs echo** :
```bash
#!/bin/bash

# Méthode 1 : return (code sortie)
addition_return() {
    local RESULT=$(($1 + $2))
    # return ne peut retourner que 0-255 !
    return $RESULT  # ⚠️ Limité à 0-255
}

addition_return 10 20
echo "Code retour: $?"  # 30 (OK ici, mais limité)

addition_return 200 100
echo "Code retour: $?"  # 44 ! (300 % 256 = 44) ❌

# Méthode 2 : echo (retourner valeur texte)
addition_echo() {
    echo $(($1 + $2))  # Affiche résultat
}

RESULT=$(addition_echo 200 100)
echo "Résultat: $RESULT"  # 300 ✅

# Recommandation:
# - return : Pour codes succès/erreur (0-255)
# - echo + $() : Pour retourner valeurs/chaînes
```

**Fonction avec validation** :
```bash
#!/bin/bash

backup_file() {
    local FILE=$1
    
    # Validation
    if [ $# -eq 0 ]; then
        echo "ERREUR: Aucun fichier spécifié" >&2
        return 2  # Erreur arguments
    fi
    
    if [ ! -f "$FILE" ]; then
        echo "ERREUR: Fichier $FILE introuvable" >&2
        return 1  # Fichier non trouvé
    fi
    
    # Backup
    cp "$FILE" "$FILE.bak"
    
    if [ $? -eq 0 ]; then
        echo "Backup créé: $FILE.bak"
        return 0  # Succès
    else
        echo "ERREUR: Échec backup" >&2
        return 3  # Erreur backup
    fi
}

# Utilisation
if backup_file "/etc/hosts"; then
    echo "Opération réussie"
else
    echo "Opération échouée (code $?)"
fi
```

**Fonction récursive** :
```bash
#!/bin/bash

# Factorielle récursive
factorielle() {
    local N=$1
    
    if [ $N -le 1 ]; then
        echo 1
        return 0
    else
        local PREV=$(factorielle $((N - 1)))
        echo $((N * PREV))
        return 0
    fi
}

echo "5! = $(factorielle 5)"
# 5! = 120
```

---

## 🛡️ Commande `trap` - Capture de Signaux

### Syntaxe

**`trap`** : Intercepter **signaux système** ou événements pour exécuter commandes.

```bash
trap 'COMMANDES' SIGNAUX
trap '' SIGNAUX          # Ignorer signaux
trap - SIGNAUX           # Restaurer comportement par défaut
```

### Signaux Courants

| Signal | Numéro | Description | Usage |
|--------|--------|-------------|-------|
| `SIGINT` | 2 | Ctrl+C (interruption) | Annulation utilisateur |
| `SIGTERM` | 15 | Terminaison demandée | Arrêt propre (kill PID) |
| `SIGHUP` | 1 | Terminal fermé | Déconnexion session |
| `SIGQUIT` | 3 | Ctrl+\ (quit + core dump) | Debug |
| `SIGKILL` | 9 | Extinction forcée | **Non capturable** |
| `SIGUSR1` | 10 | Signal utilisateur 1 | Custom |
| `SIGUSR2` | 12 | Signal utilisateur 2 | Custom |
| `EXIT` | - | Sortie script (pseudo-signal) | Nettoyage final |

### Exemples

**Ignorer Ctrl+C** :
```bash
#!/bin/bash

# Ignorer signal 2 (SIGINT = Ctrl+C)
trap '' 2

echo "Essayez Ctrl+C, ça ne marche pas!"
sleep 10
echo "Terminé (Ctrl+C ignoré)"

# Utilisateur tape Ctrl+C → rien ne se passe
```

**Nettoyage avant sortie** :
```bash
#!/bin/bash

# Fonction de nettoyage
cleanup() {
    echo "Nettoyage en cours..."
    rm -f /tmp/tempfile_$$
    echo "Fichiers temporaires supprimés"
}

# Capturer EXIT (sortie script)
trap cleanup EXIT

# Script principal
echo "Création fichier temporaire..."
touch /tmp/tempfile_$$
echo "Travail en cours..."
sleep 5
echo "Fini"

# cleanup() toujours exécuté, même si Ctrl+C ou erreur
```

**Message personnalisé Ctrl+C** :
```bash
#!/bin/bash

# Fonction appelée sur SIGINT
sortir() {
    echo ""
    echo "Signal SIGINT reçu (Ctrl+C)"
    echo "Arrêt propre du script..."
    exit 0
}

# Capturer signal 2 (SIGINT)
trap sortir 2

echo "Script en cours (Ctrl+C pour arrêter)..."
while true; do
    echo "Travail... $(date)"
    sleep 2
done
```

**Exemple du livre** :
```bash
#!/bin/bash

# Fonction de sortie personnalisée
sortir() {
    echo "Signal 15 reçu (SIGTERM)"
    echo "Fermeture propre..."
    # Nettoyage, sauvegarde, etc.
    exit 0
}

# Capturer signal 15 (SIGTERM)
trap sortir 15

echo "Script PID: $$"
echo "Envoyez 'kill $$' pour tester"

# Boucle infinie
while true; do
    echo "En attente... ($(date))"
    sleep 3
done

# Test depuis autre terminal:
# $ kill PID_DU_SCRIPT
# → Fonction sortir() appelée
```

**Multiples signaux** :
```bash
#!/bin/bash

gestion_signaux() {
    echo ""
    echo "Signal reçu : sortie propre"
    rm -rf /tmp/workdir_$$
    exit 1
}

# Capturer plusieurs signaux
trap gestion_signaux SIGINT SIGTERM SIGHUP

mkdir /tmp/workdir_$$
cd /tmp/workdir_$$

echo "Travail en cours..."
echo "PID: $$"
echo "Testez: kill $$ ou Ctrl+C"

while true; do
    sleep 1
done
```

**Restaurer comportement par défaut** :
```bash
#!/bin/bash

# Phase 1 : Ignorer Ctrl+C
trap '' SIGINT
echo "Phase 1 : Ctrl+C ignoré"
sleep 5

# Phase 2 : Restaurer Ctrl+C par défaut
trap - SIGINT
echo "Phase 2 : Ctrl+C fonctionne normalement"
sleep 5

echo "Terminé"
```

**Capturer EXIT pour débug** :
```bash
#!/bin/bash

# Fonction debug
debug_exit() {
    echo "Script terminé avec code: $?"
    echo "Dernière commande exécutée à ligne: ${BASH_LINENO[0]}"
}

trap debug_exit EXIT

echo "Commande 1"
false  # Code retour 1
echo "Commande 2"
true   # Code retour 0

# Output final:
# Commande 1
# Commande 2
# Script terminé avec code: 0
# Dernière commande exécutée à ligne: ...
```

**Backup automatique avant Ctrl+C** :
```bash
#!/bin/bash

sauvegarde_urgence() {
    echo ""
    echo "Interruption détectée!"
    echo "Sauvegarde de progression..."
    
    cp "$FICHIER_TRAVAIL" "$FICHIER_TRAVAIL.backup_$(date +%Y%m%d_%H%M%S)"
    echo "Backup créé avant sortie"
    
    exit 130  # Code standard pour SIGINT (128 + 2)
}

trap sauvegarde_urgence SIGINT

FICHIER_TRAVAIL="/tmp/work.txt"

echo "Travail en cours sur $FICHIER_TRAVAIL"
echo "Ctrl+C = sauvegarde automatique avant sortie"

# Simulation travail
for i in {1..60}; do
    echo "Ligne $i: $(date)" >> "$FICHIER_TRAVAIL"
    sleep 1
done
```

**Désactiver temporairement trap** :
```bash
#!/bin/bash

handler() {
    echo "Signal capturé"
    exit 1
}

trap handler SIGINT

echo "Phase 1: trap actif"
sleep 5

# Désactiver trap
trap '' SIGINT
echo "Phase 2: Ctrl+C ignoré"
sleep 5

# Réactiver trap
trap handler SIGINT
echo "Phase 3: trap réactivé"
sleep 5
```

**Liste des traps actifs** :
```bash
# Afficher traps définis
trap -p

# Ou pour signal spécifique
trap -p SIGINT
```

---

## 🔢 Arithmétique

Bash offre **plusieurs méthodes** pour effectuer des calculs arithmétiques :

### 1️⃣ `let` - Évaluation Arithmétique

**Rôle** : Évaluer **expressions arithmétiques** (entiers seulement).

**Syntaxe** :
```bash
let EXPRESSION
let VAR=EXPRESSION
```

**Appel direct sans substitution** :
```bash
VAR=5
let VAR=VAR+1    # Modifie directement VAR
echo $VAR        # 6

# Ou plus court
let VAR++
echo $VAR        # 7
```

---

### 2️⃣ `expr` - Expression Externe

**Rôle** : Commande **externe** pour évaluation arithmétique (ancienne méthode).

**Syntaxe** :
```bash
VAR=$(expr EXPRESSION)
```

**⚠️ Nécessite substitution de commande** :
```bash
VAR=5
VAR=$(expr $VAR + 1)    # Doit utiliser $() ou ``
echo $VAR               # 6

# ATTENTION : Espaces obligatoires autour opérateurs
VAR=$(expr 5 + 3)       # ✅ OK
VAR=$(expr 5+3)         # ❌ Erreur

# Multiplication nécessite échappement *
VAR=$(expr 5 \* 3)      # ✅ 15
VAR=$(expr 5 * 3)       # ❌ Erreur (glob expansion)
```

---

### 3️⃣ `$(( ))` - Expansion Arithmétique

**Rôle** : Substitution arithmétique **intégrée** Bash (plus rapide).

**Syntaxe** :
```bash
VAR=$((EXPRESSION))
```

**Avantages** :
- ✅ **Plus rapide** (builtin, pas de processus externe)
- ✅ Pas besoin `$` devant variables
- ✅ Pas d'échappement nécessaire

```bash
VAR=5
VAR=$((VAR + 1))        # ✅ Rapide et propre
echo $VAR               # 6

# Sans $ devant variables
VAR=$((VAR + 1))        # ✅ Fonctionne
VAR=$((5 * 3))          # ✅ Pas besoin \*
echo $VAR               # 15

# Incrément
VAR=$((VAR + 1))
# Équivalent
((VAR++))
```

---

### 4️⃣ `typeset -i` - Variable Entière

**Rôle** : Déclarer variable comme **entier** (calculs automatiques).

**Syntaxe** :
```bash
typeset -i VARIABLE
```

**Fonctionnement** :
```bash
# Déclarer variable entière
typeset -i a
a=2+2
echo $a
# 4  ✅ Calcul automatique (pas "2+2" string)

# Comparaison sans typeset
b=2+2
echo $b
# 2+2  ❌ String

# Autres opérations
typeset -i num
num=10*3+5
echo $num
# 35  ✅ Évalué automatiquement
```

**Avantage** : Pas besoin `let` ou `$(())` pour calculs ultérieurs.

```bash
typeset -i COUNT
COUNT=0
COUNT=COUNT+1    # Fonctionne directement
echo $COUNT      # 1
```

---

### 5️⃣ `bc` - Calculatrice Nombres Réels

**Rôle** : Commande **externe** pour calculs avec **nombres flottants**.

**Caractéristiques** :
- ✅ Supporte **décimales** (contrairement à let/expr/$((  )))
- ⚠️ Commande **externe** (plus lent)
- ⚠️ Mode **interactif** par défaut

**Syntaxe** :
```bash
RESULT=$(echo "EXPRESSION" | bc)
```

**Exemples** :
```bash
# Division flottante (let/expr donnent division entière)
let RESULT=10/3
echo $RESULT
# 3  ← Division entière

RESULT=$(echo "10/3" | bc)
echo $RESULT
# 3  ← Toujours entier par défaut !

# Précision avec scale
RESULT=$(echo "scale=2; 10/3" | bc)
echo $RESULT
# 3.33  ✅ Flottant avec 2 décimales

# Opérations complexes
RESULT=$(echo "scale=4; (10 + 5) * 2.5 / 3" | bc)
echo $RESULT
# 12.5000

# Racine carrée
RESULT=$(echo "scale=2; sqrt(16)" | bc)
echo $RESULT
# 4.00

# Puissance
RESULT=$(echo "2^10" | bc)
echo $RESULT
# 1024
```

**Mode interactif** :
```bash
$ bc
bc 1.07.1
10 + 5
15
scale=2
10/3
3.33
quit
```

---

### 📊 Opérateurs Arithmétiques

**Opérateurs disponibles** (toutes méthodes sauf différences indiquées) :

| Opérateur | Description | Exemple | Résultat |
|-----------|-------------|---------|----------|
| `+` | Addition | `5 + 3` | `8` |
| `-` | Soustraction | `10 - 3` | `7` |
| `*` | Multiplication | `4 * 5` | `20` |
| `/` | Division (entière pour let/expr/$(())) | `10 / 3` | `3` |
| `%` | Modulo (reste) | `10 % 3` | `1` |
| `**` | Puissance (pas expr) | `2 ** 3` | `8` |
| `++` | Incrément (pas expr) | `VAR++` | `VAR+1` |
| `--` | Décrément (pas expr) | `VAR--` | `VAR-1` |
| `+=` | Addition composée | `VAR+=5` | `VAR=VAR+5` |
| `-=` | Soustraction composée | `VAR-=5` | `VAR=VAR-5` |
| `*=` | Multiplication composée | `VAR*=2` | `VAR=VAR*2` |
| `/=` | Division composée | `VAR/=2` | `VAR=VAR/2` |

### 🔢 Opérateurs de Comparaison `expr`

Opérateur `expr` inclut aussi **comparaisons** :

| Opérateur | Description | Exemple |
|-----------|-------------|---------|
| `=` | Égal | `expr 5 = 5` → `1` (vrai) |
| `!=` | Différent | `expr 5 != 3` → `1` (vrai) |
| `<` | Inférieur | `expr 3 \< 5` → `1` |
| `>` | Supérieur | `expr 5 \> 3` → `1` |
| `<=` | Inférieur ou égal | `expr 3 \<= 5` → `1` |
| `>=` | Supérieur ou égal | `expr 5 \>= 3` → `1` |
| `:` | Compare chaîne à regex | `expr "abc123" : '.*[0-9]'` → `6` (nb caractères si match) |

```bash
# Comparaison chaînes avec regex
expr "test123" : '.*[0-9]'
# 7  ← Nombre de caractères de la chaîne si correspond

expr "test" : '.*[0-9]'
# 0  ← 0 si ne correspond pas

# Utiliser dans condition
if expr 5 \> 3 > /dev/null; then
    echo "5 est supérieur à 3"
fi
```

---

### 📋 Comparaison des Méthodes

| Méthode | Vitesse | Flottants | Syntaxe | Usage |
|---------|---------|-----------|---------|-------|
| `let` | ⭐⭐⭐ Rapide | ❌ | Simple | Compteurs, incréments |
| `expr` | ⭐ Lent (externe) | ❌ | Espaces requis | Legacy, compatibilité |
| `$(())` | ⭐⭐⭐ **Très rapide** | ❌ | Simple | ✅ **RECOMMANDÉ** |
| `typeset -i` | ⭐⭐⭐ Rapide | ❌ | Auto | Variables dédiées |
| `bc` | ⭐ Lent (externe) | ✅ | Pipe requis | Calculs scientifiques |

**Exemples comparatifs** :
```bash
VAR=5

# 1. let
let VAR=VAR+1

# 2. expr (doit être dans substitution)
VAR=$(expr $VAR + 1)

# 3. $(()) - PLUS RAPIDE ✅
VAR=$((VAR + 1))

# 4. typeset -i
typeset -i VAR
VAR=VAR+1    # Calcul automatique

# 5. bc (pour flottants)
VAR=$(echo "$VAR + 1.5" | bc)
```

**Recommandations** :
- ✅ **Entiers** : Utiliser `$(())`  (plus rapide et propre)
- ✅ **Flottants** : Utiliser `bc` ou `awk`
- ❌ Éviter `expr` (lent, syntaxe compliquée)
- ✅ `typeset -i` pour variables dédiées calculs fréquents

---

## 📧 Envoi de Mails

### ✉️ mail / mailx - Envoyer Emails

**Installation** :
```bash
# Debian/Ubuntu
apt install mailutils

# RHEL/CentOS
yum install mailx

# Vérifier
which mail
# /usr/bin/mail
```

**Syntaxe de base** :
```bash
mail [OPTIONS] DESTINATAIRE(S)
```

**Options courantes** :

| Option | Description | Exemple |
|--------|-------------|---------|
| `-s "sujet"` | Sujet du mail | `mail -s "Test" user@example.com` |
| `-a fichier` | Pièce jointe | `mail -a file.pdf -s "Doc" user@...` |
| `-c email` | CC (copie carbone) | `mail -c cc@... user@...` |
| `-b email` | BCC (copie cachée) | `mail -b bcc@... user@...` |
| `-r from` | Expéditeur (From) | `mail -r sender@... user@...` |

**Exemples** :
```bash
# 1. Mode interactif (console, peu utilisé en scripts)
mail user@example.com
Subject: Test
Ceci est un test.
^D  # Ctrl+D pour terminer

# 2. Corps via echo | (commun scripts)
echo "Ceci est le corps du message" | mail -s "Sujet du mail" user@example.com

# 3. Corps multiligne via heredoc
mail -s "Rapport quotidien" admin@example.com <<'EOF'
Rapport du $(date)

Tâches effectuées:
- Backup complet
- Nettoyage logs
- Mise à jour système

Statut: OK
EOF

# 4. Fichier comme corps
mail -s "Log d'erreurs" admin@example.com < /var/log/myapp/errors.log

# 5. Avec pièce jointe
echo "Voir pièce jointe" | mail -s "Rapport mensuel" -a /tmp/report.pdf admin@example.com

# 6. Multiples destinataires
echo "Alerte importante" | mail -s "ALERTE" user1@example.com user2@example.com

# 7. CC et BCC
echo "Info" | mail -s "Information" -c cc@example.com -b bcc@example.com main@example.com

# 8. Corps depuis variable
BODY="Connexion SSH détectée\nUtilisateur: $USER\nHeure: $(date)"
echo -e "$BODY" | mail -s "Alerte SSH" admin@example.com
```

**Cas d'usage scripts** :
```bash
#!/bin/bash

# 1. Notification backup
if backup_script.sh; then
    echo "Backup réussi à $(date)" | mail -s "✓ Backup OK" admin@company.com
else
    echo "Backup ÉCHOUÉ à $(date)" | mail -s "✗ BACKUP FAILED" admin@company.com
fi

# 2. Alerte espace disque
USAGE=$(df -h / | awk 'NR==2 {print $5}' | tr -d '%')
if [ $USAGE -gt 90 ]; then
    mail -s "⚠️  Alerte Disque ($USAGE%)" admin@company.com <<EOF
Serveur: $(hostname)
Partition /: $USAGE% utilisé
Espace libre: $(df -h / | awk 'NR==2 {print $4}')

Action requise: Nettoyer espace disque
EOF
fi

# 3. Rapport quotidien
LOGFILE="/tmp/daily-report-$(date +%Y%m%d).log"
{
    echo "Rapport du $(date)"
    echo "===================="
    echo ""
    echo "Uptime:"
    uptime
    echo ""
    echo "Espace disque:"
    df -h
    echo ""
    echo "Services:"
    systemctl list-units --type=service --state=running
} > "$LOGFILE"

mail -s "Rapport quotidien $(hostname)" -a "$LOGFILE" admin@company.com <<EOF
Voir rapport en pièce jointe.

Serveur: $(hostname)
Date: $(date)
EOF

# 4. Alerte échec cron
#!/bin/bash
# Dans crontab: 0 2 * * * /path/to/script.sh || echo "Script failed" | mail -s "Cron Failed" admin@...

set -e  # Arrêter si erreur

# ... Tâches

# Si arrive ici : succès
echo "Script terminé avec succès" | mail -s "Cron Success" admin@company.com
```

**Mail local (pour root)** :
```bash
# Mail utilisateur local (commun pour cron)
echo "Test notification locale" | mail -s "Test" root

# Lire mails locaux
mail
# & 1  # Lire mail #1
# & d 1  # Supprimer mail #1
# & q  # Quitter

# Fichier mailbox
cat /var/mail/root
# ou
cat /var/spool/mail/root
```

**Configuration MTA (Mail Transfer Agent)** :
```bash
# Vérifier MTA installé
dpkg -l | grep -E "postfix|sendmail|exim"

# postfix (recommandé)
apt install postfix

# Configuration basique /etc/postfix/main.cf
relayhost = smtp.gmail.com:587
smtp_sasl_auth_enable = yes
smtp_sasl_security_options = noanonymous
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_tls_security_level = encrypt

# Redémarrer
systemctl restart postfix

# Tester
echo "Test mail externe" | mail -s "Test" your@email.com

# Vérifier logs
tail -f /var/log/mail.log
```

**⚠️ Sans MTA** : `mail` peut échouer silencieusement. Toujours vérifier logs.

---

## 🎓 10 Scénarios Pratiques Complets

### Scénario 1️⃣ : Script backup avec arguments et vérifications

**Contexte** : Script backup flexible avec destination et source en arguments.

```bash
#!/bin/bash
# backup.sh - Script de backup avec arguments

# Vérifier nombre d'arguments
if [ $# -ne 2 ]; then
    echo "Usage: $0 <source_dir> <backup_dest>"
    echo "Exemple: $0 /home/alice /mnt/backups"
    exit 2
fi

SOURCE="$1"
DEST="$2"

# Vérifier source existe
if [ ! -d "$SOURCE" ]; then
    echo "ERREUR: Répertoire source '$SOURCE' introuvable"
    exit 1
fi

# Vérifier destination existe et accessible en écriture
if [ ! -d "$DEST" ]; then
    echo "ERREUR: Répertoire destination '$DEST' introuvable"
    exit 1
fi

if [ ! -w "$DEST" ]; then
    echo "ERREUR: Pas de permission écriture dans '$DEST'"
    exit 1
fi

# Nom fichier backup
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="$DEST/backup-$(basename "$SOURCE")-$TIMESTAMP.tar.gz"

# Confirmation
echo "===== Backup ====="
echo "Source: $SOURCE"
echo "Destination: $BACKUP_FILE"
echo "=================="
read -p "Continuer? (y/n): " -n 1 -r CONFIRM
echo

if [[ ! "$CONFIRM" =~ ^[Yy]$ ]]; then
    echo "Backup annulé"
    exit 0
fi

# Création backup
echo "Création du backup..."
if tar czf "$BACKUP_FILE" "$SOURCE" 2>/dev/null; then
    SIZE=$(du -h "$BACKUP_FILE" | cut -f1)
    echo "✓ Backup réussi: $BACKUP_FILE ($SIZE)"
    
    # Notification
    echo "Backup créé avec succès" | mail -s "✓ Backup OK - $(hostname)" admin@company.com
    
    exit 0
else
    echo "✗ Backup échoué"
    
    # Notification échec
    echo "Backup échoué sur $(hostname)" | mail -s "✗ BACKUP FAILED" admin@company.com
    
    exit 1
fi
```

**Utilisation** :
```bash
chmod +x backup.sh

# OK
./backup.sh /home/alice /mnt/backups
# ===== Backup =====
# Source: /home/alice
# Destination: /mnt/backups/backup-alice-20260210_153045.tar.gz
# ==================
# Continuer? (y/n): y
# Création du backup...
# ✓ Backup réussi: /mnt/backups/backup-alice-20260210_153045.tar.gz (2.5G)

# Erreur: Mauvais arguments
./backup.sh
# Usage: ./backup.sh <source_dir> <backup_dest>

# Erreur: Source inexistante
./backup.sh /nonexistent /mnt/backups
# ERREUR: Répertoire source '/nonexistent' introuvable
```

---

### Scénario 2️⃣ : Monitoring espace disque avec alertes

**Contexte** : Script vérifiant espace disque, alerte si >80%.

```bash
#!/bin/bash
# disk-monitor.sh - Monitoring espace disque

THRESHOLD=80
ALERT_EMAIL="admin@company.com"

echo "Monitoring espace disque - Seuil: ${THRESHOLD}%"
echo "=============================================="

# Fonction vérifier partition
check_partition() {
    local MOUNT="$1"
    local USAGE=$(df -h "$MOUNT" | awk 'NR==2 {print $5}' | tr -d '%')
    local AVAIL=$(df -h "$MOUNT" | awk 'NR==2 {print $4}')
    
    echo -n "Partition $MOUNT: ${USAGE}% utilisé (${AVAIL} libre) - "
    
    if [ "$USAGE" -ge "$THRESHOLD" ]; then
        echo "⚠️  ALERTE"
        
        # Mail alerte
        mail -s "⚠️  Alerte Disque: $MOUNT ($USAGE%)" "$ALERT_EMAIL" <<EOF
Serveur: $(hostname)
Partition: $MOUNT
Utilisation: ${USAGE}%
Espace libre: $AVAIL

Action requise: Nettoyer espace disque

Détails:
$(df -h "$MOUNT")
EOF
        
        return 1
    else
        echo "✓ OK"
        return 0
    fi
}

# Vérifier partitions principales
ALERTS=0

for MOUNT in / /home /var; do
    if [ -d "$MOUNT" ]; then
        check_partition "$MOUNT" || let ALERTS++
    fi
done

echo "=============================================="

if [ $ALERTS -gt 0 ]; then
    echo "⚠️  $ALERTS alerte(s) détectée(s)"
    exit 1
else
    echo "✓ Tous les disques OK"
    exit 0
fi
```

**Crontab (quotidien)** :
```bash
crontab -e
# 0 8 * * * /usr/local/bin/disk-monitor.sh
```

---

### Scénario 3️⃣ : Script de déploiement avec vérifications pré-requis

**Contexte** : Déployer application avec checks environnement.

```bash
#!/bin/bash
# deploy.sh - Déploiement application

set -e  # Arrêter si erreur

APP_DIR="/var/www/myapp"
REQUIRED_PACKAGES=(nginx php-fpm mysql-server)
REQUIRED_SPACE_MB=500

echo "===== Déploiement Application ====="

# 1. Vérifier user (doit être root)
if [ "$EUID" -ne 0 ]; then
    echo "ERREUR: Ce script doit être exécuté en root"
    exit 1
fi

# 2. Vérifier espace disque disponible
AVAIL_SPACE=$(df -m /var | awk 'NR==2 {print $4}')
if [ "$AVAIL_SPACE" -lt "$REQUIRED_SPACE_MB" ]; then
    echo "ERREUR: Espace insuffisant (/var: ${AVAIL_SPACE}MB < ${REQUIRED_SPACE_MB}MB requis)"
    exit 1
fi

echo "✓ Espace disque OK (${AVAIL_SPACE}MB disponible)"

# 3. Vérifier paquets requis
echo "Vérification des dépendances..."
MISSING_PACKAGES=()

for PKG in "${REQUIRED_PACKAGES[@]}"; do
    if ! dpkg -l | grep -q "^ii  $PKG"; then
        MISSING_PACKAGES+=("$PKG")
        echo "  ✗ $PKG manquant"
    else
        echo "  ✓ $PKG installé"
    fi
done

if [ ${#MISSING_PACKAGES[@]} -gt 0 ]; then
    echo ""
    read -p "Installer paquets manquants? (y/n): " -n 1 -r INSTALL
    echo
    
    if [[ "$INSTALL" =~ ^[Yy]$ ]]; then
        apt update
        apt install -y "${MISSING_PACKAGES[@]}"
    else
        echo "Installation annulée"
        exit 1
    fi
fi

# 4. Sauvegarder version existante
if [ -d "$APP_DIR" ]; then
    echo "Sauvegarde version actuelle..."
    BACKUP_DIR="/var/backups/myapp-$(date +%Y%m%d_%H%M%S)"
    mkdir -p "$BACKUP_DIR"
    cp -r "$APP_DIR" "$BACKUP_DIR/"
    echo "✓ Sauvegarde dans $BACKUP_DIR"
fi

# 5. Déployer nouvelle version
echo "Déploiement de la nouvelle version..."
mkdir -p "$APP_DIR"
# ... Déploiement fichiers ...
echo "✓ Fichiers déployés"

# 6. Permissions
chown -R www-data:www-data "$APP_DIR"
echo "✓ Permissions configurées"

# 7. Redémarrer services
for SERVICE in nginx php-fpm; do
    systemctl restart "$SERVICE"
    
    if systemctl is-active "$SERVICE" > /dev/null; then
        echo "✓ $SERVICE redémarré"
    else
        echo "✗ ERREUR: $SERVICE non actif"
        exit 1
    fi
done

echo ""
echo "===== Déploiement Terminé ====="
echo "Application: $APP_DIR"
echo "Backup: $BACKUP_DIR"

# Notification
mail -s "✓ Déploiement réussi - $(hostname)" admin@company.com <<EOF
Déploiement réussi sur $(hostname)

Application: $APP_DIR
Backup: $BACKUP_DIR
Date: $(date)
EOF

exit 0
```

---

### Scénario 4️⃣ : Script interactif de création d'utilisateur

**Contexte** : Créer utilisateur avec saisie interactive et validations.

```bash
#!/bin/bash
# create-user.sh - Création utilisateur interactive

# Vérifier root
if [ "$EUID" -ne 0 ]; then
    echo "ERREUR: Exécuter en root"
    exit 1
fi

echo "===== Création d'Utilisateur ====="
echo ""

# 1. Nom d'utilisateur
while true; do
    read -p "Nom d'utilisateur: " USERNAME
    
    # Vérifier format (alphanumeric + - _)
    if [[ ! "$USERNAME" =~ ^[a-z][-a-z0-9_]*$ ]]; then
        echo "  ✗ Format invalide (minuscules, chiffres, -, _ seulement)"
        continue
    fi
    
    # Vérifier si existe déjà
    if id "$USERNAME" &>/dev/null; then
        echo "  ✗ Utilisateur '$USERNAME' existe déjà"
        continue
    fi
    
    echo "  ✓ Nom d'utilisateur valide"
    break
done

# 2. Nom complet
read -p "Nom complet (GECOS): " FULLNAME

# 3. Shell
echo "Shell par défaut:"
echo "  1) /bin/bash"
echo "  2) /bin/sh"
echo "  3) /bin/zsh"
read -p "Choix (1-3) [1]: " SHELL_CHOICE

case "$SHELL_CHOICE" in
    2) SHELL="/bin/sh" ;;
    3) SHELL="/bin/zsh" ;;
    *) SHELL="/bin/bash" ;;
esac

# 4. Groupes additionnels
read -p "Groupes additionnels (séparés par virgule) []: " GROUPS

# 5. Créer home directory?
read -p "Créer répertoire home? (Y/n): " -n 1 CREATE_HOME
echo
[[ "$CREATE_HOME" =~ ^[Nn]$ ]] && CREATE_HOME_FLAG="" || CREATE_HOME_FLAG="-m"

# Récapitulatif
echo ""
echo "===== Récapitulatif ====="
echo "Username: $USERNAME"
echo "Nom complet: $FULLNAME"
echo "Shell: $SHELL"
[ -n "$GROUPS" ] && echo "Groupes: $GROUPS"
[ -n "$CREATE_HOME_FLAG" ] && echo "Home: /home/$USERNAME"
echo "========================="
echo ""

read -p "Créer cet utilisateur? (y/n): " -n 1 CONFIRM
echo

if [[ ! "$CONFIRM" =~ ^[Yy]$ ]]; then
    echo "Annulé"
    exit 0
fi

# Création utilisateur
USERADD_CMD="useradd $CREATE_HOME_FLAG -s $SHELL -c \"$FULLNAME\""
[ -n "$GROUPS" ] && USERADD_CMD="$USERADD_CMD -G $GROUPS"
USERADD_CMD="$USERADD_CMD $USERNAME"

echo "Commande: $USERADD_CMD"

if eval "$USERADD_CMD"; then
    echo "✓ Utilisateur créé"
    
    # Mot de passe
    read -p "Définir mot de passe maintenant? (y/n): " -n 1 SET_PASS
    echo
    
    if [[ "$SET_PASS" =~ ^[Yy]$ ]]; then
        passwd "$USERNAME"
    else
        echo "  ⚠️  Pensez à définir le mot de passe : passwd $USERNAME"
    fi
    
    echo ""
    echo "===== Utilisateur Créé ====="
    id "$USERNAME"
    
else
    echo "✗ Échec création utilisateur"
    exit 1
fi
```

---

### Scénario 5️⃣ : Traitement batch fichiers avec boucle for

**Contexte** : Conversion images, renommage, traitement en masse.

```bash
#!/bin/bash
# process-images.sh - Traitement batch images

INPUT_DIR="$1"
OUTPUT_DIR="$2"

# Vérifier arguments
if [ $# -ne 2 ]; then
    echo "Usage: $0 <input_dir> <output_dir>"
    exit 2
fi

# Vérifier répertoires
if [ ! -d "$INPUT_DIR" ]; then
    echo "ERREUR: Répertoire source '$INPUT_DIR' introuvable"
    exit 1
fi

mkdir -p "$OUTPUT_DIR"

echo "Traitement des images de $INPUT_DIR vers $OUTPUT_DIR"
echo "======================================================"

# Compteurs
TOTAL=0
SUCCESS=0
FAILED=0

# Traiter chaque image
for IMAGE in "$INPUT_DIR"/*.{jpg,jpeg,png,gif}; do
    # Skip si pas de fichier (glob échoue)
    [ -f "$IMAGE" ] || continue
    
    let TOTAL++
    
    FILENAME=$(basename "$IMAGE")
    BASENAME="${FILENAME%.*}"
    EXT="${FILENAME##*.}"
    
    # Nom de sortie (timestamp + original)
    OUTPUT_FILE="$OUTPUT_DIR/${BASENAME}-$(date +%Y%m%d).jpg"
    
    echo -n "Traitement de $FILENAME... "
    
    # Conversion (requiert ImageMagick)
    if command -v convert &>/dev/null; then
        if convert "$IMAGE" -resize 800x600 -quality 85 "$OUTPUT_FILE" 2>/dev/null; then
            echo "✓ OK"
            let SUCCESS++
        else
            echo "✗ Échec"
            let FAILED++
        fi
    else
        echo "✗ convert (ImageMagick) non installé"
        let FAILED++
    fi
done

echo "======================================================"
echo "Résumé:"
echo "  Total: $TOTAL"
echo "  Succès: $SUCCESS"
echo "  Échecs: $FAILED"

# Mail rapport
if [ $TOTAL -gt 0 ]; then
    mail -s "Rapport traitement images" admin@company.com <<EOF
Traitement terminé

Total: $TOTAL images
Succès: $SUCCESS
Échecs: $FAILED

Source: $INPUT_DIR
Destination: $OUTPUT_DIR
EOF
fi

# Code retour selon succès/échecs
[ $FAILED -eq 0 ] && exit 0 || exit 1
```

---

### Scénario 6️⃣ : Menu interactif avec case et boucle while

**Contexte** : Interface CLI pour administration serveur.

```bash
#!/bin/bash
# server-menu.sh - Menu administration serveur

# Vérifier root pour certaines actions
check_root() {
    if [ "$EUID" -ne 0 ]; then
        echo "⚠️  Action nécessite droits root"
        return 1
    fi
    return 0
}

while true; do
    clear
    echo "╔═══════════════════════════════════════╗"
    echo "║   Menu Administration Serveur        ║"
    echo "╚═══════════════════════════════════════╝"
    echo ""
    echo "  1) Informations système"
    echo "  2) Espace disque"
    echo "  3) Processus actifs"
    echo "  4) Services (systemd)"
    echo "  5) Utilisateurs connectés"
    echo "  6) Logs récents"
    echo "  7) Redémarrer service"
    echo "  8) Mise à jour système"
    echo "  9) Quitter"
    echo ""
    read -p "Choix: " CHOICE
    
    case "$CHOICE" in
        1)
            echo ""
            echo "===== Informations Système ====="
            hostname
            uname -a
            uptime
            free -h
            ;;
        
        2)
            echo ""
            echo "===== Espace Disque ====="
            df -h
            ;;
        
        3)
            echo ""
            echo "===== Processus Actifs (Top 10 CPU) ====="
            ps aux --sort=-%cpu | head -11
            ;;
        
        4)
            echo ""
            echo "===== Services Systemd ====="
            systemctl list-units --type=service --state=running
            ;;
        
        5)
            echo ""
            echo "===== Utilisateurs Connectés ====="
            who -u
            echo ""
            last -n 10
            ;;
        
        6)
            echo ""
            echo "===== Logs Récents (50 lignes) ====="
            journalctl -n 50 --no-pager
            ;;
        
        7)
            echo ""
            read -p "Nom du service: " SERVICE
            
            if check_root; then
                echo "Redémarrage de $SERVICE..."
                systemctl restart "$SERVICE"
                
                if systemctl is-active "$SERVICE" &>/dev/null; then
                    echo "✓ Service $SERVICE redémarré avec succès"
                else
                    echo "✗ Échec redémarrage de $SERVICE"
                    systemctl status "$SERVICE"
                fi
            fi
            ;;
        
        8)
            echo ""
            if check_root; then
                read -p "Mettre à jour le système? (y/n): " -n 1 CONFIRM
                echo
                
                if [[ "$CONFIRM" =~ ^[Yy]$ ]]; then
                    apt update && apt upgrade -y
                    echo "✓ Mise à jour terminée"
                fi
            fi
            ;;
        
        9)
            echo ""
            echo "Au revoir !"
            exit 0
            ;;
        
        *)
            echo ""
            echo "✗ Choix invalide"
            ;;
    esac
    
    echo ""
    read -p "Appuyez sur Entrée pour continuer..." 
done
```

---

### Scénario 7️⃣ : Monitoring service avec boucle until

**Contexte** : Attendre qu'un service soit disponible avant continuer.

```bash
#!/bin/bash
# wait-for-service.sh - Attendre disponibilité service

SERVICE="$1"
TIMEOUT=300  # 5 minutes
INTERVAL=5   # Vérifier toutes les 5 secondes

if [ -z "$SERVICE" ]; then
    echo "Usage: $0 <service_name>"
    exit 2
fi

echo "Attente du service '$SERVICE'..."
echo "Timeout: ${TIMEOUT}s, Intervalle: ${INTERVAL}s"

ELAPSED=0

until systemctl is-active "$SERVICE" &>/dev/null; do
    if [ $ELAPSED -ge $TIMEOUT ]; then
        echo "✗ Timeout: Service '$SERVICE' non disponible après ${TIMEOUT}s"
        
        # Logs pour debug
        echo ""
        echo "Status du service:"
        systemctl status "$SERVICE"
        
        # Notification
        mail -s "✗ Service Timeout: $SERVICE" admin@company.com <<EOF
Le service '$SERVICE' n'est pas démarré après ${TIMEOUT}s

Serveur: $(hostname)
Date: $(date)

Status:
$(systemctl status "$SERVICE")
EOF
        
        exit 1
    fi
    
    echo "  $(date +%H:%M:%S) - Service inactif, nouvelle tentative dans ${INTERVAL}s... (${ELAPSED}/${TIMEOUT}s)"
    sleep $INTERVAL
    let ELAPSED+=INTERVAL
done

echo "✓ Service '$SERVICE' actif après ${ELAPSED}s"

# Logs de démarrage
echo ""
echo "Logs récents du service:"
journalctl -u "$SERVICE" -n 20 --no-pager

exit 0
```

**Utilisation en déploiement** :
```bash
#!/bin/bash
# deploy-with-wait.sh

# Déployer application
echo "Déploiement de l'application..."
cp -r /tmp/myapp/* /var/www/myapp/

# Redémarrer service
systemctl restart myapp

# Attendre qu'il soit prêt
./wait-for-service.sh myapp

if [ $? -eq 0 ]; then
    echo "✓ Déploiement réussi, service actif"
else
    echo "✗ Déploiement échoué, rollback nécessaire"
    exit 1
fi
```

---

### Scénario 8️⃣ : Script de nettoyage avec conditions multiples

**Contexte** : Nettoyer fichiers anciens selon critères.

```bash
#!/bin/bash
# cleanup.sh - Nettoyage intelligent fichiers

LOG_DIR="/var/log"
BACKUP_DIR="/var/backups"
TMP_DIR="/tmp"
AGE_DAYS=30
DRY_RUN=false

# Arguments
while getopts "d:a:n" OPT; do
    case $OPT in
        d) LOG_DIR="$OPTARG" ;;
        a) AGE_DAYS="$OPTARG" ;;
        n) DRY_RUN=true ;;
        *) echo "Usage: $0 [-d dir] [-a days] [-n]"; exit 2 ;;
    esac
done

echo "===== Nettoyage Fichiers ====="
echo "Répertoire: $LOG_DIR"
echo "Âge: >$AGE_DAYS jours"
[ "$DRY_RUN" = true ] && echo "Mode: DRY RUN (simulation)"
echo "==============================="

FILES_DELETED=0
SPACE_FREED=0

# Trouver et nettoyer fichiers
while IFS= read -r FILE; do
    # Vérifier fichier existe encore (sécurité)
    [ -f "$FILE" ] || continue
    
    SIZE=$(stat -c%s "$FILE" 2>/dev/null || echo 0)
    AGE=$(find "$FILE" -mtime +$AGE_DAYS 2>/dev/null | wc -l)
    
    # Conditions de suppression
    SHOULD_DELETE=false
    REASON=""
    
    # Logs compressés vieux
    if [[ "$FILE" =~ \.gz$ ]] && [ $AGE -eq 1 ]; then
        SHOULD_DELETE=true
        REASON="Log compressé ancien"
    
    # Fichiers temporaires
    elif [[ "$FILE" =~ /tmp/ ]] && [ $AGE -eq 1 ]; then
        SHOULD_DELETE=true
        REASON="Fichier temporaire ancien"
    
    # Gros fichiers (>100MB) anciens
    elif [ $SIZE -gt 104857600 ] && [ $AGE -eq 1 ]; then
        SHOULD_DELETE=true
        REASON="Gros fichier ancien"
    
    # Fichiers vides anciens
    elif [ $SIZE -eq 0 ] && [ $AGE -eq 1 ]; then
        SHOULD_DELETE=true
        REASON="Fichier vide ancien"
    fi
    
    if [ "$SHOULD_DELETE" = true ]; then
        SIZE_MB=$(echo "scale=2; $SIZE / 1048576" | bc)
        
        if [ "$DRY_RUN" = true ]; then
            echo "[DRY RUN] Supprimerait: $FILE (${SIZE_MB}MB) - $REASON"
        else
            echo "Suppression: $FILE (${SIZE_MB}MB) - $REASON"
            rm -f "$FILE"
        fi
        
        let FILES_DELETED++
        let SPACE_FREED+=SIZE
    fi
    
done < <(find "$LOG_DIR" -type f -mtime +$AGE_DAYS 2>/dev/null)

SPACE_FREED_MB=$(echo "scale=2; $SPACE_FREED / 1048576" | bc)

echo "==============================="
echo "Fichiers supprimés: $FILES_DELETED"
echo "Espace libéré: ${SPACE_FREED_MB}MB"

# Rapport par mail
if [ $FILES_DELETED -gt 0 ] && [ "$DRY_RUN" = false ]; then
    mail -s "Rapport nettoyage - $FILES_DELETED fichiers" admin@company.com <<EOF
Nettoyage effectué sur $(hostname)

Répertoire: $LOG_DIR
Fichiers supprimés: $FILES_DELETED
Espace libéré: ${SPACE_FREED_MB}MB
Critère âge: >$AGE_DAYS jours

Date: $(date)
EOF
fi

exit 0
```

**Utilisation** :
```bash
# Dry run (simulation)
./cleanup.sh -n

# Réel avec répertoire personnalisé
./cleanup.sh -d /var/log/myapp

# Âge personnalisé (7 jours)
./cleanup.sh -a 7

# Combiné
./cleanup.sh -d /tmp -a 1 -n
```

---

### Scénario 9️⃣ : Parsing fichier CSV avec read et IFS

**Contexte** : Traiter fichier CSV pour créer utilisateurs en masse.

```bash
#!/bin/bash
# import-users.sh - Import utilisateurs depuis CSV

CSV_FILE="$1"

if [ $# -ne 1 ]; then
    echo "Usage: $0 <users.csv>"
    echo "Format CSV: username,fullname,email,groups"
    exit 2
fi

if [ ! -f "$CSV_FILE" ]; then
    echo "ERREUR: Fichier '$CSV_FILE' introuvable"
    exit 1
fi

if [ "$EUID" -ne 0 ]; then
    echo "ERREUR: Exécuter en root"
    exit 1
fi

echo "Import utilisateurs depuis $CSV_FILE"
echo "======================================"

CREATED=0
SKIPPED=0
ERRORS=0
LINENUM=0

# Lire CSV ligne par ligne
while IFS=',' read -r USERNAME FULLNAME EMAIL GROUPS; do
    let LINENUM++
    
    # Skip header
    if [ $LINENUM -eq 1 ] && [ "$USERNAME" = "username" ]; then
        continue
    fi
    
    # Skip lignes vides
    [ -z "$USERNAME" ] && continue
    
    echo -n "[$LINENUM] $USERNAME ($FULLNAME)... "
    
    # Vérifier si utilisateur existe
    if id "$USERNAME" &>/dev/null; then
        echo "✗ Existe déjà (skipped)"
        let SKIPPED++
        continue
    fi
    
    # Créer utilisateur
    if useradd -m -c "$FULLNAME" -G "$GROUPS" "$USERNAME" 2>/dev/null; then
        # Mot de passe temporaire (username)
        echo "$USERNAME:$USERNAME" | chpasswd
        
        # Forcer changement au premier login
        passwd -e "$USERNAME" &>/dev/null
        
        echo "✓ Créé (pw: $USERNAME, changement requis)"
        let CREATED++
        
        # Mail de bienvenue
        if [ -n "$EMAIL" ]; then
            mail -s "Compte créé sur $(hostname)" "$EMAIL" <<EOF
Bonjour $FULLNAME,

Votre compte a été créé sur $(hostname).

Nom d'utilisateur: $USERNAME
Mot de passe temporaire: $USERNAME
⚠️  Vous devrez changer le mot de passe au premier login.

Groupes: $GROUPS

Cordialement,
L'équipe IT
EOF
        fi
    else
        echo "✗ Erreur création"
        let ERRORS++
    fi
    
done < "$CSV_FILE"

echo "======================================"
echo "Résumé:"
echo "  Créés: $CREATED"
echo "  Skipped: $SKIPPED"
echo "  Erreurs: $ERRORS"

# Rapport admin
mail -s "Import utilisateurs - $CREATED créés" admin@company.com <<EOF
Import utilisateurs terminé

Fichier: $CSV_FILE
Créés: $CREATED
Skipped (existants): $SKIPPED
Erreurs: $ERRORS

Serveur: $(hostname)
Date: $(date)
EOF

exit 0
```

**Fichier users.csv** :
```csv
username,fullname,email,groups
alice,Alice Wonder,alice@company.com,developers,sudo
bob,Bob Builder,bob@company.com,developers
charlie,Charlie Admin,charlie@company.com,sudo,sysadmin
```

---

### Scénario 🔟 : Script avec exec pour wrapper applicatif

**Contexte** : Wrapper qui configure environnement puis remplace par application.

```bash
#!/bin/bash
# myapp-wrapper - Wrapper pour MyApp avec config env

# Ce script configure l'environnement puis REMPLACE le shell par myapp
# Avantage : PID conservé, pas de processus wrapper restant

APP_NAME="myapp"
APP_DIR="/opt/myapp"
CONFIG_FILE="/etc/myapp/config.conf"
LOG_DIR="/var/log/myapp"

# Vérifier installation
if [ ! -d "$APP_DIR" ]; then
    echo "ERREUR: Application non installée ($APP_DIR introuvable)"
    exit 1
fi

if [ ! -f "$APP_DIR/bin/$APP_NAME" ]; then
    echo "ERREUR: Binaire $APP_NAME introuvable"
    exit 1
fi

# Configuration environnement
export MYAPP_HOME="$APP_DIR"
export MYAPP_CONFIG="$CONFIG_FILE"
export MYAPP_LOG_DIR="$LOG_DIR"
export LD_LIBRARY_PATH="$APP_DIR/lib:$LD_LIBRARY_PATH"
export PATH="$APP_DIR/bin:$PATH"

# Créer répertoires nécessaires
mkdir -p "$LOG_DIR"
chown myapp:myapp "$LOG_DIR" 2>/dev/null || true

# Vérifier configuration
if [ ! -f "$CONFIG_FILE" ]; then
    echo "AVERTISSEMENT: Fichier config $CONFIG_FILE absent"
    echo "Utilisation config par défaut"
fi

# Logger démarrage
echo "[$(date)] Démarrage $APP_NAME (PID $$)" >> "$LOG_DIR/wrapper.log"

# ⚠️ exec REMPLACE le shell actuel par myapp
# Après exec, ce script n'existe plus en mémoire
# myapp hérite du PID de ce wrapper

# Option 1 : exec simple (avec arguments passés au wrapper)
# exec "$APP_DIR/bin/$APP_NAME" "$@"

# Option 2 : exec avec env propre (-c)
# Utile si env actuel pollué
# exec -c "$APP_DIR/bin/$APP_NAME" "$@"

# Option 3 : exec avec changement $0 (-a)
# Application verra nom custom dans $0
exec -a "$APP_NAME" "$APP_DIR/bin/$APP_NAME" "$@"

# ⚠️ CODE APRÈS EXEC JAMAIS EXÉCUTÉ
echo "Cette ligne ne sera jamais affichée"
```

**Comparaison avec/sans exec** :
```bash
# SANS exec (processus wrapper reste)
#!/bin/bash
# myapp-launcher (sans exec)
export MYAPP_HOME="/opt/myapp"
/opt/myapp/bin/myapp "$@"

# Résultat ps:
# PID  PPID  CMD
# 1000 999   bash myapp-launcher
# 1001 1000  /opt/myapp/bin/myapp
#            ↑ Processus wrapper RESTE en mémoire

# AVEC exec (remplacement)
#!/bin/bash
# myapp-wrapper (avec exec)
export MYAPP_HOME="/opt/myapp"
exec /opt/myapp/bin/myapp "$@"

# Résultat ps:
# PID  PPID  CMD
# 1000 999   /opt/myapp/bin/myapp
#            ↑ Pas de wrapper (remplacé), PID conservé
```

**Systemd service** :
```ini
[Unit]
Description=MyApp Service
After=network.target

[Service]
Type=simple
User=myapp
Group=myapp
ExecStart=/usr/local/bin/myapp-wrapper --daemon
Restart=always

[Install]
WantedBy=multi-user.target
```

---

## 🚨 10 Erreurs Courantes et Solutions

### Erreur 1️⃣ : Oubli shebang ou mauvais interpréteur

**Symptôme** :
```bash
# script.sh (sans shebang)
echo "Hello"

./script.sh
# ./script.sh: line 1: syntax error near unexpected token `('
```

**Cause** : Sans shebang, script exécuté avec `$SHELL` actuel (peut être incompatible).

**Solutions** :
```bash
# 1. Ajouter shebangtoujours en première ligne
#!/bin/bash
echo "Hello"

# 2. Vérifier interpréteur existe
which bash
# /bin/bash  ✅

#!/bin/bash
# vs
#!/usr/bin/bash  # ❌ Si n'existe pas sur système

# 3. Portable (cherche dans PATH)
#!/usr/bin/env bash

# 4. Tester syntaxe
bash -n script.sh
# (aucune sortie = OK)

# 5. Exécuter avec interpréteur explicite (test)
bash script.sh  # Force Bash même sans shebang
```

---

### Erreur 2️⃣ : Variables non quotées dans tests

**Symptôme** :
```bash
VAR="valeur avec espaces"
[ $VAR = "test" ]
# bash: [: too many arguments
```

**Cause** : `$VAR` expande à `valeur avec espaces` → test interprète comme arguments multiples.

**Solutions** :
```bash
# INCORRECT
VAR="valeur avec espaces"
[ $VAR = "test" ]
# Interprété : [ valeur avec espaces = "test" ]

# CORRECT : Toujours quoter
[ "$VAR" = "test" ]
# Interprété : [ "valeur avec espaces" = "test" ]

# Variable vide aussi problématique
VAR=""
[ $VAR = "test" ]
# Interprété : [ = "test" ]  ← Syntax error

[ "$VAR" = "test" ]
# Interprété : [ "" = "test" ]  ✅

# Recommandation : TOUJOURS quoter variables dans tests
if [ "$VAR" = "value" ]; then
    ...
fi
```

---

### Erreur 3️⃣ : Confondre = et -eq

**Symptôme** :
```bash
NUM=10
[ "$NUM" = 10 ]  # OK mais compare comme string
[ "$NUM" -eq "10x" ]
# bash: [: 10x: integer expression expected
```

**Cause** : `=` compare **chaînes**, `-eq` compare **entiers**.

**Solutions** :
```bash# Nombres : Utiliser -eq, -lt, -gt, etc.
NUM=10
[ "$NUM" -eq 10 ]     # ✅ Comparaison numérique

# Chaînes : Utiliser =, !=
STR="hello"
[ "$STR" = "hello" ]  # ✅ Comparaison chaîne

# PIÈGE : = fonctionne pour nombres MAIS compare comme string
[ "10" = "010" ]      # Faux (strings différents)
[ 10 -eq 010 ]        # Vrai (10 = 8 en octal = 10 en décimal ? Non !)
[ 10 -eq 10 ]         # Vrai

# Vérifier si variable est nombre avant -eq
if [[ "$VAR" =~ ^[0-9]+$ ]]; then
    if [ "$VAR" -gt 5 ]; then
        echo "Supérieur à 5"
    fi
else
    echo "Pas un nombre"
fi
```

---

### Erreur 4️⃣ : Arithmétique sans let ou $(( ))

**Symptôme** :
```bash
COUNT=5
COUNT=$COUNT+1
echo $COUNT
# 5+1  ← String, pas calcul !
```

**Cause** : Bash traite par défaut comme **chaînes**, pas arithmétique.

**Solutions** :
```bash
# INCORRECT (traitement string)
COUNT=5
COUNT=$COUNT+1
echo $COUNT  # 5+1

# CORRECT : let
COUNT=5
let COUNT=COUNT+1
echo $COUNT  # 6

# CORRECT : (( ))
COUNT=5
((COUNT = COUNT + 1))
echo $COUNT  # 6

# CORRECT : $(( ))
COUNT=5
COUNT=$((COUNT + 1))
echo $COUNT  # 6

# CORRECT : Opérateurs abrégés
COUNT=5
let COUNT++
echo $COUNT  # 6

((COUNT++))
echo $COUNT  # 7

# Recommandation : $(( )) (plus lisible)
RESULT=$((5 + 3 * 2))  # 11
```

---

### Erreur 5️⃣ : Boucle for sur sortie commande avec espaces

**Symptôme** :
```bash
# Fichier "my file.txt" avec espace
for FILE in $(ls); do
    echo "Fichier: $FILE"
done
# Fichier: my
# Fichier: file.txt  ← Split sur espace !
```

**Cause** : Substitution commande split sur `IFS` (espaces par défaut).

**Solutions** :
```bash
# INCORRECT : for sur $(ls)
for FILE in $(ls); do
    echo "$FILE"
done

# CORRECT 1 : Globbing (expansion fichiers)
for FILE in *; do
    [ -f "$FILE" ] || continue  # Skip si pas fichier
    echo "$FILE"
done

# CORRECT 2 : while read (fichiers avec newline)
find . -type f -print0 | while IFS= read -r -d '' FILE; do
    echo "$FILE"
done

# CORRECT 3 : Tableau avec mapfile
mapfile -t FILES < <(ls)
for FILE in "${FILES[@]}"; do
    echo "$FILE"
done

# Règle : NE JAMAIS for sur $(commande) pour fichiers
```

---

### Erreur 6️⃣ : $? écrasé par commande suivante

**Symptôme** :
```bash
grep "error" /var/log/syslog
echo "Code retour: $?"  # ← $? de echo, pas grep !
# Code retour: 0
```

**Cause** : `$?` contient le code de la **dernière** commande (ici `echo`).

**Solutions** :
```bash
# INCORRECT
grep "error" /var/log/syslog
echo "Code retour: $?"  # $? de echo !

# CORRECT : Sauvegarder immédiatement
grep "error" /var/log/syslog
RETVAL=$?
echo "Code retour: $RETVAL"

# CORRECT : Tester directement
if grep "error" /var/log/syslog; then
    echo "Erreurs trouvées"
fi

# CORRECT : Fonction
check_errors() {
    grep "error" /var/log/syslog
    return $?  # Retourner code grep
}

if check_errors; then
    echo "Erreurs trouvées"
fi

# Pipeline : $? = dernier code
cmd1 | cmd2 | cmd3
echo $?  # Code de cmd3 seulement

# Pour tous codes : PIPESTATUS
cmd1 | cmd2 | cmd3
echo "${PIPESTATUS[@]}"  # Codes de cmd1, cmd2, cmd3
```

---

### Erreur 7️⃣ : read dans boucle pipe (sous-shell)

**Symptôme** :
```bash
COUNT=0
cat file.txt | while read LINE; do
    ((COUNT++))
done
echo "Lignes: $COUNT"
# Lignes: 0  ← COUNT pas incrémenté !
```

**Cause** : Pipe crée **sous-shell** → modifications variables perdues.

**Solutions** :
```bash
# INCORRECT : while dans pipe
COUNT=0
cat file.txt | while read LINE; do
    ((COUNT++))
done
echo $COUNT  # 0 (sous-shell)

# CORRECT 1 : Redirection (pas de pipe)
COUNT=0
while read LINE; do
    ((COUNT++))
done < file.txt
echo $COUNT  # Nombre réel ✅

# CORRECT 2 : Process substitution
COUNT=0
while read LINE; do
    ((COUNT++))
done < <(cat file.txt)
echo $COUNT  # Nombre réel ✅

# CORRECT 3 : Tableau
mapfile -t LINES < file.txt
COUNT=${#LINES[@]}
echo $COUNT  # Nombre réel ✅

# Règle : Éviter pipe vers while si variables modifiées
```

---

### Erreur 8️⃣ : Condition if avec [[ et & ou |

**Symptôme** :
```bash
if [[ -f file.txt && -r file.txt ]]; then
    echo "OK"
fi
# bash: syntax error near unexpected token `&&'
```

**Cause** : Dans `[[ ]]`, utiliser `-a` (and) ou `&&` **hors** crochets.

**Solutions** :
```bash
# INCORRECT : && dans [ simple
if [ -f file.txt && -r file.txt ]; then  # ❌
    echo "OK"
fi

# CORRECT 1 : -a dans [ ]
if [ -f file.txt -a -r file.txt ]; then
    echo "OK"
fi

# CORRECT 2 : && entre [ ] séparés
if [ -f file.txt ] && [ -r file.txt ]; then
    echo "OK"
fi

# CORRECT 3 : && dans [[ ]] (Bash étendu)
if [[ -f file.txt && -r file.txt ]]; then
    echo "OK"
fi

# Recommandation : [[ ]] pour Bash (plus lisible)
# [ ] pour compatibilité POSIX sh
```

---

### Erreur 9️⃣ : mail sans MTA configuré

**Symptôme** :
```bash
echo "Test" | mail -s "Subject" user@example.com
# (aucune sortie, aucun mail reçu)
```

**Cause** : `mail` requiert **MTA** (Mail Transfer Agent) configuré.

**Solutions** :
```bash
# 1. Vérifier MTA installé
dpkg -l | grep -E "postfix|sendmail|exim"
# (vide = aucun MTA)

# 2. Installer postfix (recommandé)
apt install postfix mailutils

# Configuration : "Internet Site"
# System mail name: votre-domaine.com

# 3. Tester mail local
echo "Test local" | mail -s "Test" root

# Lire mails locaux
mail
# & 1  # Lire mail #1
# &  q  # Quitter

# 4. Vérifier logs
tail -f /var/log/mail.log
# Voir tentatives envoi, erreurs

# 5. Tester avec telnet (debug SMTP)
telnet localhost 25
# EHLO localhost
# MAIL FROM: test@localhost
# RCPT TO: root@localhost
# DATA
# Subject: Test
# Test message
# .
# QUIT

# 6. Configuration relais SMTP (Gmail, etc.)
# /etc/postfix/main.cf
relayhost = [smtp.gmail.com]:587
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_security_options = noanonymous
smtp_tls_security_level = encrypt

# /etc/postfix/sasl_passwd
[smtp.gmail.com]:587 your@gmail.com:app_password

postmap /etc/postfix/sasl_passwd
systemctl restart postfix

# 7. Vérifier queue
mailq
# Mails en attente

postqueue -f  # Forcer envoi queue
```

---

### Erreur 🔟 : exec dans script sans comprendre conséquences

**Symptôme** :
```bash
#!/bin/bash
echo "Avant exec"
exec ls /tmp
echo "Après exec"  # ❌ JAMAIS exécuté
```

**Cause** : `exec` **remplace** le shell → code après exec jamais atteint.

**Solutions** :
```bash
# exec REMPLACE le processus actuel
#!/bin/bash
echo "Avant"
exec date
echo "Après"  # JAMAIS exécuté

# Utilisation correcte : En FIN de script
#!/bin/bash
# Configuration
export VAR=value
cd /app

# Remplacer par application (dernière instruction)
exec /app/bin/myapp "$@"

# Wrapper systemd
#!/bin/bash
# setup-and-run
export CONFIG=/etc/myapp.conf
exec /usr/bin/myapp --daemon  # Remplace wrapper

# PID conservé :
# ps montrera /usr/bin/myapp avec PID du script

# ⚠️ NE PAS utiliser exec si code après
# Utiliser sous-shell normal
#!/bin/bash
echo "Avant"
date  # Ou bash -c date
echo "Après"  # ✅ Exécuté

# exec utile pour :
# - Wrappers (remplacer wrapper par app)
# - Redirection persistante (exec > file.log 2>&1)
# - Économie ressources (pas de processus parent)
```

---

## 📝 Antisèche (Cheat Sheet)

### Commandes Combinées
```bash
cmd1 ; cmd2        # Séquentiel (toujours exécuter)
cmd1 && cmd2       # SI succès cmd1
cmd1 || cmd2       # SI échec cmd1
cmd1 && cmd2 || cmd3  # If-then-else compact
```

### Arguments et Variables Spéciales
```bash
$0                 # Nom du script
$1, $2, ..., $9    # Arguments 1-9
${10}, ${11}       # Arguments ≥10
$#                 # Nombre d'arguments
$@                 # Tous arguments (séparés)
"$@"               # Tous arguments (préservés)
$*                 # Tous arguments (chaîne)
$$                 # PID script
$?                 # Code retour dernière commande
```

### Conditions if
```bash
if CONDITION; then
    COMMANDES
fi

if CONDITION; then
    VRAI
else
    FAUX
fi

if COND1; then
    BLOC1
elif COND2; then
    BLOC2
else
    DEFAUT
fi
```

### Tests [ ]
```bash
# Chaînes
[ "$VAR" = "value" ]   # Égalité
[ "$VAR" != "value" ]  # Différence
[ -z "$VAR" ]          # Vide
[ -n "$VAR" ]          # Non vide

# Nombres
[ "$NUM" -eq 5 ]       # Égal
[ "$NUM" -ne 5 ]       # Différent
[ "$NUM" -lt 10 ]      # Inférieur <
[ "$NUM" -le 10 ]      # Inférieur ou égal ≤
[ "$NUM" -gt 5 ]       # Supérieur >
[ "$NUM" -ge 5 ]       # Supérieur ou égal ≥

# Fichiers
[ -e fichier ]         # Existe
[ -f fichier ]         # Fichier régulier
[ -d répertoire ]      # Répertoire
[ -s fichier ]         # Non vide
[ -r fichier ]         # Lisible
[ -w fichier ]         # Modifiable
[ -x fichier ]         # Exécutable
[ -L lien ]            # Lien symbolique

# Logique
[ ! CONDITION ]        # NON
[ COND1 -a COND2 ]     # ET
[ COND1 -o COND2 ]     # OU
[ COND1 ] && [ COND2 ] # ET (préféré)
[ COND1 ] || [ COND2 ] # OU (préféré)
```

### Boucles
```bash
# for (liste)
for VAR in liste...; do
    COMMANDES
done

# for (séquence)
for i in {1..10}; do
    echo $i
done

# for (style C)
for ((i=0; i<10; i++)); do
    echo $i
done

# while (tant que)
while CONDITION; do
    COMMANDES
done

# until (jusqu'à)
until CONDITION; do
    COMMANDES
done

# Contrôle
break              # Sortir de boucle
continue           # Itération suivante
break 2            # Sortir 2 niveaux
```

### read - Entrées
```bash
read VAR                    # Simple
read -p "Prompt: " VAR      # Avec prompt
read -s PASSWORD            # Silent (masqué)
read -t 5 VAR               # Timeout 5s
read -n 1 KEY               # 1 caractère
read V1 V2 V3               # Multiples variables
IFS=',' read A B C          # Séparateur custom
while read LINE; do...; done < file  # Fichier
```

### Arithmétique
```bash
let VAR=5+3                 # let
let VAR++                   # Incrément
((VAR = 5 + 3))             # (( ))
VAR=$((5 + 3))              # $(( ))
RESULT=$((NUM * 2 + 5))     # Expressions

# Opérateurs
+ - * / %                   # Arithmétique
** ++  --                   # Puissance, incr, décr
+= -= *= /=                 # Assignation composée
```

### exec
```bash
exec COMMANDE               # Remplacer shell
exec -c COMMANDE            # Env propre
exec -a NAME COMMANDE       # Changer $0
exec > file.log 2>&1        # Redirection persistante
```

### mail
```bash
mail -s "Sujet" user@domain                    # Simple
echo "Corps" | mail -s "Sujet" user@domain     # Avec corps
mail -s "Sujet" -a file.pdf user@domain        # Pièce jointe
mail -s "Sujet" -c cc@domain user@domain       # CC
mail -s "Sujet" user1 user2 user3              # Multiples
```

---

## 🎯 Points Clés pour l'Examen LPIC-1

### ✅ Concepts à Maîtriser Absolument

1. **Shebang obligatoire** :
   - `#!/bin/bash` première ligne
   - `/usr/bin/env bash` portable
   - Détermine interpréteur

2. **Variables arguments** :
   - `$1, $2, ...` : Arguments positionnels
   - `$#` : Nombre d'arguments
   - `"$@"` : Tous arguments (préservés)
   - `$?` : Code retour dernière commande

3. **Codes retour** :
   - `0` = Succès
   - `1-255` = Échec (plage complète)
   - `exit CODE` pour retourner code personnalisé
   - Fonctions : `return CODE`

4. **Tests [ ] vs [[ ]]** :
   - `[ ]` : POSIX, compatible tous shells
   - `[[ ]]` : Bash étendu, plus features
   - **Toujours quoter** variables : `[ "$VAR" = "value" ]`

5. **Opérateurs comparaison** :
   - **Chaînes** : `=`, `!=`, `-z` (vide), `-n` (non vide)
   - **Nombres** : `-eq`, `-ne`, `-lt`, `-le`, `-gt`, `-ge`
   - **Fichiers** : `-f`, `-d`, `-e`, `-r`, `-w`, `-x`, `-s`

6. **Boucles** :
   - `for` : Itération sur liste/séquence
   - `while` : Tant que condition vraie
   - `until` : Jusqu'à condition vraie
   - `break` / `continue` contrôle

7. **read** :
   - `-p "prompt"` : Afficher prompt
   - `-t SECS` : Timeout
   - `-s` : Silent (mot de passe)
   - `-n N` : N caractères
   - Variables multiples : séparation IFS

8. **Arithmétique** :
   - `let VAR=EXPR` : Évaluation arithmétique
   - `expr $VAR + 1` : Commande externe (espaces obligatoires)
   - `$((EXPR))` : Expansion arithmétique ✅ **RECOMMANDÉ**
   - `((VAR++))` : Opérations
   - `typeset -i VAR` : Déclaration variable entière
   - `bc` : Calculs flottants (externe)
   - **SANS** : Traitement string !

9. **Manipulation paramètres** :
   - `set arg1 arg2` : Redéfinir paramètres de position
   - `shift` : Décaler paramètres gauche (supprime $1)
   - `shift N` : Décaler de N positions
   - `$*` vs `"$@"` : Différence avec guillemets

10. **Structures de contrôle** :
    - `case` : Sélection multiple avec wildcards (*, ?, [...], |)
    - `select` : Menu interactif automatique avec PS3
    - `for` sans liste = utilise $@
    - `while read; done < file` : Redirection après done

11. **Fonctions** :
    - Déclaration : `nom() { commandes; }`
    - `return CODE` : Sortie avec code 0-255
    - `echo` + `$()` : Retourner valeurs/chaînes
    - `local` : Variables locales fonction

12. **trap** :
    - Capturer signaux : `trap 'cmd' SIGNAL`
    - Ignorer : `trap '' SIGNAL`
    - Restaurer : `trap - SIGNAL`
    - Signaux : SIGINT(2), SIGTERM(15), EXIT
    - **SIGKILL(9) non capturable**

13. **exec** :
    - Remplace shell actuel par commande
    - PID conservé
    - Pas de retour (shell remplacé)
    - Usage : Wrappers, optimisation

14. **mail** :
    - Requiert MTA (postfix)
    - `-s "sujet"` obligatoire
    - Corps via pipe ou heredoc
    - `-a fichier` pour pièce jointe

### 📚 Commandes à Connaître par Cœur

```bash
# Tests
[ condition ]       # Test simple
[[ condition ]]     # Test Bash étendu
test condition      # Équivalent [ ]

# Boucles
for var in list; do...; done
for var; do...; done            # Sans liste = utilise $@
while cond; do...; done
while read var; do...; done < file  # Redirection après done
until cond; do...; done

# Sélection
case $VAR in
    pattern1) cmd1 ;;
    pattern2) cmd2 ;;
    *) default ;;
esac

select VAR in list; do...; done  # Menu auto PS3

# Fonctions
nom() { commandes; }
return CODE                 # 0-255
local VAR=value            # Variable locale

# Entrées
read VAR
read -p "Prompt: " VAR
read -t 5 VAR

# Arithmétique
let VAR=EXPR
VAR=$((EXPR))              # ✅ Recommandé
expr $VAR + 1              # Externe, espaces requis
typeset -i VAR             # Variable entière
bc                         # Flottants

# Paramètres
set arg1 arg2              # Redéfinir $1 $2 ...
shift                      # Décaler left ($2→$1)
shift N                    # Décaler N positions

# Signaux
trap 'cmd' SIGNAL          # Capturer
trap '' SIGNAL             # Ignorer
trap - SIGNAL              # Restaurer défaut

# exec
exec COMMAND

# mail
echo "msg" | mail -s "subject" user@domain
```

### ⚠️ Pièges d'Examen Fréquents

1. **Variables non quotées dans tests**
   ```bash
   [ $VAR = "test" ]  # ❌ Erreur si VAR espaces/vide
   [ "$VAR" = "test" ]  # ✅ Toujours quoter
   ```

2. **= vs -eq**
   ```bash
   [ "$NUM" = 10 ]   # Compare string
   [ "$NUM" -eq 10 ] # Compare nombre ✅
   ```

3. **Arithmétique sans let/$(())**
   ```bash
   VAR=$VAR+1  # ❌ String "5+1"
   let VAR=VAR+1  # ✅ Calcul 6
   ```

4. **$? écrasé**
   ```bash
   commande
   echo $?  # ✅ Code de commande
   
   commande
   echo "test"
   echo $?  # ❌ Code de echo, pas commande
   
   # Sauvegarder : RETVAL=$?
   ```

5. **for sur $(ls) avec espaces**
   ```bash
   for f in $(ls); do  # ❌ Split sur espaces
   for f in *; do      # ✅ Globbing
   ```

6. **read dans pipe (sous-shell)**
   ```bash
   cat f | while read; COUNT++; done
   echo $COUNT  # ❌ 0 (sous-shell)
   
   while read; COUNT++; done < f
   echo $COUNT  # ✅ Valeur réelle
   ```

7. **Oubli shebang**
   - Script peut échouer selon shell par défaut
   - Toujours `#!/bin/bash`

8. **exec sans comprendre**
   - Code après exec **jamais exécuté**
   - Shell remplacé

9. **if avec && dans [ simple**
   ```bash
   [ -f f && -r f ]  # ❌ Erreur
   [ -f f -a -r f ]  # ✅ -a dans [ ]
   [[ -f f && -r f ]]  # ✅ && dans [[ ]]
   ```

10. **mail sans MTA**
    - `mail` silencieux si MTA absent
    - Toujours vérifier `/var/log/mail.log`

11. **case sans ;; final**
    ```bash
    case $VAR in
        a) echo "A"  # ❌ Manque ;;
    esac
    # Erreur syntaxe
    ```

12. **select sans break**
    ```bash
    select OPT in a b c; do
        echo $OPT  # ❌ Boucle infinie
    done
    # Toujours break après action
    ```

13. **return > 255**
    ```bash
    fonction() {
        return 300  # ❌ Devient 44 (300 % 256)
    }
    # Codes retour limités 0-255
    ```

14. **trap SIGKILL**
    ```bash
    trap 'cmd' 9  # ❌ SIGKILL non capturable
    # Seuls SIGKILL et SIGSTOP ne peuvent être capturés
    ```

15. **expr sans échappement ***
    ```bash
    expr 5 * 3  # ❌ Glob expansion
    expr 5 \* 3  # ✅ 15
    ```

16. **shift sans vérifier $#**
    ```bash
    shift 5  # ❌ Erreur si $# < 5
    [ $# -ge 5 ] && shift 5  # ✅ Vérifier avant
    ```

### 🔑 Valeurs à Mémoriser

**Codes retour standards** :
```
0     Succès
1     Erreur générale
2     Usage incorrect
126   Non exécutable
127   Commande non trouvée
130   Ctrl+C (128+2)
```

**Opérateurs test fichiers** :
```
-e  Existe
-f  Fichier régulier
-d  Répertoire
-s  Non vide
-b  Périphérique bloc (/dev/sda)
-c  Périphérique caractère (/dev/tty)
-p  Tube nommé (FIFO)
-L  Lien symbolique
-r  Lisible
-w  Modifiable
-x  Exécutable
```

**Opérateurs test nombres** :
```
-eq  Égal (=)
-ne  Différent (≠)
-lt  Inférieur (<)
-le  Inférieur/égal (≤)
-gt  Supérieur (>)
-ge  Supérieur/égal (≥)
```

**Boucles syntax** :
```bash
for i in {1..10}; do...; done
while [ cond ]; do...; done
until [ cond ]; do...; done
```

---

## 📖 Résumé Final

Ce cours couvre les **bases du scripting Shell** pour LPIC-1 102 :

1. **Commandes combinées** : `;` (séquentiel), `&&` (succès), `||` (échec)

2. **Shebang** : 
   - `#!/bin/bash` spécifie interpréteur
   - `#!/usr/bin/env bash` portable (cherche PATH)

3. **Exécution scripts** :
   - Avec `+x` : `./script.sh`
   - Sans `+x` : `bash script.sh`

4. **Arguments** : 
   - `$1`, `$#`, `"$@"`, `$?`, `$$`
   - `"$@"` préserve arguments individuels
   - `"$*"` fusionne en une chaîne
   - `set` : Redéfinir paramètres
   - `shift` : Décaler paramètres

5. **Substitution** : `$(commande)` ou backticks

6. **exec** : Remplacer shell actuel (PID conservé, pas de retour)

7. **Conditions** :
   - `if / then / elif / else / fi`
   - `[ ]` (POSIX) ou `[[ ]]` (Bash étendu, gère variables vides)
   - Opérateurs : `=` (string), `-eq` (nombre), `-f` (fichier), `-b` (bloc), `-c` (caractère), `-p` (pipe)
   - `\( ... \)` : Groupement tests
   - Toujours quoter dans `[ ]` : `[ "$VAR" = "val" ]`
   - `grep -q` dans conditions : retourne 0/1

8. **read** : Lire entrées utilisateur
   - `-p` prompt, `-t` timeout, `-s` silent, `-n N` N caractères
   - Variables multiples avec IFS

9. **Boucles** :
   - `for` : Itération liste/séquence
   - `for` sans liste : utilise automatiquement `$@`
   - `while` : Tant que vrai
   - `while read; done < file` : Redirection après done
   - `until` : Jusqu'à vrai
   - `break` / `continue` / `exit`

10. **case** : Sélection multiple
    - Wildcards : `*` (tout), `?` (un car.), `[...]` (classe), `|` (OU)
    - Toujours terminer par `;;`

11. **select** : Menu interactif
    - Génère numérotation automatique
    - Utilise variable `PS3` pour prompt
    - `$REPLY` contient numéro saisi
    - Boucle jusqu'à `break`

12. **Arithmétique** :
    - `let VAR=EXPR` : Direct sans substitution
    - `expr $VAR + 1` : Externe, espaces obligatoires, `*` échappé
    - `$((EXPR))` : ✅ **RECOMMANDÉ** (rapide, propre)
    - `typeset -i VAR` : Variable entière (calculs auto)
    - `bc` : Flottants avec `scale=N`
    - Sans méthode arithmétique → traitement string !

13. **Fonctions** :
    - Déclaration : `nom() { commandes; }`
    - `return CODE` : Code sortie 0-255
    - `echo` + `$()` : Retourner valeurs/strings
    - `local` : Variables locales

14. **trap** : Capture signaux
    - `trap 'cmd' SIGNAL` : Exécuter commande
    - `trap '' SIGNAL` : Ignorer signal
    - `trap - SIGNAL` : Restaurer défaut
    - Signaux : SIGINT(2), SIGTERM(15), SIGHUP(1), EXIT
    - SIGKILL(9) **non capturable**
    - Usage : Nettoyage avant sortie

15. **mail** : Envoyer emails
    - Requiert MTA (postfix)
    - `mail -s "sujet" destinataire`
    - Corps via pipe ou heredoc

**Règles d'or** :
- ✅ Toujours quoter variables dans `[ ]` : `[ "$VAR" = "val" ]`
- ✅ Vérifier nombre arguments : `if [ $# -eq 0 ]`
- ✅ Tester fichiers avant usage : `if [ -f "$FILE" ]`
- ✅ Sauvegarder `$?` immédiatement : `RETVAL=$?`
- ✅ Utiliser `"$@"` pour arguments (préserve espaces)
- ✅ Privilégier `[[ ]]` pour Bash, `[ ]` pour POSIX
- ✅ Arithmétique : Préférer `$(())` à `let` ou `expr`
- ✅ Redirection `while read` après `done` : `done < file`
- ✅ Codes retour 0-255 seulement
- ✅ Toujours terminer `case` par `;;`
- ✅ `select` nécessite `break` pour sortir

Le scripting Shell est **fondamental** pour l'automation Linux et l'examen LPIC-1 102.

---

*Documentation créée le 10 février 2026 - Formation LPIC-1 102 - Objectif 105.2*

