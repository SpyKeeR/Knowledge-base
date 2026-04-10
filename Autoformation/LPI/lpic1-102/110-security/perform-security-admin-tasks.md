# 110.1 - Perform Security Administration Tasks 🔐👮

## 📋 Informations du Module

- **Poids à l'examen** : 3 points
- **Objectifs clés** :
  - Maîtriser l'élévation de privilèges avec `su` et `sudo` ✅
  - Gérer les utilisateurs connectés et les mots de passe
  - Comprendre et configurer les permissions spéciales (SUID, SGID, Sticky Bit)
  - Configurer les limites de ressources utilisateurs (`ulimit`)
  - Vérifier les ports ouverts et services en écoute
  - Scanner les réseaux et ports avec `nmap`

---

## 🎯 Partie 1 : Élévation de Privilèges et Gestion des Mots de Passe

### 🔑 Différences `su` vs `sudo`

#### Commande `su` - Switch User

**Rôle** : **Changer d'utilisateur** dans le terminal actuel.

**Syntaxe** :

```bash
su [OPTIONS] [utilisateur]
```

**Comportement par défaut** :
- Sans argument : Passe à l'utilisateur **root**
- Avec utilisateur : Passe à l'utilisateur spécifié

**Fonctionnement** :
- ✅ Demande le **mot de passe de l'utilisateur cible** (root, bob, etc.)
- ✅ Ouvre un nouveau shell avec les privilèges de cet utilisateur
- ❌ Requiert de connaître le mot de passe root (risque sécurité)

**Exemples** :

```bash
# Passer à root (demande mot de passe root)
$ su
Password: [mot_de_passe_root]
# 

# Passer à utilisateur alice (demande mot de passe alice)
$ su alice
Password: [mot_de_passe_alice]
alice@machine:~$

# Passer à root avec shell login (- ou -l)
$ su -
# ou
$ su -l
# ou
$ su --login
Password: [mot_de_passe_root]
root@machine:~#
```

---

#### Différence Shell Login vs Non-Login

**Shell Non-Login** (sans `-`) :

```bash
$ su
# 
```

- **N'exécute PAS** les scripts de profil (`/etc/profile`, `~/.bash_profile`, `~/.profile`)
- **Conserve** l'environnement du shell précédent (variables, PATH, etc.)
- **Ne change PAS** le répertoire courant

**Exemple** :

```bash
alice@machine:/tmp$ echo $USER
alice

alice@machine:/tmp$ su bob
Password:
bob@machine:/tmp$ echo $USER
bob

bob@machine:/tmp$ pwd
/tmp  # Toujours dans /tmp

bob@machine:/tmp$ echo $HOME
/home/alice  # Variable HOME inchangée !
```

**Shell Login** (avec `-`) :

```bash
$ su -
# ou
$ su - bob
```

- **Exécute** les scripts de profil complets
- **Réinitialise** l'environnement (comme connexion fraîche)
- **Change** le répertoire vers `$HOME` de l'utilisateur cible
- **Charge** PATH, variables, aliases de l'utilisateur cible

**Exemple** :

```bash
alice@machine:/tmp$ su - bob
Password:
bob@machine:~$ pwd
/home/bob  # Changé vers HOME de bob

bob@machine:~$ echo $USER
bob

bob@machine:~$ echo $HOME
/home/bob  # Variable HOME correcte

bob@machine:~$ echo $PATH
/usr/local/bin:/usr/bin:/bin  # PATH de bob chargé
```

**Tableau récapitulatif** :

| Caractéristique | `su` (sans `-`) | `su -` (avec `-`) |
|-----------------|-----------------|-------------------|
| **Scripts profil** | ❌ Non exécutés | ✅ Exécutés |
| **Environnement** | Hérité shell précédent | Réinitialisé (frais) |
| **Répertoire** | Inchangé | `$HOME` utilisateur cible |
| **Variables** | Héritées (USER, HOME incorrect) | Correctes |
| **PATH** | Hérité | PATH utilisateur cible |
| **Usage recommandé** | Commande ponctuelle | Session complète |

**Commande dans contexte utilisateur** :

```bash
# Exécuter commande en tant que root sans ouvrir shell
$ su -c "apt update"
Password: [root]

# Exécuter commande en tant qu'utilisateur spécifique
$ su alice -c "echo \$USER"
Password: [alice]
alice
```

---

#### Commande `sudo` - Superuser Do

**Rôle** : Exécuter une **commande avec privilèges root** (ou autre utilisateur) **sans** connaître le mot de passe root.

**Syntaxe** :

```bash
sudo [OPTIONS] <commande>
```

**Fonctionnement** :
- ✅ Demande le **mot de passe de l'utilisateur actuel** (pas root !)
- ✅ Vérifie si l'utilisateur est autorisé dans `/etc/sudoers`
- ✅ Exécute la commande avec privilèges root (ou autre utilisateur)
- ✅ Met en cache le mot de passe (défaut 15 minutes)
- ✅ **Journalise** toutes les commandes (sécurité, audit)

**Exemples** :

```bash
# Installer un paquet (nécessite root)
$ sudo apt install vim
[sudo] password for alice: [mot_de_passe_alice]
# Installation...

# Éditer fichier système
$ sudo nano /etc/hosts

# Redémarrer service
$ sudo systemctl restart apache2

# Ouvrir shell root (-i = interactive login)
$ sudo -i
root@machine:~#

# Ouvrir shell root (sans login)
$ sudo -s
# 

# Exécuter commande en tant qu'autre utilisateur (-u)
$ sudo -u bob whoami
bob

$ sudo -u www-data cat /var/www/html/index.php
```

---

#### Comparaison `su` vs `sudo`

| Critère | `su` | `sudo` |
|---------|------|--------|
| **Mot de passe** | Utilisateur **cible** (root) | Utilisateur **actuel** |
| **Sécurité** | ❌ Nécessite partager mdp root | ✅ Pas besoin mdp root |
| **Granularité** | ❌ Tout ou rien | ✅ Permissions par commande |
| **Journalisation** | ❌ Minimale | ✅ Complète (`/var/log/auth.log`) |
| **Session** | Shell complet | Commande ponctuelle (par défaut) |
| **Configuration** | Aucune | `/etc/sudoers` |
| **Groupe requis** | Mot de passe root | Groupe `sudo` ou `wheel` |
| **Audit** | ❌ Difficile | ✅ Facile (qui a fait quoi) |
| **Usage moderne** | ❌ Déconseillé | ✅ Recommandé |

**Résumé** :
- `su` : Changer d'utilisateur (nécessite mdp cible)
- `sudo` : Exécuter commande privilégiée (nécessite mdp actuel, plus sûr)

---

### ⚙️ Configuration `sudo` - Fichier `/etc/sudoers`

#### Éditer le Fichier avec `visudo`

⚠️ **IMPORTANT** : **NE JAMAIS** éditer `/etc/sudoers` directement avec `nano`, `vim`, etc. !

**Utiliser `visudo`** :

```bash
$ sudo visudo
```

**Avantages de `visudo`** :
- ✅ **Verrouillage** : Empêche éditions simultanées (corruption fichier)
- ✅ **Validation syntaxe** : Vérifie erreurs avant sauvegarde
- ✅ **Prévient verrouillage** : Si erreur syntaxe → sudo cassé → impossible corriger !
- ✅ **Éditeur configurable** : `EDITOR=nano sudo visudo`

**Éditeur par défaut** : `vi` ou `vim`

**Changer éditeur** :

```bash
# Temporaire
$ sudo EDITOR=nano visudo

# Permanent
$ sudo update-alternatives --config editor
# Choisir nano ou autre
```

---

#### Structure du Fichier `/etc/sudoers`

**Exemple de contenu** :

```bash
$ sudo cat /etc/sudoers
# User privilege specification
root    ALL=(ALL:ALL) ALL

# Members of the admin group may gain root privileges
%admin ALL=(ALL) ALL

# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# User alice can run apt commands without password
alice   ALL=(ALL) NOPASSWD: /usr/bin/apt

# User bob can restart apache as www-data
bob     ALL=(www-data) /usr/bin/systemctl restart apache2

# Group webadmin can manage web services
%webadmin ALL=(ALL) /usr/bin/systemctl restart nginx, /usr/bin/systemctl reload nginx

# Includes
@includedir /etc/sudoers.d
```

---

#### Syntaxe des Lignes de Configuration

**Format général** :

```
utilisateur  hôtes=(utilisateurs_cibles:groupes_cibles)  commandes
```

**Détails** :

```
alice  ALL=(ALL:ALL)  NOPASSWD: /usr/bin/apt, /usr/bin/systemctl
  │     │    │   │       │           └─ Commandes autorisées
  │     │    │   │       └─ Option : pas de mot de passe
  │     │    │   └─ Groupes cibles (peut devenir)
  │     │    └─ Utilisateurs cibles (peut devenir)
  │     └─ Hôtes applicables (ALL = tous)
  └─ Utilisateur/groupe concerné
```

**Exemples décryptés** :

**1. Accès root complet** :

```
root    ALL=(ALL:ALL) ALL
```

- `root` = Utilisateur root
- `ALL` (hôtes) = Sur toutes les machines (réseau multi-hôtes)
- `(ALL:ALL)` = Peut devenir n'importe quel utilisateur et groupe
- `ALL` (commandes) = Peut exécuter toutes les commandes

**2. Groupe sudo** :

```
%sudo   ALL=(ALL:ALL) ALL
```

- `%sudo` = Groupe "sudo" (% = groupe)
- Membres du groupe `sudo` peuvent tout faire avec `sudo`

**3. Commande spécifique sans mot de passe** :

```
alice   ALL=(ALL) NOPASSWD: /usr/bin/apt
```

- `alice` peut exécuter `sudo apt` sans entrer mot de passe
- Utile pour scripts automatisés

**4. Commande en tant qu'utilisateur spécifique** :

```
bob     ALL=(www-data) /usr/bin/systemctl restart apache2
```

- `bob` peut faire : `sudo -u www-data systemctl restart apache2`
- Utile pour gestion services web

**5. Plusieurs commandes** :

```
alice   ALL=(ALL) /usr/bin/systemctl restart nginx, /usr/bin/systemctl reload nginx
```

- Plusieurs commandes séparées par virgules

**6. Commandes avec arguments** :

```
alice   ALL=(ALL) /usr/bin/systemctl restart *
```

- `*` = Wildcard, permet n'importe quel argument

**7. Alias de commandes** :

```bash
# Définir alias
Cmnd_Alias WEB_SERVICES = /usr/bin/systemctl restart nginx, /usr/bin/systemctl reload nginx

# Utiliser alias
%webadmin ALL=(ALL) WEB_SERVICES
```

**8. Alias d'utilisateurs** :

```bash
User_Alias ADMINS = alice, bob, charlie
ADMINS ALL=(ALL) ALL
```

---

#### Création des Alias - Syntaxe Complète

**Alias d'utilisateurs** :

```bash
# Syntaxe
User_Alias NOMALIAS = NomUser1, NomUser2, ..., NomUserN

# Exemples
User_Alias ADMINS = alice, bob, charlie
User_Alias WEBTEAM = www-admin, webdev1, webdev2
User_Alias DBADMINS = dba1, dba2

# Utilisation
ADMINS ALL=(ALL) ALL
WEBTEAM ALL=(ALL) /usr/bin/systemctl restart nginx
DBADMINS ALL=(postgres) /usr/bin/psql
```

**Alias de machines** :

```bash
# Syntaxe
Host_Alias NOMALIAS = Hostname1, Hostname2, ..., HostnameN

# Exemples
Host_Alias WEBSERVERS = web1, web2, web3
Host_Alias DBSERVERS = db1.exemple.com, db2.exemple.com
Host_Alias PRODSERVERS = WEBSERVERS, DBSERVERS

# Utilisation
alice WEBSERVERS=(ALL) /usr/bin/systemctl restart nginx
bob DBSERVERS=(ALL) /usr/bin/systemctl restart postgresql
```

**Alias de commandes** :

```bash
# Syntaxe
Cmnd_Alias NOMALIAS = Commande1, Commande2, ..., CommandeN

# Exemples
Cmnd_Alias NETWORKING = /usr/sbin/ifconfig, /usr/sbin/route, /usr/bin/ip
Cmnd_Alias SOFTWARE = /usr/bin/apt, /usr/bin/dpkg, /usr/bin/apt-get
Cmnd_Alias WEB_SERVICES = /usr/bin/systemctl restart nginx, /usr/bin/systemctl reload nginx

# Utilisation
%netadmin ALL=(ALL) NETWORKING
%developers ALL=(ALL) SOFTWARE
%webadmin ALL=(ALL) WEB_SERVICES
```

**💡 Notes importantes** :

- Les **commandes** doivent être spécifiées par le **chemin d'accès complet** de leur exécutable
- Elles peuvent prendre des **options et arguments** :
  ```bash
  Cmnd_Alias RESTART_NGINX = /usr/bin/systemctl restart nginx
  Cmnd_Alias APT_UPDATE = /usr/bin/apt update, /usr/bin/apt upgrade
  ```

**Désactiver demande de mot de passe** :

```bash
# Syntaxe
user machine = NOPASSWD: commande

# Exemples
alice ALL = NOPASSWD: /usr/bin/apt
bob web1 = NOPASSWD: /usr/bin/systemctl restart nginx
%developers ALL = NOPASSWD: /usr/bin/git
```

**Associer commande à un compte utilisateur** :

```bash
# Syntaxe
user machine = (NomUser) commande

# Exemples
bob ALL = (www-data) /usr/bin/systemctl restart apache2
alice ALL = (postgres) /usr/bin/psql
%webadmin ALL = (www-data) /usr/bin/systemctl restart nginx, /usr/bin/systemctl reload nginx
```

---

#### Groupe `sudo` et `wheel`

**Groupe `sudo`** (Debian/Ubuntu) :

```bash
# Vérifier si groupe existe
$ getent group sudo
sudo:x:27:alice,bob

# Ajouter utilisateur au groupe sudo
$ sudo usermod -aG sudo charlie

# Vérifier appartenance
$ groups charlie
charlie : charlie sudo

# Tester
$ su - charlie
charlie@machine:~$ sudo apt update
[sudo] password for charlie:
# ✅ Fonctionne !
```

**Groupe `wheel`** (RHEL/CentOS/Fedora) :

Même principe, mais nom différent : `wheel`

```bash
# RHEL/CentOS
$ sudo usermod -aG wheel charlie

# Configuration sudoers
$ sudo grep wheel /etc/sudoers
%wheel  ALL=(ALL)  ALL
```

**Vérifier configuration active** :

```bash
$ sudo grep -E '^%sudo|^%wheel' /etc/sudoers
%sudo   ALL=(ALL:ALL) ALL
```

---

#### Fichier `/etc/sudoers.d/`

**Bonne pratique** : Placer configurations personnalisées dans `/etc/sudoers.d/` au lieu de modifier `/etc/sudoers` directement.

**Avantages** :
- ✅ Organisation (1 fichier par utilisateur/groupe)
- ✅ Évite conflits lors mises à jour système
- ✅ Facilite gestion

**Exemples** :

```bash
# Créer fichier pour utilisateur alice
$ sudo visudo -f /etc/sudoers.d/alice
# Contenu :
alice ALL=(ALL) NOPASSWD: /usr/bin/apt, /usr/bin/systemctl

# Créer fichier pour groupe webadmin
$ sudo visudo -f /etc/sudoers.d/webadmin
# Contenu :
%webadmin ALL=(ALL) /usr/bin/systemctl restart nginx

# Vérifier inclusions
$ sudo grep includedir /etc/sudoers
@includedir /etc/sudoers.d
```

**Permissions requises** : `440` (r--r-----)

```bash
$ ls -l /etc/sudoers.d/alice
-r--r----- 1 root root 58 Feb 12 10:00 /etc/sudoers.d/alice
```

---

### 🔒 Gestion des Mots de Passe - Commande `passwd`

#### Syntaxe de Base

```bash
passwd [OPTIONS] [utilisateur]
```

**Sans argument** : Change mot de passe utilisateur actuel

```bash
$ passwd
Changing password for alice.
Current password: [ancien_mdp]
New password: [nouveau_mdp]
Retype new password: [nouveau_mdp]
passwd: password updated successfully
```

**Avec utilisateur (root requis)** :

```bash
# Changer mot de passe de bob
$ sudo passwd bob
New password: [nouveau_mdp_bob]
Retype new password: [nouveau_mdp_bob]
passwd: password updated successfully
```

---

#### Options Principales `passwd`

| Option | Description | Exemple |
|--------|-------------|---------|
| `-S` | **Status** : Afficher infos mot de passe | `passwd -S alice` |
| `-l` | **Lock** : Verrouiller compte (désactiver connexion) | `sudo passwd -l bob` |
| `-u` | **Unlock** : Déverrouiller compte | `sudo passwd -u bob` |
| `-d` | **Delete** : Supprimer mot de passe (connexion sans mdp) | `sudo passwd -d alice` |
| `-e` | **Expire** : Forcer changement mdp à prochaine connexion | `sudo passwd -e bob` |
| `-n <jours>` | **Minimum** : Durée min avant changement mdp | `sudo passwd -n 7 alice` |
| `-x <jours>` | **Maximum** : Durée max validité mdp | `sudo passwd -x 90 alice` |
| `-w <jours>` | **Warning** : Avertissement avant expiration | `sudo passwd -w 14 alice` |
| `-i <jours>` | **Inactive** : Délai inactivité après expiration | `sudo passwd -i 30 alice` |

---

#### Option `-S` - Status du Mot de Passe

**Afficher informations sur le mot de passe** :

```bash
$ passwd -S alice
alice P 02/12/2026 7 90 14 30

# Format :
# utilisateur  statut  dernière_modif  min  max  warn  inactive
```

**Champs** :
- **`alice`** : Nom d'utilisateur
- **`P`** : Statut
  - `P` = Password (mot de passe actif)
  - `L` = Locked (compte verrouillé)
  - `NP` = No Password (pas de mot de passe)
- **`02/12/2026`** : Date dernière modification mdp
- **`7`** : Jours minimum avant prochain changement
- **`90`** : Jours maximum validité mdp (expire après 90 jours)
- **`14`** : Jours d'avertissement avant expiration
- **`30`** : Jours d'inactivité tolérés après expiration

**Exemples** :

```bash
# Utilisateur normal
$ passwd -S alice
alice P 02/12/2026 0 99999 7 -1
#      │     │       │   │    │  └─ Pas de délai inactivité
#      │     │       │   │    └─ Avertissement 7 jours avant
#      │     │       │   └─ Expiration dans 99999 jours (≈ jamais)
#      │     │       └─ Changement immédiat possible
#      │     └─ Dernière modif
#      └─ Mot de passe actif

# Compte verrouillé
$ sudo passwd -l bob
$ passwd -S bob
bob L 01/15/2026 0 99999 7 -1
#    └─ L = Locked (verrouillé)

# Compte sans mot de passe
$ sudo passwd -d charlie
$ passwd -S charlie
charlie NP 02/12/2026 0 99999 7 -1
#        └─ NP = No Password
```

---

#### Options `-l` et `-u` - Verrouiller/Déverrouiller

**Verrouiller compte (`-l`)** :

```bash
# Verrouiller bob (empêche connexion par mdp)
$ sudo passwd -l bob
passwd: password expiry information changed.

# Vérifier statut
$ sudo passwd -S bob
bob L 01/15/2026 0 99999 7 -1
#    └─ L = Locked

# Tentative connexion
$ su - bob
Password: [même_bon_mdp]
su: Authentication failure
#  ^^^
#  Connexion refusée même avec bon mdp
```

**Mécanisme** : Ajoute `!` devant hash du mot de passe dans `/etc/shadow`

```bash
$ sudo grep bob /etc/shadow
bob:!$6$abcd...:19751:0:99999:7:::
#     └─ ! = compte verrouillé
```

**Déverrouiller compte (`-u`)** :

```bash
$ sudo passwd -u bob
passwd: password expiry information changed.

$ sudo passwd -S bob
bob P 01/15/2026 0 99999 7 -1
#    └─ P = Password (actif)
```

**Cas d'usage** :
- Suspension temporaire compte (ex : employé en congé longue durée)
- Sécuriser compte compromis avant investigation

---

#### Option `-e` - Forcer Expiration Mot de Passe

**Forcer changement mdp à prochaine connexion** :

```bash
$ sudo passwd -e alice
passwd: password expiry information changed.

# Vérifier
$ sudo chage -l alice | grep "Password expires"
Password expires                                        : password must be changed
```

**Comportement** :

Lors prochaine connexion :

```
Welcome to Ubuntu 22.04
alice@server's password: [mdp_actuel]

You are required to change your password immediately (administrator enforced).
Current password: [mdp_actuel]
New password: [nouveau_mdp]
Retype new password: [nouveau_mdp]
passwd: password updated successfully
```

**Cas d'usage** :
- Nouveau compte créé (forcer mdp personnalisé)
- Réinitialisation mdp après incident sécurité
- Politique entreprise (changement périodique)

---

### 📅 Gestion Avancée des Mots de Passe - Commande `chage`

**Rôle** : Gérer la **politique d'expiration** des mots de passe.

#### Syntaxe

```bash
chage [OPTIONS] <utilisateur>
```

#### Mode Interactif (sans options)

```bash
$ sudo chage alice
Changing the aging information for alice
Enter the new value, or press ENTER for the default

        Minimum Password Age [0]: 7
        Maximum Password Age [99999]: 90
        Last Password Change (YYYY-MM-DD) [2026-02-12]:
        Password Expiration Warning [7]: 14
        Password Inactive [-1]: 30
        Account Expiration Date (YYYY-MM-DD) [-1]: 2026-12-31
```

---

#### Options Non-Interactives

| Option | Description | Exemple |
|--------|-------------|---------|
| `-l` | **List** : Afficher infos expiration | `sudo chage -l alice` |
| `-M <jours>` | **Maximum** : Durée max validité mdp | `sudo chage -M 90 alice` |
| `-m <jours>` | **Minimum** : Durée min avant changement | `sudo chage -m 7 alice` |
| `-W <jours>` | **Warning** : Avertissement avant expiration | `sudo chage -W 14 alice` |
| `-I <jours>` | **Inactive** : Délai inactivité après expiration | `sudo chage -I 30 alice` |
| `-E <date>` | **Expire** : Date expiration compte (YYYY-MM-DD) | `sudo chage -E 2026-12-31 alice` |
| `-d <date>` | **Last day** : Date dernière modif mdp | `sudo chage -d 0 alice` (force changement) |

---

#### Exemples Pratiques

**Afficher informations expiration** :

```bash
$ sudo chage -l alice
Last password change                                    : Feb 12, 2026
Password expires                                        : May 13, 2026
Password inactive                                       : Jun 12, 2026
Account expires                                         : Dec 31, 2026
Minimum number of days between password change          : 7
Maximum number of days between password change          : 90
Number of days of warning before password expires       : 14
```

**Définir expiration mot de passe 90 jours** :

```bash
$ sudo chage -M 90 alice
```

**Empêcher changement mdp pendant 7 jours** :

```bash
$ sudo chage -m 7 alice
```

**Avertir 14 jours avant expiration** :

```bash
$ sudo chage -W 14 alice
```

**Désactiver compte 30 jours après expiration mdp** :

```bash
$ sudo chage -I 30 alice
```

**Définir expiration compte à date précise** :

```bash
$ sudo chage -E 2026-12-31 alice
# Le compte alice sera désactivé le 31 déc 2026
```

**Forcer changement mdp immédiat** :

```bash
$ sudo chage -d 0 alice
# Équivalent à : sudo passwd -e alice
```

**Désactiver expiration mdp** :

```bash
# Mdp jamais expire
$ sudo chage -M 99999 alice

# Désactiver toutes expirations
$ sudo chage -M 99999 -m 0 -W 7 -I -1 -E -1 alice
```

---

#### Politique de Mots de Passe Recommandée

**Exemple entreprise sécurisée** :

```bash
#!/bin/bash
# Script configuration politique mdp pour utilisateur

USER="alice"

sudo chage \
  -M 90 \     # Expire tous les 90 jours
  -m 7 \      # Changement min tous les 7 jours
  -W 14 \     # Avertissement 14 jours avant
  -I 30 \     # Compte désactivé 30j après expiration mdp
  $USER

echo "Politique mdp configurée pour $USER :"
sudo chage -l $USER
```

**Vérification** :

```bash
$ sudo chage -l alice
Last password change                                    : Feb 12, 2026
Password expires                                        : May 13, 2026
Password inactive                                       : Jun 12, 2026
Account expires                                         : never
Minimum number of days between password change          : 7
Maximum number of days between password change          : 90
Number of days of warning before password expires       : 14
```

---

## 🎯 Partie 2 : Permissions Spéciales (SUID, SGID) et Limites Ressources

### 🔐 Permissions Spéciales - SUID et SGID

#### Rappel Permissions Classiques

**Format** : `rwxrwxrwx`

```
rwx rwx rwx
│   │   └─ Autres (other)
│   └─ Groupe (group)
└─ Propriétaire (user)

r = Read (4)
w = Write (2)
x = Execute (1)
```

**Exemple** :

```bash
$ ls -l /bin/bash
-rwxr-xr-x 1 root root 1234567 Jan 10 10:00 /bin/bash
 │││ │││ │││
 │││ │││ └┴┴─ Autres : r-x (5) = lecture + exécution
 │││ └┴┴─ Groupe : r-x (5)
 └┴┴─ Proprio : rwx (7)
```

---

#### SUID - Set User ID 🔴

**Rôle** : Exécuter un fichier avec les **privilèges du propriétaire** du fichier (au lieu de l'utilisateur qui l'exécute).

**Symbole** : `s` à la place de `x` pour le propriétaire

**Exemple classique** : `/usr/bin/passwd`

```bash
$ ls -l /usr/bin/passwd
-rwsr-xr-x 1 root root 68208 Jan 10 10:00 /usr/bin/passwd
    └─ s = SUID actif
```

**Pourquoi ?**
- La commande `passwd` modifie `/etc/shadow` (fichier root uniquement)
- Grâce au **SUID**, quand Alice exécute `passwd`, le processus s'exécute **en tant que root**
- Alice peut donc modifier `/etc/shadow` pour changer **son propre** mot de passe

**Sans SUID** :

```bash
$ ls -l /usr/bin/passwd
-rwxr-xr-x 1 root root 68208 Jan 10 10:00 /usr/bin/passwd
#  └─ x normal, pas de SUID

$ passwd
passwd: Authentication token manipulation error
passwd: password unchanged
#  ^^^
#  Échec : pas de permissions pour modifier /etc/shadow
```

**Fonctionnement** :

```
┌─────────────────────────────────────────────────┐
│ Alice exécute : passwd                          │
└────────────────┬────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────┐
│ Fichier /usr/bin/passwd                         │
│ - Propriétaire : root                           │
│ - Permissions : rwsr-xr-x (SUID actif)         │
└────────────────┬────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────┐
│ Processus passwd s'exécute avec :              │
│ - EUID (Effective User ID) = root              │
│ - Peut modifier /etc/shadow                     │
└─────────────────────────────────────────────────┘
```

**Définir SUID** :

```bash
# Symbolique : u+s
$ chmod u+s fichier

# Numérique : 4 devant permissions
$ chmod 4755 fichier
# 4755 = SUID + rwxr-xr-x

# Exemple
$ sudo chmod 4755 /usr/local/bin/mon_script.sh
$ ls -l /usr/local/bin/mon_script.sh
-rwsr-xr-x 1 root root 1234 Feb 12 10:00 /usr/local/bin/mon_script.sh
```

**Retirer SUID** :

```bash
$ chmod u-s fichier
# ou
$ chmod 0755 fichier
```

**Vérifier SUID** :

```bash
# S majuscule = SUID sans exécution (rare, incorrect)
-rwSr-xr-x  # S = SUID sans x

# s minuscule = SUID avec exécution (correct)
-rwsr-xr-x  # s = SUID avec x
```

**Exemples de fichiers SUID système** :

```bash
$ find /usr/bin -perm -4000 -ls 2>/dev/null
-rwsr-xr-x /usr/bin/passwd
-rwsr-xr-x /usr/bin/sudo
-rwsr-xr-x /usr/bin/su
-rwsr-xr-x /usr/bin/mount
-rwsr-xr-x /usr/bin/umount
-rwsr-xr-x /usr/bin/ping
```

⚠️ **Risque sécurité** :
- SUID sur scripts shell = **DANGEREUX** (vulnérabilités exploitation)
- Limiter SUID au strict nécessaire
- Auditer régulièrement fichiers SUID

---

#### SGID - Set Group ID 🟡

**Rôle** : Deux comportements selon type de fichier.

**SGID sur fichier exécutable** :
- Exécuter avec privilèges du **groupe propriétaire** du fichier

**SGID sur répertoire** :
- Nouveaux fichiers créés héritent du **groupe du répertoire** (au lieu du groupe principal de l'utilisateur)

**Symbole** : `s` à la place de `x` pour le groupe

---

##### SGID sur Fichier Exécutable

**Exemple** :

```bash
$ ls -l /usr/bin/wall
-rwxr-sr-x 1 root tty 14336 Jan 10 10:00 /usr/bin/wall
      └─ s = SGID actif (groupe tty)
```

**Fonctionnement** :
- Quand Alice exécute `wall`, processus s'exécute avec **groupe tty**
- Permet écrire sur terminaux (`/dev/tty*`) appartenant au groupe tty

**Définir SGID** :

```bash
# Symbolique : g+s
$ chmod g+s fichier

# Numérique : 2 devant permissions
$ chmod 2755 fichier
# 2755 = SGID + rwxr-xr-x

# Exemple
$ sudo chmod 2755 /usr/local/bin/script.sh
$ ls -l /usr/local/bin/script.sh
-rwxr-sr-x 1 root staff 1234 Feb 12 10:00 /usr/local/bin/script.sh
```

---

##### SGID sur Répertoire 📁

**Rôle le plus courant** : Partage de fichiers entre groupe.

**Fonctionnement** :
- Fichiers créés dans répertoire héritent **groupe du répertoire**
- Utile pour collaboration (tous fichiers appartiennent au même groupe)

**Exemple sans SGID** :

```bash
# Créer répertoire partagé
$ sudo mkdir /shared
$ sudo chown root:team /shared
$ sudo chmod 770 /shared
$ ls -ld /shared
drwxrwx--- 2 root team 4096 Feb 12 10:00 /shared

# Alice crée fichier
alice@server$ touch /shared/fichier_alice.txt
alice@server$ ls -l /shared/fichier_alice.txt
-rw-r--r-- 1 alice alice 0 Feb 12 10:00 /shared/fichier_alice.txt
#                   └─ Groupe alice, pas team !

# Bob ne peut pas modifier (pas membre groupe alice)
bob@server$ echo "test" >> /shared/fichier_alice.txt
bash: /shared/fichier_alice.txt: Permission denied
```

**Exemple AVEC SGID** :

```bash
# Activer SGID sur répertoire
$ sudo chmod g+s /shared
$ ls -ld /shared
drwxrws--- 2 root team 4096 Feb 12 10:00 /shared
#     └─ s = SGID actif

# Alice crée fichier
alice@server$ touch /shared/fichier_alice2.txt
alice@server$ ls -l /shared/fichier_alice2.txt
-rw-r--r-- 1 alice team 0 Feb 12 10:00 /shared/fichier_alice2.txt
#                   └─ Groupe team hérité du répertoire ! ✅

# Bob (membre groupe team) peut modifier
bob@server$ echo "Collaboration !" >> /shared/fichier_alice2.txt
# ✅ Fonctionne !
```

**Cas d'usage** :
- Répertoires de projets partagés
- Zones de dépôt `/var/www/uploads`
- Répertoires collaboratifs

---

#### Sticky Bit 🟢 (Bonus)

**Rôle** : Sur répertoire, seul le **propriétaire du fichier** peut le supprimer (même si autres ont permissions write).

**Symbole** : `t` à la place de `x` pour autres

**Exemple** : `/tmp`

```bash
$ ls -ld /tmp
drwxrwxrwt 20 root root 4096 Feb 12 10:00 /tmp
        └─ t = Sticky bit
```

**Fonctionnement** :
- Alice crée `/tmp/fichier_alice.txt`
- Bob a permissions write sur `/tmp` (rwx pour autres)
- **MAIS** Bob ne peut PAS supprimer fichier d'Alice (sticky bit)

**Définir sticky bit** :

```bash
# Symbolique : +t
$ chmod +t /shared
# ou
$ chmod o+t /shared

# Numérique : 1 devant permissions
$ chmod 1777 /tmp
# 1777 = Sticky + rwxrwxrwx
```

---

#### Tableau Récapitulatif Permissions Spéciales

| Permission | Symbole | Numérique | Cible | Effet | Exemple |
|------------|---------|-----------|-------|-------|---------|
| **SUID** | `s` (user) | `4xxx` | Fichier exécutable | Exécute avec UID propriétaire | `/usr/bin/passwd` (root) |
| **SGID** | `s` (group) | `2xxx` | Fichier exécutable | Exécute avec GID groupe | `/usr/bin/wall` (tty) |
| **SGID** | `s` (group) | `2xxx` | Répertoire | Fichiers héritent groupe répertoire | `/shared` (team) |
| **Sticky Bit** | `t` (other) | `1xxx` | Répertoire | Seul proprio peut supprimer | `/tmp` |

**Combinaisons possibles** :

```bash
# SUID + SGID + Sticky Bit
$ chmod 7777 fichier
# 7777 = 4000 (SUID) + 2000 (SGID) + 1000 (Sticky) + 777 (rwxrwxrwx)
-rwsrwsrwt

# SUID + SGID
$ chmod 6755 fichier
-rwsr-sr-x

# SGID + Sticky Bit (répertoire partagé sécurisé)
$ chmod 3770 /shared
drwxrws--T
```

---

### 📊 Limites de Ressources - Commande `ulimit`

#### Description

`ulimit` gère les **limites de ressources** pour les processus utilisateur (mémoire, fichiers ouverts, CPU, etc.).

**Rôle** :
- ✅ Empêcher utilisateur/processus de monopoliser ressources système
- ✅ Protéger contre fork bombs, fuites mémoire
- ✅ Garantir stabilité système multi-utilisateurs

#### Syntaxe

```bash
ulimit [OPTIONS] [limite]
```

---

#### Option `-a` - Afficher Toutes les Limites

```bash
$ ulimit -a
real-time non-blocking time  (microseconds, -R) unlimited
core file size              (blocks, -c) 0
data seg size               (kbytes, -d) unlimited
scheduling priority                 (-e) 0
file size                   (blocks, -f) unlimited
pending signals                     (-i) 15390
max locked memory           (kbytes, -l) 250492
max memory size             (kbytes, -m) unlimited
open files                          (-n) 1024
pipe size                (512 bytes, -p) 8
POSIX message queues         (bytes, -q) 819200
real-time priority                  (-r) 0
stack size                  (kbytes, -s) 8192
cpu time                   (seconds, -t) unlimited
max user processes                  (-u) 15390
virtual memory              (kbytes, -v) unlimited
file locks                          (-x) unlimited
```

**Lecture** :
- Chaque ligne = Une limite
- Entre parenthèses : Option et unité
- Valeur : Limite actuelle (`unlimited` = pas de limite)

---

#### Options de Limites Spécifiques

| Option | Description | Unité | Exemple |
|--------|-------------|-------|---------|
| `-c` | Taille fichiers **core dump** | Blocs (512 octets) | `ulimit -c 1000` |
| `-d` | Taille segment **data** processus | Ko | `ulimit -d 1000000` |
| `-f` | Taille max **fichiers créés** | Blocs (512 octets) | `ulimit -f 2000000` |
| `-l` | Mémoire **verrouillable** (locked) | Ko | `ulimit -l 64` |
| `-m` | Taille max **mémoire résidente** (RSS) | Ko | `ulimit -m 500000` |
| `-n` | Nombre **fichiers ouverts** (file descriptors) | Nombre | `ulimit -n 2048` |
| `-s` | Taille **pile** (stack) processus | Ko | `ulimit -s 10240` |
| `-t` | Temps **CPU** max | Secondes | `ulimit -t 3600` |
| `-u` | Nombre max **processus utilisateur** | Nombre | `ulimit -u 512` |
| `-v` | Mémoire **virtuelle** max | Ko | `ulimit -v 2000000` |
| `-x` | Nombre max **verrous fichiers** | Nombre | `ulimit -x 100` |

---

#### Afficher et Modifier Limites

**Afficher limite spécifique** :

```bash
# Fichiers ouverts
$ ulimit -n
1024

# Processus utilisateur
$ ulimit -u
15390

# Taille fichiers
$ ulimit -f
unlimited

# Core dump
$ ulimit -c
0  # Désactivé
```

**Modifier limite pour shell actuel** :

```bash
# Augmenter fichiers ouverts à 2048
$ ulimit -n 2048
$ ulimit -n
2048

# Limiter temps CPU à 1 heure (3600s)
$ ulimit -t 3600

# Activer core dumps (taille max 100 blocs)
$ ulimit -c 100

# Limiter processus à 512
$ ulimit -u 512
```

⚠️ **Temporaire** : Limites modifiées avec `ulimit` **ne persistent pas** (perdues à fermeture shell).

---

#### Limites Hard vs Soft

**Deux types de limites** :

1. **Soft Limit** (limite souple)
   - Limite **actuelle** appliquée
   - Utilisateur peut **augmenter** jusqu'à hard limit
   - Plus facile à modifier

2. **Hard Limit** (limite dure)
   - Limite **maximale absolue**
   - Seul **root** peut augmenter
   - Plafond non dépassable

**Options** :

```bash
# Afficher soft limit (défaut)
$ ulimit -n
1024

# Afficher hard limit
$ ulimit -Hn
4096

# Afficher soft limit explicitement
$ ulimit -Sn
1024
```

**Modifier** :

```bash
# Modifier soft limit (utilisateur normal)
$ ulimit -Sn 2048
$ ulimit -n
2048

# Modifier hard limit (root requis)
$ sudo ulimit -Hn 8192
$ ulimit -Hn
8192

# Tentative dépassement hard limit (utilisateur)
$ ulimit -n 10000
bash: ulimit: open files: cannot modify limit: Operation not permitted
```

---

### ⚙️ Configuration Permanente - Fichier `/etc/security/limits.conf`

#### Description

Pour rendre limites **permanentes** (persistent après reboot/logout), configurer `/etc/security/limits.conf`.

#### Format du Fichier

```
<domain>  <type>  <item>  <value>
```

**Champs** :

- **`<domain>`** : Utilisateur, groupe, ou wildcard
  - `alice` = Utilisateur alice
  - `@team` = Groupe team (@ = groupe)
  - `*` = Tous les utilisateurs

- **`<type>`** : Type de limite
  - `soft` = Limite souple
  - `hard` = Limite dure
  - `-` = Soft ET hard

- **`<item>`** : Ressource limitée
  - `nofile` = Nombre fichiers ouverts
  - `nproc` = Nombre processus
  - `core` = Taille core dump
  - `cpu` = Temps CPU (minutes)
  - `fsize` = Taille fichiers
  - `memlock` = Mémoire verrouillable
  - `stack` = Taille pile
  - Etc.

- **`<value>`** : Valeur limite (nombre ou `unlimited`)

---

#### Exemples de Configuration

**Fichier `/etc/security/limits.conf`** :

```bash
$ sudo cat /etc/security/limits.conf
# /etc/security/limits.conf
#
#<domain>      <type>  <item>         <value>
#

# Limiter core dumps pour tous
*               soft    core            0
*               hard    core            0

# Augmenter fichiers ouverts pour alice
alice           soft    nofile          2048
alice           hard    nofile          4096

# Limiter processus pour groupe students
@students       soft    nproc           512
@students       hard    nproc           1024

# Limiter temps CPU pour bob (60 min)
bob             soft    cpu             60
bob             hard    cpu             60

# Groupe webadmin : nombreux fichiers ouverts
@webadmin       soft    nofile          8192
@webadmin       hard    nofile          16384

# Serveur base de données : mémoire verrouillable
@dba            soft    memlock         unlimited
@dba            hard    memlock         unlimited

# Limites pour service nginx (via user www-data)
www-data        soft    nofile          10000
www-data        hard    nofile          20000
```

---

#### Scopes/Domains

**Utilisateur spécifique** :

```
alice           soft    nofile          2048
```

**Groupe** (préfixe `@`) :

```
@developers     soft    nproc           1024
```

**Tous les utilisateurs** (`*`) :

```
*               hard    core            0
```

**Wildcard avec exceptions** :

```bash
# Tous : max 512 processus
*               hard    nproc           512

# Sauf alice : 2048 processus
alice           hard    nproc           2048
```

---

#### Appliquer les Modifications

**Méthode 1** : Logout/Login

```bash
# 1. Modifier fichier
$ sudo nano /etc/security/limits.conf

# 2. Déconnexion/reconnexion
$ logout
# (Se reconnecter)

# 3. Vérifier
$ ulimit -n
2048  # ✅ Nouvelle limite appliquée
```

**Méthode 2** : Nouveau shell avec su

```bash
# Modifier fichier
$ sudo nano /etc/security/limits.conf

# Ouvrir nouveau shell
$ su - $USER
Password:

# Vérifier
$ ulimit -n
2048
```

**Méthode 3** : Redémarrer service (pour daemons)

```bash
# Pour service systemd
$ sudo systemctl restart nginx
```

---

#### Vérifier Limites Actives d'un Processus

**Via `/proc/[PID]/limits`** :

```bash
# Trouver PID du processus
$ ps aux | grep nginx
www-data  1234 ...

# Voir limites
$ cat /proc/1234/limits
Limit                     Soft Limit           Hard Limit           Units
Max cpu time              unlimited            unlimited            seconds
Max file size             unlimited            unlimited            bytes
Max data size             unlimited            unlimited            bytes
Max stack size            8388608              unlimited            bytes
Max core file size        0                    0                    bytes
Max resident set          unlimited            unlimited            bytes
Max processes             15390                15390                processes
Max open files            10000                20000                files
Max locked memory         250492000            250492000            bytes
Max address space         unlimited            unlimited            bytes
Max file locks            unlimited            unlimited            locks
Max pending signals       15390                15390                signals
Max msgqueue size         819200               819200               bytes
Max nice priority         0                    0
Max realtime priority     0                    0
Max realtime timeout      unlimited            unlimited            us
```

---

#### Exemples Pratiques Limites

**1. Prévenir fork bomb** :

```bash
# /etc/security/limits.conf
@users          hard    nproc           1024
```

Fork bomb : `:(){ :|:& };:` crée processus infinis → Bloqué à 1024 processus max.

**2. Serveur web haute performance** :

```bash
# /etc/security/limits.conf
www-data        soft    nofile          10000
www-data        hard    nofile          20000
```

Nginx/Apache gérant milliers de connexions simultanées.

**3. Développeur avec compilation intensive** :

```bash
@developers     soft    nproc           2048
@developers     hard    nproc           4096
@developers     soft    nofile          4096
@developers     hard    nofile          8192
```

---

## 🎯 Partie 3 : Vérification Ports Ouverts et Scan Réseau

### 🔍 Outils de Diagnostic Local

#### Commande `netstat` - Network Statistics

**Rappel** : Déjà couvert dans 109.3, focus ici sur sécurité.

**Options utiles pour sécurité** :

```bash
# -t : TCP
# -u : UDP  
# -n : Numérique (pas de résolution DNS)
# -a : Tous (listening + established)

# Combinaison magique
$ sudo netstat -tuna
```

**Exemple sortie** :

```bash
$ sudo netstat -tuna
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN
tcp        0      0 192.168.1.10:22         192.168.1.50:54321      ESTABLISHED
tcp6       0      0 :::80                   :::*                    LISTEN
udp        0      0 0.0.0.0:68              0.0.0.0:*
udp        0      0 0.0.0.0:123             0.0.0.0:*
```

**Interprétation sécurité** :
- `0.0.0.0:22` → SSH écoute **toutes interfaces** (exposé réseau)
- `127.0.0.1:3306` → MySQL écoute **localhost uniquement** (sécurisé, pas exposé)
- `0.0.0.0:80` → HTTP exposé publiquement
- `192.168.1.10:22` ESTABLISHED → Connexion SSH active

**Trouver services non sécurisés** :

```bash
# Services écoutant sur toutes interfaces (potentiellement exposés)
$ sudo netstat -tuln | grep "0.0.0.0"
tcp  0  0  0.0.0.0:22     0.0.0.0:*  LISTEN
tcp  0  0  0.0.0.0:80     0.0.0.0:*  LISTEN
```

---

#### Commande `ss` - Socket Statistics

**Plus rapide et moderne** que `netstat`.

**Options identiques** :

```bash
$ sudo ss -tuna
Netid  State   Recv-Q  Send-Q    Local Address:Port     Peer Address:Port
tcp    LISTEN  0       128       0.0.0.0:22            0.0.0.0:*
tcp    LISTEN  0       128       127.0.0.1:3306        0.0.0.0:*
tcp    LISTEN  0       128       0.0.0.0:80            0.0.0.0:*
tcp    ESTAB   0       0         192.168.1.10:22       192.168.1.50:54321
```

**Avec processus (`-p`)** :

```bash
$ sudo ss -tulnp
Netid  State   Recv-Q  Send-Q  Local Address:Port  Peer Address:Port  Process
tcp    LISTEN  0       128     0.0.0.0:22         0.0.0.0:*          users:(("sshd",pid=1234,fd=3))
tcp    LISTEN  0       128     127.0.0.1:3306     0.0.0.0:*          users:(("mysqld",pid=5678,fd=10))
tcp    LISTEN  0       128     0.0.0.0:80         0.0.0.0:*          users:(("apache2",pid=9012,fd=4))
```

**Identifier services suspects** :

```bash
# Services écoutant sur ports non standards
$ sudo ss -tulnp | grep -v ":22\|:80\|:443\|:3306"
tcp  LISTEN  0  128  0.0.0.0:8888  0.0.0.0:*  users:(("unknown",pid=6666,fd=3))
#  ^^^
#  Port 8888 suspect ? Processus "unknown" ?
```

---

#### Commande `lsof` - List Open Files

**Rôle** : Lister **tous les fichiers ouverts** (sockets réseau inclus).

**Option `-i` : Internet sockets** (réseau)

```bash
# Tous les sockets réseau
$ sudo lsof -i
COMMAND   PID     USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
sshd      1234    root    3u  IPv4  12345      0t0  TCP *:ssh (LISTEN)
apache2   5678    root    4u  IPv4  23456      0t0  TCP *:http (LISTEN)
mysqld    9012   mysql   10u  IPv4  34567      0t0  TCP localhost:mysql (LISTEN)
firefox   1111   alice   45u  IPv4  45678      0t0  TCP 192.168.1.10:54321->93.184.216.34:https (ESTABLISHED)
```

**Filtrer par port** :

```bash
# Qui écoute sur port 80 ?
$ sudo lsof -i :80
COMMAND   PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
apache2   5678  root    4u  IPv4  23456      0t0  TCP *:http (LISTEN)
apache2   5679 www-data 4u  IPv4  23456      0t0  TCP *:http (LISTEN)

# Qui écoute sur port 3306 ?
$ sudo lsof -i :3306
COMMAND   PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
mysqld    9012 mysql  10u  IPv4  34567      0t0  TCP localhost:mysql (LISTEN)
```

**Filtrer par protocole** :

```bash
# Sockets TCP
$ sudo lsof -i tcp

# Sockets UDP
$ sudo lsof -i udp

# TCP sur port 22
$ sudo lsof -i tcp:22
```

**Filtrer par état** :

```bash
# Connexions LISTEN
$ sudo lsof -i -sTCP:LISTEN

# Connexions ESTABLISHED
$ sudo lsof -i -sTCP:ESTABLISHED
```

**Filtrer par utilisateur** :

```bash
# Sockets ouverts par alice
$ sudo lsof -i -u alice

# Sockets ouverts par www-data
$ sudo lsof -i -u www-data
```

**Combinaisons** :

```bash
# TCP LISTEN par root
$ sudo lsof -i tcp -sTCP:LISTEN -u root

# Connexions vers port 443 (HTTPS sortant)
$ sudo lsof -i tcp:443 -sTCP:ESTABLISHED
```

---

#### Commande `fuser` - Find User of Files/Sockets

**Rôle** : Identifier **processus utilisant** un fichier/socket.

**Syntaxe pour ports** :

```bash
fuser <port>/<protocol>
```

**Exemples** :

```bash
# Qui utilise port 22 (SSH) ?
$ sudo fuser 22/tcp
22/tcp:               1234

# Afficher PID seulement
$ sudo fuser -n tcp 22
22/tcp:               1234

# Avec détails verbeux (-v)
$ sudo fuser -v 22/tcp
                     USER        PID ACCESS COMMAND
22/tcp:              root       1234 F.... sshd

# Qui utilise port 80 ?
$ sudo fuser -v 80/tcp
                     USER        PID ACCESS COMMAND
80/tcp:              root       5678 F.... apache2
                     www-data   5679 F.... apache2
                     www-data   5680 F.... apache2
```

**Options** :

| Option | Description |
|--------|-------------|
| `-v` | Verbose (afficher détails) |
| `-n <space>` | Namespace (tcp, udp, file) |
| `-k` | **Kill** processus utilisant ressource |
| `-i` | Interactif (demander confirmation avant kill) |

**Tuer processus utilisant port** :

```bash
# Tuer processus sur port 8080
$ sudo fuser -k 8080/tcp
8080/tcp:             6666
# Processus 6666 tué

# Avec confirmation
$ sudo fuser -ki 8080/tcp
8080/tcp:             6666
Kill process 6666 ? (y/N) y
```

---

### 🌐 Scan Réseau - Commande `nmap`

#### Description

**Nmap** (Network Mapper) = **Scanner de ports** et **découverte réseau** le plus puissant.

**Usages** :
- ✅ Scanner ports ouverts sur machine distante
- ✅ Identifier services/versions
- ✅ Détecter OS (fingerprinting)
- ✅ Découvrir hôtes réseau
- ✅ Audit sécurité

⚠️ **Légalité** : Scanner **uniquement vos propres machines** ou avec autorisation écrite. Scanner réseau tiers = **ILLÉGAL**.

---

#### Installation

```bash
# Debian/Ubuntu
$ sudo apt install nmap

# RHEL/CentOS
$ sudo yum install nmap

# Vérifier version
$ nmap --version
Nmap version 7.94
```

---

#### Syntaxe de Base

```bash
nmap [OPTIONS] <cible>
```

**Cibles** :
- Adresse IP : `192.168.1.10`
- Nom de domaine : `example.com`
- Plage IP : `192.168.1.1-254`
- Réseau CIDR : `192.168.1.0/24`
- Multiples : `192.168.1.10 192.168.1.20`
- Fichier : `-iL hosts.txt`

---

#### Types de Scans

| Option | Type de Scan | Description | Stealthiness |
|--------|--------------|-------------|--------------|
| `-sT` | **TCP Connect** | Scan TCP complet (3-way handshake) | ❌ Bruyant, loggé |
| `-sS` | **SYN Stealth** | Demi-connexion (SYN sans ACK final) | ✅ Discret (root requis) |
| `-sU` | **UDP** | Scan ports UDP | ⏱️ Lent |
| `-sA` | **ACK** | Détection firewall | 🔍 Avancé |
| `-sN` | **NULL** | Pas de flags TCP | 🥷 Furtif |
| `-sF` | **FIN** | Flag FIN uniquement | 🥷 Furtif |
| `-sX` | **Xmas** | Flags FIN, PSH, URG | 🥷 Furtif |

---

#### Scan TCP Connect (`-sT`)

**Scan par défaut sans root** : Connexion TCP complète.

```bash
# Scan ports par défaut (1000 plus courants)
$ nmap 192.168.1.10
Starting Nmap 7.94
Nmap scan report for 192.168.1.10
Host is up (0.00023s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
443/tcp open  https

Nmap done: 1 IP address (1 host up) scanned in 0.15 seconds
```

**Interprétation** :
- **22/tcp open** : SSH accessible
- **80/tcp open** : HTTP accessible
- **443/tcp open** : HTTPS accessible
- **997 closed** : 997 autres ports fermés

---

#### Scan SYN Stealth (`-sS`)

**Scan par défaut avec root** : Demi-connexion (plus discret).

```bash
$ sudo nmap -sS 192.168.1.10
Starting Nmap 7.94
Nmap scan report for 192.168.1.10
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
443/tcp open  https
```

**Fonctionnement** :
1. Envoie SYN
2. Reçoit SYN-ACK (port ouvert) → **N'envoie PAS ACK final**
3. Connexion jamais complétée → Moins de logs

---

#### Scan UDP (`-sU`)

**Scan ports UDP** (DNS, DHCP, SNMP, etc.) :

```bash
$ sudo nmap -sU 192.168.1.10
PORT    STATE         SERVICE
53/udp  open|filtered dns
123/udp open          ntp
161/udp open          snmp
```

**Notes** :
- UDP = Lent (pas d'accusé réception)
- `open|filtered` = Pas de réponse (port ouvert ou filtré par firewall)

---

#### Plages de Ports

Par défaut, nmap scan **1000 ports les plus courants**.

**Options de plage** :

```bash
# Tous les ports (1-65535)
$ nmap -p- 192.168.1.10

# Ports spécifiques
$ nmap -p 22,80,443 192.168.1.10

# Plage de ports
$ nmap -p 1-1000 192.168.1.10

# Top 100 ports
$ nmap --top-ports 100 192.168.1.10

# Ports UDP et TCP
$ sudo nmap -p U:53,161,T:22,80 192.168.1.10
```

---

#### Détection de Services et Versions (`-sV`)

**Identifier versions logiciels** :

```bash
$ sudo nmap -sV 192.168.1.10
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
80/tcp  open  http    Apache httpd 2.4.52 ((Ubuntu))
443/tcp open  ssl/http Apache httpd 2.4.52 ((Ubuntu))
3306/tcp open  mysql   MySQL 8.0.32-0ubuntu0.22.04.2
```

**Informations** :
- Version OpenSSH : 8.9p1
- Version Apache : 2.4.52
- OS : Ubuntu
- Version MySQL : 8.0.32

💡 Utile pour identifier **versions vulnérables**.

---

#### Détection d'OS (`-O`)

**Fingerprinting système d'exploitation** :

```bash
$ sudo nmap -O 192.168.1.10
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http

Device type: general purpose
Running: Linux 5.X
OS CPE: cpe:/o:linux:linux_kernel:5
OS details: Linux 5.4 - 5.10
Network Distance: 1 hop
```

**Précision** : Basé sur stack TCP/IP, pas toujours 100% précis.

---

#### Détection Agressive (`-A`)

**Combine plusieurs détections** : Version, OS, scripts NSE, traceroute.

```bash
$ sudo nmap -A 192.168.1.10
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 8.9p1
|_ssh-hostkey: RSA: 2048 12:34:56:...
80/tcp  open  http    Apache httpd 2.4.52
|_http-title: Welcome to Ubuntu
|_http-server-header: Apache/2.4.52 (Ubuntu)

Device type: general purpose
Running: Linux 5.X
OS details: Linux 5.4 - 5.10
```

⚠️ **Bruyant** : Génère beaucoup de trafic, facilement détecté.

---

#### Vitesse de Scan

**Templates de timing** (`-T0` à `-T5`) :

| Option | Nom | Vitesse | Discrétion | Usage |
|--------|-----|---------|------------|-------|
| `-T0` | Paranoid | 🐌 Très lent | 🥷 Maximum | IDS evasion |
| `-T1` | Sneaky | 🐌 Lent | 🥷 Élevée | IDS evasion |
| `-T2` | Polite | 🐢 Modéré | ✅ Bonne | Peu de bande passante |
| `-T3` | Normal | ⚡ Normal | 🆗 Moyenne | **Défaut** |
| `-T4` | Aggressive | ⚡⚡ Rapide | ❌ Faible | Réseau rapide |
| `-T5` | Insane | ⚡⚡⚡ Très rapide | ❌ Nulle | Réseau local haute vitesse |

**Exemples** :

```bash
# Scan lent discret
$ sudo nmap -sS -T1 192.168.1.10

# Scan rapide (réseau local)
$ sudo nmap -sS -T4 192.168.1.0/24

# Scan maximum vitesse
$ sudo nmap -sS -T5 192.168.1.10
```

---

#### Découverte d'Hôtes

**Scanner tout un réseau** :

```bash
# Scan réseau /24 (254 hôtes)
$ sudo nmap 192.168.1.0/24
Nmap scan report for 192.168.1.1
Host is up (0.0010s latency).

Nmap scan report for 192.168.1.10
Host is up (0.0005s latency).

Nmap scan report for 192.168.1.50
Host is up (0.0020s latency).

Nmap done: 254 IP addresses (3 hosts up) scanned in 2.45 seconds
```

**Ping scan uniquement** (pas de scan ports) :

```bash
# Découvrir hôtes actifs sans scanner ports
$ sudo nmap -sn 192.168.1.0/24
Nmap scan report for 192.168.1.1
Host is up (0.0010s latency).
...
Nmap done: 254 IP addresses (15 hosts up) scanned in 3.12 seconds
```

---

#### Scripts NSE (Nmap Scripting Engine)

**Scripts d'audit avancés** :

```bash
# Script par défaut
$ sudo nmap -sC 192.168.1.10

# Script spécifique
$ sudo nmap --script vuln 192.168.1.10
# Cherche vulnérabilités connues

# Script HTTP
$ sudo nmap --script http-enum 192.168.1.10
# Énumère répertoires web

# Script brute-force SSH
$ sudo nmap --script ssh-brute 192.168.1.10
```

**Lister scripts disponibles** :

```bash
$ ls /usr/share/nmap/scripts/ | head
http-enum.nse
ssh-brute.nse
vuln.nse
...
```

---

#### Options de Sortie

```bash
# Sortie normale (console)
$ nmap 192.168.1.10

# Sortie dans fichier texte
$ nmap 192.168.1.10 -oN scan.txt

# Sortie XML (parseable)
$ nmap 192.168.1.10 -oX scan.xml

# Sortie grepable (1 ligne par port)
$ nmap 192.168.1.10 -oG scan.grep

# Toutes les sorties à la fois
$ nmap 192.168.1.10 -oA scan
# Génère : scan.nmap, scan.xml, scan.gnmap
```

---

#### Exemples Pratiques

**1. Audit sécurité complet** :

```bash
$ sudo nmap -sS -sV -O -p- -T4 -A --script vuln -oA audit_complet 192.168.1.10
```

- `-sS` : SYN scan
- `-sV` : Détection versions
- `-O` : Détection OS
- `-p-` : Tous les ports
- `-T4` : Rapide
- `-A` : Agressif
- `--script vuln` : Scripts vulnérabilités
- `-oA` : Toutes sorties

**2. Découvrir serveurs web réseau** :

```bash
$ sudo nmap -p 80,443,8080 --open 192.168.1.0/24
```

- `--open` : Seulement ports ouverts

**3. Scan discret IDS evasion** :

```bash
$ sudo nmap -sS -T1 -f --randomize-hosts 192.168.1.0/24
```

- `-T1` : Lent
- `-f` : Fragmenter paquets
- `--randomize-hosts` : Ordre aléatoire

**4. Scan UDP services critiques** :

```bash
$ sudo nmap -sU -p 53,161,162,123,69 192.168.1.10
```

**5. Vérifier firewall** :

```bash
$ sudo nmap -sA -p 80,443 192.168.1.10
# ACK scan détecte règles firewall
```

---

## 🛠️ Scénarios Pratiques

### Scénario 1 : Ajouter Utilisateur au Groupe sudo

```bash
# 1. Créer utilisateur
$ sudo useradd -m -s /bin/bash charlie

# 2. Définir mot de passe
$ sudo passwd charlie

# 3. Ajouter au groupe sudo
$ sudo usermod -aG sudo charlie

# 4. Vérifier
$ groups charlie
charlie : charlie sudo

# 5. Tester
$ su - charlie
charlie@server:~$ sudo apt update
[sudo] password for charlie:
# ✅ Fonctionne !
```

---

### Scénario 2 : Configurer SGID sur Répertoire Partagé

```bash
# 1. Créer répertoire et groupe
$ sudo mkdir /shared/project
$ sudo groupadd developers

# 2. Ajouter membres au groupe
$ sudo usermod -aG developers alice
$ sudo usermod -aG developers bob

# 3. Configurer permissions
$ sudo chown root:developers /shared/project
$ sudo chmod 2770 /shared/project
#              └─ SGID + rwxrwx---

# 4. Vérifier
$ ls -ld /shared/project
drwxrws--- 2 root developers 4096 Feb 12 10:00 /shared/project
#     └─ s = SGID

# 5. Tester
$ su - alice
alice$ touch /shared/project/file_alice.txt
alice$ ls -l /shared/project/file_alice.txt
-rw-r--r-- 1 alice developers 0 Feb 12 10:00 file_alice.txt
#                   └─ Groupe developers hérité ✅
```

---

### Scénario 3 : Augmenter Limite Fichiers Ouverts pour Nginx

```bash
# 1. Vérifier limite actuelle
$ su - www-data
$ ulimit -n
1024  # Trop bas pour serveur haute charge

# 2. Configurer limite permanente
$ sudo nano /etc/security/limits.conf
# Ajouter :
www-data        soft    nofile          10000
www-data        hard    nofile          20000

# 3. Redémarrer nginx
$ sudo systemctl restart nginx

# 4. Vérifier limite appliquée
$ cat /proc/$(pgrep nginx | head -1)/limits | grep "open files"
Max open files            10000                20000                files
```

---

### Scénario 4 : Identifier Processus Utilisant Port Suspect

```bash
# 1. Découvrir port suspect
$ sudo ss -tulnp | grep -v ":22\|:80\|:443"
tcp  LISTEN  0  128  0.0.0.0:8888  0.0.0.0:*  users:(("unknown",pid=6666,fd=3))

# 2. Identifier avec lsof
$ sudo lsof -i :8888
COMMAND   PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
unknown   6666 alice   3u  IPv4  45678      0t0  TCP *:8888 (LISTEN)

# 3. Vérifier processus
$ ps aux | grep 6666
alice     6666  0.1  0.2  12345  6789 ?  S    10:00   0:01 /tmp/backdoor

# 4. Tuer processus malveillant
$ sudo kill -9 6666

# 5. Vérifier disparition
$ sudo lsof -i :8888
# (aucun résultat = port libéré)
```

---

### Scénario 5 : Scanner Réseau pour Découvrir Serveurs SSH

```bash
# 1. Découvrir hôtes actifs
$ sudo nmap -sn 192.168.1.0/24
Nmap scan report for 192.168.1.10
Host is up.
Nmap scan report for 192.168.1.20
Host is up.
# ...

# 2. Scanner port SSH (22)
$ sudo nmap -p 22 --open 192.168.1.0/24
PORT   STATE SERVICE
22/tcp open  ssh
Nmap scan report for 192.168.1.10

PORT   STATE SERVICE
22/tcp open  ssh
Nmap scan report for 192.168.1.20

# 3. Identifier versions SSH
$ sudo nmap -sV -p 22 192.168.1.10 192.168.1.20
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu
Nmap scan report for 192.168.1.10

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.4

# 4. Machine 192.168.1.20 a vieille version (7.4) → Vulnérable !
```

---

## ❌ Erreurs Courantes et Solutions

### Erreur 1 : `sudo` ne fonctionne pas pour utilisateur

```bash
$ sudo apt update
alice is not in the sudoers file. This incident will be reported.
```

**Cause** : Utilisateur pas dans groupe sudo

**Solution** :

```bash
# Se connecter en root
$ su -
Password:

# Ajouter au groupe sudo
# usermod -aG sudo alice

# Ou modifier /etc/sudoers
# visudo
# Ajouter :
alice   ALL=(ALL:ALL) ALL

# Vérifier
# groups alice
alice : alice sudo
```

---

### Erreur 2 : Fichier SUID ne fonctionne pas

```bash
$ ls -l /usr/local/bin/script.sh
-rwsr-xr-x 1 root root 1234 Feb 12 10:00 /usr/local/bin/script.sh

$ ./script.sh
# S'exécute avec permissions alice, pas root !
```

**Cause** : Noyau Linux **ignore SUID sur scripts shell** (sécurité)

**Solution** : Wrapper en C

```c
// wrapper.c
#include <unistd.h>

int main() {
    setuid(0);  // root
    execl("/usr/local/bin/script.sh", "script.sh", NULL);
    return 0;
}
```

```bash
$ gcc wrapper.c -o wrapper
$ sudo chown root:root wrapper
$ sudo chmod 4755 wrapper
$ ./wrapper
# ✅ S'exécute en root
```

---

### Erreur 3 : `ulimit` modifications ignorées après logout

```bash
$ ulimit -n 4096
$ ulimit -n
4096

# Logout/login
$ ulimit -n
1024  # ❌ Revenu à défaut
```

**Cause** : `ulimit` temporaire (shell actuel uniquement)

**Solution** : Configurer `/etc/security/limits.conf`

```bash
$ sudo nano /etc/security/limits.conf
alice           soft    nofile          4096
alice           hard    nofile          8192
```

---

### Erreur 4 : `nmap` bloqué par firewall

```bash
$ sudo nmap -sS 192.168.1.10
All 1000 scanned ports on 192.168.1.10 are filtered
```

**Cause** : Firewall bloque scan

**Solutions** :

```bash
# 1. Essayer scan TCP Connect (moins suspect)
$ nmap -sT 192.168.1.10

# 2. Scan fragmenté (contourne certains firewalls)
$ sudo nmap -f 192.168.1.10

# 3. Scan lent discret
$ sudo nmap -T1 192.168.1.10

# 4. Utiliser différentes méthodes
$ sudo nmap -sN 192.168.1.10  # NULL scan
$ sudo nmap -sF 192.168.1.10  # FIN scan
```

---

### Erreur 5 : Permission denied lors `fuser -k`

```bash
$ fuser -k 8080/tcp
Cannot kill process 6666: Operation not permitted
```

**Cause** : Processus appartient à autre utilisateur

**Solution** :

```bash
$ sudo fuser -k 8080/tcp
# ✅ Fonctionne avec sudo
```

---

## 📝 Cheat Sheet - Administration Sécurité

### Élévation Privilèges

```bash
su                                # Passer root (shell non-login)
su -                              # Passer root (shell login)
su - alice                        # Passer utilisateur alice (login)
su alice -c "command"             # Exécuter commande en tant qu'alice

sudo command                      # Exécuter commande en root
sudo -i                           # Shell root (login)
sudo -s                           # Shell root (non-login)
sudo -u bob command               # Exécuter en tant que bob
sudo visudo                       # Éditer /etc/sudoers
```

---

### Gestion Mots de Passe

```bash
passwd                            # Changer son mdp
sudo passwd alice                 # Changer mdp alice
passwd -S alice                   # Status mdp alice
sudo passwd -l bob                # Verrouiller compte bob
sudo passwd -u bob                # Déverrouiller bob
sudo passwd -e alice              # Forcer changement mdp

sudo chage -l alice               # Infos expiration alice
sudo chage -M 90 alice            # Expiration mdp 90 jours
sudo chage -m 7 alice             # Min 7 jours entre changements
sudo chage -W 14 alice            # Avertissement 14 jours
sudo chage -I 30 alice            # Inactivité 30 jours
sudo chage -E 2026-12-31 alice    # Expiration compte 31 déc
sudo chage -d 0 alice             # Forcer changement immédiat
```

---

### Permissions Spéciales

```bash
# SUID
chmod u+s fichier                 # Activer SUID
chmod 4755 fichier                # SUID + rwxr-xr-x
find /usr/bin -perm -4000         # Trouver fichiers SUID

# SGID
chmod g+s fichier                 # Activer SGID
chmod 2755 fichier                # SGID + rwxr-xr-x
chmod g+s /shared                 # SGID sur répertoire

# Sticky Bit
chmod +t /shared                  # Activer sticky bit
chmod 1777 /tmp                   # Sticky + rwxrwxrwx
```

---

### Limites Ressources

```bash
ulimit -a                         # Toutes limites
ulimit -n                         # Fichiers ouverts
ulimit -u                         # Processus max
ulimit -n 2048                    # Définir fichiers ouverts

ulimit -Hn                        # Hard limit fichiers
ulimit -Sn                        # Soft limit fichiers

# Permanent
sudo nano /etc/security/limits.conf
alice           soft    nofile          4096
@developers     hard    nproc           2048
```

---

### Ports et Connexions

```bash
# netstat (obsolète)
sudo netstat -tulnp               # Ports écoute avec processus
sudo netstat -tuna                # Tous sockets TCP/UDP

# ss (moderne)
sudo ss -tulnp                    # Ports écoute avec processus
sudo ss -tuna                     # Tous sockets
sudo ss -tn state established     # Connexions établies

# lsof
sudo lsof -i                      # Tous sockets réseau
sudo lsof -i :80                  # Port 80
sudo lsof -i tcp                  # Sockets TCP
sudo lsof -i -sTCP:LISTEN         # Ports écoute
sudo lsof -i -u alice             # Sockets utilisateur alice

# fuser
sudo fuser -v 22/tcp              # Processus utilisant port 22
sudo fuser -k 8080/tcp            # Tuer processus sur port 8080
```

---

### Scan Réseau (nmap)

```bash
# Scans basiques
nmap 192.168.1.10                 # Scan ports courants
sudo nmap -sS 192.168.1.10        # SYN scan (stealth)
sudo nmap -sU 192.168.1.10        # Scan UDP

# Plages ports
nmap -p- 192.168.1.10             # Tous ports
nmap -p 22,80,443 192.168.1.10    # Ports spécifiques
nmap --top-ports 100 192.168.1.10 # Top 100 ports

# Détections
sudo nmap -sV 192.168.1.10        # Versions services
sudo nmap -O 192.168.1.10         # OS fingerprinting
sudo nmap -A 192.168.1.10         # Scan agressif complet

# Réseau
sudo nmap 192.168.1.0/24          # Scanner réseau /24
sudo nmap -sn 192.168.1.0/24      # Ping scan (découverte hôtes)

# Options avancées
sudo nmap -T4 192.168.1.10        # Scan rapide
sudo nmap --script vuln 192.168.1.10  # Scripts vulnérabilités
nmap -oA scan 192.168.1.10        # Sauvegarder résultats
```

---

## 🎯 Points Clés pour l'Examen LPIC-1

### Ce qu'il faut maîtriser ✅

1. **`su` vs `sudo`** 🔑
   - `su` : Mot de passe cible, `su -` login shell
   - `sudo` : Mot de passe actuel, plus sûr
   - Groupe `sudo` (Debian) ou `wheel` (RHEL)

2. **Fichier `/etc/sudoers`** ⚙️
   - Éditer avec `visudo` UNIQUEMENT
   - Syntaxe : `user hosts=(runas) commands`
   - `NOPASSWD:` pour scripts automatisés
   - Fichiers `/etc/sudoers.d/` recommandés

3. **Gestion mots de passe** 🔒
   - `passwd -S` : Status
   - `passwd -l/-u` : Lock/Unlock
   - `passwd -e` : Forcer expiration
   - `chage -M/-m/-W/-I/-E` : Politique expiration

4. **SUID et SGID** 🔐
   - SUID (`4xxx`, `u+s`) : Exécute avec UID propriétaire
   - SGID fichier (`2xxx`, `g+s`) : Exécute avec GID groupe
   - SGID répertoire : Héritage groupe
   - Sticky bit (`1xxx`, `+t`) : Protection suppression

5. **`ulimit`** 📊
   - `-a` : Toutes limites
   - `-n` : Fichiers ouverts
   - `-u` : Processus max
   - Configuration permanente : `/etc/security/limits.conf`
   - Soft vs hard limits

6. **Vérification ports** 🔍
   - `ss -tulnp` (moderne) ou `netstat -tulnp` (obsolète)
   - `lsof -i :port` : Identifier processus
   - `fuser -v port/tcp` : Qui utilise port

7. **`nmap`** 🌐
   - Types scans : `-sS` (SYN), `-sU` (UDP), `-sT` (TCP Connect)
   - Détections : `-sV` (versions), `-O` (OS), `-A` (agressif)
   - Plages : `-p-`, `-p 22,80,443`, `--top-ports 100`
   - Vitesse : `-T0` à `-T5`

---

### Pièges Fréquents à l'Examen ⚠️

1. **Oublier `-` avec `su`** :
   - `su` ≠ `su -`
   - Sans `-` : Environnement hérité (incorrect)

2. **Modifier `/etc/sudoers` sans `visudo`** :
   - Erreur syntaxe → sudo cassé → Impossible corriger !

3. **Confondre SUID fichier vs SGID répertoire** :
   - SUID fichier : Exécution avec UID proprio
   - SGID répertoire : Héritage groupe (usage courant)

4. **`ulimit` temporaire** :
   - Modifications shell actuel uniquement
   - Permanent : `/etc/security/limits.conf`

5. **Soft vs hard limit** :
   - Utilisateur peut augmenter soft jusqu'à hard
   - Seul root peut augmenter hard

6. **`netstat` vs `ss`** :
   - `ss` moderne, plus rapide
   - Syntaxe identique : `-tulnp`

7. **Scan `nmap` sans `sudo`** :
   - `-sS`, `-sU`, `-O` nécessitent root
   - Sans root : `-sT` (TCP Connect) utilisé

8. **SUID sur shell scripts** :
   - Noyau Linux **ignore SUID** sur scripts shell
   - Solution : Wrapper C

---

### Commandes à Connaître par Cœur 💯

```bash
su -                              # Root login shell
sudo command                      # Exécuter avec privilèges root
sudo visudo                       # Éditer sudoers
passwd -S alice                   # Status mdp alice
sudo chage -l alice               # Info expiration alice
chmod u+s fichier                 # SUID
chmod g+s répertoire              # SGID répertoire
ulimit -a                         # Toutes limites
sudo ss -tulnp                    # Ports écoute + processus
sudo lsof -i :port                # Processus sur port
sudo fuser -v port/tcp            # Identifier processus
sudo nmap -sS cible               # SYN scan
sudo nmap -sV -O cible            # Versions + OS
```

---

**Poids du module** : 3 points  
**Difficulté** : ⭐⭐⭐ Moyenne  
**Temps de préparation recommandé** : 5-7 heures

🎯 **Focus examen** : Maîtriser différences `su`/`sudo`, savoir éditer `/etc/sudoers` avec `visudo`, comprendre SUID/SGID, configurer `ulimit` permanent, et scanner réseau avec `nmap` !
