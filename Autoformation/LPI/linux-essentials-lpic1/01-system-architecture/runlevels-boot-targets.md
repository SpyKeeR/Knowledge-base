# 🎚️ Changer les Runlevels / Boot Targets

---

## 🏛️ SysVinit - Le Système Historique

### Qui est-il ?

**SysVinit** = **Sys**tem **V** **init**ialization
- Le tout premier gestionnaire de services moderne
- Prend le **PID 1** (Process ID 1 = premier processus lancé)
- Père de tous les processus du système

**Analogie** 🎭 : SysVinit est le **chef d'orchestre** qui dirige tous les musiciens (services)

**Emplacement** : `/sbin/init`

---

## 🎚️ Les Runlevels - 7 Modes de Fonctionnement

### Table des Runlevels

| Runlevel | Nom | Description | Usage |
|----------|-----|-------------|-------|
| **0** | Shutdown | Arrêt du système | `shutdown`, `poweroff` |
| **1** ou **S** | Single User Mode | Mode maintenance, **PAS de réseau** | Dépannage, réparation |
| **2** | Multi-user | Multi-utilisateur avec réseau | Peu utilisé |
| **3** | Multi-user | Multi-utilisateur avec réseau | **Mode texte standard** |
| **4** | Multi-user | Multi-utilisateur avec réseau | Personnalisable, rarement utilisé |
| **5** | Multi-user Graphical | Multi-utilisateur + **serveur graphique** (X11) | **Mode graphique standard** |
| **6** | Reboot | Redémarrage du système | `reboot` |

### 💡 À retenir

**Runlevels populaires** :
- **1 (S)** : Maintenance / Recovery
- **3** : Serveur (sans interface graphique)
- **5** : Desktop (avec interface graphique)

**Runlevels techniques** :
- **0** : Éteindre
- **6** : Redémarrer

**Runlevels peu utilisés** : 2 et 4

---

## 📄 /etc/inittab - Le Fichier de Configuration

### C'est quoi ?

Le **fichier maître** qui définit :
- Quel est le runlevel par défaut
- Quels processus lancer pour chaque runlevel
- Comment réagir aux événements système

**Emplacement** : `/etc/inittab`

---

### Format d'une Ligne

```
id:runlevels:action:process
│      │       │       │
│      │       │       └─── Commande à exécuter
│      │       └─────────── Comment l'exécuter
│      └─────────────────── Pour quels runlevels (0-6)
└────────────────────────── Identifiant unique (4 caractères max)
```

**Exemple concret** :
```bash
l0:0:wait:/etc/init.d/rc 0
│  │  │   └─── Exécuter le script rc avec argument 0
│  │  └─────── Attendre la fin du processus
│  └────────── Pour le runlevel 0 uniquement
└───────────── ID = "l0"
```

---

### Définir le Runlevel par Défaut

**Syntaxe spéciale** :
```bash
id:5:initdefault:
│  │     │
│  │     └───────── Mot-clé "initdefault" (littéral)
│  └─────────────── Numéro du runlevel par défaut
└────────────────── "id" (littéral)
```

**Exemple** :
```bash
id:3:initdefault:
# Le système démarre en runlevel 3 (multi-user texte)

id:5:initdefault:
# Le système démarre en runlevel 5 (graphique)
```

---

### Les Actions Disponibles

| Action | Description | Runlevels ? |
|--------|-------------|-------------|
| `boot` | Exécuté à l'initialisation | **Ignore** les runlevels |
| `bootwait` | Comme `boot` mais **attend** la fin | **Ignore** les runlevels |
| `sysinit` | Exécuté **après** l'init | **Ignore** les runlevels |
| `wait` | Exécuté pour un runlevel, **attend** la fin | ✅ Respecte les runlevels |
| `respawn` | Relancé automatiquement s'il se termine | ✅ Respecte les runlevels |
| `ctrlaltdel` | Exécuté lors de Ctrl+Alt+Del | **Ignore** les runlevels |

#### Exemples pratiques

**Initialisation système** :
```bash
si::sysinit:/etc/init.d/rcS
# "si" = ID
# Pas de runlevel (sysinit ignore les runlevels)
# Exécute rcS au démarrage
```

**Getty (login terminal)** :
```bash
1:2345:respawn:/sbin/getty 38400 tty1
# ID = "1"
# Runlevels 2, 3, 4, 5
# respawn = relancé automatiquement
# Lance getty sur tty1
```

**Ctrl+Alt+Del** :
```bash
ca::ctrlaltdel:/sbin/shutdown -t3 -r now
# Redémarre le système quand on appuie sur Ctrl+Alt+Del
```

**Runlevel 0 (shutdown)** :
```bash
l0:0:wait:/etc/init.d/rc 0
# Attend que tous les services du runlevel 0 soient terminés
```

---

### Recharger inittab

Après modification de `/etc/inittab` :

```bash
telinit q
# ou
telinit Q
```

**💡 Mnémonique** : `q` = **Q**uery / Recharge la **q**onfiguration

**⚠️ Attention** : Ne modifiez `/etc/inittab` que si vous savez ce que vous faites !

---

## 📁 /etc/init.d/ - Les Scripts de Services

### Structure des Répertoires

```
/etc/
├── init.d/          → Scripts de services (actifs)
│   ├── networking
│   ├── ssh
│   ├── apache2
│   └── ...
├── rc0.d/           → Scripts pour runlevel 0 (shutdown)
├── rc1.d/           → Scripts pour runlevel 1 (single)
├── rc2.d/           → Scripts pour runlevel 2
├── rc3.d/           → Scripts pour runlevel 3
├── rc4.d/           → Scripts pour runlevel 4
├── rc5.d/           → Scripts pour runlevel 5
└── rc6.d/           → Scripts pour runlevel 6 (reboot)
```

**Organisation** :
- `/etc/init.d/` = Scripts **originaux**
- `/etc/rcX.d/` = **Liens symboliques** vers les scripts de `/etc/init.d/`

---

### Convention de Nommage : K vs S

**Dans les répertoires rcX.d/** :

```
rc3.d/
├── K01apache2       → Kill apache2
├── K02mysql         → Kill mysql
├── S01networking    → Start networking
├── S02ssh           → Start ssh
└── S99rc.local      → Start rc.local (dernier)
```

#### Décryptage

```
S99ssh
│││ │
││└─ Nom du service
│└── Ordre d'exécution (00-99)
└─── S = Start / K = Kill
```

**Ordre d'exécution** :
- Plus le numéro est **petit**, plus il s'exécute **tôt**
- `S01` avant `S02` avant `S99`

**K vs S** :
- **K** = **Kill** (arrêter le service)
- **S** = **Start** (démarrer le service)

---

### Exemple : Passage en Runlevel 3

```bash
# Depuis runlevel 5 → runlevel 3
1. Exécuter les scripts K* du rc3.d/ (arrêter services)
   K01displaymanager    # Arrête le serveur graphique
   
2. Exécuter les scripts S* du rc3.d/ (démarrer services)
   S01networking
   S02ssh
   ...
```

**Logique** : 
- On **tue** (K) ce qui ne doit pas tourner dans ce runlevel
- On **démarre** (S) ce qui doit tourner dans ce runlevel

---

## 🔍 Commandes de Gestion des Runlevels

### runlevel - "Où suis-je ?"

```bash
runlevel
```

**Résultat** :
```
N 5
│ │
│ └─── Runlevel actuel
└───── Runlevel précédent (N = aucun changement depuis boot)
```

**Exemples** :
```bash
N 5     # Boot en runlevel 5, jamais changé
3 5     # Était en runlevel 3, maintenant en 5
5 1     # Était en runlevel 5, maintenant en 1 (maintenance)
```

**💡 Astuce** : `N` = **N**ew boot (pas de changement de runlevel)

---

### telinit - Changer de Runlevel

```bash
telinit [runlevel]
```

**Exemples pratiques** :

```bash
# Passer en mode maintenance
telinit 1
# ou
telinit S

# Passer en mode texte
telinit 3

# Passer en mode graphique
telinit 5

# Redémarrer
telinit 6

# Éteindre
telinit 0
```

**⚠️ Important** : Nécessite les **droits root** !

---

### Autres Commandes Utiles

```bash
# Afficher le runlevel actuel (alternatif)
who -r

# Éteindre le système (équivalent telinit 0)
shutdown -h now
halt
poweroff

# Redémarrer (équivalent telinit 6)
shutdown -r now
reboot
```

---

## 📋 Antisèche - Commandes du Jour

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `runlevel` | Voir le runlevel actuel et précédent | `runlevel` |
| `telinit 1` | Passer en mode maintenance | `telinit 1` |
| `telinit 3` | Passer en mode texte | `telinit 3` |
| `telinit 5` | Passer en mode graphique | `telinit 5` |
| `telinit 6` | Redémarrer | `telinit 6` |
| `telinit q` | Recharger /etc/inittab | `telinit q` |
| `who -r` | Voir le runlevel actuel | `who -r` |
| `ls /etc/rc3.d/` | Voir les services du runlevel 3 | `ls /etc/rc3.d/` |

---

## 🎓 Points Clés pour l'Examen

✅ **PID 1** : Init est toujours le processus avec PID 1  
✅ **Runlevel 0** : Shutdown (arrêt)  
✅ **Runlevel 1 ou S** : Single User Mode (maintenance, pas de réseau)  
✅ **Runlevel 3** : Multi-user mode texte (serveur)  
✅ **Runlevel 5** : Multi-user mode graphique (desktop)  
✅ **Runlevel 6** : Reboot (redémarrage)  
✅ **inittab format** : `id:runlevels:action:process`  
✅ **Default runlevel** : `id:5:initdefault:`  
✅ **Scripts K*** : Kill (arrêter) un service  
✅ **Scripts S*** : Start (démarrer) un service  
✅ **Ordre scripts** : S01 avant S02 avant S99  
✅ **telinit q** : Recharger /etc/inittab  
✅ **runlevel** : Affiche "précédent actuel" (N = pas de changement)  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Passer en mode maintenance pour réparer

```bash
# Depuis un terminal root
telinit 1

# Vérification
runlevel
# Résultat : 5 1 (était en 5, maintenant en 1)

# Faire vos réparations...
# ...

# Retour en mode graphique
telinit 5
```

---

### Scénario 2 : Redémarrer proprement

```bash
# Méthode 1 : Via telinit
telinit 6

# Méthode 2 : Via shutdown
shutdown -r now

# Méthode 3 : Via reboot
reboot
```

---

### Scénario 3 : Changer le runlevel par défaut (boot en mode texte)

```bash
# Éditer /etc/inittab
vim /etc/inittab

# Modifier la ligne :
id:5:initdefault:
# En :
id:3:initdefault:

# Recharger la config
telinit q

# Au prochain reboot → démarrage en mode texte
```

---

### Scénario 4 : Lister les services qui démarrent en runlevel 5

```bash
ls -l /etc/rc5.d/

# Résultat :
# lrwxrwxrwx 1 root root 20 Jan 15 10:00 S01networking -> ../init.d/networking
# lrwxrwxrwx 1 root root 17 Jan 15 10:00 S02ssh -> ../init.d/ssh
# lrwxrwxrwx 1 root root 15 Jan 15 10:00 S99gdm -> ../init.d/gdm

# Les liens pointent vers /etc/init.d/
```

**Pour voir uniquement les services démarrés (S)** :
```bash
ls /etc/rc5.d/S*
```

**Pour voir uniquement les services arrêtés (K)** :
```bash
ls /etc/rc5.d/K*
```

---

### Scénario 5 : Gérer le comportement de Ctrl+Alt+Del

```bash
# Éditer /etc/inittab
vim /etc/inittab

# Trouver la ligne :
ca::ctrlaltdel:/sbin/shutdown -t3 -r now

# Désactiver (commenter) :
#ca::ctrlaltdel:/sbin/shutdown -t3 -r now

# Ou changer le comportement (éteindre au lieu de redémarrer) :
ca::ctrlaltdel:/sbin/shutdown -t3 -h now

# Recharger
telinit q
```

---

### Scénario 6 : Vérifier dans quel runlevel vous êtes

```bash
# Méthode 1
runlevel
# N 5

# Méthode 2
who -r
# run-level 5  2026-01-22 09:30

# Méthode 3 (avec systemd aussi compatible)
systemctl get-default
# graphical.target (équivalent runlevel 5)
```

---

## 🔄 Tableau de Correspondance Actions

| Action | Attente ? | Respecte runlevel ? | Usage typique |
|--------|-----------|---------------------|---------------|
| `boot` | Non | Non | Scripts rapides au boot |
| `bootwait` | **Oui** | Non | Scripts critiques au boot |
| `sysinit` | Non | Non | Init système après boot |
| `wait` | **Oui** | **Oui** | Transition de runlevel |
| `respawn` | N/A | **Oui** | Getty (login), services critiques |
| `ctrlaltdel` | Variable | Non | Gestion Ctrl+Alt+Del |

---

## 📁 Arborescence Complète SysVinit

```
/
├── sbin/
│   └── init              → Binaire SysVinit (PID 1)
├── etc/
│   ├── inittab           → Configuration runlevels
│   ├── init.d/           → Scripts de services (originaux)
│   │   ├── networking
│   │   ├── ssh
│   │   ├── apache2
│   │   └── ...
│   ├── rc0.d/            → Liens pour runlevel 0
│   ├── rc1.d/            → Liens pour runlevel 1
│   ├── rc2.d/            → Liens pour runlevel 2
│   ├── rc3.d/            → Liens pour runlevel 3
│   ├── rc4.d/            → Liens pour runlevel 4
│   ├── rc5.d/            → Liens pour runlevel 5
│   └── rc6.d/            → Liens pour runlevel 6
```

---

## ⚙️ systemd - Le Gestionnaire Moderne

### Rappel : Différence avec SysVinit

| SysVinit | systemd |
|----------|---------|
| Runlevels (0-6) | **Targets** |
| Séquentiel | **Parallèle** |
| Scripts shell | **Units** (fichiers déclaratifs) |
| `/etc/init.d/` | `/lib/systemd/system/` |

**Analogie** 🏗️ : SysVinit = construction brique par brique, systemd = préfabriqué assemblé en parallèle

---

## 🧩 Les Types d'Units - Les Briques de systemd

systemd ne gère pas que des services ! Il gère **7 types d'unités** différentes.

### 📦 service - Services Système

**C'est quoi ?** Des ressources système actives (daemons, processus)

**Caractéristiques** :
- Peut être **initialisé** (start)
- Peut être **interrompu** (stop)  
- Peut être **redémarré** (restart)

**Exemples** :
- `ssh.service` → Serveur SSH
- `nginx.service` → Serveur web
- `cron.service` → Planificateur de tâches

---

### 🔌 socket - Sockets Réseau ou IPC

**C'est quoi ?** Un point de communication (réseau ou fichier)

**Usage** :
- Socket activation → Le service démarre **quand quelqu'un se connecte**
- Optimisation du boot → Service pas lancé tant qu'on n'en a pas besoin

**Exemples** :
- `sshd.socket` → Écoute sur le port 22
- `cups.socket` → Socket d'impression
- `dbus.socket` → Communication inter-processus

**💡 Astuce** : Un `.socket` peut activer un `.service` à la demande !

---

### 🖥️ device - Périphériques Matériels

**C'est quoi ?** Matériel identifié par le kernel

**⚠️ Condition** : Nécessite une **règle udev** pour être exposé comme unit

**Usage** :
- Résoudre les **dépendances matérielles**
- "Ce service ne démarre que si cette carte réseau est présente"

**Exemples** :
- `dev-sda.device` → Disque /dev/sda
- `sys-devices-...-ttyUSB0.device` → Port série USB

---

### 💾 mount - Points de Montage

**C'est quoi ?** Comme une entrée dans `/etc/fstab`, mais géré par systemd

**Correspondance** :
```
/etc/fstab          →  systemd mount unit
/dev/sdb1 /data     →  data.mount
```

**Avantages** :
- Dépendances gérées par systemd
- Montage parallélisé
- Meilleure gestion d'erreurs

**Exemples** :
- `home.mount` → Montage de /home
- `tmp.mount` → Montage de /tmp

---

### 🔄 automount - Montage à la Demande

**C'est quoi ?** Point de montage initialisé **lors de la première tentative d'accès**

**Analogie** 📂 : Comme un dossier qui ne "s'ouvre vraiment" que quand on clique dessus

**Usage** :
- Partages réseau (NFS, SMB)
- Disques externes
- Optimisation du boot

**Exemple** :
```bash
# L'utilisateur fait : cd /mnt/backup
# → systemd monte automatiquement le disque
# → Puis change de répertoire
```

---

### 🎯 target - Groupes d'Unités

**C'est quoi ?** Un **groupe d'unités** managé comme une seule unité

**Analogie** 🎭 : Comme un "niveau de jeu" qui active plusieurs éléments en même temps

**Équivalence avec Runlevels** :

| Runlevel | systemd Target | Description |
|----------|----------------|-------------|
| 0 | `poweroff.target` | Arrêt |
| 1 | `rescue.target` | Mode maintenance |
| 3 | `multi-user.target` | Multi-user texte |
| 5 | `graphical.target` | Multi-user graphique |
| 6 | `reboot.target` | Redémarrage |

**Exemples** :
- `graphical.target` → Active réseau + multi-user + interface graphique
- `multi-user.target` → Active réseau + services essentiels

---

### 📸 snapshot - Instantanés (Rare)

**C'est quoi ?** Capture de l'état actuel du gestionnaire systemd

**⚠️ Attention** : **Pas disponible sur toutes les distributions** (fonctionnalité deprecated)

**Usage historique** :
- Sauvegarder l'état actuel
- Revenir à cet état plus tard

**Note** : Peu utilisé en pratique, remplacé par d'autres mécanismes

---

## 📁 Localisation des Units

```
/lib/systemd/system/          → Units fournies par le système
├── ssh.service
├── nginx.service
├── graphical.target
└── ...

/etc/systemd/system/          → Units personnalisées/prioritaires
├── my-custom.service
└── multi-user.target.wants/  → Liens symboliques
```

**Priorité** : `/etc/systemd/system/` > `/lib/systemd/system/`

**💡 Règle** : Ne jamais modifier `/lib/systemd/system/` directement !

---

## 🔍 Lister les Units

### systemctl list-unit-files - TOUS les Fichiers

```bash
systemctl list-unit-files
```

**Affiche** : **TOUS** les fichiers d'unités (actifs ou non)

**Résultat** :
```
UNIT FILE                    STATE
ssh.service                  enabled
nginx.service                enabled
bluetooth.service            disabled
tmp.mount                    static
graphical.target             static
```

**États possibles** :
- `enabled` → Démarre au boot
- `disabled` → Ne démarre pas au boot
- `static` → Activé par dépendance (pas directement)
- `masked` → Désactivé de force (impossible de démarrer)

---

### Filtrer par Type

```bash
systemctl list-unit-files --type=service
# Seulement les services

systemctl list-unit-files --type=socket
# Seulement les sockets

systemctl list-unit-files --type=target
# Seulement les targets
```

**Types disponibles** : `service`, `socket`, `device`, `mount`, `automount`, `target`, `snapshot`, `timer`, `path`, `slice`, `scope`

---

### systemctl list-units - Unités ACTIVES Seulement

```bash
systemctl list-units
```

**Différence avec list-unit-files** :
- `list-unit-files` → **Tous** les fichiers (même désactivés)
- `list-units` → **Seulement** ce qui est actuellement actif

**Résultat** :
```
UNIT                    LOAD   ACTIVE SUB     DESCRIPTION
ssh.service             loaded active running OpenSSH server
nginx.service           loaded active running nginx web server
graphical.target        loaded active active  Graphical Interface
```

**Filtrage** :
```bash
systemctl list-units --type=service      # Services actifs
systemctl list-units --type=target       # Targets actifs
systemctl list-units --state=failed      # Unités en erreur
```

---

## ⚡ Gestion de l'Alimentation avec systemd

### suspend - Mode Veille (RAM)

```bash
systemctl suspend
```

**Ce qui se passe** :
- Système en **basse consommation**
- Données gardées en **RAM**
- Réveil rapide (quelques secondes)

**Analogie** 💤 : Comme "mettre en pause" un jeu vidéo

**⚠️ Limitation** : Batterie se vide lentement (RAM toujours alimentée)

---

### hibernate - Hibernation (Disque)

```bash
systemctl hibernate
```

**Ce qui se passe** :
- Données de la RAM écrites sur le **disque** (partition swap)
- Système complètement **éteint**
- Au redémarrage : restauration de l'état exact

**Analogie** 💾 : Comme "sauvegarder" un jeu vidéo avant de quitter

**Avantages** :
- Aucune consommation électrique
- État préservé même si batterie retirée

**Prérequis** :
- Partition swap >= taille de la RAM
- Swap configuré dans `/etc/fstab`

---

### hybrid-sleep - Mode Hybride

```bash
systemctl hybrid-sleep
```

**Combine** : Suspend + Hibernate
- Écrit sur disque (comme hibernate)
- Garde en RAM (comme suspend)
- Si batterie tient → réveil rapide depuis RAM
- Si batterie morte → réveil depuis disque

---

## ⚙️ Configuration de l'Alimentation

### Fichiers de Configuration

#### Fichier principal : /etc/systemd/logind.conf

```bash
vim /etc/systemd/logind.conf
```

**Options disponibles** :
```ini
[Login]
HandlePowerKey=poweroff           # Bouton power → éteindre
HandleSuspendKey=suspend          # Bouton veille → suspend
HandleHibernateKey=hibernate      # Bouton hibernation
HandleLidSwitch=suspend           # Fermeture écran laptop → suspend
HandleLidSwitchExternalPower=ignore  # Écran fermé sur secteur → rien
IdleAction=ignore                 # Action si inactif
IdleActionSec=30min               # Délai avant IdleAction
```

**Valeurs possibles** :
- `poweroff` → Éteindre
- `reboot` → Redémarrer
- `suspend` → Veille
- `hibernate` → Hibernation
- `hybrid-sleep` → Hybride
- `ignore` → Ne rien faire
- `lock` → Verrouiller l'écran

---

#### Fichiers supplémentaires : /etc/systemd/logind.conf.d/

```bash
# Créer un fichier de configuration personnalisé
cat > /etc/systemd/logind.conf.d/laptop.conf << EOF
[Login]
HandleLidSwitch=hibernate
HandleLidSwitchExternalPower=suspend
EOF
```

**Avantages** :
- Configuration modulaire
- Pas de modification du fichier principal
- Priorité sur `/etc/systemd/logind.conf`

**Application des changements** :
```bash
systemctl restart systemd-logind
```

---

## ⚠️ Conflit avec ACPI Daemon (acpid)

### Le Problème

**systemd** et **acpid** gèrent tous deux les événements d'alimentation

**⚠️ Règle** : On ne peut pas avoir les deux en même temps !

### acpid - Advanced Configuration and Power Interface Daemon

**Avantages d'acpid** :
- Contrôles **plus fins** sur les événements d'alimentation
- Scripts personnalisés pour chaque événement
- Particulièrement utile sur **laptops**

**Exemples d'événements gérés** :
- Batterie faible
- Adaptateur AC branché/débranché
- Température élevée
- Boutons spéciaux du laptop

---

### Choisir entre systemd et acpid

| Critère | systemd logind | acpid |
|---------|----------------|-------|
| Simplicité | ✅ Facile | ⚠️ Plus complexe |
| Flexibilité | ⚠️ Limitée | ✅ Scripts personnalisés |
| Laptops | ✅ Suffisant pour basique | ✅ Recommandé pour avancé |
| Serveurs | ✅ Recommandé | ❌ Inutile |

**Vérifier si acpid est actif** :
```bash
systemctl status acpid

# Si actif et vous voulez utiliser systemd logind :
systemctl stop acpid
systemctl disable acpid
```

**Vérifier si systemd-logind gère l'alimentation** :
```bash
systemctl status systemd-logind
```

---

## 📋 Antisèche - Commandes systemd

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `systemctl list-unit-files` | Voir tous les fichiers d'units | `systemctl list-unit-files` |
| `systemctl list-unit-files --type=service` | Voir tous les services | `systemctl list-unit-files --type=service` |
| `systemctl list-units` | Voir les units actives | `systemctl list-units` |
| `systemctl list-units --type=service` | Voir les services actifs | `systemctl list-units --type=service` |
| `systemctl list-units --state=failed` | Voir les units en erreur | `systemctl list-units --state=failed` |
| `systemctl suspend` | Mettre en veille (RAM) | `systemctl suspend` |
| `systemctl hibernate` | Hiberner (disque) | `systemctl hibernate` |
| `systemctl hybrid-sleep` | Veille hybride | `systemctl hybrid-sleep` |

---

## 🎓 Points Clés pour l'Examen

✅ **7 types d'units** : service, socket, device, mount, automount, target, snapshot  
✅ **Units location** : `/lib/systemd/system/` (système) et `/etc/systemd/system/` (custom)  
✅ **list-unit-files** : Tous les fichiers (actifs ou non)  
✅ **list-units** : Seulement les units actives  
✅ **--type=service** : Filtrer par type d'unit  
✅ **suspend** : Données en RAM, basse consommation  
✅ **hibernate** : Données sur disque, arrêt complet  
✅ **logind.conf** : Configuration alimentation systemd  
✅ **Conflit** : systemd logind ⚔️ acpid (un seul à la fois)  
✅ **acpid** : Contrôles fins, recommandé laptops avancés  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Lister tous les services et leur état

```bash
systemctl list-unit-files --type=service | grep -E "enabled|disabled"

# Ou pour voir seulement les services actifs
systemctl list-units --type=service
```

---

### Scénario 2 : Configurer la fermeture de l'écran laptop

```bash
# Créer une configuration personnalisée
sudo tee /etc/systemd/logind.conf.d/lid.conf << EOF
[Login]
HandleLidSwitch=suspend
HandleLidSwitchExternalPower=ignore
EOF

# Appliquer
sudo systemctl restart systemd-logind
```

**Résultat** :
- Sur batterie → fermeture écran = suspend
- Sur secteur → fermeture écran = rien

---

### Scénario 3 : Désactiver acpid pour utiliser systemd

```bash
# Vérifier si acpid est actif
systemctl status acpid

# Désactiver acpid
sudo systemctl stop acpid
sudo systemctl disable acpid
sudo systemctl mask acpid   # Empêche réactivation accidentelle

# Vérifier systemd-logind
systemctl status systemd-logind
```

---

### Scénario 4 : Trouver les services en erreur

```bash
systemctl list-units --state=failed

# Pour plus de détails sur un service en erreur
systemctl status nom-du-service.service
journalctl -u nom-du-service.service
```

---

### Scénario 5 : Voir tous les targets disponibles

```bash
systemctl list-unit-files --type=target

# Voir quel target est actif
systemctl list-units --type=target
```

---

### Scénario 6 : Tester l'hibernation

```bash
# Prérequis : vérifier le swap
swapon --show
# Doit afficher au moins autant que votre RAM

# Tester l'hibernation
sudo systemctl hibernate

# Au redémarrage, vérifier que l'état est restauré
```

---

## 🔄 Comparaison list-unit-files vs list-units

```bash
# Exemple concret

# list-unit-files : TOUS les fichiers
systemctl list-unit-files --type=service
# nginx.service     enabled   ← Installé et activé
# apache2.service   disabled  ← Installé mais désactivé
# ssh.service       enabled   ← Installé et activé

# list-units : Seulement ce qui TOURNE
systemctl list-units --type=service
# nginx.service     loaded active running   ← Actuellement en cours
# ssh.service       loaded active running   ← Actuellement en cours
# (apache2 n'apparaît pas car disabled et pas lancé)
```

**💡 Mnémonique** :
- `list-unit-**files**` = Tout ce qui **existe** sur le disque
- `list-**units**` = Tout ce qui est **chargé** en mémoire

---

## 🔄 Upstart - Le Système de Transition

### Contexte Historique

**Upstart** = Créé par Ubuntu, abandonné au profit de systemd

**Position** : Entre SysVinit (ancien) et systemd (moderne)

**Statut actuel** : Rarement utilisé aujourd'hui, mais peut être rencontré sur d'anciennes installations

---

### 📁 Structure Upstart

**Répertoire de configuration** : `/etc/init/`

```bash
/etc/init/
├── tty1.conf
├── ssh.conf
├── networking.conf
└── ...
```

**Différence avec SysV** :
- SysVinit → `/etc/init.d/` (scripts shell)
- Upstart → `/etc/init/` (fichiers de configuration)

---

### Gestion des Services avec initctl

#### Lister les services

```bash
initctl list
```

**Résultat** :
```
ssh start/running, process 1234
networking start/running
tty1 start/running, process 5678
```

**Format** :
```
service_name état/sous-état, process PID
```

---

#### Contrôler les services

```bash
# Démarrer un service
start nom_service
# Exemple
start ssh

# Arrêter un service
stop nom_service
# Exemple
stop ssh

# Vérifier le statut
status nom_service
# Exemple
status ssh
```

**💡 Similaire à systemctl** :
- Upstart : `start ssh`
- systemd : `systemctl start ssh`

---

### Compatibilité avec SysVinit

**Upstart comprend les commandes SysV** !

```bash
# Ces commandes fonctionnent sous Upstart
runlevel          # Voir le runlevel actuel
telinit 3         # Changer de runlevel
telinit 6         # Redémarrer
```

**Pourquoi ?** Upstart a été conçu pour faciliter la transition depuis SysV

**Analogie** 🔄 : Upstart parle deux langues (SysV et Upstart)

---

## 🔌 Arrêter et Redémarrer le Système

### shutdown - La Commande Universelle

**Disponible sur** : SysVinit, Upstart, systemd

**Fonction** : Arrêter ou redémarrer le système **proprement**

---

### Ce qui se passe lors d'un shutdown

```
1. Nouveaux logins BLOQUÉS
   └── Plus personne ne peut se connecter

2. Message envoyé à tous les utilisateurs
   └── "System going down in X minutes"

3. Signal SIGTERM envoyé à tous les processus
   └── "S'il te plaît, termine-toi gentiment"

4. Attente de quelques secondes
   └── Laisser le temps aux processus de se terminer

5. Signal SIGKILL envoyé aux processus restants
   └── "Maintenant tu DOIS te terminer !"

6. Démontage des filesystems
   └── Garantir l'intégrité des données

7. Arrêt ou redémarrage
   └── Selon l'option choisie
```

**💡 Point clé** : SIGTERM (poli) puis SIGKILL (forcé)

---

### Syntaxe shutdown

```bash
shutdown [options] temps [message]
          │         │       │
          │         │       └─── Message aux utilisateurs
          │         └─────────── OBLIGATOIRE
          └───────────────────── Optionnel
```

---

### Format du Temps

#### Heure absolue (HH:MM)

```bash
shutdown 02:00
# Arrêt à 2h du matin

shutdown 14:30
# Arrêt à 14h30

shutdown 23:59
# Arrêt à 23h59
```

**Usage** : Maintenance programmée la nuit

---

#### Délai relatif (+minutes)

```bash
shutdown +20
# Dans 20 minutes

shutdown +5
# Dans 5 minutes

shutdown +60
# Dans 1 heure (60 minutes)
```

**Usage** : Laisser le temps aux utilisateurs de sauvegarder

---

#### Immédiat (now)

```bash
shutdown now
# Tout de suite !
```

**⚠️ Attention** : Aucun délai pour les utilisateurs !

---

### Options de shutdown

| Option | Description | Exemple |
|--------|-------------|---------|
| `-h` | **Halt** (arrêter) | `shutdown -h now` |
| `-r` | **Reboot** (redémarrer) | `shutdown -r +10` |
| `-H` | Halt (arrêt matériel) | `shutdown -H now` |
| `-P` | **Poweroff** (éteindre) | `shutdown -P now` |
| `-c` | **Cancel** (annuler) | `shutdown -c` |
| `-k` | **Fake** (message seulement) | `shutdown -k +5` |

---

### Exemples Pratiques

#### Arrêt dans 10 minutes avec message

```bash
shutdown -h +10 "Maintenance système, sauvegardez vos travaux !"
```

**Ce qui arrive** :
```
Broadcast message from root@server (pts/0) (Wed Jan 22 10:00:00 2026):

Maintenance système, sauvegardez vos travaux !
The system is going down for halt in 10 minutes!
```

---

#### Redémarrage programmé

```bash
shutdown -r 03:00 "Mise à jour du kernel - redémarrage automatique"
```

---

#### Arrêt immédiat

```bash
shutdown -h now
# ou simplement
shutdown now
# (par défaut = halt)
```

---

#### Annuler un shutdown programmé

```bash
# Programmer un shutdown
shutdown -h +30 "Maintenance dans 30 minutes"

# Changer d'avis
shutdown -c

# Message envoyé aux utilisateurs :
# "System shutdown cancelled"
```

---

#### Fake shutdown (blague ou test)

```bash
shutdown -k +5 "Le système va redémarrer !"
```

**Effet** : 
- ✅ Message envoyé aux utilisateurs
- ❌ Le système ne s'arrête PAS réellement

**Usage** : Tester les réactions, ou... faire peur aux collègues 😈

---

### 🔐 Contrôle d'Accès Ctrl+Alt+Del (SysV)

#### Le Problème

Par défaut, **n'importe qui** peut redémarrer via Ctrl+Alt+Del sur la console physique

#### La Solution : /etc/shutdown.allow

**Étape 1** : Modifier `/etc/inittab`

```bash
# Ligne originale :
ca::ctrlaltdel:/sbin/shutdown -t3 -r now

# Ajouter l'option -a :
ca::ctrlaltdel:/sbin/shutdown -t3 -r -a now
#                                      ↑
#                              Option -a ajoutée
```

**Étape 2** : Créer `/etc/shutdown.allow`

```bash
# Lister les utilisateurs autorisés
cat > /etc/shutdown.allow << EOF
root
admin
john
EOF
```

**Étape 3** : Recharger inittab

```bash
telinit q
```

**Résultat** : Seuls root, admin et john peuvent redémarrer avec Ctrl+Alt+Del

**💡 Point clé** : L'option `-a` active la vérification du fichier `/etc/shutdown.allow`

---

## ⚡ Arrêt/Redémarrage avec systemd

### Les Commandes Modernes

```bash
# Redémarrer
systemctl reboot

# Éteindre
systemctl poweroff

# Arrêter (halt)
systemctl halt
```

**⚠️ Prérequis** : **Élévation de privilèges nécessaire** (root ou sudo)

---

### Comparaison des Commandes

| Action | Ancienne commande | systemd |
|--------|-------------------|---------|
| Redémarrer | `shutdown -r now` | `systemctl reboot` |
| Éteindre | `shutdown -h now` | `systemctl poweroff` |
| Arrêter | `halt` | `systemctl halt` |
| Suspendre | N/A | `systemctl suspend` |
| Hiberner | N/A | `systemctl hibernate` |

**💡 Toutes restent valides** : Les anciennes commandes fonctionnent toujours !

---

### Pourquoi systemctl ?

**Avantages** :
- Plus cohérent (tout via `systemctl`)
- Meilleure intégration avec systemd
- Plus d'options (suspend, hibernate, hybrid-sleep)

**Inconvénient** :
- Moins portable (spécifique à systemd)

**Recommandation** :
- Scripts portables → `shutdown`
- Systèmes modernes systemd → `systemctl`

---

## 📢 wall - Envoyer un Message à Tous

### C'est quoi ?

**wall** = **W**rite to **ALL** users

**Fonction** : Envoyer un message sur le terminal de **tous les utilisateurs connectés**

---

### Syntaxe

```bash
wall "Votre message"
# ou
wall < fichier.txt
# ou
echo "Message" | wall
```

---

### Exemples Pratiques

#### Message simple

```bash
wall "Le serveur va redémarrer dans 5 minutes !"
```

**Résultat sur les terminaux des utilisateurs** :
```
Broadcast message from root@server (pts/0) (Wed Jan 22 14:30:00 2026):

Le serveur va redémarrer dans 5 minutes !
```

---

#### Combiner avec shutdown

```bash
wall "Attention : maintenance système dans 10 minutes" && shutdown -h +10
```

**Séquence** :
1. Message immédiat via `wall`
2. Puis shutdown programmé (qui enverra aussi ses propres messages)

**💡 Astuce** : Double avertissement = utilisateurs doublement prévenus !

---

#### Message depuis un fichier

```bash
cat > /tmp/message.txt << EOF
╔════════════════════════════════╗
║   MAINTENANCE PROGRAMMÉE       ║
║   Arrêt dans 15 minutes        ║
║   Sauvegardez vos travaux !    ║
╚════════════════════════════════╝
EOF

wall < /tmp/message.txt
```

---

#### Message avec emoji (si terminal compatible)

```bash
wall "⚠️  URGENT : Mise à jour de sécurité - redémarrage dans 5 min ⚠️"
```

---

### Qui reçoit le message ?

**Tous les utilisateurs avec un terminal ouvert** :
- Sessions SSH
- Consoles physiques (tty1, tty2...)
- Terminaux graphiques (pts/0, pts/1...)

**Qui NE reçoit PAS** :
- Utilisateurs déconnectés
- Sessions sans terminal (cron jobs, services...)

---

### Permissions

```bash
# Utilisateur normal peut envoyer des messages
wall "Hello tout le monde"
# ✅ Fonctionne

# Mais pour shutdown, il faut root
shutdown -h +5
# ❌ Permission denied (sans sudo)
```

**💡 Règle** :
- `wall` → Tout le monde
- `shutdown` → Root uniquement

---

## 📋 Antisèche - Commandes Upstart et Shutdown

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `initctl list` | Lister les services Upstart | `initctl list` |
| `start service` | Démarrer un service | `start ssh` |
| `stop service` | Arrêter un service | `stop ssh` |
| `status service` | Voir le statut | `status ssh` |
| `shutdown -h +10` | Arrêter dans 10 min | `shutdown -h +10 "Maintenance"` |
| `shutdown -r now` | Redémarrer maintenant | `shutdown -r now` |
| `shutdown 02:00` | Arrêter à 2h | `shutdown 02:00` |
| `shutdown -c` | Annuler shutdown | `shutdown -c` |
| `systemctl reboot` | Redémarrer (systemd) | `sudo systemctl reboot` |
| `systemctl poweroff` | Éteindre (systemd) | `sudo systemctl poweroff` |
| `wall "message"` | Envoyer message à tous | `wall "Serveur en maintenance"` |

---

## 🎓 Points Clés pour l'Examen

✅ **Upstart location** : `/etc/init/` (vs SysV `/etc/init.d/`)  
✅ **initctl list** : Lister services Upstart avec état et PID  
✅ **Compatibilité** : Upstart comprend runlevel et telinit (SysV)  
✅ **shutdown bloque** : Nouveaux logins interdits après shutdown  
✅ **Signaux** : SIGTERM (gentil) puis SIGKILL (forcé)  
✅ **Temps shutdown** : `HH:MM`, `+minutes`, `now`  
✅ **shutdown -r** : Reboot  
✅ **shutdown -h** : Halt (arrêt)  
✅ **shutdown -c** : Cancel (annuler)  
✅ **shutdown -a** : Vérifier `/etc/shutdown.allow` (SysV)  
✅ **systemctl reboot/poweroff** : Nécessite privilèges root  
✅ **wall** : Envoyer message à tous les terminaux connectés  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Maintenance programmée avec avertissements

```bash
# T-15 minutes : Premier avertissement
wall "⚠️  Maintenance dans 15 minutes - Sauvegardez vos travaux !"

# T-10 minutes : Programmer le shutdown
shutdown -h +10 "Maintenance système - Le serveur s'arrêtera dans 10 minutes"

# Les utilisateurs reçoivent :
# 1. Message wall immédiatement
# 2. Messages shutdown automatiques à -10, -5, -1 minutes
```

---

### Scénario 2 : Annuler un shutdown par erreur

```bash
# Oups, mauvaise commande !
shutdown -h now

# VITE ! Annuler (si vous avez encore le temps)
shutdown -c

# Message diffusé :
# "System shutdown cancelled"
```

---

### Scénario 3 : Redémarrage programmé la nuit

```bash
# Programmer pour 3h du matin
shutdown -r 03:00 "Mise à jour kernel - redémarrage automatique"

# Le système :
# 1. Continue de fonctionner normalement
# 2. À 2h50 : avertit les utilisateurs connectés
# 3. À 3h00 : redémarre
```

---

### Scénario 4 : Restreindre Ctrl+Alt+Del (SysV)

```bash
# 1. Éditer /etc/inittab
sudo vim /etc/inittab

# 2. Modifier la ligne ctrlaltdel
ca::ctrlaltdel:/sbin/shutdown -t3 -r -a now

# 3. Créer la liste blanche
echo "root" | sudo tee /etc/shutdown.allow
echo "admin" | sudo tee -a /etc/shutdown.allow

# 4. Recharger
sudo telinit q

# Maintenant : seuls root et admin peuvent redémarrer avec Ctrl+Alt+Del
```

---

### Scénario 5 : Message d'urgence à tous les utilisateurs

```bash
# Créer un message stylisé
cat > /tmp/urgent.txt << 'EOF'
╔═══════════════════════════════════════╗
║          ⚠️  ALERTE SÉCURITÉ ⚠️       ║
║                                       ║
║  Activité suspecte détectée          ║
║  Changez votre mot de passe          ║
║  immédiatement !                     ║
║                                       ║
║  Contact: security@example.com       ║
╚═══════════════════════════════════════╝
EOF

# Envoyer à tous
wall < /tmp/urgent.txt
```

---

### Scénario 6 : Fake shutdown pour test

```bash
# Tester la réaction des utilisateurs sans vraiment arrêter
shutdown -k +5 "ATTENTION : Le système va redémarrer dans 5 minutes !"

# Résultat :
# ✅ Message envoyé à tous
# ✅ Utilisateurs paniquent (peut-être)
# ❌ Système ne s'arrête PAS réellement

# 5 minutes plus tard... rien ne se passe !
```

---

### Scénario 7 : Comparaison shutdown vs systemctl

```bash
# Méthode traditionnelle (portable)
shutdown -r +5 "Redémarrage pour mise à jour"

# Méthode systemd (moderne, immédiat)
sudo systemctl reboot

# Les deux fonctionnent, choisir selon :
# - shutdown : si délai nécessaire ou compatibilité
# - systemctl : si systemd et action immédiate
```

---

## 🔄 Tableau Récapitulatif Init Systems

| Caractéristique | SysVinit | Upstart | systemd |
|-----------------|----------|---------|---------|
| **Config** | `/etc/inittab` | `/etc/init/` | `/lib/systemd/system/` |
| **Scripts** | `/etc/init.d/` | `/etc/init/` | Units |
| **Lister services** | `ls /etc/init.d/` | `initctl list` | `systemctl list-units` |
| **Démarrer** | `/etc/init.d/ssh start` | `start ssh` | `systemctl start ssh` |
| **Arrêter** | `/etc/init.d/ssh stop` | `stop ssh` | `systemctl stop ssh` |
| **Statut** | `/etc/init.d/ssh status` | `status ssh` | `systemctl status ssh` |
| **Runlevels** | 0-6 | 0-6 (compatibilité) | Targets |
| **Changer runlevel** | `telinit 3` | `telinit 3` | `systemctl isolate multi-user.target` |
| **Parallélisation** | ❌ Non | ✅ Oui | ✅ Oui |
| **Encore utilisé ?** | ⚠️ Rare | ❌ Abandonné | ✅ Standard |

---

**🎯 Prochaine étape** : Quiz et Ressources du cours System Architecture

*Dernière mise à jour: 22 janvier 2026*
