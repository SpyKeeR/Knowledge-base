# 🥾 GRUB Legacy - Installation du Bootloader

---

## 🤔 Qu'est-ce que GRUB Legacy ?

**GRUB** = **GR**and **U**nified **B**ootloader

**Version Legacy** : Ancienne version de GRUB (version 0.9x)
**Version moderne** : GRUB 2 (version 1.9x+)

**Analogie** 📖 : GRUB Legacy = livre de poche ancien, GRUB 2 = encyclopédie moderne

**Où trouve-t-on GRUB Legacy ?**
- ⚠️ Anciennes distributions (avant 2010)
- ⚠️ Systèmes embarqués
- ⚠️ Systèmes legacy non migrés

**💡 Note** : La plupart des systèmes modernes utilisent GRUB 2, mais GRUB Legacy peut apparaître à l'examen LPIC-1

---

## 📍 Convention de Nommage des Disques

### Format GRUB Legacy

```
(hd0,0)
  │  │
  │  └─── Partition (commence à 0)
  └────── Disque (commence à 0)
```

**Exemples** :

| Linux | GRUB Legacy | Description |
|-------|-------------|-------------|
| `/dev/sda1` | `(hd0,0)` | 1er disque, 1ère partition |
| `/dev/sda2` | `(hd0,1)` | 1er disque, 2ème partition |
| `/dev/sdb1` | `(hd1,0)` | 2ème disque, 1ère partition |
| `/dev/sdc3` | `(hd2,2)` | 3ème disque, 3ème partition |

**⚠️ Attention** : GRUB Legacy compte depuis **0** (pas depuis 1) !

**Mnémonique** 🧮 :
- Linux : `/dev/sda1` → 1 = première partition
- GRUB Legacy : `(hd0,0)` → 0 = première partition

---

## 🔧 Installer GRUB depuis un Système Démarré

### La Commande grub-install

```bash
grub-install /dev/sda
```

**Ce qui se passe** :
1. GRUB s'installe dans le **MBR** de `/dev/sda`
2. Fichiers GRUB copiés dans `/boot/grub/`
3. Le système peut maintenant booter depuis ce disque

**⚠️ Point crucial** : On pointe sur le **DISQUE** (`/dev/sda`), **PAS** sur une partition (`/dev/sda1`)

**Pourquoi ?** Le MBR se trouve au début du disque, pas dans une partition

---

### Option --boot-directory

**Usage** : Spécifier un répertoire `/boot` non conventionnel

**Syntaxe** :
```bash
grub-install --boot-directory=/other/directory /dev/sda
```

**Cas d'usage typique** : Installation depuis un Live CD

**Exemple concret** :
```bash
# Situation : Système cassé, boot depuis Live CD
# Disque système monté dans /mnt

# Installer GRUB sur le disque système
grub-install --boot-directory=/mnt/boot /dev/sda
```

**Ce qui se passe** :
- MBR écrit sur `/dev/sda`
- Fichiers GRUB installés dans `/mnt/boot/grub/` (pas `/boot/grub/`)

**Analogie** 🏥 : Opération chirurgicale depuis l'extérieur

---

### Exemple : Réparation depuis Live CD

```bash
# 1. Booter sur Live CD

# 2. Monter la partition système
mount /dev/sda1 /mnt

# 3. Installer GRUB
grub-install --boot-directory=/mnt/boot /dev/sda

# 4. Redémarrer
reboot
```

---

## 🖥️ Installer GRUB depuis le GRUB Shell

### Accéder au GRUB Shell

**Situation** : Vous avez un disque de démarrage GRUB ou vous êtes dans le menu GRUB

**Action** : Appuyer sur **`C`** (comme **C**ommand)

**Résultat** :
```
grub>
```

**💡 Vous êtes dans le shell GRUB** : Interface en ligne de commande pour gérer GRUB

---

### Les Commandes Essentielles

#### 1. root - Définir le Périphérique de Démarrage

```bash
grub> root (hd0,0)
```

**Signification** : 
- "Le système va booter depuis `(hd0,0)` = `/dev/sda1`"
- C'est là que se trouve `/boot/`

**Résultat** :
```
Filesystem type is ext2fs, partition type 0x83
```

**💡 Point clé** : `root` dans GRUB = "où chercher les fichiers de boot" (pas le filesystem root `/`)

---

#### 2. find - Chercher un Fichier

**Usage** : Localiser une partition contenant un fichier spécifique

```bash
grub> find /boot/grub/stage1
```

**Résultat typique** :
```
(hd0,0)
```

**Signification** : Le fichier `stage1` existe sur `(hd0,0)` = `/dev/sda1`

**Cas d'usage** : 
- Vous ne savez pas sur quelle partition se trouve `/boot`
- `find` scanne tous les disques pour vous

**Autre exemple** :
```bash
grub> find /vmlinuz
(hd0,0)
(hd0,2)
```
→ Le kernel existe sur deux partitions

---

#### 3. setup - Installer GRUB dans le MBR

```bash
grub> setup (hd0)
```

**Ce qui se passe** :
- Installe **stage1** dans le **MBR** de `/dev/sda`
- Configure les pointeurs vers stage2
- GRUB est maintenant bootable

**Résultat** :
```
Checking if "/boot/grub/stage1" exists... yes
Checking if "/boot/grub/stage2" exists... yes
Running "install /boot/grub/stage1 (hd0) /boot/grub/stage2 p /boot/grub/menu.lst"... succeeded
Done.
```

**⚠️ Attention** : `setup (hd0)` pointe sur le **disque**, pas `(hd0,0)` qui serait une partition !

---

### Procédure Complète depuis GRUB Shell

```bash
# 1. Appuyer sur 'C' pour entrer dans le shell
grub>

# 2. Trouver la partition de boot
grub> find /boot/grub/stage1
(hd0,0)

# 3. Définir cette partition comme root
grub> root (hd0,0)
Filesystem type is ext2fs, partition type 0x83

# 4. Installer GRUB dans le MBR
grub> setup (hd0)
Running "install"... succeeded
Done.

# 5. Quitter et redémarrer
grub> quit
```

---

## 📄 Fichier de Configuration - menu.lst

### Emplacement

```
/boot/grub/menu.lst
```

**Alias** : Peut aussi être nommé `/boot/grub/grub.conf` (lien symbolique)

**Rôle** : Définir les **entrées du menu GRUB** au démarrage

---

### Structure du Fichier

```bash
# This is a comment
# Les lignes commençant par # sont des commentaires

title Système d'Exploitation
root (hd0,0)
kernel /vmlinuz root=/dev/hda1
initrd /initrd.img
module /boot/grub/i386-pc/915resolution.mod
```

---

### Décryptage Ligne par Ligne

#### title - Nom de l'Entrée

```bash
title Système d'Exploitation
```

**Effet** : Nom affiché dans le menu GRUB

**Résultat visuel** :
```
  Système d'Exploitation
  Windows XP
  Mode Recovery
```

**💡 Ce que voit l'utilisateur** au démarrage

---

#### root - Partition de Boot

```bash
root (hd0,0)
```

**Signification** : Les fichiers de boot (kernel, initrd) sont dans `(hd0,0)` = `/dev/sda1`

**⚠️ Attention** : C'est le root **GRUB**, pas le root filesystem Linux !

---

#### kernel - Charger le Noyau Linux

```bash
kernel /vmlinuz root=/dev/hda1
```

**Décryptage** :
```
kernel /vmlinuz root=/dev/hda1
   │       │         │
   │       │         └─── Paramètre : partition root du système
   │       └───────────── Fichier kernel (dans /boot/)
   └───────────────────── Commande GRUB
```

**Signification** :
- Charger le kernel `/boot/vmlinuz`
- Dire au kernel que la partition root est `/dev/hda1`

**Équivalent complet** :
```bash
kernel /vmlinuz root=/dev/hda1 ro quiet
        │         │            │  │
        │         │            │  └─── Mode silencieux
        │         │            └────── Read-only au boot
        │         └─────────────────── Root filesystem
        └───────────────────────────── Kernel
```

---

#### initrd - Initial RAM Disk

```bash
initrd /initrd.img
```

**Rôle** : Charger le système de fichiers temporaire en RAM (contient drivers)

**Emplacement** : `/boot/initrd.img`

**Pourquoi ?** Le kernel a besoin de certains drivers **avant** de pouvoir accéder au disque

**Analogie** 🎒 : Le sac à dos de survie avant d'accéder au vrai équipement

---

#### module - Charger un Module (Optionnel)

```bash
module /boot/grub/i386-pc/915resolution.mod
```

**Usage** : Charger des modules GRUB supplémentaires

**Exemple typique** : `915resolution.mod` pour ajuster la résolution vidéo sur vieux matériel Intel

**💡 Rarement utilisé** dans les configurations modernes

---

### Exemple d'Entrée Linux Complète

```bash
title Ubuntu 20.04 LTS
root (hd0,0)
kernel /vmlinuz-5.4.0-42-generic root=/dev/sda1 ro quiet splash
initrd /initrd.img-5.4.0-42-generic
```

**Résultat** : Entrée de menu fonctionnelle pour Ubuntu

---

## 🪟 Entrée pour Windows

### Configuration Dual-Boot

**Situation** : Windows installé sur `/dev/sda2` (deuxième partition)

```bash
title Windows XP
root (hd0,1)
makeactive
chainload +1
boot
```

---

### Décryptage Ligne par Ligne

#### title

```bash
title Windows XP
```

**Effet** : Nom dans le menu GRUB

---

#### root

```bash
root (hd0,1)
```

**Signification** : Windows est sur `(hd0,1)` = `/dev/sda2`

---

#### makeactive

```bash
makeactive
```

**Rôle** : Placer le **flag active** sur la partition

**Analogie** 🚩 : Dire au BIOS "cette partition est bootable"

**Technique** : Windows nécessite que sa partition soit marquée "active" pour démarrer

**Équivalent** : `fdisk` avec option "a" (toggle bootable flag)

---

#### chainload +1

```bash
chainload +1
```

**Signification** : 
- **Chainload** = Charger un autre bootloader
- **+1** = Premier secteur de la partition (où se trouve le bootloader Windows)

**Analogie** 🔗 : GRUB passe le relais au bootloader Windows (bootmgr)

**Pourquoi ?** Windows a son propre bootloader, GRUB ne sait pas booter Windows directement

---

#### boot

```bash
boot
```

**Effet** : Lancer le bootloader chargé (Windows bootmgr)

**💡 Commande finale** qui démarre effectivement Windows

---

### Exemple Multi-OS Complet

```bash
# Entrée 1 : Linux
title Ubuntu 20.04
root (hd0,0)
kernel /vmlinuz root=/dev/sda1 ro quiet
initrd /initrd.img

# Entrée 2 : Windows
title Windows 10
root (hd0,1)
makeactive
chainload +1
boot

# Entrée 3 : Mode Recovery Linux
title Ubuntu Recovery Mode
root (hd0,0)
kernel /vmlinuz root=/dev/sda1 ro single
initrd /initrd.img
```

**Résultat visuel au boot** :
```
  Ubuntu 20.04
  Windows 10
  Ubuntu Recovery Mode
```

---

## 📋 Antisèche - Commandes GRUB Legacy

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `grub-install /dev/sda` | Installer GRUB sur disque | `grub-install /dev/sda` |
| `grub-install --boot-directory` | Installer avec /boot custom | `grub-install --boot-directory=/mnt/boot /dev/sda` |
| `root (hd0,0)` | Définir partition de boot | `grub> root (hd0,0)` |
| `find /boot/grub/stage1` | Chercher partition GRUB | `grub> find /boot/grub/stage1` |
| `setup (hd0)` | Installer dans MBR | `grub> setup (hd0)` |
| `title` | Nom entrée menu | `title Ubuntu` |
| `kernel` | Charger noyau | `kernel /vmlinuz root=/dev/sda1` |
| `initrd` | Charger initramfs | `initrd /initrd.img` |
| `makeactive` | Flag partition active | `makeactive` |
| `chainload +1` | Charger bootloader Windows | `chainload +1` |
| `boot` | Démarrer | `boot` |

---

## 🎓 Points Clés pour l'Examen

✅ **GRUB Legacy** : Ancienne version (0.9x), remplacée par GRUB 2  
✅ **Convention** : `(hd0,0)` = `/dev/sda1` (compte depuis 0 !)  
✅ **grub-install** : Pointe sur le **disque** (`/dev/sda`), pas partition  
✅ **--boot-directory** : Spécifier /boot non conventionnel (réparation)  
✅ **Touche C** : Accéder au GRUB shell  
✅ **root (hd0,0)** : Définir partition de boot dans GRUB  
✅ **find** : Chercher un fichier sur toutes les partitions  
✅ **setup (hd0)** : Installer stage1 dans MBR du disque  
✅ **/boot/grub/menu.lst** : Fichier de configuration du menu  
✅ **title** : Nom de l'entrée de menu  
✅ **kernel** : Charger noyau Linux avec paramètres  
✅ **initrd** : Charger initial ramdisk  
✅ **makeactive** : Placer flag active sur partition (Windows)  
✅ **chainload +1** : Charger bootloader Windows  
✅ **boot** : Démarrer le système chargé  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Installation GRUB Standard

```bash
# Installation classique sur /dev/sda
sudo grub-install /dev/sda

# Vérification
ls -l /boot/grub/
# Doit contrer stage1, stage2, menu.lst, etc.

# Redémarrer pour tester
reboot
```

---

### Scénario 2 : Réparation GRUB depuis Live CD

```bash
# 1. Booter sur Live CD/USB

# 2. Identifier la partition système
sudo fdisk -l
# /dev/sda1 contient le système

# 3. Monter la partition
sudo mount /dev/sda1 /mnt

# 4. Installer GRUB
sudo grub-install --boot-directory=/mnt/boot /dev/sda

# 5. Redémarrer
sudo reboot
```

---

### Scénario 3 : Installation depuis GRUB Shell

```bash
# 1. Booter sur disque GRUB, appuyer sur C

# 2. Chercher la partition de boot
grub> find /boot/grub/stage1
(hd0,0)

# 3. Définir comme root
grub> root (hd0,0)
Filesystem type is ext2fs

# 4. Installer dans MBR
grub> setup (hd0)
Running "install"... succeeded

# 5. Redémarrer
grub> reboot
```

---

### Scénario 4 : Créer une Entrée Linux dans menu.lst

```bash
# Éditer le fichier
sudo vim /boot/grub/menu.lst

# Ajouter l'entrée
title Ubuntu 20.04 LTS
root (hd0,0)
kernel /vmlinuz-5.4.0-42-generic root=/dev/sda1 ro quiet splash
initrd /initrd.img-5.4.0-42-generic

# Sauvegarder et redémarrer
sudo reboot
```

---

### Scénario 5 : Ajouter Windows en Dual-Boot

```bash
# Situation : 
# - Linux sur /dev/sda1 (hd0,0)
# - Windows sur /dev/sda2 (hd0,1)

# Éditer menu.lst
sudo vim /boot/grub/menu.lst

# Ajouter après l'entrée Linux
title Windows 10
root (hd0,1)
makeactive
chainload +1
boot

# Sauvegarder et tester
sudo reboot
```

**Résultat au boot** :
```
  Ubuntu 20.04 LTS
  Windows 10
```

---

### Scénario 6 : Mode Recovery avec Entrée Dédiée

```bash
# menu.lst

# Entrée normale
title Ubuntu 20.04
root (hd0,0)
kernel /vmlinuz root=/dev/sda1 ro quiet splash
initrd /initrd.img

# Entrée recovery
title Ubuntu Recovery Mode
root (hd0,0)
kernel /vmlinuz root=/dev/sda1 ro single
#                                    └──── Mode single user
initrd /initrd.img
```

**Mode single** :
- Démarre en mode maintenance
- Utilisateur root automatique
- Services minimaux
- Idéal pour réparation

---

### Scénario 7 : Tester une Entrée depuis GRUB Shell

```bash
# Au menu GRUB, appuyer sur C

# Tester manuellement une configuration
grub> root (hd0,0)
grub> kernel /vmlinuz root=/dev/sda1
grub> initrd /initrd.img
grub> boot

# Si ça fonctionne, ajouter dans menu.lst
# Si ça échoue, ajuster les paramètres
```

---

## 🔄 GRUB Legacy vs Linux : Conversion

| Partition Linux | GRUB Legacy | Explication |
|-----------------|-------------|-------------|
| `/dev/sda1` | `(hd0,0)` | 1er disque, 1ère partition |
| `/dev/sda2` | `(hd0,1)` | 1er disque, 2ème partition |
| `/dev/sda5` | `(hd0,4)` | 1er disque, 5ème partition (1ère logique) |
| `/dev/sdb1` | `(hd1,0)` | 2ème disque, 1ère partition |
| `/dev/sdc3` | `(hd2,2)` | 3ème disque, 3ème partition |

**Formule** :
- Disque : lettre → numéro (a=0, b=1, c=2...)
- Partition : numéro - 1 (1→0, 2→1, 3→2...)

---

## 🆚 GRUB Legacy vs GRUB 2

| Caractéristique | GRUB Legacy | GRUB 2 |
|-----------------|-------------|--------|
| **Version** | 0.9x | 1.9x+ |
| **Config** | `/boot/grub/menu.lst` | `/boot/grub/grub.cfg` |
| **Convention disque** | `(hd0,0)` | `(hd0,1)` ⚠️ Partitions depuis 1 ! |
| **Génération config** | Manuel | `grub-mkconfig` (automatique) |
| **Modules** | Limités | Nombreux (cryptage, LVM, RAID...) |
| **Syntaxe** | Simple | Plus complexe (scripting) |
| **Statut** | ⚠️ Legacy | ✅ Standard actuel |

**💡 Différence majeure** : GRUB 2 compte les partitions depuis **1** (pas 0) !

---

## ⚠️ Erreurs Courantes

### Erreur 1 : "Error 15: File not found"

**Cause** : Mauvais chemin vers kernel ou initrd

**Solution** :
```bash
# Vérifier le chemin exact
grub> find /vmlinuz
# Utiliser le chemin retourné
```

---

### Erreur 2 : "Error 17: Cannot mount selected partition"

**Cause** : Partition GRUB `root` incorrecte

**Solution** :
```bash
# Trouver la bonne partition
grub> find /boot/grub/stage1
(hd0,0)

# Utiliser cette partition
grub> root (hd0,0)
```

---

### Erreur 3 : Windows ne boot pas (chainload échoue)

**Cause** : Partition pas marquée "active"

**Solution** :
```bash
# Ajouter makeactive dans menu.lst
title Windows
root (hd0,1)
makeactive    ← Crucial !
chainload +1
boot
```

---

**🎯 Prochaine étape** : Manage Shared Libraries

---

## 🆕 GRUB 2 - Le Bootloader Moderne

### Différences Majeures avec GRUB Legacy

**GRUB 2** = Version moderne (1.9x+), standard actuel

**Changements principaux** :

| Aspect | GRUB Legacy | GRUB 2 |
|--------|-------------|--------|
| **Config éditable** | `/boot/grub/menu.lst` | `/etc/default/grub` |
| **Config générée** | `/boot/grub/menu.lst` | `/boot/grub2/grub.cfg` |
| **Convention partition** | `(hd0,0)` = 1ère partition | `(hd0,1)` = 1ère partition ⚠️ |
| **Génération config** | Manuel | **Automatique** via script |
| **Commande install** | `grub-install` | `grub2-install` |
| **Modules** | Limités | Avancés (LVM, RAID, crypto...) |

**💡 Point critique** : GRUB 2 compte les partitions depuis **1** (pas 0 comme Legacy) !

---

## 🚑 Réparation GRUB 2 depuis Mode Rescue

### Contexte : CentOS 8 / RHEL 8 Rescue Mode

**Situation** : GRUB cassé, impossible de booter

**Solution** : Booter sur le mode rescue depuis le média d'installation

---

### Étape 1 : Localiser l'Installation Linux

```bash
# Le système rescue monte automatiquement dans /mnt/sysroot
# Vérifier que le système est bien monté
find /mnt/sysroot
```

**Résultat typique** :
```
/mnt/sysroot
/mnt/sysroot/boot
/mnt/sysroot/etc
/mnt/sysroot/home
...
```

**💡 Ce qui se passe** : Le mode rescue détecte et monte votre installation Linux

---

### Étape 2 : Chroot dans le Système

**Qu'est-ce que chroot ?**

**chroot** = **Ch**ange **root** directory

**Analogie** 🏠 : Déménager temporairement dans votre ancien appartement pour le réparer

```bash
chroot /mnt/sysroot
```

**Effet** :
- Votre shell "pense" maintenant qu'il est dans le vrai système
- `/` pointe vers `/mnt/sysroot`
- Vous pouvez exécuter les commandes normalement

**Vérification** :
```bash
# Avant chroot
ls /
# → Contenu du rescue system

# Après chroot
chroot /mnt/sysroot
ls /
# → Contenu de VOTRE système (boot, etc, home...)
```

---

### Étape 3 : Identifier le Type de Boot

**Question** : Votre système utilise BIOS ou UEFI ?

```bash
# Vérifier
ls /sys/firmware/efi
# Si existe → UEFI
# Si n'existe pas → BIOS (MBR)
```

**Alternative** :
```bash
# Voir les partitions
lsblk

# Chercher une partition EFI (type EFI System Partition)
# Typiquement 100-500 MB, montée sur /boot/efi
```

---

## 🔧 Générer le Fichier de Configuration

### grub2-mkconfig - La Commande Magique

**Rôle** : Générer automatiquement le fichier `grub.cfg`

**Pourquoi ?** GRUB 2 utilise des scripts pour créer la config (pas manuel comme GRUB Legacy)

---

### Pour BIOS/MBR

```bash
grub2-mkconfig -o /boot/grub2/grub.cfg
```

**Décryptage** :
```
grub2-mkconfig -o /boot/grub2/grub.cfg
      │         │         │
      │         │         └─── Fichier de sortie
      │         └───────────── Option -o (output)
      └─────────────────────── Générer config GRUB2
```

**Chemin config** : `/boot/grub2/grub.cfg`

---

### Pour UEFI/EFI

```bash
grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg
```

**Chemin config** : `/boot/efi/EFI/centos/grub.cfg`

**💡 Différence** : Fichier dans la partition EFI (FAT32)

**Variantes selon distribution** :
```bash
# CentOS/RHEL
grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg

# Ubuntu
grub-mkconfig -o /boot/efi/EFI/ubuntu/grub.cfg

# Fedora
grub2-mkconfig -o /boot/efi/EFI/fedora/grub.cfg
```

---

### Ce que fait grub2-mkconfig

```
1. Lit /etc/default/grub (configuration utilisateur)
2. Exécute les scripts dans /etc/grub.d/
   ├── 00_header
   ├── 10_linux (détecte les kernels)
   ├── 20_linux_xen
   ├── 30_os-prober (détecte Windows, autres OS)
   └── 40_custom
3. Génère grub.cfg automatiquement
```

**💡 Point clé** : Vous ne modifiez **JAMAIS** `grub.cfg` directement !

---

## 🔧 Installer GRUB 2

### Identifier le Disque de Boot

```bash
# Lister les disques et partitions
lsblk
```

**Résultat typique** :
```
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0  500G  0 disk
├─sda1   8:1    0    1G  0 part /boot
├─sda2   8:2    0  100G  0 part /
└─sda3   8:3    0  399G  0 part /home
```

**Question** : Sur quel disque installer GRUB ?  
**Réponse** : `/dev/sda` (le disque entier, pas une partition)

---

### Installation BIOS (MBR)

```bash
grub2-install /dev/sda
```

**Ce qui se passe** :
1. Écrit **stage1** dans le MBR de `/dev/sda`
2. Installe les modules GRUB dans `/boot/grub2/`
3. Configure les pointeurs vers `grub.cfg`

**Résultat** :
```
Installation finished. No error reported.
```

**💡 Point clé** : Comme GRUB Legacy, on pointe sur le **DISQUE** (`/dev/sda`), pas la partition

---

### Installation UEFI (EFI)

**Différence majeure** : Pas de `grub2-install` pour UEFI !

**Solution** : Réinstaller les paquets EFI

```bash
dnf reinstall grub2-efi grub2-efi-modules shim
```

**Décryptage** :
- **grub2-efi** : GRUB2 pour systèmes UEFI
- **grub2-efi-modules** : Modules GRUB2 pour UEFI
- **shim** : Bootloader intermédiaire pour Secure Boot

**Pourquoi `dnf reinstall` ?**
- Réécrit les fichiers EFI dans `/boot/efi/EFI/centos/`
- Reconfigure les entrées EFI dans la NVRAM
- Répare les fichiers corrompus

**Alternative (Debian/Ubuntu)** :
```bash
apt-get install --reinstall grub-efi-amd64 shim-signed
```

---

### Vérification Post-Installation

```bash
# Pour BIOS
ls -l /boot/grub2/

# Pour UEFI
ls -l /boot/efi/EFI/centos/

# Vérifier les entrées EFI (UEFI seulement)
efibootmgr
```

---

## ⚙️ Configuration GRUB 2

### Le Fichier Maître : /etc/default/grub

**Rôle** : Configuration **éditable** par l'utilisateur

**Emplacement** : `/etc/default/grub`

**💡 Point crucial** : C'est le **SEUL** fichier que vous devez modifier manuellement

---

### Structure du Fichier

```bash
# Ouvrir l'éditeur
vim /etc/default/grub
```

**Contenu typique** :
```bash
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="crashkernel=auto resume=/dev/mapper/cl-swap rd.lvm.lv=cl/root rd.lvm.lv=cl/swap rhgb quiet"
GRUB_DISABLE_RECOVERY="true"
```

---

### Directives Importantes

#### GRUB_TIMEOUT

```bash
GRUB_TIMEOUT=5
```

**Signification** : Temps d'affichage du menu GRUB (en secondes)

**Valeurs possibles** :
```bash
GRUB_TIMEOUT=0     # Pas de menu, boot immédiat
GRUB_TIMEOUT=5     # Menu affiché 5 secondes
GRUB_TIMEOUT=-1    # Menu affiché indéfiniment (attendre choix utilisateur)
```

**💡 Cas d'usage** :
- Serveur → `0` (boot rapide)
- Dual-boot → `10` (laisser temps de choisir)
- Développement → `-1` (toujours choisir)

---

#### GRUB_CMDLINE_LINUX

```bash
GRUB_CMDLINE_LINUX="crashkernel=auto resume=/dev/mapper/cl-swap rd.lvm.lv=cl/root rhgb quiet"
```

**Signification** : **Arguments passés au kernel Linux** à chaque démarrage

**Décryptage** :
```
GRUB_CMDLINE_LINUX="crashkernel=auto resume=/dev/mapper/cl-swap rhgb quiet"
                       │                │                        │    │
                       │                │                        │    └─── Mode silencieux
                       │                │                        └──────── Red Hat Graphical Boot
                       │                └─────────────────────────────── Partition pour hibernation
                       └──────────────────────────────────────────────── Kernel crash dump
```

**Paramètres courants** :

| Paramètre | Description | Exemple |
|-----------|-------------|---------|
| `quiet` | Mode silencieux (peu de messages) | `quiet` |
| `rhgb` | Red Hat Graphical Boot (splash screen) | `rhgb` |
| `splash` | Écran de démarrage graphique (Ubuntu) | `splash` |
| `resume=` | Partition pour hibernation | `resume=/dev/sda2` |
| `root=` | Partition root (si pas détecté) | `root=/dev/sda1` |
| `ro` | Monter root en read-only au boot | `ro` |
| `nomodeset` | Désactiver mode graphique (dépannage) | `nomodeset` |
| `single` | Mode single user (maintenance) | `single` |
| `systemd.unit=` | Target systemd au boot | `systemd.unit=multi-user.target` |

---

#### GRUB_DEFAULT

```bash
GRUB_DEFAULT=0
# ou
GRUB_DEFAULT=saved
```

**Signification** : Entrée de menu par défaut

**Valeurs** :
```bash
GRUB_DEFAULT=0         # Première entrée (compte depuis 0)
GRUB_DEFAULT=1         # Deuxième entrée
GRUB_DEFAULT=saved     # Dernière entrée utilisée (mémoire)
GRUB_DEFAULT="Ubuntu"  # Entrée nommée "Ubuntu"
```

**💡 `saved`** : Pratique pour dual-boot (retient votre choix)

---

#### GRUB_DISABLE_RECOVERY

```bash
GRUB_DISABLE_RECOVERY="true"
```

**Effet** : Cache les entrées "recovery mode" du menu

**Valeurs** :
- `"true"` → Pas d'entrées recovery
- `"false"` → Afficher les entrées recovery

---

#### Autres Directives Utiles

```bash
# Terminal utilisé (console ou gfxterm)
GRUB_TERMINAL_OUTPUT="console"

# Résolution graphique
GRUB_GFXMODE=1024x768

# Image de fond
GRUB_BACKGROUND="/boot/grub2/themes/system/background.png"

# Désactiver détection autres OS
GRUB_DISABLE_OS_PROBER="true"
```

---

### Exemple : Personnaliser le Boot

```bash
# Éditer la config
sudo vim /etc/default/grub

# Modifier
GRUB_TIMEOUT=10                    # 10 secondes pour choisir
GRUB_CMDLINE_LINUX="quiet"         # Retirer rhgb, garder quiet
GRUB_DEFAULT=saved                 # Mémoriser le dernier choix
GRUB_DISABLE_RECOVERY="false"      # Afficher recovery mode

# Sauvegarder et quitter
:wq
```

---

## 🔄 Appliquer les Modifications

### Le Workflow Complet

```
1. Modifier /etc/default/grub
2. Régénérer grub.cfg avec grub2-mkconfig
3. Redémarrer pour tester
```

**⚠️ CRUCIAL** : Les modifications dans `/etc/default/grub` ne sont **PAS** actives tant que vous n'avez pas régénéré `grub.cfg` !

---

### Régénération BIOS/MBR

```bash
# 1. Modifier la config
sudo vim /etc/default/grub

# 2. Régénérer grub.cfg
sudo grub2-mkconfig -o /boot/grub2/grub.cfg

# 3. Vérifier la génération
echo $?
# 0 = succès

# 4. Redémarrer
sudo reboot
```

---

### Régénération UEFI/EFI

```bash
# 1. Modifier la config
sudo vim /etc/default/grub

# 2. Régénérer grub.cfg (chemin UEFI différent)
sudo grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg

# 3. Redémarrer
sudo reboot
```

---

### Raccourci (selon distribution)

**Sur certaines distributions** :

```bash
# Debian/Ubuntu
sudo update-grub
# Équivalent de : grub-mkconfig -o /boot/grub/grub.cfg

# Fedora/RHEL (avec script)
sudo grub2-mkconfig -o $(readlink -f /etc/grub2.cfg)
# Suit automatiquement le lien symbolique
```

---

## 📋 Antisèche - Commandes GRUB 2

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `grub2-mkconfig -o /boot/grub2/grub.cfg` | Générer config (BIOS) | `grub2-mkconfig -o /boot/grub2/grub.cfg` |
| `grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg` | Générer config (UEFI) | `grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg` |
| `grub2-install /dev/sda` | Installer GRUB (BIOS) | `grub2-install /dev/sda` |
| `dnf reinstall grub2-efi shim` | Réparer GRUB (UEFI) | `dnf reinstall grub2-efi grub2-efi-modules shim` |
| `vim /etc/default/grub` | Éditer config GRUB | `vim /etc/default/grub` |
| `chroot /mnt/sysroot` | Entrer dans système monté | `chroot /mnt/sysroot` |
| `lsblk` | Voir disques et partitions | `lsblk` |
| `efibootmgr` | Gérer entrées EFI | `efibootmgr` |

---

## 🎓 Points Clés pour l'Examen

✅ **GRUB 2** : Version moderne (1.9x+), standard actuel  
✅ **Config éditable** : `/etc/default/grub` (JAMAIS modifier grub.cfg)  
✅ **Config générée** : `/boot/grub2/grub.cfg` (BIOS) ou `/boot/efi/EFI/centos/grub.cfg` (UEFI)  
✅ **grub2-mkconfig** : Génère automatiquement grub.cfg  
✅ **BIOS install** : `grub2-install /dev/sda` (sur le disque)  
✅ **UEFI install** : `dnf reinstall grub2-efi grub2-efi-modules shim`  
✅ **chroot** : Change root directory (pour réparation)  
✅ **GRUB_TIMEOUT** : Temps d'affichage menu (secondes)  
✅ **GRUB_CMDLINE_LINUX** : Arguments passés au kernel  
✅ **Workflow** : Modifier `/etc/default/grub` → `grub2-mkconfig` → reboot  
✅ **Convention partition** : GRUB 2 compte depuis **1** (pas 0 !)  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Réparation GRUB 2 Complète (BIOS)

```bash
# 1. Booter sur média d'installation en mode rescue

# 2. Vérifier que le système est monté
find /mnt/sysroot
# /mnt/sysroot existe et contient boot/, etc/, home/...

# 3. Chroot dans le système
chroot /mnt/sysroot

# 4. Identifier le disque de boot
lsblk
# /dev/sda avec partitions

# 5. Générer la config
grub2-mkconfig -o /boot/grub2/grub.cfg

# 6. Installer GRUB
grub2-install /dev/sda

# 7. Sortir du chroot
exit

# 8. Redémarrer
reboot
```

---

### Scénario 2 : Réparation GRUB 2 Complète (UEFI)

```bash
# 1. Booter en mode rescue

# 2. Chroot
chroot /mnt/sysroot

# 3. Vérifier que c'est bien UEFI
ls /sys/firmware/efi
# Existe → UEFI confirmé

# 4. Générer la config
grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg

# 5. Réinstaller les paquets EFI
dnf reinstall grub2-efi grub2-efi-modules shim

# 6. Vérifier les entrées EFI
efibootmgr
# Doit lister CentOS

# 7. Sortir et redémarrer
exit
reboot
```

---

### Scénario 3 : Augmenter le Timeout GRUB

```bash
# 1. Éditer la config
sudo vim /etc/default/grub

# 2. Modifier
GRUB_TIMEOUT=10
# (passer de 5 à 10 secondes)

# 3. Sauvegarder
:wq

# 4. Régénérer (BIOS)
sudo grub2-mkconfig -o /boot/grub2/grub.cfg

# 5. Redémarrer pour tester
sudo reboot
```

---

### Scénario 4 : Retirer "quiet" pour Voir Messages de Boot

```bash
# 1. Éditer
sudo vim /etc/default/grub

# 2. Modifier
# Avant
GRUB_CMDLINE_LINUX="crashkernel=auto resume=/dev/mapper/cl-swap rhgb quiet"

# Après (retirer quiet et rhgb)
GRUB_CMDLINE_LINUX="crashkernel=auto resume=/dev/mapper/cl-swap"

# 3. Régénérer
sudo grub2-mkconfig -o /boot/grub2/grub.cfg

# 4. Reboot
sudo reboot

# Résultat : Messages détaillés au boot (debugging)
```

---

### Scénario 5 : Ajouter Paramètre Kernel Permanent

```bash
# Besoin : Désactiver mode graphique (nomodeset)

# 1. Éditer
sudo vim /etc/default/grub

# 2. Ajouter à GRUB_CMDLINE_LINUX
GRUB_CMDLINE_LINUX="... nomodeset"

# 3. Régénérer
sudo grub2-mkconfig -o /boot/grub2/grub.cfg

# 4. Reboot
sudo reboot

# Le kernel démarre maintenant toujours avec nomodeset
```

---

### Scénario 6 : Mémoriser le Dernier OS Booté (Dual-Boot)

```bash
# Utile si dual-boot Linux/Windows

# 1. Éditer
sudo vim /etc/default/grub

# 2. Modifier
GRUB_DEFAULT=saved
GRUB_SAVEDEFAULT=true

# 3. Régénérer
sudo grub2-mkconfig -o /boot/grub2/grub.cfg

# 4. Reboot
sudo reboot

# Résultat : GRUB mémorise votre dernier choix
# Si vous bootez Windows, la prochaine fois Windows sera sélectionné par défaut
```

---

### Scénario 7 : Boot Immédiat Sans Menu (Serveur)

```bash
# Pour serveur production : pas besoin du menu

# 1. Éditer
sudo vim /etc/default/grub

# 2. Modifier
GRUB_TIMEOUT=0
GRUB_HIDDEN_TIMEOUT=0

# 3. Régénérer
sudo grub2-mkconfig -o /boot/grub2/grub.cfg

# 4. Reboot
sudo reboot

# Résultat : Boot direct sans afficher le menu
# (Peut quand même forcer menu en appuyant sur ESC au boot)
```

---

## 🗂️ Structure des Répertoires GRUB 2

### BIOS/MBR

```
/boot/
├── grub2/
│   ├── grub.cfg              ← Config générée (NE PAS MODIFIER)
│   ├── grubenv               ← Variables d'environnement
│   ├── i386-pc/              ← Modules GRUB pour BIOS
│   │   ├── boot.mod
│   │   ├── linux.mod
│   │   └── ...
│   └── fonts/                ← Polices
│       └── unicode.pf2
└── vmlinuz-5.x.x             ← Kernels Linux

/etc/
├── default/
│   └── grub                  ← Config ÉDITABLE
└── grub.d/                   ← Scripts de génération
    ├── 00_header
    ├── 10_linux
    ├── 30_os-prober
    └── 40_custom
```

---

### UEFI/EFI

```
/boot/
├── efi/
│   └── EFI/
│       └── centos/
│           ├── grub.cfg      ← Config générée
│           ├── grubx64.efi   ← Bootloader EFI
│           ├── shimx64.efi   ← Secure Boot shim
│           └── fonts/
└── vmlinuz-5.x.x

/etc/
├── default/
│   └── grub                  ← Config ÉDITABLE (même fichier BIOS/UEFI)
└── grub.d/                   ← Scripts (idem)
```

---

## ⚠️ Erreurs Courantes GRUB 2

### Erreur 1 : "grub rescue>" après boot

**Cause** : grub.cfg non trouvé ou corrompu

**Solution** :
```bash
# Au prompt grub rescue>
grub rescue> ls
# Liste les partitions disponibles

grub rescue> set prefix=(hd0,1)/boot/grub2
grub rescue> set root=(hd0,1)
grub rescue> insmod normal
grub rescue> normal

# Puis réparer depuis le système
```

---

### Erreur 2 : Modifications non appliquées

**Cause** : Oublié de régénérer grub.cfg

**Solution** :
```bash
# Toujours après modification de /etc/default/grub
sudo grub2-mkconfig -o /boot/grub2/grub.cfg
```

---

### Erreur 3 : grub2-install échoue (UEFI)

**Cause** : On essaie grub2-install sur système UEFI

**Solution** : Utiliser `dnf reinstall` à la place
```bash
# NE PAS faire
grub2-install /dev/sda  # ❌ Échoue sur UEFI

# FAIRE
dnf reinstall grub2-efi grub2-efi-modules shim  # ✅ Correct
```

---

## 🔄 GRUB Legacy vs GRUB 2 : Tableau Complet

| Aspect | GRUB Legacy | GRUB 2 |
|--------|-------------|--------|
| **Version** | 0.9x | 1.9x / 2.x |
| **Config user** | `/boot/grub/menu.lst` (manuel) | `/etc/default/grub` (puis auto-généré) |
| **Config boot** | `/boot/grub/menu.lst` | `/boot/grub2/grub.cfg` |
| **Édition config** | ✅ Direct | ❌ Jamais (via grub2-mkconfig) |
| **Partition (hd0,X)** | 0 = 1ère partition | 1 = 1ère partition |
| **Install BIOS** | `grub-install /dev/sda` | `grub2-install /dev/sda` |
| **Install UEFI** | N/A (pas de support) | `dnf reinstall grub2-efi shim` |
| **Génération** | Manuel | Automatique (scripts) |
| **Modules** | Basiques | Avancés (LVM, RAID, crypto, etc.) |
| **Scripting** | Limité | Complet (boucles, conditions) |
| **Themes** | Basiques | Avancés (graphiques) |
| **Secure Boot** | ❌ Non supporté | ✅ Supporté (via shim) |
| **Détection OS** | Manuel | Automatique (os-prober) |

---

**🎯 Prochaine étape** : Manage Shared Libraries

*Dernière mise à jour: 22 janvier 2026*
