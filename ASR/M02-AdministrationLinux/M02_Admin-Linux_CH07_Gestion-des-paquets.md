# M02 - Administration Linux

## CH07 - Gestion des paquets

RHEL contient environ 10 000 paquets. C'est une distribution orientée entreprise et serveur : la priorité est la **stabilité**, pas la quantité de logiciels. Certains paquets arrivent en version stable plus tardivement que sur d'autres distributions, et c'est voulu.

---

### 7.1 - Principes généraux

Le système de gestion de paquets sous Linux existe depuis les années 2000, bien avant les stores d'Apple, Google ou Microsoft. Le principe est le même :

- 📦 Des **paquets** (applications compilées et empaquetées) sont hébergés sur des **dépôts** (repos) en ligne
- 🔐 Chaque paquet est **signé par clé GPG** pour garantir son intégrité et son origine
- 🔗 Le gestionnaire de paquets gère automatiquement les **dépendances**
- 🔄 Installation, mise à jour et suppression sont centralisées

---

### 7.2 - Architecture côté serveur

#### 📦 Les paquets RPM

Un paquet RPM (Red Hat Package Manager) est une archive contenant :

- Le logiciel compilé pour la distribution
- Des scripts pre/post-installation
- Une signature GPG
- Des métadonnées (description, liste des fichiers, dépendances, etc.)

#### 📁 Les groupes de paquets

Un groupe est un méta-paquet qui contient la liste des RPM nécessaires à un usage donné. Exemple : le groupe **"Outils de développement"** installe tout ce qu'il faut pour compiler.

#### 🌐 Les dépôts (repositories)

Un dépôt est un site web structuré contenant :

- Une base de données indexant tous les paquets disponibles
- Les fichiers RPM signés

#### 🪞 Les miroirs (mirrors)

Les miroirs sont des répliques des dépôts réparties géographiquement pour améliorer la disponibilité et les performances.

⚠️ Un fichier `.repo` ne peut pas avoir les directives `mirrorlist` et `baseurl` actives en même temps.

---

### 7.3 - Fichiers de configuration

#### 📄 Configuration globale

Le fichier `/etc/yum.conf` définit le comportement général de YUM/DNF.

#### 📄 Configuration des dépôts

Les dépôts sont déclarés dans des fichiers `.repo` dans `/etc/yum.repos.d/`.

Structure d'un fichier `.repo` :

```ini
[ol9_baseos_latest]
name=Oracle Linux 9 BaseOS Latest ($basearch)
baseurl=https://yum.oracle.com/repo/OracleLinux/OL9/baseos/latest/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
enabled=1
```

| Directive | Rôle |
| --- | --- |
| `[nom]` | Identifiant unique du dépôt |
| `name=` | Nom descriptif |
| `baseurl=` | URL du serveur de dépôt |
| `mirrorlist=` | URL du miroir (mutuellement exclusif avec `baseurl`) |
| `gpgcheck=1` | Active la vérification de signature GPG |
| `gpgkey=` | Emplacement de la clé publique GPG |
| `enabled=1` | Active le dépôt (absent = actif par défaut) |

💡 Il est possible d'ajouter des dépôts tiers comme **EPEL** (Extra Packages for Enterprise Linux) ou **RPMforge** pour accéder à des logiciels non inclus dans la distribution.

---

### 7.4 - DNF (haut niveau)

`dnf` est le gestionnaire de paquets haut niveau de RHEL 8+/Oracle Linux 9. Il remplace `yum` (qui reste disponible comme alias de compatibilité). Sa syntaxe est quasi identique à `yum`.

#### Fonctionnement interne

1. `dnf` télécharge la base de données des paquets depuis les repos (mise en **cache local**, rafraîchi automatiquement toutes les 6h)
2. Les commandes d'info interrogent le **cache local**, pas les repos
3. Lors d'une installation, `dnf` résout les dépendances, télécharge les RPM, puis les installe via `rpm` (commande bas niveau)

#### 🔧 Commandes principales

##### 📋 Gestion des dépôts

```bash
dnf repolist           # Repos actifs
dnf repolist --all     # Repos actifs ET inactifs
```

##### 🔍 Recherche et information

```bash
dnf list installed         # Paquets installés
dnf list available         # Paquets disponibles
dnf list all               # Tous les paquets
dnf list kernel            # Versions du kernel installées et dispo
dnf search <mot>           # Rechercher un paquet par mot-clé
dnf info <paquet>          # Infos détaillées sur un paquet
dnf provides "*/fichier"   # Quel paquet contient ce fichier ?
dnf grouplist              # Lister les groupes de paquets
```

💡 `dnf provides` est très utile quand une compilation échoue avec un message du type `Could not find superlib.so` : il permet de retrouver le paquet RPM contenant la lib manquante.

##### 📥 Installation

```bash
# dnf install <paquet>                 # Depuis les dépôts
# dnf install <fichier.rpm>            # Depuis un fichier local
# dnf install <url://vers/paquet.rpm>  # Depuis une URL
# dnf groupinstall "<groupe>"          # Installer un groupe
# dnf install @"<groupe>"              # Syntaxe alternative pour les groupes
```

##### 🗑️ Suppression

```bash
# dnf remove <paquet>
# dnf remove @"<groupe>"
# dnf autoremove                       # Supprimer les dépendances orphelines
```

##### 🔄 Mise à jour

```bash
# dnf check-update                     # Vérifier les MAJ disponibles
# dnf check-update --sec-severity Important  # MAJ de sécurité importantes
# dnf update <paquet>                  # MAJ d'un paquet spécifique
# dnf update                           # MAJ complète du système
# dnf update kernel                    # MAJ uniquement le kernel
```

📌 `dnf upgrade` et `dnf update` sont des alias : même comportement.

##### 📜 Historique

```bash
$ dnf history                  # Voir l'historique des transactions
$ dnf history info <n°>        # Détail d'une transaction
# dnf history undo <n°>        # Annuler une transaction
# dnf history redo <n°>        # Rejouer une transaction
```

💡 L'historique `dnf` est très puissant : il permet de revenir en arrière sur une installation ou mise à jour problématique.

##### 🧹 Cache

```bash
dnf clean all                # Vider tout le cache local
dnf makecache                # Regénérer le cache
```

---

### 7.5 - RPM (bas niveau)

Avant `dnf` (et même avant `yum`), l'installation se faisait directement avec `rpm`. Cette commande reste indispensable pour l'interrogation fine des paquets.

#### ⚠️ Limites de `rpm`

- Ne télécharge **pas** les paquets (pas de résolution de dépôts)
- Ne résout **pas** les dépendances automatiquement (les signale mais ne les installe pas)

#### 🔧 Commandes essentielles

##### 📥 Installation / Mise à jour

```bash
# rpm -vUh <paquet.rpm>    # Installe OU met à jour (recommandé par Red Hat)
# rpm -ivh <paquet.rpm>    # Installe uniquement (échoue si déjà présent)
```

- `-v` : verbeux
- `-U` : upgrade (installe si absent, met à jour si présent)
- `-h` : affiche une barre de progression

##### 🗑️ Désinstallation

```bash
# rpm -e <nom_programme>   # Utiliser le nom court, pas le nom complet du .rpm
```

⚠️ Si le paquet est une dépendance d'un autre, `rpm` vous avertira.

##### 🔍 Interrogation (queries)

```bash
rpm -qa                  # Lister TOUS les paquets installés
rpm -qi <paquet>         # Infos détaillées
rpm -ql <paquet>         # Lister les fichiers installés par le paquet
rpm -qf /chemin/fichier  # Quel paquet a installé ce fichier ?
rpm -qd <paquet>         # Fichiers de documentation
rpm -qc <paquet>         # Fichiers de configuration
rpm -q --scripts <paquet>  # Scripts pre/post install
rpm -qp <fichier.rpm>    # Interroger un .rpm non installé (ajouter -p)
```

##### ✅ Vérification

```bash
rpm -V <paquet>          # Vérifier l'intégrité d'un paquet installé
rpm -Va                  # Vérifier TOUS les paquets
```

---

### 7.6 - Installation à partir des sources

Dernière méthode d'installation : la compilation depuis les sources. C'est la plus flexible mais aussi la plus complexe.

#### ✅ Avantages

- Accès à la **dernière version** du logiciel
- **Hautement configurable** (options de compilation)

#### ⚠️ Inconvénients

- Nécessite les **outils de compilation** (`gcc`, `make`, etc.)
- Gestion manuelle des **dépendances** (fonctionnelles ET de compilation)
- Pas de scripts de service automatiques (si c'est un daemon)
- Pas intégré au gestionnaire de paquets (pas de `dnf remove` possible)

#### 📌 Les deux types de dépendances

| Type | Rôle | Nommage typique |
| --- | --- | --- |
| Fonctionnelles | Nécessaires au fonctionnement | `programme2000.rpm` |
| De compilation | Nécessaires à la compilation (headers, libs) | `programme2000-devel.rpm` |

💡 Si la compilation échoue avec `Could not find superlib.so`, utiliser :

```bash
dnf provides "*/superlib.so"
```

#### 🔧 Les 4 étapes de compilation

⚠️ **Toujours compiler sous un utilisateur lambda, JAMAIS en root** (sauf l'étape `make install`).

##### 1. 📖 Lire la documentation (README, INSTALL)

Les fichiers README/INSTALL listent les dépendances et les options de compilation.

##### 2. ⚙️ Configuration

```bash
./configure
./configure --prefix=/opt/monapp   # Installer dans un répertoire custom
```

Vérifie l'architecture, le compilateur, les bibliothèques. Génère le `Makefile`.

##### 3. 🏗️ Compilation

```bash
make
```

Étape la plus longue. Génère les binaires et fichiers liés.

**4. 📦 Installation** (en root)

```bash
# make install
# make install DESTDIR=/opt/monapp   # Installer dans un répertoire spécifique
```

Copie les fichiers compilés dans l'arborescence système (ou le répertoire custom spécifié).

---

### 🧪 Atelier 6 - Gestion des applications

#### Sur toutes les machines

🔧 Modifier les URLs des dépôts de `https` en `http` (astuce one-liner) :

```bash
# sed -i 's/https:/http:/g' /etc/yum.repos.d/*.repo
```

🔧 Mettre à jour uniquement le kernel :

```bash
# dnf update kernel
$ dnf list kernel          # Voir les versions installées et disponibles
```

🔧 Désinstaller nano :

```bash
# dnf remove nano
```

#### Sur `srv-gui`

```bash
$ dnf list --installed firefox              # Voir de quel dépôt vient firefox
$ dnf repolist --all                        # Lister repos actifs et inactifs
# dnf install epel-release && dnf install terminator   # Ajouter EPEL + installer terminator
$ dnf check-update --sec-severity Important # MAJ de sécurité importantes
# dnf update                                # MAJ complète
# dnf clean all                             # Nettoyage du cache
```

#### Sur `srv-cli`

```bash
# dnf update -y       # MAJ complète (auto-confirm)
# dnf clean all        # Nettoyage du cache
```

#### Bonus : compilation depuis les sources (Vim)

```bash
# Récupérer les sources
$ git clone --branch v9.1.0837 https://github.com/vim/vim.git

# Installer les dépendances de compilation
# dnf install gcc ncurses-devel

# Créer le répertoire d'installation
# mkdir /opt/vim

# Compiler et installer
$ cd vim
$ ./configure
$ make
# make install DESTDIR=/opt/vim

# Exécuter
$ /opt/vim/usr/local/bin/vim
```

#### Bonus : installation depuis un dépôt Git (atop)

```bash
# Installer git et les dépendances
# dnf install git gcc make zlib-devel ncurses-devel

# Cloner le dépôt
$ git clone https://github.com/Atoptool/atop.git

# Compiler et installer
$ cd atop
$ make
# make install

# Exécuter
$ atop
```

---

### 7.7 - Parcours d'un logiciel : des sources au dépôt

Le cycle de vie simplifié d'un paquet :

1. 👨‍💻 Un développeur crée une application et publie les sources
2. 📦 Un "packageur" de la distribution génère les RPM (32 bit, 64 bit, src.rpm) avec scripts, signature GPG, métadonnées
3. 🧪 Phase de tests et débogage
4. 🌐 Intégration au dépôt officiel
5. 👤 L'utilisateur final installe via `dnf install`

---

### 📌 À retenir

1. **`dnf`** est le gestionnaire haut niveau (résolution de dépendances, téléchargement). **`rpm`** est le bas niveau (interrogation fine, installation locale).
2. Les dépôts sont configurés dans `/etc/yum.repos.d/*.repo`. Ne pas mélanger `baseurl` et `mirrorlist`.
3. **`rpm -qa`** pour lister tous les paquets, **`rpm -qf /chemin`** pour trouver le paquet propriétaire d'un fichier.
4. **`dnf provides "*/fichier"`** est l'outil indispensable pour résoudre les dépendances manquantes lors d'une compilation.
5. Compilation depuis les sources : `./configure && make && make install`. Toujours compiler en **utilisateur non-root**.
