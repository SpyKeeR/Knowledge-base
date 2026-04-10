# 109.3 - Basic Network Troubleshooting 🔍🌐

## 📋 Informations du Module

- **Poids à l'examen** : 4 points
- **Objectifs clés** :
  - Isoler et résoudre les problèmes réseau courants ✅
  - Utiliser les outils de diagnostic réseau (ping, traceroute, dig, netcat, ss, tcpdump)
  - Comprendre et manipuler les tables de routage
  - Analyser les connexions actives et le flux de données
  - Appliquer une méthodologie de troubleshooting systématique

---

## 🎯 Partie 1 : Concepts et Outils Basiques (ip, ping, route, traceroute)

### 📚 Méthodologie de Troubleshooting Réseau

#### Approche Ascendante selon le Modèle OSI

Le **dépannage ascendant** suit les couches du modèle OSI du bas vers le haut :

```
┌─────────────────────────────────────────────────┐
│ 7. Application  │ HTTP, DNS, SSH               │ ← dig, curl, telnet
├─────────────────────────────────────────────────┤
│ 6. Présentation │ Chiffrement, encodage        │
├─────────────────────────────────────────────────┤
│ 5. Session      │ Établissement sessions       │
├─────────────────────────────────────────────────┤
│ 4. Transport    │ TCP/UDP, ports               │ ← netstat, ss, netcat
├─────────────────────────────────────────────────┤
│ 3. Réseau       │ IP, routage, ICMP            │ ← ping, traceroute, ip route
├─────────────────────────────────────────────────┤
│ 2. Liaison      │ Ethernet, MAC, switch        │ ← ip link, arp
├─────────────────────────────────────────────────┤
│ 1. Physique     │ Câbles, signaux, NIC         │ ← ip link, ethtool, câbles
└─────────────────────────────────────────────────┘
```

**Méthodologie systématique** :

1. **Couche 1 - Physique** 🔌
   - Le câble est-il branché ?
   - Voyant LED de la carte réseau actif ?
   - `ip link show` → interface `UP` ou `DOWN` ?
   - `ethtool eth0` → Link detected: yes ?

2. **Couche 2 - Liaison** 🔗
   - Interface réseau configurée ?
   - `ip addr show` → adresse IP assignée ?
   - Tables ARP peuplées ? `ip neigh show` ou `arp -a`

3. **Couche 3 - Réseau** 🌐
   - Connectivité locale ? `ping 127.0.0.1` (loopback)
   - Connectivité réseau local ? `ping <gateway>`
   - Routage configuré ? `ip route show` → route par défaut ?
   - Connectivité Internet ? `ping 8.8.8.8`

4. **Couche 4 - Transport** 🚪
   - Service écoute sur le bon port ? `ss -tlnp` ou `netstat -tlnp`
   - Firewall bloque le port ? `iptables -L` ou `firewall-cmd --list-all`
   - Connexion établie ? `ss -tn` état `ESTABLISHED`

5. **Couche 7 - Application** 🖥️
   - Résolution DNS fonctionne ? `nslookup google.com` ou `dig google.com`
   - Service applicatif répond ? `curl http://localhost:80`
   - Logs d'erreurs ? `journalctl -u service.service`

**Avantages approche ascendante** :
- ✅ Méthodique, ne saute pas d'étapes
- ✅ Isole rapidement le problème au bon niveau
- ✅ Évite de chercher des problèmes DNS quand le câble est débranché !

---

### 🛣️ Concepts de Routage

#### Fonctionnement du Routage IP

**Principe** : Quand une machine envoie un paquet IP, elle détermine :
1. La destination est-elle dans mon réseau local ?
   - **OUI** → Envoi direct (ARP pour obtenir MAC de destination)
   - **NON** → Envoi vers la **passerelle par défaut** (gateway/routeur)

**Table de Routage** : Liste des routes connues par la machine

```bash
# Afficher la table de routage
ip route show
# ou (ancien)
route -n
```

**Exemple de table de routage** :

```
default via 192.168.1.254 dev eth0 proto dhcp metric 100
10.0.0.0/8 via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.10
```

**Interprétation** :
- `default via 192.168.1.254` → Toutes les destinations inconnues vont vers cette gateway
- `10.0.0.0/8 via 192.168.1.1` → Réseau 10.x.x.x passe par le routeur 192.168.1.1
- `192.168.1.0/24 dev eth0` → Réseau local directement accessible via eth0

**Sélection de route** :
- Le noyau Linux choisit la route **la plus spécifique** (plus long préfixe / CIDR le plus élevé)
- Exemple : Pour destination `192.168.1.50`
  - ✅ Route `/24` (192.168.1.0/24) plus spécifique que `/0` (default)
  - → Envoi direct via eth0

**Métrique** :
- Si plusieurs routes identiques existent, la **métrique la plus faible** gagne
- `metric 100` vs `metric 200` → Route metric 100 préférée

---

### 🔧 Commande `ip route` - Gestion des Routes (Moderne)

#### Syntaxe Générale

```bash
ip route { show | add | del | replace | flush } [OPTIONS]
```

#### Afficher les Routes

| Commande | Description | Exemple Sortie |
|----------|-------------|----------------|
| `ip route show` | Afficher toutes les routes | Liste complète |
| `ip route` | Alias de `show` | Idem |
| `ip route list` | Alias de `show` | Idem |
| `ip route show table all` | Toutes les tables (main, local, default) | Tables multiples |
| `ip route show table local` | Table local uniquement | Routes locales |
| `ip route get 8.8.8.8` | Route utilisée pour une IP spécifique | `8.8.8.8 via 192.168.1.254 dev eth0 src 192.168.1.10` |

**Exemple détaillé** :

```bash
$ ip route show
default via 192.168.1.254 dev eth0 proto dhcp metric 100
10.0.0.0/8 via 192.168.1.1 dev eth0 proto static metric 50
192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.10 metric 100
```

**Colonnes** :
- **Destination** : `default` (0.0.0.0/0), `10.0.0.0/8`, `192.168.1.0/24`
- **via** : Adresse IP du routeur suivant (next-hop)
- **dev** : Interface réseau de sortie
- **proto** : Origine (kernel, dhcp, static, boot)
- **scope** : Portée (link = lien direct, global = routé)
- **src** : IP source utilisée pour ce réseau
- **metric** : Priorité (plus bas = meilleur)

#### Ajouter une Route

**Syntaxe** :

```bash
sudo ip route add <RÉSEAU/CIDR> via <GATEWAY> dev <INTERFACE> [metric <N>]
```

**Exemples** :

```bash
# Ajouter route par défaut (gateway)
sudo ip route add default via 192.168.1.254 dev eth0

# Ajouter route vers un réseau spécifique
sudo ip route add 10.0.0.0/8 via 192.168.1.1 dev eth0

# Route avec métrique personnalisée
sudo ip route add 172.16.0.0/12 via 192.168.1.2 dev eth0 metric 50

# Route vers un hôte unique
sudo ip route add 8.8.8.8 via 192.168.1.254 dev eth0

# Route directe sans gateway (réseau accessible directement)
sudo ip route add 192.168.2.0/24 dev eth1
```

**Options avancées** :

```bash
# Route avec IP source spécifique
sudo ip route add 10.0.0.0/8 via 192.168.1.1 src 192.168.1.10

# Route blackhole (paquets supprimés silencieusement)
sudo ip route add blackhole 192.168.100.0/24

# Route unreachable (ICMP "Network Unreachable" renvoyé)
sudo ip route add unreachable 192.168.200.0/24

# Route prohibit (ICMP "Communication Administratively Prohibited")
sudo ip route add prohibit 10.99.0.0/16
```

#### Supprimer une Route

**Syntaxe** :

```bash
sudo ip route del <RÉSEAU/CIDR> [via <GATEWAY>] [dev <INTERFACE>]
```

**Exemples** :

```bash
# Supprimer route par défaut
sudo ip route del default

# Supprimer route spécifique
sudo ip route del 10.0.0.0/8 via 192.168.1.1

# Supprimer route sans spécifier gateway
sudo ip route del 192.168.2.0/24 dev eth1

# Supprimer route vers hôte
sudo ip route del 8.8.8.8
```

#### Modifier/Remplacer une Route

```bash
# Remplacer une route existante
sudo ip route replace 10.0.0.0/8 via 192.168.1.5 dev eth0

# Change la gateway pour le réseau 10.0.0.0/8
```

#### Vider les Routes

```bash
# Supprimer TOUTES les routes (DANGER !)
sudo ip route flush table main

# Supprimer routes d'un protocole spécifique
sudo ip route flush proto static

# Supprimer routes d'une interface
sudo ip route flush dev eth0
```

⚠️ **ATTENTION** : `ip route flush` peut couper toute connectivité réseau !

---

### 🗺️ Commande `route` - Gestion des Routes (Obsolète)

La commande `route` fait partie du paquet **net-tools** (obsolète, remplacé par `ip route`).

#### Installation (si absente)

```bash
# Debian/Ubuntu
sudo apt install net-tools

# RHEL/CentOS
sudo yum install net-tools
```

#### Afficher les Routes

```bash
# Affichage classique avec résolution DNS
route

# Affichage numérique (sans résolution, plus rapide)
route -n
```

**Exemple de sortie `route -n`** :

```
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.1.254   0.0.0.0         UG    100    0        0 eth0
10.0.0.0        192.168.1.1     255.0.0.0       UG    50     0        0 eth0
192.168.1.0     0.0.0.0         255.255.255.0   U     100    0        0 eth0
```

**Colonnes** :
- **Destination** : Réseau de destination (`0.0.0.0` = default)
- **Gateway** : Passerelle (`0.0.0.0` = pas de gateway, direct)
- **Genmask** : Masque réseau (notation décimale au lieu de CIDR)
- **Flags** :
  - `U` = Up (route active)
  - `G` = Gateway (passe par un routeur)
  - `H` = Host (route vers un hôte spécifique, pas un réseau)
  - `!` = Reject (route rejetée)
- **Metric** : Métrique (priorité)
- **Ref** : Références à cette route (inutilisé sous Linux)
- **Use** : Nombre d'utilisations (inutilisé sous Linux)
- **Iface** : Interface réseau

#### Ajouter une Route

**Syntaxe** :

```bash
sudo route add -net <RÉSEAU> netmask <MASQUE> gw <GATEWAY> [dev <INTERFACE>]
sudo route add -host <IP> gw <GATEWAY> [dev <INTERFACE>]
sudo route add default gw <GATEWAY> [dev <INTERFACE>]
```

**Exemples** :

```bash
# Ajouter route par défaut
sudo route add default gw 192.168.1.254 dev eth0

# Ajouter route vers un réseau
sudo route add -net 10.0.0.0 netmask 255.0.0.0 gw 192.168.1.1 dev eth0

# Ajouter route vers un hôte unique
sudo route add -host 8.8.8.8 gw 192.168.1.254 dev eth0

# Route directe (pas de gateway)
sudo route add -net 192.168.2.0 netmask 255.255.255.0 dev eth1
```

#### Supprimer une Route

**Syntaxe** :

```bash
sudo route del -net <RÉSEAU> netmask <MASQUE> [gw <GATEWAY>]
sudo route del -host <IP> [gw <GATEWAY>]
sudo route del default [gw <GATEWAY>]
```

**Exemples** :

```bash
# Supprimer route par défaut
sudo route del default

# Supprimer route vers un réseau
sudo route del -net 10.0.0.0 netmask 255.0.0.0

# Supprimer route vers un hôte
sudo route del -host 8.8.8.8
```

**Équivalence `route` ↔ `ip route`** :

| Commande `route` (obsolète) | Commande `ip route` (moderne) |
|------------------------------|-------------------------------|
| `route -n` | `ip route show` |
| `route add default gw 192.168.1.254` | `ip route add default via 192.168.1.254` |
| `route add -net 10.0.0.0 netmask 255.0.0.0 gw 192.168.1.1` | `ip route add 10.0.0.0/8 via 192.168.1.1` |
| `route del -net 10.0.0.0 netmask 255.0.0.0` | `ip route del 10.0.0.0/8` |
| `route add -host 8.8.8.8 gw 192.168.1.254` | `ip route add 8.8.8.8 via 192.168.1.254` |

---

### 🏓 Commande `ping` - Test de Connectivité ICMP

#### Description

`ping` envoie des paquets **ICMP Echo Request** (Type 8) et attend des **Echo Reply** (Type 0) pour tester la connectivité réseau.

#### Syntaxe de Base

```bash
ping [OPTIONS] <DESTINATION>
```

#### Options Principales

| Option | Description | Exemple |
|--------|-------------|---------|
| `-c <count>` | Nombre de pings à envoyer puis arrêter | `ping -c 4 google.com` |
| `-i <interval>` | Intervalle en secondes entre les pings (défaut 1s) | `ping -i 0.5 8.8.8.8` (toutes les 0.5s) |
| `-I <interface>` | Interface ou IP source à utiliser | `ping -I eth0 192.168.1.1` |
| `-s <size>` | Taille du payload ICMP en octets (défaut 56) | `ping -s 1024 google.com` |
| `-a` | Audible ping (bip sonore à chaque réponse) | `ping -a 192.168.1.1` |
| `-w <deadline>` | Timeout total en secondes | `ping -w 10 8.8.8.8` (arrêt après 10s) |
| `-W <timeout>` | Timeout d'attente par ping en secondes (défaut 10s) | `ping -W 2 192.168.1.100` |
| `-q` | Quiet mode (affiche seulement le résumé final) | `ping -q -c 10 google.com` |
| `-n` | Numérique (pas de résolution DNS inverse) | `ping -n 8.8.8.8` |
| `-f` | Flood ping (envoi rapide, root requis) | `sudo ping -f 192.168.1.1` |
| `-v` | Verbose (affiche détails ICMP) | `ping -v google.com` |
| `-4` | Forcer IPv4 | `ping -4 google.com` |
| `-6` | Forcer IPv6 | `ping -6 google.com` |

#### Exemples Pratiques

```bash
# Ping simple (Ctrl+C pour arrêter)
ping google.com

# Ping 5 fois puis arrêter
ping -c 5 8.8.8.8

# Ping rapide (toutes les 0.2 secondes)
ping -i 0.2 -c 10 192.168.1.1

# Ping avec grande taille de paquet (test MTU)
ping -s 1472 google.com
# MTU Ethernet = 1500 octets (1472 payload + 28 octets header IP/ICMP)

# Ping avec taille excessive pour tester fragmentation
ping -s 2000 google.com

# Ping audible (bip à chaque réponse)
ping -a -c 10 192.168.1.254

# Ping depuis une interface spécifique
ping -I eth0 192.168.1.1

# Ping depuis une IP source spécifique
ping -I 192.168.1.10 8.8.8.8

# Ping avec timeout par paquet de 2 secondes
ping -W 2 192.168.1.100

# Ping avec timeout global de 10 secondes
ping -w 10 -c 100 192.168.1.200

# Ping quiet (résumé seulement)
ping -q -c 100 google.com

# Flood ping (test performance, root requis)
sudo ping -f -c 1000 192.168.1.1
```

#### Interprétation des Résultats

**Réponse normale** :

```bash
$ ping -c 3 google.com
PING google.com (142.250.201.46) 56(84) bytes of data.
64 bytes from par21s17-in-f14.1e100.net (142.250.201.46): icmp_seq=1 ttl=117 time=12.3 ms
64 bytes from par21s17-in-f14.1e100.net (142.250.201.46): icmp_seq=2 ttl=117 time=11.8 ms
64 bytes from par21s17-in-f14.1e100.net (142.250.201.46): icmp_seq=3 ttl=117 time=12.1 ms

--- google.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 11.753/12.067/12.251/0.207 ms
```

**Informations** :
- `64 bytes` : Taille totale du paquet ICMP reçu
- `icmp_seq=1` : Numéro de séquence (détecte les pertes/duplicatas)
- `ttl=117` : Time To Live (nombre de sauts restants, max 255)
- `time=12.3 ms` : Round-Trip Time (RTT, latence aller-retour)
- `0% packet loss` : Aucun paquet perdu (100% reçus)
- `rtt min/avg/max/mdev` : Latence min/moyenne/max/écart-type

**Pas de réponse (timeout)** :

```bash
$ ping -c 3 -W 2 192.168.1.200
PING 192.168.1.200 (192.168.1.200) 56(84) bytes of data.

--- 192.168.1.200 ping statistics ---
3 packets transmitted, 0 received, 100% packet loss, time 2046ms
```

**Causes possibles** :
- ❌ Hôte éteint ou inexistant
- ❌ Firewall bloque ICMP
- ❌ Routage incorrect
- ❌ Interface réseau down

**Destination inaccessible** :

```bash
$ ping 10.99.1.1
PING 10.99.1.1 (10.99.1.1) 56(84) bytes of data.
From 192.168.1.254 icmp_seq=1 Destination Net Unreachable
```

**Causes possibles** :
- ❌ Pas de route vers le réseau
- ❌ Gateway ne connaît pas le chemin

---

### 🌐 Variantes IPv6 des Outils Réseau

Beaucoup d'outils réseau ont une **variante IPv6** qui est souvent un **lien symbolique** vers l'outil standard avec une option `-6` implicite.

#### Commandes IPv6

| Commande IPv4 | Commande IPv6 | Équivalent |
|---------------|---------------|------------|
| `ping` | `ping6` | `ping -6` |
| `traceroute` | `traceroute6` | `traceroute -6` |
| `tracepath` | `tracepath6` | `tracepath -6` |
| `ip -4 addr` | `ip -6 addr` | Afficher adresses IPv6 |

**Vérification** :

```bash
# ping6 est souvent un lien symbolique vers ping
ls -la /bin/ping6
# lrwxrwxrwx 1 root root 4 Jan 10 12:00 /bin/ping6 -> ping

# Ou une commande distincte mais appelant ping avec -6
```

**Exemples IPv6** :

```bash
# Ping IPv6 loopback
ping6 ::1
# ou
ping -6 ::1

# Ping IPv6 d'un serveur Google
ping6 2001:4860:4860::8888

# Traceroute IPv6
traceroute6 google.com
# ou
traceroute -6 google.com

# Afficher adresses IPv6
ip -6 addr show

# Afficher routes IPv6
ip -6 route show
```

**Options `-4` et `-6`** :

De nombreux outils acceptent ces options pour forcer la version IP :

```bash
# Forcer IPv4
ping -4 google.com
traceroute -4 google.com
ssh -4 user@server.com

# Forcer IPv6
ping -6 google.com
traceroute -6 google.com
ssh -6 user@server.com
```

---

### 🗺️ Commande `traceroute` - Tracer le Chemin Réseau

#### Description

`traceroute` trace le **chemin** que prennent les paquets pour atteindre une destination en affichant tous les **routeurs intermédiaires** (sauts/hops).

**Principe de fonctionnement** :
1. Envoie des paquets avec **TTL=1**, routeur 1 répond ICMP "Time Exceeded"
2. Envoie des paquets avec **TTL=2**, routeur 2 répond ICMP "Time Exceeded"
3. Continue jusqu'à atteindre la destination (ICMP Echo Reply ou ICMP Port Unreachable)

#### Installation

```bash
# Debian/Ubuntu
sudo apt install traceroute

# RHEL/CentOS
sudo yum install traceroute
```

#### Syntaxe

```bash
traceroute [OPTIONS] <DESTINATION>
```

#### Options Principales

| Option | Description | Exemple |
|--------|-------------|---------|
| `-n` | Numérique (pas de résolution DNS inverse) | `traceroute -n 8.8.8.8` |
| `-I` | Utiliser ICMP Echo au lieu de UDP | `sudo traceroute -I google.com` |
| `-T` | Utiliser TCP SYN au lieu de UDP | `sudo traceroute -T google.com` |
| `-p <port>` | Port de destination (défaut UDP 33434+) | `traceroute -p 80 google.com` |
| `-m <max_ttl>` | TTL maximum (défaut 30) | `traceroute -m 15 8.8.8.8` |
| `-w <timeout>` | Timeout par sonde en secondes (défaut 5s) | `traceroute -w 2 google.com` |
| `-q <nqueries>` | Nombre de sondes par saut (défaut 3) | `traceroute -q 1 8.8.8.8` |
| `-f <first_ttl>` | TTL initial (défaut 1) | `traceroute -f 5 google.com` |
| `-4` | Forcer IPv4 | `traceroute -4 google.com` |
| `-6` | Forcer IPv6 | `traceroute -6 google.com` |

#### Exemple de Sortie

```bash
$ traceroute -n 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  192.168.1.254  1.234 ms  1.123 ms  1.056 ms
 2  10.255.255.1  8.456 ms  8.234 ms  8.123 ms
 3  89.2.0.1  12.345 ms  12.234 ms  12.123 ms
 4  * * *
 5  142.251.65.17  15.678 ms  15.567 ms  15.456 ms
 6  8.8.8.8  16.789 ms  16.678 ms  16.567 ms
```

**Interprétation** :
- **Colonne 1** : Numéro du saut (hop)
- **Colonne 2** : Adresse IP du routeur (ou nom si pas `-n`)
- **Colonnes 3-5** : RTT des 3 sondes envoyées (en millisecondes)
- `* * *` : Timeout (routeur ne répond pas ou filtre ICMP)

**Causes de `* * *`** :
- Routeur configuré pour ne pas répondre aux ICMP Time Exceeded
- Firewall filtre les paquets traceroute
- Pas nécessairement un problème si la destination finale répond

#### Méthodes de Traceroute

`traceroute` peut utiliser **3 méthodes** différentes :

| Méthode | Option | Protocole | Privilèges | Description |
|---------|--------|-----------|------------|-------------|
| **UDP** | (défaut) | UDP vers port aléatoire (33434+) | Utilisateur | Méthode par défaut sous Linux |
| **ICMP** | `-I` | ICMP Echo Request | **Root requis** | Similaire à ping |
| **TCP** | `-T` | TCP SYN vers port spécifique | **Root requis** | Utile si firewalls bloquent UDP/ICMP |

**Exemples** :

```bash
# Traceroute UDP classique
traceroute google.com

# Traceroute ICMP (root requis)
sudo traceroute -I google.com

# Traceroute TCP vers port 443 (root requis)
sudo traceroute -T -p 443 google.com

# Traceroute rapide (1 sonde par saut, numérique)
traceroute -n -q 1 8.8.8.8
```

---

### 🛤️ Commande `tracepath` - Tracer le Chemin avec MTU

#### Description

`tracepath` est similaire à `traceroute` mais :
- ✅ **Pas besoin de privilèges root**
- ✅ Détecte automatiquement le **MTU Path** (Maximum Transmission Unit)
- ❌ Moins d'options que `traceroute`
- ❌ Utilise uniquement UDP

#### Installation

Généralement pré-installé (paquet `iputils`).

#### Syntaxe

```bash
tracepath [OPTIONS] <DESTINATION>[:<PORT>]
```

#### Options

| Option | Description |
|--------|-------------|
| `-n` | Numérique (pas de résolution DNS) |
| `-b` | Affiche à la fois IP et hostname |
| `-l <pktlen>` | Taille initiale du paquet |
| `-m <max_hops>` | Nombre maximum de sauts (défaut 30) |
| `-p <port>` | Port de destination (défaut 33434) |

#### Exemple

```bash
$ tracepath google.com
 1?: [LOCALHOST]                      pmtu 1500
 1:  _gateway                                              1.234ms
 2:  10.255.255.1                                          8.456ms
 3:  89.2.0.1                                             12.345ms
 4:  no reply
 5:  142.251.65.17                                        15.678ms asymm  6
 6:  google.com                                           16.789ms reached
     Resume: pmtu 1500 hops 6 back 6
```

**Informations supplémentaires** :
- `pmtu 1500` : Path MTU découvert (taille max paquet sans fragmentation)
- `asymm 6` : Chemin asymétrique (retour différent de l'aller)
- `Resume` : Résumé du tracé

**Différences `traceroute` vs `tracepath`** :

| Caractéristique | `traceroute` | `tracepath` |
|-----------------|--------------|-------------|
| Privilèges root | Requis pour ICMP/TCP | Jamais requis |
| Protocoles | UDP, ICMP, TCP | UDP uniquement |
| Détection MTU | Non | **Oui** |
| Options avancées | Nombreuses | Limitées |
| Usage | Diagnostic avancé | Diagnostic rapide simple |

---

### 🔍 Commande `nslookup` - Interrogation DNS

#### Description

`nslookup` interroge les serveurs **DNS** pour résoudre des noms de domaine en adresses IP (et vice-versa).

⚠️ **Note** : `nslookup` est considéré **obsolète** mais encore très utilisé. Les alternatives modernes sont `dig` et `host`.

#### Installation

Paquet `dnsutils` (Debian/Ubuntu) ou `bind-utils` (RHEL/CentOS).

```bash
# Debian/Ubuntu
sudo apt install dnsutils

# RHEL/CentOS
sudo yum install bind-utils
```

#### Modes d'Utilisation

**Mode non-interactif** (une requête) :

```bash
nslookup <HOSTNAME> [<DNS_SERVER>]
```

**Mode interactif** :

```bash
nslookup
> google.com
> exit
```

#### Exemples

```bash
# Résolution simple (utilise DNS de /etc/resolv.conf)
nslookup google.com

# Résolution avec serveur DNS spécifique
nslookup google.com 8.8.8.8

# Résolution inverse (IP → nom)
nslookup 8.8.8.8

# Requête type spécifique (MX = serveurs mail)
nslookup -query=MX google.com

# Requête type NS (serveurs DNS autoritaires)
nslookup -query=NS google.com

# Requête type ANY (tous les enregistrements)
nslookup -query=ANY google.com

# Mode interactif
nslookup
> server 1.1.1.1           # Changer de serveur DNS
> set type=MX              # Définir type de requête
> google.com               # Interroger
> exit
```

**Exemple de sortie** :

```bash
$ nslookup google.com
Server:         192.168.1.254
Address:        192.168.1.254#53

Non-authoritative answer:
Name:   google.com
Address: 142.250.201.46
Name:   google.com
Address: 2a00:1450:4007:80c::200e
```

**Interprétation** :
- `Server` : Serveur DNS utilisé pour la requête
- `Non-authoritative answer` : Réponse depuis cache (pas le serveur autoritaire)
- `Name` / `Address` : Résolution obtenue

---

## 🎯 Partie 2 : Vérification des Connexions et Flux de Données

### 🔌 Commande `ss` - Socket Statistics (Moderne)

#### Description

`ss` (Socket Statistics) est le **remplacement moderne** de `netstat` pour afficher les connexions réseau, sockets, et statistiques.

**Avantages de `ss`** :
- ✅ **Beaucoup plus rapide** que `netstat` (directement depuis le noyau)
- ✅ Affiche plus d'informations (timers TCP, infos congestion, etc.)
- ✅ Filtres puissants intégrés
- ✅ Pré-installé sur la plupart des distributions modernes

#### Syntaxe de Base

```bash
ss [OPTIONS] [FILTER]
```

#### Options Principales (similaires à netstat)

| Option | Description | Équivalent netstat |
|--------|-------------|-------------------|
| `-a` | **All** : Toutes les sockets (listening + established) | `-a` |
| `-l` | **Listening** : Sockets en écoute uniquement | `-l` |
| `-t` | **TCP** : Connexions TCP uniquement | `-t` |
| `-u` | **UDP** : Connexions UDP uniquement | `-u` |
| `-n` | **Numeric** : Affichage numérique (pas de résolution DNS) | `-n` |
| `-p` | **Process** : Afficher le PID/programme associé | `-p` |
| `-r` | **Resolve** : Résoudre adresses IP en noms d'hôtes | (inverse de `-n`) |
| `-e` | **Extended** : Informations étendues | `-e` |
| `-s` | **Summary** : Statistiques résumées | `-s` |
| `-4` | IPv4 uniquement | `-4` |
| `-6` | IPv6 uniquement | `-6` |

#### Combinaisons Courantes

```bash
# Toutes les connexions TCP en écoute avec processus (très courant)
sudo ss -tlnp

# Toutes les connexions TCP et UDP en écoute
sudo ss -tulnp

# Toutes les connexions actives (établies) TCP
ss -tn

# Toutes les connexions actives avec processus
sudo ss -tnp

# Statistiques résumées
ss -s
```

#### Exemples Détaillés

**Afficher tous les sockets TCP en écoute** :

```bash
$ sudo ss -tlnp
State    Recv-Q   Send-Q     Local Address:Port      Peer Address:Port   Process
LISTEN   0        128              0.0.0.0:22             0.0.0.0:*       users:(("sshd",pid=1234,fd=3))
LISTEN   0        128              0.0.0.0:80             0.0.0.0:*       users:(("apache2",pid=5678,fd=4))
LISTEN   0        128            127.0.0.1:3306           0.0.0.0:*       users:(("mysqld",pid=9012,fd=10))
LISTEN   0        128                 [::]:22                [::]:*       users:(("sshd",pid=1234,fd=4))
```

**Colonnes** :
- **State** : État de la socket (`LISTEN`, `ESTAB`, `TIME-WAIT`, etc.)
- **Recv-Q** : Données en file d'attente réception
- **Send-Q** : Données en file d'attente envoi
- **Local Address:Port** : Adresse et port locaux
  - `0.0.0.0` = Écoute sur toutes les interfaces IPv4
  - `127.0.0.1` = Écoute sur localhost uniquement
  - `::` = Écoute sur toutes les interfaces IPv6
- **Peer Address:Port** : Adresse et port distants (`*` pour listening)
- **Process** : Programme et PID associé (nécessite `sudo` ou root)

**Afficher toutes les connexions établies** :

```bash
$ ss -tn state established
Recv-Q   Send-Q     Local Address:Port       Peer Address:Port
0        0          192.168.1.10:55432       93.184.216.34:443
0        0          192.168.1.10:22          192.168.1.50:49823
```

**Filtres avancés** :

```bash
# Connexions vers le port 80
ss -tn '( dport = :80 or sport = :80 )'

# Connexions depuis une IP spécifique
ss -tn src 192.168.1.50

# Connexions vers un réseau
ss -tn dst 10.0.0.0/8

# Connexions établies TCP
ss -tn state established

# Connexions en TIME-WAIT
ss -tn state time-wait

# Sockets Unix (IPC local)
ss -xl
```

**Statistiques résumées** :

```bash
$ ss -s
Total: 523
TCP:   15 (estab 3, closed 5, orphaned 0, timewait 2)

Transport Total     IP        IPv6
RAW       1         0         1
UDP       8         5         3
TCP       10        6         4
INET      19        11        8
FRAG      0         0         0
```

---

### 📡 Commande `netstat` - Network Statistics (Obsolète)

#### Description

`netstat` affiche les connexions réseau, tables de routage, statistiques d'interfaces, etc.

⚠️ **Obsolète** : Remplacé par `ss`, `ip route`, `ip -s link`.  
📦 Fait partie du paquet **`net-tools`** (non installé par défaut sur distributions récentes).

#### Installation

```bash
# Debian/Ubuntu
sudo apt install net-tools

# RHEL/CentOS
sudo yum install net-tools
```

#### Syntaxe de Base

```bash
netstat [OPTIONS]
```

#### Options Principales

| Option | Description | Exemple |
|--------|-------------|---------|
| `-n` | **Numeric** : Affichage numérique (pas de résolution DNS/service) | `netstat -n` |
| `-a` | **All** : Toutes les connexions/sockets (listening + established) | `netstat -a` |
| `-t` | **TCP** : Connexions TCP uniquement | `netstat -t` |
| `-u` | **UDP** : Connexions UDP uniquement | `netstat -u` |
| `-l` | **Listening** : Sockets en écoute uniquement | `netstat -l` |
| `-p` | **Program** : Afficher PID et nom du programme (root requis) | `sudo netstat -p` |
| `-r` | **Route** : Afficher la table de routage | `netstat -r` |
| `-i` | **Interfaces** : Statistiques des interfaces réseau | `netstat -i` |
| `-s` | **Statistics** : Statistiques par protocole | `netstat -s` |
| `-c` | **Continuous** : Rafraîchir continuellement | `netstat -c` |
| `-e` | **Extend** : Informations étendues | `netstat -e` |
| `-4` | IPv4 uniquement | `netstat -4` |
| `-6` | IPv6 uniquement | `netstat -6` |

#### Combinaison Magique : `-tulpn` 🎯

**La combinaison la plus utilisée** pour voir tous les ports en écoute :

```bash
sudo netstat -tulpn
```

**Décryptage** :
- `-t` : **T**CP sockets
- `-u` : **U**DP sockets
- `-l` : Sockets en écoute (**L**istening)
- `-p` : Afficher le **P**rocessus (PID/programme)
- `-n` : **N**umérique (pas de résolution DNS)

**Exemple de sortie** :

```bash
$ sudo netstat -tulpn
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1234/sshd
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      5678/apache2
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN      9012/mysqld
tcp6       0      0 :::22                   :::*                    LISTEN      1234/sshd
tcp6       0      0 :::80                   :::*                    LISTEN      5678/apache2
udp        0      0 0.0.0.0:68              0.0.0.0:*                           2345/dhclient
udp        0      0 0.0.0.0:123             0.0.0.0:*                           3456/ntpd
```

**Interprétation** :
- **Proto** : Protocole (tcp, tcp6, udp, udp6)
- **Recv-Q / Send-Q** : Données en attente réception/envoi
- **Local Address** :
  - `0.0.0.0:22` = Écoute sur toutes les interfaces IPv4, port 22
  - `127.0.0.1:3306` = Écoute localhost uniquement, port 3306
  - `:::80` = Écoute toutes les interfaces IPv6, port 80
- **Foreign Address** : Adresse distante (`*` pour listening)
- **State** :
  - `LISTEN` = En écoute
  - `ESTABLISHED` = Connexion établie
  - `TIME_WAIT` = Attente fermeture connexion
  - `CLOSE_WAIT` = Attente fermeture par application locale
- **PID/Program** : `1234/sshd` = PID 1234, programme sshd

#### Autres Exemples

```bash
# Afficher la table de routage
netstat -r
# Équivalent à : route -n  ou  ip route show

# Statistiques des interfaces réseau
netstat -i
# Équivalent à : ip -s link

# Statistiques détaillées par protocole
netstat -s

# Connexions actives établies
netstat -tn

# Toutes les connexions UDP
netstat -aun

# Rafraîchissement continu toutes les 2 secondes
netstat -tulpn -c 2
```

#### Équivalence `netstat` ↔ `ss`

| Commande `netstat` | Commande `ss` équivalente |
|--------------------|---------------------------|
| `netstat -tulpn` | `ss -tulnp` |
| `netstat -tn` | `ss -tn` |
| `netstat -r` | `ip route show` |
| `netstat -i` | `ip -s link` |
| `netstat -s` | `ss -s` |
| `netstat -anp` | `ss -anp` |

---

### 🔌 Commande `netcat` (nc) - Swiss Army Knife du Réseau

#### Description

`netcat` (ou `nc`) est un **outil réseau polyvalent** permettant de :
- Créer des connexions TCP/UDP
- Écouter sur des ports (serveur basique)
- Transférer des fichiers
- Scanner des ports
- Tester la connectivité
- Déboguer des protocoles réseau

💡 Surnommé le **"couteau suisse du réseau"**.

#### Installation

```bash
# Debian/Ubuntu (version traditionnelle)
sudo apt install netcat-traditional
# ou (version OpenBSD avec plus d'options)
sudo apt install netcat-openbsd

# RHEL/CentOS
sudo yum install nmap-ncat
# ou
sudo yum install nc
```

**Différentes versions** :
- `netcat-traditional` : Version originale classique
- `netcat-openbsd` : Version OpenBSD avec fonctionnalités supplémentaires
- `nmap-ncat` : Version du projet Nmap (ncat)

#### Syntaxe de Base

```bash
# Mode client (connexion)
nc [OPTIONS] <HOST> <PORT>

# Mode serveur (écoute)
nc -l [OPTIONS] <PORT>
```

#### Options Principales

| Option | Description | Exemple |
|--------|-------------|---------|
| `-l` | **Listen** : Mode écoute (serveur) | `nc -l 1337` |
| `-p <port>` | Port local à utiliser | `nc -l -p 8080` |
| `-u` | Mode UDP (défaut TCP) | `nc -u 192.168.1.10 123` |
| `-v` | Verbose (afficher détails connexion) | `nc -v google.com 80` |
| `-z` | Zero-I/O (scan, pas de données envoyées) | `nc -zv 192.168.1.1 1-1000` |
| `-n` | Numérique (pas de résolution DNS) | `nc -nv 8.8.8.8 53` |
| `-w <sec>` | Timeout connexion en secondes | `nc -w 5 192.168.1.10 22` |
| `-k` | Keep listening après déconnexion (avec `-l`) | `nc -lk 1337` |
| `-e <cmd>` | Exécuter commande sur connexion (dangereux!) | `nc -l -p 1337 -e /bin/bash` |

#### Exemples Pratiques

**1. Créer un serveur d'écoute simple** :

Terminal 1 (serveur) :
```bash
# Écouter sur port 1337
nc -l 1337
# ou avec verbose
nc -lv 1337
```

Terminal 2 (client) :
```bash
# Se connecter au serveur
nc localhost 1337
# ou
nc 192.168.1.10 1337
```

Maintenant, tout ce que vous tapez dans un terminal apparaît dans l'autre ! 🎉

**2. Tester si un port est ouvert** :

```bash
# Tester port SSH (22)
nc -zv 192.168.1.10 22
# Sortie si ouvert :
# Connection to 192.168.1.10 22 port [tcp/ssh] succeeded!

# Tester plage de ports (scan)
nc -zv 192.168.1.10 1-1000
```

**3. Transférer un fichier** :

Terminal 1 (récepteur) :
```bash
# Écouter et rediriger vers fichier
nc -l 9999 > fichier_recu.txt
```

Terminal 2 (émetteur) :
```bash
# Envoyer fichier
nc 192.168.1.10 9999 < fichier_a_envoyer.txt
```

**4. Chat réseau simple** :

Permet de chater entre deux machines :

Machine 1 :
```bash
nc -l 1337
```

Machine 2 :
```bash
nc 192.168.1.10 1337
```

**5. Tester un serveur HTTP** :

```bash
# Connexion au port 80
nc google.com 80

# Puis taper une requête HTTP manuelle :
GET / HTTP/1.1
Host: google.com

# (Appuyer 2 fois sur Entrée pour envoyer)
```

**6. Créer un serveur web ultra-basique** :

```bash
# Terminal 1 : Créer réponse HTTP dans fichier
echo -e "HTTP/1.1 200 OK\n\nHello, World!" > response.txt

# Terminal 2 : Serveur en boucle (keep listening)
while true; do nc -l -p 8080 < response.txt; done

# Test dans navigateur : http://localhost:8080
```

**7. Analyser le trafic d'un port** :

```bash
# Écouter sur port 1337 et afficher tout ce qui arrive
nc -l -v 1337

# Depuis une autre machine :
telnet <server_ip> 1337
# ou
nc <server_ip> 1337
```

**8. Test connectivité UDP** :

```bash
# Serveur UDP
nc -ul 5000

# Client UDP
echo "Test UDP" | nc -u <server_ip> 5000
```

**9. Port forwarding simple** :

```bash
# Rediriger port 8080 local vers port 80 d'un serveur distant
nc -l -p 8080 -c "nc remote_server 80"
```

⚠️ **Sécurité** :
- `nc -e /bin/bash` permet un **shell distant** sans authentification → **DANGEREUX**
- N'exposez jamais netcat en écoute sur Internet sans sécurisation !
- Utilisé souvent pour des **backdoors** → Prudence !

---

### 🔍 Commande `dig` - Domain Information Groper (DNS Avancé)

#### Description

`dig` est l'outil **moderne et puissant** pour interroger les serveurs DNS et obtenir des informations détaillées.

**Avantages sur `nslookup`** :
- ✅ Sortie détaillée et structurée
- ✅ Contrôle total sur les requêtes DNS
- ✅ Format adapté aux scripts
- ✅ Support complet des types d'enregistrements DNS

#### Installation

```bash
# Debian/Ubuntu
sudo apt install dnsutils

# RHEL/CentOS
sudo yum install bind-utils
```

#### Syntaxe de Base

```bash
dig [@SERVER] <DOMAIN> [TYPE] [OPTIONS]
```

#### Exemples de Base

```bash
# Requête simple (utilise DNS de /etc/resolv.conf)
dig google.com

# Requête avec serveur DNS spécifique
dig @8.8.8.8 google.com

# Requête type A (IPv4) - par défaut
dig google.com A

# Requête type AAAA (IPv6)
dig google.com AAAA

# Requête type MX (serveurs mail)
dig google.com MX

# Requête type NS (serveurs DNS autoritaires)
dig google.com NS

# Requête type TXT (enregistrements texte, SPF, DKIM, etc.)
dig google.com TXT

# Requête type ANY (tous les enregistrements)
dig google.com ANY

# Requête inverse (IP → nom)
dig -x 8.8.8.8
```

#### Options Principales

| Option | Description | Exemple |
|--------|-------------|---------|
| `+short` | Affichage court (réponse uniquement) | `dig +short google.com` |
| `+noall +answer` | Afficher seulement la section ANSWER | `dig +noall +answer google.com` |
| `+trace` | Tracer la résolution DNS complète (root → TLD → autoritaire) | `dig +trace google.com` |
| `+dnssec` | Demander validation DNSSEC | `dig +dnssec google.com` |
| `+tcp` | Utiliser TCP au lieu d'UDP | `dig +tcp google.com` |
| `+notcp` | Forcer UDP | `dig +notcp google.com` |
| `-4` | IPv4 uniquement | `dig -4 google.com` |
| `-6` | IPv6 uniquement | `dig -6 google.com` |
| `-x <IP>` | Requête inverse (PTR) | `dig -x 8.8.8.8` |

#### Exemple de Sortie Complète

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
;; WHEN: Wed Feb 12 14:30:25 CET 2026
;; MSG SIZE  rcvd: 55
```

**Sections importantes** :
- **QUESTION** : Requête envoyée (`google.com IN A`)
- **ANSWER** : Réponse reçue (`142.250.201.46` avec TTL `123` secondes)
- **Query time** : Temps de réponse (12 ms)
- **SERVER** : Serveur DNS utilisé (192.168.1.254)

#### Exemples Avancés

**Affichage court (réponse uniquement)** :

```bash
$ dig +short google.com
142.250.201.46

$ dig +short google.com MX
10 smtp.google.com.
```

**Tracer la résolution DNS complète** :

```bash
$ dig +trace google.com
# Affiche toute la chaîne :
# Root servers → .com TLD servers → google.com autoritaires
```

**Requête vers serveur DNS spécifique** :

```bash
# Google Public DNS
dig @8.8.8.8 google.com

# Cloudflare DNS
dig @1.1.1.1 google.com

# Quad9 DNS
dig @9.9.9.9 google.com
```

**Requêtes multiples types** :

```bash
# Enregistrements mail (MX)
dig +short google.com MX
# 10 smtp.google.com.

# Serveurs DNS autoritaires (NS)
dig +short google.com NS
# ns1.google.com.
# ns2.google.com.
# ...

# Enregistrements TXT (SPF, DKIM, etc.)
dig +short google.com TXT
# "v=spf1 include:_spf.google.com ~all"
```

**Résolution inverse (IP → nom)** :

```bash
$ dig -x 8.8.8.8 +short
dns.google.

$ dig -x 142.250.201.46 +short
par21s17-in-f14.1e100.net.
```

**Batch de domaines** :

```bash
# Créer un fichier avec liste de domaines
echo -e "google.com\nfacebook.com\ntwitter.com" > domains.txt

# Boucle de résolution
for domain in $(cat domains.txt); do
    echo "$domain: $(dig +short $domain)"
done
```

**Afficher seulement la section ANSWER** :

```bash
$ dig +noall +answer google.com
google.com.             123     IN      A       142.250.201.46
```

---

### 📦 Commande `tcpdump` - Capture de Paquets Réseau

#### Description

`tcpdump` est un **analyseur de paquets en ligne de commande** permettant de capturer et afficher le trafic réseau.

**Usages** :
- Débogage réseau avancé
- Analyse de protocoles
- Détection d'intrusions
- Diagnostic de problèmes applicatifs

⚠️ **Privilèges root requis** pour capturer le trafic.

#### Installation

```bash
# Debian/Ubuntu
sudo apt install tcpdump

# RHEL/CentOS
sudo yum install tcpdump
```

#### Syntaxe de Base

```bash
sudo tcpdump [OPTIONS] [FILTER]
```

#### Options Principales

| Option | Description | Exemple |
|--------|-------------|---------|
| `-i <interface>` | Interface réseau à écouter (`any` = toutes) | `tcpdump -i eth0` |
| `-n` | Numérique (pas de résolution DNS) | `tcpdump -n` |
| `-nn` | Numérique complet (DNS + ports) | `tcpdump -nn` |
| `-c <count>` | Capturer N paquets puis arrêter | `tcpdump -c 100` |
| `-v` | Verbose (détails) | `tcpdump -v` |
| `-vv` | Très verbose | `tcpdump -vv` |
| `-vvv` | Maximum verbose | `tcpdump -vvv` |
| `-X` | Afficher le contenu en hexadécimal et ASCII | `tcpdump -X` |
| `-A` | Afficher le contenu en ASCII uniquement | `tcpdump -A` |
| `-w <file>` | Écrire capture dans un fichier .pcap | `tcpdump -w capture.pcap` |
| `-r <file>` | Lire depuis un fichier .pcap | `tcpdump -r capture.pcap` |
| `-s <snaplen>` | Taille de capture par paquet (0 = complet) | `tcpdump -s 0` |

#### Filtres Courants

**Protocoles** :

```bash
# Capturer seulement TCP
sudo tcpdump tcp

# Capturer seulement UDP
sudo tcpdump udp

# Capturer seulement ICMP (ping)
sudo tcpdump icmp

# Capturer seulement ARP
sudo tcpdump arp
```

**Ports** :

```bash
# Capturer port 80 (HTTP)
sudo tcpdump port 80

# Capturer port 443 (HTTPS)
sudo tcpdump port 443

# Capturer port 22 (SSH)
sudo tcpdump port 22

# Capturer port source 80
sudo tcpdump src port 80

# Capturer port destination 80
sudo tcpdump dst port 80

# Capturer plage de ports
sudo tcpdump portrange 8000-9000
```

**Hôtes** :

```bash
# Capturer trafic de/vers une IP
sudo tcpdump host 192.168.1.10

# Capturer trafic depuis une IP
sudo tcpdump src 192.168.1.10

# Capturer trafic vers une IP
sudo tcpdump dst 192.168.1.10

# Capturer trafic d'un réseau
sudo tcpdump net 192.168.1.0/24
```

**Combinaisons** :

```bash
# HTTP depuis une IP spécifique
sudo tcpdump -nn src 192.168.1.10 and port 80

# SSH ou HTTP
sudo tcpdump 'port 22 or port 80'

# Tout sauf SSH
sudo tcpdump 'not port 22'

# TCP vers Google DNS
sudo tcpdump -nn tcp and dst host 8.8.8.8

# ICMP depuis réseau local
sudo tcpdump -nn icmp and src net 192.168.1.0/24
```

#### Exemples Pratiques

**Capturer tout le trafic HTTP** :

```bash
sudo tcpdump -nn -A port 80
```

**Capturer pings (ICMP)** :

```bash
sudo tcpdump -nn icmp
```

**Capturer et sauvegarder dans un fichier** :

```bash
# Capture
sudo tcpdump -i eth0 -w capture.pcap

# Relire
sudo tcpdump -r capture.pcap
```

**Capturer 100 paquets puis arrêter** :

```bash
sudo tcpdump -c 100 -w sample.pcap
```

**Afficher le contenu des paquets en ASCII** :

```bash
sudo tcpdump -A -i eth0 port 80
```

**Capturer le trafic d'une conversation TCP complète** :

```bash
sudo tcpdump -nn -X 'tcp and (host 192.168.1.10 and host 192.168.1.20)'
```

**Capturer requêtes DNS** :

```bash
sudo tcpdump -nn -i eth0 port 53
```

**Capturer trafic HTTPS (TLS)** :

```bash
sudo tcpdump -nn -i eth0 port 443
```

**Filtrer par flags TCP (SYN, ACK, FIN, etc.)** :

```bash
# Capturer seulement paquets SYN (nouvelle connexion)
sudo tcpdump 'tcp[tcpflags] & tcp-syn != 0'

# Capturer SYN-ACK
sudo tcpdump 'tcp[tcpflags] & (tcp-syn|tcp-ack) == (tcp-syn|tcp-ack)'

# Capturer RST (reset)
sudo tcpdump 'tcp[tcpflags] & tcp-rst != 0'
```

---

### 🌊 Commande `tcpflow` - Analyse de Flux TCP

#### Description

`tcpflow` est un outil similaire à `tcpdump` mais **reconstruit les flux TCP** complets, ce qui facilite l'analyse du contenu.

**Avantages** :
- ✅ Reconstruit automatiquement les sessions TCP
- ✅ Sauvegarde chaque flux dans un fichier séparé
- ✅ Plus facile pour analyser des conversations HTTP, FTP, etc.
- ❌ Moins complet que tcpdump pour diagnostic bas niveau

#### Installation

```bash
# Debian/Ubuntu
sudo apt install tcpflow

# RHEL/CentOS
sudo yum install tcpflow
```

#### Syntaxe de Base

```bash
sudo tcpflow [OPTIONS] [FILTER]
```

#### Options Principales

| Option | Description | Exemple |
|--------|-------------|---------|
| `-c` | Afficher flux sur console (au lieu de fichiers) | `tcpflow -c port 80` |
| `-i <interface>` | Interface réseau à écouter | `tcpflow -i eth0` |
| `-p` | Mode promiscuous désactivé | `tcpflow -p` |
| `-o <dir>` | Répertoire de sortie pour fichiers | `tcpflow -o /tmp/capture` |
| `-r <file>` | Lire depuis fichier .pcap | `tcpflow -r capture.pcap` |

#### Exemples

**Capturer trafic HTTP et afficher sur console** :

```bash
sudo tcpflow -c port 80
```

Quand un client accède à un site web, vous verrez :
```
192.168.001.010.54321-093.184.216.034.00080:
GET / HTTP/1.1
Host: example.com
User-Agent: curl/7.68.0

093.184.216.034.00080-192.168.001.010.54321:
HTTP/1.1 200 OK
Content-Type: text/html

<!DOCTYPE html>
<html>...
```

**Capturer et sauvegarder dans fichiers** :

```bash
sudo tcpflow -i eth0 port 80
# Crée des fichiers nommés :
# 192.168.001.010.54321-093.184.216.034.00080
# 093.184.216.034.00080-192.168.001.010.54321
```

Chaque fichier contient le flux TCP complet d'une direction.

**Analyser un fichier .pcap existant** :

```bash
# D'abord capturer avec tcpdump
sudo tcpdump -i eth0 -w capture.pcap

# Puis analyser avec tcpflow
tcpflow -r capture.pcap
```

**Capturer seulement trafic vers un serveur** :

```bash
sudo tcpflow -c dst host 192.168.1.100
```

**Exemple complet : Analyser requêtes HTTP** :

```bash
# Terminal 1 : Capturer
sudo tcpflow -c -i eth0 port 80

# Terminal 2 : Générer trafic
curl http://example.com

# Terminal 1 affichera la requête HTTP complète et la réponse
```

---

## 🛠️ Scénarios Pratiques de Troubleshooting

### Scénario 1 : Pas de Connectivité Internet 🌐❌

**Symptôme** : Impossible de joindre Internet.

**Méthodologie ascendante OSI** :

```bash
# 1. Couche Physique/Liaison - Interface active ?
ip link show
# Chercher : UP, LOWER_UP

# Si DOWN :
sudo ip link set eth0 up

# 2. Couche Réseau - IP configurée ?
ip addr show eth0
# Chercher : inet 192.168.1.10/24

# Si pas d'IP (DHCP):
sudo dhclient eth0

# 3. Couche Réseau - Loopback OK ?
ping -c 3 127.0.0.1
# Doit répondre (test pile TCP/IP locale)

# 4. Couche Réseau - Gateway joignable ?
ip route show
# Noter l'IP après "default via"
ping -c 3 192.168.1.254

# Si pas de réponse :
# - Vérifier câble/WiFi
# - Vérifier routeur allumé

# 5. Couche Réseau - Internet joignable ?
ping -c 3 8.8.8.8
# Si OK → Problème DNS, pas réseau !

# 6. Couche Application - DNS fonctionne ?
nslookup google.com
# ou
dig +short google.com

# Si échec DNS (mais 8.8.8.8 OK):
cat /etc/resolv.conf
# Ajouter nameserver si absent :
echo "nameserver 8.8.8.8" | sudo tee -a /etc/resolv.conf

# 7. Test final
ping -c 3 google.com
curl -I http://google.com
```

---

### Scénario 2 : Service Web ne Répond Pas 🌐🔴

**Symptôme** : Apache/Nginx installé mais `http://localhost` ne fonctionne pas.

**Diagnostic** :

```bash
# 1. Service actif ?
sudo systemctl status apache2
# ou
sudo systemctl status nginx

# Si inactif :
sudo systemctl start apache2

# 2. Service écoute sur port 80 ?
sudo ss -tlnp | grep :80
# ou
sudo netstat -tlnp | grep :80

# Doit afficher :
# LISTEN  0.0.0.0:80  ... apache2

# Si absent → Service pas démarré ou config incorrecte

# 3. Firewall bloque ?
# Debian/Ubuntu (ufw)
sudo ufw status
sudo ufw allow 80/tcp

# RHEL/CentOS (firewalld)
sudo firewall-cmd --list-all
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --reload

# 4. Test local
curl -I http://localhost
# Doit renvoyer : HTTP/1.1 200 OK

# 5. Test depuis réseau local (autre machine)
curl -I http://192.168.1.10

# Si échec :
# - Interface écoute seulement 127.0.0.1 ?
sudo ss -tlnp | grep :80
# Si "127.0.0.1:80" → Changer config pour "0.0.0.0:80"

# 6. Logs d'erreurs
sudo tail -f /var/log/apache2/error.log
sudo tail -f /var/log/nginx/error.log
```

---

### Scénario 3 : Latence Réseau Élevée 🐌

**Symptôme** : Connexions lentes, pings > 100ms.

**Diagnostic** :

```bash
# 1. Mesurer latence vers gateway
ping -c 10 $(ip route show | grep default | awk '{print $3}')

# Si latence élevée vers gateway → Problème local (WiFi, switch)

# 2. Mesurer latence Internet
ping -c 10 8.8.8.8

# 3. Tracer le chemin et identifier le saut lent
traceroute -n 8.8.8.8
# ou
mtr -n 8.8.8.8  # MTR = meilleur que traceroute (live)

# Chercher le saut avec forte latence

# 4. Test MTU (fragmentation ?)
ping -c 5 -M do -s 1472 8.8.8.8
# -M do = Don't Fragment
# -s 1472 = Payload (1472 + 28 header = 1500 MTU)

# Si échec → Problème MTU, réduire :
ping -c 5 -M do -s 1400 8.8.8.8

# 5. Test perte de paquets
ping -c 100 -i 0.2 8.8.8.8 | grep loss
# Idéal : 0% packet loss
# Problématique : >5% packet loss

# 6. Vérifier bande passante (si disponible)
# Installer speedtest-cli
speedtest-cli
```

---

### Scénario 4 : Route Manquante vers un Réseau 🛣️

**Symptôme** : Impossible de joindre le réseau `10.0.0.0/8`.

```bash
# 1. Vérifier route existe
ip route show | grep "10.0.0.0"

# Si absent → Ajouter route
sudo ip route add 10.0.0.0/8 via 192.168.1.1 dev eth0

# 2. Tester connectivité
ping -c 3 10.0.0.5

# 3. Rendre permanent (Debian /etc/network/interfaces)
sudo nano /etc/network/interfaces
# Ajouter :
# up ip route add 10.0.0.0/8 via 192.168.1.1 dev eth0

# Ou (RHEL /etc/sysconfig/network-scripts/route-eth0)
echo "10.0.0.0/8 via 192.168.1.1 dev eth0" | sudo tee /etc/sysconfig/network-scripts/route-eth0

# 4. Redémarrer réseau
sudo systemctl restart networking
```

---

### Scénario 5 : Port Apparemment Ouvert mais Inaccessible 🔒

**Symptôme** : `ss` montre port 3306 (MySQL) en écoute mais connexion refuse.

```bash
# 1. Vérifier binding
sudo ss -tlnp | grep 3306

# Si : 127.0.0.1:3306 → Écoute localhost uniquement !
# Modifier config MySQL pour écouter 0.0.0.0 ou IP publique

# Fichier : /etc/mysql/mysql.conf.d/mysqld.cnf ou /etc/my.cnf
# Chercher ligne : bind-address = 127.0.0.1
# Remplacer par : bind-address = 0.0.0.0

# 2. Redémarrer service
sudo systemctl restart mysql

# 3. Vérifier nouveau binding
sudo ss -tlnp | grep 3306
# Devrait afficher : 0.0.0.0:3306

# 4. Tester connexion externe
# Depuis autre machine :
mysql -h 192.168.1.10 -u root -p

# Ou avec nc :
nc -zv 192.168.1.10 3306
```

---

## ❌ Erreurs Courantes et Solutions

### Erreur 1 : "Network is unreachable"

```bash
$ ping 8.8.8.8
connect: Network is unreachable
```

**Causes** :
- ❌ Pas de route par défaut (gateway)
- ❌ Interface réseau down

**Solution** :

```bash
# Vérifier routes
ip route show

# Ajouter gateway si absente
sudo ip route add default via 192.168.1.254 dev eth0

# Activer interface si down
sudo ip link set eth0 up
```

---

### Erreur 2 : "Destination Host Unreachable"

```bash
$ ping 192.168.1.50
From 192.168.1.10 icmp_seq=1 Destination Host Unreachable
```

**Causes** :
- ❌ Hôte éteint ou inexistant
- ❌ Pas de route ARP (couche 2)
- ❌ Firewall bloque

**Solution** :

```bash
# Vérifier ARP
ip neigh show | grep 192.168.1.50

# Si "FAILED" → Hôte ne répond pas (éteint?)

# Tenter ping broadcast pour forcer ARP
ping -b 192.168.1.255

# Vérifier routage local
ip route get 192.168.1.50
```

---

### Erreur 3 : "No route to host"

```bash
$ ping 10.0.0.5
connect: No route to host
```

**Causes** :
- ❌ Pas de route vers le réseau

**Solution** :

```bash
# Ajouter route
sudo ip route add 10.0.0.0/8 via 192.168.1.1 dev eth0

# Ou route par défaut si route spécifique manque
sudo ip route add default via 192.168.1.254
```

---

### Erreur 4 : `ss` ou `netstat` n'affiche pas les processus (colonne vide)

```bash
$ ss -tlnp
# Colonne Process vide
```

**Cause** :
- ❌ Pas de privilèges root

**Solution** :

```bash
# Utiliser sudo
sudo ss -tlnp
sudo netstat -tlnp
```

---

### Erreur 5 : `traceroute` bloqué (tous les sauts `* * *`)

```bash
$ traceroute google.com
 1  * * *
 2  * * *
 3  * * *
...
```

**Causes** :
- ❌ Firewall bloque UDP (traceroute par défaut)
- ❌ ISP filtre le trafic

**Solution** :

```bash
# Essayer ICMP (root requis)
sudo traceroute -I google.com

# Essayer TCP (root requis)
sudo traceroute -T -p 80 google.com
```

---

### Erreur 6 : `ping` fonctionne mais `dig` échoue

```bash
$ ping -c 1 8.8.8.8
# OK

$ dig google.com
;; connection timed out; no servers could be reached
```

**Cause** :
- ❌ DNS non configuré dans `/etc/resolv.conf`

**Solution** :

```bash
# Vérifier config DNS
cat /etc/resolv.conf

# Ajouter serveur DNS si vide
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf

# Tester
dig +short google.com
```

---

## 📝 Cheat Sheet - Troubleshooting Réseau

### Diagnostic Rapide

```bash
# 1. Interface UP ?
ip link show

# 2. IP configurée ?
ip addr show

# 3. Loopback OK ?
ping -c 1 127.0.0.1

# 4. Gateway joignable ?
ping -c 1 $(ip route | grep default | awk '{print $3}')

# 5. Internet OK ?
ping -c 1 8.8.8.8

# 6. DNS OK ?
dig +short google.com
```

---

### Commandes Essentielles par Catégorie

**Routes** :
```bash
ip route show                              # Afficher routes
sudo ip route add 10.0.0.0/8 via 192.168.1.1  # Ajouter route
sudo ip route del 10.0.0.0/8              # Supprimer route
ip route get 8.8.8.8                      # Route utilisée pour IP
```

**Connectivité** :
```bash
ping -c 4 google.com                      # Test connectivité
ping -c 4 -I eth0 8.8.8.8                # Ping depuis interface
traceroute -n 8.8.8.8                     # Tracer chemin
tracepath google.com                      # Tracer avec MTU
```

**DNS** :
```bash
nslookup google.com                       # Résolution simple
dig +short google.com                     # Résolution courte
dig @8.8.8.8 google.com MX               # Requête type MX serveur spécifique
dig +trace google.com                     # Tracer résolution complète
dig -x 8.8.8.8                           # Résolution inverse
```

**Connexions** :
```bash
sudo ss -tulnp                            # Ports en écoute
sudo ss -tnp                              # Connexions TCP actives
sudo netstat -tulpn                       # (obsolète) Ports en écoute
```

**Tests Ports** :
```bash
nc -zv 192.168.1.10 22                   # Test port SSH ouvert
nc -l 1337                               # Écouter port 1337
echo "test" | nc 192.168.1.10 1337       # Envoyer vers port
```

**Capture Paquets** :
```bash
sudo tcpdump -i eth0 -nn port 80         # Capturer HTTP
sudo tcpdump -i eth0 -w capture.pcap     # Sauvegarder capture
sudo tcpflow -c port 80                   # Analyser flux HTTP
```

---

### Combinaisons Utiles

```bash
# Tout-en-un : Diagnostic complet
ping -c 1 127.0.0.1 && \
ping -c 1 $(ip route | grep default | awk '{print $3}') && \
ping -c 1 8.8.8.8 && \
dig +short google.com && \
echo "✅ Réseau OK"

# Scanner ports avec nc
for port in 20 21 22 23 80 443; do
    nc -zv -w 1 192.168.1.10 $port 2>&1 | grep succeeded
done

# Top 10 connexions actives par IP
sudo ss -tn | awk '{print $5}' | cut -d: -f1 | sort | uniq -c | sort -nr | head -10
```

---

## 🎯 Points Clés pour l'Examen LPIC-1

### Ce qu'il faut maîtriser ✅

1. **Méthodologie de troubleshooting ascendante (OSI)** 🎯
   - Toujours commencer par couche physique/liaison
   - Tester loopback avant gateway
   - Isoler le niveau où le problème apparaît

2. **Gestion des routes** 🛣️
   - `ip route show` pour afficher
   - `ip route add/del` pour modifier
   - Différence route spécifique vs route par défaut
   - Priorité métrique

3. **Commande `ping`** 🏓
   - Options `-c`, `-i`, `-I`, `-s`, `-w`, `-W`
   - Interpréter RTT, TTL, packet loss
   - Utiliser pour tests MTU (`-s 1472 -M do`)

4. **Tracer le chemin réseau** 🗺️
   - `traceroute` avec méthodes UDP/ICMP/TCP
   - `tracepath` pour MTU discovery
   - Interpréter `* * *` (pas forcément problème)

5. **Connexions actives** 🔌
   - `ss` (moderne) vs `netstat` (obsolète)
   - Combinaison `-tulnp` essentielle
   - Interpréter états TCP (LISTEN, ESTAB, TIME_WAIT)

6. **Tests de ports** 🚪
   - `nc -zv` pour scanner
   - `nc -l` pour écouter
   - Utilisation pour transfert fichiers

7. **DNS avancé** 🔍
   - `dig` préféré à `nslookup`
   - Options `+short`, `+trace`, `+noall +answer`
   - Types d'enregistrements (A, AAAA, MX, NS, TXT)

8. **Capture de paquets** 📦
   - `tcpdump` avec filtres (port, host, tcp/udp)
   - `tcpflow` pour reconstruction flux TCP
   - Sauvegarder/relire fichiers .pcap

---

### Pièges Fréquents à l'Examen ⚠️

1. **Confusion `netstat` vs `ss`** :
   - ❌ `netstat` est obsolète mais encore dans exam
   - ✅ Connaître les deux syntaxes

2. **Oublier `sudo` pour `ss -tlnp`** :
   - Sans root, colonne Process vide !

3. **Différence `ip route` vs `route`** :
   - `ip route` : notation CIDR (`10.0.0.0/8`)
   - `route` : notation netmask (`10.0.0.0 netmask 255.0.0.0`)

4. **Binding 127.0.0.1 vs 0.0.0.0** :
   - `127.0.0.1:80` → Accessible localhost uniquement
   - `0.0.0.0:80` → Accessible toutes interfaces

5. **Méthodes traceroute** :
   - UDP par défaut (pas root)
   - ICMP (`-I`) et TCP (`-T`) nécessitent root

6. **`dig` vs `nslookup`** :
   - `dig` moderne et recommandé
   - `nslookup` obsolète mais encore utilisé

---

### Questions Types Examen

**Q1** : Comment afficher toutes les connexions TCP en écoute avec les processus associés ?
```bash
sudo ss -tlnp
# ou
sudo netstat -tlnp
```

**Q2** : Comment ajouter une route vers le réseau 172.16.0.0/12 via le routeur 192.168.1.1 ?
```bash
sudo ip route add 172.16.0.0/12 via 192.168.1.1
```

**Q3** : Comment tester si le port 3306 de l'hôte 192.168.1.50 est ouvert ?
```bash
nc -zv 192.168.1.50 3306
```

**Q4** : Comment afficher uniquement les adresses IP IPv4 de google.com ?
```bash
dig +short google.com A
```

**Q5** : Comment capturer les 50 premiers paquets HTTP sur eth0 et les sauvegarder ?
```bash
sudo tcpdump -i eth0 -c 50 -w capture.pcap port 80
```

**Q6** : Comment tracer le chemin vers 8.8.8.8 en utilisant ICMP ?
```bash
sudo traceroute -I 8.8.8.8
```

**Q7** : Comment afficher la route qui serait utilisée pour joindre 10.0.5.100 ?
```bash
ip route get 10.0.5.100
```

**Q8** : Comment envoyer exactement 5 pings avec une taille de 1000 octets ?
```bash
ping -c 5 -s 1000 google.com
```

---

### Commandes à Connaître par Cœur 💯

```bash
ip route show                    # Routes
ip route add/del                 # Modifier routes
ping -c N -I iface -s size      # Test connectivité
traceroute -n -I -T             # Tracer chemin
tracepath                        # Tracer + MTU
nslookup domain [server]        # DNS simple
dig [@server] domain [type]     # DNS avancé
ss -tulnp                        # Connexions modernes
netstat -tulnp                   # Connexions obsolètes
nc -zv host port                 # Test port
nc -l port                       # Écouter port
tcpdump -i eth0 [filter]        # Capture paquets
tcpflow -c [filter]             # Flux TCP
```

---

**Poids du module** : 4 points  
**Difficulté** : ⭐⭐⭐ Moyenne  
**Temps de préparation recommandé** : 4-6 heures

🎯 **Pratique essentielle** : Ces outils sont utilisés **quotidiennement** par les administrateurs système. Concentrez-vous sur les scénarios réels de troubleshooting plutôt que mémoriser toutes les options !
