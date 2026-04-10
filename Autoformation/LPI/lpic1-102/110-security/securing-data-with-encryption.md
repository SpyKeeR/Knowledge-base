# 110.3 - Securing Data with Encryption 🔐🔑

## 📋 Informations du Module

- **Poids à l'examen** : 4 points
- **Objectifs clés** :
  - Comprendre le chiffrement asymétrique (paires de clés publique/privée) 🔑
  - Maîtriser SSH et l'authentification par clés publiques
  - Utiliser `ssh-keygen`, `ssh-agent`, `ssh-add`
  - Créer et gérer tunnels SSH (local, remote, dynamique)
  - Configurer X11 forwarding via SSH
  - Utiliser GPG pour chiffrer/déchiffrer et signer données
  - Gérer clés GPG (génération, export, import, révocation)
  - Comprendre et utiliser `gpg-agent`

---

## 🔐 Partie 1 : Paires de Clés (Publique & Privée) et SSH

### 🔑 Chiffrement Asymétrique - Concepts Fondamentaux

#### Principe de Base

**Chiffrement symétrique** (ancien) :
- ❌ Même clé pour chiffrer ET déchiffrer
- ❌ Problème : Comment partager la clé en toute sécurité ?

**Chiffrement asymétrique** (moderne) :
- ✅ **2 clés** : Publique + Privée (paire mathématiquement liée)
- ✅ Clé publique : **Partageable** (peut être publiée partout)
- ✅ Clé privée : **Secrète** (jamais partagée, protégée par passphrase)

---

#### Rôles des Clés

**Schéma relationnel** :

```
┌─────────────────┐         ┌─────────────────┐
│  CLÉ PUBLIQUE   │◄───────►│  CLÉ PRIVÉE     │
│  (partageable)  │         │  (secrète)      │
└─────────────────┘         └─────────────────┘
       │                            │
       │                            │
       ▼                            ▼
  ┌────────────────────────────────────┐
  │   Liées mathématiquement           │
  │   Ce qu'une clé chiffre,           │
  │   seule l'autre peut déchiffrer    │
  └────────────────────────────────────┘
```

---

#### Usage 1 : Chiffrement de Données 🔒

**Objectif** : Alice envoie message **confidentiel** à Bob.

**Processus** :

```
ALICE (émetteur)                                BOB (récepteur)
       │                                              │
       │ 1. Bob génère paire de clés                 │
       │                                              ├─ Clé privée (gardée secrète)
       │◄─────────────────────────────────────────── ├─ Clé publique (publiée)
       │ 2. Bob envoie clé publique
       │
       ├─ 3. Alice chiffre message avec clé publique Bob
       │    Message chiffré : Illisible sans clé privée
       │
       ├────────────────────────────────────────────►│
       │ 4. Alice envoie message chiffré              │
       │                                              │
       │                                              ├─ 5. Bob déchiffre avec sa clé privée
       │                                              └─ Message en clair ✅
```

**Règle** :
- ✅ **Chiffrer** : Utilise clé **publique** du destinataire
- ✅ **Déchiffrer** : Utilise clé **privée** du destinataire

**Sécurité** :
- ✅ Seul Bob (détenteur clé privée) peut lire message
- ✅ Même si clé publique interceptée = Impossible de déchiffrer
- ✅ Même si message chiffré intercepté = Impossible de déchiffrer sans clé privée

---

#### Usage 2 : Signature Numérique ✍️

**Objectif** : Alice prouve qu'elle est bien l'auteur du message (authenticité).

**Processus** :

```
ALICE (signataire)                              BOB (vérificateur)
       │
       ├─ Clé privée Alice (gardée secrète)
       ├─ Clé publique Alice (publiée) ───────────►│
       │                                            │
       ├─ 1. Alice signe message avec sa clé privée
       │    Signature = Preuve d'authenticité
       │
       ├────────────────────────────────────────────►│
       │ 2. Alice envoie message + signature         │
       │                                             │
       │                                             ├─ 3. Bob vérifie signature avec clé publique Alice
       │                                             │
       │                                             └─ Signature valide = Message bien de Alice ✅
```

**Règle** :
- ✅ **Signer** : Utilise clé **privée** de l'émetteur
- ✅ **Vérifier** : Utilise clé **publique** de l'émetteur

**Sécurité** :
- ✅ Seule Alice peut signer (détient clé privée)
- ✅ Tout le monde peut vérifier (clé publique disponible)
- ✅ Prouve authorship ET intégrité (message non modifié)

---

#### Tableau Récapitulatif

| Action | Clé utilisée | Qui peut faire ? | Objectif |
|--------|--------------|------------------|----------|
| **Chiffrer** | Clé **publique** destinataire | Tout le monde | Confidentialité |
| **Déchiffrer** | Clé **privée** destinataire | Destinataire uniquement | Lire message |
| **Signer** | Clé **privée** émetteur | Émetteur uniquement | Authenticité |
| **Vérifier signature** | Clé **publique** émetteur | Tout le monde | Prouver authorship |

---

### 🔐 SSH et Authentification par Clés

#### Fingerprint Serveur SSH

**Rôle** : **Empreinte unique** (hash) de la clé publique du serveur SSH.

**Fonctionnement** :

```
PREMIÈRE CONNEXION au serveur SSH

1. Client SSH demande clé publique serveur
        │
        ▼
2. Serveur envoie sa clé publique
        │
        ▼
3. Client calcule fingerprint (hash SHA256 de la clé)
        │
        ▼
4. Client affiche fingerprint et demande confirmation
        │
        ▼
The authenticity of host '192.168.1.10' can't be established.
ED25519 key fingerprint is SHA256:abcd1234efgh5678ijkl9012mnop3456qrst7890.
Are you sure you want to continue connecting (yes/no)?
        │
        ▼
5. Utilisateur vérifie fingerprint (comparaison hors-bande)
        │
        ▼
6. Si "yes" → Clé publique serveur enregistrée dans ~/.ssh/known_hosts
```

**Exemple connexion initiale** :

```bash
$ ssh alice@192.168.1.10
The authenticity of host '192.168.1.10 (192.168.1.10)' can't be established.
ED25519 key fingerprint is SHA256:3c8f2d7a6b1e5f9c4d8a2e7b3f5c9d1a6e8b2f4c7d9a3e5b1f8c4d6a9e2b5f7c.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.1.10' (ED25519) to the list of known hosts.
alice@192.168.1.10's password:
```

**Vérifier fingerprint serveur** (avant première connexion) :

```bash
# Sur le SERVEUR (192.168.1.10)
$ ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub
256 SHA256:3c8f2d7a6b1e5f9c4d8a2e7b3f5c9d1a6e8b2f4c7d9a3e5b1f8c4d6a9e2b5f7c root@server (ED25519)
#   └─ Fingerprint à comparer

# Envoyer ce fingerprint à l'utilisateur via canal sécurisé (email chiffré, téléphone, etc.)
```

**Pourquoi c'est important ?** 🔴
- ✅ Protège contre attaques **Man-in-the-Middle** (MITM)
- ✅ Confirme que vous vous connectez au BON serveur
- ❌ Si fingerprint différent lors prochaine connexion → **Alerte sécurité !**

---

#### Fichier `~/.ssh/known_hosts`

**Rôle** : Stocke clés publiques **connues** des serveurs SSH.

**Format** :

```
hostname,ip algorithme clé_publique_base64
```

**Exemple** :

```bash
$ cat ~/.ssh/known_hosts
192.168.1.10 ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAbCdEfGhIjKlMnOpQrStUvWxYz...
github.com ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAq2A7hRGmdnm9tUDbO9IDSwBK6TbQa+...
10.0.0.50 ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTY...
```

**Champs** :
1. **Hostname/IP** : Identité serveur (`192.168.1.10`, `github.com`, etc.)
2. **Algorithme** : Type de clé (`ssh-ed25519`, `ssh-rsa`, `ecdsa-sha2-nistp256`)
3. **Clé publique** : Encodée en Base64

**Lors connexions ultérieures** :

```bash
$ ssh alice@192.168.1.10
alice@192.168.1.10's password:
# ✅ Pas d'avertissement = Clé serveur reconnue (dans known_hosts)
```

**Alerte si clé serveur change** :

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ED25519 key sent by the remote host is
SHA256:NEW_DIFFERENT_FINGERPRINT_HERE.
Please contact your system administrator.
Add correct host key in /home/alice/.ssh/known_hosts to get rid of this message.
Offending ED25519 key in /home/alice/.ssh/known_hosts:5
Host key verification failed.
```

**Causes légitimes** :
- ✅ Serveur réinstallé (nouvelles clés générées)
- ✅ Migration serveur (nouvelle machine, même IP)

**Causes malveillantes** :
- ❌ Attaque Man-in-the-Middle (quelqu'un intercepte connexion)

---

### 🔧 Outil `ssh-keygen`

#### Description

**Rôle** : Générer paires de clés SSH (publique + privée).

#### Générer Paire de Clés

**Commande basique** :

```bash
$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/alice/.ssh/id_rsa): [ENTER]
Enter passphrase (empty for no passphrase): [passphrase_optionnelle]
Enter same passphrase again: [passphrase_optionnelle]
Your identification has been saved in /home/alice/.ssh/id_rsa
Your public key has been saved in /home/alice/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:abcd1234efgh5678ijkl9012mnop3456qrst7890 alice@laptop
The key's randomart image is:
+---[RSA 3072]----+
|    .o+o.        |
|   . +=o         |
|    =.=.         |
|   o B.o         |
|  . = S .        |
| . + * o         |
|  o * = .        |
| . o = E         |
|  . . o          |
+----[SHA256]-----+
```

**Fichiers créés** :

```bash
$ ls -l ~/.ssh/
-rw------- 1 alice alice 2602 Feb 12 10:00 id_rsa        # Clé PRIVÉE (permissions 600)
-rw-r--r-- 1 alice alice  571 Feb 12 10:00 id_rsa.pub    # Clé PUBLIQUE (permissions 644)
```

⚠️ **Important** :
- **Clé privée** (`id_rsa`) : **JAMAIS** partager, permissions `600` (lecture/écriture owner uniquement)
- **Clé publique** (`id_rsa.pub`) : Partageable, copier sur serveurs SSH

---

#### Option `-t` - Choisir Algorithme

**Syntaxe** :

```bash
ssh-keygen -t <algorithme>
```

**Algorithmes disponibles** :

| Algorithme | Force | Taille clé | Vitesse | Recommandation | Exemple |
|------------|-------|------------|---------|----------------|---------|
| **RSA** | ⭐⭐⭐ | 2048-4096 bits | Lent | ✅ Compatible partout | `-t rsa -b 4096` |
| **DSA** | ❌ Faible | 1024 bits (fixe) | Rapide | ❌ **Obsolète** (vulnérable) | `-t dsa` (à éviter) |
| **ECDSA** | ⭐⭐⭐⭐ | 256/384/521 bits | Rapide | ✅ Bon | `-t ecdsa -b 521` |
| **Ed25519** | ⭐⭐⭐⭐⭐ | 256 bits (fixe) | Très rapide | ✅✅ **Meilleur** (moderne) | `-t ed25519` |

**Exemples** :

```bash
# RSA 4096 bits (très sécurisé, compatible)
$ ssh-keygen -t rsa -b 4096
# Fichiers : ~/.ssh/id_rsa + ~/.ssh/id_rsa.pub

# Ed25519 (recommandé moderne)
$ ssh-keygen -t ed25519
# Fichiers : ~/.ssh/id_ed25519 + ~/.ssh/id_ed25519.pub

# ECDSA 521 bits
$ ssh-keygen -t ecdsa -b 521
# Fichiers : ~/.ssh/id_ecdsa + ~/.ssh/id_ecdsa.pub
```

**Avec commentaire et nom fichier personnalisé** :

```bash
$ ssh-keygen -t ed25519 -C "alice@laptop-work" -f ~/.ssh/id_work
```

**Options utiles** :

| Option | Description | Exemple |
|--------|-------------|---------|
| `-t` | Type algorithme | `-t ed25519` |
| `-b` | Taille clé (bits) | `-b 4096` (RSA, ECDSA) |
| `-C` | Commentaire | `-C "alice@work"` |
| `-f` | Nom fichier | `-f ~/.ssh/id_backup` |
| `-N` | Passphrase (non-interactif) | `-N "my_passphrase"` |
| `-q` | Quiet (silencieux) | `-q -N ""` (pas passphrase) |

---

#### Option `-R` - Supprimer Entrée `known_hosts`

**Rôle** : Retirer clé publique d'un serveur du fichier `known_hosts`.

**Cas d'usage** :
- ✅ Serveur réinstallé (nouvelle clé générée)
- ✅ Adresse IP réutilisée (nouveau serveur)
- ✅ Résoudre avertissement "REMOTE HOST IDENTIFICATION HAS CHANGED"

**Syntaxe** :

```bash
ssh-keygen -R <hostname_or_ip>
```

**Exemples** :

```bash
# Supprimer clé pour 192.168.1.10
$ ssh-keygen -R 192.168.1.10
# Host 192.168.1.10 found: line 5
/home/alice/.ssh/known_hosts updated.
Original contents retained as /home/alice/.ssh/known_hosts.old

# Supprimer clé pour hostname
$ ssh-keygen -R server.example.com

# Supprimer plusieurs entrées (IP + hostname)
$ ssh-keygen -R 192.168.1.10
$ ssh-keygen -R server.example.com
```

**Vérifier suppression** :

```bash
$ grep "192.168.1.10" ~/.ssh/known_hosts
# (aucun résultat = entrée supprimée)
```

**Reconnecter après suppression** :

```bash
$ ssh alice@192.168.1.10
The authenticity of host '192.168.1.10' can't be established.
ED25519 key fingerprint is SHA256:NEW_FINGERPRINT.
Are you sure you want to continue connecting (yes/no)? yes
# ✅ Nouvelle clé acceptée et ajoutée à known_hosts
```

---

### 📂 Emplacements Clés SSH

#### Clés Client (utilisateur)

**Répertoire** : `~/.ssh/`

```bash
$ ls -la ~/.ssh/
drwx------  2 alice alice 4096 Feb 12 10:00 .
-rw-------  1 alice alice 2602 Feb 12 10:00 id_ed25519       # Clé privée
-rw-r--r--  1 alice alice  571 Feb 12 10:00 id_ed25519.pub   # Clé publique
-rw-------  1 alice alice  400 Feb 12 10:00 id_rsa           # Clé privée RSA
-rw-r--r--  1 alice alice   88 Feb 12 10:00 id_rsa.pub       # Clé publique RSA
-rw-------  1 alice alice  444 Feb 12 10:00 authorized_keys  # Clés publiques autorisées (si serveur SSH)
-rw-------  1 alice alice 1234 Feb 12 10:00 known_hosts      # Clés publiques serveurs connus
-rw-------  1 alice alice  156 Feb 12 10:00 config           # Configuration SSH client
```

**Permissions critiques** :

```bash
# Répertoire .ssh : Privé (700)
$ chmod 700 ~/.ssh

# Clés privées : Lecture/écriture owner uniquement (600)
$ chmod 600 ~/.ssh/id_*
$ chmod 600 ~/.ssh/authorized_keys

# Clés publiques : Lisibles par tous (644)
$ chmod 644 ~/.ssh/*.pub

# known_hosts : Privé (600)
$ chmod 600 ~/.ssh/known_hosts
```

---

#### Clés Serveur SSH

**Répertoire** : `/etc/ssh/`

```bash
$ sudo ls -l /etc/ssh/
-rw-------  1 root root  505 Jan 10 10:00 ssh_host_ed25519_key       # Clé privée Ed25519
-rw-r--r--  1 root root   93 Jan 10 10:00 ssh_host_ed25519_key.pub   # Clé publique Ed25519
-rw-------  1 root root  411 Jan 10 10:00 ssh_host_ecdsa_key         # Clé privée ECDSA
-rw-r--r--  1 root root  175 Jan 10 10:00 ssh_host_ecdsa_key.pub     # Clé publique ECDSA
-rw-------  1 root root 2602 Jan 10 10:00 ssh_host_rsa_key           # Clé privée RSA
-rw-r--r--  1 root root  571 Jan 10 10:00 ssh_host_rsa_key.pub       # Clé publique RSA
-rw-r--r--  1 root root 3265 Jan 10 10:00 sshd_config                # Configuration serveur SSH
```

**Générer nouvelles clés serveur** (après réinstallation) :

```bash
# Supprimer anciennes clés
$ sudo rm /etc/ssh/ssh_host_*

# Régénérer toutes les clés
$ sudo ssh-keygen -A
ssh-keygen: generating new host keys: RSA DSA ECDSA ED25519

# Vérifier clés créées
$ sudo ls -l /etc/ssh/ssh_host_*.pub
-rw-r--r-- 1 root root 175 Feb 12 10:00 ssh_host_ecdsa_key.pub
-rw-r--r-- 1 root root  93 Feb 12 10:00 ssh_host_ed25519_key.pub
-rw-r--r-- 1 root root 571 Feb 12 10:00 ssh_host_rsa_key.pub

# Redémarrer SSH
$ sudo systemctl restart sshd
```

**Afficher fingerprints clés serveur** :

```bash
# Tous algorithmes
$ for key in /etc/ssh/ssh_host_*.pub; do ssh-keygen -lf $key; done
256 SHA256:3c8f2d7a... root@server (ECDSA)
256 SHA256:9e2b5f7c... root@server (ED25519)
3072 SHA256:1a6e8b2f... root@server (RSA)
```

---

### 🔐 Authentification par Clés Publiques

#### Principe

**Remplacement mot de passe** par **preuve cryptographique** (possession clé privée).

**Avantages** :
- ✅ Plus sécurisé (pas de mot de passe réseau)
- ✅ Automatisation (scripts, cron sans mot de passe)
- ✅ Résistant brute-force (pas de mot de passe à deviner)
- ✅ Révocable facilement (supprimer clé publique serveur)

**Processus** :

```
CLIENT (Alice)                              SERVEUR (192.168.1.10)
     │
     ├─ Clé privée (~/.ssh/id_ed25519)
     ├─ Clé publique (~/.ssh/id_ed25519.pub)
     │
     ├─────────────────────────────────────►│
     │ 1. Alice : "Je veux me connecter"    │
     │                                       │
     │◄──────────────────────────────────── ├─ 2. Serveur : "Prouve que tu as clé privée"
     │    Challenge aléatoire               │    (envoie challenge chiffré avec clé publique Alice)
     │                                       │
     ├─ 3. Alice déchiffre challenge        │
     │    avec sa clé privée                │
     │                                       │
     ├─────────────────────────────────────►│
     │ 4. Alice renvoie réponse signée      │
     │                                       │
     │                                       ├─ 5. Serveur vérifie signature avec clé publique Alice
     │                                       │    (~alice/.ssh/authorized_keys)
     │                                       │
     │◄──────────────────────────────────── ├─ 6. Signature valide → Accès accordé ✅
     │ Connexion SSH établie                │
```

---

#### Fichier `~/.ssh/authorized_keys` (sur serveur)

**Rôle** : Stocke clés publiques **autorisées** pour l'utilisateur.

**Format** :

```
algorithme clé_publique_base64 commentaire
```

**Exemple** :

```bash
# Sur SERVEUR (192.168.1.10)
$ cat ~alice/.ssh/authorized_keys
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAbCdEfGhIjKlMnOpQrSt... alice@laptop
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC1234abcd... alice@desktop
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIXyZ789efgh... bob@workstation
```

**Ajouter clé publique manuellement** :

```bash
# 1. Sur CLIENT : Afficher clé publique
$ cat ~/.ssh/id_ed25519.pub
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAbCdEfGhIjKlMnOpQrSt... alice@laptop

# 2. Sur SERVEUR : Ajouter à authorized_keys
$ mkdir -p ~/.ssh
$ echo "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAbCdEfGhIjKlMnOpQrSt... alice@laptop" >> ~/.ssh/authorized_keys

# 3. Permissions correctes
$ chmod 700 ~/.ssh
$ chmod 600 ~/.ssh/authorized_keys
```

**Permissions critiques** :

```bash
$ ls -ld ~/.ssh
drwx------ 2 alice alice 4096 Feb 12 10:00 /home/alice/.ssh
#  └─ 700 (rwx------)

$ ls -l ~/.ssh/authorized_keys
-rw------- 1 alice alice 444 Feb 12 10:00 /home/alice/.ssh/authorized_keys
#  └─ 600 (rw-------)
```

⚠️ **Si permissions incorrectes** → SSH ignore `authorized_keys` et demande mot de passe !

---

#### Configuration Serveur `/etc/ssh/sshd_config`

**Activer authentification par clés publiques** :

```bash
$ sudo nano /etc/ssh/sshd_config

# Trouver et décommenter/modifier :
PubkeyAuthentication yes
```

**Autres directives importantes** :

```bash
# Autoriser clés publiques
PubkeyAuthentication yes

# Fichier authorized_keys par défaut
AuthorizedKeysFile .ssh/authorized_keys

# Désactiver mot de passe (forcer clés uniquement) - OPTIONNEL
PasswordAuthentication no

# Désactiver root par mot de passe (root par clé OK)
PermitRootLogin prohibit-password

# Autoriser root par clé
PermitRootLogin yes

# Désactiver complètement root
PermitRootLogin no
```

**Appliquer modifications** :

```bash
# Vérifier syntaxe config
$ sudo sshd -t
# (aucun message = OK)

# Redémarrer SSH
$ sudo systemctl restart sshd
```

---

#### Commande `ssh-copy-id`

**Rôle** : **Automatiser** copie clé publique vers serveur.

**Syntaxe** :

```bash
ssh-copy-id [OPTIONS] user@serveur
```

**Fonctionnement** :
1. Se connecte au serveur (demande mot de passe)
2. Crée répertoire `~/.ssh` si nécessaire
3. Ajoute clé publique à `~/.ssh/authorized_keys`
4. Configure permissions correctes

**Exemple basique** :

```bash
# Copier clé par défaut (~/.ssh/id_rsa.pub ou id_ed25519.pub)
$ ssh-copy-id alice@192.168.1.10
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
alice@192.168.1.10's password: [mot_de_passe_alice]

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'alice@192.168.1.10'"
and check to make sure that only the key(s) you wanted were added.
```

**Tester connexion sans mot de passe** :

```bash
$ ssh alice@192.168.1.10
Welcome to Ubuntu 22.04 LTS
alice@server:~$ 
# ✅ Connecté sans mot de passe !
```

**Options utiles** :

```bash
# Spécifier clé publique
$ ssh-copy-id -i ~/.ssh/id_work.pub alice@192.168.1.10

# Spécifier port SSH non-standard
$ ssh-copy-id -p 2222 alice@192.168.1.10

# Mode dry-run (afficher ce qui serait fait)
$ ssh-copy-id -n alice@192.168.1.10
```

---

### 🔍 Option `-v` - Verbose SSH

**Rôle** : Afficher détails établissement connexion SSH (debug).

**Niveaux verbosité** :

| Option | Niveau | Usage |
|--------|--------|-------|
| `-v` | Verbose | Détails standard (connexion, authentification) |
| `-vv` | Plus verbose | Détails protocole, échanges clés |
| `-vvv` | Maximum verbose | Tous détails (debug complet) |

**Exemple connexion** :

```bash
$ ssh -v alice@192.168.1.10
OpenSSH_8.9p1 Ubuntu-3ubuntu0.1, OpenSSL 3.0.2 15 Mar 2022
debug1: Reading configuration data /home/alice/.ssh/config
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: Connecting to 192.168.1.10 [192.168.1.10] port 22.
debug1: Connection established.
debug1: identity file /home/alice/.ssh/id_ed25519 type 3
debug1: Local version string SSH-2.0-OpenSSH_8.9p1
debug1: Remote protocol version 2.0, remote software version OpenSSH_8.9p1
debug1: SSH2_MSG_KEXINIT sent
debug1: SSH2_MSG_KEXINIT received
debug1: kex: algorithm: curve25519-sha256
debug1: kex: host key algorithm: ssh-ed25519
debug1: Server host key: ssh-ed25519 SHA256:3c8f2d7a6b1e5f9c4d8a...
debug1: Host '192.168.1.10' is known and matches the ED25519 host key.
debug1: Found key in /home/alice/.ssh/known_hosts:5
debug1: Authentications that can continue: publickey,password
debug1: Next authentication method: publickey
debug1: Offering public key: /home/alice/.ssh/id_ed25519 ED25519 SHA256:abcd1234...
debug1: Server accepts key: /home/alice/.ssh/id_ed25519 ED25519 SHA256:abcd1234...
debug1: Authentication succeeded (publickey).
Authenticated to 192.168.1.10 ([192.168.1.10]:22).
debug1: channel 0: new [client-session]
debug1: Entering interactive session.
Welcome to Ubuntu 22.04 LTS
alice@server:~$
```

**Informations utiles dans output** :
- ✅ Fichiers config lus
- ✅ Clés essayées (`identity file`)
- ✅ Algorithmes négociés (`kex`, `host key algorithm`)
- ✅ Méthodes authentification disponibles (`publickey`, `password`)
- ✅ Clé acceptée par serveur
- ✅ Authentification réussie

**Debug problème authentification** :

```bash
$ ssh -vv alice@192.168.1.10
...
debug2: we sent a publickey packet, wait for reply
debug1: Authentications that can continue: publickey,password
debug1: Trying private key: /home/alice/.ssh/id_ed25519
debug2: we did not send a packet, disable method
debug1: Next authentication method: password
# ^^^ Clé refusée, fallback sur mot de passe
```

---

### 🔑 SSH Agent - `ssh-agent` et `ssh-add`

#### Concept `ssh-agent`

**Problème** : Clé privée protégée par passphrase → Demande passphrase à chaque utilisation.

```bash
# Clé protégée par passphrase
$ ssh alice@192.168.1.10
Enter passphrase for key '/home/alice/.ssh/id_ed25519': [passphrase]
alice@server:~$

# Nouvelle connexion
$ ssh alice@192.168.1.20
Enter passphrase for key '/home/alice/.ssh/id_ed25519': [passphrase]
# ^^^ Repetitif !
```

**Solution : `ssh-agent`**

**Rôle** :
- ✅ Garde clés privées **déverrouillées en mémoire**
- ✅ Demande passphrase **une seule fois** au démarrage
- ✅ SSH utilise clés depuis agent (pas de passphrase répétée)
- ✅ Utile jump servers, multiples connexions, forwarding

---

#### Démarrer `ssh-agent`

**Méthode 1 : Session shell** :

```bash
# Démarrer agent
$ eval $(ssh-agent)
Agent pid 12345

# Vérifier agent actif
$ echo $SSH_AUTH_SOCK
/tmp/ssh-XXXXXX/agent.12345
# ^^^ Socket agent disponible
```

**Méthode 2 : Automatique (shell login)** :

```bash
# Ajouter à ~/.bashrc ou ~/.zshrc
if [ -z "$SSH_AUTH_SOCK" ]; then
    eval $(ssh-agent) > /dev/null
fi
```

**Méthode 3 : systemd user service** (moderne) :

```bash
# Activer agent systemd
$ systemctl --user enable ssh-agent.service
$ systemctl --user start ssh-agent.service

# Variable d'environnement automatique
$ echo $SSH_AUTH_SOCK
/run/user/1000/ssh-agent.socket
```

---

#### Commande `ssh-add`

**Rôle** : Ajouter clés privées à l'agent SSH.

**Syntaxe** :

```bash
ssh-add [OPTIONS] [fichier_clé_privée]
```

**Ajouter clé à l'agent** :

```bash
# Ajouter clé par défaut (~/.ssh/id_rsa, id_ed25519, etc.)
$ ssh-add
Enter passphrase for /home/alice/.ssh/id_ed25519: [passphrase]
Identity added: /home/alice/.ssh/id_ed25519 (alice@laptop)

# Ajouter clé spécifique
$ ssh-add ~/.ssh/id_work
Enter passphrase for /home/alice/.ssh/id_work: [passphrase]
Identity added: /home/alice/.ssh/id_work (alice@work)
```

**Lister clés chargées** :

```bash
$ ssh-add -l
256 SHA256:abcd1234efgh5678ijkl9012mnop3456qrst7890 alice@laptop (ED25519)
3072 SHA256:xyz789abcd1234efgh5678ijkl9012mnop3456 alice@work (RSA)
```

**Supprimer clé de l'agent** :

```bash
# Supprimer clé spécifique
$ ssh-add -d ~/.ssh/id_work
Identity removed: /home/alice/.ssh/id_work (alice@work)

# Supprimer toutes les clés
$ ssh-add -D
All identities removed.
```

**Définir durée de vie clé** :

```bash
# Clé expire après 1 heure (3600 secondes)
$ ssh-add -t 3600 ~/.ssh/id_ed25519

# Vérifier temps restant
$ ssh-add -l
256 SHA256:abcd1234... alice@laptop (ED25519) [Expires: Wed Feb 12 11:30:00 2026]
```

**Options utiles** :

| Option | Description | Exemple |
|--------|-------------|---------|
| (aucune) | Ajouter clé(s) par défaut | `ssh-add` |
| `-l` | Lister clés chargées | `ssh-add -l` |
| `-L` | Afficher clés publiques chargées | `ssh-add -L` |
| `-d` | Supprimer clé | `ssh-add -d ~/.ssh/id_work` |
| `-D` | Supprimer toutes clés | `ssh-add -D` |
| `-t` | Durée vie (secondes) | `ssh-add -t 3600` |
| `-x` | Verrouiller agent (passphrase) | `ssh-add -x` |
| `-X` | Déverrouiller agent | `ssh-add -X` |

---

#### Utilisation avec Jump Servers (ProxyJump)

**Scénario** : Connexion à serveur **derrière bastion** (jump host).

```
Laptop (Alice) → Bastion (10.0.0.1) → Serveur Prod (192.168.10.50)
```

**Sans agent** : Clé privée derrière copier sur bastion (DANGEREUX ❌)

**Avec agent + forwarding** : Clé reste sur laptop, forwarding sécurisé ✅

**Configuration** :

```bash
# Connexion avec agent forwarding (-A)
$ ssh-add ~/.ssh/id_ed25519
$ ssh -A alice@10.0.0.1

# Depuis bastion, connexion à serveur prod (utilise clé laptop via forward)
alice@bastion:~$ ssh alice@192.168.10.50
alice@prod-server:~$ 
# ✅ Connecté sans clé privée sur bastion
```

**Ou avec ProxyJump (direct)** :

```bash
$ ssh -J alice@10.0.0.1 alice@192.168.10.50
# ^^^ Connexion directe via bastion (transparent)
```

**Configuration `~/.ssh/config`** :

```bash
$ nano ~/.ssh/config

Host bastion
    HostName 10.0.0.1
    User alice
    ForwardAgent yes

Host prod-server
    HostName 192.168.10.50
    User alice
    ProxyJump bastion

# Utilisation :
$ ssh prod-server
# ✅ Connexion automatique via bastion
```

---

## 🚇 Partie 2 : Tunnels SSH et X11 Forwarding

### 🌉 Tunnels SSH - Concepts

**Rôle** : **Encapsuler** trafic réseau dans connexion SSH chiffrée.

**Usages** :
- ✅ Accéder services internes (base de données, admin web) depuis extérieur
- ✅ Contourner firewalls/NAT
- ✅ Chiffrer trafic non-sécurisé (HTTP, VNC, etc.)
- ✅ Proxy SOCKS pour navigateur

**Types de tunnels** :
1. **Local (`-L`)** : Port local → Serveur SSH → Destination
2. **Remote (`-R`)** : Port serveur SSH → Client → Destination
3. **Dynamique (`-D`)** : Proxy SOCKS

---

### 🔵 Tunnel Local (`-L`)

#### Syntaxe

```bash
ssh -L [bind_address:]local_port:destination_host:destination_port user@ssh_server
```

**Simplifié** :

```bash
ssh -L local_port:destination:destination_port user@serveur_ssh
```

---

#### Exemple 1 : Accéder Base de Données Distante

**Scénario** :
- Serveur MySQL sur `192.168.10.50:3306` (réseau interne, pas accessible depuis Internet)
- Serveur SSH sur `ssh.example.com` (accessible, même réseau que MySQL)
- Laptop local veut accéder MySQL via tunnel SSH

**Commande** :

```bash
$ ssh -L 9000:192.168.10.50:3306 alice@ssh.example.com
```

**Schéma** :

```
┌──────────────────┐
│   Laptop Local   │
│                  │
│ localhost:9000   │◄── Application MySQL Workbench
└────────┬─────────┘
         │
         │ Tunnel SSH chiffré
         │
         ▼
┌──────────────────┐
│ ssh.example.com  │
│  (Serveur SSH)   │
└────────┬─────────┘
         │
         │ Connexion réseau interne
         │
         ▼
┌──────────────────┐
│ 192.168.10.50    │
│   MySQL :3306    │
└──────────────────┘
```

**Utilisation** :

```bash
# 1. Créer tunnel
$ ssh -L 9000:192.168.10.50:3306 alice@ssh.example.com
alice@ssh-server:~$ 
# ^^^ Shell SSH actif, tunnel en arrière-plan

# 2. Depuis laptop (autre terminal), se connecter à localhost:9000
$ mysql -h 127.0.0.1 -P 9000 -u root -p
Enter password: [password_mysql]
mysql>
# ✅ Connecté à MySQL distant via tunnel SSH chiffré
```

---

#### Exemple 2 : Accéder Service Web Interne

**Commande** :

```bash
$ ssh -L 8080:google.com:80 alice@ssh-server.example.com
```

**Utilisation** :

```bash
# Ouvrir navigateur : http://localhost:8080
# → Redirigé vers google.com:80 via ssh-server.example.com
```

**Schéma** :

```
Navigateur → localhost:8080 → Tunnel SSH → ssh-server.example.com → google.com:80
```

---

#### Bind Address (Interface Écoute)

**Par défaut** : Tunnel écoute `127.0.0.1` (localhost uniquement).

**Autoriser autres machines réseau local** :

```bash
# Écouter toutes interfaces (0.0.0.0)
$ ssh -L 0.0.0.0:9000:192.168.10.50:3306 alice@ssh-server.example.com

# Écouter interface spécifique
$ ssh -L 192.168.1.100:9000:192.168.10.50:3306 alice@ssh-server.example.com
```

**Maintenant, autres machines LAN peuvent utiliser tunnel** :

```bash
# Depuis autre machine LAN (192.168.1.200)
$ mysql -h 192.168.1.100 -P 9000 -u root -p
# ✅ Connexion MySQL via tunnel sur machine 192.168.1.100
```

---

### 🔴 Tunnel Remote (`-R`)

#### Syntaxe

```bash
ssh -R [bind_address:]remote_port:destination_host:destination_port user@ssh_server
```

**Simplifié** :

```bash
ssh -R remote_port:localhost:local_port user@serveur_ssh
```

---

#### Exemple 1 : Exposer Service Local

**Scénario** :
- Serveur web local (laptop) sur `localhost:80`
- Vouloir partager à client distant via serveur SSH public

**Commande** :

```bash
$ ssh -R 8000:localhost:80 alice@ssh-server.example.com
```

**Schéma** :

```
┌──────────────────┐
│   Laptop Local   │
│                  │
│  localhost:80    │◄── Apache/Nginx
└────────▲─────────┘
         │
         │ Tunnel SSH (reverse)
         │
         ▼
┌──────────────────┐
│ ssh.example.com  │
│                  │
│ localhost:8000   │◄── Utilisateurs se connectent ici
└──────────────────┘
```

**Utilisation** :

```bash
# 1. Créer tunnel reverse
$ ssh -R 8000:localhost:80 alice@ssh-server.example.com
alice@ssh-server:~$

# 2. Sur serveur SSH, vérifier port 8000 écoute
alice@ssh-server:~$ ss -tuln | grep 8000
tcp   LISTEN 0  128  127.0.0.1:8000   0.0.0.0:*

# 3. Accéder depuis serveur SSH
alice@ssh-server:~$ curl http://localhost:8000
# ✅ Contenu du serveur web laptop affiché
```

---

#### Exemple 2 : Bind Address Remote

**Par défaut** : Port remote écoute `127.0.0.1` (localhost serveur uniquement).

**Écouter toutes interfaces serveur** :

```bash
$ ssh -R 0.0.0.0:8000:localhost:80 alice@ssh-server.example.com
```

⚠️ **Configuration serveur requise** : `/etc/ssh/sshd_config`

```bash
$ sudo nano /etc/ssh/sshd_config

# Ajouter/décommenter :
GatewayPorts clientspecified
# Ou :
GatewayPorts yes

# Redémarrer SSH
$ sudo systemctl restart sshd
```

**Options `GatewayPorts`** :

| Valeur | Comportement |
|--------|--------------|
| `no` | Défaut. Port remote écoute **localhost** uniquement |
| `yes` | Port remote écoute **toutes interfaces** (`0.0.0.0`) |
| `clientspecified` | Client peut spécifier bind address (`0.0.0.0` ou `127.0.0.1`) |

⚠️ **Sécurité** : `GatewayPorts yes/clientspecified` = **Pas sécuritaire** (expose ports publiquement).

---

#### Exemple 3 : Interface Spécifique

```bash
# Écouter seulement localhost serveur
$ ssh -R 127.0.0.1:8000:localhost:80 alice@ssh-server.example.com

# Écouter toutes interfaces serveur
$ ssh -R 0.0.0.0:8000:localhost:80 alice@ssh-server.example.com

# Écouter IP spécifique serveur
$ ssh -R 192.168.1.50:8000:localhost:80 alice@ssh-server.example.com
```

---

### 🟣 Tunnel Dynamique (`-D`) - Proxy SOCKS

#### Syntaxe

```bash
ssh -D [bind_address:]port user@serveur_ssh
```

**Simplifié** :

```bash
ssh -D port user@serveur_ssh
```

---

#### Concept

**Proxy SOCKS** : Tunnel qui accepte **toute connexion TCP** dynamiquement.

**Usages** :
- ✅ Navigateur web via proxy (chiffrer HTTP, contourner géo-restrictions)
- ✅ `proxychains` (router applications via tunnel SSH)
- ✅ Torrent via tunnel SSH

---

#### Exemple 1 : Navigateur via Proxy SOCKS

**Commande** :

```bash
$ ssh -D 1080 alice@ssh-server.example.com
alice@ssh-server:~$
# ^^^ Tunnel SOCKS actif sur localhost:1080
```

**Configuration navigateur (Firefox)** :

```
Paramètres → Réseau → Paramètres de connexion
  ○ Pas de proxy
  ● Configuration manuelle du proxy
  
  Hôte SOCKS : 127.0.0.1
  Port : 1080
  ☑ SOCKS v5
  ☑ DNS par proxy SOCKS5 (important pour vie privée)
```

**Utilisation** :

```
Navigateur → localhost:1080 (proxy SOCKS) → Tunnel SSH → ssh-server → Internet
```

**Vérifier IP sortante** :

```
http://ifconfig.me
# Affiche IP de ssh-server.example.com (pas IP laptop)
```

---

#### Exemple 2 : `proxychains`

**Rôle** : Router applications via proxy SOCKS.

**Installation** :

```bash
$ sudo apt install proxychains4
```

**Configuration** :

```bash
$ sudo nano /etc/proxychains4.conf

# Ajouter à la fin :
[ProxyList]
socks5 127.0.0.1 1080
```

**Créer tunnel** :

```bash
$ ssh -D 1080 alice@ssh-server.example.com
```

**Utiliser proxychains** :

```bash
# Router curl via tunnel SSH
$ proxychains4 curl http://ifconfig.me
[proxychains] config file found: /etc/proxychains4.conf
[proxychains] Strict chain ... 127.0.0.1:1080 ... ifconfig.me:80 ... OK
203.0.113.50  # IP du serveur SSH
#  ^^^
#  Trafic passé par tunnel

# Router nmap via tunnel
$ proxychains4 nmap -sT 192.168.10.0/24

# Router Firefox via tunnel (ligne commande)
$ proxychains4 firefox
```

---

### 🖥️ X11 Forwarding

#### Concept

**X11** : Système fenêtrage graphique Linux/Unix.

**X11 Forwarding** : Exécuter application graphique **distante** avec affichage **local**.

**Fonctionnement** :

```
┌──────────────────┐
│   Laptop Local   │
│                  │
│  Serveur X11     │◄── Affiche fenêtre graphique
│  (affichage)     │
└────────▲─────────┘
         │
         │ Tunnel SSH (X11 forwarding)
         │
         ▼
┌──────────────────┐
│ Serveur Distant  │
│                  │
│  Firefox         │◄── Application exécutée ici
│  (processus)     │
└──────────────────┘
```

**Avantages** :
- ✅ Ressources distantes (CPU, RAM serveur)
- ✅ Affichage local (confortable)
- ✅ Trafic chiffré SSH

---

#### Configuration

**Prérequis** :
- ✅ Serveur X11 local (généralement installé si environnement graphique)
- ✅ Application graphique distante
- ✅ X11 forwarding activé serveur SSH

**Configuration serveur** (`/etc/ssh/sshd_config`) :

```bash
$ sudo nano /etc/ssh/sshd_config

# Activer X11 forwarding
X11Forwarding yes

# Optionnel : Adresse écoute X11
X11DisplayOffset 10

# Redémarrer SSH
$ sudo systemctl restart sshd
```

---

#### Utilisation

**Commande** :

```bash
ssh -X user@serveur
```

**Options** :

| Option | Description |
|--------|-------------|
| `-X` | X11 forwarding basique |
| `-Y` | X11 forwarding **trusted** (moins sécurisé, moins restrictions) |

**Exemple** :

```bash
# Connexion avec X11 forwarding
$ ssh -X alice@192.168.1.10
alice@server:~$

# Vérifier variable DISPLAY
alice@server:~$ echo $DISPLAY
localhost:10.0
#  ^^^
#  X11 forwarding actif

# Lancer application graphique distante
alice@server:~$ firefox &
# ✅ Firefox s'affiche sur laptop local (exécuté sur serveur)

# Autre exemple : gedit
alice@server:~$ gedit fichier.txt &
# ✅ Éditeur graphique affiché localement
```

**Vérifier forwarding** :

```bash
# Sans X11 forwarding
$ ssh alice@192.168.1.10
alice@server:~$ echo $DISPLAY
# (vide = pas de forwarding)

alice@server:~$ firefox
Error: cannot open display
# ❌ Échec

# Avec X11 forwarding
$ ssh -X alice@192.168.1.10
alice@server:~$ echo $DISPLAY
localhost:10.0
# ✅ Forwarding actif
```

---

### ⚙️ Options Tunnels Utiles

#### Option `-N` - No Shell

**Rôle** : Créer tunnel **sans** ouvrir shell interactif.

**Usage** : Tunnels dédiés (pas besoin terminal distant).

**Exemple** :

```bash
# Tunnel local sans shell
$ ssh -N -L 9000:192.168.10.50:3306 alice@ssh-server.example.com
# ^^^ Connexion établie, tunnel actif, MAIS pas de prompt shell
# Terminal bloqué (Ctrl+C pour arrêter tunnel)
```

---

#### Option `-f` - Background

**Rôle** : Envoyer SSH en **arrière-plan** après connexion.

**Usage** : Tunnel persistent sans monopoliser terminal.

**Exemple** :

```bash
# Tunnel en arrière-plan
$ ssh -f -N -L 9000:192.168.10.50:3306 alice@ssh-server.example.com
# ^^^ Tunnel créé, SSH en background, terminal libre

# Vérifier processus
$ ps aux | grep "ssh -f"
alice  12345  0.0  0.1  ssh -f -N -L 9000:192.168.10.50:3306 alice@ssh-server.example.com

# Utiliser tunnel
$ mysql -h 127.0.0.1 -P 9000 -u root -p

# Tuer tunnel
$ kill 12345
```

---

#### Combinaisons Pratiques

```bash
# Tunnel local en background
$ ssh -f -N -L 9000:db.internal:3306 alice@bastion.example.com

# Tunnel remote en background
$ ssh -f -N -R 8000:localhost:80 alice@public-server.example.com

# Proxy SOCKS en background
$ ssh -f -N -D 1080 alice@proxy-server.example.com

# X11 forwarding avec verbose (debug)
$ ssh -X -v alice@app-server.example.com

# Plusieurs tunnels simultanés
$ ssh -L 9000:db1.internal:3306 -L 9001:db2.internal:3306 alice@bastion.example.com
```

---

## 🔐 Partie 3 : GPG - Chiffrement et Signature

### 🔑 Introduction GPG

**GPG** (GNU Privacy Guard) : Implémentation libre de **OpenPGP** (Pretty Good Privacy).

**Rôle** :
- ✅ **Chiffrer** emails, fichiers (confidentialité)
- ✅ **Signer** documents (authenticité, intégrité)
- ✅ Gestion clés (génération, import/export, révocation)

**Différence SSH vs GPG** :

| Aspect | SSH | GPG |
|--------|-----|-----|
| **Usage principal** | Connexions distantes sécurisées | Chiffrement/signature fichiers, emails |
| **Clés** | Paire SSH (client/serveur) | Paire PGP (personnelle, échange emails) |
| **Algorithmes** | RSA, Ed25519, ECDSA | RSA, DSA, ElGamal |
| **Certificats** | known_hosts | Web of Trust (réseau confiance) |

---

### 📋 Lister Clés - `gpg --list-keys`

**Rôle** : Afficher clés publiques dans trousseau GPG.

**Commande** :

```bash
$ gpg --list-keys
# Ou version courte :
$ gpg -k
```

**Exemple sortie** :

```
/home/alice/.gnupg/pubring.kbx
--------------------------------
pub   rsa3072 2026-02-12 [SC] [expires: 2028-02-12]
      1234567890ABCDEF1234567890ABCDEF12345678
uid           [ultimate] Alice Dupont <alice@example.com>
sub   rsa3072 2026-02-12 [E] [expires: 2028-02-12]
```

**Explications** :

```
pub   rsa3072 2026-02-12 [SC] [expires: 2028-02-12]
│     │       │          │     └─ Date expiration
│     │       │          └─ Usage : S=Sign, C=Certify
│     │       └─ Date création
│     └─ Algorithme + taille (RSA 3072 bits)
└─ Type : pub = Clé publique principale

      1234567890ABCDEF1234567890ABCDEF12345678
      └─ Fingerprint (empreinte unique clé)

uid           [ultimate] Alice Dupont <alice@example.com>
│             │          └─ Identité (nom + email)
│             └─ Niveau confiance : ultimate, full, marginal, unknown
└─ User ID

sub   rsa3072 2026-02-12 [E] [expires: 2028-02-12]
│     └─ Usage : E=Encrypt (sous-clé chiffrement)
└─ Sous-clé
```

**Lister clés privées** :

```bash
$ gpg --list-secret-keys
# Ou version courte :
$ gpg -K
```

---

### 🔨 Générer Paire de Clés - `gpg --gen-key`

**Rôle** : Créer nouvelle paire de clés GPG (publique + privée).

**Commande interactive** :

```bash
$ gpg --gen-key
gpg (GnuPG) 2.2.27; Copyright (C) 2021 Free Software Foundation, Inc.

GnuPG needs to construct a user ID to identify your key.

Real name: Alice Dupont
Email address: alice@example.com
You selected this USER-ID:
    "Alice Dupont <alice@example.com>"

Change (N)ame, (E)mail, or (O)kay/(Q)uit? O

We need to generate a lot of entropy. Please do other work during this process.
```

**Génération entropie** :

```
gpg: key 1234567890ABCDEF marked as ultimately trusted
gpg: revocation certificate stored as '/home/alice/.gnupg/openpgp-revocs.d/1234567890ABCDEF.rev'
public and secret key created and signed.

pub   rsa3072 2026-02-12 [SC] [expires: 2028-02-12]
      1234567890ABCDEF1234567890ABCDEF12345678
uid                      Alice Dupont <alice@example.com>
sub   rsa3072 2026-02-12 [E] [expires: 2028-02-12]
```

**Entropie** : Données aléatoires nécessaires pour générer clés sécurisées.

**Sources entropie** :
- ✅ Mouvements souris
- ✅ Frappes clavier
- ✅ Activité disque
- ✅ `/dev/random`, `/dev/urandom`

**Accélérer génération entropie** :

```bash
# Terminal 1 : Générer clés
$ gpg --gen-key

# Terminal 2 : Générer activité système
$ find / -type f 2>/dev/null > /dev/null &
$ dd if=/dev/zero of=/tmp/test.img bs=1M count=100
$ ls -R / > /dev/null
```

---

#### Version Complète - `--full-gen-key`

**Plus d'options** (algorithme, taille, expiration) :

```bash
$ gpg --full-gen-key
gpg (GnuPG) 2.2.27; Copyright (C) 2021 Free Software Foundation, Inc.

Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
   (9) ECC and ECC
  (10) ECC (sign only)
Your selection? 1

RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (3072) 4096

Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 2y

Real name: Alice Dupont
Email address: alice@example.com
Comment: Work key
You selected this USER-ID:
    "Alice Dupont (Work key) <alice@example.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O

Enter passphrase: [passphrase_forte]
Repeat passphrase: [passphrase_forte]

# Génération clés...
```

**Recommandations** :
- ✅ RSA 4096 bits (maximum sécurité)
- ✅ Expiration 2-3 ans (renouvelable)
- ✅ Passphrase forte (protège clé privée)

---

### 📤 Exporter Clé Publique - `gpg --export`

**Rôle** : Extraire clé publique pour partager avec autres.

**Syntaxe** :

```bash
gpg --export [OPTIONS] <user_id>
```

---

#### Export Binaire (par défaut)

```bash
# Export binaire
$ gpg --export alice@example.com > alice.key.pub

# Vérifier fichier (binaire)
$ file alice.key.pub
alice.key.pub: OpenPGP Public Key Version 4
```

---

#### Export ASCII - Option `-a` / `--armor`

**Format ASCII** : Lisible texte (emails, web).

```bash
# Export ASCII
$ gpg -a --export alice@example.com > alice.key.pub.asc
# Ou :
$ gpg --armor --export alice@example.com > alice.key.pub.asc

# Vérifier fichier (ASCII)
$ cat alice.key.pub.asc
-----BEGIN PGP PUBLIC KEY BLOCK-----

mQGNBGLR3ZYBDAC1234abcd...
...Long string Base64...
=abc1
-----END PGP PUBLIC KEY BLOCK-----
```

**Avantages ASCII** :
- ✅ Copiable/collable (emails, forum)
- ✅ Compatible tous systèmes
- ✅ Lisible humains (headers visibles)

---

#### Publier Clé Publique

**Serveurs de clés publiques** :

```bash
# Publier sur serveur clés Ubuntu
$ gpg --keyserver keyserver.ubuntu.com --send-keys 1234567890ABCDEF

# Publier sur serveur clés MIT
$ gpg --keyserver pgp.mit.edu --send-keys 1234567890ABCDEF

# Rechercher clé publique
$ gpg --keyserver keyserver.ubuntu.com --search-keys alice@example.com
```

---

### 📥 Importer Clé Publique - `gpg --import`

**Rôle** : Ajouter clé publique d'un tiers à trousseau GPG.

**Syntaxe** :

```bash
gpg --import <fichier_clé_publique>
```

**Exemples** :

```bash
# Importer clé binaire
$ gpg --import bob.key.pub
gpg: key ABCDEF1234567890: public key "Bob Martin <bob@example.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1

# Importer clé ASCII
$ gpg --import charlie.key.pub.asc
```

**Vérifier import** :

```bash
$ gpg --list-keys bob@example.com
pub   rsa3072 2026-02-12 [SC] [expires: 2028-02-12]
      ABCDEF1234567890ABCDEF1234567890ABCDEF12
uid           [ unknown] Bob Martin <bob@example.com>
sub   rsa3072 2026-02-12 [E] [expires: 2028-02-12]
```

**Niveau confiance** `[ unknown]` → Vérifier fingerprint avant utiliser.

**Vérifier fingerprint** :

```bash
# Afficher fingerprint clé importée
$ gpg --fingerprint bob@example.com
pub   rsa3072 2026-02-12 [SC] [expires: 2028-02-12]
      ABCD EF12 3456 7890 ABCD  EF12 3456 7890 ABCD EF12
uid           [ unknown] Bob Martin <bob@example.com>
sub   rsa3072 2026-02-12 [E] [expires: 2028-02-12]

# Comparer avec fingerprint communiqué par Bob (téléphone, en personne)
```

---

### 🔒 Chiffrer Fichier - `gpg --encrypt`

**Rôle** : Chiffrer fichier avec clé publique destinataire.

**Syntaxe** :

```bash
gpg --output <fichier_chiffré> --encrypt --recipient <destinataire> <fichier_clair>
```

**Exemples** :

```bash
# Chiffrer fichier pour Bob
$ gpg --output message.txt.gpg --encrypt --recipient bob@example.com message.txt

# Version courte
$ gpg -o message.txt.gpg -e -r bob@example.com message.txt

# Vérifier fichier chiffré (binaire)
$ file message.txt.gpg
message.txt.gpg: GPG encrypted data

# Impossible lire sans clé privée Bob
$ cat message.txt.gpg
�▒▒▒�▒▒�▒▒▒...binaire illisible...
```

**Chiffrer avec ASCII armor** :

```bash
$ gpg -a -o message.txt.asc -e -r bob@example.com message.txt

$ cat message.txt.asc
-----BEGIN PGP MESSAGE-----

hQGMA1234abcd...
...Base64 chiffré...
=xyz9
-----END PGP MESSAGE-----
```

**Chiffrer pour plusieurs destinataires** :

```bash
$ gpg -o secret.txt.gpg -e -r alice@example.com -r bob@example.com -r charlie@example.com secret.txt
# ✅ Alice, Bob ET Charlie peuvent déchiffrer
```

---

### 🔓 Déchiffrer Fichier - `gpg --decrypt`

**Rôle** : Déchiffrer fichier chiffré avec clé privée.

**Syntaxe** :

```bash
gpg --output <fichier_clair> --decrypt <fichier_chiffré>
```

**Exemples** :

```bash
# Déchiffrer fichier
$ gpg --output message.txt --decrypt message.txt.gpg
gpg: encrypted with 3072-bit RSA key, ID ABCDEF1234567890, created 2026-02-12
      "Bob Martin <bob@example.com>"
Enter passphrase: [passphrase_clé_privée_bob]

# ✅ message.txt restauré (clair)
$ cat message.txt
Ceci est un message secret pour Bob.
```

**Version courte** :

```bash
$ gpg -o message.txt -d message.txt.gpg
```

**Afficher contenu sans sauvegarder** :

```bash
# Déchiffrer vers stdout
$ gpg -d message.txt.gpg
gpg: encrypted with 3072-bit RSA key, ID ABCDEF1234567890
Ceci est un message secret pour Bob.
```

---

### ✍️ Signer Fichier - `gpg --sign`

**Rôle** : Signer fichier avec clé privée (prouver authorship).

**Syntaxe** :

```bash
gpg --output <fichier_signé> --sign <fichier>
```

**Exemples** :

```bash
# Signer fichier
$ gpg --output document.txt.sig --sign document.txt
Enter passphrase: [passphrase_clé_privée_alice]

# Fichier signé (binaire, compressé + signature)
$ file document.txt.sig
document.txt.sig: GPG signed data
```

**⚠️ Important** : Fichier original **remplacé** par version signée compressée.

---

#### Signature Détachée - `--detach-sign`

**Rôle** : Créer fichier signature **séparé** (garde fichier original intact).

```bash
# Signature détachée
$ gpg --detach-sign document.txt
# Crée : document.txt.sig (signature seule)

# Fichier original intact
$ ls
document.txt
document.txt.sig

# Vérifier signature
$ gpg --verify document.txt.sig document.txt
gpg: Signature made Wed Feb 12 10:00:00 2026 CET
gpg:                using RSA key 1234567890ABCDEF
gpg: Good signature from "Alice Dupont <alice@example.com>" [ultimate]
```

---

#### Signature Texte Clair - `--clearsign`

**Rôle** : Signer fichier **texte** en gardant contenu **lisible**.

```bash
# Signature texte clair
$ gpg --output document.txt.asc --clearsign document.txt

# Contenu toujours lisible
$ cat document.txt.asc
-----BEGIN PGP SIGNED MESSAGE-----
Hash: SHA256

Ceci est le contenu du document en clair.
Il reste parfaitement lisible.
-----BEGIN PGP SIGNATURE-----

iQGzBAEBCAAdFiEE...
...Signature Base64...
=abc1
-----END PGP SIGNATURE-----
```

**Usage** : Emails signés, annonces publiques (signature visible).

---

### ✅ Vérifier Signature - `gpg --verify`

**Rôle** : Vérifier authenticité et intégrité fichier signé.

**Syntaxe signature détachée** :

```bash
gpg --verify <fichier_signature> <fichier_original>
```

**Syntaxe signature intégrée** :

```bash
gpg --verify <fichier_signé>
```

**Exemples** :

```bash
# Vérifier signature détachée
$ gpg --verify document.txt.sig document.txt
gpg: Signature made Wed Feb 12 10:00:00 2026 CET
gpg:                using RSA key 1234567890ABCDEF1234567890ABCDEF12345678
gpg: Good signature from "Alice Dupont <alice@example.com>" [ultimate]
# ✅ Signature valide

# Vérifier clearsign
$ gpg --verify document.txt.asc
gpg: Signature made Wed Feb 12 10:30:00 2026 CET
gpg:                using RSA key 1234567890ABCDEF
gpg: Good signature from "Alice Dupont <alice@example.com>" [ultimate]
```

**Extraire contenu depuis signature** (fichier signé complet) :

```bash
$ gpg --output document.txt --decrypt document.txt.sig
gpg: Signature made Wed Feb 12 10:00:00 2026 CET
gpg: Good signature from "Alice Dupont <alice@example.com>" [ultimate]

# document.txt restauré avec vérification signature
```

**États signature** :

```bash
# ✅ Signature valide
gpg: Good signature from "Alice Dupont <alice@example.com>" [ultimate]

# ❌ Signature invalide (fichier modifié)
gpg: BAD signature from "Alice Dupont <alice@example.com>" [ultimate]

# ⚠️ Clé publique manquante
gpg: Can't check signature: No public key

# ⚠️ Clé expirée
gpg: Good signature from "Alice Dupont <alice@example.com>" [expired]
```

---

### 🚫 Révoquer Clé - `gpg --gen-revoke`

**Rôle** : Générer **certificat de révocation** (invalider clé publique).

**Cas d'usage** :
- ✅ Clé privée compromise (volée, perdue)
- ✅ Passphrase oubliée (clé inutilisable)
- ✅ Clé expirée non renouvelable

---

#### Générer Certificat Révocation

```bash
$ gpg --output alice.revoke.key --gen-revoke alice@example.com

sec  rsa3072/1234567890ABCDEF 2026-02-12 Alice Dupont <alice@example.com>

Create a revocation certificate for this key? (y/N) y
Please select the reason for the revocation:
  0 = No reason specified
  1 = Key has been compromised
  2 = Key is superseded
  3 = Key is no longer used
Your decision? 1

Enter an optional description; end it with an empty line:
> Clé compromise suite incident sécurité serveur.
> 
Reason for revocation: Key has been compromised
Clé compromise suite incident sécurité serveur.
Is this okay? (y/N) y
Enter passphrase: [passphrase_clé]

Revocation certificate created.

# ✅ Certificat révocation créé : alice.revoke.key
```

**⚠️ Important** : **Stocker certificat révocation en lieu sûr** (backup, coffre-fort numérique).

---

#### Publier Révocation

**Importer certificat révocation** :

```bash
$ gpg --import alice.revoke.key
gpg: key 1234567890ABCDEF: "Alice Dupont <alice@example.com>" revocation certificate imported
gpg: Total number processed: 1
gpg:    new key revocations: 1
```

**Publier sur serveurs clés** :

```bash
$ gpg --keyserver keyserver.ubuntu.com --send-keys 1234567890ABCDEF
# ✅ Clé révoquée publiée
```

**Vérifier révocation** :

```bash
$ gpg --list-keys alice@example.com
pub   rsa3072 2026-02-12 [SC] [revoked: 2026-02-12]
#                               └─ Révoquée
      1234567890ABCDEF1234567890ABCDEF12345678
uid           [ revoked] Alice Dupont <alice@example.com>
```

---

### 🔑 GPG Agent - `gpg-agent`

#### Concept

**Problème** : Clé privée GPG protégée par passphrase → Demande passphrase à chaque opération.

```bash
$ gpg -d secret1.txt.gpg > secret1.txt
Enter passphrase: [passphrase]

$ gpg -d secret2.txt.gpg > secret2.txt
Enter passphrase: [passphrase]
# ^^^ Répétitif !
```

**Solution : `gpg-agent`**

**Rôle** :
- ✅ Garde clé privée **déverrouillée en mémoire**
- ✅ Demande passphrase **une seule fois**
- ✅ Cache passphrase durée configurable (défaut 10 minutes)
- ✅ Auto-verrouillage après inactivité

---

#### Démarrer GPG Agent

**Automatique** : Généralement démarré automatiquement par GPG moderne.

**Vérifier agent actif** :

```bash
$ echo $GPG_AGENT_INFO
/run/user/1000/gnupg/S.gpg-agent:12345:1
#  ^^^
#  Agent actif

# Ou :
$ gpg-connect-agent --help
# (Si agent actif, commande répond)
```

**Démarrer manuellement** :

```bash
$ gpg-agent --daemon
GPG_AGENT_INFO=/tmp/gpg-XXXXXX/S.gpg-agent:12345:1; export GPG_AGENT_INFO;
```

---

#### Configuration

**Fichier** : `~/.gnupg/gpg-agent.conf`

```bash
$ nano ~/.gnupg/gpg-agent.conf

# Durée cache passphrase (secondes)
default-cache-ttl 3600      # 1 heure
max-cache-ttl 86400         # 24 heures max

# Demander passphrase via GUI (pinentry)
pinentry-program /usr/bin/pinentry-gtk-2

# Recharger agent
$ gpg-connect-agent reloadagent /bye
OK
```

---

#### Utilisation

**Avec agent actif** :

```bash
# 1ère opération : Demande passphrase
$ gpg -d secret1.txt.gpg > secret1.txt
Enter passphrase: [passphrase_une_seule_fois]

# Opérations suivantes : Pas de passphrase (cache)
$ gpg -d secret2.txt.gpg > secret2.txt
# ✅ Déchiffré sans passphrase

$ gpg -d secret3.txt.gpg > secret3.txt
# ✅ Déchiffré sans passphrase

# Après 1 heure (default-cache-ttl) → Redemande passphrase
```

**Vider cache agent** (déverrouillage immédiat) :

```bash
$ gpg-connect-agent reloadagent /bye
```

---

## 🛠️ Scénarios Pratiques

### Scénario 1 : Connexion SSH sans Mot de Passe

```bash
# 1. Générer paire clés Ed25519
$ ssh-keygen -t ed25519 -C "alice@laptop"
Enter file: [ENTER]
Enter passphrase: [passphrase_forte]

# 2. Copier clé publique sur serveur
$ ssh-copy-id alice@192.168.1.10
alice@192.168.1.10's password: [mot_de_passe_alice_une_dernière_fois]

# 3. Démarrer ssh-agent et ajouter clé
$ eval $(ssh-agent)
$ ssh-add ~/.ssh/id_ed25519
Enter passphrase: [passphrase_une_seule_fois]

# 4. Se connecter (pas de mot de passe !)
$ ssh alice@192.168.1.10
alice@server:~$ 
# ✅ Connecté sans passphrase
```

---

### Scénario 2 : Tunnel Local pour Accès Base de Données

```bash
# Contexte : MySQL sur 10.0.10.50:3306 (inaccessible depuis laptop)
#            Bastion SSH sur bastion.example.com (accessible)

# 1. Créer tunnel local en background
$ ssh -f -N -L 9000:10.0.10.50:3306 alice@bastion.example.com

# 2. Se connecter à MySQL via tunnel
$ mysql -h 127.0.0.1 -P 9000 -u admin -p
Enter password: [password_mysql]
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| production_db      |
+--------------------+
# ✅ Connecté à MySQL distant via tunnel SSH chiffré

# 3. Tuer tunnel quand fini
$ pkill -f "ssh -f -N -L 9000"
```

---

### Scénario 3 : Proxy SOCKS pour Navigation Sécurisée

```bash
# 1. Créer tunnel SOCKS en background
$ ssh -f -N -D 1080 alice@vpn-server.example.com

# 2. Configurer Firefox :
#    Paramètres → Réseau → Proxy manuel
#    SOCKS : 127.0.0.1:1080
#    SOCKS v5 ☑
#    DNS par SOCKS ☑

# 3. Vérifier IP sortante
$ firefox http://ifconfig.me
# Affiche IP de vpn-server.example.com (pas laptop)

# 4. Tuer tunnel
$ pkill -f "ssh -f -N -D 1080"
```

---

### Scénario 4 : Chiffrer et Signer Email avec GPG

```bash
# 1. Générer clés GPG
$ gpg --full-gen-key
# (RSA 4096, expiration 2 ans, passphrase forte)

# 2. Exporter clé publique Alice
$ gpg -a --export alice@example.com > alice_public.asc

# 3. Envoyer alice_public.asc à Bob (email, web)

# 4. Bob importe clé publique Alice
$ gpg --import alice_public.asc

# 5. Alice chiffre + signe email pour Bob
$ gpg -a -o message.asc --encrypt --sign --recipient bob@example.com message.txt
Enter passphrase: [passphrase_alice]

# 6. Alice envoie message.asc à Bob

# 7. Bob déchiffre + vérifie signature
$ gpg -o message.txt -d message.asc
gpg: encrypted with 3072-bit RSA key, ID BOBKEY, created 2026-02-12
gpg: Signature made Wed Feb 12 10:00:00 2026 CET
gpg: Good signature from "Alice Dupont <alice@example.com>" [full]
# ✅ Message chiffré ET authentifié
```

---

### Scénario 5 : Révocation Clé Compromise

```bash
# Situation : Clé privée Alice volée !

# 1. Générer certificat révocation immédiatement
$ gpg --output alice.revoke.asc --gen-revoke alice@example.com
Reason: 1 (Key has been compromised)
Description: Clé volée incident sécurité 12/02/2026

# 2. Importer révocation
$ gpg --import alice.revoke.asc

# 3. Publier révocation serveurs clés
$ gpg --keyserver keyserver.ubuntu.com --send-keys ALICE_KEY_ID

# 4. Informer contacts (email, annonce)
# 5. Générer NOUVELLE paire clés
$ gpg --full-gen-key

# 6. Publier nouvelle clé
$ gpg -a --export alice@example.com > alice_new_public.asc
$ gpg --keyserver keyserver.ubuntu.com --send-keys NEW_KEY_ID
```

---

## ❌ Erreurs Courantes et Solutions

### Erreur 1 : Permissions `authorized_keys` Incorrectes

```bash
# SSH demande toujours mot de passe malgré clé publique configurée

# Cause : Permissions trop permissives
$ ls -l ~/.ssh/authorized_keys
-rw-rw-rw- 1 alice alice 444 Feb 12 10:00 /home/alice/.ssh/authorized_keys
#  └─ 666 trop permissif ! SSH ignore fichier
```

**Solution** :

```bash
$ chmod 700 ~/.ssh
$ chmod 600 ~/.ssh/authorized_keys

# Vérifier
$ ls -ld ~/.ssh
drwx------ 2 alice alice 4096 Feb 12 10:00 /home/alice/.ssh
$ ls -l ~/.ssh/authorized_keys
-rw------- 1 alice alice 444 Feb 12 10:00 /home/alice/.ssh/authorized_keys
```

---

### Erreur 2 : Tunnel SSH Port Déjà Utilisé

```bash
$ ssh -L 9000:db.internal:3306 alice@bastion.example.com
bind: Address already in use
channel_setup_fwd_listener_tcpip: cannot listen to port: 9000
```

**Cause** : Port local 9000 déjà utilisé.

**Solution** :

```bash
# Vérifier qui utilise port 9000
$ sudo lsof -i :9000
COMMAND  PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
ssh      5678 alice   4u  IPv4  12345      0t0  TCP localhost:9000 (LISTEN)
# ^^^ Ancien tunnel SSH

# Tuer processus
$ kill 5678

# Ou choisir autre port
$ ssh -L 9001:db.internal:3306 alice@bastion.example.com
```

---

### Erreur 3 : X11 Forwarding Ne Fonctionne Pas

```bash
$ ssh -X alice@server.example.com
alice@server:~$ firefox
Error: cannot open display
```

**Causes possibles** :

1. **X11Forwarding désactivé serveur**

```bash
# Sur serveur
$ sudo nano /etc/ssh/sshd_config
X11Forwarding yes  # Vérifier activé

$ sudo systemctl restart sshd
```

2. **Pas de serveur X11 local**

```bash
# Vérifier DISPLAY local
$ echo $DISPLAY
:0  # ✅ Serveur X11 actif

# Si vide :
$ startx  # Démarrer X11 (si environnement graphique installé)
```

3. **Utiliser `-Y` (trusted) au lieu de `-X`**

```bash
$ ssh -Y alice@server.example.com
alice@server:~$ firefox
# ✅ Fonctionne (moins sécurisé mais moins restrictions)
```

---

### Erreur 4 : GPG Clé Publique Destinataire Manquante

```bash
$ gpg -e -r bob@example.com message.txt
gpg: bob@example.com: skipped: No public key
gpg: message.txt: encryption failed: No public key
```

**Cause** : Clé publique Bob pas dans trousseau GPG.

**Solution** :

```bash
# 1. Obtenir clé publique Bob (fichier ou serveur clés)
$ gpg --import bob_public.asc
# Ou :
$ gpg --keyserver keyserver.ubuntu.com --recv-keys BOB_KEY_ID

# 2. Vérifier import
$ gpg --list-keys bob@example.com

# 3. Chiffrer à nouveau
$ gpg -e -r bob@example.com message.txt
# ✅ Fonctionne
```

---

### Erreur 5 : GatewayPorts Ignoré (Tunnel Remote)

```bash
$ ssh -R 0.0.0.0:8000:localhost:80 alice@server.example.com

# Sur serveur, vérifier port
$ ss -tuln | grep 8000
tcp   LISTEN 0  128  127.0.0.1:8000   0.0.0.0:*
#                     └─ Écoute localhost uniquement, pas 0.0.0.0 !
```

**Cause** : `GatewayPorts` désactivé ou `no` dans `/etc/ssh/sshd_config`.

**Solution** :

```bash
# Sur serveur SSH
$ sudo nano /etc/ssh/sshd_config

# Modifier
GatewayPorts clientspecified
# Ou :
GatewayPorts yes

# Redémarrer SSH
$ sudo systemctl restart sshd

# Refaire tunnel
$ ssh -R 0.0.0.0:8000:localhost:80 alice@server.example.com

# Vérifier
$ ss -tuln | grep 8000
tcp   LISTEN 0  128  0.0.0.0:8000   0.0.0.0:*
#  ✅ Écoute toutes interfaces
```

---

## 📝 Cheat Sheet - Chiffrement & SSH

### SSH - Clés et Authentification

```bash
# Générer clés
ssh-keygen -t ed25519 -C "user@host"      # Ed25519 (recommandé)
ssh-keygen -t rsa -b 4096                 # RSA 4096 bits
ssh-keygen -t ecdsa -b 521                # ECDSA 521 bits

# Gérer known_hosts
ssh-keygen -R hostname                    # Supprimer entrée
ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub  # Fingerprint serveur

# Copier clé publique
ssh-copy-id user@server                   # Automatique
ssh-copy-id -i ~/.ssh/id_work.pub -p 2222 user@server

# SSH verbose
ssh -v user@server                        # Debug connexion
ssh -vv user@server                       # Plus verbeux
ssh -vvv user@server                      # Maximum verbeux

# SSH Agent
eval $(ssh-agent)                         # Démarrer agent
ssh-add                                   # Ajouter clé défaut
ssh-add ~/.ssh/id_work                    # Ajouter clé spécifique
ssh-add -l                                # Lister clés chargées
ssh-add -D                                # Supprimer toutes clés
ssh-add -t 3600                           # Expiration 1 heure
```

---

### Tunnels SSH

```bash
# Tunnel local (-L)
ssh -L local_port:destination:dest_port user@ssh_server
ssh -L 9000:db.internal:3306 user@bastion
ssh -L 0.0.0.0:9000:db.internal:3306 user@bastion  # Toutes interfaces

# Tunnel remote (-R)
ssh -R remote_port:localhost:local_port user@ssh_server
ssh -R 8000:localhost:80 user@public-server
ssh -R 0.0.0.0:8000:localhost:80 user@server  # (GatewayPorts requis)

# Tunnel dynamique (SOCKS)
ssh -D 1080 user@proxy-server             # Proxy SOCKS5

# X11 Forwarding
ssh -X user@server                        # X11 basique
ssh -Y user@server                        # X11 trusted

# Options tunnels
ssh -N -L 9000:db:3306 user@bastion       # Pas de shell
ssh -f -N -L 9000:db:3306 user@bastion    # Background
ssh -f -N -D 1080 user@proxy              # Proxy background
```

---

### GPG - Clés

```bash
# Lister clés
gpg --list-keys                           # Clés publiques
gpg -k                                    # Version courte
gpg --list-secret-keys                    # Clés privées
gpg -K                                    # Version courte

# Générer clés
gpg --gen-key                             # Interactif simple
gpg --full-gen-key                        # Options complètes

# Export/Import
gpg --export user@example.com > user.pub          # Binaire
gpg -a --export user@example.com > user.pub.asc   # ASCII
gpg --import bob.pub.asc                  # Importer clé publique
gpg --fingerprint user@example.com        # Afficher fingerprint

# Serveurs clés
gpg --keyserver keyserver.ubuntu.com --send-keys KEY_ID
gpg --keyserver keyserver.ubuntu.com --recv-keys KEY_ID
gpg --keyserver keyserver.ubuntu.com --search-keys user@example.com
```

---

### GPG - Chiffrement & Signature

```bash
# Chiffrer
gpg -o file.gpg -e -r recipient@example.com file.txt
gpg -a -o file.asc -e -r bob@example.com file.txt    # ASCII
gpg -e -r alice -r bob -r charlie file.txt            # Plusieurs destinataires

# Déchiffrer
gpg -o file.txt -d file.gpg               # Vers fichier
gpg -d file.gpg                           # Vers stdout

# Signer
gpg -o file.sig --sign file.txt           # Signature intégrée
gpg --detach-sign file.txt                # Signature détachée (.sig)
gpg -o file.asc --clearsign file.txt      # Signature texte clair

# Vérifier
gpg --verify file.sig file.txt            # Signature détachée
gpg --verify file.asc                     # Clearsign
gpg -d file.sig                           # Extraire contenu + vérifier

# Chiffrer + Signer
gpg -a -o msg.asc --encrypt --sign -r bob@example.com message.txt
```

---

### GPG - Révocation & Agent

```bash
# Révocation
gpg --output user.revoke.asc --gen-revoke user@example.com
gpg --import user.revoke.asc              # Appliquer révocation
gpg --keyserver keyserver.ubuntu.com --send-keys KEY_ID  # Publier

# GPG Agent
nano ~/.gnupg/gpg-agent.conf
default-cache-ttl 3600                    # Cache 1 heure
max-cache-ttl 86400                       # Max 24 heures

gpg-connect-agent reloadagent /bye        # Recharger agent
```

---

## 🎯 Points Clés pour l'Examen LPIC-1

### Ce qu'il faut maîtriser ✅

1. **Chiffrement asymétrique** 🔑
   - Paire clés : Publique (partageable) + Privée (secrète)
   - Chiffrement : Clé publique destinataire
   - Signature : Clé privée émetteur
   - Vérification : Clé publique émetteur

2. **SSH - Clés** 🔐
   - `ssh-keygen -t ed25519` (recommandé)
   - Algorithmes : Ed25519 > RSA 4096 > ECDSA > DSA (obsolète)
   - Fingerprint serveur : Protection MITM
   - `~/.ssh/known_hosts` : Clés serveurs connus
   - `ssh-keygen -R hostname` : Supprimer entrée

3. **SSH - Authentification** 🔒
   - Clés serveur : `/etc/ssh/ssh_host_*`
   - `~/.ssh/authorized_keys` : Clés publiques autorisées (permissions 600)
   - `/etc/ssh/sshd_config` : `PubkeyAuthentication yes`
   - `ssh-copy-id` : Copie automatique clé publique
   - `ssh -v` : Debug connexion

4. **ssh-agent** 🔑
   - Garde clés déverrouillées en mémoire
   - `eval $(ssh-agent)` : Démarrer
   - `ssh-add` : Ajouter clé
   - `ssh-add -l` : Lister clés
   - Forwarding jump servers

5. **Tunnels SSH** 🌉
   - **Local (`-L`)** : `ssh -L local_port:dest:dest_port user@server`
   - **Remote (`-R`)** : `ssh -R remote_port:localhost:local_port user@server`
   - **Dynamique (`-D`)** : `ssh -D 1080 user@server` (proxy SOCKS)
   - Options : `-N` (no shell), `-f` (background)
   - `GatewayPorts clientspecified` : Remote binding 0.0.0.0

6. **X11 Forwarding** 🖥️
   - `ssh -X user@server` : Applications distantes affichage local
   - `/etc/ssh/sshd_config` : `X11Forwarding yes`
   - Vérifier : `echo $DISPLAY`
   - `-Y` : Trusted (moins sécurisé)

7. **GPG - Clés** 🔐
   - `gpg --gen-key` : Générer paire
   - `gpg --list-keys` (-k) : Publiques
   - `gpg --list-secret-keys` (-K) : Privées
   - `gpg -a --export user > user.pub.asc` : Export ASCII
   - `gpg --import user.pub` : Import
   - Entropie : Aléatoire pour génération clés sécurisées

8. **GPG - Opérations** 🔒
   - Chiffrer : `gpg -o file.gpg -e -r recipient file.txt`
   - Déchiffrer : `gpg -o file.txt -d file.gpg`
   - Signer : `gpg --sign file.txt`, `--detach-sign`, `--clearsign`
   - Vérifier : `gpg --verify file.sig file.txt`
   - Révocation : `gpg --gen-revoke user`

9. **gpg-agent** 🔑
   - Cache passphrase en mémoire
   - `~/.gnupg/gpg-agent.conf` : `default-cache-ttl`, `max-cache-ttl`
   - Évite passphrase répétée

---

### Pièges Fréquents à l'Examen ⚠️

1. **Permissions SSH** :
   - `~/.ssh/` : 700 (drwx------)
   - `~/.ssh/authorized_keys` : 600 (-rw-------)
   - Si incorrectes → SSH ignore et demande mot de passe

2. **Fingerprint serveur** :
   - Vérifier fingerprint AVANT première connexion (protection MITM)
   - `ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub` sur serveur

3. **Algorithmes SSH** :
   - Ed25519 > RSA 4096 > ECDSA > DSA
   - DSA : Obsolète (1024 bits fixe, vulnérable)

4. **Tunnels SSH direction** :
   - Local (`-L`) : Port local → Destination via serveur SSH
   - Remote (`-R`) : Port serveur → Destination via client

5. **GatewayPorts** :
   - Défaut `no` : Remote tunnel écoute `127.0.0.1` uniquement
   - `clientspecified` : Client peut spécifier `0.0.0.0`
   - Pas sécuritaire (expose ports publiquement)

6. **GPG Chiffrement vs Signature** :
   - Chiffrer : Clé **publique** destinataire
   - Signer : Clé **privée** émetteur
   - Confondre les deux = Échec

7. **Export GPG** :
   - Défaut : Binaire
   - `-a` / `--armor` : ASCII (emails, web)

8. **Signature GPG types** :
   - `--sign` : Signature intégrée (fichier remplacé)
   - `--detach-sign` : Signature séparée (.sig)
   - `--clearsign` : Texte clair signé

---

### Commandes à Connaître par Cœur 💯

```bash
# SSH
ssh-keygen -t ed25519                     # Générer clé Ed25519
ssh-keygen -R hostname                    # Supprimer known_hosts
ssh-copy-id user@server                   # Copier clé publique
ssh -v user@server                        # Debug connexion
eval $(ssh-agent); ssh-add               # Agent + ajouter clé

# Tunnels
ssh -L 9000:db:3306 user@bastion          # Tunnel local
ssh -R 8000:localhost:80 user@server      # Tunnel remote
ssh -D 1080 user@proxy                    # Proxy SOCKS
ssh -X user@server                        # X11 forwarding
ssh -f -N -L 9000:db:3306 user@bastion    # Background

# GPG
gpg --gen-key                             # Générer clés
gpg -k / gpg -K                           # Lister clés
gpg -a --export user > user.pub.asc       # Export ASCII
gpg --import user.pub                     # Import
gpg -o file.gpg -e -r recipient file.txt  # Chiffrer
gpg -o file.txt -d file.gpg               # Déchiffrer
gpg --sign file.txt                       # Signer
gpg --verify file.sig file.txt            # Vérifier
gpg --gen-revoke user                     # Révocation
```

---

**Poids du module** : 4 points  
**Difficulté** : ⭐⭐⭐⭐ Moyenne-Élevée  
**Temps de préparation recommandé** : 7-9 heures

🎯 **Focus examen** : Maîtriser différences clés publiques/privées, savoir créer tunnels SSH (`-L`, `-R`, `-D`), comprendre chiffrement/signature GPG, et configurer authentification SSH par clés !

🎉 **Félicitations** : C'est le **dernier cours** de la playlist LPIC-1 102-500 ! Vous avez couvert l'ensemble du programme 🚀
