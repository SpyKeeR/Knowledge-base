# 🔌 Montage et Démontage des Systèmes de Fichiers - fstab, autofs, systemd

## 📋 Informations du Module

- **Module** : 4 - Devices, Linux Filesystems, Filesystem Hierarchy Standard
- **Cours** : Control Mounting and Unmounting of Filesystems (Part 1 & 2)
- **Objectif** : Maîtriser le montage manuel, automatique au boot (fstab), à la demande (autofs), et via systemd (mount/automount units)

---

## 🎯 Concepts Fondamentaux

### 📂 Le Montage de Systèmes de Fichiers

**Analogie** : Imaginez votre système Linux comme une bibliothèque géante :
- **Le montage** = Ouvrir une nouvelle section de rayonnages et l'intégrer dans le catalogue général
- **Point de montage** = L'emplacement du rayon dans la bibliothèque (ex: `/mnt/usb`)
- **Démontage** = Fermer la section et la retirer du catalogue (les livres sont toujours là, mais inaccessibles)

**Principe du montage** :
```
Avant montage :
/
├── home/
├── var/
└── mnt/
    └── disk/           ← Répertoire vide

Après montage de /dev/sdb1 sur /mnt/disk :
/
├── home/
├── var/
└── mnt/
    └── disk/           ← Contient maintenant les fichiers de /dev/sdb1
        ├── documents/
        ├── photos/
        └── videos/
```

### 🔄 Méthodes de Montage

| Méthode | Quand | Persistance | Automatisation | Usage typique |
|---------|-------|-------------|----------------|---------------|
| **mount** (manuel) | À la demande | Non (perte au reboot) | Aucune | Tests, montages temporaires |
| **/etc/fstab** | Au démarrage | Oui | Boot systemd | Disques système permanents |
| **autofs** | À l'accès | Oui | Démon autofs | Partages réseau, montage paresseux |
| **systemd .mount** | Contrôle systemd | Oui | systemd | Intégration systemd avancée |
| **systemd .automount** | À l'accès (systemd) | Oui | systemd | Alternative moderne à autofs |

---

## 📝 /etc/fstab - Montages Automatiques au Boot

### Structure du fichier /etc/fstab

**Format** : 6 colonnes séparées par espaces/tabs

```
<device>  <mount_point>  <type>  <options>  <dump>  <fsck>
```

**Exemple complet** :
```bash
# <device>                            <mount point>  <type>  <options>           <dump>  <fsck>
UUID=a1b2c3d4-e5f6-7890-abcd-ef123456  /              ext4    defaults            1       1
UUID=12345678-90ab-cdef-1234-567890ab  /home          ext4    defaults,noatime    0       2
/dev/sdb1                              /mnt/data      xfs     defaults            0       2
//192.168.1.100/share                  /mnt/network   cifs    credentials=/root/.smbcreds,_netdev  0  0
tmpfs                                  /tmp           tmpfs   defaults,size=2G    0       0
/dev/cdrom                             /media/cdrom   auto    noauto,ro,user      0       0
```

### Colonne 1️⃣ : Device (Périphérique)

**Formats d'identification** :

| Format | Exemple | Avantages | Inconvénients |
|--------|---------|-----------|---------------|
| **Chemin /dev** | `/dev/sdb1` | Simple, direct | ❌ Change si déplacement du disque |
| **UUID** | `UUID=a1b2c3d4-...` | ✅ Unique, stable | Plus verbeux |
| **LABEL** | `LABEL=MyDisk` | ✅ Lisible | Peut être dupliqué |
| **PARTUUID** | `PARTUUID=12345...` | ✅ UUID de partition GPT | GPT uniquement |
| **Chemin by-uuid** | `/dev/disk/by-uuid/a1b2c3d4-...` | ✅ Stable | Long |
| **Chemin by-label** | `/dev/disk/by-label/MyDisk` | ✅ Lisible | Peut être dupliqué |

**⭐ Recommandation** : Utiliser **UUID** pour la stabilité en cas de déplacement de disques sur d'autres ports SATA/USB.

**Obtenir l'UUID d'un périphérique** :
```bash
# Méthode 1 : blkid (recommandé)
blkid /dev/sdb1
# /dev/sdb1: UUID="a1b2c3d4-e5f6-7890-abcd-ef123456" TYPE="ext4" PARTUUID="..."

# Méthode 2 : lsblk
lsblk -f /dev/sdb1
# NAME  FSTYPE LABEL UUID                                 MOUNTPOINT
# sdb1  ext4   Data  a1b2c3d4-e5f6-7890-abcd-ef123456

# Méthode 3 : ls -l /dev/disk/by-uuid/
ls -l /dev/disk/by-uuid/
# lrwxrwxrwx 1 root root 10 Feb  3 10:00 a1b2c3d4-... -> ../../sdb1
```

### Colonne 2️⃣ : Mount Point (Point de Montage)

**Règles** :
- Doit être un répertoire existant (créé avec `mkdir -p`)
- Recommandation : `/mnt/` pour montages temporaires, `/media/` pour périphériques amovibles
- Caractères spéciaux : `\040` = espace, `\011` = tab

**Exemples** :
```bash
/home                          # Répertoire simple
/mnt/external                  # Disque externe
/media/USB\040Drive            # Nom avec espace : "USB Drive"
none                           # Pour swap (pas de point de montage)
```

### Colonne 3️⃣ : Type (Système de Fichiers)

**Types courants** :

| Type | Description | Utilisation |
|------|-------------|-------------|
| `ext4` | Système de fichiers Linux standard | Partitions Linux |
| `xfs` | Haute performance | Gros volumes, serveurs |
| `btrfs` | Fonctionnalités avancées | Snapshots, compression |
| `vfat` | FAT32 | Clés USB, compatibilité Windows |
| `ntfs` | NTFS Windows | Disques Windows (via ntfs-3g) |
| `nfs` / `nfs4` | Network File System | Partages NFS |
| `cifs` | SMB/SAMBA | Partages Windows |
| `auto` | Détection automatique | Périphériques amovibles |
| `tmpfs` | Système de fichiers en RAM | /tmp, /run |
| `swap` | Espace d'échange | Partition swap |
| `iso9660` | CD/DVD | Montage d'ISO |

### Colonne 4️⃣ : Options (Mount Options)

**Options générales** :

| Option | Description | Usage |
|--------|-------------|-------|
| `defaults` | Équivalent à : rw, suid, dev, exec, auto, nouser, async | **Par défaut** |
| `rw` / `ro` | Lecture-écriture / Lecture seule | Sécurité, protection |
| `auto` / `noauto` | Monter au boot / Ne pas monter | `noauto` pour CD/DVD |
| `exec` / `noexec` | Autoriser/interdire exécution | Sécurité `/tmp` |
| `suid` / `nosuid` | Autoriser/interdire bit SUID | Sécurité |
| `dev` / `nodev` | Autoriser/interdire périphériques | Sécurité |
| `user` | Utilisateurs peuvent monter | Clés USB utilisateurs |
| `nouser` | Seul root peut monter | **Par défaut** |
| `users` | Utilisateurs peuvent monter/démonter | Environnements multi-utilisateurs |
| `owner` | Propriétaire du device peut monter | Contrôle d'accès |
| `group` | Membres du groupe peuvent monter | Gestion par groupe |
| `sync` / `async` | Synchrone / Asynchrone | `sync` pour USB (sécurité) |
| `atime` / `noatime` | Mettre à jour access time / Non | `noatime` = performance |
| `nodiratime` | Pas d'atime pour répertoires | Performance |
| `relatime` | Atime relatif (compromis) | Bon compromis |
| `_netdev` | Attend réseau avant montage | Partages réseau |
| `nofail` | Pas d'erreur si montage échoue | Disques optionnels |

**Options spécifiques** :

```bash
# NTFS
uid=1000,gid=1000,dmask=022,fmask=133

# CIFS/SMB
credentials=/root/.smbcreds,vers=3.0,uid=1000,gid=1000

# NFS
vers=4.2,rsize=32768,wsize=32768,hard,intr

# tmpfs
size=2G,mode=1777

# BTRFS
compress=zstd:3,subvol=home,space_cache=v2
```

### Colonne 5️⃣ : Dump (Sauvegarde)

**Valeurs** : `0` ou `1`

| Valeur | Signification |
|--------|---------------|
| `0` | Ne **PAS** sauvegarder avec `dump` |
| `1` | **SAUVEGARDER** avec `dump` |

**Contexte historique** : La commande `dump` était utilisée pour sauvegardes incrémentales de systèmes de fichiers ext2/ext3. Aujourd'hui **obsolète** et rarement utilisée. La plupart des systèmes modernes utilisent `rsync`, `tar`, `borg`, ou solutions de backup dédiées.

**Recommandation moderne** : Mettre `0` partout (sauf raison spécifique de compatibilité avec ancien système de backup utilisant `dump`).

### Colonne 6️⃣ : fsck (Vérification au Boot)

**Valeurs** : `0`, `1`, ou `2`

| Valeur | Signification | Utilisation |
|--------|---------------|-------------|
| `0` | **Pas de vérification** fsck | Partages réseau, tmpfs, swap |
| `1` | **Vérification prioritaire** (en premier) | Partition racine `/` uniquement |
| `2` | **Vérification non-prioritaire** (après `/`) | Autres partitions locales |

**Ordre d'exécution** :
```
Boot
 └─> fsck sur partitions avec pass=1 (racine /)
      └─> fsck en parallèle sur partitions avec pass=2 (/home, /var, etc.)
           └─> Montage de toutes les partitions
```

**⚠️ Important** : Le champ `fsck` ne détermine **PAS** si le filesystem sera vérifié à chaque boot. La fréquence des vérifications est configurée au niveau du système de fichiers lui-même :

```bash
# ext4 : Configuration avec tune2fs
tune2fs -l /dev/sdb1 | grep -i "mount count\|check interval"
# Maximum mount count:      30           ← Vérif tous les 30 montages
# Check interval:           15552000     ← Vérif tous les 180 jours

# Désactiver vérifications périodiques ext4
tune2fs -c 0 -i 0 /dev/sdb1

# XFS : Pas de vérification périodique automatique (xfs_repair manuel)
```

### 🔄 Recharger /etc/fstab sans Reboot

**Après modification de /etc/fstab** :

```bash
# Option 1 : Recharger configuration systemd (recommandé)
systemctl daemon-reload

# Option 2 : Monter tous les FS listés dans fstab
mount -a

# Option 3 : Remonter un FS spécifique avec nouvelles options
mount -o remount /home

# Option 4 : Tester fstab avant reboot (évite kernel panic)
findmnt --verify
# ou
mount -a --fake --verbose
```

**⚠️ Test avant reboot** (évite système non-bootable) :
```bash
# Vérifier syntaxe fstab
findmnt --verify

# Sortie si OK :
# Success, no errors or warnings detected

# Sortie si erreur :
# /etc/fstab:5: parse error: expected fstype
```

### 📌 Exemples /etc/fstab Complets

**Poste de travail Linux classique** :
```bash
# Partition racine (priorité fsck)
UUID=a1b2c3d4-e5f6-7890-abcd-ef123456  /       ext4    defaults            1  1

# Partition home (performance optimisée)
UUID=b2c3d4e5-f6a7-8901-bcde-f0123456  /home   ext4    defaults,noatime    0  2

# Partition swap
UUID=c3d4e5f6-a7b8-9012-cdef-01234567  none    swap    sw                  0  0

# Disque de données XFS
UUID=d4e5f6a7-b8c9-0123-def0-12345678  /data   xfs     defaults,noatime    0  2

# Partition NTFS Windows (lecture/écriture pour utilisateur)
UUID=E4F6A8B2C4D6E8F0                  /mnt/windows  ntfs-3g  uid=1000,gid=1000,dmask=022,fmask=133  0  0

# tmpfs pour /tmp (2 Go en RAM)
tmpfs  /tmp  tmpfs  defaults,size=2G,mode=1777  0  0
```

**Serveur avec partages réseau** :
```bash
# Partition racine
UUID=a1b2c3d4-e5f6-7890-abcd-ef123456  /       ext4    defaults            1  1

# Partage NFS (attend réseau)
192.168.1.50:/export/data  /mnt/nfs  nfs4  defaults,_netdev,nofail  0  0

# Partage CIFS/SMB Windows
//192.168.1.100/share  /mnt/samba  cifs  credentials=/root/.smbcreds,_netdev,nofail,uid=1000,gid=1000  0  0

# Disque optionnel (pas d'erreur si absent)
UUID=f8a9b0c1-d2e3-f4a5-b6c7-d8e9f0a1b2c3  /mnt/external  ext4  defaults,nofail  0  2
```

**Fichier credentials CIFS** (`/root/.smbcreds`) :
```bash
username=myuser
password=mypassword
domain=WORKGROUP
```
```bash
chmod 600 /root/.smbcreds  # Sécuriser le fichier
```

---

## 🤖 autofs - Montage Automatique à la Demande

### 📖 Concept autofs

**Analogie** : autofs est comme un bibliothécaire intelligent :
- Les rayonnages (partages) ne sont ouverts **que quand quelqu'un veut emprunter un livre**
- Après un délai d'inactivité (timeout), le rayon se ferme automatiquement pour économiser l'espace
- Pas besoin de maintenir tous les rayonnages ouverts en permanence

**Avantages autofs** :
- ✅ **Économie de ressources** : Montages uniquement si nécessaire
- ✅ **Transparent** : L'utilisateur accède au répertoire comme s'il était toujours monté
- ✅ **Démontage automatique** : Libération des ressources après inactivité
- ✅ **Idéal pour partages réseau** : Évite blocages au boot si réseau indisponible
- ✅ **Gestion centralisée** : Configuration via fichiers de configuration

**Différence avec fstab** :
```
┌────────────────────────────────────────────────────────┐
│ /etc/fstab                                             │
├────────────────────────────────────────────────────────┤
│ Boot système                                           │
│   └─> Montage immédiat de /mnt/nfs                   │
│        └─> FS monté en permanence (même si non utilisé)│
│             └─> Ressources consommées                 │
└────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────┐
│ autofs                                                 │
├────────────────────────────────────────────────────────┤
│ Boot système                                           │
│   └─> autofs surveille /mnt/nfs                       │
│        └─> Utilisateur fait: cd /mnt/nfs              │
│             └─> autofs monte à la volée              │
│                  └─> Après 5 min inactivité : démontage│
└────────────────────────────────────────────────────────┘
```

### 📦 Installation autofs

```bash
# Debian/Ubuntu
apt update
apt install autofs

# RHEL/CentOS/Fedora
dnf install autofs

# Vérifier installation
systemctl status autofs

# Activer au démarrage
systemctl enable autofs
```

### 🔧 Configuration autofs

**Architecture des fichiers** :

```
/etc/auto.master         ← Configuration principale (points de montage parents)
    ├─> /etc/auto.misc   ← Montages divers (inclus par défaut)
    ├─> /etc/auto.net    ← Montages réseau automatiques
    └─> /etc/auto.shares ← Fichier personnalisé (à créer)
```

### 📄 Fichier Principal : /etc/auto.master

**Syntaxe** :
```
<point_montage_parent>  <fichier_config>  [options]
```

**Exemple /etc/auto.master** :
```bash
# Point de montage parent : /shares
# Configuration : /etc/auto.shares
# Timeout : 400 secondes (inactivité avant démontage)
/shares  /etc/auto.shares  --timeout=400

# Point de montage parent : /mnt/network
# Configuration : /etc/auto.network
# Timeout par défaut : 300 secondes
/mnt/network  /etc/auto.network

# Montage direct (point de montage absolu dans le fichier de config)
# Utiliser "/-" comme point parent
/-  /etc/auto.direct  --timeout=600
```

**Options auto.master courantes** :

| Option | Description | Exemple |
|--------|-------------|---------|
| `--timeout=<secondes>` | Délai inactivité avant démontage | `--timeout=600` (10 min) |
| `--ghost` | Créer répertoires vides pour visibilité | Affiche les montages possibles avec `ls` |
| `--browse` | Permettre navigation (deprecated, utiliser --ghost) | Ancien système |

### 📄 Fichiers de Configuration des Montages

**Syntaxe générale** :
```
<point_montage_relatif>  [options]  <emplacement_ressource>
```

#### Exemple 1 : /etc/auto.shares (montages relatifs)

```bash
# share1 sera accessible via /shares/share1
# fstype=auto : détection automatique du type
# Ressource : NFS depuis 127.0.0.1:/etc
share1  -fstype=auto  127.0.0.1:/etc

# share2 avec options multiples
# ro : lecture seule, noexec : pas d'exécution
share2  -fstype=nfs4,ro,noexec  192.168.1.50:/export/data

# Montage local d'un disque bloc
# /shares/backup pointera vers /dev/vdb2
backup  -fstype=ext4  :/dev/vdb2

# Partage CIFS/SMB Windows
windows_share  -fstype=cifs,credentials=/root/.smbcreds,uid=1000  ://192.168.1.100/Public

# Montage avec substitution de variables
# $USER sera remplacé par le nom d'utilisateur
userhome  -fstype=nfs4  server.example.com:/home/&
# Si user "john" accède /shares/userhome, monte server:/home/john
```

**Chemin final** : `/shares/share1`, `/shares/share2`, `/shares/backup`, etc.

#### Exemple 2 : /etc/auto.direct (montages directs)

**Quand utiliser "/-" dans auto.master** :
- Besoin de chemins absolus spécifiques
- Intégration dans arborescence système existante
- Montages dans `/opt`, `/usr/local`, etc.

**/etc/auto.master** :
```bash
/-  /etc/auto.direct  --timeout=600
```

**/etc/auto.direct** :
```bash
# Chemins ABSOLUS (pas relatifs)
/opt/app/data  -fstype=nfs4  nfs-server:/export/app_data
/mnt/backup    -fstype=ext4  :/dev/sdc1
/media/cdrom   -fstype=iso9660,ro  :/dev/sr0
```

### 🔄 Syntaxe Détaillée des Options autofs

**Format complet** :
```
<mount_point>  -<option1>,<option2>,<option3>  <location>
```

**Composants** :

| Composant | Description | Exemples |
|-----------|-------------|----------|
| **mount_point** | Nom du sous-répertoire (relatif) ou chemin absolu (avec `/-`) | `share1`, `/opt/data` |
| **-fstype=** | Type de système de fichiers | `auto`, `nfs4`, `ext4`, `cifs`, `xfs` |
| **Options mount** | Séparées par virgules (comme mount -o) | `ro`, `noexec`, `nosuid`, `rw` |
| **location** | Chemin vers la ressource | `server:/path`, `:/dev/sdb1`, `://server/share` |

**Formats de location** :

| Type | Format | Exemple |
|------|--------|---------|
| **NFS** | `serveur:/chemin` | `192.168.1.50:/export/data` |
| **CIFS/SMB** | `://serveur/partage` | `://192.168.1.100/Public` |
| **Local (bloc)** | `:/dev/device` | `:/dev/sdb1` |
| **Local (ISO)** | `:/chemin/fichier.iso` | `:/opt/images/ubuntu.iso` |

**Substitution de variables** :

| Variable | Signification | Exemple |
|----------|---------------|---------|
| `&` | Valeur du mount_point | `home/&` → si key=john, monte home/john |
| `$USER` | Nom de l'utilisateur | `server:/home/$USER` |
| `$UID` | UID de l'utilisateur | Moins courant |

### 🔄 Rechargement Configuration autofs

```bash
# Méthode 1 : Reload (recommandé, pas d'interruption)
systemctl reload autofs

# Méthode 2 : Restart (interruption temporaire)
systemctl restart autofs

# Méthode 3 : Recharger avec ancien init.d
service autofs reload

# Vérifier statut
systemctl status autofs

# Voir les montages autofs actifs
mount | grep autofs
# /etc/auto.shares on /shares type autofs (rw,relatime,fd=...)
```

### 🧪 Test et Débogage autofs

```bash
# Activer logs détaillés dans /etc/default/autofs (Debian/Ubuntu)
echo 'OPTIONS="--verbose"' >> /etc/default/autofs
systemctl restart autofs

# Ou /etc/sysconfig/autofs (RHEL/CentOS)
echo 'OPTIONS="--verbose"' >> /etc/sysconfig/autofs
systemctl restart autofs

# Suivre les logs en temps réel
journalctl -u autofs -f
# ou
tail -f /var/log/syslog | grep automount

# Lister les montages configurés (avec --ghost)
ls -la /shares/
# dr-xr-xr-x  2 root root 0 Feb  3 10:00 share1
# dr-xr-xr-x  2 root root 0 Feb  3 10:00 share2

# Tester accès (déclenche montage)
cd /shares/share1
ls

# Vérifier que c'est monté
mount | grep share1
# 192.168.1.50:/export on /shares/share1 type nfs4 (rw,relatime,...)

# Attendre timeout, puis vérifier démontage
# (après 400s d'inactivité avec --timeout=400)
sleep 420
mount | grep share1
# (aucune sortie = démonté)
```

---

## 🔧 systemd - Montage via Units (.mount et .automount)

### 📖 Concept systemd Mount Units

**Philosophie systemd** : Tout est une "unit" (service, socket, timer, mount, etc.). Les montages peuvent être gérés comme n'importe quel autre service systemd.

**Avantages systemd mount** :
- ✅ **Intégration native** : Contrôle avec `systemctl` (start, stop, status, enable)
- ✅ **Dépendances** : Gestion d'ordre de démarrage (After=, Requires=, etc.)
- ✅ **Parallélisation** : systemd peut monter en parallèle
- ✅ **Automount** : Alternative moderne à autofs
- ✅ **Logs centralisés** : `journalctl -u mnt-data.mount`

**Inconvénient** :
- ❌ **Nommage strict** : Le nom du fichier .mount doit correspondre au chemin (slashes → tirets)

### 📝 systemd .mount Units

**Règle de nommage** :
```
Point de montage : /mnt/external
Nom du fichier   : mnt-external.mount

Point de montage : /mnt/my-data/backup
Nom du fichier   : mnt-my\x2ddata-backup.mount
                      (x2d = encodage du tiret)
```

**Générateur de nom** :
```bash
# Convertir chemin → nom de fichier .mount
systemd-escape -p --suffix=mount /mnt/external
# mnt-external.mount

systemd-escape -p --suffix=mount /mnt/my-data/backup
# mnt-my\x2ddata-backup.mount
```

### 📄 Structure d'un fichier .mount

**Emplacement** : `/etc/systemd/system/<nom>.mount`

**Sections** :

```ini
[Unit]
Description=Description du montage
After=network.target
Requires=network-online.target

[Mount]
What=/dev/disk/by-uuid/UUID-DU-DISQUE
Where=/mnt/external
Type=ext4
Options=defaults,noatime

[Install]
WantedBy=multi-user.target
```

### 🔍 Détails des Sections

#### [Unit] - Métadonnées et Dépendances

| Directive | Description | Exemple |
|-----------|-------------|---------|
| `Description=` | Description lisible | `Description=External USB Disk` |
| `After=` | Démarrer après ces units | `After=network.target` |
| `Before=` | Démarrer avant ces units | `Before=nginx.service` |
| `Requires=` | Nécessite ces units (hard dependency) | `Requires=network-online.target` |
| `Wants=` | Souhaite ces units (soft dependency) | `Wants=network.target` |
| `BindsTo=` | Lié au cycle de vie d'une autre unit | `BindsTo=dev-sdb1.device` |
| `RequiresMountsFor=` | Nécessite montage d'un chemin | `RequiresMountsFor=/mnt/parent` |

#### [Mount] - Configuration du Montage

| Directive | Description | Exemple |
|-----------|-------------|---------|
| `What=` | **Périphérique/Ressource à monter** | `What=/dev/disk/by-uuid/a1b2c3d4-...` |
| `Where=` | **Point de montage** (doit correspondre au nom du fichier) | `Where=/mnt/external` |
| `Type=` | **Type de système de fichiers** | `Type=ext4`, `Type=nfs4`, `Type=cifs` |
| `Options=` | **Options de montage** (comme mount -o) | `Options=defaults,ro,noexec` |
| `SloppyOptions=` | Ignorer options non reconnues | `SloppyOptions=yes` |
| `DirectoryMode=` | Permissions du point de montage | `DirectoryMode=0755` |
| `TimeoutSec=` | Timeout pour le montage | `TimeoutSec=30s` |

**⚠️ Important** : `Where=` **DOIT** correspondre au nom du fichier .mount
```
Fichier : /etc/systemd/system/mnt-external.mount
Where=  : /mnt/external  ✅ CORRECT

Fichier : /etc/systemd/system/mnt-external.mount
Where=  : /mnt/data      ❌ ERREUR systemd
```

#### [Install] - Activation

| Directive | Description | Exemple |
|-----------|-------------|---------|
| `WantedBy=` | Activé par cette target | `WantedBy=multi-user.target` |
| `RequiredBy=` | Requis par cette target | `RequiredBy=local-fs.target` |

**Targets courants** :
- `multi-user.target` : Mode multi-utilisateurs (serveur sans GUI)
- `graphical.target` : Mode graphique (desktop)
- `local-fs.target` : Systèmes de fichiers locaux
- `remote-fs.target` : Systèmes de fichiers distants (NFS, CIFS)

### 📄 Exemples de .mount Units

#### Exemple 1 : Disque externe local (ext4)

**/etc/systemd/system/mnt-external.mount** :
```ini
[Unit]
Description=External USB Data Disk
# Monter après que les périphériques locaux soient détectés
After=local-fs-pre.target

[Mount]
What=/dev/disk/by-uuid/5df5a6f4-54df-54fd-a8b3-c9d0e1f2a3b4
Where=/mnt/external
Type=ext4
Options=defaults,noatime,nodiratime

[Install]
WantedBy=multi-user.target
```

#### Exemple 2 : Partition NTFS Windows (lecture seule)

**/etc/systemd/system/mnt-windows.mount** :
```ini
[Unit]
Description=Windows C: Drive (Read-Only)

[Mount]
What=/dev/disk/by-uuid/E4F6A8B2C4D6E8F0
Where=/mnt/windows
Type=ntfs-3g
Options=ro,uid=1000,gid=1000,dmask=022,fmask=133

[Install]
WantedBy=multi-user.target
```

#### Exemple 3 : Partage NFS distant

**/etc/systemd/system/mnt-nfs.mount** :
```ini
[Unit]
Description=NFS Share from Production Server
# Attendre que le réseau soit fonctionnel
After=network-online.target
Wants=network-online.target

[Mount]
What=192.168.1.50:/export/data
Where=/mnt/nfs
Type=nfs4
Options=defaults,vers=4.2,rsize=32768,wsize=32768,hard,intr

[Install]
WantedBy=remote-fs.target
```

#### Exemple 4 : Partage CIFS/SMB Windows

**/etc/systemd/system/mnt-samba.mount** :
```ini
[Unit]
Description=Windows File Share (CIFS/SMB)
After=network-online.target
Wants=network-online.target

[Mount]
What=//192.168.1.100/Public
Where=/mnt/samba
Type=cifs
Options=credentials=/root/.smbcreds,vers=3.0,uid=1000,gid=1000,iocharset=utf8

[Install]
WantedBy=remote-fs.target
```

**/root/.smbcreds** :
```
username=myuser
password=mypassword
domain=WORKGROUP
```

#### Exemple 5 : tmpfs en RAM

**/etc/systemd/system/mnt-ramdisk.mount** :
```ini
[Unit]
Description=Temporary RAM Disk
DefaultDependencies=no
Conflicts=umount.target
Before=local-fs.target umount.target

[Mount]
What=tmpfs
Where=/mnt/ramdisk
Type=tmpfs
Options=size=1G,mode=1777

[Install]
WantedBy=local-fs.target
```

### 🔄 Gestion des .mount Units

```bash
# Créer le fichier .mount
nano /etc/systemd/system/mnt-external.mount

# Recharger la configuration systemd (OBLIGATOIRE)
systemctl daemon-reload

# Démarrer le montage immédiatement
systemctl start mnt-external.mount

# Vérifier le statut
systemctl status mnt-external.mount
● mnt-external.mount - External USB Data Disk
     Loaded: loaded (/etc/systemd/system/mnt-external.mount; enabled; ...)
     Active: active (mounted) since Mon 2026-02-03 10:00:00 CET; 5min ago
      Where: /mnt/external
       What: /dev/sdb1
      Tasks: 0 (limit: 4915)
     Memory: 64.0K
        CPU: 10ms
     CGroup: /system.slice/mnt-external.mount

# Activer au démarrage (persistant)
systemctl enable mnt-external.mount
# Created symlink /etc/systemd/system/multi-user.target.wants/mnt-external.mount → ...

# Démonter
systemctl stop mnt-external.mount

# Désactiver au démarrage
systemctl disable mnt-external.mount

# Voir les logs
journalctl -u mnt-external.mount

# Lister tous les .mount
systemctl list-units --type=mount

# Voir les units .mount en échec
systemctl --failed --type=mount
```

### 🤖 systemd .automount Units - Montage à la Demande

**Concept** : Alternative moderne à **autofs**, intégrée nativement dans systemd.

**Principe** :
```
1. Au boot : systemd crée un point de surveillance sur /mnt/external
2. Utilisateur accède à /mnt/external
3. systemd détecte l'accès → monte automatiquement
4. Après inactivité : démonte automatiquement
```

**Avantages vs autofs** :
- ✅ Intégration systemd native (pas de démon supplémentaire)
- ✅ Même syntaxe que .mount
- ✅ Gestion avec systemctl
- ❌ Moins flexible qu'autofs pour wildcards complexes

### 📄 Structure d'un fichier .automount

**Emplacement** : `/etc/systemd/system/<nom>.automount`

**Règle** : Même nom que le .mount correspondant !

```
mnt-external.mount      ← Définition du montage
mnt-external.automount  ← Active le montage automatique
```

**Structure** :
```ini
[Unit]
Description=Automount for External Disk

[Automount]
Where=/mnt/external
TimeoutIdleSec=300

[Install]
WantedBy=multi-user.target
```

### 🔍 Détails Section [Automount]

| Directive | Description | Exemple |
|-----------|-------------|---------|
| `Where=` | **Point de montage** (identique au .mount) | `Where=/mnt/external` |
| `TimeoutIdleSec=` | Délai inactivité avant démontage | `TimeoutIdleSec=600` (10 min) |
| `DirectoryMode=` | Permissions du point de montage | `DirectoryMode=0755` |

### 📄 Exemple Complet .mount + .automount

#### Fichier 1 : /etc/systemd/system/mnt-external.mount

```ini
[Unit]
Description=External Data Disk

[Mount]
What=/dev/disk/by-uuid/5df5a6f4-54df-54fd-a8b3-c9d0e1f2a3b4
Where=/mnt/external
Type=ext4
Options=defaults,noatime

[Install]
WantedBy=multi-user.target
```

#### Fichier 2 : /etc/systemd/system/mnt-external.automount

```ini
[Unit]
Description=Automount External Data Disk
# L'automount doit être avant le mount
Before=mnt-external.mount

[Automount]
Where=/mnt/external
TimeoutIdleSec=300

[Install]
WantedBy=multi-user.target
```

### 🔄 Gestion des .automount Units

```bash
# Créer les deux fichiers .mount et .automount
nano /etc/systemd/system/mnt-external.mount
nano /etc/systemd/system/mnt-external.automount

# Recharger systemd
systemctl daemon-reload

# Activer et démarrer l'automount (PAS le .mount)
systemctl enable mnt-external.automount
systemctl start mnt-external.automount

# Vérifier le statut
systemctl status mnt-external.automount
● mnt-external.automount - Automount External Data Disk
     Loaded: loaded (/etc/systemd/system/mnt-external.automount; enabled; ...)
     Active: active (waiting) since Mon 2026-02-03 10:00:00 CET; 1min ago
    Triggers: ● mnt-external.mount
      Where: /mnt/external

# Le point de montage existe mais n'est pas encore monté
ls -ld /mnt/external
drwxr-xr-x 2 root root 0 Feb  3 10:00 /mnt/external

mount | grep external
# (rien = pas encore monté)

# Accéder déclenche le montage automatique
cd /mnt/external
ls

# Vérifier que c'est monté
systemctl status mnt-external.mount
● mnt-external.mount - External Data Disk
     Active: active (mounted) since Mon 2026-02-03 10:05:00 CET; 10s ago

mount | grep external
/dev/sdb1 on /mnt/external type ext4 (rw,noatime,...)

# Après 300s d'inactivité, démontage automatique
# Vérifier avec:
watch -n 10 'systemctl status mnt-external.mount'
```

### 🆚 Comparaison .mount vs .automount

| Aspect | .mount | .automount |
|--------|--------|------------|
| **Montage** | Immédiat (au démarrage si enabled) | À la demande (premier accès) |
| **Démontage** | Manuel (`systemctl stop`) | Automatique après timeout |
| **Ressources** | Consommées en permanence | Économisées jusqu'à utilisation |
| **Logs** | `journalctl -u X.mount` | `journalctl -u X.automount` |
| **Usage** | FS système, données permanentes | Partages réseau, disques optionnels |
| **Commande enable** | `systemctl enable X.mount` | `systemctl enable X.automount` |

**⚠️ Attention** : Si vous utilisez `.automount`, **ne pas activer** le `.mount` !
```bash
# ✅ CORRECT
systemctl enable mnt-external.automount
systemctl start mnt-external.automount

# ❌ INCORRECT (conflit)
systemctl enable mnt-external.mount
systemctl enable mnt-external.automount
```

---

## 🎓 10 Scénarios Pratiques Complets

### Scénario 1️⃣ : Monter un disque externe de manière permanente avec fstab

**Contexte** : Disque USB externe ext4 utilisé pour backups, doit être monté automatiquement au boot.

```bash
# 1. Identifier le disque
lsblk -f
# NAME   FSTYPE LABEL    UUID                                 MOUNTPOINT
# sdb
# └─sdb1 ext4   BackupHD a1b2c3d4-e5f6-7890-abcd-ef123456

# 2. Obtenir l'UUID
blkid /dev/sdb1
# /dev/sdb1: UUID="a1b2c3d4-e5f6-7890-abcd-ef123456" TYPE="ext4"

# 3. Créer le point de montage
mkdir -p /mnt/backup

# 4. Tester le montage manuel
mount UUID=a1b2c3d4-e5f6-7890-abcd-ef123456 /mnt/backup
ls /mnt/backup
# Vérifier que les données sont accessibles

# 5. Démonter pour tester fstab
umount /mnt/backup

# 6. Ajouter à /etc/fstab
echo "UUID=a1b2c3d4-e5f6-7890-abcd-ef123456  /mnt/backup  ext4  defaults,noatime,nofail  0  2" >> /etc/fstab

# 7. Vérifier la syntaxe fstab
findmnt --verify

# 8. Tester sans reboot
mount -a

# 9. Vérifier
df -h /mnt/backup
# /dev/sdb1       500G  100G  400G  20% /mnt/backup

# 10. Recharger systemd pour synchroniser
systemctl daemon-reload

# 11. Reboot de test
reboot
# Après reboot, vérifier :
df -h /mnt/backup
```

**Explication des options** :
- `defaults` : rw, suid, dev, exec, auto, nouser, async
- `noatime` : Pas de mise à jour de l'access time (performance)
- `nofail` : Le boot continue même si le disque est absent
- `0` : Pas de backup avec dump
- `2` : Vérification fsck non-prioritaire

---

### Scénario 2️⃣ : Configurer autofs pour montage NFS à la demande

**Contexte** : Serveur de fichiers NFS accessible à la demande pour économiser les ressources réseau.

```bash
# 1. Installer autofs
apt update
apt install autofs

# 2. Créer configuration principale
nano /etc/auto.master

# Ajouter :
/mnt/nfs  /etc/auto.nfs  --timeout=600 --ghost

# 3. Créer fichier de configuration des partages NFS
nano /etc/auto.nfs

# Ajouter :
# <nom>    <options>                          <serveur:chemin>
data      -fstype=nfs4,ro,vers=4.2            192.168.1.50:/export/data
projects  -fstype=nfs4,rw,vers=4.2            192.168.1.50:/export/projects
archive   -fstype=nfs4,ro,vers=4.2,noexec     192.168.1.50:/export/archive

# 4. Vérifier les fichiers de configuration
cat /etc/auto.master | grep -v "^#"
cat /etc/auto.nfs

# 5. Activer et démarrer autofs
systemctl enable autofs
systemctl start autofs

# 6. Vérifier le statut
systemctl status autofs

# 7. Lister les points de montage (grâce à --ghost)
ls -la /mnt/nfs/
# dr-xr-xr-x 2 root root 0 Feb  3 10:00 archive
# dr-xr-xr-x 2 root root 0 Feb  3 10:00 data
# dr-xr-xr-x 2 root root 0 Feb  3 10:00 projects

# 8. Vérifier qu'aucun n'est monté
mount | grep /mnt/nfs
# (aucune sortie)

# 9. Accéder à un partage (déclenche montage automatique)
cd /mnt/nfs/data
ls

# 10. Vérifier que c'est monté
mount | grep /mnt/nfs/data
# 192.168.1.50:/export/data on /mnt/nfs/data type nfs4 (ro,relatime,...)

# 11. Suivre les logs autofs
journalctl -u autofs -f

# 12. Après 600s d'inactivité, vérifier démontage
sleep 620
mount | grep /mnt/nfs/data
# (aucune sortie = démonté automatiquement)

# 13. En cas de modification, recharger
nano /etc/auto.nfs
# ... modifications ...
systemctl reload autofs
```

**Explication des options NFS** :
- `vers=4.2` : Version NFS 4.2
- `ro` : Lecture seule
- `rw` : Lecture-écriture
- `noexec` : Interdire l'exécution de binaires

---

### Scénario 3️⃣ : Créer systemd .mount pour disque de données

**Contexte** : Disque XFS pour stockage de données, géré par systemd pour intégration avancée.

```bash
# 1. Identifier le disque
lsblk -f | grep xfs
# sdc1  xfs  DataDisk  d4e5f6a7-b8c9-0123-def0-12345678

# 2. Générer le nom du fichier .mount
systemd-escape -p --suffix=mount /mnt/data
# mnt-data.mount

# 3. Créer le point de montage
mkdir -p /mnt/data

# 4. Créer le fichier .mount
cat > /etc/systemd/system/mnt-data.mount <<'EOF'
[Unit]
Description=XFS Data Storage Disk
After=local-fs-pre.target
Before=local-fs.target

[Mount]
What=/dev/disk/by-uuid/d4e5f6a7-b8c9-0123-def0-12345678
Where=/mnt/data
Type=xfs
Options=defaults,noatime,nodiratime,inode64

[Install]
WantedBy=multi-user.target
EOF

# 5. Recharger systemd
systemctl daemon-reload

# 6. Vérifier la syntaxe du fichier
systemd-analyze verify mnt-data.mount

# 7. Démarrer le montage
systemctl start mnt-data.mount

# 8. Vérifier le statut
systemctl status mnt-data.mount
● mnt-data.mount - XFS Data Storage Disk
     Loaded: loaded (/etc/systemd/system/mnt-data.mount; disabled; ...)
     Active: active (mounted) since Mon 2026-02-03 10:00:00 CET; 5s ago
      Where: /mnt/data
       What: /dev/sdc1

# 9. Vérifier le montage
df -h /mnt/data
mount | grep /mnt/data

# 10. Activer au démarrage
systemctl enable mnt-data.mount
# Created symlink /etc/systemd/system/multi-user.target.wants/mnt-data.mount

# 11. Voir les logs
journalctl -u mnt-data.mount

# 12. Test : Démonter et remonter
systemctl stop mnt-data.mount
systemctl start mnt-data.mount

# 13. Reboot de test
reboot
# Après reboot :
systemctl status mnt-data.mount  # Doit être active (mounted)
```

**Options XFS** :
- `inode64` : Utiliser inodes 64-bit (performance sur gros volumes)

---

### Scénario 4️⃣ : Configurer systemd .automount pour partage CIFS Windows

**Contexte** : Partage SMB Windows accessible à la demande pour économiser les connexions réseau.

```bash
# 1. Créer fichier credentials
cat > /root/.smbcreds <<'EOF'
username=windowsuser
password=SecureP@ss123
domain=WORKGROUP
EOF
chmod 600 /root/.smbcreds

# 2. Tester montage manuel
mkdir -p /mnt/windows
mount -t cifs //192.168.1.100/Public /mnt/windows -o credentials=/root/.smbcreds,vers=3.0
ls /mnt/windows
umount /mnt/windows

# 3. Générer nom du fichier
systemd-escape -p --suffix=mount /mnt/windows
# mnt-windows.mount

# 4. Créer fichier .mount
cat > /etc/systemd/system/mnt-windows.mount <<'EOF'
[Unit]
Description=Windows File Share via CIFS
After=network-online.target
Wants=network-online.target

[Mount]
What=//192.168.1.100/Public
Where=/mnt/windows
Type=cifs
Options=credentials=/root/.smbcreds,vers=3.0,uid=1000,gid=1000,iocharset=utf8,file_mode=0644,dir_mode=0755

[Install]
WantedBy=remote-fs.target
EOF

# 5. Créer fichier .automount
cat > /etc/systemd/system/mnt-windows.automount <<'EOF'
[Unit]
Description=Automount Windows Share
Before=mnt-windows.mount

[Automount]
Where=/mnt/windows
TimeoutIdleSec=300

[Install]
WantedBy=multi-user.target
EOF

# 6. Recharger systemd
systemctl daemon-reload

# 7. Activer et démarrer l'automount (PAS le .mount)
systemctl enable mnt-windows.automount
systemctl start mnt-windows.automount

# 8. Vérifier statut automount
systemctl status mnt-windows.automount
● mnt-windows.automount - Automount Windows Share
     Active: active (waiting) since Mon 2026-02-03 10:00:00 CET; 10s ago
    Triggers: ● mnt-windows.mount
      Where: /mnt/windows

# 9. Vérifier que rien n'est monté
mount | grep windows
# (aucune sortie)

# 10. Accéder (déclenche montage)
ls /mnt/windows

# 11. Vérifier montage actif
systemctl status mnt-windows.mount
● mnt-windows.mount - Windows File Share via CIFS
     Active: active (mounted) since Mon 2026-02-03 10:05:00 CET; 5s ago

mount | grep windows
//192.168.1.100/Public on /mnt/windows type cifs (rw,relatime,...)

# 12. Suivre démontage automatique après 300s
watch -n 10 'systemctl status mnt-windows.mount | grep Active'

# 13. Logs
journalctl -u mnt-windows.mount
journalctl -u mnt-windows.automount
```

**Options CIFS importantes** :
- `vers=3.0` : Version SMB 3.0 (sécurisé)
- `uid=1000,gid=1000` : Propriétaire des fichiers côté Linux
- `file_mode=0644` : Permissions fichiers (rw-r--r--)
- `dir_mode=0755` : Permissions répertoires (rwxr-xr-x)
- `iocharset=utf8` : Encodage UTF-8 pour caractères spéciaux

---

### Scénario 5️⃣ : Montage tmpfs pour performances I/O élevées

**Contexte** : Application nécessitant I/O très rapides pour fichiers temporaires.

```bash
# 1. Vérifier RAM disponible
free -h
#               total        used        free      shared  buff/cache   available
# Mem:           16Gi       2.0Gi        10Gi       200Mi       4.0Gi        14Gi

# 2. Créer point de montage
mkdir -p /mnt/ramdisk

# 3. Tester montage manuel
mount -t tmpfs -o size=2G,mode=1777 tmpfs /mnt/ramdisk
df -h /mnt/ramdisk
# tmpfs           2.0G     0  2.0G   0% /mnt/ramdisk

# 4. Test performance
dd if=/dev/zero of=/mnt/ramdisk/testfile bs=1M count=1024
# 1024+0 records in
# 1024+0 records out
# 1073741824 bytes (1.1 GB) copied, 0.5 s, 2.1 GB/s  ← Très rapide !

rm /mnt/ramdisk/testfile
umount /mnt/ramdisk

# 5. Ajouter à fstab (méthode traditionnelle)
echo "tmpfs  /mnt/ramdisk  tmpfs  defaults,size=2G,mode=1777,nodev,nosuid,noexec  0  0" >> /etc/fstab

# 6. Ou créer systemd .mount (méthode moderne)
cat > /etc/systemd/system/mnt-ramdisk.mount <<'EOF'
[Unit]
Description=2GB RAM Disk for Temporary High-Speed Storage
DefaultDependencies=no
Conflicts=umount.target
Before=local-fs.target umount.target

[Mount]
What=tmpfs
Where=/mnt/ramdisk
Type=tmpfs
Options=size=2G,mode=1777,nodev,nosuid,noexec

[Install]
WantedBy=local-fs.target
EOF

# 7. Recharger et activer
systemctl daemon-reload
systemctl enable mnt-ramdisk.mount
systemctl start mnt-ramdisk.mount

# 8. Vérifier
df -h /mnt/ramdisk
systemctl status mnt-ramdisk.mount

# 9. Benchmark comparatif
time dd if=/dev/zero of=/tmp/testfile bs=1M count=1024
# → Disque SSD : ~2 secondes
time dd if=/dev/zero of=/mnt/ramdisk/testfile bs=1M count=1024
# → RAM : ~0.5 secondes (4x plus rapide)
```

**Options tmpfs** :
- `size=2G` : Taille maximale (2 Go)
- `mode=1777` : rwxrwxrwxt (sticky bit pour /tmp)
- `nodev` : Pas de périphériques
- `nosuid` : Pas de SUID
- `noexec` : Pas d'exécution (sécurité)

**⚠️ Attention** : Données perdues au reboot !

---

### Scénario 6️⃣ : Partage home utilisateur avec autofs et substitution de variables

**Contexte** : Environnement multi-utilisateurs avec home directories sur serveur NFS, montage automatique par utilisateur.

```bash
# 1. Configuration serveur NFS (sur 192.168.1.50)
# /etc/exports :
# /export/home  192.168.1.0/24(rw,sync,no_subtree_check,no_root_squash)

# 2. Créer structure sur client
mkdir -p /home/network

# 3. Installer autofs
apt install autofs

# 4. Configurer auto.master
cat >> /etc/auto.master <<'EOF'
/home/network  /etc/auto.home  --timeout=600
EOF

# 5. Créer auto.home avec substitution
cat > /etc/auto.home <<'EOF'
# Montage du home de chaque utilisateur
# & = nom du répertoire demandé
*  -fstype=nfs4,rw,vers=4.2,hard,intr  192.168.1.50:/export/home/&
EOF

# Explication :
# Si user "alice" accède /home/network/alice
# autofs monte 192.168.1.50:/export/home/alice

# 6. Recharger autofs
systemctl reload autofs

# 7. Test utilisateur alice
su - alice
cd /home/network/alice
ls
# → Montage automatique de 192.168.1.50:/export/home/alice

# 8. Vérifier montage
mount | grep alice
# 192.168.1.50:/export/home/alice on /home/network/alice type nfs4 (rw,...)

# 9. Test utilisateur bob
su - bob
cd /home/network/bob
# → Montage automatique de 192.168.1.50:/export/home/bob

# 10. Vérifier que les deux sont montés
mount | grep "192.168.1.50:/export/home"
# 192.168.1.50:/export/home/alice on /home/network/alice type nfs4 (rw,...)
# 192.168.1.50:/export/home/bob on /home/network/bob type nfs4 (rw,...)

# 11. Après inactivité (600s), démontage automatique
```

**Avantage** : Un seul fichier de configuration pour tous les utilisateurs grâce à la substitution `&`.

---

### Scénario 7️⃣ : Convertir fstab vers systemd .mount units

**Contexte** : Migration d'une configuration fstab vers systemd pour meilleure intégration.

```bash
# Configuration fstab initiale :
# UUID=a1b2c3d4-e5f6-7890-abcd-ef123456  /mnt/data  ext4  defaults,noatime  0  2

# 1. Identifier les entrées fstab à convertir
grep -v "^#" /etc/fstab | grep "/mnt/data"

# 2. Récupérer UUID
blkid | grep "/dev/sd"
# /dev/sdb1: UUID="a1b2c3d4-e5f6-7890-abcd-ef123456" TYPE="ext4"

# 3. Générer nom du fichier systemd
systemd-escape -p --suffix=mount /mnt/data
# mnt-data.mount

# 4. Créer fichier .mount équivalent
cat > /etc/systemd/system/mnt-data.mount <<'EOF'
[Unit]
Description=Data Partition (migrated from fstab)
After=local-fs-pre.target
Before=local-fs.target

[Mount]
What=/dev/disk/by-uuid/a1b2c3d4-e5f6-7890-abcd-ef123456
Where=/mnt/data
Type=ext4
Options=defaults,noatime

[Install]
WantedBy=multi-user.target
EOF

# 5. Recharger systemd
systemctl daemon-reload

# 6. Tester AVANT de modifier fstab
systemctl start mnt-data.mount
systemctl status mnt-data.mount

# 7. Si OK, commenter ligne fstab
sed -i '/\/mnt\/data/s/^/#/' /etc/fstab

# 8. Vérifier fstab
grep "/mnt/data" /etc/fstab
# #UUID=a1b2c3d4-e5f6-7890-abcd-ef123456  /mnt/data  ext4  defaults,noatime  0  2

# 9. Activer systemd .mount
systemctl enable mnt-data.mount

# 10. Recharger systemd après modification fstab
systemctl daemon-reload

# 11. Reboot de test
reboot

# 12. Après reboot, vérifier
systemctl status mnt-data.mount  # active (mounted)
df -h /mnt/data
```

**Avantages de la migration** :
- Meilleur contrôle avec `systemctl`
- Logs centralisés avec `journalctl`
- Gestion de dépendances avancée (After, Before, Requires)

---

### Scénario 8️⃣ : Montage conditionnel avec systemd (dépendances)

**Contexte** : Monter un disque de données SEULEMENT si le disque parent est monté.

```bash
# Hiérarchie :
# /mnt/storage      ← Disque principal
# /mnt/storage/backup  ← Disque backup (dépend de storage)

# 1. Créer .mount pour disque principal
cat > /etc/systemd/system/mnt-storage.mount <<'EOF'
[Unit]
Description=Main Storage Disk

[Mount]
What=/dev/disk/by-uuid/a1b2c3d4-e5f6-7890-abcd-ef123456
Where=/mnt/storage
Type=ext4
Options=defaults,noatime

[Install]
WantedBy=multi-user.target
EOF

# 2. Créer sous-répertoire pour montage imbriqué
mkdir -p /mnt/storage/backup

# 3. Créer .mount pour backup (avec dépendance)
cat > /etc/systemd/system/mnt-storage-backup.mount <<'EOF'
[Unit]
Description=Backup Disk (depends on main storage)
# Monter APRÈS le disque storage
After=mnt-storage.mount
# Nécessite absolument que storage soit monté
Requires=mnt-storage.mount
# Si storage est démonté, démonter backup aussi
BindsTo=mnt-storage.mount

[Mount]
What=/dev/disk/by-uuid/b2c3d4e5-f6a7-8901-bcde-f0123456
Where=/mnt/storage/backup
Type=xfs
Options=defaults,noatime

[Install]
WantedBy=multi-user.target
EOF

# 4. Recharger systemd
systemctl daemon-reload

# 5. Activer les deux
systemctl enable mnt-storage.mount
systemctl enable mnt-storage-backup.mount

# 6. Démarrer dans l'ordre (systemd gère automatiquement)
systemctl start mnt-storage-backup.mount
# → systemd démarre automatiquement mnt-storage.mount d'abord

# 7. Vérifier l'ordre
systemctl list-dependencies mnt-storage-backup.mount
# mnt-storage-backup.mount
# ● ├─mnt-storage.mount  ← Dépendance visible
# ● ├─system.slice
# ● └─sysinit.target

# 8. Test : Démonter storage (backup doit se démonter aussi)
systemctl stop mnt-storage.mount
systemctl status mnt-storage-backup.mount
# ● mnt-storage-backup.mount
#      Loaded: loaded (...)
#      Active: inactive (dead)  ← Démonté automatiquement grâce à BindsTo

# 9. Remonter
systemctl start mnt-storage.mount
systemctl start mnt-storage-backup.mount
```

**Directives de dépendances** :
- `After=` : Ordre de démarrage (démarrer après)
- `Requires=` : Dépendance forte (si échec, cette unit échoue)
- `BindsTo=` : Lié au cycle de vie (si arrêt, cette unit s'arrête aussi)

---

### Scénario 9️⃣ : Montage read-only pour sécurité avec remount read-write temporaire

**Contexte** : Disque de configuration monté en lecture seule, remontage temporaire en RW pour mises à jour.

```bash
# 1. Ajouter à fstab en read-only
echo "UUID=c3d4e5f6-a7b8-9012-cdef-01234567  /mnt/config  ext4  ro,noatime  0  2" >> /etc/fstab

# 2. Monter
mount /mnt/config

# 3. Vérifier mode read-only
mount | grep /mnt/config
# /dev/sdb1 on /mnt/config type ext4 (ro,noatime)

# 4. Tentative d'écriture (échoue)
touch /mnt/config/test.txt
# touch: cannot touch '/mnt/config/test.txt': Read-only file system

# 5. Remonter temporairement en read-write
mount -o remount,rw /mnt/config

# 6. Vérifier
mount | grep /mnt/config
# /dev/sdb1 on /mnt/config type ext4 (rw,noatime)

# 7. Maintenant écriture possible
echo "config_option=value" >> /mnt/config/settings.conf
sync  # Forcer l'écriture sur disque

# 8. Remonter en read-only
mount -o remount,ro /mnt/config

# 9. Vérifier
mount | grep /mnt/config
# /dev/sdb1 on /mnt/config type ext4 (ro,noatime)

# 10. Alternative avec systemd .mount
cat > /etc/systemd/system/mnt-config.mount <<'EOF'
[Unit]
Description=Configuration Files (Read-Only)

[Mount]
What=/dev/disk/by-uuid/c3d4e5f6-a7b8-9012-cdef-01234567
Where=/mnt/config
Type=ext4
Options=ro,noatime

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl enable mnt-config.mount
systemctl start mnt-config.mount

# 11. Remount RW avec systemd
systemctl stop mnt-config.mount
# Modifier temporairement Options= dans le fichier .mount
sed -i 's/Options=ro,noatime/Options=rw,noatime/' /etc/systemd/system/mnt-config.mount
systemctl daemon-reload
systemctl start mnt-config.mount

# 12. Revenir en RO
sed -i 's/Options=rw,noatime/Options=ro,noatime/' /etc/systemd/system/mnt-config.mount
systemctl daemon-reload
systemctl restart mnt-config.mount
```

**Usage** : Protection contre modifications accidentelles ou malveillantes.

---

### Scénario 🔟 : Debugging montage qui échoue au boot

**Contexte** : Système ne boot pas à cause d'une erreur dans fstab.

```bash
# === Phase 1 : Le problème ===

# fstab contient une erreur :
# UUID=WRONG-UUID  /mnt/data  ext4  defaults  0  2
# → UUID inexistant, système attend 90s puis passe en emergency mode

# === Phase 2 : Accès emergency shell ===

# 1. Au boot, système affiche :
# "A start job is running for /mnt/data (1min 30s / no limit)"
# Puis : "You are in emergency mode..."

# 2. Se connecter en root

# 3. Remonter / en read-write
mount -o remount,rw /

# 4. Identifier le problème dans fstab
cat /etc/fstab
nano /etc/fstab

# 5. Corriger ou commenter la ligne problématique
# Avant :
# UUID=WRONG-UUID  /mnt/data  ext4  defaults  0  2
# Après :
# #UUID=WRONG-UUID  /mnt/data  ext4  defaults  0  2

# 6. Sauvegarder et quitter

# 7. Tester avant reboot
findmnt --verify
# Success, no errors or warnings detected

# 8. Recharger systemd
systemctl daemon-reload

# 9. Tenter montage manuel
mount -a

# 10. Reboot
reboot

# === Phase 3 : Prévention ===

# 1. Utiliser option nofail pour disques optionnels
UUID=correct-uuid  /mnt/external  ext4  defaults,nofail  0  2

# 2. Tester fstab après chaque modification
findmnt --verify
mount -a --fake --verbose

# 3. Alternative : utiliser systemd .mount avec nofail
cat > /etc/systemd/system/mnt-external.mount <<'EOF'
[Unit]
Description=External Disk (Optional)
DefaultDependencies=no

[Mount]
What=/dev/disk/by-uuid/correct-uuid
Where=/mnt/external
Type=ext4
Options=defaults,nofail

[Install]
WantedBy=multi-user.target
EOF

# 4. Logs pour diagnostiquer
journalctl -xb | grep -i mount
journalctl -u mnt-external.mount
```

**Options de récupération** :
- `nofail` dans fstab : Continue boot même si montage échoue
- `x-systemd.device-timeout=10` : Timeout réduit (10s au lieu de 90s)
- Emergency mode : Accès root pour réparer

---

## 🚨 10 Erreurs Courantes et Solutions

### Erreur 1️⃣ : "mount: wrong fs type, bad option, bad superblock..."

**Symptôme** :
```bash
mount /dev/sdb1 /mnt/data
# mount: /mnt/data: wrong fs type, bad option, bad superblock on /dev/sdb1
```

**Causes possibles** :
1. Type de FS incorrect
2. Module kernel manquant
3. FS corrompu
4. Device non formaté

**Solutions** :

```bash
# 1. Vérifier le type de FS réel
blkid /dev/sdb1
# /dev/sdb1: UUID="..." TYPE="ntfs"

lsblk -f /dev/sdb1
# NAME  FSTYPE LABEL UUID  MOUNTPOINT
# sdb1  ntfs   Data  ...

# 2. Spécifier le type explicitement
mount -t ntfs-3g /dev/sdb1 /mnt/data

# 3. Vérifier que le module kernel est chargé
lsmod | grep ntfs
# (rien)

# Charger le module
modprobe fuse
apt install ntfs-3g  # Si manquant

# 4. Si FS corrompu, tenter réparation
# ext4
e2fsck -f /dev/sdb1

# XFS
xfs_repair /dev/sdb1

# NTFS
ntfsfix /dev/sdb1

# 5. Si non formaté
mkfs.ext4 /dev/sdb1  # ⚠️ Détruit les données
```

---

### Erreur 2️⃣ : "Target is busy" lors du démontage

**Symptôme** :
```bash
umount /mnt/data
# umount: /mnt/data: target is busy
```

**Cause** : Processus ou fichiers ouverts dans le FS.

**Solutions** :

```bash
# 1. Identifier les processus utilisant le FS
lsof | grep /mnt/data
# bash    1234 user  cwd   DIR  8,17  /mnt/data
# vim     1235 user  txt   REG  8,17  /mnt/data/file.txt

# 2. Ou avec fuser
fuser -m /mnt/data
# /mnt/data: 1234c 1235

# 3. Tuer les processus (avec précaution)
fuser -km /mnt/data  # -k = kill, -m = mount point

# 4. Alternative : lsof + kill manuel
lsof /mnt/data | awk 'NR>1 {print $2}' | xargs kill

# 5. Forcer démontage (dernier recours, risque de perte de données)
umount -f /mnt/data     # Force
umount -l /mnt/data     # Lazy (déconnexion immédiate, nettoyage différé)

# 6. Vérifier qu'aucun shell n'est dans le répertoire
pwd
# /mnt/data  ← Vous êtes dans le répertoire !
cd /
umount /mnt/data  # Maintenant ça fonctionne
```

---

### Erreur 3️⃣ : systemd .mount unit échoue à cause du nom

**Symptôme** :
```bash
systemctl start my-disk.mount
# Failed to start my-disk.mount: Unit my-disk.mount has a bad unit file setting
```

**Cause** : Le nom du fichier .mount ne correspond pas au point de montage.

**Solution** :

```bash
# ❌ INCORRECT
# Fichier : /etc/systemd/system/my-disk.mount
# Where=/mnt/data  ← Incohérence !

# ✅ CORRECT
# Option 1 : Renommer le fichier
mv /etc/systemd/system/my-disk.mount /etc/systemd/system/mnt-data.mount

# Option 2 : Utiliser systemd-escape pour générer le nom
systemd-escape -p --suffix=mount /mnt/data
# mnt-data.mount  ← Nom correct

# 3. Créer avec le bon nom
cat > /etc/systemd/system/mnt-data.mount <<'EOF'
[Unit]
Description=Data Disk

[Mount]
What=/dev/sdb1
Where=/mnt/data  ← Doit correspondre au nom du fichier
Type=ext4
Options=defaults

[Install]
WantedBy=multi-user.target
EOF

# 4. Pour chemins complexes avec tirets
systemd-escape -p --suffix=mount /mnt/my-data
# mnt-my\x2ddata.mount  ← x2d = encodage du tiret

# 5. Recharger et tester
systemctl daemon-reload
systemctl start mnt-data.mount
```

---

### Erreur 4️⃣ : autofs ne monte pas automatiquement

**Symptôme** :
```bash
ls /mnt/autofs/share1
# ls: cannot access '/mnt/autofs/share1': No such file or directory
```

**Causes possibles** :
1. Service autofs non démarré
2. Erreur de syntaxe dans configuration
3. Permissions insuffisantes
4. Ressource réseau inaccessible

**Solutions** :

```bash
# 1. Vérifier que autofs est actif
systemctl status autofs
# Active: active (running)

# Si inactif :
systemctl start autofs

# 2. Vérifier les logs
journalctl -u autofs -n 50

# 3. Vérifier syntaxe auto.master
cat /etc/auto.master | grep -v "^#"
# /mnt/autofs  /etc/auto.shares  --timeout=400

# 4. Vérifier syntaxe auto.shares
cat /etc/auto.shares
# share1  -fstype=nfs4  192.168.1.50:/export/data

# 5. Tester manuellement la ressource
mount -t nfs4 192.168.1.50:/export/data /mnt/test
# Si échec : problème réseau/NFS

# 6. Vérifier que le point parent existe
ls -ld /mnt/autofs
# drwxr-xr-x 2 root root 0 Feb  3 10:00 /mnt/autofs

# 7. Recharger autofs après modification
systemctl reload autofs

# 8. Activer logs verbose
echo 'OPTIONS="--verbose"' >> /etc/default/autofs
systemctl restart autofs
journalctl -u autofs -f
# Puis tester accès

# 9. Vérifier permissions NFS (côté serveur)
showmount -e 192.168.1.50
# Export list for 192.168.1.50:
# /export/data 192.168.1.0/24

# 10. Utiliser option --ghost pour debug
# /etc/auto.master :
# /mnt/autofs  /etc/auto.shares  --timeout=400 --ghost
systemctl reload autofs
ls /mnt/autofs/  # Doit afficher share1 même non monté
```

---

### Erreur 5️⃣ : "Bad option; for several filesystems (e.g. nfs, cifs) you might need..."

**Symptôme** :
```bash
mount //192.168.1.100/share /mnt/samba
# mount: /mnt/samba: bad option; for several filesystems (e.g. nfs, cifs)
# you might need a /sbin/mount.<type> helper program.
```

**Cause** : Paquet manquant pour type de FS.

**Solutions** :

```bash
# 1. Identifier le type manquant (ici CIFS)

# 2. Installer le paquet nécessaire

# Debian/Ubuntu - CIFS/SMB
apt install cifs-utils

# Debian/Ubuntu - NFS
apt install nfs-common

# Debian/Ubuntu - NTFS
apt install ntfs-3g

# RHEL/CentOS/Fedora - CIFS
dnf install cifs-utils

# RHEL/CentOS/Fedora - NFS
dnf install nfs-utils

# 3. Vérifier installation
which mount.cifs
# /sbin/mount.cifs

ls /sbin/mount.*
# /sbin/mount.cifs
# /sbin/mount.nfs
# /sbin/mount.nfs4
# /sbin/mount.ntfs-3g

# 4. Réessayer montage
mount -t cifs //192.168.1.100/share /mnt/samba -o credentials=/root/.smbcreds
```

---

### Erreur 6️⃣ : Oubli de daemon-reload après modification systemd

**Symptôme** :
```bash
# Modifier un fichier .mount
nano /etc/systemd/system/mnt-data.mount

systemctl start mnt-data.mount
# → Utilise l'ancienne configuration (pas les nouvelles modifications)
```

**Cause** : systemd cache la configuration, `daemon-reload` requis.

**Solution** :

```bash
# 1. TOUJOURS faire daemon-reload après modification
nano /etc/systemd/system/mnt-data.mount
# ... modifications ...

systemctl daemon-reload  # ← OBLIGATOIRE

systemctl restart mnt-data.mount

# 2. Vérifier que les changements sont pris en compte
systemctl cat mnt-data.mount
# Affiche le contenu actuel connu par systemd

# 3. Pour fstab aussi (pour synchroniser avec systemd)
nano /etc/fstab
# ... modifications ...

systemctl daemon-reload

# 4. Automatiser avec fonction bash
mount_reload() {
    systemctl daemon-reload
    systemctl restart "$1"
}

# Usage :
nano /etc/systemd/system/mnt-data.mount
mount_reload mnt-data.mount
```

**⚠️ Rappel** : `daemon-reload` ne redémarre PAS les services, seulement recharge les fichiers de configuration.

---

### Erreur 7️⃣ : fstab avec UUID incorrect cause boot en emergency mode

**Symptôme** :
```bash
# Au boot :
# "A start job is running for /mnt/data (1min 30s / no limit)"
# Puis : "You are in emergency mode."
```

**Cause** : UUID dans fstab ne correspond à aucun périphérique.

**Solutions** :

```bash
# === En emergency mode ===

# 1. Remonter / en read-write
mount -o remount,rw /

# 2. Vérifier les UUIDs disponibles
blkid
# /dev/sda1: UUID="a1b2c3d4-..." TYPE="ext4"
# /dev/sdb1: UUID="b2c3d4e5-..." TYPE="ext4"

# 3. Comparer avec fstab
cat /etc/fstab | grep -v "^#"
# UUID=WRONG-UUID  /mnt/data  ext4  defaults  0  2

# 4. Corriger ou commenter
nano /etc/fstab
# Option A : Corriger UUID
# UUID=b2c3d4e5-...  /mnt/data  ext4  defaults  0  2

# Option B : Commenter temporairement
# #UUID=WRONG-UUID  /mnt/data  ext4  defaults  0  2

# 5. Tester
findmnt --verify
mount -a

# 6. Reboot
systemctl reboot

# === Prévention ===

# 1. Toujours utiliser nofail pour disques optionnels
UUID=...  /mnt/external  ext4  defaults,nofail  0  2

# 2. Tester AVANT de rebooter
findmnt --verify
mount -a --fake --verbose

# 3. Utiliser blkid pour copier UUID (évite erreurs de frappe)
blkid /dev/sdb1 | grep -o 'UUID="[^"]*"'
# UUID="b2c3d4e5-f6a7-8901-bcde-f0123456"

# Copier-coller directement dans fstab
```

---

### Erreur 8️⃣ : Montage NFS bloque le boot (réseau pas prêt)

**Symptôme** :
```bash
# Boot très lent (90s d'attente)
# Messages : "A dependency job for mnt-nfs.mount failed"
```

**Cause** : Montage NFS démarre avant que le réseau soit disponible.

**Solutions** :

```bash
# === Option 1 : fstab avec _netdev et nofail ===

# Avant (problématique) :
# 192.168.1.50:/export/data  /mnt/nfs  nfs4  defaults  0  0

# Après (corrigé) :
192.168.1.50:/export/data  /mnt/nfs  nfs4  defaults,_netdev,nofail,x-systemd.device-timeout=10  0  0

# _netdev       : Attend network-online.target
# nofail        : Continue boot si échec
# x-systemd.device-timeout=10 : Timeout 10s au lieu de 90s

# Recharger
systemctl daemon-reload
mount -a

# === Option 2 : systemd .mount avec dépendances réseau ===

cat > /etc/systemd/system/mnt-nfs.mount <<'EOF'
[Unit]
Description=NFS Production Share
# Attendre réseau
After=network-online.target
Wants=network-online.target
# Timeout réduit
DefaultDependencies=no

[Mount]
What=192.168.1.50:/export/data
Where=/mnt/nfs
Type=nfs4
Options=defaults,vers=4.2,soft,timeo=10,retrans=2
TimeoutSec=15s

[Install]
WantedBy=remote-fs.target
EOF

systemctl daemon-reload
systemctl enable mnt-nfs.mount

# === Option 3 : autofs (meilleure solution pour NFS) ===

# Montage à la demande, jamais de blocage au boot
apt install autofs
cat >> /etc/auto.master <<'EOF'
/mnt/nfs  /etc/auto.nfs  --timeout=600
EOF

cat > /etc/auto.nfs <<'EOF'
data  -fstype=nfs4,vers=4.2  192.168.1.50:/export/data
EOF

systemctl enable autofs
systemctl start autofs
```

**Options NFS pour robustesse** :
- `soft` : Retourne erreur si serveur inaccessible (au lieu de bloquer)
- `timeo=10` : Timeout 1 seconde (10 × 0.1s)
- `retrans=2` : 2 tentatives maximum

---

### Erreur 9️⃣ : .automount et .mount tous les deux enabled (conflit)

**Symptôme** :
```bash
systemctl status mnt-data.mount
# Conflicting units both active

# ou
# mount: /mnt/data: already mounted
```

**Cause** : .mount et .automount activés simultanément.

**Solution** :

```bash
# ❌ INCORRECT (les deux enabled)
systemctl enable mnt-data.mount
systemctl enable mnt-data.automount

# ✅ CORRECT (seulement .automount)

# 1. Désactiver .mount
systemctl disable mnt-data.mount
systemctl stop mnt-data.mount

# 2. Activer seulement .automount
systemctl enable mnt-data.automount
systemctl start mnt-data.automount

# 3. Vérifier
systemctl status mnt-data.automount
# Active: active (waiting)  ← OK

systemctl status mnt-data.mount
# Active: inactive (dead)   ← OK (sera activé à la demande)

# 4. Lister les units enabled
systemctl list-unit-files --type=mount,automount | grep mnt-data
# mnt-data.automount  enabled   ← OK
# mnt-data.mount      disabled  ← OK

# 5. Tester automount
ls /mnt/data
# → .mount démarre automatiquement

systemctl status mnt-data.mount
# Active: active (mounted)  ← Démarré par .automount
```

**Règle** : Si vous utilisez `.automount`, **ne jamais** activer le `.mount` correspondant.

---

### Erreur 🔟 : Caractères spéciaux dans chemins fstab

**Symptôme** :
```bash
# fstab :
# /dev/sdb1  /mnt/My Data  ext4  defaults  0  2

mount -a
# mount: /mnt/My: can't find in /etc/fstab
# mount: Data: can't find in /etc/fstab
```

**Cause** : Espace dans le chemin non échappé.

**Solutions** :

```bash
# === Option 1 : Octets échappés ===

# Espace = \040
# Tab   = \011
# Newline = \012

# fstab corrigé :
/dev/sdb1  /mnt/My\040Data  ext4  defaults  0  2

# === Option 2 : Éviter espaces (recommandé) ===

# Renommer le répertoire
mv "/mnt/My Data" /mnt/MyData

# fstab :
/dev/sdb1  /mnt/MyData  ext4  defaults  0  2

# === Option 3 : systemd .mount (gère automatiquement) ===

# Générer nom avec espaces
systemd-escape -p --suffix=mount "/mnt/My Data"
# mnt-My\x20Data.mount  ← x20 = espace

cat > /etc/systemd/system/mnt-My\\x20Data.mount <<'EOF'
[Unit]
Description=Disk with space in name

[Mount]
What=/dev/sdb1
Where=/mnt/My Data
Type=ext4
Options=defaults

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl start mnt-My\\x20Data.mount

# === Autres caractères ===

# Générer encodage pour tout chemin
systemd-escape -p --suffix=mount "/mnt/special-chars_@#"
# mnt-special\x2dchars_\x40\x23.mount
```

---

## 📝 Antisèche (Cheat Sheet)

### Montage Manuel (mount)

```bash
# Montage basique
mount /dev/sdb1 /mnt/data
mount -t ext4 /dev/sdb1 /mnt/data
mount UUID=a1b2c3d4-... /mnt/data

# Avec options
mount -o ro,noexec /dev/sdb1 /mnt/data

# Remonter avec nouvelles options
mount -o remount,rw /mnt/data

# Monter selon fstab
mount /mnt/data        # Si défini dans fstab
mount -a               # Monter tout fstab

# NFS
mount -t nfs4 192.168.1.50:/export /mnt/nfs

# CIFS/SMB
mount -t cifs //server/share /mnt/smb -o credentials=/root/.smbcreds

# ISO
mount -o loop image.iso /mnt/iso

# Démonter
umount /mnt/data
umount -f /mnt/data    # Force
umount -l /mnt/data    # Lazy
```

### /etc/fstab - Format

```
<device>  <mount_point>  <type>  <options>  <dump>  <fsck>
```

**Exemples** :
```bash
# Local ext4 avec UUID
UUID=a1b2c3d4-...  /home  ext4  defaults,noatime  0  2

# NTFS avec permissions
UUID=E4F6A8B2C4D6  /mnt/win  ntfs-3g  uid=1000,gid=1000,dmask=022,fmask=133  0  0

# NFS avec options réseau
server:/export  /mnt/nfs  nfs4  defaults,_netdev,nofail  0  0

# tmpfs
tmpfs  /tmp  tmpfs  defaults,size=2G,mode=1777  0  0

# Swap
UUID=...  none  swap  sw  0  0
```

**Champs dump et fsck** :
- **dump** : `0` (pas de backup) ou `1` (backup avec dump - obsolète)
- **fsck** : `0` (pas de check), `1` (check prioritaire `/`), `2` (check normal)

### Obtenir UUID

```bash
blkid /dev/sdb1
lsblk -f /dev/sdb1
ls -l /dev/disk/by-uuid/
```

### Recharger fstab

```bash
findmnt --verify           # Vérifier syntaxe
mount -a                   # Monter tout
systemctl daemon-reload    # Synchroniser systemd
```

### autofs

**Installation** :
```bash
apt install autofs  # Debian/Ubuntu
dnf install autofs  # RHEL/Fedora
```

**Configuration** :

**/etc/auto.master** :
```
<parent_mount>  <config_file>  [options]

/mnt/autofs  /etc/auto.shares  --timeout=400 --ghost
/-           /etc/auto.direct   --timeout=600
```

**/etc/auto.shares** (montages relatifs) :
```
<subdir>  <options>  <location>

share1  -fstype=nfs4,ro      192.168.1.50:/export/data
backup  -fstype=ext4         :/dev/sdb1
cifs    -fstype=cifs,creds=  ://server/share
```

**/etc/auto.direct** (montages absolus avec `/-`) :
```
<absolute_path>  <options>  <location>

/opt/data  -fstype=nfs4  server:/export
/mnt/disk  -fstype=ext4  :/dev/sdc1
```

**Gestion** :
```bash
systemctl enable autofs
systemctl start autofs
systemctl reload autofs    # Après modification config
journalctl -u autofs -f    # Logs
```

### systemd .mount Units

**Générer nom** :
```bash
systemd-escape -p --suffix=mount /mnt/data
# mnt-data.mount
```

**Structure** :
```ini
[Unit]
Description=My Data Disk
After=local-fs-pre.target

[Mount]
What=/dev/disk/by-uuid/UUID
Where=/mnt/data
Type=ext4
Options=defaults,noatime

[Install]
WantedBy=multi-user.target
```

**Gestion** :
```bash
systemctl daemon-reload           # OBLIGATOIRE après création/modif
systemctl start mnt-data.mount
systemctl enable mnt-data.mount
systemctl status mnt-data.mount
journalctl -u mnt-data.mount
```

### systemd .automount Units

**Structure** :
```ini
[Unit]
Description=Automount My Data Disk

[Automount]
Where=/mnt/data
TimeoutIdleSec=300

[Install]
WantedBy=multi-user.target
```

**Gestion** :
```bash
# Activer seulement .automount (PAS .mount)
systemctl enable mnt-data.automount
systemctl start mnt-data.automount
systemctl status mnt-data.automount
```

### Options de Montage Courantes

| Option | Effet |
|--------|-------|
| `defaults` | rw,suid,dev,exec,auto,nouser,async |
| `ro` / `rw` | Lecture seule / Lecture-écriture |
| `auto` / `noauto` | Monter au boot / Ne pas monter |
| `user` | Utilisateurs peuvent monter |
| `nouser` | Seul root peut monter (défaut) |
| `exec` / `noexec` | Autoriser/interdire exécution |
| `suid` / `nosuid` | Autoriser/interdire SUID |
| `noatime` | Pas de mise à jour access time |
| `nodiratime` | Pas d'atime pour répertoires |
| `_netdev` | Attend réseau (fstab) |
| `nofail` | Continue boot si échec |
| `x-systemd.device-timeout=10` | Timeout 10s |

### Diagnostic

```bash
# Vérifier montages actifs
mount | grep /mnt
df -h
findmnt

# Identifier processus utilisant FS
lsof | grep /mnt/data
fuser -m /mnt/data

# Forcer démontage
fuser -km /mnt/data    # Kill processus + umount
umount -f /mnt/data    # Force
umount -l /mnt/data    # Lazy

# Logs systemd
journalctl -u mnt-data.mount
journalctl -u autofs
journalctl -xb | grep mount

# Vérifier fstab
findmnt --verify
mount -a --fake --verbose
```

---

## 🎯 Points Clés pour l'Examen LPIC-1

### ✅ Concepts à Maîtriser Absolument

1. **/etc/fstab - Structure 6 colonnes**
   - Device (UUID recommandé)
   - Mount point
   - Type (ext4, xfs, nfs4, cifs, tmpfs, swap)
   - Options (defaults, noatime, _netdev, nofail)
   - Dump (0 ou 1 - obsolète, mettre 0)
   - fsck (0/1/2 : pas de check / prioritaire `/` / normal)

2. **UUID vs /dev/sdX**
   - ✅ UUID : Stable, recommandé (obtenir avec `blkid`)
   - ❌ /dev/sdX : Change si déplacement disque

3. **autofs - Montage à la demande**
   - Installation : `apt install autofs`
   - Config principale : `/etc/auto.master`
   - Config montages : `/etc/auto.<nom>` (ex: auto.shares)
   - Point parent relatif : `/mnt/autofs` → montage `/mnt/autofs/share1`
   - Point parent absolu : `/-` → chemins absolus dans config
   - Rechargement : `systemctl reload autofs`
   - Timeout : `--timeout=<secondes>`
   - Substitution : `&` remplacé par nom du sous-répertoire

4. **systemd .mount Units**
   - Nom fichier = point de montage encodé
   - Générer : `systemd-escape -p --suffix=mount /mnt/data`
   - Sections : [Unit], [Mount], [Install]
   - `What=` : Device/UUID
   - `Where=` : Point de montage (doit correspondre au nom)
   - `Type=` : ext4, xfs, nfs4, cifs, tmpfs
   - `Options=` : Comme mount -o
   - TOUJOURS : `systemctl daemon-reload` après modification

5. **systemd .automount Units**
   - Complément de .mount
   - Même nom que .mount correspondant
   - `Where=` : Point de montage (identique à .mount)
   - `TimeoutIdleSec=` : Délai avant démontage
   - **Activer seulement .automount, PAS .mount**

6. **Options de montage critiques**
   - `defaults` = rw,suid,dev,exec,auto,nouser,async
   - `noatime` : Performance (pas de mise à jour access time)
   - `_netdev` : Attend réseau (NFS, CIFS)
   - `nofail` : Continue boot si échec (disques optionnels)
   - `user` : Utilisateurs peuvent monter
   - `ro` : Lecture seule (sécurité)

7. **Montage NFS**
   - Type : `nfs` ou `nfs4`
   - Format : `serveur:/chemin`
   - Options : `vers=4.2,hard,intr` ou `soft,timeo=10`
   - fstab : Toujours ajouter `_netdev,nofail`

8. **Montage CIFS/SMB**
   - Type : `cifs`
   - Format : `//serveur/partage`
   - Credentials : `credentials=/root/.smbcreds`
   - Options : `vers=3.0,uid=1000,gid=1000`
   - Paquet requis : `cifs-utils`

9. **Commandes essentielles**
   - `mount` : Montage manuel
   - `umount` : Démontage
   - `mount -a` : Monter tout fstab
   - `mount -o remount,rw /mnt` : Remonter
   - `findmnt --verify` : Vérifier fstab
   - `blkid` : Obtenir UUID
   - `lsof` / `fuser -m` : Processus utilisant FS
   - `systemctl daemon-reload` : Recharger systemd

10. **Diagnostic et Dépannage**
    - fstab invalide → emergency mode au boot
    - Solution : `mount -o remount,rw /` puis éditer fstab
    - `nofail` évite blocage boot
    - `fuser -km /mnt` : Kill processus + umount
    - `journalctl -u <unit>.mount` : Logs systemd
    - `systemctl list-units --type=mount` : Lister montages

### 📚 Commandes à Connaître par Cœur

```bash
# Montage/Démontage
mount /dev/sdb1 /mnt/data
mount -t nfs4 server:/export /mnt/nfs
mount -o remount,rw /mnt/data
umount /mnt/data
mount -a

# UUID
blkid /dev/sdb1
lsblk -f

# fstab
findmnt --verify
mount -a --fake --verbose

# autofs
systemctl reload autofs
journalctl -u autofs

# systemd
systemd-escape -p --suffix=mount /mnt/data
systemctl daemon-reload
systemctl start mnt-data.mount
systemctl enable mnt-data.automount

# Diagnostic
lsof | grep /mnt
fuser -m /mnt/data
df -h
findmnt
```

### ⚠️ Pièges d'Examen Fréquents

1. **fstab : Oublier `_netdev` pour montages réseau**
   - Sans `_netdev`, systemd tente montage avant réseau → échec

2. **systemd .mount : Nom fichier ≠ point de montage**
   - Fichier `my-disk.mount` avec `Where=/mnt/data` → ERREUR
   - Utiliser `systemd-escape` pour générer le bon nom

3. **autofs : Chemin relatif vs absolu**
   - `/mnt/autofs` dans auto.master → chemins **relatifs** dans auto.shares
   - `/-` dans auto.master → chemins **absolus** dans auto.direct

4. **Activer .mount ET .automount ensemble → conflit**
   - Seulement activer .automount, jamais les deux

5. **Oublier `systemctl daemon-reload` après modification**
   - systemd utilise ancienne config

6. **dump et fsck dans fstab**
   - dump : Toujours `0` (obsolète)
   - fsck : `1` pour `/`, `2` pour autres, `0` pour réseau/swap

7. **UUID dans fstab sans "UUID="**
   - ❌ `a1b2c3d4-... /mnt ext4 defaults 0 2`
   - ✅ `UUID=a1b2c3d4-... /mnt ext4 defaults 0 2`

8. **Espaces dans chemins fstab**
   - Utiliser `\040` pour espaces
   - Ou éviter espaces complètement (recommandé)

9. **mount -a ne remonte pas les FS déjà montés**
   - Utiliser `mount -o remount` pour modifications

10. **NFS/CIFS sans paquet requis**
    - `apt install nfs-common cifs-utils`

### 🔑 Acronymes et Termes

- **UUID** : Universally Unique Identifier
- **PARTUUID** : Partition UUID (GPT)
- **LABEL** : Étiquette de système de fichiers
- **NFS** : Network File System
- **CIFS** : Common Internet File System (SMB/Samba)
- **tmpfs** : Temporary File System (en RAM)
- **autofs** : Automounter Daemon
- **fstab** : File System Table
- **fsck** : File System Consistency Check
- **dump** : Backup utility (obsolète)
- **_netdev** : Network device (option fstab)
- **nofail** : No fail (option fstab)

---

## 📖 Résumé Final

Ce cours couvre les **trois méthodes principales de montage de systèmes de fichiers** sous Linux :

1. **/etc/fstab** : Montages **automatiques au boot**
   - Format 6 colonnes : device, mount_point, type, options, dump, fsck
   - UUID recommandé pour stabilité
   - Options critiques : `_netdev` (réseau), `nofail` (optionnel), `noatime` (performance)
   - Vérifier avec `findmnt --verify` avant reboot

2. **autofs** : Montages **à la demande** (on-demand)
   - Économise ressources (montage uniquement si accès)
   - Configuration : `/etc/auto.master` + `/etc/auto.<nom>`
   - Démontage automatique après timeout
   - Idéal pour partages réseau NFS/CIFS

3. **systemd mount/automount** : Gestion **intégrée systemd**
   - `.mount` : Montage contrôlé par systemd (comme service)
   - `.automount` : Alternative moderne à autofs
   - Nom fichier = point de montage encodé (`systemd-escape`)
   - Gestion avec `systemctl`, logs avec `journalctl`
   - **TOUJOURS** `daemon-reload` après modification

**Choisir la méthode appropriée** :
- **fstab** : Disques système permanents, simples à configurer
- **autofs** : Partages réseau, montages conditionnels, économie ressources
- **systemd** : Intégration avancée, dépendances complexes, contrôle fin

La maîtrise de ces trois approches est essentielle pour l'administration système Linux et la certification LPIC-1.

---

*Documentation créée le 3 février 2026 - Formation Linux Essentials / LPIC-1 - Module 4*
