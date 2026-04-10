# 📦 Gestion de Paquets Debian - DPKG

---

## 🎯 Système de Paquets Debian

**DPKG** = **D**ebian **P**ac**k**a**g**e

**Rôle** : Gestionnaire de paquets **bas niveau** pour Debian/Ubuntu

**Format paquet** : `.deb`

**Analogie** 📦 : 
- **Paquet .deb** = Boîte Amazon contenant un logiciel
- **dpkg** = Livreur qui ouvre la boîte et installe le contenu

**Différence avec apt** :
- `dpkg` → Bas niveau (1 paquet à la fois, pas de dépendances)
- `apt` → Haut niveau (télécharge, résout dépendances, appelle dpkg)

**💡 Point clé** : `apt` utilise `dpkg` en arrière-plan

---

## 📥 dpkg -i : Installer un Paquet

### Syntaxe

```bash
dpkg -i paquet.deb
```

**Rôle** : **I**nstaller un paquet `.deb`

---

### Exemple

```bash
sudo dpkg -i google-chrome-stable_current_amd64.deb
```

**Résultat** :
```
Sélection du paquet google-chrome-stable précédemment désélectionné.
(Lecture de la base de données... 245678 fichiers et répertoires déjà installés.)
Préparation du dépaquetage de google-chrome-stable_current_amd64.deb ...
Dépaquetage de google-chrome-stable (108.0.5359.124-1) ...
Paramétrage de google-chrome-stable (108.0.5359.124-1) ...
```

**Étapes** :
1. ✅ Vérification intégrité paquet
2. ✅ Extraction fichiers vers système
3. ✅ Configuration post-installation
4. ✅ Enregistrement dans base de données dpkg

---

### ⚠️ Problème Fréquent : Dépendances Manquantes

```bash
sudo dpkg -i paquet.deb
```

**Erreur** :
```
dpkg: problèmes de dépendances empêchent la configuration de paquet :
 paquet dépend de libfoo (>= 1.2.3); cependant :
  Le paquet libfoo n'est pas installé.
```

**Solutions** :

#### Solution 1 : apt --fix-broken

```bash
# Installer malgré l'erreur
sudo dpkg -i paquet.deb
# Erreur de dépendances...

# Résoudre les dépendances
sudo apt --fix-broken install
# ou
sudo apt -f install
```

**💡 apt télécharge et installe les dépendances manquantes**

---

#### Solution 2 : Utiliser apt directement

```bash
# apt gère les dépendances automatiquement
sudo apt install ./paquet.deb
#                 │
#                 └─── ./ indique fichier local (pas dépôt)
```

**Recommandé** ✅

---

## 🗑️ dpkg -r : Désinstaller un Paquet

### Syntaxe

```bash
dpkg -r nom-paquet
```

**Rôle** : **R**emove (supprimer) un paquet

**💡 Important** : Conserve les **fichiers de configuration**

---

### Exemple

```bash
sudo dpkg -r google-chrome-stable
```

**Résultat** :
```
(Lecture de la base de données... 245678 fichiers et répertoires déjà installés.)
Suppression de google-chrome-stable (108.0.5359.124-1) ...
```

**Ce qui est supprimé** :
- ✅ Exécutables (`/usr/bin/`)
- ✅ Bibliothèques (`/usr/lib/`)
- ✅ Documentation (`/usr/share/doc/`)

**Ce qui reste** :
- ⚠️ Fichiers configuration (`/etc/`)
- ⚠️ Préférences utilisateur (`~/.config/`)

**Usage** : Désinstaller temporairement (réinstaller sans reconfigurer)

---

## 🧹 dpkg -P : Purger Complètement

### Syntaxe

```bash
dpkg -P nom-paquet
```

**Rôle** : **P**urge (supprimer + config)

**Analogie** 🧹 :
- `dpkg -r` = Déménager (laisser quelques affaires)
- `dpkg -P` = Déménager + nettoyer complètement

---

### Exemple

```bash
sudo dpkg -P google-chrome-stable
```

**Résultat** :
```
(Lecture de la base de données...)
Suppression de google-chrome-stable (108.0.5359.124-1) ...
Purge des fichiers de configuration de google-chrome-stable (108.0.5359.124-1) ...
```

**Ce qui est supprimé** :
- ✅ Exécutables
- ✅ Bibliothèques
- ✅ Documentation
- ✅ **Fichiers de configuration** (`/etc/`)

**⚠️ Attention** : Irréversible ! (config perdue)

**Usage** : Nettoyage complet avant réinstallation propre

---

### Comparaison -r vs -P

| Critère | dpkg -r | dpkg -P |
|---------|---------|---------|
| Supprime binaires | ✅ | ✅ |
| Supprime libs | ✅ | ✅ |
| Supprime doc | ✅ | ✅ |
| Supprime config `/etc/` | ❌ | ✅ |
| Réinstallation | Garde config | Config par défaut |
| Usage | Désinstall temporaire | Nettoyage complet |

---

## ⚡ dpkg --force : Forcer Installation

### Syntaxe

```bash
dpkg --force-option -i paquet.deb
```

**Rôle** : **Bypasser** les protections de dpkg

**⚠️ DANGER** : À utiliser **quasi jamais** !

**Analogie** 🚨 : Couper le circuit de sécurité électrique

---

### Options --force Courantes

| Option | Effet |
|--------|-------|
| `--force-depends` | Ignorer dépendances manquantes |
| `--force-conflicts` | Ignorer conflits entre paquets |
| `--force-overwrite` | Écraser fichiers d'autres paquets |
| `--force-downgrade` | Installer version plus ancienne |
| `--force-all` | Ignorer TOUTES protections ☠️ |

---

### Exemple (À NE PAS FAIRE)

```bash
# ❌ MAUVAISE PRATIQUE
sudo dpkg --force-depends -i paquet.deb
```

**Risques** :
- 💥 Système cassé (dépendances manquantes)
- 💥 Conflits de fichiers
- 💥 Instabilité générale

**Quand utiliser ?**
- 🆘 Récupération système cassé (mode rescue)
- 🆘 Instructions explicites support technique
- 🆘 Vous savez **exactement** ce que vous faites

**💡 Préférer toujours** : `apt --fix-broken install`

---

## ℹ️ dpkg -I : Informations sur un Paquet

### Syntaxe

```bash
dpkg -I paquet.deb
```

**Rôle** : Afficher **I**nfo d'un paquet `.deb` (non installé)

**💡 Point clé** : Inspecter **avant** installation

---

### Exemple

```bash
dpkg -I google-chrome-stable_current_amd64.deb
```

**Résultat** :
```
 nouveau paquet Debian, version 2.0.
 taille 89234560 octets : archive de contrôle = 4520 octets.
    1234 octets,    25 lignes      control
   12345 octets,   156 lignes      md5sums
     456 octets,    12 lignes   *  postinst             #!/bin/sh
     234 octets,     8 lignes   *  postrm               #!/bin/sh
     123 octets,     5 lignes   *  prerm                #!/bin/sh
 Package: google-chrome-stable
 Version: 108.0.5359.124-1
 Architecture: amd64
 Maintainer: Chrome Linux Team <chrome-linux-team@google.com>
 Installed-Size: 298456
 Depends: ca-certificates, fonts-liberation, libasound2 (>= 1.0.16), libatk-bridge2.0-0 (>= 2.5.3)
 Section: web
 Priority: optional
 Description: The web browser from Google
  Google Chrome is a browser that combines a minimal design with sophisticated technology to make the web faster, safer, and easier.
```

**Sections importantes** :
- **Package** : Nom du paquet
- **Version** : Numéro version
- **Architecture** : amd64, i386, arm64...
- **Installed-Size** : Taille après installation (Ko)
- **Depends** : Dépendances requises
- **Description** : Explication paquet

**Usage** : Vérifier compatibilité avant installer

---

### Lire Section Spécifique

```bash
# Voir seulement le fichier control
dpkg -I paquet.deb control

# Voir les scripts post-installation
dpkg -I paquet.deb postinst
```

---

## 📋 dpkg --get-selections : Lister Paquets Installés

### Syntaxe

```bash
dpkg --get-selections
```

**Rôle** : Afficher **tous** les paquets avec leur état

---

### Exemple

```bash
dpkg --get-selections
```

**Résultat** :
```
accountsservice                install
acl                            install
adduser                        install
apache2                        install
apache2-bin                    install
apache2-data                   install
apt                            install
apt-utils                      install
base-files                     install
bash                           install
bind9-dnsutils                 install
...
vim                            install
wget                           install
```

**Format** : `nom-paquet<TAB>état`

**États possibles** :
- `install` → Installé
- `deinstall` → Supprimé (config reste)
- `purge` → Marqué pour purge
- `hold` → Verrouillé (pas de màj)

---

### Filtrer la Liste

```bash
# Seulement les paquets installés
dpkg --get-selections | grep -w install

# Chercher un paquet spécifique
dpkg --get-selections | grep apache
# apache2                            install
# apache2-bin                        install

# Compter paquets installés
dpkg --get-selections | grep -c install
# 1234
```

---

### Sauvegarder/Restaurer Liste

**Sauvegarder** :
```bash
dpkg --get-selections > mes-paquets.txt
```

**Restaurer** (autre machine) :
```bash
# 1. Restaurer sélections
sudo dpkg --set-selections < mes-paquets.txt

# 2. Installer tous les paquets
sudo apt-get dselect-upgrade
```

**Usage** : Cloner configuration entre serveurs

---

## 📝 dpkg -l : Lister Paquets (Format Détaillé)

### Syntaxe

```bash
dpkg -l [motif]
```

**Rôle** : **L**ister paquets (format tableau)

**Différence avec --get-selections** :
- `--get-selections` → Format simple (nom + état)
- `-l` → Format riche (version, architecture, description)

---

### Exemple

```bash
dpkg -l
```

**Résultat** :
```
Souhait=inconnU/Installé/suppRimé/Purgé/H=à garder
| État=Non/Installé/fichier-Config/dépaqUeté/échec-conFig/H=semi-installé/W=attente-déclenchement
|/ Err?=(aucune)/besoin Réinstallation (État,Err: majuscule=mauvais)
||/ Nom                     Version          Architecture Description
+++-========================-================-============-=================================
ii  accountsservice          0.6.55-0ubuntu12 amd64        query and manipulate user account
ii  acl                      2.3.1-1          amd64        access control list - utilities
ii  adduser                  3.118ubuntu5     all          add and remove users and groups
ii  apache2                  2.4.52-1ubuntu4  amd64        Apache HTTP Server
ii  apt                      2.4.5            amd64        commandline package manager
rc  bind9                    1:9.18.1-1       amd64        Internet Domain Name Server
```

**Colonnes** :
1. **État désiré** : i (install), r (remove), p (purge), h (hold)
2. **État actuel** : i (installed), c (config-files), n (not-installed)
3. **Erreur** : vide (ok), R (reinstall needed)
4. **Nom** : Nom du paquet
5. **Version** : Version installée
6. **Architecture** : amd64, i386, all...
7. **Description** : Courte description

---

### Codes État Importants

| Code | Signification |
|------|---------------|
| `ii` | **Installé** correctement ✅ |
| `rc` | **Supprimé**, config reste |
| `un` | **Non installé** |
| `iU` | **Installé**, màj disponible |
| `iF` | **Échec** configuration ❌ |

**💡 Focus sur** : `ii` (bon état) et `iF` (problème)

---

### Filtrer avec Motif

```bash
# Tous les paquets apache
dpkg -l apache*
# apache2
# apache2-bin
# apache2-data

# Tous les paquets contenant "python"
dpkg -l '*python*'

# Paquet spécifique
dpkg -l vim
```

---

### Identifier Paquets Cassés

```bash
# Chercher état anormal
dpkg -l | grep ^iF
# iF  paquet-casse    1.2.3    amd64    Broken package

# Réparer
sudo dpkg --configure -a
sudo apt --fix-broken install
```

---

## 📂 dpkg -L : Fichiers d'un Paquet

### Syntaxe

```bash
dpkg -L nom-paquet
```

**Rôle** : **L**ister fichiers installés par un paquet

**💡 Point clé** : Paquet doit être **déjà installé**

---

### Exemple

```bash
dpkg -L vim
```

**Résultat** :
```
/.
/usr
/usr/bin
/usr/bin/vim.basic
/usr/bin/vim.tiny
/usr/share
/usr/share/applications
/usr/share/applications/vim.desktop
/usr/share/doc
/usr/share/doc/vim
/usr/share/doc/vim/changelog.Debian.gz
/usr/share/doc/vim/copyright
/usr/share/icons
/usr/share/icons/hicolor
/usr/share/icons/hicolor/48x48
/usr/share/icons/hicolor/48x48/apps
/usr/share/icons/hicolor/48x48/apps/gvim.png
/usr/share/man
/usr/share/man/man1
/usr/share/man/man1/vim.1.gz
...
```

**Catégories de fichiers** :
- `/usr/bin/` → Exécutables
- `/usr/share/doc/` → Documentation
- `/usr/share/man/` → Pages manuel
- `/etc/` → Configuration
- `/usr/lib/` → Bibliothèques

---

### Cas d'Usage Pratiques

#### Trouver Emplacement Exécutable

```bash
dpkg -L apache2 | grep bin
# /usr/sbin/apache2
# /usr/sbin/apache2ctl
```

---

#### Voir Fichiers Config

```bash
dpkg -L nginx | grep /etc/
# /etc/nginx
# /etc/nginx/nginx.conf
# /etc/nginx/sites-available
# /etc/nginx/sites-enabled
```

---

#### Compter Fichiers Installés

```bash
dpkg -L firefox | wc -l
# 4567
```

---

## 🔍 dpkg-query -S : Quel Paquet a Installé ce Fichier ?

### Syntaxe

```bash
dpkg-query -S /chemin/fichier
```

**Rôle** : **S**earch (chercher) quel paquet possède un fichier

**Analogie** 🕵️ : Retrouver le fabricant d'une pièce

---

### Exemple

```bash
dpkg-query -S /usr/bin/vim
```

**Résultat** :
```
vim-basic: /usr/bin/vim.basic
vim-tiny: /usr/bin/vim.tiny
```

**Format** : `paquet: fichier`

**💡 Point clé** : Peut retourner **plusieurs paquets** (fichier partagé)

---

### Cas d'Usage Pratiques

#### Fichier de Configuration

```bash
dpkg-query -S /etc/nginx/nginx.conf
# nginx-core: /etc/nginx/nginx.conf
```

**Usage** : Savoir quel paquet reconfigurer

---

#### Bibliothèque

```bash
dpkg-query -S /usr/lib/x86_64-linux-gnu/libssl.so.1.1
# libssl1.1:amd64: /usr/lib/x86_64-linux-gnu/libssl.so.1.1
```

**Usage** : Comprendre dépendances

---

#### Commande Système

```bash
dpkg-query -S /bin/ls
# coreutils: /bin/ls

dpkg-query -S /usr/bin/python3
# python3-minimal: /usr/bin/python3.10
```

---

#### Recherche par Motif

```bash
# Tous fichiers nginx
dpkg-query -S '*nginx*'

# Fichiers Python en /usr/bin
dpkg-query -S /usr/bin/python*
```

---

### Fichier Non Trouvé

```bash
dpkg-query -S /usr/local/bin/custom
```

**Résultat** :
```
dpkg-query: aucun paquet trouvé possédant /usr/local/bin/custom
```

**Signification** :
- ✅ Fichier installé manuellement (pas par paquet)
- ✅ Fichier compilé localement
- ✅ Script utilisateur

---

## 🔧 dpkg-reconfigure : Reconfigurer Paquet

### Syntaxe

```bash
dpkg-reconfigure nom-paquet
```

**Rôle** : Relancer l'**assistant de configuration** post-installation

**Analogie** 🔄 : Redémarrer l'installation Windows pour changer paramètres

---

### Exemple Typique : Timezone

```bash
sudo dpkg-reconfigure tzdata
```

**Effet** :
```
┌─────────────┤ Configuring tzdata ├──────────────┐
│ Please select the geographic area in which you  │
│ live. Subsequent configuration questions will   │
│ narrow this down by presenting a list of cities │
│                                                  │
│         1. Africa      7. Australia             │
│         2. America     8. Europe                │
│         3. Antarctica  9. Indian                │
│         4. Arctic     10. Pacific               │
│         5. Asia       11. SystemV               │
│         6. Atlantic   12. US                    │
│                                                  │
│              <Ok>          <Cancel>             │
└──────────────────────────────────────────────────┘
```

**Usage** : Changer fuseau horaire

---

### Exemple : Locales

```bash
sudo dpkg-reconfigure locales
```

**Effet** : Assistant choix langues système (fr_FR.UTF-8, en_US.UTF-8...)

---

### Exemple : Clavier

```bash
sudo dpkg-reconfigure keyboard-configuration
```

**Effet** : Reconfigurer disposition clavier (AZERTY, QWERTY...)

---

### Exemple : Réseau

```bash
sudo dpkg-reconfigure resolvconf
```

**Effet** : Reconfigurer DNS

---

### Cas d'Usage

#### Configuration Corrompue

```bash
# Config paquet cassée
sudo dpkg-reconfigure paquet-probleme
# Régénère config par défaut
```

---

#### Changer Paramètres

```bash
# Changer niveau interactivité Debconf
sudo dpkg-reconfigure debconf
# critical / high / medium / low
```

---

#### Réinitialiser Config

```bash
# Option 1 : Purge + réinstall
sudo dpkg -P paquet
sudo apt install paquet

# Option 2 : dpkg-reconfigure (plus rapide)
sudo dpkg-reconfigure paquet
```

**💡 dpkg-reconfigure** = Plus rapide que purge/reinstall

---

## 📋 Antisèche - Commandes DPKG

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `dpkg -i paquet.deb` | Installer paquet local | `sudo dpkg -i chrome.deb` |
| `dpkg -r paquet` | Désinstaller (garde config) | `sudo dpkg -r apache2` |
| `dpkg -P paquet` | Purger (suppr. config) | `sudo dpkg -P apache2` |
| `dpkg -I paquet.deb` | Infos sur paquet .deb | `dpkg -I chrome.deb` |
| `dpkg --get-selections` | Lister tous paquets | `dpkg --get-selections` |
| `dpkg -l` | Lister paquets (détaillé) | `dpkg -l` |
| `dpkg -l motif` | Chercher paquet | `dpkg -l apache*` |
| `dpkg -L paquet` | Fichiers d'un paquet | `dpkg -L vim` |
| `dpkg-query -S fichier` | Quel paquet a ce fichier ? | `dpkg-query -S /bin/ls` |
| `dpkg-reconfigure paquet` | Reconfigurer paquet | `sudo dpkg-reconfigure tzdata` |
| `apt --fix-broken install` | Réparer dépendances | `sudo apt -f install` |
| `dpkg --configure -a` | Configurer paquets cassés | `sudo dpkg --configure -a` |

---

## 🎓 Points Clés pour l'Examen

✅ **DPKG** : Gestionnaire paquets bas niveau Debian/Ubuntu  
✅ **Format** : `.deb`  
✅ **dpkg -i** : Installer paquet local  
✅ **dpkg -r** : Remove (garde config)  
✅ **dpkg -P** : Purge (supprime config)  
✅ **dpkg --force** : ⚠️ Forcer (DANGER, éviter)  
✅ **dpkg -I** : Info paquet `.deb` (avant install)  
✅ **dpkg --get-selections** : Lister paquets (format simple)  
✅ **dpkg -l** : Lister paquets (format tableau)  
✅ **dpkg -L** : Fichiers installés par paquet  
✅ **dpkg-query -S** : Quel paquet possède un fichier  
✅ **dpkg-reconfigure** : Relancer config post-install  
✅ **apt vs dpkg** : apt = haut niveau (dépendances), dpkg = bas niveau  
✅ **Réparer dépendances** : `apt --fix-broken install`  
✅ **États** : `ii` (installé), `rc` (config reste), `iF` (cassé)  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Installer Paquet Téléchargé

```bash
# 1. Télécharger .deb
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb

# 2. Inspecter avant install
dpkg -I google-chrome-stable_current_amd64.deb
# Vérifier dépendances, version, architecture

# 3. Installer
sudo dpkg -i google-chrome-stable_current_amd64.deb

# 4. Si erreur dépendances
sudo apt --fix-broken install
```

---

### Scénario 2 : Désinstaller Proprement

```bash
# Méthode 1 : Remove (garde config)
sudo dpkg -r apache2

# Méthode 2 : Purge (supprime config)
sudo dpkg -P apache2

# Vérifier suppression
dpkg -l apache2
# rc  → Removed, config reste
# (vide) → Purgé complètement
```

---

### Scénario 3 : Audit Fichiers Paquet

```bash
# Quel paquet a installé ce fichier ?
dpkg-query -S /etc/nginx/nginx.conf
# nginx-core: /etc/nginx/nginx.conf

# Quels fichiers ce paquet a installés ?
dpkg -L nginx-core
# /etc/nginx/nginx.conf
# /usr/sbin/nginx
# /var/log/nginx/
# ...

# Compter fichiers
dpkg -L nginx-core | wc -l
# 234
```

---

### Scénario 4 : Réparer Paquet Cassé

```bash
# Symptôme : apt échoue
sudo apt install nouveau-paquet
# dpkg: problèmes de dépendances...

# Diagnostic
dpkg -l | grep ^iF
# iF  paquet-casse    1.2.3

# Réparation
sudo dpkg --configure -a
sudo apt --fix-broken install

# Vérification
dpkg -l paquet-casse
# ii  paquet-casse    1.2.3  ✅
```

---

### Scénario 5 : Cloner Configuration

```bash
# Machine source
dpkg --get-selections > paquets-serveur.txt

# Copier fichier vers nouvelle machine
scp paquets-serveur.txt user@new-server:~

# Machine destination
sudo dpkg --set-selections < paquets-serveur.txt
sudo apt-get dselect-upgrade
# Installe tous les paquets listés
```

---

### Scénario 6 : Changer Timezone

```bash
# Voir timezone actuelle
timedatectl
# Time zone: UTC

# Reconfigurer
sudo dpkg-reconfigure tzdata
# Sélectionner : Europe → Paris

# Vérifier
timedatectl
# Time zone: Europe/Paris (CET, +0100)
```

---

### Scénario 7 : Recherche Avancée Paquets

```bash
# Tous paquets Python installés
dpkg -l '*python*' | grep ^ii

# Paquets Apache (tous états)
dpkg -l apache*

# Paquets avec config restante (orphelins)
dpkg -l | grep ^rc
# rc  old-package    1.0.0

# Purger tous orphelins
dpkg -l | grep ^rc | awk '{print $2}' | xargs sudo dpkg -P
```

---

### Scénario 8 : Downgrade Paquet (Rare)

```bash
# Télécharger ancienne version
wget http://archive.ubuntu.com/ubuntu/pool/main/v/vim/vim_8.0.1234_amd64.deb

# Forcer downgrade (⚠️ risqué)
sudo dpkg -i --force-downgrade vim_8.0.1234_amd64.deb

# Verrouiller version (empêcher màj)
echo "vim hold" | sudo dpkg --set-selections

# Vérifier
dpkg --get-selections | grep vim
# vim    hold

# Débloquer plus tard
echo "vim install" | sudo dpkg --set-selections
```

---

## 🔄 Workflow Complet

### Installation Standard

```
1. Télécharger .deb
   wget paquet.deb

2. Inspecter
   dpkg -I paquet.deb

3. Installer (méthode recommandée)
   sudo apt install ./paquet.deb
   (gère dépendances automatiquement)

   OU (bas niveau)
   sudo dpkg -i paquet.deb
   sudo apt --fix-broken install
```

---

### Désinstallation Complète

```
1. Lister fichiers
   dpkg -L paquet

2. Désinstaller
   sudo dpkg -r paquet   (garde config)
   OU
   sudo dpkg -P paquet   (purge config)

3. Vérifier
   dpkg -l paquet
   (vide ou rc)
```

---

### Diagnostic Problème

```
1. Voir état
   dpkg -l paquet

2. Si iF (cassé)
   sudo dpkg --configure -a
   sudo apt --fix-broken install

3. En dernier recours
   sudo dpkg -P paquet
   sudo apt install paquet
```

---

## ⚠️ Erreurs Courantes

### Erreur 1 : Dépendances Non Satisfaites

```
dpkg: problèmes de dépendances empêchent la configuration de paquet
```

**Solution** :
```bash
sudo apt --fix-broken install
```

---

### Erreur 2 : Paquet Déjà Installé

```
dpkg: erreur de traitement de paquet (--install):
 tentative d'écrasement de...
```

**Solution** :
```bash
# Option 1 : Réinstaller
sudo dpkg -i --force-overwrite paquet.deb

# Option 2 : Remove puis install
sudo dpkg -r paquet
sudo dpkg -i paquet.deb
```

---

### Erreur 3 : Architecture Incompatible

```
paquet-i386.deb
dpkg: erreur: architecture 'i386' incompatible avec 'amd64'
```

**Solution** :
```bash
# Télécharger version correcte
wget paquet-amd64.deb

# Ou activer multi-arch (avancé)
sudo dpkg --add-architecture i386
sudo apt update
```

---

### Erreur 4 : Aucun Paquet Trouvé (dpkg-query)

```
dpkg-query -S /opt/custom/bin/app
dpkg-query: aucun paquet trouvé
```

**Normal** : Fichier installé manuellement (pas par paquet)

---

## 🗂️ Base de Données DPKG

**Emplacement** : `/var/lib/dpkg/`

**Fichiers importants** :

| Fichier | Contenu |
|---------|---------|
| `/var/lib/dpkg/status` | État tous paquets installés |
| `/var/lib/dpkg/available` | Paquets disponibles |
| `/var/lib/dpkg/info/paquet.list` | Fichiers installés par paquet |
| `/var/lib/dpkg/info/paquet.conffiles` | Fichiers config du paquet |

**Exemple** :
```bash
# Voir fichiers d'un paquet (alternative à dpkg -L)
cat /var/lib/dpkg/info/vim.list

# Voir config
cat /var/lib/dpkg/info/nginx-core.conffiles
# /etc/nginx/nginx.conf
# /etc/nginx/sites-available/default
```

**💡 Ne JAMAIS modifier manuellement** (risque corruption base)

---

**🎯 Prochaine étape** : Use Debian Package Management - APT

*Dernière mise à jour: 31 janvier 2026*
