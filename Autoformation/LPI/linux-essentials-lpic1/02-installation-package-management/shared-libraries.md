# 📚 Gérer les Bibliothèques Partagées

---

## 🤔 Qu'est-ce qu'une Bibliothèque Logicielle ?

**Bibliothèque** = Collection de **code réutilisable** utilisée par plusieurs programmes

**Analogie** 📚 : 
- **Programme** = Votre recette de cuisine
- **Bibliothèque** = Livre de techniques culinaires que vous consultez

**Avantages** :
- ✅ Évite la duplication de code
- ✅ Facilite les mises à jour (un seul endroit)
- ✅ Réduit la taille des exécutables
- ✅ Partage de code entre applications

**Exemples concrets** :
- `libc` → Fonctions C standard (printf, malloc, etc.)
- `libpthread` → Gestion des threads
- `libssl` → Cryptographie SSL/TLS
- `libcurl` → Téléchargement HTTP/FTP

---

## 🏗️ De Code Source à Exécutable

### Les 2 Étapes de la Compilation

```
Code Source (.c, .cpp)
        ↓
   1. COMPILER
        ↓
Fichiers Objets (.o)
        ↓
   2. LINKER
        ↓
Exécutable Final
```

---

### 1. Compiler - Code Source → Code Machine

**Rôle** : Transformer le code source en **code machine** (fichiers objets)

```bash
# Exemple avec GCC
gcc -c program.c -o program.o
#    │    │             │
#    │    │             └─── Fichier objet de sortie
#    │    └─────────────── Code source
#    └──────────────────── Option -c (compile sans linker)
```

**Résultat** : Fichier `.o` (code machine, mais pas encore exécutable)

**Analogie** 🧩 : Fabriquer des pièces de puzzle individuelles

---

### 2. Linker - Assemblage Final

**Rôle** : Combiner les fichiers objets et les **lier aux bibliothèques**

```bash
# Exemple
gcc program.o -o program -lpthread
#   │          │  │        │
#   │          │  │        └─── Lier avec libpthread
#   │          │  └──────────── Exécutable final
#   │          └─────────────── Option -o (output)
#   └────────────────────────── Fichier objet
```

**Résultat** : Exécutable final prêt à lancer

**Analogie** 🧩 : Assembler toutes les pièces du puzzle pour former l'image complète

---

## 🔗 Linking Statique vs Dynamique

### Linking Statique

**Fonctionnement** : Bibliothèque **copiée directement** dans l'exécutable

```
Programme + Bibliothèque = Exécutable GROS
```

**Avantages** :
- ✅ Autonome (pas besoin de la lib sur le système)
- ✅ Pas de problème de version

**Inconvénients** :
- ❌ Exécutable très gros
- ❌ Mise à jour lib = recompiler tout
- ❌ Gaspillage mémoire (lib dupliquée)

**Extension** : `.a` (archive statique)

---

### Linking Dynamique (Partagé)

**Fonctionnement** : Bibliothèque **référencée**, chargée au lancement

```
Programme → Référence → Bibliothèque (séparée)
```

**Avantages** :
- ✅ Exécutable léger
- ✅ Mise à jour lib = tous programmes bénéficient
- ✅ Mémoire partagée (lib chargée 1× pour tous)

**Inconvénients** :
- ⚠️ Dépendance (lib doit être présente)
- ⚠️ Problèmes de version possibles

**Extension** : `.so` (shared object)

**💡 Standard Linux** : Linking dynamique par défaut

**Analogie** 📖 :
- **Statique** = Photocopier le dictionnaire dans chaque livre
- **Dynamique** = Référencer le dictionnaire sur l'étagère (partagé)

---

## 📛 Soname - Convention de Nommage

### Structure d'un Soname

```
libpthread.so.0
│   │      │  │
│   │      │  └─── Numéro de version (0, 1, 2...)
│   │      └────── Extension .so (Shared Object)
│   └───────────── Nom de la bibliothèque
└───────────────── Préfixe "lib" (library)
```

**Format général** :
```
lib + nom + .so + .version
```

---

### Exemples Réels

| Soname | Signification |
|--------|---------------|
| `libc.so.6` | Bibliothèque C standard, version 6 |
| `libpthread.so.0` | Bibliothèque threads POSIX, version 0 |
| `libssl.so.1.1` | OpenSSL, version 1.1 |
| `libcurl.so.4` | cURL library, version 4 |
| `libz.so.1` | Bibliothèque zlib (compression), version 1 |

---

### Pourquoi le Numéro de Version ?

**Problème** : Les API peuvent changer entre versions
- Version 0 → API compatible
- Version 1 → API incompatible (breaking changes)

**Solution** : Numéro de version dans le nom
- Programme compilé avec `libfoo.so.1` → Cherche toujours `.so.1`
- Version 2 installée → Ne casse pas les anciens programmes

**💡 Compatibilité garantie** : Plusieurs versions peuvent coexister

---

## 📁 Emplacements des Bibliothèques

### Répertoires Standards

```
/lib/                      → Bibliothèques essentielles (système de base)
/lib32/                    → Bibliothèques 32-bit
/lib64/                    → Bibliothèques 64-bit
/usr/lib/                  → Bibliothèques applications utilisateur
/usr/local/lib/            → Bibliothèques installées localement
```

---

### Sous-Répertoires par Architecture

**Format** : `/lib/architecture-os-gnu/`

**Exemples** :
```
/lib/x86_64-linux-gnu/     → Bibliothèques 64-bit Intel/AMD
/lib/i386-linux-gnu/       → Bibliothèques 32-bit Intel
/lib/aarch64-linux-gnu/    → Bibliothèques ARM 64-bit
```

**Pourquoi ?** Permet le multi-architecture sur un même système

---

### Exemple Concret : libc

```bash
ls -l /lib/x86_64-linux-gnu/libc*
```

**Résultat** :
```
lrwxrwxrwx libc.so.6 -> libc-2.31.so
-rwxr-xr-x libc-2.31.so
```

**Explication** :
- `libc-2.31.so` → Fichier réel (version 2.31)
- `libc.so.6` → **Lien symbolique** vers la version installée

---

### Pourquoi un Lien Symbolique ?

**Problème** : Mise à jour de version

```
Avant : libc-2.24.so
Après : libc-2.31.so
```

**Sans lien** : Tous les programmes cherchent `libc-2.24.so` → ❌ Cassé !

**Avec lien** :
```
libc.so.6 → libc-2.24.so  (avant mise à jour)
libc.so.6 → libc-2.31.so  (après mise à jour)
```

**Résultat** : Les programmes référencent `libc.so.6` → ✅ Fonctionne toujours !

**💡 Point clé** : Le linker référence le **lien symbolique**, pas le fichier réel

---

## ⚙️ Dynamic Linker - Le Chef d'Orchestre

### C'est quoi le Dynamic Linker ?

**Rôle** : Charger les bibliothèques partagées au lancement d'un programme

**Noms selon système** :
- `ld.so` (ancien, 32-bit)
- `ld-linux.so` (générique)
- `ld-linux-x86-64.so.2` (64-bit x86)
- `ld-linux-aarch64.so.1` (ARM 64-bit)

**Emplacement** : `/lib64/ld-linux-x86-64.so.2` (ou `/lib/`)

**Analogie** 🎭 : Le régisseur qui appelle les acteurs (bibliothèques) sur scène

---

### Voir le Dynamic Linker d'un Programme

```bash
ldd /bin/ls | grep ld-linux
# linux-vdso.so.1 (0x00007ffe...)
# /lib64/ld-linux-x86-64.so.2 (0x00007f...)
```

---

## 📝 Configuration du Dynamic Linker

### Fichier Principal : /etc/ld.so.conf

**Rôle** : Lister les **répertoires** contenant des bibliothèques

```bash
cat /etc/ld.so.conf
```

**Contenu typique** :
```
include /etc/ld.so.conf.d/*.conf
/usr/local/lib
```

**💡 Point clé** : Contient des **chemins**, pas des fichiers !

---

### Répertoire : /etc/ld.so.conf.d/

**Organisation modulaire** : Un fichier `.conf` par thématique

```bash
ls /etc/ld.so.conf.d/
```

**Résultat** :
```
libc.conf
x86_64-linux-gnu.conf
fakeroot-x86_64-linux-gnu.conf
```

**Exemple de contenu** (`x86_64-linux-gnu.conf`) :
```
/lib/x86_64-linux-gnu
/usr/lib/x86_64-linux-gnu
```

**Avantages** :
- ✅ Configuration segmentée
- ✅ Facile d'ajouter/retirer des chemins
- ✅ Packages peuvent ajouter leur propre `.conf`

---

## 🔄 ldconfig - Mise à Jour du Cache

### Pourquoi un Cache ?

**Problème** : Chercher dans tous les répertoires à chaque lancement = lent

**Solution** : **Cache** des bibliothèques dans `/etc/ld.so.cache`

**Analogie** 📇 : Index d'une bibliothèque (trouve un livre sans chercher partout)

---

### Commande ldconfig

```bash
ldconfig
```

**Rôle** : Lire `/etc/ld.so.conf`, scanner les répertoires, **créer/mettre à jour le cache**

**Quand l'utiliser ?**
- ✅ Après installation d'une nouvelle bibliothèque
- ✅ Après modification de `/etc/ld.so.conf` ou `/etc/ld.so.conf.d/*.conf`
- ✅ Après création d'un lien symbolique vers une lib

**⚠️ Privilèges** : Nécessite **root**

---

### ldconfig -v (Verbose)

```bash
ldconfig -v
```

**Effet** : Affiche le **contenu du cache** avec détails

**Résultat** :
```
/lib/x86_64-linux-gnu:
    libpthread.so.0 -> libpthread-2.31.so
    libc.so.6 -> libc-2.31.so
    libm.so.6 -> libm-2.31.so
/usr/lib/x86_64-linux-gnu:
    libcurl.so.4 -> libcurl.so.4.6.0
    libssl.so.1.1 -> libssl.so.1.1
```

**Usage** :
- Voir toutes les bibliothèques reconnues
- Vérifier qu'une lib est bien dans le cache
- Voir les liens symboliques et leurs versions

---

### ldconfig -p (Print Cache)

```bash
ldconfig -p
```

**Effet** : Affiche **complètement** le cache (format tabulaire)

**Résultat** :
```
1234 libs found in cache `/etc/ld.so.cache'
    libc.so.6 (libc6,x86-64) => /lib/x86_64-linux-gnu/libc.so.6
    libpthread.so.0 (libc6,x86-64) => /lib/x86_64-linux-gnu/libpthread.so.0
    libssl.so.1.1 (libc6,x86-64) => /usr/lib/x86_64-linux-gnu/libssl.so.1.1
    ...
```

**Colonnes** :
- Nom de la lib
- Architecture
- Chemin complet

**Usage** : Rechercher rapidement une bibliothèque

```bash
# Trouver libssl
ldconfig -p | grep libssl
```

---

## 🌍 Variable LD_LIBRARY_PATH

### C'est quoi ?

**LD_LIBRARY_PATH** = Variable d'environnement contenant des **chemins supplémentaires** pour chercher les bibliothèques

**Format** : Liste de chemins séparés par `:`

```bash
export LD_LIBRARY_PATH=/opt/myapp/lib:/usr/local/custom/lib
#                       │              │
#                       │              └─── Deuxième chemin
#                       └──────────────── Premier chemin
```

---

### Ordre de Recherche

Quand un programme cherche une bibliothèque :

```
1. LD_LIBRARY_PATH  (priorité maximale)
2. /etc/ld.so.cache (cache ldconfig)
3. /lib
4. /usr/lib
```

**💡 Point clé** : `LD_LIBRARY_PATH` **override** les bibliothèques système !

---

### Cas d'Usage

#### Développement

```bash
# Tester une nouvelle version de lib sans l'installer
export LD_LIBRARY_PATH=/home/user/mylib:$LD_LIBRARY_PATH
./myprogram
```

#### Application Isolée

```bash
# Application avec ses propres libs
export LD_LIBRARY_PATH=/opt/app/lib
/opt/app/bin/program
```

---

### ⚠️ Dangers de LD_LIBRARY_PATH

**Problème 1** : Casse des programmes système

```bash
export LD_LIBRARY_PATH=/custom/lib
ls  # ❌ Peut utiliser une mauvaise libc et crasher !
```

**Problème 2** : Sécurité

```bash
# Attaquant peut injecter une lib malveillante
export LD_LIBRARY_PATH=/tmp/evil
```

**💡 Recommandations** :
- ✅ Utiliser temporairement (une session)
- ✅ Préférer `/etc/ld.so.conf.d/` pour permanent
- ❌ Ne JAMAIS mettre dans `/etc/profile` (global)

---

### Voir la Variable

```bash
echo $LD_LIBRARY_PATH

# Si vide (normal)
# (aucune sortie)

# Si définie
/opt/myapp/lib:/usr/local/lib
```

---

## 🔍 ldd - Afficher les Dépendances

### C'est quoi ldd ?

**ldd** = **L**ist **D**ynamic **D**ependencies

**Rôle** : Afficher les bibliothèques utilisées par un **exécutable**

---

### Syntaxe de Base

```bash
ldd /chemin/vers/executable
```

**Exemple** :
```bash
ldd /usr/bin/git
```

**Résultat** :
```
linux-vdso.so.1 (0x00007ffe1a3f2000)
libpcre2-8.so.0 => /lib/x86_64-linux-gnu/libpcre2-8.so.0 (0x00007f8a2b1e0000)
libz.so.1 => /lib/x86_64-linux-gnu/libz.so.1 (0x00007f8a2afc0000)
libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f8a2adc0000)
/lib64/ld-linux-x86-64.so.2 (0x00007f8a2b400000)
```

**Colonnes** :
- **Nom bibliothèque** : `libc.so.6`
- **=>** : Pointe vers
- **Chemin réel** : `/lib/x86_64-linux-gnu/libc.so.6`
- **Adresse mémoire** : `(0x00007f8a...)` où elle sera chargée

---

### Interpréter la Sortie

#### Bibliothèque Trouvée

```
libz.so.1 => /lib/x86_64-linux-gnu/libz.so.1 (0x00007f...)
```

✅ Bibliothèque OK, trouvée dans le système

---

#### Bibliothèque Manquante

```
libfoo.so.2 => not found
```

❌ **ERREUR** : Bibliothèque non trouvée !

**Solutions** :
```bash
# 1. Installer le package contenant libfoo
apt-get install libfoo2

# 2. Ajouter le chemin à ld.so.conf
echo "/opt/custom/lib" > /etc/ld.so.conf.d/custom.conf
ldconfig

# 3. Temporaire
export LD_LIBRARY_PATH=/opt/custom/lib
```

---

#### Bibliothèque Virtuelle

```
linux-vdso.so.1 (0x00007ffe...)
```

**Pas de chemin** : Bibliothèque virtuelle fournie par le kernel

**💡 Normal** : Présente sur tous les systèmes Linux

---

### ldd sur une Bibliothèque

**Oui, c'est possible !** Une bibliothèque peut dépendre d'autres bibliothèques

```bash
ldd /lib/x86_64-linux-gnu/libcurl.so.4
```

**Résultat** :
```
libssl.so.1.1 => /usr/lib/x86_64-linux-gnu/libssl.so.1.1
libcrypto.so.1.1 => /usr/lib/x86_64-linux-gnu/libcrypto.so.1.1
libz.so.1 => /lib/x86_64-linux-gnu/libz.so.1
libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6
```

**💡 Usage** : Comprendre la chaîne de dépendances complète

---

### ldd -u (Unused)

**Option** : `-u` ou `--unused`

**Rôle** : Afficher les bibliothèques **référencées mais non utilisées**

```bash
ldd -u /usr/bin/monprogramme
```

**Résultat si unused** :
```
Unused direct dependencies:
    /lib/x86_64-linux-gnu/libm.so.6
```

**Signification** :
- Le programme est lié avec `libm` (bibliothèque mathématique)
- Mais n'utilise **aucune** fonction de cette bibliothèque

**Pourquoi c'est important ?**
- ⚠️ Ralentit le chargement du programme
- ⚠️ Augmente la mémoire utilisée
- 💡 Devrait être enlevé lors de la compilation

**Résultat si tout est utilisé** :
```
(aucune sortie)
```

**Usage développeur** : Optimiser les dépendances

---

## 📋 Antisèche - Commandes Bibliothèques

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `ldconfig` | Mettre à jour cache bibliothèques | `sudo ldconfig` |
| `ldconfig -v` | Voir cache avec détails | `ldconfig -v` |
| `ldconfig -p` | Afficher tout le cache | `ldconfig -p` |
| `ldconfig -p \| grep lib` | Chercher une bibliothèque | `ldconfig -p \| grep libssl` |
| `ldd executable` | Voir dépendances d'un programme | `ldd /usr/bin/git` |
| `ldd bibliotheque.so` | Voir dépendances d'une lib | `ldd /lib/x86_64-linux-gnu/libcurl.so.4` |
| `ldd -u executable` | Voir libs inutilisées | `ldd -u /usr/bin/program` |
| `echo $LD_LIBRARY_PATH` | Voir chemins personnalisés | `echo $LD_LIBRARY_PATH` |

---

## 🎓 Points Clés pour l'Examen

✅ **Bibliothèque** : Code réutilisable par plusieurs programmes  
✅ **Compilation** : Compiler (source → objet) + Linker (objet + libs → exécutable)  
✅ **Statique** : Lib copiée dans exécutable (.a)  
✅ **Dynamique** : Lib référencée, chargée au lancement (.so)  
✅ **Soname** : `lib + nom + .so + .version` (ex: `libc.so.6`)  
✅ **Emplacements** : `/lib`, `/lib64`, `/usr/lib`, `/usr/local/lib`  
✅ **Lien symbolique** : `libc.so.6 → libc-2.31.so` (facilite mises à jour)  
✅ **Dynamic linker** : `ld.so`, `ld-linux.so` (charge les libs)  
✅ **Configuration** : `/etc/ld.so.conf` + `/etc/ld.so.conf.d/*.conf`  
✅ **ldconfig** : Met à jour cache (`/etc/ld.so.cache`)  
✅ **ldconfig -v** : Affiche cache avec liens symboliques  
✅ **ldconfig -p** : Print cache complet  
✅ **LD_LIBRARY_PATH** : Chemins supplémentaires (prioritaire)  
✅ **ldd** : Liste dépendances d'un exécutable  
✅ **ldd -u** : Bibliothèques référencées mais inutilisées  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Installer une Bibliothèque Custom

```bash
# 1. Compiler et installer la lib
cd /opt/mylib
make
sudo make install
# Installation dans /usr/local/lib

# 2. Ajouter le chemin à ldconfig
echo "/usr/local/lib" | sudo tee /etc/ld.so.conf.d/mylib.conf

# 3. Mettre à jour le cache
sudo ldconfig

# 4. Vérifier
ldconfig -p | grep mylib
# mylib.so.1 => /usr/local/lib/mylib.so.1
```

---

### Scénario 2 : Résoudre "library not found"

```bash
# Programme échoue avec :
# error while loading shared libraries: libfoo.so.2: cannot open shared object file

# 1. Vérifier les dépendances
ldd /usr/local/bin/monprog
# libfoo.so.2 => not found

# 2. Trouver où est la lib
find /usr -name "libfoo.so*"
# /usr/local/lib/libfoo.so.2

# 3. Ajouter le chemin
echo "/usr/local/lib" | sudo tee /etc/ld.so.conf.d/local.conf

# 4. Mettre à jour
sudo ldconfig

# 5. Vérifier
ldd /usr/local/bin/monprog
# libfoo.so.2 => /usr/local/lib/libfoo.so.2 ✅
```

---

### Scénario 3 : Tester une Nouvelle Version de Lib

```bash
# Nouvelle version dans ~/dev/newlib/
# Sans l'installer système

# 1. Définir LD_LIBRARY_PATH temporairement
export LD_LIBRARY_PATH=~/dev/newlib:$LD_LIBRARY_PATH

# 2. Lancer le programme
./myprogram

# 3. Vérifier quelle lib est utilisée
ldd ./myprogram
# libmylib.so.1 => /home/user/dev/newlib/libmylib.so.1

# 4. Quitter le terminal → LD_LIBRARY_PATH disparaît
```

---

### Scénario 4 : Voir Toutes les Dépendances d'un Programme

```bash
# Exemple avec git
ldd /usr/bin/git

# Résultat :
# linux-vdso.so.1 (0x00007...)
# libpcre2-8.so.0 => /lib/x86_64-linux-gnu/libpcre2-8.so.0
# libz.so.1 => /lib/x86_64-linux-gnu/libz.so.1
# libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6
# /lib64/ld-linux-x86-64.so.2

# Git dépend de : pcre2 (regex), zlib (compression), libc (standard)
```

---

### Scénario 5 : Vérifier Version d'une Bibliothèque

```bash
# Quelle version de libssl ?

# Méthode 1 : ldconfig -v
ldconfig -v 2>/dev/null | grep libssl
# libssl.so.1.1 -> libssl.so.1.1

# Méthode 2 : ls avec lien symbolique
ls -l /usr/lib/x86_64-linux-gnu/libssl.so*
# libssl.so.1.1 -> libssl.so.1.1

# Méthode 3 : ldconfig -p
ldconfig -p | grep libssl
# libssl.so.1.1 (libc6,x86-64) => /usr/lib/x86_64-linux-gnu/libssl.so.1.1
```

---

### Scénario 6 : Détecter Dépendances Inutilisées

```bash
# Programme développé, vérifier optimisation

ldd -u /opt/myapp/bin/myprogram

# Résultat :
# Unused direct dependencies:
#     /usr/lib/x86_64-linux-gnu/libm.so.6
#     /usr/lib/x86_64-linux-gnu/libpthread.so.0

# Action : Recompiler sans -lm et -lpthread
gcc myprogram.c -o myprogram -lssl
# (retirer -lm et -lpthread)
```

---

### Scénario 7 : Multi-Architecture (32-bit et 64-bit)

```bash
# Installer lib 32-bit sur système 64-bit

# 1. Installer package 32-bit
sudo apt-get install libcurl4:i386

# 2. Vérifier
ls /lib/i386-linux-gnu/libcurl*
# libcurl.so.4 -> libcurl.so.4.6.0

# 3. Programme 32-bit peut maintenant utiliser libcurl
ldd /opt/app32/program
# libcurl.so.4 => /lib/i386-linux-gnu/libcurl.so.4
```

---

## 🔄 Flux Complet : Code → Exécution

```
1. CODE SOURCE
   program.c

2. COMPILATION
   gcc -c program.c -o program.o
   → Fichier objet

3. LINKING
   gcc program.o -o program -lpthread
   → Exécutable référençant libpthread.so.0

4. INSTALLATION
   Exécutable dans /usr/bin/program
   Lib dans /lib/x86_64-linux-gnu/libpthread.so.0

5. LANCEMENT
   ./program
   → Dynamic linker charge libpthread.so.0 en mémoire

6. EXÉCUTION
   Programme + Bibliothèque en RAM
```

---

## 🗂️ Hiérarchie Complète Bibliothèques

```
/
├── lib/                           → Libs essentielles 64-bit (ou 32-bit selon système)
│   ├── x86_64-linux-gnu/         → Libs 64-bit architecture spécifique
│   │   ├── libc.so.6 → libc-2.31.so
│   │   ├── libpthread.so.0 → libpthread-2.31.so
│   │   └── ...
│   └── i386-linux-gnu/           → Libs 32-bit (si installées)
├── lib32/                         → Libs 32-bit (alternative)
├── lib64/                         → Libs 64-bit (alternative)
│   └── ld-linux-x86-64.so.2      → Dynamic linker
├── usr/
│   ├── lib/                       → Libs applications utilisateur
│   │   └── x86_64-linux-gnu/
│   │       ├── libssl.so.1.1
│   │       ├── libcurl.so.4
│   │       └── ...
│   └── local/
│       └── lib/                   → Libs compilées localement
└── etc/
    ├── ld.so.conf                 → Config linker (master)
    ├── ld.so.conf.d/              → Configs segmentées
    │   ├── libc.conf
    │   ├── x86_64-linux-gnu.conf
    │   └── custom.conf
    └── ld.so.cache                → Cache généré par ldconfig
```

---

## ⚠️ Erreurs Courantes

### Erreur 1 : "cannot open shared object file"

```
error while loading shared libraries: libfoo.so.2: cannot open shared object file: No such file or directory
```

**Cause** : Bibliothèque manquante ou non dans le cache

**Solutions** :
```bash
# 1. Installer la lib
apt-get install libfoo2

# 2. Ou ajouter son chemin
echo "/chemin/vers/lib" | sudo tee /etc/ld.so.conf.d/custom.conf
sudo ldconfig
```

---

### Erreur 2 : Oublier ldconfig après modification

**Symptôme** : Lib installée mais programme ne la trouve pas

**Cause** : Cache pas mis à jour

**Solution** :
```bash
sudo ldconfig
# Toujours après modification de /etc/ld.so.conf*
```

---

### Erreur 3 : Mauvaise Version de Lib

```
version `GLIBC_2.34' not found
```

**Cause** : Programme compilé pour version plus récente

**Solutions** :
```bash
# 1. Recompiler le programme sur ce système
# 2. Mettre à jour glibc (risqué)
# 3. Utiliser une version compatible du programme
```

---

**🎯 Prochaine étape** : Use Debian Package Management

*Dernière mise à jour: 31 janvier 2026*
