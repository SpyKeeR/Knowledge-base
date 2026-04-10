# 🔗 Gestion des Liens Durs et Symboliques

## 📋 Informations du Module

- **Module** : 4 - Devices, Linux Filesystems, Filesystem Hierarchy Standard
- **Cours** : Create and Change Hard Links / Create and Change Soft Links
- **Objectif** : Maîtriser la création et gestion des hard links (liens physiques) et soft links (liens symboliques), comprendre les inodes, et utiliser ln/readlink

---

## 🎯 Concepts Fondamentaux

### 🏗️ Architecture Filesystem : Inodes et Données

**Analogie** : Imaginez une bibliothèque :
- **Inode** = Fiche catalogue (métadonnées : auteur, taille, emplacement)
- **Bloc de données** = Livre physique (contenu réel)
- **Hard link** = Plusieurs fiches pointant vers le même livre
- **Soft link** = Note "Voir livre X" (référence)

**Structure filesystem** :

```
┌─────────────────────────────────────────────────────────────┐
│                     SYSTÈME DE FICHIERS                      │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Inode Table                    Data Blocks                 │
│  ┌──────────────┐              ┌──────────────┐            │
│  │ Inode 131072 │──────────────▶│ Bloc 1000   │            │
│  ├──────────────┤              ├──────────────┤            │
│  │ Type: fichier│              │ "Contenu du │            │
│  │ Perms: 644   │              │  fichier..."│            │
│  │ Owner: alice │              └──────────────┘            │
│  │ Size: 1024   │              ┌──────────────┐            │
│  │ Links: 3     │──────────────▶│ Bloc 1001   │            │
│  │ Blocks: ────────────────────▶│ "Suite..."  │            │
│  └──────────────┘              └──────────────┘            │
│        ▲                                                     │
│        │                                                     │
│        │  Pointent vers même inode                          │
│        │                                                     │
│  ┌─────┴─────┬─────────┬─────────┐                         │
│  │ fichier.txt│ backup.txt│ copy.txt│ ◀── Hard Links       │
│  │  (nom 1)   │  (nom 2)  │  (nom 3)│                      │
│  └───────────┴─────────┴─────────┘                         │
│                                                              │
│  ┌──────────────┐              ┌──────────────┐            │
│  │ Inode 131073 │              │ Bloc 2000    │            │
│  ├──────────────┤              ├──────────────┤            │
│  │ Type: symlink│              │"/home/alice/ │            │
│  │ Perms: 777   │              │fichier.txt"  │            │
│  │ Size: 20     │──────────────▶│ (chemin)    │            │
│  └──────────────┘              └──────────────┘            │
│        ▲                                                     │
│        │                                                     │
│  ┌─────┴─────┐                                              │
│  │ lien.txt  │ ◀── Soft Link (Symbolic Link)               │
│  └───────────┘                                              │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### 📊 Qu'est-ce qu'une Inode ?

**Définition** : **Index Node** = Structure de données contenant les métadonnées d'un fichier

**Contenu d'une inode** :
- **Type** : Fichier, répertoire, lien symbolique, device, etc.
- **Permissions** : rwxr-xr-x (mode)
- **Propriété** : UID (user), GID (group)
- **Taille** : En octets
- **Timestamps** : atime (accès), mtime (modification), ctime (changement)
- **Nombre de liens** : Combien de hard links pointent vers cette inode
- **Pointeurs vers blocs** : Adresses des blocs de données sur le disque

**Ce que l'inode ne contient PAS** : Le nom du fichier !

**Visualiser une inode** :
```bash
# Obtenir numéro d'inode
ls -i fichier.txt
# 131072 fichier.txt

# Afficher détails inode avec stat
stat fichier.txt
#   File: fichier.txt
#   Size: 1024      Blocks: 8          IO Block: 4096   regular file
# Device: 802h/2050d  Inode: 131072     Links: 1
# Access: (0644/-rw-r--r--)  Uid: ( 1000/   alice)   Gid: ( 1000/   alice)

# Afficher seulement numéro inode
stat -c '%i' fichier.txt
# 131072

# Lister avec inodes
ls -li
# 131072 -rw-r--r-- 1 alice alice 1024 Feb  3 10:00 fichier.txt
# 131073 lrwxrwxrwx 1 alice alice   20 Feb  3 10:00 lien.txt -> fichier.txt
```

---

## 🔩 Hard Links (Liens Physiques)

### 🎯 Définition et Fonctionnement

**Définition** : Un hard link est un **nom alternatif** pour un fichier existant. Tous les hard links pointent vers **la même inode**.

**Principe** :
- Création d'une nouvelle **entrée de répertoire** (directory entry)
- Pointe vers **l'inode existante**
- Incrémente le **compteur de liens** dans l'inode
- Aucune copie des données

**Schéma** :

```
Directory Entries                Inode Table              Data Blocks
┌─────────────────┐             ┌──────────────┐        ┌──────────┐
│ /home/alice/    │             │ Inode 131072 │        │ "Hello   │
│                 │             ├──────────────┤        │  World"  │
│ fichier.txt ────┼────────────▶│ Links: 3     │───────▶│          │
│ backup.txt  ────┼────────────▶│ Size: 12     │        └──────────┘
│ copy.txt    ────┼────────────▶│ Perms: 644   │
│                 │             └──────────────┘
└─────────────────┘
     │                 │                 │
     └─────────────────┴─────────────────┘
            Tous pointent vers
            la MÊME inode 131072
```

### 📝 Commande ln - Créer Hard Link

**Syntaxe** :
```bash
ln target link_name
```

**Exemples** :

```bash
# 1. Créer fichier original
echo "Contenu important" > original.txt

ls -li original.txt
# 131072 -rw-r--r-- 1 alice alice 18 Feb  3 10:00 original.txt
#        │          │
#        │          └─ Links: 1 (seulement le nom original)
#        └──────────── Inode: 131072

# 2. Créer hard link
ln original.txt hardlink.txt

# 3. Vérifier
ls -li original.txt hardlink.txt
# 131072 -rw-r--r-- 2 alice alice 18 Feb  3 10:00 original.txt
# 131072 -rw-r--r-- 2 alice alice 18 Feb  3 10:00 hardlink.txt
#   │                │
#   │                └─ Links: 2 (incrémenté)
#   └──────────────────── Même inode !

# 4. Modifier via hardlink
echo "Nouveau contenu" > hardlink.txt

# 5. Vérifier propagation
cat original.txt
# Nouveau contenu  ← Modifié aussi !

cat hardlink.txt
# Nouveau contenu

# 6. Créer plusieurs hard links
ln original.txt backup1.txt
ln original.txt backup2.txt

ls -li original.txt hardlink.txt backup1.txt backup2.txt
# 131072 -rw-r--r-- 4 alice alice 16 Feb  3 10:05 original.txt
# 131072 -rw-r--r-- 4 alice alice 16 Feb  3 10:05 hardlink.txt
# 131072 -rw-r--r-- 4 alice alice 16 Feb  3 10:05 backup1.txt
# 131072 -rw-r--r-- 4 alice alice 16 Feb  3 10:05 backup2.txt
#                  │
#                  └─ Links: 4

# 7. Supprimer un lien (les autres persistent)
rm hardlink.txt

ls -li original.txt backup1.txt backup2.txt
# 131072 -rw-r--r-- 3 alice alice 16 Feb  3 10:05 original.txt
# 131072 -rw-r--r-- 3 alice alice 16 Feb  3 10:05 backup1.txt
# 131072 -rw-r--r-- 3 alice alice 16 Feb  3 10:05 backup2.txt
#                  │
#                  └─ Links: 3 (décrémenté)

# Données supprimées seulement quand Links = 0
```

### ⚠️ Limitations des Hard Links

#### 1️⃣ **Impossible de pointer vers un répertoire**

**Raison** : Éviter boucles infinies dans l'arborescence

```bash
mkdir testdir
ln testdir testdir_link
# ln: testdir: hard link not allowed for directory

# ❌ INTERDIT (sauf pour . et .. par le système)
```

**Exception** : Les liens `.` (répertoire actuel) et `..` (répertoire parent) sont des hard links créés par le système.

```bash
ls -lai /home/alice
# 131074 drwxr-xr-x  3 alice alice 4096 Feb  3 10:00 .
# 131073 drwxr-xr-x  5 root  root  4096 Feb  3 10:00 ..
#   │      │           │
#   │      │           └─ Links: 3 (., .., sous-répertoires)
#   │      └──────────────── . et .. sont des hard links
#   └────────────────────── Inode du répertoire
```

#### 2️⃣ **Impossible de traverser les filesystems**

**Raison** : Les inodes sont spécifiques à un filesystem

```bash
# /home sur /dev/sda1
# /mnt/usb sur /dev/sdb1

echo "test" > /home/alice/fichier.txt

ln /home/alice/fichier.txt /mnt/usb/lien.txt
# ln: failed to create hard link '/mnt/usb/lien.txt' => '/home/alice/fichier.txt': Invalid cross-device link

# ❌ IMPOSSIBLE (filesystems différents)
```

**Vérifier filesystem** :
```bash
df -h fichier1.txt fichier2.txt
# Filesystem      Size  Used Avail Use% Mounted on
# /dev/sda1        50G   20G   30G  40% /home
# /dev/sdb1        10G    1G    9G  10% /mnt/usb
#   │                                      │
#   └──────────────────────────────────────┘
#          Filesystems différents !
```

### 🔑 Propriétés des Hard Links

#### ✅ Permissions Partagées

**Les permissions sont stockées dans l'inode** → Modifications propagées à tous les hard links

```bash
# Créer hard links
echo "data" > file.txt
ln file.txt link1.txt
ln file.txt link2.txt

ls -li file.txt link1.txt link2.txt
# 131072 -rw-r--r-- 3 alice alice 5 Feb  3 10:00 file.txt
# 131072 -rw-r--r-- 3 alice alice 5 Feb  3 10:00 link1.txt
# 131072 -rw-r--r-- 3 alice alice 5 Feb  3 10:00 link2.txt

# Modifier permissions via link1
chmod 600 link1.txt

# Vérifier propagation
ls -li file.txt link1.txt link2.txt
# 131072 -rw------- 3 alice alice 5 Feb  3 10:00 file.txt
# 131072 -rw------- 3 alice alice 5 Feb  3 10:00 link1.txt
# 131072 -rw------- 3 alice alice 5 Feb  3 10:00 link2.txt
#        │
#        └─ Tous modifiés (même inode)

# Changer propriétaire via link2
sudo chown bob:staff link2.txt

ls -li file.txt link1.txt link2.txt
# 131072 -rw------- 3 bob staff 5 Feb  3 10:00 file.txt
# 131072 -rw------- 3 bob staff 5 Feb  3 10:00 link1.txt
# 131072 -rw------- 3 bob staff 5 Feb  3 10:00 link2.txt
#                    │   │
#                    └───┴─ Tous modifiés
```

#### ✅ Suppression Sécurisée

**Les données persistent tant que Links > 0**

```bash
# Créer fichier avec plusieurs liens
echo "Important data" > important.txt
ln important.txt backup1.txt
ln important.txt backup2.txt
ln important.txt backup3.txt

ls -li important.txt backup*.txt
# 131072 -rw-r--r-- 4 alice alice 15 Feb  3 10:00 important.txt
# 131072 -rw-r--r-- 4 alice alice 15 Feb  3 10:00 backup1.txt
# 131072 -rw-r--r-- 4 alice alice 15 Feb  3 10:00 backup2.txt
# 131072 -rw-r--r-- 4 alice alice 15 Feb  3 10:00 backup3.txt

# Supprimer "original"
rm important.txt

ls -li backup*.txt
# 131072 -rw-r--r-- 3 alice alice 15 Feb  3 10:00 backup1.txt
# 131072 -rw-r--r-- 3 alice alice 15 Feb  3 10:00 backup2.txt
# 131072 -rw-r--r-- 3 alice alice 15 Feb  3 10:00 backup3.txt
#                  │
#                  └─ Links: 3 (décrémenté)

# Données toujours accessibles
cat backup1.txt
# Important data  ← Toujours là !

# Supprimer tous les liens sauf un
rm backup1.txt backup2.txt

ls -li backup3.txt
# 131072 -rw-r--r-- 1 alice alice 15 Feb  3 10:00 backup3.txt
#                  │
#                  └─ Links: 1 (dernier lien)

# Supprimer dernier lien
rm backup3.txt

# Maintenant Links = 0 → inode libérée, données supprimées
```

**Note** : `rm` ne "supprime" pas vraiment, il **décrémente le compteur de liens**. Quand compteur = 0, le système libère l'inode et les blocs de données.

### 🔍 Trouver tous les Hard Links d'un Fichier

```bash
# Méthode 1 : Via numéro d'inode
INODE=$(stat -c '%i' fichier.txt)
find / -inum $INODE 2>/dev/null

# Exemple complet
echo "test" > original.txt
ln original.txt link1.txt
ln original.txt /tmp/link2.txt

stat -c '%i' original.txt
# 131072

find /home /tmp -inum 131072 2>/dev/null
# /home/alice/original.txt
# /home/alice/link1.txt
# /tmp/link2.txt

# Méthode 2 : Script
cat > find_hardlinks.sh <<'EOF'
#!/bin/bash
FILE="$1"
INODE=$(stat -c '%i' "$FILE")
echo "Recherche hard links pour inode $INODE :"
find / -inum "$INODE" 2>/dev/null
EOF

chmod +x find_hardlinks.sh
./find_hardlinks.sh original.txt
```

---

## 🔗 Soft Links / Symbolic Links (Liens Symboliques)

### 🎯 Définition et Fonctionnement

**Définition** : Un soft link (ou symlink) est un **fichier spécial** contenant le **chemin** vers un autre fichier.

**Principe** :
- Création d'une **nouvelle inode** (type: symbolic link)
- Contenu = **chemin vers la cible** (texte)
- Taille = **longueur du chemin**
- Redirection transparente par le noyau

**Schéma** :

```
Directory Entries                Inode Table              Data Blocks
┌─────────────────┐             ┌──────────────┐        ┌──────────┐
│ /home/alice/    │             │ Inode 131072 │        │ "Hello   │
│                 │             ├──────────────┤        │  World"  │
│ fichier.txt ────┼────────────▶│ Type: file   │───────▶│          │
│                 │             │ Links: 1     │        └──────────┘
│                 │             │ Size: 12     │
│                 │             └──────────────┘
│                 │
│                 │             ┌──────────────┐        ┌──────────┐
│ lien.txt    ────┼────────────▶│ Inode 131073 │        │"/home/   │
│                 │             ├──────────────┤        │ alice/   │
└─────────────────┘             │ Type: symlink│───────▶│fichier.  │
                                │ Links: 1     │        │txt"      │
                                │ Size: 20     │        └──────────┘
                                └──────────────┘
                                        │
                                        │ Résolution
                                        ▼
                                  Inode 131072
                                (fichier cible)
```

### 📝 Commande ln -s - Créer Soft Link

**Syntaxe** :
```bash
ln -s target link_name
```

**Exemples** :

```bash
# 1. Créer fichier original
echo "Contenu original" > original.txt

ls -li original.txt
# 131072 -rw-r--r-- 1 alice alice 17 Feb  3 10:00 original.txt

# 2. Créer lien symbolique
ln -s original.txt symlink.txt

# 3. Vérifier
ls -li original.txt symlink.txt
# 131072 -rw-r--r-- 1 alice alice 17 Feb  3 10:00 original.txt
# 131073 lrwxrwxrwx 1 alice alice 12 Feb  3 10:00 symlink.txt -> original.txt
#   │    │          │            │                     │
#   │    │          │            └─ Taille = longueur du chemin "original.txt" = 12 octets
#   │    │          └────────────── Links: 1 (symlink a sa propre inode)
#   │    └───────────────────────── Type: l (symbolic link)
#   └────────────────────────────── Inode différente !

# 4. Lire via symlink
cat symlink.txt
# Contenu original  ← Redirection transparente

# 5. Modifier via symlink
echo "Nouveau contenu" > symlink.txt

cat original.txt
# Nouveau contenu  ← Fichier original modifié

# 6. Vérifier avec stat
stat symlink.txt
#   File: symlink.txt -> original.txt
#   Size: 12        Blocks: 0          IO Block: 4096   symbolic link
# Device: 802h/2050d  Inode: 131073     Links: 1
# Access: (0777/lrwxrwxrwx)  Uid: ( 1000/   alice)   Gid: ( 1000/   alice)
```

### 🛣️ Chemins Absolus vs Relatifs

#### Chemin Absolu

**Définition** : Chemin complet depuis la racine `/`

**Avantages** :
- Fonctionne **depuis n'importe où**
- Pas de surprise lors de déplacements

**Inconvénients** :
- Casse si la cible est déplacée
- Moins portable

```bash
# Créer avec chemin absolu
ln -s /home/alice/documents/rapport.txt /tmp/lien_rapport.txt

ls -l /tmp/lien_rapport.txt
# lrwxrwxrwx 1 alice alice 32 Feb  3 10:00 /tmp/lien_rapport.txt -> /home/alice/documents/rapport.txt
#                                                                     │
#                                                                     └─ Chemin absolu

# Fonctionne depuis n'importe quel répertoire
cd /var/log
cat /tmp/lien_rapport.txt  # ✅ OK

# Mais si rapport.txt est déplacé...
mv /home/alice/documents/rapport.txt /home/alice/rapport.txt

cat /tmp/lien_rapport.txt
# cat: /tmp/lien_rapport.txt: No such file or directory  ❌ Cassé !

ls -l /tmp/lien_rapport.txt
# lrwxrwxrwx 1 alice alice 32 Feb  3 10:00 /tmp/lien_rapport.txt -> /home/alice/documents/rapport.txt
#                                                                                          ^^^^^^^^^^^^^^
#                                                                                          Lien mort (rouge dans ls coloré)
```

#### Chemin Relatif

**Définition** : Chemin **relatif à l'emplacement du lien symbolique**

**Avantages** :
- Résistant aux déplacements **si lien et cible bougent ensemble**
- Portable

**Inconvénients** :
- Doit être calculé correctement
- Peut casser si lien déplacé seul

```bash
# Structure
/home/alice/project/
├── src/
│   └── main.py
└── bin/
    └── run.sh -> ../src/main.py  (lien relatif)

# Créer avec chemin relatif (depuis /home/alice/project/bin/)
cd /home/alice/project/bin
ln -s ../src/main.py run.sh

ls -l run.sh
# lrwxrwxrwx 1 alice alice 14 Feb  3 10:00 run.sh -> ../src/main.py
#                                                      │
#                                                      └─ Relatif au répertoire du lien (bin/)

# Résolution :
# run.sh est dans : /home/alice/project/bin/
# Cible : ../src/main.py
# Résolu : /home/alice/project/bin/../src/main.py
#        = /home/alice/project/src/main.py  ✅

# Déplacer tout le projet
mv /home/alice/project /home/bob/myproject

# Le lien fonctionne toujours !
cat /home/bob/myproject/bin/run.sh  # ✅ OK
# Car la relation relative est préservée

# Mais si on déplace seulement le lien...
mv /home/bob/myproject/bin/run.sh /tmp/

cat /tmp/run.sh
# cat: /tmp/run.sh: No such file or directory  ❌ Cassé !
# Résolution depuis /tmp/ : /tmp/../src/main.py = /src/main.py (n'existe pas)
```

**Règle** : Utiliser chemin relatif si lien et cible sont dans **même arborescence** et bougent ensemble.

#### Calcul Chemin Relatif

```bash
# De /home/alice/project/bin/ vers /home/alice/project/src/main.py
# 1. Remonter de bin/ vers project/ : ..
# 2. Descendre dans src/ : src/
# 3. Fichier : main.py
# Résultat : ../src/main.py

# De /home/alice/docs/ vers /home/alice/downloads/file.pdf
# Même niveau → chemin frère
# Résultat : ../downloads/file.pdf

# De /home/alice/a/b/c/ vers /home/alice/x/y/file.txt
# 1. Remonter de c/ vers a/ : ../../..
# 2. Descendre dans x/y/ : x/y/
# 3. Fichier : file.txt
# Résultat : ../../../x/y/file.txt
```

### 🌟 Avantages des Soft Links

#### ✅ Peut pointer vers un répertoire

```bash
# Créer lien vers répertoire
ln -s /var/log /home/alice/logs

ls -ld /home/alice/logs
# lrwxrwxrwx 1 alice alice 8 Feb  3 10:00 /home/alice/logs -> /var/log

# Utiliser comme répertoire normal
cd /home/alice/logs
ls
# syslog  auth.log  kern.log  ...

# pwd montre le chemin via le lien
pwd
# /home/alice/logs

# pwd -P montre le chemin réel (résolu)
pwd -P
# /var/log
```

#### ✅ Peut traverser filesystems

```bash
# /home sur /dev/sda1
# /mnt/usb sur /dev/sdb1

ln -s /mnt/usb/backup.tar.gz /home/alice/backup_link.tar.gz

ls -l /home/alice/backup_link.tar.gz
# lrwxrwxrwx 1 alice alice 24 Feb  3 10:00 backup_link.tar.gz -> /mnt/usb/backup.tar.gz

# ✅ FONCTIONNE (soft link peut traverser filesystems)

# Vérifier
df -h /home/alice/backup_link.tar.gz
# Filesystem      Size  Used Avail Use% Mounted on
# /dev/sdb1        10G    5G    5G  50% /mnt/usb
#                                          │
#                                          └─ Pointe bien vers /mnt/usb
```

#### ✅ Peut pointer vers cible inexistante

```bash
# Créer lien vers fichier qui n'existe pas encore
ln -s /tmp/future_file.txt /home/alice/future_link.txt

ls -l /home/alice/future_link.txt
# lrwxrwxrwx 1 alice alice 19 Feb  3 10:00 future_link.txt -> /tmp/future_file.txt
#                                                                    ^^^^^^^^^^^^^^^^
#                                                                    Lien mort (pour l'instant)

cat /home/alice/future_link.txt
# cat: /home/alice/future_link.txt: No such file or directory

# Créer la cible
echo "Contenu" > /tmp/future_file.txt

# Maintenant le lien fonctionne
cat /home/alice/future_link.txt
# Contenu  ✅
```

### 📖 Commande readlink - Lire Cible du Lien

**Syntaxe** :
```bash
readlink [options] symlink
```

**Options importantes** :

| Option | Description |
|--------|-------------|
| `-f` | Résoudre récursivement (suivre tous les liens) |
| `-e` | Résoudre et vérifier que la cible existe |
| `-m` | Résoudre sans vérifier l'existence |
| `-n` | Ne pas ajouter newline |

**Exemples** :

```bash
# 1. Créer structure de liens
echo "data" > /tmp/original.txt
ln -s /tmp/original.txt link1.txt
ln -s link1.txt link2.txt
ln -s link2.txt link3.txt

# 2. readlink simple (1 niveau)
readlink link3.txt
# link2.txt  ← Seulement la cible directe

readlink link2.txt
# link1.txt

readlink link1.txt
# /tmp/original.txt

# 3. readlink -f (récursif, résolution complète)
readlink -f link3.txt
# /tmp/original.txt  ← Suit toute la chaîne

# 4. ls -l (peut tronquer)
ln -s /very/long/path/to/some/deeply/nested/directory/with/a/very/long/filename.txt short.txt

ls -l short.txt
# lrwxrwxrwx 1 alice alice 75 Feb  3 10:00 short.txt -> /very/long/path/to/some/deeply/nested/di...
#                                                                                                ^^^
#                                                                                                Tronqué !

# readlink affiche tout
readlink short.txt
# /very/long/path/to/some/deeply/nested/directory/with/a/very/long/filename.txt  ← Complet

# 5. Différence -e vs -m
# -e : Erreur si cible n'existe pas
readlink -e broken_link.txt
# readlink: broken_link.txt: No such file or directory

# -m : Retourne chemin même si inexistant
readlink -m broken_link.txt
# /home/alice/nonexistent.txt

# 6. Vérifier si lien symbolique
if [ -L fichier.txt ]; then
    echo "C'est un lien symbolique vers : $(readlink fichier.txt)"
else
    echo "Pas un lien symbolique"
fi

# 7. Script : résoudre tous les liens dans un répertoire
for link in *.txt; do
    if [ -L "$link" ]; then
        echo "$link -> $(readlink -f "$link")"
    fi
done
```

### 🔑 Permissions des Soft Links

**Permissions affichées** : `rwxrwxrwx` (777) **toujours**

**Raison** : Les permissions du lien **ne sont pas utilisées**. Le noyau utilise les permissions de **la cible**.

```bash
# Créer fichier avec permissions restrictives
echo "secret" > secret.txt
chmod 600 secret.txt

ls -l secret.txt
# -rw------- 1 alice alice 7 Feb  3 10:00 secret.txt

# Créer lien symbolique
ln -s secret.txt link_secret.txt

ls -l link_secret.txt
# lrwxrwxrwx 1 alice alice 10 Feb  3 10:00 link_secret.txt -> secret.txt
#  │││││││││
#  └┴┴┴┴┴┴┴┴─ 777 affiché, mais pas utilisé

# Tenter lecture en tant qu'utilisateur bob
su - bob
cat /home/alice/link_secret.txt
# cat: /home/alice/link_secret.txt: Permission denied
# ❌ Refusé car secret.txt a permissions 600 (pas le lien)

# chmod sur le lien n'a AUCUN effet
chmod 000 link_secret.txt  # Accepté mais ignoré

ls -l link_secret.txt
# lrwxrwxrwx 1 alice alice 10 Feb  3 10:00 link_secret.txt -> secret.txt
#  │││││││││
#  └┴┴┴┴┴┴┴┴─ Toujours 777 !

# Les permissions de la CIBLE comptent
chmod 644 secret.txt
su - bob
cat /home/alice/link_secret.txt
# secret  ✅ Fonctionne maintenant (secret.txt est 644)
```

**Note** : Certains systèmes supportent `lchmod` (change permissions du lien lui-même), mais très rare.

### 🔗 Chaînes de Liens Symboliques

**Linux supporte liens symboliques vers d'autres liens symboliques**

```bash
# Créer chaîne
echo "final data" > target.txt
ln -s target.txt link1.txt
ln -s link1.txt link2.txt
ln -s link2.txt link3.txt
ln -s link3.txt link4.txt

# Visualiser
ls -l target.txt link*.txt
# -rw-r--r-- 1 alice alice 11 Feb  3 10:00 target.txt
# lrwxrwxrwx 1 alice alice 10 Feb  3 10:00 link1.txt -> target.txt
# lrwxrwxrwx 1 alice alice  9 Feb  3 10:00 link2.txt -> link1.txt
# lrwxrwxrwx 1 alice alice  9 Feb  3 10:00 link3.txt -> link2.txt
# lrwxrwxrwx 1 alice alice  9 Feb  3 10:00 link4.txt -> link3.txt

# Lire via link4 (résolution récursive)
cat link4.txt
# final data  ✅ Fonctionne

# Résoudre avec readlink -f
readlink -f link4.txt
# /home/alice/target.txt

# Limite : Noyau limite la profondeur (40 niveaux par défaut)
# Au-delà : "Too many levels of symbolic links"
```

---

## 📊 Comparaison Hard Links vs Soft Links

| Caractéristique | Hard Link | Soft Link (Symlink) |
|-----------------|-----------|---------------------|
| **Inode** | Partage la même inode | Inode séparée (type: symlink) |
| **Contenu** | Pointe vers blocs de données | Contient le chemin (texte) |
| **Répertoires** | ❌ Impossible | ✅ Possible |
| **Cross-filesystem** | ❌ Impossible | ✅ Possible |
| **Cible inexistante** | ❌ Impossible | ✅ Possible (lien mort) |
| **Taille** | Taille des données | Taille du chemin |
| **Permissions** | Partagées (inode) | Ignorées (utilise cible) |
| **Suppression original** | Données persistent | Lien mort |
| **Performance** | Légèrement plus rapide | Résolution supplémentaire |
| **Détection** | `ls -i` (même inode) | `ls -l` (type `l`) |
| **Commande** | `ln target link` | `ln -s target link` |

**Cas d'usage** :

**Hard Links** :
- Backups/sauvegardes (économie d'espace)
- Fichiers système critiques
- Garantir données même si nom supprimé

**Soft Links** :
- Raccourcis (comme Windows)
- Liens vers répertoires
- Liens cross-filesystem
- Compatibilité (chemins alternatifs)

---

## 🎓 10 Scénarios Pratiques Complets

### Scénario 1️⃣ : Backup incrémental avec hard links (économie d'espace)

**Contexte** : Créer backups quotidiens sans dupliquer fichiers inchangés.

```bash
# === Principe : rsync --link-dest ===

# 1. Premier backup (complet)
rsync -a /home/alice/documents/ /backup/2026-02-01/

du -sh /backup/2026-02-01
# 2.5G  /backup/2026-02-01

# 2. Deuxième backup (incrémental avec hard links)
rsync -a --link-dest=/backup/2026-02-01 /home/alice/documents/ /backup/2026-02-02/

# 3. Analyser
du -sh /backup/2026-02-02
# 50M  /backup/2026-02-02  ← Seulement nouveaux/modifiés !

# Total réel
du -sh /backup/
# 2.55G  /backup  ← Pas 2.5G + 2.5G = 5G !

# 4. Vérifier hard links
ls -li /backup/2026-02-01/report.pdf /backup/2026-02-02/report.pdf
# 131072 -rw-r--r-- 2 alice alice 1048576 Feb  1 10:00 /backup/2026-02-01/report.pdf
# 131072 -rw-r--r-- 2 alice alice 1048576 Feb  1 10:00 /backup/2026-02-02/report.pdf
#   │                │
#   │                └─ Links: 2 (fichier inchangé, hard link créé)
#   └──────────────────── Même inode

# Fichier modifié → nouvelle inode
ls -li /backup/2026-02-01/notes.txt /backup/2026-02-02/notes.txt
# 131073 -rw-r--r-- 1 alice alice 2048 Feb  1 10:00 /backup/2026-02-01/notes.txt
# 131074 -rw-r--r-- 1 alice alice 2100 Feb  2 15:30 /backup/2026-02-02/notes.txt
#   │       │                                                       │
#   └───────┼───────────────────────────────────────────────────────┘
#           └─ Links: 1 (fichier modifié, copie complète)

# 5. Script backup incrémental
cat > backup_incremental.sh <<'EOF'
#!/bin/bash

SOURCE="/home/alice/documents"
BACKUP_ROOT="/backup"
DATE=$(date +%Y-%m-%d)
LATEST=$(ls -1d $BACKUP_ROOT/2* 2>/dev/null | tail -1)

if [ -z "$LATEST" ]; then
    # Premier backup
    rsync -av "$SOURCE/" "$BACKUP_ROOT/$DATE/"
else
    # Incrémental avec hard links
    rsync -av --link-dest="$LATEST" "$SOURCE/" "$BACKUP_ROOT/$DATE/"
fi

echo "Backup créé : $BACKUP_ROOT/$DATE"
EOF

chmod +x backup_incremental.sh

# 6. Exécuter quotidiennement (cron)
# 0 2 * * * /home/alice/backup_incremental.sh
```

---

### Scénario 2️⃣ : Versionning de fichiers avec hard links

**Contexte** : Conserver versions d'un fichier sans dupliquer données.

```bash
# 1. Créer système de versions
mkdir -p /home/alice/project/.versions

# 2. Fichier initial
echo "Version 1.0" > /home/alice/project/config.txt

# 3. Créer hard link version
ln /home/alice/project/config.txt /home/alice/project/.versions/config.txt.v1

ls -li /home/alice/project/config.txt /home/alice/project/.versions/config.txt.v1
# 131072 -rw-r--r-- 2 alice alice 12 Feb  3 10:00 /home/alice/project/config.txt
# 131072 -rw-r--r-- 2 alice alice 12 Feb  3 10:00 /home/alice/project/.versions/config.txt.v1

# 4. Modifier fichier (casse hard link)
# IMPORTANT : Ne PAS utiliser echo > (troncature in-place)
# Utiliser éditeur ou temp file
echo "Version 2.0" > /tmp/config.txt.tmp
mv /tmp/config.txt.tmp /home/alice/project/config.txt

# 5. Vérifier
ls -li /home/alice/project/config.txt /home/alice/project/.versions/config.txt.v1
# 131073 -rw-r--r-- 1 alice alice 12 Feb  3 11:00 /home/alice/project/config.txt
# 131072 -rw-r--r-- 1 alice alice 12 Feb  3 10:00 /home/alice/project/.versions/config.txt.v1
#   │       │                                                         │       │
#   │       └─ Links: 1 (nouveau fichier)                            │       └─ Links: 1 (ancienne version préservée)
#   └─ Inode différente                                              └─ Ancienne inode

cat /home/alice/project/.versions/config.txt.v1
# Version 1.0  ← Préservée !

# 6. Créer version 2
ln /home/alice/project/config.txt /home/alice/project/.versions/config.txt.v2

# 7. Script automatique
cat > save_version.sh <<'EOF'
#!/bin/bash
FILE="$1"
VERSIONS_DIR="$(dirname "$FILE")/.versions"
BASENAME="$(basename "$FILE")"

mkdir -p "$VERSIONS_DIR"

# Trouver prochain numéro de version
LAST_VERSION=$(ls -1 "$VERSIONS_DIR/$BASENAME".v* 2>/dev/null | sed 's/.*\.v//' | sort -n | tail -1)
NEXT_VERSION=$((LAST_VERSION + 1))

# Créer hard link version
ln "$FILE" "$VERSIONS_DIR/$BASENAME.v$NEXT_VERSION"

echo "Version $NEXT_VERSION sauvegardée : $VERSIONS_DIR/$BASENAME.v$NEXT_VERSION"
EOF

chmod +x save_version.sh

# Utiliser
./save_version.sh /home/alice/project/config.txt
# Version 3 sauvegardée : /home/alice/project/.versions/config.txt.v3

# 8. Restaurer version
cat > restore_version.sh <<'EOF'
#!/bin/bash
FILE="$1"
VERSION="$2"
VERSIONS_DIR="$(dirname "$FILE")/.versions"
BASENAME="$(basename "$FILE")"

if [ ! -f "$VERSIONS_DIR/$BASENAME.v$VERSION" ]; then
    echo "Version $VERSION introuvable"
    exit 1
fi

cp "$VERSIONS_DIR/$BASENAME.v$VERSION" "$FILE"
echo "Version $VERSION restaurée"
EOF

chmod +x restore_version.sh
./restore_version.sh /home/alice/project/config.txt 1
```

---

### Scénario 3️⃣ : Liens symboliques pour chemins compatibles

**Contexte** : Application attend fichier à `/usr/local/bin/python`, mais Python installé dans `/usr/bin/python3.11`.

```bash
# 1. Vérifier installation Python
which python3.11
# /usr/bin/python3.11

# 2. Application cherche "python" (générique)
/usr/local/bin/some_app
# Error: /usr/local/bin/python not found

# 3. Créer lien symbolique
ln -s /usr/bin/python3.11 /usr/local/bin/python

ls -l /usr/local/bin/python
# lrwxrwxrwx 1 root root 19 Feb  3 10:00 /usr/local/bin/python -> /usr/bin/python3.11

# 4. Vérifier
/usr/local/bin/python --version
# Python 3.11.7

# 5. Cas complexe : Alternatives (Debian/Ubuntu)
update-alternatives --list python
# /usr/bin/python3.11
# /usr/bin/python3.10

update-alternatives --install /usr/bin/python python /usr/bin/python3.11 1
update-alternatives --install /usr/bin/python python /usr/bin/python3.10 2

ls -l /usr/bin/python
# lrwxrwxrwx 1 root root 24 Feb  3 10:00 /usr/bin/python -> /etc/alternatives/python

ls -l /etc/alternatives/python
# lrwxrwxrwx 1 root root 19 Feb  3 10:00 /etc/alternatives/python -> /usr/bin/python3.10

# Changer version
update-alternatives --config python
# Selection    Path                Priority   Status
# * 0          /usr/bin/python3.10  2         auto mode
#   1          /usr/bin/python3.11  1         manual mode
#   2          /usr/bin/python3.10  2         manual mode
```

---

### Scénario 4️⃣ : Répertoires partagés avec symlinks

**Contexte** : Partager répertoire `/var/www/project` dans home utilisateur.

```bash
# 1. Projet dans /var/www (ownership root:www-data)
ls -ld /var/www/mysite
# drwxr-xr-x 5 root www-data 4096 Feb  3 10:00 /var/www/mysite

# 2. Utilisateur alice (membre de www-data)
groups alice
# alice : alice www-data

# 3. Créer lien dans home
ln -s /var/www/mysite /home/alice/mysite

ls -ld /home/alice/mysite
# lrwxrwxrwx 1 alice alice 16 Feb  3 10:00 /home/alice/mysite -> /var/www/mysite

# 4. Utiliser normalement
cd /home/alice/mysite
ls
# index.html  css/  js/  uploads/

# 5. pwd vs pwd -P
pwd
# /home/alice/mysite  ← Chemin via lien

pwd -P
# /var/www/mysite  ← Chemin réel

# 6. Modifications via lien
echo "Update" >> /home/alice/mysite/index.html

# Vérifier dans répertoire réel
cat /var/www/mysite/index.html | tail -1
# Update  ← Modifié

# 7. Cas : Lien vers sous-répertoire
ln -s /var/www/mysite/uploads /home/alice/uploads_rapide

cd /home/alice/uploads_rapide
# → Accès direct à /var/www/mysite/uploads
```

---

### Scénario 5️⃣ : Détecter et nettoyer liens morts (broken symlinks)

**Contexte** : Trouver liens symboliques cassés après réorganisation.

```bash
# 1. Créer quelques liens morts
ln -s /tmp/deleted.txt link1.txt
ln -s /nonexistent/file.txt link2.txt
ln -s /home/alice/moved.txt link3.txt

# 2. Identifier liens morts avec find
find /home/alice -type l -xtype l

# Explication :
# -type l : Trouver symbolic links
# -xtype l : Dont la cible résout en symbolic link (= lien mort)

# Ou plus explicite :
find /home/alice -xtype l

# Résultat :
# /home/alice/link1.txt
# /home/alice/link2.txt
# /home/alice/link3.txt

# 3. Vérifier avec ls (liens morts en rouge/clignotant)
ls -l link*.txt
# lrwxrwxrwx 1 alice alice 16 Feb  3 10:00 link1.txt -> /tmp/deleted.txt
# lrwxrwxrwx 1 alice alice 21 Feb  3 10:00 link2.txt -> /nonexistent/file.txt
# lrwxrwxrwx 1 alice alice 21 Feb  3 10:00 link3.txt -> /home/alice/moved.txt

# 4. Vérifier avec test
for link in link*.txt; do
    if [ -L "$link" ] && [ ! -e "$link" ]; then
        echo "Lien mort : $link -> $(readlink "$link")"
    fi
done

# 5. Script détection complet
cat > find_broken_links.sh <<'EOF'
#!/bin/bash

SEARCH_DIR="${1:-.}"

echo "Recherche liens morts dans : $SEARCH_DIR"
echo "─────────────────────────────────────────"

find "$SEARCH_DIR" -type l | while read -r link; do
    if [ ! -e "$link" ]; then
        target=$(readlink "$link")
        echo "❌ $link -> $target"
    fi
done
EOF

chmod +x find_broken_links.sh
./find_broken_links.sh /home/alice

# 6. Supprimer liens morts
find /home/alice -xtype l -delete

# 7. Ou interactif
find /home/alice -xtype l -ok rm {} \;
# < rm ... /home/alice/link1.txt > ? y
# < rm ... /home/alice/link2.txt > ? y

# 8. Réparer liens (si cible déplacée)
# Exemple : moved.txt déplacé dans /home/alice/documents/
rm link3.txt
ln -s /home/alice/documents/moved.txt link3.txt

# 9. Script de réparation automatique
cat > repair_links.sh <<'EOF'
#!/bin/bash

# Chercher fichier déplacé et mettre à jour lien
OLD_TARGET="$1"
NEW_TARGET="$2"
LINK="$3"

if [ -L "$LINK" ] && [ "$(readlink "$LINK")" = "$OLD_TARGET" ]; then
    rm "$LINK"
    ln -s "$NEW_TARGET" "$LINK"
    echo "✓ Lien réparé : $LINK -> $NEW_TARGET"
else
    echo "✗ Erreur : $LINK ne pointe pas vers $OLD_TARGET"
fi
EOF

chmod +x repair_links.sh
./repair_links.sh /home/alice/moved.txt /home/alice/documents/moved.txt link3.txt
```

---

### Scénario 6️⃣ : Organisation logs avec rotation (symlink 'current')

**Contexte** : Pointer toujours vers log le plus récent via lien symbolique.

```bash
# 1. Structure logs avec rotation
mkdir -p /var/log/myapp

# 2. Créer logs datés
touch /var/log/myapp/app-2026-02-01.log
touch /var/log/myapp/app-2026-02-02.log
touch /var/log/myapp/app-2026-03.log

# 3. Créer lien 'current' vers le plus récent
ln -s /var/log/myapp/app-2026-02-03.log /var/log/myapp/current.log

ls -l /var/log/myapp/current.log
# lrwxrwxrwx 1 root root 30 Feb  3 10:00 current.log -> /var/log/myapp/app-2026-02-03.log

# 4. Application écrit toujours dans 'current'
echo "Log entry" >> /var/log/myapp/current.log

# 5. Rotation quotidienne (logrotate ou script)
cat > rotate_log.sh <<'EOF'
#!/bin/bash

APP="myapp"
LOG_DIR="/var/log/$APP"
DATE=$(date +%Y-%m-%d)
NEW_LOG="$LOG_DIR/app-$DATE.log"
CURRENT_LINK="$LOG_DIR/current.log"

# Créer nouveau log
touch "$NEW_LOG"

# Mettre à jour lien
ln -sf "$NEW_LOG" "$CURRENT_LINK"

echo "Log rotated: $CURRENT_LINK -> $NEW_LOG"

# Compresser anciens logs (>7 jours)
find "$LOG_DIR" -name "app-*.log" -mtime +7 -exec gzip {} \;
EOF

chmod +x rotate_log.sh

# 6. Cron quotidien (0h01)
# 1 0 * * * /usr/local/bin/rotate_log.sh

# 7. Application lit toujours 'current.log'
tail -f /var/log/myapp/current.log
# → Suit le fichier actuel même après rotation (si re-ouvert)

# 8. Lister historique
ls -lhtr /var/log/myapp/
# -rw-r--r-- 1 root root 1.2K Feb  1 23:59 app-2026-02-01.log.gz
# -rw-r--r-- 1 root root 1.5K Feb  2 23:59 app-2026-02-02.log.gz
# -rw-r--r-- 1 root root 856  Feb  3 10:15 app-2026-02-03.log
# lrwxrwxrwx 1 root root 30   Feb  3 00:01 current.log -> app-2026-02-03.log
```

---

### Scénario 7️⃣ : Déploiement web avec symlinks (blue/green deployment)

**Contexte** : Basculer entre versions d'application sans downtime.

```bash
# 1. Structure déploiement
/var/www/myapp/
├── releases/
│   ├── v1.0/
│   ├── v1.1/
│   └── v2.0/
├── current -> releases/v1.1  (symlink)
└── shared/
    ├── uploads/
    └── config/

# 2. Configuration Apache/Nginx pointe vers 'current'
# DocumentRoot /var/www/myapp/current

# 3. Déployer nouvelle version
mkdir -p /var/www/myapp/releases/v2.0
rsync -a /tmp/deploy/ /var/www/myapp/releases/v2.0/

# 4. Créer liens vers shared
ln -s /var/www/myapp/shared/uploads /var/www/myapp/releases/v2.0/uploads
ln -s /var/www/myapp/shared/config /var/www/myapp/releases/v2.0/config

# 5. Tester v2.0
curl http://localhost/test -H "Host: myapp.com" --resolve myapp.com:80:127.0.0.1

# 6. Basculer (atomic)
ln -sfn /var/www/myapp/releases/v2.0 /var/www/myapp/current

ls -l /var/www/myapp/current
# lrwxrwxrwx 1 www-data www-data 28 Feb  3 10:20 current -> releases/v2.0
#                                                                      ^^^^^ Mis à jour

# 7. Vérifier en production
curl http://myapp.com
# → Nouvelle version

# 8. Rollback instantané si problème
ln -sfn /var/www/myapp/releases/v1.1 /var/www/myapp/current

# 9. Script déploiement
cat > deploy.sh <<'EOF'
#!/bin/bash

VERSION="$1"
APP_ROOT="/var/www/myapp"
RELEASE_DIR="$APP_ROOT/releases/$VERSION"
CURRENT_LINK="$APP_ROOT/current"

if [ -z "$VERSION" ]; then
    echo "Usage: $0 <version>"
    exit 1
fi

if [ ! -d "$RELEASE_DIR" ]; then
    echo "Release $VERSION not found"
    exit 1
fi

# Backup current symlink
PREVIOUS=$(readlink "$CURRENT_LINK")

# Switch
ln -sfn "$RELEASE_DIR" "$CURRENT_LINK"

echo "✓ Deployed $VERSION"
echo "  Previous: $PREVIOUS"
echo "  Current:  $(readlink "$CURRENT_LINK")"
echo ""
echo "Rollback: ln -sfn $PREVIOUS $CURRENT_LINK"
EOF

chmod +x deploy.sh
./deploy.sh v2.0
```

---

### Scénario 8️⃣ : Trouver fichiers avec multiples hard links

**Contexte** : Identifier fichiers ayant plusieurs hard links (potentiels dupliqués).

```bash
# 1. Trouver fichiers avec Links > 1
find /home/alice -type f -links +1

# Résultat :
# /home/alice/original.txt
# /home/alice/backup/copy.txt
# /home/alice/documents/link.txt

# 2. Afficher avec nombre de liens
find /home/alice -type f -links +1 -exec ls -lhi {} \;
# 131072 -rw-r--r-- 3 alice alice 1024 Feb  3 10:00 /home/alice/original.txt
# 131072 -rw-r--r-- 3 alice alice 1024 Feb  3 10:00 /home/alice/backup/copy.txt
# 131072 -rw-r--r-- 3 alice alice 1024 Feb  3 10:00 /home/alice/documents/link.txt

# 3. Grouper par inode
find /home/alice -type f -links +1 -printf '%i %n %p\n' | sort -n
# 131072 3 /home/alice/backup/copy.txt
# 131072 3 /home/alice/documents/link.txt
# 131072 3 /home/alice/original.txt
# 131073 2 /home/alice/file1.txt
# 131073 2 /home/alice/file1_backup.txt

# 4. Script : lister tous les hard links d'un fichier
cat > find_all_hardlinks.sh <<'EOF'
#!/bin/bash

FILE="$1"

if [ ! -f "$FILE" ]; then
    echo "Fichier introuvable"
    exit 1
fi

INODE=$(stat -c '%i' "$FILE")
LINKS=$(stat -c '%h' "$FILE")

echo "Fichier : $FILE"
echo "Inode   : $INODE"
echo "Links   : $LINKS"
echo ""
echo "Tous les hard links :"

find / -inum "$INODE" 2>/dev/null
EOF

chmod +x find_all_hardlinks.sh
./find_all_hardlinks.sh /home/alice/original.txt

# 5. Trouver doublons potentiels (économie d'espace)
# hardlink - outil dédié (à installer)
apt install hardlink

# Créer hard links pour fichiers identiques
hardlink -c /home/alice/documents

# Avant :
# file1.txt (1MB)
# file2.txt (1MB, contenu identique)
# Total: 2MB

# Après :
# file1.txt (1MB, Links: 2)
# file2.txt (1MB, Links: 2) → Même inode
# Total: 1MB (économie 50%)

# 6. Statistiques hard links
find /home/alice -type f -printf '%h\n' | sort -n | uniq -c | sort -rn | head
# Fichiers avec le plus de hard links
```

---

### Scénario 9️⃣ : Chemins relatifs vs absolus dans scripts portables

**Contexte** : Application portable avec config via symlinks.

```bash
# === Structure application portable ===

/opt/myapp/
├── bin/
│   └── myapp
├── config/
│   └── default.conf
└── data/

# Utilisateur veut config custom dans home
/home/alice/
└── myapp-config/
    └── custom.conf

# 1. Approche ABSOLUE (non portable)
ln -s /home/alice/myapp-config/custom.conf /opt/myapp/config/user.conf

ls -l /opt/myapp/config/user.conf
# lrwxrwxrwx 1 alice alice 38 Feb  3 10:00 user.conf -> /home/alice/myapp-config/custom.conf

# Problème : Si /home/alice déplacé ou utilisateur différent → cassé

# 2. Approche RELATIVE (portable si structure préservée)
# Impossible ici (chemins trop différents)

# 3. Solution : Variables d'environnement
export MYAPP_CONFIG="/home/alice/myapp-config/custom.conf"
/opt/myapp/bin/myapp
# Application lit $MYAPP_CONFIG

# === Cas portable : Application dans home ===

/home/alice/project/
├── bin/
│   └── run.sh
├── config/
│   └── app.conf
└── lib/
    └── utils.sh

# 4. Liens relatifs (depuis bin/)
cd /home/alice/project/bin
ln -s ../config/app.conf config_link.conf
ln -s ../lib/utils.sh utils_link.sh

ls -l
# lrwxrwxrwx 1 alice alice 18 Feb  3 10:00 config_link.conf -> ../config/app.conf
# lrwxrwxrwx 1 alice alice 16 Feb  3 10:00 utils_link.sh -> ../lib/utils.sh

# 5. Déplacer projet
mv /home/alice/project /home/bob/myproject

# Liens fonctionnent toujours
cat /home/bob/myproject/bin/config_link.conf
# ✅ OK (relation relative préservée)

# 6. Script utilisant lien relatif
cat > /home/bob/myproject/bin/run.sh <<'EOF'
#!/bin/bash

# Obtenir répertoire du script
SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
PROJECT_ROOT="$(dirname "$SCRIPT_DIR")"

# Utiliser chemins relatifs
CONFIG="$PROJECT_ROOT/config/app.conf"
source "$PROJECT_ROOT/lib/utils.sh"

echo "Config: $CONFIG"
EOF

# Fonctionne depuis n'importe où
/home/bob/myproject/bin/run.sh
# Config: /home/bob/myproject/config/app.conf
```

---

### Scénario 🔟 : Migration données avec préservation hard links

**Contexte** : Copier données vers nouveau serveur en préservant hard links.

```bash
# === Serveur source ===

# 1. Vérifier hard links
ls -li /data/backups/
# 131072 -rw-r--r-- 5 alice alice 1048576 Feb  1 10:00 backup-2026-02-01.tar
# 131072 -rw-r--r-- 5 alice alice 1048576 Feb  1 10:00 backup-2026-02-02.tar
# 131072 -rw-r--r-- 5 alice alice 1048576 Feb  1 10:00 backup-2026-02-03.tar
# 131073 -rw-r--r-- 3 alice alice 2097152 Feb  2 10:00 backup-2026-02-04.tar
# 131073 -rw-r--r-- 3 alice alice 2097152 Feb  2 10:00 backup-2026-02-05.tar

# Espace réel
du -sh /data/backups
# 3.0M  /data/backups  (1M + 2M, pas 5 × 1M + 2 × 2M = 9M)

# 2. Copie INCORRECTE (perd hard links)
cp -r /data/backups /tmp/copy

ls -li /tmp/copy/
# 200001 -rw-r--r-- 1 alice alice 1048576 Feb  3 11:00 backup-2026-02-01.tar
# 200002 -rw-r--r-- 1 alice alice 1048576 Feb  3 11:00 backup-2026-02-02.tar
# 200003 -rw-r--r-- 1 alice alice 1048576 Feb  3 11:00 backup-2026-02-03.tar
#   │       │
#   │       └─ Links: 1 (hard links perdus)
#   └─ Inodes différentes

du -sh /tmp/copy
# 9.0M  /tmp/copy  ← 3× plus d'espace !

# 3. Copie CORRECTE (préserve hard links)
# Option -a (archive) inclut -H ou -l selon version cp
cp -a /data/backups /tmp/copy_correct

# Ou explicite
cp -al /data/backups /tmp/copy_hardlinks  # -l = hard links

ls -li /tmp/copy_hardlinks/
# Inodes différentes MAIS hard links entre fichiers préservés

# 4. rsync (recommandé)
rsync -aH /data/backups/ /tmp/rsync_copy/
# -a : archive
# -H : preserve hard links

# Vérifier
ls -li /tmp/rsync_copy/
# 300001 -rw-r--r-- 5 alice alice 1048576 Feb  1 10:00 backup-2026-02-01.tar
# 300001 -rw-r--r-- 5 alice alice 1048576 Feb  1 10:00 backup-2026-02-02.tar
# 300001 -rw-r--r-- 5 alice alice 1048576 Feb  1 10:00 backup-2026-02-03.tar
#   │       │
#   │       └─ Links: 5 (hard links préservés)
#   └─ Nouvelle inode (filesystem différent) mais relations préservées

du -sh /tmp/rsync_copy
# 3.0M  ✅ Correct

# 5. Migration serveur distant
rsync -aH --stats /data/backups/ remote_server:/backup/

# Vérifier statistiques
# Number of regular files transferred: 5
# Number of files: 5
# Total file size: 3,145,728 bytes  ← Pas 9MB
# Total transferred file size: 3,145,728 bytes

# 6. tar (préserve hard links par défaut)
tar czf /tmp/backup.tar.gz /data/backups

# Extraire sur serveur distant
scp /tmp/backup.tar.gz remote_server:/tmp/
ssh remote_server
tar xzf /tmp/backup.tar.gz -C /

# Hard links préservés
ls -li /data/backups/
# Même structure Links: 5, Links: 3
```

---

## 🚨 10 Erreurs Courantes et Solutions

### Erreur 1️⃣ : Hard link vers répertoire

**Symptôme** :
```bash
mkdir testdir
ln testdir testdir_link
# ln: testdir: hard link not allowed for directory
```

**Cause** : Hard links vers répertoires interdits (sauf . et ..).

**Solutions** :

```bash
# ❌ INCORRECT
ln testdir testdir_link

# ✅ CORRECT : Utiliser lien symbolique
ln -s testdir testdir_link

ls -ld testdir_link
# lrwxrwxrwx 1 alice alice 7 Feb  3 10:00 testdir_link -> testdir

# Utiliser normalement
cd testdir_link
ls
# ✅ Fonctionne
```

**Explication** : Hard links vers répertoires créeraient boucles infinies dans l'arborescence.

---

### Erreur 2️⃣ : Hard link cross-filesystem

**Symptôme** :
```bash
ln /home/alice/file.txt /mnt/usb/link.txt
# ln: failed to create hard link '/mnt/usb/link.txt' => '/home/alice/file.txt': Invalid cross-device link
```

**Cause** : Hard links nécessitent même filesystem (inodes spécifiques au FS).

**Solutions** :

```bash
# 1. Vérifier filesystems
df -h /home/alice/file.txt /mnt/usb/
# Filesystem      Size  Used Avail Use% Mounted on
# /dev/sda1        50G   20G   30G  40% /home
# /dev/sdb1        10G    1G    9G  10% /mnt/usb
#   │                                      │
#   └──────────────────────────────────────┘
#           Différents !

# ❌ Hard link impossible

# ✅ Solution 1 : Lien symbolique
ln -s /home/alice/file.txt /mnt/usb/link.txt

# ✅ Solution 2 : Copie
cp /home/alice/file.txt /mnt/usb/file.txt

# ✅ Solution 3 : rsync
rsync -a /home/alice/file.txt /mnt/usb/
```

---

### Erreur 3️⃣ : Lien symbolique relatif cassé après déplacement

**Symptôme** :
```bash
# Créer lien relatif
cd /home/alice/project
ln -s ../data/file.txt link.txt

cat link.txt
# ✅ OK

# Déplacer lien ailleurs
mv link.txt /tmp/

cat /tmp/link.txt
# cat: /tmp/link.txt: No such file or directory  ❌
```

**Cause** : Lien relatif résolu depuis **emplacement du lien**.

**Solutions** :

```bash
# 1. Comprendre résolution
readlink /tmp/link.txt
# ../data/file.txt

# Résolu depuis /tmp/ :
# /tmp/../data/file.txt = /data/file.txt  ← N'existe pas

# 2. Solution : Recréer avec chemin absolu
rm /tmp/link.txt
ln -s /home/alice/data/file.txt /tmp/link.txt

cat /tmp/link.txt
# ✅ Fonctionne

# 3. Alternative : Convertir relatif → absolu
OLD_LINK="/home/alice/project/link.txt"
TARGET=$(readlink -f "$OLD_LINK")  # Résout en absolu

rm "$OLD_LINK"
ln -s "$TARGET" /tmp/link.txt

# 4. Règle : Utiliser absolu si lien et cible dans arborescences différentes
```

---

### Erreur 4️⃣ : Modifier fichier casse hard link (éditeur crée nouveau fichier)

**Symptôme** :
```bash
echo "data" > original.txt
ln original.txt backup.txt

ls -li original.txt backup.txt
# 131072 -rw-r--r-- 2 alice alice 5 Feb  3 10:00 original.txt
# 131072 -rw-r--r-- 2 alice alice 5 Feb  3 10:00 backup.txt

# Éditer avec vim/nano
vim original.txt
# (modifier et sauvegarder)

ls -li original.txt backup.txt
# 131073 -rw-r--r-- 1 alice alice 10 Feb  3 11:00 original.txt
# 131072 -rw-r--r-- 1 alice alice  5 Feb  3 10:00 backup.txt
#   │       │                                         │       │
#   │       └─ Links: 1 (hard link cassé)            │       └─ Inchangé (ancienne version)
#   └─ Nouvelle inode

cat backup.txt
# data  ← Version originale préservée

cat original.txt
# data modified  ← Nouvelle version
```

**Cause** : Éditeurs (vim, nano, etc.) créent fichier temporaire et renomment (mv) → nouvelle inode.

**Solutions** :

```bash
# 1. Si vous voulez CASSER le hard link (comportement normal)
# → Pas de problème, c'est intentionnel
# backup.txt conserve ancienne version

# 2. Si vous voulez PRÉSERVER le hard link
# → Modifier in-place (dangereux, pas d'atomic write)
echo "new data" > original.txt  # Écrase contenu, garde inode

ls -li original.txt backup.txt
# 131072 -rw-r--r-- 2 alice alice 9 Feb  3 11:05 original.txt
# 131072 -rw-r--r-- 2 alice alice 9 Feb  3 11:05 backup.txt
#                  │
#                  └─ Links: 2 (hard link préservé)

cat backup.txt
# new data  ← Modifié aussi (même inode)

# 3. Alternative : Recréer hard link après édition
vim original.txt
rm backup.txt
ln original.txt backup.txt

# 4. Pour versionning, voir Scénario 2
```

**Note** : Comportement éditeurs protège contre corruption (atomic write).

---

### Erreur 5️⃣ : Confondre readlink et readlink -f

**Symptôme** :
```bash
ln -s file1.txt link1.txt
ln -s link1.txt link2.txt

readlink link2.txt
# link1.txt  ← Seulement 1 niveau

cat link2.txt
# (contenu de file1.txt)  ← Mais ça fonctionne ?
```

**Cause** : `readlink` retourne seulement la cible **directe**, pas la résolution complète.

**Solutions** :

```bash
# 1. readlink (1 niveau)
readlink link2.txt
# link1.txt

# 2. readlink -f (résolution complète)
readlink -f link2.txt
# /home/alice/file1.txt  ← Chemin absolu résolu

# 3. Chaîne de liens
ln -s /tmp/target.txt link1.txt
ln -s link1.txt link2.txt
ln -s link2.txt link3.txt

readlink link3.txt
# link2.txt  ← 1 niveau

readlink -f link3.txt
# /tmp/target.txt  ← Résolution complète

# 4. Différence avec -e et -m
# -e : Erreur si cible inexistante
readlink -e broken_link.txt
# readlink: broken_link.txt: No such file or directory

# -f : Résout même si inexistant (pas d'erreur)
readlink -f broken_link.txt
# /home/alice/nonexistent.txt

# -m : Résout sans vérifier existence (comme -f)
readlink -m broken_link.txt
# /home/alice/nonexistent.txt

# 5. Cas d'usage
# Obtenir cible directe : readlink
# Obtenir chemin final : readlink -f
# Vérifier existence : readlink -e
```

---

### Erreur 6️⃣ : Lien symbolique vers chemin relatif depuis mauvais endroit

**Symptôme** :
```bash
# Créer lien depuis /tmp/ vers fichier dans /home/alice/
cd /tmp
ln -s ../home/alice/file.txt link.txt

cat link.txt
# cat: link.txt: No such file or directory  ❌
```

**Cause** : Chemin relatif `../home/alice/file.txt` résolu **depuis /tmp/** → `/home/alice/file.txt` (faux).

**Solutions** :

```bash
# 1. Comprendre résolution
readlink link.txt
# ../home/alice/file.txt

# Résolu depuis /tmp/ :
# /tmp/../home/alice/file.txt = /home/alice/file.txt  ✅ Correct en fait !

# Erreur dans l'exemple ci-dessus, corrigeons :

# Créer lien INCORRECT
cd /tmp
ln -s alice/file.txt link.txt  # Relatif à /tmp/

cat link.txt
# cat: link.txt: No such file or directory
# Cherche /tmp/alice/file.txt (n'existe pas)

# 2. Solution 1 : Chemin absolu
rm link.txt
ln -s /home/alice/file.txt link.txt

cat link.txt
# ✅ OK

# 3. Solution 2 : Créer lien depuis répertoire cible
cd /home/alice
ln -s file.txt /tmp/link.txt
# Chemin relatif résolu depuis /home/alice/

# Non, ça ne marche pas. Correction :

# Chemin relatif dans ln est toujours relatif au répertoire ACTUEL (pwd)
cd /home/alice
ln -s ../../home/alice/file.txt /tmp/link.txt
# Résolu depuis /tmp/ : /tmp/../../home/alice/file.txt = /home/alice/file.txt ✅

# 4. Règle simple : Pour lien cross-directory, utiliser ABSOLU
ln -s /home/alice/file.txt /tmp/link.txt
```

---

### Erreur 7️⃣ : Supprimer tous les hard links perd données

**Symptôme** :
```bash
echo "important" > file.txt
ln file.txt backup1.txt
ln file.txt backup2.txt

rm file.txt backup1.txt backup2.txt

# Données perdues ! (si Links atteint 0)
```

**Cause** : Données supprimées quand compteur Links = 0.

**Solutions** :

```bash
# 1. Vérifier nombre de liens AVANT suppression
ls -li file.txt
# 131072 -rw-r--r-- 3 alice alice 10 Feb  3 10:00 file.txt
#                  │
#                  └─ Links: 3

# 2. Lister tous les hard links
find / -inum 131072 2>/dev/null
# /home/alice/file.txt
# /home/alice/backup1.txt
# /home/alice/backup2.txt

# 3. Supprimer partiellement (données persistent)
rm file.txt backup1.txt

ls -li backup2.txt
# 131072 -rw-r--r-- 1 alice alice 10 Feb  3 10:00 backup2.txt
#                  │
#                  └─ Links: 1 (dernier lien)

cat backup2.txt
# important  ✅ Toujours là

# 4. Protection : Garder toujours au moins 1 lien
# Ou créer archive avant suppression
tar czf backup.tar.gz backup2.txt
rm backup2.txt  # Maintenant Links = 0, données supprimées

# 5. Script de sécurité
cat > safe_rm.sh <<'EOF'
#!/bin/bash

FILE="$1"
LINKS=$(stat -c '%h' "$FILE" 2>/dev/null)

if [ -z "$LINKS" ]; then
    echo "Fichier introuvable"
    exit 1
fi

if [ "$LINKS" -eq 1 ]; then
    echo "⚠️  ATTENTION : Dernier lien (Links: 1)"
    echo "Supprimer ce fichier effacera définitivement les données"
    read -p "Continuer ? (y/n) " -n 1 -r
    echo
    if [[ ! $REPLY =~ ^[Yy]$ ]]; then
        exit 0
    fi
fi

rm "$FILE"
echo "✓ Supprimé (Links restants: $((LINKS - 1)))"
EOF

chmod +x safe_rm.sh
./safe_rm.sh file.txt
```

---

### Erreur 8️⃣ : chmod sur lien symbolique (effet sur cible)

**Symptôme** :
```bash
echo "data" > target.txt
chmod 644 target.txt

ln -s target.txt link.txt

chmod 600 link.txt  # Intention : modifier lien

ls -l target.txt link.txt
# -rw------- 1 alice alice 5 Feb  3 10:00 target.txt  ← Modifié !
# lrwxrwxrwx 1 alice alice 10 Feb  3 10:00 link.txt -> target.txt
```

**Cause** : `chmod` suit les liens symboliques et modifie **la cible**.

**Solutions** :

```bash
# 1. Comportement par défaut : chmod suit symlinks
chmod 600 link.txt
# → Modifie target.txt (pas link.txt)

ls -l target.txt
# -rw------- 1 alice alice 5 Feb  3 10:00 target.txt

# 2. Permissions du lien lui-même (toujours rwxrwxrwx)
ls -l link.txt
# lrwxrwxrwx 1 alice alice 10 Feb  3 10:00 link.txt -> target.txt
#  │││││││││
#  └┴┴┴┴┴┴┴┴─ Inchangé (pas important)

# 3. lchmod (rare, pas toujours disponible)
# Change permissions du lien lui-même (pas la cible)
# Mais ignoré par la plupart des filesystems

# 4. Si vous voulez modifier SEULEMENT la cible
chmod 600 target.txt  # Direct

# 5. Si vous voulez éviter de suivre les symlinks (find)
find /path -type f -exec chmod 644 {} \;
# -type f : Seulement fichiers réguliers (exclut symlinks)

find /path -type l -exec chmod 777 {} \;
# Accepté mais aucun effet (permissions symlink ignorées)

# 6. Règle : Permissions symlink ne comptent pas
# Toujours 777, contrôle d'accès sur la cible
```

---

### Erreur 9️⃣ : ln ordre arguments inversé

**Symptôme** :
```bash
# Intention : Créer lien "link.txt" vers "target.txt"
ln link.txt target.txt
# ln: failed to create hard link 'target.txt' => 'link.txt': No such file or directory
```

**Cause** : Ordre `ln TARGET LINK_NAME` (comme `cp SOURCE DEST`).

**Solutions** :

```bash
# ❌ INCORRECT (arguments inversés)
ln link.txt target.txt
# Cherche "link.txt" (n'existe pas) comme cible

# ✅ CORRECT
ln target.txt link.txt
# target.txt = cible (doit exister)
# link.txt = nom du lien (créé)

# Mnémotechnique : Comme cp
cp source.txt destination.txt
ln source.txt link_to_source.txt
#  │          │
#  │          └─ Nouveau nom (créé)
#  └──────────── Fichier existant

# Lien symbolique pareil
ln -s target.txt link.txt
#     │          │
#     │          └─ Nom du lien
#     └──────────── Cible (peut ne pas exister pour symlink)

# Vérification avec echo
echo ln target.txt link.txt
# Affiche commande avant exécution

# Alternative : --verbose
ln -v target.txt link.txt
# 'link.txt' -> 'target.txt'
```

---

### Erreur 🔟 : Liens symboliques récursifs (boucle infinie)

**Symptôme** :
```bash
ln -s link2.txt link1.txt
ln -s link1.txt link2.txt

cat link1.txt
# cat: link1.txt: Too many levels of symbolic links
```

**Cause** : Boucle dans chaîne de résolution (link1 → link2 → link1 → ...).

**Solutions** :

```bash
# 1. Détecter boucle
readlink -f link1.txt
# readlink: link1.txt: Too many levels of symbolic links

# 2. Analyser manuellement
readlink link1.txt
# link2.txt

readlink link2.txt
# link1.txt  ← Boucle !

# 3. Supprimer et recréer correctement
rm link1.txt link2.txt

echo "data" > target.txt
ln -s target.txt link1.txt
ln -s link1.txt link2.txt

cat link2.txt
# data  ✅ Fonctionne

# 4. Auto-référence (cas extrême)
ln -s link.txt link.txt
# Certains systèmes empêchent, d'autres créent boucle

cat link.txt
# cat: link.txt: Too many levels of symbolic links

# 5. Prévention : Vérifier cible avant création
TARGET="target.txt"
LINK="link.txt"

if [ -L "$TARGET" ]; then
    RESOLVED=$(readlink -f "$TARGET" 2>/dev/null)
    if [ -z "$RESOLVED" ]; then
        echo "⚠️  Cible contient boucle ou lien mort"
        exit 1
    fi
fi

ln -s "$TARGET" "$LINK"

# 6. find pour détecter boucles
find /path -follow -type l 2>&1 | grep "Too many levels"
# Fichiers avec boucles
```

---

## 📝 Antisèche (Cheat Sheet)

### Hard Links

```bash
# Créer hard link
ln target.txt hardlink.txt

# Vérifier (même inode, Links > 1)
ls -li target.txt hardlink.txt
# 131072 -rw-r--r-- 2 alice alice 1024 Feb  3 10:00 target.txt
# 131072 -rw-r--r-- 2 alice alice 1024 Feb  3 10:00 hardlink.txt

# Trouver tous les hard links d'un fichier
INODE=$(stat -c '%i' fichier.txt)
find / -inum $INODE 2>/dev/null

# Trouver fichiers avec multiples hard links
find /path -type f -links +1

# Compter hard links
stat -c '%h' fichier.txt
# 3
```

**Limitations** :
- ❌ Répertoires (sauf . et ..)
- ❌ Cross-filesystem
- ✅ Partage inode (permissions, données)
- ✅ Suppression sûre (tant que Links > 0)

### Soft Links (Symbolic Links)

```bash
# Créer lien symbolique
ln -s target.txt symlink.txt

# Chemin absolu
ln -s /home/alice/file.txt /tmp/link.txt

# Chemin relatif
ln -s ../data/file.txt link.txt

# Vérifier
ls -l symlink.txt
# lrwxrwxrwx 1 alice alice 10 Feb  3 10:00 symlink.txt -> target.txt

# Lire cible
readlink symlink.txt
# target.txt

# Résolution complète
readlink -f symlink.txt
# /home/alice/target.txt

# Vérifier si lien mort
[ -L link.txt ] && [ ! -e link.txt ] && echo "Lien mort"

# Trouver liens morts
find /path -xtype l
```

**Avantages** :
- ✅ Répertoires
- ✅ Cross-filesystem
- ✅ Cible inexistante (lien mort)
- ✅ Permissions de la cible utilisées

### Comparaison Rapide

| Commande | Hard Link | Soft Link |
|----------|-----------|-----------|
| Créer | `ln target link` | `ln -s target link` |
| Inode | Même inode | Inode différente |
| Taille | Taille des données | Taille du chemin |
| Type | Fichier régulier | Type `l` |
| Permissions | Partagées | Ignorées (777) |
| Cross-FS | ❌ | ✅ |
| Répertoires | ❌ | ✅ |

### Commandes Utiles

```bash
# Afficher inode
ls -i fichier.txt
stat -c '%i' fichier.txt

# Afficher nombre de liens
stat -c '%h' fichier.txt

# Afficher type
stat -c '%F' fichier.txt
# regular file / symbolic link / directory

# Tester type
[ -f fichier.txt ] && echo "Fichier régulier"
[ -L lien.txt ] && echo "Lien symbolique"
[ -d repertoire/ ] && echo "Répertoire"

# Résolution chemin absolu
readlink -f lien.txt
realpath lien.txt

# pwd réel (pas via symlinks)
pwd -P
```

### Scripts Courants

```bash
# Trouver hard links
find_hardlinks() {
    local inode=$(stat -c '%i' "$1")
    find / -inum "$inode" 2>/dev/null
}

# Trouver liens morts
find_broken_symlinks() {
    find "${1:-.}" -xtype l
}

# Résoudre chaîne de symlinks
resolve_symlink() {
    readlink -f "$1"
}

# Compter hard links
count_hardlinks() {
    stat -c '%h' "$1"
}
```

---

## 🎯 Points Clés pour l'Examen LPIC-1

### ✅ Concepts à Maîtriser Absolument

1. **Inode : Structure centrale**
   - Contient métadonnées (type, permissions, owner, size, timestamps)
   - Contient pointeurs vers blocs de données
   - **Ne contient PAS le nom du fichier**
   - Numéro unique **par filesystem**

2. **Hard Link = Nom alternatif**
   - Pointe vers **même inode**
   - Partage **données et métadonnées**
   - Compteur `Links` dans inode
   - Données supprimées quand `Links = 0`

3. **Soft Link = Fichier spécial contenant chemin**
   - **Inode séparée** (type: symbolic link)
   - Contenu = **chemin vers cible** (texte)
   - Taille = **longueur du chemin**
   - Redirection par noyau

4. **Limitations Hard Links**
   - ❌ **Répertoires interdits** (éviter boucles)
   - ❌ **Cross-filesystem impossible** (inodes locales au FS)
   - ✅ Fichiers réguliers seulement

5. **Avantages Soft Links**
   - ✅ **Répertoires permis**
   - ✅ **Cross-filesystem** (stocke chemin)
   - ✅ **Cible peut ne pas exister** (lien mort)

6. **Commande ln**
   - `ln target link` : Hard link
   - `ln -s target link` : Soft link
   - Ordre : **TARGET puis LINK_NAME** (comme cp)

7. **Permissions**
   - **Hard link** : Partagées (même inode)
   - **Soft link** : Ignorées (toujours 777), permissions de **la cible** utilisées

8. **Chemins symboliques**
   - **Absolu** : `/home/alice/file.txt` (depuis racine)
   - **Relatif** : `../data/file.txt` (depuis emplacement du lien)
   - Relatif casse si lien déplacé seul

9. **readlink - Lire cible**
   - `readlink link.txt` : Cible directe (1 niveau)
   - `readlink -f link.txt` : Résolution complète (récursif)
   - `readlink -e link.txt` : Vérifier existence cible

10. **Détection**
    - Hard link : `ls -i` (même inode), `stat -c '%h'` (Links > 1)
    - Soft link : `ls -l` (type `l`, `->`)
    - Lien mort : `find -xtype l`, `[ -L link ] && [ ! -e link ]`

### 📚 Commandes à Connaître par Cœur

```bash
# Hard links
ln target.txt link.txt
ls -i fichier.txt
stat -c '%i %h' fichier.txt
find / -inum 131072
find /path -type f -links +1

# Soft links
ln -s target.txt link.txt
ln -s /absolute/path link.txt
ln -s ../relative/path link.txt
readlink link.txt
readlink -f link.txt
ls -l link.txt

# Détection
[ -L fichier ] && echo "Symlink"
[ -f fichier ] && echo "Regular file"
find /path -xtype l  # Liens morts

# Inode
stat fichier.txt
stat -c '%i' fichier.txt  # Numéro inode
stat -c '%h' fichier.txt  # Nombre de liens
stat -c '%F' fichier.txt  # Type
```

### ⚠️ Pièges d'Examen Fréquents

1. **Hard link vers répertoire → IMPOSSIBLE**
   - Exception : `.` et `..` (créés par système)

2. **Hard link cross-filesystem → IMPOSSIBLE**
   - Inodes sont spécifiques à un filesystem

3. **Ordre ln : TARGET puis LINK_NAME**
   - `ln target link` (pas l'inverse)
   - Comme `cp source dest`

4. **Soft link permissions toujours 777**
   - Permissions **ignorées**
   - Permissions de **la cible** utilisées

5. **Chemin relatif résolu depuis emplacement du LIEN**
   - Pas depuis `pwd` ou cible
   - Casse si lien déplacé seul

6. **readlink vs readlink -f**
   - `readlink` : 1 niveau
   - `readlink -f` : Résolution complète

7. **Modification fichier peut casser hard link**
   - Éditeurs créent nouveau fichier (nouvelle inode)
   - `echo >` in-place préserve hard link

8. **Suppression "original" ne supprime PAS données**
   - Tant que `Links > 0`, données persistent
   - `rm` décrémente compteur

9. **Liens morts (broken symlinks) possibles**
   - Soft link vers cible inexistante
   - Créé avant cible, ou cible supprimée

10. **chmod sur symlink modifie LA CIBLE**
    - Pas le lien lui-même
    - Permissions du lien ignorées

### 🔑 Valeurs à Mémoriser

**Nombre de liens par défaut** :
- Fichier créé : `Links: 1`
- Répertoire vide : `Links: 2` (`.` et `..`)
- Répertoire avec 1 sous-rép : `Links: 3` (`.`, `..`, `subdir/..`)

**Permissions par défaut** :
- Hard link : Hérite de l'inode (644, 755, etc.)
- Soft link : Toujours `lrwxrwxrwx` (777)

**Taille** :
- Hard link : Taille des données
- Soft link : Longueur du chemin (caractères)

**Cas d'usage examen** :
- **Hard links** : Backups incrémentaux, économie d'espace
- **Soft links** : Raccourcis, compatibilité chemins, liens vers répertoires

---

## 📖 Résumé Final

Ce cours couvre la **gestion complète des liens durs et symboliques** :

1. **Inode** : Structure contenant métadonnées (permissions, owner, size, timestamps, pointeurs blocs), mais **pas le nom**

2. **Hard Links** :
   - **Nom alternatif** pointant vers même inode
   - Partage données et métadonnées
   - ❌ Répertoires, ❌ Cross-filesystem
   - ✅ Suppression sûre (Links > 0)
   - Commande : `ln target link`

3. **Soft Links (Symbolic Links)** :
   - **Fichier spécial** contenant chemin vers cible
   - Inode séparée, taille = longueur chemin
   - ✅ Répertoires, ✅ Cross-filesystem, ✅ Cible inexistante
   - Permissions ignorées (toujours 777)
   - Commande : `ln -s target link`

4. **Chemins** :
   - **Absolu** : `/path/to/file` (stable, fonctionne partout)
   - **Relatif** : `../path/file` (résolu depuis emplacement du lien)

5. **Outils** :
   - `ln` : Créer liens
   - `ls -i` : Afficher inode
   - `stat` : Détails inode
   - `readlink` : Lire cible symlink
   - `find -inum` : Trouver hard links
   - `find -xtype l` : Trouver liens morts

6. **Cas d'usage** :
   - **Hard links** : Backups incrémentaux (rsync --link-dest), économie d'espace, versionning
   - **Soft links** : Raccourcis, compatibilité, répertoires partagés, déploiement (current → version)

La distinction hard/soft links est **fondamentale** pour comprendre filesystems Unix/Linux et optimiser l'utilisation du stockage.

---

*Documentation créée le 3 février 2026 - Formation Linux Essentials / LPIC-1 - Module 4*
