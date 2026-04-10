# 📂 Gestion de Fichiers Basiques - Part 2 : find et Regex

**Module** : GNU and Unix Commands  
**Cours** : Perform Basic File Management Part 2 - Using find  
**Objectif** : Recherche avancée fichiers avec find, grep et expressions régulières

---

## 🔍 Expressions Régulières (Regex)

### Regex : Concepts

**Expression régulière** = **Motif de recherche** (pattern) pour filtrer texte

**Analogie** 🎯 :
- **Regex** = Tamis avec mailles configurables (laisse passer ce qui correspond au motif)
- **Motif** = Configuration du filtre (taille mailles, forme)

**Usages** :
- ✅ Recherche texte (grep)
- ✅ Validation formats (emails, IPs)
- ✅ Extraction données structurées
- ✅ Remplacement conditionnel (sed)

---

## 📐 Opérateurs Regex

### Opérateurs de Position

| Opérateur | Signification | Exemple | Correspondance |
|-----------|---------------|---------|----------------|
| `^` | **Début de ligne** | `^Hello` | Lignes commençant par "Hello" |
| `$` | **Fin de ligne** | `world$` | Lignes finissant par "world" |
| `^$` | **Ligne vide** | `^$` | Lignes sans contenu |

**Exemples** :

```bash
# Fichier
cat file.txt
# Hello world
# world peace
# Goodbye world
# Hello universe

# Lignes commençant par "Hello"
grep '^Hello' file.txt
# Hello world
# Hello universe

# Lignes finissant par "world"
grep 'world$' file.txt
# Hello world
# Goodbye world

# Lignes vides
grep '^$' file.txt
# (affiche lignes vides)
```

---

### Opérateurs de Caractères

| Opérateur | Signification | Exemple | Correspondance |
|-----------|---------------|---------|----------------|
| `.` | **N'importe quel caractère** (1 seul) | `c.t` | cat, cot, c9t, c@t |
| `*` | **0 ou plusieurs** occurrences précédent | `ca*t` | ct, cat, caat, caaat |
| `+` | **1 ou plusieurs** occurrences précédent | `ca+t` | cat, caat, caaat (pas ct) |
| `?` | **0 ou 1** occurrence précédent | `colou?r` | color, colour (pas colouur) |

**Exemples** :

```bash
# Fichier
cat words.txt
# cat
# cot
# cart
# ct
# caat
# c9t

# Point : N'importe quel caractère (1 seul)
grep 'c.t' words.txt
# cat
# cot
# c9t

# Astérisque : 0 ou plusieurs 'a'
grep 'ca*t' words.txt
# ct   ← 0 fois 'a'
# cat  ← 1 fois 'a'
# caat ← 2 fois 'a'
```

---

### Opérateurs de Quantité

| Opérateur | Signification | Exemple | Correspondance |
|-----------|---------------|---------|----------------|
| `{n}` | **Exactement n** occurrences | `a{3}` | aaa (exactement 3 fois 'a') |
| `{n,m}` | **Entre n et m** occurrences | `a{2,4}` | aa, aaa, aaaa |
| `{n,}` | **Minimum n** occurrences | `a{3,}` | aaa, aaaa, aaaaa... |
| `{,m}` | **Maximum m** occurrences | `a{,3}` | ε, a, aa, aaa (0 à 3) |

**Exemples** :

```bash
# Fichier
cat numbers.txt
# 123
# 12345
# 1234567
# 12

# Exactement 5 chiffres
grep -E '^[0-9]{5}$' numbers.txt
# 12345

# Entre 3 et 5 chiffres
grep -E '^[0-9]{3,5}$' numbers.txt
# 123
# 12345

# Minimum 6 chiffres
grep -E '^[0-9]{6,}$' numbers.txt
# 1234567

# Maximum 3 chiffres
grep -E '^[0-9]{,3}$' numbers.txt
# 123
# 12
```

**💡 Syntaxe {}** :
- `{3,5}` → Plage autorisée de **3 à 5** occurrences
- `{,5}` → De **0 à 5** occurrences
- `{5,}` → De **5 à illimité** occurrences
- `{5}` → **Exactement 5** occurrences

---

### Opérateurs de Choix et Groupes

| Opérateur | Signification | Exemple | Correspondance |
|-----------|---------------|---------|----------------|
| `\|` | **OU logique** | `cat\|dog` | cat OU dog |
| `[]` | **Classe caractères** | `[abc]` | a OU b OU c |
| `[^]` | **Négation classe** | `[^abc]` | Tout SAUF a, b, c |
| `()` | **Groupe** | `(cat)+` | cat, catcat, catcatcat |

**Exemples** :

```bash
# Fichier
cat animals.txt
# cat
# dog
# bird
# fish

# OU logique : cat OU dog
grep -E 'cat|dog' animals.txt
# cat
# dog

# Classe : Commence par c, d ou f
grep '^[cdf]' animals.txt
# cat
# dog
# fish

# Négation : Pas c, d ou f
grep '^[^cdf]' animals.txt
# bird
```

---

### Classes de Caractères Raccourcis

| Classe | Signification | Équivalent |
|--------|---------------|------------|
| `[0-9]` | Chiffres | 0123456789 |
| `[a-z]` | Minuscules | abcdefgh...xyz |
| `[A-Z]` | Majuscules | ABCDEFGH...XYZ |
| `[a-zA-Z]` | Lettres (toutes) | a-z + A-Z |
| `[a-zA-Z0-9]` | Alphanumériques | Lettres + chiffres |
| `[^0-9]` | Non-chiffres | Tout sauf 0-9 |

**Exemples** :

```bash
# Lignes contenant chiffres
grep '[0-9]' file.txt

# Lignes commençant par majuscule
grep '^[A-Z]' file.txt

# Lignes contenant uniquement lettres
grep '^[a-zA-Z]*$' file.txt

# Lignes ne contenant PAS de chiffres
grep '^[^0-9]*$' file.txt
```

---

### Groupes avec Parenthèses

**Rôle** : Appliquer opérateurs regex sur **groupe** de caractères

```bash
# Sans groupe : 'ha' répété
grep 'haha' file.txt
# haha

# Avec groupe : '(ha)' répété
grep -E '(ha)+' file.txt
# ha      ← 1 fois
# haha    ← 2 fois
# hahaha  ← 3 fois

# Groupe avec quantificateur
grep -E '(abc){2}' file.txt
# abcabc  ← Exactement 2 fois 'abc'

# Groupe avec OU
grep -E '(cat|dog)s' file.txt
# cats
# dogs
```

**Analogie** 🎲 :
- **Sans groupe** : Dé individuel (lance 1 dé)
- **Avec groupe** : Lot de dés (lance ensemble plusieurs fois)

---

## 🔧 grep : Recherche dans Fichiers

### grep Basique vs Étendu

**grep par défaut** = Expressions régulières **basiques** (BRE - Basic Regular Expressions)

**Problème** : Opérateurs avancés nécessitent **échappement** avec `\`

```bash
# ❌ grep basique : + ne fonctionne pas
grep 'ca+t' file.txt
# Cherche littéralement "ca+t" (pas regex)

# ✅ grep basique avec échappement
grep 'ca\+t' file.txt
# cat, caat, caaat (regex)

# ❌ grep basique : ? ne fonctionne pas
grep 'colou?r' file.txt
# Cherche littéralement "colou?r"

# ✅ grep basique avec échappement
grep 'colou\?r' file.txt
# color, colour
```

---

### Option -E : Extended Regex

**Syntaxe** :
```bash
grep -E 'pattern' fichier
```

**Effet** : Active expressions régulières **étendues** (ERE - Extended Regular Expressions)

**Opérateurs automatiques** (sans `\`) :
- `+` → 1 ou plusieurs
- `?` → 0 ou 1
- `{}` → Quantificateurs
- `()` → Groupes
- `|` → OU logique

```bash
# ✅ grep -E : Pas d'échappement nécessaire
grep -E 'ca+t' file.txt
# cat, caat, caaat

grep -E 'colou?r' file.txt
# color, colour

grep -E '[0-9]{3}' file.txt
# 123, 456, 789 (3 chiffres)

grep -E '(cat|dog)' file.txt
# cat
# dog
```

---

### egrep : Alias de grep -E

**egrep** = **E**xtended **grep** (alias historique)

```bash
# Équivalents
grep -E 'pattern' file.txt
egrep 'pattern' file.txt

# Exemples
egrep 'ca+t' file.txt       # Même résultat que grep -E
egrep 'colou?r' file.txt    # Même résultat que grep -E
egrep '[0-9]{3,5}' file.txt # Même résultat que grep -E
```

**💡 Recommandation moderne** : Utiliser `grep -E` (egrep déprécié dans certaines distributions)

---

### Échappement Métacaractères

**Problème** : Chercher caractères **littéraux** ayant fonction regex

**Métacaractères à échapper** : `. * + ? { } [ ] ( ) | ^ $ \`

```bash
# Chercher point littéral (pas "n'importe quel caractère")
grep '\.' file.txt
# 192.168.1.1  ← Contient points

# Chercher astérisque littéral
grep '\*' file.txt
# **Important**  ← Contient *

# Chercher parenthèses littérales
grep '(\|)' file.txt
# (example)  ← Contient ( et )

# Chercher dollar littéral
grep '\$' file.txt
# Price: $100  ← Contient $

# Chercher crochets littéraux
grep '\[' file.txt
# [NOTE]  ← Contient [
```

**💡 Règle** : Backslash `\` avant métacaractère pour recherche littérale

---

### Comparaison BRE vs ERE

| Opérateur | BRE (grep) | ERE (grep -E / egrep) |
|-----------|------------|-----------------------|
| `+` (1 ou plus) | `\+` | `+` |
| `?` (0 ou 1) | `\?` | `?` |
| `{}` (quantité) | `\{n,m\}` | `{n,m}` |
| `()` (groupe) | `\(abc\)` | `(abc)` |
| `\|` (OU) | `\|` | `\|` |
| `.` (any) | `.` | `.` |
| `*` (0 ou plus) | `*` | `*` |
| `[]` (classe) | `[]` | `[]` |
| `^` (début) | `^` | `^` |
| `$` (fin) | `$` | `$` |

---

## 🔎 grep : Options Courantes

### Options Affichage

```bash
# Numéros de lignes
grep -n 'pattern' file.txt
# 5:matching line
# 12:another match

# Compter occurrences
grep -c 'pattern' file.txt
# 3

# Afficher seulement noms fichiers
grep -l 'pattern' *.txt
# file1.txt
# file3.txt

# Inverser recherche (lignes NE contenant PAS)
grep -v 'pattern' file.txt
# (toutes lignes sans 'pattern')

# Ignorer casse
grep -i 'hello' file.txt
# Hello
# HELLO
# hello
```

---

### Options Récursives

```bash
# Recherche récursive dans dossiers
grep -r 'pattern' /path/to/dir/

# Récursif avec numéros lignes
grep -rn 'TODO' /home/user/project/
# /home/user/project/main.py:15:# TODO: Fix this
# /home/user/project/utils.py:42:# TODO: Optimize

# Récursif avec contexte
grep -r -A 2 -B 2 'ERROR' /var/log/
# (2 lignes avant/après chaque match)
```

---

### Options Contexte

```bash
# Lignes APRÈS match
grep -A 3 'pattern' file.txt
# (affiche match + 3 lignes suivantes)

# Lignes AVANT match
grep -B 2 'pattern' file.txt
# (affiche 2 lignes précédentes + match)

# Lignes AUTOUR match
grep -C 2 'pattern' file.txt
# (affiche 2 lignes avant + match + 2 lignes après)
```

---

## 🔍 find : Recherche Fichiers

### find : Syntaxe de Base

**Syntaxe** :
```bash
find [chemin] [critères] [actions]
```

**Exemples** :

```bash
# Tous fichiers dossier actuel
find .

# Tous fichiers /home/user
find /home/user

# Tous fichiers système (depuis racine)
find /
```

---

### find : Recherche par Nom

**Option** : `-name` (sensible casse) ou `-iname` (ignore casse)

```bash
# Fichiers nommés "config.txt"
find /etc -name "config.txt"

# Fichiers commençant par "log"
find /var/log -name "log*"

# Fichiers finissant par ".conf"
find /etc -name "*.conf"

# Ignorer casse
find /home -iname "readme.txt"
# README.txt
# readme.txt
# ReadMe.txt
```

**⚠️ Important** : Entourer pattern de quotes pour éviter expansion shell

---

### find : Recherche par Type

**Option** : `-type [f|d|l]`

```bash
# Seulement fichiers réguliers
find /home/user -type f

# Seulement répertoires
find /home/user -type d

# Seulement liens symboliques
find /home/user -type l

# Fichiers .txt seulement (pas dossiers)
find . -type f -name "*.txt"
```

**Types** :
- `f` → Fichier régulier
- `d` → Répertoire (directory)
- `l` → Lien symbolique (link)
- `b` → Block device
- `c` → Character device

---

### find : Recherche par Taille

**Option** : `-size [+|-]N[c|k|M|G]`

```bash
# Fichiers exactement 100 Ko
find . -size 100k

# Fichiers > 10 Mo
find . -size +10M

# Fichiers < 1 Go
find . -size -1G

# Fichiers > 100 octets
find . -size +100c

# Fichiers entre 1M et 10M
find . -size +1M -size -10M
```

**Unités** :
- `c` → Octets (bytes)
- `k` → Kilo-octets
- `M` → Méga-octets
- `G` → Giga-octets

**Préfixes** :
- `+N` → Plus grand que N
- `-N` → Plus petit que N
- `N` → Exactement N

---

### find : Recherche par Temps

**Options** :
- `-mtime` → Dernière **modification** (modified)
- `-atime` → Dernier **accès** (accessed)
- `-ctime` → Dernier changement **métadonnées** (changed)

```bash
# Modifiés dans les 7 derniers jours
find . -mtime -7

# Modifiés il y a exactement 10 jours
find . -mtime 10

# Modifiés il y a plus de 30 jours
find . -mtime +30

# Accédés dans les 2 derniers jours
find . -atime -2

# Métadonnées changées > 60 jours
find . -ctime +60
```

**⚠️ Temps en jours** (24h)

**Alternatives minutes** :
- `-mmin` → Minutes modification
- `-amin` → Minutes accès
- `-cmin` → Minutes changement métadonnées

```bash
# Modifiés dans les 60 dernières minutes
find . -mmin -60

# Accédés il y a plus de 120 minutes
find . -amin +120
```

---

### find : Recherche par Permissions

**Option** : `-perm [mode]`

```bash
# Fichiers avec permissions exactes 644
find . -perm 644

# Fichiers avec au moins permissions 755
find . -perm -755

# Fichiers exécutables par propriétaire
find . -perm /u+x

# Fichiers world-writable (dangereux)
find / -perm -002 -type f

# Fichiers SUID (dangereux si mal configurés)
find / -perm -4000 -type f
```

---

### find : Recherche par Propriétaire

**Options** :
- `-user` → Propriétaire utilisateur
- `-group` → Propriétaire groupe
- `-uid` → User ID
- `-gid` → Group ID

```bash
# Fichiers appartenant à 'john'
find /home -user john

# Fichiers du groupe 'developers'
find /var/www -group developers

# Fichiers UID 1000
find / -uid 1000

# Fichiers sans propriétaire valide (orphelins)
find / -nouser

# Fichiers sans groupe valide
find / -nogroup
```

---

### find : Actions sur Résultats

**Actions** :
- `-print` → Afficher chemin (défaut)
- `-delete` → Supprimer fichiers
- `-exec` → Exécuter commande
- `-ok` → Exécuter avec confirmation

---

#### Action -delete

```bash
# Supprimer fichiers .tmp
find . -name "*.tmp" -delete

# Supprimer fichiers vides
find . -type f -empty -delete

# Supprimer fichiers > 30 jours
find /tmp -type f -mtime +30 -delete
```

**⚠️ DANGER** : Pas de corbeille, suppression définitive !

---

#### Action -exec

**Syntaxe** :
```bash
find [critères] -exec commande {} \;
```

**`{}`** = Remplacé par chemin fichier trouvé  
**`\;`** = Fin commande (échappement `;`)

```bash
# Lister détails fichiers .conf
find /etc -name "*.conf" -exec ls -lh {} \;

# Copier fichiers .txt dans /backup
find . -name "*.txt" -exec cp {} /backup/ \;

# Changer permissions fichiers .sh
find . -name "*.sh" -exec chmod +x {} \;

# Compter lignes tous fichiers .py
find . -name "*.py" -exec wc -l {} \;

# Archiver fichiers logs
find /var/log -name "*.log" -exec gzip {} \;
```

**Alternative** : `+` au lieu de `\;` (plus efficace, regroupe arguments)

```bash
# Exécuter commande UNE fois avec tous fichiers
find . -name "*.txt" -exec rm {} +
# Équivalent à : rm file1.txt file2.txt file3.txt...

# Vs \; exécute pour CHAQUE fichier
find . -name "*.txt" -exec rm {} \;
# Équivalent à : rm file1.txt; rm file2.txt; rm file3.txt...
```

---

#### Action -ok (Confirmation)

**Syntaxe** : Identique `-exec` mais demande confirmation

```bash
# Supprimer avec confirmation
find . -name "*.bak" -ok rm {} \;
# < rm ... file1.bak > ? y
# < rm ... file2.bak > ? n
# (y = oui, n = non)

# Archiver avec confirmation
find . -name "*.log" -ok gzip {} \;
```

**💡 Sécurité** : Utiliser `-ok` au lieu de `-exec` pour actions destructrices

---

### find : Combinaisons Logiques

**Opérateurs** :
- `-and` (ou juste espace) → ET logique
- `-or` → OU logique
- `-not` (ou `!`) → NON logique
- `()` → Groupes (échapper `\(` `\)`)

```bash
# Fichiers .txt ET modifiés < 7 jours
find . -name "*.txt" -and -mtime -7
find . -name "*.txt" -mtime -7  # Équivalent (AND implicite)

# Fichiers .txt OU .md
find . -name "*.txt" -or -name "*.md"

# Fichiers PAS .tmp
find . -not -name "*.tmp"
find . ! -name "*.tmp"  # Équivalent

# Fichiers .log ET > 100M
find . -name "*.log" -and -size +100M

# (Fichiers .txt OU .md) ET modifiés < 7 jours
find . \( -name "*.txt" -or -name "*.md" \) -and -mtime -7
```

---

### find : Profondeur Recherche

**Options** :
- `-maxdepth N` → Maximum N niveaux profondeur
- `-mindepth N` → Minimum N niveaux profondeur

```bash
# Seulement dossier actuel (pas sous-dossiers)
find . -maxdepth 1 -name "*.txt"

# Maximum 2 niveaux profondeur
find /home -maxdepth 2 -name "config"

# Au moins 3 niveaux profondeur
find . -mindepth 3 -type f

# Entre 2 et 4 niveaux
find . -mindepth 2 -maxdepth 4 -name "*.log"
```

---

## 📋 Antisèche - Regex et find

### Regex Opérateurs

| Opérateur | Signification | Exemple |
|-----------|---------------|---------|
| `^` | Début ligne | `^Hello` |
| `$` | Fin ligne | `world$` |
| `.` | Any caractère | `c.t` |
| `*` | 0 ou + | `ca*t` |
| `+` | 1 ou + | `ca+t` (avec -E) |
| `?` | 0 ou 1 | `colou?r` (avec -E) |
| `{n}` | Exactement n | `a{3}` (avec -E) |
| `{n,m}` | Entre n et m | `a{2,4}` (avec -E) |
| `\|` | OU | `cat\|dog` (avec -E) |
| `[]` | Classe | `[abc]` |
| `[^]` | Négation | `[^abc]` |
| `()` | Groupe | `(cat)+` (avec -E) |

---

### grep Commandes

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `grep 'pattern' file` | Chercher motif | `grep 'error' log.txt` |
| `grep -E 'pattern' file` | Regex étendu | `grep -E 'ca+t' file.txt` |
| `egrep 'pattern' file` | Alias grep -E | `egrep 'colou?r' file.txt` |
| `grep -i 'pattern' file` | Ignorer casse | `grep -i 'hello' file.txt` |
| `grep -n 'pattern' file` | Avec numéros lignes | `grep -n 'TODO' code.py` |
| `grep -v 'pattern' file` | Inverser (PAS) | `grep -v '#' config.txt` |
| `grep -r 'pattern' dir/` | Récursif | `grep -r 'ERROR' /var/log/` |
| `grep -c 'pattern' file` | Compter | `grep -c 'error' log.txt` |
| `grep -A 3 'pattern' file` | 3 lignes après | `grep -A 3 'ERROR' log.txt` |
| `grep -B 2 'pattern' file` | 2 lignes avant | `grep -B 2 'ERROR' log.txt` |

---

### find Commandes

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `find . -name "*.txt"` | Par nom | `find /etc -name "*.conf"` |
| `find . -iname "readme*"` | Nom (ignore casse) | `find . -iname "README*"` |
| `find . -type f` | Fichiers seulement | `find /home -type f` |
| `find . -type d` | Dossiers seulement | `find /var -type d` |
| `find . -size +10M` | Taille > 10M | `find . -size +100M` |
| `find . -mtime -7` | Modifiés < 7 jours | `find . -mtime -30` |
| `find . -user john` | Par propriétaire | `find /home -user alice` |
| `find . -perm 644` | Par permissions | `find . -perm 755` |
| `find . -empty` | Fichiers vides | `find /tmp -empty -delete` |
| `find . -name "*.tmp" -delete` | Supprimer | `find . -name "*.bak" -delete` |
| `find . -name "*.sh" -exec chmod +x {} \;` | Exécuter commande | `find . -name "*.log" -exec gzip {} \;` |

---

## 🎓 Points Clés pour l'Examen

✅ **^ et $** : Ancres début/fin ligne  
✅ **.** : N'importe quel caractère (1 seul)  
✅ **\*** : 0 ou plusieurs occurrences  
✅ **+ et ?** : Nécessitent `-E` (ou `\+` `\?` en BRE)  
✅ **{n,m}** : Quantificateurs (min, max occurrences)  
✅ **[]** : Classe caractères (`[a-z]`, `[0-9]`)  
✅ **[^]** : Négation classe (`[^0-9]` = pas chiffre)  
✅ **()** : Groupes pour appliquer opérateurs  
✅ **|** : OU logique (avec `-E`)  
✅ **\\** : Échapper métacaractères (chercher littéral)  
✅ **grep -E** ou **egrep** : Regex étendues (ERE)  
✅ **grep basique** : Nécessite `\` pour +, ?, {}, (), |  
✅ **find -name** : Recherche par nom (quotes obligatoires)  
✅ **find -type [f|d|l]** : Par type (file, directory, link)  
✅ **find -size [+|-]N[k|M|G]** : Par taille  
✅ **find -mtime [+|-]N** : Par date modification (jours)  
✅ **find -exec command {} \;** : Exécuter sur résultats  
✅ **find -delete** : Supprimer (DANGER définitif)  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Validation Format Email

```bash
# Fichier emails.txt
cat emails.txt
# john@example.com
# alice.smith@company.co.uk
# invalid-email
# bob+filter@domain.org
# bad@
# charlie@test.com

# Regex email simple (basique)
grep -E '^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$' emails.txt
# john@example.com
# alice.smith@company.co.uk
# bob+filter@domain.org
# charlie@test.com

# Explication :
# ^                  → Début ligne
# [a-zA-Z0-9._%+-]+  → 1+ caractères valides avant @
# @                  → @ littéral
# [a-zA-Z0-9.-]+     → 1+ caractères domaine
# \.                 → Point littéral échappé
# [a-zA-Z]{2,}       → 2+ lettres (extension)
# $                  → Fin ligne
```

---

### Scénario 2 : Extraction Adresses IP Logs

```bash
# Logs Apache
cat access.log
# 192.168.1.100 - - [02/Feb/2026] "GET /" 200
# 203.0.113.50 - - [02/Feb/2026] "POST /api" 201
# Invalid line without IP
# 10.0.0.25 - - [02/Feb/2026] "GET /about" 200

# Regex IP (simplifiée)
grep -E '^[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}' access.log
# 192.168.1.100 - - [02/Feb/2026] "GET /" 200
# 203.0.113.50 - - [02/Feb/2026] "POST /api" 201
# 10.0.0.25 - - [02/Feb/2026] "GET /about" 200

# Extraire seulement IPs
grep -oE '^[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}' access.log
# 192.168.1.100
# 203.0.113.50
# 10.0.0.25
```

---

### Scénario 3 : Recherche Numéros Téléphone

```bash
# Fichier contacts.txt
cat contacts.txt
# John: 06 12 34 56 78
# Alice: 0612345678
# Bob: +33 6 12 34 56 78
# Charlie: 01-23-45-67-89
# Invalid: 123456

# Format avec espaces (06 12 34 56 78)
grep -E '0[0-9] [0-9]{2} [0-9]{2} [0-9]{2} [0-9]{2}' contacts.txt
# John: 06 12 34 56 78

# Format sans espaces (0612345678)
grep -E '0[0-9]{9}' contacts.txt
# Alice: 0612345678

# Format international (+33 6 12 34 56 78)
grep -E '\+33 [0-9] [0-9]{2} [0-9]{2} [0-9]{2} [0-9]{2}' contacts.txt
# Bob: +33 6 12 34 56 78

# Tous formats
grep -E '(0[0-9] [0-9]{2} [0-9]{2} [0-9]{2} [0-9]{2}|0[0-9]{9}|\+33 [0-9] [0-9]{2} [0-9]{2} [0-9]{2} [0-9]{2})' contacts.txt
```

---

### Scénario 4 : Nettoyage Logs Anciens

```bash
# Supprimer logs > 30 jours
find /var/log -name "*.log" -mtime +30 -delete

# Supprimer logs > 30 jours ET > 100M
find /var/log -name "*.log" -mtime +30 -size +100M -delete

# Lister avant suppression (sécurité)
find /var/log -name "*.log" -mtime +30 -ls

# Archiver avant suppression
find /var/log -name "*.log" -mtime +30 -exec gzip {} \;

# Déplacer vers archive
find /var/log -name "*.log" -mtime +30 -exec mv {} /archive/ \;
```

---

### Scénario 5 : Recherche Fichiers Configuration

```bash
# Tous fichiers .conf dans /etc
find /etc -name "*.conf" -type f

# Fichiers .conf modifiés < 7 jours
find /etc -name "*.conf" -mtime -7

# Fichiers .conf contenant "database"
find /etc -name "*.conf" -type f -exec grep -l "database" {} \;

# Fichiers .conf > 1M (suspects)
find /etc -name "*.conf" -size +1M -ls

# Copier vers backup
find /etc -name "*.conf" -type f -exec cp {} /backup/configs/ \;
```

---

### Scénario 6 : Audit Sécurité Fichiers

```bash
# Fichiers world-writable (DANGEREUX)
find / -perm -002 -type f -ls 2>/dev/null

# Fichiers SUID root (vérifier légitimité)
find / -user root -perm -4000 -type f 2>/dev/null

# Fichiers sans propriétaire (orphelins)
find / -nouser -ls 2>/dev/null

# Fichiers modifiés dernières 24h (intrusion ?)
find /etc /bin /sbin -mtime 0 -type f

# Scripts shell exécutables world-writable
find / -name "*.sh" -perm -002 -type f 2>/dev/null
```

---

### Scénario 7 : Recherche Code Source

```bash
# Tous fichiers Python projet
find ./project -name "*.py" -type f

# Fichiers Python contenant "TODO"
find . -name "*.py" -exec grep -Hn "TODO" {} \;

# Fichiers Python modifiés aujourd'hui
find . -name "*.py" -mtime 0

# Compter lignes tous fichiers Python
find . -name "*.py" -exec wc -l {} + | tail -1

# Fichiers Python > 500 lignes (refactoring ?)
find . -name "*.py" -type f -exec sh -c 'lines=$(wc -l < "$1"); [ "$lines" -gt 500 ] && echo "$1: $lines lines"' _ {} \;
```

---

### Scénario 8 : Recherche Motifs Complexes

```bash
# Lignes commençant par majuscule finissant par point
grep -E '^[A-Z].*\.$' file.txt

# Lignes contenant 3 chiffres consécutifs minimum
grep -E '[0-9]{3,}' file.txt

# Lignes contenant "error" OU "warning" (ignore casse)
grep -iE 'error|warning' log.txt

# Lignes NE contenant PAS de chiffres
grep -E '^[^0-9]*$' file.txt

# Lignes vides OU commentaires (#)
grep -E '(^$|^#)' config.txt

# IPv4 valide (motif simplifié)
grep -E '([0-9]{1,3}\.){3}[0-9]{1,3}' file.txt

# URLs HTTP/HTTPS
grep -E 'https?://[a-zA-Z0-9.-]+' file.txt
```

---

### Scénario 9 : Maintenance Disque Espace

```bash
# Fichiers > 1G
find / -size +1G -type f -ls 2>/dev/null

# Top 20 plus gros fichiers
find / -type f -exec du -h {} + 2>/dev/null | sort -rh | head -20

# Fichiers temporaires > 7 jours
find /tmp -type f -mtime +7 -delete

# Dossiers vides (candidats suppression)
find /home -type d -empty

# Fichiers dupliqués par taille (suspect)
find . -type f -exec du -b {} + | sort -n | uniq -d -w 10
```

---

### Scénario 10 : Recherche Permissions Spécifiques

```bash
# Scripts shell non exécutables (corriger)
find . -name "*.sh" -type f ! -perm -111

# Rendre exécutables tous scripts
find . -name "*.sh" -type f -exec chmod +x {} \;

# Fichiers exécutables PAS .sh/.py/.pl
find . -type f -perm -111 ! -name "*.sh" ! -name "*.py" ! -name "*.pl"

# Fichiers propriétaire www-data (web)
find /var/www -user www-data -type f

# Changer propriétaire tous fichiers .php
find /var/www -name "*.php" -exec chown www-data:www-data {} \;
```

---

## ⚠️ Erreurs Courantes

### Erreur 1 : Oublier -E pour Opérateurs Avancés

```bash
# ❌ ERREUR : + sans -E
grep 'ca+t' file.txt
# Cherche littéralement "ca+t"

# ✅ SOLUTION 1 : grep -E
grep -E 'ca+t' file.txt

# ✅ SOLUTION 2 : Échapper avec \
grep 'ca\+t' file.txt
```

---

### Erreur 2 : Oublier Quotes find

```bash
# ❌ RISQUE : Pas de quotes
find . -name *.txt
# Shell expande *.txt AVANT find

# Si dossier contient file1.txt :
# Commande devient : find . -name file1.txt
# Cherche seulement file1.txt !

# ✅ CORRECT : Quotes
find . -name "*.txt"
# find reçoit littéralement *.txt
```

---

### Erreur 3 : Échapper Points Incorrectement

```bash
# ❌ ERREUR : Point non échappé dans regex IP
grep -E '[0-9]+.[0-9]+.[0-9]+.[0-9]+' file.txt
# . = N'importe quel caractère !
# Matche 192X168X1X100

# ✅ CORRECT : Échapper points
grep -E '[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+' file.txt
# \. = Point littéral
```

---

### Erreur 4 : find -delete Sans Vérification

```bash
# ❌ DANGER : Supprimer directement
find . -name "*.bak" -delete
# Pas de confirmation, suppression définitive !

# ✅ SÉCURITÉ : Lister avant
find . -name "*.bak" -ls
# Vérifier résultats

# Puis supprimer
find . -name "*.bak" -delete

# OU : Utiliser -ok (confirmation)
find . -name "*.bak" -ok rm {} \;
```

---

### Erreur 5 : Syntaxe -exec Incorrecte

```bash
# ❌ ERREUR : Oublier {} ou \;
find . -name "*.txt" -exec rm
# Syntaxe incomplète

# ❌ ERREUR : Oublier échapper ;
find . -name "*.txt" -exec rm {} ;
# Shell interprète ; (erreur)

# ✅ CORRECT : {} et \;
find . -name "*.txt" -exec rm {} \;

# ✅ OU : {} et +
find . -name "*.txt" -exec rm {} +
```

---

### Erreur 6 : Confusion BRE vs ERE

```bash
# ❌ Mélange syntaxes
grep -E 'ca\+t' file.txt
# grep -E n'a PAS besoin de \

# ✅ CORRECT
grep -E 'ca+t' file.txt  # ERE
grep 'ca\+t' file.txt    # BRE
```

---

### Erreur 7 : Classe Négation Mal Placée

```bash
# ❌ ERREUR : ^ pas au début classe
grep '[a-z^0-9]' file.txt
# Cherche a-z OU ^ OU 0-9 (^ littéral)

# ✅ CORRECT : ^ au début
grep '[^a-z0-9]' file.txt
# Cherche PAS a-z ni 0-9
```

---

## 📦 cpio : Archivage Fichiers

### cpio : Concepts

**cpio** = **C**o**p**y **I**n and **O**ut (copie entrée/sortie)

**Rôle** : Créer/extraire **archives** fichiers

**Extension** : `.cpio`

**Analogie** 📦 :
- **cpio** = Carton déménagement (range/sort fichiers)
- **Archive** = Carton fermé (contient plusieurs fichiers)
- **Liste fichiers** = Inventaire (ce qu'on met dans carton)

---

### cpio vs tar

| Caractéristique | cpio | tar |
|-----------------|------|-----|
| **Input** | Liste fichiers stdin | Arguments ligne commande |
| **Flexibilité** | ✅ Haute (pipe, find) | ⚠️ Moyenne |
| **Syntaxe** | Complexe | Simple |
| **Usage moderne** | ⚠️ Moins courant | ✅ Standard |
| **Backup système** | ✅ Excellent (avec find) | ✅ Excellent |

**💡 Quand utiliser cpio** :
- ✅ Combinaison avec `find` (fichiers sélectifs)
- ✅ Backup système complexe
- ✅ Restauration RPM (format utilisé)
- ⚠️ Sinon : `tar` plus simple

---

## 🎯 Modes cpio

### 3 Modes Principaux

| Mode | Option | Nom | Rôle |
|------|--------|-----|------|
| **Copy-out** | `-o` | Créer archive | Fichiers → Archive |
| **Copy-in** | `-i` | Extraire archive | Archive → Fichiers |
| **Copy-pass** | `-p` | Copier répertoires | Dir1 → Dir2 |

---

### Mode -o : Copy-Out (Créer Archive)

**Syntaxe** :
```bash
liste_fichiers | cpio -o > archive.cpio
```

**Exemple basique** :

```bash
# Lister fichiers à archiver
ls file1.txt file2.txt file3.txt

# Créer archive
ls file1.txt file2.txt file3.txt | cpio -o > archive.cpio

# Vérifier archive créée
ls -lh archive.cpio
# -rw-r--r-- 1 user user 10K Feb  2 10:30 archive.cpio
```

---

### Mode -i : Copy-In (Extraire Archive)

**Syntaxe** :
```bash
cpio -i < archive.cpio
```

**Options importantes** :
- `-d` → Créer **répertoires** manquants
- `-v` → **Verbose** (afficher fichiers extraits)
- `-t` → **Table** (lister contenu sans extraire)

**Exemples** :

```bash
# Extraire archive (répertoire actuel)
cpio -i < archive.cpio

# Extraire avec création répertoires
cpio -id < archive.cpio

# Extraire avec verbose
cpio -idv < archive.cpio
# file1.txt
# file2.txt
# dir1/file3.txt
# 3 blocks

# Lister contenu SANS extraire
cpio -it < archive.cpio
# file1.txt
# file2.txt
# dir1/file3.txt
```

---

### Mode -p : Copy-Pass (Copier Répertoires)

**Syntaxe** :
```bash
liste_fichiers | cpio -p destination/
```

**Rôle** : Copier fichiers vers autre répertoire (sans archive intermédiaire)

```bash
# Copier fichiers vers /backup
ls *.txt | cpio -pd /backup/

# Avec find (plus puissant)
find . -name "*.conf" | cpio -pd /backup/configs/

# Verbose
find . -type f | cpio -pdv /backup/
# file1.txt
# file2.txt
# dir1/file3.txt
```

**💡 Avantage** : Préserve structure répertoires avec `-d`

---

## ⚙️ Options cpio Courantes

### Options Principales

| Option | Signification | Usage |
|--------|---------------|-------|
| `-o` | **Copy-out** (créer archive) | `cpio -o > file.cpio` |
| `-i` | **Copy-in** (extraire archive) | `cpio -i < file.cpio` |
| `-p` | **Copy-pass** (copier direct) | `cpio -p /dest/` |
| `-d` | **Directories** (créer répertoires) | `cpio -id < file.cpio` |
| `-v` | **Verbose** (afficher détails) | `cpio -idv < file.cpio` |
| `-t` | **Table** (lister contenu) | `cpio -it < file.cpio` |
| `-u` | **Unconditional** (écraser existants) | `cpio -idu < file.cpio` |
| `-m` | **Modification time** (préserver dates) | `cpio -idm < file.cpio` |

---

### Option -d : Créer Répertoires

**Problème sans -d** : Extraction échoue si répertoires manquants

```bash
# Archive avec structure
cpio -it < archive.cpio
# dir1/file1.txt
# dir2/subdir/file2.txt

# ❌ Sans -d : Erreur si dir1/, dir2/subdir/ n'existent pas
cpio -i < archive.cpio
# cpio: dir1/file1.txt: No such file or directory

# ✅ Avec -d : Crée répertoires automatiquement
cpio -id < archive.cpio
# (dir1/ et dir2/subdir/ créés)
```

**💡 Toujours utiliser -d** pour extraction sûre

---

### Option -v : Verbose

```bash
# Création avec verbose
ls *.txt | cpio -ov > archive.cpio
# file1.txt
# file2.txt
# file3.txt
# 5 blocks

# Extraction avec verbose
cpio -idv < archive.cpio
# file1.txt
# file2.txt
# file3.txt
# 5 blocks
```

---

### Option -t : Table (Lister)

```bash
# Lister contenu archive
cpio -t < archive.cpio
# file1.txt
# file2.txt
# dir/file3.txt

# Avec verbose (détails)
cpio -tv < archive.cpio
# -rw-r--r-- 1 user user  1024 Feb  2 10:30 file1.txt
# -rw-r--r-- 1 user user  2048 Feb  2 10:31 file2.txt
# -rw-r--r-- 1 user user   512 Feb  2 10:32 dir/file3.txt
```

---

### Option -u : Unconditional (Écraser)

```bash
# Extraire et ÉCRASER fichiers existants
cpio -idu < archive.cpio

# Sans -u : Fichiers existants pas écrasés
cpio -id < archive.cpio
# (garde versions existantes)
```

---

## 🔗 Combinaison cpio + find

### Puissance cpio : Pipe avec find

**Avantage** : Sélection **précise** fichiers à archiver

```bash
# Archiver tous fichiers .conf
find /etc -name "*.conf" | cpio -o > configs.cpio

# Archiver fichiers modifiés < 7 jours
find . -mtime -7 -type f | cpio -o > recent.cpio

# Archiver fichiers > 10M
find . -size +10M -type f | cpio -o > large-files.cpio

# Archiver avec verbose
find /home/user/docs -type f | cpio -ov > docs-backup.cpio
```

---

### Backup Sélectif avec find

```bash
# Backup fichiers utilisateur (pas .cache, .tmp)
find /home/user -type f ! -path "*/.cache/*" ! -name "*.tmp" | cpio -o > user-backup.cpio

# Backup logs > 30 jours
find /var/log -name "*.log" -mtime +30 | cpio -o > old-logs.cpio

# Backup scripts shell
find /opt -name "*.sh" -type f | cpio -o > scripts-backup.cpio
```

---

## 🗜️ Compression avec cpio

### cpio + gzip

**cpio seul** : Pas de compression intégrée

**Solution** : Combiner avec **gzip** ou **bzip2**

```bash
# Créer archive compressée (.cpio.gz)
find . -type f | cpio -o | gzip > archive.cpio.gz

# Extraire archive compressée
gunzip -c archive.cpio.gz | cpio -id

# OU avec zcat
zcat archive.cpio.gz | cpio -id
```

---

### cpio + bzip2

```bash
# Créer avec bzip2 (meilleure compression)
find . -type f | cpio -o | bzip2 > archive.cpio.bz2

# Extraire
bunzip2 -c archive.cpio.bz2 | cpio -id

# OU avec bzcat
bzcat archive.cpio.bz2 | cpio -id
```

---

### Comparaison Compression

```bash
# Sans compression
find /var/log -name "*.log" | cpio -o > logs.cpio
ls -lh logs.cpio
# -rw-r--r-- 1 user user 100M Feb 2 10:30 logs.cpio

# Avec gzip
find /var/log -name "*.log" | cpio -o | gzip > logs.cpio.gz
ls -lh logs.cpio.gz
# -rw-r--r-- 1 user user 10M Feb 2 10:31 logs.cpio.gz  ← 90% compression

# Avec bzip2
find /var/log -name "*.log" | cpio -o | bzip2 > logs.cpio.bz2
ls -lh logs.cpio.bz2
# -rw-r--r-- 1 user user 8M Feb 2 10:32 logs.cpio.bz2  ← 92% compression
```

---

## 📋 Antisèche - cpio

### Commandes Essentielles

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `ls \| cpio -o > file.cpio` | Créer archive | `ls *.txt \| cpio -o > archive.cpio` |
| `cpio -i < file.cpio` | Extraire archive | `cpio -i < archive.cpio` |
| `cpio -id < file.cpio` | Extraire + créer dirs | `cpio -id < backup.cpio` |
| `cpio -it < file.cpio` | Lister contenu | `cpio -it < archive.cpio` |
| `cpio -idv < file.cpio` | Extraire verbose | `cpio -idv < archive.cpio` |
| `find . \| cpio -o > file.cpio` | Archive avec find | `find /etc -name "*.conf" \| cpio -o > configs.cpio` |
| `find . \| cpio -pd /dest/` | Copier répertoire | `find . -type f \| cpio -pd /backup/` |
| `find . \| cpio -o \| gzip > file.cpio.gz` | Archive compressée | `find . \| cpio -o \| gzip > backup.cpio.gz` |
| `zcat file.cpio.gz \| cpio -id` | Extraire compressée | `zcat backup.cpio.gz \| cpio -id` |

---

### Workflow Typique

```bash
# 1. Créer archive fichiers .txt
ls *.txt | cpio -ov > text-files.cpio

# 2. Lister contenu
cpio -it < text-files.cpio

# 3. Extraire dans nouveau dossier
mkdir restore
cd restore
cpio -idv < ../text-files.cpio

# 4. Archive compressée complète
find /home/user -type f | cpio -o | gzip > user-backup.cpio.gz

# 5. Restaurer compressée
zcat user-backup.cpio.gz | cpio -idv
```

---

## 🎓 Points Clés pour l'Examen

✅ **cpio** : Copy In and Out (archivage fichiers)  
✅ **Extension** : `.cpio`  
✅ **Mode -o** : Copy-out (créer archive depuis stdin)  
✅ **Mode -i** : Copy-in (extraire archive vers stdout)  
✅ **Mode -p** : Copy-pass (copier sans archive)  
✅ **Option -d** : Créer répertoires manquants (ESSENTIEL extraction)  
✅ **Option -v** : Verbose (afficher fichiers)  
✅ **Option -t** : Table (lister contenu)  
✅ **Option -u** : Unconditional (écraser existants)  
✅ **Input stdin** : `ls | cpio -o` ou `find | cpio -o`  
✅ **Redirection** : `>` pour créer, `<` pour extraire  
✅ **Compression** : Combiner avec gzip/bzip2 (pas intégré)  
✅ **Combinaison find** : Sélection précise fichiers  
✅ **Syntaxe création** : `liste | cpio -o > archive.cpio`  
✅ **Syntaxe extraction** : `cpio -id < archive.cpio`  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Backup Configuration Système

```bash
# Archiver tous fichiers .conf de /etc
find /etc -name "*.conf" -type f | cpio -ov > /backup/etc-configs.cpio

# Vérifier contenu archive
cpio -t < /backup/etc-configs.cpio | head -20

# Compter fichiers archivés
cpio -t < /backup/etc-configs.cpio | wc -l
# 156

# Restaurer dans dossier test
mkdir /tmp/restore-test
cd /tmp/restore-test
cpio -idv < /backup/etc-configs.cpio
```

---

### Scénario 2 : Backup Sélectif Projet

```bash
# Archiver seulement fichiers source (pas .git, node_modules)
cd /home/user/project

find . -type f \
  ! -path "*/.git/*" \
  ! -path "*/node_modules/*" \
  ! -path "*/dist/*" \
  ! -name "*.log" \
  | cpio -o | gzip > /backup/project-source.cpio.gz

# Vérifier taille
ls -lh /backup/project-source.cpio.gz
# -rw-r--r-- 1 user user 5.2M Feb 2 10:30 project-source.cpio.gz

# Lister contenu compressé
zcat /backup/project-source.cpio.gz | cpio -t | less
```

---

### Scénario 3 : Migration Fichiers Utilisateur

```bash
# Copier documents utilisateur vers nouveau serveur
cd /home/olduser

# Mode copy-pass (sans archive intermédiaire)
find . -type f \
  -name "*.pdf" -o \
  -name "*.docx" -o \
  -name "*.xlsx" \
  | cpio -pdv /mnt/newserver/home/newuser/

# Vérifier copie
ls -lR /mnt/newserver/home/newuser/ | head -20
```

---

### Scénario 4 : Backup Logs Anciens

```bash
# Archiver logs > 30 jours et supprimer originaux
find /var/log -name "*.log" -mtime +30 -type f | tee /tmp/old-logs-list.txt | cpio -o | gzip > /archive/old-logs-$(date +%Y%m%d).cpio.gz

# Vérifier archive créée
ls -lh /archive/old-logs-*.cpio.gz
# -rw-r--r-- 1 root root 25M Feb 2 10:30 /archive/old-logs-20260202.cpio.gz

# Vérifier contenu
zcat /archive/old-logs-20260202.cpio.gz | cpio -t | head -20

# Supprimer originaux (après vérification)
cat /tmp/old-logs-list.txt | xargs rm
```

---

### Scénario 5 : Backup Incrémental

```bash
# Jour 1 : Backup complet
find /data -type f | cpio -o | gzip > /backup/full-20260201.cpio.gz

# Jour 2 : Seulement fichiers modifiés depuis hier
find /data -type f -mtime -1 | cpio -o | gzip > /backup/incr-20260202.cpio.gz

# Jour 3 : Seulement fichiers modifiés depuis hier
find /data -type f -mtime -1 | cpio -o | gzip > /backup/incr-20260203.cpio.gz

# Restauration complète :
# 1. Restaurer full
zcat /backup/full-20260201.cpio.gz | cpio -id

# 2. Restaurer incrémentaux (écrasent avec -u)
zcat /backup/incr-20260202.cpio.gz | cpio -idu
zcat /backup/incr-20260203.cpio.gz | cpio -idu
```

---

### Scénario 6 : Archive Fichiers par Extension

```bash
# Archiver tous fichiers Python projet
find /home/user/projects -name "*.py" -type f | cpio -ov > /backup/python-files.cpio

# Archiver tous scripts shell
find /opt /usr/local/bin -name "*.sh" -type f | cpio -ov > /backup/shell-scripts.cpio

# Archiver tous configs Nginx/Apache
find /etc/nginx /etc/apache2 -name "*.conf" -type f | cpio -ov > /backup/webserver-configs.cpio

# Créer archive compressée tous fichiers images
find /var/www/html -type f \( -name "*.jpg" -o -name "*.png" -o -name "*.gif" \) | cpio -o | gzip > /backup/web-images.cpio.gz
```

---

### Scénario 7 : Restauration Sélective

```bash
# Archive complète
zcat /backup/full-backup.cpio.gz | cpio -t > /tmp/archive-content.txt

# Chercher fichier spécifique
grep "important-file.txt" /tmp/archive-content.txt
# /home/user/documents/important-file.txt

# Extraire SEULEMENT ce fichier
zcat /backup/full-backup.cpio.gz | cpio -idv "*/important-file.txt"

# Ou avec pattern
zcat /backup/full-backup.cpio.gz | cpio -idv "*.conf"
# (extrait tous .conf)
```

---

### Scénario 8 : Copie Structure Répertoire

```bash
# Copier structure /etc vers /backup (avec mode -p)
cd /etc
find . -type f | cpio -pdv /backup/etc-copy/

# Vérifier structure identique
diff -r /etc /backup/etc-copy/
# (affiche différences si existent)

# Comparer arborescences
tree /etc > /tmp/etc-tree.txt
tree /backup/etc-copy/ > /tmp/backup-tree.txt
diff /tmp/etc-tree.txt /tmp/backup-tree.txt
```

---

### Scénario 9 : Archive Fichiers Propriétaire

```bash
# Archiver tous fichiers utilisateur 'john'
find /home -user john -type f | cpio -o | gzip > /backup/john-files.cpio.gz

# Archiver fichiers groupe 'developers'
find /var/www -group developers -type f | cpio -o | gzip > /backup/dev-files.cpio.gz

# Archiver fichiers modifiés par utilisateur spécifique
find /data -user alice -mtime -7 -type f | cpio -ov > /backup/alice-recent.cpio
```

---

### Scénario 10 : Vérification Intégrité Archive

```bash
# Créer archive avec liste fichiers
find /data -type f | tee /tmp/backup-list.txt | cpio -o | gzip > /backup/data.cpio.gz

# Vérifier contenu archive correspond
zcat /backup/data.cpio.gz | cpio -t | sort > /tmp/archive-list.txt
cat /tmp/backup-list.txt | sort > /tmp/original-list.txt

# Comparer listes
diff /tmp/original-list.txt /tmp/archive-list.txt
# (vide si identique)

# Compter fichiers
echo "Original : $(wc -l < /tmp/original-list.txt)"
echo "Archive  : $(wc -l < /tmp/archive-list.txt)"
```

---

## ⚠️ Erreurs Courantes

### Erreur 1 : Oublier -d à l'Extraction

```bash
# ❌ ERREUR : Sans -d
cpio -i < archive.cpio
# cpio: dir1/file.txt: No such file or directory
# (échoue si dir1/ n'existe pas)

# ✅ CORRECT : Avec -d
cpio -id < archive.cpio
# (crée dir1/ automatiquement)
```

---

### Erreur 2 : Mauvaise Redirection

```bash
# ❌ ERREUR : > au lieu de <
cpio -i > archive.cpio
# (rien ne se passe, attend stdin)

# ❌ ERREUR : < au lieu de >
ls *.txt | cpio -o < archive.cpio
# (erreur syntaxe)

# ✅ CORRECT : Création
ls *.txt | cpio -o > archive.cpio

# ✅ CORRECT : Extraction
cpio -i < archive.cpio
```

**💡 Mnémotechnique** :
- **Créer** : `-o` → **O**ut → Sortie → `>`
- **Extraire** : `-i` → **I**n → Entrée → `<`

---

### Erreur 3 : Oublier Pipe pour -o

```bash
# ❌ ERREUR : -o sans input
cpio -o > archive.cpio
# (attend stdin indéfiniment, Ctrl+C pour arrêter)

# ✅ CORRECT : Avec liste fichiers
ls *.txt | cpio -o > archive.cpio

# OU
find . -type f | cpio -o > archive.cpio
```

---

### Erreur 4 : Extraction Sans Créer Dossier Destination

```bash
# ❌ RISQUE : Extraire dans /
cd /
cpio -id < /backup/full-system.cpio
# (mélange fichiers archive avec système !)

# ✅ CORRECT : Dossier dédié
mkdir /restore
cd /restore
cpio -id < /backup/full-system.cpio
# (isolé dans /restore/)
```

---

### Erreur 5 : Décompression Oubliée

```bash
# ❌ ERREUR : Archive compressée, pas décompressée
cpio -id < backup.cpio.gz
# cpio: premature end of archive
# (lit données compressées directement)

# ✅ CORRECT : Décompresser avant
zcat backup.cpio.gz | cpio -id

# OU
gunzip -c backup.cpio.gz | cpio -id
```

---

### Erreur 6 : Chemin Absolu vs Relatif

```bash
# Archive avec chemins absolus
find /home/user -type f | cpio -o > backup.cpio

# Extraction recrée /home/user dans répertoire actuel
cpio -it < backup.cpio
# /home/user/file1.txt  ← Chemin absolu !

# Problème : Extrait vers /home/user (pas répertoire actuel)
mkdir /tmp/restore
cd /tmp/restore
cpio -id < backup.cpio
ls
# (vide !)
ls /home/user/
# (fichiers extraits ici)

# ✅ SOLUTION : Chemins relatifs
cd /home/user
find . -type f | cpio -o > backup.cpio
# ./file1.txt  ← Chemin relatif

cd /tmp/restore
cpio -id < /home/user/backup.cpio
ls
# file1.txt  ← Extrait ici !
```

---

### Erreur 7 : Pas de Verbose (Debug Difficile)

```bash
# ❌ Sans verbose : Pas de retour
cpio -id < archive.cpio
# (silence complet, impossible savoir si succès)

# ✅ Avec verbose : Retour clair
cpio -idv < archive.cpio
# file1.txt
# file2.txt
# dir/file3.txt
# 10 blocks
# (confirmation fichiers extraits)
```

---

## 🗜️ Compression et Décompression Fichiers

### Compression : Concepts

**Compression** = Réduire taille fichier (économiser espace/bande passante)

**Analogie** 🧳 :
- **Fichier non compressé** = Valise avec vêtements pliés normalement
- **Fichier compressé** = Valise sous vide (même contenu, moins d'espace)
- **Décompression** = Ouvrir sachet, retrouver vêtements originaux

**Usages** :
- ✅ Économiser espace disque
- ✅ Transferts réseau plus rapides
- ✅ Archivage long terme
- ✅ Backups

---

### Outils Compression Linux

**3 outils natifs principaux** :

| Outil | Extension | Compression | Vitesse | Usage |
|-------|-----------|-------------|---------|-------|
| **gzip** | `.gz` | ⭐⭐⭐ Moyenne | ⭐⭐⭐⭐ Rapide | Standard Linux |
| **bzip2** | `.bz2` | ⭐⭐⭐⭐ Meilleure | ⭐⭐⭐ Moyen | Archivage |
| **xz** | `.xz` | ⭐⭐⭐⭐⭐ Excellente | ⭐⭐ Lent | Haute compression |

**Outil supplémentaire** :
- **zip** : `.zip` (compatibilité Windows/MacOS)

---

## 🔴 gzip : Compression Standard

### gzip : Syntaxe de Base

**Rôle** : Compression/décompression **gzip** (GNU zip)

```bash
# Compresser fichier
gzip file.txt
# Crée : file.txt.gz
# Original : SUPPRIMÉ

# Décompresser
gzip -d file.txt.gz
# OU
gunzip file.txt.gz
# Crée : file.txt
# Archive : SUPPRIMÉE
```

**⚠️ Important** : Par défaut, **supprime fichier original**

---

### Option -k : Keep (Garder Original)

```bash
# Compresser EN GARDANT original
gzip -k file.txt
# Crée : file.txt.gz
# Original : file.txt CONSERVÉ ✅

# Décompresser EN GARDANT archive
gzip -dk file.txt.gz
# Crée : file.txt
# Archive : file.txt.gz CONSERVÉE ✅
```

**💡 Recommandation** : Toujours utiliser `-k` pour sécurité

---

### Option -l : List (Lister Infos)

```bash
# Afficher infos archive
gzip -l file.txt.gz
#          compressed        uncompressed  ratio uncompressed_name
#                1234               5678  78.3% file.txt

# Colonnes :
# - compressed : Taille compressée
# - uncompressed : Taille originale
# - ratio : Taux compression (%)
# - uncompressed_name : Nom fichier original
```

---

### Options gzip Courantes

```bash
# Niveau compression (1-9)
gzip -1 file.txt  # Rapide, compression faible
gzip -9 file.txt  # Lent, compression maximale
gzip -6 file.txt  # Défaut (compromis)

# Compresser vers stdout (pipe)
gzip -c file.txt > file.txt.gz
# Original conservé automatiquement

# Décompresser vers stdout
gzip -dc file.txt.gz > file-restored.txt
# Archive conservée

# Forcer écrasement
gzip -f file.txt
# Écrase file.txt.gz si existe

# Verbose
gzip -v file.txt
# file.txt:  78.3% -- replaced with file.txt.gz

# Récursif (dossier)
gzip -r directory/
# Compresse tous fichiers dans directory/
```

---

### gunzip : Alias Décompression

**gunzip** = `gzip -d` (décompression)

```bash
# Équivalents
gzip -d file.txt.gz
gunzip file.txt.gz

# Avec keep
gzip -dk file.txt.gz
gunzip -k file.txt.gz

# Avec verbose
gzip -dv file.txt.gz
gunzip -v file.txt.gz
```

---

### zcat : Afficher Contenu Compressé

**zcat** = `gzip -dc` (décompresser vers stdout)

```bash
# Afficher contenu SANS décompresser
zcat file.txt.gz
# (affiche contenu, archive intacte)

# Pipe vers grep
zcat logfile.log.gz | grep "ERROR"

# Pipe vers less
zcat bigfile.txt.gz | less

# Comparer fichiers compressés
diff <(zcat file1.txt.gz) <(zcat file2.txt.gz)
```

---

## 🔵 bzip2 : Compression Optimale

### bzip2 : Syntaxe de Base

**Rôle** : Compression **bzip2** (meilleur taux que gzip)

```bash
# Compresser
bzip2 file.txt
# Crée : file.txt.bz2
# Original : SUPPRIMÉ

# Décompresser
bzip2 -d file.txt.bz2
# OU
bunzip2 file.txt.bz2
# Crée : file.txt
# Archive : SUPPRIMÉE
```

---

### Options bzip2

**Identiques à gzip** :

```bash
# Garder original
bzip2 -k file.txt

# Décompresser avec keep
bzip2 -dk file.txt.bz2

# Lister infos
bzip2 -l file.txt.bz2  # ⚠️ PAS supporté par bzip2
# Utiliser : ls -lh

# Niveau compression (1-9)
bzip2 -1 file.txt  # Rapide
bzip2 -9 file.txt  # Maximal (défaut)

# Verbose
bzip2 -v file.txt
# file.txt:  2.123:1,  3.766 bits/byte, 52.89% saved
```

---

### bunzip2 et bzcat

```bash
# Décompresser
bunzip2 file.txt.bz2
# Équivalent : bzip2 -d file.txt.bz2

# Afficher contenu compressé
bzcat file.txt.bz2
# (décompresse vers stdout)

# Exemples
bzcat logfile.log.bz2 | grep "WARNING"
bzcat data.csv.bz2 | head -20
```

---

## 🟢 xz : Compression Maximale

### xz : Syntaxe de Base

**Rôle** : Compression **xz** (meilleur ratio, plus lent)

```bash
# Compresser
xz file.txt
# Crée : file.txt.xz
# Original : SUPPRIMÉ

# Décompresser
xz -d file.txt.xz
# OU
unxz file.txt.xz
# Crée : file.txt
# Archive : SUPPRIMÉE
```

---

### Options xz

```bash
# Garder original
xz -k file.txt

# Décompresser avec keep
xz -dk file.txt.xz

# Lister infos
xz -l file.txt.xz
# Strms  Blocks   Compressed Uncompressed  Ratio  Check   Filename
#     1       1      1234 B      5678 B  0.217  CRC64   file.txt.xz

# Niveau compression (0-9)
xz -0 file.txt  # Rapide
xz -9 file.txt  # Maximal (défaut -6)

# Compression extrême (très lent)
xz -e file.txt
xz -9e file.txt  # Niveau 9 + extrême

# Verbose
xz -v file.txt
# file.txt (1/1)
#   100 %        1,234 B / 5,678 B = 0.217
```

---

### unxz et xzcat

```bash
# Décompresser
unxz file.txt.xz
# Équivalent : xz -d file.txt.xz

# Afficher contenu compressé
xzcat file.txt.xz
# (décompresse vers stdout)

# Exemples
xzcat archive.tar.xz | tar -tv
xzcat bigdata.txt.xz | less
```

---

## 🗂️ Comparaison gzip vs bzip2 vs xz

### Test Compression

```bash
# Fichier original
ls -lh file.txt
# -rw-r--r-- 1 user user 10M Feb 2 10:30 file.txt

# Compresser avec les 3 outils
gzip -k file.txt
bzip2 -k file.txt
xz -k file.txt

# Comparer tailles
ls -lh file.txt*
# -rw-r--r-- 1 user user 10M  file.txt
# -rw-r--r-- 1 user user 3.2M file.txt.gz   ← 68% compression
# -rw-r--r-- 1 user user 2.8M file.txt.bz2  ← 72% compression
# -rw-r--r-- 1 user user 2.1M file.txt.xz   ← 79% compression
```

---

### Tableau Comparatif

| Critère | gzip | bzip2 | xz |
|---------|------|-------|-----|
| **Compression** | 60-70% | 70-80% | 75-85% |
| **Vitesse compression** | ⭐⭐⭐⭐ Rapide | ⭐⭐⭐ Moyen | ⭐⭐ Lent |
| **Vitesse décompression** | ⭐⭐⭐⭐ Rapide | ⭐⭐⭐ Moyen | ⭐⭐⭐ Moyen |
| **RAM nécessaire** | Faible | Moyenne | Élevée |
| **Usage courant** | Logs, backups | Archives | Distribution logiciels |
| **Compatibilité** | ✅ Universelle | ✅ Large | ⚠️ Moderne (Linux récent) |

**💡 Règle** :
- **gzip** → Rapidité (logs temps réel, backups fréquents)
- **bzip2** → Compromis (archives générales)
- **xz** → Maximum compression (distribution, stockage long terme)

---

## 📦 zip : Compatibilité Multi-plateformes

### zip : Syntaxe de Base

**Rôle** : Compression **zip** (compatible Windows/MacOS/Linux)

**Différence** : Archive **multiple fichiers** (contrairement gzip/bzip2/xz)

```bash
# Compresser fichier(s)
zip archive.zip file1.txt file2.txt file3.txt

# Compresser avec wildcards
zip archive.zip *.txt

# Décompresser
unzip archive.zip

# Lister contenu SANS extraire
unzip -l archive.zip
# Archive:  archive.zip
#   Length      Date    Time    Name
# ---------  ---------- -----   ----
#      1234  02-02-2026 10:30   file1.txt
#      5678  02-02-2026 10:31   file2.txt
# ---------                     -------
#      6912                     2 files
```

---

### Option -r : Récursif (Dossier)

```bash
# Compresser dossier entier
zip -r archive.zip directory/

# Exemple
zip -r backup.zip /home/user/documents/

# Vérifier
unzip -l backup.zip | head -20
```

---

### Options zip Courantes

```bash
# Niveau compression (0-9)
zip -0 archive.zip file.txt  # Pas de compression (stockage)
zip -9 archive.zip file.txt  # Compression maximale

# Mise à jour archive (ajouter/modifier)
zip -u archive.zip newfile.txt

# Supprimer fichier archive
zip -d archive.zip oldfile.txt

# Exclure fichiers
zip -r archive.zip directory/ -x "*.log" "*.tmp"

# Mot de passe
zip -e -r secure.zip documents/
# Enter password: ****
# Verify password: ****

# Verbose
zip -v archive.zip *.txt
```

---

### unzip : Options

```bash
# Lister contenu
unzip -l archive.zip

# Tester intégrité
unzip -t archive.zip
# testing: file1.txt                OK
# testing: file2.txt                OK
# No errors detected

# Extraire vers dossier spécifique
unzip archive.zip -d /tmp/extract/

# Extraire fichier spécifique
unzip archive.zip file1.txt

# Extraire avec pattern
unzip archive.zip "*.txt"

# Écraser sans confirmation
unzip -o archive.zip

# Ne PAS écraser
unzip -n archive.zip

# Verbose
unzip -v archive.zip
```

---

## 📼 tar : Tape Archive

### tar : Concepts

**tar** = **T**ape **AR**chive (archive bande magnétique)

**Rôle** : **Regrouper** fichiers/dossiers en **une** archive

**⚠️ Important** : tar seul = **PAS de compression** (juste regroupement)

**Analogie** 📦 :
- **tar** = Carton (contient plusieurs objets)
- **gzip/bzip2/xz** = Film plastique (réduit taille carton)
- **tar.gz** = Carton emballé sous film (regroupement + compression)

---

### Extensions tar

| Extension | Signification | Compression |
|-----------|---------------|-------------|
| `.tar` | Archive seule | ❌ Aucune |
| `.tar.gz` ou `.tgz` | Archive + gzip | ✅ gzip |
| `.tar.bz2` ou `.tbz2` | Archive + bzip2 | ✅ bzip2 |
| `.tar.xz` ou `.txz` | Archive + xz | ✅ xz |

---

### tar : Options Principales

**Modes** :

| Option | Mode | Rôle |
|--------|------|------|
| `-c` | **Create** | Créer archive |
| `-x` | **Extract** | Extraire archive |
| `-t` | **Table** | Lister contenu |

**Options compression** :

| Option | Compression | Équivalent |
|--------|-------------|------------|
| `-z` | **gzip** | `.tar.gz` |
| `-j` | **bzip2** | `.tar.bz2` |
| `-J` | **xz** | `.tar.xz` |
| `-a` | **Auto** | Détecte extension fichier sortie |

**Options communes** :

| Option | Rôle |
|--------|------|
| `-v` | **Verbose** (afficher fichiers) |
| `-f` | **File** (nom archive - **OBLIGATOIRE**) |
| `-C` | **Change directory** (extraire vers) |
| `-r` | **Append** (ajouter à archive) |
| `--exclude` | Exclure pattern |

---

### Créer Archive tar

#### Archive sans compression

```bash
# Créer archive .tar (pas de compression)
tar -cvf archive.tar directory/

# Explication :
# -c : Create
# -v : Verbose
# -f : File (nom archive)

# Vérifier création
ls -lh archive.tar
# -rw-r--r-- 1 user user 50M Feb 2 10:30 archive.tar
```

---

#### Archive avec compression gzip (-z)

```bash
# Créer archive .tar.gz
tar -czvf archive.tar.gz directory/

# Explication :
# -c : Create
# -z : gzip compression
# -v : Verbose
# -f : File

# Extension courte
tar -czvf archive.tgz directory/
```

---

#### Archive avec compression bzip2 (-j)

```bash
# Créer archive .tar.bz2
tar -cjvf archive.tar.bz2 directory/

# Extension courte
tar -cjvf archive.tbz2 directory/
```

---

#### Archive avec compression xz (-J)

```bash
# Créer archive .tar.xz
tar -cJvf archive.tar.xz directory/

# Extension courte
tar -cJvf archive.txz directory/
```

---

### Option -a : Auto-detect Compression

**Rôle** : Détecte compression depuis **extension** fichier sortie

```bash
# tar détecte automatiquement compression
tar -cavf archive.tar.gz directory/   # → gzip
tar -cavf archive.tar.bz2 directory/  # → bzip2
tar -cavf archive.tar.xz directory/   # → xz
tar -cavf archive.tar directory/      # → pas de compression

# Équivalents :
tar -czvf archive.tar.gz directory/   # Même résultat
tar -cavf archive.tar.gz directory/   # Plus simple !
```

**💡 Avantage** : Plus besoin mémoriser `-z`, `-j`, `-J`

---

### Extraire Archive tar

```bash
# Extraire .tar
tar -xvf archive.tar

# Extraire .tar.gz
tar -xzvf archive.tar.gz

# Extraire .tar.bz2
tar -xjvf archive.tar.bz2

# Extraire .tar.xz
tar -xJvf archive.tar.xz

# Avec auto-detect
tar -xavf archive.tar.gz    # Détecte gzip
tar -xavf archive.tar.bz2   # Détecte bzip2
tar -xavf archive.tar.xz    # Détecte xz
```

---

### Option -C : Extraire vers Dossier

```bash
# Extraire vers /tmp/restore
tar -xzvf archive.tar.gz -C /tmp/restore/

# Créer dossier si n'existe pas
mkdir -p /backup/restore
tar -xzvf backup.tar.gz -C /backup/restore/

# Exemple complet
tar -xzvf website-backup.tar.gz -C /var/www/html/
```

---

### Lister Contenu Archive

```bash
# Lister sans extraire
tar -tvf archive.tar

# Avec compression gzip
tar -tzvf archive.tar.gz

# Avec auto-detect
tar -tavf archive.tar.gz

# Exemple résultat
tar -tvf backup.tar.gz
# drwxr-xr-x user/user       0 2026-02-02 10:30 directory/
# -rw-r--r-- user/user    1234 2026-02-02 10:30 directory/file1.txt
# -rw-r--r-- user/user    5678 2026-02-02 10:31 directory/file2.txt
```

---

### Exclure Fichiers

```bash
# Exclure fichiers .log
tar -czvf archive.tar.gz --exclude="*.log" directory/

# Exclure plusieurs patterns
tar -czvf backup.tar.gz \
  --exclude="*.log" \
  --exclude="*.tmp" \
  --exclude=".git" \
  /home/user/project/

# Exclure dossier spécifique
tar -czvf backup.tar.gz --exclude="node_modules" project/
```

---

### Ajouter Fichiers Archive

```bash
# Créer archive
tar -cvf archive.tar file1.txt file2.txt

# Ajouter fichier
tar -rvf archive.tar file3.txt

# Vérifier
tar -tvf archive.tar
# file1.txt
# file2.txt
# file3.txt

# ⚠️ Fonctionne SEULEMENT avec .tar (pas .tar.gz compressé)
```

---

## 📋 Antisèche - Compression

### gzip/bzip2/xz Commandes

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `gzip file.txt` | Compresser (supprime original) | `gzip log.txt` |
| `gzip -k file.txt` | Compresser (garde original) | `gzip -k data.csv` |
| `gzip -d file.gz` | Décompresser | `gzip -d archive.gz` |
| `gunzip file.gz` | Décompresser (alias) | `gunzip log.gz` |
| `gzip -l file.gz` | Lister infos | `gzip -l backup.gz` |
| `zcat file.gz` | Afficher contenu | `zcat log.gz \| grep ERROR` |
| `bzip2 -k file.txt` | Compresser bzip2 | `bzip2 -k large.txt` |
| `bunzip2 file.bz2` | Décompresser bzip2 | `bunzip2 archive.bz2` |
| `bzcat file.bz2` | Afficher bzip2 | `bzcat log.bz2 \| less` |
| `xz -k file.txt` | Compresser xz | `xz -k document.txt` |
| `unxz file.xz` | Décompresser xz | `unxz backup.xz` |
| `xzcat file.xz` | Afficher xz | `xzcat data.xz \| head` |

---

### tar Commandes

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `tar -cvf archive.tar dir/` | Créer archive | `tar -cvf backup.tar /home/` |
| `tar -czvf archive.tar.gz dir/` | Créer + gzip | `tar -czvf web.tar.gz /var/www/` |
| `tar -cjvf archive.tar.bz2 dir/` | Créer + bzip2 | `tar -cjvf data.tar.bz2 /data/` |
| `tar -cJvf archive.tar.xz dir/` | Créer + xz | `tar -cJvf docs.tar.xz /docs/` |
| `tar -cavf archive.tar.gz dir/` | Créer + auto | `tar -cavf backup.tar.xz /backup/` |
| `tar -xvf archive.tar` | Extraire | `tar -xvf backup.tar` |
| `tar -xzvf archive.tar.gz` | Extraire gzip | `tar -xzvf web.tar.gz` |
| `tar -xavf archive.tar.gz` | Extraire auto | `tar -xavf backup.tar.bz2` |
| `tar -tvf archive.tar` | Lister contenu | `tar -tvf backup.tar.gz` |
| `tar -xzvf file.tar.gz -C /dest/` | Extraire vers | `tar -xzvf app.tar.gz -C /opt/` |
| `tar --exclude="*.log" -czf ...` | Exclure pattern | `tar --exclude=".git" -czf backup.tar.gz project/` |

---

### zip Commandes

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `zip archive.zip file.txt` | Compresser fichier | `zip backup.zip config.txt` |
| `zip -r archive.zip dir/` | Compresser dossier | `zip -r web.zip /var/www/` |
| `unzip archive.zip` | Décompresser | `unzip backup.zip` |
| `unzip -l archive.zip` | Lister contenu | `unzip -l files.zip` |
| `unzip archive.zip -d /dest/` | Extraire vers | `unzip app.zip -d /opt/app/` |
| `unzip -t archive.zip` | Tester intégrité | `unzip -t backup.zip` |

---

## 🎓 Points Clés pour l'Examen

✅ **3 outils compression** : gzip (.gz), bzip2 (.bz2), xz (.xz)  
✅ **Option -d** : Décompresser (tous outils)  
✅ **Option -k** : Keep fichier original (IMPORTANT)  
✅ **Option -l** : Lister infos compression (gzip, xz)  
✅ **Par défaut** : Suppression fichier original (sans -k)  
✅ **zcat/bzcat/xzcat** : Afficher contenu compressé sans décompresser  
✅ **gunzip/bunzip2/unxz** : Alias décompression  
✅ **Compression** : xz > bzip2 > gzip (ratio)  
✅ **Vitesse** : gzip > bzip2 > xz (compression/décompression)  
✅ **zip -r** : Récursif pour compresser dossiers  
✅ **tar** : Tape Archive (regroupement, PAS compression native)  
✅ **tar -c** : Create, **-x** : Extract, **-t** : Table (lister)  
✅ **tar -z** : gzip, **-j** : bzip2, **-J** : xz  
✅ **tar -a** : Auto-detect compression depuis extension  
✅ **tar -v** : Verbose, **-f** : File (obligatoire)  
✅ **tar -C** : Change directory (extraire vers)  
✅ **tar --exclude** : Exclure fichiers/dossiers  
✅ **Extensions** : .tar.gz/.tgz, .tar.bz2/.tbz2, .tar.xz/.txz  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Backup Logs avec Rotation

```bash
# Compresser logs anciens (> 7 jours)
find /var/log -name "*.log" -mtime +7 | while read logfile; do
    gzip -k "$logfile"
    # Crée .log.gz, garde .log
done

# Supprimer originaux après vérification
find /var/log -name "*.log.gz" -mtime +7 | while read gzfile; do
    original="${gzfile%.gz}"
    if [ -f "$gzfile" ] && [ -f "$original" ]; then
        rm "$original"
    fi
done

# Vérifier compression
gzip -l /var/log/app.log.gz
# compressed   uncompressed  ratio
#     12345          98765  87.5%
```

---

### Scénario 2 : Archiver Projet Web

```bash
# Créer backup site web (exclure cache, logs, uploads)
tar -czvf /backup/website-$(date +%Y%m%d).tar.gz \
  --exclude="wp-content/cache" \
  --exclude="wp-content/uploads" \
  --exclude="*.log" \
  /var/www/html/

# Vérifier archive créée
ls -lh /backup/website-*.tar.gz
# -rw-r--r-- 1 root root 25M Feb 2 10:30 website-20260202.tar.gz

# Lister contenu
tar -tzvf /backup/website-20260202.tar.gz | head -20

# Tester extraction (sans vraiment extraire)
tar -tzvf /backup/website-20260202.tar.gz > /dev/null && echo "OK" || echo "ERREUR"
```

---

### Scénario 3 : Comparer Outils Compression

```bash
# Fichier test 100M
dd if=/dev/zero of=test.dat bs=1M count=100

# Compresser avec les 3 outils
time gzip -k test.dat
# real    0m2.345s (rapide)

time bzip2 -k test.dat
# real    0m8.123s (moyen)

time xz -k test.dat
# real    0m15.678s (lent)

# Comparer tailles
ls -lh test.dat*
# -rw-r--r-- 100M test.dat
# -rw-r--r-- 512K test.dat.gz   ← 99.5% compression
# -rw-r--r-- 495K test.dat.bz2  ← 99.5% compression
# -rw-r--r-- 428K test.dat.xz   ← 99.6% compression

# Meilleur outil : xz (meilleure compression)
# Plus rapide : gzip (vitesse)
```

---

### Scénario 4 : Backup Incrémental avec tar

```bash
# Jour 1 : Backup complet
tar -czvf /backup/full-20260201.tar.gz \
  --listed-incremental=/backup/snapshot.snar \
  /home/user/documents/

# Jour 2 : Backup incrémental (seulement modifications)
tar -czvf /backup/incr-20260202.tar.gz \
  --listed-incremental=/backup/snapshot.snar \
  /home/user/documents/

# Jour 3 : Backup incrémental
tar -czvf /backup/incr-20260203.tar.gz \
  --listed-incremental=/backup/snapshot.snar \
  /home/user/documents/

# Restauration complète :
# 1. Restaurer full
tar -xzvf /backup/full-20260201.tar.gz -C /restore/

# 2. Restaurer incrémentiaux dans l'ordre
tar -xzvf /backup/incr-20260202.tar.gz -C /restore/
tar -xzvf /backup/incr-20260203.tar.gz -C /restore/
```

---

### Scénario 5 : Transfert Fichiers Réseau

```bash
# Compresser + transférer via SSH
tar -czf - /home/user/project | ssh user@remote "cat > /backup/project.tar.gz"

# OU : Compresser à la volée
tar -czf - /home/user/project | ssh user@remote "tar -xzf - -C /restore/"

# Avec xz (meilleure compression, transfert moins de données)
tar -cJf - /data/large-dataset | ssh backup-server "cat > /backups/dataset.tar.xz"

# Vérifier taille transférée
du -sh /home/user/project
# 500M
tar -cJf - /home/user/project | wc -c
# 52428800  (50M compressé, 90% économie bande passante)
```

---

### Scénario 6 : Archive Partitionnée (Split)

```bash
# Créer archive volumineuse
tar -czvf - /var/lib/mysql | split -b 1G - mysql-backup.tar.gz.part

# Résultat :
ls -lh mysql-backup.tar.gz.part*
# -rw-r--r-- 1.0G mysql-backup.tar.gz.partaa
# -rw-r--r-- 1.0G mysql-backup.tar.gz.partab
# -rw-r--r-- 512M mysql-backup.tar.gz.partac

# Restaurer
cat mysql-backup.tar.gz.part* | tar -xzvf - -C /restore/

# OU : Reconstituer puis extraire
cat mysql-backup.tar.gz.part* > mysql-backup.tar.gz
tar -xzvf mysql-backup.tar.gz
```

---

### Scénario 7 : Extraction Sélective

```bash
# Lister contenu archive
tar -tzvf backup.tar.gz | grep "important"
# -rw-r--r-- user/user 1234 dir/important-file.txt
# -rw-r--r-- user/user 5678 dir/subdir/important-doc.pdf

# Extraire SEULEMENT fichiers spécifiques
tar -xzvf backup.tar.gz dir/important-file.txt

# Extraire avec pattern
tar -xzvf backup.tar.gz --wildcards "*.conf"

# Extraire dossier spécifique
tar -xzvf backup.tar.gz etc/nginx/
```

---

### Scénario 8 : Compression Logs Quotidiens

```bash
# Script rotation logs (logrotate alternative simple)
#!/bin/bash

LOGDIR="/var/log/myapp"
ARCHIVE_DIR="/var/log/archive"

# Créer archive logs du jour précédent
YESTERDAY=$(date -d "yesterday" +%Y%m%d)

tar -czvf "$ARCHIVE_DIR/logs-$YESTERDAY.tar.gz" \
  "$LOGDIR"/*.log

# Vider logs actuels après archivage
truncate -s 0 "$LOGDIR"/*.log

# Supprimer archives > 90 jours
find "$ARCHIVE_DIR" -name "logs-*.tar.gz" -mtime +90 -delete

# Vérifier espace économisé
du -sh "$LOGDIR"
du -sh "$ARCHIVE_DIR"
```

---

### Scénario 9 : Archive Compatible Windows

```bash
# Créer archive .zip pour partage Windows
zip -r project-export.zip /home/user/project/ \
  -x "*.git/*" "node_modules/*" "*.log"

# Avec mot de passe
zip -e -r confidential.zip /data/sensitive/
# Enter password: ****

# Transférer via SCP
scp project-export.zip user@windows-machine:/C:/Users/john/Downloads/

# Destinataire Windows : Double-clic pour extraire
# (compatibilité native Windows Explorer)
```

---

### Scénario 10 : Vérification Intégrité Archives

```bash
# Créer checksums archives
find /backup -name "*.tar.gz" -type f -exec sha256sum {} \; > /backup/checksums.txt

# Vérifier intégrité
sha256sum -c /backup/checksums.txt
# backup-20260201.tar.gz: OK
# backup-20260202.tar.gz: OK
# backup-20260203.tar.gz: FAILED

# Tester extraction archives
for archive in /backup/*.tar.gz; do
    echo "Testing $archive..."
    tar -tzf "$archive" > /dev/null && echo "OK" || echo "CORRUPTED: $archive"
done

# Résultat :
# Testing backup-20260201.tar.gz... OK
# Testing backup-20260202.tar.gz... OK
# Testing backup-20260203.tar.gz... CORRUPTED: backup-20260203.tar.gz
```

---

## ⚠️ Erreurs Courantes

### Erreur 1 : Perte Fichier Original

```bash
# ❌ DANGER : Compression sans -k
gzip important-file.txt
# important-file.txt SUPPRIMÉ !
# Seulement important-file.txt.gz reste

# ✅ SÉCURITÉ : Toujours utiliser -k
gzip -k important-file.txt
# important-file.txt CONSERVÉ
# important-file.txt.gz créé
```

---

### Erreur 2 : Oublier -f dans tar

```bash
# ❌ ERREUR : Pas de -f
tar -czv archive.tar.gz directory/
# tar: Cowardly refusing to create an empty archive
# (attend nom fichier après options)

# ✅ CORRECT : -f OBLIGATOIRE (et en dernier)
tar -czvf archive.tar.gz directory/

# ⚠️ ORDRE IMPORTANT : -f doit précéder nom fichier
tar -cvfz archive.tar.gz directory/  # ❌ z devient nom fichier
tar -cvzf archive.tar.gz directory/  # ✅ archive.tar.gz est nom fichier
```

---

### Erreur 3 : Mauvaise Extension tar

```bash
# ❌ CONFUSION : Extension ne correspond pas
tar -czvf backup.tar directory/
# Crée backup.tar avec compression gzip
# Mais extension .tar suggère pas de compression !

# ✅ COHÉRENCE : Extension = compression
tar -czvf backup.tar.gz directory/  # gzip
tar -cjvf backup.tar.bz2 directory/ # bzip2
tar -cJvf backup.tar.xz directory/  # xz

# OU : Utiliser -a (auto-detect)
tar -cavf backup.tar.gz directory/
tar -cavf backup.tar.bz2 directory/
```

---

### Erreur 4 : Extraction Sans -C

```bash
# ❌ RISQUE : Extraction dans /
cd /
tar -xzvf /backup/full-system.tar.gz
# ÉCRASE système en production !

# ✅ SÉCURITÉ : Toujours -C vers dossier dédié
mkdir /restore
tar -xzvf /backup/full-system.tar.gz -C /restore/

# OU : cd vers dossier extraction
mkdir /restore
cd /restore
tar -xzvf /backup/full-system.tar.gz
```

---

### Erreur 5 : Chemins Absolus dans Archive

```bash
# ❌ PROBLÈME : Chemins absolus
cd /
tar -czvf backup.tar.gz /home/user/data
# Archive contient : /home/user/data/...

# Extraction recrée /home/user/data (pas flexible)
cd /restore
tar -xzvf backup.tar.gz
# Crée /home/user/data (pas /restore/home/user/data)

# ✅ SOLUTION : Chemins relatifs
cd /home/user
tar -czvf backup.tar.gz data/
# Archive contient : data/...

cd /restore
tar -xzvf backup.tar.gz
# Crée /restore/data/ ✅
```

---

### Erreur 6 : Double Compression

```bash
# ❌ INEFFICACE : Compresser fichier déjà compressé
gzip file.txt.gz
# Crée file.txt.gz.gz
# Aucun gain (voire augmentation taille)

# Vérifier :
ls -lh file.txt*
# -rw-r--r-- 1.2M file.txt.gz
# -rw-r--r-- 1.3M file.txt.gz.gz  ← Plus gros !

# ✅ RÈGLE : Pas compresser archives déjà compressées
# (.tar.gz, .zip, .jpg, .mp4 déjà compressés)
```

---

### Erreur 7 : Mauvais Niveau Compression

```bash
# ❌ LENT : Niveau 9 pour fichiers temporaires
gzip -9 temp-log-file.txt
# Très lent pour gain minime

# ✅ OPTIMAL : Adapter niveau
gzip -1 temp-files.txt     # Rapide, fichiers temporaires
gzip -6 standard-file.txt  # Défaut (bon compromis)
gzip -9 archive-longue-duree.txt  # Max, archivage long terme
```

---

### Erreur 8 : zip Sans -r pour Dossiers

```bash
# ❌ ERREUR : Oublier -r
zip backup.zip /home/user/documents/
# zip warning: name not matched: /home/user/documents/
# Archive vide !

# ✅ CORRECT : -r pour récursivité
zip -r backup.zip /home/user/documents/
```

---

## 🔄 rsync : Synchronisation Fichiers

### rsync : Concepts

**rsync** = **R**emote **SYNC**hronization (synchronisation à distance)

**Rôle** : **Synchroniser** répertoires (local ↔ local ou local ↔ remote)

**Analogie** 🔄 :
- **Copie classique (cp)** = Photocopier document complet à chaque fois
- **rsync** = Mise à jour document (seulement modifications)
- **Avantage** = Économie temps/bande passante (transfert seulement différences)

**Caractéristiques** :
- ✅ **Incrémental** : Transfert seulement fichiers modifiés
- ✅ **Bidirectionnel** : Backup OU restauration
- ✅ **Préservation** : Permissions, dates, propriétaires
- ✅ **Remote** : Via SSH (sécurisé)
- ✅ **Reprise** : Continue transfert interrompu

---

### rsync : Syntaxe de Base

```bash
# Synchronisation locale
rsync [options] source/ destination/

# Synchronisation remote (backup)
rsync [options] source/ user@remote:/destination/

# Synchronisation remote (restauration)
rsync [options] user@remote:/source/ destination/
```

**⚠️ Important** : Slash `/` final dans chemin source a son importance !

```bash
# AVEC slash : Contenu dossier
rsync -a source/ destination/
# Copie CONTENU de source/ dans destination/
# destination/fichier1.txt
# destination/fichier2.txt

# SANS slash : Dossier lui-même
rsync -a source destination/
# Copie DOSSIER source dans destination/
# destination/source/fichier1.txt
# destination/source/fichier2.txt
```

---

### Option -a : Archive Mode

**Option -a** = **Archive mode** (mode le plus utilisé)

**Équivalent** : `-rlptgoD` (regroupe plusieurs options)

**Fonctions** :
- ✅ **Récursif** (-r) : Sous-dossiers inclus
- ✅ **Liens symboliques** (-l) : Préservés
- ✅ **Permissions** (-p) : Conservées
- ✅ **Times** (-t) : Dates modification/accès préservées
- ✅ **Group** (-g) : Groupe propriétaire conservé
- ✅ **Owner** (-o) : Propriétaire conservé (nécessite sudo)
- ✅ **Devices** (-D) : Fichiers spéciaux préservés

```bash
# Mode archive (recommandé)
rsync -a documents/ /backup/documents/

# Équivalent explicite (pas nécessaire)
rsync -rlptgoD documents/ /backup/documents/
```

**💡 Règle** : **Toujours** utiliser `-a` pour backups complets

---

### Synchronisation Locale

```bash
# Synchroniser dossier local → local
rsync -av /home/user/documents/ /backup/documents/

# Options :
# -a : Archive mode
# -v : Verbose (afficher fichiers)

# Résultat :
# sending incremental file list
# file1.txt
# file2.txt
# subdir/file3.txt
#
# sent 12,345 bytes  received 89 bytes  24,868.00 bytes/sec
# total size is 98,765  speedup is 7.94
```

---

### Synchronisation Remote : Backup

**Prérequis** : Serveur **SSH** actif sur machine distante

```bash
# Backup LOCAL → REMOTE
rsync -av /home/user/documents/ user@backup-server:/backups/documents/

# Syntaxe :
# source : /home/user/documents/ (LOCAL)
# destination : user@backup-server:/backups/documents/ (REMOTE)

# Processus :
# 1. Connexion SSH vers backup-server
# 2. Comparaison fichiers (checksums)
# 3. Transfert seulement différences
# 4. Mise à jour permissions/dates
```

---

### Synchronisation Remote : Restauration

**Ordre inversé** : Remote → Local

```bash
# Restauration REMOTE → LOCAL
rsync -av user@backup-server:/backups/documents/ /home/user/documents/

# Syntaxe :
# source : user@backup-server:/backups/documents/ (REMOTE)
# destination : /home/user/documents/ (LOCAL)
```

**💡 Mnémotechnique** :
- **Backup** : Local en premier (`local/ remote:`)
- **Restauration** : Remote en premier (`remote: local/`)

---

### Options rsync Courantes

#### Verbose et Progress

```bash
# Verbose (afficher fichiers)
rsync -av source/ destination/

# Progress (barre progression)
rsync -av --progress source/ destination/
# file1.txt
#   1,234,567  100%   50.00MB/s    0:00:00 (xfr#1, to-chk=9/10)

# Statistiques détaillées
rsync -av --stats source/ destination/
```

---

#### Dry Run (Simulation)

```bash
# Simuler SANS exécuter (tester)
rsync -av --dry-run source/ destination/

# OU
rsync -avn source/ destination/

# Affiche ce qui SERAIT fait
# (aucun fichier transféré)
```

**💡 Toujours** tester avec `--dry-run` avant synchronisation importante

---

#### Delete (Supprimer Fichiers Destination)

```bash
# Synchronisation exacte (supprimer fichiers absents source)
rsync -av --delete source/ destination/

# Exemple :
# source/ contient : file1.txt, file2.txt
# destination/ contient : file1.txt, file2.txt, file3.txt (ancien)

# Résultat après rsync --delete :
# destination/ contient : file1.txt, file2.txt
# file3.txt SUPPRIMÉ (absent de source)
```

**⚠️ DANGER** : `--delete` supprime fichiers destination absents de source

---

#### Exclude (Exclure Fichiers)

```bash
# Exclure fichiers/dossiers
rsync -av --exclude="*.log" source/ destination/

# Exclure plusieurs patterns
rsync -av \
  --exclude="*.log" \
  --exclude="*.tmp" \
  --exclude=".git" \
  --exclude="node_modules" \
  source/ destination/

# Exclure depuis fichier
cat exclude-list.txt
# *.log
# *.tmp
# .git/
# node_modules/

rsync -av --exclude-from=exclude-list.txt source/ destination/
```

---

#### Bandwidth Limit

```bash
# Limiter bande passante (éviter saturer réseau)
rsync -av --bwlimit=1000 source/ user@remote:/backup/
# Limite : 1000 Ko/s (1 Mo/s)

# Exemples :
rsync -av --bwlimit=500 ...   # 500 Ko/s
rsync -av --bwlimit=5000 ...  # 5 Mo/s
```

---

#### Compression

```bash
# Compresser transfert (économiser bande passante)
rsync -avz source/ user@remote:/backup/

# -z : Compression activée
# Utile pour connexions lentes
```

---

#### SSH Options

```bash
# Port SSH personnalisé
rsync -av -e "ssh -p 2222" source/ user@remote:/backup/

# Clé SSH spécifique
rsync -av -e "ssh -i /home/user/.ssh/backup_key" source/ user@remote:/backup/

# Options SSH multiples
rsync -av -e "ssh -p 2222 -i /path/to/key" source/ user@remote:/backup/
```

---

## 💾 dd : Disk Duplicator

### dd : Concepts

**dd** = **D**isk **D**uplicator (duplicateur disque)

**Rôle** : Copier **blocs de données bruts** (disques, partitions, fichiers)

**Analogie** 💿 :
- **rsync/cp** = Copier fichiers individuellement (système fichiers)
- **dd** = Photocopier disque bit par bit (niveau bas)
- **Usage** = Clonage disque, création images, backup partitions

**⚠️ DANGER** : **Destructeur** si mauvais paramètres (écrase données)

**Surnom** : "Disk Destroyer" (à cause erreurs fréquentes)

---

### dd : Syntaxe de Base

```bash
dd if=INPUT_FILE of=OUTPUT_FILE [options]
```

**Paramètres obligatoires** :

| Paramètre | Signification | Exemple |
|-----------|---------------|---------|
| `if=` | **I**nput **F**ile (source) | `if=/dev/sda1` |
| `of=` | **O**utput **F**ile (destination) | `of=backup.img` |

**Paramètres optionnels** :

| Paramètre | Signification | Exemple |
|-----------|---------------|---------|
| `bs=` | **B**lock **S**ize (taille bloc) | `bs=1M` |
| `count=` | Nombre blocs à copier | `count=100` |
| `status=` | Affichage progression | `status=progress` |
| `conv=` | Conversions (sync, noerror) | `conv=noerror,sync` |

---

### Option if : Input File

**Rôle** : Fichier/périphérique **source**

```bash
# Lire depuis partition
dd if=/dev/sda1 of=backup.img

# Lire depuis disque entier
dd if=/dev/sda of=disk-full.img

# Lire depuis fichier
dd if=input.bin of=output.bin

# Lire depuis zéros (création fichier vide)
dd if=/dev/zero of=empty-file.dat bs=1M count=100

# Lire depuis aléatoire (données random)
dd if=/dev/urandom of=random-data.bin bs=1M count=10
```

---

### Option of : Output File

**Rôle** : Fichier/périphérique **destination**

```bash
# Écrire vers fichier
dd if=/dev/sda1 of=partition-backup.img

# Écrire vers partition (RESTAURATION)
dd if=backup.img of=/dev/sda1

# Écrire vers disque USB
dd if=ubuntu.iso of=/dev/sdb  # Créer clé USB bootable

# Écrire vers stdout
dd if=file.bin of=/dev/stdout | hexdump -C
```

**⚠️ DANGER** : Vérifier **deux fois** destination avant exécuter !

```bash
# ❌ CATASTROPHE
dd if=/dev/zero of=/dev/sda  # EFFACE DISQUE COMPLET

# ✅ VÉRIFIER destination
lsblk  # Lister disques/partitions AVANT
# Ensuite seulement : dd if=... of=/dev/sdX
```

---

### Option bs : Block Size

**Rôle** : Taille **bloc** lecture/écriture

**Unités** :
- `c` → 1 byte
- `k` → 1024 bytes (1 Ko)
- `M` → 1048576 bytes (1 Mo)
- `G` → 1073741824 bytes (1 Go)

```bash
# Block size 512 bytes (défaut ancien)
dd if=/dev/sda1 of=backup.img bs=512

# Block size 1 Mo (recommandé moderne)
dd if=/dev/sda1 of=backup.img bs=1M

# Block size 4 Mo (encore plus rapide)
dd if=/dev/sda1 of=backup.img bs=4M

# Block size 1 Go (très rapide, beaucoup RAM)
dd if=/dev/sda1 of=backup.img bs=1G
```

**💡 Performance** : bs plus grand = copie plus rapide (mais consomme RAM)

**Recommandation** : `bs=1M` ou `bs=4M` (compromis)

---

### Option status : Affichage Progression

```bash
# Aucun affichage (défaut ancien)
dd if=/dev/sda1 of=backup.img
# (silence complet, impossible savoir progression)

# Affichage final seulement
dd if=/dev/sda1 of=backup.img status=none
# (rien affiché)

# Affichage progression (RECOMMANDÉ)
dd if=/dev/sda1 of=backup.img bs=1M status=progress
# 1234567890 bytes (1.2 GB, 1.1 GiB) copied, 45 s, 27.4 MB/s
# (mise à jour temps réel)
```

**💡 Toujours** utiliser `status=progress` pour visibilité

---

### Option count : Limiter Nombre Blocs

```bash
# Copier exactement 100 blocs
dd if=/dev/sda of=partial.img bs=1M count=100
# Copie 100 Mo (100 blocs × 1 Mo)

# Créer fichier 1 Go de zéros
dd if=/dev/zero of=1gb-file.dat bs=1M count=1024

# Créer fichier 10 Mo aléatoire
dd if=/dev/urandom of=random-10mb.bin bs=1M count=10
```

---

### Option conv : Conversions

```bash
# Ignorer erreurs lecture (disque défectueux)
dd if=/dev/sda of=backup.img bs=1M conv=noerror,sync

# noerror : Continue malgré erreurs
# sync : Remplit blocs erreur par zéros

# Conversion ASCII → EBCDIC
dd if=input.txt of=output.txt conv=ebcdic

# Conversion minuscules → majuscules
echo "hello world" | dd conv=ucase
# HELLO WORLD
```

---

## 📋 Antisèche - rsync et dd

### rsync Commandes

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `rsync -av src/ dst/` | Synchroniser local | `rsync -av /data/ /backup/` |
| `rsync -av src/ user@srv:/dst/` | Backup remote | `rsync -av /home/ user@backup:/backups/` |
| `rsync -av user@srv:/src/ dst/` | Restauration remote | `rsync -av user@backup:/backups/ /home/` |
| `rsync -avn src/ dst/` | Simulation (dry run) | `rsync -avn /data/ /backup/` |
| `rsync -av --delete src/ dst/` | Sync exact (supprime) | `rsync -av --delete /src/ /dst/` |
| `rsync -av --exclude="*.log" src/ dst/` | Exclure pattern | `rsync -av --exclude="*.tmp" /data/ /backup/` |
| `rsync -av --progress src/ dst/` | Avec progression | `rsync -av --progress /large/ /backup/` |
| `rsync -avz src/ user@srv:/dst/` | Avec compression | `rsync -avz /data/ user@remote:/backup/` |
| `rsync -av --bwlimit=1000 src/ dst/` | Limiter bande passante | `rsync -av --bwlimit=5000 /data/ /backup/` |

---

### dd Commandes

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `dd if=/dev/sdX of=backup.img bs=1M status=progress` | Backup partition | `dd if=/dev/sda1 of=part1.img bs=1M status=progress` |
| `dd if=backup.img of=/dev/sdX bs=1M status=progress` | Restaurer partition | `dd if=part1.img of=/dev/sda1 bs=1M status=progress` |
| `dd if=/dev/sdX of=/dev/sdY bs=4M status=progress` | Cloner disque | `dd if=/dev/sda of=/dev/sdb bs=4M status=progress` |
| `dd if=ubuntu.iso of=/dev/sdX bs=4M status=progress` | Créer clé USB bootable | `dd if=linux.iso of=/dev/sdb bs=4M status=progress` |
| `dd if=/dev/zero of=file.dat bs=1M count=100` | Créer fichier zéros | `dd if=/dev/zero of=empty.dat bs=1M count=1024` |
| `dd if=/dev/urandom of=file.bin bs=1M count=10` | Créer fichier aléatoire | `dd if=/dev/urandom of=random.bin bs=1M count=100` |
| `dd if=/dev/sdX \| gzip > backup.img.gz` | Backup compressé | `dd if=/dev/sda1 bs=1M \| gzip > backup.img.gz` |
| `gunzip -c backup.img.gz \| dd of=/dev/sdX bs=1M` | Restaurer compressé | `gunzip -c backup.img.gz \| dd of=/dev/sda1 bs=1M` |

---

## 🎓 Points Clés pour l'Examen

✅ **rsync** : Remote synchronization (synchronisation incrémentale)  
✅ **rsync prérequis remote** : Serveur SSH actif destination  
✅ **rsync -a** : Archive mode (préserve permissions, dates, propriétaire, récursif)  
✅ **rsync slash** : `source/` (contenu) vs `source` (dossier lui-même)  
✅ **rsync backup** : `rsync -av local/ user@remote:/backup/`  
✅ **rsync restauration** : `rsync -av user@remote:/backup/ local/`  
✅ **rsync --delete** : Synchronisation exacte (supprime fichiers destination absents source)  
✅ **rsync --exclude** : Exclure fichiers/patterns  
✅ **rsync --dry-run** : Simulation SANS exécution  
✅ **rsync -z** : Compression transfert (bande passante)  
✅ **dd** : Disk Duplicator (copie blocs bruts niveau bas)  
✅ **dd if=** : Input File (source)  
✅ **dd of=** : Output File (destination)  
✅ **dd bs=** : Block Size (taille blocs, recommandé 1M ou 4M)  
✅ **dd status=progress** : Afficher progression (ESSENTIEL)  
✅ **dd DANGER** : Vérifier destination (risque écrasement données)  
✅ **dd vs rsync** : dd = niveau bas (partitions/disques), rsync = niveau fichiers  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Backup Quotidien Serveur avec rsync

```bash
#!/bin/bash
# Script backup quotidien

# Variables
SOURCE="/var/www/html"
BACKUP_SERVER="user@backup-srv"
BACKUP_DIR="/backups/web-$(date +%Y%m%d)"
LOG="/var/log/backup-web.log"

# Backup avec rsync
echo "=== Backup $(date) ===" >> "$LOG"

rsync -avz \
  --delete \
  --exclude="cache/" \
  --exclude="*.log" \
  --stats \
  "$SOURCE/" \
  "$BACKUP_SERVER:$BACKUP_DIR/" \
  >> "$LOG" 2>&1

# Vérifier succès
if [ $? -eq 0 ]; then
    echo "Backup réussi" >> "$LOG"
else
    echo "Backup ÉCHOUÉ" >> "$LOG"
    # Envoyer alerte email
    mail -s "Backup failed" admin@example.com < "$LOG"
fi
```

---

### Scénario 2 : Synchronisation Bidirectionnelle

```bash
# Serveur A → Serveur B (backup)
rsync -avz --delete \
  /data/shared/ \
  userb@server-b:/data/shared/

# Serveur B → Serveur A (restauration si nécessaire)
rsync -avz --delete \
  userb@server-b:/data/shared/ \
  /data/shared/

# Dry run avant synchronisation
rsync -avn --delete /data/ userb@server-b:/data/
# Vérifier output
# Puis exécuter pour de vrai
rsync -av --delete /data/ userb@server-b:/data/
```

---

### Scénario 3 : Cloner Disque avec dd

```bash
# Lister disques disponibles
lsblk
# sda      8:0    0  500G  0 disk  ← Source
# ├─sda1   8:1    0  100G  0 part
# └─sda2   8:2    0  400G  0 part
# sdb      8:16   0  500G  0 disk  ← Destination (vide)

# Cloner disque complet sda → sdb
sudo dd if=/dev/sda of=/dev/sdb bs=4M status=progress
# 512000000000 bytes (512 GB, 477 GiB) copied, 1800 s, 284 MB/s

# Vérifier clonage
sudo fdisk -l /dev/sdb
# (partitions identiques à /dev/sda)

# Alternative : Cloner partition par partition
sudo dd if=/dev/sda1 of=/dev/sdb1 bs=4M status=progress
sudo dd if=/dev/sda2 of=/dev/sdb2 bs=4M status=progress
```

---

### Scénario 4 : Créer Clé USB Bootable

```bash
# Télécharger ISO
wget https://releases.ubuntu.com/ubuntu-22.04.iso

# Identifier clé USB
lsblk
# sdb      8:16   1   32G  0 disk  ← Clé USB
# └─sdb1   8:17   1   32G  0 part

# ⚠️ Démonter clé USB
sudo umount /dev/sdb1

# Créer clé bootable
sudo dd if=ubuntu-22.04.iso of=/dev/sdb bs=4M status=progress
# 2500000000 bytes (2.5 GB, 2.3 GiB) copied, 120 s, 20.8 MB/s

# Synchroniser (vider buffers)
sync

# Éjecter
sudo eject /dev/sdb

# Clé USB bootable prête !
```

---

### Scénario 5 : Backup Partition Compressé

```bash
# Backup partition avec compression (économiser espace)
sudo dd if=/dev/sda1 bs=1M status=progress | gzip -c > /backup/sda1-backup.img.gz
# 10000000000 bytes (10 GB) copied → 2.3 GB compressed

# Vérifier taille
ls -lh /backup/sda1-backup.img.gz
# -rw-r--r-- 1 root root 2.3G Feb 2 11:00 sda1-backup.img.gz

# Restaurer partition compressée
gunzip -c /backup/sda1-backup.img.gz | sudo dd of=/dev/sda1 bs=1M status=progress

# Alternative : pv pour progression
sudo dd if=/dev/sda1 bs=1M | pv | gzip > /backup/sda1.img.gz
# 2.31GiB 0:02:15 [17.4MiB/s] [=====>                ] 23% ETA 0:07:45
```

---

### Scénario 6 : rsync Avec Exclusions Multiples

```bash
# Fichier exclusions
cat /etc/rsync-exclude.txt
# *.log
# *.tmp
# .git/
# node_modules/
# __pycache__/
# .cache/
# *.pyc
# .DS_Store
# Thumbs.db

# Backup projet avec exclusions
rsync -av \
  --exclude-from=/etc/rsync-exclude.txt \
  --delete \
  --stats \
  /home/user/projects/ \
  user@backup:/backups/projects/

# OU : Exclusions inline
rsync -av \
  --exclude="*.log" \
  --exclude="*.tmp" \
  --exclude=".git" \
  --exclude="node_modules" \
  --exclude="__pycache__" \
  --delete \
  /home/user/projects/ \
  user@backup:/backups/projects/
```

---

### Scénario 7 : Effacer Disque Sécurisé avec dd

```bash
# Effacer disque avec zéros (rapide)
sudo dd if=/dev/zero of=/dev/sdb bs=4M status=progress
# (écrase tout avec 00000000...)

# Effacer disque avec aléatoire (sécurisé, lent)
sudo dd if=/dev/urandom of=/dev/sdb bs=1M status=progress
# (écrase avec données aléatoires)

# Effacer plusieurs passes (très sécurisé)
for i in {1..3}; do
    echo "Pass $i/3"
    sudo dd if=/dev/urandom of=/dev/sdb bs=1M status=progress
done

# Alternative moderne : shred
sudo shred -v -n 3 /dev/sdb
# (3 passes aléatoires automatiques)
```

---

### Scénario 8 : Backup Incrémental rsync

```bash
# Backup complet initial
rsync -av --delete \
  /data/ \
  /backup/full-$(date +%Y%m%d)/

# Backup incrémental (hard links vers unchanged files)
rsync -av \
  --delete \
  --link-dest=/backup/full-20260201/ \
  /data/ \
  /backup/incr-$(date +%Y%m%d)/

# Résultat :
# /backup/full-20260201/  ← Backup complet (1 To)
# /backup/incr-20260202/  ← Incrémental (100 Mo nouveaux + liens)
# /backup/incr-20260203/  ← Incrémental (50 Mo nouveaux + liens)

# Économie espace : Hard links fichiers inchangés
ls -lhi /backup/*/important-file.txt
# 1234567 -rw-r--r-- 3 user user 10M file  ← Même inode
# (3 liens, même fichier physique, pas 3 copies)
```

---

### Scénario 9 : Test Vitesse Disque avec dd

```bash
# Test écriture disque
dd if=/dev/zero of=/tmp/test-write.dat bs=1M count=1024 oflag=direct
# 1073741824 bytes (1.1 GB) copied, 5.2 s, 206 MB/s

# Test lecture disque
sudo sh -c "sync; echo 3 > /proc/sys/vm/drop_caches"  # Vider cache
dd if=/tmp/test-write.dat of=/dev/null bs=1M iflag=direct
# 1073741824 bytes (1.1 GB) copied, 3.8 s, 282 MB/s

# Nettoyage
rm /tmp/test-write.dat

# Benchmark complet
echo "=== Benchmark disque ==="
echo "Écriture:"
dd if=/dev/zero of=/tmp/bench bs=1G count=1 oflag=direct 2>&1 | grep copied
echo "Lecture:"
dd if=/tmp/bench of=/dev/null bs=1G iflag=direct 2>&1 | grep copied
rm /tmp/bench
```

---

### Scénario 10 : Récupération Données Disque Défectueux

```bash
# Disque avec secteurs défectueux
# dd seul échoue aux erreurs

# ❌ Échoue :
sudo dd if=/dev/sda of=/backup/rescue.img bs=1M status=progress
# dd: error reading '/dev/sda': Input/output error

# ✅ Continue malgré erreurs :
sudo dd if=/dev/sda of=/backup/rescue.img bs=1M conv=noerror,sync status=progress
# conv=noerror : Continue lecture malgré erreurs
# conv=sync : Remplit secteurs illisibles par zéros

# Alternative : ddrescue (plus puissant)
sudo ddrescue -d -r3 /dev/sda /backup/rescue.img /backup/rescue.log
# -d : Direct I/O
# -r3 : 3 tentatives secteurs défectueux
# Crée log pour reprise si interruption

# Monter image récupérée
sudo mount -o loop,ro /backup/rescue.img /mnt/rescue
# Copier fichiers récupérables
rsync -av /mnt/rescue/ /recovery/
```

---

## ⚠️ Erreurs Courantes

### Erreur 1 : rsync Slash Oublié

```bash
# ❌ Sans slash : Copie DOSSIER
rsync -av source destination/
# Résultat : destination/source/file1.txt

# ✅ Avec slash : Copie CONTENU
rsync -av source/ destination/
# Résultat : destination/file1.txt

# Règle :
# source/  → Contenu de source
# source   → Dossier source lui-même
```

---

### Erreur 2 : dd Inversion if/of

```bash
# ❌ CATASTROPHE : Inversion source/destination
sudo dd if=/dev/sda of=/dev/sdb  # ✅ OK (clone sda → sdb)
sudo dd if=/dev/sdb of=/dev/sda  # ❌ INVERSE (écrase sda avec sdb vide!)

# ⚠️ TOUJOURS vérifier :
lsblk  # Identifier disques
# sda = 500G (données)  ← Source
# sdb = 500G (vide)     ← Destination

# if = source (input)
# of = destination (output)
```

---

### Erreur 3 : dd Sans status=progress

```bash
# ❌ Pas de retour :
sudo dd if=/dev/sda of=/dev/sdb bs=4M
# (silence complet pendant 2 heures, impossible savoir progression)

# ✅ Avec progression :
sudo dd if=/dev/sda of=/dev/sdb bs=4M status=progress
# 52428800000 bytes (52 GB) copied, 300 s, 174 MB/s
# (mise à jour temps réel)

# Alternative : pv
sudo dd if=/dev/sda bs=4M | pv -s 500G | sudo dd of=/dev/sdb bs=4M
# 52.4GiB 0:05:00 [178MiB/s] [==>                    ] 10% ETA 0:45:00
```

---

### Erreur 4 : rsync --delete Sans Vérification

```bash
# ❌ DANGER : --delete supprime fichiers
rsync -av --delete source/ destination/

# Si source/ vide par erreur :
# destination/ ENTIÈREMENT VIDÉE !

# ✅ SÉCURITÉ : Toujours dry run avant
rsync -avn --delete source/ destination/
# Vérifier output (quels fichiers seraient supprimés)

# Puis exécuter
rsync -av --delete source/ destination/
```

---

### Erreur 5 : dd Block Size Trop Petit

```bash
# ❌ LENT : Block size trop petit
sudo dd if=/dev/sda of=/dev/sdb bs=512 status=progress
# 52428800000 bytes copied, 5400 s, 9.7 MB/s  ← 1h30 !

# ✅ RAPIDE : Block size optimal
sudo dd if=/dev/sda of=/dev/sdb bs=4M status=progress
# 52428800000 bytes copied, 300 s, 174 MB/s  ← 5 min !

# Recommandation : bs=1M ou bs=4M
```

---

### Erreur 6 : rsync Remote Sans SSH

```bash
# ❌ ERREUR : SSH pas installé/actif
rsync -av /data/ user@remote:/backup/
# ssh: connect to host remote port 22: Connection refused

# ✅ VÉRIFIER SSH destination :
# Sur serveur remote :
sudo systemctl status ssh
# ● ssh.service - OpenBSD Secure Shell server
#    Active: active (running)

# Si inactif :
sudo systemctl start ssh
sudo systemctl enable ssh

# Tester connexion :
ssh user@remote
# Puis rsync
```

---

### Erreur 7 : dd Partition Montée

```bash
# ❌ ERREUR : Partition montée pendant dd
mount | grep sda1
# /dev/sda1 on /mnt/data type ext4

sudo dd if=/dev/sda1 of=/backup/sda1.img
# (données corrompues, fichiers en cours écriture)

# ✅ CORRECT : Démonter avant
sudo umount /dev/sda1
sudo dd if=/dev/sda1 of=/backup/sda1.img bs=1M status=progress

# OU : Snapshot LVM (si disponible)
sudo lvcreate -s -L 10G -n snap /dev/vg0/lv0
sudo dd if=/dev/vg0/snap of=/backup/lv0.img bs=1M status=progress
```

---

### Erreur 8 : rsync Permissions Insuffisantes

```bash
# ❌ ERREUR : Permissions destination
rsync -av /home/user/ /backup/
# rsync: mkstemp "/backup/.file.txt.XXXXXX" failed: Permission denied

# ✅ SOLUTION 1 : sudo local
sudo rsync -av /home/user/ /backup/

# ✅ SOLUTION 2 : Permissions remote via sudo destination
rsync -av /home/user/ user@remote:/backup/
# (user doit avoir droits écriture /backup)

# ✅ SOLUTION 3 : rsync avec sudo remote
rsync -av --rsync-path="sudo rsync" /home/user/ user@remote:/backup/
```

---

**🎯 Prochaine étape** : Work on the Command Line - Part 3 (Commandes shell avancées)

*Dernière mise à jour: 2 février 2026*
