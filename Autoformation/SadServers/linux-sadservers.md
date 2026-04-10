# Scenario 1 : Analyse de logs serveurs avec awk, sort, uniq, head, tail et pipelines Unix.

## 🎓 Apprentissages Clés

### 1. awk - Text Processing Power Tool

**Philosophie :** Traiter texte **ligne par ligne**, **champ par champ**.

**Syntaxe générale :**
```bash
awk 'PATTERN { ACTION }' file
```

**Champs automatiques :**
- `$0` : Ligne complète
- `$1` : 1er champ
- `$2` : 2ème champ
- `$NF` : Dernier champ
- `NR` : Numéro ligne courante
- `NF` : Nombre champs ligne courante

**Exemples basiques :**
```bash
# Afficher 1ère colonne
awk '{print $1}' file.txt

# Afficher colonnes 1 et 3
awk '{print $1, $3}' file.txt

# Afficher avec séparateur custom
awk '{print $1 ":" $3}' file.txt

# Changer séparateur input
awk -F: '{print $1}' /etc/passwd
# -F: = field separator ":"
```

**Exemples avancés :**
```bash
# Filtrer lignes (afficher si 3ème champ > 100)
awk '$3 > 100' data.txt

# Calculer somme colonne 2
awk '{sum += $2} END {print sum}' numbers.txt

# Compter lignes
awk 'END {print NR}' file.txt

# Afficher lignes 10-20
awk 'NR >= 10 && NR <= 20' file.txt

# Ignorer lignes vides
awk 'NF > 0' file.txt

# Imprimer numéro ligne + contenu
awk '{print NR ": " $0}' file.txt
```

**Cas d'usage logs :**
```bash
# Extraire IPs uniques d'Apache log
awk '{print $1}' access.log | sort -u

# Compter codes HTTP
awk '{print $9}' access.log | sort | uniq -c

# Requêtes entre 10h-11h
awk '$4 ~ /27\/Jun\/2023:10/ {print $0}' access.log

# Taille totale transferts (bytes)
awk '{sum += $10} END {print sum}' access.log
```

---

### 2. sort - Tri puissant

**Options essentielles :**

| Option | Description | Exemple |
|--------|-------------|---------|
| `-n` | Tri **numérique** | `sort -n numbers.txt` |
| `-r` | **Reverse** (décroissant) | `sort -r file.txt` |
| `-k` | Trier par **colonne** | `sort -k2 file.txt` (2ème colonne) |
| `-u` | **Unique** (déduplique) | `sort -u file.txt` |
| `-t` | **Delimiter** custom | `sort -t: -k3 /etc/passwd` |
| `-h` | Tri **human-readable** (1K, 5M, 2G) | `du -h | sort -h` |

**Exemples pratiques :**
```bash
# Trier fichiers par taille (human-readable)
ls -lh | sort -k5 -h

# Trier /etc/passwd par UID (3ème champ, numérique)
sort -t: -k3 -n /etc/passwd

# Top 10 IPs access.log
awk '{print $1}' access.log | sort | uniq -c | sort -nr | head -10

# Trier CSV par 2ème colonne
sort -t, -k2 data.csv
```

**Performance tips :**
```bash
# Fichiers énormes : augmenter buffer
sort -S 2G largefile.txt

# Paralléliser tri (multi-core)
sort --parallel=4 largefile.txt

# Tri temporaire autre disque
sort -T /mnt/fast_ssd largefile.txt
```

---

### 3. uniq - Dédupliquer et compter

**⚠️ Prérequis :** Lignes **DOIVENT être consécutives** (donc trier avant).

**Options utiles :**

| Option | Description | Exemple |
|--------|-------------|---------|
| `-c` | **Count** occurrences | `uniq -c` |
| `-d` | Afficher **duplicates** uniquement | `uniq -d` |
| `-u` | Afficher **unique** uniquement (1 occurrence) | `uniq -u` |
| `-i` | **Ignore case** | `uniq -i` |

**Exemples :**
```bash
# Compter lignes identiques
sort file.txt | uniq -c

# Top 10 lignes plus fréquentes
sort file.txt | uniq -c | sort -nr | head -10

# Afficher duplicates seulement
sort file.txt | uniq -d

# Afficher lignes uniques seulement (non dupliquées)
sort file.txt | uniq -u

# Compter requêtes HTTP par IP
awk '{print $1}' access.log | sort | uniq -c
```

**vs `sort -u` (déduplique sans compter) :**
```bash
# sort -u : déduplique directement
sort -u file.txt  # Équivalent : sort file.txt | uniq

# uniq -c : garde duplicates avec count
sort file.txt | uniq -c
```

---

### 4. Pipelines - Combiner commandes

**Philosophie Unix :** Programs that do one thing well, combined via pipes.

**Principe :**
```bash
cmd1 | cmd2 | cmd3
└─┬─┘   └─┬─┘   └─┬─┘
  │       │       └─ Reçoit output cmd2 (stdin)
  │       └─ Reçoit output cmd1 (stdin)
  └─ Génère output (stdout)
```

**Exemples classiques :**
```bash
# Top 10 processus CPU
ps aux | sort -k3 -nr | head -10

# Compter fichiers répertoire
ls | wc -l

# Trouver fichiers + compter lignes
find . -name "*.py" | xargs wc -l

# Logs erreurs dernière heure
tail -1000 /var/log/syslog | grep ERROR | wc -l
```

**Pipeline log analysis complet :**
```bash
# Analyser access.log Apache

# 1. Top 10 IPs
awk '{print $1}' access.log | sort | uniq -c | sort -nr | head -10

# 2. Top 10 URLs
awk '{print $7}' access.log | sort | uniq -c | sort -nr | head -10

# 3. Codes HTTP distribution
awk '{print $9}' access.log | sort | uniq -c

# 4. Erreurs 404
awk '$9 == 404 {print $7}' access.log | sort | uniq -c | sort -nr

# 5. Traffic par heure
awk '{print substr($4,14,2)}' access.log | sort | uniq -c
# Extrait heure [27/Jun/2023:10:45:23] → "10"

# 6. Bandwidth total (MB)
awk '{sum += $10} END {print sum/1024/1024 " MB"}' access.log
```

---

### 5. head / tail - Extraire lignes

**head - Début fichier :**
```bash
# 10 premières lignes (défaut)
head file.txt

# n premières lignes
head -n 20 file.txt
head -20 file.txt  # Raccourci

# Tout SAUF n dernières lignes
head -n -5 file.txt  # Tout sauf 5 dernières
```

**tail - Fin fichier :**
```bash
# 10 dernières lignes (défaut)
tail file.txt

# n dernières lignes
tail -n 50 file.txt
tail -50 file.txt  # Raccourci

# Tout SAUF n premières lignes
tail -n +10 file.txt  # À partir ligne 10

# Suivre fichier en temps réel
tail -f /var/log/syslog
# -f = follow (mise à jour live)
```

**Combinaisons utiles :**
```bash
# Afficher lignes 100-110
head -110 file.txt | tail -10

# Afficher ligne exacte 50
head -50 file.txt | tail -1
# OU
sed -n '50p' file.txt
```

---

## 📊 Application Professionnelle

### Cas d'usage réels :

**1. Analyse logs attaques DDoS**
```bash
# Identifier IPs suspectes (>1000 requêtes/minute)
awk '{print $1}' access.log | sort | uniq -c | awk '$1 > 1000 {print $2}'

# Bloquer avec firewall
for ip in $(awk '{print $1}' access.log | sort | uniq -c | awk '$1 > 1000 {print $2}'); do
  iptables -A INPUT -s $ip -j DROP
done
```

**2. Monitoring erreurs application**
```bash
# Erreurs 500 dernière heure
tail -10000 access.log | awk '$9 >= 500 {print $7}' | sort | uniq -c | sort -nr

# Alerter si > 100 erreurs
count=$(tail -10000 access.log | awk '$9 >= 500' | wc -l)
if [ $count -gt 100 ]; then
  echo "ALERT: $count erreurs 500 dernière heure" | mail -s "Alert" admin@example.com
fi
```

**3. Analyse performance web**
```bash
# URLs plus lentes (supposant log avec temps réponse)
awk '{print $7, $NF}' access_with_time.log | \
  awk '{sum[$1]+=$2; count[$1]++} END {for (url in sum) print sum[url]/count[url], url}' | \
  sort -nr | head -10
```

**4. Détecter scrapers/bots**
```bash
# User-Agents suspects (>500 requêtes)
awk -F'"' '{print $6}' access.log | sort | uniq -c | sort -nr | head -20

# Bloquer User-Agent spécifique (nginx)
if ($http_user_agent ~* "BadBot") {
  return 403;
}
```

**5. Génération rapports quotidiens**
```bash
#!/bin/bash
# daily-report.sh

LOG="/var/log/nginx/access.log"
DATE=$(date +%Y-%m-%d)
REPORT="/var/reports/report-$DATE.txt"

{
  echo "=== Daily Report $DATE ==="
  echo
  echo "Top 10 IPs:"
  awk '{print $1}' $LOG | sort | uniq -c | sort -nr | head -10
  echo
  echo "Top 10 URLs:"
  awk '{print $7}' $LOG | sort | uniq -c | sort -nr | head -10
  echo
  echo "HTTP Status Codes:"
  awk '{print $9}' $LOG | sort | uniq -c | sort -nr
  echo
  echo "Total Requests: $(wc -l < $LOG)"
  echo "Unique IPs: $(awk '{print $1}' $LOG | sort -u | wc -l)"
} > $REPORT

# Envoyer par email
mail -s "Daily Report $DATE" admin@example.com < $REPORT
```

---

## � Commandes à retenir

```bash
# Compter IPs uniques dans logs
awk '{print $1}' access.log | sort -u | wc -l

# Top 10 IPs les plus actives
awk '{print $1}' access.log | sort | uniq -c | sort -rn | head -10

# Pipeline awk :
# awk '{print $1}' : extraire 1ère colonne (IP)
# sort -u : trier + unique
# wc -l : compter lignes

# Alternative cut
cut -d' ' -f1 access.log | sort -u | wc -l
```

---

**Tags :** #Linux #Awk #LogAnalysis #TextProcessing #SadServers

**Aucune difficulté majeure :** Scenario straightforward pour pratiquant pipelines Unix.

**Pièges potentiels débutants :**

1. **Oublier `sort` avant `uniq -c`**
   ```bash
   # INCORRECT
   awk '{print $1}' access.log | uniq -c
   # Compte mal (lignes non consécutives)

   # CORRECT
   awk '{print $1}' access.log | sort | uniq -c
   ```

2. **Tri alphabétique au lieu de numérique**
   ```bash
   # INCORRECT
   uniq -c | sort -r
   # 9 192.168.1.10
   # 89 10.0.0.5  ← 89 < 9 alphabétiquement

   # CORRECT
   uniq -c | sort -nr  # -n = numeric
   ```

3. **Écraser fichier au lieu d'append**
   ```bash
   # >> = append (ajoute)
   echo "data" >> file.txt

   # > = overwrite (écrase)
   echo "data" > file.txt
   ```

---

## 🔗 Ressources

**Documentation :**
- `man awk` : Pattern scanning and processing
- `man sort` : Sort lines of text files
- `man uniq` : Report or omit repeated lines
- `man head` / `man tail` : Output file parts

**Guides avancés :**
- [AWK One-Liners Explained](https://catonmat.net/awk-one-liners-explained-part-one)
- [Bash Redirections Cheat Sheet](https://catonmat.net/bash-one-liners-explained-part-three)

**Outils alternatifs :**
- **GoAccess** : Real-time web log analyzer (terminal + HTML)
- **Apache Lounge** : Log analysis GUI
- **AWStats** : Advanced web statistics

# Scenario 2 : Maîtrise de grep, regex, head, tail et investigation méthodique pour extraire des informations spécifiques dans des fichiers texte.


### 1. grep - Recherche Texte Puissante

**Syntaxe générale :**
```bash
grep [OPTIONS] PATTERN [FILES]
```

**Options essentielles :**

| Option | Description | Exemple |
|--------|-------------|---------|
| `-r` | **Recursive** (répertoires) | `grep -r "error" /var/log` |
| `-i` | **Ignore case** | `grep -i "error" log.txt` |
| `-v` | **Invert** (lignes ne matchant PAS) | `grep -v "^#" config.conf` |
| `-n` | **Line number** | `grep -n "TODO" code.py` |
| `-c` | **Count** matches | `grep -c "ERROR" syslog` |
| `-l` | **List filenames** only | `grep -rl "password" /etc` |
| `-A n` | **After** context (n lignes après) | `grep -A 3 "error" log.txt` |
| `-B n` | **Before** context (n lignes avant) | `grep -B 2 "error" log.txt` |
| `-C n` | **Context** (n lignes avant+après) | `grep -C 5 "error" log.txt` |
| `-E` | **Extended regex** (ERE) | `grep -E "foo|bar" file.txt` |
| `-P` | **Perl regex** (PCRE) | `grep -P "\d{3}-\d{4}" phones.txt` |

**Exemples pratiques :**
```bash
# Trouver tous fichiers contenant "password"
grep -rl "password" /etc/

# Erreurs Apache dernière heure (avec contexte)
grep -A 5 -B 2 "error" /var/log/apache2/error.log

# Compter occurrences
grep -c "ERROR" syslog  # Nombre lignes
grep -o "ERROR" syslog | wc -l  # Nombre total occurrences (multiple par ligne)

# Ignorer commentaires config
grep -v "^#" /etc/ssh/sshd_config | grep -v "^$"
# ^# = ligne commence par #
# ^$ = ligne vide

# Chercher IP address
grep -E "[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}" access.log
```

---

### 2. Regex - Extended Regular Expressions

**Métacaractères de base :**

| Pattern | Signification | Exemple | Match |
|---------|---------------|---------|-------|
| `.` | N'importe quel caractère | `a.c` | "abc", "a3c", "a c" |
| `*` | 0 ou plus répétitions | `ab*c` | "ac", "abc", "abbbbc" |
| `+` | 1 ou plus répétitions | `ab+c` | "abc", "abbbbc" (pas "ac") |
| `?` | 0 ou 1 occurrence (optionnel) | `colou?r` | "color", "colour" |
| `^` | Début ligne | `^Error` | Lignes commençant par "Error" |
| `$` | Fin ligne | `done$` | Lignes finissant par "done" |
| `[]` | Classe caractères | `[aeiou]` | Voyelles |
| `[^]` | Négation classe | `[^0-9]` | Non-chiffres |
| `\|` | Alternation (OU) | `cat\|dog` | "cat" ou "dog" |
| `()` | Groupement | `(ab)+` | "ab", "abab", "ababab" |

**Quantifiers :**
- `{n}` : Exactement n fois → `a{3}` = "aaa"
- `{n,}` : Au moins n fois → `a{2,}` = "aa", "aaa", "aaaa"...
- `{n,m}` : Entre n et m fois → `a{2,4}` = "aa", "aaa", "aaaa"

**Exemples scenario :**
```bash
# Plaque L337...9 (solution scenario)
grep -E "L337.*9" vehicles
# L337 = littéral
# .* = n'importe quoi au milieu
# 9 = fin

# Variantes possibles
grep -E "L337[A-Z0-9]{2}9" vehicles  # Exactement 2 caractères alphanumériques

# Email validation
grep -E "^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}$" emails.txt

# Numéro téléphone (format 123-456-7890)
grep -E "[0-9]{3}-[0-9]{3}-[0-9]{4}" contacts.txt

# Trouver IPs privées (192.168.x.x)
grep -E "192\.168\.[0-9]{1,3}\.[0-9]{1,3}" network.log
# \. = échapper le point (sinon . = n'importe quel caractère)
```

**grep vs grep -E vs grep -P :**
```bash
# Basic regex (BRE) - défaut grep
grep "foo\|bar" file.txt  # | échappé

# Extended regex (ERE) - grep -E ou egrep
grep -E "foo|bar" file.txt  # | non échappé

# Perl regex (PCRE) - grep -P (plus puissant)
grep -P "\d{3}-\d{4}" file.txt  # \d = chiffre
grep -P "(?<=@)[a-z]+" emails.txt  # Lookahead/lookbehind
```

---

### 3. head + tail - Extraction Lignes Spécifiques

**Lire ligne exacte N (technique combo) :**
```bash
# Ligne 179 (solution scenario)
head -n 179 file.txt | tail -n 1
```

**Décomposition :**
1. `head -n 179` : Extraire lignes 1-179
2. `tail -n 1` : Garder dernière = ligne 179

**Autres techniques ligne spécifique :**
```bash
# sed (plus direct)
sed -n '179p' file.txt
# -n = quiet mode
# 179p = print ligne 179

# awk
awk 'NR==179' file.txt

# Ligne range (100-110)
sed -n '100,110p' file.txt
awk 'NR>=100 && NR<=110' file.txt
head -110 file.txt | tail -11
```

**Comparaison performance (fichiers énormes) :**
```bash
# head+tail : Lit tout jusqu'à ligne N (lent si N grand)
time head -n 1000000 hugefile.txt | tail -n 1
# real: 5.2s

# sed : Peut stopper après ligne cible (optimisé)
time sed -n '1000000p;1000000q' hugefile.txt
# real: 4.8s

# awk : Lit tout (pas optimisé arrêt)
time awk 'NR==1000000 {print; exit}' hugefile.txt
# real: 5.0s
```

**Astuce grandes fichiers :**
```bash
# sed avec quit après ligne
sed -n '179p;179q' file.txt  # Stop après ligne 179 (fast)
```

---

### 4. Investigation Méthodique - Approche Déductive

**Workflow résolution :**

```
1. COLLECTE INDICES
   ↓
2. IDENTIFIER TÉMOINS/SOURCES
   ↓
3. RECOUPER INFORMATIONS
   ↓
4. ÉLIMINER SUSPECTS (process of elimination)
   ↓
5. VÉRIFIER HYPOTHÈSE
   ↓
6. VALIDATION
```

**Application scenario :**

**1. Collecte indices :**
- `grep -r "CLUE"` → 3 indices (profil, memberships, témoin)

**2. Identifier témoins :**
- `grep "Annabel" people` → 2 Annabel
- `head+tail` interrogation → 1 témoin pertinent (Annabel Church)

**3. Recouper informations :**
- Témoin → description voiture (Honda Blue L337...9)
- `grep -E "L337.*9" vehicles` → 3 suspects

**4. Éliminer suspects :**
- Jacqui Maher → ❌ femme (indice = male)
- Jeremy Bowers → ❌ 2 memberships (indice = 4)
- Joe Germuska → ✅ Tous critères matchent

**5. Vérifier hypothèse :**
- `grep -r "Joe Germuska" memberships` → 4 clubs confirmés

**6. Validation :**
- `echo "Joe Germuska" > solution`
- `md5sum solution` → Hash correct ✅

---

### 5. Contexte grep (-A, -B, -C)

**Pourquoi contexte utile ?**

Souvent l'information pertinente est **autour** du match, pas dans la ligne exacte.

**Exemple scenario :**
```bash
grep -E "L337.*9" -A 5 mystery/vehicles
```

**Sans `-A 5` :**
```
License Plate L337DV9
```

**Avec `-A 5` :**
```
License Plate L337DV9
Make: Honda
Color: Blue
Owner: Joe Germuska
Height: 6'2"
Weight: 164 lbs
```

**Cas d'usage réels :**

**1. Log errors avec stack trace :**
```bash
grep -A 10 "Exception" app.log
# Exception in thread "main"
#   at com.example.App.main(App.java:42)
#   at java.base/jdk.internal.reflect.NativeMethodAccessorImpl...
#   ...
```

**2. Config avec commentaires :**
```bash
grep -B 2 "PermitRootLogin" /etc/ssh/sshd_config
# Authentication:
# LoginGraceTime 2m
PermitRootLogin no
```

**3. Identifier patterns avant/après :**
```bash
# Requêtes lentes (>1s) avec URL
grep -C 3 "query_time: [2-9]\." mysql-slow.log
```

---

## 📊 Application Professionnelle

### Cas d'usage réels :

**1. Incident Response - Analyse Intrusion**

**Scenario :** Détection connexion SSH suspecte.

```bash
# 1. Identifier IP attaquant
grep "Failed password" /var/log/auth.log | \
  awk '{print $(NF-3)}' | sort | uniq -c | sort -nr | head -1
# 342 192.168.1.100

# 2. Timeline attaque
grep "192.168.1.100" /var/log/auth.log | \
  grep -E "Failed password|Accepted password"
# Jan  5 10:23:15 Failed password
# Jan  5 10:23:18 Failed password
# ...
# Jan  5 10:45:32 Accepted password  ← Breach!

# 3. Vérifier commandes exécutées
grep "192.168.1.100" /var/log/auth.log -A 20 | grep "sudo"

# 4. Bloquer IP
iptables -A INPUT -s 192.168.1.100 -j DROP
```

---

**2. Troubleshooting Application - Error Correlation**

**Scenario :** Application crash, trouver root cause.

```bash
# 1. Identifier timestamp premier error
grep -n "ERROR" app.log | head -1
# 12450:2026-01-05 14:32:15 ERROR Connection refused

# 2. Contexte avant crash (20 lignes avant)
grep -B 20 "ERROR" app.log | head -21

# 3. Chercher pattern récurrent
grep -E "ERROR|WARN" app.log | awk '{print $4}' | sort | uniq -c
# 342 OutOfMemoryError
# 89 ConnectionTimeout
# 12 NullPointerException

# 4. Analyser OutOfMemoryError
grep "OutOfMemoryError" -C 5 app.log | less
```

---

**3. Security Audit - Compliance Check**

**Scenario :** Vérifier config serveurs conforme CIS Benchmarks.

```bash
# 1. SSH hardening checks
grep "^PermitRootLogin" /etc/ssh/sshd_config
grep "^PasswordAuthentication" /etc/ssh/sshd_config
grep "^PermitEmptyPasswords" /etc/ssh/sshd_config

# 2. Firewall rules
iptables -L -n | grep -E "DROP|REJECT"

# 3. Accounts sans password
grep -E '^[^:]+:[^!*]' /etc/shadow

# 4. SUID binaries (potentiels vecteurs attaque)
find / -perm -4000 -ls 2>/dev/null | grep -v "/snap/"
```

---

**4. Data Mining - Business Intelligence**

**Scenario :** Analyser logs e-commerce trouver patterns clients.

```bash
# 1. Top produits consultés
grep "product_view" access.log | \
  awk -F'product_id=' '{print $2}' | \
  cut -d'&' -f1 | sort | uniq -c | sort -nr | head -10

# 2. Conversion rate par source traffic
grep "purchase" access.log | \
  grep -oP 'source=\K[^&]+' | sort | uniq -c

# 3. Identifier bots (patterns suspects)
awk '{print $1, $12}' access.log | \
  grep -v "Mozilla" | \
  awk '{print $1}' | sort | uniq -c | sort -nr
```

---

**5. Code Review - Find Security Issues**

**Scenario :** Chercher vulnérabilités codebase.

```bash
# 1. Hardcoded passwords
grep -rn "password\s*=\s*['\"]" /var/www/html --include="*.php"

# 2. SQL injection risks
grep -rn "mysql_query.*\$_" /var/www/html --include="*.php"

# 3. Unescaped user input
grep -rn "echo.*\$_GET\|echo.*\$_POST" /var/www/html --include="*.php"

# 4. Debug code oublié
grep -rn "var_dump\|print_r\|console.log" /var/www/html
```

---

## 💡 Difficultés Rencontrées

**1. Optimiser grep avec regex**

**Problème initial :** Syntaxe regex Extended vs Basic confuse.

**Solution :**
- Utiliser `-E` pour regex lisibles (pas besoin échapper `|`, `+`, `?`)
- Tester regex avec [regex101.com](https://regex101.com) avant grep

**Exemple :**
```bash
# Confus (BRE)
grep "L337\(.\)*9" vehicles  # Échapper parenthèses

# Clair (ERE)
grep -E "L337.*9" vehicles
```

---

**2. Se rappeler syntaxe grep contexte**

**Problème :** Oublier `-A` (After) vs `-B` (Before).

**Mnémotechnique :**
- **A**fter = **A**près (alphabétique)
- **B**efore = **B**efore (idem)
- **C**ontext = **C**ombine both (A+B)

**Vérification rapide :**
```bash
grep --help | grep -E "^\s+-[ABC]"
# -A NUM : print NUM lines after match
# -B NUM : print NUM lines before match
# -C NUM : print NUM lines of context
```

---

**3. Extraction ligne spécifique (head+tail)**

**Première tentative (incorrecte) :**
```bash
# Ligne 179 (wrong)
tail -n 179 file.txt  # Donne 179 DERNIÈRES lignes, pas ligne 179
```

**Correction :**
```bash
# Ligne 179 (correct)
head -n 179 file.txt | tail -n 1  # 179 premières → dernière = ligne 179
```

**Alternative mémorisée :**
```bash
sed -n '179p' file.txt  # Plus direct
```

---

## 🔗 Ressources

**Documentation :**
- `man grep` : Pattern searching
- `man regex` : POSIX regular expressions
- `man head` / `man tail` : File output parts

**Regex Testers :**
- [regex101.com](https://regex101.com) : Tester/debugger regex (visual explanation)
- [regexr.com](https://regexr.com) : Cheatsheet interactive
- [RegexOne](https://regexone.com) : Tutorial interactif

**Command Line Murders :**
- [Original Game](https://github.com/veltman/clmystery) : Version originale GitHub
- [Solutions Walkthrough](https://github.com/veltman/clmystery/blob/master/solution) : Approches alternatives

**Grep Advanced :**
- [GNU Grep Manual](https://www.gnu.org/software/grep/manual/grep.html)
- [Grep Tutorial (Baeldung)](https://www.baeldung.com/linux/grep-command)



---

## 📌 Concepts - Port Knocking

**Port Knocking** : Technique de sécurité réseau masquant des services en les fermant par défaut, débloqués uniquement après une "séquence de frappe" sur des ports spécifiques.

**Fonctionnement** :
1. Service (ex: SSH, HTTP) fermé par firewall
2. Daemon `knockd` surveille tentatives connexion sur ports spécifiques
3. Client frappe séquence de ports : `knock server 1234 5678 9012`
4. Si séquence correcte → firewall ouvre port service temporairement
5. Client se connecte au service
6. Après timeout → firewall referme port

**Avantages** :
- Masque services critiques des scans réseau (nmap ne voit rien)
- Protection contre bruteforce (port "invisible" par défaut)
- Couche sécurité supplémentaire avant authentification

**Commandes knock** :

| Commande | Usage |
|----------|-------|
| `knock HOST PORT` | Frappe port TCP |
| `knock HOST -u PORT` | Frappe port UDP |
| `knock HOST PORT1 PORT2 PORT3` | Séquence de ports |
| `knock HOST PORT:tcp PORT:udp` | Mélange TCP/UDP |

**Exemple configuration knockd** (`/etc/knockd.conf`) :
```
[openSSH]
sequence = 1234,5678,9012
seq_timeout = 10
command = /sbin/iptables -A INPUT -s %IP% -p tcp --dport 22 -j ACCEPT
tcpflags = syn

[closeSSH]
sequence = 9012,5678,1234
seq_timeout = 10
command = /sbin/iptables -D INPUT -s %IP% -p tcp --dport 22 -j ACCEPT
tcpflags = syn
```

**Use case professionnel** :
- SSH sur serveur public (éviter scans/bruteforce)
- Services internes accessibles uniquement après authentification "knock"
- Combinaison avec fail2ban pour sécurité renforcée

---



---

## 📌 Concepts - awk

**awk** : Langage de traitement et d'analyse de texte ligne par ligne, extrêmement puissant pour manipulation de données tabulaires.

### Syntaxe de base

```bash
awk 'pattern {action}' file
```

- **pattern** : Condition pour exécuter action (optionnel, défaut = toutes lignes)
- **action** : Commandes à exécuter (optionnel, défaut = print ligne)

### Variables spéciales

| Variable | Description |
|----------|-------------|
| `$0` | Ligne entière |
| `$1, $2, $n` | Colonnes 1, 2, n (séparateur = espace/tab par défaut) |
| `NF` | Number of Fields (nombre de colonnes) |
| `NR` | Number of Record (numéro de ligne) |
| `FS` | Field Separator (séparateur de champs, défaut espace) |
| `OFS` | Output Field Separator (séparateur sortie) |

### Blocs spéciaux

| Bloc | Exécution |
|------|-----------|
| `BEGIN { ... }` | Avant traitement des lignes (initialisation) |
| `{ ... }` | Pour chaque ligne du fichier |
| `END { ... }` | Après traitement de toutes les lignes (finalisation) |

### Exemples pratiques

**Afficher colonne spécifique** :
```bash
awk '{print $2}' file.txt          # Colonne 2
awk '{print $1, $3}' file.txt      # Colonnes 1 et 3
```

**Calculer somme** :
```bash
awk '{sum+=$2} END {print sum}' file.txt
```

**Calculer moyenne** :
```bash
awk '{sum+=$2; count++} END {print sum/count}' file.txt
```

**Filtrer lignes** :
```bash
awk '$3 > 50' file.txt                    # Lignes où colonne 3 > 50
awk '/error/ {print $0}' file.txt         # Lignes contenant "error"
awk 'NR > 1' file.txt                     # Ignorer première ligne (header)
```

**Formater sortie** :
```bash
awk '{printf "%-10s %5d\n", $1, $2}' file.txt
# %-10s : chaîne alignée gauche, largeur 10
# %5d : entier aligné droite, largeur 5
```

**Changer séparateur** :
```bash
awk -F: '{print $1}' /etc/passwd    # Séparateur = :
awk -F, '{print $2}' data.csv       # Séparateur = ,
```

**Conditions multiples** :
```bash
awk '$3 > 50 && $4 < 100' file.txt
awk '$2 == "active" || $2 == "pending"' file.txt
```

**Variables utilisateur** :
```bash
awk '{total+=$2} END {avg=total/NR; print "Average:", avg}' file.txt
```

### Opérateurs awk

**Arithmétiques** : `+`, `-`, `*`, `/`, `%`, `^` (puissance)  
**Comparaison** : `==`, `!=`, `<`, `>`, `<=`, `>=`  
**Logiques** : `&&` (AND), `||` (OR), `!` (NOT)  
**String** : Concaténation (espace), `~` (match regex), `!~` (not match)

### Use cases professionnels

**Analyser logs Apache** :
```bash
awk '$9 == 404 {print $7}' access.log    # URLs avec erreur 404
awk '{sum+=$10} END {print sum/1024/1024 " MB"}' access.log    # Bande passante totale
```

**Traiter fichiers CSV** :
```bash
awk -F, 'NR>1 {sum+=$3} END {print sum}' sales.csv    # Somme colonne 3 (skip header)
```

**Statistiques système** :
```bash
ps aux | awk '{sum+=$4} END {print "Total Memory:", sum "%"}'    # Mémoire totale processus
```

**Rapport formaté** :
```bash
awk 'BEGIN {print "User\t\tUID"} {printf "%-15s %5d\n", $1, $3}' /etc/passwd
```

---
