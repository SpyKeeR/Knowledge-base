# 107.1 - Gestion des Utilisateurs et Groupes Linux

## 🎯 Informations sur le Module

**Poids à l'examen :** 5  
**Objectif :** Gérer les comptes utilisateurs et groupes, comprendre les fichiers de configuration système, maîtriser les outils d'administration des comptes.

**Concepts clés :**
- Création, modification et suppression de comptes utilisateurs et groupes
- Fichiers de configuration : `/etc/passwd`, `/etc/shadow`, `/etc/group`, `/etc/gshadow`
- Gestion de l'expiration des mots de passe (password aging)
- Verrouillage et déverrouillage de comptes
- Politiques de sécurité des mots de passe
- Consultation des informations utilisateur et groupe

---

## 📋 Concepts Détaillés

### 1. Gestion des Comptes Utilisateurs

#### Commande `useradd` - Création d'utilisateurs

**Syntaxe de base :**
```bash
useradd [options] nom_utilisateur
```

**Options principales :**

| Option | Description | Exemple |
|--------|-------------|---------|
| `-m` | Crée le répertoire personnel | `useradd -m alice` |
| `-d /chemin` | Spécifie le répertoire personnel | `useradd -m -d /home/users/alice alice` |
| `-s /bin/shell` | Définit le shell par défaut | `useradd -s /bin/bash alice` |
| `-g groupe` | Définit le groupe primaire | `useradd -g developers alice` |
| `-G groupe1,groupe2` | Ajoute aux groupes supplémentaires | `useradd -G sudo,docker alice` |
| `-u UID` | Spécifie l'UID | `useradd -u 1500 alice` |
| `-c "commentaire"` | Ajoute un commentaire (GECOS) | `useradd -c "Alice Dupont" alice` |
| `-e YYYY-MM-DD` | Date d'expiration du compte | `useradd -e 2026-12-31 stagiaire` |
| `-f jours` | Délai d'inactivité avant désactivation | `useradd -f 30 alice` |
| `-r` | Crée un compte système (UID < 1000) | `useradd -r serveur_app` |
| `-k /chemin/skel` | Spécifie le répertoire squelette | `useradd -k /etc/skel.custom -m alice` |
| `-M` | Ne crée PAS de répertoire personnel | `useradd -M compte_service` |

**Exemple complet :**
```bash
# Création d'un utilisateur avec options multiples
useradd -m -d /home/alice -s /bin/bash -g developers -G sudo,docker -c "Alice Dupont - DevOps" alice

# Définir le mot de passe
passwd alice
```

#### Commande `usermod` - Modification d'utilisateurs

**Syntaxe de base :**
```bash
usermod [options] nom_utilisateur
```

**Options principales :**

| Option | Description | Exemple |
|--------|-------------|---------|
| `-l nouveau_nom` | Change le nom d'utilisateur | `usermod -l alice_new alice` |
| `-d /nouveau/chemin` | Change le répertoire personnel | `usermod -d /home/alice_new alice` |
| `-m` | Déplace le contenu (avec -d) | `usermod -d /home/alice_new -m alice` |
| `-s /bin/shell` | Change le shell | `usermod -s /bin/zsh alice` |
| `-g groupe` | Change le groupe primaire | `usermod -g staff alice` |
| `-G groupe1,groupe2` | Remplace les groupes secondaires | `usermod -G sudo,docker alice` |
| `-aG groupe1,groupe2` | Ajoute aux groupes secondaires | `usermod -aG audio,video alice` |
| `-u UID` | Change l'UID | `usermod -u 1600 alice` |
| `-c "commentaire"` | Modifie le commentaire GECOS | `usermod -c "Alice Martin" alice` |
| `-e YYYY-MM-DD` | Change la date d'expiration | `usermod -e 2027-06-30 stagiaire` |
| `-f jours` | Modifie le délai d'inactivité | `usermod -f 60 alice` |
| `-L` | **Verrouille le compte (lock)** | `usermod -L alice` |
| `-U` | **Déverrouille le compte (unlock)** | `usermod -U alice` |

**🔒 Verrouillage/Déverrouillage de comptes :**

```bash
# Verrouiller un compte (ajoute ! devant le hash du mot de passe dans /etc/shadow)
usermod -L alice
# Résultat dans /etc/shadow : alice:!$6$xyz...

# Déverrouiller un compte (retire le ! devant le hash)
usermod -U alice
# Résultat dans /etc/shadow : alice:$6$xyz...

# Alternative avec passwd
passwd -l alice    # Lock
passwd -u alice    # Unlock

# Vérifier l'état du compte
passwd -S alice
# alice LK 2026-01-15 0 99999 7 -1 (LK = Locked)
```

**⚠️ Différence importante : `-G` vs `-aG`**
```bash
# REMPLACE tous les groupes secondaires par docker et sudo
usermod -G docker,sudo alice

# AJOUTE docker et sudo aux groupes existants
usermod -aG docker,sudo alice
```

#### Commande `userdel` - Suppression d'utilisateurs

**Syntaxe de base :**
```bash
userdel [options] nom_utilisateur
```

**Options principales :**

| Option | Description | Exemple |
|--------|-------------|---------|
| `-r` | **Supprime le répertoire personnel ET le spool mail** | `userdel -r alice` |
| `-f` | Force la suppression (même si connecté) | `userdel -f alice` |

**Exemples :**
```bash
# Suppression simple (garde le home et le mail spool)
userdel alice
# /home/alice existe toujours
# /var/spool/mail/alice existe toujours

# Suppression complète avec -r
userdel -r alice
# /home/alice est supprimé
# /var/spool/mail/alice est supprimé

# Vérifier si un utilisateur est connecté avant suppression
w | grep alice
who | grep alice
pkill -u alice    # Déconnecter l'utilisateur
userdel -r alice
```

### 2. Gestion des Groupes

#### Commande `groupadd` - Création de groupes

```bash
# Création simple
groupadd developers

# Spécifier un GID
groupadd -g 2000 developers

# Créer un groupe système (GID < 1000)
groupadd -r service_app

# Vérification
cat /etc/group | grep developers
getent group developers
```

#### Commande `groupmod` - Modification de groupes

```bash
# Renommer un groupe
groupmod -n new_name old_name

# Changer le GID
groupmod -g 2500 developers

# Exemple complet
groupmod -n development -g 3000 developers
```

#### Commande `groupdel` - Suppression de groupes

```bash
# Supprimer un groupe (ne peut pas être groupe primaire d'un utilisateur)
groupdel developers

# Vérifier les utilisateurs utilisant ce groupe comme primaire
cat /etc/passwd | cut -d: -f1,4 | grep :2000
```

#### Gestion des appartenances aux groupes

```bash
# Voir les groupes d'un utilisateur
groups alice
id alice

# Ajouter un utilisateur à un groupe (moderne)
usermod -aG docker alice

# Ajouter un utilisateur à un groupe (méthode legacy)
gpasswd -a alice docker

# Retirer un utilisateur d'un groupe
gpasswd -d alice docker

# Définir plusieurs administrateurs de groupe
gpasswd -A alice,bob developers

# Définir les membres d'un groupe
gpasswd -M user1,user2,user3 developers
```

#### 🔄 Changement de groupe avec `newgrp` et `sg`

**`newgrp` - Changer le groupe primaire effectif durant la session**

**Syntaxe :**
```bash
newgrp nom_groupe
```

**Fonctionnement :**
- Démarre un nouveau shell avec le groupe spécifié comme **groupe primaire effectif**
- L'utilisateur doit être membre du groupe (primaire ou secondaire)
- Les fichiers créés dans ce shell appartiendront au nouveau groupe
- Pour quitter : `exit` ou `Ctrl+D`

**Exemple :**
```bash
# Situation initiale
id
# uid=1001(alice) gid=1001(alice) groups=1001(alice),2000(developers),2001(finance)

# Changer vers le groupe developers
newgrp developers

# Nouveau contexte (dans un sous-shell)
id
# uid=1001(alice) gid=2000(developers) groups=2000(developers),1001(alice),2001(finance)
# Le GID effectif est maintenant developers

# Créer un fichier
touch rapport.txt
ls -l rapport.txt
# -rw-r--r-- 1 alice developers 0 Feb 16 10:00 rapport.txt
# ✅ Le groupe est bien "developers"

# Quitter le sous-shell
exit

# Retour au contexte initial
id
# uid=1001(alice) gid=1001(alice) groups=1001(alice),2000(developers),2001(finance)
```

**`sg` - Exécuter une commande unique avec un groupe différent**

**Syntaxe :**
```bash
sg nom_groupe -c "commande"
```

**Fonctionnement :**
- Exécute **une seule commande** avec le groupe spécifié
- Ne crée **pas** de sous-shell persistant
- Plus rapide et propre que `newgrp` pour des actions ponctuelles

**Exemples :**
```bash
# Créer un fichier
 avec le groupe developers
sg developers -c "touch projet_dev.txt"
ls -l projet_dev.txt
# -rw-r--r-- 1 alice developers 0 Feb 16 10:05 projet_dev.txt

# Créer un répertoire avec le bon groupe
sg finance -c "mkdir /shared/reports_finance"
ls -ld /shared/reports_finance
# drwxr-xr-x 2 alice finance 4096 Feb 16 10:06 /shared/reports_finance

# Copier des fichiers avec le groupe approprié
sg developers -c "cp /tmp/src/* /project/developers/"

# Combiner avec d'autres commandes
sg finance -c "echo 'Rapport Q1' > /shared/finance/Q1.txt && chmod 660 /shared/finance/Q1.txt"
```

**📊 Comparaison `newgrp` vs `sg` :**

| Critère | `newgrp` | `sg` |
|---------|----------|------|
| **Durée** | Crée un sous-shell persistant | Exécute une commande unique |
| **Usage** | Plusieurs opérations successives | Action ponctuelle |
| **Sortie** | Nécessite `exit` | Retourne automatiquement |
| **Performance** | Plus lourd (nouveau shell) | Plus rapide |
| **Cas d'usage** | Session de travail prolongée | Script, commande unique |

**Exemples de cas d'usage :**

**Scénario 1 : Développeur travaillant sur plusieurs projets**
```bash
# Alice travaille sur le projet A (plusieurs fichiers à créer)
newgrp projet_a
touch file1.c file2.c file3.c
make
exit

# Alice crée un fichier ponctuel pour le projet B
sg projet_b -c "touch hotfix.patch"
```

**Scénario 2 : Script automatisé**
```bash
#!/bin/bash
# Déploiement multi-projets

# Projet developers
sg developers -c "cp /tmp/build/* /srv/www/dev/"

# Projet staging
sg staging -c "cp /tmp/build/* /srv/www/staging/"

# Projet production
sg production -c "cp /tmp/build/* /srv/www/production/"
```

**⚠️ Permissions requises :**
- L'utilisateur doit être **membre du groupe** (vérifier avec `id -Gn`)
- Si le groupe a un mot de passe (rare), il peut être demandé
- Les groupes système peuvent être restreints

**Vérification de l'appartenance avant utilisation :**
```bash
# Vérifier si alice est dans le groupe developers
id alice | grep -q developers && echo "✅ Membre" || echo "❌ Non membre"

# Lister tous les groupes accessibles
id -Gn
```

### 3. Fichiers de Configuration

#### `/etc/passwd` - Base de données des utilisateurs

**Format (7 champs séparés par `:`) :**
```
nom_utilisateur:x:UID:GID:GECOS:repertoire_personnel:shell
```

**Exemple détaillé :**
```
alice:x:1001:1001:Alice Dupont,Bureau 205,01.23.45.67.89:/home/alice:/bin/bash
```

**Signification des champs :**

| Champ | Description | Exemple |
|-------|-------------|---------|
| 1. Nom d'utilisateur | Login | `alice` |
| 2. Mot de passe | `x` = stocké dans /etc/shadow | `x` |
| 3. UID | User ID (0=root, 1-999=système, ≥1000=users) | `1001` |
| 4. GID | Group ID primaire | `1001` |
| 5. GECOS | Nom complet, infos supplémentaires | `Alice Dupont,Bureau 205` |
| 6. Répertoire personnel | Home directory | `/home/alice` |
| 7. Shell | Shell de connexion | `/bin/bash` |

**Exemples de comptes spéciaux :**
```
root:x:0:0:root:/root:/bin/bash
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
sshd:x:110:65534::/run/sshd:/usr/sbin/nologin
```

#### `/etc/shadow` - Mots de passe et politiques

**Format (9 champs séparés par `:`) :**
```
nom:mot_de_passe_hashé:dernier_changement:minimum:maximum:avertissement:inactivité:expiration:réservé
```

**Exemple détaillé :**
```
alice:$6$xyz...ABC:19750:0:99999:7:30:20000:
```

**Signification des champs :**

| Champ | Description | Détails |
|-------|-------------|---------|
| 1. Nom d'utilisateur | Login | `alice` |
| 2. Hash du mot de passe | Format : `$id$salt$hash` | `$6$` = SHA-512 |
| 3. Dernier changement | Jours depuis epoch (01/01/1970) | `19750` = ~2024 |
| 4. Âge minimum | Jours avant pouvoir changer le mdp | `0` = immédiat |
| 5. Âge maximum | Jours avant expiration du mdp | `99999` = ~273 ans |
| 6. Période d'avertissement | Jours avant expiration pour alerter | `7` |
| 7. Période d'inactivité | Jours après expiration avant lock | `30` |
| 8. Date d'expiration | Jours depuis epoch | `20000` = date fixe |
| 9. Réservé | Inutilisé | |

**📅 Calcul des dates depuis l'Epoch (01/01/1970) :**

Les champs 3 et 8 utilisent le **nombre de jours écoulés depuis le 1er janvier 1970 (Epoch Unix)**.

**Convertir une date en jours depuis Epoch :**
```bash
# Méthode 1 : avec date
date -d "2026-12-31" +%s
# Sortie : 1798761600 (secondes)
# Diviser par 86400 pour obtenir les jours
echo $(( $(date -d "2026-12-31" +%s) / 86400 ))
# Sortie : 20818

# Méthode 2 : directement en jours
echo $(( ( $(date -d "2026-12-31" +%s) - $(date -d "1970-01-01" +%s) ) / 86400 ))
# Sortie : 20818

# Exemples de calcul :
# 01/01/1970 = 0
# 01/01/2000 = 10957
# 01/01/2024 = 19724
# 16/02/2026 = 20500
# 31/12/2026 = 20818
```

**Convertir les jours depuis Epoch en date lisible :**
```bash
# Champ 3 : dernier changement (exemple : 19750)
date -d "1970-01-01 + 19750 days"
# Sortie : Thu Feb 15 00:00:00 UTC 2024

# Champ 8 : expiration du compte (exemple : 20818)
date -d "1970-01-01 + 20818 days"
# Sortie : Wed Dec 31 00:00:00 UTC 2026

# En format court
date -d "1970-01-01 + 19750 days" "+%Y-%m-%d"
# Sortie : 2024-02-15
```

**Exemples d'interprétation complets :**
```bash
# Ligne /etc/shadow :
# alice:$6$xyz:19750:7:90:14:30:20818:

# Champ 3 (19750) : Dernier changement le 15/02/2024
date -d "1970-01-01 + 19750 days" "+Le %d/%m/%Y"
# Sortie : Le 15/02/2024

# Champ 4 (7) : Doit attendre 7 jours minimum avant de changer le mdp
# → Prochain changement possible : 15/02/2024 + 7 jours = 22/02/2024

# Champ 5 (90) : Mot de passe expire après 90 jours
# → Expiration du mot de passe : 15/02/2024 + 90 jours = 16/05/2024

# Champ 6 (14) : Avertissement 14 jours avant l'expiration
# → Warning dès le : 16/05/2024 - 14 jours = 02/05/2024

# Champ 7 (30) : Après expiration, 30 jours de grâce avant lock
# → Verrouillage automatique le : 16/05/2024 + 30 jours = 15/06/2024

# Champ 8 (20818) : Compte expire le 31/12/2026
date -d "1970-01-01 + 20818 days" "+Le %d/%m/%Y"
# Sortie : Le 31/12/2026
# → Peu importe le mot de passe, le compte sera désactivé à cette date
```

**🔧 Définir manuellement une date dans /etc/shadow avec `chage` :**
```bash
# Définir l'expiration du compte au 31/12/2026
DAYS=$(( ( $(date -d "2026-12-31" +%s) - $(date -d "1970-01-01" +%s) ) / 86400 ))
chage -E $DAYS alice

# Ou directement avec chage (plus simple)
chage -E 2026-12-31 alice

# Forcer le changement au prochain login (mettre 0 dans champ 3)
chage -d 0 alice
# Résultat dans /etc/shadow : alice:$6$xyz:0:...

# Vérification des calculs
chage -l alice
```

**Valeurs spéciales :**

| Valeur | Signification | Usage |
|--------|---------------|-------|
| `0` (champ 3) | Forcer changement au login | `chage -d 0 user` |
| `-1` ou vide | Aucune limite/expiration | Jamais expirer |
| `99999` (champ 5) | ~273 ans | Pas d'expiration pratique |

**États spéciaux du champ mot de passe :**

| Valeur | Signification | Exemple |
|--------|---------------|---------|
| `$6$xyz...` | Hash SHA-512 valide | Compte actif avec mdp |
| `!` | Compte verrouillé | `!$6$xyz...` |
| `!!` | Pas de mot de passe défini (RedHat) | Compte jamais utilisé |
| `*` | Compte désactivé (Debian/Ubuntu) | Connexion impossible |
| Vide | Pas de mot de passe requis | **Dangereux !** |

**🔍 Identifier l'état d'un compte :**
```bash
# Vérifier le statut d'un compte
passwd -S alice
# alice PS 2026-02-01 0 99999 7 -1 (PS = Password Set)
# alice LK 2026-02-01 0 99999 7 -1 (LK = Locked)
# alice NP 2026-02-01 0 99999 7 -1 (NP = No Password)

# Examiner /etc/shadow directement
sudo grep alice /etc/shadow

# Voir uniquement le champ mot de passe
sudo awk -F: '/^alice/ {print $2}' /etc/shadow
```

**Formats de hachage :**

| ID | Algorithme | Format |
|----|------------|--------|
| `$1$` | MD5 | Obsolète, non sécurisé |
| `$2a$`, `$2y$` | Blowfish | bcrypt |
| `$5$` | SHA-256 | Acceptable |
| `$6$` | SHA-512 | **Recommandé** |
| `$y$` | yescrypt | Moderne (Debian 11+) |

#### `/etc/group` - Groupes système

**Format (4 champs séparés par `:`) :**
```
nom_groupe:mot_de_passe:GID:liste_membres
```

**Exemple :**
```
developers:x:2000:alice,bob,charlie
sudo:x:27:alice,admin
docker:x:999:alice,bob
```

**Signification des champs :**

| Champ | Description | Exemple |
|-------|-------------|---------|
| 1. Nom du groupe | Nom | `developers` |
| 2. Mot de passe | `x` = dans /etc/gshadow | `x` |
| 3. GID | Group ID | `2000` |
| 4. Membres | Liste séparée par `,` | `alice,bob,charlie` |

**⚠️ Important :** Ce fichier liste uniquement les **groupes supplémentaires**. Le groupe primaire de l'utilisateur est défini dans `/etc/passwd` (champ 4).

```bash
# alice a comme groupe primaire GID 1001 (alice)
# et comme groupes secondaires : sudo, docker, developers
cat /etc/passwd | grep alice
alice:x:1001:1001:Alice:/home/alice:/bin/bash

cat /etc/group | grep -E "(sudo|docker|developers)"
sudo:x:27:alice
docker:x:999:alice
developers:x:2000:alice

# Vérification complète
id alice
# uid=1001(alice) gid=1001(alice) groups=1001(alice),27(sudo),999(docker),2000(developers)
```

#### `/etc/gshadow` - Mots de passe de groupes

**Format :**
```
nom_groupe:mot_de_passe_hashé:administrateurs:membres
```

**Exemple :**
```
developers:!::alice,bob,charlie
sudo:!:admin:alice
```

#### ⚠️ Édition sécurisée avec `vipw` et `vigr`

**⚠️ AVERTISSEMENT IMPORTANT : `vipw` et `vigr` permettent l'édition sécurisée des fichiers de comptes, mais sont déconseillées car elles ne réalisent que l'édition des fichiers. Contrairement aux commandes standard (`useradd`, `usermod`, etc.), elles ne créent PAS les répertoires, ne mettent PAS à jour les appartenances, n'envoient PAS de mails.**

**`vipw` - Éditer les fichiers utilisateurs**

**Syntaxe :**
```bash
vipw [options]
```

**Options :**

| Option | Fichier édité | Description |
|--------|---------------|-------------|
| *(défaut)* | `/etc/passwd` | Base des utilisateurs |
| `-s` | `/etc/shadow` | Mots de passe et aging |
| `-p` | `/etc/passwd` | Explicite (équivalent défaut) |
| `-g` | `/etc/group` | Base des groupes |
| `-gs` ou `-s -g` | `/etc/gshadow` | Mots de passe de groupes |

**`vigr` - Éditer les fichiers groupes**

**Options :**

| Option | Fichier édité | Description |
|--------|---------------|-------------|
| *(défaut)* | `/etc/group` | Base des groupes |
| `-s` | `/etc/gshadow` | Mots de passe de groupes |

**Fonctionnement sécurisé :**
```bash
# Éditer /etc/passwd
vipw
# 1. Crée un verrou (empêche éditions concurrentes)
# 2. Ouvre avec $EDITOR (par défaut vi/vim)
# 3. Vérifie la syntaxe à la sauvegarde
# 4. Libère le verrou

# Éditer /etc/shadow
vipw -s

# Éditer /etc/group
vigr
# ou
vipw -g

# Éditer /etc/gshadow
vigr -s
# ou
vipw -gs
```

**🔒 Avantages de vipw/vigr :**
✅ **Verrouillage** : Empêche les modifications concurrentes  
✅ **Vérification syntaxe** : Refuse les erreurs de format  
✅ **Cohérence** : Détecte les incohérences de champs  
✅ **Sauvegarde automatique** : Crée `.old` avant modification  

**❌ Limitations majeures :**
❌ **N'effectue AUCUNE action automatique**  
❌ **Ne crée pas les répertoires personnels**  
❌ **Ne met pas à jour les permissions**  
❌ **Ne génère pas les spools mail**  
❌ **Ne met pas à jour les fichiers de groupes liés**  
❌ **Ne hash pas les mots de passe** (si édition directe dans shadow)  

**Exemple de problème :**
```bash
# ❌ MAUVAISE PRATIQUE : Ajouter un utilisateur avec vipw
vipw
# Ajouter manuellement :
# bob:x:1050:1050:Bob:/home/bob:/bin/bash

# Problèmes résultants :
ls /home/bob
# ls: cannot access '/home/bob': No such file or directory
# → Le répertoire n'existe pas !

ls /var/mail/bob
# ls: cannot access '/var/mail/bob': No such file or directory
# → Le spool mail n'existe pas !

# Il faudrait créer manuellement :
mkdir -p /home/bob
chown bob:bob /home/bob
chmod 755 /home/bob
cp -r /etc/skel/. /home/bob/
chown -R bob:bob /home/bob
touch /var/mail/bob
chown bob:mail /var/mail/bob
# ❌ Beaucoup de travail manuel, risques d'erreur !

# ✅ BONNE PRATIQUE : Utiliser useradd
useradd -m -c "Bob" -s /bin/bash bob
# → Tout est créé automatiquement et correctement
```

**Cas d'usage légitime (rare) :**
```bash
# Correction d'urgence d'une erreur de syntaxe dans /etc/passwd
# (exemple : champ mal formaté bloquant le système)
vipw
# Corriger manuellement l'entrée corrompue

# Modification d'un GECOS complexe
vipw
# alice:x:1001:1001:Alice Dupont,Dept IT,Bureau 205,+33.1.23.45.67.89,alice@example.com:/home/alice:/bin/bash

# Audit de sécurité : vérifier les UIDs en double
vipw
# Parcourir visuellement les UIDs
```

**⚠️ Recommandation :**
**Utilisez TOUJOURS les commandes standard (`useradd`, `usermod`, `userdel`, `groupadd`, etc.) sauf en cas d'urgence ou de maintenance système critique.**

**Vérification après édition manuelle :**
```bash
# Vérifier la cohérence des fichiers
pwck -r          # Vérifie /etc/passwd et /etc/shadow
grpck -r         # Vérifie /etc/group et /etc/gshadow

# Vérifier les permissions des homes
find /home -maxdepth 1 -type d -ls

# Vérifier les spools mail
ls -l /var/mail/
```

#### `/etc/default/useradd` - Paramètres par défaut useradd

**Contenu typique :**
```bash
GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/bash
SKEL=/etc/skel
CREATE_MAIL_SPOOL=yes
```

**Afficher les paramètres :**
```bash
useradd -D
```

**Modifier les paramètres par défaut :**
```bash
# Changer le shell par défaut
useradd -D -s /bin/zsh

# Changer le répertoire de base
useradd -D -b /home/users

# Définir une expiration par défaut
useradd -D -e 2026-12-31
```

#### `/etc/login.defs` - Politiques système globales

**Paramètres importants :**
```bash
# Plages d'UID/GID
UID_MIN                  1000
UID_MAX                 60000
SYS_UID_MIN               100
SYS_UID_MAX               999
GID_MIN                  1000
GID_MAX                 60000

# Politiques de mot de passe
PASS_MAX_DAYS           90      # Âge maximum du mdp
PASS_MIN_DAYS           0       # Âge minimum du mdp
PASS_MIN_LEN            8       # Longueur minimale
PASS_WARN_AGE           7       # Jours d'avertissement

# Création de répertoire personnel
CREATE_HOME             yes
UMASK                   022

# Algorithme de hachage
ENCRYPT_METHOD          SHA512

# Mail spool
MAIL_DIR                /var/mail
```

**📋 Directives complètes de `/etc/login.defs` :**

**1. Gestion des UID/GID :**

| Directive | Description | Valeur typique | Exemple |
|-----------|-------------|----------------|---------|
| `UID_MIN` | UID minimum pour utilisateurs normaux | `1000` | Utilisateurs créés ≥ 1000 |
| `UID_MAX` | UID maximum pour utilisateurs normaux | `60000` | Limite haute |
| `SYS_UID_MIN` | UID minimum pour comptes système | `100` | Services/daemons |
| `SYS_UID_MAX` | UID maximum pour comptes système | `999` | Avant utilisateurs normaux |
| `GID_MIN` | GID minimum pour groupes normaux | `1000` | Idem UID |
| `GID_MAX` | GID maximum pour groupes normaux | `60000` | Limite haute |
| `SYS_GID_MIN` | GID minimum pour groupes système | `100` | Groupes services |
| `SYS_GID_MAX` | GID maximum pour groupes système | `999` | Avant groupes normaux |

**2. Politiques de mot de passe :**

| Directive | Description | Valeur typique | Exemple |
|-----------|-------------|----------------|---------|
| `PASS_MAX_DAYS` | Durée max validité mdp (jours) | `90` | Changement tous les 3 mois |
| `PASS_MIN_DAYS` | Délai min entre changements | `0` | Changement immédiat autorisé |
| `PASS_MIN_LEN` | Longueur minimale (obsolète, voir PAM) | `8` | ⚠️ Remplacé par pam_pwquality |
| `PASS_WARN_AGE` | Période d'avertissement avant expiration | `7` | Alerte 7 jours avant |

**3. Création et configuration utilisateur :**

| Directive | Description | Valeur typique | Exemple |
|-----------|-------------|----------------|---------|
| `CREATE_HOME` | Créer automatiquement le home | `yes` | Équivalent `-m` de useradd |
| `UMASK` | Masque de permissions par défaut | `022` | rwxr-xr-x (755 dirs, 644 files) |
| `DEFAULT_HOME` | Autoriser login sans home | `yes` | Si `no`, refuse connexion si home absent |
| `ENV_PATH` | PATH par défaut utilisateurs | `/usr/local/bin:/usr/bin:/bin` | Variable $PATH initiale |
| `ENV_ROOTPATH` ou `ENV_SUPATH` | PATH par défaut pour root | `/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin` | PATH administrateur |
| `USERGROUPS_ENAB` | Créer un groupe privé par utilisateur | `yes` | alice → groupe alice (GID=UID) |

**4. Sécurité et connexions :**

| Directive | Description | Valeur typique | Exemple |
|-----------|-------------|----------------|---------|
| `LOGIN_RETRIES` | Nombre max tentatives login | `3` | Après 3 échecs → ferme session |
| `LOGIN_TIMEOUT` | Timeout login (secondes) | `60` | 60s d'inactivité → déconnexion |
| `FAIL_DELAY` | Délai après échec login (secondes) | `3` | Ralentit brute-force |
| `LOG_UNKFAIL_ENAB` | Logger tentatives utilisateur inconnu | `no` | Si `yes`, log dans syslog |
| `LOG_OK_LOGINS` | Logger connexions réussies | `no` | Utile pour audit |
| `LASTLOG_ENAB` | Activer /var/log/lastlog | `yes` | Historique dernières connexions |
| `HUSHLOGIN_FILE` | Fichier pour login silencieux | `.hushlogin` | Si présent dans home, pas d'infos au login |
| `MOTD_FILE` | Chemin Message Of The Day | `/etc/motd` | Message après login |
| `TTYGROUP` | Groupe propriétaire des TTY | `tty` | Groupe pour /dev/tty* |
| `TTYPERM` | Permissions des TTY | `0600` | rw------- (sécurité) |
| `TTYTYPE_FILE` | Fichier définition types terminaux | `/etc/ttytype` | Rarement utilisé |

**5. Chiffrement et hachage :**

| Directive | Description | Valeur typique | Exemple |
|-----------|-------------|----------------|---------|
| `ENCRYPT_METHOD` | Algorithme de hachage | `SHA512` ou `YESCRYPT` | $6$ (SHA512), $y$ (yescrypt) |
| `SHA_CRYPT_MIN_ROUNDS` | Rounds minimum SHA | `5000` | Sécurité vs performance |
| `SHA_CRYPT_MAX_ROUNDS` | Rounds maximum SHA | `5000` | Si min=max, pas d'aléa |
| `YESCRYPT_COST_FACTOR` | Facteur coût yescrypt | `5` | Debian 11+ |

**6. Mail et répertoires :**

| Directive | Description | Valeur typique | Exemple |
|-----------|-------------|----------------|---------|
| `MAIL_DIR` | Répertoire spool mail | `/var/mail` | Boîtes utilisateurs |
| `MAIL_FILE` | Fichier mail relatif au home | `.mail` | Obsolète |
| `CREATE_MAIL_SPOOL` | Créer spool mail automatiquement | `yes` | Touch /var/mail/user |

**7. Divers :**

| Directive | Description | Valeur typique | Exemple |
|-----------|-------------|----------------|---------|
| `CHFN_RESTRICT` | Champs GECOS modifiables par user | `rwh` | r=room, w=work, h=home (pas f=fullname) |
| `QUOTAS_ENAB` | Activer quotas disque | `no` | Nécessite kernel support |
| `USERDEL_CMD` | Script exécuté avant userdel | `/usr/local/bin/userdel_pre` | Hook personnalisé |
| `USERGROUPS_ENAB` | Supprimer groupe si dernier membre | `yes` | Cleanup automatique |

**📝 Exemple de configuration complète :**

```bash
# /etc/login.defs - Configuration système étendue

# ===== Plages UID/GID =====
UID_MIN                  1000
UID_MAX                 60000
SYS_UID_MIN               201
SYS_UID_MAX               999
GID_MIN                  1000
GID_MAX                 60000
SYS_GID_MIN               201
SYS_GID_MAX               999

# ===== Politiques mot de passe =====
PASS_MAX_DAYS            90
PASS_MIN_DAYS             7
PASS_MIN_LEN             12
PASS_WARN_AGE            14

# ===== Création utilisateur =====
CREATE_HOME              yes
UMASK                    027          # Plus restrictif : rwxr-x---
DEFAULT_HOME             yes
USERGROUPS_ENAB          yes
CREATE_MAIL_SPOOL        yes

# ===== Chemins par défaut =====
ENV_PATH                 PATH=/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games
ENV_ROOTPATH             PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

# ===== Sécurité connexions =====
LOGIN_RETRIES            3
LOGIN_TIMEOUT            60
FAIL_DELAY               4
LOG_UNKFAIL_ENAB         yes
LOG_OK_LOGINS            no
LASTLOG_ENAB             yes

# ===== Messages =====
MOTD_FILE                /etc/motd
HUSHLOGIN_FILE           .hushlogin

# ===== TTY =====
TTYGROUP                 tty
TTYPERM                  0600

# ===== Chiffrement =====
ENCRYPT_METHOD           SHA512
SHA_CRYPT_MIN_ROUNDS     5000
SHA_CRYPT_MAX_ROUNDS     5000

# ===== Mail =====
MAIL_DIR                 /var/mail

# ===== Restrictions =====
CHFN_RESTRICT            rwh
```

**🔧 Tester et appliquer les changements :**

```bash
# 1. Sauvegarder l'original
cp /etc/login.defs /etc/login.defs.backup.$(date +%Y%m%d)

# 2. Éditer avec root
visudo /etc/login.defs
# ou
vi /etc/login.defs

# 3. Vérifier les valeurs actives
grep -v "^#" /etc/login.defs | grep -v "^$"

# 4. Tester avec un nouvel utilisateur
useradd -m testuser
passwd testuser
chage -l testuser
# Vérifier que les valeurs PASS_MAX_DAYS, PASS_MIN_DAYS, etc. sont appliquées

# 5. Vérifier le PATH
su - testuser
echo $PATH
# Doit correspondre à ENV_PATH

# 6. Cleanup
userdel -r testuser
```

**💡 Bonnes pratiques :**

✅ **Documenter les changements** : Ajouter des commentaires dans `/etc/login.defs`  
✅ **Politique uniforme** : Appliquer aussi avec `chage`/`passwd` pour utilisateurs existants  
✅ **UMASK restrictif** : `027` ou `077` pour environnements sensibles  
✅ **PASS_MIN_DAYS ≥ 1** : Empêche changements répétés pour contourner historique  
✅ **FAIL_DELAY ≥ 3** : Ralentit attaques par force brute  
❌ **Ne pas modifier UID_MIN/GID_MIN** après création utilisateurs (incohérences)  
❌ **PASS_MIN_LEN obsolète** : Utiliser pam_pwquality pour vraie politique  

**🔍 Interroger les valeurs avec `getconf` :**

```bash
# Longueur max login
getconf LOGIN_NAME_MAX
# 256

# Voir toutes les variables système
getconf -a | grep -i login
```

**Visualiser la configuration :**
```bash
cat /etc/login.defs | grep -v "^#" | grep -v "^$"
```

#### `/etc/skel/` - Squelette pour nouveaux utilisateurs

**Contenu typique :**
```bash
ls -la /etc/skel/
.bash_logout
.bashrc
.profile
.config/
```

**Le contenu de `/etc/skel/` est copié dans le home de chaque nouvel utilisateur créé avec `-m`.**

**Personnalisation :**
```bash
# Ajouter un fichier de bienvenue
echo "Bienvenue sur le serveur !" > /etc/skel/README.txt

# Créer une structure de répertoires
mkdir -p /etc/skel/{Documents,Downloads,Projects}

# Créer un .bashrc personnalisé
cat <<'EOF' > /etc/skel/.bashrc
# Custom bashrc
alias ll='ls -lah'
alias gs='git status'
export EDITOR=vim
EOF

# Les nouveaux utilisateurs auront automatiquement cette configuration
useradd -m newuser
ls -la /home/newuser/
```

#### 🔍 Interroger `/etc/login.defs` avec `getconf`

**La commande `getconf` permet d'interroger des valeurs de configuration système spécifiques, notamment celles définies dans `/etc/login.defs`.**

**Syntaxe :**
```bash
getconf VARIABLE_NAME
```

**Exemples pratiques :**
```bash
# Obtenir la longueur maximale autorisée pour un nom d'utilisateur
getconf LOGIN_NAME_MAX
# Sortie : 256

# Obtenir la taille maximale du PATH
getconf PATH_MAX /
# Sortie : 4096

# Afficher tous les paramètres disponibles
getconf -a

# En script : vérifier avant de créer un utilisateur
MAX_LEN=$(getconf LOGIN_NAME_MAX)
USERNAME="un_tres_long_nom_d_utilisateur"

if [ ${#USERNAME} -gt $MAX_LEN ]; then
    echo "❌ Erreur : nom d'utilisateur trop long (max: $MAX_LEN caractères)"
else
    echo "✅ OK : nom d'utilisateur valide"
fi
```

**Variables courantes interrogeables :**

| Variable | Description | Valeur typique |
|----------|-------------|----------------|
| `LOGIN_NAME_MAX` | Longueur max du nom d'utilisateur | 256 |
| `HOST_NAME_MAX` | Longueur max du hostname | 64 |
| `PATH_MAX` | Longueur max d'un chemin | 4096 |
| `ARG_MAX` | Taille max des arguments de commande | 2097152 |
| `CHILD_MAX` | Nombre max de processus par utilisateur | 31422 |
| `OPEN_MAX` | Nombre max de fichiers ouverts | 1024 |

**💡 Astuce :** `getconf` utilise `/etc/login.defs` et les limites système du noyau pour fournir les valeurs actuelles.

#### 📢 Messages de connexion - `/etc/issue`, `/etc/issue.net`, `/etc/motd`

**Ces fichiers permettent d'afficher des messages aux utilisateurs lors de la connexion au système.**

**📁 Fichiers de notification :**

| Fichier | Quand affiché | Type connexion | Exemple usage |
|---------|---------------|----------------|---------------|
| `/etc/issue` | **AVANT** login | Locale (console, tty) | Bannière légale, avertissement |
| `/etc/issue.net` | **AVANT** login | Distante (SSH, telnet) | Avertissement accès distant |
| `/etc/motd` | **APRÈS** login réussi | Toutes | Message du jour, annonces |

**1. `/etc/issue` - Message pré-login local**

**Affiché sur la console locale (tty1-6) AVANT la saisie du login.**

**Exemple simple :**
```bash
cat /etc/issue
Debian GNU/Linux 12 \n \l

Welcome to Production Server
Unauthorized access is prohibited.
```

**Codes d'échappement (escape codes) disponibles :**

| Code | Description | Exemple résultat |
|------|-------------|------------------|
| `\d` | Date actuelle | `Mon Feb 16 2026` |
| `\t` | Heure actuelle | `14:30:25` |
| `\n` | Nom hôte | `server01` |
| `\s` | Nom système | `Linux` |
| `\m` | Architecture machine | `x86_64` |
| `\r` | Release | `6.1.0-17-amd64` |
| `\v` | Version OS | `#1 SMP PREEMPT_DYNAMIC Debian 6.1.69-1 (2023-12-30)` |
| `\l` | Ligne tty | `tty1` |
| `\o` | Nom domaine | `example.com` |
| `\b` | Vitesse bauds ligne série | `9600` |
| `\u` | Nb utilisateurs connectés | `5` |
| `\U` | "user" ou "users" | `users` (pluriel si >1) |
| `\\` | Backslash | `\` |

**Exemple avec codes d'échappement :**
```bash
cat > /etc/issue << 'EOF'
╔═══════════════════════════════════════════╗
║   🖥️  PRODUCTION SERVER - \n          ║
╚═══════════════════════════════════════════╝

Date système  : \d \t
Architecture  : \s \m \r
Terminal      : \l

⚠️  AVERTISSEMENT :
Ce système est réservé aux utilisateurs autorisés.
Toute tentative d'accès non autorisé sera journalisée
et poursuivie conformément à la législation en vigueur.

EOF
```

**Résultat affiché :**
```
╔═══════════════════════════════════════════╗
║   🖥️  PRODUCTION SERVER - server01      ║
╚═══════════════════════════════════════════╝

Date système  : Mon Feb 16 2026 14:30:25
Architecture  : Linux x86_64 6.1.0-17-amd64
Terminal      : tty1

⚠️  AVERTISSEMENT :
Ce système est réservé aux utilisateurs autorisés.
Toute tentative d'accès non autorisé sera journalisée
et poursuivie conformément à la législation en vigueur.

server01 login: _
```

**2. `/etc/issue.net` - Message pré-login distant**

**Affiché lors de connexions distantes (SSH, telnet) AVANT la saisie du login.**

**⚠️ Importante différence :** Les codes d'échappement (`\n`, `\d`, etc.) **NE FONCTIONNENT PAS** dans `/etc/issue.net`.

**Configuration SSH pour afficher /etc/issue.net :**

```bash
# Éditer /etc/ssh/sshd_config
vi /etc/ssh/sshd_config

# Ajouter ou décommenter
Banner /etc/issue.net

# Redémarrer SSH
systemctl restart sshd
```

**Exemple /etc/issue.net :**
```bash
cat > /etc/issue.net << 'EOF'
*******************************************
*  AUTHORIZED ACCESS ONLY                *
*  Production environment                *
*  All connections are monitored        *
*******************************************

This server is for authorized personnel ONLY.
Unauthorized access attempts will be logged and prosecuted.

By continuing, you acknowledge that:
- Your actions are subject to monitoring
- You have explicit authorization
- You agree to comply with security policies

EOF
```

**Test depuis machine distante :**
```bash
# Connexion SSH affichera le banner
ssh user@server
# *******************************************
# *  AUTHORIZED ACCESS ONLY                *
# ...
# user@server's password: _
```

**3. `/etc/motd` - Message Of The Day**

**Affiché APRÈS un login réussi, pour toutes les connexions (locale et distante).**

**Message du jour, annonces, maintenance :**

```bash
cat > /etc/motd << 'EOF'
╔════════════════════════════════════════════════════════╗
║              MESSAGE DU JOUR - 16/02/2026             ║
╚════════════════════════════════════════════════════════╝

📢 ANNONCES :
✅ Mise à jour système prévue : Dimanche 22/02 - 2h-4h du matin
⚠️  Serveur de fichiers migré : utilisez //nas02 au lieu de //nas01
📊 Quota disque : pensez à nettoyer vos répertoires temporaires

🔧 MAINTENANCE :
- Base de données : sauvegarde quotidienne à 3h du matin
- Snapshots : toutes les 6h (consultez /snapshots)

📞 SUPPORT :
En cas de problème, contactez : support@example.com
Urgences uniquement : +33 1 23 45 67 89

Bon travail ! 🚀
EOF
```

**Résultat lors du login :**
```bash
ssh alice@server
alice@server's password: *******

╔════════════════════════════════════════════════════════╗
║              MESSAGE DU JOUR - 16/02/2026             ║
╚════════════════════════════════════════════════════════╝

📢 ANNONCES :
✅ Mise à jour système prévue : Dimanche 22/02 - 2h-4h du matin
...

Last login: Mon Feb 16 10:25:42 2026 from 192.168.1.100
alice@server:~$ _
```

**📂 MOTD dynamique avec `/etc/update-motd.d/` (Ubuntu/Debian) :**

**Système moderne : scripts exécutés dynamiquement**

```bash
# Lister les scripts MOTD
ls -l /etc/update-motd.d/
# -rwxr-xr-x 1 root root   10-help-text
# -rwxr-xr-x 1 root root   50-landscape-sysinfo
# -rwxr-xr-x 1 root root   90-updates-available
# -rwxr-xr-x 1 root root   91-release-upgrade

# Désactiver un script (retirer exécution)
chmod -x /etc/update-motd.d/10-help-text

# Créer un script personnalisé
cat > /etc/update-motd.d/99-custom << 'EOF'
#!/bin/bash
echo ""
echo "🔒 Dernières connexions échouées :"
lastb -n 3 2>/dev/null | head -n 3 || echo "Aucune"
echo ""
echo "💾 Espace disque :"
df -h / | tail -n 1 | awk '{print $4 " disponible sur " $6}'
echo ""
EOF

chmod +x /etc/update-motd.d/99-custom

# Tester
run-parts /etc/update-motd.d/
```

**Désactiver MOTD pour certains utilisateurs :**

```bash
# Créer .hushlogin dans le home de l'utilisateur
touch /home/alice/.hushlogin

# Alice ne verra plus MOTD ni lastlog
```

**📊 Comparaison des 3 fichiers :**

| Critère | `/etc/issue` | `/etc/issue.net` | `/etc/motd` |
|---------|--------------|------------------|-------------|
| **Moment** | Avant login | Avant login | Après login |
| **Connexion** | Locale (tty) | Distante (SSH) | Toutes |
| **Codes échappement** | ✅ Oui (`\n`, `\d`, etc.) | ❌ Non | ❌ Non |
| **Dynamique** | ❌ Statique | ❌ Statique | ✅ Peut être dynamique (/etc/update-motd.d/) |
| **Configuration SSH** | Auto | Nécessite `Banner` dans sshd_config | Auto |
| **Usage typique** | Bannière légale console | Avertissement SSH | Annonces quotidiennes |
| **Bypass avec .hushlogin** | ❌ Non | ❌ Non | ✅ Oui |

**🔧 Exemples pratiques :**

**Bannière légale minimale :**
```bash
# /etc/issue ET /etc/issue.net
cat > /etc/issue << 'EOF'
UNAUTHORIZED ACCESS PROHIBITED
This system is for authorized use only.
All activity is monitored and logged.
Disconnect now if you are not authorized.
EOF

cp /etc/issue /etc/issue.net

# Ne pas oublier de configurer SSH
echo "Banner /etc/issue.net" >> /etc/ssh/sshd_config
systemctl restart sshd
```

**MOTD avec informations système dynamiques :**
```bash
cat > /etc/update-motd.d/90-sysinfo << 'BASH'
#!/bin/bash
echo "╔═══════════════════════ Informations Système ════════════════════════╗"
echo "  Charge: $(uptime | awk -F'load average:' '{print $2}')"
echo "  Mémoire: $(free -h | awk '/^Mem:/ {print $3 "/" $2}')"
echo "  Disque /: $(df -h / | awk 'NR==2 {print $3 "/" $2 " (" $5 " utilisé)"}')"
echo "  Utilisateurs connectés: $(who | wc -l)"
echo "╚═════════════════════════════════════════════════════════════════════╝"
BASH

chmod +x /etc/update-motd.d/90-sysinfo
```

**⚠️ Bonnes pratiques juridiques et sécurité :**

✅ **Issue/issue.net :** Bannière légale claire (accès autorisé uniquement)  
✅ **Pas d'informations sensibles** : Version OS, services, IPs internes  
✅ **MOTD :** Informations utiles (maintenance, contacts support)  
❌ **Ne pas révéler** : Détails techniques facilitant attaques  
❌ **Éviter** : Messages hostiles ou ambigus juridiquement  

**Exemple de bannière conforme :**
```
AVERTISSEMENT LÉGAL

Accès réservé au personnel autorisé.
L'accès non autorisé est strictement interdit.
Toute activité est surveillée et enregistrée.
Les contrevenants seront poursuivis conformément à la loi.

En continuant, vous reconnaissez avoir lu et accepté
les conditions d'utilisation de ce système.
```

### 4. Gestion de l'Expiration des Mots de Passe - `chage`

**La commande `chage` (CHange AGE) permet de gérer les politiques d'expiration des mots de passe.**

#### Affichage des informations - `chage -l`

```bash
# Afficher les informations d'aging pour un utilisateur
chage -l alice

# Sortie exemple :
Last password change                                    : Feb 01, 2026
Password expires                                        : May 02, 2026
Password inactive                                       : Jun 01, 2026
Account expires                                         : Dec 31, 2026
Minimum number of days between password change          : 0
Maximum number of days between password change          : 90
Number of days of warning before password expires       : 7
```

#### Mode interactif - `chage <utilisateur>`

```bash
# Lancer le mode interactif
chage alice

# Questions posées :
Changing the aging information for alice
Enter the new value, or press ENTER for the default

        Minimum Password Age [0]: 1
        Maximum Password Age [90]: 60
        Last Password Change (YYYY-MM-DD) [2026-02-01]: 
        Password Expiration Warning [7]: 14
        Password Inactive [30]: 15
        Account Expiration Date (YYYY-MM-DD) [-1]: 2026-12-31
```

#### Options de `chage` en ligne de commande

| Option | Description | Exemple |
|--------|-------------|---------|
| `-l` | Afficher les informations | `chage -l alice` |
| `-m jours` | Âge minimum du mot de passe | `chage -m 1 alice` |
| `-M jours` | Âge maximum du mot de passe | `chage -M 90 alice` |
| `-d YYYY-MM-DD` | Date du dernier changement | `chage -d 2026-02-01 alice` |
| `-d 0` | Force changement au prochain login | `chage -d 0 alice` |
| `-E YYYY-MM-DD` | Date d'expiration du compte | `chage -E 2026-12-31 stagiaire` |
| `-E -1` | Désactive l'expiration du compte | `chage -E -1 alice` |
| `-I jours` | Période d'inactivité avant lock | `chage -I 30 alice` |
| `-W jours` | Période d'avertissement | `chage -W 14 alice` |

**Exemples pratiques :**
```bash
# Forcer un utilisateur à changer son mot de passe à la prochaine connexion
chage -d 0 alice
# ou
passwd -e alice

# Configurer une politique stricte
chage -m 7 -M 90 -W 14 -I 30 alice
# Minimum 7 jours, Maximum 90 jours, Warning 14 jours, Inactive 30 jours

# Compte temporaire expirant le 31/12/2026
chage -E 2026-12-31 stagiaire

# Désactiver l'expiration pour un compte de service
chage -M -1 service_app
chage -E -1 service_app

# Vérifier les comptes qui vont expirer bientôt
for user in $(cut -d: -f1 /etc/passwd); do
    chage -l $user 2>/dev/null | grep -q "never" || echo $user
done
```

### 5. Politiques de Mots de Passe

#### 🔐 Politiques appliquées par `passwd`

**⚠️ DÉCOUVERTE IMPORTANTE : Les restrictions de politique de mot de passe (longueur, complexité, historique) sont appliquées par la commande `passwd` SAUF si c'est root qui exécute la commande.**

```bash
# En tant qu'utilisateur normal
alice$ passwd
Changing password for alice.
Current password: 
New password: 123
BAD PASSWORD: The password is shorter than 8 characters
# La politique est APPLIQUÉE

# En tant que root
root# passwd alice
New password: 123
Retype new password: 123
passwd: password updated successfully
# La politique est IGNORÉE - root peut définir n'importe quel mot de passe !
```

**Pourquoi cette différence ?**
- Root a besoin de pouvoir définir des mots de passe simples temporaires
- Root peut créer des comptes de service avec des politiques spécifiques
- Mais **c'est une responsabilité** : un administrateur peut créer des comptes non sécurisés

#### Module PAM `pam_pwquality` (ex-`pam_cracklib`)

**Configuration dans `/etc/security/pwquality.conf` :**
```bash
# Longueur minimale
minlen = 12

# Nombre minimum de classes de caractères (majuscules, minuscules, chiffres, spéciaux)
minclass = 3

# Crédits pour chaque classe (négatif = requis)
dcredit = -1    # Au moins 1 chiffre
ucredit = -1    # Au moins 1 majuscule
lcredit = -1    # Au moins 1 minuscule
ocredit = -1    # Au moins 1 caractère spécial

# Nombre maximum de caractères identiques consécutifs
maxrepeat = 2

# Nombre maximum de caractères de la même classe consécutifs
maxclassrepeat = 3

# Vérifier si le mot de passe contient le nom d'utilisateur
usercheck = 1

# Vérifier le mot de passe contre un dictionnaire
dictcheck = 1

# Nombre de mots de passe précédents à retenir (empêche réutilisation)
# Configuré dans PAM avec pam_pwhistory : remember=5
```

**Configuration PAM dans `/etc/pam.d/common-password` (Debian/Ubuntu) :**
```
# Vérification de la qualité
password requisite pam_pwquality.so retry=3 minlen=12 minclass=3

# Historique des mots de passe (empêche réutilisation)
password required pam_pwhistory.so remember=5 use_authtok

# Mise à jour du mot de passe (hachage)
password [success=1 default=ignore] pam_unix.so obscure use_authtok try_first_pass sha512 rounds=5000
```

**RedHat/CentOS - `/etc/pam.d/system-auth` :**
```
password requisite pam_pwquality.so try_first_pass local_users_only retry=3 authtok_type=
password sufficient pam_unix.so sha512 shadow use_authtok remember=5
```

**Tester la politique :**
```bash
# En tant qu'utilisateur (applique les restrictions)
passwd
# Testera : longueur, complexité, dictionnaire, historique

# En tant que root (ignore les restrictions)
passwd alice
# Accepte n'importe quel mot de passe
```

#### 🔧 Options de la commande `passwd`

**`passwd` permet non seulement de changer les mots de passe, mais aussi de gérer l'état des comptes et leurs politiques d'expiration.**

**Syntaxe :**
```bash
passwd [options] [username]
```

**Options principales :**

| Option | Description | Équivalent | Exemple |
|--------|-------------|------------|---------|
| `-l, --lock` | **Verrouiller** le compte (ajoute `!` devant le hash) | `usermod -L` | `passwd -l alice` |
| `-u, --unlock` | **Déverrouiller** le compte (retire le `!`) | `usermod -U` | `passwd -u alice` |
| `-d, --delete` | **Supprimer** le mot de passe (compte sans mdp) | - | `passwd -d alice` |
| `-e, --expire` | **Expirer** le mot de passe (force changement au login) | `chage -d 0` | `passwd -e alice` |
| `-n, --minimum` | **Âge minimum** du mot de passe (jours) | `chage -m` | `passwd -n 7 alice` |
| `-x, --maximum` | **Âge maximum** du mot de passe (jours) | `chage -M` | `passwd -x 90 alice` |
| `-w, --warning` | **Période d'avertissement** avant expiration (jours) | `chage -W` | `passwd -w 14 alice` |
| `-i, --inactive` | **Période d'inactivité** avant verrouillage (jours) | `chage -I` | `passwd -i 30 alice` |
| `-S, --status` | **Afficher le statut** du compte | - | `passwd -S alice` |

**Exemples d'utilisation :**

**1. Verrouillage / Déverrouillage :**
```bash
# Verrouiller le compte
passwd -l alice
# Résultat dans /etc/shadow : alice:!$6$xyz...
# L'utilisateur ne peut plus se connecter

# Vérifier le statut
passwd -S alice
# alice LK 2026-02-16 0 99999 7 -1 (Password locked)

# Déverrouiller le compte
passwd -u alice

# Vérifier à nouveau
passwd -S alice
# alice PS 2026-02-16 0 99999 7 -1 (Password set, SHA512 crypt)
```

**⚠️ Attention avec `-u` :**
```bash
# Si le compte n'a jamais eu de mot de passe
passwd -u alice
# passwd: unlocking the password would result in a passwordless account.
# passwd: you should set a password with usermod -p to unlock the password of this account.

# Solution : utiliser -f (force)
passwd -u -f alice
# ❌ Attention : crée un compte SANS MOT DE PASSE (dangereux !)

# Meilleure solution : définir d'abord un mot de passe
passwd alice
# Puis déverrouiller si nécessaire
```

**2. Supprimer le mot de passe (root seulement) :**
```bash
# Supprimer le mot de passe
passwd -d alice
# alice::19750:0:99999:7:::
# ❌ Le compte devient accessible SANS MOT DE PASSE (très dangereux)

# Vérification
passwd -S alice
# alice NP 2026-02-16 0 99999 7 -1 (Empty password)

# Usage légitime : comptes de service avec clés SSH uniquement
passwd -d service_account
```

**3. Expirer le mot de passe :**
```bash
# Forcer le changement au prochain login
passwd -e alice

# Résultat dans /etc/shadow :
# alice:$6$xyz:0:0:99999:7:::
# Champ 3 = 0 (force le changement)

# Au login :
# WARNING: Your password has expired.
# You must change your password now and login again!
```

**4. Politiques d'expiration :**
```bash
# Définir l'âge minimum à 7 jours (ne peut pas changer avant)
passwd -n 7 alice

# Définir l'âge maximum à 90 jours (doit changer après)
passwd -x 90 alice

# Définir l'avertissement à 14 jours avant expiration
passwd -w 14 alice

# Définir la période d'inactivité avant verrouillage à 30 jours
passwd -i 30 alice

# Combiner plusieurs options
passwd -n 7 -x 90 -w 14 -i 30 alice

# Vérification
passwd -S alice
# alice PS 2026-02-16 7 90 14 30 (Password set, SHA512 crypt)
```

**5. Afficher le statut :**
```bash
# Statut détaillé
passwd -S alice
# Format : username status date min max warn inactive
# alice PS 2026-02-16 7 90 14 30

# Statuts possibles :
# PS (Password Set)     : Mot de passe défini
# LK (Locked)           : Compte verrouillé
# NP (No Password)      : Pas de mot de passe
# L (Locked)            : Alias de LK
```

**📊 Comparaison `passwd` vs `chage` pour l'aging :**

| Action | `passwd` | `chage` |
|--------|----------|---------|
| Âge minimum | `passwd -n 7 user` | `chage -m 7 user` |
| Âge maximum | `passwd -x 90 user` | `chage -M 90 user` |
| Avertissement | `passwd -w 14 user` | `chage -W 14 user` |
| Inactivité | `passwd -i 30 user` | `chage -I 30 user` |
| Expiration compte | ❌ Non disponible | `chage -E 2026-12-31 user` |
| Forcer changement | `passwd -e user` | `chage -d 0 user` |
| Affichage | `passwd -S user` | `chage -l user` |

**💡 Quand utiliser `passwd` vs `chage` ?**
- **`passwd`** : Modifications rapides, verrouillage/déverrouillage, statut simple
- **`chage`** : Gestion complète de l'aging, dates d'expiration, mode interactif

**Exemple de script de gestion :**
```bash
#!/bin/bash
# Créer un compte temporaire avec politique stricte

USERNAME="intern_2026"

# Créer l'utilisateur
useradd -m -c "Stagiaire 2026" -s /bin/bash $USERNAME

# Définir un mot de passe temporaire
echo "$USERNAME:Welcome2026!" | chpasswd

# Forcer le changement au 1er login
passwd -e $USERNAME

# Politique : 7 jours min, 60 jours max, avertissement 7 jours, inactivité 14 jours
passwd -n 7 -x 60 -w 7 -i 14 $USERNAME

# Expiration du compte dans 6 mois
chage -E $(date -d "+6 months" "+%Y-%m-%d") $USERNAME

# Afficher le résumé
echo "=== Compte créé ==="
passwd -S $USERNAME
chage -l $USERNAME
```

### 5.1. Architecture PAM (Pluggable Authentication Modules)

**PAM (Pluggable Authentication Modules) est un framework modulaire qui centralise l'authentification et l'autorisation sur Linux.**

**🔑 Principe de PAM :**
- **Séparation** : Les applications n'implémentent pas leur propre authentification
- **Modularité** : Les mécanismes d'authentification sont des modules interchangeables
- **Centralisation** : Configuration unique dans `/etc/pam.d/`
- **Flexibilité** : Possibilité d'empiler plusieurs modules (ex: mot de passe + 2FA)

**📂 Structure PAM :**

```
/etc/pam.d/
├── common-account        # Vérifications compte (expiré, autorisé, etc.)
├── common-auth           # Authentification (vérifier mot de passe)
├── common-password       # Gestion mot de passe (changement, politique)
├── common-session        # Configuration session (montage home, logs, etc.)
├── login                 # Configuration login console
├── sshd                  # Configuration SSH
├── sudo                  # Configuration sudo
├── passwd                # Configuration changement mdp
├── chfn                  # Configuration chfn
├── chsh                  # Configuration chsh
└── ...
```

**📝 Format d'une ligne PAM :**

```
type    control    module    arguments
```

**Exemple complet :**
```
auth       required       pam_unix.so         nullok
password   requisite      pam_pwquality.so    retry=3
session    optional       pam_lastlog.so      showfailed
```

#### 1. Types de modules PAM

**Les 4 types principaux :**

| Type | Rôle | Exemple module | Quand exécuté |
|------|------|----------------|---------------|
| **`auth`** | **Authentification** : Vérifier l'identité | `pam_unix.so`, `pam_google_authenticator.so` | À la connexion |
| **`account`** | **Autorisation** : Vérifier droits d'accès | `pam_nologin.so`, `pam_time.so`, `pam_access.so` | Après auth, avant session |
| **`password`** | **Gestion mot de passe** : Politique et mise à jour | `pam_pwquality.so`, `pam_unix.so`, `pam_pwhistory.so` | Lors de `passwd` |
| **`session`** | **Configuration session** : Environnement, logs | `pam_limits.so`, `pam_env.so`, `pam_lastlog.so` | Après auth |

**Détail par type :**

**`auth` - Authentification :**
```bash
# Exemples :
auth    required    pam_unix.so              # Vérifier mdp dans /etc/shadow
auth    required    pam_google_authenticator.so    # 2FA Google auth
auth    required    pam_sss.so               # LDAP via SSSD
```

**`account` - Vérifications du compte :**
```bash
# Exemples :
account required    pam_unix.so              # Compte valide (pas expiré)
account required    pam_nologin.so           # Bloquer si /etc/nologin existe
account required    pam_time.so              # Accès selon plages horaires
account required    pam_access.so            # Restrictions /etc/security/access.conf
```

**`password` - Politiques et changement de mot de passe :**
```bash
# Exemples :
password requisite  pam_pwquality.so retry=3 minlen=12    # Politique complexité
password required   pam_pwhistory.so remember=5           # Historique (pas réutilisation)
password required   pam_unix.so sha512 rounds=5000        # Hasher et stocker
```

**`session` - Configuration de la session :**
```bash
# Exemples :
session required    pam_unix.so                  # Session standard
session required    pam_limits.so                # Limites ressources (/etc/security/limits.conf)
session optional    pam_lastlog.so showfailed    # Afficher dernières tentatives échouées
session optional    pam_motd.so                  # Afficher MOTD
session optional    pam_mail.so standard         # Notifier nouveau mail
session required    pam_env.so                   # Variables environnement
```

#### 2. Contrôles d'exécution PAM

**Les contrôles déterminent comment le résultat d'un module affecte le processus global.**

| Contrôle | Comportement | En cas de succès | En cas d'échec |
|----------|--------------|------------------|----------------|
| **`required`** | **Obligatoire**, mais continue jusqu'au bout | ✅ Continue | ❌ Continue (mais échec final) |
| **`requisite`** | **Obligatoire**, arrêt immédiat sur échec | ✅ Continue | ❌ **Arrêt immédiat** |
| **`sufficient`** | **Suffisant** pour réussir (si aucun required avant a échoué) | ✅ **Arrêt immédiat** (succès) | ❌ Continue |
| **`optional`** | **Optionnel**, n'affecte pas le résultat global | ✅ Continue | ❌ Continue |

**📊 Exemples détaillés :**

**Scénario 1 : `required` vs `requisite`**
```bash
# Configuration avec required
auth    required    pam_unix.so         # Échec : continue quand même
auth    required    pam_ldap.so         # Testé même si pam_unix échoué
auth    required    pam_google_auth.so  # Testé aussi

# ❌ Résultat final : ÉCHEC (mais tous les modules ont été exécutés)
# ⚠️ Problème : Révèle l'existence d'autres mécanismes d'auth aux attaquants

# Configuration avec requisite
auth    requisite   pam_unix.so         # Échec : ARRÊT IMMÉDIAT
auth    required    pam_ldap.so         # ❌ PAS EXÉCUTÉ
auth    required    pam_google_auth.so  # ❌ PAS EXÉCUTÉ

# ❌ Résultat final : ÉCHEC (arrêt dès le premier échec)
# ✅ Avantage : Ne révèle pas les autres mécanismes
```

**Scénario 2 : `sufficient`**
```bash
# Configuration avec sufficient
auth    required     pam_env.so          # Toujours exécuté
auth    sufficient   pam_unix.so         # Si succès → ARRÊT (login OK)
auth    sufficient   pam_ldap.so         # ❌ Pas testé si pam_unix a réussi
auth    required     pam_deny.so         # Bloc par défaut

# Flux :
# 1. pam_env.so : OK → continue
# 2. pam_unix.so : OK → ✅ ARRÊT, LOGIN RÉUSSI (grâce à sufficient)
# (pam_ldap et pam_deny ne sont pas exécutés)

# Si pam_unix.so échoue :
# 1. pam_env.so : OK → continue
# 2. pam_unix.so : ❌ Échec → continue
# 3. pam_ldap.so : OK → ✅ ARRÊT, LOGIN RÉUSSI
```

**Scénario 3 : `optional`**
```bash
# Configuration avec optional
auth    required    pam_unix.so         # Doit réussir
auth    optional    pam_google_auth.so  # Bonus, mais pas obligatoire
account required    pam_unix.so

# Si pam_google_auth disponible : +Sécurité (2FA)
# Si pam_google_auth absent/échoue : Login autorisé quand même
```

#### 3. Modules PAM principaux

**Modules d'authentification :**

| Module | Rôle | Configuration |
|--------|------|---------------|
| **`pam_unix.so`** | Authentification classique `/etc/shadow` | `auth required pam_unix.so` |
| **`pam_sss.so`** | LDAP/AD via SSSD | `auth sufficient pam_sss.so` |
| **`pam_ldap.so`** | LDAP direct | `auth sufficient pam_ldap.so` |
| **`pam_krb5.so`** | Kerberos | `auth sufficient pam_krb5.so` |
| **`pam_google_authenticator.so`** | 2FA TOTP | `auth required pam_google_authenticator.so` |

**Modules de restriction d'accès (account) :**

| Module | Rôle | Configuration |
|--------|------|---------------|
| **`pam_nologin.so`** | Bloquer connexion si `/etc/nologin` existe (sauf root) | `account required pam_nologin.so` |
| **`pam_securetty.so`** | Root uniquement sur TTY sécurisés (`/etc/securetty`) | `auth required pam_securetty.so` |
| **`pam_time.so`** | Restrictions horaires (`/etc/security/time.conf`) | `account required pam_time.so` |
| **`pam_access.so`** | ACL d'accès (`/etc/security/access.conf`) | `account required pam_access.so` |
| **`pam_limits.so`** | Limites ressources (`/etc/security/limits.conf`) | `session required pam_limits.so` |

**Modules de mot de passe :**

| Module | Rôle | Configuration |
|--------|------|---------------|
| **`pam_pwquality.so`** | Politique de complexité du mot de passe | `password requisite pam_pwquality.so retry=3 minlen=12` |
| **`pam_pwhistory.so`** | Historique mots de passe (empêche réutilisation) | `password required pam_pwhistory.so remember=5` |
| **`pam_unix.so`** | Hash et stocke le mot de passe | `password required pam_unix.so sha512 rounds=5000` |
| **`pam_cracklib.so`** | Ancien module complexité (remplacé par pwquality) | Obsolète |

**Modules de session :**

| Module | Rôle | Configuration |
|--------|------|---------------|
| **`pam_env.so`** | Variables d'environnement (`/etc/security/pam_env.conf`) | `session required pam_env.so` |
| **`pam_lastlog.so`** | Afficher dernière connexion | `session optional pam_lastlog.so showfailed` |
| **`pam_motd.so`** | Afficher MOTD | `session optional pam_motd.so` |
| **`pam_mail.so`** | Notifier nouveau mail | `session optional pam_mail.so standard` |
| **`pam_umask.so`** | Définir umask | `session optional pam_umask.so` |
| **`pam_systemd.so`** | Intégration systemd (user sessions) | `session optional pam_systemd.so` |

#### 4. Exemples de configuration PAM

**Exemple 1 : `/etc/pam.d/login` (connexion console)**

```bash
cat /etc/pam.d/login
# PAM configuration for login

# Authentification
auth       requisite      pam_nologin.so          # Bloquer si /etc/nologin
auth       required       pam_securetty.so        # Root uniquement TTY sécurisés
auth       required       pam_env.so              # Charger variables env
auth       required       pam_unix.so nullok      # Vérifier mot de passe

# Account (vérifications)
account    required       pam_nologin.so
account    required       pam_unix.so
account    required       pam_time.so             # Restrictions horaires

# Password (changement)
password   required       pam_unix.so sha512

# Session
session    required       pam_limits.so           # Appliquer limites
session    required       pam_unix.so
session    optional       pam_lastlog.so          # Afficher dernière connexion
session    optional       pam_motd.so             # Afficher MOTD
session    optional       pam_mail.so standard    # Notifier mail
```

**Exemple 2 : `/etc/pam.d/sshd` (connexion SSH)**

```bash
cat /etc/pam.d/sshd
# PAM configuration for SSH

# Authentification
@include common-auth             # Inclut /etc/pam.d/common-auth

# Account
@include common-account

# Password
@include common-password

# Session
session    required     pam_limits.so
session    required     pam_env.so user_readenv=1
session    optional     pam_motd.so noupdate
@include common-session
```

**Exemple 3 : `/etc/pam.d/passwd` (changement mot de passe)**

```bash
cat /etc/pam.d/passwd
# PAM configuration for passwd command

@include common-password
```

**Exemple 4 : `/etc/pam.d/common-password` (Debian/Ubuntu)**

```bash
cat /etc/pam.d/common-password
# Vérifier la qualité (complexité, dictionnaire)
password   requisite      pam_pwquality.so retry=3 minlen=12 minclass=3

# Historique (empêcher réutilisation des 5 derniers mots de passe)
password   required       pam_pwhistory.so remember=5 use_authtok

# Hasher et stocker (SHA512, 5000 rounds)
password   [success=1 default=ignore]   pam_unix.so obscure use_authtok try_first_pass sha512 rounds=5000
```

**Exemple 5 : Activer 2FA avec Google Authenticator**

```bash
# Installer google-authenticator-libpam
apt install libpam-google-authenticator

# Chaque utilisateur configure son 2FA
google-authenticator
# QR Code affiché → Scanner avec app mobile

# Éditer /etc/pam.d/sshd
vi /etc/pam.d/sshd
# Ajouter AVANT @include common-auth :
auth required pam_google_authenticator.so

# Éditer /etc/ssh/sshd_config
ChallengeResponseAuthentication yes

# Redémarrer SSH
systemctl restart sshd

# Connexion nécessite maintenant : Mdp + Code OTP
```

**🔧 Debugging PAM :**

```bash
# Activer logs verbeux dans un module
auth required pam_unix.so debug

# Logs PAM dans /var/log/auth.log
tail -f /var/log/auth.log | grep pam

# Exemple de log :
# Feb 16 10:30:15 server sshd[1234]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=192.168.1.50 user=alice
```

**⚠️ Précautions critiques :**

❌ **Ne jamais tester PAM sur session unique !** → Risque de se bloquer  
✅ **Toujours garder 2 sessions root ouvertes** lors de modifications PAM  
✅ **Tester sur compte test** avant d'appliquer globalement  
✅ **Sauvegarder** `/etc/pam.d/` avant modifications  
❌ **Ne pas retirer `pam_unix.so`** sans mécanisme de remplacement  

```bash
# Sauvegarde avant modification
cp -r /etc/pam.d /etc/pam.d.backup.$(date +%Y%m%d)

# Tester sans fermer session root
# Terminal 1 : session root existante
# Terminal 2 : tester login utilisateur
# Terminal 3 : tester SSH

# Restaurer en cas de problème
cp -r /etc/pam.d.backup.YYYYMMDD/* /etc/pam.d/
```

### 6. Commandes de Consultation

#### Commande `id` - Informations d'identité

**Syntaxe et options :**
```bash
# Afficher toutes les informations (UID, GID, groupes)
id
# uid=1001(alice) gid=1001(alice) groups=1001(alice),27(sudo),999(docker)

# Informations pour un utilisateur spécifique
id alice
id bob

# Afficher uniquement l'UID
id -u alice
# 1001

# Afficher uniquement le GID (groupe primaire)
id -g alice
# 1001

# Afficher uniquement les groupes supplémentaires
id -G alice
# 1001 27 999

# Afficher les noms au lieu des numéros
id -Gn alice
# alice sudo docker

# Afficher le nom du groupe primaire
id -gn alice
# alice

# Afficher le contexte SELinux (si applicable)
id -Z
# unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
```

**Cas d'usage :**
```bash
# Vérifier si un utilisateur est dans le groupe sudo
id alice | grep -q sudo && echo "Admin" || echo "User"

# Script vérifiant les permissions
if [ $(id -u) -eq 0 ]; then
    echo "Exécution en tant que root"
else
    echo "Exécution en tant qu'utilisateur normal (UID $(id -u))"
fi

# Lister tous les groupes de l'utilisateur courant
echo "Mes groupes : $(id -Gn | tr ' ' ',')"
```

#### Commande `getent` - Interrogation des bases de données système

**`getent` interroge les bases de données système (NSS - Name Service Switch) définies dans `/etc/nsswitch.conf`.**

**Syntaxe :**
```bash
getent base_de_données [clé]
```

**Bases de données disponibles :**
- `passwd` : Utilisateurs
- `group` : Groupes
- `shadow` : Mots de passe (require root)
- `hosts` : Résolution DNS/hosts
- `services` : Services réseau
- `protocols` : Protocoles réseau
- `networks` : Réseaux

**Exemples avec `passwd` :**
```bash
# Afficher tous les utilisateurs
getent passwd

# Afficher un utilisateur spécifique
getent passwd alice
# alice:x:1001:1001:Alice Dupont:/home/alice:/bin/bash

# Afficher un utilisateur par UID
getent passwd 1001

# Extraire uniquement le shell
getent passwd alice | cut -d: -f7
# /bin/bash

# Lister tous les utilisateurs avec UID >= 1000 (utilisateurs humains)
getent passwd | awk -F: '$3 >= 1000 {print $1}'
```

**Exemples avec `group` :**
```bash
# Afficher tous les groupes
getent group

# Afficher un groupe spécifique
getent group sudo
# sudo:x:27:alice,admin

# Afficher un groupe par GID
getent group 27

# Lister les membres d'un groupe
getent group docker | cut -d: -f4
# alice,bob

# Vérifier si un utilisateur est dans un groupe
getent group sudo | grep -q alice && echo "Alice est admin" || echo "Alice n'est pas admin"
```

**Exemples avec `shadow` (require root) :**
```bash
# Afficher les informations shadow d'un utilisateur
sudo getent shadow alice
# alice:$6$xyz...:19750:0:99999:7:30::

# Vérifier l'état du mot de passe
sudo getent shadow alice | cut -d: -f2 | head -c 2
# $6 = SHA-512
# ! = Locked
# !! = No password
```

**🌐 Avantage de `getent` vs fichiers locaux :**

`getent` interroge NSS, donc il récupère les informations depuis :
- Fichiers locaux (`/etc/passwd`, `/etc/group`)
- LDAP (si configuré)
- Active Directory (si configuré avec Samba/Winbind)
- NIS/NIS+ (si configuré)
- Bases de données SQL (si configuré avec nss-mysql)

```bash
# Affiche l'utilisateur, qu'il soit local ou LDAP
getent passwd alice

# Recherche uniquement dans le fichier local
grep alice /etc/passwd

# Résolution DNS complète (fichiers + DNS)
getent hosts www.example.com

# Vérifier la configuration NSS
cat /etc/nsswitch.conf
# passwd:         files ldap
# group:          files ldap
# shadow:         files ldap
```

**Exemples pratiques de scripts :**
```bash
# Trouver les utilisateurs sans shell (comptes de service)
getent passwd | awk -F: '$7 ~ /nologin|false/ {print $1}'

# Trouver les groupes vides (sans membres)
getent group | awk -F: '$4 == "" {print $1":"$3}'

# Lister tous les utilisateurs d'un groupe spécifique
getent group developers | cut -d: -f4 | tr ',' '\n'

# Vérifier si un port est utilisé
getent services 22
# ssh                   22/tcp

# Résolution DNS via getent
getent ahosts google.com
```

#### Commande `lastlog` - Dernière connexion par utilisateur

**`lastlog` affiche les informations de dernière connexion pour tous les utilisateurs en interrogeant `/var/log/lastlog`.**

**Syntaxe :**
```bash
lastlog [options]
```

**Fichier source :**
- `/var/log/lastlog` : Base de données binaire indexée par UID
- Format binaire, ne PAS éditer directement
- Taille fixe par utilisateur (même si jamais connecté)

**Options :**

| Option | Description | Exemple |
|--------|-------------|---------|
| *(défaut)* | Afficher tous les utilisateurs | `lastlog` |
| `-u LOGIN` | Utilisateur spécifique | `lastlog -u alice` |
| `-u UID` | Par UID | `lastlog -u 1001` |
| `-u MIN-MAX` | Plage d'UIDs | `lastlog -u 1000-2000` |
| `-t DAYS` | Depuis N jours | `lastlog -t 7` |
| `-b DAYS` | Avant N jours | `lastlog -b 30` |
| `-C` | Compact (une ligne par utilisateur) | `lastlog -C` |

**Exemples d'utilisation :**

**1. Afficher toutes les dernières connexions :**
```bash
lastlog
# Sortie :
# Username         Port     From             Latest
# root             tty1                      Tue Feb 11 08:30:15 +0100 2026
# alice            pts/0    192.168.1.100    Mon Feb 16 10:25:42 +0100 2026
# bob              pts/1    192.168.1.101    Sun Feb 15 14:20:10 +0100 2026
# charlie          **Never logged in**
# daemon           **Never logged in**
```

**2. Dernière connexion d'un utilisateur spécifique :**
```bash
# Par nom
lastlog -u alice
# Username         Port     From             Latest
# alice            pts/0    192.168.1.100    Mon Feb 16 10:25:42 +0100 2026

# Par UID
lastlog -u 1001

# Plage d'UIDs (utilisateurs humains uniquement)
lastlog -u 1000-60000
```

**3. Utilisateurs connectés récemment :**
```bash
# Connectés dans les derniers 7 jours
lastlog -t 7
# Affiche uniquement les utilisateurs connectés cette semaine

# Connectés dans les dernières 24 heures
lastlog -t 1
```

**4. Utilisateurs inactifs :**
```bash
# Pas connectés depuis plus de 30 jours
lastlog -b 30

# Identifier comptes dormants (> 90 jours)
lastlog -b 90 -u 1000-60000
```

**5. Format compact :**
```bash
# Affichage compact (une ligne)
lastlog -C -u alice
```

**📊 Cas d'usage pratiques :**

**Identifier les comptes jamais utilisés :**
```bash
# Comptes entre UID 1000-9999 jamais connectés
lastlog -u 1000-9999 | grep "Never logged in"

# Lister uniquement les noms
lastlog -u 1000-9999 | grep "Never logged in" | awk '{print $1}'
```

**Audit de sécurité - Comptes dormants :**
```bash
#!/bin/bash
# Trouver les comptes inactifs depuis plus de 90 jours

echo "=== Comptes inactifs (>90 jours) ==="
lastlog -b 90 -u 1000-60000 | tail -n +2 | while read user port from latest; do
    if [ "$port" != "**Never" ]; then
        echo "$user : dernière connexion il y a plus de 90 jours"
    fi
done
```

**Vérifier la dernière connexion root :**
```bash
lastlog -u root
```

#### Commande `last` - Historique des connexions

**`last` affiche l'historique des connexions utilisateurs en interrogeant `/var/log/wtmp`.**

**Syntaxe :**
```bash
last [options] [username] [tty]
```

**Fichier source :**
- `/var/log/wtmp` : Historique complet des connexions/déconnexions
- Format binaire rotatif (wtmp, wtmp.1, wtmp.2.gz...)
- Contient : logins, logouts, reboots, runlevel changes

**Options :**

| Option | Description | Exemple |
|--------|-------------|---------|
| *(défaut)* | Afficher tout l'historique | `last` |
| `username` | Filtrer par utilisateur | `last alice` |
| `tty` | Filtrer par terminal | `last tty1` |
| `-n NUM` | Limiter à N entrées | `last -n 10` |
| `-NUM` | Raccourci pour `-n` | `last -10` |
| `-t YYYYMMDDHHMMSS` | Jusqu'à une date | `last -t 20260215000000` |
| `-s YYYYMMDDHHMMSS` | Depuis une date | `last -s 20260210000000` |
| `-p HH:MM` | À une heure spécifique | `last -p 10:00` |
| `-i` | Afficher l'IP au lieu de l'hostname | `last -i` |
| `-F` | Afficher dates complètes | `last -F` |
| `-w` | Afficher nom d'utilisateur complet | `last -w` |
| `-R` | Ne pas afficher hostname | `last -R` |
| `-x` | Inclure runlevel et arrêts | `last -x` |

**Exemples d'utilisation :**

**1. Historique complet (dernières entrées) :**
```bash
# 10 dernières connexions
last -10
# Sortie :
# alice    pts/0    192.168.1.100  Mon Feb 16 10:25   still logged in
# bob      pts/1    192.168.1.101  Mon Feb 16 09:15 - 10:20  (01:05)
# alice    tty7     :0             Mon Feb 16 08:00   still logged in
# root     tty1                    Mon Feb 16 07:45 - 08:00  (00:15)
# reboot   system boot  6.1.0-17   Mon Feb 16 07:44   still running
```

**2. Historique d'un utilisateur spécifique :**
```bash
# Toutes les connexions d'Alice
last alice

# 5 dernières connexions d'Alice
last -5 alice

# Avec dates complètes
last -F alice
```

**3. Filtrer par terminal :**
```bash
# Connexions sur tty1 (console physique)
last tty1

# Connexions SSH (pts/*)
last | grep pts
```

**4. Période spécifique :**
```bash
# Depuis le 10 février 2026
last -s 20260210000000

# Du 10 au 15 février
last -s 20260210000000 -t 20260215235959

# Avec format lisible
last -F -s 20260210000000 -t 20260215235959
```

**5. Afficher les IPs :**
```bash
# Afficher adresses IP au lieu des hostnames
last -i

# Combiné avec utilisateur
last -i alice
```

**6. Inclure reboots et arrêts :**
```bash
# Voir tous les événements (reboot, shutdown, runlevel)
last -x

# Uniquement les reboots
last reboot

# Derniers arrêts du système
last -x shutdown
```

**📊 Cas d'usage pratiques :**

**Analyser les connexions SSH suspectes :**
```bash
# Connexions depuis une IP spécifique
last | grep 192.168.1.50

# Tentatives de connexion root
last root | grep pts

# Connexions en dehors des heures de bureau (après 18h)
last -F | grep -E '1[89]:|2[0-3]:'
```

**Audit de sécurité - Connexions par utilisateur :**
```bash
#!/bin/bash
# Compter les connexions par utilisateur ce mois-ci

echo "=== Connexions ce mois ==="
MONTH_START=$(date -d "$(date +%Y-%m-01)" +%Y%m%d%H%M%S)
last -s $MONTH_START | awk '{print $1}' | grep -v "^$\|^wtmp\|^reboot" | sort | uniq -c | sort -rn
```

**Temps de connexion total par utilisateur :**
```bash
# Utiliser ac (accounting, complément de last)
ac -p
# alice                             120.50
# bob                                45.25
# root                                2.10
```

**Identifier les connexions simultanées d'un même utilisateur :**
```bash
# Vérifier si alice est connectée plusieurs fois
last alice | grep "still logged in"
```

**Dernière connexion avant reboot :**
```bash
# Voir qui était connecté avant le dernier reboot
last -x | grep -A 10 "system boot"
```

**📝 Différence `lastlog` vs `last` :**

| Critère | `lastlog` | `last` |
|---------|-----------|--------|
| **Source** | `/var/log/lastlog` | `/var/log/wtmp` |
| **Contenu** | Dernière connexion uniquement | Historique complet |
| **Par utilisateur** | Une entrée par utilisateur | Toutes les connexions |
| **Déconnexions** | ❌ Non | ✅ Oui (login/logout) |
| **Filtrage temporel** | `-t` / `-b` (jours) | `-s` / `-t` (timestamp précis) |
| **Taille fichier** | Fixe (indexé par UID) | Variable (rotatif) |
| **Usage** | "Quand Alice s'est connectée la dernière fois ?" | "Combien de fois Alice s'est connectée cette semaine ?" |

**Exemple combiné :**
```bash
# Dernière connexion d'alice
lastlog -u alice
# Username         Port     From             Latest
# alice            pts/0    192.168.1.100    Mon Feb 16 10:25:42 +0100 2026

# Historique complet d'alice
last -10 alice
# alice    pts/0    192.168.1.100  Mon Feb 16 10:25   still logged in
# alice    pts/2    192.168.1.100  Sun Feb 15 14:30 - 18:45  (04:15)
# alice    tty7     :0             Fri Feb 13 08:00 - 17:00  (09:00)
```

**⚠️ Notes importantes :**
- `/var/log/wtmp` peut devenir volumineux → rotation via `logrotate`
- `/var/log/lastlog` peut être sparse (trous pour UIDs jamais utilisés)
- Nécessite accès root pour lire certaines informations sensibles
- `last` peut être trompé si `/var/log/wtmp` est modifié → audit régulier

#### Commande `chsh` - Changer le shell

**`chsh` (CHange SHell) permet de modifier le shell de connexion d'un utilisateur.**

**Syntaxe :**
```bash
chsh [options] [username]
```

**Restriction de sécurité :**
⚠️ **Le shell choisi DOIT être listé dans `/etc/shells`**

**Options :**

| Option | Description | Exemple |
|--------|-------------|---------|
| *(défaut)* | Mode interactif | `chsh` |
| `-s SHELL` | Spécifier le shell directement | `chsh -s /bin/zsh alice` |
| `-l, --list-shells` | Lister les shells disponibles | `chsh -l` |

**Fichier de référence : `/etc/shells`**

**Contenu typique :**
```bash
cat /etc/shells
# /etc/shells: valid login shells
/bin/sh
/bin/bash
/bin/dash
/bin/zsh
/usr/bin/zsh
/bin/fish
/usr/bin/fish
/bin/ksh
/usr/bin/tmux
```

**Exemples d'utilisation :**

**1. Mode interactif (utilisateur change son propre shell) :**
```bash
# En tant qu'utilisateur normal
chsh
# Password: *********
# Changing the login shell for alice
# Enter the new value, or press ENTER for the default
#         Login Shell [/bin/bash]: /bin/zsh
```

**2. Mode direct (root peut changer le shell de n'importe qui) :**
```bash
# Root change le shell d'alice
chsh -s /bin/zsh alice

# Vérification
getent passwd alice | cut -d: -f7
# /bin/zsh

# Ou
grep alice /etc/passwd
# alice:x:1001:1001:Alice Dupont:/home/alice:/bin/zsh
```

**3. Lister les shells disponibles :**
```bash
# Afficher les shells autorisés
chsh -l
# /bin/sh
# /bin/bash
# /bin/zsh
# /bin/fish

# Ou directement
cat /etc/shells
```

**4. Utilisateur change son propre shell (sans interaction) :**
```bash
# Alice change son shell en zsh
chsh -s /bin/zsh
# Password: *********

# Vérification
echo $SHELL
# /bin/bash (encore l'ancien, car session en cours)

# Se déconnecter et reconnecter
exit
# Nouvelle session
echo $SHELL
# /bin/zsh (nouveau shell actif)
```

**❌ Erreurs courantes :**

**1. Shell non listé dans /etc/shells :**
```bash
# Tenter d'utiliser un shell non autorisé
chsh -s /usr/local/bin/fish alice
# chsh: /usr/local/bin/fish is an invalid shell

# Solution : ajouter le shell à /etc/shells
echo "/usr/local/bin/fish" | sudo tee -a /etc/shells
chsh -s /usr/local/bin/fish alice
# ✅ OK
```

**2. Shell inexistant via:**
```bash
# Tenter un shell qui n'existe pas
chsh -s /bin/nonexistent alice
# chsh: /bin/nonexistent does not exist

# Vérifier d'abord
which zsh
# /usr/bin/zsh
chsh -s /usr/bin/zsh alice
```

**🔧 Cas d'usage pratiques :**

**Désactiver la connexion interactive (compte de service) :**
```bash
# Compte sans shell interactif
chsh -s /usr/sbin/nologin service_account
# ou
chsh -s /bin/false service_account

# Vérifier /etc/shells
grep -E "nologin|false" /etc/shells
# Si absent, ajouter :
echo "/usr/sbin/nologin" | sudo tee -a /etc/shells
echo "/bin/false" | sudo tee -a /etc/shells
```

**Forcer bash pour tous les nouveaux utilisateurs :**
```bash
# Modifier le défaut dans /etc/default/useradd
useradd -D -s /bin/bash
```

**Script de migration de shell :**
```bash
#!/bin/bash
# Migrer tous les utilisateurs de bash vers zsh

for user in $(getent passwd | awk -F: '$7 == "/bin/bash" && $3 >= 1000 {print $1}'); do
    echo "Migration de $user vers zsh..."
    chsh -s /bin/zsh $user
done
```

#### Commande `chfn` - Changer les informations GECOS

**`chfn` (CHange Full Name) permet de modifier le champ GECOS (commentaire) dans `/etc/passwd`.**

**Syntaxe :**
```bash
chfn [options] [username]
```

**Champ GECOS - Format typique :**
```
Nom Complet,Lieu/Bureau,Téléphone Bureau,Téléphone Domicile,Autre
```

**Options :**

| Option | Description | Exemple |
|--------|-------------|---------|
| *(défaut)* | Mode interactif | `chfn` |
| `-f "Nom"` | Nom complet | `chfn -f "Alice Dupont" alice` |
| `-r "Lieu"` | Numéro de bureau/lieu | `chfn -r "Bureau 205" alice` |
| `-w "Tél"` | Téléphone bureau | `chfn -w "01.23.45.67.89" alice` |
| `-h "Tél"` | Téléphone domicile | `chfn -h "01.98.76.54.32" alice` |
| `-o "Autre"` | Autres informations | `chfn -o "Dept IT" alice` |

**Exemples d'utilisation :**

**1. Mode interactif (utilisateur modifie ses propres infos) :**
```bash
# En tant qu'utilisateur normal
chfn
# Password: *********
# Changing the user information for alice
# Enter the new value, or press ENTER for the default
#         Full Name []: Alice Dupont
#         Room Number []: 205
#         Work Phone []: 01.23.45.67.89
#         Home Phone []: 01.98.76.54.32
#         Other []: IT Department
```

**2. Mode direct (root modifie les infos de n'importe qui) :**
```bash
# Modifier seulement le nom complet
chfn -f "Alice Martin-Dupont" alice

# Modifier plusieurs champs
chfn -f "Bob Smith" -r "Bureau 310" -w "01.11.22.33.44" bob

# Vérification
getent passwd alice
# alice:x:1001:1001:Alice Martin-Dupont,205,01.23.45.67.89,01.98.76.54.32,IT Department:/home/alice:/bin/zsh
```

**3. Modifier un seul champ :**
```bash
# Changer le numéro de bureau
chfn -r "Bureau 310" alice

# Changer le téléphone bureau
chfn -w "01.99.88.77.66" alice

# Ajouter une information supplémentaire
chfn -o "Manager DevOps" alice
```

**4. Vider un champ :**
```bash
# Supprimer le téléphone domicile
chfn -h "" alice

# Résultat dans /etc/passwd :
# alice:x:1001:1001:Alice Dupont,205,01.23.45.67.89,,IT Department:/home/alice:/bin/bash
#                                                      ^^ vide
```

**📋 Format GECOS détaillé :**

**Structure dans `/etc/passwd` :**
```
username:x:UID:GID:full_name,room,work_phone,home_phone,other:home:shell
                   └──────────────── GECOS ─────────────────┘
```

**Exemples réels :**
```bash
# Utilisateur simple
alice:x:1001:1001:Alice Dupont:/home/alice:/bin/bash

# Utilisateur avec bureau
bob:x:1002:1002:Bob Smith,210:/home/bob:/bin/bash

# Utilisateur complet
charlie:x:1003:1003:Charlie Johnson,305,01.23.45.67.89,01.98.76.54.32,IT Support:/home/charlie:/bin/bash

# Compte système (GECOS descriptif)
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
```

**🔍 Consultation du GECOS :**

```bash
# Extraire le GECOS
getent passwd alice | cut -d: -f5
# Alice Dupont,205,01.23.45.67.89,01.98.76.54.32,IT Department

# Extraire seulement le nom complet
getent passwd alice | cut -d: -f5 | cut -d, -f1
# Alice Dupont

# Avec finger (si installé)
finger alice
# Login: alice                    Name: Alice Dupont
# Directory: /home/alice          Shell: /bin/bash
# Office: 205, 01.23.45.67.89     Home Phone: 01.98.76.54.32
# On since Mon Feb 16 10:25 (CET) on pts/0 from 192.168.1.100
```

**⚠️ Restrictions de sécurité :**

**Configuration PAM dans `/etc/pam.d/chfn` :**
```bash
# Par défaut, utilisateurs peuvent modifier leurs propres infos
auth       sufficient   pam_rootok.so
auth       required     pam_unix.so

# Restreindre certains champs
# Dans /etc/login.defs :
CHFN_RESTRICT rwh   # r=room, w=work_phone, h=home_phone
# Utilisateurs ne peuvent modifier que ces 3 champs (pas le nom complet)
```

**🔧 Scripts d'administration :**

**Mettre à jour en masse les informations de département :**
```bash
#!/bin/bash
# Ajouter le département dans GECOS pour tous les utilisateurs IT

IT_USERS="alice bob charlie"

for user in $IT_USERS; do
    chfn -o "IT Department" $user
    echo "✅ $user : Département ajouté"
done
```

**Extraire un annuaire des employés :**
```bash
#!/bin/bash
# Générer un annuaire depuis GECOS

echo "=== Annuaire du Personnel ==="
echo "Nom | Bureau | Tél Bureau | Tél Domicile"
echo "--------------------------------------------"

getent passwd | awk -F: '$3 >= 1000 && $3 < 60000 {
    split($5, gecos, ",")
    printf "%-20s | %-10s | %-15s | %-15s\n", gecos[1], gecos[2], gecos[3], gecos[4]
}'
```

**📊 Comparaison chsh vs chfn vs usermod :**

| Action | `chsh` | `chfn` | `usermod` |
|--------|--------|--------|-----------|
| **Changer shell** | ✅ `-s` | ❌ | ✅ `-s` |
| **GECOS** | ❌ | ✅ `-f/-r/-w/-h/-o` | ✅ `-c` |
| **UID** | ❌ | ❌ | ✅ `-u` |
| **GID** | ❌ | ❌ | ✅ `-g` |
| **Home** | ❌ | ❌ | ✅ `-d/-m` |
| **Groupes secondaires** | ❌ | ❌ | ✅ `-aG` |
| **Utilisateur normal peut s'utiliser** | ✅ | ✅ | ❌ (root only) |

**💡 Bonnes pratiques :**

✅ Utiliser GECOS pour des informations professionnelles utiles  
✅ Standardiser le format GECOS dans l'organisation  
✅ Ne PAS mettre d'informations sensibles (mot de passe, SSN, etc.)  
✅ Vérifier `/etc/shells` avant d'ajouter un nouveau shell  
✅ Tester le nouveau shell avant de le définir en permanence  
❌ Ne jamais utiliser un shell inexistant (bloquerait la connexion)  

### 7. Vérification de l'Intégrité des Fichiers - `pwck` et `grpck`

**`pwck` - Vérifier la cohérence de `/etc/passwd` et `/etc/shadow`**

**`pwck` (Password Check) détecte les erreurs et incohérences dans les fichiers de comptes utilisateurs.**

**Syntaxe :**
```bash
pwck [options]
```

**Options :**

| Option | Description | Exemple |
|--------|-------------|---------|
| *(défaut)* | Mode interactif, propose corrections | `pwck` |
| `-r` | **Mode read-only**, affiche erreurs sans modifier | `pwck -r` |
| `-q` | Silence, affiche uniquement les erreurs | `pwck -q` |
| `-s` | Trier par UID | `pwck -s` |

**Types d'erreurs détectées :**
✅ **Nombre de champs incorrect** (doit être 7 pour passwd, 9 pour shadow)  
✅ **Nom d'utilisateur invalide** (caractères interdits, trop long)  
✅ **UID en double** (plusieurs utilisateurs avec le même UID)  
✅ **GID inexistant** (groupe primaire non présent dans /etc/group)  
✅ **Répertoire personnel manquant**  
✅ **Shell inexistant ou invalide**  
✅ **Entrée dans shadow sans correspondance dans passwd**  
✅ **Entrée dans passwd sans correspondance dans shadow**  

**Exemples d'utilisation :**

**1. Vérification standard (mode interactif) :**
```bash
# Vérification avec propositions de correction
pwck
# user 'baduser': directory '/home/baduser' does not exist
# Delete line 'baduser:x:1050:1050::/home/baduser:/bin/bash'? no
```

**2. Mode read-only (audit, sans modification) :**
```bash
# Afficher uniquement les problèmes sans corriger
pwck -r
# user 'alice': directory '/home/alice' does not exist
# duplicate user entry 'bob'
# user 'charlie': GID '9999' does not exist
# no matching password file entry in /etc/shadow for testuser
```

**3. Vérification silencieuse (scripts) :**
```bash
# Uniquement les erreurs, pas de blabla
pwck -q -r
```

**4. Triée par UID :**
```bash
# Vérifier et trier par UID
pwck -s

# Combiner avec read-only
pwck -s -r
```

**Exemple de sortie avec erreurs :**
```bash
$ pwck -r
user 'backup': directory '/var/backups' does not exist
user 'testuser1': GID '5000' does not exist
duplicate UID 1001: alice, alice_backup
no matching password file entry in /etc/shadow for orphanuser
pwck: no changes
```

**🔧 Corrections typiques :**

Pour corriger les erreurs détectées :
```bash
# Erreur : Répertoire manquant
# user 'alice': directory '/home/alice' does not exist
mkdir -p /home/alice
chown alice:alice /home/alice
chmod 755 /home/alice

# Erreur : GID inexistant
# user 'bob': GID '5000' does not exist
groupadd -g 5000 bob_group
# ou
usermod -g 1000 bob  # Changer vers un groupe existant

# Erreur : UID en double
# duplicate UID 1001: alice, alice_backup
usermod -u 1050 alice_backup  # Changer l'UID du second

# Erreur : Entrée orpheline dans shadow
# no matching password file entry in /etc/shadow for olduser
# Éditer /etc/shadow pour supprimer la ligne (avec vipw -s)
vipw -s
# Ou manuellement (déconseillé) :
sudo sed -i '/^olduser:/d' /etc/shadow
```

**`grpck` - Vérifier la cohérence de `/etc/group` et `/etc/gshadow`**

**`grpck` (Group Check) fonctionne de manière similaire à `pwck` pour les groupes.**

**Syntaxe :**
```bash
grpck [options]
```

**Options identiques à `pwck` :**

| Option | Description | Exemple |
|--------|-------------|---------|
| *(défaut)* | Mode interactif | `grpck` |
| `-r` | **Read-only** | `grpck -r` |
| `-q` | Silence | `grpck -q` |
| `-s` | Trier par GID | `grpck -s` |

**Types d'erreurs détectées :**
✅ **Nombre de champs incorrect** (4 pour group, 4 pour gshadow)  
✅ **Nom de groupe invalide**  
✅ **GID en double**  
✅ **Membre non existant** (utilisateur listé qui n'existe pas)  
✅ **Entrée dans gshadow sans correspondance dans group**  
✅ **Administrateurs invalides**  

**Exemples d'utilisation :**

**1. Vérification standard :**
```bash
# Vérification avec propositions de correction
grpck
# group 'developers': member 'oldemployee' does not exist
# Delete member 'oldemployee'? yes
```

**2. Mode read-only (audit) :**
```bash
# Afficher les problèmes
grpck -r
# group 'docker': member 'alice' does not exist
# duplicate group entry 'developers'
# group 'finance': GID '3000' exists twice
```

**3. Trier par GID :**
```bash
grpck -s -r
```

**Exemple de sortie avec erreurs :**
```bash
$ grpck -r
duplicate group entry 'developers'
group 'oldproject': member 'bob_old' does not exist
group 'finance': GID '2000' exists twice
grpck: no changes
```

**🔧 Corrections typiques :**

```bash
# Erreur : Membre inexistant
# group 'developers': member 'oldemployee' does not exist
gpasswd -d oldemployee developers
# Ou éditer /etc/group manuellement (déconseillé)

# Erreur : GID en double
# duplicate GID 2000: finance, accounting
groupmod -g 2100 accounting  # Changer le GID

# Erreur : Groupe en double
# duplicate group entry 'developers'
# Supprimer une des entrées avec vigr
vigr
```

**📋 Utilisation recommandée dans la maintenance :**

**Script d'audit mensuel :**
```bash
#!/bin/bash
# audit_accounts.sh - Vérification mensuelle des comptes

echo "=== Audit des comptes utilisateurs ==="
echo "Date : $(date)"
echo ""

echo "--- Vérification /etc/passwd et /etc/shadow ---"
pwck -r
STATUS_PWCK=$?

echo ""
echo "--- Vérification /etc/group et /etc/gshadow ---"
grpck -r
STATUS_GRPCK=$?

echo ""
if [ $STATUS_PWCK -eq 0 ] && [ $STATUS_GRPCK -eq 0 ]; then
    echo "✅ Aucune erreur détectée"
else
    echo "❌ Erreurs détectées - Action requise"
fi

# Autres vérifications utiles
echo ""
echo "--- UIDs en double ---"
awk -F: '{print $3}' /etc/passwd | sort | uniq -d

echo ""
echo "--- GIDs en double ---"
awk -F: '{print $3}' /etc/group | sort | uniq -d

echo ""
echo "--- Utilisateurs sans répertoire personnel ---"
while IFS=: read -r user _ uid gid _ home _; do
    if [ $uid -ge 1000 ] && [ ! -d "$home" ]; then
        echo "$user : $home (manquant)"
    fi
done < /etc/passwd
```

**Exécution régulière via cron :**
```bash
# Ajouter au crontab root
0 2 1 * * /usr/local/bin/audit_accounts.sh > /var/log/account_audit.log 2>&1
# Exécute le 1er de chaque mois à 2h du matin
```

**⚠️ Bonnes pratiques :**
- Toujours utiliser `-r` pour un audit avant de corriger
- Faire une sauvegarde avant de lancer en mode interactif
- Exécuter après des modifications manuelles de fichiers
- Intégrer dans les procédures de maintenance régulière

---

## 🔧 10 Scénarios Pratiques

### Scénario 1 : Création d'un nouvel employé avec configuration complète

**Contexte :** Alice Dupont rejoint l'équipe DevOps. Elle doit avoir accès sudo, Docker, et son compte doit expirer dans 6 mois.

**Solution :**
```bash
# Création du compte avec date d'expiration
useradd -m -c "Alice Dupont - DevOps Team" -s /bin/bash -e 2026-08-10 alice

# Ajouter aux groupes sudo et docker
usermod -aG sudo,docker alice

# Définir le mot de passe et forcer le changement au 1er login
passwd alice
chage -d 0 alice

# Configurer la politique de mot de passe (90 jours max, 7 jours warning)
chage -M 90 -W 7 alice

# Vérification
id alice
chage -l alice
getent passwd alice
```

### Scénario 2 : Migration d'un utilisateur vers un nouveau répertoire personnel

**Contexte :** Le home de Bob doit être déplacé de `/home/bob` vers `/data/users/bob`.

**Solution :**
```bash
# Vérifier que Bob n'est pas connecté
w | grep bob
pkill -u bob  # Si nécessaire

# Créer le nouveau répertoire
mkdir -p /data/users

# Déplacer le home ET le contenu (-m)
usermod -d /data/users/bob -m bob

# Vérification
getent passwd bob | cut -d: -f6
# /data/users/bob

ls -la /data/users/bob
# Contenu déplacé

# Vérifier les permissions
ls -ld /data/users/bob
# drwxr-xr-x 15 bob bob 4096 Feb 10 10:30 /data/users/bob
```

### Scénario 3 : Création de comptes stagiaires temporaires

**Contexte :** Créer 5 comptes stagiaires qui expirent le 30 juin 2026, avec mot de passe simple qu'ils doivent changer.

**Solution :**
```bash
# Script de création
for i in {1..5}; do
    username="stagiaire$i"
    
    # Création avec expiration
    useradd -m -c "Stagiaire $i" -s /bin/bash -e 2026-06-30 $username
    
    # Mot de passe temporaire (root peut ignorer les règles)
    echo "$username:Welcome2026" | chpasswd
    
    # Forcer le changement au 1er login
    chage -d 0 $username
    
    # Politique stricte : changement tous les 30 jours
    chage -M 30 -W 7 $username
    
    echo "Compte $username créé"
done

# Vérification
for i in {1..5}; do
    echo "=== stagiaire$i ==="
    chage -l stagiaire$i | grep -E "(expire|Last)"
done
```

### Scénario 4 : Verrouillage temporaire d'un compte suspect

**Contexte :** Le compte de Charlie montre une activité suspecte. Verrouiller temporairement le compte pour investigation.

**Solution :**
```bash
# Méthode 1 : usermod -L (recommandé)
usermod -L charlie
# Ajoute ! devant le hash dans /etc/shadow

# Méthode 2 : passwd -l
passwd -l charlie

# Vérification de l'état
passwd -S charlie
# charlie LK 2026-02-01 0 99999 7 -1 (LK = Locked)

# Vérifier dans /etc/shadow
sudo grep charlie /etc/shadow
# charlie:!$6$xyz...:...

# Investigation terminée : déverrouiller
usermod -U charlie

# Vérification
passwd -S charlie
# charlie PS 2026-02-01 0 99999 7 -1 (PS = Password Set)
```

### Scénario 5 : Gestion des groupes pour un projet

**Contexte :** Créer un groupe `projet_phoenix` avec Alice comme admin et Bob, Charlie comme membres. Les fichiers créés doivent appartenir au groupe.

**Solution :**
```bash
# Créer le groupe
groupadd -g 3000 projet_phoenix

# Ajouter les membres
usermod -aG projet_phoenix alice
usermod -aG projet_phoenix bob
usermod -aG projet_phoenix charlie

# Définir Alice comme administrateur du groupe
gpasswd -A alice projet_phoenix

# Créer le répertoire de projet avec setgid
mkdir /data/projet_phoenix
chgrp projet_phoenix /data/projet_phoenix
chmod 2775 /data/projet_phoenix
# 2 = setgid : les fichiers créés héritent du groupe

# Vérification
ls -ld /data/projet_phoenix
# drwxrwsr-x 2 root projet_phoenix 4096 Feb 10 11:00 /data/projet_phoenix

getent group projet_phoenix
# projet_phoenix:x:3000:alice,bob,charlie

# Test : bob crée un fichier
su - bob
cd /data/projet_phoenix
touch test.txt
ls -l test.txt
# -rw-rw-r-- 1 bob projet_phoenix 0 Feb 10 11:05 test.txt
# Le groupe est bien projet_phoenix grâce au setgid !
```

### Scénario 6 : Suppression propre d'un ancien utilisateur

**Contexte :** L'employé David a quitté l'entreprise. Archiver ses fichiers avant de supprimer le compte.

**Solution :**
```bash
# Verrouiller immédiatement le compte
usermod -L david

# Vérifier les processus en cours
ps -u david
pkill -u david

# Archiver le répertoire personnel
tar -czf /backup/david_home_$(date +%Y%m%d).tar.gz /home/david

# Archiver le mail spool
tar -czf /backup/david_mail_$(date +%Y%m%d).tar.gz /var/mail/david

# Trouver tous les fichiers appartenant à david dans le système
find / -user david -ls > /backup/david_files_$(date +%Y%m%d).txt 2>/dev/null

# Suppression complète du compte
userdel -r david

# Vérification
getent passwd david
# (aucun résultat)

ls /home/david
# ls: cannot access '/home/david': No such file or directory

ls /backup/david_*
# /backup/david_home_20260210.tar.gz
# /backup/david_mail_20260210.tar.gz
# /backup/david_files_20260210.txt
```

### Scénario 7 : Audit des comptes utilisateurs

**Contexte :** Générer un rapport complet de tous les utilisateurs humains (UID ≥ 1000) avec leurs informations.

**Solution :**
```bash
#!/bin/bash
# audit_users.sh

echo "=== AUDIT DES COMPTES UTILISATEURS ==="
echo "Date : $(date)"
echo ""
echo "Format: Username | UID | Shell | Groupes | Statut Mdp | Expiration Compte"
echo "------------------------------------------------------------------------------------"

# Parcourir les utilisateurs avec UID >= 1000
getent passwd | awk -F: '$3 >= 1000 && $3 < 65534 {print $1}' | while read user; do
    uid=$(id -u $user)
    shell=$(getent passwd $user | cut -d: -f7)
    groups=$(id -Gn $user | tr ' ' ',')
    
    # Statut du mot de passe
    status=$(passwd -S $user 2>/dev/null | awk '{print $2}')
    
    # Date d'expiration du compte
    expiry=$(chage -l $user 2>/dev/null | grep "Account expires" | cut -d: -f2 | xargs)
    
    printf "%-15s | %-5s | %-15s | %-30s | %-5s | %s\n" \
        "$user" "$uid" "$shell" "${groups:0:30}" "$status" "$expiry"
done

echo ""
echo "=== STATISTIQUES ==="
total=$(getent passwd | awk -F: '$3 >= 1000 && $3 < 65534' | wc -l)
locked=$(getent passwd | awk -F: '$3 >= 1000 && $3 < 65534 {print $1}' | while read u; do passwd -S $u 2>/dev/null | grep " L "; done | wc -l)
echo "Total utilisateurs : $total"
echo "Comptes verrouillés : $locked"

# Comptes expirant dans les 30 jours
echo ""
echo "=== COMPTES EXPIRANT DANS LES 30 JOURS ==="
for user in $(getent passwd | awk -F: '$3 >= 1000 && $3 < 65534 {print $1}'); do
    chage -l $user 2>/dev/null | grep -q "never$" && continue
    expiry_line=$(chage -l $user 2>/dev/null | grep "Account expires")
    echo "$user: $expiry_line"
done
```

### Scénario 8 : Changement de shell pour tous les utilisateurs

**Contexte :** Migrer tous les utilisateurs de `/bin/bash` vers `/bin/zsh` pour standardisation.

**Solution :**
```bash
# Installer zsh si nécessaire
apt-get install zsh -y  # Debian/Ubuntu
yum install zsh -y      # RedHat/CentOS

# Vérifier que zsh est dans /etc/shells
grep -q '/bin/zsh' /etc/shells || echo '/bin/zsh' >> /etc/shells

# Lister les utilisateurs avec bash
getent passwd | awk -F: '$7 == "/bin/bash" && $3 >= 1000 && $3 < 65534 {print $1}'

# Changer le shell pour chaque utilisateur
for user in $(getent passwd | awk -F: '$7 == "/bin/bash" && $3 >= 1000 && $3 < 65534 {print $1}'); do
    echo "Changement du shell pour $user : bash -> zsh"
    usermod -s /bin/zsh $user
done

# Copier une configuration zsh par défaut
cp /usr/share/zsh/functions/Newuser/.zshrc /etc/skel/

# Vérification
echo "=== Utilisateurs avec zsh ==="
getent passwd | awk -F: '$7 == "/bin/zsh" {print $1": "$7}'
```

### Scénario 9 : Réinitialisation massive des mots de passe

**Contexte :** Suite à une potentielle compromission, forcer tous les utilisateurs à changer leur mot de passe au prochain login.

**Solution :**
```bash
#!/bin/bash
# force_password_change.sh

echo "=== FORÇAGE DU CHANGEMENT DE MOT DE PASSE ==="

# Liste des utilisateurs humains (UID >= 1000)
for user in $(getent passwd | awk -F: '$3 >= 1000 && $3 < 65534 {print $1}'); do
    # Vérifier que le compte n'est pas verrouillé
    status=$(passwd -S $user 2>/dev/null | awk '{print $2}')
    
    if [ "$status" != "L" ] && [ "$status" != "LK" ]; then
        echo "Expiration du mot de passe pour : $user"
        chage -d 0 $user
        
        # Optionnel : définir une politique stricte
        chage -M 90 -W 14 -I 30 $user
    else
        echo "IGNORÉ (compte verrouillé) : $user"
    fi
done

echo ""
echo "=== VÉRIFICATION ==="
for user in $(getent passwd | awk -F: '$3 >= 1000 && $3 < 65534 {print $1}'); do
    last_change=$(chage -l $user 2>/dev/null | grep "Last password change" | cut -d: -f2 | xargs)
    echo "$user : $last_change"
done

echo ""
echo "Les utilisateurs devront changer leur mot de passe à la prochaine connexion."
```

### Scénario 10 : Création d'utilisateurs depuis un fichier CSV

**Contexte :** Créer plusieurs utilisateurs à partir d'un fichier CSV : `nom,prenom,username,groupe,expiration`

**Fichier `users.csv` :**
```csv
Dupont,Alice,alice,developers,2027-12-31
Martin,Bob,bob,developers,2027-12-31
Bernard,Charlie,charlie,operations,2026-06-30
Dubois,David,david,operations,2026-06-30
```

**Solution :**
```bash
#!/bin/bash
# import_users.sh

CSV_FILE="users.csv"

# Vérifier que le fichier existe
if [ ! -f "$CSV_FILE" ]; then
    echo "Erreur : $CSV_FILE introuvable"
    exit 1
fi

# Lecture du CSV (skip header si présent)
tail -n +1 "$CSV_FILE" | while IFS=',' read -r nom prenom username groupe expiration; do
    # Ignorer les lignes vides
    [ -z "$username" ] && continue
    
    echo "=== Création de $username ($prenom $nom) ==="
    
    # Vérifier si l'utilisateur existe déjà
    if id "$username" &>/dev/null; then
        echo "ERREUR : $username existe déjà"
        continue
    fi
    
    # Créer le groupe s'il n'existe pas
    if ! getent group "$groupe" &>/dev/null; then
        echo "Création du groupe : $groupe"
        groupadd "$groupe"
    fi
    
    # Créer l'utilisateur
    useradd -m \
        -c "$prenom $nom" \
        -s /bin/bash \
        -g "$groupe" \
        -e "$expiration" \
        "$username"
    
    # Générer un mot de passe temporaire
    temp_password=$(openssl rand -base64 12)
    echo "$username:$temp_password" | chpasswd
    
    # Forcer le changement au 1er login
    chage -d 0 "$username"
    
    # Sauvegarder le mot de passe temporaire (à envoyer par email)
    echo "$username,$temp_password" >> /tmp/passwords_$(date +%Y%m%d).csv
    
    echo "✓ $username créé avec succès (expire: $expiration)"
    echo ""
done

echo "=== RÉSUMÉ ==="
echo "Mots de passe temporaires : /tmp/passwords_$(date +%Y%m%d).csv"
echo "IMPORTANT : Envoyer les mots de passe de manière sécurisée et SUPPRIMER ce fichier !"
```

---

## ⚠️ 10 Erreurs Courantes

### Erreur 1 : Suppression d'utilisateur sans l'option `-r`

**Problème :**
```bash
userdel alice
# Le compte est supprimé, mais /home/alice et /var/mail/alice restent
```

**Conséquence :** Fichiers orphelins (UID sans propriétaire), gaspillage d'espace disque, potentielle fuite de données.

**Solution :**
```bash
# Toujours utiliser -r pour une suppression complète
userdel -r alice

# Ou archiver avant suppression
tar -czf /backup/alice_$(date +%Y%m%d).tar.gz /home/alice
userdel -r alice

# Trouver les fichiers orphelins après coup
find / -nouser -ls 2>/dev/null
# Supprimer ou ré-attribuer
find / -nouser -exec chown root:root {} \; 2>/dev/null
```

### Erreur 2 : Oublier `-a` avec `-G` dans `usermod`

**Problème :**
```bash
# Alice fait partie de : sudo, docker, developers
id alice
# groups=1001(alice),27(sudo),999(docker),2000(developers)

# ERREUR : utiliser -G sans -a
usermod -G video alice

# Résultat : Alice a PERDU sudo, docker, developers !
id alice
# groups=1001(alice),44(video)
```

**Solution :**
```bash
# CORRECT : utiliser -aG pour AJOUTER
usermod -aG video alice
# groups=1001(alice),27(sudo),999(docker),2000(developers),44(video)

# Pour remplacer intentionnellement tous les groupes
usermod -G sudo,docker,video alice
```

### Erreur 3 : Verrouiller root avec `usermod -L`

**Problème :**
```bash
# DANGER : ne JAMAIS faire ça !
usermod -L root
# Résultat : impossible de se connecter en root, même en rescue mode
```

**Conséquence :** Perte totale d'accès administrateur, nécessite un boot en mode rescue pour corriger.

**Solution :**
```bash
# Si cela arrive, booter en mode rescue/single-user
# Au menu GRUB, éditer la ligne linux et ajouter : init=/bin/bash
# Remonter le système en écriture
mount -o remount,rw /

# Déverrouiller root
usermod -U root

# Ou éditer /etc/shadow manuellement
vi /etc/shadow
# Retirer le ! devant le hash de root

# Redémarrer
exec /sbin/init
```

### Erreur 4 : Créer des utilisateurs sans répertoire personnel

**Problème :**
```bash
# Oublier -m
useradd alice
# Pas de /home/alice créé

# Alice se connecte
ssh alice@server
# Pas de répertoire personnel, crée des problèmes pour les configs
```

**Solution :**
```bash
# TOUJOURS utiliser -m (sauf pour les comptes système)
useradd -m alice

# Si oublié, créer manuellement
mkdir /home/alice
cp -r /etc/skel/. /home/alice/
chown -R alice:alice /home/alice
chmod 700 /home/alice

# Ou modifier et recréer
usermod -d /home/alice -m alice
```

### Erreur 5 : Mauvaise manipulation de `/etc/shadow`

**Problème :**
```bash
# Édition manuelle incorrecte
vim /etc/shadow
# Erreur de syntaxe : champ manquant, format incorrect

# Résultat : TOUS les comptes sont bloqués !
# Impossible de se connecter
```

**Prévention :**
```bash
# NE JAMAIS éditer /etc/shadow directement
# Utiliser les commandes dédiées : passwd, chage, usermod

# Si édition nécessaire, utiliser vipw -s (verrouillage + vérification)
vipw -s
# Vérification de la syntaxe à la sauvegarde

# Backup automatique
cp -p /etc/shadow /etc/shadow.backup.$(date +%Y%m%d)

# En cas de corruption, restaurer
cp /etc/shadow- /etc/shadow  # Backup automatique (shadow-)
```

### Erreur 6 : Confondre groupe primaire et groupes supplémentaires

**Problème :**
```bash
# Alice est dans le groupe developers
usermod -g developers alice

# Mais getent group developers ne montre PAS alice
getent group developers
# developers:x:2000:bob,charlie

# Pourquoi ? Parce que developers est son groupe PRIMAIRE
getent passwd alice
# alice:x:1001:2000:...
#                ^ GID 2000 = developers
```

**Explication :**
- **Groupe primaire** : défini dans `/etc/passwd` (champ 4), modifié avec `-g`
- **Groupes supplémentaires** : définis dans `/etc/group`, modifiés avec `-G` ou `-aG`
- Les fichiers créés appartiennent au groupe primaire par défaut

**Solution :**
```bash
# Voir TOUS les groupes (primaire + supplémentaires)
id alice
groups alice

# Voir seulement le groupe primaire
id -gn alice

# Voir seulement les groupes supplémentaires
id -Gn alice | tr ' ' '\n' | tail -n +2
```

### Erreur 7 : Politique de mot de passe incohérente

**Problème :**
```bash
# Définir un âge minimum supérieur à l'âge maximum
chage -m 30 -M 7 alice
# Minimum 30 jours, Maximum 7 jours → IMPOSSIBLE de changer le mot de passe !
```

**Conséquence :** L'utilisateur est bloqué après expiration du mot de passe.

**Solution :**
```bash
# TOUJOURS : minimum < maximum
chage -m 1 -M 90 -W 14 -I 30 alice
# Minimum : 1 jour
# Maximum : 90 jours
# Warning : 14 jours avant expiration
# Inactive : 30 jours après expiration avant lock

# Vérifier la cohérence
chage -l alice
```

### Erreur 8 : Supprimer un groupe utilisé comme groupe primaire

**Problème :**
```bash
# alice a developers comme groupe primaire (GID 2000)
getent passwd alice
# alice:x:1001:2000:...

# Tentative de suppression du groupe
groupdel developers
# groupdel: cannot remove the primary group of user 'alice'
```

**Solution :**
```bash
# 1. Trouver les utilisateurs utilisant ce groupe comme primaire
getent passwd | awk -F: '$4 == 2000 {print $1}'

# 2. Changer leur groupe primaire
for user in $(getent passwd | awk -F: '$4 == 2000 {print $1}'); do
    usermod -g users $user
done

# 3. Maintenant on peut supprimer le groupe
groupdel developers
```

### Erreur 9 : Oublier de forcer le changement de mot de passe

**Problème :**
```bash
# Admin crée un compte avec mot de passe temporaire
useradd -m alice
passwd alice
# Entre : TemporaryPassword123

# Alice se connecte avec ce mot de passe
# Et ne le change jamais !
```

**Solution :**
```bash
# TOUJOURS forcer le changement au 1er login
useradd -m alice
passwd alice
chage -d 0 alice

# Ou en une commande
passwd -e alice

# Vérification
passwd -S alice
chage -l alice | grep "Last password change"
# Last password change                                    : password must be changed
```

### Erreur 10 : Droits insuffisants sur le répertoire personnel

**Problème :**
```bash
# Création manuelle du home avec mauvais propriétaire
mkdir /home/alice
cp -r /etc/skel/. /home/alice/
# Oublie de changer le propriétaire !

# Alice ne peut pas écrire dans son propre home
ls -ld /home/alice
# drwxr-xr-x 3 root root 4096 Feb 10 12:00 /home/alice
```

**Conséquence :** Alice ne peut pas créer de fichiers, modifier sa config, SSH refuse les clés, etc.

**Solution :**
```bash
# Corriger les permissions
chown -R alice:alice /home/alice
chmod 700 /home/alice

# Vérification
ls -ld /home/alice
# drwx------ 15 alice alice 4096 Feb 10 12:00 /home/alice

# Permissions recommandées
# Home dir : 700 (drwx------)
# Fichiers perso : 600 (-rw-------)
# .ssh/ : 700
# .ssh/authorized_keys : 600
```

---

## 📝 Cheat Sheet - Gestion Utilisateurs/Groupes

### Commandes Utilisateurs

```bash
# CRÉATION
useradd -m -s /bin/bash -c "Nom Complet" username
useradd -m -g groupe_primaire -G groupe1,groupe2 username
useradd -m -e 2026-12-31 username                    # Compte temporaire
useradd -r username                                   # Compte système (UID < 1000)

# MODIFICATION
usermod -l nouveau_nom ancien_nom                     # Renommer
usermod -d /nouveau/home -m username                  # Déplacer home
usermod -s /bin/zsh username                          # Changer shell
usermod -g nouveau_groupe_primaire username           # Changer groupe primaire
usermod -G groupe1,groupe2 username                   # REMPLACER groupes secondaires
usermod -aG groupe1,groupe2 username                  # AJOUTER groupes secondaires
usermod -L username                                   # VERROUILLER (lock)
usermod -U username                                   # DÉVERROUILLER (unlock)
usermod -e 2026-12-31 username                        # Définir expiration

# SUPPRESSION
userdel username                                      # Supprime le compte seulement
userdel -r username                                   # Supprime compte + home + mail
userdel -f username                                   # Force (même si connecté)

# MOT DE PASSE
passwd username                                       # Changer mot de passe
passwd -l username                                    # Verrouiller
passwd -u username                                    # Déverrouiller
passwd -e username                                    # Expirer (force changement)
passwd -S username                                    # Afficher le statut
echo "user:password" | chpasswd                       # Changer en batch

# AGING (EXPIRATION MOT DE PASSE)
chage -l username                                     # Afficher infos
chage -d 0 username                                   # Force changement au login
chage -M 90 username                                  # Max 90 jours
chage -m 7 username                                   # Min 7 jours
chage -W 14 username                                  # Warning 14 jours avant
chage -I 30 username                                  # Inactive 30 jours après expiration
chage -E 2026-12-31 username                          # Expiration compte
chage -E -1 username                                  # Jamais expirer
chage username                                        # Mode interactif
```

### Commandes Groupes

```bash
# CRÉATION
groupadd nom_groupe
groupadd -g 3000 nom_groupe                           # Avec GID spécifique
groupadd -r nom_groupe                                # Groupe système (GID < 1000)

# MODIFICATION
groupmod -n nouveau_nom ancien_nom                    # Renommer
groupmod -g 3500 nom_groupe                           # Changer GID

# SUPPRESSION
groupdel nom_groupe                                   # Supprimer groupe

# GESTION MEMBRES (gpasswd)
gpasswd -a username groupe                            # Ajouter un membre
gpasswd -d username groupe                            # Retirer un membre
gpasswd -M user1,user2,user3 groupe                   # Définir les membres
gpasswd -A admin1,admin2 groupe                       # Définir les admins
gpasswd groupe                                        # Définir mot de passe de groupe
```

### Consultation

```bash
# INFORMATIONS UTILISATEUR
id                                                    # UID, GID, groupes (moi)
id username                                           # UID, GID, groupes (autre)
id -u username                                        # UID seulement
id -g username                                        # GID seulement (groupe primaire)
id -G username                                        # Tous les GID
id -Gn username                                       # Tous les groupes (noms)
groups username                                       # Groupes de l'utilisateur
whoami                                                # Mon nom d'utilisateur
who                                                   # Utilisateurs connectés
w                                                     # Utilisateurs + charge système
last                                                  # Historique connexions
lastlog                                               # Dernière connexion par user

# CONSULTATION BASES DE DONNÉES
getent passwd                                         # Tous les utilisateurs
getent passwd username                                # Un utilisateur
getent passwd 1001                                    # Par UID
getent group                                          # Tous les groupes
getent group groupname                                # Un groupe
getent group 2000                                     # Par GID
getent shadow username                                # Shadow (root only)

# HISTORIQUE CONNEXIONS
lastlog                                               # Dernière connexion (tous users)
lastlog -u alice                                      # Dernière connexion (alice)
lastlog -t 7                                          # Connectés derniers 7 jours
lastlog -b 90                                         # Pas connectés depuis 90 jours
last                                                  # Historique complet connexions
last -10                                              # 10 dernières connexions
last alice                                            # Historique d'alice
last -F                                               # Format date complet
last -i                                               # Afficher IPs
last reboot                                           # Historique reboots

# CHANGER INFORMATIONS UTILISATEUR
chsh                                                  # Changer son shell (interactif)
chsh -s /bin/zsh alice                                # Changer shell d'alice
chsh -l                                               # Lister shells disponibles
chfn                                                  # Changer ses infos GECOS (interactif)
chfn -f "Alice Dupont" alice                          # Changer nom complet
chfn -r "Bureau 205" alice                            # Changer bureau
chfn -w "01.23.45.67.89" alice                        # Changer tél bureau

# CHANGEMENT DE GROUPE TEMPORAIRE
newgrp developers                                     # Changer groupe primaire (session)
sg developers -c "touch file.txt"                     # Exécuter commande avec autre groupe

# FICHIERS
cat /etc/passwd                                       # Liste utilisateurs
cat /etc/shadow                                       # Mots de passe (root)
cat /etc/group                                        # Liste groupes
cat /etc/gshadow                                      # Mots de passe groupes (root)
cat /etc/default/useradd                              # Defaults useradd
cat /etc/login.defs                                   # Politiques globales
ls -la /etc/skel/                                     # Squelette nouveaux users
```

### Filtres et Recherches Utiles

```bash
# Utilisateurs humains (UID >= 1000)
getent passwd | awk -F: '$3 >= 1000 && $3 < 65534 {print $1}'

# Utilisateurs système (UID < 1000)
getent passwd | awk -F: '$3 < 1000 {print $1":"$3}'

# Utilisateurs avec shell actif (pas nologin)
getent passwd | awk -F: '$7 !~ /nologin|false/ {print $1":"$7}'

# Membres d'un groupe
getent group sudo | cut -d: -f4 | tr ',' '\n'

# Trouver le groupe primaire d'un user
id -gn username

# Trouver tous les groupes d'un user
id -Gn username | tr ' ' '\n'

# Comptes verrouillés
passwd -S --all | grep " L "

# Comptes sans mot de passe
sudo awk -F: '$2 == "" {print $1}' /etc/shadow

# Comptes expirant bientôt
for u in $(getent passwd | awk -F: '$3>=1000 && $3<65534 {print $1}'); do chage -l $u 2>/dev/null | grep -v never | grep "Account expires"; done

# Fichiers orphelins (utilisateur supprimé)
find / -nouser -ls 2>/dev/null

# Processus d'un utilisateur
ps -u username
pgrep -u username
```

### Configuration et Politiques

```bash
# Afficher les defaults useradd
useradd -D

# Modifier les defaults
useradd -D -s /bin/zsh                                # Shell par défaut
useradd -D -b /home/users                             # Base directory
useradd -D -e 2026-12-31                              # Expiration par défaut

# Édition sécurisée des fichiers système
vipw                                                  # Éditer /etc/passwd
vipw -s                                               # Éditer /etc/shadow
vipw -g                                               # Éditer /etc/group
vigr                                                  # Éditer /etc/group
vigr -s                                               # Éditer /etc/gshadow

# Vérification intégrité fichiers
pwck                                                  # Vérifier passwd + shadow
pwck -r                                               # Mode read-only (audit)
grpck                                                 # Vérifier group + gshadow
grpck -r                                              # Mode read-only (audit)

# Interroger login.defs
getconf LOGIN_NAME_MAX                                # Longueur max login
getconf PATH_MAX /                                    # Taille max chemin
getconf -a                                            # Toutes variables

# Politique de mot de passe globale (/etc/login.defs)
grep -E "PASS_MAX_DAYS|PASS_MIN_DAYS|PASS_WARN_AGE" /etc/login.defs

# PAM - Pluggable Authentication Modules
cat /etc/pam.d/login                                  # Config PAM login console
cat /etc/pam.d/sshd                                   # Config PAM SSH
cat /etc/pam.d/common-auth                            # Authentification commune
cat /etc/pam.d/common-password                        # Politique mdp commune
cat /etc/security/pwquality.conf                      # Config pwquality (complexité)
cat /etc/security/limits.conf                         # Limites ressources

# Messages connexion
cat /etc/issue                                        # Message pré-login local
cat /etc/issue.net                                    # Message pré-login SSH
cat /etc/motd                                         # Message post-login
```

### One-Liners Utiles

```bash
# Créer user complet en une ligne
useradd -m -s /bin/bash -c "Alice Dupont" -G sudo,docker alice && passwd alice && chage -d 0 alice

# Lister tous les sudoers
getent group sudo | cut -d: -f4 | tr ',' '\n'

# Compter les utilisateurs humains
getent passwd | awk -F: '$3 >= 1000 && $3 < 65534' | wc -l

# Verrouiller tous les comptes sauf root et soi-même
for user in $(getent passwd | awk -F: '$3 >= 1000 && $3 < 65534 {print $1}'); do [ "$user" != "$(whoami)" ] && usermod -L $user; done

# Trouver les utilisateurs avec UID dupliqué
cut -d: -f3 /etc/passwd | sort | uniq -d

# Trouver les groupes vides
getent group | awk -F: 'NF==3 || $4=="" {print $1":"$3}'

# Expirer les mots de passe de tous les users
for user in $(getent passwd | awk -F: '$3>=1000 && $3<65534 {print $1}'); do chage -d 0 $user; done
```

---

## 🎓 Points Clés pour l'Examen LPIC-1

### 1. Commandes à Connaître Absolument

✅ **Gestion utilisateurs :** `useradd`, `usermod`, `userdel`  
✅ **Gestion groupes :** `groupadd`, `groupmod`, `groupdel`, `gpasswd`  
✅ **Mots de passe :** `passwd`, `chage`  
✅ **Consultation :** `id`, `getent`, `groups`, `who`, `w`, `last`  

### 2. Fichiers de Configuration Critiques

📁 `/etc/passwd` - Base utilisateurs (7 champs: user:x:UID:GID:GECOS:home:shell)  
📁 `/etc/shadow` - Mots de passe (9 champs: user:hash:last:min:max:warn:inactive:expire:reserved)  
📁 `/etc/group` - Groupes (4 champs: group:x:GID:members)  
📁 `/etc/gshadow` - Mots de passe groupes  
📁 `/etc/default/useradd` - Defaults useradd  
📁 `/etc/login.defs` - Politiques globales (UID_MIN, PASS_MAX_DAYS, etc.)  
📁 `/etc/skel/` - Squelette pour nouveaux utilisateurs  

### 3. Différences Critiques à Maîtriser

🔑 **`usermod -G` vs `usermod -aG` :**
- `-G` → REMPLACE tous les groupes secondaires
- `-aG` → AJOUTE aux groupes existants

🔑 **`userdel` vs `userdel -r` :**
- Sans `-r` → Garde le home et le mail spool
- Avec `-r` → Supprime TOUT (home + mail + contenu)

🔑 **Groupe primaire vs groupes secondaires :**
- Primaire : défini dans `/etc/passwd` (champ 4), modifié avec `-g`
- Secondaires : définis dans `/etc/group`, modifiés avec `-G` ou `-aG`

🔑 **`usermod -L` vs `passwd -l` :**
- Les deux verrouillent le compte (ajoute `!` dans /etc/shadow)
- `usermod -U` ou `passwd -u` pour déverrouiller

### 4. Options de `chage` à Retenir

| Option | Signification | Exemple |
|--------|---------------|---------|
| `-d 0` | Force changement au login | `chage -d 0 user` |
| `-M 90` | Max 90 jours avant expiration | `chage -M 90 user` |
| `-E YYYY-MM-DD` | Date d'expiration du compte | `chage -E 2026-12-31 user` |
| `-l` | Lister les informations | `chage -l user` |

### 5. États du Mot de Passe dans `/etc/shadow`

| Valeur | Signification |
|--------|---------------|
| `$6$xyz...` | Hash SHA-512 valide → Compte actif |
| `!$6$xyz...` | `!` devant le hash → Compte **verrouillé** |
| `!!` | Aucun mot de passe défini (RedHat) → Jamais utilisé |
| `*` | Compte désactivé (Debian/Ubuntu) → Connexion impossible |

### 6. Root et les Politiques de Mot de Passe

⚠️ **CRUCIAL :** Les restrictions de politique de mot de passe (longueur, complexité, PAM) sont appliquées par `passwd` **SAUF si c'est root qui exécute la commande**.

```bash
# En tant qu'utilisateur normal
passwd              # Politique APPLIQUÉE

# En tant que root
passwd alice        # Politique IGNORÉE (root peut tout)
```

### 7. Commande `getent` vs Fichiers Locaux

🌐 **`getent`** interroge NSS (Name Service Switch) = fichiers locaux + LDAP + AD + NIS
```bash
getent passwd alice     # Cherche partout (local + LDAP + etc.)
grep alice /etc/passwd  # Cherche UNIQUEMENT dans le fichier local
```

### 8. Scénarios d'Examen Typiques

📝 Créer un utilisateur avec expiration et groupe spécifique  
📝 Modifier un utilisateur (changer shell, ajouter aux groupes)  
📝 Verrouiller/déverrouiller un compte  
📝 Forcer le changement de mot de passe au prochain login  
📝 Interpréter une ligne de `/etc/passwd` et `/etc/shadow`  
📝 Trouver les membres d'un groupe  
📝 Supprimer proprement un utilisateur avec son home  

### 9. Pièges Classiques d'Examen

❌ Confondre `-G` et `-aG` → Perte de groupes  
❌ Utiliser `userdel` sans `-r` → Fichiers orphelins  
❌ Verrouiller root avec `usermod -L root` → Perte totale d'accès  
❌ Créer un utilisateur sans `-m` → Pas de home  
❌ Politique incohérente : min > max dans chage  
❌ Oublier de forcer le changement de mdp (`chage -d 0`)  

### 10. Commandes de Vérification Essentielles

```bash
# Vérifier l'identité et les groupes
id username

# Vérifier le statut du mot de passe
passwd -S username

# Vérifier la politique d'expiration
chage -l username

# Vérifier l'existence d'un utilisateur
getent passwd username

# Vérifier les membres d'un groupe
getent group groupname | cut -d: -f4
```

---

## 📌 Résumé des Découvertes du Cours

### 🆕 Découverte 1 : Root ignore les politiques de mot de passe

Lorsque **root** exécute `passwd username`, les restrictions PAM (longueur, complexité, historique) sont **ignorées**. Root peut définir n'importe quel mot de passe, même `123`.

**Impact :** Responsabilité accrue pour l'administrateur de respecter les bonnes pratiques.

### 🆕 Découverte 2 : Options `-L` / `-U` de `usermod`

- `usermod -L username` → **Verrouille** le compte (ajoute `!` devant le hash dans /etc/shadow)
- `usermod -U username` → **Déverrouille** le compte (retire le `!`)

Alternative équivalente : `passwd -l` et `passwd -u`

### 🆕 Découverte 3 : Option `-r` de `userdel`

`userdel -r username` supprime :
- Le compte utilisateur
- Le répertoire personnel (`/home/username`)
- Le contenu du spool mail (`/var/mail/username`)

Sans `-r`, ces fichiers restent orphelins.

### 🆕 Découverte 4 : Commande `chage` et mode interactif

```bash
# Mode interactif : pose des questions
chage username

# Affichage des informations
chage -l username

# Force le changement au login
chage -d 0 username
```

### 🆕 Découverte 5 : Signification de `!!` et `!` dans `/etc/shadow`

| Symbole | Signification |
|---------|---------------|
| `!!` | Pas de mot de passe défini, compte jamais utilisé (RedHat) |
| `!` | Compte verrouillé (hash précédé de `!`) |
| `*` | Compte désactivé (Debian/Ubuntu) |

### 🆕 Découverte 6 : Commandes `id` et `getent`

**`id`** : Affiche UID, GID, et tous les groupes (primaire + secondaires)
```bash
id alice
# uid=1001(alice) gid=1001(alice) groups=1001(alice),27(sudo),999(docker)
```

**`getent`** : Interroge NSS (fichiers locaux + LDAP + AD + etc.)
```bash
getent passwd alice     # Cherche dans toutes les sources
getent group sudo       # Affiche le groupe sudo
```

### 🆕 Découverte 7 : `pwck` et `grpck` - Vérification d'intégrité

**`pwck` et `grpck` détectent les erreurs dans les fichiers de comptes :**
- UIDs/GIDs en double
- Membres inexistants dans les groupes
- Répertoires personnels manquants
- Entrées orphelines dans shadow/gshadow

```bash
pwck -r    # Audit passwd/shadow (read-only)
grpck -r   # Audit group/gshadow (read-only)
```

**⚠️ Toujours utiliser `-r` pour audit avant correction !**

### 🆕 Découverte 8 : `lastlog` vs `last`

| Commande | Source | Contenu |
|----------|--------|---------|
| `lastlog` | `/var/log/lastlog` | **Dernière** connexion par utilisateur |
| `last` | `/var/log/wtmp` | **Historique complet** des connexions |

```bash
lastlog -u alice       # Dernière connexion d'Alice
lastlog -t 7           # Users connectés derniers 7 jours
last -10 alice         # 10 dernières connexions d'Alice
last reboot            # Historique des reboots
```

### 🆕 Découverte 9 : `chsh` et `chfn` - Modification par utilisateur

**Contrairement à `usermod`, les utilisateurs normaux peuvent utiliser `chsh` et `chfn` pour modifier leurs propres informations :**

```bash
# Utilisateur change son shell (doit être dans /etc/shells)
chsh -s /bin/zsh

# Utilisateur change ses infos GECOS
chfn -f "Alice Dupont" -r "Bureau 205"
```

**`usermod`** nécessite root, **`chsh`/`chfn`** peuvent être utilisés par l'utilisateur lui-même.

### 🆕 Découverte 10 : `newgrp` vs `sg`

| Commande | Usage | Durée |
|----------|-------|-------|
| `newgrp groupe` | Crée un sous-shell avec nouveau groupe primaire | Jusqu'à `exit` |
| `sg groupe -c "cmd"` | Exécute UNE commande avec groupe différent | Commande unique |

```bash
# Session prolongée
newgrp developers
touch file1 file2 file3   # Tous appartiennent au groupe developers
exit                      # Retour au groupe original

# Commande ponctuelle
sg developers -c "touch hotfix.txt"   # Seulement ce fichier
```

### 🆕 Découverte 11 : `vipw` et `vigr` - À utiliser avec précaution

**⚠️ `vipw`/`vigr` NE font QUE éditer les fichiers. Ils ne :**
- Ne créent PAS les répertoires personnels
- Ne mettent PAS à jour les appartenances
- Ne génèrent PAS les spools mail

**→ Utiliser TOUJOURS `useradd`/`usermod` sauf urgence système !**

### 🆕 Découverte 12 : PAM - Framework d'authentification modulaire

**PAM sépare l'authentification de l'application :**
- **4 types** : `auth` (authentification), `account` (autorisation), `password` (politique), `session` (env)
- **4 contrôles** : `required`, `requisite`, `sufficient`, `optional`
- **Configuration** : `/etc/pam.d/`

```bash
# Voir config PAM SSH
cat /etc/pam.d/sshd

# Politique de complexité mot de passe
cat /etc/security/pwquality.conf
```

**Module clé :** `pam_pwquality.so` (complexité du mot de passe)

### 🆕 Découverte 13 : Messages de connexion

| Fichier | Quand | Type |
|---------|-------|------|
| `/etc/issue` | **AVANT** login | Console locale |
| `/etc/issue.net` | **AVANT** login | SSH (si `Banner` activé) |
| `/etc/motd` | **APRÈS** login | Toutes connexions |

**Codes échappement (`\n` = hostname, `\d` = date) fonctionnent UNIQUEMENT dans `/etc/issue`, pas dans `/etc/issue.net`.**

### 🆕 Découverte 14 : `/etc/shadow` - Calcul des dates

**Les champs 3 et 8 utilisent le nombre de jours depuis le 1er janvier 1970 (Epoch) :**

```bash
# Convertir date → jours depuis Epoch
echo $(( ( $(date -d "2026-12-31" +%s) - $(date -d "1970-01-01" +%s) ) / 86400 ))
# Résultat : 20818

# Convertir jours depuis Epoch → date
date -d "1970-01-01 + 19750 days" "+%Y-%m-%d"
# Résultat : 2024-02-15
```

**Champ 3 = 0** → Force changement au prochain login

### 🆕 Découverte 15 : `passwd` vs `chage` pour l'aging

**`passwd` peut aussi gérer l'aging (pas seulement les mots de passe) :**

```bash
passwd -n 7 -x 90 -w 14 -i 30 alice
# Équivalent à :
chage -m 7 -M 90 -W 14 -I 30 alice
```

**Mais `chage` offre plus d'options (dates d'expiration compte, mode interactif).**

### 🆕 Découverte 16 : `/etc/login.defs` - Directives étendues

**Au-delà des UID/GID et politiques de mot de passe, `/etc/login.defs` contrôle :**
- `LOGIN_RETRIES` : Nombre max tentatives login (défaut: 3)
- `FAIL_DELAY` : Délai après échec (ralentit brute-force)
- `DEFAULT_HOME` : Autoriser login sans home
- `ENV_PATH` / `ENV_ROOTPATH` : PATH par défaut
- `USERGROUPS_ENAB` : Créer groupe privé par utilisateur

**Interroger avec `getconf` :**
```bash
getconf LOGIN_NAME_MAX   # 256
```

---

**Formation LPIC-1 102 - Préparation Certification Linux**  
**Cours 107.1 - Gestion des Utilisateurs et Groupes - Version Enrichie**  
**Maxime Chenaud - Février 2026**
