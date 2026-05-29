# M02 - Administration Linux

## CH10 - Droits sur les fichiers et répertoires

Les droits d'accès sont inscrits dans l'**inode** de chaque fichier/répertoire. Ils sont découpés en trois colonnes : propriétaire (user), groupe (group), autres (other).

---

### 10.1 - Les droits standard

| Droit | Octal | Sur un fichier | Sur un répertoire |
| --- | --- | --- | --- |
| Lecture (`r`) | 4 | Lire / ouvrir (`cat`, `less`) | Lister le contenu (`ls`) |
| Écriture (`w`) | 2 | Modifier le fichier (nécessite aussi `r`) | Créer, renommer, supprimer des fichiers/sous-dossiers |
| Exécution (`x`) | 1 | Exécuter un binaire ou script (nécessite aussi `r`) | Traverser / accéder au contenu (`cd`) |

⚠️ Le droit `w` sur un **répertoire** permet de supprimer **n'importe quel fichier** qu'il contient, même si l'utilisateur n'est pas propriétaire du fichier. C'est le Sticky bit qui corrige ce comportement (voir section droits spéciaux).

📌 Calcul rapide en octal : `r`=4, `w`=2, `x`=1. On additionne par colonne.

| Octal | Droits | Signification |
| --- | --- | --- |
| `755` | `rwxr-xr-x` | Propriétaire : tout. Groupe/Autres : lecture + exécution |
| `644` | `rw-r--r--` | Propriétaire : lecture/écriture. Groupe/Autres : lecture seule |
| `700` | `rwx------` | Propriétaire uniquement |
| `770` | `rwxrwx---` | Propriétaire + groupe |
| `777` | `rwxrwxrwx` | Tout le monde (⚠️ à éviter) |

---

### 10.2 - Modification des droits (`chmod`)

Deux syntaxes possibles :

#### 🔧 Notation octale (absolue)

```bash
# chmod 770 /data/commun
# chmod 644 fichier.txt
```

#### 🔧 Notation symbolique (relative)

```bash
# chmod g+w,o-rx /data/commun    # Ajouter w au groupe, retirer rx aux autres
# chmod u=rwx,g=rx,o= fichier    # Définir exactement les droits
# chmod a+r fichier               # Ajouter lecture pour tout le monde
```

| Symbole | Cible |
| --- | --- |
| `u` | User (propriétaire) |
| `g` | Group |
| `o` | Other |
| `a` | All (u+g+o) |

#### 🔧 Récursif

```bash
# chmod -R 755 /data/commun
```

⚠️ Le `-R` récursif doit être utilisé avec réflexion. Appliquer `chmod -R 755` met le bit `x` sur les fichiers aussi, ce qui n'est pas souhaitable pour des fichiers de données.

---

### 10.3 - Modification du propriétaire (`chown`)

```bash
# chown penthium:users /data       # Changer user ET groupe
# chown :users /data/commun        # Changer uniquement le groupe
# chown penthium fichier            # Changer uniquement le user
# chown -R penthium:users /data     # Récursif
```

💡 `chgrp users /data/commun` est équivalent à `chown :users /data/commun`.

---

### 10.4 - Umask

L'umask détermine les droits **par défaut** à la création d'un fichier ou répertoire. La valeur de l'umask est "soustraite" aux droits maximaux :

| Type | Droits max | Umask 0022 | Résultat |
| --- | --- | --- | --- |
| Fichier | `0666` | `-0022` | `0644` (`rw-r--r--`) |
| Répertoire | `0777` | `-0022` | `0755` (`rwxr-xr-x`) |

Sur RHEL, deux umask par défaut :

- **`0002`** : utilisateurs standards (UID > 199 avec groupe principal = login)
- **`0022`** : root et utilisateurs de service

```bash
umask              # Afficher le masque courant
umask 0007         # Modifier (temporaire, session courante)
```

💡 Pour rendre un umask permanent, l'ajouter dans `~/.bashrc` :

```bash
umask 0027
```

📌 Laisser l'umask de root et des services à `0022` pour des raisons de sécurité.

---

### 10.5 - Droits spéciaux

Trois droits supplémentaires au-delà des droits standard :

| Droit | Octal | Sur un fichier | Sur un répertoire |
| --- | --- | --- | --- |
| **SetUID** | `4---` | Exécution avec les droits du **propriétaire** | Non utilisé |
| **SetGID** | `2---` | Exécution avec les droits du **groupe** | Les fichiers créés héritent du **groupe du répertoire**. Les sous-dossiers héritent aussi du SetGID. |
| **Sticky bit** | `1---` | Mise en zone de swap (obsolète) | Seul le **propriétaire** du fichier (ou root) peut le supprimer |

#### 🔧 Exemples concrets

**SetUID** (`s` sur la colonne user) :

```bash
# chmod u+s /usr/bin/passwd     # ou chmod 4755
$ ls -l /usr/bin/passwd
-rwsr-xr-x 1 root root ... /usr/bin/passwd
```

📌 `passwd` a besoin du SetUID car il modifie `/etc/shadow` (propriété de root) même quand un utilisateur standard le lance.

**SetGID** (`s` sur la colonne group) :

```bash
# chmod g+s /srv/Documentation   # ou chmod 2770
$ ls -ld /srv/Documentation
drwxrws--- 2 root users ... /srv/Documentation
```

📌 Tout fichier créé dans ce dossier appartiendra automatiquement au groupe `users`, quel que soit le groupe principal de l'utilisateur qui le crée.

**Sticky bit** (`t` sur la colonne other) :

```bash
# chmod +t /srv/Public           # ou chmod 1777
$ ls -ld /srv/Public
drwxrwxrwt 2 root root ... /srv/Public
```

📌 `/tmp` utilise le Sticky bit : tout le monde peut y écrire, mais chacun ne peut supprimer que ses propres fichiers.

#### 🔍 Lecture des droits spéciaux

| Affichage | Signification |
| --- | --- |
| `s` minuscule | Droit spécial (SetUID/SetGID) **avec** le bit `x` positionné |
| `S` majuscule | Droit spécial **sans** le bit `x` (configuration incohérente) |
| `t` minuscule | Sticky bit **avec** le bit `x` |
| `T` majuscule | Sticky bit **sans** le bit `x` |

#### 🔍 Trouver les fichiers avec droits spéciaux

```bash
find / -perm -4000 2>/dev/null    # Fichiers SetUID
find / -perm -2000 2>/dev/null    # Fichiers/dossiers SetGID
find / -perm -1000 2>/dev/null    # Dossiers Sticky bit
find / -perm /6000 2>/dev/null    # SetUID OU SetGID
```

⚠️ Attention à la différence : `-perm -6000` = SetUID **ET** SetGID, `-perm /6000` = SetUID **OU** SetGID.

---

### 🧪 Atelier 9 - Gestion des permissions

Structure à créer dans `/srv` :

```bash
# mkdir /srv/{Complots,Depot,Documentation,Public}
```

| Dossier | Besoin | Commandes |
| --- | --- | --- |
| **Depot** | Accès et CRUD par tous | `chmod a=rwx /srv/Depot` |
| **Documentation** | CRUD par le groupe `users` uniquement, héritage du groupe | `chown :users /srv/Documentation && chmod u=,g=rwxs,o= /srv/Documentation` |
| **Public** | CRUD par tous, mais seul le propriétaire peut supprimer | `chmod a=rwx,o+t /srv/Public` |
| **Complots** | Accès et modification réservés au groupe `vip` | `chown :vip /srv/Complots && chmod a=,g=rwx /srv/Complots` |

📌 Le SetGID (`g+s`) sur Documentation garantit que tout nouveau fichier hérite du groupe `users`. Le Sticky bit (`+t`) sur Public empêche la suppression de fichiers par quelqu'un d'autre que le propriétaire.

Relever la taille des homes :

```bash
du -sh /home/* | grep -v lost+found
```

---

### 📌 À retenir

1. Droits : `r`=4, `w`=2, `x`=1. `chmod 755` = `rwxr-xr-x`. Deux notations : octale et symbolique.
2. **`chown user:group`** change le propriétaire. Penser au `-R` pour le récursif.
3. **Umask** : soustrait aux droits max (`0666` fichiers, `0777` répertoires). `0022` → fichiers 644, dossiers 755.
4. **SetGID sur un dossier** (`g+s`) : les fichiers créés héritent du groupe du dossier. Indispensable pour les dossiers partagés.
5. **Sticky bit** (`+t`) : empêche la suppression de fichiers par un non-propriétaire. Utilisé sur `/tmp` et les dossiers publics.
