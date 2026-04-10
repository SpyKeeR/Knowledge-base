# 📦 Gestion de Paquets SUSE - Zypper

---

## 🎯 Écosystème SUSE

**Distributions concernées** :
- 🦎 **openSUSE Leap** (stable, communautaire)
- 🦎 **openSUSE Tumbleweed** (rolling release)
- 🟢 **SUSE Linux Enterprise** (SLES, commercial)

**Format paquet** : `.rpm` (comme Red Hat)

**Gestionnaires** :
- `rpm` → Bas niveau (comme dpkg)
- `zypper` → Haut niveau (comme apt/yum)

**Particularité** : Utilise **métadonnées** avancées pour résolution dépendances

**Analogie écosystèmes** :

| Debian/Ubuntu | Red Hat/CentOS | SUSE/openSUSE |
|---------------|----------------|---------------|
| `.deb` | `.rpm` | `.rpm` |
| `dpkg` | `rpm` | `rpm` |
| `apt` | `yum`/`dnf` | `zypper` |
| `/etc/apt/sources.list` | `/etc/yum.repos.d/` | `/etc/zypp/repos.d/` |

**💡 Point clé** : SUSE utilise RPM mais avec Zypper (différent de YUM)

---

## 🔄 zypper refresh : Rafraîchir Métadonnées

### Syntaxe

```bash
zypper refresh
```

**Alias** : `zypper ref`

**Rôle** : Télécharger **métadonnées** à jour depuis repositories

**Équivalent** :
- `apt-get update` (Debian)
- `yum makecache` (Red Hat)

**Analogie** 📋 : Mettre à jour le catalogue du magasin

---

### Exemple

```bash
sudo zypper refresh
```

**Résultat** :
```
Retrieving repository 'openSUSE-Leap-15.4-Oss' metadata ...................[done]
Building repository 'openSUSE-Leap-15.4-Oss' cache ........................[done]
Retrieving repository 'openSUSE-Leap-15.4-Update' metadata ................[done]
Building repository 'openSUSE-Leap-15.4-Update' cache .....................[done]
Retrieving repository 'openSUSE-Leap-15.4-Non-Oss' metadata ...............[done]
Building repository 'openSUSE-Leap-15.4-Non-Oss' cache ....................[done]
All repositories have been refreshed.
```

**Étapes** :
1. ✅ Télécharge métadonnées (listes paquets)
2. ✅ Construit cache local
3. ✅ Répète pour chaque repo actif

**Cache stocké** : `/var/cache/zypp/`

---

### Quand Utiliser ?

```bash
# ✅ Avant recherche/installation
sudo zypper refresh
sudo zypper install paquet

# ✅ Après ajout nouveau repo
sudo zypper addrepo URL
sudo zypper refresh

# ✅ Régulièrement (1×/jour sur serveur)
sudo zypper refresh
```

**💡 Bonne pratique** : Toujours refresh avant install/search

---

### Options Utiles

```bash
# Forcer refresh (même si cache récent)
sudo zypper refresh --force

# Refresh uniquement services (pas repos)
sudo zypper refresh-services

# Refresh repo spécifique
sudo zypper refresh nom-repo
```

---

## 🔍 zypper se : Rechercher Paquet

### Syntaxe

```bash
zypper search mot-clé
```

**Alias** : `zypper se`

**Rôle** : Chercher paquets par **nom** ou **description**

**Équivalent** :
- `apt-cache search` (Debian)
- `yum search` (Red Hat)

---

### Exemple Simple

```bash
zypper se nginx
```

**Résultat** :
```
Loading repository data...
Reading installed packages...

S | Name              | Summary                           | Type
--+-------------------+-----------------------------------+--------
  | nginx             | HTTP and reverse proxy server     | package
  | nginx-module-geoip| GeoIP dynamic modules for nginx   | package
i | nginx-module-perl | Perl dynamic modules for nginx    | package
```

**Colonnes** :
- **S** : Statut (`i` = installé, vide = non installé)
- **Name** : Nom du paquet
- **Summary** : Description courte
- **Type** : Type (package, pattern, patch...)

**💡 `i` dans colonne S** : Paquet déjà installé

---

### zypper se -i : Paquets Installés Seulement

```bash
zypper se -i mot-clé
```

**Alias** : `zypper se --installed-only`

**Rôle** : Chercher **uniquement** dans paquets installés

**Équivalent** : `dpkg -l | grep` (Debian)

---

**Exemple** :

```bash
zypper se -i python
```

**Résultat** :
```
S | Name              | Summary                           | Type
--+-------------------+-----------------------------------+--------
i | python3           | Python 3 Interpreter              | package
i | python3-base      | Python 3 Interpreter base package | package
i | python3-pip       | Package installer for Python      | package
```

**Tous ont `i`** : Uniquement paquets installés affichés

**Usage** : Vérifier si paquet déjà installé

---

### zypper se -u : Paquets Non Installés (Repositories)

```bash
zypper se -u mot-clé
```

**Alias** : `zypper se --uninstalled-only`

**Rôle** : Chercher **uniquement** paquets disponibles (non installés)

---

**Exemple** :

```bash
zypper se -u nginx
```

**Résultat** :
```
S | Name              | Summary                           | Type
--+-------------------+-----------------------------------+--------
  | nginx             | HTTP and reverse proxy server     | package
  | nginx-module-geoip| GeoIP dynamic modules for nginx   | package
  | nginx-module-rtmp | RTMP dynamic modules for nginx    | package
```

**Aucun `i`** : Uniquement paquets non installés

**Usage** : Voir ce qui est disponible

---

### Comparaison Options Recherche

| Option | Affiche | Usage |
|--------|---------|-------|
| `zypper se` | **Tous** paquets (installés + dispo) | Recherche générale |
| `zypper se -i` | **Installés** uniquement | Vérifier présence |
| `zypper se -u` | **Non installés** (dispo) | Découvrir nouveaux |

---

### Options Recherche Avancées

```bash
# Recherche exacte (nom complet)
zypper se --match-exact nginx

# Recherche dans descriptions
zypper se --search-descriptions web

# Tri par nom
zypper se --sort-by-name python

# Chercher patterns (groupes)
zypper se -t pattern
```

---

## 📥 zypper in : Installer Paquet

### Syntaxe

```bash
zypper install nom-paquet
```

**Alias** : `zypper in`

**Rôle** : Télécharger + installer paquet + **dépendances**

**Équivalent** :
- `apt-get install` (Debian)
- `yum install` (Red Hat)

---

### Exemple

```bash
sudo zypper in nginx
```

**Résultat** :
```
Loading repository data...
Reading installed packages...
Resolving package dependencies...

The following NEW package is going to be installed:
  nginx

1 new package to install.
Overall download size: 512.4 KiB. Already cached: 0 B. After the operation, additional 1.2 MiB will be used.
Continue? [y/n/...? shows all options] (y): y
Retrieving package nginx-1.21.6-1.1.x86_64 (1/1), 512.4 KiB (1.2 MiB unpacked)
Retrieving: nginx-1.21.6-1.1.x86_64.rpm ...................................[done]

Checking for file conflicts: .............................................[done]
(1/1) Installing: nginx-1.21.6-1.1.x86_64 .................................[done]
```

**Étapes** :
1. ✅ Charger données repos
2. ✅ Résoudre dépendances
3. ✅ Afficher résumé (taille, espace)
4. ✅ Demander confirmation
5. ✅ Télécharger paquets
6. ✅ Vérifier conflits fichiers
7. ✅ Installer via rpm

---

### Installation Multiple

```bash
sudo zypper in apache2 mariadb php8
```

**Effet** : Installe **3 paquets** + dépendances

---

### Installation Sans Confirmation

```bash
sudo zypper in -y nginx
#               │
#               └─── -y = assume yes
```

**Ou**:

```bash
sudo zypper --non-interactive in nginx
```

**Usage** : Scripts automatisés

---

## 💿 zypper in /path/paquet.rpm : Installer RPM Local

### Syntaxe

```bash
zypper install /chemin/paquet.rpm
```

**Alias** : `zypper in /chemin/paquet.rpm`

**Rôle** : Installer fichier `.rpm` local **avec résolution dépendances**

**Avantage sur rpm -i** :
- ✅ Résout dépendances (télécharge depuis repos)
- ✅ Vérifie signatures
- ✅ Enregistre dans base zypper

**Équivalent** :
- `apt install ./paquet.deb` (Debian)
- `yum install ./paquet.rpm` (Red Hat)

---

### Exemple

```bash
# Téléchargé google-chrome-stable.rpm
sudo zypper in ./google-chrome-stable_current_x86_64.rpm
```

**Résultat** :
```
Loading repository data...
Reading installed packages...
Resolving package dependencies...

The following NEW packages are going to be installed:
  google-chrome-stable liberation-fonts

2 new packages to install.
Overall download size: 1.3 MiB. Already cached: 89.2 MiB. After the operation, additional 298.5 MiB will be used.
Continue? [y/n/...] (y): y
```

**💡 Point clé** : Dépendances (liberation-fonts) automatiquement installées depuis repos

---

### Chemin Requis

```bash
# ✅ BON
sudo zypper in ./paquet.rpm
sudo zypper in /opt/paquets/paquet.rpm

# ❌ MAUVAIS (sans chemin)
sudo zypper in paquet.rpm
# Erreur : 'paquet.rpm' not found in package names.
```

---

## 🗑️ zypper rm : Supprimer Paquet

### Syntaxe

```bash
zypper remove nom-paquet
```

**Alias** : `zypper rm`

**Rôle** : Désinstaller paquet + **dépendances inutilisées**

**Équivalent** :
- `apt-get remove` (Debian)
- `yum remove` (Red Hat)

---

### Exemple

```bash
sudo zypper rm nginx
```

**Résultat** :
```
Loading repository data...
Reading installed packages...
Resolving package dependencies...

The following package is going to be REMOVED:
  nginx

1 package to remove.
After the operation, 1.2 MiB will be freed.
Continue? [y/n/...] (y): y
(1/1) Removing nginx-1.21.6-1.1.x86_64 ....................................[done]
```

---

### Suppression Automatique Dépendances

**Comportement par défaut** : Zypper **supprime automatiquement** dépendances orphelines

```bash
# Installer paquet (installe dépendances)
sudo zypper in apache2
# (installe apache2 + apr + apr-util)

# Supprimer
sudo zypper rm apache2
```

**Résultat** :
```
The following 3 packages are going to be REMOVED:
  apache2 apr apr-util

3 packages to remove.
```

**💡 Pas besoin** de commande séparée comme `autoremove` (apt/yum)

---

### Supprimer Avec Config

```bash
# Par défaut : garde config
sudo zypper rm paquet

# Supprimer config aussi (purge)
sudo zypper rm --clean-deps paquet
```

---

## 📂 zypper repos : Lister Repositories

### Syntaxe

```bash
zypper repos
```

**Alias** : `zypper lr` (list repos)

**Rôle** : Afficher **tous** repositories configurés

**Équivalent** :
- `yum repolist --all` (Red Hat)
- `cat /etc/apt/sources.list` (Debian)

---

### Exemple

```bash
zypper repos
```

**Résultat** :
```
# | Alias                        | Name                              | Enabled | GPG Check | Refresh
--+------------------------------+-----------------------------------+---------+-----------+--------
1 | repo-oss                     | openSUSE-Leap-15.4-Oss            | Yes     | (r ) Yes  | Yes
2 | repo-non-oss                 | openSUSE-Leap-15.4-Non-Oss        | Yes     | (r ) Yes  | Yes
3 | repo-update                  | openSUSE-Leap-15.4-Update         | Yes     | (r ) Yes  | Yes
4 | repo-update-non-oss          | openSUSE-Leap-15.4-Update-Non-Oss | Yes     | (r ) Yes  | Yes
5 | packman                      | Packman Repository                | No      | (r ) Yes  | No
```

**Colonnes** :
- **#** : Numéro (pour référence)
- **Alias** : Nom court (utilisé dans commandes)
- **Name** : Nom descriptif complet
- **Enabled** : Activé (Yes/No)
- **GPG Check** : Vérification signature
- **Refresh** : Auto-refresh métadonnées

---

### Détails Repository

```bash
zypper repos --details
```

**Résultat** (extrait) :
```
Alias                 : repo-oss
Name                  : openSUSE-Leap-15.4-Oss
URI                   : http://download.opensuse.org/distribution/leap/15.4/repo/oss/
Enabled               : Yes
GPG Check             : Yes
GPG Key URI           : file:///usr/share/doc/packages/openSUSE-release/GPG-KEY
Priority              : 99
Autorefresh           : Yes
Keep Packages         : No
Type                  : rpm-md
MD Cache Path         : /var/cache/zypp/raw/repo-oss
```

**Informations supplémentaires** :
- **URI** : URL du repo
- **Priority** : Priorité (nombre bas = priorité haute)
- **Autorefresh** : Refresh auto métadonnées

---

### Filtrer Repositories

```bash
# Uniquement repos activés
zypper repos | grep Yes

# Compter repos
zypper repos | tail -n +3 | wc -l
```

---

## 🔧 zypper modifyrepo : Modifier Repository

### Activer Repository

```bash
zypper modifyrepo --enable nom-repo
```

**Alias** : `zypper mr -e nom-repo`

**Rôle** : **Activer** un repository désactivé

---

**Exemple** :

```bash
# Lister repos
zypper repos
# 5 | packman    | Packman Repository    | No  | ...

# Activer
sudo zypper modifyrepo --enable packman

# Vérifier
zypper repos
# 5 | packman    | Packman Repository    | Yes | ...
```

**Résultat** :
```
Repository 'packman' has been successfully enabled.
```

---

### Désactiver Repository

```bash
zypper modifyrepo --disable nom-repo
```

**Alias** : `zypper mr -d nom-repo`

**Rôle** : **Désactiver** un repository

---

**Exemple** :

```bash
sudo zypper modifyrepo --disable repo-non-oss
```

**Résultat** :
```
Repository 'repo-non-oss' has been successfully disabled.
```

**Usage** : Désactiver temporairement repo problématique

---

### Activer Autorefresh

```bash
zypper modifyrepo --refresh nom-repo
```

**Alias** : `zypper mr -f nom-repo`

**Rôle** : Activer **rafraîchissement automatique** métadonnées

---

**Exemple** :

```bash
sudo zypper modifyrepo --refresh packman
```

**Résultat** :
```
Autorefresh has been enabled for repository 'packman'.
```

**Effet** : Métadonnées rafraîchies automatiquement lors `zypper in`/`zypper up`

**💡 Recommandé** pour repos tiers fréquemment mis à jour

---

### Désactiver Autorefresh

```bash
zypper modifyrepo --no-refresh nom-repo
```

**Alias** : `zypper mr -F nom-repo`

**Rôle** : Désactiver rafraîchissement automatique

---

**Exemple** :

```bash
sudo zypper modifyrepo --no-refresh repo-oss
```

**Résultat** :
```
Autorefresh has been disabled for repository 'repo-oss'.
```

**Usage** : Repos stables rarement mis à jour (économiser bande passante)

---

### Modifier Plusieurs Repos

```bash
# Activer tous repos
sudo zypper mr --enable --all

# Désactiver tous repos
sudo zypper mr --disable --all

# Activer autorefresh sur tous
sudo zypper mr --refresh --all
```

**⚠️ Attention** : `--all` affecte **tous** les repos

---

### Tableau Récapitulatif modifyrepo

| Commande | Alias | Action |
|----------|-------|--------|
| `zypper modifyrepo --enable repo` | `zypper mr -e repo` | Activer repo |
| `zypper modifyrepo --disable repo` | `zypper mr -d repo` | Désactiver repo |
| `zypper modifyrepo --refresh repo` | `zypper mr -f repo` | Activer autorefresh |
| `zypper modifyrepo --no-refresh repo` | `zypper mr -F repo` | Désactiver autorefresh |
| `zypper modifyrepo --all` | `zypper mr -a` | Modifier tous repos |

---

## ➕ zypper addrepo : Ajouter Repository

### Syntaxe

```bash
zypper addrepo URL alias
```

**Alias** : `zypper ar URL alias`

**Rôle** : Ajouter nouveau repository

**Équivalent** :
- `yum-config-manager --add-repo` (Red Hat)
- Éditer `/etc/apt/sources.list` (Debian)

---

### Exemple Simple

```bash
sudo zypper addrepo https://download.opensuse.org/repositories/mozilla/openSUSE_Leap_15.4/ mozilla
```

**Résultat** :
```
Adding repository 'mozilla' ...................................................[done]
Repository 'mozilla' successfully added

URI         : https://download.opensuse.org/repositories/mozilla/openSUSE_Leap_15.4/
Enabled     : Yes
GPG Check   : Yes
Autorefresh : No
Priority    : 99 (default priority)
```

**Fichier créé** : `/etc/zypp/repos.d/mozilla.repo`

---

### Options Utiles

```bash
# Ajouter avec autorefresh activé
sudo zypper addrepo --refresh URL alias

# Ajouter désactivé (activer plus tard)
sudo zypper addrepo --disable URL alias

# Ajouter avec priorité élevée (nombre bas = priorité haute)
sudo zypper addrepo --priority 50 URL alias

# Sans vérification GPG (⚠️ moins sûr)
sudo zypper addrepo --no-gpgcheck URL alias
```

---

### Exemple Packman Repository

**Packman** = Repository communautaire populaire (codecs multimédia)

```bash
# openSUSE Leap 15.4
sudo zypper addrepo --refresh --priority 90 \
  https://ftp.gwdg.de/pub/linux/misc/packman/suse/openSUSE_Leap_15.4/ \
  packman

# Rafraîchir métadonnées
sudo zypper refresh

# Importer clé GPG
sudo zypper refresh
# (demande automatiquement d'importer clé)
```

---

### Vérifier Ajout

```bash
# Lister repos
zypper repos

# Voir détails
zypper repos --details | grep -A 10 packman
```

---

## 🗑️ zypper removerepo : Supprimer Repository

### Syntaxe

```bash
zypper removerepo nom-repo
```

**Alias** : `zypper rr nom-repo`

**Rôle** : Supprimer repository de la configuration

**⚠️ Attention** : Paquets installés depuis ce repo **restent** installés

---

### Exemple

```bash
sudo zypper removerepo mozilla
```

**Résultat** :
```
Removing repository 'mozilla' ..................................................[done]
Repository 'mozilla' has been removed.
```

**Fichier supprimé** : `/etc/zypp/repos.d/mozilla.repo`

---

### Supprimer par Numéro

```bash
# Lister repos avec numéros
zypper repos
# 5 | packman    | Packman Repository    | Yes | ...

# Supprimer par numéro
sudo zypper removerepo 5
```

**💡 Pratique** quand nom long/compliqué

---

## 📋 Antisèche - Commandes Zypper

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `zypper refresh` | Rafraîchir métadonnées | `sudo zypper refresh` |
| `zypper se mot` | Chercher paquet (tous) | `zypper se nginx` |
| `zypper se -i mot` | Chercher installés | `zypper se -i python` |
| `zypper se -u mot` | Chercher non installés | `zypper se -u apache` |
| `zypper in paquet` | Installer paquet | `sudo zypper in nginx` |
| `zypper in ./rpm` | Installer RPM local | `sudo zypper in ./paquet.rpm` |
| `zypper rm paquet` | Supprimer paquet | `sudo zypper rm nginx` |
| `zypper repos` | Lister repositories | `zypper repos` |
| `zypper mr -e repo` | Activer repo | `sudo zypper mr -e packman` |
| `zypper mr -d repo` | Désactiver repo | `sudo zypper mr -d packman` |
| `zypper mr -f repo` | Activer autorefresh | `sudo zypper mr -f packman` |
| `zypper mr -F repo` | Désactiver autorefresh | `sudo zypper mr -F packman` |
| `zypper ar URL alias` | Ajouter repo | `sudo zypper ar URL packman` |
| `zypper rr repo` | Supprimer repo | `sudo zypper rr packman` |

---

## 🎓 Points Clés pour l'Examen

✅ **Zypper** : Gestionnaire paquets SUSE/openSUSE  
✅ **Format** : `.rpm` (comme Red Hat)  
✅ **zypper refresh** : Rafraîchir métadonnées (comme `apt-get update`)  
✅ **zypper se** : Chercher paquet (tous)  
✅ **zypper se -i** : Chercher paquets **installés** uniquement  
✅ **zypper se -u** : Chercher paquets **non installés** (repos)  
✅ **zypper in** : Installer paquet + dépendances  
✅ **zypper in /path/rpm** : Installer RPM local avec dépendances  
✅ **zypper rm** : Supprimer paquet + dépendances **automatiquement**  
✅ **zypper repos** : Lister repositories  
✅ **zypper mr -e** : Activer repository  
✅ **zypper mr -d** : Désactiver repository  
✅ **zypper mr -f** : Activer autorefresh métadonnées  
✅ **zypper mr -F** : Désactiver autorefresh  
✅ **zypper ar** : Ajouter repository  
✅ **zypper rr** : Supprimer repository  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Installation Standard

```bash
# 1. Rafraîchir métadonnées
sudo zypper refresh

# 2. Chercher paquet
zypper se nginx
# nginx - HTTP and reverse proxy server

# 3. Installer
sudo zypper in nginx

# 4. Démarrer service
sudo systemctl start nginx
sudo systemctl enable nginx

# 5. Vérifier
systemctl status nginx
# ● nginx.service - HTTP Server
#    Active: active (running)
```

---

### Scénario 2 : Ajouter Packman Repository

```bash
# 1. Ajouter repo (avec autorefresh)
sudo zypper addrepo --refresh --priority 90 \
  https://ftp.gwdg.de/pub/linux/misc/packman/suse/openSUSE_Leap_15.4/ \
  packman

# 2. Rafraîchir
sudo zypper refresh

# 3. Accepter clé GPG
# (demandé automatiquement)

# 4. Vérifier
zypper repos | grep packman
# packman | Packman Repository | Yes | Yes | Yes

# 5. Installer paquet depuis Packman
sudo zypper in vlc
```

---

### Scénario 3 : Rechercher Paquet Installé

```bash
# Chercher si python3 installé
zypper se -i python3

# Résultat si installé :
# i | python3 | Python 3 Interpreter | package

# Résultat si non installé :
# No packages found.

# Voir détails
zypper info python3
```

---

### Scénario 4 : Installer RPM Téléchargé (Chrome)

```bash
# 1. Télécharger
wget https://dl.google.com/linux/direct/google-chrome-stable_current_x86_64.rpm

# 2. Installer (avec dépendances auto)
sudo zypper in ./google-chrome-stable_current_x86_64.rpm

# 3. Confirmer
# (Zypper résout dépendances depuis repos)

# 4. Lancer
google-chrome
```

---

### Scénario 5 : Désactiver/Activer Repository

```bash
# Lister repos
zypper repos
# 5 | repo-non-oss | openSUSE-Leap-15.4-Non-Oss | Yes | ...

# Désactiver temporairement
sudo zypper mr -d repo-non-oss

# Vérifier
zypper repos
# 5 | repo-non-oss | openSUSE-Leap-15.4-Non-Oss | No  | ...

# Réactiver
sudo zypper mr -e repo-non-oss
```

---

### Scénario 6 : Activer Autorefresh sur Repository

```bash
# Lister repos
zypper repos
# packman | Packman Repository | Yes | Yes | No

# Activer autorefresh
sudo zypper mr -f packman

# Vérifier
zypper repos
# packman | Packman Repository | Yes | Yes | Yes
#                                              │
#                                              └─── Refresh = Yes
```

---

### Scénario 7 : Supprimer Paquet et Dépendances

```bash
# Installer (avec dépendances)
sudo zypper in apache2
# (installe apache2 + apr + apr-util + libapr1...)

# Lister paquets installés
zypper se -i apache
# i | apache2
# i | apache2-utils
# i | apr
# i | apr-util

# Supprimer (dépendances auto-supprimées)
sudo zypper rm apache2
# Removing: apache2 apache2-utils apr apr-util

# Vérifier
zypper se -i apache
# (aucun résultat)
```

---

### Scénario 8 : Chercher Paquets Disponibles

```bash
# Chercher paquets PHP disponibles (non installés)
zypper se -u php

# Résultat :
#   | php8              | PHP 8 Interpreter
#   | php8-mysql        | MySQL support for PHP
#   | php8-gd           | GD extension for PHP

# Installer ce qui manque
sudo zypper in php8 php8-mysql php8-gd
```

---

### Scénario 9 : Gérer Plusieurs Repositories

```bash
# Désactiver tous repos
sudo zypper mr --disable --all

# Activer uniquement repos essentiels
sudo zypper mr -e repo-oss
sudo zypper mr -e repo-update

# Vérifier
zypper repos
# repo-oss      | Yes
# repo-non-oss  | No
# repo-update   | Yes
```

---

### Scénario 10 : Supprimer Repository Non Utilisé

```bash
# Lister repos
zypper repos
# 8 | old-repo | Old Repository | Yes | ...

# Supprimer
sudo zypper rr old-repo
# Repository 'old-repo' has been removed.

# Vérifier
zypper repos
# (old-repo n'apparaît plus)

# Nettoyer cache
sudo zypper clean --all
```

---

## 🔄 Workflow Complet

### Installation Standard

```
1. REFRESH
   sudo zypper refresh
   → Rafraîchir métadonnées

2. SEARCH
   zypper se paquet
   → Trouver nom exact

3. INSTALL
   sudo zypper in paquet
   → Installer + dépendances

4. VERIFY
   zypper se -i paquet
   → Vérifier installation (i)
```

---

### Ajout Repository Tiers

```
1. ADDREPO
   sudo zypper ar --refresh URL alias
   → Ajouter repo

2. REFRESH
   sudo zypper refresh
   → Télécharger métadonnées

3. VERIFY
   zypper repos
   → Vérifier présence

4. INSTALL
   sudo zypper in paquet-du-nouveau-repo
   → Tester installation
```

---

### Désinstallation Complète

```
1. REMOVE
   sudo zypper rm paquet
   → Supprime paquet + dépendances

2. VERIFY
   zypper se -i paquet
   → Vérifier suppression

3. CLEAN (optionnel)
   sudo zypper clean
   → Nettoyer cache
```

---

## 🔄 Commandes Supplémentaires Utiles

### Mise à Jour

```bash
# Vérifier màj disponibles
zypper list-updates

# Mettre à jour tous paquets
sudo zypper update
# Ou
sudo zypper up

# Mettre à jour paquet spécifique
sudo zypper update nginx
```

---

### Informations Paquet

```bash
# Détails paquet
zypper info nginx

# Dépendances
zypper info --requires nginx

# Fichiers d'un paquet
rpm -ql nginx
```

---

### Nettoyage

```bash
# Nettoyer cache métadonnées
sudo zypper clean --metadata

# Nettoyer paquets téléchargés
sudo zypper clean --packages

# Nettoyer tout
sudo zypper clean --all
```

---

### Patterns (Groupes)

```bash
# Lister patterns disponibles
zypper search -t pattern

# Installer pattern (groupe)
sudo zypper in -t pattern lamp_server

# Supprimer pattern
sudo zypper rm -t pattern lamp_server
```

---

## ⚠️ Erreurs Courantes

### Erreur 1 : Métadonnées Obsolètes

```
Warning: Repository metadata has not been refreshed.
```

**Solution** :
```bash
sudo zypper refresh
```

---

### Erreur 2 : Repository Non Trouvé

```
Repository 'packman' not found.
```

**Solution** :
```bash
# Vérifier nom exact
zypper repos

# Utiliser bon nom ou numéro
sudo zypper mr -e nom-exact
```

---

### Erreur 3 : Clé GPG Non Approuvée

```
Do you want to reject the key, trust temporarily, or trust always? [r/t/a/?]
```

**Solution** :
```bash
# Vérifier empreinte clé (fingerprint)
# Puis choisir : a (trust always)
```

---

### Erreur 4 : Conflit Fichiers

```
File conflicts detected.
```

**Solution** :
```bash
# Voir détails conflits
zypper verify

# Forcer installation (⚠️ risqué)
sudo zypper in --force paquet
```

---

### Erreur 5 : Paquet Non Trouvé

```
'paquet.rpm' not found in package names.
```

**Solution** :
```bash
# Ajouter chemin
sudo zypper in ./paquet.rpm
# Ou chemin complet
sudo zypper in /chemin/complet/paquet.rpm
```

---

## 🗂️ Arborescence Zypper

```
/etc/zypp/
├── repos.d/                  → Fichiers repositories
│   ├── repo-oss.repo
│   ├── repo-non-oss.repo
│   ├── repo-update.repo
│   └── packman.repo
├── zypp.conf                 → Configuration principale
└── locks                     → Paquets verrouillés (pas de màj)

/var/cache/zypp/
├── raw/                      → Métadonnées repos
│   ├── repo-oss/
│   └── packman/
└── solv/                     → Cache résolution dépendances

/var/log/zypper.log           → Logs zypper
```

---

## 📊 Comparaison Gestionnaires

| Tâche | Debian/Ubuntu | Red Hat/CentOS | SUSE/openSUSE |
|-------|---------------|----------------|---------------|
| Rafraîchir | `apt-get update` | `yum makecache` | `zypper refresh` |
| Chercher | `apt-cache search` | `yum search` | `zypper se` |
| Installer | `apt-get install` | `yum install` | `zypper in` |
| Supprimer | `apt-get remove` | `yum remove` | `zypper rm` |
| Lister repos | `cat sources.list` | `yum repolist` | `zypper repos` |
| Ajouter repo | Éditer sources.list | `yum-config-manager` | `zypper ar` |
| Màj tout | `apt-get upgrade` | `yum update` | `zypper up` |

---

**🎯 Prochaine étape** : Work on the Command Line (Module 3)

*Dernière mise à jour: 31 janvier 2026*
