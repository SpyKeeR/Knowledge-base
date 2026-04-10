# 🕐 Automatisation des Tâches d'Administration Linux

## 📋 Informations du Module

- **Examen** : LPIC-1 102
- **Objectif** : 107.2 - Automate system administration tasks by scheduling jobs
- **Poids** : 4 points
- **Objectif** : Maîtriser la planification de tâches avec cron, at et systemd timers

---

## 🎯 Vue d'Ensemble des Outils de Planification

Linux offre plusieurs mécanismes pour automatiser l'exécution de tâches :

| Outil | Type | Usage Principal | Récurrence |
|-------|------|-----------------|------------|
| **cron** | Daemon | Tâches récurrentes régulières | Oui (minutes, heures, jours...) |
| **at** | Daemon | Tâches ponctuelles (one-shot) | Non (une seule fois) |
| **systemd timers** | Systemd | Tâches récurrentes modernes | Oui (très flexible) |
| **systemd-run** | Systemd | Exécution temporaire rapide | Optionnel |
| **anacron** | Daemon | Tâches périodiques (machines non 24/7) | Oui (mais rattrapage) |

**Choix selon le besoin :**
```
┌─────────────────────────────────────────────────────────┐
│  Besoin                          → Solution             │
├─────────────────────────────────────────────────────────┤
│  Backup quotidien à 2h du matin  → cron / systemd timer │
│  Script une seule fois demain    → at                   │
│  Tâche dans 2 heures ponctuelle  → at / systemd-run     │
│  Service avec dépendances        → systemd timer        │
│  Laptop non allumé 24/7          → anacron              │
│  Intégration systemd complète    → systemd timer        │
└─────────────────────────────────────────────────────────┘
```

---

## 🔁 CRON - Tâches Récurrentes

### 📅 Principe de Cron

**Cron** : Daemon (`crond` ou `cron`) exécutant des commandes à intervalles réguliers définis.

**Architecture :**
```
┌──────────────────────────────────────────────────────┐
│  crond (daemon)                                      │
│  - Vérifie les crontabs chaque minute               │
│  - Exécute les commandes selon planning              │
└──────────────┬───────────────────────────────────────┘
               │
    ┌──────────┴──────────┬──────────────┬─────────────┐
    │                     │              │             │
┌───▼─────────┐  ┌────────▼──────┐  ┌───▼───────┐  ┌──▼────────┐
│ User crons  │  │ System cron   │  │ /etc/cron.d│  │ Cron dirs │
│ /var/spool/ │  │ /etc/crontab  │  │ (packages) │  │ .hourly   │
│ cron/crontabs│  │               │  │            │  │ .daily    │
│             │  │               │  │            │  │ .weekly   │
└─────────────┘  └───────────────┘  └────────────┘  │ .monthly  │
                                                     └───────────┘
```

### 📝 Format Crontab (5 champs temporels)

**Syntaxe standard :**
```
┌───────────── minute (0 - 59)
│ ┌───────────── heure (0 - 23)
│ │ ┌───────────── jour du mois (1 - 31)
│ │ │ ┌───────────── mois (1 - 12)
│ │ │ │ ┌───────────── jour de la semaine (0 - 7) (0 et 7 = dimanche)
│ │ │ │ │
│ │ │ │ │
* * * * * commande à exécuter
```

**Caractères spéciaux :**

| Symbole | Signification | Exemple |
|---------|---------------|---------|
| `*` | Toutes les valeurs | `* * * * *` = chaque minute |
| `,` | Énumération | `0 8,12,18 * * *` = 8h, 12h et 18h |
| `-` | Plage | `0 9-17 * * *` = de 9h à 17h |
| `/` | Intervalle (step) | `*/15 * * * *` = toutes les 15 min |
| `0-23/2` | Combinaison | Toutes les 2 heures |

**Exemples pratiques :**
```bash
# Tous les jours à 2h30 du matin
30 2 * * * /usr/local/bin/backup.sh

# Toutes les 15 minutes
*/15 * * * * /usr/bin/check_disk.sh

# Chaque lundi à 9h
0 9 * * 1 /usr/local/bin/weekly_report.sh

# Premier jour de chaque mois à minuit
0 0 1 * * /usr/local/bin/monthly_cleanup.sh

# Tous les jours ouvrés (lundi-vendredi) à 8h
0 8 * * 1-5 /usr/local/bin/morning_task.sh

# Samedi et Dimanche à 10h
0 10 * * 6,0 /usr/local/bin/weekend_task.sh

# Toutes les heures impaires
0 1-23/2 * * * /usr/bin/hourly_odd.sh

# Toutes les 5 minutes entre 9h et 17h du lundi au vendredi
*/5 9-17 * * 1-5 /usr/bin/business_hours.sh

# Chaque trimestre (janvier, avril, juillet, octobre) le 1er à minuit
0 0 1 1,4,7,10 * /usr/local/bin/quarterly.sh
```

### 🏷️ Raccourcis Spéciaux (@)

**Cron supporte des raccourcis lisibles :**

| Raccourci | Équivalent | Description |
|-----------|------------|-------------|
| `@reboot` | - | Au démarrage du système |
| `@yearly` | `0 0 1 1 *` | Une fois par an (1er janvier à minuit) |
| `@annually` | `0 0 1 1 *` | Identique à @yearly |
| `@monthly` | `0 0 1 * *` | Une fois par mois (1er du mois à minuit) |
| `@weekly` | `0 0 * * 0` | Une fois par semaine (dimanche à minuit) |
| `@daily` | `0 0 * * *` | Une fois par jour (minuit) |
| `@midnight` | `0 0 * * *` | Identique à @daily |
| `@hourly` | `0 * * * *` | Toutes les heures |

**Exemples avec raccourcis :**
```bash
# Backup quotidien à minuit
@daily /usr/local/bin/daily_backup.sh

# Nettoyage hebdomadaire
@weekly /usr/local/bin/clean_logs.sh

# Script au démarrage
@reboot /usr/local/bin/startup_tasks.sh

# Mise à jour mensuelle
@monthly apt update && apt upgrade -y

# Vérification horaire
@hourly /usr/bin/check_services.sh
```

### 👤 Crontab Utilisateur

**Chaque utilisateur dispose de sa propre crontab.**

#### Commandes de Gestion

```bash
# Éditer la crontab de l'utilisateur courant
crontab -e
# Ouvre l'éditeur (EDITOR ou vi par défaut)

# Lister la crontab actuelle
crontab -l

# Supprimer sa crontab (avec confirmation)
crontab -r

# Supprimer sa crontab (sans confirmation)
crontab -r -i

# Éditer la crontab d'un autre utilisateur (root seulement)
crontab -u alice -e

# Lister la crontab d'un autre utilisateur
crontab -u bob -l

# Charger une crontab depuis un fichier
crontab mon_fichier_cron.txt

# Afficher la crontab sans éditeur (utile pour backup)
crontab -l > backup_crontab_$(date +%Y%m%d).txt
```

#### Emplacement de Stockage

**Les crontabs utilisateurs sont stockées dans :**
```bash
# Debian/Ubuntu
/var/spool/cron/crontabs/username

# RedHat/CentOS/Fedora
/var/spool/cron/username

# Exemple
ls -la /var/spool/cron/crontabs/
# -rw------- 1 alice crontab  256 Feb 11 10:30 alice
# -rw------- 1 bob   crontab  512 Feb 10 15:20 bob
```

⚠️ **Ne jamais éditer ces fichiers directement ! Toujours utiliser `crontab -e`.**

**Pourquoi ?**
- `crontab` vérifie la syntaxe
- Permissions correctes automatiques
- Notifications au daemon crond

#### Exemple de Crontab Utilisateur

```bash
# Édition
crontab -e

# Contenu du fichier
# Backup personnel tous les jours à 23h
0 23 * * * /home/alice/scripts/personal_backup.sh

# Vérification espace disque toutes les heures
0 * * * * df -h > /home/alice/logs/disk_usage_$(date +\%Y\%m\%d_\%H).log

# Nettoyage des fichiers temporaires chaque dimanche à 3h
0 3 * * 0 find /home/alice/tmp -type f -mtime +7 -delete

# Rappel hebdomadaire le lundi à 9h
0 9 * * 1 echo "Réunion hebdo aujourd'hui" | mail -s "Rappel" alice@localhost

# Synchronisation avec serveur distant toutes les 30 minutes
*/30 * * * * rsync -avz /home/alice/photos/ remote:/backup/photos/

# Au redémarrage : lancer un monitoring
@reboot /home/alice/scripts/start_monitoring.sh

# Sauvegarder après édition
# Ctrl + O (Write Out) puis Ctrl + X (Exit) dans nano
# :wq dans vim
```

### 🖥️ Crontab Système - `/etc/crontab`

**Le fichier `/etc/crontab` est la crontab système globale.**

**Différence majeure : champ utilisateur supplémentaire** (6 champs au lieu de 5).

#### Format `/etc/crontab`

```
┌───────────── minute (0 - 59)
│ ┌───────────── heure (0 - 23)
│ │ ┌───────────── jour du mois (1 - 31)
│ │ │ ┌───────────── mois (1 - 12)
│ │ │ │ ┌───────────── jour de la semaine (0 - 7)
│ │ │ │ │
│ │ │ │ │  ┌────────── UTILISATEUR
│ │ │ │ │  │
* * * * * user commande
```

#### Variables d'Environnement

**`/etc/crontab` peut définir des variables d'environnement :**

```bash
# Exemple de /etc/crontab
SHELL=/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=admin@example.com
HOME=/

# m h dom mon dow user  command
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )

# Tâches personnalisées
0  2    * * *   root    /usr/local/bin/system_backup.sh
30 3    * * 0   www-data /var/www/scripts/cache_cleanup.sh
*/10 *  * * *   monitor /usr/local/bin/check_resources.sh
0  0    1 * *   root    /usr/local/bin/monthly_report.sh >> /var/log/reports.log 2>&1
```

**Variables importantes :**

| Variable | Description | Exemple |
|----------|-------------|---------|
| `SHELL` | Shell utilisé pour exécuter les commandes | `/bin/bash` |
| `PATH` | Chemins de recherche des exécutables | `/usr/local/bin:/usr/bin:/bin` |
| `MAILTO` | Destinataire de la sortie des commandes | `admin@example.com` |
| `HOME` | Répertoire de travail | `/` ou `/root` |
| `LOGNAME` | Nom de l'utilisateur | `root` |

**⚠️ PATH limité :** Par défaut, cron utilise un PATH minimal. **Toujours utiliser des chemins absolus** ou définir PATH.

```bash
# ❌ MAUVAIS (peut ne pas trouver python3)
0 2 * * * root python3 /usr/local/bin/mon_script.py

# ✅ BON (chemin absolu)
0 2 * * * root /usr/bin/python3 /usr/local/bin/mon_script.py

# ✅ BON (PATH défini)
PATH=/usr/local/bin:/usr/bin:/bin
0 2 * * * root python3 /usr/local/bin/mon_script.py
```

**MAILTO - Gestion des Sorties**

```bash
# Envoyer les sorties à admin@example.com
MAILTO=admin@example.com
0 2 * * * root /usr/local/bin/backup.sh

# Désactiver l'envoi d'emails (sortie vers /dev/null)
MAILTO=""
0 2 * * * root /usr/local/bin/silent_task.sh

# Ou rediriger dans la commande
0 2 * * * root /usr/local/bin/task.sh > /dev/null 2>&1

# Rediriger vers un fichier log
0 2 * * * root /usr/local/bin/backup.sh >> /var/log/backup.log 2>&1
```

### 📂 Répertoire `/etc/cron.d/` - Tâches Modulaires

**`/etc/cron.d/` permet d'organiser les tâches cron par package/service.**

**Avantage :** Les packages peuvent installer leurs propres crontabs sans modifier `/etc/crontab`.

**Format identique à `/etc/crontab`** (6 champs avec utilisateur).

```bash
# Lister les fichiers dans /etc/cron.d/
ls -la /etc/cron.d/
# -rw-r--r-- 1 root root  102 Jan 15 10:25 php
# -rw-r--r-- 1 root root  712 Dec 10 08:30 sysstat
# -rw-r--r-- 1 root root  235 Nov 22 14:15 certbot

# Exemple : /etc/cron.d/certbot (renouvellement SSL)
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

0 */12 * * * root test -x /usr/bin/certbot && perl -e 'sleep int(rand(43200))' && certbot -q renew

# Exemple : /etc/cron.d/monitoring
# Vérification services toutes les 5 minutes
*/5 * * * * monitor /usr/local/bin/check_services.sh >> /var/log/monitoring.log 2>&1

# Nettoyage logs toutes les nuits à 3h
0 3 * * * root find /var/log/monitoring -type f -mtime +30 -delete
```

**Permissions requises :**
```bash
# Les fichiers dans /etc/cron.d/ doivent être :
# - Propriétaire : root
# - Non inscriptibles par groupe/autres
chmod 644 /etc/cron.d/mon_fichier

# Vérification
ls -la /etc/cron.d/mon_fichier
# -rw-r--r-- 1 root root 256 Feb 11 11:00 /etc/cron.d/mon_fichier
```

### 📅 Répertoires Automatiques - Cron Simplifiés

**Cron fournit des répertoires pour scripts exécutés automatiquement à intervalles réguliers.**

**Aucune configuration cron nécessaire !** Il suffit de placer un script exécutable dans le répertoire.

#### Répertoires Standard

```bash
/etc/cron.hourly/    # Exécuté toutes les heures
/etc/cron.daily/     # Exécuté une fois par jour
/etc/cron.weekly/    # Exécuté une fois par semaine
/etc/cron.monthly/   # Exécuté une fois par mois
```

**Mécanisme d'exécution :**
```bash
# Dans /etc/crontab (Debian/Ubuntu)
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )

# run-parts : exécute tous les scripts d'un répertoire
```

**⏰ Horaires par défaut (Debian/Ubuntu) :**
- **Hourly** : À 17 minutes après chaque heure (ex: 10:17, 11:17...)
- **Daily** : À 6h25 du matin
- **Weekly** : Dimanche à 6h47 du matin
- **Monthly** : Le 1er du mois à 6h52 du matin

**⚙️ Configuration anacron** (`/etc/anacrontab`) :
```bash
# Sur systèmes avec anacron, ce fichier définit les horaires
# /etc/anacrontab
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# period  delay  job-identifier  command
1         5      cron.daily      run-parts /etc/cron.daily
7         10     cron.weekly     run-parts /etc/cron.weekly
@monthly  15     cron.monthly    run-parts /etc/cron.monthly
```

#### Utilisation

```bash
# 1. Créer le script
sudo vim /etc/cron.daily/backup_databases

#!/bin/bash
# Backup des bases de données
mysqldump --all-databases | gzip > /backup/db_$(date +\%Y\%m\%d).sql.gz

# 2. Rendre exécutable
sudo chmod +x /etc/cron.daily/backup_databases

# 3. Vérification
ls -la /etc/cron.daily/backup_databases
# -rwxr-xr-x 1 root root 156 Feb 11 12:00 /etc/cron.daily/backup_databases

# Script sera exécuté automatiquement chaque jour !
```

**⚠️ Règles de nommage :**
```bash
# ✅ Noms VALIDES (exécutés)
/etc/cron.daily/backup
/etc/cron.daily/cleanup_logs
/etc/cron.daily/update-system

# ❌ Noms INVALIDES (ignorés par run-parts)
/etc/cron.daily/backup.sh        # Extension .sh
/etc/cron.daily/script.bak       # Extension .bak
/etc/cron.daily/test~            # Tilde
/etc/cron.daily/.hidden          # Commence par .
/etc/cron.daily/script.dpkg-old  # Extension .dpkg-*

# Règle : pas d'extensions, caractères alphanumériques, -, _ seulement
```

**Tester manuellement :**
```bash
# Exécuter tous les scripts d'un répertoire (comme cron le ferait)
run-parts /etc/cron.daily

# Tester un script spécifique
run-parts --test /etc/cron.hourly
# Affiche la liste des scripts qui seraient exécutés

# Exécuter avec rapport détaillé
run-parts --report /etc/cron.daily
```

### 🔒 Contrôle d'Accès - Allow/Deny

**Cron permet de restreindre l'accès via des fichiers whitelist/blacklist.**

#### Fichiers de Contrôle

```bash
/etc/cron.allow    # Liste blanche (si existe, seuls ces users peuvent utiliser cron)
/etc/cron.deny     # Liste noire (users interdits de cron)
```

**Logique d'accès :**
```
┌──────────────────────────────────────────────────────┐
│  1. Si /etc/cron.allow existe :                      │
│     → Seuls les utilisateurs listés peuvent cron     │
│     → /etc/cron.deny est ignoré                      │
│                                                      │
│  2. Si /etc/cron.allow n'existe pas :                │
│     → Vérifier /etc/cron.deny                        │
│     → Users listés = INTERDITS                       │
│     → Autres users = AUTORISÉS                       │
│                                                      │
│  3. Si aucun fichier n'existe :                      │
│     → Debian/Ubuntu : Tous autorisés (sauf autre config)│
│     → RedHat/CentOS : Seul root autorisé            │
└──────────────────────────────────────────────────────┘
```

**Exemples :**

**Mode liste blanche :**
```bash
# Créer /etc/cron.allow (1 user par ligne)
sudo vim /etc/cron.allow

root
alice
bob
admin

# Résultat : SEULS root, alice, bob, admin peuvent utiliser cron

# Test
su - charlie
crontab -e
# You (charlie) are not allowed to use this program (crontab)
```

**Mode liste noire :**
```bash
# S'assurer que /etc/cron.allow n'existe PAS
sudo rm /etc/cron.allow

# Créer /etc/cron.deny
sudo vim /etc/cron.deny

guest
temp_user
stagiaire

# Résultat : guest, temp_user, stagiaire ne peuvent PAS utiliser cron
# Tous les autres utilisateurs : OK
```

### 🔍 Dépannage Cron

```bash
# Vérifier que crond est actif
systemctl status cron        # Debian/Ubuntu
systemctl status crond       # RedHat/CentOS

# Logs cron (Debian/Ubuntu)
grep CRON /var/log/syslog
tail -f /var/log/syslog | grep CRON

# Logs cron (RedHat/CentOS)
grep CROND /var/log/cron
tail -f /var/log/cron

# Forcer reload de toutes les crontabs
systemctl reload cron

# Tester un script manuellement (simulation cron)
su - root
cd /
env -i SHELL=/bin/bash HOME=/root PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin \
  /usr/local/bin/mon_script.sh

# Vérifier la syntaxe d'un fichier crontab
crontab -l | crontab -T   # (si supporté)
```

**Erreurs fréquentes :**
```bash
# ❌ Utiliser % dans la commande (caractère spécial pour cron)
0 2 * * * backup_$(date +%Y%m%d).sh   # ERREUR

# ✅ Échapper les %
0 2 * * * backup_$(date +\%Y\%m\%d).sh

# ❌ PATH incomplet
0 2 * * * python script.py   # python non trouvé

# ✅ Chemin absolu
0 2 * * * /usr/bin/python3 /home/user/script.py

# ❌ Script non exécutable
0 2 * * * /usr/local/bin/backup.sh
# -rw-r--r-- backup.sh (pas +x)

# ✅ Rendre exécutable
chmod +x /usr/local/bin/backup.sh
```

---

## ⏰ AT - Tâches Ponctuelles (One-Shot)

### 📌 Principe de `at`

**`at`** : Exécute une commande **une seule fois** à un moment déterminé dans le futur.

**Différence avec cron :**
- **Cron** : Tâches **récurrentes** (tous les jours, toutes les heures...)
- **at** : Tâches **ponctuelles** (une seule exécution)

**Daemon :** `atd` (doit être installé et actif)

```bash
# Vérifier si at est installé
which at
dpkg -l | grep ^ii | grep " at "     # Debian/Ubuntu
rpm -qa | grep ^at-                   # RedHat/CentOS

# Installer si nécessaire
sudo apt install at         # Debian/Ubuntu
sudo yum install at         # RedHat/CentOS

# Vérifier le service atd
systemctl status atd

# Activer et démarrer
sudo systemctl enable --now atd
```

### 📝 Utilisation de `at`

#### Planifier une Tâche

**Syntaxe :**
```bash
at [OPTIONS] TIMESPEC
```

**Spécification de temps (TIMESPEC) :**

| Format | Exemple | Description |
|--------|---------|-------------|
| **Heure** | `17:30` | Aujourd'hui à 17h30 (ou demain si déjà passé) |
|  | `5:00 PM` | 17h00 |
|  | `now + 30 minutes` | Dans 30 minutes |
|  | `now + 1 hour` | Dans 1 heure |
|  | `now + 2 hours` | Dans 2 heures |
|  | `now + 3 days` | Dans 3 jours |
|  | `now + 2 weeks` | Dans 2 semaines |
| **Date + Heure** | `10:00 2026-12-25` | 25 décembre 2026 à 10h |
|  | `2:00 AM tomorrow` | Demain à 2h du matin |
|  | `4:00 PM next Monday` | Lundi prochain à 16h |
| **Mots-clés** | `now` | Maintenant (immédiat) |
|  | `noon` | Midi |
|  | `midnight` | Minuit |
|  | `teatime` | 16h00 (4 PM) |
|  | `today` | Aujourd'hui |
|  | `tomorrow` | Demain |
|  | `next week` | La semaine prochaine |

**Mode interactif :**
```bash
# Lancer at avec spécification de temps
at now + 1 hour

# at> invite apparaît, entrer les commandes
at> echo "Tâche exécutée" > /tmp/at_test.txt
at> df -h >> /tmp/disk_usage.log
at> mail -s "Rapport" user@example.com < /tmp/report.txt
at> <Ctrl+D>   # Terminer avec Ctrl+D

# Confirmation
job 5 at Mon Feb 12 13:45:00 2026
```

**Exemples pratiques :**

```bash
# Dans 10 minutes
at now + 10 minutes
at> /usr/local/bin/cleanup.sh
at> <Ctrl+D>

# Demain à 9h
at 9:00 AM tomorrow
at> /usr/local/bin/morning_report.sh
at> <Ctrl+D>

# À minuit
at midnight
at> /usr/local/bin/daily_backup.sh
at> <Ctrl+D>

# Dimanche prochain à 14h
at 2:00 PM next Sunday
at> /usr/local/bin/weekly_maintenance.sh
at> <Ctrl+D>

# Date spécifique
at 10:30 2026-12-25
at> echo "Joyeux Noël !" | wall
at> <Ctrl+D>

# Immédiatement (utile pour test)
at now
at> echo "Test immédiat" > /tmp/test.txt
at> <Ctrl+D>
```

**Commandes multiples :**
```bash
at now + 2 hours
at> cd /var/log
at> tar -czf backup_logs_$(date +\%Y\%m\%d).tar.gz *.log
at> mv backup_logs_*.tar.gz /backup/
at> echo "Backup terminé" | mail -s "Logs archivés" admin@localhost
at> <Ctrl+D>
```

**Depuis stdin (non-interactif) :**
```bash
# Pipe une commande
echo "/usr/local/bin/script.sh" | at now + 30 minutes

# Heredoc
at now + 1 hour <<EOF
/usr/bin/systemctl restart nginx
echo "Nginx redémarré à \$(date)" >> /var/log/nginx_restart.log
EOF

# Depuis fichier
at -f /home/user/script.sh now + 2 hours
```

**Options de `at` :**

| Option | Description | Exemple |
|--------|-------------|---------|
| `-f fichier` | Exécuter les commandes depuis un fichier | `at -f script.sh tomorrow` |
| `-m` | Envoyer un mail même si pas de sortie | `at -m now + 1 hour` |
| `-M` | Ne jamais envoyer de mail | `at -M midnight` |
| `-v` | Afficher l'heure d'exécution | `at -v now + 30 min` |
| `-q queue` | Spécifier une file (a-z, A-Z) | `at -q b 17:00` |

**Files (queues) :**
```bash
# Files par défaut
a = at (défaut, priorité normale)
b = batch (priorité basse, s'exécute quand charge système faible)
c-z = autres files personnalisées

# Files majuscules (A-Z) pour "at" équivalent mail jobs

# Utiliser une file spécifique
at -q b now + 1 hour    # File 'b' = batch (basse priorité)
```

### 📋 Gestion des Tâches `at`

#### Lister les Tâches - `atq`

```bash
# Lister toutes les tâches en attente (at queue)
atq

# Sortie exemple :
# 5       Mon Feb 12 13:45:00 2026 a alice
# 6       Mon Feb 12 18:00:00 2026 a root
# 7       Tue Feb 13 09:00:00 2026 a bob
# ^       ^                        ^ ^
# Job ID  Date/Heure               Queue User

# Lister mes tâches seulement
atq

# Lister les tâches d'un utilisateur (root seulement)
sudo atq -u alice

# Alias : at -l (identique à atq)
at -l
```

#### Examiner une Tâche - `at -c`

```bash
# Afficher le contenu complet d'une tâche
at -c 5

# Sortie : script complet + environnement
# #!/bin/sh
# # atrun uid=1001 gid=1001
# # mail alice 0
# umask 22
# ...
# cd /home/alice || {
#     echo 'Execution directory inaccessible' >&2
#     exit 1
# }
# ...
# /usr/local/bin/cleanup.sh   # ← Commande réelle
```

#### Supprimer une Tâche - `atrm`

```bash
# Supprimer la tâche numéro 5
atrm 5

# Vérifier suppression
atq
# (job 5 a disparu)

# Supprimer plusieurs tâches
atrm 6 7 8

# Supprimer toutes mes tâches
atq | awk '{print $1}' | xargs -r atrm

# Alias : at -r (identique à atrm)
at -r 5
```

#### Commande `batch` - Exécution à Charge Faible

**`batch`** : Variante de `at` qui s'exécute quand la charge système est faible.

```bash
# Planifier une tâche batch (s'exécute quand load < 0.8)
batch
at> /usr/local/bin/heavy_processing.sh
at> <Ctrl+D>

# Équivalent
at -q b now

# Spécifier le seuil de charge (RedHat/CentOS)
batch -l 1.5   # S'exécute si load average < 1.5
```

**Cas d'usage batch :**
- Tâches gourmandes en CPU
- Tâches pouvant attendre (non urgentes)
- Éviter de surcharger le système

### 🔒 Contrôle d'Accès `at`

**Fichiers de contrôle identiques à cron :**

```bash
/etc/at.allow    # Liste blanche (si existe, seuls ces users)
/etc/at.deny     # Liste noire (users interdits)
```

**Logique identique à cron :**

```bash
# Mode liste blanche
sudo vim /etc/at.allow
root
alice
admin

# Résultat : seuls root, alice, admin peuvent utiliser at

# Mode liste noire (at.allow n'existe pas)
sudo vim /etc/at.deny
guest
temp

# Résultat : guest et temp ne peuvent PAS utiliser at
```

### 🔍 Dépannage `at`

```bash
# Vérifier le service
systemctl status atd
journalctl -u atd -f

# Logs at
grep atd /var/log/syslog        # Debian/Ubuntu
grep atd /var/log/messages      # RedHat/CentOS

# Vérifier les tâches en cours
atq

# Tester at
echo "date > /tmp/at_test_$(whoami).txt" | at now + 1 minute
# Attendre 1 minute
cat /tmp/at_test_$(whoami).txt

# Permissions répertoires
ls -ld /var/spool/at
# drwx------ 2 daemon daemon

# Fichier de tâches
ls -la /var/spool/at/
```

---

## ⏲️ SYSTEMD TIMERS - Planification Moderne

### 🎯 Principe des Systemd Timers

**Systemd timers** : Alternative moderne à cron, intégrée à systemd.

**Avantages vs cron :**
- ✅ Intégration complète systemd (dépendances, logs, statut)
- ✅ Gestion fine du timing (précision, randomisation, persistance)
- ✅ Exécution même si système éteint (persistance)
- ✅ Logs centralisés (`journalctl`)
- ✅ Gestion des erreurs (restart policies)
- ✅ Isolation (sandboxing, cgroups)

**Architecture :**
```
┌──────────────────┐         ┌──────────────────┐
│  myservice.timer │ active  │  myservice.service│
│  (Planification) ├────────>│  (Tâche réelle)  │
└──────────────────┘         └──────────────────┘
         │
         │ OnCalendar=daily
         │ Persistent=true
         │
    systemd gère le déclenchement
```

**⚠️ Prérequis : Un timer nécessite TOUJOURS un service du même nom.**

```bash
# Pour mytask.timer, il FAUT mytask.service
/etc/systemd/system/mytask.timer
/etc/systemd/system/mytask.service
```

### 📝 Création d'un Timer Systemd

#### Étape 1 : Créer le Service

**Fichier : `/etc/systemd/system/mybackup.service`**
```ini
[Unit]
Description=Backup quotidien des données utilisateur
Wants=network-online.target
After=network-online.target

[Service]
Type=oneshot
ExecStart=/usr/local/bin/backup_script.sh
User=backup
Group=backup
StandardOutput=journal
StandardError=journal

# Sécurité (optionnel)
PrivateTmp=yes
NoNewPrivileges=yes
```

**Type=oneshot :** Le service s'exécute une fois puis se termine (idéal pour timer).

#### Étape 2 : Créer le Timer

**Fichier : `/etc/systemd/system/mybackup.timer`**
```ini
[Unit]
Description=Lance le backup quotidien à 2h du matin
Requires=mybackup.service

[Timer]
OnCalendar=daily
OnCalendar=*-*-* 02:00:00
Persistent=true

[Install]
WantedBy=timers.target
```

**Sections :**

**[Unit]** :
- `Description` : Description du timer
- `Requires=mybackup.service` : Dépendance au service (optionnel mais recommandé)

**[Timer]** :
- Définit QUAND le service est déclenché

**[Install]** :
- `WantedBy=timers.target` : Active le timer au démarrage

#### Étape 3 : Activer et Démarrer

```bash
# Recharger la configuration systemd (OBLIGATOIRE après création/modification)
sudo systemctl daemon-reload

# Activer le timer (démarrage automatique au boot)
sudo systemctl enable mybackup.timer

# Démarrer le timer immédiatement
sudo systemctl start mybackup.timer

# Ou en une commande
sudo systemctl enable --now mybackup.timer

# Vérifier le statut
systemctl status mybackup.timer
● mybackup.timer - Lance le backup quotidien à 2h du matin
     Loaded: loaded (/etc/systemd/system/mybackup.timer; enabled; vendor preset: enabled)
     Active: active (waiting) since Mon 2026-02-11 12:00:00 CET; 5s ago
    Trigger: Tue 2026-02-12 02:00:00 CET; 14h left
   Triggers: ● mybackup.service

# Lister tous les timers actifs
systemctl list-timers

# Sortie :
NEXT                         LEFT          LAST                         PASSED       UNIT
Tue 2026-02-12 02:00:00 CET  14h left      Mon 2026-02-11 02:00:00 CET  10h ago      mybackup.timer
Tue 2026-02-12 06:25:00 CET  18h left      Mon 2026-02-11 06:25:00 CET  5h ago       apt-daily.timer
```

### ⏰ Directives de Planification

#### OnCalendar - Planification Calendaire

**Format complet :**
```
OnCalendar=DayOfWeek YYYY-MM-DD HH:MM:SS
```

**Composants :**

| Champ | Values | Description |
|-------|--------|-------------|
| DayOfWeek | Mon, Tue, Wed, Thu, Fri, Sat, Sun | Jour de la semaine (optionnel) |
| Year | YYYY ou `*` | Année |
| Month | 01-12 ou `*` | Mois |
| Day | 01-31 ou `*` | Jour du mois |
| Hour | 00-23 | Heure |
| Minute | 00-59 | Minute |
| Second | 00-59 | Seconde (optionnel) |

**Métacaractères :**

| Symbole | Signification | Exemple |
|---------|---------------|---------|
| `*` | Toutes les valeurs | `* * * * *` (chaque minute) |
| `,` | Énumération | `Mon,Wed,Fri` |
| `..` | Plage | `1..7` (1 à 7) |
| `/` | Intervalle | `*/15` (toutes les 15 unités) |

**Exemples OnCalendar :**

```ini
# Tous les jours à 2h42
OnCalendar=*-*-* 02:42:00

# Chaque lundi à 9h
OnCalendar=Mon *-*-* 09:00:00

# Premier samedi de chaque mois à 3h
OnCalendar=Sat *-*-1..7 03:00:00

# Tous les jours à minuit
OnCalendar=*-*-* 00:00:00

# Toutes les heures (minute 0)
OnCalendar=*-*-* *:00:00

# Toutes les 15 minutes
OnCalendar=*-*-* *:00/15:00

# Chaque jour ouvré (lundi-vendredi) à 8h30
OnCalendar=Mon..Fri *-*-* 08:30:00

# 1er janvier de chaque année à midi
OnCalendar=*-01-01 12:00:00

# Dimanche à 23h
OnCalendar=Sun *-*-* 23:00:00

# 15 de chaque mois à 14h
OnCalendar=*-*-15 14:00:00

# Chaque trimestre (1er janvier, avril, juillet, octobre)
OnCalendar=*-01,04,07,10-01 00:00:00
```

**Raccourcis :**

| Raccourci | Équivalent |
|-----------|------------|
| `minutely` | `*-*-* *:*:00` |
| `hourly` | `*-*-* *:00:00` |
| `daily` | `*-*-* 00:00:00` |
| `weekly` | `Mon *-*-* 00:00:00` |
| `monthly` | `*-*-01 00:00:00` |
| `yearly` | `*-01-01 00:00:00` |
| `annually` | `*-01-01 00:00:00` |
| `quarterly` | `*-01,04,07,10-01 00:00:00` |
| `semiannually` | `*-01,07-01 00:00:00` |

**Exemples avec raccourcis :**
```ini
[Timer]
OnCalendar=daily           # Minuit chaque jour

[Timer]
OnCalendar=weekly          # Lundi minuit

[Timer]
OnCalendar=monthly         # 1er du mois à minuit
```

**Tester un format OnCalendar :**
```bash
# Vérifier si le format est valide et voir les prochaines occurrences
systemd-analyze calendar "Sat *-*-1..7 02:42:00"

# Sortie :
  Original form: Sat *-*-1..7 02:42:00
Normalized form: Sat *-*-01..07 02:42:00
    Next elapse: Sat 2026-03-07 02:42:00 CET
       (in UTC): Sat 2026-03-07 01:42:00 UTC
       From now: 3 weeks 2 days left

# Autres exemples
systemd-analyze calendar "Mon..Fri *-*-* 08:30:00"
systemd-analyze calendar "daily"
systemd-analyze calendar "*-*-* *:00/15:00"
```

#### Autres Directives de Timing

**OnActiveSec** : Temps après activation du timer
```ini
[Timer]
OnActiveSec=5min
# Lance le service 5 minutes après activation du timer
```

**OnBootSec** : Temps après démarrage système
```ini
[Timer]
OnBootSec=10min
# Lance le service 10 minutes après boot
```

**OnStartupSec** : Temps après démarrage systemd (user services)
```ini
[Timer]
OnStartupSec=1h
# Lance 1 heure après démarrage service manager (utilisateur)
```

**OnUnitActiveSec** : Temps après dernière activation du service
```ini
[Timer]
OnUnitActiveSec=30min
# Relance le service toutes les 30 minutes après sa dernière activation
```

**OnUnitInactiveSec** : Temps après dernière désactivation du service
```ini
[Timer]
OnUnitInactiveSec=1h
# Relance 1 heure après que le service se soit terminé
```

**Unités de temps :**
```
usec, us, µs    microseconds
msec, ms        milliseconds
seconds, second, sec, s
minutes, minute, min, m
hours, hour, hr, h
days, day, d
weeks, week, w
months, month, M
years, year, y
```

**Combinaisons :**
```ini
[Timer]
OnUnitActiveSec=5min 20s    # 5 minutes et 20 secondes
OnBootSec=2h 30min          # 2 heures et 30 minutes
```

**Exemple : Timer récurrent basé sur l'exécution**
```ini
[Timer]
OnActiveSec=0               # Immédiatement la première fois
OnUnitActiveSec=1h          # Puis toutes les heures après chaque exécution
```

### 🎲 Directives Avancées

#### Persistent - Rattrapage après Arrêt

**Persistent=true** : Exécute la tâche au démarrage si elle a été manquée pendant que le système était éteint.

```ini
[Timer]
OnCalendar=daily
Persistent=true
# Si le système était éteint à minuit, la tâche s'exécute au démarrage
```

**Cas d'usage :** Laptops, serveurs non 24/7, VM arrêtées régulièrement.

#### RandomizedDelaySec - Randomisation

**RandomizedDelaySec** : Ajoute un délai aléatoire avant exécution.

```ini
[Timer]
OnCalendar=daily
RandomizedDelaySec=12h
# S'exécute entre minuit et midi (0-12h de délai aléatoire)
```

**Avantage :** Éviter pics de charge (ex: 1000 serveurs lançant backup exactement à minuit).

#### AccuracySec - Précision

**AccuracySec** : Fenêtre de précision (économie d'énergie).

```ini
[Timer]
OnCalendar=hourly
AccuracySec=5min
# Peut s'exécuter entre HH:00 et HH:05 (systemd regroupe les timers)
```

**Par défaut :** 1 minute

**Avantage :** Économie batterie sur laptops (regroupe wake-ups CPU).

#### Exemple Complet avec Toutes les Options

```ini
[Unit]
Description=Backup avec randomisation et persistance
Requires=backup.service

[Timer]
# Tous les jours à 2h du matin
OnCalendar=daily
OnCalendar=*-*-* 02:00:00

# Randomisation 0-12h (s'exécutera entre 2h et 14h)
RandomizedDelaySec=12h

# Précision 20 minutes (peut s'exécuter ±20min autour de l'heure planifiée)
AccuracySec=20min

# Rattrapage si système éteint
Persistent=true

# Exécuter aussi 15 minutes après boot
OnBootSec=15min

[Install]
WantedBy=timers.target
```

### 🔧 Gestion des Timers

```bash
# Lister tous les timers
systemctl list-timers
systemctl list-timers --all    # Inclure inactifs

# Statut d'un timer
systemctl status backup.timer

# Activer/Désactiver
systemctl enable backup.timer
systemctl disable backup.timer

# Démarrer/Arrêter
systemctl start backup.timer
systemctl stop backup.timer

# Recharger après modification
sudo systemctl daemon-reload
sudo systemctl restart backup.timer

# Afficher la configuration complète
systemctl cat backup.timer

# Voir les propriétés
systemctl show backup.timer

# Logs du service déclenché
journalctl -u backup.service
journalctl -u backup.service -f    # Follow
journalctl -u backup.service -b    # Depuis dernier boot

# Forcer l'exécution immédiate du service
systemctl start backup.service

# Vérifier la prochaine exécution
systemctl list-timers backup.timer
```

### 📊 Exemple Complet : Nettoyage Logs Hebdomadaire

**1. Script : `/usr/local/bin/cleanup_logs.sh`**
```bash
#!/bin/bash
# Nettoyage des anciens logs

LOG_DIR="/var/log/myapp"
RETENTION_DAYS=90

find "$LOG_DIR" -type f -name "*.log" -mtime +$RETENTION_DAYS -delete
find "$LOG_DIR" -type f -name "*.gz" -mtime +$RETENTION_DAYS -delete

echo "Logs nettoyés : $(date)" >> /var/log/cleanup.log
```

```bash
# Rendre exécutable
chmod +x /usr/local/bin/cleanup_logs.sh
```

**2. Service : `/etc/systemd/system/cleanup-logs.service`**
```ini
[Unit]
Description=Nettoyage des anciens fichiers logs
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/local/bin/cleanup_logs.sh
User=root
StandardOutput=journal
StandardError=journal
```

**3. Timer : `/etc/systemd/system/cleanup-logs.timer`**
```ini
[Unit]
Description=Nettoyage hebdomadaire des logs
Requires=cleanup-logs.service

[Timer]
# Tous les dimanches à 3h du matin
OnCalendar=Sun *-*-* 03:00:00

# Randomisation 2h (entre 3h et 5h)
RandomizedDelaySec=2h

# Rattrapage si système éteint
Persistent=true

[Install]
WantedBy=timers.target
```

**4. Activation**
```bash
sudo systemctl daemon-reload
sudo systemctl enable --now cleanup-logs.timer

# Vérification
systemctl status cleanup-logs.timer
systemctl list-timers cleanup-logs.timer

# Test manuel
sudo systemctl start cleanup-logs.service
journalctl -u cleanup-logs.service
```

---

## 🚀 SYSTEMD-RUN - Exécution Temporaire

### 🎯 Principe de `systemd-run`

**`systemd-run`** : Exécute une commande dans un **service/timer temporaire** sans créer de fichiers unit.

**Avantages :**
- ✅ Pas de fichier `.service` ou `.timer` à créer
- ✅ Unités temporaires auto-nettoyées
- ✅ Isolation immédiate (cgroups, namespaces)
- ✅ Idéal pour tests ou tâches ponctuelles

**Alternative à `at` avec intégration systemd complète.**

### 📝 Utilisation de systemd-run

#### Exécution Immédiate

```bash
# Exécuter une commande immédiatement dans un service temporaire
systemd-run /usr/local/bin/my_script.sh

# Sortie :
Running as unit: run-u123.service

# Voir le statut
systemctl status run-u123.service

# Logs
journalctl -u run-u123.service
```

#### Exécution Différée (Timer Temporaire)

**Syntaxe planification :**
```bash
systemd-run --on-active=DURATION commande
systemd-run --on-calendar=CALENDAR_SPEC commande
```

**Exemples avec délai relatif :**

```bash
# Dans 2 heures
systemd-run --on-active="2hours" /usr/local/bin/script.sh

# Dans 30 minutes
systemd-run --on-active="30min" /usr/bin/systemctl restart nginx

# Dans 5 minutes et 30 secondes
systemd-run --on-active="5min 30s" echo "Tâche terminée"

# Demain à cette heure
systemd-run --on-active="1day" /usr/local/bin/daily_task.sh
```

**Exemples avec calendrier :**

```bash
# Tous les jours à 2h du matin
systemd-run --on-calendar="*-*-* 02:00:00" /usr/local/bin/backup.sh

# Chaque lundi à 9h
systemd-run --on-calendar="Mon *-*-* 09:00:00" /usr/local/bin/weekly_report.sh

# Dans 10 minutes (calendrier absolu)
systemd-run --on-calendar="$(date -d '+10 minutes' '+%Y-%m-%d %H:%M:%S')" echo "Test"
```

#### Nommer l'Unité Temporaire

**Option `--unit` :** Spécifier le nom du service/timer temporaire.

```bash
# Sans --unit (nom auto-généré)
systemd-run --on-active="2hours" /usr/local/bin/backup.sh
# Running as unit: run-u456.service
# Running timer as unit: run-u456.timer

# Avec --unit (nom personnalisé)
systemd-run --on-active="2hours" --unit="mybackup.service" /usr/local/bin/backup.sh
# Running as unit: mybackup.service
# Running timer as unit: mybackup.timer

# Vérifier
systemctl list-timers mybackup.timer
systemctl status mybackup.service
```

#### Exécuter un Service Existant

**Si le service existe déjà :**

```bash
# Supposons que backup.service existe déjà
ls /etc/systemd/system/backup.service

# Planifier son exécution dans 2 heures
systemd-run --on-active="2hours" --unit="backup.service"

# OU (si backup.service prend des arguments)
systemd-run --on-active="2hours" --unit="backup.service" -- --full-backup
```

#### Script Inline (Pas de Fichier)

```bash
# Commande simple inline
systemd-run --on-active="1hour" /bin/bash -c "echo 'Backup terminé' | mail -s 'Status' admin@localhost"

# Script multi-lignes
systemd-run --on-active="30min" /bin/bash -c '
    cd /var/log
    tar -czf backup_logs_$(date +%Y%m%d).tar.gz *.log
    mv backup_logs_*.tar.gz /backup/
    echo "Logs archivés"
'
```

### 🎛️ Options de systemd-run

| Option | Description | Exemple |
|--------|-------------|---------|
| `--on-active=DURATION` | Délai relatif depuis maintenant | `--on-active="2h"` |
| `--on-boot=DURATION` | Délai depuis boot système | `--on-boot="15min"` |
| `--on-calendar=SPEC` | Planification calendaire | `--on-calendar="daily"` |
| `--unit=NAME` | Nom du service temporaire | `--unit="myservice"` |
| `--description=TEXT` | Description du service | `--description="Backup quotidien"` |
| `--remain-after-exit` | Garder le service actif après fin | `--remain-after-exit` |
| `--send-sighup` | Envoyer SIGHUP à la fin | `--send-sighup` |
| `--service-type=TYPE` | Type de service (oneshot, simple...) | `--service-type=oneshot` |
| `--uid=USER` | Exécuter en tant qu'utilisateur | `--uid=alice` |
| `--gid=GROUP` | Exécuter en tant que groupe | `--gid=staff` |
| `--nice=LEVEL` | Priorité CPU | `--nice=19` |
| `--property=PROP` | Définir propriété systemd | `--property=CPUQuota=50%` |
| `--working-directory=DIR` | Répertoire de travail | `--working-directory=/tmp` |

**Exemples avec options :**

```bash
# Exécution en tant qu'utilisateur alice
systemd-run --uid=alice --on-active="1hour" /home/alice/script.sh

# Avec description
systemd-run --on-active="2h" \
  --unit="cleanup" \
  --description="Nettoyage temporaire des logs" \
  /usr/local/bin/cleanup.sh

# Limiter CPU à 50%
systemd-run --on-active="30min" \
  --property=CPUQuota=50% \
  /usr/bin/heavy_process.sh

# Répertoire de travail spécifique
systemd-run --on-active="5min" \
  --working-directory=/var/www \
  /usr/bin/php artisan queue:work
```

### 📋 Gestion des Unités Temporaires

```bash
# Lister les timers temporaires
systemctl list-timers
systemctl list-timers 'run-*'

# Statut d'une unité temporaire
systemctl status run-u123.service

# Logs
journalctl -u run-u123.service

# Arrêter un timer temporaire avant exécution
systemctl stop run-u123.timer

# Les unités temporaires sont auto-nettoyées après exécution
# Mais on peut forcer le nettoyage
systemctl reset-failed
```

### 🆚 systemd-run vs at

| Critère | systemd-run | at |
|---------|-------------|-----|
| **Intégration** | Systemd natif | Daemon séparé (atd) |
| **Logs** | journalctl | Mail + logs système |
| **Isolation** | cgroups, namespaces | Process simple |
| **Ressources** | Limites CPU/RAM possibles | Aucune limitation |
| **Complexité** | Moyen (syntaxe systemd) | Simple (syntaxe naturelle) |
| **Dépendances** | Systemd (toujours présent) | atd (peut ne pas être installé) |

**Quand utiliser quoi ?**

```bash
# at : Simple et rapide
echo "echo Test" | at now + 5 minutes

# systemd-run : Contrôle avancé, logs centralisés
systemd-run --on-active="5min" \
  --property=CPUQuota=20% \
  --uid=backup \
  /usr/local/bin/heavy_backup.sh
```

---

## 📊 Comparaison Globale

| Outil | Récurrence | Précision | Setup | Logs | Persistance | Cas d'usage |
|-------|------------|-----------|-------|------|-------------|-------------|
| **cron** | ✅ Oui (minute) | 1 min | Facile | syslog/mail | ❌ Non | Standard, simple, universel |
| **anacron** | ✅ Oui (jour) | 1 jour | Facile | syslog | ✅ Oui (rattrapage) | Machines non 24/7 |
| **at** | ❌ Non (one-shot) | 1 min | Très facile | Mail | ❌ Non | Tâche ponctuelle simple |
| **systemd timer** | ✅ Oui (seconde) | 1 µs | Moyen | journalctl | ✅ Oui (optionnel) | Intégration systemd, complexe |
| **systemd-run** | 🔶 Optionnel | 1 µs | Facile | journalctl | ❌ Non | Exécution rapide temporaire |

---

## 🎓 Scénarios Pratiques

### Scénario 1 : Backup Quotidien (cron)

**Besoin :** Sauvegarder `/home` tous les jours à 2h du matin.

```bash
# Créer le script
sudo vim /usr/local/bin/daily_backup.sh

#!/bin/bash
BACKUP_DIR="/backup"
DATE=$(date +%Y%m%d)
tar -czf $BACKUP_DIR/home_$DATE.tar.gz /home
find $BACKUP_DIR -name "home_*.tar.gz" -mtime +30 -delete

sudo chmod +x /usr/local/bin/daily_backup.sh

# Ajouter à crontab système
sudo vim /etc/crontab

# Backup quotidien
0 2 * * * root /usr/local/bin/daily_backup.sh >> /var/log/backup.log 2>&1

# Ou dans répertoire automatique
sudo cp /usr/local/bin/daily_backup.sh /etc/cron.daily/backup
sudo chmod +x /etc/cron.daily/backup
```

### Scénario 2 : Redémarrage Service (at)

**Besoin :** Redémarrer nginx dans 30 minutes (maintenance planifiée).

```bash
# Planifier le redémarrage
at now + 30 minutes
at> systemctl restart nginx
at> echo "Nginx redémarré à $(date)" | mail -s "Maintenance" admin@localhost
at> <Ctrl+D>

# Vérifier
atq
# 12      Mon Feb 12 14:30:00 2026 a root

# Annuler si nécessaire
atrm 12
```

### Scénario 3 : Nettoyage Hebdomadaire (systemd timer)

**Besoin :** Supprimer fichiers temporaires tous les dimanches à 3h.

**Service : `/etc/systemd/system/cleanup-tmp.service`**
```ini
[Unit]
Description=Nettoyage fichiers temporaires

[Service]
Type=oneshot
ExecStart=/usr/bin/find /tmp -type f -atime +7 -delete
ExecStart=/usr/bin/find /var/tmp -type f -atime +30 -delete
```

**Timer : `/etc/systemd/system/cleanup-tmp.timer`**
```ini
[Unit]
Description=Nettoyage hebdomadaire /tmp

[Timer]
OnCalendar=Sun *-*-* 03:00:00
Persistent=true

[Install]
WantedBy=timers.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now cleanup-tmp.timer
systemctl list-timers cleanup-tmp.timer
```

### Scénario 4 : Monitoring Toutes les 5 Minutes (cron)

**Besoin :** Vérifier espace disque toutes les 5 minutes, alerter si >90%.

```bash
# Script
sudo vim /usr/local/bin/check_disk.sh

#!/bin/bash
THRESHOLD=90
USAGE=$(df / | tail -1 | awk '{print $5}' | sed 's/%//')

if [ $USAGE -gt $THRESHOLD ]; then
    echo "ALERTE : Disque à ${USAGE}%" | mail -s "Disk Alert" admin@localhost
fi

sudo chmod +x /usr/local/bin/check_disk.sh

# Crontab utilisateur
crontab -e

*/5 * * * * /usr/local/bin/check_disk.sh
```

### Scénario 5 : Tâche Ponctuelle avec systemd-run

**Besoin :** Exporter base de données dans 1 heure.

```bash
systemd-run --on-active="1hour" \
  --unit="db-export" \
  --description="Export base de données" \
  /usr/bin/mysqldump --all-databases | gzip > /backup/db_$(date +%Y%m%d_%H%M).sql.gz

# Vérifier
systemctl list-timers db-export.timer
journalctl -u db-export.service -f
```

---

## ⚠️ Erreurs Courantes

### Erreur 1 : Cron - PATH incomplet

**Problème :**
```bash
0 2 * * * /root/backup.sh
# backup.sh appelle "mysqldump" qui n'est pas trouvé
```

**Solution :**
```bash
# Définir PATH au début de crontab
PATH=/usr/local/bin:/usr/bin:/bin

# Ou utiliser chemin absolu dans le script
/usr/bin/mysqldump ...
```

### Erreur 2 : at - Service atd non démarré

```bash
at now + 1 hour
# Cannot find atd daemon

# Solution
sudo systemctl start atd
sudo systemctl enable atd
```

### Erreur 3 : systemd timer - Oublier daemon-reload

```bash
# Modifier .timer
vim /etc/systemd/system/backup.timer

# Redémarrer sans reload
systemctl restart backup.timer
# Anciennes config appliquées !

# Solution : TOUJOURS
sudo systemctl daemon-reload
sudo systemctl restart backup.timer
```

### Erreur 4 : Cron - Caractère % non échappé

```bash
# ❌ ERREUR
0 2 * * * tar -czf backup_$(date +%Y%m%d).tar.gz /home

# ✅ CORRECT
0 2 * * * tar -czf backup_$(date +\%Y\%m\%d).tar.gz /home
```

### Erreur 5 : systemd timer - Pas de service correspondant

```bash
# Créer mytask.timer SANS mytask.service
systemctl start mytask.timer
# Failed to start mytask.timer: Unit mytask.service not found.

# Solution : TOUJOURS créer le service
```

---

## 📝 Cheat Sheet

### Cron

```bash
# Éditer crontab utilisateur
crontab -e

# Lister
crontab -l

# Supprimer
crontab -r

# Format (5 champs)
# m h dom mon dow command
0 2 * * * /usr/local/bin/script.sh

# Raccourcis
@reboot, @yearly, @monthly, @weekly, @daily, @hourly

# Système
/etc/crontab           # Crontab système (6 champs avec user)
/etc/cron.d/           # Crontabs modulaires
/etc/cron.{hourly,daily,weekly,monthly}/  # Répertoires auto

# Logs
grep CRON /var/log/syslog
```

### at

```bash
# Planifier
at now + 30 minutes
at 17:00
at tomorrow
at> commande
at> <Ctrl+D>

# Lister
atq

# Supprimer
atrm <job_id>

# Examiner
at -c <job_id>

# Service
systemctl status atd
```

### systemd timers

```bash
# Fichiers
/etc/systemd/system/name.service
/etc/systemd/system/name.timer

# [Timer] section
OnCalendar=daily
OnActiveSec=5min
Persistent=true

# Gestion
systemctl daemon-reload
systemctl enable --now name.timer
systemctl list-timers
systemctl status name.timer
journalctl -u name.service
```

### systemd-run

```bash
# Exécution immédiate
systemd-run /usr/local/bin/script.sh

# Exécution différée
systemd-run --on-active="2hours" /usr/local/bin/script.sh
systemd-run --on-calendar="daily" /usr/local/bin/script.sh

# Avec nom
systemd-run --on-active="1h" --unit="mybackup" /usr/local/bin/backup.sh

# Logs
journalctl -u run-*.service
```

---

**Formation LPIC-1 102 - Préparation Certification Linux**  
**Cours 107.2 - Automatisation des Tâches Administration**  
**Maxime Chenaud - 11 Février 2026**
