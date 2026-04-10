# 109.4 - Client Side DNS Configuration 🌐🔍

## 📋 Informations du Module

- **Poids à l'examen** : 2 points
- **Objectifs clés** :
  - Comprendre le système de résolution de noms DNS ✅
  - Maîtriser les types d'enregistrements DNS (A, AAAA, MX, CNAME, etc.)
  - Utiliser les outils de requête DNS (`host`, `dig`, `getent`)
  - Configurer la résolution DNS côté client
  - Comprendre le rôle de systemd-resolved
  - Diagnostiquer les problèmes de résolution DNS

---

## 📚 Concepts Fondamentaux du DNS

### Qu'est-ce que le DNS ?

**DNS (Domain Name System)** = Système de résolution de noms de domaine en adresses IP.

**Analogie** : DNS = Annuaire téléphonique d'Internet  
- 🧑 Nom de domaine = Nom dans l'annuaire (`google.com`)
- 📞 Adresse IP = Numéro de téléphone (`142.250.201.46`)

**Pourquoi nécessaire ?** 🤔
- Les humains mémorisent facilement les noms : `google.com`, `github.com`
- Les machines utilisent des adresses IP : `142.250.201.46`, `140.82.121.4`
- DNS traduit automatiquement noms → IP

---

### Hiérarchie DNS

Le DNS fonctionne selon une **hiérarchie arborescente** :

```
                          Root (.)
                             |
        ┌────────────────────┼────────────────────┐
        |                    |                    |
      .com                 .org                 .fr
        |                    |                    |
    ┌───┴───┐            ┌───┴───┐          ┌────┴────┐
 google  amazon       wikipedia example   gouv    lemonde
    |       |              |       |         |         |
   www     www            www     www       www       www
```

**Résolution complète** : `www.google.com.`

- `.` = Root (racine, généralement implicite)
- `.com` = TLD (Top-Level Domain, domaine de premier niveau)
- `google` = Domaine de second niveau
- `www` = Sous-domaine / hôte

---

### Processus de Résolution DNS

**Étapes lorsque vous tapez `www.google.com` dans un navigateur** :

```
1. Client vérifie /etc/hosts (résolution locale)
   └─> Si trouvé : FIN
   └─> Sinon : continue

2. Client vérifie son cache DNS local
   └─> Si trouvé et non expiré (TTL valide) : FIN
   └─> Sinon : continue

3. Client interroge le résolveur DNS configuré (/etc/resolv.conf)
   └─> Généralement : serveur DNS FAI ou 8.8.8.8 (Google)

4. Résolveur vérifie son cache
   └─> Si trouvé : renvoie la réponse
   └─> Sinon : résolution récursive

5. Résolution récursive complète :
   a) Résolveur interroge serveur Root (.)
      └─> "Qui gère .com ?"
      └─> Root répond : "Serveur TLD .com à 192.5.6.30"
   
   b) Résolveur interroge serveur TLD .com
      └─> "Qui gère google.com ?"
      └─> TLD répond : "Serveur DNS Google à 216.239.32.10"
   
   c) Résolveur interroge serveur autoritaire google.com
      └─> "Quelle est l'IP de www.google.com ?"
      └─> Autoritaire répond : "142.250.201.46"

6. Résolveur met en cache la réponse (durée = TTL)
7. Résolveur renvoie la réponse au client
8. Client met en cache et se connecte à 142.250.201.46
```

**Types de requêtes** :
- **Récursive** : Client demande au résolveur de faire tout le travail
- **Itérative** : Serveur répond "Je ne sais pas, demande à X"

---

### TTL - Time To Live ⏱️

**TTL (Time To Live)** = Durée de validité d'un enregistrement DNS dans le cache (en secondes).

**Exemple** :

```bash
$ dig google.com

;; ANSWER SECTION:
google.com.             123     IN      A       142.250.201.46
#                        ^^^
#                        TTL = 123 secondes restantes
```

**Signification** :
- TTL = 123s → L'enregistrement sera gardé en cache 123 secondes
- Après 123s → Cache expiré, nouvelle requête DNS nécessaire

**Valeurs courantes** :
- **300s (5 min)** : Sites avec changements fréquents
- **3600s (1h)** : Sites stables typiques
- **86400s (24h)** : Sites très stables (serveurs DNS, etc.)
- **60s ou moins** : Avant migration DNS (réduire propagation)

**Impact** :
- ✅ TTL élevé → Moins de requêtes DNS, plus rapide, moins de charge
- ❌ TTL élevé → Changements DNS propagent lentement
- ✅ TTL faible → Changements DNS rapides
- ❌ TTL faible → Plus de requêtes DNS, plus lent

---

## 📝 Types d'Enregistrements DNS

Un serveur DNS stocke différents **types d'enregistrements** pour chaque domaine.

### Tableau Récapitulatif des Types DNS Courants

| Type | Nom Complet | Rôle | Exemple |
|------|-------------|------|---------|
| **A** | Address | Nom de domaine → **IPv4** | `google.com → 142.250.201.46` |
| **AAAA** | IPv6 Address | Nom de domaine → **IPv6** | `google.com → 2a00:1450:4007:80c::200e` |
| **CNAME** | Canonical Name | **Alias** (nom → autre nom) | `www.example.com → example.com` |
| **MX** | Mail Exchange | Serveurs de **mail** pour domaine | `10 smtp.google.com.` |
| **NS** | Name Server | Serveurs **DNS autoritaires** | `ns1.google.com.` |
| **TXT** | Text | Texte arbitraire (SPF, DKIM, vérifications) | `"v=spf1 include:_spf.google.com ~all"` |
| **PTR** | Pointer | **Résolution inverse** (IP → nom) | `46.201.250.142.in-addr.arpa → google.com` |
| **SOA** | Start of Authority | Infos zone DNS (admin, serial, refresh) | Métadonnées zone |
| **SRV** | Service | Localisation de **services** (port, priorité) | `_sip._tcp.example.com` |
| **CAA** | Certification Authority Authorization | Autorités certifs SSL autorisées | `0 issue "letsencrypt.org"` |

---

### A - Address (IPv4) 📍

**Rôle** : Associe un **nom de domaine** à une **adresse IPv4**.

**Format** : `nom_domaine. TTL IN A adresse_ipv4`

**Exemples** :

```
google.com.              300    IN    A    142.250.201.46
www.example.com.         3600   IN    A    93.184.216.34
mail.example.com.        600    IN    A    192.168.1.50
```

**Requête** :

```bash
$ dig google.com A +short
142.250.201.46

$ host -t A google.com
google.com has address 142.250.201.46
```

**Usage** :
- Type d'enregistrement **le plus courant**
- Chaque site web nécessite au moins un enregistrement A
- Peut y avoir **plusieurs enregistrements A** pour un même domaine (load balancing)

---

### AAAA - IPv6 Address 📍📍

**Rôle** : Associe un **nom de domaine** à une **adresse IPv6**.

**Format** : `nom_domaine. TTL IN AAAA adresse_ipv6`

**Exemples** :

```
google.com.              300    IN    AAAA    2a00:1450:4007:80c::200e
www.example.com.         3600   IN    AAAA    2606:2800:220:1:248:1893:25c8:1946
```

**Requête** :

```bash
$ dig google.com AAAA +short
2a00:1450:4007:80c::200e

$ host -t AAAA google.com
google.com has IPv6 address 2a00:1450:4007:80c::200e
```

**Note** : Nom **AAAA** car IPv6 = 4× plus long qu'IPv4 (128 bits vs 32 bits) → 4× "A" 😄

---

### CNAME - Canonical Name (Alias) 🔗

**Rôle** : Crée un **alias** (nom alternatif) pointant vers un autre nom de domaine.

**Format** : `alias. TTL IN CNAME nom_canonique.`

**Exemples** :

```
www.example.com.         3600   IN    CNAME   example.com.
ftp.example.com.         3600   IN    CNAME   example.com.
blog.example.com.        300    IN    CNAME   example.github.io.
```

**Requête** :

```bash
$ dig www.github.com +short
github.com.

$ host -t CNAME www.github.com
www.github.com is an alias for github.com.
```

**Usage** :
- ✅ Centraliser changements d'IP (modifier seulement l'enregistrement A principal)
- ✅ Rediriger sous-domaines vers CDN, services tiers
- ❌ **Interdit** pour domaine racine (`example.com` ne peut pas être CNAME)
- ❌ **Incompatible** avec autres enregistrements au même nom (sauf DNSSEC)

**Exemple de résolution en chaîne** :

```bash
$ dig www.example.com

;; ANSWER SECTION:
www.example.com.         3600   IN    CNAME   example.com.
example.com.             3600   IN    A       93.184.216.34
```

1. `www.example.com` → CNAME → `example.com`
2. `example.com` → A → `93.184.216.34`
3. Résultat final : `93.184.216.34`

---

### MX - Mail Exchange (Serveurs Mail) 📧

**Rôle** : Indique les **serveurs de messagerie** qui acceptent les emails pour un domaine.

**Format** : `domaine. TTL IN MX priorité serveur_mail.`

**Exemples** :

```
google.com.              3600   IN    MX    10 smtp.google.com.
google.com.              3600   IN    MX    20 smtp2.google.com.
google.com.              3600   IN    MX    30 smtp-backup.google.com.
```

**Priorité** : Valeur numérique (0-65535)
- **Plus bas = plus prioritaire**
- Serveur MX 10 est tenté **avant** serveur MX 20
- Si MX 10 indisponible → Essayer MX 20, puis MX 30, etc.

**Requête** :

```bash
$ dig google.com MX +short
10 smtp.google.com.

$ host -t MX google.com
google.com mail is handled by 10 smtp.google.com.
```

**Usage** :
- Obligatoire pour recevoir des emails
- Permet **redondance** (plusieurs serveurs de secours)
- Permet **load balancing** (même priorité = répartition)

**Scénario** : Envoi d'email à `user@example.com`
1. Requête DNS : `MX de example.com ?`
2. Réponse : `10 mail.example.com.`
3. Résolution : `A de mail.example.com ?` → `192.168.1.50`
4. Connexion SMTP vers `192.168.1.50:25`

---

### NS - Name Server (Serveurs DNS Autoritaires) 🏛️

**Rôle** : Indique les **serveurs DNS autoritaires** responsables d'un domaine.

**Format** : `domaine. TTL IN NS serveur_dns.`

**Exemples** :

```
google.com.              21600  IN    NS    ns1.google.com.
google.com.              21600  IN    NS    ns2.google.com.
google.com.              21600  IN    NS    ns3.google.com.
google.com.              21600  IN    NS    ns4.google.com.
```

**Requête** :

```bash
$ dig google.com NS +short
ns1.google.com.
ns2.google.com.
ns3.google.com.
ns4.google.com.

$ host -t NS google.com
google.com name server ns1.google.com.
```

**Usage** :
- Définit **qui gère** les enregistrements DNS du domaine
- Utilisé lors de la **délégation** de sous-domaines
- Plusieurs serveurs NS pour **redondance**

**Concept de délégation** :

```
Zone: example.com
NS: ns1.example.com, ns2.example.com

Sous-zone déléguée: sub.example.com
sub.example.com.    IN    NS    ns1.subdomain.com.
sub.example.com.    IN    NS    ns2.subdomain.com.
```

---

### TXT - Text (Texte Arbitraire) 📝

**Rôle** : Stocke du **texte libre** pour diverses utilisations (vérification domaine, SPF, DKIM, etc.).

**Format** : `domaine. TTL IN TXT "texte arbitraire"`

**Exemples** :

```
example.com.             3600   IN    TXT   "v=spf1 include:_spf.google.com ~all"
_dmarc.example.com.      3600   IN    TXT   "v=DMARC1; p=reject; rua=mailto:dmarc@example.com"
example.com.             300    IN    TXT   "google-site-verification=abcd1234..."
```

**Requête** :

```bash
$ dig google.com TXT +short
"v=spf1 include:_spf.google.com ~all"
"docusign=1234abcd..."
"globalsign-smime-dv=..."

$ host -t TXT google.com
google.com descriptive text "v=spf1 include:_spf.google.com ~all"
```

**Usages courants** :

1. **SPF (Sender Policy Framework)** : Lutte anti-spam
   ```
   "v=spf1 include:_spf.google.com ~all"
   # Autorise serveurs Google à envoyer emails pour ce domaine
   ```

2. **DKIM (DomainKeys Identified Mail)** : Signature emails
   ```
   selector._domainkey.example.com. IN TXT "v=DKIM1; k=rsa; p=MIGfMA0..."
   ```

3. **DMARC (Domain-based Message Authentication)** : Politique emails
   ```
   "_dmarc.example.com. IN TXT "v=DMARC1; p=reject; rua=mailto:reports@example.com"
   ```

4. **Vérification propriété domaine** : Google Search Console, services tiers
   ```
   "google-site-verification=abcd1234xyz..."
   ```

5. **Informations générales** :
   ```
   "Contact: admin@example.com"
   ```

---

### PTR - Pointer (Résolution Inverse) 🔄

**Rôle** : **Résolution inverse** = Convertir une **adresse IP en nom de domaine**.

**Format spécial** : Utilise domaines spéciaux `.in-addr.arpa` (IPv4) et `.ip6.arpa` (IPv6)

**IPv4 inverse** :

```
# IP: 192.0.2.1
# Domaine inverse: 1.2.0.192.in-addr.arpa.
1.2.0.192.in-addr.arpa.  3600   IN    PTR   example.com.
```

**Syntaxe** : IP écrite **à l'envers** + `.in-addr.arpa`

**Requête** :

```bash
$ dig -x 8.8.8.8 +short
dns.google.

$ host 8.8.8.8
8.8.8.8.in-addr.arpa domain name pointer dns.google.
```

**IPv6 inverse** : `.ip6.arpa` (encore plus complexe, chaque nibble hexadécimal inversé)

**Usage** :
- Serveurs de mail (vérification anti-spam, reverse DNS obligatoire)
- Logs serveurs (afficher noms au lieu d'IPs)
- Sécurité (vérifier identité serveur distant)

⚠️ **Important** : PTR géré par le propriétaire de l'IP (FAI, hébergeur), pas propriétaire du domaine !

---

### SOA - Start of Authority (Métadonnées Zone) 📜

**Rôle** : Contient les **métadonnées** d'une zone DNS (serveur principal, admin, numéros de série, timers).

**Format** :

```
exemple.com. 3600 IN SOA ns1.exemple.com. admin.exemple.com. (
    2024021201 ; Serial (YYYYMMDDnn)
    7200       ; Refresh (2h)
    3600       ; Retry (1h)
    1209600    ; Expire (2 semaines)
    86400      ; Negative Cache TTL (1 jour)
)
```

**Champs** :
- **ns1.exemple.com.** : Serveur DNS principal (master)
- **admin.exemple.com.** : Email administrateur (`admin@exemple.com`, @ remplacé par .)
- **Serial** : Numéro de version zone (incrémenté à chaque modification)
- **Refresh** : Fréquence vérification slave vers master (secondes)
- **Retry** : Délai retry si refresh échoue
- **Expire** : Durée max sans refresh avant invalidation zone slave
- **Negative Cache TTL** : Durée cache pour réponses NXDOMAIN (domaine inexistant)

**Requête** :

```bash
$ dig google.com SOA +short
ns1.google.com. dns-admin.google.com. 605031139 900 900 1800 60

$ host -t SOA google.com
google.com has SOA record ns1.google.com. dns-admin.google.com. 605031139 900 900 1800 60
```

**Usage** :
- 📌 **Automatique**, rarement manipulé directement
- Important pour **synchronisation** serveurs DNS primaire/secondaire

---

### SRV - Service (Localisation Services) 🎯

**Rôle** : Indique **hôte et port** pour un service spécifique (SIP, XMPP, LDAP, etc.).

**Format** : `_service._proto.domaine. TTL IN SRV priorité poids port cible.`

**Exemple** :

```
_sip._tcp.example.com.   3600   IN    SRV   10 60 5060 sipserver.example.com.
_xmpp._tcp.example.com.  3600   IN    SRV   5  0  5222 xmpp.example.com.
_ldap._tcp.example.com.  3600   IN    SRV   0  0  389  ldap.example.com.
```

**Champs** :
- **Priorité** : Comme MX, plus bas = prioritaire
- **Poids** : Load balancing entre serveurs même priorité
- **Port** : Numéro de port du service
- **Cible** : Nom d'hôte du serveur

**Requête** :

```bash
$ dig _sip._tcp.example.com SRV

$ host -t SRV _sip._tcp.example.com
```

**Usage** :
- VoIP (SIP, H.323)
- Messagerie instantanée (XMPP/Jabber)
- Active Directory (LDAP, Kerberos)

---

### CAA - Certification Authority Authorization 🔐

**Rôle** : Spécifie quelles **autorités de certification (CA)** sont autorisées à émettre des certificats SSL/TLS pour un domaine.

**Format** : `domaine. TTL IN CAA flags tag "value"`

**Exemples** :

```
example.com.             3600   IN    CAA   0 issue "letsencrypt.org"
example.com.             3600   IN    CAA   0 issuewild ";"
example.com.             3600   IN    CAA   0 iodef "mailto:security@example.com"
```

**Tags** :
- `issue` : Autoriser émission certificats pour domaine
- `issuewild` : Autoriser certificats wildcard (`*.example.com`)
- `iodef` : Contact pour violations (email/URL)

**Requête** :

```bash
$ dig example.com CAA

$ host -t CAA example.com
```

**Usage** :
- Sécurité supplémentaire contre certificats frauduleux
- Obligatoire depuis 2017 pour CAs (RFC 6844)

---

## 🔧 Commande `host` - Résolution DNS Simple

### Description

`host` est un **outil simple** pour résolution DNS rapide.

**Avantages** :
- ✅ Syntaxe simple et intuitive
- ✅ Sortie concise et lisible
- ✅ Rapide pour requêtes basiques

**Inconvénients** :
- ❌ Moins de détails que `dig`
- ❌ Moins d'options de formatage

### Installation

Fait partie du paquet `bind-utils` (RHEL/CentOS) ou `dnsutils` (Debian/Ubuntu).

```bash
# Debian/Ubuntu
sudo apt install dnsutils

# RHEL/CentOS
sudo yum install bind-utils
```

### Syntaxe de Base

```bash
host [OPTIONS] <NOM_DOMAINE> [<SERVEUR_DNS>]
```

### Options Principales

| Option | Description | Exemple |
|--------|-------------|---------|
| `-t <type>` | Type d'enregistrement (A, AAAA, MX, NS, etc.) | `host -t MX google.com` |
| `-a` | All (équivalent à `-t ANY`) | `host -a google.com` |
| `-v` | Verbose (sortie détaillée) | `host -v google.com` |
| `-l` | Lister tous les hôtes d'une zone (zone transfer) | `host -l example.com ns1.example.com` |
| `-r` | Non-récursif | `host -r google.com` |
| `-T` | Utiliser TCP au lieu d'UDP | `host -T google.com` |
| `-W <time>` | Timeout en secondes (défaut 5s) | `host -W 2 google.com` |
| `-4` | IPv4 uniquement | `host -4 google.com` |
| `-6` | IPv6 uniquement | `host -6 google.com` |

### Exemples d'Utilisation

**Résolution simple (type A par défaut)** :

```bash
$ host google.com
google.com has address 142.250.201.46
google.com has IPv6 address 2a00:1450:4007:80c::200e
google.com mail is handled by 10 smtp.google.com.
```

**Résolution spécifique type A** :

```bash
$ host -t A google.com
google.com has address 142.250.201.46
```

**Résolution type AAAA (IPv6)** :

```bash
$ host -t AAAA google.com
google.com has IPv6 address 2a00:1450:4007:80c::200e
```

**Résolution type MX (mail)** :

```bash
$ host -t MX google.com
google.com mail is handled by 10 smtp.google.com.
```

**Résolution type NS (serveurs DNS)** :

```bash
$ host -t NS google.com
google.com name server ns1.google.com.
google.com name server ns2.google.com.
google.com name server ns3.google.com.
google.com name server ns4.google.com.
```

**Résolution type TXT** :

```bash
$ host -t TXT google.com
google.com descriptive text "v=spf1 include:_spf.google.com ~all"
google.com descriptive text "docusign=..."
```

**Résolution type SOA** :

```bash
$ host -t SOA google.com
google.com has SOA record ns1.google.com. dns-admin.google.com. 605031139 900 900 1800 60
```

**Résolution type CNAME** :

```bash
$ host -t CNAME www.github.com
www.github.com is an alias for github.com.
```

**Résolution inverse (IP → nom)** :

```bash
$ host 8.8.8.8
8.8.8.8.in-addr.arpa domain name pointer dns.google.

$ host 142.250.201.46
46.201.250.142.in-addr.arpa domain name pointer par21s17-in-f14.1e100.net.
```

**Utiliser un serveur DNS spécifique** :

```bash
# Google DNS
$ host google.com 8.8.8.8
Using domain server:
Name: 8.8.8.8
Address: 8.8.8.8#53
Aliases:

google.com has address 142.250.201.46

# Cloudflare DNS
$ host google.com 1.1.1.1

# DNS local
$ host example.local 192.168.1.1
```

**Mode verbose** :

```bash
$ host -v google.com
Trying "google.com"
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 12345
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             123     IN      A       142.250.201.46

Received 44 bytes from 192.168.1.254#53 in 12 ms
```

**Résolution de tous types (ANY)** :

```bash
$ host -a google.com
# Affiche A, AAAA, MX, NS, TXT, SOA, etc.
```

---

## 🔍 Commande `dig` - DNS Avancé (Rappel Approfondi)

### Description

`dig` (Domain Information Groper) est l'outil **le plus complet** pour requêtes DNS.

💡 **Déjà couvert dans 109.3**, mais approfondi ici pour configuration DNS côté client.

### Structure d'une Réponse `dig`

```bash
$ dig google.com

; <<>> DiG 9.18.1-1ubuntu1.3-Ubuntu <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 12345
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             123     IN      A       142.250.201.46

;; Query time: 12 msec
;; SERVER: 192.168.1.254#53(192.168.1.254)
;; WHEN: Wed Feb 12 15:30:25 CET 2026
;; MSG SIZE  rcvd: 55
```

**Sections importantes** :

1. **HEADER** : Informations sur la requête
   - `status: NOERROR` : Succès (autres : NXDOMAIN, SERVFAIL, etc.)
   - `flags: qr rd ra` :
     - `qr` = Query Response (c'est une réponse)
     - `rd` = Recursion Desired (récursion demandée)
     - `ra` = Recursion Available (récursion disponible)

2. **QUESTION SECTION** : Requête envoyée
   ```
   ;google.com.                    IN      A
   #  Domaine                     Classe  Type
   ```
   - Classe `IN` = Internet (toujours IN)

3. **ANSWER SECTION** : Réponse reçue
   ```
   google.com.             123     IN      A       142.250.201.46
   #  Domaine              TTL   Classe Type     Réponse
   ```
   - **TTL = 123 secondes** → Durée de cache restante

4. **AUTHORITY SECTION** : Serveurs DNS autoritaires (optionnel)

5. **ADDITIONAL SECTION** : Informations supplémentaires (IPs des serveurs NS, etc.)

6. **STATS** :
   - `Query time: 12 msec` → Temps de réponse
   - `SERVER: 192.168.1.254#53` → Serveur DNS utilisé
   - `WHEN` → Timestamp requête
   - `MSG SIZE` → Taille message DNS

---

### Exemples Pratiques avec TTL

**Voir le TTL d'un enregistrement** :

```bash
$ dig google.com +noall +answer
google.com.             123     IN      A       142.250.201.46
#                        ^^^
#                        TTL = 123 secondes
```

**Observer le TTL décrémenter** :

```bash
# Requête 1
$ dig google.com +noall +answer
google.com.             300     IN      A       142.250.201.46

# Attendre 10 secondes...

# Requête 2 (depuis cache local)
$ dig google.com +noall +answer
google.com.             290     IN      A       142.250.201.46
#                        ^^^
#                    290 = 300 - 10 secondes écoulées
```

**Forcer requête sans cache** :

```bash
# Vider cache DNS local d'abord
sudo systemd-resolve --flush-caches  # systemd-resolved
# ou
sudo service nscd restart             # nscd

# Puis requêter
$ dig google.com +noall +answer
google.com.             300     IN      A       142.250.201.46
#                        ^^^
#                TTL complet (réponse fraîche du serveur)
```

---

### Utiliser un Serveur DNS Spécifique avec `@`

**Syntaxe** : `dig @<serveur_dns> <domaine>`

**Exemples** :

```bash
# Google Public DNS (8.8.8.8)
$ dig @8.8.8.8 google.com +short
142.250.201.46

# Cloudflare DNS (1.1.1.1)
$ dig @1.1.1.1 google.com +short
142.250.201.46

# Quad9 DNS (9.9.9.9)
$ dig @9.9.9.9 google.com +short
142.250.201.46

# DNS local (routeur/box)
$ dig @192.168.1.1 google.com

# Serveur DNS autoritaire Google directement
$ dig @ns1.google.com google.com
```

**Comparer réponses de différents serveurs DNS** :

```bash
echo "=== Google DNS ==="
dig @8.8.8.8 example.com +short

echo "=== Cloudflare DNS ==="
dig @1.1.1.1 example.com +short

echo "=== DNS Local ==="
dig @192.168.1.1 example.com +short
```

**Usage** :
- Tester si problème DNS vient du serveur configuré
- Vérifier propagation DNS (serveurs différents = réponses différentes ?)
- Bypass DNS FAI (censure, filtrage parental)

---

### Options de Formatage `dig`

**Affichage court (réponse uniquement)** :

```bash
$ dig google.com +short
142.250.201.46
```

**Afficher seulement section ANSWER** :

```bash
$ dig google.com +noall +answer
google.com.             123     IN      A       142.250.201.46
```

**Afficher ANSWER et AUTHORITY** :

```bash
$ dig google.com +noall +answer +authority
```

**Afficher toutes les sections** :

```bash
$ dig google.com +all
```

**Tracer la résolution DNS complète** :

```bash
$ dig +trace google.com
# Affiche Root servers → TLD .com → Autoritaires google.com
```

**Différents types** :

```bash
dig google.com A           # IPv4
dig google.com AAAA        # IPv6
dig google.com MX          # Mail servers
dig google.com NS          # DNS servers
dig google.com TXT         # Text records
dig google.com SOA         # Start of Authority
dig google.com ANY         # Tous types
```

**Résolution inverse** :

```bash
dig -x 8.8.8.8 +short
dns.google.
```

---

## 🔎 Commande `getent` - Résolution Système

### Description

`getent` (Get Entries) interroge les **bases de données système** configurées via `/etc/nsswitch.conf`.

**Différence avec `dig`/`host`** :
- `dig`/`host` : Interrogent **directement** les serveurs DNS
- `getent` : Suit la **configuration système** (`/etc/nsswitch.conf`)
  - Peut chercher dans `/etc/hosts` local **avant** interroger DNS
  - Respecte l'ordre de résolution configuré

### Syntaxe

```bash
getent <database> <clé>
```

**Databases courantes** :
- `hosts` : Résolution hostname → IP
- `ahosts` : Résolution avec infos de socket (IPv4/IPv6)
- `ahostsv4` : IPv4 uniquement
- `ahostsv6` : IPv6 uniquement
- `passwd` : Utilisateurs système
- `group` : Groupes système
- `services` : Services réseau et ports
- `protocols` : Protocoles réseau

### Exemples avec `hosts`

**Résolution hostname** :

```bash
$ getent hosts google.com
142.250.201.46  google.com
```

**Différence avec `/etc/hosts` local** :

```bash
# Ajouter entrée locale
echo "127.0.0.1 google.com" | sudo tee -a /etc/hosts

# dig ignore /etc/hosts (interroge DNS directement)
$ dig google.com +short
142.250.201.46

# getent respecte /etc/hosts (priorité selon nsswitch.conf)
$ getent hosts google.com
127.0.0.1       google.com
#  ^^^
#  Résolution locale prioritaire !

# Retirer l'entrée
sudo sed -i '/127.0.0.1 google.com/d' /etc/hosts
```

**Résolution avec détails socket (ahosts)** :

```bash
$ getent ahosts google.com
142.250.201.46  STREAM google.com
142.250.201.46  DGRAM
142.250.201.46  RAW
2a00:1450:4007:80c::200e STREAM
2a00:1450:4007:80c::200e DGRAM
2a00:1450:4007:80c::200e RAW
```

**Colonnes** :
- **IP** : Adresse résolue
- **Type socket** : STREAM (TCP), DGRAM (UDP), RAW (raw sockets)
- **Hostname** : Nom (affiché seulement première fois)

**Résolution IPv4 uniquement** :

```bash
$ getent ahostsv4 google.com
142.250.201.46  STREAM google.com
142.250.201.46  DGRAM
142.250.201.46  RAW
```

**Résolution IPv6 uniquement** :

```bash
$ getent ahostsv6 google.com
2a00:1450:4007:80c::200e STREAM google.com
2a00:1450:4007:80c::200e DGRAM
2a00:1450:4007:80c::200e RAW
```

**Résolution inverse** :

```bash
$ getent hosts 8.8.8.8
8.8.8.8         dns.google
```

---

### Exemples avec Autres Databases

**Services (ports)** :

```bash
# Résoudre nom service → port
$ getent services ssh
ssh                   22/tcp

$ getent services http
http                  80/tcp www www-http

# Résoudre port → nom service
$ getent services 443/tcp
https                 443/tcp

$ getent services 3306/tcp
mysql                 3306/tcp
```

**Utilisateurs système** :

```bash
$ getent passwd root
root:x:0:0:root:/root:/bin/bash

$ getent passwd $(whoami)
alice:x:1000:1000:Alice Smith,,,:/home/alice:/bin/bash
```

**Groupes système** :

```bash
$ getent group sudo
sudo:x:27:alice,bob

$ getent group docker
docker:x:999:alice
```

---

### Utilité de `getent` pour DNS

**Scénario** : Débogage résolution DNS

```bash
# 1. Tester résolution système (respecte /etc/hosts + DNS)
$ getent hosts example.local
192.168.1.50    example.local

# 2. Tester interrogation DNS directe
$ dig example.local +short
# (vide si seulement dans /etc/hosts)

# 3. Vérifier ordre résolution
$ grep ^hosts /etc/nsswitch.conf
hosts:          files dns
#                ^^^  ^^^
#               /etc/hosts prioritaire, puis DNS
```

**Cas d'usage** :
- ✅ Tester résolution telle que vue par les **applications**
- ✅ Vérifier priorité `/etc/hosts` vs DNS
- ✅ Diagnostiquer blocages via `/etc/hosts` (ad-blocking, etc.)

---

## ⚙️ Configuration DNS Côté Client

### Fichier `/etc/resolv.conf` (Rappel)

**Rôle** : Configurer les **serveurs DNS** utilisés par le système.

**Contenu typique** :

```bash
$ cat /etc/resolv.conf
nameserver 8.8.8.8
nameserver 8.8.4.4
search example.com internal.local
options timeout:2 attempts:3
```

**Directives** :

- **`nameserver <IP>`** : Serveur DNS à utiliser (max 3)
  ```
  nameserver 8.8.8.8
  nameserver 1.1.1.1
  nameserver 192.168.1.1
  ```
  - Interrogés dans l'ordre
  - Si premier timeout → 2ème essayé, etc.

- **`search <domain1> <domain2> ...`** : Domaines de recherche (auto-complétion)
  ```
  search example.com lab.local
  ```
  - Si vous tapez `ping web` :
    1. Essaie `web.example.com`
    2. Si échec, essaie `web.lab.local`
    3. Si échec, essaie `web` (nom court)

- **`domain <domain>`** : Domaine local (obsolète, remplacé par `search`)

- **`options <option:value>`** : Options avancées
  ```
  options timeout:2         # Timeout par serveur DNS (secondes)
  options attempts:3        # Nombre de tentatives
  options rotate            # Rotation serveurs (load balancing)
  options single-request    # Éviter problèmes IPv4/IPv6
  ```

**Gestion automatique** :
- ⚠️ Souvent géré par **NetworkManager** ou **systemd-resolved**
- Modifications manuelles peuvent être **écrasées** automatiquement !

**Désactiver gestion automatique** :

```bash
# NetworkManager
sudo nano /etc/NetworkManager/NetworkManager.conf
# Ajouter :
[main]
dns=none

sudo systemctl restart NetworkManager

# systemd-resolved (voir section suivante)
sudo systemctl disable systemd-resolved
sudo rm /etc/resolv.conf
sudo nano /etc/resolv.conf
# (créer fichier statique)
```

---

### Fichier `/etc/hosts` (Rappel)

**Rôle** : Résolution **locale** (name → IP) **avant** interrogation DNS.

**Format** :

```
<IP>    <hostname>  [alias1]  [alias2]  ...
```

**Exemple** :

```bash
$ cat /etc/hosts
127.0.0.1       localhost
127.0.1.1       mycomputer.local mycomputer
::1             localhost ip6-localhost ip6-loopback

# Réseau local
192.168.1.10    server-web web www
192.168.1.20    server-db db mysql
192.168.1.30    nas.local nas

# Blocage publicités
0.0.0.0         ads.example.com
0.0.0.0         tracker.bad.net
```

**Priorité** :

Définie dans `/etc/nsswitch.conf` :

```bash
$ grep ^hosts /etc/nsswitch.conf
hosts:          files dns mdns4_minimal [NOTFOUND=return] dns mdns4
#                ^^^  ^^^
#           1. files (/etc/hosts)
#           2. dns (serveurs DNS)
```

**Test** :

```bash
# Ajouter entrée
echo "127.0.0.1 test.local" | sudo tee -a /etc/hosts

# Tester avec getent (respecte /etc/hosts)
$ getent hosts test.local
127.0.0.1       test.local

# Tester avec dig (ignore /etc/hosts)
$ dig test.local +short
# (rien, car pas dans DNS)

# Tester avec ping (respecte /etc/hosts)
$ ping -c 1 test.local
PING test.local (127.0.0.1) ...
```

---

### Fichier `/etc/nsswitch.conf` (Rappel)

**Rôle** : Définir **ordre de résolution** pour différents services (hosts, passwd, group, etc.).

**Ligne importante pour DNS** :

```bash
$ grep ^hosts /etc/nsswitch.conf
hosts:          files dns mdns4_minimal [NOTFOUND=return] dns mdns4
```

**Sources courantes** :
- `files` : `/etc/hosts`
- `dns` : Serveurs DNS (`/etc/resolv.conf`)
- `mdns4` / `mdns6` : Multicast DNS (Avahi, réseau local `.local`)
- `wins` : Windows Internet Name Service
- `nis` : Network Information Service (obsolète)

**Ordre classique** :

```
hosts:          files dns
```
1. Chercher dans `/etc/hosts`
2. Si pas trouvé → Interroger DNS

**Avec mDNS (Avahi)** :

```
hosts:          files mdns4_minimal [NOTFOUND=return] dns mdns4
```
1. `/etc/hosts`
2. mDNS pour noms `.local` seulement
3. Si `.local` pas trouvé → STOP (ne pas interroger DNS)
4. Sinon → DNS
5. En dernier recours → mDNS général

---

## 🔄 systemd-resolved - Résolveur DNS Moderne

### Introduction

**systemd-resolved** = Service de **résolution DNS** intégré à systemd.

**Rôle** :
- Agit comme **résolveur DNS local** (cache)
- Écoute sur **127.0.0.53:53** (loopback)
- Centralise la configuration DNS
- Supporte DNSSEC, DNS-over-TLS

**Avantages** :
- ✅ Cache DNS local (résolutions plus rapides)
- ✅ Intégration NetworkManager
- ✅ DNS par interface (différents DNS pour VPN, LAN, etc.)
- ✅ Validation DNSSEC

**Inconvénients** :
- ❌ Complexité supplémentaire
- ❌ Peut entrer en conflit avec autres résolveurs (dnsmasq, etc.)

---

### Architecture systemd-resolved

```
┌─────────────────────────────────────────────────┐
│ Application (curl, ping, navigateur)           │
└───────────────────┬─────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────────────┐
│ /etc/resolv.conf                                │
│ → Lien symbolique vers                          │
│   /run/systemd/resolve/stub-resolv.conf         │
│                                                 │
│   nameserver 127.0.0.53                         │
└───────────────────┬─────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────────────┐
│ systemd-resolved (127.0.0.53:53)               │
│ • Cache DNS local                               │
│ • Gestion DNSSEC                                │
│ • Configuration par interface                   │
└───────────────────┬─────────────────────────────┘
                    │
        ┌───────────┼───────────┐
        ▼           ▼           ▼
     8.8.8.8    1.1.1.1    192.168.1.1
  (Google DNS) (Cloudflare) (DNS local)
```

---

### Vérifier si systemd-resolved est Actif

```bash
# Statut du service
$ systemctl status systemd-resolved
● systemd-resolved.service - Network Name Resolution
     Loaded: loaded (/lib/systemd/system/systemd-resolved.service; enabled)
     Active: active (running) since ...

# Vérifier /etc/resolv.conf
$ ls -la /etc/resolv.conf
lrwxrwxrwx 1 root root 39 Jan 10 10:00 /etc/resolv.conf -> ../run/systemd/resolve/stub-resolv.conf
#  ^^^
#  Lien symbolique = systemd-resolved actif

# Voir contenu
$ cat /etc/resolv.conf
# This is /run/systemd/resolve/stub-resolv.conf
nameserver 127.0.0.53
options edns0 trust-ad
search example.com

# Écoute sur 127.0.0.53
$ sudo ss -tlnp | grep 127.0.0.53
LISTEN  0  4096  127.0.0.53%lo:53  0.0.0.0:*  users:(("systemd-resolve",pid=12345))
```

---

### Configuration systemd-resolved

**Fichier principal** : `/etc/systemd/resolved.conf`

```bash
$ cat /etc/systemd/resolved.conf
[Resolve]
DNS=8.8.8.8 1.1.1.1
FallbackDNS=8.8.4.4 1.0.0.1
Domains=example.com
DNSSEC=allow-downgrade
DNSOverTLS=opportunistic
#Cache=yes
#DNSStubListener=yes
```

**Directives** :

- **`DNS=<IP1> <IP2> ...`** : Serveurs DNS principaux
  ```
  DNS=8.8.8.8 1.1.1.1
  ```

- **`FallbackDNS=<IP1> <IP2> ...`** : Serveurs DNS de secours
  ```
  FallbackDNS=8.8.4.4 1.0.0.1
  ```

- **`Domains=<domain1> <domain2> ...`** : Domaines de recherche
  ```
  Domains=example.com lab.local
  ```

- **`DNSSEC=yes|no|allow-downgrade`** : Validation DNSSEC
  - `yes` : Obligatoire (échoue si pas supporté)
  - `no` : Désactivé
  - `allow-downgrade` : Activé mais fallback si pas supporté (défaut)

- **`DNSOverTLS=yes|no|opportunistic`** : DNS chiffré (TLS)
  - `yes` : Obligatoire
  - `no` : Désactivé
  - `opportunistic` : Utilisé si disponible (défaut)

- **`Cache=yes|no`** : Cache DNS local
  ```
  Cache=yes
  ```

- **`DNSStubListener=yes|no`** : Écoute sur 127.0.0.53
  ```
  DNSStubListener=yes
  ```

**Appliquer les changements** :

```bash
sudo systemctl restart systemd-resolved
```

---

### Commandes `resolvectl` / `systemd-resolve`

**`resolvectl`** (moderne) ou **`systemd-resolve`** (ancien, alias) permettent de gérer systemd-resolved.

#### Afficher Statut

```bash
# Statut général
$ resolvectl status
Global
       Protocols: +LLMNR +mDNS -DNSOverTLS DNSSEC=allow-downgrade/supported
resolv.conf mode: stub
Current DNS Server: 8.8.8.8
       DNS Servers: 8.8.8.8 1.1.1.1

Link 2 (enp3s0)
    Current Scopes: DNS
         Protocols: +DefaultRoute +LLMNR -mDNS -DNSOverTLS DNSSEC=allow-downgrade/supported
Current DNS Server: 192.168.1.1
       DNS Servers: 192.168.1.1 8.8.8.8
        DNS Domain: example.com
```

**Informations** :
- **Global** : Configuration globale
- **Link 2 (enp3s0)** : Configuration par interface

#### Statistiques Cache

```bash
$ resolvectl statistics
DNSSEC supported by current servers: yes

Transactions
Current Transactions: 0
  Total Transactions: 1234

Cache
  Current Cache Size: 56
          Cache Hits: 890
        Cache Misses: 344

DNSSEC Verdicts
              Secure: 123
            Insecure: 567
               Bogus: 0
       Indeterminate: 0
```

#### Vider le Cache DNS

```bash
$ sudo resolvectl flush-caches
# ou
$ sudo systemd-resolve --flush-caches
```

**Vérifier** :

```bash
$ resolvectl statistics
Cache
  Current Cache Size: 0
#  ^^^
#  Cache vidé
```

#### Requête DNS via systemd-resolved

```bash
# Résoudre nom
$ resolvectl query google.com
google.com: 142.250.201.46
            2a00:1450:4007:80c::200e

-- Information acquired via protocol DNS in 12ms.
-- Data is authenticated: no; Data was acquired via local or encrypted transport: no

# Résolution inverse
$ resolvectl query 8.8.8.8
8.8.8.8: dns.google

# Spécifier type
$ resolvectl query --type=MX google.com
google.com IN MX 10 smtp.google.com
```

---

### Fichiers systemd-resolved

**Structure** :

```
/etc/systemd/resolved.conf          # Configuration principale
/run/systemd/resolve/               # Fichiers générés dynamiquement
  ├── stub-resolv.conf              # Pointe vers 127.0.0.53
  ├── resolv.conf                   # DNS réels (sans 127.0.0.53)
  └── netif/                        # Config par interface
```

**`/run/systemd/resolve/stub-resolv.conf`** :

```bash
$ cat /run/systemd/resolve/stub-resolv.conf
nameserver 127.0.0.53
options edns0 trust-ad
search example.com
```

👉 Pointe vers **systemd-resolved** (`127.0.0.53`)

**`/run/systemd/resolve/resolv.conf`** :

```bash
$ cat /run/systemd/resolve/resolv.conf
nameserver 8.8.8.8
nameserver 1.1.1.1
search example.com
```

👉 Contient les **serveurs DNS réels** (utile pour conteneurs Docker, etc.)

---

### Désactiver systemd-resolved

Si vous préférez gérer DNS manuellement :

```bash
# 1. Arrêter et désactiver le service
sudo systemctl stop systemd-resolved
sudo systemctl disable systemd-resolved

# 2. Supprimer le lien symbolique
sudo rm /etc/resolv.conf

# 3. Créer fichier statique
sudo nano /etc/resolv.conf
# Contenu :
nameserver 8.8.8.8
nameserver 1.1.1.1

# 4. Protéger contre modifications
sudo chattr +i /etc/resolv.conf
# (optionnel, empêche NetworkManager de modifier)
```

**Réactiver** :

```bash
sudo chattr -i /etc/resolv.conf  # Si protégé
sudo rm /etc/resolv.conf
sudo ln -s /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf
sudo systemctl enable --now systemd-resolved
```

---

## 🛠️ Scénarios Pratiques

### Scénario 1 : Diagnostiquer Résolution DNS Lente 🐌

**Symptôme** : Sites web mettent 2-3 secondes à charger.

**Diagnostic** :

```bash
# 1. Mesurer temps résolution DNS
$ time host google.com
google.com has address 142.250.201.46
real    0m2.345s
#  ^^^
#  2.3 secondes = TRÈS lent !

# 2. Tester avec serveur DNS alternatif
$ time host google.com 8.8.8.8
google.com has address 142.250.201.46
real    0m0.015s
#  ^^^
#  15ms = rapide !

# Conclusion : Problème avec serveur DNS configuré

# 3. Vérifier /etc/resolv.conf
$ cat /etc/resolv.conf
nameserver 192.168.1.1  # DNS box lent ?

# 4. Changer pour DNS Google
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf

# 5. Vider cache
sudo resolvectl flush-caches  # systemd-resolved
# ou
sudo systemctl restart nscd    # nscd

# 6. Tester à nouveau
$ time host google.com
real    0m0.012s  # ✅ Rapide !
```

---

### Scénario 2 : Résoudre Domaine Interne `.local` 🏠

**Objectif** : Résoudre `server.local` sans interroger DNS public.

**Solution** : Utiliser `/etc/hosts`

```bash
# 1. Ajouter entrée locale
echo "192.168.1.50 server.local server" | sudo tee -a /etc/hosts

# 2. Tester avec getent (respecte /etc/hosts)
$ getent hosts server.local
192.168.1.50    server.local server

# 3. Tester avec ping
$ ping -c 1 server.local
PING server.local (192.168.1.50) ...
64 bytes from 192.168.1.50: ...

# 4. Vérifier priorité résolution
$ grep ^hosts /etc/nsswitch.conf
hosts:          files dns
#                ^^^
#           /etc/hosts prioritaire ✅

# 5. SSH avec nom
$ ssh admin@server.local
```

---

### Scénario 3 : Bloquer Domaines Publicitaires 🚫

**Objectif** : Bloquer `ads.example.com` et `tracker.bad.net` au niveau système.

**Solution** : Rediriger vers `0.0.0.0` dans `/etc/hosts`

```bash
# 1. Ajouter entrées de blocage
sudo tee -a /etc/hosts <<EOF
0.0.0.0 ads.example.com
0.0.0.0 tracker.bad.net
0.0.0.0 analytics.annoying.com
EOF

# 2. Tester résolution
$ getent hosts ads.example.com
0.0.0.0         ads.example.com

# 3. Tester connexion
$ curl http://ads.example.com
curl: (7) Failed to connect to ads.example.com port 80: Connection refused
#  ^^^
#  Bloqué ✅

# 4. Utiliser liste de blocage complète (optionnel)
# Source : https://github.com/StevenBlack/hosts
wget https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts -O /tmp/hosts-blocklist
sudo cat /tmp/hosts-blocklist >> /etc/hosts
```

---

### Scénario 4 : Vérifier Enregistrements MX pour Email 📧

**Objectif** : Configurer serveur mail, vérifier enregistrements MX corrects.

```bash
# 1. Vérifier enregistrements MX
$ host -t MX example.com
example.com mail is handled by 10 mail.example.com.
example.com mail is handled by 20 mail2.example.com.

# 2. Résoudre serveur mail principal
$ host mail.example.com
mail.example.com has address 192.168.1.50

# 3. Tester connexion SMTP (port 25)
$ nc -zv mail.example.com 25
Connection to mail.example.com 25 port [tcp/smtp] succeeded!

# 4. Vérifier TTL
$ dig example.com MX +noall +answer
example.com.             3600   IN      MX      10 mail.example.com.
#                        ^^^^
#                    TTL 1h (3600s)

# 5. Vérifier reverse DNS (important anti-spam!)
$ host 192.168.1.50
50.1.168.192.in-addr.arpa domain name pointer mail.example.com.
#  ^^^
#  Reverse DNS configuré ✅
```

---

### Scénario 5 : Configurer DNS Différent pour VPN 🔐

**Objectif** : Utiliser DNS d'entreprise (`10.0.0.1`) quand connecté au VPN.

**Solution** : systemd-resolved avec DNS par interface

```bash
# 1. Vérifier interface VPN
$ ip link show
...
5: tun0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> ...

# 2. Configurer DNS pour interface VPN
$ sudo resolvectl dns tun0 10.0.0.1

# 3. Définir domaines de recherche VPN
$ sudo resolvectl domain tun0 company.local

# 4. Vérifier configuration
$ resolvectl status tun0
Link 5 (tun0)
Current DNS Server: 10.0.0.1
       DNS Servers: 10.0.0.1
        DNS Domain: company.local

# 5. Tester résolution domaine interne
$ host server.company.local
server.company.local has address 10.0.1.50
```

**Permanent** : Configurer via NetworkManager

```bash
# Fichier de connexion VPN
sudo nano /etc/NetworkManager/system-connections/Company-VPN.nmconnection

# Ajouter section
[ipv4]
dns=10.0.0.1;
dns-search=company.local;
```

---

## ❌ Erreurs Courantes et Solutions

### Erreur 1 : "Temporary failure in name resolution"

```bash
$ ping google.com
ping: google.com: Temporary failure in name resolution
```

**Causes** :
- ❌ Pas de serveur DNS configuré (`/etc/resolv.conf` vide)
- ❌ Serveur DNS inaccessible
- ❌ Pas de connexion réseau

**Solutions** :

```bash
# Vérifier /etc/resolv.conf
$ cat /etc/resolv.conf
# Si vide :
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf

# Tester connectivité DNS
$ ping -c 1 8.8.8.8
# Si échec → Problème réseau, pas DNS

# Vérifier service systemd-resolved
$ systemctl status systemd-resolved
# Si inactif :
sudo systemctl start systemd-resolved
```

---

### Erreur 2 : Résolution fonctionne avec `dig` mais pas `ping`

```bash
$ dig google.com +short
142.250.201.46  # ✅ Fonctionne

$ ping google.com
ping: google.com: Name or service not known  # ❌ Échec
```

**Cause** : `/etc/nsswitch.conf` mal configuré

**Solution** :

```bash
# Vérifier nsswitch.conf
$ grep ^hosts /etc/nsswitch.conf
hosts:          dns files
#  ^^^
#  Ordre inversé !

# Corriger (files avant dns)
sudo nano /etc/nsswitch.conf
# Modifier :
hosts:          files dns mdns4_minimal [NOTFOUND=return] dns mdns4

# Tester
$ ping -c 1 google.com
# ✅ Fonctionne
```

---

### Erreur 3 : DNS Lent Malgré Bon Serveur

```bash
$ time host google.com
real    0m2.000s  # 2s = trop lent
```

**Cause** : IPv6 activé mais non fonctionnel (timeout sur AAAA)

**Solution** :

```bash
# Désactiver IPv6 pour DNS
sudo nano /etc/gai.conf
# Décommenter ligne :
precedence ::ffff:0:0/96  100

# Ou désactiver IPv6 complètement
sudo nano /etc/sysctl.conf
# Ajouter :
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1

sudo sysctl -p

# Tester
$ time host google.com
real    0m0.015s  # ✅ Rapide
```

---

### Erreur 4 : Modifications `/etc/resolv.conf` Écrasées

```bash
# Modifier fichier
$ echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf

# Après reboot ou restart réseau :
$ cat /etc/resolv.conf
nameserver 127.0.0.53  # ❌ Modifications perdues !
```

**Cause** : systemd-resolved ou NetworkManager gère le fichier

**Solutions** :

**Option 1** : Désactiver gestion automatique

```bash
# NetworkManager
sudo nano /etc/NetworkManager/NetworkManager.conf
[main]
dns=none

sudo systemctl restart NetworkManager
```

**Option 2** : Configurer via systemd-resolved

```bash
sudo nano /etc/systemd/resolved.conf
[Resolve]
DNS=8.8.8.8 1.1.1.1

sudo systemctl restart systemd-resolved
```

**Option 3** : Protéger fichier (déconseillé)

```bash
sudo chattr +i /etc/resolv.conf
# Empêche TOUTE modification (même par root)
```

---

### Erreur 5 : DNSSEC Bloque Certains Domaines

```bash
$ host example.com
Host example.com not found: 2(SERVFAIL)
```

**Cause** : Validation DNSSEC échoue (config serveur DNS incorrecte)

**Solution temporaire** : Désactiver DNSSEC

```bash
# systemd-resolved
sudo nano /etc/systemd/resolved.conf
[Resolve]
DNSSEC=no

sudo systemctl restart systemd-resolved

# Tester
$ host example.com
example.com has address 93.184.216.34  # ✅ Fonctionne
```

---

## 📝 Cheat Sheet - Configuration DNS Client

### Outils de Requête DNS

```bash
# host - Simple et rapide
host google.com                      # Résolution basique
host -t MX google.com               # Type MX
host -t AAAA google.com             # IPv6
host -a google.com                  # Tous types
host google.com 8.8.8.8             # Serveur DNS spécifique
host 8.8.8.8                        # Résolution inverse

# dig - Complet et détaillé
dig google.com                      # Résolution complète
dig google.com +short               # Réponse courte
dig @8.8.8.8 google.com            # Serveur DNS spécifique
dig google.com MX +noall +answer   # Type MX, section ANSWER uniquement
dig +trace google.com               # Tracer résolution complète
dig -x 8.8.8.8                     # Résolution inverse

# getent - Respecte configuration système
getent hosts google.com             # Résolution système
getent ahosts google.com            # Avec détails socket
getent ahostsv4 google.com         # IPv4 uniquement
getent services ssh                 # Services (ports)
```

---

### Fichiers de Configuration

```bash
# /etc/resolv.conf - Serveurs DNS
cat /etc/resolv.conf
nameserver 8.8.8.8
nameserver 1.1.1.1
search example.com
options timeout:2

# /etc/hosts - Résolution locale
cat /etc/hosts
127.0.0.1       localhost
192.168.1.10    server.local

# /etc/nsswitch.conf - Ordre résolution
grep ^hosts /etc/nsswitch.conf
hosts:          files dns
```

---

### systemd-resolved

```bash
# Statut
resolvectl status                   # Statut général
resolvectl statistics               # Statistiques cache
systemctl status systemd-resolved   # Statut service

# Cache
sudo resolvectl flush-caches        # Vider cache
resolvectl statistics               # Vérifier cache vidé

# Requêtes
resolvectl query google.com         # Résoudre nom
resolvectl query --type=MX google.com  # Type spécifique
resolvectl query 8.8.8.8           # Résolution inverse

# Configuration
cat /etc/systemd/resolved.conf      # Config principale
cat /run/systemd/resolve/resolv.conf  # DNS réels
```

---

### Tests Diagnostics

```bash
# Comparer différents serveurs DNS
dig @8.8.8.8 google.com +short      # Google DNS
dig @1.1.1.1 google.com +short      # Cloudflare DNS
dig @192.168.1.1 google.com +short  # DNS local

# Mesurer temps résolution
time host google.com                # Temps résolution
time dig google.com                 # Temps avec dig

# Vérifier types enregistrements
host -t A google.com                # IPv4
host -t AAAA google.com             # IPv6
host -t MX google.com               # Mail
host -t NS google.com               # DNS servers
host -t TXT google.com              # TXT records
```

---

## 🎯 Points Clés pour l'Examen LPIC-1

### Ce qu'il faut maîtriser ✅

1. **Types d'enregistrements DNS** 📝
   - A (IPv4), AAAA (IPv6), MX (mail), NS (DNS servers)
   - CNAME (alias), TXT (texte), PTR (reverse), SOA (zone metadata)
   - Savoir quand utiliser chaque type

2. **Commande `host`** 🔍
   - Résolution simple : `host google.com`
   - Types : `host -t MX google.com`
   - Serveur spécifique : `host google.com 8.8.8.8`
   - Résolution inverse : `host 8.8.8.8`

3. **Commande `dig`** 🔎
   - Syntaxe complète et sections réponse
   - Options : `+short`, `+noall +answer`, `+trace`
   - Serveur DNS : `dig @8.8.8.8 google.com`
   - Comprendre TTL dans réponse

4. **Commande `getent`** 📋
   - Différence avec `dig` : respecte `/etc/nsswitch.conf`
   - `getent hosts`, `getent ahosts`, `getent services`
   - Usage pour tester résolution système complète

5. **Fichiers de configuration** ⚙️
   - `/etc/resolv.conf` : nameserver, search, options
   - `/etc/hosts` : résolution locale prioritaire
   - `/etc/nsswitch.conf` : ordre résolution (`hosts: files dns`)

6. **systemd-resolved** 🔄
   - Rôle : résolveur DNS local 127.0.0.53
   - Configuration : `/etc/systemd/resolved.conf`
   - Commandes : `resolvectl status`, `resolvectl flush-caches`
   - Fichiers : `/run/systemd/resolve/stub-resolv.conf`

7. **TTL (Time To Live)** ⏱️
   - Concept : durée cache enregistrement DNS
   - Voir dans `dig` : colonne TTL décrémente
   - Impact sur propagation changements DNS

---

### Pièges Fréquents à l'Examen ⚠️

1. **Confusion CNAME vs A** :
   - CNAME = alias (nom → nom)
   - A = résolution finale (nom → IP)
   - CNAME interdit pour domaine racine

2. **Priorité MX** :
   - ❌ Plus haut = prioritaire
   - ✅ **Plus bas = prioritaire** (10 avant 20)

3. **Ordre résolution** :
   - `dig` interroge **directement DNS** (ignore `/etc/hosts`)
   - `getent` suit **`/etc/nsswitch.conf`** (respecte `/etc/hosts`)
   - Applications utilisent `getent`, pas `dig` !

4. **systemd-resolved** :
   - `/etc/resolv.conf` souvent lien symbolique
   - Modifications manuelles écrasées si géré par systemd-resolved
   - DNS 127.0.0.53 = systemd-resolved actif

5. **TTL décrémente** :
   - Valeur affichée = **temps restant en cache**, pas TTL original
   - Pour voir TTL original : vider cache puis requêter

6. **Résolution inverse** :
   - Format spécial : `1.2.0.192.in-addr.arpa` (IP inversée)
   - Commande : `dig -x 192.0.2.1` ou `host 192.0.2.1`
   - PTR géré par propriétaire IP, pas domaine

---

### Questions Types Examen

**Q1** : Comment obtenir uniquement l'adresse IPv4 de google.com ?
```bash
dig google.com A +short
# ou
host -t A google.com
```

**Q2** : Comment afficher les serveurs mail pour example.com avec priorités ?
```bash
host -t MX example.com
# ou
dig example.com MX +noall +answer
```

**Q3** : Comment vérifier quel serveur DNS est utilisé par systemd-resolved ?
```bash
resolvectl status
# ou
cat /etc/systemd/resolved.conf
```

**Q4** : Comment forcer résolution via DNS Google (8.8.8.8) ?
```bash
dig @8.8.8.8 example.com
# ou
host example.com 8.8.8.8
```

**Q5** : Comment résoudre hostname en respectant /etc/hosts (comme une application) ?
```bash
getent hosts example.com
```

**Q6** : Comment vider le cache DNS de systemd-resolved ?
```bash
sudo resolvectl flush-caches
```

**Q7** : Quelle est la différence entre `files` et `dns` dans nsswitch.conf ?
- `files` = `/etc/hosts`
- `dns` = Serveurs DNS (`/etc/resolv.conf`)
- Ordre `files dns` = local d'abord, puis DNS

**Q8** : Comment ajouter un serveur DNS permanent avec systemd-resolved ?
```bash
sudo nano /etc/systemd/resolved.conf
[Resolve]
DNS=8.8.8.8 1.1.1.1

sudo systemctl restart systemd-resolved
```

---

### Commandes à Connaître par Cœur 💯

```bash
host google.com                    # Résolution simple
host -t MX google.com              # Type MX
host google.com 8.8.8.8            # Serveur DNS spécifique
host 8.8.8.8                       # Résolution inverse

dig google.com                     # Résolution détaillée
dig google.com +short              # Réponse courte
dig @8.8.8.8 google.com           # Serveur DNS spécifique
dig -x 8.8.8.8                    # Résolution inverse
dig +trace google.com              # Tracer résolution

getent hosts google.com            # Résolution système
getent ahosts google.com           # Avec détails

resolvectl status                  # Statut systemd-resolved
resolvectl flush-caches            # Vider cache
resolvectl query google.com        # Résoudre via systemd-resolved
```

---

**Poids du module** : 2 points  
**Difficulté** : ⭐⭐ Faible-Moyenne  
**Temps de préparation recommandé** : 2-3 heures

🎯 **Focus examen** : Maîtriser `host`, `dig`, `getent` et comprendre la différence de comportement entre interrogation DNS directe vs résolution système respectant `/etc/hosts` et `/etc/nsswitch.conf` !
