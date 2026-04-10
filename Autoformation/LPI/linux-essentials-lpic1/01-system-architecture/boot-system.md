# 🚀 Démarrer le Système Linux

---

## 🎬 L'Ordre du Boot - Les 4 Actes

```
BIOS/UEFI → BootLoader → Kernel → Init
    │           │           │        │
  Acteur 1   Acteur 2   Acteur 3  Acteur 4
  (Casting)  (Régie)   (Acteur)  (Production)
```

**Analogie** 🎭 : Le démarrage est comme une pièce de théâtre
1. **BIOS/UEFI** = Directeur de casting (trouve qui va jouer)
2. **BootLoader** = Régisseur (prépare la scène)
3. **Kernel** = Acteur principal (entre en scène)
4. **Init** = Équipe de production (tout le reste de l'équipe arrive)

---

## 🔧 BIOS - Basic Input Output System

### Le vieux sage du démarrage

**Mission** : Trouver et charger le **bootstrap** (petit programme de démarrage)

### Où cherche-t-il ?

**Les 440 premiers bytes** du premier périphérique (selon le Boot Order)

```
MBR (Master Boot Record) = 512 bytes total
├── 440 bytes → Bootstrap code
├── 64 bytes  → Partition Table
└── 2 bytes   → Signature (0x55AA)
```

**💡 Point clé** : Le MBR fait partie du format **DOS Partition**

### Les 4 étapes du BIOS

```
1. POST (Power-On Self Test)
   └── Vérifie que le matériel fonctionne (RAM, CPU, etc.)

2. Activation des composants basiques
   └── Video output, clavier/souris, stockage

3. Chargement du bootstrap depuis le MBR
   └── Lecture des 440 bytes magiques

4. Chargement du second stage bootloader
   └── Passe les options au kernel (via GRUB)
```

**Limite majeure** : MBR limité à 2 To et 4 partitions primaires

---

## 🆕 UEFI - Unified Extensible Firmware Interface

### Le nouveau standard moderne

**Différence majeure** : Ne lit PAS le MBR, charge des **applications EFI** depuis sa NVRAM

### ESP - EFI System Partition

**C'est quoi ?** Une partition spéciale sur votre disque
- **Filesystem** : FAT32 (ou FAT16)
- **Emplacement** : `/boot/efi` une fois Linux démarré
- **Contenu** : Répertoire `EFI/` avec les applications bootables

**Structure typique** :
```
/boot/efi/EFI/
├── ubuntu/
│   └── grubx64.efi
├── Microsoft/
│   └── bootmgfw.efi
└── BOOT/
    └── BOOTX64.EFI (fallback)
```

### Les 4 étapes de l'UEFI

```
1. POST
   └── Tests matériels (comme BIOS)

2. Activation des composants basiques
   └── Video, input, storage

3. Exécution des applications EFI depuis l'ESP
   └── Lit la NVRAM pour savoir quelle app lancer

4. Bootloader charge le kernel
   └── L'app EFI (GRUB) prend le relais
```

### 🔒 Secure Boot

**But** : Charger uniquement des applications **signées** par les constructeurs

**Avantages** :
- Protection contre les rootkits
- Garantie d'intégrité du bootloader

**Inconvénient** :
- Peut bloquer certains drivers Linux non signés
- Peut nécessiter d'être désactivé pour certaines distributions

---

## 🎛️ GRUB - GRand Unified Bootloader

### Le bootloader le plus populaire

**Rôle** : Faire le pont entre le firmware (BIOS/UEFI) et le kernel Linux

### Le forcer à apparaître

Par défaut, GRUB peut être masqué pour un démarrage rapide.

**Pour l'afficher** :
- **BIOS** : Maintenir `SHIFT` au démarrage
- **UEFI** : Appuyer sur `ESC` au démarrage

**💡 Pourquoi ?** Pour accéder aux options avancées, mode recovery, choix du kernel...

---

### Options du Kernel dans GRUB

**Où ?** Dans la ligne `cmdline` de la configuration GRUB

**Comment modifier ?** Appuyer sur `e` dans le menu GRUB

#### Options essentielles

| Option | Description | Exemple |
|--------|-------------|---------|
| `acpi` | Activer/désactiver ACPI | `acpi=off` |
| `init` | Définir le programme init | `init=/bin/bash` |
| `systemd.unit` | Target systemd au boot | `systemd.unit=graphical.target` |
| `mem` | Limiter la RAM disponible | `mem=512M` |
| `maxcpus` | Limiter le nombre de CPUs | `maxcpus=2` ou `maxcpus=0` |
| `quiet` | Mode silencieux | `quiet` (retirer = verbose) |
| `vga` | Mode vidéo | `vga=ask` |
| `root` | Partition racine | `root=/dev/sda2` |
| `rootflags` | Options de montage root | `rootflags=ro` ou `rw` |

#### 🎯 Options les plus utilisées

**Mode recovery/debug** :
```bash
init=/bin/bash
# Lance un shell au lieu du système complet
# Utile pour réinitialiser un mot de passe
```

**Désactiver multicore** :
```bash
maxcpus=0
# Teste si un problème vient du multiprocessing
```

**Mode graphique vs text** :
```bash
systemd.unit=multi-user.target    # Mode texte
systemd.unit=graphical.target     # Mode graphique
```

**Boot verbeux** :
```bash
# Retirer "quiet" de la ligne de commande
# → Voir tous les messages du kernel
```

---

## 🔄 Init - Le Processus d'Initialisation

### Les 5 étapes après le chargement du kernel

```
1. Kernel chargé en RAM
   └── Le noyau prend vie

2. Chargement de l'initramfs
   └── RAM filesystem temporaire avec drivers/modules

3. Initialisation complète des périphériques
   └── Grâce aux modules de l'initramfs

4. Montage des filesystems (/etc/fstab)
   └── Votre "vrai" système de fichiers

5. Lancement de /sbin/init
   └── Le chef d'orchestre des services

6. initramfs supprimé de la RAM
   └── Plus besoin, on libère la mémoire
```

### 📦 initramfs - C'est quoi ?

**Initial RAM FileSystem** = Système de fichiers temporaire en mémoire

**Contenu** :
- Drivers/modules non inclus dans le kernel
- Scripts d'initialisation
- Outils de démarrage essentiels

**Pourquoi ?** Le kernel ne peut pas tout contenir → trop gros !

**Analogie** 📦 : C'est la trousse de premiers secours que le kernel utilise avant d'accéder au vrai système de fichiers

---

## 🎭 Les 3 Systèmes Init

### 1. SysV Init - Le Pionnier

**Concept** : **Runlevels** (niveaux d'exécution)

| Runlevel | Description |
|----------|-------------|
| 0 | Shutdown (arrêt) |
| 1 | Single User Mode (mode maintenance) |
| 2 | Multi-user sans réseau |
| 3 | Multi-user avec réseau (mode texte) |
| 4 | Non utilisé (personnalisable) |
| 5 | Multi-user graphique (X11) |
| 6 | Reboot (redémarrage) |

**Fonctionnement** : Scripts séquentiels dans `/etc/rc.d/` ou `/etc/init.d/`

**Limite** : Séquentiel → lent au démarrage

---

### 2. systemd - Le Moderne ⭐

**Le standard actuel** sur la majorité des distributions

#### Améliorations majeures

✅ **Parallélisation** : Lance plusieurs services en même temps  
✅ **Gestion des dépendances** : "Service A a besoin de Service B"  
✅ **Activation à la demande** : Service démarre quand on en a besoin  
✅ **Socket activation** : Service démarre quand quelqu'un le contacte  
✅ **D-Bus integration** : Communication inter-processus  
✅ **Monitoring via cgroups** : Surveillance des processus  
✅ **Snapshots** : Sauvegarder l'état du système  
✅ **Points de montage** : Gestion avancée des filesystems  

#### Concept : Targets (pas Runlevels)

**Équivalence SysV ↔ systemd** :

| SysV Runlevel | systemd Target | Description |
|---------------|----------------|-------------|
| 0 | `poweroff.target` | Arrêt |
| 1 | `rescue.target` | Mode maintenance |
| 3 | `multi-user.target` | Multi-user texte |
| 5 | `graphical.target` | Multi-user graphique |
| 6 | `reboot.target` | Redémarrage |

**Compatibilité** : systemd comprend les runlevels de SysV

---

### 3. Upstart - L'Abandonné

**Histoire** : Créé par Ubuntu, abandonné ensuite

**Concept** : Lancement de services en parallèle (comme systemd)

**Statut** : Plus vraiment utilisé aujourd'hui

---

## 📜 Kernel Ring Buffer - Les Messages du Kernel

### dmesg - Voir ce que le kernel raconte

**C'est quoi ?** Un **buffer circulaire** en mémoire contenant tous les messages du kernel

```bash
dmesg
# TRÈS verbeux ! Des centaines de lignes
```

**Résultat typique** :
```
[    0.000000] Linux version 5.15.0-56-generic
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz root=/dev/sda1
[    0.123456] Memory: 8GB RAM detected
[    1.234567] usb 1-1: new high-speed USB device
```

### Format des messages

```
[timestamp] sous-système: message
    │            │           │
    │            │           └── Description
    │            └───────────── Qui parle
    └────────────────────────── Secondes depuis boot
```

### Utilisation pratique avec grep

```bash
# Filtrer par mot-clé
dmesg | grep -i error
dmesg | grep -i usb
dmesg | grep -i network

# Voir uniquement les dernières lignes
dmesg | tail -50

# Suivre en temps réel (comme tail -f)
dmesg -w

# Messages liés à un périphérique spécifique
dmesg | grep sda
```

**💡 Astuce** : `dmesg` est votre ami pour diagnostiquer les problèmes matériels !

---

## 📋 journalctl - Les Logs sous systemd

### Le remplaçant moderne de /var/log/messages

**Différence majeure** : Logs en format **binaire** (pas de fichiers texte plats)

### Options essentielles

#### Lister les boots enregistrés

```bash
journalctl --list-boots
```

**Résultat** :
```
 0 abc123... Wed 2026-01-22 09:00:00 CET  (boot actuel)
-1 def456... Tue 2026-01-21 08:30:00 CET
-2 ghi789... Mon 2026-01-20 09:15:00 CET
```

**Lecture** :
- `0` = Boot actuel
- `-1` = Boot précédent
- `-2` = Avant-avant-dernier boot

#### Consulter les logs d'un boot spécifique

```bash
# Boot actuel
journalctl -b 0
# ou simplement
journalctl -b

# Boot précédent
journalctl -b -1

# Avant-dernier boot
journalctl -b -2
```

**Cas d'usage** : "Mon système a planté hier, je veux voir les logs"
```bash
journalctl -b -1
```

#### Consulter un fichier de log journald

```bash
journalctl -D /chemin/vers/logs/
# Ou
journalctl --directory=/var/log/journal/machine-id/
```

**Pourquoi `-D` ?** Les logs journald ne sont PAS lisibles directement (format binaire)

### 📁 Localisation des logs

```
/var/log/
├── journal/          → Logs journald (binaire)
├── syslog           → Logs système traditionnels
├── auth.log         → Authentification
├── kern.log         → Kernel
└── dmesg            → Kernel ring buffer
```

### Autres options utiles

```bash
# Suivre les logs en temps réel
journalctl -f

# Logs depuis une date
journalctl --since "2026-01-20 10:00:00"

# Logs d'un service spécifique
journalctl -u ssh.service

# Logs avec niveau de priorité
journalctl -p err    # Erreurs uniquement
journalctl -p warning  # Warnings et plus grave

# Inverser l'ordre (plus récent en premier)
journalctl -r

# Limiter le nombre de lignes
journalctl -n 50    # 50 dernières lignes
```

---

## 📋 Antisèche - Commandes du Jour

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `dmesg` | Voir les messages du kernel | `dmesg \| grep usb` |
| `dmesg -w` | Suivre les messages en temps réel | `dmesg -w` |
| `journalctl` | Voir tous les logs systemd | `journalctl` |
| `journalctl -b` | Logs du boot actuel | `journalctl -b 0` |
| `journalctl -b -1` | Logs du boot précédent | `journalctl -b -1` |
| `journalctl --list-boots` | Lister les boots enregistrés | `journalctl --list-boots` |
| `journalctl -f` | Suivre les logs en direct | `journalctl -f` |
| `journalctl -u` | Logs d'un service | `journalctl -u ssh` |

---

## 🎓 Points Clés pour l'Examen

✅ **Ordre boot** : BIOS/UEFI → BootLoader → Kernel → Init  
✅ **MBR** : 512 bytes (440 bootstrap + 64 partition table + 2 signature)  
✅ **ESP** : Partition FAT32 contenant les applications EFI  
✅ **GRUB shortcuts** : SHIFT (BIOS) ou ESC (UEFI)  
✅ **Option kernel critique** : `init=/bin/bash` pour recovery  
✅ **initramfs** : Système de fichiers temporaire en RAM  
✅ **Runlevels** : 0=shutdown, 1=single, 3=multi-user, 5=graphical, 6=reboot  
✅ **systemd targets** : `multi-user.target`, `graphical.target`  
✅ **dmesg** : Messages du kernel depuis le boot  
✅ **journalctl -b** : Logs d'un boot spécifique (0, -1, -2...)  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Mot de passe root oublié

```bash
1. Redémarrer et afficher GRUB (SHIFT ou ESC)
2. Appuyer sur 'e' pour éditer
3. Trouver la ligne "linux" et ajouter à la fin :
   init=/bin/bash
4. Appuyer sur Ctrl+X ou F10 pour démarrer
5. Vous êtes root sans mot de passe !
6. passwd root    # Changer le mot de passe
7. reboot -f
```

### Scénario 2 : Le système ne démarre plus après une mise à jour

```bash
# Une fois redémarré avec un LiveUSB ou en mode recovery
journalctl -b -1 | grep -i error
# Consulter les logs du dernier boot raté
```

### Scénario 3 : Identifier un périphérique qui pose problème au boot

```bash
dmesg | grep -i fail
dmesg | grep -i error
# Rechercher les erreurs dans les messages du kernel
```

### Scénario 4 : Démarrer en mode texte (pas d'interface graphique)

```bash
1. Dans GRUB, éditer la ligne de boot
2. Remplacer ou ajouter :
   systemd.unit=multi-user.target
3. Boot → Mode texte uniquement
```

### Scénario 5 : Voir pourquoi le système a redémarré hier

```bash
journalctl --list-boots       # Lister les boots
journalctl -b -1              # Logs du boot d'hier
journalctl -b -1 -p err       # Seulement les erreurs
```

---

**🎯 Prochaine étape** : Change Runlevels / Boot Targets and Shutdown or Reboot System

*Dernière mise à jour: 22 janvier 2026*
