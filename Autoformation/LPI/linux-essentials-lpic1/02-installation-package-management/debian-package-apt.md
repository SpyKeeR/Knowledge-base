# 📦 Gestion de Paquets Debian - APT

---

## 🎯 APT : Advanced Package Tool

**APT** = Front-end **intelligent** de dpkg

**Analogie** 🏪 :
- **dpkg** = Caissier (gère 1 paquet à la fois)
- **apt** = Manager du magasin (gère stock, commandes, dépendances)

**Différence clé** :

| Critère | dpkg | apt |
|---------|------|-----|
| Niveau | Bas | Haut |
| Dépendances | ❌ Ne résout pas | ✅ Résout automatiquement |
| Téléchargement | ❌ Non | ✅ Depuis repositories |
| Usage | Fichier `.deb` local | Nom de paquet |
| Exemple | `dpkg -i chrome.deb` | `apt install vim` |

**💡 Point clé** : apt **utilise** dpkg en arrière-plan

---

## 🌐 Repositories (Dépôts)

**Repository** = Serveur contenant des **milliers de paquets**

**Analogie** 📚 : Bibliothèque en ligne de logiciels

**Types de repositories** :
- ✅ **Officiels** : Debian, Ubuntu
- ✅ **Tiers** : Google Chrome, Docker, Microsoft

**Avantages** :
- ✅ Installation par nom (pas besoin télécharger .deb)
- ✅ Mises à jour centralisées
- ✅ Vérification signatures (sécurité)
- ✅ Résolution dépendances

**Exemple** :
```bash
# Avec dpkg (manuel)
wget https://example.com/paquet.deb
sudo dpkg -i paquet.deb
sudo apt --fix-broken install

# Avec apt (automatique)
sudo apt install paquet
# Télécharge + dépendances + installe
```

**💡 Toujours préférer apt** quand disponible

---

## 🛠️ Commandes APT : Famille apt-*

### Vue d'Ensemble

**Commandes historiques** (toujours utilisées) :
- `apt-get` → Installation, màj, suppression
- `apt-cache` → Recherche, informations
- `apt-file` → Recherche fichiers dans paquets

**Commande moderne** :
- `apt` → Surcouche simplifiée (combine apt-get + apt-cache)

**💡 Focus examen** : Connaître `apt-get` et `apt-cache` (universels)

**Équivalences** :

| apt moderne | apt-get / apt-cache |
|-------------|---------------------|
| `apt install` | `apt-get install` |
| `apt remove` | `apt-get remove` |
| `apt update` | `apt-get update` |
| `apt upgrade` | `apt-get upgrade` |
| `apt search` | `apt-cache search` |
| `apt show` | `apt-cache show` |

**⚠️ apt peut être absent** sur vieux systèmes → Utiliser apt-get

---

## 📥 apt-get install : Installer un Paquet

### Syntaxe

```bash
apt-get install nom-paquet
```

**Rôle** : Télécharger + installer paquet + **dépendances**

---

### Exemple Simple

```bash
sudo apt-get install vim
```

**Résultat** :
```
Lecture des listes de paquets... Fait
Construction de l'arbre des dépendances... Fait
Les NOUVEAUX paquets suivants seront installés :
  vim vim-common vim-runtime
0 mis à jour, 3 nouvellement installés, 0 à enlever et 0 non mis à jour.
Il est nécessaire de prendre 8 234 ko dans les archives.
Après cette opération, 36,8 Mo d'espace disque supplémentaires seront utilisés.
Souhaitez-vous continuer ? [O/n] O
```

**Étapes** :
1. ✅ Vérifier si paquet existe dans repositories
2. ✅ Calculer dépendances
3. ✅ Afficher résumé (paquets + taille)
4. ✅ Demander confirmation
5. ✅ Télécharger tous paquets
6. ✅ Installer via dpkg

---

### Installation Multiple

```bash
sudo apt-get install apache2 mysql-server php
```

**Effet** : Installe **3 paquets** + leurs dépendances en une commande

---

### Installation Sans Confirmation

```bash
sudo apt-get install -y vim
#                      │
#                      └─── -y = yes (assume yes)
```

**Usage** : Scripts automatisés, Dockerfiles

---

### Installer Version Spécifique

```bash
sudo apt-get install paquet=version
```

**Exemple** :
```bash
sudo apt-get install vim=2:8.1.2269-1ubuntu5
```

**💡 Voir versions disponibles** :
```bash
apt-cache policy vim
```

---

## 🗑️ apt-get remove : Désinstaller

### Syntaxe

```bash
apt-get remove nom-paquet
```

**Rôle** : Supprimer paquet + **dépendances inutilisées**

**⚠️ Important** : Garde fichiers de **configuration**

---

### Exemple

```bash
sudo apt-get remove apache2
```

**Résultat** :
```
Les paquets suivants seront ENLEVÉS :
  apache2 apache2-bin apache2-data apache2-utils
0 mis à jour, 0 nouvellement installés, 4 à enlever et 0 non mis à jour.
Après cette opération, 2 345 ko d'espace disque seront libérés.
```

**Ce qui reste** :
- `/etc/apache2/` → Fichiers configuration

**💡 Usage** : Désinstaller temporairement (réinstaller sans reconfigurer)

---

### Supprimer Dépendances Orphelines

```bash
sudo apt-get autoremove
```

**Rôle** : Supprimer paquets installés automatiquement (dépendances) mais **plus nécessaires**

**Exemple** :
```bash
# 1. Installer apache2 (installe dépendances)
sudo apt-get install apache2

# 2. Supprimer apache2
sudo apt-get remove apache2

# 3. Nettoyer dépendances orphelines
sudo apt-get autoremove
# Supprime apache2-bin, apache2-data, etc.
```

**💡 Bonne pratique** : Exécuter après chaque remove

---

## 🧹 apt-get purge : Purger Complètement

### Syntaxe

```bash
apt-get purge nom-paquet
```

**Rôle** : Supprimer paquet + **configuration**

**Équivalent** : `dpkg -P`

---

### Exemple

```bash
sudo apt-get purge apache2
```

**Résultat** :
```
Les paquets suivants seront ENLEVÉS :
  apache2* apache2-bin* apache2-data* apache2-utils*
0 mis à jour, 0 nouvellement installés, 4 à enlever et 0 non mis à jour.
Après cette opération, 2 345 ko d'espace disque seront libérés.
```

**Astérisque (*)** = Configuration sera supprimée

**Ce qui est supprimé** :
- ✅ Binaires
- ✅ Bibliothèques
- ✅ **Configuration `/etc/`**

**Usage** : Nettoyage complet

---

### Purge + Autoremove

```bash
sudo apt-get purge apache2
sudo apt-get autoremove --purge
#                        │
#                        └─── Purge aussi les dépendances
```

**💡 Nettoyage maximal**

---

## 🔧 apt-get install -f : Fix Broken

### Syntaxe

```bash
apt-get install -f
```

**Rôle** : **F**ixer problèmes de dépendances

**Alias** : `apt-get install --fix-broken`

---

### Scénario Typique

```bash
# 1. Installer .deb qui échoue
sudo dpkg -i paquet.deb
# dpkg: problèmes de dépendances...

# 2. Réparer
sudo apt-get install -f
```

**Résultat** :
```
Correction des dépendances... Fait
Les paquets suivants ont été installés automatiquement et ne sont plus nécessaires :
  libdep1 libdep2
Les NOUVEAUX paquets suivants seront installés :
  paquet-manquant
```

**Effet** :
- ✅ Installe dépendances manquantes
- ✅ Configure paquets partiellement installés
- ✅ Ou propose de supprimer paquet problématique

**💡 Commande de secours** après erreur dpkg

---

## 🔄 apt-get update : Mettre à Jour Liste Paquets

### Syntaxe

```bash
apt-get update
```

**Rôle** : Télécharger **liste des paquets** depuis repositories

**Analogie** 📋 : Mettre à jour le catalogue du magasin

**⚠️ NE FAIT PAS** : Installer/mettre à jour paquets

---

### Exemple

```bash
sudo apt-get update
```

**Résultat** :
```
Atteint:1 http://fr.archive.ubuntu.com/ubuntu jammy InRelease
Réception de:2 http://fr.archive.ubuntu.com/ubuntu jammy-updates InRelease [119 kB]
Réception de:3 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
Réception de:4 http://fr.archive.ubuntu.com/ubuntu jammy-backports InRelease [107 kB]
336 kB réceptionnés en 2s (168 kB/s)
Lecture des listes de paquets... Fait
```

**Ce qui est mis à jour** :
- `/var/lib/apt/lists/` → Listes de paquets disponibles

**Quand l'utiliser ?**
- ✅ **Avant** tout `apt-get install` (bonnes pratiques)
- ✅ Après ajout nouveau repository
- ✅ Régulièrement (1×/jour sur serveur)

**💡 Workflow standard** :
```bash
sudo apt-get update
sudo apt-get install paquet
```

---

## ⬆️ apt-get upgrade : Mettre à Jour Paquets

### Syntaxe

```bash
apt-get upgrade
```

**Rôle** : Mettre à jour **tous** les paquets installés

**⚠️ Pré-requis** : Exécuter `apt-get update` **avant**

---

### Exemple

```bash
sudo apt-get update
sudo apt-get upgrade
```

**Résultat** :
```
Lecture des listes de paquets... Fait
Construction de l'arbre des dépendances... Fait
Calcul de la mise à jour... Fait
Les paquets suivants ont été conservés :
  linux-image-generic linux-headers-generic
Les paquets suivants seront mis à jour :
  vim vim-common vim-runtime firefox libc6 libssl3
6 mis à jour, 0 nouvellement installés, 0 à enlever et 2 non mis à jour.
Il est nécessaire de prendre 45,6 Mo dans les archives.
Après cette opération, 123 ko d'espace disque supplémentaires seront utilisés.
Souhaitez-vous continuer ? [O/n] O
```

**Comportement** :
- ✅ Mise à jour paquets (versions mineures)
- ⚠️ **Ne supprime PAS** de paquets
- ⚠️ **Ne change PAS** dépendances majeures

**Paquets "conservés"** : Nécessitent install/suppression autres paquets

---

### apt-get dist-upgrade (Avancé)

```bash
sudo apt-get dist-upgrade
```

**Rôle** : Mise à jour **intelligente** (peut ajouter/supprimer paquets)

**Différence** :

| Commande | Ajoute paquets | Supprime paquets | Usage |
|----------|----------------|------------------|-------|
| `upgrade` | ❌ | ❌ | Màj sécurisée |
| `dist-upgrade` | ✅ | ✅ | Màj distribution |

**Exemple** :
```bash
# Mise à jour Ubuntu 22.04 → 22.04.1
sudo apt-get update
sudo apt-get dist-upgrade
```

**💡 Pour montée version** (22.04 → 24.04) : Utiliser `do-release-upgrade`

---

### Mettre à Jour Paquet Spécifique

```bash
sudo apt-get install --only-upgrade nom-paquet
```

**Exemple** :
```bash
# Seulement vim
sudo apt-get install --only-upgrade vim
```

**Ou simplement** :
```bash
sudo apt-get install vim
# Si déjà installé → met à jour
```

---

## 🧽 apt-get clean : Nettoyer Cache

### Comprendre le Cache

**Emplacement cache** :
```
/var/cache/apt/archives/        → Paquets .deb téléchargés
/var/cache/apt/archives/partial/ → Téléchargements en cours
```

**Exemple** :
```bash
ls /var/cache/apt/archives/
# vim_8.1.2269-1ubuntu5_amd64.deb
# apache2_2.4.52-1ubuntu4_amd64.deb
# firefox_108.0+build2-0ubuntu0.22.04.1_amd64.deb
```

**Problème** : Cache peut atteindre **plusieurs Go**

---

### apt-get clean

```bash
apt-get clean
```

**Rôle** : Supprimer **tout** le cache

**Résultat** :
```bash
# Avant
du -sh /var/cache/apt/archives/
# 2,3G

# Après clean
sudo apt-get clean
du -sh /var/cache/apt/archives/
# 0
```

**⚠️ Sans danger** : Paquets seront re-téléchargés si besoin

---

### apt-get autoclean

```bash
apt-get autoclean
```

**Rôle** : Supprimer **uniquement** paquets obsolètes (anciennes versions)

**Différence** :

| Commande | Supprime | Garde |
|----------|----------|-------|
| `clean` | Tout | Rien |
| `autoclean` | Anciennes versions | Versions actuelles |

**Usage** : `autoclean` = nettoyage régulier, `clean` = libérer espace max

---

## 🔍 apt-cache search : Rechercher Paquet

### Syntaxe

```bash
apt-cache search mot-clé
```

**Rôle** : Chercher paquets par **nom** ou **description**

---

### Exemple

```bash
apt-cache search web server
```

**Résultat** :
```
apache2 - Apache HTTP Server
nginx - small, powerful, scalable web/proxy server
lighttpd - fast webserver with minimal memory footprint
tomcat9 - Apache Tomcat 9 - Servlet and JSP engine
nodejs - evented I/O for V8 javascript - runtime executable
```

**Format** : `nom-paquet - description`

---

### Recherche Précise

```bash
# Chercher "nginx" exactement
apt-cache search ^nginx$

# Tous paquets Python
apt-cache search python | grep ^python

# Paquets contenant "compiler"
apt-cache search compiler
```

---

### Filtrer Résultats

```bash
# Chercher + compter
apt-cache search editor | wc -l
# 234

# Chercher + trier
apt-cache search database | sort

# Chercher paquets installés seulement
apt-cache search mysql | grep -f <(dpkg -l | grep ^ii | awk '{print $2}')
```

---

## 📄 apt-cache show : Détails Paquet

### Syntaxe

```bash
apt-cache show nom-paquet
```

**Rôle** : Afficher **informations détaillées** sur un paquet

**Similaire à** : `dpkg -I paquet.deb` (mais pour paquets repository)

---

### Exemple

```bash
apt-cache show vim
```

**Résultat** :
```
Package: vim
Version: 2:8.2.3995-1ubuntu2.1
Architecture: amd64
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Installed-Size: 3570
Depends: vim-common (= 2:8.2.3995-1ubuntu2.1), vim-runtime (= 2:8.2.3995-1ubuntu2.1), libacl1 (>= 2.2.23), libc6 (>= 2.34), libgpm2 (>= 1.20.7), libpython3.10 (>= 3.10.0), libselinux1 (>= 3.1~), libsodium23 (>= 1.0.14), libtinfo6 (>= 6)
Suggests: ctags, vim-doc, vim-scripts
Homepage: https://www.vim.org/
Priority: optional
Section: editors
Filename: pool/main/v/vim/vim_8.2.3995-1ubuntu2.1_amd64.deb
Size: 1719324
MD5sum: 7f3b8b4b5c8d9e1f2a3c4d5e6f7a8b9c
SHA256: a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0u1v2w3x4y5z6
Description: Vi IMproved - enhanced vi editor
 Vim is an almost compatible version of the UNIX editor Vi.
 .
 Many new features have been added: multi level undo, syntax
 highlighting, command line history, on-line help, filename
 completion, block operations, folding, Unicode support, etc.
```

**Sections importantes** :
- **Version** : Numéro version disponible
- **Depends** : Dépendances requises
- **Suggests** : Paquets recommandés
- **Size** : Taille téléchargement (octets)
- **Description** : Détails paquet

---

### Cas d'Usage

#### Vérifier Version Disponible

```bash
apt-cache show nginx | grep Version
# Version: 1.18.0-0ubuntu1.4
```

---

#### Voir Dépendances Avant Install

```bash
apt-cache show apache2 | grep Depends
# Depends: apache2-bin (= 2.4.52-1ubuntu4), apache2-data (= 2.4.52-1ubuntu4), ...
```

---

#### Comparer Versions

```bash
# Version installée
dpkg -l vim | grep ^ii
# ii  vim  2:8.1.2269-1ubuntu5

# Version disponible
apt-cache show vim | grep Version
# Version: 2:8.2.3995-1ubuntu2.1

# Mise à jour dispo ? Oui !
```

---

### apt-cache policy (Alternative)

```bash
apt-cache policy vim
```

**Résultat** :
```
vim:
  Installé : 2:8.1.2269-1ubuntu5
  Candidat : 2:8.2.3995-1ubuntu2.1
 Table de version :
     2:8.2.3995-1ubuntu2.1 500
        500 http://fr.archive.ubuntu.com/ubuntu jammy-updates/main amd64 Packages
 *** 2:8.1.2269-1ubuntu5 100
        100 /var/lib/dpkg/status
```

**Avantage** : Montre version installée **ET** disponible

---

## 📂 /etc/apt/sources.list : Configuration Repositories

### Fichier Principal

**Emplacement** : `/etc/apt/sources.list`

**Rôle** : Définir **sources** (repositories) des paquets

---

### Exemple Contenu

```bash
cat /etc/apt/sources.list
```

**Résultat** :
```
deb http://fr.archive.ubuntu.com/ubuntu/ jammy main restricted
deb http://fr.archive.ubuntu.com/ubuntu/ jammy-updates main restricted
deb http://fr.archive.ubuntu.com/ubuntu/ jammy universe
deb http://fr.archive.ubuntu.com/ubuntu/ jammy-updates universe
deb http://fr.archive.ubuntu.com/ubuntu/ jammy multiverse
deb http://fr.archive.ubuntu.com/ubuntu/ jammy-updates multiverse
deb http://security.ubuntu.com/ubuntu jammy-security main restricted
deb http://security.ubuntu.com/ubuntu jammy-security universe
deb http://security.ubuntu.com/ubuntu jammy-security multiverse
```

---

### Format Ligne Repository

```
deb http://repository/ubuntu/ codename depot1 depot2 depot3
│   │                         │        │
│   │                         │        └─── Composants (main, universe...)
│   │                         └──────────── Nom de code distribution
│   └────────────────────────────────────── URL repository
└────────────────────────────────────────── Type (deb ou deb-src)
```

---

### Composants de la Ligne

#### 1. Type Repository

| Type | Contenu |
|------|---------|
| `deb` | Paquets **binaires** (.deb) |
| `deb-src` | Code **source** des paquets |

**Exemple** :
```
deb     http://archive.ubuntu.com/ubuntu jammy main
deb-src http://archive.ubuntu.com/ubuntu jammy main
```

**💡 Usage** : `deb-src` pour développeurs (compiler depuis source)

---

#### 2. URL Repository

**Formats** :
```
http://archive.ubuntu.com/ubuntu/
https://deb.debian.org/debian/
ftp://ftp.fr.debian.org/debian/
file:///mnt/local-repo/
```

**Miroirs** : Choisir proche géographiquement (plus rapide)
- 🇫🇷 `fr.archive.ubuntu.com`
- 🇺🇸 `us.archive.ubuntu.com`
- 🇩🇪 `de.archive.ubuntu.com`

---

#### 3. Codename (Nom de Code Distribution)

**Ubuntu** :

| Codename | Version | Année |
|----------|---------|-------|
| `jammy` | 22.04 LTS | 2022 |
| `focal` | 20.04 LTS | 2020 |
| `bionic` | 18.04 LTS | 2018 |
| `noble` | 24.04 LTS | 2024 |

**Debian** :

| Codename | Version | Année |
|----------|---------|-------|
| `bookworm` | 12 | 2023 |
| `bullseye` | 11 | 2021 |
| `buster` | 10 | 2019 |

**Suffixes** :
- `jammy` → Version actuelle
- `jammy-updates` → Mises à jour
- `jammy-security` → Correctifs sécurité
- `jammy-backports` → Versions récentes backportées

---

#### 4. Composants (Sous-dépôts)

### Ubuntu

| Composant | Description |
|-----------|-------------|
| `main` | Logiciels **open-source** officiellement supportés |
| `restricted` | Logiciels **closed-source** officiels (drivers propriétaires) |
| `universe` | Logiciels open-source **maintenus par communauté** |
| `multiverse` | Logiciels closed-source ou **patentés** non supportés |

**Exemple** :
```
deb http://archive.ubuntu.com/ubuntu jammy main restricted universe multiverse
```

**💡 Support Ubuntu** :
- ✅ `main` + `restricted` → Support officiel complet
- ⚠️ `universe` + `multiverse` → Support communautaire

---

### Debian

| Composant | Description |
|-----------|-------------|
| `main` | Conforme **Debian Free Software Guidelines** (DFSG) |
| `contrib` | DFSG mais **dépendances** dans non-free |
| `non-free` | **Non conforme** DFSG (licences propriétaires) |
| `security` | **Correctifs sécurité** |
| `backports` | Versions **plus récentes** backportées |

**Exemple** :
```
deb http://deb.debian.org/debian bookworm main contrib non-free
deb http://security.debian.org/debian-security bookworm-security main
deb http://deb.debian.org/debian bookworm-backports main
```

**💡 backports** : Installer version récente sans changer de distribution

---

## 📁 /etc/apt/sources.list.d/ : Repositories Tiers

### Organisation Modulaire

**Répertoire** : `/etc/apt/sources.list.d/`

**Rôle** : Fichiers `.list` **additionnels** (1 fichier = 1 repository tiers)

**Avantage** :
- ✅ Séparation sources officielles / tiers
- ✅ Facile ajouter/retirer repository
- ✅ Paquets peuvent installer leur `.list`

---

### Exemple Structure

```bash
ls /etc/apt/sources.list.d/
```

**Résultat** :
```
docker.list
google-chrome.list
microsoft-prod.list
nodejs.list
```

---

### Exemple Contenu

```bash
cat /etc/apt/sources.list.d/docker.list
```

**Résultat** :
```
deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu jammy stable
```

**Options avancées** :
- `arch=amd64` → Architecture spécifique
- `signed-by=...` → Clé GPG pour vérification

---

### Ajouter Repository Tiers

**Méthode manuelle** :
```bash
# 1. Créer fichier
sudo nano /etc/apt/sources.list.d/custom.list

# 2. Ajouter ligne
deb http://ppa.launchpad.net/custom/ppa/ubuntu jammy main

# 3. Sauvegarder et quitter

# 4. Mettre à jour
sudo apt-get update
```

**Méthode add-apt-repository** (Ubuntu) :
```bash
# Ajouter PPA
sudo add-apt-repository ppa:custom/ppa
sudo apt-get update
```

**💡 Toujours ajouter clé GPG** (sécurité) :
```bash
# Importer clé
wget -qO - https://example.com/key.gpg | sudo apt-key add -

# Ou méthode moderne
wget -qO - https://example.com/key.gpg | sudo gpg --dearmor -o /usr/share/keyrings/example-keyring.gpg
```

---

## 🔎 apt-file : Recherche Fichiers dans Paquets

### Installation

**⚠️ Pas installé par défaut**

```bash
sudo apt-get install apt-file
```

---

### apt-file update : Initialiser Base

```bash
apt-file update
```

**Rôle** : Télécharger **base de données** des fichiers de tous paquets

**Résultat** :
```
Téléchargement des listes de fichiers... Fait
```

**⚠️ Obligatoire** avant première utilisation

**Emplacement DB** : `/var/cache/apt/apt-file/`

---

### apt-file list : Fichiers d'un Paquet

```bash
apt-file list nom-paquet
```

**Rôle** : Lister fichiers **même si paquet non installé**

**Différence avec dpkg -L** :

| Commande | Condition | Source |
|----------|-----------|--------|
| `dpkg -L paquet` | Paquet **installé** | Système local |
| `apt-file list paquet` | Paquet dans **repository** | Base apt-file |

---

### Exemple

```bash
apt-file list vim
```

**Résultat** :
```
vim: /usr/bin/vim.basic
vim: /usr/bin/vim.tiny
vim: /usr/share/applications/vim.desktop
vim: /usr/share/doc/vim/changelog.Debian.gz
vim: /usr/share/doc/vim/copyright
vim: /usr/share/icons/hicolor/48x48/apps/gvim.png
vim: /usr/share/man/man1/vim.1.gz
...
```

**Format** : `paquet: fichier`

**Usage** : Voir contenu paquet **avant** installation

---

### apt-file search : Trouver Paquet par Fichier

```bash
apt-file search /chemin/fichier
```

**Rôle** : Chercher quel paquet contient un fichier (dans **tous** repositories)

**Différence avec dpkg-query -S** :

| Commande | Scope | Condition |
|----------|-------|-----------|
| `dpkg-query -S fichier` | Paquets **installés** | Sur système |
| `apt-file search fichier` | **Tous paquets** repositories | Même non installés |

---

### Exemple

```bash
apt-file search /usr/bin/vim
```

**Résultat** :
```
vim: /usr/bin/vim.basic
vim: /usr/bin/vim.tiny
vim-nox: /usr/bin/vim.nox
vim-gtk3: /usr/bin/vim.gtk3
```

**💡 Plusieurs paquets** peuvent contenir même fichier

---

### Cas d'Usage Pratiques

#### Trouver Bibliothèque Manquante

```bash
# Programme échoue : "libfoo.so.1: not found"
apt-file search libfoo.so.1
# libfoo1: /usr/lib/x86_64-linux-gnu/libfoo.so.1

# Installer
sudo apt-get install libfoo1
```

---

#### Trouver Header Développement

```bash
# Compilation échoue : "stdio.h: No such file"
apt-file search stdio.h
# libc6-dev: /usr/include/stdio.h

# Installer
sudo apt-get install libc6-dev
```

---

#### Chercher Commande

```bash
# Commande inconnue
which docker
# (aucune sortie)

# Chercher
apt-file search /usr/bin/docker
# docker.io: /usr/bin/docker

# Installer
sudo apt-get install docker.io
```

---

### Recherche par Motif

```bash
# Tous fichiers .so contenant "ssl"
apt-file search libssl | grep '\.so'

# Fichiers Python en /usr/bin
apt-file search /usr/bin/python

# Headers contenant "curl"
apt-file search curl.h
```

---

## 📋 Antisèche - Commandes APT

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `apt-get update` | Mettre à jour liste paquets | `sudo apt-get update` |
| `apt-get install paquet` | Installer paquet | `sudo apt-get install vim` |
| `apt-get install -y paquet` | Installer sans confirmation | `sudo apt-get install -y nginx` |
| `apt-get remove paquet` | Désinstaller (garde config) | `sudo apt-get remove apache2` |
| `apt-get purge paquet` | Purger (supprime config) | `sudo apt-get purge apache2` |
| `apt-get autoremove` | Supprimer dépendances orphelines | `sudo apt-get autoremove` |
| `apt-get install -f` | Réparer dépendances cassées | `sudo apt-get install -f` |
| `apt-get upgrade` | Mettre à jour tous paquets | `sudo apt-get upgrade` |
| `apt-get dist-upgrade` | Màj avec gestion dépendances | `sudo apt-get dist-upgrade` |
| `apt-get clean` | Vider cache complet | `sudo apt-get clean` |
| `apt-get autoclean` | Vider cache (anciennes versions) | `sudo apt-get autoclean` |
| `apt-cache search mot` | Chercher paquet | `apt-cache search web server` |
| `apt-cache show paquet` | Détails paquet | `apt-cache show vim` |
| `apt-cache policy paquet` | Versions disponibles | `apt-cache policy nginx` |
| `apt-file update` | Mettre à jour DB apt-file | `apt-file update` |
| `apt-file list paquet` | Fichiers d'un paquet (repo) | `apt-file list vim` |
| `apt-file search fichier` | Quel paquet a ce fichier (repo) | `apt-file search libssl.so.1` |

---

## 🎓 Points Clés pour l'Examen

✅ **APT** : Front-end intelligent de dpkg (résout dépendances)  
✅ **Repositories** : Serveurs contenant paquets  
✅ **apt-get install** : Installer paquet + dépendances  
✅ **apt-get remove** : Désinstaller (garde config)  
✅ **apt-get purge** : Purger (supprime config)  
✅ **apt-get autoremove** : Supprimer dépendances orphelines  
✅ **apt-get install -f** : Fix dépendances cassées  
✅ **apt-get update** : Mettre à jour **liste** paquets (obligatoire avant install)  
✅ **apt-get upgrade** : Mettre à jour **tous** paquets  
✅ **apt-get clean** : Vider cache `/var/cache/apt/archives/`  
✅ **apt-cache search** : Chercher paquet par mot-clé  
✅ **apt-cache show** : Détails paquet (version, dépendances, description)  
✅ **sources.list** : `/etc/apt/sources.list` + `/etc/apt/sources.list.d/*.list`  
✅ **Format sources.list** : `deb http://repo/ codename composants`  
✅ **Ubuntu** : main, restricted, universe, multiverse  
✅ **Debian** : main, contrib, non-free, security, backports  
✅ **apt-file** : Chercher fichiers dans **tous** paquets repositories  
✅ **apt-file update** : Initialiser DB (obligatoire)  
✅ **apt-file search** : Quel paquet contient fichier (même non installé)  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Installation Standard

```bash
# 1. Mettre à jour liste paquets
sudo apt-get update

# 2. Installer
sudo apt-get install nginx

# 3. Vérifier
systemctl status nginx
# ● nginx.service - A high performance web server
#    Loaded: loaded
#    Active: active (running)
```

---

### Scénario 2 : Désinstallation Complète

```bash
# 1. Purger paquet
sudo apt-get purge apache2

# 2. Supprimer dépendances orphelines
sudo apt-get autoremove --purge

# 3. Nettoyer cache
sudo apt-get clean

# 4. Vérifier
dpkg -l | grep apache2
# (aucune sortie = OK)
```

---

### Scénario 3 : Rechercher et Installer

```bash
# 1. Chercher paquet
apt-cache search web server
# nginx - small, powerful web server
# apache2 - Apache HTTP Server

# 2. Voir détails
apt-cache show nginx
# Version: 1.18.0-0ubuntu1.4
# Depends: nginx-core...

# 3. Installer
sudo apt-get update
sudo apt-get install nginx
```

---

### Scénario 4 : Résoudre "Command not found"

```bash
# Commande introuvable
htop
# bash: htop: command not found

# Méthode 1 : Chercher paquet
apt-cache search htop
# htop - interactive process viewer

# Méthode 2 : apt-file (si installé)
apt-file search /usr/bin/htop
# htop: /usr/bin/htop

# Installer
sudo apt-get install htop
```

---

### Scénario 5 : Trouver Bibliothèque Manquante

```bash
# Programme échoue
./myapp
# error while loading shared libraries: libfoo.so.1: cannot open shared object file

# Chercher avec apt-file
apt-file search libfoo.so.1
# libfoo1: /usr/lib/x86_64-linux-gnu/libfoo.so.1

# Installer
sudo apt-get install libfoo1

# Relancer
./myapp
# ✅ Fonctionne
```

---

### Scénario 6 : Ajouter Repository Tiers (Docker)

```bash
# 1. Installer prérequis
sudo apt-get install ca-certificates curl gnupg

# 2. Ajouter clé GPG
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# 3. Ajouter repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list

# 4. Mettre à jour
sudo apt-get update

# 5. Installer
sudo apt-get install docker-ce
```

---

### Scénario 7 : Mise à Jour Système

```bash
# 1. Mettre à jour liste
sudo apt-get update

# 2. Voir paquets à mettre à jour
apt list --upgradable

# 3. Mettre à jour
sudo apt-get upgrade -y

# 4. Nettoyer
sudo apt-get autoremove
sudo apt-get autoclean
```

---

### Scénario 8 : Réparer Système Cassé

```bash
# Symptôme : apt échoue
sudo apt-get install paquet
# E: dpkg was interrupted...

# Étape 1 : Reconfigurer dpkg
sudo dpkg --configure -a

# Étape 2 : Fix dépendances
sudo apt-get install -f

# Étape 3 : Update + upgrade
sudo apt-get update
sudo apt-get upgrade

# Étape 4 : Vérifier
dpkg -l | grep ^iF
# (aucune sortie = OK)
```

---

### Scénario 9 : Chercher Fichier Header pour Compilation

```bash
# Compilation échoue
gcc myprogram.c
# fatal error: openssl/ssl.h: No such file or directory

# Chercher header
apt-file search openssl/ssl.h
# libssl-dev: /usr/include/openssl/ssl.h

# Installer package dev
sudo apt-get install libssl-dev

# Recompiler
gcc myprogram.c -lssl
# ✅ Compilation réussie
```

---

### Scénario 10 : Audit Sources Repositories

```bash
# Voir repositories actifs
cat /etc/apt/sources.list
ls /etc/apt/sources.list.d/

# Voir d'où vient un paquet
apt-cache policy vim
# vim:
#   Installé : 2:8.1.2269-1ubuntu5
#   Candidat : 2:8.2.3995-1ubuntu2.1
#  Table de version :
#      2:8.2.3995-1ubuntu2.1 500
#         500 http://fr.archive.ubuntu.com/ubuntu jammy-updates/main amd64

# Repository = jammy-updates/main
```

---

## 🔄 Workflow Complet

### Installation Depuis Repository

```
1. UPDATE
   sudo apt-get update
   → Télécharge listes paquets

2. SEARCH (optionnel)
   apt-cache search paquet
   → Trouver nom exact

3. SHOW (optionnel)
   apt-cache show paquet
   → Voir version, dépendances

4. INSTALL
   sudo apt-get install paquet
   → Télécharge + installe + dépendances

5. VERIFY
   dpkg -l paquet
   → Vérifier installation (ii)
```

---

### Désinstallation Propre

```
1. REMOVE ou PURGE
   sudo apt-get purge paquet
   → Supprime paquet + config

2. AUTOREMOVE
   sudo apt-get autoremove --purge
   → Supprime dépendances orphelines

3. CLEAN
   sudo apt-get autoclean
   → Nettoie cache

4. VERIFY
   dpkg -l paquet
   → Aucune sortie = OK
```

---

### Maintenance Régulière

```
1. UPDATE
   sudo apt-get update

2. UPGRADE
   sudo apt-get upgrade -y

3. DIST-UPGRADE (si nécessaire)
   sudo apt-get dist-upgrade

4. CLEANUP
   sudo apt-get autoremove
   sudo apt-get autoclean

5. AUDIT
   dpkg -l | grep ^iF
   → Chercher paquets cassés
```

---

## ⚠️ Erreurs Courantes

### Erreur 1 : "Unable to locate package"

```
E: Unable to locate package paquet
```

**Causes** :
- ❌ Liste paquets pas à jour
- ❌ Paquet dans repository non activé
- ❌ Faute orthographe

**Solutions** :
```bash
# 1. Update
sudo apt-get update

# 2. Chercher nom exact
apt-cache search mot-clé

# 3. Vérifier composants dans sources.list
# Ajouter universe/multiverse si Ubuntu
```

---

### Erreur 2 : "Hash Sum mismatch"

```
E: Failed to fetch http://...
  Hash Sum mismatch
```

**Cause** : Cache corrompu

**Solution** :
```bash
# Nettoyer cache
sudo rm -rf /var/lib/apt/lists/*
sudo apt-get update
```

---

### Erreur 3 : "dpkg was interrupted"

```
E: dpkg was interrupted, you must manually run 'sudo dpkg --configure -a'
```

**Cause** : Installation précédente interrompue

**Solution** :
```bash
sudo dpkg --configure -a
sudo apt-get install -f
```

---

### Erreur 4 : Dépendances Non Satisfaites

```
The following packages have unmet dependencies:
  paquet : Depends: libfoo but it is not going to be installed
```

**Solutions** :
```bash
# 1. Fix automatique
sudo apt-get install -f

# 2. Installer dépendance manuellement
sudo apt-get install libfoo

# 3. En dernier recours
sudo apt-get install paquet libfoo
```

---

### Erreur 5 : "NO_PUBKEY"

```
W: GPG error: https://... NO_PUBKEY 1234567890ABCDEF
```

**Cause** : Clé GPG repository manquante

**Solution** :
```bash
# Importer clé
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 1234567890ABCDEF

# Ou méthode moderne
wget -qO - https://repo.example.com/key.gpg | sudo gpg --dearmor -o /usr/share/keyrings/example.gpg
```

---

## 🗂️ Arborescence APT

```
/etc/apt/
├── sources.list              → Repositories principaux
├── sources.list.d/           → Repositories tiers (.list)
│   ├── docker.list
│   ├── google-chrome.list
│   └── custom.list
├── apt.conf.d/               → Configuration APT
├── preferences.d/            → Priorités paquets (pinning)
└── trusted.gpg.d/            → Clés GPG repositories

/var/lib/apt/
├── lists/                    → Listes paquets téléchargées (apt-get update)
└── periodic/                 → Logs màj automatiques

/var/cache/apt/
├── archives/                 → Paquets .deb téléchargés
│   ├── vim_8.2.3995_amd64.deb
│   └── partial/              → Téléchargements en cours
└── apt-file/                 → DB apt-file
```

---

**🎯 Prochaine étape** : Work on the Command Line

*Dernière mise à jour: 31 janvier 2026*
