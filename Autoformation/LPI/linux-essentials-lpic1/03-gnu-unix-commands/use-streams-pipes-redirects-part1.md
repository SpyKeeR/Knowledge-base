# 🔀 Utiliser Streams, Pipes et Redirections - Part 1

**Module** : GNU and Unix Commands  
**Cours** : Use Streams, Pipes, and Redirects Part 1  
**Objectif** : Maîtriser redirections stdin/stdout/stderr et pipes

---

## 📊 Streams : Concepts

### File Descriptors (Descripteurs Fichiers)

**Stream** = Flux de données (entrée/sortie programme)

**3 streams standards** Linux :

| Stream | Nom | Descriptor | Rôle | Symbole |
|--------|-----|------------|------|---------|
| **stdin** | Standard Input | 0 | **Entrée** programme | < |
| **stdout** | Standard Output | 1 | **Sortie** normale | > |
| **stderr** | Standard Error | 2 | **Sortie** erreurs | 2> |

**Analogie** 🎬 :
- **stdin** = Acteur (reçoit script/instructions)
- **stdout** = Dialogue acteur (sortie normale)
- **stderr** = Erreurs acteur (oubli texte, improvisation)

---

### Flux par Défaut

**Sans redirection** :

```bash
# Programme lit stdin depuis CLAVIER
cat
# (attend saisie utilisateur)
Hello world
# Hello world (écho)
Ctrl+D  # Fin stdin

# Programme écrit stdout vers TERMINAL
echo "Message"
# Message (affiché terminal)

# Programme écrit stderr vers TERMINAL
ls fichier-inexistant
# ls: cannot access 'fichier-inexistant': No such file or directory
```

---

## ➡️ Redirections Basiques

### Redirection stdout : >

**Rôle** : Rediriger sortie standard vers **fichier**

```bash
# Écrire stdout dans fichier (écrase si existe)
echo "Hello" > output.txt

# Vérifier
cat output.txt
# Hello

# Écrasement
echo "World" > output.txt
cat output.txt
# World  ← "Hello" ÉCRASÉ
```

**⚠️ Attention** : `>` **écrase** fichier existant

---

### Redirection stdout : >> (Append)

**Rôle** : **Ajouter** à la fin fichier (pas écraser)

```bash
# Première ligne
echo "Line 1" > file.txt

# Ajouter ligne
echo "Line 2" >> file.txt

# Ajouter encore
echo "Line 3" >> file.txt

# Vérifier
cat file.txt
# Line 1
# Line 2
# Line 3
```

---

### Redirection stdin : <

**Rôle** : Lire entrée depuis **fichier** (pas clavier)

```bash
# Créer fichier input
cat > input.txt
Line 1
Line 2
Line 3
Ctrl+D

# Lire stdin depuis fichier
wc -l < input.txt
# 3

# Équivalent (mais différent techniquement)
wc -l input.txt
# 3 input.txt  ← Affiche nom fichier
```

**Différence** :

```bash
# Avec < : Programme reçoit CONTENU (stdin)
wc -l < file.txt
# 3

# Sans < : Programme reçoit NOM FICHIER (argument)
wc -l file.txt
# 3 file.txt
```

---

### Redirection stderr : 2>

**Rôle** : Rediriger erreurs vers fichier

```bash
# Commande avec erreur
ls fichier-inexistant
# ls: cannot access 'fichier-inexistant': No such file or directory

# Rediriger stderr vers fichier
ls fichier-inexistant 2> errors.log

# Terminal : (vide)
# Fichier errors.log contient erreur

cat errors.log
# ls: cannot access 'fichier-inexistant': No such file or directory
```

---

### Combiner stdout et stderr

#### Rediriger séparément

```bash
# stdout vers output.txt, stderr vers errors.log
ls /etc /fichier-inexistant > output.txt 2> errors.log

# output.txt : Liste /etc
# errors.log : Erreur /fichier-inexistant
```

---

#### Rediriger vers même fichier

```bash
# Méthode moderne (recommandée)
ls /etc /fichier-inexistant > all.log 2>&1

# Explication :
# > all.log     → stdout vers all.log
# 2>&1          → stderr vers même destination que stdout (1)

# Syntaxe Bash moderne (plus simple)
ls /etc /fichier-inexistant &> all.log

# OU
ls /etc /fichier-inexistant >& all.log
```

**💡 Ordre important** : `> fichier 2>&1` (pas `2>&1 > fichier`)

---

#### Supprimer sortie : /dev/null

```bash
# Supprimer stdout
ls /etc > /dev/null
# (rien affiché)

# Supprimer stderr
ls /fichier-inexistant 2> /dev/null
# (rien affiché, pas d'erreur visible)

# Supprimer TOUT (stdout + stderr)
ls /etc /fichier-inexistant &> /dev/null
# (silence complet)
```

**Usage** : Scripts silencieux, masquer erreurs attendues

---

## 📖 Here Documents (Heredoc)

### Heredoc : Syntaxe

**Rôle** : Fournir **bloc texte multi-lignes** à stdin

**Syntaxe** :
```bash
commande <<DELIMITER
ligne 1
ligne 2
ligne 3
DELIMITER
```

**Délimiteur** : Mot-clé marquant fin bloc (souvent `EOF`, `END`, `HEREDOC`)

---

### Heredoc : Exemples Basiques

```bash
# Créer fichier multi-lignes
cat <<EOF > config.txt
server=192.168.1.100
port=8080
debug=true
EOF

# Vérifier
cat config.txt
# server=192.168.1.100
# port=8080
# debug=true
```

---

### Heredoc avec Variables

**Par défaut** : Variables **interprétées**

```bash
USER="alice"
HOME_DIR="/home/alice"

cat <<EOF > user-info.txt
Username: $USER
Home: $HOME_DIR
Date: $(date)
EOF

cat user-info.txt
# Username: alice
# Home: /home/alice
# Date: Sun Feb  2 12:00:00 CET 2026
```

---

### Heredoc Littéral (Pas Expansion)

**Syntaxe** : Délimiteur entre **quotes** `'DELIMITER'`

```bash
# Variables PAS interprétées
cat <<'EOF' > script-template.sh
#!/bin/bash
echo "User: $USER"
echo "Home: $HOME"
EOF

cat script-template.sh
# #!/bin/bash
# echo "User: $USER"     ← $USER littéral (pas expandé)
# echo "Home: $HOME"     ← $HOME littéral
```

---

### Heredoc : Cas Pratiques

#### Script SQL

```bash
mysql -u root -p <<EOF
CREATE DATABASE mydb;
USE mydb;
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100)
);
INSERT INTO users VALUES (1, 'Alice', 'alice@example.com');
EOF
```

---

#### Configuration Multi-lignes

```bash
cat <<EOF > /etc/nginx/sites-available/mysite
server {
    listen 80;
    server_name example.com;
    root /var/www/html;
    
    location / {
        try_files \$uri \$uri/ =404;
    }
}
EOF
```

**Note** : `\$uri` échappé (sinon expansion variable bash)

---

#### Email via Sendmail

```bash
sendmail user@example.com <<EOF
Subject: Alert
From: admin@server.com

This is an automated alert message.

Server: $(hostname)
Date: $(date)
EOF
```

---

### Heredoc avec Indentation : <<-

**Syntaxe** : `<<-` supprime **tabulations** début lignes

```bash
# Avec tabulations (plus lisible dans script)
if true; then
    cat <<-EOF
	Line 1
	Line 2
	Line 3
	EOF
fi

# Résultat (tabulations supprimées) :
# Line 1
# Line 2
# Line 3
```

**⚠️ Important** : Fonctionne avec **TABULATIONS** (pas espaces)

---

## 📝 Here Strings (<<<)

### HereString : Syntaxe

**Rôle** : Passer **chaîne unique** directement à stdin

**Syntaxe** :
```bash
commande <<< "chaîne"
```

**Analogie** 📬 :
- **Heredoc** = Lettre plusieurs pages (multi-lignes)
- **HereString** = Post-it unique (une ligne/phrase)

---

### HereString : Exemples

```bash
# Passer chaîne à stdin
wc -w <<< "Hello world from bash"
# 4

# Calculatrice bc
bc <<< "1+2+3+4"
# 10

bc <<< "scale=2; 22/7"
# 3.14

# Lire variables
read var <<< "Hello"
echo $var
# Hello

# Lire plusieurs variables
read a b c <<< "one two three"
echo "a=$a, b=$b, c=$c"
# a=one, b=two, c=three
```

---

### HereString vs Arguments

```bash
# Argument (commande reçoit paramètre)
echo "Hello world"
# Hello world

# HereString (commande reçoit stdin)
cat <<< "Hello world"
# Hello world

# Différence visible avec wc
wc -w "file.txt"          # Compte mots fichier (argument)
wc -w <<< "Hello world"   # Compte mots chaîne (stdin)
# 2
```

---

### HereString : Cas Pratiques

#### Validation Input

```bash
# Tester regex
grep -E '^[0-9]+$' <<< "12345"
# 12345  ← Match

grep -E '^[0-9]+$' <<< "abc"
# (vide) ← Pas match

# Validation email
email="user@example.com"
grep -E '^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$' <<< "$email" && echo "Valid" || echo "Invalid"
# Valid
```

---

#### Conversion Base

```bash
# Décimal → Hexadécimal
printf '%x\n' <<< "255"
# ff

# Calculs
bc <<< "scale=4; sqrt(2)"
# 1.4142
```

---

#### Traitement Chaîne

```bash
# Majuscules
tr 'a-z' 'A-Z' <<< "hello world"
# HELLO WORLD

# Inverser
rev <<< "hello"
# olleh

# Compter caractères
wc -c <<< "Hello"
# 6  ← 5 lettres + \n
```

---

## 🔗 Pipes : |

### Pipe : Concept

**Pipe** `|` = Connecter **stdout** commande 1 vers **stdin** commande 2

**Analogie** 🚰 :
- **Commande 1** = Robinet (source eau)
- **Pipe** = Tuyau
- **Commande 2** = Évier (destination)

**Syntaxe** :
```bash
commande1 | commande2 | commande3
```

**Flux** : `cmd1 stdout` → `cmd2 stdin` → `cmd2 stdout` → `cmd3 stdin`

---

### Pipe : Exemples Basiques

```bash
# Liste fichiers → Compter lignes
ls | wc -l
# 42

# Logs → Filtrer erreurs
cat /var/log/syslog | grep ERROR

# Processus → Filtrer par nom
ps aux | grep apache

# Historique → Chercher commande
history | grep ssh
```

---

### Pipes Multiples

```bash
# 3 commandes chaînées
ls -l | grep ".txt" | wc -l
# 1. ls -l : Liste détaillée
# 2. grep ".txt" : Filtre .txt
# 3. wc -l : Compte lignes

# Analyse logs complexe
cat access.log | cut -d ' ' -f 1 | sort | uniq -c | sort -rn | head -10
# 1. cat : Affiche logs
# 2. cut : Extrait IP (1er champ)
# 3. sort : Trie IPs
# 4. uniq -c : Compte occurrences
# 5. sort -rn : Trie par nombre (décroissant)
# 6. head -10 : Top 10
```

---

### Pipe vs Redirection

```bash
# ❌ ERREUR : > au lieu de |
ls > grep ".txt"
# Crée fichier "grep" et fichier ".txt"

# ✅ CORRECT : | pour chaîner
ls | grep ".txt"
# Filtre résultat ls

# Combinaison pipe + redirection
ls | grep ".txt" > txt-files.list
# 1. ls | grep ".txt" : Filtre
# 2. > txt-files.list : Sauvegarde résultat
```

---

## 📐 column : Formatter Colonnes

### column -t : Table Alignée

**Rôle** : Formatter données **colonnes alignées**

**Syntaxe** :
```bash
column -t fichier
```

**Option -t** : **Table mode** (aligner colonnes automatiquement)

---

### column : Exemples

#### Sans column (brut)

```bash
# Fichier CSV avec espaces
cat data.txt
# Name Age City
# Alice 28 Paris
# Bob 35 London
# Charlie 42 NewYork
```

**Problème** : Colonnes mal alignées (longueurs variables)

---

#### Avec column -t

```bash
# Formatter colonnes
column -t data.txt
# Name     Age  City
# Alice    28   Paris
# Bob      35   London
# Charlie  42   NewYork
```

**Résultat** : Colonnes **alignées automatiquement**

---

### column -s : Délimiteur Custom

**Option -s** : Spécifier **délimiteur** colonnes

```bash
# Fichier CSV (virgules)
cat users.csv
# john,doe,john@example.com
# alice,smith,alice@example.com
# bob,jones,bob@example.com

# Formatter avec délimiteur virgule
column -t -s ',' users.csv
# john   doe    john@example.com
# alice  smith  alice@example.com
# bob    jones  bob@example.com
```

---

### column : Cas Pratiques

#### Formater df

```bash
# Sans column
df -h
# Filesystem      Size  Used Avail Use% Mounted on
# /dev/sda1       100G   45G   50G  48% /
# /dev/sdb1       500G  234G  241G  50% /data

# Avec column (plus propre)
df -h | column -t
# Filesystem  Size  Used  Avail  Use%  Mounted  on
# /dev/sda1   100G  45G   50G    48%   /
# /dev/sdb1   500G  234G  241G   50%   /data
```

---

#### Formater ps

```bash
# Processus formatés
ps aux | head -5 | column -t
# USER  PID  %CPU  %MEM  VSZ    RSS   TTY   STAT  START  TIME  COMMAND
# root  1    0.0   0.2   22560  2456  ?     Ss    Jan01  0:05  /sbin/init
```

---

#### Formater Tableau Custom

```bash
# Créer données
cat <<EOF > employees.txt
Name Department Salary
Alice Engineering 75000
Bob Marketing 65000
Charlie Sales 70000
EOF

# Formatter
column -t employees.txt
# Name     Department    Salary
# Alice    Engineering   75000
# Bob      Marketing     65000
# Charlie  Sales         70000
```

---

## 📋 Antisèche - Redirections et Pipes

### Redirections

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `cmd > file` | Écraser stdout fichier | `ls > list.txt` |
| `cmd >> file` | Ajouter stdout fichier | `echo "log" >> app.log` |
| `cmd < file` | Lire stdin fichier | `wc -l < data.txt` |
| `cmd 2> file` | Écraser stderr fichier | `ls bad 2> errors.log` |
| `cmd 2>> file` | Ajouter stderr fichier | `ls bad 2>> errors.log` |
| `cmd > file 2>&1` | stdout+stderr fichier | `cmd > all.log 2>&1` |
| `cmd &> file` | stdout+stderr (moderne) | `cmd &> all.log` |
| `cmd > /dev/null` | Supprimer stdout | `ls > /dev/null` |
| `cmd 2> /dev/null` | Supprimer stderr | `ls bad 2> /dev/null` |
| `cmd &> /dev/null` | Supprimer tout | `cmd &> /dev/null` |

---

### Heredoc et HereString

| Syntaxe | Je veux... | Exemple |
|---------|-----------|---------|
| `cmd <<EOF` | Heredoc multi-lignes | `cat <<EOF > file` |
| `cmd <<'EOF'` | Heredoc littéral (no expansion) | `cat <<'EOF' > script.sh` |
| `cmd <<-EOF` | Heredoc (supprime tabs) | `cat <<-EOF` (dans if) |
| `cmd <<< "text"` | HereString (une ligne) | `bc <<< "1+2+3"` |
| `cmd <<< "$var"` | HereString variable | `wc -w <<< "$text"` |

---

### Pipes

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `cmd1 \| cmd2` | Chaîner commandes | `ls \| grep txt` |
| `cmd1 \| cmd2 \| cmd3` | Pipe multiple | `ls \| grep txt \| wc -l` |
| `cmd1 \| tee file` | Stdout fichier + terminal | `ls \| tee list.txt` |
| `cmd1 \| tee -a file` | Stdout fichier (append) + terminal | `ls \| tee -a list.txt` |

---

### column

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `column -t file` | Formatter table | `column -t data.txt` |
| `column -t -s ','` | Formatter CSV | `column -t -s ',' users.csv` |
| `cmd \| column -t` | Formatter sortie commande | `df -h \| column -t` |

---

## 🎓 Points Clés pour l'Examen

✅ **3 streams** : stdin (0), stdout (1), stderr (2)  
✅ **> fichier** : Redirige stdout, **écrase** fichier  
✅ **>> fichier** : Redirige stdout, **ajoute** à fichier  
✅ **< fichier** : Redirige stdin depuis fichier  
✅ **2> fichier** : Redirige stderr vers fichier  
✅ **2>&1** : Redirige stderr vers même destination que stdout  
✅ **&> fichier** : Redirige stdout+stderr (syntaxe moderne)  
✅ **/dev/null** : "Trou noir" (supprime sortie)  
✅ **Heredoc <<EOF** : Bloc texte multi-lignes vers stdin  
✅ **Heredoc <<'EOF'** : Littéral (pas expansion variables)  
✅ **Heredoc <<-EOF** : Supprime tabulations début lignes  
✅ **HereString <<<** : Passe chaîne unique à stdin  
✅ **Pipe |** : Connecte stdout cmd1 → stdin cmd2  
✅ **column -t** : Formate colonnes alignées (table)  
✅ **column -s** : Spécifie délimiteur colonnes  
✅ **tee** : Écrit stdout vers fichier ET affiche terminal  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Logging Script Complet

```bash
#!/bin/bash
# Script avec logging stdout + stderr

LOGFILE="/var/log/backup.log"
ERRORLOG="/var/log/backup-errors.log"

# Rediriger stdout et stderr
exec 1> >(tee -a "$LOGFILE")
exec 2> >(tee -a "$ERRORLOG" >&2)

echo "=== Backup started $(date) ==="

# Commandes backup
rsync -av /data/ /backup/ || echo "ERROR: Backup failed"

echo "=== Backup completed $(date) ==="

# Résultat :
# - Terminal : Affiche tout
# - backup.log : stdout
# - backup-errors.log : stderr
```

---

### Scénario 2 : Configuration Serveur avec Heredoc

```bash
#!/bin/bash
# Générer configuration Nginx

DOMAIN="example.com"
ROOT="/var/www/html"

cat <<EOF > /etc/nginx/sites-available/$DOMAIN
server {
    listen 80;
    server_name $DOMAIN www.$DOMAIN;
    root $ROOT;
    
    location / {
        try_files \$uri \$uri/ =404;
    }
    
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
    }
}
EOF

# Activer site
ln -s /etc/nginx/sites-available/$DOMAIN /etc/nginx/sites-enabled/

# Tester config
nginx -t && systemctl reload nginx
```

---

### Scénario 3 : Analyse Logs Avancée

```bash
# Pipeline complet analyse logs Apache
cat /var/log/apache2/access.log | \
  grep -v "bot\|spider" | \                      # Exclure bots
  cut -d ' ' -f 1 | \                            # Extraire IPs
  sort | \                                       # Trier
  uniq -c | \                                    # Compter occurrences
  sort -rn | \                                   # Trier par nombre (desc)
  head -20 | \                                   # Top 20
  column -t > /tmp/top-ips.txt                   # Formatter

# Afficher résultat
cat /tmp/top-ips.txt
#    543  192.168.1.100
#    421  203.0.113.50
#    389  10.0.0.25
```

---

### Scénario 4 : Validation Input Utilisateur

```bash
#!/bin/bash
# Validation email avec HereString

read -p "Enter email: " email

# Valider format
if grep -qE '^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$' <<< "$email"; then
    echo "Valid email: $email"
else
    echo "Invalid email format" >&2
    exit 1
fi

# Test domaine MX (DNS)
domain=$(cut -d '@' -f 2 <<< "$email")
if host -t MX "$domain" &> /dev/null; then
    echo "Domain has MX records"
else
    echo "Warning: No MX records for $domain" >&2
fi
```

---

### Scénario 5 : Rapport Système Formaté

```bash
#!/bin/bash
# Générer rapport système avec column

# Collecter infos
cat <<EOF > /tmp/system-report.txt
Component Value
Hostname $(hostname)
Kernel $(uname -r)
Uptime $(uptime -p | sed 's/up //')
Load $(uptime | awk -F'load average:' '{print $2}')
Memory $(free -h | awk '/Mem:/ {print $3"/"$2}')
Disk $(df -h / | awk 'NR==2 {print $3"/"$2}')
Users $(who | wc -l)
Processes $(ps aux | wc -l)
EOF

# Formatter
echo "=== System Report $(date) ==="
column -t /tmp/system-report.txt
```

---

### Scénario 6 : Backup Base Données avec Heredoc

```bash
#!/bin/bash
# Backup MySQL avec script SQL

DB_NAME="production"
BACKUP_FILE="/backup/db-$(date +%Y%m%d).sql"

# Générer script SQL backup
mysql -u root -p <<EOF
USE $DB_NAME;
LOCK TABLES;

-- Dump database
\! mysqldump -u root -p $DB_NAME > $BACKUP_FILE

UNLOCK TABLES;

-- Optimize tables
OPTIMIZE TABLE users, orders, products;

-- Show status
SELECT COUNT(*) as total_users FROM users;
SELECT COUNT(*) as total_orders FROM orders;
EOF

echo "Backup saved to $BACKUP_FILE"

# Compresser backup
gzip "$BACKUP_FILE"
```

---

### Scénario 7 : Pipeline Traitement CSV

```bash
# Fichier CSV sales.csv
cat sales.csv
# Date,Product,Quantity,Price
# 2026-01-01,Laptop,5,1000
# 2026-01-02,Mouse,20,25
# 2026-01-03,Keyboard,15,75

# Traiter et formatter
cat sales.csv | \
  column -t -s ',' | \
  tee /tmp/formatted-sales.txt
  
# Date        Product   Quantity  Price
# 2026-01-01  Laptop    5         1000
# 2026-01-02  Mouse     20        25
# 2026-01-03  Keyboard  15        75

# Calculer total ventes (Quantity × Price)
tail -n +2 sales.csv | \
  awk -F',' '{total += $3 * $4} END {print "Total Sales: $"total}'
# Total Sales: $6875
```

---

### Scénario 8 : Alertes Multiples Logs

```bash
#!/bin/bash
# Surveiller logs multiples et alerter

# Surveiller en temps réel
tail -f /var/log/syslog /var/log/auth.log 2>&1 | \
  grep --line-buffered -iE 'error|failed|critical' | \
  while read line; do
    # Logger
    echo "$(date) - $line" >> /var/log/alerts.log
    
    # Alerte email si critique
    if grep -qi "critical" <<< "$line"; then
      echo "$line" | mail -s "CRITICAL ALERT" admin@example.com
    fi
  done &

echo "Monitoring started (PID: $!)"
```

---

### Scénario 9 : Comparaison Fichiers avec Redirection

```bash
# Comparer 2 listes utilisateurs
cat <<EOF > old-users.txt
alice
bob
charlie
david
EOF

cat <<EOF > new-users.txt
alice
bob
eve
frank
EOF

# Utilisateurs supprimés (dans old, pas dans new)
comm -23 <(sort old-users.txt) <(sort new-users.txt) > deleted-users.txt
# charlie
# david

# Utilisateurs ajoutés (dans new, pas dans old)
comm -13 <(sort old-users.txt) <(sort new-users.txt) > added-users.txt
# eve
# frank

# Utilisateurs communs
comm -12 <(sort old-users.txt) <(sort new-users.txt) > common-users.txt
# alice
# bob
```

---

### Scénario 10 : Menu Interactif avec Heredoc

```bash
#!/bin/bash
# Menu interactif

while true; do
    cat <<EOF
=================================
    System Administration Menu
=================================
1. Show Disk Usage
2. Show Memory Usage
3. Show Running Processes
4. Backup Data
5. Exit
=================================
EOF

    read -p "Enter choice [1-5]: " choice
    
    case $choice in
        1) df -h | column -t ;;
        2) free -h ;;
        3) ps aux | head -20 | column -t ;;
        4) rsync -av /data/ /backup/ ;;
        5) echo "Goodbye!"; exit 0 ;;
        *) echo "Invalid choice" >&2 ;;
    esac
    
    echo
    read -p "Press Enter to continue..."
done
```

---

## ⚠️ Erreurs Courantes

### Erreur 1 : Ordre Redirections

```bash
# ❌ ERREUR : 2>&1 AVANT >
cmd 2>&1 > file.log
# stderr → terminal (copie stdout actuel = terminal)
# stdout → file.log
# Résultat : Seulement stdout dans fichier

# ✅ CORRECT : > AVANT 2>&1
cmd > file.log 2>&1
# stdout → file.log
# stderr → même destination que stdout (file.log)
# Résultat : stdout + stderr dans fichier

# OU : Syntaxe moderne
cmd &> file.log
```

---

### Erreur 2 : > vs >>

```bash
# ❌ PERTE DONNÉES : > écrase
echo "Line 1" > log.txt
echo "Line 2" > log.txt
cat log.txt
# Line 2  ← Line 1 écrasée !

# ✅ AJOUT : >> append
echo "Line 1" > log.txt
echo "Line 2" >> log.txt
cat log.txt
# Line 1
# Line 2
```

---

### Erreur 3 : Heredoc Délimiteur Indenté

```bash
# ❌ ERREUR : Délimiteur indenté (avec espaces)
cat <<EOF
Line 1
Line 2
    EOF  ← Espaces devant
# bash: warning: here-document delimited by end-of-file (wanted `EOF')

# ✅ CORRECT : Délimiteur début ligne (aucun espace)
cat <<EOF
Line 1
Line 2
EOF

# OU : Utiliser <<- avec TABULATIONS (pas espaces)
cat <<-EOF
	Line 1
	Line 2
	EOF
```

---

### Erreur 4 : Pipe avec Redirection

```bash
# ❌ CONFUSION : > au lieu de |
ls > grep txt
# Crée 2 fichiers : "grep" et "txt"

# ✅ CORRECT : | pour pipe
ls | grep txt
# Filtre résultat ls

# Combiner pipe + redirection
ls | grep txt > results.txt
```

---

### Erreur 5 : HereString Multi-lignes

```bash
# ❌ ERREUR : HereString multi-lignes
wc -l <<< "Line 1
Line 2
Line 3"
# bash: syntax error near unexpected token `Line'

# ✅ SOLUTION : Utiliser Heredoc
wc -l <<EOF
Line 1
Line 2
Line 3
EOF
# 3

# HereString = UNE ligne seulement
wc -w <<< "one two three"
# 3
```

---

### Erreur 6 : Expansion Variables Heredoc

```bash
USER="alice"

# ❌ PROBLÈME : Variables expandées (non voulu)
cat <<EOF > script.sh
#!/bin/bash
echo "User: $USER"
EOF

cat script.sh
# #!/bin/bash
# echo "User: alice"  ← $USER déjà remplacé

# ✅ SOLUTION : Quotes délimiteur
cat <<'EOF' > script.sh
#!/bin/bash
echo "User: $USER"
EOF

cat script.sh
# #!/bin/bash
# echo "User: $USER"  ← $USER littéral
```

---

### Erreur 7 : column Sans Délimiteur

```bash
# Fichier CSV
cat data.csv
# john,25,paris
# alice,30,london

# ❌ Sans -s : Délimiteur = espace (par défaut)
column -t data.csv
# john,25,paris   ← Pas formaté (une colonne)

# ✅ Avec -s ',' : Délimiteur virgule
column -t -s ',' data.csv
# john   25  paris
# alice  30  london
```

---

**🎯 Prochaine étape** : Use Streams, Pipes, and Redirects - Part 2 (tee, process substitution, named pipes)

*Dernière mise à jour: 2 février 2026*
