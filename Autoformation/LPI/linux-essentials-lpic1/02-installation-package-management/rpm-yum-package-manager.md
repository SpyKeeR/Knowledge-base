# 📦 Gestion de Paquets RPM/YUM

---

## 🎯 Écosystème Red Hat

**Distributions concernées** :
- 🔴 **RHEL** (Red Hat Enterprise Linux)
- 🎩 **CentOS** (Community ENTerprise Operating System)
- 🔵 **Fedora**
- 🟢 **Rocky Linux**
- 🟣 **AlmaLinux**

**Format paquet** : `.rpm` (Red Hat Package Manager)

**Gestionnaires** :
- `rpm` → Bas niveau (comme dpkg)
- `yum` → Haut niveau (comme apt)
- `dnf` → Nouvelle génération (remplace yum)

**Analogie écosystèmes** :

| Debian/Ubuntu | Red Hat/CentOS |
|---------------|----------------|
| `.deb` | `.rpm` |
| `dpkg` | `rpm` |
| `apt` | `yum` / `dnf` |
| `/etc/apt/sources.list` | `/etc/yum.repos.d/` |

---

## 🔄 DNF : Nouvelle Génération

**DNF** = **D**andified **Y**UM

**Points clés** :
- ✅ **Rétrocompatible** avec yum (syntaxe identique)
- ✅ Plus rapide (meilleure résolution dépendances)
- ✅ Meilleure gestion mémoire
- ✅ Par défaut sur Fedora 22+, RHEL 8+, CentOS 8+

**Interchangeabilité** :
```bash
yum install paquet
# OU
dnf install paquet
# → Même résultat
```

**💡 Sur systèmes récents** : `yum` est souvent un alias vers `dnf`

```bash
# Vérifier
ls -l /usr/bin/yum
# lrwxrwxrwx /usr/bin/yum -> dnf-3
```

**Pour l'examen** : Connaître les **deux** commandes

---

## 🔐 RHEL : Subscription Manager

### Comprendre les Abonnements Red Hat

**RHEL** = Distribution **commerciale** (nécessite abonnement)

**Types d'abonnements** :
- 🆓 **Red Hat Developer** (gratuit, 16 systèmes)
- 💼 **Red Hat Enterprise** (payant, support)

**Pourquoi ?** Accès aux **repositories officiels** Red Hat

**Sans abonnement** :
```bash
yum install paquet
# Erreur : This system is not registered with an entitlement server
```

**Analogie** 🔑 : Abonnement = Clé pour accéder au magasin

---

### subscription-manager register : Enregistrer Système

```bash
subscription-manager register --username UTILISATEUR --password MOTDEPASSE
```

**Rôle** : Lier le système à un **compte Red Hat**

**Exemple** :
```bash
sudo subscription-manager register --username john@example.com --password MonMotDePasse123
```

**Résultat** :
```
The system has been registered with ID: a1b2c3d4-e5f6-7890-abcd-ef1234567890
The registered system name is: myserver.example.com
```

**Ce qui se passe** :
- ✅ Système identifié sur Red Hat Customer Portal
- ✅ UUID attribué
- ✅ Certificats téléchargés dans `/etc/pki/`

---

### subscription-manager attach : Attacher Abonnement

```bash
subscription-manager attach --auto
```

**Rôle** : Associer le système à un **abonnement disponible**

**Option** :
- `--auto` → Choix automatique (recommandé)
- `--pool=POOL_ID` → Pool spécifique

**Résultat** :
```
Installed Product Current Status:
Product Name: Red Hat Enterprise Linux Server
Status:       Subscribed
```

**Ce qui se passe** :
- ✅ Accès aux repositories activé
- ✅ `yum` peut télécharger paquets officiels

---

### Workflow Complet Enregistrement

```bash
# 1. Enregistrer système
sudo subscription-manager register --username user@example.com --password pass

# 2. Attacher abonnement
sudo subscription-manager attach --auto

# 3. Vérifier
sudo subscription-manager status
# Status: Current

# 4. Tester yum
sudo yum repolist
# Repos disponibles
```

---

### Commandes Utiles subscription-manager

```bash
# Voir statut abonnement
subscription-manager status

# Lister abonnements disponibles
subscription-manager list --available

# Voir abonnements consommés
subscription-manager list --consumed

# Désattacher
subscription-manager remove --all

# Désenregistrer système
subscription-manager unregister
```

---

## 📂 yum repolist : Lister Repositories

### Syntaxe

```bash
yum repolist
```

**Rôle** : Afficher repositories **actifs**

---

### Exemple

```bash
yum repolist
```

**Résultat** :
```
repo id                          repo name
rhel-8-for-x86_64-baseos-rpms    Red Hat Enterprise Linux 8 for x86_64 - BaseOS (RPMs)
rhel-8-for-x86_64-appstream-rpms Red Hat Enterprise Linux 8 for x86_64 - AppStream (RPMs)
```

**Colonnes** :
- **repo id** : Identifiant technique
- **repo name** : Nom descriptif

---

### yum repolist -v (Verbose)

```bash
yum repolist -v
```

**Résultat** (extrait) :
```
Repo-id      : rhel-8-for-x86_64-baseos-rpms
Repo-name    : Red Hat Enterprise Linux 8 for x86_64 - BaseOS (RPMs)
Repo-status  : enabled
Repo-baseurl : https://cdn.redhat.com/content/dist/rhel8/8/x86_64/baseos/os
Repo-expire  : 86400 second(s)
Repo-updated : Thu 30 Jan 2026 14:23:45
Repo-pkgs    : 4567
```

**Informations supplémentaires** :
- URL base
- Expiration cache
- Nombre de paquets

---

### yum repolist --all

```bash
yum repolist --all
```

**Rôle** : Afficher **tous** repos (actifs + désactivés)

**Résultat** :
```
repo id                          repo name                                          status
rhel-8-for-x86_64-baseos-rpms    Red Hat Enterprise Linux 8 - BaseOS               enabled
rhel-8-for-x86_64-appstream-rpms Red Hat Enterprise Linux 8 - AppStream            enabled
codeready-builder-for-rhel-8     CodeReady Linux Builder for RHEL 8                disabled
rhel-8-supplementary             Red Hat Enterprise Linux 8 Supplementary          disabled
```

**Colonne status** : `enabled` ou `disabled`

**Usage** : Voir repos disponibles mais non activés

---

## 🔓 Activer/Désactiver Repositories

### Méthode 1 : subscription-manager (RHEL)

**Pour repositories Red Hat officiels**

#### Activer

```bash
subscription-manager repos --enable nom-repo
```

**Exemple** :
```bash
sudo subscription-manager repos --enable codeready-builder-for-rhel-8-x86_64-rpms
```

**Résultat** :
```
Repository 'codeready-builder-for-rhel-8-x86_64-rpms' is enabled for this system.
```

---

#### Désactiver

```bash
subscription-manager repos --disable nom-repo
```

**Exemple** :
```bash
sudo subscription-manager repos --disable rhel-8-supplementary
```

---

#### Lister Repos Disponibles

```bash
subscription-manager repos --list
```

**Résultat** :
```
Available Repositories in /etc/yum.repos.d/redhat.repo
Repo ID:   rhel-8-for-x86_64-baseos-rpms
Repo Name: Red Hat Enterprise Linux 8 for x86_64 - BaseOS (RPMs)
Enabled:   1

Repo ID:   codeready-builder-for-rhel-8-x86_64-rpms
Repo Name: CodeReady Linux Builder for RHEL 8
Enabled:   0
```

---

### Méthode 2 : yum-config-manager (Universel)

**Prérequis** : Paquet `yum-utils`

```bash
sudo yum install yum-utils
```

#### Activer

```bash
yum-config-manager --enable nom-repo
```

**Exemple** :
```bash
sudo yum-config-manager --enable powertools
```

---

#### Désactiver

```bash
yum-config-manager --disable nom-repo
```

**Exemple** :
```bash
sudo yum-config-manager --disable extras
```

---

### Méthode 3 : dnf config-manager (DNF)

**Sur systèmes récents** (RHEL 8+, Fedora)

#### Activer

```bash
dnf config-manager --set-enabled nom-repo
```

**Exemple** :
```bash
sudo dnf config-manager --set-enabled powertools
```

---

#### Désactiver

```bash
dnf config-manager --set-disabled nom-repo
```

**Exemple** :
```bash
sudo dnf config-manager --set-disabled extras
```

---

### Comparaison Méthodes

| Méthode | Commande | Scope | Disponibilité |
|---------|----------|-------|---------------|
| subscription-manager | `subscription-manager repos --enable` | Repos **Red Hat** officiels | RHEL uniquement |
| yum-config-manager | `yum-config-manager --enable` | **Tous** repos | Nécessite yum-utils |
| dnf config-manager | `dnf config-manager --set-enabled` | **Tous** repos | RHEL 8+, Fedora |

**💡 Pour l'examen** : Connaître les 3 méthodes

---

## ➕ Ajouter Repository Tiers

### yum-config-manager --add-repo

```bash
yum-config-manager --add-repo URL_REPO/repo.repo
```

**Rôle** : Télécharger fichier `.repo` et l'ajouter à `/etc/yum.repos.d/`

---

### Exemple : Docker Repository

```bash
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

**Résultat** :
```
Adding repo from: https://download.docker.com/linux/centos/docker-ce.repo
```

**Fichier créé** : `/etc/yum.repos.d/docker-ce.repo`

---

### Exemple : EPEL (Extra Packages for Enterprise Linux)

**EPEL** = Repository communautaire populaire

```bash
# RHEL/CentOS 8
sudo yum install epel-release

# OU manuellement
sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
```

**Effet** : Ajoute `/etc/yum.repos.d/epel.repo`

---

## 📝 Format Fichier .repo

### Emplacement

```
/etc/yum.repos.d/*.repo
```

**Similaire à** : `/etc/apt/sources.list.d/` (Debian)

---

### Structure Fichier .repo

```ini
[repoid]
name=Nom Complet du Repository
baseurl=https://repo.example.com/centos/$releasever/$basearch/
enabled=1
gpgcheck=1
gpgkey=https://repo.example.com/RPM-GPG-KEY-repo
```

---

### Explication Champs

| Champ | Valeur | Description |
|-------|--------|-------------|
| `[repoid]` | Identifiant unique | Nom court (sans espaces) |
| `name` | Texte descriptif | Nom affiché par `yum repolist` |
| `baseurl` | URL | Chemin base du repository |
| `enabled` | `0` ou `1` | `1` = activé, `0` = désactivé |
| `gpgcheck` | `0` ou `1` | `1` = vérifier signature GPG (recommandé) |
| `gpgkey` | URL ou chemin | Clé GPG publique du repo |

---

### Variables Disponibles

| Variable | Signification | Exemple |
|----------|---------------|---------|
| `$releasever` | Version majeure OS | `8` (RHEL 8) |
| `$basearch` | Architecture | `x86_64`, `aarch64` |
| `$arch` | Architecture détaillée | `i386`, `i686` |

**Exemple URL** :
```
https://mirror.example.com/centos/$releasever/$basearch/os/
→ https://mirror.example.com/centos/8/x86_64/os/
```

---

### Exemple Réel : Docker

```bash
cat /etc/yum.repos.d/docker-ce.repo
```

**Résultat** :
```ini
[docker-ce-stable]
name=Docker CE Stable - $basearch
baseurl=https://download.docker.com/linux/centos/$releasever/$basearch/stable
enabled=1
gpgcheck=1
gpgkey=https://download.docker.com/linux/centos/gpg

[docker-ce-stable-debuginfo]
name=Docker CE Stable - Debuginfo $basearch
baseurl=https://download.docker.com/linux/centos/$releasever/debug-$basearch/stable
enabled=0
gpgcheck=1
gpgkey=https://download.docker.com/linux/centos/gpg
```

**Plusieurs sections** : 1 fichier peut contenir plusieurs repos

---

### Créer Fichier .repo Manuellement

```bash
# 1. Créer fichier
sudo nano /etc/yum.repos.d/custom.repo

# 2. Ajouter contenu
[custom-repo]
name=My Custom Repository
baseurl=https://repo.example.com/centos/8/x86_64/
enabled=1
gpgcheck=1
gpgkey=https://repo.example.com/RPM-GPG-KEY

# 3. Sauvegarder (Ctrl+O, Ctrl+X)

# 4. Recharger cache
sudo yum makecache
```

---

## 🔄 yum makecache : Recharger Métadonnées

### Syntaxe

```bash
yum makecache
```

**Rôle** : Télécharger **métadonnées** des repositories

**Équivalent** : `apt-get update` (Debian)

**Analogie** 📋 : Mettre à jour le catalogue du magasin

---

### Exemple

```bash
sudo yum makecache
```

**Résultat** :
```
Red Hat Enterprise Linux 8 for x86_64 - BaseOS    1.2 MB/s | 2.4 MB     00:02
Red Hat Enterprise Linux 8 for x86_64 - AppStream 1.5 MB/s | 3.8 MB     00:02
Metadata cache created.
```

**Ce qui est téléchargé** :
- Liste paquets disponibles
- Métadonnées (versions, dépendances)
- Clés GPG

**Cache stocké** : `/var/cache/yum/` ou `/var/cache/dnf/`

**Quand utiliser ?**
- ✅ Après ajout nouveau repo
- ✅ Après activation/désactivation repo
- ✅ Régulièrement (automatique lors install/update)

---

## 🔍 yum search : Rechercher Paquet

### Syntaxe

```bash
yum search mot-clé
```

**Rôle** : Chercher paquets par **nom** ou **description**

**Équivalent** : `apt-cache search` (Debian)

---

### Exemple

```bash
yum search 'web server'
```

**Résultat** :
```
==================== Name & Summary Matched: web server ====================
httpd.x86_64 : Apache HTTP Server
nginx.x86_64 : High performance web server
lighttpd.x86_64 : Lightning fast webserver with light system requirements

======================= Summary Matched: web server =======================
tomcat.noarch : Apache Servlet/JSP Engine
nodejs.x86_64 : JavaScript runtime
```

**Sections** :
- **Name & Summary Matched** : Mot-clé dans nom **et** description
- **Summary Matched** : Mot-clé seulement dans description

---

### Recherche Précise

```bash
# Chercher exactement "httpd"
yum search httpd

# Chercher avec wildcards
yum search 'python3*'

# Plusieurs mots-clés
yum search apache server
```

---

## ℹ️ yum info : Informations Paquet

### Syntaxe

```bash
yum info nom-paquet
```

**Rôle** : Afficher **détails** d'un paquet

**Équivalent** : `apt-cache show` (Debian)

---

### Exemple

```bash
yum info httpd
```

**Résultat** :
```
Available Packages
Name         : httpd
Version      : 2.4.51
Release      : 7.el8_5
Architecture : x86_64
Size         : 1.5 M
Source       : httpd-2.4.51-7.el8_5.src.rpm
Repository   : rhel-8-for-x86_64-appstream-rpms
Summary      : Apache HTTP Server
URL          : https://httpd.apache.org/
License      : ASL 2.0
Description  : The Apache HTTP Server is a powerful, efficient, and extensible
             : web server.
```

**Champs importants** :
- **Version** : Numéro version
- **Repository** : D'où vient le paquet
- **Size** : Taille téléchargement
- **Description** : Détails fonctionnalités

---

### Paquet Déjà Installé

```bash
yum info vim-enhanced
```

**Résultat** :
```
Installed Packages
Name         : vim-enhanced
Version      : 8.0.1763
Release      : 16.el8
Architecture : x86_64
Size         : 2.9 M
...
```

**Section** : `Installed Packages` au lieu de `Available Packages`

---

## 📥 yum install : Installer Paquet

### Syntaxe

```bash
yum install nom-paquet
```

**Rôle** : Télécharger + installer paquet + **dépendances**

**Équivalent** : `apt-get install` (Debian)

---

### Exemple

```bash
sudo yum install httpd
```

**Résultat** :
```
Dependencies resolved.
================================================================================
 Package           Arch   Version          Repository                    Size
================================================================================
Installing:
 httpd             x86_64 2.4.51-7.el8_5   rhel-8-for-x86_64-appstream  1.5 M
Installing dependencies:
 httpd-tools       x86_64 2.4.51-7.el8_5   rhel-8-for-x86_64-appstream  108 k
 apr               x86_64 1.6.3-12.el8     rhel-8-for-x86_64-appstream  125 k
 apr-util          x86_64 1.6.1-6.el8      rhel-8-for-x86_64-appstream  105 k

Transaction Summary
================================================================================
Install  4 Packages

Total download size: 1.8 M
Installed size: 5.1 M
Is this ok [y/N]: y
```

**Étapes** :
1. ✅ Résoudre dépendances
2. ✅ Afficher résumé (paquets + taille)
3. ✅ Demander confirmation
4. ✅ Télécharger
5. ✅ Installer via rpm

---

### Installation Multiple

```bash
sudo yum install httpd mysql-server php
```

**Effet** : Installe 3 paquets + dépendances

---

### Installation Sans Confirmation

```bash
sudo yum install -y httpd
#                  │
#                  └─── -y = assume yes
```

**Usage** : Scripts, automatisation

---

## 🔄 yum reinstall : Réinstaller Paquet

### Syntaxe

```bash
yum reinstall nom-paquet
```

**Rôle** : Réinstaller paquet (fichiers **écrasés**)

**Cas d'usage** :
- ⚠️ Fichier de configuration corrompu
- ⚠️ Binaire modifié/supprimé accidentellement
- ⚠️ Restaurer fichiers par défaut

---

### Exemple

```bash
# Fichier config cassé
sudo yum reinstall httpd
```

**Résultat** :
```
Reinstalling:
 httpd             x86_64 2.4.51-7.el8_5   rhel-8-for-x86_64-appstream  1.5 M

Transaction Summary
================================================================================
Reinstall  1 Package
```

**Effet** :
- ✅ Fichiers réinstallés
- ⚠️ Configuration **peut être écrasée** (vérifier backups)

---

## 🗑️ yum remove : Supprimer Paquet

### Syntaxe

```bash
yum remove nom-paquet
```

**Rôle** : Désinstaller paquet + **dépendances**

**Équivalent** : `apt-get remove` (Debian)

**⚠️ Attention** : Supprime aussi paquets dépendants !

---

### Exemple

```bash
sudo yum remove httpd
```

**Résultat** :
```
Dependencies resolved.
================================================================================
 Package           Arch   Version          Repository                    Size
================================================================================
Removing:
 httpd             x86_64 2.4.51-7.el8_5   @rhel-8-for-x86_64-appstream 5.1 M
Removing unused dependencies:
 httpd-tools       x86_64 2.4.51-7.el8_5   @rhel-8-for-x86_64-appstream 205 k
 apr               x86_64 1.6.3-12.el8     @rhel-8-for-x86_64-appstream 284 k
 apr-util          x86_64 1.6.1-6.el8      @rhel-8-for-x86_64-appstream 213 k

Transaction Summary
================================================================================
Remove  4 Packages

Freed space: 5.8 M
Is this ok [y/N]: y
```

**Comportement** :
- ✅ Supprime paquet principal
- ✅ Supprime dépendances **inutilisées**
- ⚠️ Demande confirmation (montre impact)

---

### ⚠️ Risque Suppression Cascade

```bash
sudo yum remove python3
```

**Résultat** :
```
Removing:
 python3           x86_64 3.6.8-45.el8     @rhel-8-for-x86_64-appstream  67 k
Removing dependent packages:
 yum               noarch 4.7.0-4.el8      @rhel-8-for-x86_64-baseos    1.2 M
 dnf               noarch 4.7.0-10.el8     @rhel-8-for-x86_64-baseos    5.4 M
 ...
 WARNING: This will remove 187 packages!
```

**💡 Toujours lire** ce qui sera supprimé avant confirmer !

---

## 📦 yum group : Groupes de Paquets

### Concept

**Groupe** = Collection de paquets liés à une **fonctionnalité**

**Exemples** :
- "Development Tools" → gcc, make, autoconf...
- "Web Server" → httpd, mod_ssl, php...
- "Graphical Administration Tools" → cockpit, system-config-*...

**Avantage** : Installer environnement complet en 1 commande

---

### yum group list : Lister Groupes

```bash
yum group list
```

**Résultat** :
```
Available Environment Groups:
   Server with GUI
   Server
   Minimal Install
   Workstation
   Custom Operating System

Available Groups:
   Development Tools
   System Tools
   Security Tools
   Graphical Administration Tools
```

---

### yum group list --hidden

```bash
yum group list --hidden
```

**Rôle** : Afficher **tous** groupes (inclus cachés)

**Résultat** : + groupes techniques/internes

---

### yum group install : Installer Groupe

```bash
yum group install "Nom Groupe"
```

**⚠️ Guillemets** : Obligatoires si nom contient espaces

**Exemple** :
```bash
sudo yum group install "Development Tools"
```

**Résultat** :
```
Dependencies resolved.
================================================================================
 Package                    Arch       Version            Repository      Size
================================================================================
Installing group/module packages:
 gcc                        x86_64     8.5.0-4.el8_5      baseos          23 M
 gcc-c++                    x86_64     8.5.0-4.el8_5      baseos          12 M
 make                       x86_64     1:4.2.1-11.el8     baseos         498 k
 autoconf                   noarch     2.69-29.el8        appstream      710 k
 automake                   noarch     1.16.1-7.el8       appstream      713 k
 ...

Installing group dependencies:
 kernel-headers             x86_64     4.18.0-348.el8     baseos         6.0 M
 glibc-devel                x86_64     2.28-164.el8       baseos         1.0 M
 ...

Transaction Summary
================================================================================
Install  54 Packages

Total download size: 94 M
Installed size: 248 M
Is this ok [y/N]: y
```

**💡 Gain de temps** : 54 paquets en 1 commande

---

### yum group remove : Supprimer Groupe

```bash
yum group remove "Nom Groupe"
```

**Exemple** :
```bash
sudo yum group remove "Development Tools"
```

**Résultat** :
```
Dependencies resolved.
================================================================================
 Package                    Arch       Version            Repository      Size
================================================================================
Removing:
 gcc                        x86_64     8.5.0-4.el8_5      @baseos         75 M
 gcc-c++                    x86_64     8.5.0-4.el8_5      @baseos         34 M
 make                       x86_64     1:4.2.1-11.el8     @baseos        1.4 M
 ...

Transaction Summary
================================================================================
Remove  54 Packages

Freed space: 248 M
Is this ok [y/N]: y
```

---

### yum group info : Détails Groupe

```bash
yum group info "Development Tools"
```

**Résultat** :
```
Group: Development Tools
 Description: A basic development environment.
 Mandatory Packages:
   autoconf
   automake
   gcc
   gcc-c++
   make
 Default Packages:
   byacc
   cscope
   diffstat
 Optional Packages:
   cmake
   expect
   rpmdevtools
```

**Types paquets** :
- **Mandatory** : Toujours installés
- **Default** : Installés par défaut
- **Optional** : Disponibles mais pas auto-installés

---

## 💿 yum install ./paquet.rpm : Installer RPM Local

### Syntaxe

```bash
yum install ./paquet.rpm
```

**Rôle** : Installer fichier `.rpm` local **avec résolution dépendances**

**Avantage sur rpm -i** :
- ✅ Résout dépendances (télécharge depuis repos)
- ✅ Vérifie signatures
- ✅ Enregistre dans base yum

**Équivalent** : `apt install ./paquet.deb` (Debian)

---

### Exemple

```bash
# Téléchargé google-chrome-stable.rpm
sudo yum install ./google-chrome-stable_current_x86_64.rpm
```

**Résultat** :
```
Dependencies resolved.
================================================================================
 Package                  Arch    Version           Repository           Size
================================================================================
Installing:
 google-chrome-stable     x86_64  108.0.5359.124-1  @commandline         89 M
Installing dependencies:
 liberation-fonts         noarch  1:2.00.3-7.el8    baseos              1.3 M
 vulkan-loader            x86_64  1.2.162.0-1.el8   appstream            44 k

Transaction Summary
================================================================================
Install  3 Packages

Total size: 90 M
Is this ok [y/N]: y
```

**💡 Point clé** : Dépendances (liberation-fonts, vulkan-loader) automatiquement installées depuis repos

---

### Chemin Obligatoire

```bash
# ❌ MAUVAIS
sudo yum install paquet.rpm
# Erreur : No match for argument: paquet.rpm

# ✅ BON
sudo yum install ./paquet.rpm
#                 │
#                 └─── ./ indique fichier local

# ✅ BON aussi
sudo yum install /chemin/complet/paquet.rpm
```

---

## 🧹 yum autoremove : Supprimer Dépendances Orphelines

### Syntaxe

```bash
yum autoremove
```

**Rôle** : Supprimer paquets installés automatiquement (dépendances) **plus utilisés**

**Équivalent** : `apt-get autoremove` (Debian)

---

### Exemple

```bash
# 1. Installer paquet
sudo yum install httpd
# (installe httpd + dépendances : httpd-tools, apr, apr-util)

# 2. Supprimer paquet principal
sudo yum remove httpd
# (httpd supprimé, dépendances restent)

# 3. Nettoyer dépendances orphelines
sudo yum autoremove
```

**Résultat** :
```
Dependencies resolved.
================================================================================
 Package           Arch   Version          Repository                    Size
================================================================================
Removing unused dependencies:
 httpd-tools       x86_64 2.4.51-7.el8_5   @rhel-8-for-x86_64-appstream 205 k
 apr               x86_64 1.6.3-12.el8     @rhel-8-for-x86_64-appstream 284 k
 apr-util          x86_64 1.6.1-6.el8      @rhel-8-for-x86_64-appstream 213 k

Transaction Summary
================================================================================
Remove  3 Packages

Freed space: 702 k
Is this ok [y/N]: y
```

**💡 Bonne pratique** : Exécuter régulièrement

---

## 🔍 yum check-update : Vérifier Mises à Jour

### Syntaxe

```bash
yum check-update
```

**Rôle** : **Lister** paquets avec màj disponible (sans installer)

**Équivalent** : `apt list --upgradable` (Debian)

---

### Exemple

```bash
yum check-update
```

**Résultat** :
```
kernel.x86_64                    4.18.0-372.9.1.el8            baseos
kernel-core.x86_64               4.18.0-372.9.1.el8            baseos
vim-enhanced.x86_64              2:8.0.1763-16.el8_5.4         appstream
openssh.x86_64                   8.0p1-13.el8                  baseos
openssh-clients.x86_64           8.0p1-13.el8                  baseos
```

**Format** :
- **Paquet** : Nom.architecture
- **Version** : Nouvelle version disponible
- **Repository** : D'où vient la màj

**Code retour** :
- `0` → Aucune màj
- `100` → Màj disponibles

---

### Usage Script

```bash
#!/bin/bash
yum check-update > /dev/null
if [ $? -eq 100 ]; then
    echo "Mises à jour disponibles"
    yum check-update
else
    echo "Système à jour"
fi
```

---

## ⬆️ yum update : Mettre à Jour Paquets

### Syntaxe

```bash
yum update
```

**Rôle** : Mettre à jour **tous** les paquets

**Équivalent** : `apt-get upgrade` (Debian)

**⚠️ Pré-requis** : Aucun (yum met à jour cache automatiquement)

---

### Exemple

```bash
sudo yum update
```

**Résultat** :
```
Dependencies resolved.
================================================================================
 Package                  Arch    Version              Repository        Size
================================================================================
Upgrading:
 kernel                   x86_64  4.18.0-372.9.1.el8   baseos            7.0 M
 kernel-core              x86_64  4.18.0-372.9.1.el8   baseos             38 M
 vim-enhanced             x86_64  2:8.0.1763-16.el8_5  appstream         1.4 M
 openssh                  x86_64  8.0p1-13.el8         baseos            522 k
 openssh-clients          x86_64  8.0p1-13.el8         baseos            668 k

Transaction Summary
================================================================================
Upgrade  5 Packages

Total download size: 47 M
Is this ok [y/N]: y
```

**Étapes** :
1. ✅ Vérifier màj disponibles
2. ✅ Résoudre dépendances
3. ✅ Télécharger paquets
4. ✅ Installer nouvelles versions

---

### Mettre à Jour Paquet Spécifique

```bash
sudo yum update nom-paquet
```

**Exemple** :
```bash
sudo yum update vim-enhanced
```

**Effet** : Seulement `vim-enhanced` est mis à jour

---

### yum update vs yum upgrade

**Sur RHEL/CentOS** : **Identique**

```bash
yum update = yum upgrade
```

**💡 Recommandation** : Utiliser `update` (plus commun)

---

## 📋 Antisèche - Commandes YUM/DNF

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `yum repolist` | Lister repos actifs | `yum repolist` |
| `yum repolist --all` | Lister tous repos | `yum repolist --all` |
| `yum-config-manager --enable` | Activer repo | `sudo yum-config-manager --enable powertools` |
| `yum-config-manager --disable` | Désactiver repo | `sudo yum-config-manager --disable extras` |
| `yum-config-manager --add-repo` | Ajouter repo tiers | `sudo yum-config-manager --add-repo URL` |
| `yum makecache` | Recharger métadonnées | `sudo yum makecache` |
| `yum search` | Chercher paquet | `yum search web server` |
| `yum info` | Détails paquet | `yum info httpd` |
| `yum install` | Installer paquet | `sudo yum install httpd` |
| `yum reinstall` | Réinstaller paquet | `sudo yum reinstall httpd` |
| `yum remove` | Supprimer paquet | `sudo yum remove httpd` |
| `yum autoremove` | Supprimer orphelins | `sudo yum autoremove` |
| `yum group list` | Lister groupes | `yum group list` |
| `yum group install` | Installer groupe | `sudo yum group install "Development Tools"` |
| `yum group remove` | Supprimer groupe | `sudo yum group remove "Development Tools"` |
| `yum install ./rpm` | Installer RPM local | `sudo yum install ./paquet.rpm` |
| `yum check-update` | Vérifier màj (sans installer) | `yum check-update` |
| `yum update` | Mettre à jour tous | `sudo yum update` |
| `yum update paquet` | Mettre à jour 1 paquet | `sudo yum update vim` |

**💡 DNF** : Remplacer `yum` par `dnf` (syntaxe identique)

---

## 🎓 Points Clés pour l'Examen

✅ **DNF** : Rétrocompatible avec yum  
✅ **RHEL subscription** : `subscription-manager register` + `attach --auto`  
✅ **yum repolist** : Lister repos actifs  
✅ **yum repolist --all** : Lister tous repos (actifs + désactivés)  
✅ **Activer repo** : `subscription-manager repos --enable` (RHEL) ou `yum-config-manager --enable`  
✅ **Ajouter repo tiers** : `yum-config-manager --add-repo URL`  
✅ **Fichier .repo** : `/etc/yum.repos.d/*.repo`  
✅ **Format .repo** : `[id]`, `name`, `baseurl`, `enabled`, `gpgcheck`, `gpgkey`  
✅ **yum makecache** : Recharger métadonnées (comme `apt-get update`)  
✅ **yum search** : Chercher paquet  
✅ **yum info** : Détails paquet  
✅ **yum install** : Installer paquet + dépendances  
✅ **yum reinstall** : Réinstaller (restaurer fichiers)  
✅ **yum remove** : Supprimer paquet + dépendances inutilisées  
✅ **yum autoremove** : Supprimer orphelins  
✅ **yum group** : Installer collections paquets  
✅ **yum install ./rpm** : Installer RPM local avec dépendances  
✅ **yum check-update** : Vérifier màj disponibles (sans installer)  
✅ **yum update** : Mettre à jour tous paquets  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Première Config RHEL

```bash
# 1. Enregistrer système
sudo subscription-manager register --username user@example.com --password pass

# 2. Attacher abonnement
sudo subscription-manager attach --auto

# 3. Vérifier repos
sudo yum repolist

# 4. Installer EPEL
sudo yum install epel-release

# 5. Mettre à jour
sudo yum update -y
```

---

### Scénario 2 : Installer Environnement Développement

```bash
# 1. Voir groupes disponibles
yum group list

# 2. Installer Development Tools
sudo yum group install "Development Tools"

# 3. Vérifier
gcc --version
make --version
```

---

### Scénario 3 : Ajouter Repository Docker

```bash
# 1. Ajouter repo
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

# 2. Vérifier
yum repolist | grep docker
# docker-ce-stable

# 3. Installer Docker
sudo yum install docker-ce

# 4. Démarrer
sudo systemctl start docker
sudo systemctl enable docker
```

---

### Scénario 4 : Activer Repository CodeReady Builder

```bash
# 1. Lister repos disponibles
subscription-manager repos --list | grep codeready

# 2. Activer
sudo subscription-manager repos --enable codeready-builder-for-rhel-8-x86_64-rpms

# 3. Vérifier
yum repolist | grep codeready
# codeready-builder-for-rhel-8-x86_64-rpms

# 4. Installer paquet du repo
sudo yum install ninja-build
```

---

### Scénario 5 : Installer RPM Téléchargé (Chrome)

```bash
# 1. Télécharger
wget https://dl.google.com/linux/direct/google-chrome-stable_current_x86_64.rpm

# 2. Installer (avec dépendances auto)
sudo yum install ./google-chrome-stable_current_x86_64.rpm

# 3. Lancer
google-chrome
```

---

### Scénario 6 : Rechercher et Installer Paquet

```bash
# 1. Chercher
yum search nginx
# nginx.x86_64 : High performance web server

# 2. Voir détails
yum info nginx
# Version: 1.14.1-9.module+el8.0.0+4108+af250afe

# 3. Installer
sudo yum install nginx

# 4. Démarrer
sudo systemctl start nginx
sudo systemctl enable nginx
```

---

### Scénario 7 : Maintenance Système

```bash
# 1. Vérifier màj disponibles
yum check-update

# 2. Mettre à jour
sudo yum update -y

# 3. Nettoyer orphelins
sudo yum autoremove

# 4. Nettoyer cache
sudo yum clean all

# 5. Vérifier espace libéré
du -sh /var/cache/yum/
```

---

### Scénario 8 : Réparer Config Cassée

```bash
# Config httpd cassée
sudo cat /etc/httpd/conf/httpd.conf
# (syntaxe incorrecte)

# Tester
sudo apachectl configtest
# Syntax error...

# Réinstaller (restaure config par défaut)
sudo yum reinstall httpd

# Vérifier
sudo apachectl configtest
# Syntax OK
```

---

### Scénario 9 : Créer Repo Local

```bash
# 1. Créer fichier repo
sudo nano /etc/yum.repos.d/local.repo

# 2. Contenu
[local-repo]
name=Local Repository
baseurl=file:///mnt/repo
enabled=1
gpgcheck=0

# 3. Sauvegarder

# 4. Recharger
sudo yum makecache

# 5. Vérifier
yum repolist
# local-repo     Local Repository
```

---

### Scénario 10 : Downgrade Paquet (Rare)

```bash
# 1. Voir versions disponibles
yum list --showduplicates httpd

# 2. Downgrade vers version spécifique
sudo yum downgrade httpd-2.4.37-43.module+el8.5.0+13806+b30d9eec

# 3. Verrouiller version (empêcher màj)
sudo yum install yum-plugin-versionlock
sudo yum versionlock httpd

# 4. Vérifier
yum versionlock list
# httpd-0:2.4.37-43...
```

---

## 🔄 Workflow Complet

### Installation Standard

```
1. MAKECACHE (optionnel)
   sudo yum makecache
   → Rafraîchir métadonnées

2. SEARCH (optionnel)
   yum search paquet
   → Trouver nom exact

3. INFO (optionnel)
   yum info paquet
   → Voir version, dépendances

4. INSTALL
   sudo yum install paquet
   → Télécharge + installe + dépendances

5. VERIFY
   rpm -q paquet
   → Vérifier installation
```

---

### Désinstallation Complète

```
1. REMOVE
   sudo yum remove paquet
   → Supprime paquet + dépendances

2. AUTOREMOVE
   sudo yum autoremove
   → Supprime orphelins

3. VERIFY
   rpm -q paquet
   → package paquet is not installed
```

---

### Maintenance Hebdomadaire

```
1. CHECK-UPDATE
   yum check-update
   → Voir màj disponibles

2. UPDATE
   sudo yum update -y
   → Installer màj

3. AUTOREMOVE
   sudo yum autoremove
   → Nettoyer orphelins

4. CLEAN
   sudo yum clean all
   → Libérer espace cache
```

---

## ⚠️ Erreurs Courantes

### Erreur 1 : Système Non Enregistré (RHEL)

```
This system is not registered with an entitlement server.
```

**Solution** :
```bash
sudo subscription-manager register --username user --password pass
sudo subscription-manager attach --auto
```

---

### Erreur 2 : Repository Désactivé

```
Error: No match for argument: paquet
```

**Solution** :
```bash
# Voir tous repos
yum repolist --all

# Activer repo nécessaire
sudo yum-config-manager --enable powertools
```

---

### Erreur 3 : Clé GPG Manquante

```
GPG key retrieval failed: [Errno 14] curl#37 - "Couldn't open file /etc/pki/rpm-gpg/RPM-GPG-KEY-..."
```

**Solution** :
```bash
# Importer clé
sudo rpm --import https://repo.example.com/RPM-GPG-KEY

# Ou désactiver gpgcheck (⚠️ moins sûr)
# Dans /etc/yum.repos.d/repo.repo
gpgcheck=0
```

---

### Erreur 4 : Dépendances Conflictuelles

```
Error: Transaction check error:
  file /usr/bin/foo conflicts between attempted installs of paquet1-1.0 and paquet2-1.0
```

**Solution** :
```bash
# Voir détails conflit
yum info paquet1 paquet2

# Choisir 1 des 2 paquets
sudo yum install paquet1
```

---

### Erreur 5 : Cache Corrompu

```
Error: Failed to download metadata for repo 'appstream'
```

**Solution** :
```bash
# Nettoyer cache
sudo yum clean all

# Régénérer
sudo yum makecache
```

---

## 🗂️ Arborescence YUM

```
/etc/yum.repos.d/
├── redhat.repo               → Repos Red Hat (géré par subscription-manager)
├── epel.repo                 → Extra Packages for Enterprise Linux
├── docker-ce.repo            → Docker
└── custom.repo               → Repo personnalisé

/var/cache/yum/               → Cache paquets (RHEL 7)
/var/cache/dnf/               → Cache paquets (RHEL 8+)

/etc/pki/rpm-gpg/             → Clés GPG
├── RPM-GPG-KEY-redhat-release
├── RPM-GPG-KEY-EPEL-8
└── RPM-GPG-KEY-docker

/var/log/yum.log              → Logs yum
/var/log/dnf.log              → Logs dnf
```

---

**🎯 Prochaine étape** : Work on the Command Line (Module 3)

*Dernière mise à jour: 31 janvier 2026*
