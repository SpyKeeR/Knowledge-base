# 110.2 - Setup Host Security 🔒🛡️

## 📋 Informations du Module

- **Poids à l'examen** : 3 points
- **Objectifs clés** :
  - Comprendre les fichiers `/etc/passwd` et `/etc/shadow` 📄
  - Configurer shells non-interactifs pour bloquer connexions
  - Utiliser `/etc/nologin` pour mode maintenance système
  - Maîtriser les super-servers (xinetd) et TCP wrappers
  - Configurer systemd socket activation
  - Gérer `/etc/hosts.allow` et `/etc/hosts.deny` (contrôle d'accès)
  - Minimiser services actifs pour renforcer sécurité
  - Gérer services SysVinit et systemd

---

## 🔐 Partie 1 : Fichiers Système d'Authentification

### 📄 Fichier `/etc/passwd`

#### Description

**Base de données utilisateurs** du système, lisible par tous.

**Historique** :
- 🕰️ **Anciennement** : Contenait mots de passe hashés (lisibles par tous ⚠️)
- 🔒 **Actuellement** : Mots de passe déplacés vers `/etc/shadow` (lisible root uniquement)

#### Format du Fichier

**Structure ligne** : 7 champs séparés par `:`

```
username:x:UID:GID:GECOS:home_directory:shell
```

**Exemple** :

```bash
$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
alice:x:1000:1000:Alice Dupont,,,:/home/alice:/bin/bash
bob:x:1001:1001:Bob Martin:/home/bob:/bin/bash
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
mysql:x:104:108:MySQL Server,,,:/nonexistent:/bin/false
```

#### Détail des Champs

| Champ | Position | Description | Exemple |
|-------|----------|-------------|---------|
| **Username** | 1 | Nom de connexion utilisateur | `alice` |
| **Password** | 2 | `x` = Mot de passe dans `/etc/shadow` | `x` |
| **UID** | 3 | User ID (0 = root, 1000+ = utilisateurs normaux) | `1000` |
| **GID** | 4 | Group ID principal | `1000` |
| **GECOS** | 5 | Informations utilisateur (nom complet, bureau, tél.) | `Alice Dupont,,,` |
| **Home** | 6 | Répertoire personnel | `/home/alice` |
| **Shell** | 7 | Shell de connexion | `/bin/bash` |

**Exemples détaillés** :

```bash
# Utilisateur root
root:x:0:0:root:/root:/bin/bash
│    │ │ │  │    │     └─ Shell : bash
│    │ │ │  │    └─ Home : /root
│    │ │ │  └─ GECOS : root
│    │ │ └─ GID : 0 (groupe root)
│    │ └─ UID : 0 (superutilisateur)
│    └─ Password : x (dans /etc/shadow)
└─ Username : root

# Utilisateur normal
alice:x:1000:1000:Alice Dupont,Bureau 205,555-1234:/home/alice:/bin/bash
│     │  │    │    │                                 │           └─ Shell interactif
│     │  │    │    │                                 └─ Home personnel
│     │  │    │    └─ GECOS : Nom, bureau, téléphone
│     │  │    └─ GID : 1000 (groupe alice)
│     │  └─ UID : 1000 (premier utilisateur normal)
│     └─ Password dans shadow
└─ Username : alice

# Service système (pas de connexion)
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
│        │ │  │   │        │        └─ Pas de shell (connexion refusée)
│        │ │  │   │        └─ Home : répertoire web
│        │ │  │   └─ GECOS : nom service
│        │ │  └─ GID : 33
│        │ └─ UID : 33 (système)
│        └─ Password inutilisé
└─ Service web
```

**Permissions** :

```bash
$ ls -l /etc/passwd
-rw-r--r-- 1 root root 2145 Feb 12 10:00 /etc/passwd
#  └─ Lisible par tous (r--r--r--)
```

⚠️ **Important** : Tout utilisateur peut lire `/etc/passwd` → C'est pourquoi mots de passe sont dans `/etc/shadow` !

---

### 🔒 Fichier `/etc/shadow`

#### Description

**Base de données mots de passe** sécurisée, **lisible root uniquement**.

**Rôle** :
- ✅ Stocker mots de passe hashés
- ✅ Gérer politique expiration mots de passe
- ✅ Protéger contre lecture non autorisée

#### Format du Fichier

**Structure ligne** : 9 champs séparés par `:`

```
username:password_hash:last_change:min:max:warn:inactive:expire:reserved
```

**Exemple** :

```bash
$ sudo cat /etc/shadow
root:$6$abcd1234$hashed_password_very_long_string...:19751:0:99999:7:::
alice:$6$xyz789$another_hashed_password_string...:19751:7:90:14:30:19900:
bob:!:19751:0:99999:7:::
www-data:*:19000:0:99999:7:::
charlie:!!:19751:0:99999:7:::
```

#### Détail des Champs

| Champ | Position | Description | Exemple |
|-------|----------|-------------|---------|
| **Username** | 1 | Nom utilisateur (correspond à `/etc/passwd`) | `alice` |
| **Password** | 2 | Hash du mot de passe (salé) | `$6$salt$hash...` |
| **Last change** | 3 | Dernière modif mdp (jours depuis 1/1/1970) | `19751` |
| **Min** | 4 | Jours minimum avant changement mdp | `7` |
| **Max** | 5 | Jours maximum validité mdp | `90` |
| **Warn** | 6 | Jours d'avertissement avant expiration | `14` |
| **Inactive** | 7 | Jours d'inactivité tolérés après expiration | `30` |
| **Expire** | 8 | Date expiration compte (jours depuis 1/1/1970) | `19900` |
| **Reserved** | 9 | Réservé (inutilisé) | (vide) |

**Exemples détaillés** :

```bash
# Compte actif avec mdp
alice:$6$xyz789$hash...:19751:7:90:14:30:19900:
│     │                 │     │ │  │  │  │
│     │                 │     │ │  │  │  └─ Expire : 19900 (31 déc 2024)
│     │                 │     │ │  │  └─ Inactive : 30 jours après expiration mdp
│     │                 │     │ │  └─ Warn : Avertissement 14 jours avant
│     │                 │     │ └─ Max : Mdp expire dans 90 jours
│     │                 │     └─ Min : Attendre 7 jours avant changement
│     │                 └─ Last change : 12 fév 2026 (19751 jours depuis 1/1/1970)
│     └─ Password : Hash SHA-512 salé
└─ Username : alice

# Compte verrouillé (!)
bob:!:19751:0:99999:7:::
    └─ ! = Compte verrouillé (connexion impossible)

# Service système (*)
www-data:*:19000:0:99999:7:::
         └─ * = Pas de mot de passe (connexion impossible)

# Nouveau compte sans mdp défini (!!)
charlie:!!:19751:0:99999:7:::
        └─ !! = Mdp non défini (compte créé mais mdp inexistant)
```

---

#### Format du Hash de Mot de Passe

**Structure** : `$id$salt$hash`

**Exemples** :

```bash
$6$rounds=5000$abcd1234$hashed_password_very_long_string...
│ │            │        └─ Hash résultant
│ │            └─ Salt (sel aléatoire)
│ └─ Rounds : Nombre d'itérations (optionnel SHA-512)
└─ ID algorithme : 6 = SHA-512
```

**Algorithmes** :

| ID | Algorithme | Sécurité | Longueur Hash | Exemple |
|----|------------|----------|---------------|---------|
| `1` | MD5 | ❌ Obsolète | 22 chars | `$1$salt$hash` |
| `2a` | Blowfish (bcrypt) | ✅ Bon | 53 chars | `$2a$rounds$salt$hash` |
| `5` | SHA-256 | ✅ Bon | 43 chars | `$5$salt$hash` |
| `6` | SHA-512 | ✅✅ Meilleur | 86 chars | `$6$salt$hash` |
| `y` | yescrypt | ✅✅✅ Moderne | Variable | `$y$params$salt$hash` |

**Vérifier algorithme utilisé** :

```bash
$ sudo grep "^alice:" /etc/shadow | cut -d: -f2
$6$xyz789$long_hash_string...
└─ $6 = SHA-512 (bon)
```

**Statuts spéciaux du champ password** :

| Valeur | Signification | Connexion possible ? |
|--------|---------------|----------------------|
| `$6$...` | Hash SHA-512 valide | ✅ Oui (avec bon mdp) |
| `!` | Compte verrouillé (`passwd -l`) | ❌ Non |
| `!!` | Mdp jamais défini (compte nouveau) | ❌ Non |
| `*` | Service système (pas de mdp) | ❌ Non |
| `!$6$...` | Verrouillé (! devant hash) | ❌ Non |
| (vide) | Pas de mdp requis (DANGEREUX) | ✅ Oui (sans mdp !) |

---

#### Salage des Mots de Passe (Salt)

**Historique** :
- 🕰️ **Avant** : Hash sans sel → Mêmes mdp = Mêmes hash (attaque rainbow tables)
- 🔒 **Maintenant** : Hash avec sel → Mêmes mdp = Hash **différents** ✅

**Exemple sans sel** (vulnérable) :

```
alice:password123 → MD5(password123) = 482c811da5d5b4bc6d497ffa98491e38
bob:password123   → MD5(password123) = 482c811da5d5b4bc6d497ffa98491e38
                                        └─ ❌ Même hash ! Vulnérable !
```

**Exemple avec sel** (sécurisé) :

```
alice:password123 → $6$abcd1234$hash_A...
bob:password123   → $6$xyz789$hash_B...
                     └─ Sels différents → Hash différents ✅
```

**Avantages** :
- ✅ Mêmes mots de passe → Hash différents
- ✅ Impossible précalculer rainbow tables
- ✅ Force attaque brute-force pour chaque utilisateur

**Processus de hashing** :

```
┌──────────────┐   ┌──────────────┐
│ Mot de passe │   │  Salt (sel)  │
│  "password"  │   │  "abcd1234"  │
└──────┬───────┘   └──────┬───────┘
       │                  │
       └────────┬─────────┘
                │
                ▼
       ┌────────────────┐
       │   SHA-512      │
       │  (5000 rounds) │
       └────────┬───────┘
                │
                ▼
       ┌────────────────┐
       │  Hash final    │
       │  $6$abcd1234$  │
       │  long_hash...  │
       └────────────────┘
```

---

#### Permissions `/etc/shadow`

```bash
$ ls -l /etc/shadow
-rw-r----- 1 root shadow 1245 Feb 12 10:00 /etc/shadow
#  └─ rw-r----- : Lecture root + groupe shadow uniquement
```

**Sécurité** :
- ✅ Lisible **uniquement par root** (et groupe shadow)
- ✅ Utilisateurs normaux **ne peuvent PAS** lire
- ✅ Protège hash contre attaques

**Vérifier permissions** (pour examen) :

```bash
# Correct
$ ls -l /etc/shadow
-rw-r----- 1 root shadow 1245 Feb 12 10:00 /etc/shadow

# ❌ DANGEREUX (trop permissif)
-rw-r--r-- 1 root root 1245 Feb 12 10:00 /etc/shadow
#     └─ r-- = Lisible par tous ! Vulnérabilité sécurité !

# Corriger permissions incorrectes
$ sudo chmod 640 /etc/shadow
$ sudo chown root:shadow /etc/shadow
```

---

### 🚫 Shells Non-Interactifs - Bloquer Connexions

#### Concept

**Rôle** : Empêcher utilisateur/service de se connecter de manière interactive.

**Cas d'usage** :
- ✅ Comptes services système (www-data, mysql, etc.)
- ✅ Comptes désactivés temporairement
- ✅ Comptes compromis (sécurité)

**Shells bloquants** :
- `/usr/sbin/nologin` : Affiche message puis ferme connexion
- `/bin/false` : Ferme connexion immédiatement (sans message)

---

#### `/usr/sbin/nologin`

**Rôle** : **Refuser connexion** avec message informatif.

**Fonctionnement** :
1. Utilisateur tente connexion (SSH, login, etc.)
2. Shell `/usr/sbin/nologin` est lancé
3. Message affiché : "This account is currently not available."
4. Connexion fermée (code retour 1)

**Configuration** :

```bash
# Définir nologin pour utilisateur
$ sudo usermod -s /usr/sbin/nologin bob

# Vérifier
$ grep bob /etc/passwd
bob:x:1001:1001:Bob Martin:/home/bob:/usr/sbin/nologin
#                                       └─ Shell bloquant
```

**Test connexion** :

```bash
# Tentative connexion SSH
$ ssh bob@localhost
bob@localhost's password: [password]
This account is currently not available.
Connection to localhost closed.
#  ^^^
#  Connexion refusée avec message
```

**Message personnalisé** :

Fichier `/etc/nologin.txt` (si créé) sera affiché au lieu du message par défaut.

```bash
# Créer message personnalisé
$ sudo nano /etc/nologin.txt
==============================================
  COMPTE DÉSACTIVÉ
  
  Votre compte a été temporairement suspendu.
  Contactez l'administrateur : admin@example.com
==============================================

# Tester
$ ssh bob@localhost
bob@localhost's password:
==============================================
  COMPTE DÉSACTIVÉ
  
  Votre compte a été temporairement suspendu.
  Contactez l'administrateur : admin@example.com
==============================================
Connection to localhost closed.
```

---

#### `/bin/false`

**Rôle** : **Refuser connexion** immédiatement **sans message**.

**Fonctionnement** :
1. Utilisateur tente connexion
2. Shell `/bin/false` est lancé
3. `/bin/false` retourne code erreur 1 et quitte immédiatement
4. Connexion fermée (aucun message)

**Configuration** :

```bash
# Définir /bin/false pour utilisateur
$ sudo usermod -s /bin/false charlie

# Vérifier
$ grep charlie /etc/passwd
charlie:x:1002:1002:Charlie:/home/charlie:/bin/false
#                                          └─ Shell /bin/false
```

**Test connexion** :

```bash
# Tentative connexion SSH
$ ssh charlie@localhost
charlie@localhost's password: [password]
Connection to localhost closed.
#  ^^^
#  Connexion fermée immédiatement, aucun message
```

**Différence `/bin/false` vs `/dev/null`** :

```bash
# /bin/false : Programme retournant toujours erreur
$ /bin/false
$ echo $?
1  # Code erreur

# /dev/null : Fichier spécial (pas un programme)
# ❌ Ne PAS utiliser /dev/null comme shell !
```

---

#### Comparaison Shells Bloquants

| Shell | Message affiché ? | Message personnalisable ? | Usage recommandé |
|-------|-------------------|---------------------------|------------------|
| `/usr/sbin/nologin` | ✅ Oui | ✅ Oui (`/etc/nologin.txt`) | Comptes utilisateurs désactivés |
| `/bin/false` | ❌ Non | ❌ Non | Services système |
| `/bin/true` | ❌ Non | ❌ Non | (rarement utilisé) |

**Exemples système réels** :

```bash
$ grep nologin /etc/passwd
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
sshd:x:105:65534::/run/sshd:/usr/sbin/nologin

$ grep "/bin/false" /etc/passwd
mysql:x:104:108:MySQL Server,,,:/nonexistent:/bin/false
postgresql:x:106:113:PostgreSQL,,,:/var/lib/postgresql:/bin/false
```

---

### 🛠️ Fichier `/etc/nologin` - Mode Maintenance

#### Description

**Rôle** : Bloquer **toutes connexions utilisateurs** (sauf root) pour maintenance système.

**Fonctionnement** :
- ✅ Si fichier `/etc/nologin` existe → Connexions utilisateurs **refusées**
- ✅ Root peut **toujours** se connecter
- ✅ Contenu du fichier affiché aux utilisateurs tentant connexion

**Différence avec `/usr/sbin/nologin`** :

| Fichier | Portée | Qui est bloqué ? |
|---------|--------|------------------|
| `/usr/sbin/nologin` | Shell utilisateur spécifique | 1 utilisateur |
| `/etc/nologin` | **Système entier** | **Tous utilisateurs** (sauf root) |

---

#### Créer Mode Maintenance

**Activer** :

```bash
# Créer fichier /etc/nologin
$ sudo touch /etc/nologin

# ✅ Maintenant : Utilisateurs normaux ne peuvent plus se connecter
# ✅ Root peut toujours se connecter
```

**Avec message personnalisé** :

```bash
$ sudo nano /etc/nologin
============================================================
        MAINTENANCE SYSTÈME EN COURS
        
Durée estimée : 2 heures (10h00 - 12h00)

Le système sera indisponible pendant cette période.
Merci de réessayer après 12h00.

Contact urgence : admin@example.com
============================================================

# Sauvegarder et quitter
```

---

#### Test Mode Maintenance

**Tentative connexion utilisateur normal** :

```bash
# Alice tente SSH
$ ssh alice@server.example.com
alice@server.example.com's password: [password]

============================================================
        MAINTENANCE SYSTÈME EN COURS
        
Durée estimée : 2 heures (10h00 - 12h00)

Le système sera indisponible pendant cette période.
Merci de réessayer après 12h00.

Contact urgence : admin@example.com
============================================================

Connection to server.example.com closed.
#  ^^^
#  Connexion refusée, message affiché
```

**Connexion root toujours possible** :

```bash
# Root peut se connecter
$ ssh root@server.example.com
root@server.example.com's password: [password]
root@server:~# 
# ✅ Root connecté sans problème
```

---

#### Désactiver Mode Maintenance

```bash
# Supprimer fichier /etc/nologin
$ sudo rm /etc/nologin

# ✅ Utilisateurs peuvent à nouveau se connecter
```

---

#### Scénarios d'Usage

**1. Maintenance planifiée** :

```bash
# 1. Prévenir utilisateurs (email, annonce intranet)
# 2. Activer mode maintenance
$ sudo nano /etc/nologin
MAINTENANCE PLANIFIÉE - 10h00 à 12h00
Merci de votre patience.

# 3. Effectuer maintenance
$ sudo apt upgrade -y
$ sudo reboot

# 4. Désactiver après maintenance
$ sudo rm /etc/nologin
```

**2. Incident sécurité** :

```bash
# Bloquer immédiatement utilisateurs
$ sudo nano /etc/nologin
INCIDENT DE SÉCURITÉ DÉTECTÉ

Toutes connexions temporairement suspendues.
Investigation en cours.
Contact : security@example.com

# ✅ Seul root peut investiguer
```

**3. Migration système** :

```bash
$ sudo nano /etc/nologin
MIGRATION EN COURS

Le système est en cours de migration vers nouveau serveur.
ETA : 1 heure
```

---

## 🌐 Partie 2 : Super-Servers et TCP Wrappers

### 🔌 Super-Servers - inetd et xinetd

#### Concept et Historique

**Problématique ancienne** :
- 🕰️ Serveurs avec **peu de ressources** (RAM, CPU limités)
- ❌ Lancer tous services en permanence = **Gaspillage ressources**
- ❌ Services peu utilisés monopolisent RAM inutilement

**Solution : Super-Server** (inetd, puis xinetd)

**Rôle** :
- ✅ **Intermédiaire** entre sockets réseau et services
- ✅ **Écoute** ports pour services inactifs
- ✅ **Démarre service** uniquement quand requête reçue
- ✅ **Met en tampon** requête en attendant démarrage service
- ✅ **Transfère connexion** au service démarré
- ✅ **Arrête service** après inactivité

**Schéma fonctionnement** :

```
Requête réseau (port 21 FTP)
        │
        ▼
┌───────────────────┐
│   xinetd écoute   │  ← Toujours actif, peu de ressources
│   tous les ports  │
└────────┬──────────┘
         │
         ├─ Port 21 (FTP) reçu
         │
         ▼
┌───────────────────┐
│  Démarre service  │  ← Service FTP lancé dynamiquement
│   vsftpd (FTP)    │
└────────┬──────────┘
         │
         ├─ Connexion transférée
         │
         ▼
┌───────────────────┐
│   Client FTP      │  ← Reçoit réponse
└───────────────────┘

(Après déconnexion)
         │
         ▼
┌───────────────────┐
│   vsftpd arrêté   │  ← Libère ressources
└───────────────────┘
```

---

#### Évolution : inetd → xinetd

**inetd** (Internet Daemon) :
- 🕰️ Super-server historique Unix/Linux
- ❌ Configuration basique `/etc/inetd.conf` (1 fichier)
- ❌ Peu de contrôles d'accès
- ❌ Logging limité

**xinetd** (eXtended Internet Daemon) :
- ✅ Version moderne et étendue
- ✅ Configuration modulaire `/etc/xinetd.d/` (1 fichier par service)
- ✅ Contrôle d'accès avancé (IP, plages horaires, etc.)
- ✅ Logging détaillé
- ✅ Limitation connexions simultanées
- ✅ Protection contre DoS

**Note moderne** : Aujourd'hui, **systemd** remplace souvent xinetd avec **socket activation**.

---

#### Fichier Configuration Général : /etc/xinetd.conf

**Rôle** : Configuration globale de xinetd (paramètres par défaut pour tous les services).

**Emplacement** : `/etc/xinetd.conf`

**Exemple** :

```bash
$ sudo cat /etc/xinetd.conf
defaults
{
    instances       = 60
    log_type        = SYSLOG authpriv
    log_on_success  = HOST PID
    log_on_failure  = HOST
    cps             = 25 30
    includedir      /etc/xinetd.d
}
```

**Directives principales** :

| Directive | Description | Exemple |
|-----------|-------------|---------||
| **instances** | Nombre maximal de processus du service actifs simultanément | `instances = 60` |
| **log_type** | Destination des logs | `SYSLOG authpriv` (→ `/var/log/secure`)<br>`FILE /var/log/xinetd` |
| **log_on_success** | Informations tracées si connexion réussit | `HOST PID` (hôte + PID processus serveur) |
| **log_on_failure** | Informations tracées si connexion échoue | `HOST` (hôte source) |
| **cps** | Connexions par seconde (anti-DoS) | `cps = 25 30` (max 25 conn/sec, attente 30s si dépassé) |
| **includedir** | Inclure options fichiers du répertoire | `includedir /etc/xinetd.d` |

**💡 log_type - Options** :
- **SYSLOG authpriv** : Messages gérés par syslog dans `/var/log/secure`
- **FILE /var/log/xinetd** : Messages écrits directement dans fichier spécifié

**💡 cps (Connexions Per Second)** :
- `cps = 25 30` : xinetd autorise max 25 connexions/seconde
- Si limite atteinte : xinetd attend 30 secondes avant d'autoriser à nouveau les connexions
- Protection contre attaques DoS

---

#### Configuration xinetd - Services

**Emplacement** : `/etc/xinetd.d/`

**Structure** : 1 fichier par service

**Exemple service FTP** (`/etc/xinetd.d/vsftpd`) :

```bash
$ sudo cat /etc/xinetd.d/vsftpd
service ftp
{
    disable         = no
    socket_type     = stream
    protocol        = tcp
    wait            = no
    user            = root
    server          = /usr/sbin/vsftpd
    server_args     = /etc/vsftpd.conf
    log_on_success  = PID HOST DURATION
    log_on_failure  = HOST
    only_from       = 192.168.1.0/24
    access_times    = 08:00-18:00
    per_source      = 5
    instances       = 10
}
```

---

#### Directives Configuration xinetd - Fichiers Service

**💡 Ligne commentaire `default`** : La première ligne en commentaire `# default: on` ou `# default: off` n'est **pas interprétée par xinetd** mais par **ntsysv** ou **chkconfig** pour déterminer si le service doit être actif par défaut.

| Directive | Description | Valeurs possibles | Exemple |
|-----------|-------------|-------------------|---------|
| **service** | Nom du service (correspond à `/etc/services`) | Nom service | `service telnet` |
| **disable** | Activer/désactiver service | `yes` / `no` | `disable = no` (actif) |
| **flags** | Attributs pour la connexion | `REUSE` (socket réutilisée) | `flags = REUSE` |
| **socket_type** | Type de socket | `stream` (TCP)<br>`dgram` (UDP)<br>`raw` (connexion IP directe) | `socket_type = stream` |
| **protocol** | Protocole réseau | `tcp` / `udp` | `protocol = tcp` |
| **wait** | Mode gestion connexions | `yes` (une seule connexion)<br>`no` (connexions multiples) | `wait = no` |
| **user** | Compte utilisateur pour lancer service | Nom utilisateur | `user = www-data` |
| **server** | Chemin de l'exécutable à lancer | Chemin absolu | `server = /usr/sbin/vsftpd` |
| **server_args** | Arguments serveur | Arguments ligne commande | `server_args = -d` |
| **log_on_success** | Événements tracés si connexion réussit | `PID`, `HOST`, `DURATION`, `USERID` | `log_on_success = PID HOST` |
| **log_on_failure** | Événements tracés si connexion échoue | `HOST`, `USERID`, `ATTEMPT` | `log_on_failure += USERID` (le `+=` ajoute aux options par défaut) |
| **only_from** | IP autorisées (whitelist) | IP, réseau CIDR, domaine | `only_from = 192.168.1.0/24` |
| **no_access** | IP bloquées (blacklist) | IP, réseau CIDR | `no_access = 10.0.0.5` |
| **access_times** | Plages horaires | `HH:MM-HH:MM` | `access_times = 08:00-18:00` |
| **per_source** | Connexions max par IP | Nombre | `per_source = 5` |
| **instances** | Connexions simultanées max | Nombre ou `UNLIMITED` | `instances = 50` |
| **cps** | Connexions par seconde (anti-DoS) | `nb_connexions délai` | `cps = 10 30` (10/sec, 30s pause si dépassé) |

**💡 Notes importantes** :
- **service** : Nom doit correspondre à un service défini dans `/etc/services`
- **flags REUSE** : Indique que la socket sera réutilisée (utile pour telnet)
- **socket_type raw** : Connexion directe IP (ni TCP ni UDP)
- **wait yes** : xinetd gère une seule connexion à la fois (services mono-thread)
- **wait no** : xinetd gère plusieurs connexions simultanées (services multi-thread)
- **log_on_failure +=** : Le `+=` indique qu'on **ajoute** l'option aux options par défaut (ex: USERID en plus de HOST)

---

#### Exemples Configuration xinetd

**1. Service Telnet (port 23)** :

```bash
$ sudo nano /etc/xinetd.d/telnet
service telnet
{
    disable         = no
    socket_type     = stream
    protocol        = tcp
    wait            = no
    user            = root
    server          = /usr/sbin/in.telnetd
    log_on_success  = PID HOST DURATION
    log_on_failure  = HOST
}
```

**2. Service TFTP (port 69 UDP)** :

```bash
$ sudo nano /etc/xinetd.d/tftp
service tftp
{
    disable         = no
    socket_type     = dgram
    protocol        = udp
    wait            = yes
    user            = nobody
    server          = /usr/sbin/in.tftpd
    server_args     = -s /tftpboot
    only_from       = 192.168.1.0/24
}
```

**3. Service SSH avec restrictions** :

```bash
service ssh
{
    disable         = no
    socket_type     = stream
    protocol        = tcp
    wait            = no
    user            = root
    server          = /usr/sbin/sshd
    server_args     = -i
    log_on_success  = PID HOST DURATION
    log_on_failure  = HOST ATTEMPT
    only_from       = 192.168.1.0/24 10.0.0.0/8
    no_access       = 192.168.1.50
    access_times    = 08:00-18:00
    per_source      = 3
    instances       = 20
    cps             = 10 30
}
```

**Explications** :
- `only_from` : Autorisé depuis `192.168.1.0/24` et `10.0.0.0/8`
- `no_access` : Bloqué depuis `192.168.1.50` (blacklist)
- `access_times` : Disponible 8h-18h uniquement
- `per_source` : Max 3 connexions par IP
- `instances` : Max 20 connexions simultanées total
- `cps = 10 30` : Max 10 connexions/seconde, sinon pause 30s (anti-DoS)

---

#### Gérer xinetd

**Démarrer/Arrêter service xinetd** :

```bash
# Avec systemd (moderne)
$ sudo systemctl start xinetd
$ sudo systemctl stop xinetd
$ sudo systemctl restart xinetd

# Avec service (compatible SysVinit)
$ sudo service xinetd start
$ sudo service xinetd stop
$ sudo service xinetd restart

# Avec scripts init.d (ancien)
$ sudo /etc/init.d/xinetd start
$ sudo /etc/init.d/xinetd stop
$ sudo /etc/init.d/xinetd restart
```

**⚠️ Important** :
- **Arrêt de xinetd** = Arrêt de **tous les services associés**
- **Démarrage de xinetd** = Démarrage de **tous les services** (si `disable = no`)
- Pas de choix sélectif : impossible de choisir quels services xinetd seront lancés dans tel runlevel ou cible systemd

**Activer xinetd au démarrage (chkconfig - Red Hat/OpenSUSE)** :

```bash
# Autoriser lancement au démarrage pour runlevels 3, 4, 5
$ sudo chkconfig --level 345 xinetd on

# Vérifier configuration
$ chkconfig --list xinetd
xinetd          0:off   1:off   2:off   3:on    4:on    5:on    6:off

# Désactiver au démarrage
$ sudo chkconfig xinetd off
```

**Redémarrer après modifications configuration** :

```bash
# Recharger configuration (sans interrompre connexions actives)
$ sudo systemctl reload xinetd

# Ou redémarrer complètement (interrompt connexions)
$ sudo systemctl restart xinetd

# Vérifier statut
$ sudo systemctl status xinetd
● xinetd.service - LSB: Starts or stops the xinetd daemon.
   Loaded: loaded (/etc/init.d/xinetd)
   Active: active (running)
```

**Vérifier services xinetd actifs** :

```bash
# Lister fichiers configuration
$ ls /etc/xinetd.d/
chargen  daytime  discard  echo  time  telnet  tftp

# Vérifier services activés
$ grep "disable.*no" /etc/xinetd.d/*
/etc/xinetd.d/telnet:    disable         = no
/etc/xinetd.d/tftp:      disable         = no

# Vérifier ports écoutés par xinetd
$ sudo netstat -tulnp | grep xinetd
tcp   0   0   0.0.0.0:23    0.0.0.0:*   LISTEN   1234/xinetd
udp   0   0   0.0.0.0:69    0.0.0.0:*             1234/xinetd
```

**Désactiver service xinetd** :

```bash
# Méthode 1 : Modifier fichier
$ sudo nano /etc/xinetd.d/telnet
# Changer : disable = yes

# Méthode 2 : Supprimer fichier
$ sudo rm /etc/xinetd.d/telnet

# Recharger xinetd
$ sudo systemctl reload xinetd
```

---

### 🔌 systemd Socket Activation

#### Concept

**Alternative moderne à xinetd** : systemd peut activer services à la demande via **socket units**.

**Avantages** :
- ✅ Intégré à systemd (pas de daemon supplémentaire)
- ✅ Activation instantanée (pas de délai démarrage)
- ✅ Gestion uniforme avec `systemctl`

**Fonctionnement** :

```
1. systemd écoute socket (ex: port 80)
         │
         ▼
2. Requête reçue → systemd active service.service
         │
         ▼
3. Service démarré, connexion transférée
         │
         ▼
4. Service traite requête
```

---

#### Lister Sockets systemd

```bash
# Lister tous sockets actifs
$ systemctl list-sockets
LISTEN                          UNIT                         ACTIVATES
/run/systemd/journal/socket     systemd-journald.socket      systemd-journald.service
/run/systemd/journal/stdout     systemd-journald.socket      systemd-journald.service
/var/run/dbus/system_bus_socket dbus.socket                  dbus.service
[::]:22                         sshd.socket                  sshd.service
0.0.0.0:631                     cups.socket                  cups.service

# Filtrer sockets réseau
$ systemctl list-sockets | grep -E ":[0-9]+"
[::]:22                         sshd.socket                  sshd.service
0.0.0.0:631                     cups.socket                  cups.service

# Détails socket spécifique
$ systemctl status sshd.socket
● sshd.socket - OpenBSD Secure Shell server socket
   Loaded: loaded (/lib/systemd/system/sshd.socket; enabled)
   Active: active (listening)
   Listen: [::]:22 (Stream)
```

---

#### Configuration Socket Unit

**Exemple** : SSH socket (`/lib/systemd/system/sshd.socket`)

```ini
[Unit]
Description=OpenBSD Secure Shell server socket
Before=sshd.service
Conflicts=sshd.service

[Socket]
ListenStream=22
Accept=yes

[Install]
WantedBy=sockets.target
```

**Sections** :

**`[Unit]`** :
- `Description` : Description service
- `Before` : Démarrer avant service
- `Conflicts` : Conflit avec service (pas les deux simultanément)

**`[Socket]`** :
- `ListenStream` : Port TCP à écouter (ou chemin socket Unix)
- `ListenDatagram` : Port UDP
- `Accept` : 
  - `yes` = Nouvelle instance par connexion
  - `no` = Même instance pour toutes connexions

**`[Install]`** :
- `WantedBy=sockets.target` : Démarré au boot avec sockets

---

#### Exemple : Service Custom avec Socket Activation

**1. Créer service** (`/etc/systemd/system/myapp.service`) :

```ini
[Unit]
Description=My Application
Requires=myapp.socket

[Service]
Type=simple
ExecStart=/usr/local/bin/myapp
StandardInput=socket
User=www-data

[Install]
WantedBy=multi-user.target
```

**2. Créer socket** (`/etc/systemd/system/myapp.socket`) :

```ini
[Unit]
Description=My Application Socket

[Socket]
ListenStream=8080
Accept=no

[Install]
WantedBy=sockets.target
```

**3. Activer et démarrer** :

```bash
# Recharger systemd
$ sudo systemctl daemon-reload

# Activer socket (pas service directement)
$ sudo systemctl enable myapp.socket

# Démarrer socket
$ sudo systemctl start myapp.socket

# Vérifier
$ systemctl list-sockets | grep myapp
0.0.0.0:8080                    myapp.socket                 myapp.service

# Tester connexion
$ curl http://localhost:8080
# → Service myapp.service démarre automatiquement
```

---

### 🛡️ TCP Wrappers - Contrôle d'Accès

#### Description

**TCP Wrappers** : Système de **contrôle d'accès** basé sur IP pour services réseau.

**Fichiers** :
- `/etc/hosts.allow` : **Whitelist** (IPs autorisées)
- `/etc/hosts.deny` : **Blacklist** (IPs bloquées)

**Ordre de traitement** :
1. Vérifier `/etc/hosts.allow` → Si match → **Autorisé** ✅
2. Vérifier `/etc/hosts.deny` → Si match → **Bloqué** ❌
3. Si aucun match → **Autorisé par défaut** ✅

**Services supportés** :
- ✅ Services compilés avec `libwrap` (sshd, vsftpd, telnetd, etc.)
- ❌ Pas tous (Apache, Nginx utilisent propres contrôles)

**Vérifier support TCP Wrappers** :

```bash
# Vérifier si programme supporte libwrap
$ ldd /usr/sbin/sshd | grep libwrap
libwrap.so.0 => /lib/x86_64-linux-gnu/libwrap.so.0
#  ^^^
#  ✅ sshd supporte TCP Wrappers
```

---

#### Fichier `/etc/hosts.allow`

**Format** :

```
service_list : client_list [ : options ]
```

**Exemples** :

```bash
$ sudo cat /etc/hosts.allow
# Autoriser SSH depuis réseau local
sshd : 192.168.1.0/255.255.255.0

# Autoriser FTP depuis IP spécifique
vsftpd : 10.0.0.50

# Autoriser tous services depuis IP confiance
ALL : 192.168.1.10

# Autoriser SSH depuis domaine
sshd : .example.com

# Autoriser avec logging
sshd : 192.168.1.0/24 : spawn /bin/echo "SSH connection from %a" >> /var/log/tcpwrappers.log
```

---

#### Fichier `/etc/hosts.deny`

**Format** : Identique à `hosts.allow`

**Exemples** :

```bash
$ sudo cat /etc/hosts.deny
# Bloquer SSH depuis IP spécifique
sshd : 10.0.0.99

# Bloquer tous services depuis réseau dangereux
ALL : 203.0.113.0/255.255.255.0

# Bloquer FTP de partout (sauf autorisés dans hosts.allow)
vsftpd : ALL

# Bloquer tout par défaut (politique restrictive)
ALL : ALL
```

---

#### Syntaxe et Wildcards

**Format général** :

```
daemon_list : [AdrIp@] client_list [ : options ]
```

**Composants** :

- **daemon_list** : Liste des exécutables séparés par des virgules. `ALL` pour spécifier tous les services.
- **AdrIp** (optionnel) : Spécifier une adresse IP locale. Par défaut, toutes les adresses IP locales.
- **client_list** : Clients autorisés ou interdits pour ce service, spécifiés par adresse IP, nom d'hôte ou de réseau, masque de réseau, etc.

**Service List** :

```bash
sshd                 # Service SSH uniquement
sshd, vsftpd         # SSH et FTP (séparés par virgules)
ALL                  # Tous services
ALL EXCEPT telnetd   # Tous sauf telnet
```

**Client List - Syntaxe Avancée** :

```bash
# Adresse IP spécifique
192.168.1.10

# Réseau (notation netmask)
192.168.1.0/255.255.255.0

# Réseau (notation préfixe partiel)
192.168.1.

# Hostname spécifique
server.example.com

# Domaine (tous *.example.com)
.example.com

# Wildcards spéciaux
ALL                             # Correspondance systématique (tous clients)
LOCAL                           # Tous les hôtes dont le nom ne contient PAS de point (poste1, poste2, etc.)
UNKNOWN                         # Hôtes dont le nom ne peut pas être résolu
KNOWN                           # Hôtes dont le nom peut être résolu
PARANOID                        # Hôtes dont le nom ne peut être résolu OU dont l'IP n'a pas de résolution inverse (DNS spoofing suspect)

# Opérateur EXCEPT (permet d'exclure certains hôtes)
ALL EXCEPT 192.168.1.50         # Tous sauf IP spécifique
192.168.1. EXCEPT 192.168.1.99  # Réseau sauf une IP
```

**💡 Ordre de lecture et priorité** :

1. **`/etc/hosts.allow` est lu en premier**
2. **`/etc/hosts.deny` est lu ensuite**
3. **La recherche s'arrête à la première correspondance trouvée**
4. **Une ligne dans `hosts.allow` autorise la connexion** ✅
5. **Une ligne dans `hosts.deny` interdit la connexion** ❌
6. **Si l'accès n'est pas explicitement refusé, la connexion est autorisée** ✅

**Exemples pratiques** :

```bash
# /etc/hosts.allow
# Autoriser SSH depuis réseau local uniquement
sshd : 192.168.1. EXCEPT 192.168.1.50

# Autoriser FTP uniquement depuis machines avec nom résolvable
vsftpd : KNOWN

# Autoriser tous services depuis poste local (sans domaine)
ALL : LOCAL

# /etc/hosts.deny
# Bloquer SSH depuis hôtes suspects (pas de reverse DNS)
sshd : PARANOID

# Bloquer FTP depuis réseau spécifique
vsftpd : 172.16.17.0/24

# Bloquer tous services sauf ceux autorisés dans hosts.allow
ALL : ALL
```

**Opérateurs Logiques** :

```bash
# ET logique (implicite)
sshd : 192.168.1. .example.com
#      └─ IP 192.168.1.x OU domaine .example.com

# SAUF
ALL : ALL EXCEPT 192.168.1.0/24
#     └─ Bloquer tout sauf réseau local

# Listes
sshd, vsftpd : 10.0.0.1, 10.0.0.2, 10.0.0.3
```

---

#### Exemples Pratiques TCP Wrappers

**Scénario 1 : SSH uniquement depuis réseau local**

```bash
$ sudo nano /etc/hosts.allow
sshd : 192.168.1.0/24

$ sudo nano /etc/hosts.deny
sshd : ALL

# ✅ SSH autorisé depuis 192.168.1.x
# ❌ SSH bloqué depuis autres IPs
```

**Scénario 2 : Bloquer IP malveillante**

```bash
$ sudo nano /etc/hosts.deny
ALL : 203.0.113.50

# ❌ IP 203.0.113.50 bloquée pour tous services
```

**Scénario 3 : Politique restrictive (deny all par défaut)**

```bash
$ sudo nano /etc/hosts.deny
ALL : ALL

$ sudo nano /etc/hosts.allow
sshd : 192.168.1.0/24
sshd : 10.0.0.10
vsftpd : 192.168.1.50

# ✅ Seulement accès explicitement autorisés dans hosts.allow
# ❌ Tout le reste bloqué
```

**Scénario 4 : Logging connexions**

```bash
$ sudo nano /etc/hosts.allow
sshd : ALL : spawn /bin/echo "`date` SSH connection from %a" >> /var/log/ssh_access.log

# %a = Adresse IP client
# %h = Hostname client
# %d = Daemon (service)
# %p = PID processus
```

**Scénario 5 : Bloquer DNS spoofing**

```bash
$ sudo nano /etc/hosts.deny
ALL : PARANOID

# ❌ Bloque si hostname DNS ne correspond pas à IP (attaque spoofing)
```

---

#### Tester TCP Wrappers

**Vérifier règles sans redémarrer** :

```bash
# Outil tcpdmatch (simulation)
$ sudo apt install tcpd

# Tester accès SSH depuis IP
$ tcpdmatch sshd 192.168.1.50
client:   address  192.168.1.50
server:   process  sshd
matched:  /etc/hosts.allow line 1
access:   granted
#  ^^^
#  ✅ Accès autorisé

# Tester IP bloquée
$ tcpdmatch sshd 203.0.113.50
client:   address  203.0.113.50
server:   process  sshd
matched:  /etc/hosts.deny line 5
access:   denied
#  ^^^
#  ❌ Accès refusé
```

**Logs connexions** :

```bash
# Vérifier logs (syslog ou auth.log)
$ sudo tail -f /var/log/auth.log | grep libwrap
Feb 12 10:30:15 server sshd[1234]: refused connect from 203.0.113.50 (203.0.113.50)
```

---

## 🔒 Partie 3 : Minimiser Services Actifs

### 🎯 Principe de Sécurité

**Règle d'or** : **Moins de services actifs = Moins de surface d'attaque**

**Pourquoi désactiver services inutiles ?**
- ✅ **Sécurité** : Chaque service = Vulnérabilité potentielle
- ✅ **Performance** : Libère RAM et CPU
- ✅ **Simplicité** : Maintenance plus facile

**Exemples services souvent inutiles** :
- `telnet` : Non chiffré (remplacé par SSH)
- `rsh`, `rlogin` : Obsolètes et dangereux
- `cups` : Impression (inutile sur serveur sans imprimante)
- `bluetooth` : Inutile sur serveur
- `avahi-daemon` : Découverte réseau (rarement nécessaire serveur)

---

### 📋 Lister Services Actifs

#### SysVinit - `service --status-all`

**Systèmes anciens** (Debian 7, Ubuntu 14.04, RHEL 6, etc.)

```bash
# Lister tous services et leur statut
$ service --status-all
 [ + ]  apache2
 [ - ]  bluetooth
 [ + ]  cron
 [ - ]  cups
 [ + ]  mysql
 [ + ]  networking
 [ + ]  ssh
 [ - ]  telnet

# Légende :
# [ + ] = Service actif (running)
# [ - ] = Service arrêté (stopped)
# [ ? ] = Statut inconnu
```

---

#### SysVinit - `chkconfig --list`

**RHEL/CentOS uniquement** (SysVinit)

```bash
# Lister services et runlevels
$ chkconfig --list
NetworkManager  0:off   1:off   2:on    3:on    4:on    5:on    6:off
acpid           0:off   1:off   2:on    3:on    4:on    5:on    6:off
crond           0:off   1:off   2:on    3:on    4:on    5:on    6:off
sshd            0:off   1:off   2:on    3:on    4:on    5:on    6:off
telnet          0:off   1:off   2:off   3:off   4:off   5:off   6:off

# Runlevels :
# 0 = Halt (arrêt système)
# 1 = Single user (mode maintenance)
# 2 = Multi-user sans réseau
# 3 = Multi-user avec réseau (mode texte)
# 4 = Non utilisé
# 5 = Multi-user avec GUI
# 6 = Reboot

# on  = Service démarre à ce runlevel
# off = Service ne démarre pas
```

---

#### systemd - `systemctl status`

**Systèmes modernes** (Debian 10+, Ubuntu 16.04+, RHEL 7+, etc.)

```bash
# Lister services actifs
$ systemctl list-units --type=service
UNIT                     LOAD   ACTIVE SUB     DESCRIPTION
apache2.service          loaded active running The Apache HTTP Server
cron.service             loaded active running Regular background program...
mysql.service            loaded active running MySQL Community Server
NetworkManager.service   loaded active running Network Manager
ssh.service              loaded active running OpenBSD Secure Shell server
systemd-journald.service loaded active running Journal Service

# Lister tous services (actifs + inactifs)
$ systemctl list-units --type=service --all

# Filtrer services actifs
$ systemctl list-units --type=service --state=running

# Filtrer services échoués
$ systemctl list-units --type=service --state=failed
```

**Vérifier service spécifique** :

```bash
$ systemctl status apache2
● apache2.service - The Apache HTTP Server
   Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
   Active: active (running) since Wed 2026-02-12 10:00:00 CET; 2h ago
     Docs: https://httpd.apache.org/docs/2.4/
 Main PID: 1234 (apache2)
    Tasks: 55 (limit: 4915)
   Memory: 15.2M
   CGroup: /system.slice/apache2.service
           ├─1234 /usr/sbin/apache2 -k start
           ├─1235 /usr/sbin/apache2 -k start
           └─1236 /usr/sbin/apache2 -k start
```

**Vérifier si service activé au démarrage** :

```bash
$ systemctl is-enabled apache2
enabled  # Se lance au boot

$ systemctl is-enabled telnet
disabled  # Ne se lance pas au boot
```

---

### 🛑 Désactiver Services

#### SysVinit - `chkconfig` (RHEL/CentOS)

```bash
# Désactiver service (ne démarre plus au boot)
$ sudo chkconfig telnet off

# Vérifier
$ chkconfig --list telnet
telnet  0:off   1:off   2:off   3:off   4:off   5:off   6:off

# Arrêter service immédiatement
$ sudo service telnet stop
```

---

#### SysVinit - `update-rc.d` (Debian/Ubuntu)

```bash
# Désactiver service (supprimer liens runlevels)
$ sudo update-rc.d telnet remove

# Vérifier (liens dans /etc/rcX.d/ supprimés)
$ ls /etc/rc*.d/ | grep telnet
# (aucun résultat = service désactivé)

# Arrêter service immédiatement
$ sudo service telnet stop
```

**Alternative : désactiver sans supprimer** :

```bash
# Désactiver (garder liens)
$ sudo update-rc.d apache2 disable

# Réactiver
$ sudo update-rc.d apache2 enable
```

---

#### systemd - `systemctl disable`

```bash
# Désactiver service (ne démarre plus au boot)
$ sudo systemctl disable apache2
Removed /etc/systemd/system/multi-user.target.wants/apache2.service

# Arrêter service immédiatement
$ sudo systemctl stop apache2

# Désactiver ET arrêter en une commande
$ sudo systemctl disable --now apache2
#                             └─ --now = stop immédiatement
```

**Vérifier** :

```bash
$ systemctl is-enabled apache2
disabled

$ systemctl is-active apache2
inactive
```

**Masquer service** (empêcher démarrage manuel également) :

```bash
# Masquer service (plus fort que disable)
$ sudo systemctl mask telnet
Created symlink /etc/systemd/system/telnet.service → /dev/null

# Tentative démarrage échoue
$ sudo systemctl start telnet
Failed to start telnet.service: Unit telnet.service is masked.

# Démasquer pour réactiver
$ sudo systemctl unmask telnet
```

---

### 📂 Scripts Init et Runlevels (SysVinit)

#### Répertoires SysVinit

**`/etc/init.d/`** : Scripts de démarrage services

```bash
$ ls /etc/init.d/
apache2  cron  mysql  networking  ssh  ...
```

**Contenu script** :

```bash
$ cat /etc/init.d/apache2
#!/bin/sh
### BEGIN INIT INFO
# Provides:          apache2
# Required-Start:    $local_fs $remote_fs $network
# Required-Stop:     $local_fs $remote_fs $network
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: Apache2 web server
### END INIT INFO

case "$1" in
  start)
    /usr/sbin/apache2ctl start
    ;;
  stop)
    /usr/sbin/apache2ctl stop
    ;;
  restart)
    /usr/sbin/apache2ctl restart
    ;;
  *)
    echo "Usage: /etc/init.d/apache2 {start|stop|restart}"
    exit 1
    ;;
esac
```

**Utiliser scripts init** :

```bash
# Démarrer service
$ sudo /etc/init.d/apache2 start

# Arrêter service
$ sudo /etc/init.d/apache2 stop

# Redémarrer service
$ sudo /etc/init.d/apache2 restart

# Statut service
$ sudo /etc/init.d/apache2 status
```

---

#### Répertoires Runlevels `/etc/rcX.d/`

**Structure** :

```
/etc/rc0.d/  → Runlevel 0 (halt)
/etc/rc1.d/  → Runlevel 1 (single user)
/etc/rc2.d/  → Runlevel 2 (multi-user)
/etc/rc3.d/  → Runlevel 3 (multi-user + réseau)
/etc/rc4.d/  → Runlevel 4 (custom)
/etc/rc5.d/  → Runlevel 5 (multi-user + GUI)
/etc/rc6.d/  → Runlevel 6 (reboot)
```

**Contenu exemple** :

```bash
$ ls -l /etc/rc3.d/
lrwxrwxrwx 1 root root  20 Jan 10 10:00 S01apache2 -> ../init.d/apache2
lrwxrwxrwx 1 root root  14 Jan 10 10:00 S01cron -> ../init.d/cron
lrwxrwxrwx 1 root root  17 Jan 10 10:00 S02mysql -> ../init.d/mysql
lrwxrwxrwx 1 root root  13 Jan 10 10:00 S03ssh -> ../init.d/ssh
lrwxrwxrwx 1 root root  17 Jan 10 10:00 K01telnet -> ../init.d/telnet

# S = Start (démarrer service à ce runlevel)
# K = Kill (arrêter service à ce runlevel)
# Numéro (01, 02, 03) = Ordre démarrage
```

**Conventions nommage** :

```
S01apache2
│ │  └─ Nom service
│ └─ Ordre (01-99)
└─ S = Start / K = Kill
```

**Ordre démarrage** :
- `S01` démarre avant `S02`
- `S02` démarre avant `S03`
- Permet gérer dépendances (ex : réseau avant Apache)

---

#### Fichier `/etc/inittab`

**Rôle** : Configuration init système (runlevel par défaut, actions à chaque runlevel).

⚠️ **Note** : Obsolète avec systemd (remplacé par `/etc/systemd/system.conf` et targets).

**Exemple `/etc/inittab`** (SysVinit) :

```bash
$ cat /etc/inittab
# Runlevel par défaut
id:3:initdefault:

# Actions au boot
si::sysinit:/etc/init.d/rcS

# Runlevels
l0:0:wait:/etc/init.d/rc 0
l1:1:wait:/etc/init.d/rc 1
l2:2:wait:/etc/init.d/rc 2
l3:3:wait:/etc/init.d/rc 3
l4:4:wait:/etc/init.d/rc 4
l5:5:wait:/etc/init.d/rc 5
l6:6:wait:/etc/init.d/rc 6

# Ctrl+Alt+Del
ca::ctrlaltdel:/sbin/shutdown -t3 -r now

# Consoles
1:2345:respawn:/sbin/getty 38400 tty1
2:23:respawn:/sbin/getty 38400 tty2
```

**Format ligne** :

```
id:runlevels:action:process
```

- `id` : Identifiant unique
- `runlevels` : Runlevels concernés (vide = tous)
- `action` : Type d'action (`initdefault`, `sysinit`, `wait`, `respawn`, etc.)
- `process` : Commande à exécuter

**Actions courantes** :

| Action | Description |
|--------|-------------|
| `initdefault` | Runlevel par défaut au boot |
| `sysinit` | Exécuté au démarrage système |
| `wait` | Attendre fin processus avant continuer |
| `respawn` | Relancer processus s'il se termine (ex : getty consoles) |
| `ctrlaltdel` | Action lors Ctrl+Alt+Del |

**Changer runlevel par défaut** :

```bash
# Éditer /etc/inittab
$ sudo nano /etc/inittab

# Modifier ligne
id:5:initdefault:
#   └─ Runlevel 5 (GUI) au lieu de 3 (texte)

# Appliquer (reboot ou)
$ sudo telinit q  # Relire inittab
```

**Vérifier runlevel actuel** :

```bash
$ runlevel
N 3
# N = Précédent (N = aucun)
# 3 = Actuel
```

**Changer runlevel à la volée** :

```bash
# Passer en runlevel 1 (single user)
$ sudo telinit 1

# Passer en runlevel 5 (GUI)
$ sudo telinit 5

# Reboot (runlevel 6)
$ sudo telinit 6
```

---

## 🛠️ Scénarios Pratiques

### Scénario 1 : Désactiver Compte Service Compromis

```bash
# 1. Vérifier compte actuel
$ grep "baduser" /etc/passwd
baduser:x:1005:1005:Bad User:/home/baduser:/bin/bash

# 2. Bloquer connexion avec nologin
$ sudo usermod -s /usr/sbin/nologin baduser

# 3. Verrouiller mot de passe
$ sudo passwd -l baduser

# 4. Vérifier
$ grep "baduser" /etc/passwd
baduser:x:1005:1005:Bad User:/home/baduser:/usr/sbin/nologin

$ sudo grep "baduser" /etc/shadow
baduser:!$6$...:19751:0:99999:7:::
#       └─ ! = Verrouillé

# 5. Tester connexion
$ ssh baduser@localhost
baduser@localhost's password:
This account is currently not available.
Connection closed.
# ✅ Connexion bloquée
```

---

### Scénario 2 : Maintenance Système avec /etc/nologin

```bash
# 1. Prévenir utilisateurs (email)
# 2. Créer fichier maintenance
$ sudo nano /etc/nologin
============================================
    MAINTENANCE PLANIFIÉE
    
Début  : 12 février 2026 - 22h00
Fin    : 13 février 2026 - 02h00

Migration base de données en cours.
Merci de votre patience.

Contact urgence : ops@example.com
============================================

# 3. Effectuer maintenance
$ sudo systemctl stop mysql
$ sudo mysqldump --all-databases > backup.sql
$ sudo apt upgrade -y
$ sudo systemctl start mysql

# 4. Retirer fichier
$ sudo rm /etc/nologin

# 5. Informer utilisateurs (email)
```

---

### Scénario 3 : Configurer SSH via xinetd avec Restrictions

```bash
# 1. Installer xinetd
$ sudo apt install xinetd

# 2. Créer configuration SSH
$ sudo nano /etc/xinetd.d/ssh
service ssh
{
    disable         = no
    socket_type     = stream
    protocol        = tcp
    wait            = no
    user            = root
    server          = /usr/sbin/sshd
    server_args     = -i
    log_on_success  = PID HOST DURATION
    log_on_failure  = HOST ATTEMPT
    only_from       = 192.168.1.0/24 10.0.0.0/8
    no_access       = 192.168.1.100
    access_times    = 06:00-22:00
    per_source      = 3
    cps             = 5 60
}

# 3. Désactiver service SSH natif (éviter conflit port 22)
$ sudo systemctl disable --now ssh

# 4. Démarrer xinetd
$ sudo systemctl restart xinetd

# 5. Vérifier
$ sudo netstat -tulnp | grep :22
tcp   0   0   0.0.0.0:22   0.0.0.0:*   LISTEN   1234/xinetd

# ✅ SSH géré par xinetd avec restrictions IP et horaires
```

---

### Scénario 4 : Bloquer IP Malveillante avec TCP Wrappers

```bash
# 1. Attaque détectée depuis IP 203.0.113.99
$ sudo tail /var/log/auth.log
Feb 12 14:30:15 server sshd[5678]: Failed password for root from 203.0.113.99
Feb 12 14:30:18 server sshd[5679]: Failed password for root from 203.0.113.99
Feb 12 14:30:21 server sshd[5680]: Failed password for root from 203.0.113.99
# ^^^ Brute-force SSH

# 2. Bloquer IP immédiatement
$ sudo nano /etc/hosts.deny
ALL : 203.0.113.99

# 3. Vérifier blocage
$ tcpdmatch sshd 203.0.113.99
client:   address  203.0.113.99
server:   process  sshd
matched:  /etc/hosts.deny line 1
access:   denied
# ✅ Bloquée

# 4. Surveiller logs
$ sudo tail -f /var/log/auth.log
Feb 12 14:35:10 server sshd[5690]: refused connect from 203.0.113.99
# ✅ Connexions refusées
```

---

### Scénario 5 : Audit et Désactivation Services Inutiles

```bash
# 1. Lister services actifs
$ systemctl list-units --type=service --state=running | grep -v systemd
apache2.service    loaded active running The Apache HTTP Server
bluetooth.service  loaded active running Bluetooth service
cups.service       loaded active running CUPS Scheduler
mysql.service      loaded active running MySQL Community Server
ssh.service        loaded active running OpenBSD Secure Shell server

# 2. Identifier services inutiles (serveur sans Bluetooth ni imprimante)
# → bluetooth.service (inutile)
# → cups.service (inutile)

# 3. Désactiver services inutiles
$ sudo systemctl disable --now bluetooth
$ sudo systemctl disable --now cups

# 4. Vérifier
$ systemctl is-enabled bluetooth
disabled

$ systemctl is-active bluetooth
inactive

# 5. Vérifier ports ouverts (moins de services exposés)
$ sudo ss -tulnp
# (Plus de ports Bluetooth/CUPS)

# ✅ Surface d'attaque réduite
```

---

## ❌ Erreurs Courantes et Solutions

### Erreur 1 : `/etc/nologin` bloque root également

```bash
# ❌ FAUX : Root ne peut jamais se connecter avec /etc/nologin
```

**Réalité** : Root **n'est PAS bloqué** par `/etc/nologin`.

```bash
$ sudo touch /etc/nologin

# Utilisateur normal bloqué
$ ssh alice@server
This account is currently not available.

# Root peut se connecter
$ ssh root@server
root@server's password:
root@server:~# 
# ✅ Root connecté sans problème
```

---

### Erreur 2 : Modifier `/etc/shadow` directement

```bash
# ❌ DANGEREUX
$ sudo nano /etc/shadow
# Risque corruption, syntaxe incorrecte, permissions cassées
```

**Solution** : Utiliser commandes dédiées

```bash
# Modifier mot de passe
$ sudo passwd alice

# Verrouiller compte
$ sudo passwd -l alice

# Politique expiration
$ sudo chage -M 90 alice

# Vérifier
$ sudo passwd -S alice
```

---

### Erreur 3 : xinetd et service natif sur même port

```bash
# Conflit : xinetd écoute port 22 ET sshd natif
$ sudo systemctl start ssh
$ sudo systemctl start xinetd
# /etc/xinetd.d/ssh configuré pour port 22

# Erreur :
Address already in use
```

**Solution** : Désactiver service natif

```bash
$ sudo systemctl disable --now ssh
$ sudo systemctl restart xinetd
# ✅ Seulement xinetd écoute port 22
```

---

### Erreur 4 : Ordre `/etc/hosts.allow` et `/etc/hosts.deny`

```bash
# ❌ FAUX : hosts.deny prioritaire
$ cat /etc/hosts.deny
ALL : ALL

$ cat /etc/hosts.allow
sshd : 192.168.1.10

# Pensée : "192.168.1.10 bloqué car ALL : ALL dans deny"
```

**Réalité** : `hosts.allow` **prioritaire** !

**Ordre traitement** :
1. Vérifier `hosts.allow` → Si match → **Autorisé** ✅ (STOP)
2. Vérifier `hosts.deny` → Si match → **Bloqué** ❌
3. Aucun match → Autorisé par défaut

```bash
# IP 192.168.1.10 pour SSH :
# 1. Match dans hosts.allow → ✅ Autorisé (ne vérifie pas deny)
# 2. (hosts.deny ignoré)
```

---

### Erreur 5 : `systemctl disable` n'arrête pas service

```bash
$ sudo systemctl disable apache2
Removed symlink...

$ systemctl is-active apache2
active
# ^^^ Service toujours actif !
```

**Cause** : `disable` empêche démarrage au boot, mais **n'arrête pas** service actuel.

**Solution** :

```bash
# Désactiver ET arrêter
$ sudo systemctl disable --now apache2
#                             └─ --now = stop immédiatement

# Vérifier
$ systemctl is-active apache2
inactive
```

---

## 📝 Cheat Sheet - Sécurité Hôte

### Fichiers Authentification

```bash
# Fichiers système
cat /etc/passwd                   # Comptes utilisateurs (lisible tous)
sudo cat /etc/shadow              # Mots de passe hashés (root uniquement)
sudo chmod 640 /etc/shadow        # Permissions correctes shadow

# Bloquer connexions utilisateur
sudo usermod -s /usr/sbin/nologin alice    # Message affiché
sudo usermod -s /bin/false bob             # Pas de message
sudo passwd -l charlie                     # Verrouiller compte
```

---

### Mode Maintenance

```bash
# Activer mode maintenance
sudo nano /etc/nologin            # Créer fichier avec message
# (Bloque utilisateurs, pas root)

# Désactiver
sudo rm /etc/nologin
```

---

### xinetd

```bash
# Fichiers configuration
ls /etc/xinetd.d/                 # Services xinetd
sudo nano /etc/xinetd.d/service   # Éditer service

# Gérer xinetd
sudo systemctl restart xinetd     # Recharger config
sudo systemctl reload xinetd      # Relire sans redémarrer

# Vérifier services
grep "disable.*no" /etc/xinetd.d/*
```

---

### systemd Socket Activation

```bash
# Lister sockets
systemctl list-sockets            # Tous sockets actifs
systemctl status service.socket   # Détails socket

# Gérer sockets
sudo systemctl start service.socket
sudo systemctl enable service.socket
```

---

### TCP Wrappers

```bash
# Fichiers contrôle d'accès
sudo nano /etc/hosts.allow        # Whitelist
sudo nano /etc/hosts.deny         # Blacklist

# Exemples règles
# /etc/hosts.allow
sshd : 192.168.1.0/24
ALL : 10.0.0.10

# /etc/hosts.deny
sshd : ALL
ALL : 203.0.113.0/24

# Tester règles
tcpdmatch sshd 192.168.1.50       # Simuler connexion

# Vérifier support libwrap
ldd /usr/sbin/sshd | grep libwrap
```

---

### Lister Services

```bash
# SysVinit
service --status-all              # Tous services
chkconfig --list                  # Services + runlevels (RHEL)

# systemd
systemctl list-units --type=service              # Services actifs
systemctl list-units --type=service --all        # Tous
systemctl list-units --type=service --state=running
systemctl status service
systemctl is-enabled service
systemctl is-active service
```

---

### Désactiver Services

```bash
# SysVinit
sudo chkconfig service off        # RHEL/CentOS
sudo update-rc.d service remove   # Debian/Ubuntu
sudo service service stop

# systemd
sudo systemctl disable service    # Désactiver au boot
sudo systemctl stop service       # Arrêter maintenant
sudo systemctl disable --now service    # Les deux
sudo systemctl mask service       # Empêcher démarrage complet
```

---

### Scripts Init

```bash
# Répertoires
ls /etc/init.d/                   # Scripts services
ls /etc/rc3.d/                    # Runlevel 3
cat /etc/inittab                  # Config init (obsolète systemd)

# Utiliser scripts
sudo /etc/init.d/service start
sudo /etc/init.d/service stop
sudo /etc/init.d/service restart
```

---

## 🎯 Points Clés pour l'Examen LPIC-1

### Ce qu'il faut maîtriser ✅

1. **Fichiers `/etc/passwd` et `/etc/shadow`** 📄
   - Format 7 champs passwd : `username:x:UID:GID:GECOS:home:shell`
   - Format 9 champs shadow : `username:hash:last:min:max:warn:inactive:expire:reserved`
   - Hash format : `$6$salt$hash` (SHA-512)
   - Salage (salt) : Même mdp → Hash différents
   - Permissions : passwd `644`, shadow `640`

2. **Shells bloquants** 🚫
   - `/usr/sbin/nologin` : Message affiché (personnalisable `/etc/nologin.txt`)
   - `/bin/false` : Pas de message
   - Usage : Comptes services, comptes désactivés

3. **Fichier `/etc/nologin`** 🛠️
   - Mode maintenance système entier
   - Bloque utilisateurs (pas root)
   - Contenu affiché aux utilisateurs

4. **Super-servers** 🔌
   - **xinetd** : Démarre services à la demande
   - Configuration : `/etc/xinetd.d/service`
   - Directives : `disable`, `socket_type`, `user`, `only_from`, `access_times`, `per_source`
   - **systemd socket** : Alternative moderne
   - `systemctl list-sockets` : Lister sockets

5. **TCP Wrappers** 🛡️
   - `/etc/hosts.allow` : Whitelist (prioritaire)
   - `/etc/hosts.deny` : Blacklist
   - Ordre : allow → deny → autorisé par défaut
   - Syntaxe : `service : client_list`
   - Tester : `tcpdmatch`

6. **Minimiser services** 🔒
   - Principe : Moins de services = Moins de vulnérabilités
   - Lister : `service --status-all`, `systemctl list-units --type=service`
   - Désactiver : `systemctl disable --now service`
   - Masquer : `systemctl mask service`

7. **SysVinit** 📂
   - Scripts : `/etc/init.d/`
   - Runlevels : `/etc/rc0.d/` à `/etc/rc6.d/`
   - `/etc/inittab` : Config init (obsolète systemd)
   - Liens : `S##service` (start), `K##service` (kill)

---

### Pièges Fréquents à l'Examen ⚠️

1. **Permissions `/etc/shadow`** :
   - ✅ Correct : `640` (rw-r-----)
   - ❌ Danger : `644` (lisible par tous)

2. **`/etc/nologin` vs `/usr/sbin/nologin`** :
   - `/etc/nologin` = Fichier système (bloque tous)
   - `/usr/sbin/nologin` = Shell utilisateur (bloque 1 utilisateur)

3. **Root et `/etc/nologin`** :
   - Root **jamais bloqué** par `/etc/nologin`

4. **Ordre TCP Wrappers** :
   - `hosts.allow` vérifié **en premier** (prioritaire)
   - Si match dans allow → Autorisé (deny ignoré)

5. **xinetd vs service natif** :
   - Conflit port si les deux actifs
   - Désactiver service natif avant activer xinetd

6. **`systemctl disable` ≠ `stop`** :
   - `disable` : Pas de démarrage au boot
   - `stop` : Arrêter maintenant
   - `disable --now` : Les deux

7. **Hash shadow** :
   - `!` = Compte verrouillé
   - `!!` = Mdp jamais défini
   - `*` = Service système (pas de connexion)

8. **Runlevels SysVinit** :
   - 0 = Halt, 1 = Single user, 3 = Multi-user réseau, 5 = GUI, 6 = Reboot
   - systemd : Targets (multi-user.target, graphical.target)

---

### Commandes à Connaître par Cœur 💯

```bash
# Authentification
cat /etc/passwd                   # Comptes utilisateurs
sudo cat /etc/shadow              # Mots de passe hashés
sudo usermod -s /usr/sbin/nologin user    # Bloquer connexion

# Mode maintenance
sudo nano /etc/nologin            # Créer fichier maintenance
sudo rm /etc/nologin              # Désactiver

# xinetd
sudo systemctl restart xinetd     # Recharger config
systemctl list-sockets            # Sockets systemd

# TCP Wrappers
sudo nano /etc/hosts.allow        # Whitelist
sudo nano /etc/hosts.deny         # Blacklist
tcpdmatch sshd 192.168.1.10       # Tester règle

# Services
service --status-all              # Lister (SysVinit)
systemctl list-units --type=service    # Lister (systemd)
sudo systemctl disable --now service   # Désactiver + arrêter
sudo systemctl mask service       # Masquer (bloquer complet)
```

---

**Poids du module** : 3 points  
**Difficulté** : ⭐⭐⭐ Moyenne  
**Temps de préparation recommandé** : 5-6 heures

🎯 **Focus examen** : Maîtriser différences `/etc/passwd`/`/etc/shadow`, comprendre shells bloquants (`nologin` vs `false`), savoir configurer xinetd et TCP Wrappers, et minimiser services actifs pour sécurité !
