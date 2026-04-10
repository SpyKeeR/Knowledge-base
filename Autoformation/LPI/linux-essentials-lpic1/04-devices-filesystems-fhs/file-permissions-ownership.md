# 🔐 Gestion des Permissions et Propriétés de Fichiers

## 📋 Informations du Module

- **Module** : 4 - Devices, Linux Filesystems, Filesystem Hierarchy Standard
- **Cours** : Manage File Permissions and Ownership
- **Objectif** : Maîtriser les permissions Unix/Linux (rwx), la propriété (owner/group), les bits spéciaux (SUID/SGID/Sticky), et les commandes associées (chmod, chown, chgrp, umask, stat)

---

## 🎯 Concepts Fondamentaux

### 🏛️ Le Modèle de Permissions Unix

**Analogie** : Imaginez un immeuble avec des appartements :
- **Propriétaire (user)** = Locataire de l'appartement (droits complets)
- **Groupe (group)** = Famille du locataire (droits partagés)
- **Autres (others)** = Visiteurs de l'immeuble (droits limités)
- **Permissions rwx** = Clés différentes (lecture/écriture/accès)

**Philosophie Unix** : "Tout est fichier" → Les permissions s'appliquent à tout (fichiers, répertoires, périphériques, sockets, pipes)

### 📊 Structure des Permissions

**Format complet** : `type user group other`

```
-rwxr-xr--
│││││││││└─ Other : read
││││││││└── Other : no write
│││││││└─── Other : no execute
││││││└──── Group : read
│││││└───── Group : no write
││││└────── Group : execute
│││└─────── User  : read
││└──────── User  : write
│└───────── User  : execute
└────────── Type  : fichier régulier (-)
```

**Exemple complet** :
```bash
ls -l /bin/bash
-rwxr-xr-x 1 root root 1396520 Jan 15 10:00 /bin/bash
│││││││││└ Autres (r-x) : Lecture + Exécution
││││││└─── Groupe (r-x) : Lecture + Exécution
│││└────── Propriétaire (rwx) : Lecture + Écriture + Exécution
││└─────── Nombre de liens physiques : 1
│└──────── Propriétaire : root
└───────── Groupe : root
```

---

## 📁 Types de Fichiers Linux

### Caractère Indicateur de Type

| Symbole | Type | Description | Exemple |
|---------|------|-------------|---------|
| `-` | **Fichier régulier** | Fichier normal (texte, binaire, script) | `-rw-r--r-- document.txt` |
| `d` | **Directory** | Répertoire | `drwxr-xr-x /home/user` |
| `l` | **Symbolic Link** | Lien symbolique (raccourci) | `lrwxrwxrwx /usr/bin/python → python3.11` |
| `c` | **Character device** | Périphérique caractère (flux de données) | `crw-rw-rw- /dev/tty1` (terminal) |
| `b` | **Block device** | Périphérique bloc (disque) | `brw-rw---- /dev/sda1` (partition) |
| `s` | **Socket** | Socket Unix (communication IPC) | `srwxrwxrwx /run/docker.sock` |
| `p` | **Named Pipe (FIFO)** | Tube nommé (communication IPC) | `prw-r--r-- /tmp/mypipe` |

### 🔍 Exemples Détaillés

```bash
# Fichier régulier
ls -l /etc/passwd
-rw-r--r-- 1 root root 2567 Feb  3 10:00 /etc/passwd

# Répertoire
ls -ld /home
drwxr-xr-x 5 root root 4096 Feb  3 10:00 /home

# Lien symbolique
ls -l /usr/bin/python
lrwxrwxrwx 1 root root 9 Feb  3 10:00 /usr/bin/python -> python3.11

# Périphérique caractère (terminal)
ls -l /dev/tty1
crw--w---- 1 root tty 4, 1 Feb  3 10:00 /dev/tty1

# Périphérique bloc (disque)
ls -l /dev/sda
brw-rw---- 1 root disk 8, 0 Feb  3 10:00 /dev/sda

# Socket
ls -l /run/systemd/notify
srw-rw-rw- 1 root root 0 Feb  3 10:00 /run/systemd/notify

# Named pipe
mkfifo /tmp/mypipe
ls -l /tmp/mypipe
prw-r--r-- 1 user user 0 Feb  3 10:00 /tmp/mypipe
```

**Identifier le type** :
```bash
# Avec ls -l (1er caractère)
ls -l fichier | cut -c1

# Avec file (détection automatique)
file /dev/sda
# /dev/sda: block special (8/0)

file /etc/passwd
# /etc/passwd: ASCII text

file /usr/bin/python
# /usr/bin/python: symbolic link to python3.11

# Avec stat (plus détaillé)
stat /dev/sda | grep "File:"
# File: /dev/sda
#   Size: 0               Blocks: 0          IO Block: 4096   block special file
```

---

## 🔑 Permissions : Lecture, Écriture, Exécution

### Permissions sur Fichiers Réguliers

| Permission | Symbole | Effet sur Fichier |
|------------|---------|-------------------|
| **Read** | `r` | Lire le contenu du fichier (`cat`, `less`, `head`) |
| **Write** | `w` | Modifier/supprimer le contenu du fichier (`nano`, `echo >>`, `rm`) |
| **Execute** | `x` | Exécuter le fichier (scripts, binaires) |

**Exemples** :
```bash
# r-- (lecture seule)
-r--r--r-- config.txt
cat config.txt       # ✅ OK
nano config.txt      # ❌ Erreur : cannot open for writing

# rw- (lecture + écriture, pas d'exécution)
-rw-r--r-- script.sh
cat script.sh        # ✅ OK
echo "test" >> script.sh  # ✅ OK
./script.sh          # ❌ Erreur : Permission denied

# rwx (lecture + écriture + exécution)
-rwxr-xr-x script.sh
./script.sh          # ✅ OK
```

### Permissions sur Répertoires

| Permission | Symbole | Effet sur Répertoire |
|------------|---------|----------------------|
| **Read** | `r` | **Lire** le contenu du répertoire (`ls`) |
| **Write** | `w` | **Modifier** le contenu : créer/supprimer fichiers (`touch`, `rm`, `mkdir`) |
| **Execute** | `x` | **Accéder** au répertoire (`cd`, traverser le chemin) |

**⚠️ Important** : Les permissions répertoire sont **différentes** des fichiers !

**Exemples** :
```bash
# r-x (lecture + accès, pas d'écriture)
dr-xr-xr-x /opt/app
ls /opt/app          # ✅ OK (lecture)
cd /opt/app          # ✅ OK (exécution = accès)
touch /opt/app/file  # ❌ Erreur : Permission denied (pas d'écriture)

# -wx (écriture + accès, pas de lecture)
d-wx--x--x /tmp/dropbox
ls /tmp/dropbox      # ❌ Erreur : Permission denied (pas de lecture)
cd /tmp/dropbox      # ✅ OK (exécution)
touch /tmp/dropbox/file  # ✅ OK (écriture)

# r-- (lecture seule, pas d'accès)
dr--r--r-- /secret
ls /secret           # ❌ Erreur : Permission denied (besoin de 'x' pour accéder)
```

**Matrice permissions répertoire** :

| r | w | x | Effet |
|---|---|---|-------|
| ❌ | ❌ | ❌ | Aucun accès |
| ❌ | ❌ | ✅ | Accès si chemin connu, pas de listing |
| ❌ | ✅ | ❌ | Inutile (besoin de 'x' pour accéder) |
| ❌ | ✅ | ✅ | Créer/supprimer fichiers, pas de listing |
| ✅ | ❌ | ❌ | Inutile (besoin de 'x' pour lire) |
| ✅ | ❌ | ✅ | **Lire + accéder** (lecture seule) |
| ✅ | ✅ | ❌ | Inutile (besoin de 'x' pour accéder) |
| ✅ | ✅ | ✅ | **Contrôle total** (standard) |

**Cas pratique** : Répertoire `r-x` (lecture + accès)
```bash
# Créer répertoire test
mkdir /tmp/test
chmod 555 /tmp/test  # r-x r-x r-x

# ✅ Lister le contenu
ls /tmp/test

# ✅ Accéder au répertoire
cd /tmp/test

# ❌ Créer un fichier
touch /tmp/test/newfile
# touch: cannot touch '/tmp/test/newfile': Permission denied
```

---

## 👥 Propriété : User et Group

### Concepts

**Propriétaire (User/Owner)** : Utilisateur possédant le fichier
**Groupe (Group)** : Groupe possédant le fichier

```
-rw-r--r-- 1 alice developers 1024 Feb  3 10:00 document.txt
           │ │     │
           │ │     └─ Groupe : developers
           │ └─────── Propriétaire : alice
           └───────── Nombre de liens physiques
```

### Commande groups - Lister les Groupes

**Syntaxe** :
```bash
groups [username]
```

**Exemples** :
```bash
# Groupes de l'utilisateur actuel
groups
# user wheel docker sudo

# Groupes d'un utilisateur spécifique
groups alice
# alice : alice developers sudo

# Groupes de plusieurs utilisateurs
groups alice bob charlie
# alice : alice developers sudo
# bob : bob users
# charlie : charlie users docker

# Groupe principal (premier de la liste)
groups | cut -d' ' -f1
# user
```

**Détails avec id** :
```bash
id
# uid=1000(user) gid=1000(user) groups=1000(user),10(wheel),998(docker)

id alice
# uid=1001(alice) gid=1001(alice) groups=1001(alice),1002(developers),27(sudo)
```

### Fichiers de Configuration

```bash
# Utilisateurs : /etc/passwd
grep alice /etc/passwd
# alice:x:1001:1001:Alice Smith:/home/alice:/bin/bash
#       │  │    │
#       │  │    └─ GID (groupe principal)
#       │  └────── UID (identifiant utilisateur)
#       └───────── Mot de passe (x = dans /etc/shadow)

# Groupes : /etc/group
grep developers /etc/group
# developers:x:1002:alice,bob,charlie
#            │  │    │
#            │  │    └─ Membres du groupe
#            │  └────── GID
#            └───────── Mot de passe de groupe (x = dans /etc/gshadow)
```

---

## 🛠️ Commandes de Gestion des Permissions

### chmod - Modifier les Permissions

**Syntaxes** : Symbolique ou Octale

#### Mode Symbolique

**Format** : `chmod [ugoa][+-=][rwx] fichier`

| Symbole | Signification |
|---------|---------------|
| `u` | User (propriétaire) |
| `g` | Group (groupe) |
| `o` | Others (autres) |
| `a` | All (tous : u+g+o) |
| `+` | Ajouter permission |
| `-` | Retirer permission |
| `=` | Définir exactement |

**Exemples** :
```bash
# Ajouter exécution pour le propriétaire
chmod u+x script.sh

# Retirer écriture pour le groupe
chmod g-w document.txt

# Ajouter lecture pour les autres
chmod o+r config.txt

# Ajouter exécution pour tous
chmod a+x script.sh
# Équivalent :
chmod +x script.sh

# Définir exactement rw- pour le propriétaire
chmod u=rw fichier.txt

# Retirer toutes permissions pour les autres
chmod o= fichier.txt
# Équivalent :
chmod o-rwx fichier.txt

# Combinaisons multiples
chmod u+x,g+x,o-rwx script.sh
chmod ug+rw,o-rwx document.txt

# Copier permissions d'une catégorie à une autre
chmod g=u fichier.txt  # Groupe = User
chmod o=g fichier.txt  # Others = Group
```

#### Mode Octal (Numérique)

**Conversion binaire → octal** :

| Binaire | Octal | Permissions | Symbole |
|---------|-------|-------------|---------|
| 000 | **0** | `---` | Aucune |
| 001 | **1** | `--x` | Exécution |
| 010 | **2** | `-w-` | Écriture |
| 011 | **3** | `-wx` | Écriture + Exécution |
| 100 | **4** | `r--` | Lecture |
| 101 | **5** | `r-x` | Lecture + Exécution |
| 110 | **6** | `rw-` | Lecture + Écriture |
| 111 | **7** | `rwx` | Tout |

**Formule** : `r=4, w=2, x=1`

**Format** : `chmod [user][group][other] fichier`

**Exemples** :
```bash
# 755 = rwxr-xr-x (standard pour exécutables)
chmod 755 script.sh
# User : 7 (4+2+1) = rwx
# Group: 5 (4+0+1) = r-x
# Other: 5 (4+0+1) = r-x

# 644 = rw-r--r-- (standard pour fichiers)
chmod 644 document.txt
# User : 6 (4+2+0) = rw-
# Group: 4 (4+0+0) = r--
# Other: 4 (4+0+0) = r--

# 700 = rwx------ (privé)
chmod 700 ~/.ssh/id_rsa
# Seul le propriétaire peut tout faire

# 600 = rw------- (privé lecture/écriture)
chmod 600 ~/.ssh/config

# 777 = rwxrwxrwx (DANGEREUX - tout le monde peut tout faire)
chmod 777 fichier.txt  # ⚠️ À éviter !

# 000 = --------- (aucune permission)
chmod 000 fichier.txt
# Même root ne peut pas lire (mais peut changer les permissions)
```

**Permissions courantes** :

| Octal | Symbole | Usage typique |
|-------|---------|---------------|
| `755` | `rwxr-xr-x` | Exécutables, scripts, répertoires accessibles |
| `644` | `rw-r--r--` | Fichiers texte, documents |
| `700` | `rwx------` | Répertoires privés (~/.ssh) |
| `600` | `rw-------` | Fichiers privés (clés SSH) |
| `666` | `rw-rw-rw-` | Fichiers partagés en écriture (rare) |
| `777` | `rwxrwxrwx` | **DANGEREUX** - Tout accessible |
| `000` | `---------` | Verrouillage total |

#### chmod Récursif

```bash
# Appliquer à tous les fichiers/sous-répertoires
chmod -R 755 /var/www/html

# Combiner find pour fichiers vs répertoires
# Répertoires : 755 (rwxr-xr-x)
find /var/www/html -type d -exec chmod 755 {} \;

# Fichiers : 644 (rw-r--r--)
find /var/www/html -type f -exec chmod 644 {} \;
```

### chown - Modifier le Propriétaire et le Groupe

**Syntaxe** :
```bash
chown [user][:group] fichier
chown user fichier           # Changer propriétaire
chown :group fichier         # Changer groupe
chown user:group fichier     # Changer les deux
```

**Exemples** :
```bash
# Changer propriétaire uniquement
chown alice document.txt
ls -l document.txt
# -rw-r--r-- 1 alice users 1024 Feb  3 10:00 document.txt

# Changer groupe uniquement
chown :developers document.txt
ls -l document.txt
# -rw-r--r-- 1 alice developers 1024 Feb  3 10:00 document.txt

# Changer propriétaire ET groupe
chown bob:staff document.txt
ls -l document.txt
# -rw-r--r-- 1 bob staff 1024 Feb  3 10:00 document.txt

# Récursif (répertoire + contenu)
chown -R alice:developers /var/www/project

# Copier ownership d'un fichier de référence
chown --reference=/etc/passwd document.txt

# Verbose (afficher les changements)
chown -v alice:developers document.txt
# changed ownership of 'document.txt' from bob:staff to alice:developers

# Utiliser UID/GID numérique
chown 1001:1002 fichier.txt
```

**⚠️ Seul root peut chown** (sauf exceptions avec capabilities)

### chgrp - Modifier uniquement le Groupe

**Syntaxe** :
```bash
chgrp group fichier
```

**Exemples** :
```bash
# Changer groupe
chgrp developers document.txt

# Récursif
chgrp -R www-data /var/www/html

# Verbose
chgrp -v staff fichier.txt
# changed group of 'fichier.txt' from users to staff

# Référence
chgrp --reference=/etc/shadow fichier.txt
```

**Note** : `chown :group` est équivalent à `chgrp group`

---

## 🎭 Bits Spéciaux : SUID, SGID, Sticky Bit

### SUID (Set User ID) - Bit 4

**Effet** : Exécute le fichier avec les permissions du **propriétaire** (au lieu de l'utilisateur qui l'exécute)

**Symbole** : `s` à la place de `x` pour le propriétaire
- `rws` : SUID + exécutable
- `rwS` : SUID sans exécutable (erreur de configuration)

**Octal** : `4xxx` (4755, 4750, etc.)

**Usage** : Programmes nécessitant privilèges root temporaires

**Exemples** :
```bash
# passwd nécessite SUID pour modifier /etc/shadow (propriété root)
ls -l /usr/bin/passwd
-rwsr-xr-x 1 root root 68208 Feb  3 10:00 /usr/bin/passwd
   │
   └─ 's' = SUID

# Utilisateur normal exécute passwd
whoami
# alice
id -u
# 1001

# Pendant l'exécution, effective UID = 0 (root)
passwd
# → Peut modifier /etc/shadow car s'exécute comme root

# Définir SUID
chmod u+s /usr/local/bin/myprog
# ou
chmod 4755 /usr/local/bin/myprog

ls -l /usr/local/bin/myprog
-rwsr-xr-x 1 root root 12345 Feb  3 10:00 /usr/local/bin/myprog

# Retirer SUID
chmod u-s /usr/local/bin/myprog
# ou
chmod 0755 /usr/local/bin/myprog
```

**⚠️ Sécurité** : SUID sur scripts shell est **ignoré** par le noyau (sécurité). Fonctionne uniquement sur binaires compilés.

**Trouver tous les SUID** :
```bash
find / -type f -perm -4000 -ls 2>/dev/null
# -rwsr-xr-x   1 root     root        68208 /usr/bin/passwd
# -rwsr-xr-x   1 root     root        44784 /usr/bin/su
# -rwsr-xr-x   1 root     root        88496 /usr/bin/sudo
```

### SGID (Set Group ID) - Bit 2

**Effet sur fichiers** : Exécute avec les permissions du **groupe propriétaire**

**Effet sur répertoires** : Les nouveaux fichiers héritent du **groupe du répertoire** (au lieu du groupe principal de l'utilisateur)

**Symbole** : `s` à la place de `x` pour le groupe
- `r-s` : SGID + exécutable
- `r-S` : SGID sans exécutable

**Octal** : `2xxx` (2755, 2770, etc.)

**Exemples fichiers** :
```bash
# Définir SGID sur fichier
chmod g+s /usr/local/bin/teamtool
# ou
chmod 2755 /usr/local/bin/teamtool

ls -l /usr/local/bin/teamtool
-rwxr-sr-x 1 root developers 12345 Feb  3 10:00 /usr/local/bin/teamtool
      │
      └─ 's' = SGID
```

**Exemples répertoires (cas d'usage principal)** :
```bash
# Répertoire partagé par une équipe
mkdir /shared/project
chgrp developers /shared/project
chmod 2775 /shared/project

ls -ld /shared/project
drwxrwsr-x 2 root developers 4096 Feb  3 10:00 /shared/project
      │
      └─ 's' = SGID

# Utilisateur alice (groupe principal: alice) crée un fichier
su - alice
id
# uid=1001(alice) gid=1001(alice) groups=1001(alice),1002(developers)

cd /shared/project
touch newfile.txt

ls -l newfile.txt
-rw-r--r-- 1 alice developers 0 Feb  3 10:00 newfile.txt
#                   │
#                   └─ Groupe = developers (hérité du répertoire)
#                      Au lieu de alice (groupe principal)

# Sans SGID, le groupe serait 'alice'
mkdir /tmp/test
touch /tmp/test/file.txt
ls -l /tmp/test/file.txt
-rw-r--r-- 1 alice alice 0 Feb  3 10:00 /tmp/test/file.txt
#                   │
#                   └─ Groupe = alice (groupe principal de l'utilisateur)
```

**Trouver tous les SGID** :
```bash
find / -type f -perm -2000 -ls 2>/dev/null
find / -type d -perm -2000 -ls 2>/dev/null  # Répertoires SGID
```

### Sticky Bit - Bit 1

**Effet** : Sur répertoire, seul le **propriétaire du fichier** (ou root) peut supprimer/renommer ses propres fichiers

**Symbole** : `t` à la place de `x` pour les autres
- `rwt` : Sticky + exécutable
- `rwT` : Sticky sans exécutable

**Octal** : `1xxx` (1777, 1755, etc.)

**Usage** : Répertoires partagés comme `/tmp` (empêche utilisateurs de supprimer fichiers des autres)

**Exemples** :
```bash
# /tmp a le sticky bit
ls -ld /tmp
drwxrwxrwt 20 root root 4096 Feb  3 10:00 /tmp
        │
        └─ 't' = Sticky bit

# Définir sticky bit
mkdir /shared/dropbox
chmod 1777 /shared/dropbox
# ou
chmod +t /shared/dropbox

ls -ld /shared/dropbox
drwxrwxrwt 2 root root 4096 Feb  3 10:00 /shared/dropbox

# Scénario : Alice et Bob utilisent /tmp
# Alice crée un fichier
su - alice
echo "Alice data" > /tmp/alice_file.txt

# Bob tente de supprimer le fichier d'Alice
su - bob
rm /tmp/alice_file.txt
# rm: cannot remove '/tmp/alice_file.txt': Operation not permitted
# → Sticky bit protège les fichiers

# Sans sticky bit, Bob pourrait supprimer (car /tmp est rwx pour tous)
```

**Retirer sticky bit** :
```bash
chmod -t /shared/dropbox
# ou
chmod 0777 /shared/dropbox
```

### Tableau Récapitulatif des Bits Spéciaux

| Bit | Octal | Symbole | Sur Fichier | Sur Répertoire | Usage |
|-----|-------|---------|-------------|----------------|-------|
| **SUID** | `4` | `s` (user) | Exécute comme propriétaire | Ignoré | `/usr/bin/passwd`, `/usr/bin/sudo` |
| **SGID** | `2` | `s` (group) | Exécute comme groupe | Fichiers héritent groupe | Répertoires partagés équipes |
| **Sticky** | `1` | `t` (other) | Ignoré | Seul proprio peut supprimer | `/tmp`, `/var/tmp` |

**Combinaisons** :
```bash
# SUID + SGID + Sticky
chmod 7755 fichier
# 7 = 4+2+1

# SUID seulement
chmod 4755 fichier

# SGID seulement
chmod 2755 fichier

# Sticky seulement
chmod 1777 répertoire
```

**Visualiser les bits spéciaux** :
```bash
# Avec stat
stat -c '%a %A %n' fichier
# 4755 -rwsr-xr-x fichier

# Avec ls -l
ls -l fichier
# -rwsr-xr-x ... fichier
#    │  │  │
#    │  │  └─ t = sticky
#    │  └──── s = SGID
#    └─────── s = SUID
```

---

## 📊 umask - Masque de Permissions par Défaut

### Concept umask

**Définition** : umask **soustrait** des permissions lors de la création de fichiers/répertoires

**Permissions maximales** :
- Fichiers : `666` (rw-rw-rw-)
- Répertoires : `777` (rwxrwxrwx)

**Formule** : `Permissions réelles = Permissions max - umask`

### Exemples

```bash
# Afficher umask actuel
umask
# 0022

# Afficher en symbolique
umask -S
# u=rwx,g=rx,o=rx

# Calcul avec umask 0022 :
# Fichiers : 666 - 022 = 644 (rw-r--r--)
# Répertoires : 777 - 022 = 755 (rwxr-xr-x)

touch newfile.txt
ls -l newfile.txt
# -rw-r--r-- 1 user user 0 Feb  3 10:00 newfile.txt

mkdir newdir
ls -ld newdir
# drwxr-xr-x 2 user user 4096 Feb  3 10:00 newdir

# Changer umask (temporaire, session actuelle)
umask 0077

# Vérifier
umask
# 0077

# Calcul avec umask 0077 :
# Fichiers : 666 - 077 = 600 (rw-------)
# Répertoires : 777 - 077 = 700 (rwx------)

touch privatefile.txt
ls -l privatefile.txt
# -rw------- 1 user user 0 Feb  3 10:00 privatefile.txt

mkdir privatedir
ls -ld privatedir
# drwx------ 2 user user 4096 Feb  3 10:00 privatedir
```

### umask Courants

| umask | Fichiers | Répertoires | Usage |
|-------|----------|-------------|-------|
| `0022` | `644` (rw-r--r--) | `755` (rwxr-xr-x) | **Par défaut** (multi-utilisateurs) |
| `0002` | `664` (rw-rw-r--) | `775` (rwxrwxr-x) | Collaboration groupe |
| `0077` | `600` (rw-------) | `700` (rwx------) | Sécurité maximale (privé) |
| `0000` | `666` (rw-rw-rw-) | `777` (rwxrwxrwx) | **DANGEREUX** (tout ouvert) |
| `0027` | `640` (rw-r-----) | `750` (rwxr-x---) | Lecture groupe, rien pour others |

### Configuration Permanente

```bash
# Pour utilisateur : ~/.bashrc ou ~/.bash_profile
echo "umask 0077" >> ~/.bashrc
source ~/.bashrc

# Pour système : /etc/profile ou /etc/bash.bashrc
echo "umask 0022" >> /etc/profile

# Vérifier après connexion
umask
# 0077
```

---

## 📈 stat - Informations Détaillées sur Fichiers

### Syntaxe

```bash
stat [options] fichier
```

### Affichage Complet

```bash
stat /etc/passwd
```

**Sortie** :
```
  File: /etc/passwd
  Size: 2567            Blocks: 8          IO Block: 4096   regular file
Device: 802h/2050d      Inode: 131588      Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2026-02-03 10:00:00.123456789 +0100
Modify: 2026-02-03 09:55:00.987654321 +0100
Change: 2026-02-03 09:55:00.987654321 +0100
 Birth: 2025-12-01 08:30:00.000000000 +0100
```

### Détails des Champs

| Champ | Description |
|-------|-------------|
| **File** | Nom du fichier |
| **Size** | Taille en octets |
| **Blocks** | Nombre de blocs de 512 octets utilisés |
| **IO Block** | Taille de bloc optimal pour I/O |
| **Type** | Type de fichier (regular file, directory, symbolic link, etc.) |
| **Device** | Périphérique (en hexa et décimal) |
| **Inode** | Numéro d'inode unique |
| **Links** | Nombre de liens physiques (hard links) |
| **Access (perms)** | Permissions en octal et symbolique |
| **Uid** | UID et nom du propriétaire |
| **Gid** | GID et nom du groupe |
| **Access (time)** | Dernier accès (atime) |
| **Modify** | Dernière modification du contenu (mtime) |
| **Change** | Dernier changement de métadonnées (ctime) |
| **Birth** | Date de création (si supporté par le FS) |

### Formats Personnalisés

**Options -c (format personnalisé)** :

```bash
# Permissions octales
stat -c '%a' fichier
# 644

# Permissions symboliques
stat -c '%A' fichier
# -rw-r--r--

# Propriétaire et groupe
stat -c '%U:%G' fichier
# root:root

# Taille
stat -c '%s' fichier
# 2567

# Type de fichier
stat -c '%F' fichier
# regular file

# Combinaisons
stat -c '%n %a %U:%G' /etc/passwd
# /etc/passwd 644 root:root

# Affichage tableau
stat -c '%n %a %A %U:%G' /bin/* | head -5
# /bin/bash 755 -rwxr-xr-x root:root
# /bin/cat 755 -rwxr-xr-x root:root
# /bin/chmod 755 -rwxr-xr-x root:root
```

**Formats courants** :

| Format | Description | Exemple |
|--------|-------------|---------|
| `%a` | Permissions (octal) | `644` |
| `%A` | Permissions (symbolique) | `-rw-r--r--` |
| `%F` | Type de fichier | `regular file` |
| `%n` | Nom du fichier | `/etc/passwd` |
| `%s` | Taille en octets | `2567` |
| `%U` | Propriétaire (nom) | `root` |
| `%G` | Groupe (nom) | `root` |
| `%u` | UID (numérique) | `0` |
| `%g` | GID (numérique) | `0` |
| `%i` | Numéro d'inode | `131588` |
| `%h` | Nombre de liens | `1` |
| `%x` | Dernier accès | `2026-02-03 10:00:00` |
| `%y` | Dernière modification | `2026-02-03 09:55:00` |
| `%z` | Dernier changement | `2026-02-03 09:55:00` |

### Comparaison avec ls

```bash
# ls -l
ls -l /etc/passwd
# -rw-r--r-- 1 root root 2567 Feb  3 09:55 /etc/passwd

# stat (plus détaillé)
stat /etc/passwd
# → Affiche inode, blocks, 3 timestamps (atime/mtime/ctime), device
```

---

## 🎓 10 Scénarios Pratiques Complets

### Scénario 1️⃣ : Sécuriser un répertoire .ssh avec bonnes permissions

**Contexte** : Configuration SSH nécessite permissions strictes pour fonctionner.

```bash
# 1. Créer répertoire .ssh
mkdir -p ~/.ssh

# 2. Permissions répertoire : 700 (rwx------)
chmod 700 ~/.ssh
ls -ld ~/.ssh
# drwx------ 2 user user 4096 Feb  3 10:00 /home/user/.ssh

# 3. Créer clé privée (simulation)
touch ~/.ssh/id_rsa

# 4. Permissions clé privée : 600 (rw-------)
chmod 600 ~/.ssh/id_rsa
ls -l ~/.ssh/id_rsa
# -rw------- 1 user user 0 Feb  3 10:00 /home/user/.ssh/id_rsa

# 5. Créer clé publique
touch ~/.ssh/id_rsa.pub

# 6. Permissions clé publique : 644 (rw-r--r--)
chmod 644 ~/.ssh/id_rsa.pub
ls -l ~/.ssh/id_rsa.pub
# -rw-r--r-- 1 user user 0 Feb  3 10:00 /home/user/.ssh/id_rsa.pub

# 7. Fichier authorized_keys
touch ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys

# 8. Fichier config
touch ~/.ssh/config
chmod 600 ~/.ssh/config

# 9. Vérifier ensemble
ls -la ~/.ssh
# drwx------  2 user user 4096 Feb  3 10:00 .
# -rw-------  1 user user    0 Feb  3 10:00 authorized_keys
# -rw-------  1 user user    0 Feb  3 10:00 config
# -rw-------  1 user user    0 Feb  3 10:00 id_rsa
# -rw-r--r--  1 user user    0 Feb  3 10:00 id_rsa.pub

# 10. Tester SSH (échoue si mauvaises permissions)
# ssh refusera de fonctionner si :
# - ~/.ssh est > 700
# - id_rsa est > 600
# - authorized_keys est > 600

# Exemple d'erreur si mauvaises permissions :
chmod 777 ~/.ssh/id_rsa
ssh-keygen -l -f ~/.ssh/id_rsa
# @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
# @         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
# @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
# Permissions 0777 for '/home/user/.ssh/id_rsa' are too open.
```

**Récapitulatif permissions SSH** :
```
~/.ssh/                  700 (drwx------)
~/.ssh/id_rsa            600 (-rw-------)
~/.ssh/id_rsa.pub        644 (-rw-r--r--)
~/.ssh/authorized_keys   600 (-rw-------)
~/.ssh/config            600 (-rw-------)
~/.ssh/known_hosts       644 (-rw-r--r--)
```

---

### Scénario 2️⃣ : Créer répertoire partagé d'équipe avec SGID

**Contexte** : Équipe de développeurs partageant fichiers, tous doivent avoir accès en écriture.

```bash
# 1. Créer répertoire projet
mkdir /shared/devteam

# 2. Créer groupe si nécessaire
groupadd developers

# 3. Ajouter utilisateurs au groupe
usermod -aG developers alice
usermod -aG developers bob
usermod -aG developers charlie

# 4. Vérifier appartenance
groups alice
# alice : alice developers

# 5. Définir groupe propriétaire
chgrp developers /shared/devteam

# 6. Permissions : rwxrwsr-x (2775)
chmod 2775 /shared/devteam
# 2 = SGID
# 7 = rwx pour user
# 7 = rwx pour group
# 5 = r-x pour others

ls -ld /shared/devteam
# drwxrwsr-x 2 root developers 4096 Feb  3 10:00 /shared/devteam
#       │
#       └─ 's' = SGID

# 7. Test : Alice crée un fichier
su - alice
cd /shared/devteam
echo "Alice's code" > code.py

ls -l code.py
# -rw-r--r-- 1 alice developers 14 Feb  3 10:00 code.py
#                    │
#                    └─ Groupe = developers (hérité grâce au SGID)

# 8. Bob peut modifier le fichier
su - bob
cd /shared/devteam
echo "Bob's modifications" >> code.py  # ✅ OK

# 9. Mais attention : permissions fichier par défaut = 644
# Bob ne peut pas écrire car 'others' n'a que r--
# Solution : Ajuster umask pour le groupe

# 10. Alice configure umask 002 (au lieu de 022)
su - alice
umask 002
echo "New file" > file2.py

ls -l file2.py
# -rw-rw-r-- 1 alice developers 9 Feb  3 10:00 file2.py
#       │
#       └─ Groupe a 'rw' grâce à umask 002

# 11. Maintenant Bob peut écrire
su - bob
echo "Bob adds code" >> file2.py  # ✅ OK

# 12. Rendre umask 002 permanent pour le groupe
# Ajouter dans /etc/profile ou ~/.bashrc de chaque membre :
echo "umask 002" >> ~/.bashrc

# 13. Alternative : ACL pour contrôle plus fin (hors scope LPIC-1)
```

---

### Scénario 3️⃣ : Protéger /tmp avec Sticky Bit

**Contexte** : Empêcher utilisateurs de supprimer fichiers d'autres utilisateurs dans /tmp.

```bash
# 1. Vérifier /tmp actuel
ls -ld /tmp
# drwxrwxrwt 20 root root 4096 Feb  3 10:00 /tmp
#         │
#         └─ 't' = Sticky bit déjà présent

# 2. Simuler répertoire partagé sans sticky bit
mkdir /shared/dropbox
chmod 777 /shared/dropbox

ls -ld /shared/dropbox
# drwxrwxrwx 2 root root 4096 Feb  3 10:00 /shared/dropbox
#         │
#         └─ 'x' = Pas de sticky bit

# 3. Alice crée un fichier
su - alice
echo "Important data" > /shared/dropbox/alice_data.txt

ls -l /shared/dropbox/alice_data.txt
# -rw-r--r-- 1 alice alice 14 Feb  3 10:00 /shared/dropbox/alice_data.txt

# 4. Bob peut SUPPRIMER le fichier d'Alice (car rwx sur répertoire)
su - bob
rm /shared/dropbox/alice_data.txt
# ✅ Supprimé ! (problème de sécurité)

# 5. Ajouter sticky bit pour protéger
chmod +t /shared/dropbox
# ou
chmod 1777 /shared/dropbox

ls -ld /shared/dropbox
# drwxrwxrwt 2 root root 4096 Feb  3 10:00 /shared/dropbox
#         │
#         └─ 't' = Sticky bit

# 6. Alice recrée son fichier
su - alice
echo "Important data" > /shared/dropbox/alice_data.txt

# 7. Bob tente de supprimer
su - bob
rm /shared/dropbox/alice_data.txt
# rm: cannot remove '/shared/dropbox/alice_data.txt': Operation not permitted
# ❌ Protégé par sticky bit !

# 8. Bob peut créer et supprimer ses propres fichiers
echo "Bob's file" > /shared/dropbox/bob_data.txt
rm /shared/dropbox/bob_data.txt  # ✅ OK

# 9. Root peut toujours tout supprimer
sudo rm /shared/dropbox/alice_data.txt  # ✅ OK

# 10. Vérifier avec stat
stat -c '%a %A' /shared/dropbox
# 1777 drwxrwxrwt
```

---

### Scénario 4️⃣ : Diagnostic et correction de permissions cassées

**Contexte** : Script ne s'exécute plus, permissions incorrectes.

```bash
# 1. Script existant ne fonctionne plus
./backup.sh
# -bash: ./backup.sh: Permission denied

# 2. Vérifier permissions
ls -l backup.sh
# -rw-r--r-- 1 user user 256 Feb  3 10:00 backup.sh
#    │  │  │
#    └──┴──┴─ Aucun 'x' → Pas exécutable

# 3. Ajouter exécution
chmod +x backup.sh

ls -l backup.sh
# -rwxr-xr-x 1 user user 256 Feb  3 10:00 backup.sh

# 4. Tester
./backup.sh  # ✅ Fonctionne

# 5. Cas 2 : Répertoire inaccessible
cd /opt/app
# -bash: cd: /opt/app: Permission denied

ls -ld /opt/app
# drw-r--r-- 5 root root 4096 Feb  3 10:00 /opt/app
#    │     │
#    └─────┴─ 'r' mais pas 'x' → Pas d'accès

# 6. Ajouter exécution sur répertoire
sudo chmod +x /opt/app

ls -ld /opt/app
# drwxr-xr-x 5 root root 4096 Feb  3 10:00 /opt/app

cd /opt/app  # ✅ Fonctionne

# 7. Cas 3 : Impossible de créer fichier dans répertoire
touch /shared/data/newfile.txt
# touch: cannot touch '/shared/data/newfile.txt': Permission denied

ls -ld /shared/data
# dr-xr-xr-x 2 root users 4096 Feb  3 10:00 /shared/data
#    │  │  │
#    └──┴──┴─ 'r-x' mais pas 'w' → Lecture seule

# 8. Ajouter écriture pour le groupe
sudo chmod g+w /shared/data

ls -ld /shared/data
# drwxrwxr-x 2 root users 4096 Feb  3 10:00 /shared/data

touch /shared/data/newfile.txt  # ✅ Fonctionne

# 9. Utiliser stat pour diagnostic détaillé
stat backup.sh
# Access: (0755/-rwxr-xr-x)  Uid: ( 1000/    user)   Gid: ( 1000/    user)

# 10. Trouver tous fichiers avec permissions bizarres
find /home/user -type f -perm 777
# → Fichiers trop ouverts (dangerous)

find /home/user -type f ! -perm -u+r
# → Fichiers que le propriétaire ne peut pas lire (bizarre)
```

---

### Scénario 5️⃣ : Application web - Permissions Apache/Nginx

**Contexte** : Configuration permissions pour serveur web.

```bash
# 1. Structure typique
/var/www/html/
├── index.html
├── css/
├── js/
├── images/
└── uploads/

# 2. Le serveur web tourne sous www-data
ps aux | grep apache
# www-data  1234  ... /usr/sbin/apache2

# 3. Propriétaire : utilisateur déployant le site
chown -R deployer:www-data /var/www/html

# 4. Répertoires : 755 (rwxr-xr-x)
find /var/www/html -type d -exec chmod 755 {} \;

ls -ld /var/www/html
# drwxr-xr-x 5 deployer www-data 4096 Feb  3 10:00 /var/www/html

# 5. Fichiers statiques : 644 (rw-r--r--)
find /var/www/html -type f -exec chmod 644 {} \;

ls -l /var/www/html/index.html
# -rw-r--r-- 1 deployer www-data 1024 Feb  3 10:00 index.html

# 6. Répertoire uploads : www-data doit pouvoir écrire
mkdir -p /var/www/html/uploads
chown deployer:www-data /var/www/html/uploads
chmod 775 /var/www/html/uploads

ls -ld /var/www/html/uploads
# drwxrwxr-x 2 deployer www-data 4096 Feb  3 10:00 /var/www/html/uploads

# 7. Ajouter SGID pour uploads (fichiers uploadés appartiennent au groupe)
chmod g+s /var/www/html/uploads

ls -ld /var/www/html/uploads
# drwxrwsr-x 2 deployer www-data 4096 Feb  3 10:00 /var/www/html/uploads

# 8. Simuler upload par Apache
sudo -u www-data touch /var/www/html/uploads/uploaded_image.jpg

ls -l /var/www/html/uploads/uploaded_image.jpg
# -rw-r--r-- 1 www-data www-data 0 Feb  3 10:00 uploaded_image.jpg
#                       │
#                       └─ Groupe = www-data (hérité grâce SGID)

# 9. Deployer peut gérer les fichiers uploadés (car membre de www-data)
groups deployer
# deployer : deployer www-data

# 10. Résumé permissions web
echo "Répertoires : 755"
echo "Fichiers    : 644"
echo "Uploads     : 775 + SGID"
echo "Propriétaire: deployer:www-data"
```

---

### Scénario 6️⃣ : Trouver et sécuriser fichiers SUID/SGID suspects

**Contexte** : Audit de sécurité pour fichiers privilégiés.

```bash
# 1. Trouver tous les SUID (bit 4)
find / -type f -perm -4000 -ls 2>/dev/null > /tmp/suid_files.txt

cat /tmp/suid_files.txt
# -rwsr-xr-x   1 root     root        68208 /usr/bin/passwd
# -rwsr-xr-x   1 root     root        44784 /usr/bin/su
# -rwsr-xr-x   1 root     root        88496 /usr/bin/sudo
# -rwsr-xr-x   1 root     root        32648 /usr/bin/ping

# 2. Trouver tous les SGID (bit 2)
find / -type f -perm -2000 -ls 2>/dev/null > /tmp/sgid_files.txt

# 3. Afficher avec stat
find / -perm -4000 -exec stat -c '%a %A %U:%G %n' {} \; 2>/dev/null
# 4755 -rwsr-xr-x root:root /usr/bin/passwd

# 4. Fichier suspect trouvé : /tmp/backdoor
ls -l /tmp/backdoor
# -rwsr-xr-x 1 root root 12345 Feb  3 10:00 /tmp/backdoor
# ⚠️ SUID root dans /tmp = TRÈS SUSPECT

# 5. Retirer SUID
chmod u-s /tmp/backdoor
# ou
chmod 0755 /tmp/backdoor

ls -l /tmp/backdoor
# -rwxr-xr-x 1 root root 12345 Feb  3 10:00 /tmp/backdoor

# 6. Ou supprimer complètement
rm /tmp/backdoor

# 7. Surveiller créations SUID avec audit
# Installer auditd
apt install auditd

# Règle audit pour SUID/SGID
auditctl -w /usr/bin -p wa -k suid_changes

# 8. Baseline : Sauvegarder liste SUID/SGID légitime
find / -type f \( -perm -4000 -o -perm -2000 \) -ls 2>/dev/null | \
  sort > /root/baseline_suid_sgid.txt

# 9. Comparer régulièrement
find / -type f \( -perm -4000 -o -perm -2000 \) -ls 2>/dev/null | \
  sort > /tmp/current_suid_sgid.txt

diff /root/baseline_suid_sgid.txt /tmp/current_suid_sgid.txt
# → Affiche nouveaux fichiers SUID/SGID

# 10. SUID légitimes courants
cat <<EOF
Légitimes :
- /usr/bin/passwd  (changer mot de passe)
- /usr/bin/su      (changer utilisateur)
- /usr/bin/sudo    (élévation privilèges)
- /usr/bin/ping    (raw sockets réseau)
- /usr/bin/mount   (monter filesystems)

Suspects :
- SUID dans /tmp, /var/tmp, /dev/shm
- SUID sur scripts shell (ignoré par noyau)
- SUID propriétaire != root (rare, vérifier)
EOF
```

---

### Scénario 7️⃣ : umask pour différents profils utilisateurs

**Contexte** : Configurer umask selon le rôle utilisateur.

```bash
# === Profil 1 : Développeur (collaboration) ===

# umask 002 → Fichiers 664, Répertoires 775
# Groupe peut lire/écrire
echo "umask 002" >> /home/developer/.bashrc

su - developer
umask
# 0002

touch testfile.txt
ls -l testfile.txt
# -rw-rw-r-- 1 developer developer 0 Feb  3 10:00 testfile.txt
#       │
#       └─ Groupe a 'rw'

mkdir testdir
ls -ld testdir
# drwxrwxr-x 2 developer developer 4096 Feb  3 10:00 testdir

# === Profil 2 : Utilisateur standard (multi-user) ===

# umask 022 → Fichiers 644, Répertoires 755
# Groupe/Others lecture seule
echo "umask 022" >> /home/user/.bashrc

su - user
umask
# 0022

touch file.txt
ls -l file.txt
# -rw-r--r-- 1 user user 0 Feb  3 10:00 file.txt
#       │  │
#       └──┴─ Groupe et others ont 'r' seulement

# === Profil 3 : Admin système (sécurité) ===

# umask 077 → Fichiers 600, Répertoires 700
# Privé (seul propriétaire)
echo "umask 077" >> /root/.bashrc

su - root
umask
# 0077

touch secretfile.txt
ls -l secretfile.txt
# -rw------- 1 root root 0 Feb  3 10:00 secretfile.txt
#       │
#       └─ Groupe et others n'ont rien

mkdir secretdir
ls -ld secretdir
# drwx------ 2 root root 4096 Feb  3 10:00 secretdir

# === Configuration système (/etc/profile) ===

cat >> /etc/profile <<'EOF'
# umask selon UID
if [ $UID -gt 199 ] && [ "$(id -gn)" = "$(id -un)" ]; then
    umask 002  # Utilisateurs normaux
else
    umask 022  # Root et utilisateurs système
fi
EOF

# === Vérification ===

# Script test_umask.sh
cat > test_umask.sh <<'EOF'
#!/bin/bash
echo "Utilisateur: $(whoami)"
echo "umask: $(umask)"
echo "Test création fichier:"
touch /tmp/test_$(whoami).txt
ls -l /tmp/test_$(whoami).txt
EOF

chmod +x test_umask.sh

# Tester avec différents utilisateurs
./test_umask.sh
su - developer -c "/path/to/test_umask.sh"
su - user -c "/path/to/test_umask.sh"
```

---

### Scénario 8️⃣ : Migration de propriétaire pour projet

**Contexte** : Transférer ownership d'un projet complet à nouveau propriétaire.

```bash
# 1. Situation initiale
ls -la /opt/project
# drwxr-xr-x  5 alice developers 4096 Feb  3 10:00 .
# -rw-r--r--  1 alice developers  256 Feb  3 10:00 README.md
# drwxr-xr-x  3 alice developers 4096 Feb  3 10:00 src/
# drwxr-xr-x  2 alice developers 4096 Feb  3 10:00 config/

# 2. Transférer à bob (nouveau maintainer)
chown -R bob:developers /opt/project

# 3. Vérifier
ls -la /opt/project
# drwxr-xr-x  5 bob developers 4096 Feb  3 10:00 .
# -rw-r--r--  1 bob developers  256 Feb  3 10:00 README.md
# drwxr-xr-x  3 bob developers 4096 Feb  3 10:00 src/
# drwxr-xr-x  2 bob developers 4096 Feb  3 10:00 config/

# 4. Vérifier récursivement avec find
find /opt/project -user alice
# (aucune sortie = tous transférés)

find /opt/project -user bob | wc -l
# 127  (tous les fichiers appartiennent à bob)

# 5. Cas complexe : Ownership multiple (plusieurs propriétaires)
# Lister tous les propriétaires
find /opt/project -printf '%u\n' | sort | uniq -c
#   100 bob
#    15 alice  ← Fichiers oubliés
#    12 charlie

# 6. Transférer seulement fichiers d'alice
find /opt/project -user alice -exec chown bob:developers {} \;

# 7. Vérifier à nouveau
find /opt/project -printf '%u\n' | sort | uniq -c
#   115 bob
#    12 charlie  ← Charlie conserve ses fichiers

# 8. Utiliser --from pour transfert conditionnel
chown --from=alice bob /opt/project/*
# Change seulement si propriétaire actuel = alice

# 9. Copier ownership d'un fichier de référence
chown --reference=/etc/passwd /opt/project/newfile.txt
ls -l /opt/project/newfile.txt
# -rw-r--r-- 1 root root 0 Feb  3 10:00 /opt/project/newfile.txt

# 10. Script de migration complet
cat > migrate_ownership.sh <<'EOF'
#!/bin/bash
OLD_USER="alice"
NEW_USER="bob"
PROJECT="/opt/project"

echo "Migration de $OLD_USER → $NEW_USER pour $PROJECT"

# Compter fichiers à migrer
COUNT=$(find "$PROJECT" -user "$OLD_USER" | wc -l)
echo "Fichiers à transférer : $COUNT"

# Demander confirmation
read -p "Continuer ? (y/n) " -n 1 -r
echo
if [[ $REPLY =~ ^[Yy]$ ]]; then
    find "$PROJECT" -user "$OLD_USER" -exec chown "$NEW_USER" {} \;
    echo "Migration terminée."
    
    # Vérifier
    REMAINING=$(find "$PROJECT" -user "$OLD_USER" | wc -l)
    echo "Fichiers restants de $OLD_USER : $REMAINING"
fi
EOF

chmod +x migrate_ownership.sh
sudo ./migrate_ownership.sh
```

---

### Scénario 9️⃣ : Permissions pour base de données PostgreSQL

**Contexte** : Configuration sécurisée de répertoire données PostgreSQL.

```bash
# 1. Localisation données PostgreSQL
PGDATA="/var/lib/postgresql/14/main"

# 2. Créer répertoire si nécessaire
mkdir -p "$PGDATA"

# 3. Propriétaire : utilisateur postgres
chown postgres:postgres "$PGDATA"

# 4. Permissions : 700 (rwx------)
# PostgreSQL REFUSE de démarrer si permissions trop ouvertes
chmod 700 "$PGDATA"

ls -ld "$PGDATA"
# drwx------ 19 postgres postgres 4096 Feb  3 10:00 /var/lib/postgresql/14/main

# 5. Vérifier avec stat
stat -c '%a %A %U:%G' "$PGDATA"
# 700 drwx------ postgres:postgres

# 6. Fichiers de configuration
ls -l "$PGDATA"/*.conf
# -rw------- 1 postgres postgres  28518 Feb  3 10:00 postgresql.conf
# -rw------- 1 postgres postgres   5086 Feb  3 10:00 pg_hba.conf
# -rw------- 1 postgres postgres   1636 Feb  3 10:00 pg_ident.conf

# 7. Test : Permissions incorrectes
chmod 755 "$PGDATA"

# Tenter démarrage PostgreSQL
systemctl start postgresql
# ✗ ERREUR :
# FATAL: data directory "/var/lib/postgresql/14/main" has invalid permissions
# DETAIL: Permissions should be u=rwx (0700)

# 8. Corriger
chmod 700 "$PGDATA"
systemctl start postgresql
# ✓ Démarre correctement

# 9. Socket Unix : 777 avec sticky bit
ls -l /var/run/postgresql/
# srwxrwxrwx 1 postgres postgres 0 Feb  3 10:00 .s.PGSQL.5432
# ^
# └─ Socket

# 10. Automatiser vérification
cat > check_pg_perms.sh <<'EOF'
#!/bin/bash
PGDATA="/var/lib/postgresql/14/main"

PERMS=$(stat -c '%a' "$PGDATA")
OWNER=$(stat -c '%U' "$PGDATA")

if [ "$PERMS" != "700" ]; then
    echo "❌ Permissions incorrectes: $PERMS (attendu: 700)"
    echo "Correction : chmod 700 $PGDATA"
    exit 1
fi

if [ "$OWNER" != "postgres" ]; then
    echo "❌ Propriétaire incorrect: $OWNER (attendu: postgres)"
    echo "Correction : chown postgres:postgres $PGDATA"
    exit 1
fi

echo "✓ Permissions PostgreSQL correctes"
EOF

chmod +x check_pg_perms.sh
./check_pg_perms.sh
```

---

### Scénario 🔟 : Audit et rapport permissions système

**Contexte** : Générer rapport de sécurité sur permissions système.

```bash
# 1. Script d'audit complet
cat > audit_permissions.sh <<'EOF'
#!/bin/bash

REPORT="/tmp/security_audit_$(date +%Y%m%d_%H%M%S).txt"

echo "=== AUDIT DE SÉCURITÉ DES PERMISSIONS ===" > "$REPORT"
echo "Date: $(date)" >> "$REPORT"
echo "" >> "$REPORT"

# 2. Fichiers world-writable (dangereux)
echo "=== Fichiers World-Writable (hors /tmp, /var/tmp) ===" >> "$REPORT"
find / -path /proc -prune -o -path /sys -prune -o -type f -perm -002 ! -path "/tmp/*" ! -path "/var/tmp/*" -ls 2>/dev/null >> "$REPORT"
echo "" >> "$REPORT"

# 3. Répertoires world-writable sans sticky bit
echo "=== Répertoires World-Writable SANS Sticky Bit ===" >> "$REPORT"
find / -path /proc -prune -o -path /sys -prune -o -type d -perm -002 ! -perm -1000 -ls 2>/dev/null >> "$REPORT"
echo "" >> "$REPORT"

# 4. Fichiers SUID root
echo "=== Fichiers SUID Root ===" >> "$REPORT"
find / -path /proc -prune -o -path /sys -prune -o -type f -perm -4000 -user root -ls 2>/dev/null >> "$REPORT"
echo "" >> "$REPORT"

# 5. Fichiers SGID
echo "=== Fichiers SGID ===" >> "$REPORT"
find / -path /proc -prune -o -path /sys -prune -o -type f -perm -2000 -ls 2>/dev/null >> "$REPORT"
echo "" >> "$REPORT"

# 6. Fichiers sans propriétaire (orphelins)
echo "=== Fichiers Sans Propriétaire ===" >> "$REPORT"
find / -path /proc -prune -o -path /sys -prune -o -nouser -ls 2>/dev/null >> "$REPORT"
echo "" >> "$REPORT"

# 7. Fichiers sans groupe
echo "=== Fichiers Sans Groupe ===" >> "$REPORT"
find / -path /proc -prune -o -path /sys -prune -o -nogroup -ls 2>/dev/null >> "$REPORT"
echo "" >> "$REPORT"

# 8. Fichiers modifiés récemment (dernières 24h)
echo "=== Fichiers Modifiés (24h) ===" >> "$REPORT"
find /etc /bin /sbin /usr/bin /usr/sbin -type f -mtime -1 -ls 2>/dev/null >> "$REPORT"
echo "" >> "$REPORT"

# 9. Permissions /etc critiques
echo "=== Vérification /etc Critique ===" >> "$REPORT"
for file in /etc/passwd /etc/shadow /etc/group /etc/gshadow /etc/sudoers; do
    if [ -f "$file" ]; then
        stat -c '%n : %a %A %U:%G' "$file" >> "$REPORT"
    fi
done
echo "" >> "$REPORT"

# 10. Statistiques
echo "=== STATISTIQUES ===" >> "$REPORT"
echo "Fichiers world-writable : $(find / -path /proc -prune -o -path /sys -prune -o -type f -perm -002 2>/dev/null | wc -l)" >> "$REPORT"
echo "Fichiers SUID : $(find / -path /proc -prune -o -path /sys -prune -o -type f -perm -4000 2>/dev/null | wc -l)" >> "$REPORT"
echo "Fichiers SGID : $(find / -path /proc -prune -o -path /sys -prune -o -type f -perm -2000 2>/dev/null | wc -l)" >> "$REPORT"
echo "Fichiers orphelins : $(find / -path /proc -prune -o -path /sys -prune -o -nouser -o -nogroup 2>/dev/null | wc -l)" >> "$REPORT"

echo "" >> "$REPORT"
echo "=== FIN DU RAPPORT ===" >> "$REPORT"

echo "Rapport généré : $REPORT"
cat "$REPORT"
EOF

chmod +x audit_permissions.sh

# 2. Exécuter audit
sudo ./audit_permissions.sh

# 3. Vérifications manuelles additionnelles
# /etc/passwd : 644
stat -c '%a' /etc/passwd
# 644

# /etc/shadow : 640 ou 000
stat -c '%a' /etc/shadow
# 640

# /etc/sudoers : 440
stat -c '%a' /etc/sudoers
# 440

# 4. Créer baseline pour comparaison future
sudo ./audit_permissions.sh > /root/baseline_permissions.txt

# 5. Comparer avec baseline dans 1 mois
sudo ./audit_permissions.sh > /tmp/current_permissions.txt
diff /root/baseline_permissions.txt /tmp/current_permissions.txt
```

---

## 🚨 10 Erreurs Courantes et Solutions

### Erreur 1️⃣ : chmod sans +x, script ne s'exécute pas

**Symptôme** :
```bash
./script.sh
# -bash: ./script.sh: Permission denied
```

**Cause** : Permission d'exécution manquante.

**Solutions** :

```bash
# 1. Vérifier permissions
ls -l script.sh
# -rw-r--r-- 1 user user 256 Feb  3 10:00 script.sh
#    │  │  │
#    └──┴──┴─ Aucun 'x'

# 2. Ajouter exécution
chmod +x script.sh

# 3. Vérifier
ls -l script.sh
# -rwxr-xr-x 1 user user 256 Feb  3 10:00 script.sh

# 4. Alternative : Exécuter avec interpréteur
bash script.sh  # Fonctionne même sans +x

# 5. Pour tous les utilisateurs
chmod a+x script.sh

# 6. Seulement propriétaire
chmod u+x script.sh
```

---

### Erreur 2️⃣ : Permissions répertoire sans 'x', accès impossible

**Symptôme** :
```bash
cd /opt/data
# -bash: cd: /opt/data: Permission denied

ls /opt/data
# ls: cannot access '/opt/data': Permission denied
```

**Cause** : Bit 'x' manquant sur répertoire.

**Solutions** :

```bash
# 1. Vérifier permissions répertoire
ls -ld /opt/data
# drw-r--r-- 2 root root 4096 Feb  3 10:00 /opt/data
#    │  │  │
#    └──┴──┴─ 'r' mais pas 'x' → Impossible d'accéder

# 2. Ajouter exécution (accès)
chmod +x /opt/data

ls -ld /opt/data
# drwxr-xr-x 2 root root 4096 Feb  3 10:00 /opt/data

# 3. Tester
cd /opt/data  # ✅ Fonctionne

# 4. Rappel : Permissions répertoire
# r = lire contenu (ls)
# w = créer/supprimer fichiers (touch, rm)
# x = accéder/traverser (cd)

# 5. Exemple : Répertoire r-x (lecture + accès, pas d'écriture)
chmod 555 /opt/data
ls /opt/data       # ✅ OK
cd /opt/data       # ✅ OK
touch /opt/data/f  # ❌ Permission denied
```

---

### Erreur 3️⃣ : chmod récursif casse exécutables

**Symptôme** :
```bash
chmod -R 644 /usr/local/bin
# Tous les scripts/binaires deviennent non-exécutables !

/usr/local/bin/mytool
# -bash: /usr/local/bin/mytool: Permission denied
```

**Cause** : chmod récursif applique mêmes permissions à fichiers et répertoires.

**Solutions** :

```bash
# ❌ INCORRECT (retire 'x' partout)
chmod -R 644 /path

# ✅ CORRECT : Séparer fichiers et répertoires

# Répertoires : 755
find /path -type d -exec chmod 755 {} \;

# Fichiers : 644
find /path -type f -exec chmod 644 {} \;

# Si certains fichiers doivent être exécutables
find /path -type f -name "*.sh" -exec chmod 755 {} \;
find /path -type f -name "*.py" -exec chmod 755 {} \;

# Restaurer exécutables dans /usr/local/bin
chmod 755 /usr/local/bin/*

# Ou plus précis : Ajouter 'x' seulement si déjà lisible
chmod -R a+X /path  # 'X' majuscule = 'x' seulement pour répertoires
```

---

### Erreur 4️⃣ : Fichier .ssh/id_rsa trop ouvert, SSH refuse

**Symptôme** :
```bash
ssh user@server
# @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
# @         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
# @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
# Permissions 0644 for '/home/user/.ssh/id_rsa' are too open.
# It is required that your private key files are NOT accessible by others.
```

**Cause** : Clé privée SSH trop permissive.

**Solutions** :

```bash
# 1. Vérifier permissions
ls -l ~/.ssh/id_rsa
# -rw-r--r-- 1 user user 3243 Feb  3 10:00 /home/user/.ssh/id_rsa
#       │  │
#       └──┴─ Groupe et others peuvent lire (DANGER)

# 2. Corriger : 600 (rw-------)
chmod 600 ~/.ssh/id_rsa

ls -l ~/.ssh/id_rsa
# -rw------- 1 user user 3243 Feb  3 10:00 /home/user/.ssh/id_rsa

# 3. Vérifier répertoire .ssh
chmod 700 ~/.ssh

# 4. Permissions SSH complètes
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
chmod 600 ~/.ssh/authorized_keys
chmod 600 ~/.ssh/config

# 5. Script automatique
cat > fix_ssh_perms.sh <<'EOF'
#!/bin/bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_* 2>/dev/null
chmod 644 ~/.ssh/*.pub 2>/dev/null
chmod 600 ~/.ssh/authorized_keys 2>/dev/null
chmod 600 ~/.ssh/config 2>/dev/null
echo "✓ Permissions SSH corrigées"
EOF

chmod +x fix_ssh_perms.sh
./fix_ssh_perms.sh
```

---

### Erreur 5️⃣ : chown impossible (seul root peut chown)

**Symptôme** :
```bash
chown bob:staff fichier.txt
# chown: changing ownership of 'fichier.txt': Operation not permitted
```

**Cause** : Utilisateur normal ne peut pas chown (sécurité Unix).

**Solutions** :

```bash
# 1. Vérifier qui vous êtes
whoami
# alice  (pas root)

# 2. Utiliser sudo
sudo chown bob:staff fichier.txt

# 3. Vérifier
ls -l fichier.txt
# -rw-r--r-- 1 bob staff 1024 Feb  3 10:00 fichier.txt

# 4. Alternative : chgrp fonctionne si membre du groupe
# Alice peut changer groupe vers un groupe dont elle est membre
groups alice
# alice : alice developers staff

chgrp staff fichier.txt  # ✅ Fonctionne si alice est dans staff

# 5. Pour propriétaire, toujours besoin de sudo/root
sudo chown bob fichier.txt

# 6. Batch operation
sudo chown -R bob:staff /path/to/project
```

---

### Erreur 6️⃣ : SGID sur répertoire non appliqué (cas spéciaux)

**Symptôme** :
```bash
chmod g+s /shared/team
ls -ld /shared/team
# drwxrwsr-x 2 root developers 4096 Feb  3 10:00 /shared/team

# Mais nouveaux fichiers n'héritent PAS du groupe
touch /shared/team/file.txt
ls -l /shared/team/file.txt
# -rw-r--r-- 1 alice alice 0 Feb  3 10:00 /shared/team/file.txt
#                   ^^^^^  ← Groupe = alice (pas developers)
```

**Causes possibles** :
1. Système de fichiers ne supporte pas SGID (FAT, exFAT, NTFS)
2. Mount option `nosuid` active

**Solutions** :

```bash
# 1. Vérifier type de filesystem
df -T /shared/team
# Filesystem     Type  ...
# /dev/sdb1      ext4  ...

# 2. Vérifier options de montage
mount | grep /dev/sdb1
# /dev/sdb1 on /shared type ext4 (rw,nosuid,nodev)
#                                    ^^^^^^ ← Problème !

# 3. Remonter sans nosuid
mount -o remount,suid /shared

# 4. Vérifier /etc/fstab
grep /shared /etc/fstab
# /dev/sdb1  /shared  ext4  defaults,nosuid  0  2
#                                   ^^^^^^ ← Retirer

# Corriger fstab
sed -i 's/,nosuid//' /etc/fstab

# 5. Remonter
umount /shared
mount /shared

# 6. Tester à nouveau
touch /shared/team/file2.txt
ls -l /shared/team/file2.txt
# -rw-r--r-- 1 alice developers 0 Feb  3 10:00 /shared/team/file2.txt
#                    ^^^^^^^^^^ ← OK !
```

---

### Erreur 7️⃣ : umask pas appliqué (shell vs système)

**Symptôme** :
```bash
umask 077
umask
# 0077

# Mais après logout/login
umask
# 0022  ← Revenu au défaut
```

**Cause** : umask défini seulement pour la session actuelle.

**Solutions** :

```bash
# 1. Définir dans ~/.bashrc (permanent pour l'utilisateur)
echo "umask 077" >> ~/.bashrc

# 2. Recharger
source ~/.bashrc

# 3. Vérifier
umask
# 0077

# 4. Pour tous les utilisateurs : /etc/profile
echo "umask 022" | sudo tee -a /etc/profile

# 5. Ou /etc/bash.bashrc (Debian/Ubuntu)
echo "umask 022" | sudo tee -a /etc/bash.bashrc

# 6. Vérifier après nouveau login
# Logout puis login
umask
# 0077  ← Persistant

# 7. umask conditionnel (selon utilisateur)
cat >> ~/.bashrc <<'EOF'
# umask sécurisé pour root
if [ $UID -eq 0 ]; then
    umask 077
else
    umask 022
fi
EOF

# 8. Ordre de chargement
# /etc/profile → ~/.bash_profile → ~/.bashrc
# Le dernier défini gagne
```

---

### Erreur 8️⃣ : Sticky bit sur fichier (effet nul)

**Symptôme** :
```bash
chmod +t fichier.txt
ls -l fichier.txt
# -rw-r--r-t 1 user user 1024 Feb  3 10:00 fichier.txt
#         │
#         └─ 't' présent mais sans effet
```

**Cause** : Sticky bit **n'a d'effet que sur répertoires**.

**Solutions** :

```bash
# 1. Sur fichier : sticky bit ignoré (historique Unix)
chmod +t fichier.txt
# → Accepté mais sans effet

# 2. Utilisation correcte : sur répertoire
mkdir /shared/dropbox
chmod +t /shared/dropbox

ls -ld /shared/dropbox
# drwxrwxrwt 2 root root 4096 Feb  3 10:00 /shared/dropbox

# 3. Effet : Seul propriétaire peut supprimer ses fichiers
# Même si répertoire est 777

# 4. Si vous voulez protéger fichier contre suppression
# → Protéger le RÉPERTOIRE parent avec sticky bit
chmod +t /path/to/parent_dir

# 5. Ou retirer permission 'w' du répertoire
chmod u-w /path/to/parent_dir
```

---

### Erreur 9️⃣ : Permissions cassées sur /etc/shadow, login impossible

**Symptôme** :
```bash
su - alice
# su: Authentication failure

# Ou au login système
# Login incorrect
```

**Cause** : Permissions /etc/shadow trop restrictives ou trop ouvertes.

**Solutions** :

```bash
# === Mode recovery (boot single-user) ===

# 1. Booter en mode rescue/single-user
# Au GRUB, ajouter : init=/bin/bash

# 2. Remonter / en read-write
mount -o remount,rw /

# 3. Vérifier permissions /etc/shadow
ls -l /etc/shadow
# -rw------- 1 root root 1234 Feb  3 10:00 /etc/shadow
# (correct)

# Si incorrect (exemple : 644)
# ---------- 1 root root 1234 Feb  3 10:00 /etc/shadow
# ou
# -rw-r--r-- 1 root root 1234 Feb  3 10:00 /etc/shadow

# 4. Corriger : 640 ou 000
chmod 640 /etc/shadow
chown root:shadow /etc/shadow

# Sur certains systèmes
chmod 000 /etc/shadow
chown root:root /etc/shadow

# 5. Vérifier autres fichiers critiques
ls -l /etc/passwd /etc/group /etc/gshadow

# /etc/passwd : 644
chmod 644 /etc/passwd
chown root:root /etc/passwd

# /etc/group : 644
chmod 644 /etc/group
chown root:root /etc/group

# /etc/gshadow : 640 ou 000
chmod 640 /etc/gshadow
chown root:shadow /etc/gshadow

# 6. Redémarrer
sync
reboot

# === Prévention ===

# Script de vérification
cat > check_critical_perms.sh <<'EOF'
#!/bin/bash

check_file() {
    FILE=$1
    EXPECTED_PERMS=$2
    EXPECTED_OWNER=$3
    
    ACTUAL_PERMS=$(stat -c '%a' "$FILE")
    ACTUAL_OWNER=$(stat -c '%U:%G' "$FILE")
    
    if [ "$ACTUAL_PERMS" != "$EXPECTED_PERMS" ] || [ "$ACTUAL_OWNER" != "$EXPECTED_OWNER" ]; then
        echo "❌ $FILE : $ACTUAL_PERMS $ACTUAL_OWNER (attendu: $EXPECTED_PERMS $EXPECTED_OWNER)"
        return 1
    else
        echo "✓ $FILE : OK"
        return 0
    fi
}

check_file /etc/passwd 644 root:root
check_file /etc/shadow 640 root:shadow
check_file /etc/group 644 root:root
check_file /etc/gshadow 640 root:shadow
check_file /etc/sudoers 440 root:root
EOF

chmod +x check_critical_perms.sh
sudo ./check_critical_perms.sh
```

---

### Erreur 🔟 : find -perm mal utilisé (modes vs bits)

**Symptôme** :
```bash
# Chercher fichiers 644
find /home -perm 644
# (résultats incomplets ou vides)
```

**Cause** : Confusion entre correspondance exacte et bits minimums.

**Solutions** :

```bash
# === Correspondance EXACTE : sans préfixe ===

# Fichiers EXACTEMENT 644 (ni plus ni moins)
find /home -type f -perm 644

# Répertoires EXACTEMENT 755
find /home -type d -perm 755

# === Bits MINIMUMS : préfixe - ===

# Fichiers avec AU MOINS 'rw' pour user (6??)
find /home -type f -perm -600

# Fichiers world-writable (??2)
find /home -type f -perm -002

# Fichiers avec SUID (4???)
find / -type f -perm -4000

# === Correspondance QUELCONQUE bit : préfixe / ===

# Fichiers avec 'x' pour user OU group OU other
find /home -type f -perm /111

# Fichiers avec 'w' pour user OU group OU other
find /home -type f -perm /222

# === Exemples pratiques ===

# 1. Fichiers lisibles par others
find /home -type f -perm -004

# 2. Fichiers exécutables (n'importe quel 'x')
find /usr/bin -type f -perm /111

# 3. Fichiers modifiables par group
find /shared -type f -perm -020

# 4. Répertoires accessibles par others
find /var/www -type d -perm -001

# 5. Fichiers SUID ou SGID
find / -type f \( -perm -4000 -o -perm -2000 \)

# === Tableauau récapitulatif ===

# find -perm 644     → EXACTEMENT 644
# find -perm -644    → AU MOINS 644 (tous les bits)
# find -perm /644    → N'IMPORTE QUEL bit de 644

# Exemple :
# Fichier 755 :
# -perm 755   → ✗ (pas exact)
# -perm -755  → ✗ (manque 'w' pour group)
# -perm /755  → ✓ (a au moins un bit de 755)

# Fichier 777 :
# -perm 755   → ✗ (pas exact)
# -perm -755  → ✓ (a au moins rwxr-xr-x)
# -perm /755  → ✓ (a au moins un bit de 755)
```

---

## 📝 Antisèche (Cheat Sheet)

### Types de Fichiers

| Symbole | Type | Exemple |
|---------|------|---------|
| `-` | Fichier régulier | `-rw-r--r-- document.txt` |
| `d` | Directory | `drwxr-xr-x /home` |
| `l` | Lien symbolique | `lrwxrwxrwx /usr/bin/python → python3` |
| `c` | Character device | `crw-rw-rw- /dev/tty` |
| `b` | Block device | `brw-rw---- /dev/sda` |
| `s` | Socket | `srwxrwxrwx /run/docker.sock` |
| `p` | Named pipe | `prw-r--r-- /tmp/fifo` |

### Permissions Symboliques

```
-rwxr-xr-x
│││││││││
│││││││└┴─ Others : r-x (lecture + exécution)
││││└┴┴─── Group  : r-x (lecture + exécution)
│└┴┴────── User   : rwx (tout)
└────────── Type  : fichier régulier
```

### Permissions Octales

| Octal | Binaire | Symbole | Permissions |
|-------|---------|---------|-------------|
| 0 | 000 | `---` | Aucune |
| 1 | 001 | `--x` | Exécution |
| 2 | 010 | `-w-` | Écriture |
| 3 | 011 | `-wx` | Écriture + Exécution |
| 4 | 100 | `r--` | Lecture |
| 5 | 101 | `r-x` | Lecture + Exécution |
| 6 | 110 | `rw-` | Lecture + Écriture |
| 7 | 111 | `rwx` | Tout |

**Permissions courantes** :
- `755` : `-rwxr-xr-x` (exécutables, répertoires)
- `644` : `-rw-r--r--` (fichiers texte)
- `700` : `-rwx------` (privé)
- `600` : `-rw-------` (privé fichiers)
- `777` : `-rwxrwxrwx` (DANGEREUX)

### chmod - Modifier Permissions

```bash
# Symbolique
chmod u+x fichier         # Ajouter exécution user
chmod g-w fichier         # Retirer écriture group
chmod o+r fichier         # Ajouter lecture others
chmod a+x fichier         # Ajouter exécution tous
chmod u=rw,g=r,o= fichier # Définir exactement

# Octal
chmod 755 fichier         # rwxr-xr-x
chmod 644 fichier         # rw-r--r--
chmod 600 fichier         # rw-------
chmod 700 répertoire      # rwx------

# Récursif
chmod -R 755 /path

# Intelligent (X = x seulement pour répertoires)
chmod -R a+X /path
```

### chown / chgrp - Modifier Propriété

```bash
# chown : user et/ou group
chown alice fichier           # Changer user
chown :developers fichier     # Changer group
chown alice:developers fichier # Changer les deux
chown -R alice:dev /path      # Récursif

# chgrp : group seulement
chgrp developers fichier
chgrp -R www-data /var/www

# Référence
chown --reference=file1 file2
```

### Bits Spéciaux

| Bit | Octal | Symbole | chmod | Effet |
|-----|-------|---------|-------|-------|
| SUID | 4 | `s` (user) | `chmod u+s` ou `chmod 4755` | Exécute comme propriétaire |
| SGID | 2 | `s` (group) | `chmod g+s` ou `chmod 2755` | Fichier: exec comme groupe<br>Répertoire: héritage groupe |
| Sticky | 1 | `t` (other) | `chmod +t` ou `chmod 1777` | Seul proprio peut supprimer |

**Exemples** :
```bash
# SUID
chmod 4755 /usr/bin/passwd
ls -l /usr/bin/passwd
# -rwsr-xr-x

# SGID répertoire
chmod 2775 /shared/team
ls -ld /shared/team
# drwxrwsr-x

# Sticky bit
chmod 1777 /tmp
ls -ld /tmp
# drwxrwxrwt
```

### umask - Masque par Défaut

```bash
# Afficher umask
umask           # 0022
umask -S        # u=rwx,g=rx,o=rx

# Définir umask
umask 077       # Privé (600/700)
umask 022       # Standard (644/755)
umask 002       # Collaboration (664/775)

# Calcul : Permissions = Max - umask
# Fichiers : 666 - umask
# Répertoires : 777 - umask

# Permanent
echo "umask 077" >> ~/.bashrc
```

### stat - Informations Détaillées

```bash
# Complet
stat fichier

# Format personnalisé
stat -c '%a' fichier        # Permissions octal
stat -c '%A' fichier        # Permissions symbole
stat -c '%U:%G' fichier     # User:Group
stat -c '%s' fichier        # Taille
stat -c '%F' fichier        # Type

# Tableau
stat -c '%n %a %A %U:%G' *
```

### groups - Lister Groupes

```bash
groups                  # Groupes de l'utilisateur actuel
groups alice            # Groupes de alice
id                      # Infos complètes (UID, GID, groupes)
id alice                # Infos de alice
```

### find - Recherche par Permissions

```bash
# Exact
find /path -perm 644

# Au moins (bits minimums)
find /path -perm -644

# N'importe quel bit
find /path -perm /644

# Exemples
find / -perm -4000      # SUID
find / -perm -2000      # SGID
find / -perm -002       # World-writable
find / -nouser          # Sans propriétaire
find / -nogroup         # Sans groupe
```

---

## 🎯 Points Clés pour l'Examen LPIC-1

### ✅ Concepts à Maîtriser Absolument

1. **Structure permissions : type + user + group + other**
   - Format : `-rwxr-xr-x` (10 caractères)
   - Position 1 : Type (-, d, l, c, b, s, p)
   - Positions 2-4 : User (rwx)
   - Positions 5-7 : Group (rwx)
   - Positions 8-10 : Others (rwx)

2. **Types de fichiers (7 types)**
   - `-` : Fichier régulier
   - `d` : Directory
   - `l` : Symbolic link
   - `c` : Character device (/dev/tty)
   - `b` : Block device (/dev/sda)
   - `s` : Socket
   - `p` : Named pipe (FIFO)

3. **Permissions sur fichiers vs répertoires**
   - **Fichier** : r=lire, w=modifier, x=exécuter
   - **Répertoire** : r=lister (ls), w=créer/supprimer, x=accéder (cd)
   - ⚠️ Répertoire nécessite 'x' pour être utilisable

4. **chmod : 2 syntaxes**
   - **Symbolique** : `chmod u+x fichier`, `chmod go-w fichier`
   - **Octale** : `chmod 755 fichier`, `chmod 644 fichier`
   - `u=user, g=group, o=others, a=all`
   - `+=ajouter, -=retirer, ==définir`

5. **Permissions octales courantes**
   - `755` : Exécutables, répertoires standards
   - `644` : Fichiers texte standards
   - `700` : Répertoires privés (~/.ssh)
   - `600` : Fichiers privés (clés SSH)
   - `777` : **DANGEREUX** (à éviter)

6. **Bits spéciaux (4, 2, 1)**
   - **SUID (4)** : Exécute comme propriétaire (`chmod 4755` ou `chmod u+s`)
   - **SGID (2)** : Fichier=exec comme groupe, Répertoire=héritage groupe (`chmod 2775` ou `chmod g+s`)
   - **Sticky (1)** : Répertoire=seul proprio supprime (`chmod 1777` ou `chmod +t`)

7. **chown et chgrp**
   - `chown user fichier` : Changer propriétaire
   - `chown :group fichier` : Changer groupe
   - `chown user:group fichier` : Changer les deux
   - `chgrp group fichier` : Changer groupe (équivalent `chown :group`)
   - **Seul root peut chown** (sécurité)

8. **umask - Masque de création**
   - Formule : `Permissions = Max - umask`
   - Fichiers max : 666, Répertoires max : 777
   - umask 022 → Fichiers 644, Répertoires 755
   - umask 077 → Fichiers 600, Répertoires 700 (privé)
   - Configuration : ~/.bashrc, /etc/profile

9. **stat - Informations détaillées**
   - `stat fichier` : Affichage complet
   - `stat -c '%a'` : Permissions octal
   - `stat -c '%A'` : Permissions symbole
   - `stat -c '%U:%G'` : Propriétaire:Groupe
   - Timestamps : atime (accès), mtime (modif), ctime (changement métadonnées)

10. **Commandes essentielles**
    - `ls -l` : Lister avec permissions
    - `chmod` : Modifier permissions
    - `chown` : Modifier propriétaire/groupe
    - `chgrp` : Modifier groupe
    - `umask` : Afficher/définir masque
    - `stat` : Informations détaillées
    - `groups` : Lister groupes
    - `id` : UID/GID/groupes

### 📚 Commandes à Connaître par Cœur

```bash
# Permissions
ls -l fichier
ls -ld répertoire
chmod 755 fichier
chmod u+x,go-w fichier
chmod -R 755 /path

# Propriété
chown alice fichier
chown alice:developers fichier
chgrp developers fichier
chown -R user:group /path

# Bits spéciaux
chmod u+s fichier        # SUID
chmod g+s répertoire     # SGID
chmod +t répertoire      # Sticky
chmod 4755 fichier       # SUID + 755
chmod 2775 répertoire    # SGID + 775
chmod 1777 répertoire    # Sticky + 777

# umask
umask
umask 077
umask -S

# stat
stat fichier
stat -c '%a %A %U:%G' fichier

# Groupes
groups
groups alice
id
id alice

# find
find / -perm -4000       # SUID
find / -perm -2000       # SGID
find / -perm -002        # World-writable
find / -nouser           # Sans propriétaire
```

### ⚠️ Pièges d'Examen Fréquents

1. **Permissions répertoire : 'x' obligatoire pour accès**
   - `dr--r--r--` → Impossible d'accéder même avec 'r'
   - Minimum utile : `r-x` (lecture + accès)

2. **chmod récursif -R casse exécutables**
   - Ne pas `chmod -R 644` (retire 'x' partout)
   - Utiliser `find` pour séparer fichiers/répertoires

3. **SGID sur répertoire (héritage groupe) ≠ SGID sur fichier (exec comme groupe)**

4. **Sticky bit : Seulement sur répertoires, ignoré sur fichiers**

5. **umask SOUSTRAIT (pas ajoute)**
   - umask 022 : Fichiers = 666 - 022 = 644

6. **chmod symbolique vs octal**
   - `chmod 644` : DÉFINIT exactement 644
   - `chmod u=rw,g=r,o=r` : Équivalent symbolique

7. **SUID sur scripts shell ignoré** (sécurité noyau)
   - Fonctionne seulement sur binaires compilés

8. **chown nécessite root** (sauf chgrp vers groupe dont on est membre)

9. **find -perm**
   - `-perm 644` : EXACTEMENT 644
   - `-perm -644` : AU MOINS 644
   - `-perm /644` : N'IMPORTE QUEL bit de 644

10. **Permissions /etc/shadow critiques**
    - Doit être 640 (root:shadow) ou 000 (root:root)
    - Si incorrect → login impossible

### 🔑 Valeurs à Mémoriser

**Permissions SSH** :
```
~/.ssh/                  700
~/.ssh/id_rsa            600
~/.ssh/id_rsa.pub        644
~/.ssh/authorized_keys   600
~/.ssh/config            600
```

**Permissions système** :
```
/etc/passwd    644
/etc/shadow    640 ou 000
/etc/group     644
/etc/gshadow   640
/etc/sudoers   440
/tmp           1777 (sticky)
```

**umask courants** :
```
022 → 644/755 (défaut multi-user)
002 → 664/775 (collaboration)
077 → 600/700 (privé)
```

---

## 📖 Résumé Final

Ce cours couvre la **gestion complète des permissions et propriétés** sous Linux :

1. **Structure des permissions** : Type (7 types : -, d, l, c, b, s, p) + User/Group/Others (rwx)

2. **Permissions fichiers vs répertoires** :
   - Fichiers : r=lire, w=modifier, x=exécuter
   - Répertoires : r=lister, w=créer/supprimer, x=accéder (**obligatoire**)

3. **Modes de modification** :
   - **chmod symbolique** : `u+x`, `g-w`, `o=r`, `a+rwx`
   - **chmod octal** : 755 (rwxr-xr-x), 644 (rw-r--r--), 600 (rw-------)

4. **Propriété** :
   - `chown user:group` : Modifier propriétaire et/ou groupe
   - `chgrp group` : Modifier groupe uniquement
   - **Seul root peut chown** (sécurité Unix)

5. **Bits spéciaux** :
   - **SUID (4)** : Exécute comme propriétaire (/usr/bin/passwd)
   - **SGID (2)** : Répertoire=héritage groupe (partages équipes)
   - **Sticky (1)** : Répertoire=protection suppression (/tmp)

6. **umask** : Masque de création (Permissions = Max - umask)
   - 022 → standard (644/755)
   - 077 → privé (600/700)

7. **Outils** :
   - `ls -l` : Visualiser permissions
   - `stat` : Informations détaillées
   - `groups` : Lister groupes
   - `find -perm` : Recherche par permissions

La maîtrise des permissions est **fondamentale** pour la sécurité Linux et la certification LPIC-1. Comprendre la différence fichiers/répertoires, les bits spéciaux, et umask est essentiel.

---

*Documentation créée le 3 février 2026 - Formation Linux Essentials / LPIC-1 - Module 4*
