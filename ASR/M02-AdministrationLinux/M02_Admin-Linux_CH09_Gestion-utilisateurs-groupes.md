# M02 - Administration Linux

## CH09 - Gestion des utilisateurs et des groupes

Sous Linux, chaque utilisateur est identifié par un **UID** (User ID) et un **GID** (Group ID) principal. Un utilisateur peut appartenir à plusieurs groupes (groupe principal + groupes secondaires).

---

### 9.1 - Types d'utilisateurs

| Type | UID / GID | Rôle |
| --- | --- | --- |
| `root` | 0 | Administrateur système, tous les droits |
| daemon / service | 1 - 999 | Comptes applicatifs (services). 99% des services tournent sous leur propre identité, pas root. |
| Utilisateur humain | ≥ 1000 | Comptes utilisateurs classiques |

⚠️ Se connecter directement en root est **déconseillé**. Préférer `su -` ou `sudo -i` depuis un compte utilisateur.

---

### 9.2 - Gestion des groupes

#### 📄 Fichiers de référence utilisateur

**`/etc/group`** : informations des groupes

```text
nomdugroupe:x:GID:membre1,membre2,membre3
```

| Champ | Rôle |
| --- | --- |
| Nom du groupe | Identifiant texte |
| `x` | Mot de passe dans `/etc/gshadow` |
| GID | Identifiant numérique |
| Membres | Liste des utilisateurs ayant ce groupe en **secondaire** (séparés par `,`) |

**`/etc/gshadow`** : informations sensibles des groupes (rarement utilisé)

```text
nomdugroupe:motdepassechiffré:administrateurs:membres
```

- `!` ou `*` dans le champ mot de passe → les utilisateurs ne peuvent pas accéder au groupe via mot de passe
- Champ vide → seuls les membres peuvent obtenir les permissions

#### 🔧 Commandes de gestion des groupes

```bash
# Créer un groupe
# groupadd mentat
# groupadd -g 2000 vip          # Avec GID spécifique

# Modifier un groupe
# groupmod -n nouveau ancien     # Renommer
# groupmod -g 2001 mentat        # Changer le GID (⚠️ attention aux orphelins)

# Supprimer un groupe
# groupdel mentat                # Doit être vidé de ses membres avant

# Gérer les appartenances (gpasswd)
# gpasswd -a jessica users       # Ajouter jessica au groupe users
# gpasswd -d leto adm            # Retirer leto du groupe adm
```

💡 `gpasswd -a/-d` est la méthode propre pour ajouter/retirer un utilisateur d'un groupe sans toucher aux autres appartenances.

---

### 9.3 - Gestion des utilisateurs

#### 📄 Fichiers de référence

##### `/etc/passwd` (7 champs)

```text
nom:x:UID:GID:nom,complet:/home/repertoire:/shell
```

| Champ | Exemple | Rôle |
| --- | --- | --- |
| Nom | `thufir` | Login |
| `x` | `x` | Mot de passe dans `/etc/shadow` |
| UID | `1001` | Identifiant numérique |
| GID | `1001` | Groupe principal |
| GECOS | `Thufir,Hawat` | Nom complet + infos (modifiable via `chfn`) |
| Home | `/home/thufir` | Répertoire d'accueil |
| Shell | `/bin/ksh` | Shell par défaut (modifiable via `chsh`) |

##### `/etc/shadow` (9 champs)

```text
user:$6$sel$hash:dernierchg:min:max:warn:inactif:expire:réservé
```

| Champ | Rôle |
| --- | --- |
| Mot de passe | Hash : `$6$` = SHA-512, `$5$` = SHA-256, `$1$` = MD5. `!` devant = verrouillé. |
| Dernier changement | Jours depuis le 01/01/1970. `0` = forcer le changement au prochain login. |
| Âge minimum | Nb jours avant de pouvoir rechanger le mot de passe |
| Âge maximum | Nb jours de validité du mot de passe |
| Avertissement | Nb jours d'alerte avant expiration |
| Inactivité | Nb jours après expiration où le mot de passe est encore accepté |
| Expiration compte | Date de fin de validité du compte (jours depuis 01/01/1970) |

📌 La structure du hash : `$algorithme$sel$hash`. Le **sel** (salt) est une chaîne aléatoire ajoutée au mot de passe avant le hachage, ce qui garantit que deux utilisateurs avec le même mot de passe auront des hash différents.

⚠️ **Expiration du mot de passe ≠ expiration du compte.** Un mot de passe expiré force un changement au login. Un compte expiré bloque toute connexion.

#### 🔧 Commandes de gestion des utilisateurs

##### `useradd` : créer un utilisateur

```bash
# useradd -m -s /bin/bash -c "Gurney,Halleck" -g instructeur -G users gurney
```

| Option | Rôle |
| --- | --- |
| `-m` | Créer le répertoire home (automatique sur RHEL) |
| `-M` | Ne PAS créer le home |
| `-d /chemin` | Spécifier le chemin du home |
| `-u 1500` | UID spécifique |
| `-g groupe` | Groupe principal |
| `-G gr1,gr2` | Groupes secondaires |
| `-s /bin/ksh` | Shell |
| `-c "Nom Complet"` | Commentaire GECOS |
| `-r` | Utilisateur système (UID < 1000) |

📂 Configuration par défaut de `useradd` : `/etc/default/useradd` (shell par défaut, home, etc.). Modifiable aussi via `useradd -D`.

📂 Le contenu de `/etc/skel/` est copié dans le home de chaque nouvel utilisateur (`.bashrc`, `.profile`, etc.).

##### `usermod` : modifier un utilisateur

```bash
# usermod -aG sudo,vip thufir    # AJOUTER aux groupes secondaires
# usermod -g usul paul            # Changer le groupe principal
# usermod -s /bin/zsh thufir      # Changer le shell
# usermod -L thufir               # Verrouiller le mot de passe (! dans shadow)
# usermod -U thufir               # Déverrouiller
# usermod -e 2027-12-31 thufir    # Date d'expiration du compte
```

⚠️ **`usermod -G` sans `-a` ÉCRASE tous les groupes secondaires existants.** Toujours utiliser **`-aG`** pour ajouter.

##### `userdel` : supprimer un utilisateur

```bash
# userdel thufir         # Supprime le compte (garde le home)
# userdel -r thufir      # Supprime le compte + le home
```

⚠️ Les fichiers de l'utilisateur en dehors de son home ne sont pas supprimés. Penser à chercher les orphelins avec `find / -nouser`.

##### `passwd` : gérer les mots de passe

```bash
# passwd thufir          # Changer le mot de passe
# passwd -e leto         # Forcer le changement au prochain login
# passwd -l paul         # Verrouiller le mot de passe
# passwd -u paul         # Déverrouiller
# passwd -S thufir       # Afficher le statut du mot de passe
```

##### `chage` : gestion fine de l'expiration

```bash
$ chage -l thufir        # Voir les infos d'expiration
# chage -d 0 leto        # Forcer changement au prochain login
# chage -M 90 thufir     # Max 90 jours de validité
# chage -m 1 thufir      # Min 1 jour entre changements
# chage -W 14 thufir     # Avertissement 14 jours avant
# chage -I 7 thufir      # Inactif 7 jours après expiration
# chage -E 2027-12-31 thufir  # Expiration du compte
```

#### 🔍 Commandes d'information

```bash
id thufir              # UID, GID, groupes
groups thufir          # Groupes uniquement
whoami                 # Utilisateur courant
who                    # Utilisateurs connectés
w                      # Utilisateurs + activité
last                   # Historique des connexions
lastb                  # Historique des échecs de connexion
```

---

### 9.4 - Changement d'identité et élévation de privilèges

#### `su` : changer d'utilisateur

```bash
su -                   # Devenir root (login complet, charge l'env root)
su                     # Devenir root (garde l'env de l'utilisateur courant)
su - thufir            # Devenir thufir
su -c 'commande' root  # Exécuter une seule commande en tant que root
```

📌 `su -` (avec le tiret) est préféré à `su` car il reproduit un **login complet** : il charge le profil, le PATH, le home de l'utilisateur cible.

#### `sudo` : élévation de privilèges

```bash
sudo commande          # Exécuter comme root
sudo -i                # Shell root interactif (équivalent su -)
sudo -u thufir cmd     # Exécuter comme un autre utilisateur
sudo -l                # Lister ses privilèges sudo
```

La configuration se fait dans `/etc/sudoers`, **toujours édité avec `visudo`** (vérifie la syntaxe avant de sauvegarder).

💡 Sur RHEL/Oracle Linux, la méthode simple pour donner tous les droits sudo à un utilisateur est de l'ajouter au groupe **`wheel`** :

```bash
# usermod -aG wheel thufir
```

📌 `sudo` demande le mot de passe de **l'utilisateur** (pas de root). L'élévation est conservée en mémoire pendant **5 minutes** par défaut.

---

### 🧪 Atelier 8 - Gestion des utilisateurs

#### Sur `srv-cli` : créer les 3 comptes

```bash
# Créer les groupes
# groupadd mentat
# groupadd vip
# groupadd duc
# groupadd instructeur

# Créer les utilisateurs
# useradd -m -s /bin/ksh -c "Thufir,Hawat" -g mentat -G adm,vip,users thufir
# useradd -m -s /bin/ksh -c "Leto,Atreides" -g duc -G adm,vip,users leto
# useradd -m -s /bin/bash -c "Gurney,Halleck" -g instructeur -G users gurney

# Mots de passe
# passwd thufir
# passwd leto && passwd -e leto    # Forcer changement au prochain login
# passwd gurney
```

#### Sur `srv-gui` : créer Paul et Jessica (outil graphique) puis

```bash
# Verrouiller le compte de Paul
# passwd -l paul
```

#### Actions supplémentaires

```bash
# Jessica : groupes duchesse (principal) et gesserit (secondaire)
# groupadd duchesse
# groupadd gesserit
# usermod -g duchesse jessica
# usermod -aG gesserit jessica

# Paul : groupe principal usul
# groupadd usul
# usermod -g usul paul

# Ajouter Paul et Jessica au groupe users
# gpasswd -a paul users
# gpasswd -a jessica users

# Retirer Leto du groupe adm
# gpasswd -d leto adm
```

---

### 📌 À retenir

1. Trois types d'utilisateurs : **root** (UID 0), **daemons** (UID 1-999), **humains** (UID ≥ 1000).
2. **`/etc/passwd`** (7 champs) et **`/etc/shadow`** (9 champs) sont les fichiers clés. Le hash utilise `$6$` pour SHA-512.
3. **`usermod -aG`** pour ajouter aux groupes secondaires. Sans `-a`, les groupes existants sont écrasés.
4. **`gpasswd -a/-d`** pour ajouter/retirer proprement un membre d'un groupe.
5. **`sudo`** via le groupe `wheel` sur RHEL. Toujours éditer `/etc/sudoers` avec `visudo`.
