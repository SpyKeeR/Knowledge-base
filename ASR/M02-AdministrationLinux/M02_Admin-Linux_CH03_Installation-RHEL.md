# M02 - Administration Linux

## CH03 - Installer RHEL

L'installation de RHEL ne pose pas de difficulté majeure. Le point d'attention principal : vérifier la compatibilité de l'architecture matérielle. Les machines récentes Intel/AMD utilisent en général une distribution **x86_64**.

🔗 Red Hat met à disposition une **HCL** (Hardware Compatibility List) listant tous les matériels certifiés.

---

### 3.1 - Les modes d'installation

#### 📀 Depuis un CD-ROM / DVD-ROM

Méthode la plus classique. Plusieurs images ISO sont disponibles en téléchargement HTTP selon les besoins.

#### 🔑 Depuis une clé USB

Les machines récentes permettent le boot USB. Des outils comme **unetbootin** créent une clé amorçable à partir d'une ISO. Certains permettent même de persister les modifications de configuration.

💡 Pratique pour avoir une distribution de secours portable, notamment sur les machines sans lecteur CD (netbooks, NetTops).

#### 🌐 Par boot réseau (PXE)

La plupart des BIOS actuels supportent l'amorçage PXE :

1. La machine émet une requête **BOOTP** pour obtenir une IP + le nom d'un fichier
2. Elle effectue une requête **TFTP** pour récupérer ce fichier
3. Le système RHEL se lance comme s'il était sur un support local

#### 🏭 Autres méthodes (déploiement de masse)

Pour déployer un grand nombre de machines :

- **FAI** (Fully Automatic Installation) : installation sans intervention
- CD personnalisés avec préconfiguration
- Outils de clonage : Symantec Ghost, Acronis True Image, **Clonezilla**, etc.

---

### 3.2 - Installation

#### 3.2.1 - Partitionnement

Le minimum recommandé lors de l'installation :

| Point de montage | Rôle |
|---|---|
| `/` | Racine du système |
| `swap` | Espace de pagination (pas un fichier d'échange comme sous Windows, c'est un espace de stockage dédié) |

📌 Un bon partitionnement facilite la récupération de données, l'organisation, et la gestion des quotas.

##### 📐 Quantité de SWAP recommandée

| RAM disponible | SWAP recommandé |
|---|---|
| < 1 Go | RAM × 1.5 |
| ≤ 2 Go | RAM × 1 |
| > 2 Go | 2 Go minimum (ajustable selon le besoin) |

#### 3.2.2 - Phases importantes de l'installation

##### 🌍 Choix de la langue et du clavier

En production, il est courant d'installer le système en **anglais** avec un **clavier français**. Raison : les messages d'erreur en anglais sont beaucoup plus faciles à rechercher sur Internet.

##### 🔐 Mot de passe root / Premier utilisateur

Deux écoles coexistent pour la gestion des privilèges admin :

**L'école du `su`** :

- On définit un mot de passe root à l'installation
- Le premier utilisateur créé n'a que des privilèges limités
- Pour élever ses droits, il faut utiliser `su -` et saisir le mot de passe root

```bash
penthium@RHEL:~$ su –
Mot de passe :
root@RHEL:~#
```

💡 Il est normal de ne rien voir s'afficher pendant la saisie du mot de passe.

**L'école du `sudo`** :

- Aucun mot de passe root n'est défini à l'installation
- Le premier utilisateur peut faire une élévation de privilèges via son propre mot de passe avec `sudo`

```bash
penthium@RHEL:~$ sudo -i
```

⚠️ **Il est fortement déconseillé de créer un seul utilisateur avec le privilège sudo sans mot de passe root.** En effet, il sera impossible de démarrer le système en mode maintenance. Des correctifs existent mais ils affaiblissent la sécurité.

🔧 Pour activer le compte root après l'installation (et permettre `su` + mode maintenance) :

```bash
penthium@RHEL:~$ sudo passwd root
```

##### 💾 Partitionner les disques

- Il est aujourd'hui conseillé de gérer les espaces disques en **LVM** (Logical Volume Manager)
- En choisissant LVM, il faut au préalable configurer un partitionnement Intel
- La partition `/boot` doit être sur une **partition primaire** (standard, pas LVM) pour des raisons de compatibilité avec le boot loader
- Le système de fichiers par défaut sur RHEL est le **XFS**

##### 📦 Sélection des logiciels

La liste des logiciels disponibles à l'installation varie selon l'utilisation ou non d'un miroir Internet.

---

### 🧪 Ateliers associés

- **Atelier 1** : Installation Oracle Linux 9 avec GUI (`srv-gui`) - partitionnement auto LVM, serveur avec GUI + outils système, mot de passe root, utilisateur standard
- **Atelier 2** : Installation Oracle Linux 9 en CLI (`srv-cli`) - partitionnement manuel (`/boot` 1 Go xfs standard, `swap` LVM, `/` 20 Go xfs LVM, `/var` 10 Go xfs LVM, `/home` restant ext4 LVM), source d'installation réseau (`http://yum.oracle.com/repo/OracleLinux/OL9/baseos/latest/x86_64`), pas de mot de passe root, utilisateur administrateur

---

### 📌 À retenir

1. Vérifier la compatibilité matérielle via la **HCL** avant installation.
2. Le SWAP est un **espace de stockage dédié**, pas un fichier d'échange.
3. Deux écoles de sécurité : `su` (mot de passe root) vs `sudo` (élévation via l'utilisateur). En production, activer les deux est recommandé.
4. `/boot` doit rester sur une **partition standard** (pas LVM) pour la compatibilité du boot loader.
5. Le système de fichiers par défaut de RHEL est **XFS**.
