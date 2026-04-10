# 🌐 Fondamentaux Réseaux - TCP/IP, Subnetting & Ports

## 📋 Informations du Module

- **Examen** : LPIC-1 102
- **Objectif** : 109.1 - Fundamentals of internet protocols
- **Poids** : 4 points
- **Objectif** : Comprendre TCP/IP, subnetting, masques réseau, ports et protocoles

---

# 📖 PARTIE 1 : FONDAMENTAUX IP NETWORKING

## 🌐 Architecture TCP/IP

### 📊 Modèle OSI vs TCP/IP

```
┌─────────────────────────────────────────────────────┐
│  MODÈLE OSI (7 couches)    vs    MODÈLE TCP/IP      │
├─────────────────────────────────────────────────────┤
│                                                     │
│   7. Application              ┌──────────────┐      │
│   6. Présentation             │ Application  │      │
│   5. Session                  └──────────────┘      │
│                                                     │
│   4. Transport                ┌──────────────┐      │
│                               │  Transport   │      │
│                               │  (TCP/UDP)   │      │
│                               └──────────────┘      │
│                                                     │
│   3. Réseau                   ┌──────────────┐      │
│                               │   Internet   │      │
│                               │     (IP)     │      │
│                               └──────────────┘      │
│                                                     │
│   2. Liaison                  ┌──────────────┐      │
│   1. Physique                 │ Accès Réseau │      │
│                               │ (Ethernet)   │      │
│                               └──────────────┘      │
│                                                     │
└─────────────────────────────────────────────────────┘
```

### 🔗 Couches TCP/IP

| Couche | Nom | Protocoles | Rôle |
|--------|-----|------------|------|
| **4** | Application | HTTP, FTP, SSH, DNS, SMTP | Applications utilisateur |
| **3** | Transport | TCP, UDP | Communication bout-en-bout |
| **2** | Internet | IP, ICMP, ARP | Routage packets |
| **1** | Accès réseau | Ethernet, WiFi, PPP | Transmission physique |

---

## 📍 Adressage IPv4

### 🔢 Structure Adresse IPv4

**Format :** 4 octets (32 bits) en notation décimale pointée.

```
192.168.1.10

┌──────┬──────┬──────┬──────┐
│ 192  │ 168  │  1   │  10  │  (Décimal)
└──────┴──────┴──────┴──────┘
    ↓      ↓      ↓      ↓
┌────────┬────────┬────────┬────────┐
│11000000│10101000│00000001│00001010│  (Binaire)
└────────┴────────┴────────┴────────┘
 8 bits   8 bits   8 bits   8 bits = 32 bits
```

**Plage :** 0.0.0.0 → 255.255.255.255

**Nombre total d'adresses :** 2³² = 4 294 967 296 adresses

### 📚 Classes d'Adresses (Historique)

**Classification classful (obsolète mais utile concept) :**

| Classe | Plage | Masque défaut | Usage | Nombre réseaux | Hôtes/réseau |
|--------|-------|---------------|-------|----------------|--------------|
| **A** | 1.0.0.0 - 126.255.255.255 | 255.0.0.0 (/8) | Très grands réseaux | 126 | 16 777 214 |
| **B** | 128.0.0.0 - 191.255.255.255 | 255.255.0.0 (/16) | Grands réseaux | 16 384 | 65 534 |
| **C** | 192.0.0.0 - 223.255.255.255 | 255.255.255.0 (/24) | Petits réseaux | 2 097 152 | 254 |
| **D** | 224.0.0.0 - 239.255.255.255 | N/A | Multicast | - | - |
| **E** | 240.0.0.0 - 255.255.255.255 | N/A | Réservé/recherche | - | - |

**Reconnaissance classe (premier octet) :**

```
Classe A : 1-126     (0xxxxxxx)
Classe B : 128-191   (10xxxxxx)
Classe C : 192-223   (110xxxxx)
Classe D : 224-239   (1110xxxx)
Classe E : 240-255   (1111xxxx)
```

### 🏠 Adresses Privées (RFC 1918)

**Plages réservées (non routables Internet) :**

| Classe | Plage privée | Masque | CIDR | Nombre IPs |
|--------|--------------|--------|------|------------|
| **A** | 10.0.0.0 - 10.255.255.255 | 255.0.0.0 | 10.0.0.0/8 | 16 777 216 |
| **B** | 172.16.0.0 - 172.31.255.255 | 255.240.0.0 | 172.16.0.0/12 | 1 048 576 |
| **C** | 192.168.0.0 - 192.168.255.255 | 255.255.0.0 | 192.168.0.0/16 | 65 536 |

**Usage :**
- Réseaux locaux (LAN)
- NAT (Network Address Translation) vers IP publique
- Économie d'adresses publiques

### 🔒 Adresses Spéciales

| Adresse/Plage | Usage | Description |
|---------------|-------|-------------|
| **0.0.0.0** | Réseau actuel | Route par défaut, bind all |
| **127.0.0.0/8** | Loopback | 127.0.0.1 (localhost) |
| **169.254.0.0/16** | APIPA | Auto-config (échec DHCP) |
| **255.255.255.255** | Broadcast limité | Diffusion locale |
| **x.x.x.0** | Adresse réseau | Identifie réseau |
| **x.x.x.255** | Broadcast | Diffusion réseau (/24) |

---

## 🌍 IPv6 - Survol

### 🔢 Format IPv6

**128 bits** (vs 32 bits IPv4) → 3.4 × 10³⁸ adresses

**Notation :** 8 groupes de 4 chiffres hexadécimaux.

```
2001:0db8:85a3:0000:0000:8a2e:0370:7334

Simplifications :
1. Supprimer 0 en tête de groupe
   2001:db8:85a3:0:0:8a2e:370:7334

2. Remplacer groupes 0 consécutifs par ::
   2001:db8:85a3::8a2e:370:7334

Loopback IPv6 :
0000:0000:0000:0000:0000:0000:0000:0001  →  ::1
```

**Préfixes courants :**

| Préfixe | Usage |
|---------|-------|
| **::/128** | Non spécifié |
| **::1/128** | Loopback (localhost) |
| **fe80::/10** | Link-local (auto-config) |
| **fc00::/7** | Unique Local (privé) |
| **2000::/3** | Global Unicast (Internet) |
| **ff00::/8** | Multicast |

**Notation CIDR :** `2001:db8::/32` (préfixe 32 bits)

---

# 🔢 PARTIE 2 : SUBNETTING, NETMASKS & CIDR

## 🎭 Masque de Sous-Réseau (Netmask)

### 🎯 Principe

**Masque réseau** : Sépare partie réseau et partie hôte d'une IP.

```
IP :      192.168.1.10
Masque :  255.255.255.0
          └─────┬──────┘ └┬┘
           Réseau      Hôte
```

**Opération ET binaire :**

```
IP :      11000000.10101000.00000001.00001010  (192.168.1.10)
Masque :  11111111.11111111.11111111.00000000  (255.255.255.0)
          ───────────────────────────────────
Réseau :  11000000.10101000.00000001.00000000  (192.168.1.0)
```

### 📐 Notations Netmask

**Notation décimale :**
```
255.0.0.0
255.255.0.0
255.255.255.0
255.255.255.128
```

**Notation CIDR (/ + nombre de bits réseau) :**
```
/8
/16
/24
/25
```

### 📊 Table Conversion Netmask ↔ CIDR

| CIDR | Masque décimal | Binaire (dernier octet) | Nombre hôtes | Classe |
|------|----------------|-------------------------|--------------|--------|
| **/8** | 255.0.0.0 | 00000000 | 16 777 214 | A |
| **/16** | 255.255.0.0 | 00000000 | 65 534 | B |
| **/24** | 255.255.255.0 | 00000000 | 254 | C |
| **/25** | 255.255.255.128 | 10000000 | 126 | - |
| **/26** | 255.255.255.192 | 11000000 | 62 | - |
| **/27** | 255.255.255.224 | 11100000 | 30 | - |
| **/28** | 255.255.255.240 | 11110000 | 14 | - |
| **/29** | 255.255.255.248 | 11111000 | 6 | - |
| **/30** | 255.255.255.252 | 11111100 | 2 | Lien P2P |
| **/31** | 255.255.255.254 | 11111110 | 2* | Lien P2P (RFC 3021) |
| **/32** | 255.255.255.255 | 11111111 | 1 (hôte unique) | - |

**Formule nombre d'hôtes :**
```
Nombre d'hôtes = 2^(32 - CIDR) - 2

Exemple /24 :
2^(32-24) - 2 = 2^8 - 2 = 256 - 2 = 254 hôtes

-2 car :
- 1 adresse réseau (tous bits hôte à 0)
- 1 adresse broadcast (tous bits hôte à 1)
```

---

## 🧮 Calculs Subnetting

### 📝 Informations à Déterminer

Pour un réseau donné, identifier :

1. **Adresse réseau** (Network address)
2. **Adresse broadcast** (Broadcast address)
3. **Première IP utilisable** (First usable host)
4. **Dernière IP utilisable** (Last usable host)
5. **Nombre d'hôtes** (Usable hosts)

### 🎓 Exemple 1 : Réseau /24 (Simple)

**Réseau : 192.168.1.0/24**

```
Adresse :       192.168.1.0/24
Masque :        255.255.255.0
Bits réseau :   24 bits
Bits hôte :     8 bits

Calculs :
Adresse réseau :     192.168.1.0
Première IP :        192.168.1.1
Dernière IP :        192.168.1.254
Broadcast :          192.168.1.255
Nombre d'hôtes :     254 (2^8 - 2)

Plage utilisable :   192.168.1.1 - 192.168.1.254
```

### 🎓 Exemple 2 : Réseau /26 (Subnetting)

**Réseau : 192.168.1.64/26**

```
Adresse :       192.168.1.64/26
Masque :        255.255.255.192
Bits réseau :   26 bits
Bits hôte :     6 bits

Conversion binaire (dernier octet) :
64 = 01000000 (binaire)
Masque /26 = 11111111.11111111.11111111.11000000

Calculs :
Taille bloc :        2^6 = 64 adresses
Adresse réseau :     192.168.1.64
Première IP :        192.168.1.65
Dernière IP :        192.168.1.126
Broadcast :          192.168.1.127
Nombre d'hôtes :     62 (2^6 - 2)

Plage utilisable :   192.168.1.65 - 192.168.1.126

Sous-réseaux /26 dans 192.168.1.0/24 :
192.168.1.0/26   (0-63)
192.168.1.64/26  (64-127)   ← Notre réseau
192.168.1.128/26 (128-191)
192.168.1.192/26 (192-255)
```

### 🎓 Exemple 3 : Réseau /28 (Petit sous-réseau)

**Réseau : 10.0.0.48/28**

```
Adresse :       10.0.0.48/28
Masque :        255.255.255.240
Bits réseau :   28 bits
Bits hôte :     4 bits

Conversion binaire :
48 = 00110000
Masque = 11110000 (/28 dernier octet)

Calculs :
Taille bloc :        2^4 = 16 adresses
Adresse réseau :     10.0.0.48
Première IP :        10.0.0.49
Dernière IP :        10.0.0.62
Broadcast :          10.0.0.63
Nombre d'hôtes :     14 (2^4 - 2)

Plage utilisable :   10.0.0.49 - 10.0.0.62

Sous-réseaux /28 possibles :
10.0.0.0/28    (0-15)
10.0.0.16/28   (16-31)
10.0.0.32/28   (32-47)
10.0.0.48/28   (48-63)    ← Notre réseau
10.0.0.64/28   (64-79)
...
```

### 🎓 Exemple 4 : Réseau /30 (Lien Point-à-Point)

**Réseau : 172.16.5.8/30**

```
Adresse :       172.16.5.8/30
Masque :        255.255.255.252
Bits réseau :   30 bits
Bits hôte :     2 bits

Conversion binaire :
8 = 00001000
Masque = 11111100 (/30 dernier octet)

Calculs :
Taille bloc :        2^2 = 4 adresses
Adresse réseau :     172.16.5.8
Première IP :        172.16.5.9
Dernière IP :        172.16.5.10
Broadcast :          172.16.5.11
Nombre d'hôtes :     2 (2^2 - 2)

Plage utilisable :   172.16.5.9 - 172.16.5.10

Usage typique : Lien routeur-routeur (WAN)
Router A :  172.16.5.9
Router B :  172.16.5.10
```

---

## 🔧 Conversion Binaire ↔ Décimale

### 📊 Valeurs Binaires (8 bits)

```
Position :  7    6    5    4    3    2    1    0
Valeur :   128   64   32   16    8    4    2    1
           2^7  2^6  2^5  2^4  2^3  2^2  2^1  2^0
```

### 🔢 Décimal → Binaire

**Exemple : 192 en binaire**

```
192 = 128 + 64

Position :  128   64   32   16    8    4    2    1
Binaire :    1    1    0    0    0    0    0    0

192 = 11000000
```

**Exemple : 168 en binaire**

```
168 = 128 + 32 + 8

Position :  128   64   32   16    8    4    2    1
Binaire :    1    0    1    0    1    0    0    0

168 = 10101000
```

### 🔢 Binaire → Décimal

**Exemple : 11000000 en décimal**

```
Binaire :    1    1    0    0    0    0    0    0
Position :  128   64   32   16    8    4    2    1

Calcul : 1×128 + 1×64 + 0×32 + ... = 128 + 64 = 192
```

### 📋 Table Conversion Rapide (0-255)

| Décimal | Binaire | Décimal | Binaire | Décimal | Binaire |
|---------|---------|---------|---------|---------|---------|
| **0** | 00000000 | **85** | 01010101 | **170** | 10101010 |
| **1** | 00000001 | **86** | 01010110 | **171** | 10101011 |
| **63** | 00111111 | **127** | 01111111 | **191** | 10111111 |
| **64** | 01000000 | **128** | 10000000 | **192** | 11000000 |
| **65** | 01000001 | **129** | 10000001 | **224** | 11100000 |
| **127** | 01111111 | **168** | 10101000 | **240** | 11110000 |
| **128** | 10000000 | **169** | 10101001 | **248** | 11111000 |
| **254** | 11111110 | **255** | 11111111 | - | - |

**Valeurs courantes netmask :**
- 255 = 11111111
- 254 = 11111110
- 252 = 11111100
- 248 = 11111000
- 240 = 11110000
- 224 = 11100000
- 192 = 11000000
- 128 = 10000000
- 0 = 00000000

---

## 📐 Méthode Rapide Calcul Subnetting

### ⚡ Technique "Magic Number"

**Magic Number = 256 - Dernier octet masque non-255**

```
Exemple /26 (255.255.255.192) :
Magic Number = 256 - 192 = 64
Incréments : 0, 64, 128, 192

Exemple /28 (255.255.255.240) :
Magic Number = 256 - 240 = 16
Incréments : 0, 16, 32, 48, 64, 80, ...

Exemple /27 (255.255.255.224) :
Magic Number = 256 - 224 = 32
Incréments : 0, 32, 64, 96, 128, 160, 192, 224
```

**Trouver sous-réseau pour IP donnée :**

```
IP : 192.168.1.75/26
Masque : 255.255.255.192
Magic : 256 - 192 = 64
Incréments /26 : 0, 64, 128, 192

75 se trouve entre 64 et 128
→ Réseau : 192.168.1.64/26
→ Broadcast : 192.168.1.127
→ Plage : 192.168.1.65 - 192.168.1.126
```

---

# 🔌 PARTIE 3 : PROTOCOLES TCP/IP & PORTS

## 📡 Protocoles Couche Transport

### 🚀 TCP - Transmission Control Protocol

**Caractéristiques :**
- ✅ **Orienté connexion** (3-way handshake)
- ✅ **Fiable** (accusés réception, retransmission)
- ✅ **Ordonné** (séquençage packets)
- ✅ **Contrôle flux** (sliding window)
- ❌ **Plus lent** (overhead)

**3-Way Handshake (établissement connexion) :**

```
Client                    Server
  │                         │
  ├─── SYN ────────────────>│  (Synchronize)
  │                         │
  │<──── SYN-ACK ───────────┤  (Synchronize-Acknowledge)
  │                         │
  ├─── ACK ────────────────>│  (Acknowledge)
  │                         │
  │   CONNEXION ÉTABLIE     │
  │                         │
  ├═══ DATA ═══════════════>│
  │<══ DATA ════════════════┤
```

**Usage :**
- HTTP/HTTPS (web)
- SSH (shell distant)
- FTP (transfert fichiers)
- SMTP (email)
- **Toute application nécessitant fiabilité**

### ⚡ UDP - User Datagram Protocol

**Caractéristiques :**
- ❌ **Sans connexion** (pas handshake)
- ❌ **Non fiable** (pas accusé réception)
- ❌ **Non ordonné** (packets arrivent désordre possible)
- ✅ **Rapide** (pas overhead TCP)
- ✅ **Simple**

**Fonctionnement :**

```
Client                    Server
  │                         │
  ├─── DATA ──────────────>│  (Envoi direct, pas setup)
  │                         │
  ├─── DATA ──────────────>│
  │                         │
  (Pas de garantie livraison ou ordre)
```

**Usage :**
- DNS (requêtes)
- DHCP (configuration IP)
- NTP (temps)
- Streaming (vidéo/audio, quelques pertes OK)
- Gaming (faible latence importante)
- SNMP (monitoring)

### 🔔 ICMP - Internet Control Message Protocol

**Rôle :** Messages de contrôle/erreur réseau (couche Internet/IP).

**Caractéristiques :**
- Pas de transport données utilisateur
- Diagnostic et contrôle réseau
- Utilisé par IP (couche 3)

**Messages ICMP courants :**

| Type | Code | Message | Usage |
|------|------|---------|-------|
| **0** | 0 | Echo Reply | Réponse ping |
| **3** | 0-15 | Destination Unreachable | Hôte/réseau/port inaccessible |
| **5** | 0-3 | Redirect | Redirection routage |
| **8** | 0 | Echo Request | Ping |
| **11** | 0-1 | Time Exceeded | TTL expiré (traceroute) |

**Outils utilisant ICMP :**

```bash
# Ping (Echo Request/Reply)
ping 8.8.8.8
# ICMP Type 8 (request) → Type 0 (reply)

# Traceroute (TTL + Time Exceeded)
traceroute google.com
# ICMP Type 11 (TTL exceeded) pour chaque hop

# MTU Path Discovery
# ICMP Type 3 Code 4 (Fragmentation needed)
```

---

## 🔌 Ports et Sockets

### 🎯 Concept de Port

**Port :** Numéro identifiant application/service sur hôte.

**Plage :** 0 - 65535 (16 bits)

```
┌─────────────────────────────────────────────────┐
│  COMMUNICATION RÉSEAU                           │
├─────────────────────────────────────────────────┤
│                                                 │
│  Client (192.168.1.10)                          │
│    │                                            │
│    └─ Firefox (port source: 54321)              │
│         │                                       │
│         ↓ TCP                                   │
│    Destination: 93.184.216.34:443 (HTTPS)       │
│                                                 │
│                                                 │
│  Serveur Web (93.184.216.34)                    │
│    │                                            │
│    └─ Nginx (port écoute: 443)                  │
│         │                                       │
│         ↓ Réponse                               │
│    Source: 93.184.216.34:443                    │
│    Destination: 192.168.1.10:54321              │
│                                                 │
└─────────────────────────────────────────────────┘
```

**Socket :** Combinaison IP:Port

```
Exemple socket :
192.168.1.10:54321  (client)
93.184.216.34:443   (serveur HTTPS)
```

### 📊 Classification Ports

| Plage | Type | Usage | Exemples |
|-------|------|-------|----------|
| **0-1023** | **Well-Known Ports** | Services standards, nécessite root | 22 (SSH), 80 (HTTP), 443 (HTTPS) |
| **1024-49151** | **Registered Ports** | Applications enregistrées IANA | 3306 (MySQL), 8080 (HTTP alt) |
| **49152-65535** | **Dynamic/Private** | Ports éphémères (clients) | Ports sources aléatoires |

### 🔐 Privilèges Root (Ports < 1024)

**Règle :** Ouvrir socket sur port **0-1023** nécessite privilèges root.

**Raison :** Sécurité (éviter utilisateur malveillant usurper service système).

```bash
# Essayer bind port 80 sans root (échec)
nc -l 80
# Permission denied

# Avec root (succès)
sudo nc -l 80

# Port > 1024 OK sans root
nc -l 8080
# OK

# Exemple Apache/Nginx (démarre root, puis drop privileges)
# 1. Démarre en root
# 2. Bind port 80
# 3. Change user vers www-data
# 4. Continue en www-data
```

**Contournement (non-root) :**
- Utiliser port > 1024 (ex: 8080)
- Redirection iptables/firewall
- Capabilities Linux (`CAP_NET_BIND_SERVICE`)

```bash
# Donner capability à binaire (bind ports < 1024)
sudo setcap 'cap_net_bind_service=+ep' /usr/bin/myapp

# Vérifier
getcap /usr/bin/myapp
```

---

## 📋 Ports Standards Importants

### 🌐 Ports Well-Known (0-1023)

| Port | Protocole | Service | Description |
|------|-----------|---------|-------------|
| **20** | TCP | FTP-DATA | Transfert données FTP (mode actif) |
| **21** | TCP | FTP | Contrôle FTP |
| **22** | TCP | SSH | Secure Shell (admin distant) |
| **23** | TCP | Telnet | Terminal distant (non sécurisé) |
| **25** | TCP | SMTP | Envoi email (Mail Transfer) |
| **53** | TCP/UDP | DNS | Résolution noms domaine |
| **80** | TCP | HTTP | Web non sécurisé |
| **110** | TCP | POP3 | Réception email (Post Office Protocol) |
| **123** | UDP | NTP | Network Time Protocol |
| **139** | TCP | NetBIOS-SSN | NetBIOS Session Service (SMB) |
| **143** | TCP | IMAP | Internet Message Access Protocol |
| **161** | UDP | SNMP | Simple Network Management Protocol |
| **162** | UDP | SNMP-TRAP | SNMP Traps (alertes) |
| **389** | TCP/UDP | LDAP | Lightweight Directory Access Protocol |
| **443** | TCP | HTTPS | HTTP Secure (TLS/SSL) |
| **465** | TCP | SMTPS | SMTP Secure (SSL) - obsolète, utiliser 587 |
| **636** | TCP | LDAPS | LDAP Secure (SSL/TLS) |
| **993** | TCP | IMAPS | IMAP Secure (SSL/TLS) |
| **995** | TCP | POP3S | POP3 Secure (SSL/TLS) |

### 📌 Autres Ports Importants

| Port | Protocole | Service | Description |
|------|-----------|---------|-------------|
| **69** | UDP | TFTP | Trivial FTP (simple, UDP) |
| **514** | UDP | Syslog | Journalisation système |
| **587** | TCP | Submission | SMTP Submission (client → serveur) |
| **873** | TCP | rsync | Synchronisation fichiers |
| **3306** | TCP | MySQL | Base données MySQL/MariaDB |
| **5432** | TCP | PostgreSQL | Base données PostgreSQL |
| **6379** | TCP | Redis | Base données clé-valeur |
| **8080** | TCP | HTTP-ALT | HTTP alternatif (proxy, dev) |
| **27017** | TCP | MongoDB | Base données MongoDB |

### 🎮 Mnémotechniques Ports

```
FTP :     20  (DATA)  21  (CONTROL)  → 20-21 = duo
SSH :     22                         → 2×2 = sécurisé
Telnet :  23                         → 22+1 (SSH obsolète)
SMTP :    25                         → 25 lettres alphabet
DNS :     53                         → 5×3 = rapide résolution
HTTP :    80                         → 8×10 = base web
POP3 :    110                        → 11×10
NTP :     123                        → 1-2-3 temps
IMAP :    143                        → 143 > 110 (plus features)
SNMP :    161                        → monitoring 24/7 → 161
LDAP :    389                        → annuaire
HTTPS :   443                        → 4×4×3 = sécurisé
```

---

## 📄 Fichier /etc/services

### 🎯 Rôle

**Mapping ports ↔ noms de services.**

```bash
# Voir fichier
cat /etc/services

# Format :
# service_name    port/protocol    [aliases]    [# commentaire]
```

### 📋 Exemples Entrées

```bash
# Extrait /etc/services

ftp-data        20/tcp
ftp             21/tcp
ssh             22/tcp                          # SSH Remote Login Protocol
ssh             22/udp
telnet          23/tcp
smtp            25/tcp          mail
time            37/tcp          timserver
time            37/udp          timserver
domain          53/tcp                          # Domain Name Server
domain          53/udp
http            80/tcp          www             # World Wide Web HTTP
pop3            110/tcp         pop-3           # POP version 3
ntp             123/tcp
ntp             123/udp                         # Network Time Protocol
netbios-ssn     139/tcp                         # NetBIOS Session Service
imap            143/tcp         imap2           # Internet Mail Access Protocol
snmp            161/tcp                         # Simple Network Management Protocol
snmp            161/udp
ldap            389/tcp                         # Lightweight Directory Access Protocol
https           443/tcp                         # http protocol over TLS/SSL
smtps           465/tcp                         # smtp protocol over TLS/SSL (obsolete)
submission      587/tcp                         # Submission [RFC4409]
imaps           993/tcp                         # IMAP over SSL
pop3s           995/tcp                         # POP-3 over SSL
mysql           3306/tcp
```

### 🔍 Utilisation par Commandes

**Résolution nom ↔ port :**

```bash
# Commandes réseau affichent noms au lieu ports
netstat -tuln
# tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN

ss -tuln
# tcp   LISTEN  0.0.0.0:ssh   0.0.0.0:*

# "ssh" affiché au lieu de "22" grâce /etc/services

# Avec numéros (option -n)
netstat -tuln
ss -tuln

# Sans -n (noms services)
netstat -tul
ss -tul
```

**Rechercher service :**

```bash
# Chercher port SSH
grep "^ssh" /etc/services
# ssh             22/tcp

# Chercher port HTTP
grep "^http" /etc/services
# http            80/tcp          www

# Chercher tous HTTPS/SSL
grep -i ssl /etc/services

# Port MySQL
grep mysql /etc/services
# mysql           3306/tcp

# Chercher par numéro port
grep -w "443/tcp" /etc/services
# https           443/tcp
```

**Résolution getent :**

```bash
# Résoudre nom service → port
getent services ssh
# ssh                   22/tcp

getent services http
# http                  80/tcp www

getent services https
# https                 443/tcp

# Résoudre port → nom
getent services 22/tcp
# ssh                   22/tcp

getent services 80/tcp
# http                  80/tcp www
```

---

## 🎓 Scénarios Pratiques

### Scénario 1 : Calcul Sous-Réseau

**Question :** Trouver infos réseau pour 172.16.50.137/27

```bash
# Données
IP :     172.16.50.137
CIDR :   /27
Masque : 255.255.255.224

# Calcul
Bits hôte : 32 - 27 = 5 bits
Magic :     256 - 224 = 32
Incréments /27 : 0, 32, 64, 96, 128, 160, 192, 224

137 se trouve entre 128 et 160

# Réponses
Réseau :        172.16.50.128/27
Broadcast :     172.16.50.159
Première IP :   172.16.50.129
Dernière IP :   172.16.50.158
Hôtes :         30 (2^5 - 2)
Plage :         172.16.50.129 - 172.16.50.158
```

### Scénario 2 : Diviser Réseau

**Question :** Diviser 192.168.10.0/24 en 4 sous-réseaux égaux.

```bash
# Besoin 4 sous-réseaux
2^n ≥ 4  →  n = 2 bits

# Nouveau CIDR
/24 + 2 = /26

# Calcul
Magic : 256 - 192 = 64

# 4 sous-réseaux /26
1. 192.168.10.0/26     (0-63)
   Plage : 192.168.10.1 - 192.168.10.62
   Broadcast : 192.168.10.63

2. 192.168.10.64/26    (64-127)
   Plage : 192.168.10.65 - 192.168.10.126
   Broadcast : 192.168.10.127

3. 192.168.10.128/26   (128-191)
   Plage : 192.168.10.129 - 192.168.10.190
   Broadcast : 192.168.10.191

4. 192.168.10.192/26   (192-255)
   Plage : 192.168.10.193 - 192.168.10.254
   Broadcast : 192.168.10.255

Chaque sous-réseau : 62 hôtes
```

### Scénario 3 : Identifier Protocole/Port

**Question :** Quel protocole/port pour serveur web HTTPS ?

```bash
# Réponse
Protocole :  TCP (fiable, connexion HTTP)
Port :       443
Service :    HTTPS (HTTP over TLS/SSL)

# Vérification
grep https /etc/services
# https           443/tcp

getent services 443/tcp
# https                 443/tcp
```

### Scénario 4 : Diagnostic Port

**Question :** Service SSH ne répond plus, diagnostiquer.

```bash
# 1. Service actif ?
systemctl status sshd

# 2. Port écoute ?
ss -tlnp | grep :22
# tcp   LISTEN  0.0.0.0:22

netstat -tlnp | grep :22

# 3. Firewall bloque ?
sudo iptables -L -n | grep 22
sudo ufw status

# 4. Test connexion local
telnet localhost 22
nc -zv localhost 22

# 5. Test distant
nc -zv server.example.com 22

# 6. Logs
journalctl -u sshd -n 50
tail -f /var/log/auth.log
```

---

## 📝 Cheat Sheet

### Subnetting Rapide

```bash
# Formules
Nombre hôtes = 2^(32 - CIDR) - 2
Magic Number = 256 - (dernier octet masque non-255)

# CIDR courants
/24 → 254 hôtes (255.255.255.0)
/25 → 126 hôtes (255.255.255.128)
/26 → 62 hôtes  (255.255.255.192)
/27 → 30 hôtes  (255.255.255.224)
/28 → 14 hôtes  (255.255.255.240)
/29 → 6 hôtes   (255.255.255.248)
/30 → 2 hôtes   (255.255.255.252) - P2P
```

### Protocoles

```bash
# TCP : Fiable, connexion, ordonné (HTTP, SSH, FTP)
# UDP : Rapide, sans connexion (DNS, DHCP, NTP)
# ICMP : Contrôle réseau (ping, traceroute)
```

### Ports Essentiels

```bash
20/21  FTP
22     SSH
23     Telnet
25     SMTP
53     DNS
80     HTTP
110    POP3
123    NTP
139    NetBIOS
143    IMAP
161    SNMP
389    LDAP
443    HTTPS
465    SMTPS (obsolète)
587    SMTP Submission
636    LDAPS
993    IMAPS
995    POP3S
```

### Fichier /etc/services

```bash
# Chercher service
grep ssh /etc/services
getent services ssh

# Chercher port
grep "80/tcp" /etc/services
getent services 80/tcp
```

---

## 🎯 Points Clés pour l'Examen LPIC-1

### Connaissances Essentielles

✅ **IPv4 :**
- Format 4 octets (32 bits)
- Classes A/B/C (historique mais concept utile)
- Adresses privées : 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16
- Loopback : 127.0.0.0/8 (127.0.0.1)

✅ **IPv6 :**
- 128 bits (notation hexadécimale)
- Loopback : ::1
- Link-local : fe80::/10

✅ **Netmask & CIDR :**
- Masque sépare réseau/hôte
- CIDR : /24 = 255.255.255.0
- Formule hôtes : 2^(32-CIDR) - 2

✅ **Calcul subnetting :**
- Adresse réseau (bits hôte = 0)
- Broadcast (bits hôte = 1)
- Plage utilisable (entre réseau et broadcast)
- Magic Number : 256 - dernier octet masque

✅ **Protocoles transport :**
- **TCP** : Fiable, connexion, ordonné (HTTP, SSH, SMTP)
- **UDP** : Rapide, sans connexion (DNS, DHCP, NTP)
- **ICMP** : Contrôle réseau (ping, traceroute)

✅ **Ports :**
- 0-1023 : Well-known (nécessite root)
- 1024-49151 : Registered
- 49152-65535 : Dynamic/Private
- Connaitre ports standards : 22, 25, 53, 80, 443, etc.

✅ **Fichier /etc/services :**
- Mapping port ↔ nom service
- Utilisé par netstat, ss pour affichage
- `getent services` pour résolution

### Pièges Fréquents

❌ Oublier -2 dans calcul nombre d'hôtes (réseau + broadcast)  
❌ Confondre broadcast réseau et broadcast limité (255.255.255.255)  
❌ Croire que /31 a 0 hôtes (RFC 3021 : 2 hôtes utilisables)  
❌ Confondre class privée B : 172.16.0.0/12 (PAS /16 !)  
❌ Penser qu'UDP est "peu fiable" = mauvais (adapté cas usage)  
❌ Oublier que DNS utilise UDP ET TCP (UDP requêtes, TCP transferts zone)  
❌ Confondre port 465 (SMTPS obsolète) et 587 (Submission moderne)  
❌ Croire que ports > 1024 ne nécessitent jamais root (capabilities)

### Exemples Examen

**Q : Combien d'hôtes utilisables dans 10.1.1.0/26 ?**
```
2^(32-26) - 2 = 2^6 - 2 = 64 - 2 = 62 hôtes
```

**Q : Adresse broadcast pour 192.168.5.64/27 ?**
```
Magic : 256 - 224 = 32
Réseau : 192.168.5.64
Broadcast : 192.168.5.95 (64 + 32 - 1)
```

**Q : Quel protocole utilise ping ?**
```
ICMP (Internet Control Message Protocol)
Type 8 : Echo Request
Type 0 : Echo Reply
```

**Q : Port standard HTTPS ?**
```
443/TCP
```

**Q : Fichier listant services réseau et ports ?**
```
/etc/services
```

**Q : Pourquoi port 80 nécessite root ?**
```
Port < 1024 (well-known) nécessite privilèges root pour bind socket.
```

---

**Formation LPIC-1 102 - Préparation Certification Linux**  
**Cours 109.1 - Fondamentaux Réseaux TCP/IP**  
**Maxime Chenaud - 11 Février 2026**
