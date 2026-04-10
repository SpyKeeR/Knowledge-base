# 💾 Gestion de l'Espace SWAP

---

## 🤔 Qu'est-ce que la SWAP ?

### Le Concept

**SWAP** = **Espace disque** utilisé comme **extension de la RAM**

**Analogie** 🗄️ : 
- **RAM** = Bureau (rapide, cher, limité)
- **SWAP** = Tiroir à côté (plus lent, moins cher, extensible)

Quand le bureau est plein, on range les documents les moins utilisés dans le tiroir.

---

### Comment ça fonctionne ?

```
1. RAM presque pleine
   └── Système cherche les données les moins utilisées

2. Déplacement vers SWAP
   └── Données transférées de la RAM vers le disque

3. Libération de RAM
   └── Nouvelles applications peuvent utiliser la RAM

4. Besoin des anciennes données ?
   └── SWAP → RAM (= swap in)

5. Rotation continue
   └── Données en roulement entre RAM et SWAP
```

**💡 Règle d'or** : Les données **les moins utilisées depuis le plus longtemps** vont en SWAP en premier

**⚠️ Attention** : SWAP est **beaucoup plus lent** que la RAM (disque vs mémoire)

---

### Quand la SWAP est-elle utilisée ?

**Scénarios typiques** :
- 🖼️ Photoshop ouvert avec 50 calques
- 🌐 100 onglets de navigateur ouverts  
- 🎮 Jeu qui consomme toute la RAM
- 💻 Serveur avec beaucoup de services
- 😴 Hibernation (tout le contenu RAM → SWAP)

**Sans SWAP** : `Out of Memory` → Kernel tue des processus (OOM Killer)

**Avec SWAP** : Système ralentit mais continue de fonctionner

---

## 🔍 Voir la SWAP Actuelle

### swapon --show

```bash
swapon --show
```

**Résultat typique** :
```
NAME      TYPE      SIZE   USED PRIO
/dev/sda5 partition   2G   512M   -2
/swapfile file      512M     0M   -3
```

**Colonnes** :
- **NAME** : Emplacement (partition ou fichier)
- **TYPE** : Type de support (partition / file)
- **SIZE** : Taille totale
- **USED** : Quantité utilisée
- **PRIO** : Priorité (-2 = plus prioritaire que -3)

---

### Interpréter les résultats

```bash
# Pas de SWAP configurée
swapon --show
# (Aucune sortie)

# SWAP configurée mais pas utilisée
NAME      TYPE      SIZE USED PRIO
/dev/sda5 partition   2G   0B   -2

# SWAP bien utilisée
NAME      TYPE      SIZE   USED PRIO
/dev/sda5 partition   2G  1.5G   -2
```

**💡 Si USED = 0** : Soit vous avez assez de RAM, soit la SWAP n'est pas sollicitée

---

### Autres commandes pour voir la mémoire

```bash
# Vue d'ensemble mémoire + swap
free -h

# Résultat :
#               total        used        free      shared  buff/cache   available
# Mem:           7.7G        2.3G        3.1G        156M        2.3G        5.0G
# Swap:          2.0G        512M        1.5G

# Informations détaillées
cat /proc/swaps

# Résultat :
# Filename      Type      Size    Used   Priority
# /dev/sda5     partition 2097148 524288 -2
```

---

## 🔧 Créer une Partition SWAP

### Étape 1 : Préparer la Partition

**Prérequis** : Avoir une partition dédiée (créée avec `fdisk`, `parted`, etc.)

```bash
# Formater la partition pour la SWAP
mkswap /dev/sdb2
```

**Ce qui se passe** :
- Création d'une signature SWAP sur la partition
- La partition devient utilisable comme SWAP

**Résultat** :
```
Setting up swapspace version 1, size = 2 GiB (2147479552 bytes)
no label, UUID=abc123-def456-...
```

**💡 Point clé** : `mkswap` = équivalent de `mkfs` pour la SWAP

---

### Étape 2 : Activer la SWAP

```bash
swapon /dev/sdb2
```

**Effet** : La partition est **ajoutée au pool de mémoire virtuelle**

**Vérification** :
```bash
swapon --show
# Votre nouvelle partition apparaît !

free -h
# La ligne Swap montre plus d'espace
```

---

### Option verbose

```bash
swapon --verbose /dev/sdb2
# ou
swapon -v /dev/sdb2
```

**Affiche** :
```
swapon: /dev/sdb2: found swap signature: version 1, page-size 4, same byte order
swapon: /dev/sdb2: pagesize=4096, swapsize=2147483648, devsize=2147483648
swapon /dev/sdb2
```

**Usage** : Debugging, vérifier que tout est correct

---

## 🔄 Désactiver la SWAP

### swapoff

```bash
swapoff /dev/sdb2
```

**Effet** : 
- Détache la partition du pool de SWAP
- Données ramenées en RAM si nécessaire
- SWAP n'est plus utilisée

**⚠️ Attention** : Si SWAP pleine et RAM insuffisante → Peut échouer ou être très lent !

**Désactiver TOUTE la SWAP** :
```bash
swapoff -a
# Désactive tous les espaces SWAP actifs
```

**Cas d'usage** :
- Avant de supprimer une partition SWAP
- Pour maintenance
- Pour tester le système sans SWAP

---

## 📄 Créer un Fichier SWAP

### Pourquoi un fichier ?

**Avantages** :
- ✅ Pas besoin de partitionner le disque
- ✅ Taille modifiable facilement
- ✅ Peut être créé/supprimé à la volée

**Inconvénients** :
- ⚠️ Légèrement plus lent qu'une partition
- ⚠️ Fragmentation possible

---

### Étape 1 : Créer le Fichier avec dd

```bash
dd if=/dev/zero of=/swap bs=1M count=128 status=progress
```

**Décryptage** :
```
dd if=/dev/zero of=/swap bs=1M count=128 status=progress
│  │            │        │       │         │
│  │            │        │       │         └─── Afficher progression
│  │            │        │       └───────────── 128 blocs
│  │            │        └───────────────────── Blocs de 1 MB
│  │            └────────────────────────────── Fichier destination
│  └─────────────────────────────────────────── Source : /dev/zero
└────────────────────────────────────────────── Commande dd
```

**Résultat** : Fichier `/swap` de **128 MB** (128 × 1 MB)

---

### Pourquoi /dev/zero ?

**`/dev/zero`** = Source inépuisable de bytes à zéro (0x00)

**Exigence SWAP** : Le fichier **DOIT** contenir des bytes à 0

**Analogie** 📝 : Comme prendre une feuille vierge avant d'écrire dessus

---

### Options de dd

| Option | Description | Exemple |
|--------|-------------|---------|
| `if=` | **I**nput **F**ile (source) | `if=/dev/zero` |
| `of=` | **O**utput **F**ile (destination) | `of=/swap` |
| `bs=` | **B**lock **S**ize (taille bloc) | `bs=1M` (1 MégaByte) |
| `count=` | Nombre de blocs | `count=128` |
| `status=progress` | Afficher progression | Barre de progression |

---

### Exemples de tailles

```bash
# 128 MB
dd if=/dev/zero of=/swap bs=1M count=128 status=progress

# 512 MB
dd if=/dev/zero of=/swap bs=1M count=512 status=progress

# 1 GB
dd if=/dev/zero of=/swap bs=1M count=1024 status=progress

# 2 GB
dd if=/dev/zero of=/swap bs=1M count=2048 status=progress

# Alternative : utiliser bs=1G
dd if=/dev/zero of=/swap bs=1G count=2 status=progress
```

**💡 Formule** : Taille finale = `bs × count`

---

### Étape 2 : Sécuriser le Fichier

**Problème** : Le fichier SWAP peut contenir des données **sensibles** (mots de passe, clés...)

**Solution** : Restreindre les permissions

```bash
chmod 600 /swap
```

**Signification** :
```
6   0   0
│   │   │
│   │   └─── Others: aucun droit (---)
│   └─────── Group: aucun droit (---)
└─────────── Owner (root): lecture + écriture (rw-)
```

**Résultat** : **Seul root** peut lire/écrire le fichier SWAP

**⚠️ Sécurité critique** : Sans `chmod 600`, un utilisateur pourrait lire des données confidentielles !

---

### Étape 3 : Formater en SWAP

```bash
mkswap /swap
```

**Résultat** :
```
Setting up swapspace version 1, size = 128 MiB (134213632 bytes)
no label, UUID=xyz789-abc123-...
```

---

### Étape 4 : Activer le Fichier SWAP

```bash
swapon /swap
```

**Vérification** :
```bash
swapon --show
# NAME      TYPE SIZE USED PRIO
# /swap     file 128M   0B   -2

free -h
# Swap: 128M (au lieu de 0B)
```

**🎉 Succès** : Votre fichier SWAP est actif !

---

## 🔁 Rendre la SWAP Permanente

### Le Problème

`swapon` active la SWAP **temporairement**
- ✅ Fonctionne jusqu'au prochain reboot
- ❌ Disparaît au redémarrage

---

### La Solution : /etc/fstab

**Éditer** `/etc/fstab` :

```bash
vim /etc/fstab
```

**Ajouter** :

#### Pour une partition SWAP

```bash
/dev/sdb2    none    swap    sw    0    0
#    │        │       │      │    │    │
#    │        │       │      │    │    └─── Pass (0 = pas de fsck)
#    │        │       │      │    └──────── Dump (0 = pas de backup)
#    │        │       │      └───────────── Options (sw = swap)
#    │        │       └──────────────────── Type (swap)
#    │        └──────────────────────────── Point de montage (none)
#    └───────────────────────────────────── Device
```

#### Pour un fichier SWAP

```bash
/swap    none    swap    sw    0    0
```

**Alternative avec UUID** (recommandé) :

```bash
# Trouver l'UUID
blkid /dev/sdb2
# /dev/sdb2: UUID="abc-123-def" TYPE="swap"

# Dans /etc/fstab
UUID=abc-123-def    none    swap    sw    0    0
```

---

### Activer tout le fstab

```bash
# Activer toutes les SWAPs du fstab
swapon -a

# Vérifier
swapon --show
```

**💡 Au prochain boot** : La SWAP sera activée automatiquement

---

## 📋 Antisèche - Commandes SWAP

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `swapon --show` | Voir les SWAPs actives | `swapon --show` |
| `free -h` | Voir mémoire + SWAP | `free -h` |
| `mkswap /dev/sdb2` | Formater partition en SWAP | `mkswap /dev/sdb2` |
| `swapon /dev/sdb2` | Activer une SWAP | `swapon /dev/sdb2` |
| `swapon -v /dev/sdb2` | Activer avec détails | `swapon -v /dev/sdb2` |
| `swapoff /dev/sdb2` | Désactiver une SWAP | `swapoff /dev/sdb2` |
| `swapoff -a` | Désactiver toutes les SWAPs | `swapoff -a` |
| `dd if=/dev/zero of=/swap bs=1M count=128` | Créer fichier SWAP 128MB | `dd if=/dev/zero of=/swap bs=1M count=512` |
| `chmod 600 /swap` | Sécuriser fichier SWAP | `chmod 600 /swap` |
| `swapon -a` | Activer toutes SWAPs (fstab) | `swapon -a` |

---

## 🎓 Points Clés pour l'Examen

✅ **SWAP** : Extension de RAM sur disque (plus lent)  
✅ **Principe** : Données les moins utilisées depuis longtemps → SWAP  
✅ **swapon --show** : Afficher les SWAPs actives  
✅ **mkswap** : Formater une partition/fichier pour SWAP  
✅ **swapon** : Activer la SWAP (ajouter au pool)  
✅ **swapoff** : Désactiver la SWAP (retirer du pool)  
✅ **Fichier SWAP** : Doit contenir des bytes à 0 (`/dev/zero`)  
✅ **dd** : Créer fichier SWAP (`if=/dev/zero of=/swap bs=1M count=N`)  
✅ **chmod 600** : Sécurité fichier SWAP (root seulement)  
✅ **/etc/fstab** : Rendre SWAP permanente au boot  
✅ **swapon -a** : Activer toutes les SWAPs du fstab  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Créer une SWAP de 2GB sur partition

```bash
# 1. Créer la partition avec fdisk/parted (type Linux swap)
fdisk /dev/sdb
# Créer partition /dev/sdb2, type 82 (Linux swap)

# 2. Formater en SWAP
sudo mkswap /dev/sdb2

# 3. Activer
sudo swapon /dev/sdb2

# 4. Vérifier
swapon --show
free -h

# 5. Rendre permanent
echo "/dev/sdb2    none    swap    sw    0    0" | sudo tee -a /etc/fstab

# 6. Tester le fstab
sudo swapoff /dev/sdb2
sudo swapon -a
swapon --show  # Doit réapparaître
```

---

### Scénario 2 : Créer un fichier SWAP de 512MB

```bash
# 1. Créer le fichier (512 MB)
sudo dd if=/dev/zero of=/swapfile bs=1M count=512 status=progress

# 2. Sécuriser
sudo chmod 600 /swapfile

# 3. Vérifier les permissions
ls -lh /swapfile
# -rw------- 1 root root 512M ...

# 4. Formater
sudo mkswap /swapfile

# 5. Activer
sudo swapon /swapfile

# 6. Vérifier
swapon --show
free -h

# 7. Rendre permanent
echo "/swapfile    none    swap    sw    0    0" | sudo tee -a /etc/fstab
```

---

### Scénario 3 : Augmenter la SWAP existante

```bash
# Vous avez 2GB de SWAP, vous voulez 4GB total

# 1. Créer un nouveau fichier de 2GB
sudo dd if=/dev/zero of=/swap2 bs=1G count=2 status=progress

# 2. Sécuriser
sudo chmod 600 /swap2

# 3. Formater
sudo mkswap /swap2

# 4. Activer
sudo swapon /swap2

# 5. Vérifier
swapon --show
# NAME       TYPE SIZE USED PRIO
# /dev/sda5  part   2G  500M   -2
# /swap2     file   2G    0B   -3

free -h
# Swap: 4G (2G + 2G)

# 6. Rendre permanent
echo "/swap2    none    swap    sw    0    0" | sudo tee -a /etc/fstab
```

---

### Scénario 4 : Supprimer une SWAP

```bash
# 1. Désactiver
sudo swapoff /swap

# 2. Retirer du fstab
sudo vim /etc/fstab
# Supprimer ou commenter la ligne correspondante

# 3. Supprimer le fichier (optionnel)
sudo rm /swap

# 4. Vérifier
swapon --show  # Ne doit plus apparaître
free -h        # Swap diminuée
```

---

### Scénario 5 : Vérifier si la SWAP est utilisée

```bash
# Méthode 1 : swapon
swapon --show
# Si USED > 0, la SWAP est utilisée

# Méthode 2 : free
free -h
# Regarder la ligne Swap, colonne "used"

# Méthode 3 : /proc/swaps
cat /proc/swaps
# Regarder la colonne "Used"

# Méthode 4 : top/htop
top
# Ligne "KiB Swap" en haut
```

---

### Scénario 6 : Configurer la priorité de SWAP

```bash
# Par défaut, priority est négative (-1, -2, ...)
# Plus la valeur est ÉLEVÉE, plus elle est PRIORITAIRE

# Activer avec priorité spécifique
sudo swapon --priority 10 /swap1
sudo swapon --priority 5 /swap2

swapon --show
# NAME    TYPE SIZE USED PRIO
# /swap1  file 1G    0B   10   ← Plus prioritaire
# /swap2  file 1G    0B    5

# Dans /etc/fstab
/swap1    none    swap    pri=10    0    0
/swap2    none    swap    pri=5     0    0
```

**Usage** : SSD (rapide) → priorité haute, HDD (lent) → priorité basse

---

### Scénario 7 : SWAP pour hibernation

```bash
# Règle : SWAP >= RAM pour l'hibernation

# 1. Vérifier la RAM
free -h
# Mem: 8G

# 2. Créer SWAP de 8G minimum
sudo dd if=/dev/zero of=/swap bs=1G count=8 status=progress
sudo chmod 600 /swap
sudo mkswap /swap
sudo swapon /swap

# 3. Configurer fstab
echo "/swap    none    swap    sw    0    0" | sudo tee -a /etc/fstab

# 4. Tester l'hibernation
sudo systemctl hibernate
```

---

## ⚖️ Combien de SWAP créer ?

### Règles Traditionnelles (Anciennes)

| RAM | SWAP recommandée |
|-----|------------------|
| < 2 GB | 2 × RAM |
| 2-8 GB | = RAM |
| > 8 GB | 0.5 × RAM |

---

### Règles Modernes (2025+)

**Dépend de l'usage** :

#### Serveur (pas d'hibernation)
- **RAM < 4 GB** → SWAP = 2 GB minimum
- **RAM ≥ 4 GB** → SWAP = 2-4 GB (sécurité)
- **RAM > 16 GB** → SWAP optionnelle (1-2 GB ou aucune)

#### Desktop (avec hibernation possible)
- **SWAP ≥ RAM** (pour pouvoir hiberner)
- Exemple : 16 GB RAM → 16 GB SWAP minimum

#### Système avec beaucoup de RAM (32+ GB)
- SWAP minimale ou aucune
- Exception : si hibernation nécessaire

---

### 💡 Conseil Pratique

**Commencez petit** :
- Créer 2-4 GB de SWAP
- Monitorer l'utilisation
- Augmenter si nécessaire (facile avec fichier SWAP)

**Monitoring** :
```bash
# Voir l'utilisation sur 24h
watch -n 60 'free -h'

# Ou vérifier régulièrement
swapon --show
```

---

**🎯 Prochaine étape** : Install a Boot Manager - GRUB

---

## 🧱 LVM - Logical Volume Manager

### C'est quoi LVM ?

**LVM** = Couche d'abstraction entre le **disque physique** et le **système de fichiers**

**Analogie** 🏗️ :
- **Sans LVM** : Murs en béton (rigides, difficiles à modifier)
- **Avec LVM** : Cloisons modulables (flexibles, faciles à déplacer)

**Avantages** :
- ✅ Redimensionner partitions à chaud
- ✅ Combiner plusieurs disques
- ✅ Déplacer données entre disques
- ✅ Snapshots (sauvegardes instantanées)

---

### 📐 Les 4 Concepts Clés

```
Disques physiques (/dev/sda, /dev/sdb)
         ↓
    🔷 PV (Physical Volume)
         ↓
    🔶 VG (Volume Group)
         ↓
    🔵 LV (Logical Volume)
         ↓
    Système de fichiers (ext4, xfs...)
```

---

#### 🔷 PV - Physical Volume

**C'est quoi ?** Une partition ou un disque entier préparé pour LVM

**Analogie** 🧱 : Les **briques** individuelles

**Exemples** :
- `/dev/sda1` → PV
- `/dev/sdb` → PV (disque entier)
- `/dev/sdc2` → PV

---

#### 🔶 VG - Volume Group

**C'est quoi ?** Un **groupe de PV** formant un pool de stockage

**Analogie** 🏗️ : Un **tas de briques** qu'on va utiliser pour construire

**Exemple** :
```
VG "vgvolume" = /dev/sda1 + /dev/sda2 + /dev/sdc
                 (500GB)    (500GB)      (1TB)
Total VG = 2 TB
```

**💡 Point clé** : On peut ajouter/retirer des PV d'un VG à tout moment

---

#### 🔵 LV - Logical Volume

**C'est quoi ?** Une "partition virtuelle" créée depuis un VG

**Analogie** 🏠 : Les **pièces** construites avec le tas de briques

**Exemple** :
```
VG "vgvolume" (2 TB)
   ├── LV "partition1" (500 GB)  → /home
   ├── LV "partition2" (300 GB)  → /var
   └── Espace libre (1.2 TB)
```

**💡 Flexibilité** : On peut redimensionner les LV facilement !

---

#### 📦 PE - Physical Extent

**C'est quoi ?** Un **bloc de données** de taille fixe (généralement 4 MB)

**Analogie** 🧩 : Les **pièces de puzzle** qui composent les volumes

**Fonctionnement** :
```
VG divisé en PE de 4 MB
LV "partition1" = 500 PE (500 × 4 MB = 2 GB)
```

**Pourquoi c'est important ?**
- Permet à un LV de **s'étendre sur plusieurs PV**
- Granularité du redimensionnement

**Exemple** :
```
LV "partition1" = 250 PE sur /dev/sda1 + 250 PE sur /dev/sdb
                  → Total 500 PE = 2 GB
```

---

## 🔍 Découvrir les Volumes LVM

### lvmdiskscan - Scanner les Supports

```bash
lvmdiskscan
```

**Résultat typique** :
```
  /dev/sda1  [     500.00 GiB] 
  /dev/sda2  [     500.00 GiB] LVM physical volume
  /dev/sdb   [       1.00 TiB] LVM physical volume
  /dev/sdc1  [     200.00 GiB]
  2 disks
  2 partitions
  0 LVM physical volume whole disks
  2 LVM physical volumes
```

**Lecture** :
- Sans mention "LVM" → Disque/partition normal
- Avec "LVM physical volume" → Déjà utilisé pour LVM

**Usage** : Savoir quels disques sont disponibles pour créer des PV

---

## 🔧 Gérer les Physical Volumes (PV)

### Créer un PV

```bash
# Préparer une partition pour LVM
pvcreate /dev/sda1

# Préparer plusieurs partitions
pvcreate /dev/sda1 /dev/sda2 /dev/sdc
```

**Résultat** :
```
Physical volume "/dev/sda1" successfully created.
```

---

### Lister les PV

```bash
pvdisplay

# Ou version courte
pvs
```

**Résultat** :
```
  PV         VG       Fmt  Attr PSize   PFree
  /dev/sda1  vgvolume lvm2 a--  500.00g 100.00g
  /dev/sda2  vgvolume lvm2 a--  500.00g 500.00g
  /dev/sdc            lvm2 ---    1.00t   1.00t
```

---

### Supprimer un PV

```bash
pvremove /dev/sdc
```

**⚠️ Attention** : Le PV ne doit **PAS** être dans un VG !

**Séquence correcte** :
```bash
# 1. Retirer du VG d'abord
vgreduce vgvolume /dev/sdc

# 2. Puis supprimer le PV
pvremove /dev/sdc
```

---

## 🔧 Gérer les Volume Groups (VG)

### Créer un VG

```bash
vgcreate nom_vg partition1 [partition2 ...]
```

**Exemple** :
```bash
vgcreate vgvolume /dev/sda1 /dev/sda2
```

**Ce qui se passe** :
- Crée un VG nommé "vgvolume"
- Combine /dev/sda1 (500GB) + /dev/sda2 (500GB)
- Pool total = 1 TB

**Résultat** :
```
Volume group "vgvolume" successfully created
```

---

### Étendre un VG (ajouter un disque)

```bash
vgextend nom_vg nouvelle_partition
```

**Exemple** :
```bash
# Ajouter /dev/sdc au VG existant
vgextend vgvolume /dev/sdc
```

**Avant** :
```
vgvolume = /dev/sda1 + /dev/sda2 = 1 TB
```

**Après** :
```
vgvolume = /dev/sda1 + /dev/sda2 + /dev/sdc = 2 TB
```

**💡 Cas d'usage** : Manque d'espace → ajouter un nouveau disque

---

### Réduire un VG (retirer un disque)

```bash
vgreduce nom_vg partition_a_retirer
```

**Exemple** :
```bash
vgreduce vgvolume /dev/sdc
```

**⚠️ Prérequis** : Le PV doit être **libre** (aucun LV dessus)

**Si PV utilisé** :
```bash
# Déplacer les données d'abord
pvmove /dev/sdc

# Puis réduire
vgreduce vgvolume /dev/sdc
```

---

### Lister les VG

```bash
vgdisplay

# Ou version courte
vgs
```

**Résultat** :
```
  VG       #PV #LV #SN Attr   VSize VFree
  vgvolume   2   1   0 wz--n- 1.95t 1.45t
```

**Colonnes** :
- **#PV** : Nombre de Physical Volumes
- **#LV** : Nombre de Logical Volumes
- **VSize** : Taille totale du VG
- **VFree** : Espace libre

---

## 🔧 Gérer les Logical Volumes (LV)

### Créer un LV

```bash
lvcreate --size taille --name nom_lv nom_vg
```

**Exemple** :
```bash
lvcreate --size 2G --name partition1 vgvolume
```

**Décryptage** :
```
lvcreate --size 2G --name partition1 vgvolume
         │      │   │      │          │
         │      │   │      │          └─── VG source
         │      │   │      └────────────── Nom du LV
         │      │   └───────────────────── Option --name
         │      └───────────────────────── Taille (2 GB)
         └──────────────────────────────── Créer LV
```

**Résultat** :
```
Logical volume "partition1" created.
```

**Emplacement** : `/dev/vgvolume/partition1`

---

### Alternatives de taille

```bash
# En GB
lvcreate --size 2G --name partition1 vgvolume

# En MB
lvcreate --size 500M --name partition1 vgvolume

# En nombre d'extents
lvcreate --extents 512 --name partition1 vgvolume
# 512 PE × 4 MB = 2 GB

# Utiliser tout l'espace libre
lvcreate --extents 100%FREE --name partition1 vgvolume

# Utiliser 50% du VG total
lvcreate --extents 50%VG --name partition1 vgvolume
```

---

### Lister les LV

```bash
lvdisplay

# Ou version courte
lvs
```

**Résultat** :
```
  LV         VG       Attr       LSize   Pool Origin Data%
  partition1 vgvolume -wi-a----- 2.00g
  partition2 vgvolume -wi-a----- 500.00m
```

---

### Formater et Monter un LV

```bash
# 1. Créer le LV
lvcreate --size 2G --name partition1 vgvolume

# 2. Formater
mkfs.ext4 /dev/vgvolume/partition1

# 3. Créer point de montage
mkdir /mnt/data

# 4. Monter
mount /dev/vgvolume/partition1 /mnt/data

# 5. Rendre permanent (/etc/fstab)
echo "/dev/vgvolume/partition1  /mnt/data  ext4  defaults  0  2" >> /etc/fstab
```

---

## 📏 Redimensionner un LV

### La Commande lvresize

```bash
lvresize [options] chemin_lv
```

---

### Agrandir un LV

#### Méthode 1 : Taille absolue

```bash
# Redimensionner à 3 GB
lvresize --size 3G vgvolume/partition1
```

**⚠️ Attention** : Ceci redimensionne le LV, **PAS** le filesystem !

---

#### Méthode 2 : Taille relative

```bash
# Ajouter 1 GB
lvresize --size +1G vgvolume/partition1

# Retirer 500 MB
lvresize --size -500M vgvolume/partition1
```

---

#### Méthode 3 : Pourcentage

```bash
# Utiliser 100% du VG
lvresize --extents 100%VG vgvolume/partition1

# Utiliser 50% du VG
lvresize --extents 50%VG vgvolume/partition1

# Utiliser 100% de l'espace libre
lvresize --extents +100%FREE vgvolume/partition1
```

**💡 Point clé** : `100%VG` = 100% du Volume Group **total**

---

### Redimensionner LV + Filesystem en Une Commande

```bash
lvresize --resizefs --size 3G vgvolume/partition1
```

**Ce qui se passe** :
1. Redimensionne le LV à 3 GB
2. Appelle automatiquement `resize2fs` (ext4) ou équivalent
3. Filesystem redimensionné automatiquement

**Équivalent de** :
```bash
# Sans --resizefs (méthode manuelle)
lvresize --size 3G vgvolume/partition1
resize2fs /dev/vgvolume/partition1
```

**💡 Gain de temps** : Plus besoin de resize2fs séparément !

---

### ⚠️ ORDRE CRITIQUE pour Réduction vs Augmentation

#### 🔺 Augmenter (Safe)

```
1. Agrandir le LV
2. Agrandir le Filesystem
```

**Pourquoi ?** Le filesystem a besoin d'espace pour grandir

**Commande simple** :
```bash
lvresize --resizefs --size +1G vgvolume/partition1
# LVM fait tout dans le bon ordre
```

---

#### 🔻 Réduire (DANGER !)

```
1. Réduire le Filesystem D'ABORD ⚠️
2. Réduire le LV ENSUITE
```

**Pourquoi ?** Si on réduit le LV avant, on **coupe** le filesystem → **PERTE DE DONNÉES** !

**Commande manuelle (OBLIGATOIRE pour réduction)** :
```bash
# 1. Démonter
umount /mnt/data

# 2. Vérifier le filesystem
e2fsck -f /dev/vgvolume/partition1

# 3. RÉDUIRE LE FS D'ABORD
resize2fs /dev/vgvolume/partition1 2G

# 4. PUIS réduire le LV
lvresize --size 2G vgvolume/partition1

# 5. Remonter
mount /dev/vgvolume/partition1 /mnt/data
```

**⚠️ --resizefs avec réduction** :
```bash
# LVM est intelligent et fait dans le bon ordre
lvresize --resizefs --size 2G vgvolume/partition1
# Mais DÉMONTER d'abord est recommandé !
```

---

### Tableau Récapitulatif

| Action | Ordre | Commande |
|--------|-------|----------|
| **Augmenter** | LV puis FS | `lvresize --resizefs --size +1G vgvolume/lv` |
| **Réduire** | FS puis LV | 1. `umount` 2. `e2fsck -f` 3. `resize2fs` 4. `lvresize` |

**💡 Mnémonique** :
- **Augmenter** : Container d'abord, contenu ensuite (facile)
- **Réduire** : Contenu d'abord, container ensuite (attention !)

---

## 📋 Antisèche - Commandes LVM

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `lvmdiskscan` | Voir disques disponibles pour LVM | `lvmdiskscan` |
| `pvcreate` | Créer un Physical Volume | `pvcreate /dev/sda1` |
| `pvs` | Lister les PV | `pvs` |
| `pvremove` | Supprimer un PV | `pvremove /dev/sdc` |
| `vgcreate` | Créer un Volume Group | `vgcreate vgvolume /dev/sda1 /dev/sda2` |
| `vgextend` | Ajouter disque à VG | `vgextend vgvolume /dev/sdc` |
| `vgreduce` | Retirer disque de VG | `vgreduce vgvolume /dev/sdc` |
| `vgs` | Lister les VG | `vgs` |
| `lvcreate` | Créer un Logical Volume | `lvcreate --size 2G --name lv1 vgvolume` |
| `lvs` | Lister les LV | `lvs` |
| `lvresize` | Redimensionner LV | `lvresize --resizefs --size 3G vgvolume/lv1` |
| `lvresize` (%) | Utiliser % du VG | `lvresize --extents 100%VG vgvolume/lv1` |

---

## 🎓 Points Clés pour l'Examen

✅ **PV** : Physical Volume (partition/disque préparé pour LVM)  
✅ **VG** : Volume Group (pool de PV)  
✅ **LV** : Logical Volume (partition virtuelle depuis VG)  
✅ **PE** : Physical Extent (blocs, généralement 4 MB)  
✅ **lvmdiskscan** : Lister supports disponibles et volumes LVM  
✅ **vgcreate** : Créer VG depuis plusieurs PV  
✅ **vgextend** : Ajouter PV à VG existant  
✅ **vgreduce** : Retirer PV de VG  
✅ **lvcreate --size** : Créer LV avec taille  
✅ **lvresize --extents 100%VG** : Redimensionner à 100% du VG  
✅ **lvresize --resizefs** : Redimensionner LV + filesystem automatiquement  
✅ **Ordre réduction** : Filesystem AVANT LV (CRITIQUE !)  
✅ **Ordre augmentation** : LV PUIS filesystem (ou --resizefs)  
✅ **Swap file location** : `/swapfile` sur Linux  
✅ **VG divisés en** : Extents (PE)  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Créer un Setup LVM Complet

```bash
# 1. Scanner les disques disponibles
lvmdiskscan

# 2. Créer les Physical Volumes
pvcreate /dev/sda1 /dev/sda2

# 3. Créer le Volume Group
vgcreate vgdata /dev/sda1 /dev/sda2

# 4. Vérifier
vgs
# VG     #PV #LV VSize VFree
# vgdata   2   0  1.95t 1.95t

# 5. Créer un Logical Volume de 100 GB
lvcreate --size 100G --name lvhome vgdata

# 6. Formater
mkfs.ext4 /dev/vgdata/lvhome

# 7. Monter
mkdir /home2
mount /dev/vgdata/lvhome /home2

# 8. Vérifier
df -h | grep lvhome
```

---

### Scénario 2 : Étendre un LV (Agrandir)

```bash
# Situation : LV de 100 GB, on veut 150 GB

# Méthode 1 : Taille absolue
lvresize --resizefs --size 150G vgdata/lvhome

# Méthode 2 : Taille relative
lvresize --resizefs --size +50G vgdata/lvhome

# Méthode 3 : Utiliser tout l'espace libre du VG
lvresize --resizefs --extents 100%VG vgdata/lvhome

# Vérification
df -h | grep lvhome
# /dev/mapper/vgdata-lvhome  150G  ...
```

---

### Scénario 3 : Ajouter un Disque au VG (Extension)

```bash
# Nouveau disque /dev/sdc disponible

# 1. Préparer le disque
pvcreate /dev/sdc

# 2. Ajouter au VG existant
vgextend vgdata /dev/sdc

# 3. Vérifier
vgs
# VG     #PV #LV VSize VFree
# vgdata   3   1  2.95t 1.00t (nouveau disque de 1TB ajouté)

# 4. Étendre le LV avec le nouvel espace
lvresize --resizefs --extents +100%FREE vgdata/lvhome
```

---

### Scénario 4 : Réduire un LV (ATTENTION !)

```bash
# Situation : LV de 100 GB, on veut réduire à 50 GB
# ⚠️ SAUVEGARDER D'ABORD !

# 1. Démonter OBLIGATOIRE
umount /home2

# 2. Vérifier le filesystem
e2fsck -f /dev/vgdata/lvhome

# 3. Réduire le FILESYSTEM d'abord
resize2fs /dev/vgdata/lvhome 50G

# 4. PUIS réduire le LV
lvresize --size 50G vgdata/lvhome

# 5. Remonter
mount /dev/vgdata/lvhome /home2

# 6. Vérifier
df -h | grep lvhome
```

**⚠️ Alternative avec --resizefs** :
```bash
# 1. Démonter
umount /home2

# 2. Vérifier
e2fsck -f /dev/vgdata/lvhome

# 3. Réduire (LVM fait FS puis LV dans le bon ordre)
lvresize --resizefs --size 50G vgdata/lvhome

# 4. Remonter
mount /dev/vgdata/lvhome /home2
```

---

### Scénario 5 : Retirer un Disque du VG

```bash
# On veut retirer /dev/sdc du VG

# 1. Vérifier si le PV est utilisé
pvs
# PV        VG     Fmt  Attr PSize PFree
# /dev/sdc  vgdata lvm2 a--  1.00t 1.00t  ← PFree = tout libre, OK !

# Si PFree < PSize, déplacer les données d'abord
pvmove /dev/sdc

# 2. Réduire le VG
vgreduce vgdata /dev/sdc

# 3. Supprimer le PV
pvremove /dev/sdc

# 4. Vérifier
vgs
# vgdata maintenant sans /dev/sdc
```

---

### Scénario 6 : Créer plusieurs LV dans un VG

```bash
# VG de 1 TB, créer 3 LV

# 1. LV pour /home (300 GB)
lvcreate --size 300G --name lvhome vgdata

# 2. LV pour /var (200 GB)
lvcreate --size 200G --name lvvar vgdata

# 3. LV pour backups (tout le reste)
lvcreate --extents 100%FREE --name lvbackup vgdata

# 4. Formater
mkfs.ext4 /dev/vgdata/lvhome
mkfs.ext4 /dev/vgdata/lvvar
mkfs.ext4 /dev/vgdata/lvbackup

# 5. Lister
lvs
# LV        VG     Attr       LSize
# lvbackup  vgdata -wi-a----- 500.00g
# lvhome    vgdata -wi-a----- 300.00g
# lvvar     vgdata -wi-a----- 200.00g
```

---

### Scénario 7 : Snapshot LVM (Sauvegarde Instantanée)

```bash
# Créer un snapshot de lvhome

# 1. Créer snapshot (10% de la taille originale)
lvcreate --size 30G --snapshot --name lvhome-snap /dev/vgdata/lvhome

# 2. Monter le snapshot
mkdir /mnt/snap
mount /dev/vgdata/lvhome-snap /mnt/snap

# 3. Faire backup depuis le snapshot
tar czf /backup/home-backup.tar.gz /mnt/snap

# 4. Supprimer le snapshot
umount /mnt/snap
lvremove /dev/vgdata/lvhome-snap
```

---

## 🆚 Comparaison Partitions Traditionnelles vs LVM

| Caractéristique | Partitions Standard | LVM |
|-----------------|---------------------|-----|
| **Redimensionnement** | ⚠️ Difficile, limité | ✅ Facile, à chaud |
| **Combiner disques** | ❌ Non | ✅ Oui (VG) |
| **Snapshots** | ❌ Non | ✅ Oui |
| **Déplacer données** | ⚠️ Copie manuelle | ✅ `pvmove` |
| **Flexibilité** | ⚠️ Limitée | ✅ Maximale |
| **Complexité** | ✅ Simple | ⚠️ Plus complexe |
| **Performance** | ✅ Légèrement plus rapide | ⚠️ Overhead minimal |

**Recommandation** :
- **Serveurs** : LVM (flexibilité essentielle)
- **Desktop** : LVM recommandé (facilite gestion)
- **Systèmes embedded** : Partitions standard (simplicité)

---

**🎯 Prochaine étape** : Install a Boot Manager - GRUB

*Dernière mise à jour: 22 janvier 2026*

