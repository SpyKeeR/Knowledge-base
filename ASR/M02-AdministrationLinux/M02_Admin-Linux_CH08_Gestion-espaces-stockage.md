# M02 - Administration Linux

## CH08 - Gestion des espaces de stockage

Chapitre dense et fondamental : partitionnement, LVM, systèmes de fichiers, montage manuel et automatique. C'est le cœur de l'administration système au quotidien.

---

### 8.1 - Partitionner un disque

Un disque dur est représenté sous Linux par un **périphérique de type bloc** dans `/dev/`.

#### 📐 Norme MBR (Master Boot Record)

Créée en 1983, elle définit l'utilisation des 512 premiers octets du disque :

| Zone | Taille | Rôle |
| --- | --- | --- |
| Boot loader (GRUB stage 1) | 446 octets | Amorçage du système |
| Table de partition | 64 octets | Position des 4 partitions primaires |
| Signature | 2 octets | `0x55AA` |

Contraintes MBR :

- Maximum **4 partitions primaires**
- Pour contourner : utiliser une **partition étendue** (contient jusqu'à 56 partitions logiques)
- Taille max d'une partition : **2,2 To**

#### 📐 Norme GPT (GUID Partition Table)

Créée en 2013, elle remplace le MBR :

- Jusqu'à **128 partitions** (voire 256)
- Taille max théorique : **9,4 Zo** (Zettaoctets)
- Associée au boot **UEFI**
- Intégrité assurée par **CRC32** (backup de la table)

| Aspect | MBR | GPT |
| --- | --- | --- |
| Taille max | 2 To | >2 To (9 ZB) |
| Partitions | 4 primaires | 128 |
| Boot | BIOS | UEFI |
| Backup table | Non | Oui (CRC32) |
| Outil principal | `fdisk` | `gdisk`, `parted` |

#### 🏷️ Nommage des disques et partitions sous Linux

**Disques SCSI/SATA** dans `/dev/` :

| Device | Signification |
| --- | --- |
| `/dev/sda` | Premier disque détecté |
| `/dev/sdb` | Deuxième disque |
| `/dev/sdc` | Troisième disque, etc. |

**Disques NVMe** :

```text
/dev/nvme0n1p1
         │  │ └─ Partition 1
         │  └─── Disque 1
         └────── Périphérique NVMe 0
```

**Partitions** : Linux réserve les numéros 1-4 pour les partitions primaires/étendue. Les partitions logiques commencent toujours à **5**, quel que soit le nombre de partitions primaires.

Exemple avec 2 primaires + 1 étendue contenant 3 logiques :

```text
/dev/sdb    → disque entier
/dev/sdb1   → 1ère partition primaire
/dev/sdb2   → 2ème partition primaire
/dev/sdb3   → partition étendue
/dev/sdb5   → 1ère partition logique
/dev/sdb6   → 2ème partition logique
/dev/sdb7   → 3ème partition logique
```

📌 Il n'y a pas de `/dev/sdb4` dans cet exemple, et les logiques sautent directement à 5.

#### 🔧 Outils de partitionnement

Principaux outils console : `fdisk`, `parted`, `sfdisk`, `cfdisk`, `gdisk`.

##### `fdisk` (MBR)

```bash
# Voir la table de partition
# fdisk -l /dev/sda

# Partitionner un disque (interactif)
# fdisk /dev/sdb
```

Commandes interactives essentielles de `fdisk` :

| Touche | Action |
| --- | --- |
| `m` | Aide |
| `p` | Afficher la table de partition |
| `n` | Nouvelle partition |
| `d` | Supprimer une partition |
| `t` | Changer le type d'une partition |
| `o` | Créer une nouvelle table MBR |
| `g` | Créer une nouvelle table GPT |
| `w` | Écrire les modifications et quitter |
| `q` | Quitter sans sauvegarder |

Types de partitions courants :

| ID | Type |
| --- | --- |
| `83` | Linux |
| `82` | Swap |
| `8e` | Linux LVM |
| `fd` | RAID Linux auto |
| `ef` | EFI System |

💡 Lors de la création : laisser `fdisk` gérer le numéro de partition et le secteur de début. Pour la taille, utiliser la notation `+6G`, `+500M`, etc.

##### `parted` (MBR et GPT, scriptable)

```bash
# parted /dev/sdb mklabel gpt
# parted /dev/sdb mkpart primary ext4 0% 50GB
# parted /dev/sdb print
```

---

### 8.2 - LVM (Logical Volume Manager)

LVM ajoute une couche logique entre les disques physiques et les systèmes de fichiers, apportant une **souplesse** majeure en production : agrandissement/réduction de volumes **à chaud**, sans être contraint par la contiguïté physique.

#### 🧱 Architecture LVM en 3 couches

```text
┌─────────────────────────────────────┐
│         Volumes Logiques (LV)       │  ← Ce qu'on formate et monte
│   /dev/vg/lv1    /dev/vg/lv2        │
├─────────────────────────────────────┤
│         Groupe de Volumes (VG)      │  ← Pool d'espace agrégé
│              vggroup1               │
├─────────────────────────────────────┤
│       Volumes Physiques (PV)        │  ← Disques/partitions physiques
│  /dev/sdb1   /dev/sdb2   /dev/sdc  │
└─────────────────────────────────────┘
```

#### 🔧 Création LVM

**Prérequis** : les partitions doivent avoir le type `8e` (Linux LVM) dans `fdisk`.

💡 Il est possible de créer un PV sur un disque entier (sans le partitionner), à condition d'effacer au préalable les 512 premiers octets.

```bash
# 1. Créer les Physical Volumes
# pvcreate /dev/sdb1 /dev/sdb2

# 2. Créer le Volume Group
# vgcreate vggroup1 /dev/sdb1
# On peut inclure plusieurs PV d'un coup :
# vgcreate vggroup1 /dev/sdb1 /dev/sdb2

# 3. Créer les Logical Volumes
# lvcreate -n lv1 -L 150M vggroup1     # Taille fixe
# lvcreate -n lv2 -l 100%FREE vggroup1 # Tout l'espace restant
```

Deux chemins pour accéder à un LV :

- `/dev/<vg>/<lv>` (ex : `/dev/ol_srv-cli/Data`)
- `/dev/mapper/<vg>-<lv>` (ex : `/dev/mapper/ol_srv--cli-Data`)

#### 🔧 Étendre un LVM

```bash
# Ajouter un PV au VG existant
# vgextend vggroup1 /dev/sdb2

# Agrandir un LV
# lvextend -L +400M /dev/vggroup1/lv1     # Ajouter 400M
# lvextend -L 1G /dev/vggroup1/lv1        # Fixer à 1 Go
# lvextend -l +100%FREE /dev/vggroup1/lv1 # Tout l'espace restant du VG
```

⚠️ Après un `lvextend`, le système de fichiers ne prend **pas automatiquement** en compte la nouvelle taille. Il faut le redimensionner :

```bash
# Pour ext4 :
# resize2fs -fp /dev/vggroup1/lv1

# Pour XFS :
# xfs_growfs /dev/vggroup1/lv1
```

💡 L'option `-r` de `lvextend` redimensionne le FS automatiquement :

```bash
# lvextend -l +100%FREE -r /dev/ol_srv-cli/var
```

#### 🔧 Réduire un LV (⚠️ opération risquée)

```bash
# lvreduce -L -200M /dev/vggroup1/lv1
```

⚠️ Toujours réduire le FS **avant** le LV. XFS ne supporte **pas** la réduction.

#### 📋 Commandes d'information LVM

| Commande | Rôle |
| --- | --- |
| `pvs` / `pvdisplay` | Infos Physical Volumes |
| `vgs` / `vgdisplay` | Infos Volume Groups |
| `lvs` / `lvdisplay` | Infos Logical Volumes |
| `lvm` | Shell interactif LVM |

Les commandes en `*s` donnent un résumé compact, les commandes en `*display` donnent le détail.

---

### 8.3 - Systèmes de fichiers

#### 🧱 Structure interne d'un FS Unix

Tous les FS Unix sont segmentés en **blocs** de 4 Ko (par défaut). Types de blocs :

| Bloc | Rôle |
| --- | --- |
| **Superbloc** | Infos vitales du FS (taille, nb montages, dates). Dupliqué par sécurité. |
| **Inode** | Métadonnées d'un fichier (type, droits, UID, GID, taille, dates, 15 adresses vers les blocs de données) |
| **Bloc de données** | Données réelles du fichier |
| **Bloc d'indirection** | Redirige vers d'autres blocs (pour les gros fichiers) |

📌 **L'inode ne contient PAS le nom du fichier.** Le nom est stocké dans le répertoire parent.

Taille d'un inode : 128 octets. Avec des blocs de 4 Ko → 32 inodes par bloc.

#### 📂 Systèmes de fichiers disponibles

**XFS** : FS par défaut sur RHEL 7+. 64 bits, journalisé, optimisé pour les gros volumes avec beaucoup de fichiers.

**ext4** : FS par défaut sur RHEL 9 (correction du support : ext4 reprend la main). Peu sujet à la fragmentation grâce à la pré-allocation de blocs contigus.

| Caractéristique | ext4 | XFS |
| --- | --- | --- |
| Taille max fichier | 16 Tio (blocs 4 Ko) | 8 Eio |
| Taille max volume | 1 Eio (16 Tio limité par e2fsprogs) | 8 Eio |
| Nb max fichiers | 4 milliards | 2^64 |
| Réduction du FS | ✅ Oui (`resize2fs`) | ❌ Non |
| Outil de vérification | `fsck.ext4` / `e2fsck` | `xfs_repair` (pas `fsck.xfs`) |

Autres FS supportés nativement : NTFS, FAT. Possibilité d'installer Btrfs, etc.

#### 🔧 Création de FS (formatage)

```bash
# mkfs.ext4 /dev/sdb1
# mkfs.ext4 -L "Data" /dev/ol_srv-cli/Data    # Avec label
# mkfs.xfs /dev/sdc1
# mkfs.xfs -L "BDD" /dev/ol_srv-cli/BDD
# mkswap /dev/sdb2                             # Swap
```

#### 🔧 Modification de FS

**Pour ext** (`tune2fs`) :

```bash
$ tune2fs -l /dev/sdb1          # Afficher les infos du superbloc
# tune2fs -L NewLabel /dev/sdb1 # Changer le label
# tune2fs -c 30 /dev/sdb1       # Check tous les 30 montages
# tune2fs -i 2w /dev/sdb1       # Check toutes les 2 semaines
# resize2fs /dev/sdb1           # Redimensionner le FS
```

**Pour XFS** :

```bash
# xfs_admin -l /dev/sdc1         # Voir le label
# xfs_admin -L NewLabel /dev/sdc1 # Changer le label
# xfs_growfs /dev/sdc1            # Agrandir le FS (monté)
$ xfs_spaceman /dev/sdc1          # Espace disponible
```

#### 🔧 Vérification d'un FS

⚠️ **Toujours démonter avant de vérifier** (sauf lecture seule) :

```bash
# umount /dev/sdb1
# fsck.ext4 /dev/sdb1      # Pour ext
# xfs_repair /dev/sdc1     # Pour XFS (fsck.xfs ne fait RIEN)
```

#### 🔍 Prise d'information

```bash
blkid                     # UUID, TYPE, LABEL de tous les devices
blkid /dev/sdb1           # Info sur un device spécifique
lsblk                     # Vue arborescente des disques
lsblk -f                  # Avec FS, UUID, mount points
lsblk -o UUID /dev/...    # Juste l'UUID
df -h                     # Espace utilisé (human readable)
df -i                     # Infos inodes
du -sh /etc               # Taille d'un répertoire (avec sous-dossiers)
```

💡 `ls -l` ne donne **pas** la vraie taille d'un répertoire (juste la taille de l'inode du répertoire). Utiliser `du -sh` à la place.

---

### 8.4 - Montage d'un système de fichiers

Sous Linux, la mise à disposition d'un FS se fait en le montant dans un **répertoire vide** (point de montage). Le contenu pré-existant du répertoire devient inaccessible tant que le FS est monté.

#### 🔧 Montage manuel (`mount`)

```bash
# mount -t ext4 /dev/sdb1 /mnt
# mount /dev/sdb1 /partition                 # Auto-détection du type FS
# mount -o ro /dev/sdb1 /mnt                 # Lecture seule
# mount -o remount,rw /mnt                   # Changer les options sans démonter
```

Options de montage courantes :

| Option | Défaut | Rôle |
| --- | --- | --- |
| `sync/async` | async | Buffering en RAM avant écriture |
| `exec/noexec` | exec | Autoriser l'exécution de binaires |
| `ro/rw` | rw | Lecture seule / lecture-écriture |
| `suid/nosuid` | suid | Interpréter les bits SUID |
| `auto/noauto` | auto | Monter automatiquement avec `mount -a` |
| `user/nouser` | nouser | Autoriser un utilisateur non-root à monter |
| `remount` | - | Changer les options sans démonter |
| `noatime` | - | Ne pas mettre à jour le timestamp d'accès (performance) |

#### 🔍 Voir les montages actifs

```bash
mount                 # Liste complète (peu lisible)
findmnt               # Vue arborescente (bien plus lisible)
findmnt /mnt          # Info sur un point de montage précis
```

#### 🔧 Démontage (`umount`)

```bash
# umount /mnt
# umount /dev/sdb1
# umount -l /mnt        # Lazy : détache immédiatement (finit en arrière-plan)
# umount -f /mnt        # Force (risque de perte de données)
```

Si le FS est occupé :

```bash
$ lsof /mnt             # Quels processus utilisent le point de montage
$ fuser -m /mnt         # Idem
# fuser -km /mnt        # Kill les processus qui bloquent (⚠️)
```

---

### 8.5 - Montage automatique (`/etc/fstab`)

Les montages automatiques sont gérés par **systemd** au démarrage, à partir du fichier `/etc/fstab`.

#### 📄 Structure de `/etc/fstab`

```bash
<file system>        <mount point>   <type>   <options>   <dump>  <pass>
UUID=xxx             /data           ext4     defaults    0       2
/dev/ol_srv-cli/Data /srv/data       ext4     defaults    0       0
/dev/mapper/ol-swap  none            swap     sw          0       0
```

| Champ | Rôle | Recommandation |
| --- | --- | --- |
| `<file system>` | Source du FS | Préférer **UUID** pour les partitions, chemin `/dev/vg/lv` pour LVM |
| `<mount point>` | Répertoire de montage | Doit exister |
| `<type>` | Type de FS | `ext4`, `xfs`, `swap`, etc. Éviter `auto` (lent) |
| `<options>` | Options de montage | `defaults` = `async,nouser,exec,rw,auto,suid,dev` |
| `<dump>` | Sauvegarde dump | 0 = pas de backup (très rarement utilisé) |
| `<pass>` | Vérification au boot | 0 = pas de check, 1 = racine, 2 = autres |

#### ⚠️ Tester une nouvelle entrée avant de rebooter

```bash
# Méthode recommandée : monter uniquement le nouveau point de montage
# mount /srv/data

# Si rien ne s'affiche → OK
# Si erreur → corriger /etc/fstab avant de rebooter !
```

⚠️ Ne pas utiliser `mount -a` pour tester : elle tente de monter **toutes** les entrées, pas juste la nouvelle.

📌 Après modification de `/etc/fstab`, penser à recharger la configuration systemd :

```bash
# systemctl daemon-reload
```

---

### 🧪 Atelier 7 - Gestion du stockage

#### Partitionnement + FS standard

```bash
# Partitionner le nouveau disque 20 Go avec fdisk
# fdisk /dev/sdb
#   o     → Table MBR
#   n     → Partition 1, taille +6G
#   n     → Partition 2, tout le reste
#   t 2   → Type 8e (LVM)
#   w     → Écrire

# Formater et monter la première partition
# mkdir /partition
# mkfs.xfs /dev/sdb1
# mount /dev/sdb1 /partition
```

#### LVM : nouveau volume Data

```bash
# pvcreate /dev/sdb2
# vgextend ol_srv-cli /dev/sdb2
# lvcreate -n Data -L 10G ol_srv-cli
# mkfs.ext4 -L "Data" /dev/ol_srv-cli/Data

# Récupérer l'UUID et configurer fstab
$ lsblk -o UUID /dev/ol_srv-cli/Data
# Ajouter dans /etc/fstab :
#   UUID=<uuid> /srv/data ext4 defaults 0 0

# systemctl daemon-reload
# mount -a
```

#### Étendre `/var` sans interruption

```bash
# Passer en rescue pour libérer /var si nécessaire
# systemctl isolate rescue.target
# lsof | grep /var
# umount /var

# Étendre le LV + le FS en une commande (-r)
# lvextend -l +100%FREE -r /dev/ol_srv-cli/var

# Remonter et revenir en mode normal
# mount /var
# systemctl isolate default.target
```

#### Nouveau disque 40 Go pour BDD

💡 Forcer la détection d'un nouveau disque SCSI à chaud (sans reboot) :

```bash
# echo "- - -" > /sys/class/scsi_host/host0/scan
# echo "- - -" > /sys/class/scsi_host/host1/scan
# echo "- - -" > /sys/class/scsi_host/host2/scan
```

```bash
# pvcreate /dev/sdc
# vgextend ol_srv-cli /dev/sdc
# lvcreate -l 100%FREE -n BDD ol_srv-cli
# mkdir /srv/BDD
# mkfs.xfs -L "BDD" /dev/ol_srv-cli/BDD

# Ajouter dans /etc/fstab :
#   /dev/ol_srv-cli/BDD  /srv/BDD  xfs  defaults  0  0

# systemctl daemon-reload
# mount -a
```

#### Taille du dossier `/etc`

```bash
du -sh /etc
```

#### Bonus : montage CIFS

```bash
# dnf install cifs-utils
# mount //10.35.0.106/distrib /srv/distrib -t cifs \
    -o username=User,domain=ad.campus-eni.fr,vers=3.0
```

#### Bonus : RAID 5 logiciel

```bash
# Scan des nouveaux disques SCSI
# echo "- - -" > /sys/class/scsi_host/host{0,1,2}/scan

# Créer la grappe RAID 5
# mdadm --create /dev/md0 --level=5 --raid-devices=3 /dev/sd{d,e,f}

# Formater et monter
# mkfs.ext4 -L "RAID5" /dev/md0
# mkdir /RAID5

# fstab :
#   /dev/md0  /RAID5  ext4  defaults  0  0
# systemctl daemon-reload
# mount -a
```

---

### 📌 À retenir

1. **MBR** : max 2 To, 4 partitions primaires. **GPT** : >2 To, 128 partitions, UEFI.
2. **LVM** = 3 couches : PV → VG → LV. Commandes : `pvcreate/vgcreate/lvcreate`, `pvs/vgs/lvs`, `vgextend/lvextend`.
3. Après `lvextend`, toujours redimensionner le FS (`resize2fs` pour ext, `xfs_growfs` pour XFS). L'option `-r` le fait automatiquement.
4. Pour vérifier un FS : `fsck` pour ext, **`xfs_repair`** pour XFS (`fsck.xfs` ne fait rien).
5. `/etc/fstab` : préférer les **UUID** pour les partitions. Toujours tester avec `mount /point` avant de rebooter. Penser au `systemctl daemon-reload`.
