# 📧 Mail Transfer Agent (MTA) - Bases Linux

## 📋 Informations du Module

- **Examen** : LPIC-1 102
- **Objectif** : 108.3 - Mail Transfer Agent (MTA) basics
- **Poids** : 3 points
- **Objectif** : Comprendre et utiliser les MTA (Postfix, Sendmail, Exim) et clients mail locaux

---

## 🎯 Vue d'Ensemble de la Messagerie

### 📨 Architecture Email

```
┌─────────────────────────────────────────────────────────┐
│  CHAÎNE DE MESSAGERIE COMPLÈTE                          │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌──────────┐  SMTP   ┌──────────┐  SMTP   ┌─────────┐ │
│  │   MUA    │ ------> │   MTA    │ ------> │   MTA   │ │
│  │ (Client) │         │ (Sender) │         │ (Recip) │ │
│  └──────────┘         └──────────┘         └─────┬───┘ │
│       ↑                                          │     │
│       │                                          ↓     │
│       │                                    ┌──────────┐ │
│       │           POP3/IMAP                │   MDA   │ │
│       └────────────────────────────────────│(Deliver)│ │
│                                            └──────────┘ │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**Composants :**

| Composant | Nom complet | Rôle | Exemples |
|-----------|-------------|------|----------|
| **MUA** | Mail User Agent | Client mail (lecture/rédaction) | Thunderbird, Outlook, mail, mutt |
| **MTA** | Mail Transfer Agent | Transfert mail (SMTP) | Postfix, Sendmail, Exim |
| **MDA** | Mail Delivery Agent | Livraison locale | procmail, dovecot-lda, maildrop |
| **MSA** | Mail Submission Agent | Soumission (port 587) | Postfix (submission), Exim |

### 📬 Messagerie Locale Linux

**Historiquement très utilisée :**
- Communication entre utilisateurs du même système
- Notifications systèmes (cron, scripts)
- Alertes administratives

```bash
# Exemple : cron envoie mail à l'utilisateur
# /var/spool/cron/crontabs/alice
MAILTO=alice
0 2 * * * /usr/local/bin/backup.sh

# Output du script → mail à alice
```

**Fichiers boîtes mail locales :**
```bash
# Format mbox (un fichier par utilisateur)
/var/mail/alice
/var/mail/bob
/var/spool/mail/root

# Ou format Maildir (répertoire avec fichiers individuels)
~/Maildir/new/
~/Maildir/cur/
~/Maildir/tmp/
```

### 📡 Protocole SMTP

**SMTP** : Simple Mail Transfer Protocol (Port 25)

**Caractéristiques :**
- Protocole texte (commandes ASCII)
- Transfert mail serveur à serveur
- Pas d'authentification (SMTP original)
- Extension ESMTP (Extended SMTP) : AUTH, STARTTLS

**Ports :**
- **25** : SMTP (transfert MTA ↔ MTA)
- **587** : Submission (client → MTA, authentifié)
- **465** : SMTPS (obsolète, SSL)

**Conversation SMTP basique :**

```
Client: EHLO client.example.com
Server: 250-server.example.com
        250-SIZE 35651584
        250-AUTH PLAIN LOGIN
        250 STARTTLS

Client: MAIL FROM:<alice@example.com>
Server: 250 OK

Client: RCPT TO:<bob@example.com>
Server: 250 OK

Client: DATA
Server: 354 Start mail input

Client: Subject: Test
        From: alice@example.com
        To: bob@example.com
        
        Ceci est un message test.
        .
Server: 250 OK Message queued

Client: QUIT
Server: 221 Closing connection
```

---

## 🚀 Les Trois Principaux MTA

### 📊 Comparaison

| Critère | Sendmail | Exim | Postfix |
|---------|----------|------|---------|
| **Année** | 1983 | 1995 | 1998 |
| **Origine** | Eric Allman (Berkeley) | Philip Hazel (Cambridge) | Wietse Venema (IBM) |
| **Configuration** | ❌ Très complexe (m4) | ⚠️ Moyennement complexe | ✅ Simple et claire |
| **Sécurité** | ⚠️ Historique chargé | ✅ Bonne | ✅ Excellente (design) |
| **Performance** | ⚠️ Moyenne | ✅ Bonne | ✅ Excellente |
| **Flexibilité** | ✅ Très complète | ✅✅ ACL puissantes | ✅ Modulaire |
| **Popularité** | ❌ Déclin | ⚠️ Debian/Ubuntu défaut | ✅✅ Défaut majorité |
| **Difficulté** | ❌❌ Expert | ⚠️ Intermédiaire | ✅ Débutant-OK |

### 📜 Sendmail - L'Ancien

**Caractéristiques :**
- **Le plus ancien MTA Unix** (40+ ans)
- Extrêmement complet et puissant
- Configuration **infernale** (macro m4)
- Historique de failles sécurité
- Encore utilisé sur systèmes legacy

**Fichiers de configuration :**
```bash
# Configuration m4 (source)
/etc/mail/sendmail.mc

# Configuration compilée (binaire)
/etc/mail/sendmail.cf

# Compilation
m4 /etc/mail/sendmail.mc > /etc/mail/sendmail.cf
```

**Exemple sendmail.cf (extrait) :**
```
# Incompréhensible pour humains normaux
R$* < @ $+ . > $*       $1 < @ $2 > $3          strip trailing dots
R$* < @ $=w > $*        $@ $>ParseLocal $>3 $1  local address
```

**Pourquoi encore présent ?**
- Standards *de facto* (commandes sendmail, mailq)
- Émulation par Postfix/Exim (compatibilité)

### 🔧 Exim - Le Flexible

**Caractéristiques :**
- MTA développé à Cambridge University
- **ACL (Access Control Lists) puissantes**
- Authentification avancée
- Vérifications email complexes
- Défaut sur Debian/Ubuntu (exim4)

**Avantages :**
- Configuration flexible (vs Sendmail)
- Excellent filtrage anti-spam
- ACL granulaires
- Debugging excellent

**Configuration :**
```bash
# Debian/Ubuntu (split config)
/etc/exim4/update-exim4.conf.conf
/etc/exim4/conf.d/*

# Configuration unique
/etc/exim4/exim4.conf

# Génération config
update-exim4.conf

# Test config
exim4 -bV    # Version
exim4 -bt alice@example.com    # Test routing
```

### ⭐ Postfix - Le Plébiscité

**Caractéristiques :**
- **MTA moderne** conçu pour sécurité
- Configuration **simple et claire**
- Architecture modulaire (processus séparés)
- Performances excellentes
- **Défaut majorité distributions**

**Philosophie design :**
- Compatibilité Sendmail (commandes)
- Sécurité par design (principe moindre privilège)
- Simplicité configuration
- Rapidité

**Architecture modulaire :**

**💡 Principe** : Postfix lance d'abord un **service maître** (**master**) qui est responsable de gérer les processus secondaires.

**Processus principaux** :
- **master** : Service maître (gestionnaire de processus)
- **smtpd** : Serveur SMTP (réception courrier)
- **pickup** : Récupération courrier local (depuis maildrop)
- **qmgr** (ou **nqmgr**) : Gestionnaire de queue (ordonnancement)
- **cleanup** : Nettoyage et préparation messages
- **smtp** : Client SMTP (livraison distante)
- **local** : Livraison locale

```
┌──────────────────────────────────────────────┐
│  POSTFIX - ARCHITECTURE MODULAIRE            │
├──────────────────────────────────────────────┤
│                                              │
│  ┌──────────┐                                    │
│  │  master   │ (Service maître)             │
│  └────┬─────┘                                    │
│       │  Lance processus :                      │
│       ├──> smtpd, pickup, qmgr/nqmgr, ...      │
│       │                                          │
│  Client SMTP / Mail local                     │
│       ↓                                          │
│  ┌─────────┐      ┌─────────┐               │
│  │ smtpd   │ ---> │ cleanup │               │
│  └─────────┘      └────┬────┘               │
│  ┌─────────┐           ↓                     │
│  │ pickup  │ ------─────────┐                 │
│  └─────────┘                │                 │
│                        ↓                     │
│                   ┌─────────┐                │
│                   │  queue  │                │
│                   └────┬────┘                │
│                        ↓                     │
│                   ┌─────────┐                │
│                   │  qmgr   │ (Queue Mgr)   │
│                   └────┬────┘                │
│        ┌───────────────┴──────────┐          │
│        ↓                          ↓          │
│   ┌─────────┐              ┌──────────┐     │
│   │  smtp   │ (Delivery)   │  local   │     │
│   └─────────┘              └──────────┘     │
│   Remote                   Local            │
│                                              │
└──────────────────────────────────────────────┘
```

**Fichiers de configuration :**
```bash
# Configuration principale
/etc/postfix/main.cf

# Services et processus
/etc/postfix/master.cf

# Autres configs
/etc/postfix/aliases       # Redirection (lien vers /etc/aliases)
/etc/postfix/virtual       # Domaines virtuels
/etc/postfix/transport     # Routage custom
```

---

## 🔄 Rétrocompatibilité Sendmail

**Problème :** Beaucoup de scripts/applications utilisent `/usr/sbin/sendmail`.

**Solution :** Postfix et Exim **émulent** la couche Sendmail.

```bash
# Avec Postfix installé
which sendmail
# /usr/sbin/sendmail

ls -la /usr/sbin/sendmail
# lrwxrwxrwx sendmail -> /etc/alternatives/sendmail

ls -la /etc/alternatives/sendmail
# lrwxrwxrwx sendmail -> /usr/sbin/sendmail.postfix

# Avec Exim installé
ls -la /usr/sbin/sendmail
# lrwxrwxrwx sendmail -> exim4

# Commandes disponibles (peu importe MTA)
sendmail -bp        # Afficher queue (= mailq)
sendmail -q         # Forcer traitement queue
sendmail user@host  # Envoyer mail
```

**Commandes universelles :**
- `sendmail` : Envoyer mail, gérer queue
- `mailq` : Afficher queue mail

### 🔄 Choisir MTA par Défaut (alternatives)

**Contexte** : Sur certaines distributions (Red Hat, Fedora, OpenSUSE), la configuration par défaut utilise **sendmail**. Si vous installez **Postfix**, vous devez indiquer au système d'utiliser Postfix à la place.

**Commande `alternatives`** : Permet de choisir l'implémentation MTA par défaut (Red Hat/Fedora/CentOS).

```bash
# Définir Postfix comme MTA par défaut
sudo alternatives --set mta /usr/sbin/sendmail.postfix

# Vérifier le choix actuel
alternatives --display mta
# mta - status is manual.
#  link currently points to /usr/sbin/sendmail.postfix

# Lister les options disponibles
alternatives --list | grep mta

# Mode interactif (choisir manuellement)
sudo alternatives --config mta
# Sélection    Commande
# *+ 1           /usr/sbin/sendmail.sendmail
#    2           /usr/sbin/sendmail.postfix
#    3           /usr/sbin/sendmail.exim
```

**💡 Note** : Sur Debian/Ubuntu, le système `update-alternatives` est utilisé de manière similaire :

```bash
# Debian/Ubuntu
sudo update-alternatives --set mta /usr/sbin/sendmail.postfix
sudo update-alternatives --config mta
```

---

## 📝 Configuration - Aliases

### 🎯 Fichier /etc/aliases

**But :** Rediriger mails vers autres adresses (locales ou externes).

**Cas d'usage :**
- Centraliser mails root → admin
- Créer adresses virtuelles (postmaster, webmaster)
- Listes de diffusion simples
- Redirection vers externe

**Format :**
```
alias: destination
```

**Exemples :**

```bash
cat /etc/aliases

# Redirection simple
postmaster: root
webmaster: root
abuse: root

# Centralisation root vers admin
root: admin

# Vers utilisateur externe
admin: admin@example.com

# Multiple destinations (liste)
support: alice,bob,charlie

# Mix local et externe
devteam: alice,bob,dev@external.com

# Alias vers fichier (mailing list archive)
announce: :include:/etc/mail/announce-list

# Commande (pipe)
bugtracker: "|/usr/local/bin/bug-tracker.sh"

# Null (blackhole)
spam: /dev/null
```

### ⚙️ Syntaxe Détaillée

| Type | Syntaxe | Exemple | Description |
|------|---------|---------|-------------|
| **Utilisateur local** | `alias: user` | `root: alice` | Redirection vers user |
| **Email externe** | `alias: user@domain` | `admin: alice@ext.com` | Redirection externe |
| **Multiple** | `alias: user1,user2` | `team: alice,bob` | Plusieurs destinations |
| **Fichier include** | `alias: :include:/path` | `list: :include:/etc/lists/dev` | Fichier avec adresses |
| **Commande** | `alias: "|/cmd"` | `tickets: "|/usr/bin/rt"` | Pipe vers commande |
| **Fichier** | `alias: /path/file` | `archive: /var/mail/archive` | Append fichier |
| **Blackhole** | `alias: /dev/null` | `spam: /dev/null` | Supprimer |

### 🔧 Commande newaliases

**Charger nouveaux aliases dans base de données.**

```bash
# Éditer aliases
sudo vim /etc/aliases

# Ajouter
devops: alice,bob,charlie@external.com

# Recharger base aliases
sudo newaliases

# Output
# /etc/aliases: 12 aliases, longest 45 bytes, 432 bytes total

# Base générée (format hash)
ls -la /etc/aliases.db
# -rw-r--r-- 1 root root 12288 Feb 11 14:35 /etc/aliases.db
```

**⚠️ Obligatoire :** `newaliases` après modification `/etc/aliases`.

**Équivalents :**
```bash
# Postfix
sudo newaliases
sudo postalias /etc/aliases

# Sendmail
sudo newaliases
sudo sendmail -bi
```

### 📂 Fichier .forward (Utilisateurs)

**But :** Redirection personnelle (sans droits root).

**Emplacement :** `~/.forward` (home utilisateur)

**Format :** Identique `/etc/aliases` (sans partie gauche)

```bash
# Alice veut rediriger vers email perso
cat ~/.forward
alice.dupont@gmail.com

# Conserver copie locale ET rediriger
cat ~/.forward
alice,alice.dupont@gmail.com

# Redirection multiple
cat ~/.forward
alice.dupont@gmail.com,alice.work@corp.com

# Vers commande (filtrage)
cat ~/.forward
"|/usr/bin/procmail"

# Vacation (auto-reply)
cat ~/.forward
\alice,"|/usr/bin/vacation alice"
# \alice = garder copie locale (sinon boucle)
```

**Permissions :**
```bash
# .forward doit être lisible par MTA
chmod 644 ~/.forward

# Vérifier
ls -la ~/.forward
# -rw-r--r-- 1 alice alice 28 Feb 11 14:00 .forward
```

**Désactiver .forward (sécurité) :**
```bash
# Postfix : /etc/postfix/main.cf
forward_path =

# Exim : /etc/exim4/exim4.conf
# Commenter section .forward
```

---

## 📬 MUA - Mail User Agent (Client Mail)

### 📧 Client mail / mailx

**Installation :**

```bash
# Debian/Ubuntu
sudo apt install mailutils
# ou
sudo apt install bsd-mailx

# RedHat/CentOS
sudo yum install mailx
```

**Commande : `mail`**

### 📖 Lire les Mails

```bash
# Lancer client mail
mail

# Si mails présents
Mail version 8.1.2. Type ? for help.
"/var/mail/alice": 3 messages 2 new
    1 root@server    Tue Feb 11 10:00  15/512   "Cron job output"
>N  2 bob@server     Tue Feb 11 12:30  22/834   "Meeting notes"
 N  3 system         Tue Feb 11 14:00  10/345   "Disk usage alert"
? 

# Symboles
# > = mail actuel
# N = nouveau (non lu)
# U = lu mais pas supprimé
# (rien) = lu
```

**Commandes interactives :**

| Commande | Description | Exemple |
|----------|-------------|---------|
| **?** ou **help** | Aide | `?` |
| **[numéro]** | Lire mail N | `2` |
| **n** | Mail suivant (next) | `n` |
| **p** | Mail précédent (previous) | `p` |
| **h** | Liste headers (list) | `h` |
| **d [N]** | Supprimer mail N | `d 3` |
| **d 1-5** | Supprimer mails 1 à 5 | `d 1-5` |
| **u [N]** | Undelete mail N | `u 2` |
| **r** | Répondre (reply) | `r` |
| **R** | Reply à tous | `R` |
| **m user** | Nouveau mail | `m alice` |
| **s [N] file** | Sauvegarder vers fichier | `s 2 ~/mail.txt` |
| **w [N] file** | Write (sans headers) | `w 2 ~/content.txt` |
| **q** | Quitter (save changes) | `q` |
| **x** | Quitter (abandon changes) | `x` |

**Exemple session :**

```bash
mail
? 2              # Lire mail 2
From: bob@server
Subject: Meeting notes
Date: Tue, 11 Feb 2026 12:30:00

Hello Alice,
Meeting is rescheduled to Friday 14:00.
Bob

? r              # Répondre
To: bob@server
Subject: Re: Meeting notes
Cc: 
OK, confirmed for Friday.
.                # Ctrl+D ou point seul = fin message
EOT
? d 2            # Supprimer mail 2
? q              # Quitter
Saved 1 message in /home/alice/mbox
Held 1 message in /var/mail/alice
```

### ✉️ Envoyer un Mail

#### Mode Interactif

```bash
# Envoyer à utilisateur local
mail alice

# Envoyer à email externe
mail bob@example.com

# Session
Subject: Test message
Bonjour Alice,
Ceci est un message test.
Cordialement,
Bob
.                 # Ctrl+D ou point seul
EOT
Cc:               # Carbon copy (optionnel)
```

#### Mode Ligne de Commande

```bash
# Avec sujet (-s)
echo "Contenu du message" | mail -s "Sujet" alice

# Vers externe
echo "Rapport quotidien" | mail -s "Daily Report" admin@example.com

# Depuis fichier
mail -s "Log files" admin@example.com < /var/log/app.log

# Avec corps et sujet
cat report.txt | mail -s "Weekly Report" team@example.com

# Copy carbon (-c)
echo "Important info" | mail -s "Alert" -c bob@example.com alice

# Blind copy (-b)
echo "Confidential" | mail -s "Secret" -b hidden@example.com alice

# Attachment (si mailx support)
echo "See attachment" | mail -s "Files" -a /tmp/data.csv alice
```

#### Exemples Scripts

```bash
#!/bin/bash
# Backup notification

if backup_success; then
    echo "Backup completed successfully at $(date)" | \
        mail -s "✅ Backup OK" admin@example.com
else
    echo "Backup FAILED at $(date). Check logs." | \
        mail -s "❌ Backup FAILED" admin@example.com
fi
```

```bash
#!/bin/bash
# Disk usage alert

USAGE=$(df -h / | awk 'NR==2 {print $5}' | sed 's/%//')

if [ $USAGE -gt 80 ]; then
    echo "Disk usage at ${USAGE}%!" | \
        mail -s "⚠️ Disk Alert" admin@example.com
fi
```

```bash
#!/bin/bash
# Daily report

{
    echo "=== System Report $(date) ==="
    echo ""
    echo "Uptime:"
    uptime
    echo ""
    echo "Disk usage:"
    df -h
    echo ""
    echo "Memory:"
    free -h
} | mail -s "Daily System Report" admin@example.com
```

### 📁 Fichiers Boîtes Mail

```bash
# Boîte principale (inbox)
/var/mail/alice
/var/spool/mail/alice

# Mbox personnel (mails lus/sauvegardés)
~/mbox

# Mails sauvegardés
~/Mail/

# Maildir (alternative à mbox)
~/Maildir/new/       # Nouveaux
~/Maildir/cur/       # Lus
~/Maildir/tmp/       # Temporaires
```

---

## 📊 Queue Mail - Commande mailq

### 🎯 Principe

**Queue mail (file d'attente) :** Mails en attente de livraison.

**Raisons :**
- Serveur destinataire injoignable
- Problème DNS
- Serveur occupé (rate limiting)
- Erreur temporaire

**Localisation :**
```bash
# Postfix
/var/spool/postfix/deferred/
/var/spool/postfix/active/
/var/spool/postfix/hold/

# Sendmail
/var/spool/mqueue/

# Exim
/var/spool/exim4/input/
```

### 📋 Commande mailq

**Afficher queue mail.**

```bash
# Afficher queue
mailq

# Postfix output
-Queue ID-  --Size-- ----Arrival Time---- -Sender/Recipient-------
ABC123DEF*     2456 Tue Feb 11 14:35:00  alice@server.local
(connect to mail.example.com[192.0.2.10]:25: Connection timed out)
                                         bob@example.com

DEF456GHI      1234 Tue Feb 11 12:00:00  root@server.local
(Host or domain name not found. Name service error for name=badhost.local)
                                         admin@badhost.local

-- 5 Kbytes in 2 Requests.
```

**Symboles :**
- `*` : Message actuel en livraison
- `!` : Message en erreur (bounce)

**Équivalent Postfix :**
```bash
postqueue -p
# Identique à mailq
```

**Détail message spécifique :**
```bash
# Postfix
postcat -q ABC123DEF

# Exim
exim4 -Mvh ABC123DEF    # Headers
exim4 -Mvb ABC123DEF    # Body
```

### 🔧 Gérer la Queue

```bash
# Forcer traitement queue (retry)
sendmail -q
# Postfix
postqueue -f
postfix flush

# Supprimer message spécifique
# Postfix
postsuper -d ABC123DEF
postsuper -d ALL deferred    # Tous deferred

# Exim
exim4 -Mrm ABC123DEF

# Voir messages en hold (admin)
# Postfix
postqueue -p | grep "^[A-F0-9].*!"

# Release message hold
postsuper -H ABC123DEF    # Hold
postsuper -r ABC123DEF    # Release

# Vider toute la queue (dangereux!)
postsuper -d ALL
```

### 📊 Statistiques Queue

```bash
# Postfix : compter messages queue
mailq | tail -1
# -- 127 Kbytes in 45 Requests.

# Nombres par status
postqueue -p | awk '/^[A-F0-9]/ {print $1}' | wc -l

# Exim : statistiques
exim4 -bpc    # Nombre messages queue
# 12

exim4 -bp     # Liste queue (= mailq)
```

---

## 🔧 Configuration Postfix Basique

### 📝 Fichier main.cf

**Emplacements configuration** :
- **Fichier principal** : `/etc/postfix/main.cf`
- **Fichiers modulaires** : `/etc/postfix/*.d/` (certaines distributions)

**Méthodes de modification** :

1. **Édition directe** du fichier `/etc/postfix/main.cf`
2. **Commande `postconf`** (recommandée)

**💡 Avantages postconf** :
- Vérifie syntaxe automatiquement
- Gère correctement les valeurs multi-lignes
- Évite les erreurs de formatage

```bash
# Lire configuration actuelle
postconf | grep mydomain
postconf myhostname

# Modifier paramètre (utilise -e pour edit)
postconf -e "myhostname = mail.exemple.com"

# Afficher toute la configuration
postconf

# Afficher uniquement valeurs non-par-défaut
postconf -n
```

### 🔑 Paramètres Principaux

**Configuration via postconf** :

```bash
# Domaine d'origine des messages (From:)
postconf -e "myorigin = mondomaine.org"

# De quels domaines recevoir le courrier (livraison locale)
postconf -e "mydestination = mondomaine.org"

# De quels clients relayer le courrier (réseaux autorisés)
postconf -e "mynetworks = 192.168.1.0/24, 127.0.0.1"

# Sur quelles interfaces écouter
postconf -e "inet_interfaces = all"  # Toutes interfaces
# Ou
postconf -e "inet_interfaces = localhost"  # Localhost uniquement (local only)
```

**Exemple complet** :

```bash
# /etc/postfix/main.cf

# Nom d'hôte mail
myhostname = mail.example.com

# Domaine origine
myorigin = $myhostname
# Ou
myorigin = example.com

# Domaines acceptés (livraison locale)
mydestination = $myhostname, localhost, localhost.localdomain, example.com

# Interfaces écoute (localhost seulement = local only)
inet_interfaces = localhost
# Ou toutes
inet_interfaces = all

# Protocoles
inet_protocols = ipv4
# Ou
inet_protocols = all

# Réseaux autorisés (relay)
mynetworks = 127.0.0.0/8, 192.168.1.0/24

# Taille max message (10MB)
message_size_limit = 10485760

# Taille max mailbox (50MB)
mailbox_size_limit = 52428800

# Format mailbox
home_mailbox = Maildir/
# Ou mbox
mail_spool_directory = /var/mail
```

**Ou via postconf (recommandé)** :

```bash
# Nom d'hôte
postconf -e "myhostname = mail.example.com"

# Domaine origine
postconf -e "myorigin = example.com"

# Domaines acceptés (livraison locale)
postconf -e "mydestination = \$myhostname, localhost, localhost.localdomain, example.com"

# Interfaces écoute
postconf -e "inet_interfaces = all"

# Protocoles
postconf -e "inet_protocols = ipv4"

# Réseaux autorisés (relay)
postconf -e "mynetworks = 127.0.0.0/8, 192.168.1.0/24"

# Taille max message (10MB)
postconf -e "message_size_limit = 10485760"

# Format mailbox
postconf -e "home_mailbox = Maildir/"
```

**Appliquer changements :**
```bash
# Vérifier config
postfix check

# Recharger config
sudo postfix reload

# Redémarrer
sudo systemctl restart postfix

# Vérifier status
systemctl status postfix
```

### 🔍 Tests et Debug

```bash
# Test connectivité SMTP local
telnet localhost 25
# Trying 127.0.0.1...
# Connected to localhost.
# Escape character is '^]'.
# 220 mail.example.com ESMTP Postfix

EHLO test
QUIT

# Envoyer test mail
echo "Test" | mail -s "Test" root

# Vérifier logs
tail -f /var/log/mail.log
# ou
journalctl -u postfix -f

# Logs Postfix
tail -f /var/log/mail.log
tail -f /var/log/mail.err

# Debug verbeux (temporaire)
postconf -e "debug_peer_list = 192.168.1.10"
postconf -e "debug_peer_level = 2"
postfix reload

# Remettre normal
postconf -e "debug_peer_list = "
postfix reload
```

---

## 🎓 Scénarios Pratiques

### Scénario 1 : Centraliser Mails Root

**Objectif :** Rediriger tous les mails root vers admin externe.

```bash
# 1. Éditer aliases
sudo vim /etc/aliases

# Ajouter/modifier
root: admin@example.com

# 2. Recharger
sudo newaliases

# 3. Tester
echo "Test mail root" | mail -s "Test" root

# 4. Vérifier
tail -f /var/log/mail.log
# ... to=<admin@example.com>, relay=...
```

### Scénario 2 : Liste Diffusion Simple

**Objectif :** Créer alias `devteam` → alice, bob, charlie + externe.

```bash
# 1. Aliases
sudo vim /etc/aliases

devteam: alice,bob,charlie,dev-external@corp.com

sudo newaliases

# 2. Tester
echo "Team meeting Friday 14:00" | mail -s "Meeting" devteam

# 3. Vérifier tous ont reçu
sudo mail -u alice
sudo mail -u bob
# etc.
```

### Scénario 3 : Monitoring Disk avec Alert

**Script `/usr/local/bin/disk-monitor.sh` :**

```bash
#!/bin/bash
THRESHOLD=80
USAGE=$(df -h / | awk 'NR==2 {print $5}' | sed 's/%//')

if [ $USAGE -gt $THRESHOLD ]; then
    {
        echo "⚠️ DISK USAGE ALERT"
        echo ""
        echo "Current usage: ${USAGE}%"
        echo "Threshold: ${THRESHOLD}%"
        echo ""
        echo "Details:"
        df -h /
        echo ""
        echo "Largest directories:"
        du -sh /var/* 2>/dev/null | sort -rh | head -5
    } | mail -s "⚠️ Disk ${USAGE}% on $(hostname)" admin@example.com
fi
```

**Cron quotidien :**
```bash
sudo vim /etc/cron.daily/disk-monitor

#!/bin/bash
/usr/local/bin/disk-monitor.sh

sudo chmod +x /etc/cron.daily/disk-monitor
```

### Scénario 4 : Vider Queue Bloquée

**Situation :** Queue pleine, serveur distant down.

```bash
# 1. Voir queue
mailq
# -- 1024 Kbytes in 283 Requests (tous vers badserver.com)

# 2. Option A : Supprimer tous vers badserver.com
postqueue -p | grep badserver.com | awk '{print $1}' | \
    xargs -n 1 postsuper -d

# 2. Option B : Vider toute queue (brutal)
postsuper -d ALL

# 3. Vérifier
mailq
# Mail queue is empty
```

### Scénario 5 : Forward Utilisateur Vacances

**Alice en vacances, forward vers Bob :**

```bash
# Connexion alice
vim ~/.forward

bob

# Tester (depuis autre compte)
echo "Test forward" | mail -s "Test" alice

# Vérifier (bob)
mail
# Should see mail originally to alice
```

**Avec copie locale :**
```bash
# ~/.forward
alice,bob
```

---

## 🎓 Clients Mail Alternatifs

### 📧 mutt - Client Avancé

```bash
# Installation
sudo apt install mutt

# Lancer
mutt

# Plus features que mail :
# - Couleurs
# - Threading (fils discussion)
# - IMAP/POP3
# - GPG/PGP
# - Attachments multiples
```

### 📨 mailutils vs bsd-mailx

```bash
# mailutils (GNU)
sudo apt install mailutils

# bsd-mailx (BSD)
sudo apt install bsd-mailx

# Différences mineures
# Commande : mail (identique)
```

---

## 📝 Cheat Sheet

### Commandes Mail

```bash
# Lire mails
mail                    # Ouvrir client
mail -u alice          # Mails utilisateur alice (root)

# Envoyer mail
mail user              # Interactif
echo "msg" | mail -s "Subject" user
mail -s "Subj" user < file.txt
mail -s "S" -c copy@user -a file.csv user

# Queue
mailq                  # Afficher queue
sendmail -q            # Forcer traitement
```

### Postfix

```bash
# Service
systemctl status postfix
systemctl restart postfix
postfix reload

# Config
/etc/postfix/main.cf
/etc/postfix/master.cf
/etc/aliases

# Vérifier config
postfix check

# Queue
postqueue -p           # Afficher
postqueue -f           # Flush (forcer)
postsuper -d ID        # Supprimer message
postsuper -d ALL       # Vider queue

# Logs
tail -f /var/log/mail.log
journalctl -u postfix -f
```

### Aliases

```bash
# Éditer
sudo vim /etc/aliases

# Format
alias: destination
root: admin@example.com
team: user1,user2,external@corp.com

# Recharger
sudo newaliases

# Forward utilisateur
vim ~/.forward
user@external.com
```

### Exim

```bash
# Service
systemctl status exim4
update-exim4.conf      # Rebuild config

# Queue
exim4 -bp              # List (= mailq)
exim4 -bpc             # Count
exim4 -Mrm ID          # Remove message

# Test
exim4 -bt user@domain  # Test routing
```

---

## 🎯 Points Clés pour l'Examen LPIC-1

### Connaissances Essentielles

✅ **Composants messagerie :**
- MUA : Client mail (Thunderbird, mail, mutt)
- MTA : Transfert SMTP (Postfix, Sendmail, Exim)
- MDA : Livraison locale (procmail)

✅ **Protocole SMTP :**
- Port 25 (transfert MTA-MTA)
- Port 587 (submission client-MTA)
- Protocole texte (EHLO, MAIL FROM, RCPT TO, DATA, QUIT)

✅ **MTA principaux :**
- **Sendmail** : Ancien, complexe (m4), historique
- **Exim** : Flexible, ACL, défaut Debian
- **Postfix** : Moderne, simple config, sécurisé, populaire

✅ **Rétrocompatibilité :**
- Postfix/Exim émulent Sendmail
- Commandes `sendmail` et `mailq` universelles

✅ **Aliases :**
- Fichier `/etc/aliases`
- Format : `alias: destination`
- Commande `newaliases` pour charger
- Users : `~/.forward` (redirection perso)

✅ **Client mail (mailx) :**
- Commande `mail` (lire/envoyer)
- Interactif : `r` (reply), `d` (delete), `q` (quit)
- Cmdline : `echo "msg" | mail -s "subject" user`

✅ **Queue mail :**
- `mailq` : Afficher queue
- `sendmail -q` : Forcer traitement
- Postfix : `postqueue -p`, `postsuper -d ID`

✅ **Fichiers clés :**
- `/etc/aliases` : Redirections mail
- `/etc/postfix/main.cf` : Config Postfix
- `/var/mail/user` : Boîtes mail (mbox)
- `~/Maildir/` : Format Maildir
- `~/.forward` : Redirection utilisateur

✅ **Logs :**
- `/var/log/mail.log` (Debian)
- `/var/log/maillog` (RedHat)
- `journalctl -u postfix`

### Commandes à Maîtriser

| Commande | Usage |
|----------|-------|
| `mail` | Lire/envoyer mails |
| `mailq` | Afficher queue |
| `sendmail -q` | Traiter queue |
| `newaliases` | Recharger aliases |
| `postfix check` | Vérifier config Postfix |
| `postfix reload` | Recharger Postfix |
| `postqueue -p` | Queue Postfix |
| `postsuper -d ID` | Supprimer message |

### Pièges Fréquents

❌ Modifier `/etc/aliases` sans `newaliases` → Pas effet  
❌ Permissions incorrectes `~/.forward` → Ignoré  
❌ Confondre MUA (client) et MTA (serveur)  
❌ Oublier `.` (point) ou Ctrl+D en mode interactif mail  
❌ Queue pleine sans surveillance → Saturation disque  
❌ Firewall bloque port 25 → Mails bloqués  
❌ Domaine `mydestination` incorrect → Mails rejetés  
❌ `inet_interfaces = all` sans sécurité → Open relay

### Exemples Examen

**Q : Comment rediriger tous les mails de root vers admin@example.com ?**
```bash
# /etc/aliases
root: admin@example.com

# Puis
sudo newaliases
```

**Q : Afficher la queue mail ?**
```bash
mailq
# ou
postqueue -p
```

**Q : Envoyer mail avec sujet depuis cmdline ?**
```bash
echo "Message content" | mail -s "Subject" user@example.com
```

**Q : Fichier config principal Postfix ?**
```
/etc/postfix/main.cf
```

**Q : Forcer traitement queue mail ?**
```bash
sendmail -q
# ou
postqueue -f
```

---

**Formation LPIC-1 102 - Préparation Certification Linux**  
**Cours 108.3 - Mail Transfer Agent (MTA) Basics**  
**Maxime Chenaud - 11 Février 2026**
