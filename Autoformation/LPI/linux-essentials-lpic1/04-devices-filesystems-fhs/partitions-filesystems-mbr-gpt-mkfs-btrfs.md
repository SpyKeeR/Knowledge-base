# 💾 Partitions et Systèmes de Fichiers - MBR/GPT, mkfs, BTRFS

## 📋 Informations du Module

- **Module** : 4 - Devices, Linux Filesystems, Filesystem Hierarchy Standard
- **Cours** : Create Partitions and Filesystems
- **Objectif** : Maîtriser la gestion des tables de partitions (MBR/GPT), la création de systèmes de fichiers (ext4, XFS, BTRFS) et les fonctionnalités avancées de BTRFS

---

## 🎯 Concepts Fondamentaux

### 📊 Tables de Partitions : MBR vs GPT

**Analogie** : Imaginez un parking géant (votre disque dur) :
- **MBR** = Plan de parking des années 1980 : limité à 4 emplacements principaux, capacité maximale 2 To
- **GPT** = Plan de parking moderne : 128 emplacements par défaut, capacité jusqu'à 9,4 ZB (zettaoctets)

**Master Boot Record (MBR)** :
```
┌─────────────────────────────────────────┐
│  MBR (512 octets)                       │
│  ├─ Bootloader (446 octets)             │
│  ├─ Table de partitions (64 octets)     │
│  │  └─ 4 entrées × 16 octets            │
│  └─ Signature (2 octets: 0x55AA)        │
└─────────────────────────────────────────┘
```

**GUID Partition Table (GPT)** :
```
┌─────────────────────────────────────────┐
│  GPT Header (LBA 1)                     │
│  ├─ Signature: "EFI PART"               │
│  ├─ Numéro de révision                  │
│  ├─ CRC32 checksum                      │
│  └─ Position de la table de partitions  │
├─────────────────────────────────────────┤
│  Partition Entries (LBA 2-33)           │
│  ├─ 128 entrées × 128 octets            │
│  └─ GUID unique par partition           │
├─────────────────────────────────────────┤
│  ... Données des partitions ...         │
├─────────────────────────────────────────┤
│  Backup Partition Entries               │
│  GPT Header Backup (dernier LBA)        │
└─────────────────────────────────────────┘
```

### 🔧 Fichiers de Périphériques Bloc

**Nomenclature des disques** :
```
/dev/sda    → Premier disque SCSI/SATA
/dev/sdb    → Deuxième disque SCSI/SATA
/dev/vda    → Premier disque virtuel (KVM/paravirtualisation)
/dev/vdb    → Deuxième disque virtuel
/dev/nvme0n1 → Premier disque NVMe
```

**Disques virtuels (vd\*)** : Le préfixe `v` indique un disque virtuel détecté par le noyau via des pilotes de paravirtualisation (virtio). L'hyperviseur (KVM, QEMU) présente le disque comme une ressource virtuelle optimisée, détectée par le noyau Linux grâce aux modules `virtio_blk` ou `virtio_scsi`.

### 📦 Systèmes de Fichiers Linux

**Analogie** : Le système de fichiers est comme un système de classement :
- **ext4** = Classeur traditionnel fiable et universel
- **XFS** = Classeur haute performance pour gros volumes
- **BTRFS** = Classeur intelligent avec photocopie automatique et archivage

---

## 🛠️ Gestion des Tables de Partitions

### Option 1️⃣ : fdisk (MBR et GPT)

**Commande de base** :
```bash
fdisk /dev/sdb
```

| Commande fdisk | Description | Exemple |
|----------------|-------------|---------|
| `m` | Afficher l'aide | Menu interactif |
| `p` | Afficher la table de partitions | Liste toutes les partitions |
| `n` | Créer une nouvelle partition | Primaire ou étendue |
| `d` | Supprimer une partition | Numéro de partition requis |
| `t` | Changer le type de partition | 83 = Linux, 82 = swap |
| `w` | Écrire les modifications et quitter | **⚠️ Permanent !** |
| `q` | Quitter sans sauvegarder | Annule les changements |
| `g` | Créer une nouvelle table GPT | Écrase MBR existante |
| `o` | Créer une nouvelle table DOS/MBR | Écrase GPT existante |

**Exemple de création de partition MBR** :
```bash
# Lancer fdisk
fdisk /dev/sdb

# Dans fdisk :
Command (m for help): n              # Nouvelle partition
Partition type: p                    # Primaire
Partition number (1-4): 1            # Première partition
First sector: [Entrée]               # Début par défaut
Last sector: +10G                    # 10 Go
Command (m for help): t              # Changer le type
Partition type: 83                   # Linux filesystem
Command (m for help): w              # Écrire et quitter
```

### Option 2️⃣ : cfdisk (Interface ncurses - Recommandé pour débutants)

**Commande de base** :
```bash
cfdisk /dev/sdb
```

**Avantages de cfdisk** :
- ✅ Interface graphique en mode texte (ncurses)
- ✅ Navigation intuitive avec flèches
- ✅ Visualisation claire de l'espace disque
- ✅ Opérations guidées avec menus
- ✅ Moins de risques d'erreurs

**Interface cfdisk** :
```
┌─────────────────────────────────────────────────────────────┐
│                    Disk: /dev/sdb                           │
│              Size: 100 GiB, 107374182400 bytes              │
├─────────────────────────────────────────────────────────────┤
│  Device      Start        End    Sectors   Size  Type       │
│  /dev/sdb1    2048   20973567   20971520    10G  Linux      │
│  Free space       20973568  209715199  188741632    90G     │
├─────────────────────────────────────────────────────────────┤
│  [  New  ] [ Delete ] [  Type  ] [  Write ] [  Quit ]      │
└─────────────────────────────────────────────────────────────┘
```

**Navigation** :
- **Flèches haut/bas** : Sélectionner partition ou espace libre
- **Flèches gauche/droite** : Naviguer entre les boutons
- **Entrée** : Exécuter l'action sélectionnée

### Option 3️⃣ : gdisk (Spécialisé GPT)

**Commande de base** :
```bash
gdisk /dev/sdb
```

| Commande gdisk | Description | Équivalent fdisk |
|----------------|-------------|------------------|
| `?` | Aide | `m` |
| `p` | Afficher la table GPT | `p` |
| `n` | Nouvelle partition | `n` |
| `d` | Supprimer partition | `d` |
| `t` | Changer le type (code GUID) | `t` |
| `w` | Écrire et quitter | `w` |
| `q` | Quitter sans sauvegarder | `q` |
| `v` | Vérifier l'intégrité GPT | - |
| `r` | Menu de récupération/transformation | - |

**Codes de type GPT courants** :
```
8300  Linux filesystem
8200  Linux swap
8301  Linux reserved
8302  Linux /home
8304  Linux x86-64 root (/)
EF00  EFI System Partition
```

### Option 4️⃣ : parted (Universel, MBR et GPT)

**Commande de base** :
```bash
parted /dev/sdb
```

| Commande parted | Description | Exemple |
|-----------------|-------------|---------|
| `print` | Afficher table de partitions | `print` ou `p` |
| `mklabel` | Créer nouvelle table | `mklabel gpt` ou `mklabel msdos` |
| `mkpart` | Créer partition | `mkpart primary ext4 1MiB 10GiB` |
| `rm` | Supprimer partition | `rm 1` |
| `resizepart` | Redimensionner partition | `resizepart 1 20GiB` |
| `set` | Définir flag | `set 1 boot on` |
| `align-check` | Vérifier alignement | `align-check optimal 1` |
| `quit` | Quitter | `q` |

**Modes parted** :
```bash
# Mode interactif
parted /dev/sdb
(parted) print
(parted) mkpart primary ext4 1MiB 10GiB
(parted) quit

# Mode ligne de commande (scripts)
parted /dev/sdb mklabel gpt
parted /dev/sdb mkpart primary ext4 1MiB 10GiB
parted /dev/sdb print
```

### 🔄 Conversion MBR ↔ GPT

**Tableau DOS (MBR)** = `msdos` dans les outils Linux

**Avec gdisk (recommandé)** :
```bash
gdisk /dev/sdb
Command: w    # Convertit automatiquement MBR → GPT si nécessaire
```

**Avec parted** :
```bash
parted /dev/sdb mklabel gpt      # MBR → GPT (⚠️ détruit les données)
parted /dev/sdb mklabel msdos    # GPT → MBR (⚠️ détruit les données)
```

### ⚡ Recharger la Table de Partitions

Après modification de partitions, le noyau doit relire la table :

```bash
# Option 1 : partprobe (recommandé)
partprobe /dev/sdb

# Option 2 : Recharger manuellement
blockdev --rereadpt /dev/sdb

# Option 3 : Vérifier que le noyau a bien vu les changements
cat /proc/partitions
lsblk
```

---

## 🏗️ Création de Systèmes de Fichiers (mkfs)

### 🔹 mkfs - Commande Générique

**Architecture mkfs** :
```
mkfs → Front-end générique
  ├─ mkfs.ext4   (alias de mke2fs -t ext4)
  ├─ mkfs.ext3   (alias de mke2fs -t ext3)
  ├─ mkfs.ext2   (alias de mke2fs -t ext2)
  ├─ mkfs.xfs    (programme xfsprogs)
  ├─ mkfs.btrfs  (programme btrfs-progs)
  ├─ mkfs.vfat   (FAT32)
  └─ mkfs.ntfs   (NTFS via ntfs-3g)
```

**Syntaxe générique** :
```bash
mkfs -t <type> [options] <device>

# Exemples équivalents
mkfs -t ext4 /dev/sdb1
mkfs.ext4 /dev/sdb1
```

### 🔸 mkfs.ext4 (Système de Fichiers ext4)

**Options principales** :

| Option | Description | Exemple |
|--------|-------------|---------|
| `-L <label>` | Définir un label (nom) | `-L "MyData"` |
| `-N <nombre>` | Nombre d'inodes | `-N 1000000` |
| `-i <bytes>` | Octets par inode | `-i 16384` |
| `-m <pourcentage>` | % réservé à root | `-m 1` (par défaut 5%) |
| `-b <taille>` | Taille des blocs | `-b 4096` |
| `-E <options>` | Options étendues | `-E stride=2,stripe-width=4` |
| `-J <options>` | Options journal | `-J size=128` |
| `-O <features>` | Activer/désactiver fonctionnalités | `-O ^has_journal` |
| `-F` | Forcer (même si monté) | `-F` ⚠️ Dangereux |

**Exemples pratiques** :

```bash
# Formatage basique avec label
mkfs.ext4 -L "BackupDisk" /dev/sdb1

# Augmenter le nombre d'inodes pour nombreux petits fichiers
mkfs.ext4 -N 2000000 /dev/sdb1

# Configurer la taille de la table d'inodes
mkfs.ext4 -i 8192 /dev/sdb1
# Avec -i 8192 : 1 inode tous les 8 Ko → 1 To = ~134 millions d'inodes

# Réduire l'espace réservé à root (utile pour gros disques de données)
mkfs.ext4 -m 1 -L "DataDisk" /dev/sdb1

# Formatage optimisé SSD (désactiver journalisation)
mkfs.ext4 -O ^has_journal -E discard -L "SSD" /dev/sdb1
```

**Problème des inodes limités** :
```bash
# Symptôme : "No space left on device" alors que df montre de l'espace
df -h /mnt/disk
# /dev/sdb1    100G   50G   50G  50% /mnt/disk

df -i /mnt/disk
# /dev/sdb1    6553600  6553600  0  100% /mnt/disk  ← Inodes épuisés !

# Solution : Reformater avec plus d'inodes
umount /mnt/disk
mkfs.ext4 -N 10000000 -L "FixedDisk" /dev/sdb1
```

### 🔹 mkfs.xfs (Système de Fichiers XFS)

**Caractéristiques XFS** :
- ✅ Haute performance pour gros fichiers
- ✅ Journalisation avancée
- ✅ Allocation dynamique des inodes (pas de limite fixe)
- ✅ Support de très gros volumes (8 exbioctets)
- ❌ Ne peut pas être réduit (seulement agrandi)

**Options principales** :

| Option | Description | Exemple |
|--------|-------------|---------|
| `-L <label>` | Définir un label | `-L "XFS_Data"` |
| `-b size=<taille>` | Taille des blocs | `-b size=4096` |
| `-i size=<taille>` | Taille des inodes | `-i size=512` |
| `-d agcount=<N>` | Nombre de groupes d'allocation | `-d agcount=8` |
| `-l size=<taille>` | Taille du log | `-l size=64m` |
| `-f` | Force le formatage | `-f` |

**Exemples pratiques** :

```bash
# Formatage basique XFS
mkfs.xfs -L "XFS_Backup" /dev/sdb1

# Configuration de la taille des inodes pour métadonnées
mkfs.xfs -i size=512 -L "XFS_Storage" /dev/sdb1

# XFS optimisé pour RAID
mkfs.xfs -d agcount=16 -L "RAID_XFS" /dev/md0

# Force formatage même si système de fichiers existant
mkfs.xfs -f /dev/sdb1
```

### 🔧 Outils de Gestion ext4 et XFS

#### tune2fs - Modification de systèmes ext2/ext3/ext4

**Options tune2fs** :

| Option | Description | Exemple |
|--------|-------------|---------|
| `-l` | Lister les informations du FS | `tune2fs -l /dev/sdb1` |
| `-L <label>` | Changer le label | `tune2fs -L "NewName" /dev/sdb1` |
| `-m <pourcentage>` | Modifier % réservé root | `tune2fs -m 2 /dev/sdb1` |
| `-c <nombre>` | Nombre de montages avant fsck | `tune2fs -c 30 /dev/sdb1` |
| `-i <jours>` | Intervalle entre fsck | `tune2fs -i 90 /dev/sdb1` |
| `-e <comportement>` | Action si erreur | `tune2fs -e remount-ro /dev/sdb1` |
| `-O <feature>` | Activer/désactiver fonctionnalité | `tune2fs -O ^has_journal /dev/sdb1` |
| `-U <UUID>` | Changer l'UUID | `tune2fs -U random /dev/sdb1` |

**Exemples pratiques** :

```bash
# Lister toutes les informations du système de fichiers
tune2fs -l /dev/sdb1

# Changer le label
tune2fs -L "ProductionData" /dev/sdb1

# Réduire l'espace réservé de 5% à 1%
tune2fs -m 1 /dev/sdb1

# Désactiver les vérifications automatiques
tune2fs -c 0 -i 0 /dev/sdb1

# Remonter en lecture seule en cas d'erreur
tune2fs -e remount-ro /dev/sdb1

# Activer la fonctionnalité dir_index (améliore performances répertoires)
tune2fs -O dir_index /dev/sdb1
```

#### xfs_admin - Modification de systèmes XFS

**Options xfs_admin** :

| Option | Description | Exemple |
|--------|-------------|---------|
| `-l` | Afficher le label | `xfs_admin -l /dev/sdb1` |
| `-L <label>` | Changer le label | `xfs_admin -L "NewXFS" /dev/sdb1` |
| `-u` | Afficher l'UUID | `xfs_admin -u /dev/sdb1` |
| `-U <uuid>` | Changer l'UUID | `xfs_admin -U generate /dev/sdb1` |

**Exemples pratiques** :

```bash
# Afficher label et UUID
xfs_admin -l -u /dev/sdb1

# Changer le label (système doit être démonté)
umount /mnt/xfs
xfs_admin -L "BackupXFS" /dev/sdb1

# Générer un nouvel UUID
xfs_admin -U generate /dev/sdb1
```

#### Autres outils XFS utiles

```bash
# xfs_info : Informations détaillées du FS XFS monté
xfs_info /mnt/xfs

# xfs_repair : Réparer un FS XFS corrompu (démonté)
umount /mnt/xfs
xfs_repair /dev/sdb1

# xfs_db : Débogage XFS (mode lecture seule par défaut)
xfs_db -r /dev/sdb1

# xfs_growfs : Agrandir un FS XFS (monté)
xfs_growfs /mnt/xfs
```

---

## 🌳 BTRFS - Better Filesystem (B-Tree FS / ButterFS)

### 📖 Introduction à BTRFS

**Noms alternatifs** :
- **BetterFS** (Better Filesystem)
- **B-Tree FS** (Structure de données B-tree)
- **ButterFS** (Prononciation anglaise)

**Caractéristiques principales** :

| Fonctionnalité | Description |
|----------------|-------------|
| **Copy-on-Write (CoW)** | Écriture sans écrasement des données |
| **Capacité maximale** | 16 EiB (exbioctets) |
| **Multi-device** | Gestion de plusieurs disques |
| **RAID logiciel** | RAID 0, 1, 5, 6, 10 intégré |
| **Compression transparente** | zlib, lzo, zstd |
| **Optimisation SSD** | Trim automatique, wear leveling |
| **Snapshots** | Instantanés incrémentaux |
| **Sous-volumes** | Partitionnement logique interne |
| **Quotas** | Limitation d'espace par sous-volume |
| **Déduplication** | Élimination des données dupliquées |
| **Checksums** | Détection automatique de corruption |
| **Self-healing** | Réparation automatique (avec RAID) |

### 🔄 Copy-on-Write (CoW) - Concept Fondamental

**Principe CoW** :

```
┌─────────────────────────────────────────────────────────┐
│  Écriture traditionnelle (ext4, XFS)                    │
├─────────────────────────────────────────────────────────┤
│  1. Lire ancien fichier                                 │
│  2. Écraser données sur place          ← RISQUE si crash│
│  3. Mettre à jour métadonnées                           │
│  └─> Corruption possible si interruption                │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│  Écriture Copy-on-Write (BTRFS)                         │
├─────────────────────────────────────────────────────────┤
│  1. Écrire nouvelle version dans espace libre           │
│  2. Vérifier intégrité (checksum)                       │
│  3. Mettre à jour pointeur vers nouvelle version        │
│  4. Supprimer ancienne version                          │
│  └─> Résilience maximale, pas de corruption            │
└─────────────────────────────────────────────────────────┘
```

**Avantages CoW** :
- ✅ **Résilience** : Crash système pendant écriture = anciennes données intactes
- ✅ **Snapshots gratuits** : Anciennes versions déjà préservées
- ✅ **Intégrité** : Checksums sur données et métadonnées
- ⚠️ **Fragmentation** : Performances peuvent diminuer avec le temps (nécessite défragmentation)

### 🔨 Création de Systèmes de Fichiers BTRFS

#### Formatage basique

```bash
# Formatage simple d'une partition
mkfs.btrfs /dev/sdb1

# Avec label
mkfs.btrfs -L "BackupBTRFS" /dev/sdb1

# Forcer le formatage
mkfs.btrfs -f /dev/sdb1
```

#### Options de mkfs.btrfs

| Option | Description | Exemple |
|--------|-------------|---------|
| `-L <label>` | Définir un label | `-L "MyBTRFS"` |
| `-d <profile>` | Profil de données | `-d raid1` |
| `-m <profile>` | Profil de métadonnées | `-m raid1` |
| `-f` | Forcer le formatage | `-f` |
| `-n <taille>` | Taille des nœuds (node size) | `-n 16384` |
| `-l <taille>` | Taille des feuilles (leaf size) | `-l 16384` |
| `-s <taille>` | Taille des secteurs | `-s 4096` |
| `-O <features>` | Activer fonctionnalités | `-O skinny-metadata` |
| `-K` | Désactiver CoW | `-K` (non recommandé) |

### 💿 BTRFS Multi-Device (RAID Logiciel)

**Profils de données et métadonnées** :

| Profil | Description | Utilisation | Redondance |
|--------|-------------|-------------|------------|
| `single` | Pas de duplication | 1 disque | Aucune |
| `dup` | Duplication sur même disque | 1 disque | Métadonnées uniquement |
| `raid0` | Agrégation par bandes | 2+ disques | Aucune (performance) |
| `raid1` | Miroir | 2+ disques | 1 copie |
| `raid1c3` | Triple miroir | 3+ disques | 2 copies |
| `raid1c4` | Quadruple miroir | 4+ disques | 3 copies |
| `raid5` | Parité simple | 3+ disques | Tolère 1 panne |
| `raid6` | Parité double | 4+ disques | Tolère 2 pannes |
| `raid10` | Miroir + agrégation | 4+ disques | 1 copie |

**⚠️ Important** : Pour BTRFS multi-device, on utilise des **disques entiers**, pas des partitions !

**Exemples multi-device** :

```bash
# RAID1 - Métadonnées et données en miroir (2 disques)
mkfs.btrfs -d raid1 -m raid1 /dev/sdb /dev/sdc

# RAID0 - Agrégation pour performance (2 disques)
mkfs.btrfs -d raid0 -m raid0 /dev/sdb /dev/sdc

# RAID10 - Haute disponibilité et performance (4 disques)
mkfs.btrfs -d raid10 -m raid10 /dev/sdb /dev/sdc /dev/sdd /dev/sde

# RAID1 métadonnées, single données (économie d'espace)
mkfs.btrfs -d single -m raid1 /dev/sdb /dev/sdc

# RAID5 pour économie d'espace avec redondance (3+ disques)
mkfs.btrfs -d raid5 -m raid1 /dev/sdb /dev/sdc /dev/sdd

# RAID6 pour redondance maximale (4+ disques)
mkfs.btrfs -d raid6 -m raid1 /dev/sdb /dev/sdc /dev/sdd /dev/sde
```

**Montage multi-device** :
```bash
# Monter un BTRFS multi-device (suffit de spécifier un seul disque)
mount /dev/sdb /mnt/btrfs
# ou
mount -t btrfs /dev/sdb /mnt/btrfs

# BTRFS détecte automatiquement les autres disques du volume
```

**Ajouter/Retirer des disques à chaud** :

```bash
# Ajouter un disque à un volume BTRFS existant
btrfs device add /dev/sdd /mnt/btrfs

# Retirer un disque (redistribue les données)
btrfs device delete /dev/sdd /mnt/btrfs

# Voir les disques du volume
btrfs filesystem show /mnt/btrfs

# Rééquilibrer les données après ajout/retrait
btrfs balance start /mnt/btrfs
```

### 📁 Sous-Volumes BTRFS

**Concept** : Les sous-volumes sont des "pseudo-répertoires" indépendants au sein d'un volume BTRFS. Chaque sous-volume :
- Peut être monté séparément
- Peut avoir ses propres snapshots
- Peut avoir des quotas indépendants
- Partage l'espace total du volume parent

**Créer un sous-volume** :

```bash
# Monter le volume principal
mount /dev/sdb1 /mnt/disk

# Créer un sous-volume
btrfs subvolume create /mnt/disk/backups
btrfs subvolume create /mnt/disk/home
btrfs subvolume create /mnt/disk/logs

# Le sous-volume apparaît comme un répertoire normal
ls -l /mnt/disk/
# drwxr-xr-x 1 root root 0 Feb  3 10:00 backups
# drwxr-xr-x 1 root root 0 Feb  3 10:00 home
# drwxr-xr-x 1 root root 0 Feb  3 10:00 logs
```

**Afficher les informations d'un sous-volume** :

```bash
# Informations détaillées
btrfs subvolume show /mnt/disk/backups

# Sortie :
# /mnt/disk/backups
#         Name:                   backups
#         UUID:                   a1b2c3d4-...
#         Parent UUID:            -
#         Received UUID:          -
#         Creation time:          2026-02-03 10:00:15
#         Subvolume ID:           257
#         Generation:             10
#         Gen at creation:        8
#         Parent ID:              5
#         Top level ID:           5
#         Flags:                  -
```

**Lister tous les sous-volumes** :

```bash
# Liste complète
btrfs subvolume list /mnt/disk

# Sortie :
# ID 257 gen 10 top level 5 path backups
# ID 258 gen 11 top level 5 path home
# ID 259 gen 12 top level 5 path logs
```

**Monter un sous-volume spécifique** :

```bash
# Option 1 : Via l'option subvol
mount -t btrfs -o subvol=backups /dev/sdb1 /mnt/backups

# Option 2 : Via l'ID du sous-volume
mount -t btrfs -o subvolid=257 /dev/sdb1 /mnt/backups

# Vérifier
df -h /mnt/backups
# /dev/sdb1    100G   10G   90G  10% /mnt/backups
```

**Exemple dans /etc/fstab** :

```bash
# Sous-volume backups monté automatiquement
/dev/sdb1  /mnt/backups  btrfs  subvol=backups,compress=zstd  0  0

# Sous-volume home
/dev/sdb1  /home         btrfs  subvol=home,compress=zstd     0  0
```

**Supprimer un sous-volume** :

```bash
# Supprimer (ne fonctionne PAS avec rm -rf !)
btrfs subvolume delete /mnt/disk/old_backups

# Pour sous-volume monté séparément
umount /mnt/old_backups
btrfs subvolume delete /mnt/disk/old_backups
```

### 📸 Snapshots BTRFS

**Concept** : Un snapshot est une "photographie" instantanée d'un sous-volume à un moment donné. Grâce au CoW, les snapshots sont quasi-instantanés et n'occupent initialement aucun espace supplémentaire (seules les modifications futures consomment de l'espace).

**Créer un snapshot** :

```bash
# Snapshot en lecture/écriture (par défaut)
btrfs subvolume snapshot /mnt/disk /mnt/disk/snapshots/disk-2026-02-03

# Snapshot en lecture seule (-r)
btrfs subvolume snapshot -r /mnt/disk /mnt/disk/snapshots/disk-readonly-2026-02-03

# Snapshot d'un sous-volume spécifique
btrfs subvolume snapshot /mnt/disk/home /mnt/disk/snapshots/home-2026-02-03
```

**⚠️ Important** : Un snapshot BTRFS est techniquement un sous-volume. Il contient les fichiers modifiés/ajoutés **depuis** le moment de la création du snapshot.

**Lister les snapshots** :

```bash
# Les snapshots apparaissent dans la liste des sous-volumes
btrfs subvolume list /mnt/disk

# ID 257 gen 20 top level 5 path home
# ID 260 gen 25 top level 5 path snapshots/disk-2026-02-03
# ID 261 gen 26 top level 5 path snapshots/home-2026-02-03
```

**Restaurer à partir d'un snapshot** :

```bash
# Option 1 : Renommer pour revenir en arrière
mv /mnt/disk/home /mnt/disk/home.broken
btrfs subvolume snapshot /mnt/disk/snapshots/home-2026-02-03 /mnt/disk/home

# Option 2 : Copier les fichiers depuis le snapshot
cp -a /mnt/disk/snapshots/home-2026-02-03/user1/.bashrc /mnt/disk/home/user1/

# Option 3 : Monter le snapshot en lecture seule pour récupération
mount -o subvol=snapshots/disk-2026-02-03,ro /dev/sdb1 /mnt/recovery
cp /mnt/recovery/important_file.txt /mnt/disk/
```

**Supprimer un snapshot** :

```bash
# Supprimer comme un sous-volume
btrfs subvolume delete /mnt/disk/snapshots/old-snapshot-2025-12-01
```

**Automatisation des snapshots** :

```bash
# Script de snapshot automatique quotidien
#!/bin/bash
DATE=$(date +%Y-%m-%d-%H%M)
btrfs subvolume snapshot -r /mnt/disk/home /mnt/disk/snapshots/home-$DATE

# Supprimer les snapshots de plus de 30 jours
find /mnt/disk/snapshots/ -maxdepth 1 -type d -mtime +30 -exec btrfs subvolume delete {} \;
```

**Exemple avec snapper (outil de gestion de snapshots)** :

```bash
# Installation
apt install snapper  # Debian/Ubuntu
dnf install snapper  # Fedora/RHEL

# Configuration d'un sous-volume pour snapper
snapper -c home create-config /home

# Créer un snapshot manuel
snapper -c home create --description "Before system upgrade"

# Lister les snapshots
snapper -c home list

# Restaurer un fichier depuis un snapshot
snapper -c home undochange 1..2  # Annule changements entre snapshot 1 et 2
```

### 🗜️ Compression Transparente BTRFS

**Algorithmes disponibles** :

| Algorithme | Performance | Taux de compression | Utilisation recommandée |
|------------|-------------|---------------------|-------------------------|
| **zlib** | Lent | Élevé (meilleur) | Archives, fichiers peu accédés |
| **lzo** | Rapide | Moyen | Usage général, équilibre perf/compression |
| **zstd** | Rapide | Élevé | **Recommandé** - Meilleur compromis |

**Niveaux de compression zstd** : `zstd:1` (rapide, faible) à `zstd:15` (lent, maximal)

**Activer la compression** :

```bash
# Au montage (temporaire)
mount -o compress=zstd /dev/sdb1 /mnt/btrfs

# Au montage avec niveau zstd
mount -o compress=zstd:3 /dev/sdb1 /mnt/btrfs

# Dans /etc/fstab (permanent)
/dev/sdb1  /mnt/btrfs  btrfs  compress=zstd:3,space_cache=v2  0  0

# Autres algorithmes
mount -o compress=lzo /dev/sdb1 /mnt/btrfs
mount -o compress=zlib /dev/sdb1 /mnt/btrfs
```

**Forcer la compression des fichiers existants** :

```bash
# Défragmenter et compresser tout le système de fichiers
btrfs filesystem defragment -r -czstd /mnt/btrfs

# Compresser un répertoire spécifique
btrfs filesystem defragment -r -czstd /mnt/btrfs/documents

# Compresser un seul fichier
btrfs filesystem defragment -czlib /mnt/btrfs/largefile.tar
```

**Désactiver la compression pour certains fichiers** :

```bash
# Attribut chattr (requiert kernel >= 4.10)
chattr +C /mnt/btrfs/databases/
# Les nouveaux fichiers dans ce répertoire ne seront pas compressés
```

**Vérifier l'efficacité de la compression** :

```bash
# Avec compsize (outil dédié BTRFS)
compsize /mnt/btrfs

# Sortie :
# Processed 50000 files, 100000 regular extents (150000 refs), 25000 inline.
# Type       Perc     Disk Usage   Uncompressed Referenced
# TOTAL       48%      24G          50G          50G
# none       100%      10G          10G          10G
# zstd        40%      14G          40G          40G
```

### 🔢 Quotas de Sous-Volumes

**Activer les quotas** :

```bash
# Activer sur le volume
btrfs quota enable /mnt/btrfs

# Vérifier le statut
btrfs qgroup show /mnt/btrfs
```

**Définir une limite** :

```bash
# Limiter un sous-volume à 50 Go
btrfs qgroup limit 50G /mnt/btrfs/home

# Limiter avec ID de qgroup (visible avec btrfs qgroup show)
btrfs qgroup limit 50G 0/257 /mnt/btrfs
```

**Désactiver les quotas** :

```bash
btrfs quota disable /mnt/btrfs
```

### 🩺 Maintenance et Outils BTRFS

**Vérifier l'espace utilisé** :

```bash
# Vue globale
btrfs filesystem usage /mnt/btrfs

# Résumé simple
btrfs filesystem df /mnt/btrfs

# Afficher informations du volume
btrfs filesystem show /mnt/btrfs
```

**Scrub (vérification d'intégrité)** :

```bash
# Lancer un scrub (vérification checksums + réparation auto si RAID)
btrfs scrub start /mnt/btrfs

# Vérifier le statut
btrfs scrub status /mnt/btrfs

# Annuler un scrub
btrfs scrub cancel /mnt/btrfs
```

**Balance (rééquilibrage)** :

```bash
# Rééquilibrer tout le système de fichiers
btrfs balance start /mnt/btrfs

# Rééquilibrer seulement les données
btrfs balance start -d /mnt/btrfs

# Rééquilibrer seulement les métadonnées
btrfs balance start -m /mnt/btrfs

# Balance avec filtre (blocs utilisés à moins de 50%)
btrfs balance start -dusage=50 /mnt/btrfs

# Vérifier statut
btrfs balance status /mnt/btrfs

# Annuler
btrfs balance cancel /mnt/btrfs
```

**Défragmentation** :

```bash
# Défragmenter tout (⚠️ Peut annuler les bénéfices des snapshots !)
btrfs filesystem defragment -r /mnt/btrfs

# Défragmenter avec compression
btrfs filesystem defragment -r -czstd /mnt/btrfs

# Défragmenter un fichier spécifique
btrfs filesystem defragment /mnt/btrfs/largefile.db
```

**Réparation (en cas de corruption)** :

```bash
# Vérifier (lecture seule)
btrfs check /dev/sdb1

# Réparation (⚠️ démonter d'abord !)
umount /mnt/btrfs
btrfs check --repair /dev/sdb1

# Réparation en dernier recours (dangereux)
btrfs rescue zero-log /dev/sdb1
btrfs check --repair /dev/sdb1
```

---

## 🎓 10 Scénarios Pratiques Complets

### Scénario 1️⃣ : Créer un disque de données ext4 optimisé pour serveur web

**Contexte** : Serveur web avec millions de petits fichiers (images, CSS, JS).

```bash
# 1. Créer partition avec cfdisk (plus ergonomique que fdisk)
cfdisk /dev/sdb
# → [New] → Taille: 500G → [Type] → Linux filesystem → [Write] → [Quit]

# 2. Recharger table de partitions
partprobe /dev/sdb

# 3. Formater avec ext4 optimisé pour nombreux fichiers
mkfs.ext4 -L "WebData" -N 10000000 -m 1 /dev/sdb1
# -N 10000000 : 10 millions d'inodes
# -m 1        : Seulement 1% réservé à root (au lieu de 5%)

# 4. Optimiser avec tune2fs
tune2fs -o journal_data_writeback /dev/sdb1
tune2fs -O dir_index /dev/sdb1

# 5. Créer point de montage et monter
mkdir -p /var/www/storage
mount -o noatime,nodiratime /dev/sdb1 /var/www/storage

# 6. Ajouter à /etc/fstab
echo "/dev/sdb1  /var/www/storage  ext4  defaults,noatime,nodiratime  0  2" >> /etc/fstab

# 7. Vérifier
df -h /var/www/storage
tune2fs -l /dev/sdb1 | grep -i inode
```

---

### Scénario 2️⃣ : Convertir un disque MBR en GPT sans perte de données

**Contexte** : Disque de 3 To en MBR (limité à 2 To), besoin de GPT.

```bash
# 1. Sauvegarder la table de partitions MBR actuelle
sfdisk -d /dev/sdb > /tmp/sdb-mbr-backup.txt

# 2. Installer gdisk si nécessaire
apt install gdisk

# 3. Convertir avec gdisk (non destructif)
gdisk /dev/sdb
# Dans gdisk:
Command: w      # Convertit automatiquement MBR → GPT et écrit
# Do you want to proceed? (Y/N): y

# 4. Vérifier la conversion
gdisk -l /dev/sdb | grep "GPT"
# Should show: "GPT: present"

# 5. Recharger la table
partprobe /dev/sdb

# 6. Vérifier que les partitions sont intactes
lsblk /dev/sdb
mount /dev/sdb1 /mnt/test
ls /mnt/test  # Données toujours présentes
```

---

### Scénario 3️⃣ : Créer un système BTRFS RAID1 pour sauvegardes critiques

**Contexte** : Deux disques de 2 To pour sauvegardes avec redondance.

```bash
# 1. Créer BTRFS RAID1 (données et métadonnées en miroir)
mkfs.btrfs -L "BackupRAID1" -d raid1 -m raid1 /dev/sdb /dev/sdc

# 2. Monter le volume
mkdir -p /mnt/backups
mount /dev/sdb /mnt/backups

# 3. Créer sous-volumes pour organisation
btrfs subvolume create /mnt/backups/daily
btrfs subvolume create /mnt/backups/weekly
btrfs subvolume create /mnt/backups/monthly

# 4. Activer compression zstd
mount -o remount,compress=zstd:3 /mnt/backups

# 5. Ajouter à /etc/fstab avec compression
echo "/dev/sdb  /mnt/backups  btrfs  defaults,compress=zstd:3,space_cache=v2  0  0" >> /etc/fstab

# 6. Créer script de snapshot automatique
cat > /usr/local/bin/backup-snapshot.sh <<'EOF'
#!/bin/bash
DATE=$(date +%Y-%m-%d)
btrfs subvolume snapshot -r /mnt/backups/daily /mnt/backups/snapshots/daily-$DATE
# Supprimer snapshots de plus de 7 jours
find /mnt/backups/snapshots/ -maxdepth 1 -name "daily-*" -mtime +7 -exec btrfs subvolume delete {} \;
EOF
chmod +x /usr/local/bin/backup-snapshot.sh

# 7. Ajouter à crontab (snapshot quotidien à 23h)
(crontab -l 2>/dev/null; echo "0 23 * * * /usr/local/bin/backup-snapshot.sh") | crontab -

# 8. Activer scrub mensuel pour vérification intégrité
(crontab -l 2>/dev/null; echo "0 3 1 * * btrfs scrub start /mnt/backups") | crontab -
```

---

### Scénario 4️⃣ : Récupérer un système avec inodes ext4 épuisés

**Contexte** : Serveur de logs avec "No space left" mais df montre de l'espace.

```bash
# 1. Diagnostiquer le problème
df -h /var/log
# /dev/sda3    100G   60G   40G  60% /var/log

df -i /var/log
# /dev/sda3    6553600  6553600  0  100% /var/log  ← Problème !

# 2. Trouver les répertoires avec le plus de fichiers
find /var/log -xdev -type f | cut -d "/" -f 1-4 | sort | uniq -c | sort -n
# 2500000 /var/log/app/debug
# 1000000 /var/log/nginx/access
#  500000 /var/log/syslog

# 3. Nettoyer temporairement
find /var/log/app/debug -type f -mtime +7 -delete
find /var/log/nginx/access -type f -mtime +30 -delete

# 4. Sauvegarder les données importantes
tar czf /tmp/var-log-backup.tar.gz /var/log

# 5. Recréer le système de fichiers avec plus d'inodes
umount /var/log
mkfs.ext4 -L "VarLog" -N 20000000 /dev/sda3
# 20 millions d'inodes au lieu de 6,5 millions

# 6. Restaurer les données
mount /dev/sda3 /var/log
tar xzf /tmp/var-log-backup.tar.gz -C /

# 7. Configurer rotation de logs pour éviter le problème
cat > /etc/logrotate.d/aggressive <<EOF
/var/log/app/debug/*.log {
    daily
    rotate 7
    compress
    delaycompress
    missingok
    notifempty
}
EOF

# 8. Vérifier
df -i /var/log
# /dev/sda3    20000000  3000000  17000000  15% /var/log  ← OK !
```

---

### Scénario 5️⃣ : Optimiser un disque XFS pour base de données MySQL

**Contexte** : Serveur MySQL nécessitant hautes performances I/O.

```bash
# 1. Créer partition avec parted (alignement optimal)
parted /dev/sdb mklabel gpt
parted -a optimal /dev/sdb mkpart primary xfs 1MiB 100%

# 2. Vérifier l'alignement
parted /dev/sdb align-check optimal 1
# 1 aligned

# 3. Formater en XFS avec optimisations
mkfs.xfs -L "MySQLData" -i size=512 -d agcount=8 /dev/sdb1
# -i size=512  : Inodes 512 octets (métadonnées optimisées)
# -d agcount=8 : 8 groupes d'allocation (parallélisme I/O)

# 4. Monter avec options optimisées
mkdir -p /var/lib/mysql-data
mount -o noatime,nodiratime,logbufs=8,logbsize=256k /dev/sdb1 /var/lib/mysql-data

# 5. Ajouter à /etc/fstab
cat >> /etc/fstab <<EOF
/dev/sdb1  /var/lib/mysql-data  xfs  noatime,nodiratime,logbufs=8,logbsize=256k  0  2
EOF

# 6. Configurer propriétaire MySQL
chown -R mysql:mysql /var/lib/mysql-data

# 7. Modifier configuration MySQL
cat >> /etc/mysql/mysql.conf.d/custom.cnf <<EOF
[mysqld]
datadir = /var/lib/mysql-data
innodb_flush_method = O_DIRECT
innodb_log_file_size = 512M
EOF

# 8. Redémarrer MySQL
systemctl restart mysql

# 9. Surveillance performances
xfs_info /var/lib/mysql-data
iostat -x 5 /dev/sdb1
```

---

### Scénario 6️⃣ : Créer un système BTRFS multi-device évolutif

**Contexte** : Stockage de médias pouvant s'agrandir avec nouveaux disques.

```bash
# 1. Créer BTRFS initial avec 2 disques en RAID0
mkfs.btrfs -L "MediaStorage" -d raid0 -m raid1 /dev/sdb /dev/sdc
# -d raid0 : Données en agrégation (performance)
# -m raid1 : Métadonnées en miroir (sécurité)

# 2. Monter
mkdir -p /mnt/media
mount /dev/sdb /mnt/media

# 3. Créer structure de sous-volumes
btrfs subvolume create /mnt/media/photos
btrfs subvolume create /mnt/media/videos
btrfs subvolume create /mnt/media/music

# 4. Activer compression adaptée
mount -o remount,compress=zstd:3 /mnt/media

# 5. Définir quotas par sous-volume
btrfs quota enable /mnt/media
btrfs qgroup limit 500G /mnt/media/photos
btrfs qgroup limit 2T /mnt/media/videos
btrfs qgroup limit 200G /mnt/media/music

# 6. Ajouter un 3ème disque à chaud (3 mois plus tard)
btrfs device add /dev/sdd /mnt/media

# 7. Rééquilibrer pour utiliser le nouveau disque
btrfs balance start -dusage=80 /mnt/media

# 8. Vérifier la distribution
btrfs filesystem show /mnt/media
# Label: 'MediaStorage'  uuid: xxx
#         Total devices 3 FS bytes used 1.5TiB
#         devid    1 size 2.00TiB used 800.00GiB path /dev/sdb
#         devid    2 size 2.00TiB used 800.00GiB path /dev/sdc
#         devid    3 size 2.00TiB used 100.00GiB path /dev/sdd

# 9. Créer snapshot hebdomadaire automatique
cat > /etc/cron.weekly/btrfs-snapshot <<'EOF'
#!/bin/bash
DATE=$(date +%Y-%m-%d)
for subvol in photos videos music; do
    btrfs subvolume snapshot -r /mnt/media/$subvol /mnt/media/.snapshots/${subvol}-${DATE}
done
EOF
chmod +x /etc/cron.weekly/btrfs-snapshot
```

---

### Scénario 7️⃣ : Migration ext4 → BTRFS sans perte de données

**Contexte** : Convertir un disque ext4 existant vers BTRFS.

```bash
# 1. Sauvegarder les données (OBLIGATOIRE)
rsync -avP /mnt/old-disk/ /mnt/backup/

# 2. Démonter le disque ext4
umount /mnt/old-disk

# 3. Vérifier l'intégrité ext4
e2fsck -f /dev/sdb1

# 4. Utiliser btrfs-convert (conversion in-place)
btrfs-convert /dev/sdb1
# This will take several minutes...

# 5. Monter en BTRFS
mount /dev/sdb1 /mnt/old-disk

# 6. Vérifier que les données sont intactes
ls -la /mnt/old-disk
df -h /mnt/old-disk

# 7. Un sous-volume "ext2_saved" contient l'ancien ext4 (rollback possible)
btrfs subvolume list /mnt/old-disk
# ID 256 gen 7 top level 5 path ext2_saved

# 8. Si tout fonctionne, supprimer le backup ext4
btrfs subvolume delete /mnt/old-disk/ext2_saved

# 9. Défragmenter et activer compression
btrfs filesystem defragment -r -czstd /mnt/old-disk

# 10. Mettre à jour /etc/fstab
sed -i 's/ext4/btrfs/' /etc/fstab
sed -i 's/errors=remount-ro/compress=zstd:3,space_cache=v2/' /etc/fstab
```

---

### Scénario 8️⃣ : Restauration après crash système grâce aux snapshots BTRFS

**Contexte** : Mise à jour système a cassé le boot, snapshots BTRFS disponibles.

```bash
# 1. Booter sur clé USB live Linux
# 2. Identifier les partitions BTRFS
lsblk -f
# /dev/sda2  btrfs  RootFS  xxx-xxx

# 3. Monter le volume BTRFS
mkdir /mnt/rescue
mount /dev/sda2 /mnt/rescue

# 4. Lister les snapshots disponibles
btrfs subvolume list /mnt/rescue
# ID 256 gen 100 top level 5 path @
# ID 257 gen 95 top level 5 path @home
# ID 258 gen 90 top level 5 path snapshots/root-2026-02-01
# ID 259 gen 99 top level 5 path snapshots/root-2026-02-03

# 5. Monter le snapshot d'avant la mise à jour
mkdir /mnt/snapshot
mount -o subvol=snapshots/root-2026-02-01 /dev/sda2 /mnt/snapshot

# 6. Vérifier que le snapshot est bon
ls /mnt/snapshot/boot
ls /mnt/snapshot/etc

# 7. Renommer le sous-volume racine actuel (cassé)
mv /mnt/rescue/@ /mnt/rescue/@.broken

# 8. Créer un nouveau sous-volume racine à partir du snapshot
btrfs subvolume snapshot /mnt/snapshot /mnt/rescue/@

# 9. Démonter et redémarrer
umount /mnt/snapshot
umount /mnt/rescue
reboot

# 10. Après redémarrage réussi, supprimer le sous-volume cassé
mount /dev/sda2 /mnt/rescue
btrfs subvolume delete /mnt/rescue/@.broken
```

---

### Scénario 9️⃣ : Optimiser compression BTRFS pour économiser l'espace

**Contexte** : Serveur de fichiers avec 10 To de données, besoin d'économiser de l'espace.

```bash
# 1. Monter avec compression zstd niveau élevé
mount -o remount,compress=zstd:9 /mnt/storage

# 2. Forcer la compression des fichiers existants
btrfs filesystem defragment -r -czstd /mnt/storage
# ⚠️ Processus long, peut prendre des heures

# 3. Suivre la progression (autre terminal)
watch -n 60 'btrfs filesystem usage /mnt/storage'

# 4. Vérifier l'efficacité avec compsize
apt install btrfs-compsize
compsize /mnt/storage

# Exemple de résultat:
# Processed 500000 files, 2000000 extents
# Type       Perc     Disk Usage   Uncompressed Referenced
# TOTAL       35%      3.5T         10T          10T
# none        80%      1.0T         1.2T         1.2T
# zstd        30%      2.5T         8.8T         8.8T
# → Économie de 65% grâce à la compression !

# 5. Exclure les fichiers incompressibles (médias déjà compressés)
find /mnt/storage -type f \( -name "*.mp4" -o -name "*.jpg" -o -name "*.mp3" \) -exec chattr +C {} \;

# 6. Mettre à jour /etc/fstab
sed -i 's/compress=zstd:3/compress=zstd:9/' /etc/fstab

# 7. Créer balance automatique hebdomadaire
cat > /etc/cron.weekly/btrfs-balance <<'EOF'
#!/bin/bash
btrfs balance start -dusage=70 -musage=70 /mnt/storage
EOF
chmod +x /etc/cron.weekly/btrfs-balance
```

---

### Scénario 🔟 : Détecter et réparer corruption silencieuse avec BTRFS scrub

**Contexte** : Prévention corruption de données sur serveur de production.

```bash
# 1. Lancer un scrub manuel pour tester
btrfs scrub start /mnt/production

# 2. Suivre la progression
btrfs scrub status /mnt/production
# scrub status for xxx
# scrub started at Mon Feb  3 10:00:00 2026 and finished after 01:23:45
# total bytes scrubbed: 5.00TiB with 0 errors

# 3. Si erreurs détectées (exemple)
btrfs scrub status /mnt/production
# scrub started at Mon Feb  3 10:00:00 2026, running for 00:15:32
# total bytes scrubbed: 1.20TiB with 15 errors
# error details: csum=15
# corrected errors: 15, uncorrectable errors: 0

# Analyse : 15 erreurs de checksum, toutes corrigées automatiquement (grâce au RAID1)

# 4. Vérifier les fichiers concernés (si erreurs non corrigées)
dmesg | grep -i btrfs
# [12345.678] BTRFS warning: checksum error at logical 123456789 on dev /dev/sdb, ...

# 5. Configurer scrub automatique mensuel
cat > /etc/cron.monthly/btrfs-scrub <<'EOF'
#!/bin/bash
MOUNT_POINTS="/mnt/production /mnt/backups"
for mount in $MOUNT_POINTS; do
    btrfs scrub start -B $mount
    if [ $? -ne 0 ]; then
        echo "ERREUR: Scrub a détecté des problèmes sur $mount" | mail -s "Alerte BTRFS" admin@example.com
    fi
done
EOF
chmod +x /etc/cron.monthly/btrfs-scrub

# 6. Monitoring avec systemd timer (alternative plus moderne)
cat > /etc/systemd/system/btrfs-scrub@.service <<'EOF'
[Unit]
Description=BTRFS scrub on %f

[Service]
Type=oneshot
ExecStart=/usr/bin/btrfs scrub start -B %f
EOF

cat > /etc/systemd/system/btrfs-scrub@.timer <<'EOF'
[Unit]
Description=Monthly BTRFS scrub on %f

[Timer]
OnCalendar=monthly
Persistent=true

[Install]
WantedBy=timers.target
EOF

# 7. Activer les timers
systemctl enable --now btrfs-scrub@mnt-production.timer
systemctl enable --now btrfs-scrub@mnt-backups.timer

# 8. Vérifier les timers
systemctl list-timers | grep btrfs
```

---

## 🚨 10 Erreurs Courantes et Solutions

### Erreur 1️⃣ : "Device or resource busy" lors de création partition

**Symptôme** :
```bash
fdisk /dev/sdb
# Device or resource busy
```

**Causes possibles** :
- Partition(s) montée(s)
- Swap actif sur le disque
- LVM/RAID utilisant le disque
- Processus avec fichiers ouverts

**Solutions** :

```bash
# 1. Vérifier les partitions montées
mount | grep /dev/sdb
umount /dev/sdb1
umount /dev/sdb2

# 2. Désactiver swap si présent
swapoff /dev/sdb2

# 3. Vérifier LVM
pvdisplay | grep /dev/sdb
vgreduce myvg /dev/sdb   # Si nécessaire

# 4. Vérifier RAID
cat /proc/mdstat
mdadm --stop /dev/md0    # Si nécessaire

# 5. Trouver processus utilisant le disque
lsof | grep /dev/sdb
fuser -m /dev/sdb
kill <PID>

# 6. En dernier recours : reboot
reboot
```

---

### Erreur 2️⃣ : Partition non détectée après création avec fdisk

**Symptôme** :
```bash
fdisk /dev/sdb
# Created partition /dev/sdb1

ls /dev/sdb1
# ls: cannot access '/dev/sdb1': No such file or directory
```

**Cause** : Le noyau n'a pas rechargé la table de partitions.

**Solutions** :

```bash
# Option 1 : partprobe (recommandé)
partprobe /dev/sdb

# Option 2 : blockdev
blockdev --rereadpt /dev/sdb

# Option 3 : Vérifier avec le noyau
cat /proc/partitions | grep sdb

# Option 4 : kpartx (pour device mapper)
kpartx -a /dev/sdb

# Option 5 : Si rien ne fonctionne, reboot
reboot
```

---

### Erreur 3️⃣ : "No space left on device" avec df montrant de l'espace libre

**Symptôme** :
```bash
touch /mnt/disk/test.txt
# touch: cannot touch '/mnt/disk/test.txt': No space left on device

df -h /mnt/disk
# /dev/sdb1    100G   60G   40G  60% /mnt/disk  ← 40 Go libres !
```

**Cause** : Inodes épuisés (ext4) ou métadonnées pleines (BTRFS).

**Solutions pour ext4** :

```bash
# 1. Vérifier les inodes
df -i /mnt/disk
# /dev/sdb1    6553600  6553600  0  100% /mnt/disk  ← Problème !

# 2. Trouver les répertoires avec le plus de fichiers
find /mnt/disk -xdev -type f | cut -d "/" -f 1-4 | sort | uniq -c | sort -n | tail -20

# 3. Nettoyer ou reformater avec plus d'inodes
umount /mnt/disk
mkfs.ext4 -N 20000000 /dev/sdb1
```

**Solutions pour BTRFS** :

```bash
# 1. Vérifier l'utilisation réelle
btrfs filesystem usage /mnt/disk

# 2. Lancer un balance
btrfs balance start -dusage=70 /mnt/disk

# 3. Supprimer les snapshots anciens
btrfs subvolume delete /mnt/disk/snapshots/old-*
```

---

### Erreur 4️⃣ : "Invalid argument" avec mkfs.btrfs multi-device

**Symptôme** :
```bash
mkfs.btrfs -d raid1 /dev/sdb1 /dev/sdc1
# ERROR: unable to open /dev/sdb1: Invalid argument
```

**Cause** : BTRFS multi-device nécessite des **disques entiers**, pas des partitions.

**Solution** :

```bash
# ❌ INCORRECT (partitions)
mkfs.btrfs -d raid1 /dev/sdb1 /dev/sdc1

# ✅ CORRECT (disques entiers)
mkfs.btrfs -d raid1 /dev/sdb /dev/sdc

# Alternative : Utiliser une seule partition par disque
mkfs.btrfs -d raid1 /dev/sdb1 /dev/sdc1 -f
# ⚠️ Fonctionne mais déconseillé, préférer disques entiers
```

---

### Erreur 5️⃣ : Impossible de supprimer un sous-volume BTRFS avec rm

**Symptôme** :
```bash
rm -rf /mnt/disk/old_subvolume
# rm: cannot remove '/mnt/disk/old_subvolume': Operation not permitted
```

**Cause** : Les sous-volumes BTRFS ne peuvent pas être supprimés avec `rm`.

**Solution** :

```bash
# ❌ INCORRECT
rm -rf /mnt/disk/old_subvolume
sudo rm -rf /mnt/disk/old_subvolume   # Ne fonctionne pas non plus

# ✅ CORRECT
btrfs subvolume delete /mnt/disk/old_subvolume

# Pour supprimer récursivement (sous-volumes imbriqués)
btrfs subvolume delete -r /mnt/disk/parent_subvolume
```

---

### Erreur 6️⃣ : BTRFS balance bloque le système

**Symptôme** :
```bash
btrfs balance start /mnt/disk
# Système devient très lent, inutilisable
```

**Cause** : Balance consomme 100% des I/O disque.

**Solutions** :

```bash
# 1. Annuler le balance en cours
btrfs balance cancel /mnt/disk

# 2. Relancer avec limitation (seulement blocs <80% pleins)
btrfs balance start -dusage=80 /mnt/disk

# 3. Utiliser nice pour baisser la priorité
nice -n 19 btrfs balance start -dusage=80 /mnt/disk

# 4. Limiter via ionice (classe 3 = idle)
ionice -c 3 btrfs balance start -dusage=80 /mnt/disk

# 5. Combiner nice + ionice + limitation usage
nice -n 19 ionice -c 3 btrfs balance start -dusage=70 -musage=70 /mnt/disk
```

---

### Erreur 7️⃣ : XFS ne peut pas être réduit

**Symptôme** :
```bash
xfs_growfs /dev/sdb1 -D 500G
# ERROR: XFS cannot be shrunk
```

**Cause** : XFS ne supporte que l'agrandissement, jamais la réduction.

**Solutions** :

```bash
# Option 1 : Sauvegarder et recréer plus petit
rsync -avP /mnt/xfs/ /mnt/backup/
umount /mnt/xfs
parted /dev/sdb resizepart 1 500GiB
mkfs.xfs /dev/sdb1
mount /dev/sdb1 /mnt/xfs
rsync -avP /mnt/backup/ /mnt/xfs/

# Option 2 : Migrer vers BTRFS (supporte resize)
# (voir Scénario 7)

# Option 3 : Utiliser LVM pour flexibilité future
pvcreate /dev/sdb
vgcreate vg_data /dev/sdb
lvcreate -L 500G -n lv_xfs vg_data
mkfs.xfs /dev/vg_data/lv_xfs
# Plus tard : lvresize --size +100G /dev/vg_data/lv_xfs ; xfs_growfs /mnt/xfs
```

---

### Erreur 8️⃣ : "Structure needs cleaning" avec XFS après crash

**Symptôme** :
```bash
mount /dev/sdb1 /mnt/xfs
# mount: /mnt/xfs: Structure needs cleaning
```

**Cause** : Journal XFS corrompu après crash système.

**Solutions** :

```bash
# 1. Tenter réparation simple
xfs_repair /dev/sdb1

# 2. Si échec : Reset du log (⚠️ potentielle perte de données récentes)
xfs_repair -L /dev/sdb1
# -L : Zero the log (efface les transactions en cours)

# 3. Si toujours échec : Reset du log de force
umount /dev/sdb1
xfs_repair -L /dev/sdb1
mount /dev/sdb1 /mnt/xfs

# 4. Vérifier l'intégrité après montage
xfs_info /mnt/xfs
find /mnt/xfs -type f -size 0   # Fichiers vides suspects
```

---

### Erreur 9️⃣ : Compression BTRFS ne réduit pas l'espace utilisé

**Symptôme** :
```bash
mount -o remount,compress=zstd /mnt/btrfs
# df -h montre toujours la même utilisation
```

**Cause** : La compression ne s'applique qu'aux **nouvelles écritures**.

**Solutions** :

```bash
# 1. Forcer la compression des fichiers existants
btrfs filesystem defragment -r -czstd /mnt/btrfs

# 2. Vérifier l'efficacité avec compsize
compsize /mnt/btrfs

# 3. Si pas d'amélioration : fichiers déjà compressés
find /mnt/btrfs -type f -exec file {} \; | grep -i "compressed"

# 4. Exclure fichiers incompressibles
find /mnt/btrfs -type f \( -name "*.jpg" -o -name "*.mp4" -o -name "*.zip" \) -exec chattr +C {} \;
```

---

### Erreur 🔟 : GPT protective MBR avec gdisk sur petit disque

**Symptôme** :
```bash
gdisk /dev/sdb
# Warning: The kernel is still using the old partition table.
# The new table will be used at the next reboot or after you
# run partprobe(8) or kpartx(8)
```

**Cause** : Conversion MBR → GPT nécessite synchronisation avec le noyau.

**Solutions** :

```bash
# 1. Forcer le rechargement
partprobe /dev/sdb

# 2. Vérifier avec gdisk
gdisk -l /dev/sdb | head -20
# Partition table scan:
#   MBR: protective
#   BSD: not present
#   APM: not present
#   GPT: present

# 3. Si "MBR: not present", créer protective MBR
gdisk /dev/sdb
Command: x      # Expert menu
Expert command: n   # Create new protective MBR
Expert command: w   # Write and exit

# 4. Vérifier que les outils lisent GPT
parted /dev/sdb print
# Partition Table: gpt

lsblk -f /dev/sdb
# NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
# sdb                                                      
# └─sdb1 ext4   Data  xxx-xxx-xxx                         /mnt/data
```

---

## 📝 Antisèche (Cheat Sheet)

### Outils de Partitionnement

| Outil | Type | Interface | Usage recommandé |
|-------|------|-----------|------------------|
| `fdisk` | MBR/GPT | Ligne de commande | Scripts, experts |
| `cfdisk` | MBR/GPT | ncurses (visuel) | **Débutants, usage interactif** |
| `gdisk` | GPT | Ligne de commande | GPT uniquement, conversion MBR→GPT |
| `parted` | MBR/GPT | Ligne/interactive | Scripts, automatisation |

### Commandes fdisk Essentielles

```bash
fdisk /dev/sdb
  m       # Aide
  p       # Afficher partitions
  n       # Nouvelle partition
  d       # Supprimer partition
  t       # Changer type
  w       # Écrire et quitter
  q       # Quitter sans sauvegarder
  g       # Créer table GPT
  o       # Créer table DOS/MBR
```

### Rechargement Table de Partitions

```bash
partprobe /dev/sdb                 # Méthode recommandée
blockdev --rereadpt /dev/sdb       # Alternative
cat /proc/partitions               # Vérifier
lsblk                              # Vue d'ensemble
```

### Création de Systèmes de Fichiers

| Commande | Système | Usage typique |
|----------|---------|---------------|
| `mkfs.ext4 -L "Label" /dev/sdb1` | ext4 | Usage général Linux |
| `mkfs.xfs -L "Label" /dev/sdb1` | XFS | Gros fichiers, performance |
| `mkfs.btrfs -L "Label" /dev/sdb1` | BTRFS | Snapshots, compression, RAID |
| `mkfs.vfat -F 32 -n "Label" /dev/sdb1` | FAT32 | Compatibilité Windows |

### Options mkfs.ext4

```bash
mkfs.ext4 -L "Label"           # Label
          -N 10000000          # Nombre d'inodes
          -i 16384             # Octets par inode
          -m 1                 # % réservé root (défaut 5%)
          -b 4096              # Taille blocs
          -O ^has_journal      # Désactiver journal (SSD)
          -E discard           # TRIM pour SSD
          /dev/sdb1
```

### Modification ext4 (tune2fs)

```bash
tune2fs -l /dev/sdb1                    # Lister infos
tune2fs -L "NewLabel" /dev/sdb1         # Changer label
tune2fs -m 2 /dev/sdb1                  # 2% réservé root
tune2fs -c 0 -i 0 /dev/sdb1             # Désactiver fsck auto
tune2fs -O dir_index /dev/sdb1          # Activer indexation répertoires
```

### Options mkfs.xfs

```bash
mkfs.xfs -L "Label"                # Label
         -i size=512               # Taille inodes
         -d agcount=8              # Groupes allocation
         -l size=64m               # Taille log
         -f                        # Forcer
         /dev/sdb1
```

### Gestion XFS

```bash
xfs_admin -l /dev/sdb1                  # Afficher label
xfs_admin -L "NewLabel" /dev/sdb1       # Changer label
xfs_admin -U generate /dev/sdb1         # Nouvel UUID
xfs_info /mnt/xfs                       # Infos FS monté
xfs_repair /dev/sdb1                    # Réparer (démonté)
xfs_growfs /mnt/xfs                     # Agrandir (monté)
```

### BTRFS - Création et Multi-Device

```bash
# Formatage simple
mkfs.btrfs -L "Label" /dev/sdb1

# RAID1 (2 disques)
mkfs.btrfs -d raid1 -m raid1 /dev/sdb /dev/sdc

# RAID0 (agrégation)
mkfs.btrfs -d raid0 -m raid0 /dev/sdb /dev/sdc

# RAID10 (4 disques)
mkfs.btrfs -d raid10 -m raid10 /dev/sd{b,c,d,e}
```

### BTRFS - Sous-Volumes

```bash
btrfs subvolume create /mnt/btrfs/subvol      # Créer
btrfs subvolume list /mnt/btrfs                # Lister
btrfs subvolume show /mnt/btrfs/subvol         # Info
btrfs subvolume delete /mnt/btrfs/subvol       # Supprimer

# Monter sous-volume
mount -o subvol=subvol /dev/sdb1 /mnt/subvol
```

### BTRFS - Snapshots

```bash
# Snapshot lecture/écriture
btrfs subvolume snapshot /mnt/btrfs /mnt/btrfs/snap

# Snapshot lecture seule
btrfs subvolume snapshot -r /mnt/btrfs /mnt/btrfs/snap-ro

# Supprimer snapshot
btrfs subvolume delete /mnt/btrfs/snap
```

### BTRFS - Compression

```bash
# Montage avec compression
mount -o compress=zstd:3 /dev/sdb1 /mnt/btrfs

# Algorithmes
compress=zlib      # Lent, meilleur taux
compress=lzo       # Rapide, taux moyen
compress=zstd:3    # Recommandé (niveau 1-15)

# Forcer compression fichiers existants
btrfs filesystem defragment -r -czstd /mnt/btrfs
```

### BTRFS - Maintenance

```bash
btrfs filesystem show /mnt/btrfs            # Infos volume
btrfs filesystem usage /mnt/btrfs           # Usage détaillé
btrfs filesystem df /mnt/btrfs              # Résumé espace

btrfs scrub start /mnt/btrfs                # Vérification intégrité
btrfs scrub status /mnt/btrfs               # Statut scrub

btrfs balance start /mnt/btrfs              # Rééquilibrage complet
btrfs balance start -dusage=70 /mnt/btrfs   # Balance partiel
btrfs balance status /mnt/btrfs             # Statut balance

btrfs device add /dev/sdd /mnt/btrfs        # Ajouter disque
btrfs device delete /dev/sdd /mnt/btrfs     # Retirer disque
```

### BTRFS - Quotas

```bash
btrfs quota enable /mnt/btrfs               # Activer quotas
btrfs qgroup show /mnt/btrfs                # Afficher quotas
btrfs qgroup limit 100G /mnt/btrfs/subvol   # Limiter sous-volume
btrfs quota disable /mnt/btrfs              # Désactiver quotas
```

### Diagnostic Systèmes de Fichiers

```bash
# Afficher type FS
lsblk -f
blkid /dev/sdb1
df -T

# Vérifier intégrité
e2fsck -f /dev/sdb1          # ext2/3/4 (démonté)
xfs_repair /dev/sdb1         # XFS (démonté)
btrfs check /dev/sdb1        # BTRFS (démonté)

# Infos détaillées
tune2fs -l /dev/sdb1         # ext4
xfs_info /mnt/xfs            # XFS (monté)
btrfs filesystem show        # BTRFS
```

---

## 🎯 Points Clés pour l'Examen LPIC-1

### ✅ Concepts à Maîtriser Absolument

1. **MBR vs GPT**
   - MBR : 4 partitions primaires max, 2 To max, 512 octets, compatible BIOS
   - GPT : 128 partitions, 9,4 ZB, GUID, UEFI, backup de la table
   - Conversion : `gdisk` permet MBR → GPT non destructive

2. **Fichiers de périphériques**
   - `/dev/sd*` : Disques SCSI/SATA
   - `/dev/vd*` : Disques virtuels (KVM/paravirtualisation)
   - `/dev/nvme*` : Disques NVMe
   - Nomenclature : `/dev/sda1` = disque a, partition 1

3. **Outils de partitionnement**
   - `fdisk` : MBR et GPT, ligne de commande
   - `cfdisk` : Interface ncurses, plus ergonomique
   - `gdisk` : Spécialisé GPT
   - `parted` : Universel, scriptable
   - `partprobe` : Recharger table de partitions

4. **ext4 : Système de fichiers par défaut Linux**
   - Journalisation : Protection contre corruptions
   - Inodes fixes : Définis au formatage avec `-N` ou `-i`
   - Espace réservé root : 5% par défaut, modifier avec `-m`
   - `tune2fs` : Modification post-création

5. **XFS : Haute performance**
   - Allocation dynamique d'inodes : Pas de limite fixe
   - Agrandissement uniquement : Pas de réduction possible
   - `xfs_growfs` : Agrandir un FS monté
   - `xfs_admin` : Modifier label/UUID
   - `xfs_repair` : Réparation (démonté)

6. **BTRFS : Fonctionnalités avancées**
   - Copy-on-Write : Résilience aux crashs
   - Snapshots instantanés : Quasi-gratuits en espace
   - Compression transparente : zlib, lzo, zstd
   - Multi-device : RAID 0/1/5/6/10 intégré
   - Sous-volumes : Partitionnement logique flexible
   - Self-healing : Auto-réparation avec RAID

7. **Compression BTRFS**
   - `zlib` : Meilleur taux, plus lent
   - `lzo` : Rapide, taux moyen
   - `zstd` : **Recommandé** - Rapide avec bon taux
   - Activer : `mount -o compress=zstd:3`
   - Forcer existants : `btrfs filesystem defragment -r -czstd`

8. **Inodes et limites**
   - ext4 : Nombre fixe à la création
   - Symptôme : "No space left" avec espace libre
   - Diagnostic : `df -i`
   - Solution : Reformater avec `-N` plus élevé

9. **Commandes essentielles**
   - `mkfs.ext4`, `mkfs.xfs`, `mkfs.btrfs`
   - `tune2fs -l` : Infos ext4
   - `xfs_info` : Infos XFS
   - `btrfs filesystem show` : Infos BTRFS
   - `lsblk -f`, `blkid` : Identifier FS

10. **Options de montage**
    - `noatime` : Désactiver mise à jour access time (performance)
    - `compress=zstd` : Compression BTRFS
    - `subvol=name` : Monter sous-volume BTRFS
    - `/etc/fstab` : Montages automatiques au boot

### 📚 Commandes à Connaître par Cœur

```bash
# Partitionnement
fdisk /dev/sdb
cfdisk /dev/sdb
gdisk /dev/sdb
parted /dev/sdb
partprobe /dev/sdb

# Formatage
mkfs.ext4 -L "Label" -N 10000000 /dev/sdb1
mkfs.xfs -L "Label" /dev/sdb1
mkfs.btrfs -L "Label" /dev/sdb1

# Modification
tune2fs -l /dev/sdb1
tune2fs -L "NewLabel" /dev/sdb1
xfs_admin -L "NewLabel" /dev/sdb1

# BTRFS
btrfs subvolume create /mnt/btrfs/sub
btrfs subvolume snapshot -r /mnt/btrfs /mnt/btrfs/snap
btrfs filesystem show
btrfs scrub start /mnt/btrfs
mount -o compress=zstd:3,subvol=home /dev/sdb1 /home

# Diagnostic
lsblk -f
blkid
df -h
df -i
cat /proc/partitions
```

### ⚠️ Pièges d'Examen Fréquents

1. **BTRFS multi-device = disques entiers, pas partitions**
   - ❌ `mkfs.btrfs /dev/sdb1 /dev/sdc1`
   - ✅ `mkfs.btrfs /dev/sdb /dev/sdc`

2. **XFS ne peut pas être réduit, seulement agrandi**
   - `xfs_growfs` existe, pas de `xfs_shrink`

3. **Sous-volumes BTRFS : supprimer avec `btrfs subvolume delete`, pas `rm -rf`**

4. **Compression BTRFS ne s'applique pas aux fichiers existants automatiquement**
   - Nécessite `btrfs filesystem defragment -r -c<algo>`

5. **Table DOS = MBR** (terminologie Linux)

6. **Partprobe nécessaire après fdisk pour recharger la table**

7. **Inodes ext4 : nombre fixe à la création**
   - Configurer avec `-N` (nombre) ou `-i` (octets/inode)

8. **BTRFS RAID5/6 : encore expérimental (mentionner avec précaution)**

9. **Snapshots BTRFS = sous-volumes spéciaux**
   - Créés avec `btrfs subvolume snapshot`

10. **`mkfs.ext4` = alias de `mke2fs -t ext4`**
    - Idem pour mkfs.ext3, mkfs.ext2

### 🔑 Acronymes et Termes

- **MBR** : Master Boot Record
- **GPT** : GUID Partition Table
- **GUID** : Globally Unique Identifier
- **BTRFS** : B-Tree Filesystem / Better FS / Butter FS
- **CoW** : Copy-on-Write
- **LBA** : Logical Block Addressing
- **DOS** : Disk Operating System (table MBR sous Linux)
- **Inode** : Index Node (métadonnées de fichier)
- **AG** : Allocation Group (XFS)
- **Scrub** : Vérification d'intégrité BTRFS
- **Balance** : Rééquilibrage des données BTRFS

---

## 📖 Résumé Final

Ce cours couvre trois composantes essentielles de la gestion du stockage sous Linux :

1. **Tables de partitions (MBR/GPT)** : Fondamentaux pour organiser les disques, avec outils `fdisk`, `cfdisk`, `gdisk`, `parted`. Comprendre les limites de MBR (4 partitions, 2 To) vs flexibilité de GPT (128 partitions, 9,4 ZB).

2. **Systèmes de fichiers classiques (ext4/XFS)** : `mkfs.ext4` pour usage général avec gestion d'inodes fixes, `mkfs.xfs` pour haute performance et gros volumes. Outils de modification : `tune2fs` (ext4) et `xfs_admin` (XFS).

3. **BTRFS moderne et avancé** : Copy-on-Write pour résilience, snapshots instantanés, compression transparente (zstd recommandé), RAID logiciel intégré, sous-volumes flexibles. Maintenance avec `scrub` (intégrité) et `balance` (rééquilibrage).

**Retenir** : Le choix du système de fichiers dépend des besoins :
- **ext4** : Fiable, universel, bon compromis
- **XFS** : Performance maximale, gros fichiers/volumes
- **BTRFS** : Fonctionnalités avancées, snapshots, compression, self-healing

La maîtrise de ces outils et concepts est essentielle pour l'administration système Linux et la certification LPIC-1.

---

*Documentation créée le 3 février 2026 - Formation Linux Essentials / LPIC-1 - Module 4*
