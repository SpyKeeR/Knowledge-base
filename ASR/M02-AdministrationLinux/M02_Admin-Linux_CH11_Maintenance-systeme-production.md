# M02 - Administration Linux

## CH11 - Maintenance du système en production

Ce chapitre couvre deux grands axes : la **gestion des logs** (journald + rsyslog) et les **outils d'analyse** du système. En production, savoir lire les logs et investiguer rapidement est une compétence critique.

---

### 11.1 - Gestion des journaux système

RHEL 9 utilise deux systèmes de journalisation complémentaires :

- **journald** (via systemd) : stocke les logs dans une base de données binaire, interrogée avec `journalctl`
- **rsyslog** : système historique, stocke les logs dans des fichiers texte dans `/var/log/`

Par défaut, journald transmet ses logs à rsyslog. Les deux coexistent.

---

### 11.2 - journald (systemd-journald)

#### 📄 Configuration

Fichier : `/etc/systemd/journald.conf`

Par défaut, journald stocke ses logs de manière **volatile** dans `/run/log/journal/` (perdus au reboot). Pour les rendre persistants :

```ini
[Journal]
Storage=persistent
SystemMaxUse=150M          # Taille max globale sur disque
SystemMaxFileSize=20M      # Taille max par fichier de log
```

💡 On peut aussi simplement créer le répertoire `/var/log/journal/` pour activer la persistance (le paramètre `Storage=auto` cherche ce répertoire).

Après modification :

```bash
# systemctl restart systemd-journald
```

📌 Si journald est configuré en mode persistant, il est possible de désactiver rsyslog pour éviter les doublons :

```bash
# systemctl disable rsyslog
```

#### 🔧 `journalctl` : la commande centrale

`journalctl` interroge la base de données de journald. Navigation avec les mêmes commandes que `less`.

##### 📋 Commandes de base

```bash
journalctl                    # Tous les logs
journalctl -f                 # Suivi en temps réel (follow)
journalctl -n 50              # 50 dernières entrées
journalctl --no-pager         # Sans pager (sortie brute)
```

##### 🔍 Filtrage par service / programme / PID

```bash
journalctl -u crond.service         # Logs d'un service
journalctl /usr/sbin/sshd           # Logs d'un programme (chemin binaire)
journalctl _PID=1234                # Logs d'un PID spécifique
```

##### 🔍 Filtrage par priorité

Du plus critique au plus informatif : `emerg` > `alert` > `crit` > `err` > `warning` > `notice` > `info` > `debug`

```bash
journalctl -p err                   # err et au-dessus (err, crit, alert, emerg)
journalctl -p warning               # warning et au-dessus
journalctl -u crond.service -p warning  # Combinaison service + priorité
```

##### 🔍 Filtrage par boot

```bash
journalctl -b                       # Boot actuel
journalctl -b -1                    # Boot précédent
journalctl --list-boots             # Lister tous les boots (requiert persistance)
journalctl -k -b -1 -p warning      # Warnings kernel du boot précédent
```

##### 🔍 Filtrage par date

```bash
journalctl --since "1 hour ago"
journalctl --since "1 week ago"
journalctl --since "2026-05-26" --until "2026-05-28"
journalctl -u crond.service --since "3 days ago" --until "1 day ago"
```

##### 🔍 Filtrage par facility syslog

```bash
journalctl SYSLOG_FACILITY=10       # Facility 10 = authpriv (sessions)
journalctl SYSLOG_FACILITY=10 --since "1 week ago" --no-pager | grep "session opened"
```

💡 Les options sont cumulables. C'est la force de `journalctl` par rapport à `grep` dans des fichiers texte.

---

### 11.3 - rsyslog

rsyslog est le système de logging historique qui écrit les logs dans des **fichiers texte** sous `/var/log/`.

#### 📄 Configuration (rsyslog)

Fichier principal : `/etc/rsyslog.conf`
Fichiers additionnels : `/etc/rsyslog.d/*.conf`

#### 🧱 Structure des règles : facility.priority → action

Chaque règle associe une **facilité** (source) et un **niveau de priorité** (sévérité minimum) à une **action** (généralement un fichier).

##### Facilities courantes

| Facility | Usage |
| --- | --- |
| `auth` / `authpriv` | Authentification, contrôle d'accès (SSH, login) |
| `daemon` | Processus systèmes et applicatifs |
| `kern` | Messages du noyau |
| `cron` | Planification de tâches |
| `mail` | Services mail |
| `user` | Par défaut si aucune facility spécifiée |
| `local0` - `local7` | Personnalisables par les applications |
| `*` | Toutes les facilities |
| `none` | Aucune facility |

##### Niveaux de priorité (du plus critique au moins critique)

| Priorité | Signification |
| --- | --- |
| `emerg` | Système inutilisable |
| `alert` | Intervention immédiate nécessaire |
| `crit` | Erreur système critique |
| `err` | Erreur de fonctionnement |
| `warning` | Avertissement |
| `notice` | Événement normal devant être signalé |
| `info` | Information |
| `debug` | Débogage |

##### Syntaxe des règles

```bash
# facility.priority    action (fichier)
auth,authpriv.*                        /var/log/auth.log
cron.warning                           /var/log/cron_warn.log
*.*;auth,authpriv.none                 -/var/log/syslog
kern.*                                 -/var/log/kern.log
```

📌 Le `-` devant un chemin indique un enregistrement **asynchrone** (meilleure performance, léger risque de perte en cas de crash brutal).

⚠️ Une règle `cron.warning` capture **warning et au-dessus** (warning, err, crit, alert, emerg). Pour capturer exactement un seul niveau, utiliser `=` : `cron.=warning`.

Après modification de rsyslog :

```bash
# systemctl restart rsyslog
```

#### 📂 Fichiers de logs principaux

| Fichier | Contenu | Distribution |
| --- | --- | --- |
| `/var/log/messages` | Log général | Red Hat / Oracle Linux |
| `/var/log/syslog` | Log général | Debian / Ubuntu |
| `/var/log/secure` | Authentification | Red Hat / Oracle Linux |
| `/var/log/auth.log` | Authentification | Debian / Ubuntu |
| `/var/log/kern.log` | Messages kernel | |
| `/var/log/cron` | Logs cron | |
| `/var/log/boot.log` | Logs de démarrage | |

---

### 11.4 - `logger` : interagir avec les logs

`logger` permet d'écrire manuellement dans journald et rsyslog. Utile pour les tests et les scripts.

```bash
logger "Message de test"                           # Facility user, priorité notice
logger -p cron.error "Test de log manuel du service cron"   # Facility + priorité spécifiques
logger -t monscript "Démarrage du backup"           # Tag personnalisé
```

---

### 11.5 - Outils d'analyse du système

#### 🔍 Informations système

| Information | Commande |
| --- | --- |
| Distribution et version | `cat /etc/oracle-release` (ou `/etc/redhat-release`) |
| Nom d'hôte | `hostname -s` (court) / `hostname -f` (FQDN) / `hostnamectl` |
| Version du noyau | `uname -r` (version seule) / `uname -a` (tout) |
| Type de CPU | `lscpu` |
| Carte graphique | `lspci \| grep VGA` |
| Périphériques PCI | `lspci` |
| Périphériques USB | `lsusb` |

#### 🔍 Mémoire et swap

```bash
free -h                   # RAM et swap (human readable)
free -h -w                # Avec colonnes buffers/cache séparées
cat /proc/meminfo         # Détail complet
```

#### 🔍 Disques et stockage

```bash
lsblk                     # Vue arborescente disques/partitions
fdisk -l                  # Toutes les partitions
pvs / vgs / lvs           # Infos LVM résumées
df -h                     # Espace utilisé par FS montés
du -sh /etc               # Taille d'un répertoire
```

#### 🔍 Réseau

```bash
ip a                      # Interfaces réseau et adresses
ip link                   # État des interfaces
ip link | grep -E "^[0-9]{1,3}:" | wc -l   # Compter les interfaces
```

#### 🔍 Processus

```bash
ps -ef                    # Tous les processus (format complet)
ps -eo pid,comm           # PID et nom uniquement
ps -eo pid,comm | grep 'd$'   # Démons (nom finissant par "d")
pgrep -l sshd             # Chercher un processus par nom
```

#### 🔍 Outils de monitoring temps réel

| Outil | Installation | Description |
| --- | --- | --- |
| `top` | Natif | Vue temps réel CPU, RAM, processus |
| `htop` | `dnf install htop` | Version améliorée de top (couleurs, navigation) |
| `atop` | `dnf install atop` | Vue système complète (CPU, RAM, disque, réseau) |
| `glances` | `dnf install glances` | Outil avancé, mode client/serveur, export vers outils de supervision |

#### 🔍 Commandes utilitaires

| Information | Commande |
| --- | --- |
| Utilisateurs standards du système | `getent passwd {1000..2000} \| cut -d: -f1` |
| Liste des paquets installés | `rpm -qa > /root/ListePaquets-$(date +%Y-%m-%d)` |
| Uptime (temps depuis dernier reboot) | `uptime -s` (date) / `uptime` (durée) |
| Nature d'un fichier | `file /bin/bash` |
| Fichiers ouverts dans un répertoire | `lsof /root` |

---

### 🧪 Atelier 10 - Gestion des logs

#### I. Configuration de journald

Éditer `/etc/systemd/journald.conf` :

```ini
[Journal]
Storage=persistent
SystemMaxUse=150M
SystemMaxFileSize=20M
```

```bash
# systemctl restart systemd-journald
```

#### II. Configuration de rsyslog

Éditer `/etc/rsyslog.conf` pour séparer les logs cron :

```bash
# Logs cron en dessous de warning (comportement existant)
cron.!warning                          /var/log/cron

# Logs cron à partir de warning
cron.warning                           /var/log/cron_warn.log
```

```bash
# systemctl restart rsyslog
```

Générer un message de test :

```bash
logger -p cron.error "Test de log manuel du service cron"
```

📌 Ce message apparaît dans plusieurs endroits : `/var/log/cron_warn.log` (règle cron.warning), `/var/log/messages` (règle `*.*`), et dans journalctl (`journalctl -u crond`).

#### III. Recherche de logs

```bash
# Sessions de la semaine (plusieurs méthodes complémentaires)
$ last --since "1 week ago" | grep -v reboot >> /adm/sessions.txt
$ journalctl SYSLOG_FACILITY=10 --since "1 week ago" --no-pager \
    | grep "session opened" >> /adm/sessions.txt

# Après pkill -9 crond + systemctl --force --force poweroff :
$ journalctl -u crond.service -p warning      # Messages cron ≥ warning
$ journalctl -k -b -1 -p warning              # Warnings/erreurs kernel du boot précédent

# Logs cron entre mardi et jeudi
$ journalctl -u crond.service --since "2026-05-26" --until "2026-05-28"
```

### 🧪 Atelier 11 - Analyse du système

| Élément | Commande |
| --- | --- |
| Distribution | `cat /etc/oracle-release` |
| Hostname | `hostname -s` |
| Carte graphique | `lspci \| grep VGA` |
| RAM disponible | `free -h -w` |
| Type CPU | `lscpu \| grep ^Arch` |
| Version kernel | `uname -r` |
| Nb disques physiques | `lsblk -d \| grep disk \| wc -l` |
| Volumes logiques | `lvs` |
| Nb interfaces réseau | `ip link \| grep -E "^[0-9]+:" \| wc -l` |
| Espace disque | `df -h` |
| Comptes utilisateurs standards | `getent passwd {1000..2000} \| cut -d: -f1` |
| Liste paquets | `rpm -qa > /root/ListePaquets-$(date +%Y-%m-%d)` |
| Démons actifs | `ps -eo pid,comm \| grep 'd$'` |
| Uptime | `uptime -s` |

---

### 📌 À retenir

1. **journald** stocke les logs dans une base binaire, interrogée avec `journalctl`. Par défaut volatile, configurer `Storage=persistent` pour garder les logs entre les reboots.
2. **rsyslog** utilise le format `facility.priority → action`. Les priorités sont ordonnées : `emerg` > `alert` > `crit` > `err` > `warning` > `notice` > `info` > `debug`.
3. `journalctl -u service -p priority --since "date"` : les filtres se cumulent. C'est l'outil de diagnostic #1.
4. `logger -p facility.priority "message"` pour injecter des logs manuellement (tests, scripts).
5. Commandes d'audit rapide : `uname -r` (kernel), `lscpu` (CPU), `free -h` (RAM), `df -h` (disques), `ps -ef` (processus), `uptime` (temps de fonctionnement).
