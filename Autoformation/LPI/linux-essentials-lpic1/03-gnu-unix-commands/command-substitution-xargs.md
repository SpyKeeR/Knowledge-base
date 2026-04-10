# 🔄 Command Substitution et xargs

**Module** : GNU and Unix Commands  
**Cours** : Command Substitution & xargs  
**Objectif** : Maîtriser substitution commandes et transformation stdin→arguments

---

## 💡 Command Substitution : Concepts

### Qu'est-ce que Command Substitution ?

**Rôle** : Exécuter commande et **insérer résultat** dans autre commande

**Analogie** 📦 :
- **Commande interne** = Fabrique (produit résultat)
- **Substitution** = Emballage résultat
- **Commande externe** = Client (utilise résultat comme paramètre)

---

### Deux Syntaxes

#### 1. Backticks `` (Ancienne)

```bash
# Syntaxe ancienne
result=`date`
echo "Today is `date`"
```

**⚠️ Obsolète** : Difficile à lire, problèmes nested

---

#### 2. $() (Moderne - Recommandée)

```bash
# Syntaxe moderne
result=$(date)
echo "Today is $(date)"
```

**✅ Recommandée** : Lisible, nested facile, standard POSIX

---

### Pourquoi $() Plutôt que Backticks ?

| Critère | Backticks `` | $() |
|---------|-------------|-----|
| **Lisibilité** | ⭐⭐ Difficile | ⭐⭐⭐⭐⭐ Excellente |
| **Nested** | ⭐ Échappement complexe | ⭐⭐⭐⭐⭐ Facile |
| **Standard** | Ancien sh | POSIX moderne |
| **Échappement** | Complexe `\`` | Simple |
| **Recommandation** | ❌ Éviter | ✅ Utiliser |

---

## 🔧 Command Substitution : Exemples

### Usage Basique

```bash
# Capturer date
current_date=$(date)
echo "Date: $current_date"
# Date: Sun Feb  2 12:00:00 CET 2026

# Inline dans commande
echo "Today is $(date +%Y-%m-%d)"
# Today is 2026-02-02

# Nombre fichiers
file_count=$(ls | wc -l)
echo "Files: $file_count"
# Files: 42
```

---

### Nested (Commandes Imbriquées)

```bash
# ❌ Backticks : DIFFICILE
echo "Files: `ls \`pwd\``"
# Échappement complexe \`

# ✅ $() : FACILE
echo "Files in $(basename $(pwd))"
# Files in my-project

# Nested complexe
echo "User $(whoami) has $(ls -1 $(echo $HOME) | wc -l) files"
# User alice has 28 files
```

---

### Dans Variables

```bash
# Hostname
SERVER=$(hostname)
echo "Server: $SERVER"
# Server: web-server-01

# Kernel version
KERNEL=$(uname -r)
echo "Kernel: $KERNEL"
# Kernel: 5.15.0-58-generic

# Uptime
UPTIME=$(uptime -p)
echo "Uptime: $UPTIME"
# Uptime: up 3 days, 4 hours, 12 minutes
```

---

### Dans Arguments Commandes

```bash
# Backup avec date
cp important.txt important-$(date +%Y%m%d).txt.bak
# important-20260202.txt.bak

# Créer dossier avec timestamp
mkdir backup-$(date +%Y%m%d-%H%M%S)
# backup-20260202-120030/

# Log avec hostname
echo "Log from $(hostname)" >> /var/log/app.log
```

---

### Calculs Arithmétiques

```bash
# Avec bc
result=$(echo "scale=2; 22/7" | bc)
echo "Pi approximation: $result"
# Pi approximation: 3.14

# Avec expr
total=$(expr 10 + 20 + 30)
echo "Total: $total"
# Total: 60

# Bash arithmetic (plus simple)
sum=$((10 + 20 + 30))
echo "Sum: $sum"
# Sum: 60
```

---

## 🔗 xargs : Concepts

### Problème Résolu par xargs

**Problème** : Beaucoup commandes prennent **arguments** (pas stdin)

```bash
# ❌ NE FONCTIONNE PAS : rm attend arguments (pas stdin)
ls *.tmp | rm
# rm: missing operand

# ✅ FONCTIONNE : xargs transforme stdin → arguments
ls *.tmp | xargs rm
```

**Analogie** 🏭 :
- **Pipe |** = Tuyau (transporte eau/données)
- **xargs** = Adaptateur (convertit flux → arguments individuels)
- **Commande finale** = Machine (attend pièces individuelles, pas flux)

---

### Fonctionnement xargs

**Syntaxe** :
```bash
commande1 | xargs commande2
```

**Flux** :
1. `commande1` produit **stdout** (lignes texte)
2. **Pipe |** envoie stdout vers stdin xargs
3. **xargs** lit stdin, découpe en **arguments**
4. **xargs** exécute `commande2 arg1 arg2 arg3...`

**Exemple** :
```bash
echo "file1.txt file2.txt file3.txt" | xargs rm

# xargs exécute :
# rm file1.txt file2.txt file3.txt
```

---

### xargs vs Boucle for

```bash
# Avec xargs (compact)
find . -name "*.log" | xargs rm

# Équivalent boucle (verbeux)
find . -name "*.log" | while read file; do
    rm "$file"
done

# OU
for file in $(find . -name "*.log"); do
    rm "$file"
done
```

**Avantages xargs** :
- ✅ Plus **rapide** (moins d'invocations commande)
- ✅ Plus **compact** (une ligne)
- ✅ Gère **limites ARG_MAX** automatiquement

---

## ⚙️ xargs : Options Principales

### -n : Nombre Arguments par Invocation

**Option -n N** : Max **N arguments** par exécution commande

```bash
# Sans -n : Tous arguments en une fois
echo "1 2 3 4 5" | xargs echo
# 1 2 3 4 5  ← Une seule exécution

# Avec -n 2 : 2 arguments par fois
echo "1 2 3 4 5" | xargs -n 2 echo
# 1 2        ← echo 1 2
# 3 4        ← echo 3 4
# 5          ← echo 5

# Pratique : Traiter par batch
echo "file1 file2 file3 file4" | xargs -n 2 cp -t /backup/
# cp file1 file2 /backup/
# cp file3 file4 /backup/
```

**Usage** : Limiter charge, éviter dépassement ARG_MAX

---

### -L : Nombre Lignes par Invocation

**Option -L N** : Max **N lignes** par exécution commande

```bash
# Fichier avec lignes
cat files.txt
# file1.txt
# file2.txt
# file3.txt

# Sans -L : Toutes lignes
cat files.txt | xargs echo
# file1.txt file2.txt file3.txt

# Avec -L 1 : Une ligne par fois
cat files.txt | xargs -L 1 echo
# file1.txt
# file2.txt
# file3.txt

# Avec -L 2 : Deux lignes par fois
cat files.txt | xargs -L 2 echo
# file1.txt file2.txt
# file3.txt
```

**Usage** : Traiter entrées ligne par ligne

---

### -0 : Délimiteur Caractère Nul

**Option -0** : Arguments séparés par **\0** (null) au lieu espaces/newlines

**Problème** : Noms fichiers avec **espaces** ou **newlines**

```bash
# ❌ ERREUR : Espace dans nom fichier
touch "my document.txt"
ls *.txt | xargs rm
# rm: cannot remove 'my': No such file or directory
# rm: cannot remove 'document.txt': No such file or directory
# xargs interprète "my" et "document.txt" comme 2 fichiers

# ✅ SOLUTION : find -print0 + xargs -0
find . -name "*.txt" -print0 | xargs -0 rm
# Fonctionne avec espaces, newlines, caractères spéciaux
```

**Caractère nul \0** : Seul caractère **interdit** dans noms fichiers Linux

---

### -I : Replacement String (Placeholder)

**Option -I {}** : Placer arguments **où on veut** dans commande

**Sans -I** : Arguments **automatiquement à la fin**

```bash
# Sans -I : Arguments à la fin
echo "file1 file2" | xargs cp /backup/
# cp /backup/ file1 file2  ← ERREUR ordre

# Avec -I : Placement manuel
echo "file1 file2" | xargs -I {} cp {} /backup/
# cp file1 /backup/
# cp file2 /backup/
```

**Placeholder** : Généralement `{}` (comme find -exec)

```bash
# Backup avec renommage
find . -name "*.txt" | xargs -I {} cp {} {}.bak

# Plusieurs occurrences
find . -name "*.log" | xargs -I {} sh -c 'gzip {} && mv {}.gz /backup/'

# Custom placeholder
find . -name "*.conf" | xargs -I FILE cp FILE /etc/backup/FILE
```

---

### -P : Parallélisation

**Option -P N** : Exécuter **N commandes en parallèle**

```bash
# Séquentiel (défaut)
cat urls.txt | xargs -n 1 wget
# Télécharge 1 par 1

# Parallèle : 5 simultanés
cat urls.txt | xargs -n 1 -P 5 wget
# Télécharge 5 en même temps

# Compression parallèle (tous CPU)
find . -name "*.log" | xargs -P $(nproc) gzip
# gzip sur tous cœurs CPU
```

**Usage** : Accélérer traitements longs (downloads, compression, conversion)

---

### -p : Prompt (Confirmation)

**Option -p** : Demander **confirmation** avant chaque exécution

```bash
# Suppression sécurisée
find . -name "*.tmp" | xargs -p rm

# Prompt pour chaque fichier :
# rm file1.tmp?...y
# rm file2.tmp?...y
# rm file3.tmp?...n  ← Refusé
```

**Usage** : Sécurité avant opérations destructrices (rm, mv)

---

### -t : Trace (Verbose)

**Option -t** : **Afficher** commande avant exécution

```bash
# Voir commandes exécutées
echo "1 2 3" | xargs -t -n 1 echo

# Affichage :
# echo 1
# 1
# echo 2
# 2
# echo 3
# 3
```

**Usage** : Debug, vérifier commandes générées

---

### -r : No Run If Empty

**Option -r** (ou **--no-run-if-empty**) : Ne **pas exécuter** si stdin vide

```bash
# Sans -r : Exécute même si vide
echo "" | xargs echo "Result:"
# Result:  ← Exécuté avec stdin vide

# Avec -r : Pas exécution si vide
echo "" | xargs -r echo "Result:"
# (rien)  ← Pas exécuté
```

**Usage** : Éviter exécutions inutiles, erreurs commandes

---

## 📋 Antisèche - Command Substitution & xargs

### Command Substitution

| Syntaxe | Je veux... | Exemple |
|---------|-----------|---------|
| `$(commande)` | Capturer résultat commande | `date=$(date)` |
| `echo $(cmd)` | Inline dans commande | `echo "Date: $(date)"` |
| `$(cmd1 $(cmd2))` | Nested (imbriqué) | `basename $(pwd)` |
| `var=$(calcul)` | Calcul arithmétique | `result=$(echo "2+2" \| bc)` |
| `cmd-$(date).log` | Nom fichier dynamique | `backup-$(date +%Y%m%d).tar.gz` |

**⚠️ Éviter backticks** `` : Obsolète, utiliser `$()` toujours

---

### xargs Options

| Option | Je veux... | Exemple |
|--------|-----------|---------|
| `xargs cmd` | stdin → arguments | `ls \| xargs rm` |
| `xargs -n N` | N arguments par invocation | `xargs -n 2 echo` |
| `xargs -L N` | N lignes par invocation | `xargs -L 1 echo` |
| `xargs -0` | Délimiteur null (espaces noms) | `find -print0 \| xargs -0 rm` |
| `xargs -I {}` | Placer arguments où je veux | `xargs -I {} cp {} /dest/` |
| `xargs -P N` | N exécutions parallèles | `xargs -P 5 wget` |
| `xargs -p` | Confirmation interactive | `xargs -p rm` |
| `xargs -t` | Afficher commandes (trace) | `xargs -t echo` |
| `xargs -r` | Pas exécuter si stdin vide | `xargs -r echo` |

---

### Combinaisons Fréquentes

| Commande | Je veux... |
|----------|-----------|
| `find -print0 \| xargs -0 rm` | Supprimer fichiers (espaces OK) |
| `find \| xargs -I {} cp {} /dest/` | Copier avec placement custom |
| `cat urls.txt \| xargs -n 1 -P 5 wget` | Download parallèle (5 simultanés) |
| `find \| xargs -p rm` | Suppression avec confirmation |
| `ls \| xargs -n 2 echo` | Traiter par batch (2 par 2) |
| `find \| xargs -t grep "pattern"` | Chercher avec trace commandes |

---

## 💡 Scénarios Pratiques

### Scénario 1 : Backup Fichiers Modifiés Récemment

```bash
#!/bin/bash
# Backup fichiers modifiés dernières 24h

BACKUP_DIR="/backup/daily-$(date +%Y%m%d)"
mkdir -p "$BACKUP_DIR"

# Trouver fichiers modifiés + copier avec structure
find /var/www/html -type f -mtime 0 -print0 | \
  xargs -0 -I {} cp --parents {} "$BACKUP_DIR/"

# Vérifier
echo "Backup saved to $BACKUP_DIR"
ls -lR "$BACKUP_DIR"

# --parents : Préserve structure répertoires
# -print0 | xargs -0 : Gère noms espaces
# -I {} : Placement argument custom
```

---

### Scénario 2 : Suppression Batch Sécurisée

```bash
# Trouver fichiers temporaires anciens
find /tmp -type f -name "*.tmp" -mtime +7

# Suppression avec confirmation AVANT chaque fichier
find /tmp -type f -name "*.tmp" -mtime +7 -print0 | \
  xargs -0 -p rm

# Prompt interactif :
# rm /tmp/session123.tmp?...y
# rm /tmp/cache456.tmp?...y
# rm /tmp/important.tmp?...n  ← Gardé

# Alternative : Voir commandes d'abord (dry run)
find /tmp -type f -name "*.tmp" -mtime +7 -print0 | \
  xargs -0 -t echo "Would delete:"
  
# Puis exécuter vraiment
find /tmp -type f -name "*.tmp" -mtime +7 -print0 | \
  xargs -0 rm
```

---

### Scénario 3 : Conversion Images Batch

```bash
#!/bin/bash
# Convertir toutes images PNG → JPEG (qualité 85%)

# Prérequis : imagemagick (convert)
# sudo apt install imagemagick

# Conversion avec renommage
find . -type f -name "*.png" | \
  xargs -I {} bash -c 'convert "{}" -quality 85 "{}.jpg" && rm "{}"'

# Ou : Garder originaux
find . -type f -name "*.png" | \
  xargs -I {} convert {} -quality 85 {}.jpg

# Avec parallélisation (4 CPU)
find . -type f -name "*.png" | \
  xargs -P 4 -I {} convert {} -quality 85 {}.jpg

# Vérifier résultats
ls -lh *.jpg
```

---

### Scénario 4 : Recherche Contenu Multiple Fichiers

```bash
#!/bin/bash
# Chercher pattern dans tous fichiers config

PATTERN="database"

# Méthode 1 : xargs grep
find /etc -type f -name "*.conf" -print0 | \
  xargs -0 grep -H "$PATTERN"

# -H : Affiche nom fichier (même un seul fichier)

# Méthode 2 : Avec comptage occurrences
find /etc -type f -name "*.conf" -print0 | \
  xargs -0 grep -c "$PATTERN" | \
  grep -v ":0$"  # Filtrer fichiers 0 occurrence

# /etc/mysql/my.cnf:5
# /etc/apache2/sites-available/default:2

# Méthode 3 : Résumé par fichier
find /etc -type f -name "*.conf" -print0 | \
  xargs -0 -I {} sh -c 'count=$(grep -c "'"$PATTERN"'" "{}"); \
    [ $count -gt 0 ] && echo "{}: $count matches"'
```

---

### Scénario 5 : Création Répertoires depuis Liste

```bash
# Fichier liste projets
cat projects.txt
# website/frontend
# website/backend
# mobile/ios
# mobile/android
# docs/api
# docs/user-guide

# Créer toute structure
cat projects.txt | xargs -I {} mkdir -p /workspace/{}

# Vérifier arborescence
tree /workspace/
# /workspace/
# ├── website/
# │   ├── frontend/
# │   └── backend/
# ├── mobile/
# │   ├── ios/
# │   └── android/
# └── docs/
#     ├── api/
#     └── user-guide/

# Avec permissions spécifiques
cat projects.txt | xargs -I {} sh -c 'mkdir -p /workspace/{} && chmod 755 /workspace/{}'
```

---

### Scénario 6 : Download URLs Parallèle

```bash
#!/bin/bash
# Télécharger liste URLs en parallèle

# Fichier URLs
cat urls.txt
# https://example.com/file1.zip
# https://example.com/file2.zip
# https://example.com/file3.zip
# https://example.com/file4.zip
# https://example.com/file5.zip

# Download séquentiel (lent)
cat urls.txt | xargs -n 1 wget

# Download parallèle : 5 simultanés (rapide ⚡)
cat urls.txt | xargs -n 1 -P 5 wget

# Avec progression et logs
cat urls.txt | xargs -n 1 -P 5 -I {} sh -c \
  'echo "Downloading {}" && wget -q {} && echo "✓ {}"'

# Avec retry si échec
cat urls.txt | xargs -n 1 -P 3 -I {} wget -t 3 -c {}
# -t 3 : 3 tentatives
# -c : Continuer download interrompu
```

---

### Scénario 7 : Compression Parallèle Logs

```bash
#!/bin/bash
# Compresser anciens logs en parallèle

# Trouver logs > 7 jours
find /var/log -type f -name "*.log" -mtime +7

# Compression parallèle (tous CPU)
CPU_COUNT=$(nproc)
echo "Using $CPU_COUNT CPUs for parallel compression"

find /var/log -type f -name "*.log" -mtime +7 -print0 | \
  xargs -0 -P "$CPU_COUNT" -I {} sh -c 'gzip "{}" && echo "Compressed {}"'

# Vérifier résultats
find /var/log -type f -name "*.log.gz" -mtime -1 -ls

# Calcul espace économisé
ORIGINAL=$(find /var/log -name "*.log.gz" -exec zcat {} \; | wc -c)
COMPRESSED=$(find /var/log -name "*.log.gz" -exec cat {} \; | wc -c)
echo "Original: $(numfmt --to=iec $ORIGINAL)"
echo "Compressed: $(numfmt --to=iec $COMPRESSED)"
echo "Saved: $((100 - COMPRESSED * 100 / ORIGINAL))%"
```

---

### Scénario 8 : Renommage Batch avec sed

```bash
#!/bin/bash
# Renommer fichiers : remplacer espaces par underscores

# Fichiers avec espaces
ls -1
# my file.txt
# another document.pdf
# final report.docx

# Renommer : espaces → underscores
ls -1 | while read file; do
    newname=$(echo "$file" | sed 's/ /_/g')
    [ "$file" != "$newname" ] && mv "$file" "$newname"
done

# Avec xargs (plus élégant)
find . -maxdepth 1 -type f -name "* *" | \
  xargs -I {} bash -c 'mv "{}" "$(echo {} | sed "s/ /_/g")"'

# Vérifier
ls -1
# my_file.txt
# another_document.pdf
# final_report.docx

# Renommage prefix (ajouter date)
find . -maxdepth 1 -type f -name "*.txt" | \
  xargs -I {} mv {} "$(date +%Y%m%d)_{}"
```

---

### Scénario 9 : Permissions Multiples Fichiers

```bash
#!/bin/bash
# Corriger permissions après extraction archive

# Problème : Tous fichiers exécutables après unzip
find . -type f -ls | head -5
# -rwxrwxrwx  script.sh
# -rwxrwxrwx  data.txt     ← Pas normal
# -rwxrwxrwx  config.json  ← Pas normal

# Corriger : Fichiers normaux → 644
find . -type f ! -name "*.sh" -print0 | \
  xargs -0 chmod 644

# Scripts → 755
find . -type f -name "*.sh" -print0 | \
  xargs -0 chmod 755

# Répertoires → 755
find . -type d -print0 | \
  xargs -0 chmod 755

# Vérifier avec trace
find . -type f -print0 | \
  xargs -0 -t -I {} stat -c "%A %n" {}
# chmod 644 ./data.txt
# -rw-r--r-- ./data.txt
# chmod 755 ./script.sh
# -rwxr-xr-x ./script.sh
```

---

### Scénario 10 : Génération Rapports avec Command Substitution

```bash
#!/bin/bash
# Générer rapport système multi-serveurs

SERVERS="web-01 web-02 db-01 cache-01"
REPORT_FILE="system-report-$(date +%Y%m%d-%H%M).txt"

# Header rapport
cat > "$REPORT_FILE" <<EOF
========================================
    System Report
    Generated: $(date)
    By: $(whoami)@$(hostname)
========================================

EOF

# Collecter infos chaque serveur
echo "$SERVERS" | xargs -n 1 -I {} sh -c "
echo '--- Server: {} ---' >> $REPORT_FILE
ssh {} 'hostname; uptime; df -h / | tail -1; free -h | grep Mem' >> $REPORT_FILE
echo '' >> $REPORT_FILE
"

# Section finale
cat >> "$REPORT_FILE" <<EOF
========================================
    Summary
    Total Servers: $(echo $SERVERS | wc -w)
    Report Location: $(pwd)/$REPORT_FILE
========================================
EOF

# Afficher
cat "$REPORT_FILE"

# Email rapport
mail -s "System Report $(date +%Y-%m-%d)" admin@example.com < "$REPORT_FILE"
```

---

## 🎓 Points Clés pour l'Examen

✅ **Command Substitution** : `$(commande)` insère résultat commande  
✅ **$() vs backticks** : Toujours utiliser `$()` (moderne, nested facile)  
✅ **Nested** : `$(cmd1 $(cmd2))` imbrication facile avec `$()`  
✅ **xargs** : Transforme stdin → arguments commande  
✅ **xargs -n N** : N arguments par invocation commande  
✅ **xargs -L N** : N lignes par invocation  
✅ **xargs -0** : Délimiteur null (avec find -print0) pour espaces noms  
✅ **xargs -I {}** : Placer arguments où on veut (replacement string)  
✅ **xargs -P N** : Parallélisation (N commandes simultanées)  
✅ **xargs -p** : Confirmation interactive (sécurité)  
✅ **xargs -t** : Trace (affiche commandes exécutées)  
✅ **xargs -r** : Pas exécuter si stdin vide  
✅ **find -print0 | xargs -0** : Combinaison STANDARD espaces noms  
✅ **ARG_MAX** : xargs gère limite arguments automatiquement  
✅ **Parallélisation** : `-P $(nproc)` utilise tous CPU  

---

## ⚠️ Erreurs Courantes

### Erreur 1 : Backticks Nested

```bash
# ❌ ERREUR : Backticks nested (échappement complexe)
echo "Path: `dirname \`pwd\``"
# bash: syntax error

# Alternative échappement (LAID)
echo "Path: `dirname \\\`pwd\\\``"

# ✅ SOLUTION : Utiliser $()
echo "Path: $(dirname $(pwd))"
# Path: /home/user
```

**Règle** : **JAMAIS** backticks, toujours `$()`

---

### Erreur 2 : xargs Sans -0 (Noms Espaces)

```bash
# Créer fichiers avec espaces
touch "my document.txt" "final report.pdf"

# ❌ ERREUR : xargs sans -0
find . -name "*.txt" | xargs rm
# rm: cannot remove './my': No such file or directory
# rm: cannot remove 'document.txt': No such file or directory

# xargs interprète :
# rm ./my document.txt  ← 2 fichiers séparés

# ✅ SOLUTION : find -print0 + xargs -0
find . -name "*.txt" -print0 | xargs -0 rm
# Fonctionne parfaitement

# Alternative : Quotes (mais -print0 meilleur)
find . -name "*.txt" -exec rm {} \;
```

---

### Erreur 3 : xargs Tous Arguments Une Fois

```bash
# ❌ PROBLÈME : Tous arguments une invocation
echo "file1 file2 file3 file4 file5" | xargs echo "Processing"
# Processing file1 file2 file3 file4 file5
# Une seule ligne (pas ce qu'on veut)

# ✅ SOLUTION : -n 1 (un par un)
echo "file1 file2 file3 file4 file5" | xargs -n 1 echo "Processing"
# Processing file1
# Processing file2
# Processing file3
# Processing file4
# Processing file5

# Utile pour commandes acceptant UN argument
cat files.txt | xargs -n 1 gzip  # Compresse chaque fichier séparément
```

---

### Erreur 4 : xargs -I Oubli Placeholder

```bash
# ❌ ERREUR : -I mais pas de placeholder
find . -name "*.txt" | xargs -I cp /backup/
# cp /backup/  ← Arguments manquants

# xargs attend {} dans commande

# ✅ SOLUTION : Utiliser placeholder
find . -name "*.txt" | xargs -I {} cp {} /backup/
# cp file1.txt /backup/
# cp file2.txt /backup/

# Multiple occurrences
find . -name "*.log" | xargs -I {} sh -c 'gzip {} && mv {}.gz /archive/'
```

---

### Erreur 5 : Command Substitution Quotes Manquantes

```bash
# Fichier avec espaces
file="my document.txt"

# ❌ ERREUR : Pas de quotes
result=$(cat $file)
# cat: my: No such file or directory
# cat: document.txt: No such file or directory

# ✅ SOLUTION : Quotes variables
result=$(cat "$file")
# Fonctionne

# Dans commandes
backup_name=backup-$(date +%Y%m%d).tar.gz  # OK (pas espaces dans date)
file_count=$(ls "$HOME" | wc -l)           # Quotes si espaces possibles
```

---

### Erreur 6 : xargs rm Sans Confirmation

```bash
# ❌ DANGEREUX : Suppression sans vérifier
find /data -name "*.tmp" | xargs rm
# Supprime TOUT immédiatement

# ✅ SÉCURITÉ 1 : Dry run (voir d'abord)
find /data -name "*.tmp" | xargs -t echo "Would delete:"
# Would delete: file1.tmp file2.tmp file3.tmp

# ✅ SÉCURITÉ 2 : Confirmation interactive
find /data -name "*.tmp" | xargs -p rm
# rm file1.tmp?...y
# rm file2.tmp?...y

# ✅ SÉCURITÉ 3 : find -ok (confirmation intégrée)
find /data -name "*.tmp" -ok rm {} \;
```

---

### Erreur 7 : xargs Parallèle Sans Limite

```bash
# ❌ PROBLÈME : Parallélisation excessive
find . -name "*.mp4" | xargs -P 100 ffmpeg -i {} {}.compressed
# 100 processus ffmpeg simultanés → CPU saturé, RAM épuisée

# ✅ SOLUTION : Limiter selon CPU disponibles
CPU_COUNT=$(nproc)
find . -name "*.mp4" | xargs -P "$CPU_COUNT" ffmpeg -i {} {}.compressed

# Ou : Limiter manuellement (4 max)
find . -name "*.mp4" | xargs -P 4 ffmpeg -i {} {}.compressed

# Règle : -P ne doit PAS dépasser CPU count
# Exceptions : I/O-bound tasks (downloads) peuvent être > CPU
cat urls.txt | xargs -n 1 -P 10 wget  # OK (I/O, pas CPU)
```

---

### Erreur 8 : xargs stdin Vide Exécute Quand Même

```bash
# ❌ PROBLÈME : Exécution même si vide
find /nonexistent -name "*.txt" | xargs echo "Found:"
# Found:  ← Exécuté avec stdin vide (pas de fichiers trouvés)

# Problème avec commandes side effects
find /tmp -name "session-*.lock" | xargs rm
# Si aucun fichier : rm exécuté sans arguments → erreur

# ✅ SOLUTION : -r (--no-run-if-empty)
find /tmp -name "session-*.lock" | xargs -r rm
# Pas exécution si stdin vide

# Vérifier avec -t
find /nonexistent -name "*.txt" | xargs -r -t echo "Found:"
# (rien)  ← Pas exécuté
```

---

### Erreur 9 : xargs -I avec -n ou -L

```bash
# ❌ INCOMPATIBLE : -I avec -n
find . -name "*.txt" | xargs -I {} -n 2 cp {} /backup/
# xargs: warning: options --max-args and --replace-str are mutually exclusive

# -I {} traite arguments UN PAR UN automatiquement
# -n N groupe arguments
# Impossible combiner

# ✅ SOLUTION : Utiliser seulement -I
find . -name "*.txt" | xargs -I {} cp {} /backup/

# Si vraiment besoin batch : Pas -I
find . -name "*.txt" | xargs -n 5 cp -t /backup/
# Copie 5 fichiers par invocation vers /backup/
```

---

### Erreur 10 : Command Substitution dans Quotes Simples

```bash
# ❌ PAS D'EXPANSION : Quotes simples
echo 'Today is $(date)'
# Today is $(date)  ← Littéral, pas exécuté

# ✅ QUOTES DOUBLES : Expansion fonctionne
echo "Today is $(date)"
# Today is Sun Feb  2 12:00:00 CET 2026

# Règle :
# 'simple quotes' → Littéral (pas expansion)
# "double quotes" → Expansion variables/commandes
# Pas quotes → Expansion + word splitting
```

---

**🎯 Prochaine étape** : Use Streams, Pipes, and Redirects - Part 2 (tee, process substitution, named pipes)

*Dernière mise à jour: 2 février 2026*
