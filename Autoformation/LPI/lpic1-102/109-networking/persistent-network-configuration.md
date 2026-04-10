# 🔌 Configuration Réseau Persistante Linux

## 📋 Informations du Module

- **Examen** : LPIC-1 102
- **Objectif** : 109.2 - Persistent network configuration
- **Poids** : 4 points
- **Objectif** : Configurer réseau de manière persistante (interfaces, routes, hostname, DNS)

---

# 📖 PARTIE 1 : BASES & INTERFACES RÉSEAU

## 🌐 Concepts Fondamentaux

### 🚪 Gateway (Passerelle)

**Rôle :** Routeur permettant d'accéder à d'autres réseaux.

```
┌─────────────────────────────────────────────────┐
│  RÔLE DE LA GATEWAY                             │
├─────────────────────────────────────────────────┤
│                                                 │
│  Réseau Local (192.168.1.0/24)                  │
│  ┌──────────────────────────────────┐           │
│  │                                  │           │
│  │  PC1          PC2         PC3    │           │
│  │  .10          .20          .30   │           │
│  │                                  │           │
│  │        Switch                    │           │
│  │           │                      │           │
│  └───────────┼──────────────────────┘           │
│              │                                  │
│              ↓                                  │
│         ┌─────────┐                             │
│         │ Gateway │  192.168.1.254              │
│         │ (Router)│                             │
│         └────┬────┘                             │
│              │                                  │
│              ↓                                  │
│         🌐 Internet                             │
│                                                 │
└─────────────────────────────────────────────────┘
```

**Fonctionnement :**

```bash
# PC1 (192.168.1.10) veut joindre 8.8.8.8 (Google DNS)

# 1. PC1 vérifie : 8.8.8.8 dans mon réseau (192.168.1.0/24) ?
#    → NON

# 2. PC1 envoie packet vers gateway (192.168.1.254)

# 3. Gateway route vers Internet

# 4. Réponse revient via gateway → PC1
```

**Configuration gateway :**
```bash
# Voir gateway actuelle
ip route show
# default via 192.168.1.254 dev eth0

route -n
# Destination     Gateway         Genmask
# 0.0.0.0         192.168.1.254   0.0.0.0

# Ajouter gateway
ip route add default via 192.168.1.254
```

### 🔌 NIC - Network Interface Card

**NIC** : Carte réseau (physique ou virtuelle).

**Types :**
- **Ethernet** : Câble RJ45 (filaire)
- **WiFi** : Sans fil
- **Loopback** : Interface virtuelle (localhost)
- **Bridge** : Pont (virtualisation)
- **Virtual** : TUN/TAP, VPN...

```bash
# Lister interfaces
ip link show

# 1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536
# 2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500
# 3: wlan0: <BROADCAST,MULTICAST> mtu 1500
```

### 🔄 Interface Loopback

**lo** : Interface locale virtuelle (127.0.0.1 / ::1)

**Caractéristiques :**
- Toujours présente
- Trafic ne sort jamais de la machine
- Utilisée pour tests, services locaux
- Ne peut pas être supprimée

```bash
# Voir loopback
ip addr show lo

# 1: lo: <LOOPBACK,UP,LOWER_UP>
#     inet 127.0.0.1/8 scope host lo
#     inet6 ::1/128 scope host

# Test loopback
ping 127.0.0.1
ping localhost
```

**Cas d'usage :**
```bash
# Service base de données écoute localhost
mysql --host=127.0.0.1

# API locale
curl http://localhost:8080/api

# Développement web
python3 -m http.server 8000
# Accessible via http://127.0.0.1:8000
```

---

## 🏷️ Nomenclature Interfaces Réseau

### 📛 Historique - eth0, wlan0

**Ancien système (avant systemd v197, ~2012) :**

| Interface | Type | Description |
|-----------|------|-------------|
| **eth0** | Ethernet | 1ère carte réseau filaire |
| **eth1** | Ethernet | 2ème carte réseau filaire |
| **wlan0** | WiFi | 1ère carte WiFi |
| **wlan1** | WiFi | 2ème carte WiFi |
| **lo** | Loopback | Interface locale |

**Problème :** Ordre non déterministe au boot.

```
Boot 1 :  eth0 = Intel card,  eth1 = Realtek card
Boot 2 :  eth0 = Realtek card, eth1 = Intel card  (INVERSÉ!)
```

### 🆕 Moderne - Predictable Network Interface Names

**Depuis systemd v197 (RHEL 7, Debian 9, Ubuntu 15.10+) :**

**Format :** `<type><source><slot>`

**Préfixes type :**

| Préfixe | Type |
|---------|------|
| **en** | Ethernet |
| **wl** | WLAN (WiFi) |
| **ww** | WWAN (cellulaire) |
| **sl** | Serial line IP (SLIP) |

**Suffixes source/slot :**

| Format | Description | Exemple |
|--------|-------------|---------|
| **o\<index>** | On-board (carte mère) | `eno1`, `eno2` |
| **s\<slot>** | Hotplug slot | `ens1`, `ens2` |
| **p\<bus>s\<slot>** | PCI geographic | `enp3s0`, `enp4s1` |
| **x\<MAC>** | MAC address | `enx78e7d1ea46da` |

**Exemples réels :**

```bash
# Carte mère intégrée #1
eno1

# PCI bus 0, slot 31, function 6
enp0s31f6

# PCI bus 3, slot 0
enp3s0

# USB Ethernet (MAC-based)
enx00e04c68def1

# WiFi PCI bus 2, slot 0
wlp2s0

# Loopback (toujours lo)
lo
```

**Avantages :**
- ✅ Noms **stables** entre reboots
- ✅ **Prévisible** selon hardware
- ✅ Pas de conflit

**Désavantages :**
- ❌ Noms **complexes** (enp3s0 vs eth0)
- ❌ Scripts legacy cassés

### 🔄 Revenir à l'Ancien Système

```bash
# Désactiver noms prévisibles (GRUB)
sudo vim /etc/default/grub

# Ajouter à GRUB_CMDLINE_LINUX
GRUB_CMDLINE_LINUX="net.ifnames=0 biosdevname=0"

# Régénérer GRUB
sudo update-grub        # Debian/Ubuntu
sudo grub2-mkconfig -o /boot/grub2/grub.cfg  # RHEL/CentOS

# Reboot
sudo reboot

# Interfaces redeviennent eth0, eth1...
```

---

## 🔍 Commandes Visualisation Interfaces

### 📊 ifconfig (Historique)

**⚠️ Obsolète** (remplacé par `ip`), mais encore présent.

```bash
# Installation si absent
sudo apt install net-tools    # Debian/Ubuntu
sudo yum install net-tools    # RHEL/CentOS

# Afficher interfaces actives
ifconfig

# eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
#         inet 192.168.1.10  netmask 255.255.255.0  broadcast 192.168.1.255
#         inet6 fe80::a00:27ff:fe4e:66a1  prefixlen 64
#         ether 08:00:27:4e:66:a1  txqueuelen 1000
#         RX packets 12345  bytes 1234567 (1.2 MB)
#         TX packets 6789   bytes 789012 (789.0 KB)

# Afficher TOUTES interfaces (même down)
ifconfig -a

# Interface spécifique
ifconfig eth0
```

**Informations affichées :**
- **inet** : IPv4
- **inet6** : IPv6
- **netmask** : Masque réseau
- **broadcast** : Adresse broadcast
- **ether** : Adresse MAC
- **RX/TX** : Statistiques réception/transmission
- **MTU** : Maximum Transmission Unit
- **flags** : État (UP, RUNNING...)

### 🆕 ip (Moderne)

**Commande moderne** (iproute2 package).

```bash
# Lister interfaces
ip link show

# 1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
#     link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
# 2: enp3s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP
#     link/ether 08:00:27:4e:66:a1 brd ff:ff:ff:ff:ff:ff

# Afficher adresses IP
ip addr show
# ou
ip a

# 2: enp3s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500
#     inet 192.168.1.10/24 brd 192.168.1.255 scope global enp3s0
#     inet6 fe80::a00:27ff:fe4e:66a1/64 scope link

# Interface spécifique
ip addr show enp3s0
ip link show enp3s0

# Format concis
ip -br addr
# lo               UNKNOWN        127.0.0.1/8 ::1/128
# enp3s0           UP             192.168.1.10/24 fe80::a00:27ff:fe4e:66a1/64

# Statistiques
ip -s link show enp3s0
# RX: bytes  packets  errors  dropped
# TX: bytes  packets  errors  dropped
```

**Comparaison ifconfig vs ip :**

| Action | ifconfig | ip |
|--------|----------|-----|
| Lister interfaces | `ifconfig -a` | `ip link show` |
| Voir IPs | `ifconfig` | `ip addr show` |
| Stats | `ifconfig -s` | `ip -s link` |
| Interface up | `ifconfig eth0 up` | `ip link set eth0 up` |
| Interface down | `ifconfig eth0 down` | `ip link set eth0 down` |

---

# 🔧 PARTIE 2 : CONFIGURATION RÉSEAU

## ⚡ Configuration Temporaire (Commandes)

### 🔧 Avec ifconfig (Obsolète)

```bash
# Assigner IP et netmask (syntaxe classique)
sudo ifconfig eth0 192.168.45.25 netmask 255.255.255.0

# Syntaxe alternative avec 'inet'
sudo ifconfig eth0 inet 192.168.1.2 netmask 255.255.255.0

# Vérifier
ifconfig eth0
# eth0: inet 192.168.45.25  netmask 255.255.255.0

# Broadcast (optionnel)
sudo ifconfig eth0 192.168.45.25 netmask 255.255.255.0 broadcast 192.168.45.255

# Activer interface
sudo ifconfig eth0 up

# Désactiver interface
sudo ifconfig eth0 down

# Supprimer IP (impossible directement avec ifconfig simple)
# Nécessite down puis reconfiguration
```

### 🆕 Avec ip (Moderne)

**Ajouter adresse IP :**

```bash
# Format CIDR (recommandé)
sudo ip addr add 192.168.42.25/24 dev eth0

# Exemple avec interface moderne (enp0s10)
sudo ip addr add 192.168.1.100/24 dev enp0s10

# Vérifier
ip addr show eth0
# 2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP>
#     inet 192.168.42.25/24 scope global eth0

# Plusieurs IPs sur même interface (OK)
sudo ip addr add 192.168.42.26/24 dev eth0
sudo ip addr add 10.0.0.5/8 dev eth0
```

**Supprimer adresse IP :**

```bash
# Supprimer IP spécifique
sudo ip addr del 192.168.42.25/24 dev eth0

# Vérifier suppression
ip addr show eth0
```

**Activer/Désactiver interface :**

```bash
# Désactiver (down)
sudo ip link set eth0 down

# Activer (up)
sudo ip link set eth0 up

# Vérifier état
ip link show eth0
# 2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP>
#          ↑ UP = activée
```

**Modifier MTU :**

```bash
# Changer MTU (Maximum Transmission Unit)
sudo ip link set eth0 mtu 9000

# Vérifier
ip link show eth0
# mtu 9000
```

### 🛣️ Configuration Routes

```bash
# Voir table routage
ip route show
# default via 192.168.1.254 dev eth0
# 192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.10

route -n  # Ancien (net-tools)

# Ajouter route par défaut (gateway) - Commande ip (moderne)
sudo ip route add default via 192.168.42.254

# Modifier route par défaut existante
sudo ip route change default via 192.168.99.1

# Ajouter route réseau spécifique
sudo ip route add 10.0.0.0/8 via 192.168.42.1 dev eth0

# Supprimer route
sudo ip route del default via 192.168.42.254
sudo ip route del 10.0.0.0/8

# Route spécifique pour hôte
sudo ip route add 8.8.8.8 via 192.168.42.1
```

**Avec route (obsolète mais toujours disponible) :**

```bash
# Ajouter route vers réseau spécifique
sudo route add -net 192.168.2.0 netmask 255.255.255.0 enp0s10

# Ajouter route par défaut (gateway)
sudo route add default gw 192.168.1.254

# Supprimer route
sudo route del -net 192.168.2.0 netmask 255.255.255.0
sudo route del default gw 192.168.1.254
```

**⚠️ Configuration temporaire :** Perdue au reboot !

---

## �️ Outils Configuration Réseau

### 🔧 netconfig - Configuration TCP/IP en Mode Texte

**netconfig :** Outil configuration TCP/IP basique en mode texte (certaines distributions).

**Fonctionnalités :**
- ⚙️ Configuration IP statique ou dynamique (DHCP)
- 🛣️ Configuration routeur/gateway
- 🏷️ Configuration hostname
- 🌐 Configuration DNS

```bash
# Lancer netconfig pour interface spécifique
sudo netconfig --device eth0

# Interface interactive démarre
# Permet de configurer :
# - IP statique ou DHCP
# - Masque réseau
# - Gateway
# - DNS primaire/secondaire
# - Nom d'hôte
```

**Alternative graphique :**

```bash
# system-config-network (interface graphique)
sudo system-config-network

# Nécessite X11 ou interface graphique
# Plus convivial pour utilisateurs non-experts
```

**💡 Utilité :** Configuration rapide sans éditer manuellement fichiers config.

**⚠️ Note :** Disponibilité varie selon distribution (surtout anciennes versions Red Hat/Fedora).

---

## �🔄 Commandes ifup / ifdown

**Utilise configuration permanente** (fichiers config).

```bash
# Activer interface avec config permanente
sudo ifup eth0

# Désactiver interface
sudo ifdown eth0

# Redémarrer interface (reload config)
sudo ifdown eth0 && sudo ifup eth0

# Toutes les interfaces
sudo ifup -a

# ⚠️ Attention :
# ifup/ifdown lisent config dans :
# - /etc/network/interfaces (Debian/Ubuntu)
# - /etc/sysconfig/network-scripts/ (RHEL/CentOS)
```

**Différence ip link vs ifup/ifdown :**

| Commande | Source config |
|----------|---------------|
| `ip link set eth0 up` | Aucune (juste activer carte) |
| `ifup eth0` | Lit `/etc/network/interfaces` ou `/etc/sysconfig/...` |

---

## 📄 Configuration Permanente - Fichiers

### 🔴 RHEL/CentOS/Fedora (RPM-based)

**Architecture :** 1 fichier par interface + 1 fichier global.

#### 📝 /etc/sysconfig/network-scripts/ifcfg-\<interface>

```bash
# Fichier : /etc/sysconfig/network-scripts/ifcfg-eth0

# Configuration STATIQUE
DEVICE=eth0
TYPE=Ethernet
ONBOOT=yes
BOOTPROTO=none
IPADDR=192.168.1.10
NETMASK=255.255.255.0
GATEWAY=192.168.1.254
DNS1=8.8.8.8
DNS2=8.8.4.4

# Ou avec PREFIX (CIDR)
# PREFIX=24
```

**Configuration DHCP :**

```bash
# Fichier : /etc/sysconfig/network-scripts/ifcfg-eth0

DEVICE=eth0
TYPE=Ethernet
ONBOOT=yes
BOOTPROTO=dhcp
```

**Directives importantes :**

| Directive | Description | Valeurs |
|-----------|-------------|---------|
| **DEVICE** | Nom interface | `eth0`, `enp3s0` |
| **TYPE** | Type connexion | `Ethernet`, `Wireless` |
| **ONBOOT** | Activer au boot | `yes`, `no` |
| **BOOTPROTO** | Protocole config | `none` (static), `dhcp`, `bootp` |
| **IPADDR** | Adresse IP | `192.168.1.10` |
| **NETMASK** | Masque réseau | `255.255.255.0` |
| **PREFIX** | CIDR (alternative NETMASK) | `24` |
| **NETWORK** | Adresse réseau (optionnel) | `192.168.1.0` |
| **BROADCAST** | Adresse broadcast (optionnel) | `192.168.1.255` |
| **GATEWAY** | Passerelle (deprecated ici) | `192.168.1.254` |
| **DNS1, DNS2** | Serveurs DNS | `8.8.8.8`, `8.8.4.4` |
| **HWADDR** | MAC address (optionnel) | `00:11:22:33:44:55` |
| **USERCTL** | User peut contrôler | `yes`, `no` |

**💡 Note importante :**
- **NETWORK** et **BROADCAST** sont **optionnels** si **IPADDR** et **NETMASK** sont présents
- Le système calcule automatiquement ces valeurs à partir de IPADDR/NETMASK

#### 📝 /etc/sysconfig/network

**Configuration globale réseau.**

```bash
# Fichier : /etc/sysconfig/network

NETWORKING=yes
HOSTNAME=server-prod-01
GATEWAY=192.168.1.1

# RHEL 7+ (systemd) : HOSTNAME ici ignoré
# Utiliser hostnamectl à la place
```

**Directives :**

| Directive | Description | Exemple |
|-----------|-------------|---------|\n| **NETWORKING** | Activer réseau | `yes`, `no` |
| **HOSTNAME** | FQDN (RHEL 6 uniquement, ignoré RHEL 7+) | `server-01.exemple.com` |
| **GATEWAY** | Passerelle par défaut (adresse IP) | `192.168.1.254` |
| **GATEWAYDEV** | Interface pour accéder à gateway | `eth0`, `enp3s0` |
| **NISDOMAIN** | Domaine NIS (si applicable) | `exemple.nis` |
| **NETWORKING_IPV6** | Activer support IPv6 | `yes`, `no` |

**Exemple complet /etc/sysconfig/network :**

```bash
NETWORKING=yes
HOSTNAME=server-prod-01.exemple.com
GATEWAY=192.168.1.1
GATEWAYDEV=eth0
NETWORKING_IPV6=yes
```

**💡 Notes :**
- **HOSTNAME** : Définit FQDN (Fully Qualified Domain Name), pas juste nom court
- **GATEWAYDEV** : Utile si plusieurs interfaces, spécifie interface routage default
- **RHEL 7+/systemd** : Préférer `hostnamectl` pour hostname (HOSTNAME ignoré)

#### 🔄 Appliquer Configuration

```bash
# RHEL 6
sudo service network restart

# RHEL 7+
sudo systemctl restart network

# Ou interface spécifique
sudo ifdown eth0 && sudo ifup eth0

# NetworkManager (si utilisé)
sudo systemctl restart NetworkManager
```

---

### 🔵 Debian/Ubuntu (DEB-based)

**Architecture :** 1 fichier unique pour toutes les interfaces.

#### 📝 /etc/network/interfaces

```bash
# Fichier : /etc/network/interfaces

# Loopback (obligatoire)
auto lo
iface lo inet loopback

# eth0 - Configuration STATIQUE
auto eth0
iface eth0 inet static
    address 192.168.1.10
    netmask 255.255.255.0
    gateway 192.168.1.254
    dns-nameservers 8.8.8.8 8.8.4.4

# Ou notation CIDR (compact)
auto eth0
iface eth0 inet static
    address 192.168.1.10/24
    gateway 192.168.1.254
    dns-nameservers 8.8.8.8 8.8.4.4
```

**Configuration DHCP :**

```bash
# eth0 - Configuration DHCP
auto eth0
iface eth0 inet dhcp
```

**Configuration avancée :**

```bash
# eth1 - Plusieurs IPs
auto eth1
iface eth1 inet static
    address 10.0.0.10/24
    gateway 10.0.0.1

# IP secondaire (alias)
iface eth1 inet static
    address 10.0.1.10/24

# WiFi avec WPA
auto wlan0
iface wlan0 inet dhcp
    wpa-ssid "MonWiFi"
    wpa-psk "MotDePasse123"

# Routes statiques
auto eth2
iface eth2 inet static
    address 172.16.0.10/16
    up ip route add 10.0.0.0/8 via 172.16.0.1
    down ip route del 10.0.0.0/8
```

**Directives importantes :**

| Directive | Description | Exemple |
|-----------|-------------|---------|
| **auto** | Activer interface au boot | `auto eth0` |
| **allow-hotplug** | Activer si détectée | `allow-hotplug eth0` |
| **iface** | Définir interface | `iface eth0 inet static` |
| **inet** | IPv4 | `inet static`, `inet dhcp` |
| **inet6** | IPv6 | `inet6 static`, `inet6 auto` |
| **address** | IP | `192.168.1.10` ou `192.168.1.10/24` |
| **netmask** | Masque | `255.255.255.0` |
| **gateway** | Passerelle | `192.168.1.254` |
| **dns-nameservers** | DNS | `8.8.8.8 8.8.4.4` |
| **dns-search** | Domaine recherche | `example.com` |
| **up** | Commande après activation | `up ip route add ...` |
| **down** | Commande avant désactivation | `down ip route del ...` |

#### 🔄 Appliquer Configuration

```bash
# Redémarrer service networking
sudo systemctl restart networking

# Ou (ancien)
sudo service networking restart

# Interface spécifique
sudo ifdown eth0 && sudo ifup eth0

# Recharger config sans restart
sudo systemctl reload networking

# NetworkManager (si installé)
sudo systemctl restart NetworkManager
```

---

## 🔷 NetworkManager

**NetworkManager :** Daemon gestion réseau moderne (GUI, CLI, TUI).

**Rôle :**
- 🔄 Configuration et administration dynamique du réseau
- 🎛️ Gestion centralisée via `nmcli`, `nmtui`
- 🌐 Détection automatique connexions

**Avantages :**
- ✅ Gestion WiFi facile
- ✅ Détection automatique
- ✅ Profils multiples
- ✅ VPN intégré
- ✅ Interface graphique

```bash
# Service
systemctl status NetworkManager

# Activer
sudo systemctl enable --now NetworkManager
```

### 🔧 Désactiver NetworkManager (Serveurs)

**Contexte :** Sur serveurs production, préférer configuration **statique classique** pour stabilité.

**Procédure complète (Red Hat/CentOS) :**

```bash
# 1. Arrêter NetworkManager
sudo systemctl stop NetworkManager

# 2. Masquer service (empêche démarrage accidentel)
sudo systemctl mask NetworkManager

# 3. Désactiver démarrage automatique
sudo systemctl disable NetworkManager

# 4. Activer service réseau classique
sudo systemctl enable network

# 5. Démasquer service réseau
sudo systemctl unmask network

# 6. Démarrer service réseau
sudo systemctl start network

# Vérifier état
systemctl status network
systemctl status NetworkManager  # Devrait montrer: inactive (dead), masked
```

**Procédure Debian/Ubuntu :**

```bash
# Remplacer 'network' par 'networking'
sudo systemctl stop NetworkManager
sudo systemctl mask NetworkManager
sudo systemctl disable NetworkManager
sudo systemctl enable networking
sudo systemctl unmask networking
sudo systemctl start networking
```

**💡 Moment approprié :**
- ✅ Serveurs avec IP statique
- ✅ Environnements nécessitant contrôle manuel total
- ❌ Workstations (meilleure expérience avec NetworkManager)

### 💻 nmcli - Interface Ligne de Commande

**Commande non-interactive** pour contrôle NetworkManager.

```bash
# Aide
nmcli --help

# Sous-commandes principales
nmcli general        # État général NetworkManager
nmcli networking     # Contrôle réseau global
nmcli radio          # WiFi, WWAN on/off
nmcli connection     # Gestion connexions (profils)
nmcli device         # Gestion périphériques
nmcli agent          # Agent secrets
nmcli monitor        # Surveiller événements
```

**Exemples pratiques :**

```bash
# État général
nmcli general status
# STATE      CONNECTIVITY  WIFI-HW  WIFI     WWAN-HW  WWAN
# connected  full          enabled  enabled  enabled  enabled

# Lister connexions
nmcli connection show
# NAME    UUID                                  TYPE      DEVICE
# eth0    abc-123-def                           ethernet  eth0
# WiFi1   xyz-789-ghi                           wifi      wlan0

# Lister périphériques
nmcli device status
# DEVICE  TYPE      STATE      CONNECTION
# eth0    ethernet  connected  eth0
# wlan0   wifi      disconnected  --
# lo      loopback  unmanaged  --

# Activer connexion
nmcli connection up eth0

# Désactiver connexion
nmcli connection down eth0
```

**Créer connexions Ethernet :**

```bash
# Mode DHCP (simple)
nmcli con add type ethernet con-name "Reseau virtuel" ifname enp0s3

# Mode STATIQUE (IP fixe)
nmcli con add type ethernet \
    con-name "Reseau virtuel" \
    ifname enp0s3 \
    ip4 10.0.2.15/24 \
    gw4 10.0.2.2

# Configuration DNS pour connexion
nmcli con mod "Reseau virtuel" ipv4.dns "8.8.8.8 8.8.4.4"

# Activer connexion créée
nmcli con up "Reseau virtuel"
```

**Ajouter routes personnalisées :**

```bash
# Ajouter route vers réseau spécifique via gateway
nmcli connection modify enp0s3 +ipv4.routes "192.168.122.0/24 10.10.10.1"

# Activer changements
nmcli con up enp0s3

# Vérifier routes
ip route show
```

**Autres opérations courantes :**

```bash
# Ajouter connexion statique (syntaxe longue)
nmcli connection add \
    con-name eth0-static \
    type ethernet \
    ifname eth0 \
    ipv4.addresses 192.168.1.10/24 \
    ipv4.gateway 192.168.1.254 \
    ipv4.dns "8.8.8.8 8.8.4.4" \
    ipv4.method manual

# Ajouter connexion DHCP
nmcli connection add \
    con-name eth0-dhcp \
    type ethernet \
    ifname eth0 \
    ipv4.method auto

# Modifier connexion existante
nmcli connection modify eth0 ipv4.addresses 192.168.1.20/24

# Supprimer connexion
nmcli connection delete eth0-static

# WiFi - Scanner
nmcli device wifi list

# WiFi - Connecter
nmcli device wifi connect "SSID_Name" password "MotDePasse"

# Contrôle WiFi
nmcli radio wifi on
nmcli radio wifi off

# Contrôle réseau global
nmcli networking on
nmcli networking off
```

### 🖥️ nmtui - Interface Texte Interactive

**TUI (Text User Interface) ncurses.**

```bash
# Lancer interface
nmtui

# Ou directement action
nmtui edit          # Éditer connexion
nmtui connect       # Activer connexion
nmtui hostname      # Définir hostname
```

**Navigation :**
- Flèches : Navigation
- Tab : Champ suivant
- Espace : Sélection
- Entrée : OK/Activer

```
┌────────────────────────────────────────┐
│        NetworkManager TUI              │
│                                        │
│  ○ Edit a connection                   │
│  ○ Activate a connection               │
│  ○ Set system hostname                 │
│                                        │
│                                   OK   │
│                                  Quit  │
└────────────────────────────────────────┘
```

---

# 🌐 PARTIE 3 : HOSTNAME, HOSTS & DNS

## 🏷️ Configuration Hostname

### 📛 Commande hostname (Temporaire)

```bash
# Voir hostname actuel
hostname
# server-prod-01

# Voir FQDN (si configuré)
hostname -f
hostname --fqdn
# server-prod-01.example.com

# Voir domaine
hostname -d
hostname --domain
# example.com

# Voir IP
hostname -I
hostname --all-ip-addresses
# 192.168.1.10 10.0.0.5

# Changer hostname (temporaire, perdu au reboot)
sudo hostname new-server-name

# Vérifier changement
hostname
# new-server-name
```

### 📄 Fichier /etc/hostname

**Hostname persistant (classique).**

```bash
# Voir contenu
cat /etc/hostname
# server-prod-01

# Modifier (Debian/Ubuntu)
echo "new-hostname" | sudo tee /etc/hostname

# Appliquer sans reboot
sudo hostname -F /etc/hostname
# ou
sudo hostname $(cat /etc/hostname)

# Redémarrer service (systemd)
sudo systemctl restart systemd-hostnamed
```

### ⚙️ hostnamectl (systemd - Recommandé)

**Commande moderne** pour gérer hostname (systemd).

```bash
# Afficher infos hostname
hostnamectl

# Output :
#    Static hostname: server-prod-01
#          Icon name: computer-vm
#            Chassis: vm
#         Machine ID: 1234abcd5678ef...
#            Boot ID: 9876fedc5432ba...
#     Virtualization: kvm
#   Operating System: Ubuntu 22.04 LTS
#             Kernel: Linux 5.15.0-56-generic
#       Architecture: x86-64

# Changer hostname (permanent)
sudo hostnamectl set-hostname new-server-name

# Vérifier
hostnamectl
#    Static hostname: new-server-name

hostname
# new-server-name

# Hostname "pretty" (espaces, caractères spéciaux OK)
sudo hostnamectl set-hostname "Production Web Server 01" --pretty

# Hostname "transient" (temporaire DHCP, réseau...)
sudo hostnamectl set-hostname temp-name --transient

# Voir seulement static hostname
hostnamectl --static

# Voir seulement pretty hostname
hostnamectl --pretty
```

**Types hostname :**

| Type | Option | Description | Exemple |
|------|--------|-------------|---------|
| **Static** | (défaut) | Permanent, stocké `/etc/hostname` | `server-01` |
| **Pretty** | `--pretty` | Descriptif, peut contenir espaces | `"Production Server #1"` |
| **Transient** | `--transient` | Temporaire (DHCP, réseau) | `dhcp-192-168-1-10` |

---

## 📇 /etc/hosts - Résolution Locale

**LMHOSTS** : Local resolution (avant DNS).

### 🎯 Format

```
IP_ADDRESS    hostname    [alias1] [alias2] ...
```

### 📝 Exemple /etc/hosts

```bash
# /etc/hosts

# Loopback
127.0.0.1       localhost
127.0.1.1       mycomputer.localdomain  mycomputer
::1             localhost ip6-localhost ip6-loopback

# Hosts locaux
192.168.1.10    server-web      web www
192.168.1.20    server-db       db database mysql-server
192.168.1.30    server-mail     mail smtp imap

# Machines réseau local
192.168.1.100   desktop-alice
192.168.1.101   desktop-bob
192.168.1.102   laptop-charlie

# Bloquer domaines (méthode simple)
0.0.0.0         ads.example.com
0.0.0.0         malicious-site.net

# IPv6
fe80::1         gateway-ipv6
2001:db8::10    server-ipv6
```

### 🔍 Utilisation

```bash
# Ping via hostname
ping server-web
# PING server-web (192.168.1.10)

ping db
# PING db (192.168.1.20)

# SSH
ssh alice@desktop-alice

# HTTP
curl http://web/index.html

# Priorité /etc/hosts sur DNS
# → Rapide, pas de requête réseau
```

### ⚠️ Ordre Résolution

**Défini dans `/etc/nsswitch.conf`** (voir plus bas).

**Classique :** `files` (hosts) → `dns` → `mdns`

---

## 🌐 /etc/resolv.conf - Configuration DNS

**Serveurs DNS** et options recherche.

### 📝 Format

```bash
# /etc/resolv.conf

# Serveurs DNS (ordre priorité)
nameserver 8.8.8.8
nameserver 8.8.4.4
nameserver 1.1.1.1

# Domaine de recherche (FQDN auto)
search example.com internal.local

# Domaine local (ancien, remplacé par search)
domain example.com

# Options
options timeout:2 attempts:3 rotate
```

### 📋 Directives

| Directive | Description | Exemple |
|-----------|-------------|---------|
| **nameserver** | Serveur DNS (max 3) | `nameserver 8.8.8.8` |
| **search** | Liste domaines recherche | `search example.com local` |
| **domain** | Domaine local (obsolète) | `domain example.com` |
| **options** | Options diverses | `options timeout:2` |

### 🔍 Directive search

**Auto-complétion FQDN.**

```bash
# /etc/resolv.conf
search example.com internal.local

# Requête : ping web
# Essaie dans l'ordre :
# 1. web.example.com
# 2. web.internal.local
# 3. web (si échecs précédents)

# Requête : ssh server-db
# Essaie :
# 1. server-db.example.com
# 2. server-db.internal.local
# 3. server-db
```

### 🔧 Options Courantes

```bash
# /etc/resolv.conf

# Timeout requête DNS (secondes, défaut 5s)
options timeout:2

# Nombre tentatives (défaut 2)
options attempts:3

# Rotation nameservers (load balancing)
options rotate

# Single-request (éviter problèmes IPv4/IPv6)
options single-request

# Tout combiné
options timeout:2 attempts:3 rotate single-request
```

### ⚠️ NetworkManager et resolv.conf

**NetworkManager peut générer `/etc/resolv.conf` automatiquement.**

```bash
# Voir si lien symbolique
ls -la /etc/resolv.conf
# lrwxrwxrwx /etc/resolv.conf -> ../run/NetworkManager/resolv.conf

# Géré par NetworkManager (dynamique)
# → Modifications manuelles écrasées

# Désactiver gestion NetworkManager
sudo vim /etc/NetworkManager/NetworkManager.conf

[main]
dns=none

sudo systemctl restart NetworkManager

# Puis gérer manuellement
sudo vim /etc/resolv.conf
```

**systemd-resolved :**

```bash
# Lien systemd-resolved
ls -la /etc/resolv.conf
# lrwxrwxrwx /etc/resolv.conf -> ../run/systemd/resolve/stub-resolv.conf

# Configurer via resolved
sudo vim /etc/systemd/resolved.conf

[Resolve]
DNS=8.8.8.8 8.8.4.4
FallbackDNS=1.1.1.1
Domains=example.com

sudo systemctl restart systemd-resolved
```

---

## 🔀 /etc/nsswitch.conf - Name Service Switch

**Ordre résolution** pour divers services (hosts, passwd, group...).

### 🎯 Rôle

Définit **sources** et **ordre** pour résolution noms.

### 📝 Format

```
database:    source1 source2 source3 ...
```

### 📋 Exemple /etc/nsswitch.conf

```bash
# /etc/nsswitch.conf

# Utilisateurs / Groupes
passwd:         files systemd
group:          files systemd
shadow:         files

# Hostnames (IMPORTANT)
hosts:          files mdns4_minimal [NOTFOUND=return] dns mdns4

# Réseaux
networks:       files

# Protocoles / Services
protocols:      db files
services:       db files

# RPC
rpc:            db files

# Ethers (MAC → IP)
ethers:         db files

# Netgroup
netgroup:       nis
```

### 🔍 Ligne hosts (Résolution Noms)

**Format :**
```
hosts:    source1 source2 source3 ...
```

**Sources courantes :**

| Source | Description |
|--------|-------------|
| **files** | `/etc/hosts` |
| **dns** | Requête DNS (serveurs `/etc/resolv.conf`) |
| **mdns4** / **mdns6** | Multicast DNS (Avahi, .local) |
| **mdns4_minimal** | mDNS seulement pour `.local` |
| **wins** | Windows Internet Name Service |
| **nis** | Network Information Service (obsolète) |

**Exemples :**

```bash
# Ordre classique
hosts:    files dns

# 1. Cherche dans /etc/hosts
# 2. Si pas trouvé → DNS (/etc/resolv.conf)

# Avec mDNS (Avahi, Zeroconf)
hosts:    files mdns4_minimal [NOTFOUND=return] dns mdns4

# 1. /etc/hosts
# 2. mDNS pour noms .local uniquement
# 3. Si .local pas trouvé → STOP (pas DNS)
# 4. Sinon → DNS
# 5. mDNS général (fallback)

# DNS seulement (ignorer /etc/hosts)
hosts:    dns

# Files seulement (pas DNS)
hosts:    files
```

**Actions conditionnelles :**

```bash
# [NOTFOUND=return] : Arrêter si NOTFOUND
hosts:    files [NOTFOUND=return] dns

# Si /etc/hosts dit "NOTFOUND" → stop, pas DNS
# Si /etc/hosts ne contient pas entrée → continue DNS

# [SUCCESS=return] : Arrêter si trouvé
hosts:    files [SUCCESS=return] dns

# Si trouvé dans files → stop
# Sinon → DNS
```

### 🔧 Test Résolution

```bash
# Résoudre nom via nsswitch
getent hosts server-web
# 192.168.1.10    server-web web www

# Test différentes sources
getent ahosts www.google.com

# Forcer DNS (ignore /etc/hosts)
dig www.google.com
nslookup www.google.com

# Tester /etc/hosts
grep google /etc/hosts
# 127.0.0.1   www.google.com  (bloqué!)

ping www.google.com
# PING www.google.com (127.0.0.1)  ← utilise /etc/hosts
```

---

## 🎓 Scénarios Pratiques

### Scénario 1 : Configurer IP Statique (Debian)

```bash
# 1. Éditer /etc/network/interfaces
sudo vim /etc/network/interfaces

auto eth0
iface eth0 inet static
    address 192.168.1.50/24
    gateway 192.168.1.1
    dns-nameservers 8.8.8.8 8.8.4.4

# 2. Redémarrer interface
sudo ifdown eth0 && sudo ifup eth0

# 3. Vérifier
ip addr show eth0
ip route show
cat /etc/resolv.conf

# 4. Tester connectivité
ping 192.168.1.1     # Gateway
ping 8.8.8.8         # DNS
ping google.com      # Internet
```

### Scénario 2 : Configurer IP Statique (RHEL)

```bash
# 1. Créer/éditer fichier interface
sudo vim /etc/sysconfig/network-scripts/ifcfg-eth0

DEVICE=eth0
TYPE=Ethernet
ONBOOT=yes
BOOTPROTO=none
IPADDR=192.168.1.50
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=8.8.8.8
DNS2=8.8.4.4

# 2. Redémarrer réseau
sudo systemctl restart network

# 3. Vérifier
ip addr show eth0
ip route
```

### Scénario 3 : Ajouter IP Secondaire

```bash
# Temporaire
sudo ip addr add 10.0.0.50/24 dev eth0

# Permanent (Debian /etc/network/interfaces)
auto eth0
iface eth0 inet static
    address 192.168.1.50/24
    gateway 192.168.1.1

iface eth0 inet static
    address 10.0.0.50/24

# Permanent (RHEL - alias)
# Créer ifcfg-eth0:0
sudo vim /etc/sysconfig/network-scripts/ifcfg-eth0:0

DEVICE=eth0:0
ONBOOT=yes
IPADDR=10.0.0.50
NETMASK=255.255.255.0
```

### Scénario 4 : Bloquer Domaine via /etc/hosts

```bash
# Bloquer publicités, sites malveillants
sudo vim /etc/hosts

# Rediriger vers localhost (bloqué)
0.0.0.0         ads.example.com
0.0.0.0         tracker.analytics.net
0.0.0.0         malware-site.org

# Tester
ping ads.example.com
# PING ads.example.com (0.0.0.0)  ← Bloqué
```

### Scénario 5 : Changer Hostname Permanent

```bash
# Méthode moderne (systemd)
sudo hostnamectl set-hostname web-server-prod

# Vérifier
hostnamectl
hostname

# Mettre à jour /etc/hosts
sudo vim /etc/hosts

127.0.0.1       localhost
127.0.1.1       web-server-prod

# Vérifier FQDN
hostname -f
```

---

## 📝 Cheat Sheet

### Interfaces Réseau

```bash
# Lister interfaces
ip link show            # Moderne
ifconfig -a             # Ancien

# Voir IPs
ip addr show            # Moderne
ifconfig                # Ancien

# Stats
ip -s link              # Moderne
ifconfig -s             # Ancien
```

### Configuration Temporaire

```bash
# Ajouter IP
ip addr add 192.168.1.10/24 dev eth0
ifconfig eth0 192.168.1.10 netmask 255.255.255.0

# Supprimer IP
ip addr del 192.168.1.10/24 dev eth0

# Up/Down
ip link set eth0 up/down
ifconfig eth0 up/down
ifup/ifdown eth0        # Utilise config permanente

# Route
ip route add default via 192.168.1.254
```

### Configuration Permanente

```bash
# RHEL/CentOS
/etc/sysconfig/network-scripts/ifcfg-eth0
/etc/sysconfig/network

# Debian/Ubuntu
/etc/network/interfaces

# Redémarrer
systemctl restart networking          # Debian
systemctl restart network             # RHEL
```

### NetworkManager

```bash
# CLI
nmcli device status
nmcli connection show
nmcli connection up/down eth0

# TUI
nmtui
```

### Hostname

```bash
# Voir
hostname
hostname -f
hostnamectl

# Changer (permanent)
hostnamectl set-hostname new-name

# Fichier
/etc/hostname
```

### DNS & Résolution

```bash
# Fichiers
/etc/hosts              # Résolution locale
/etc/resolv.conf        # DNS
/etc/nsswitch.conf      # Ordre résolution

# Tester
getent hosts hostname
ping hostname
dig hostname
```

---

## 🎯 Points Clés pour l'Examen LPIC-1

### Connaissances Essentielles

✅ **Nomenclature interfaces :**
- Ancien : eth0, eth1, wlan0
- Moderne : eno1, ens1, enp3s0, wlp2s0
- Loopback : lo (toujours)

✅ **Commandes visualisation :**
- Ancien : `ifconfig -a`
- Moderne : `ip link show`, `ip addr show`

✅ **Configuration temporaire :**
- `ip addr add/del IP/CIDR dev interface`
- `ip link set interface up/down`
- `ip route add default via GATEWAY`
- `ifup/ifdown` utilise config permanente

✅ **Configuration permanente :**
- **RHEL** : `/etc/sysconfig/network-scripts/ifcfg-*`, `/etc/sysconfig/network`
- **Debian** : `/etc/network/interfaces`
- Directives : DEVICE, ONBOOT, BOOTPROTO, IPADDR, NETMASK, GATEWAY, DNS1

✅ **NetworkManager :**
- CLI : `nmcli` (commandes connection, device, general...)
- TUI : `nmtui`

✅ **Hostname :**
- Temporaire : `hostname`
- Permanent : `hostnamectl set-hostname`, `/etc/hostname`
- Options : `--pretty`, `--transient`

✅ **Résolution noms :**
- `/etc/hosts` : résolution locale (LMHOSTS)
- `/etc/resolv.conf` : DNS (nameserver, search, domain)
- `/etc/nsswitch.conf` : ordre résolution (files dns mdns)

### Pièges Fréquents

❌ Confondre configuration temporaire (perdue reboot) vs permanente  
❌ Oublier que `ifup/ifdown` lit fichiers config (pas juste up/down)  
❌ Éditer `/etc/resolv.conf` alors que NetworkManager le régénère  
❌ Mauvais format CIDR dans fichiers Debian (`address 192.168.1.10/24`)  
❌ Oublier `auto eth0` dans `/etc/network/interfaces` → pas activée au boot  
❌ Confondre `domain` (obsolète) et `search` (actuel) dans resolv.conf  
❌ Ignorer ordre résolution dans `/etc/nsswitch.conf` (files avant dns)  
❌ Penser que modifier hostname via `hostname` est permanent

### Exemples Examen

**Q : Commande moderne lister interfaces réseau ?**
```bash
ip link show
# ou
ip addr show
```

**Q : Ajouter IP 10.0.0.5/24 sur eth0 temporairement ?**
```bash
sudo ip addr add 10.0.0.5/24 dev eth0
```

**Q : Fichier config réseau Debian ?**
```
/etc/network/interfaces
```

**Q : Fichier config interface eth0 RHEL ?**
```
/etc/sysconfig/network-scripts/ifcfg-eth0
```

**Q : Changer hostname de façon permanente (systemd) ?**
```bash
sudo hostnamectl set-hostname new-name
```

**Q : Quel fichier définit ordre résolution noms (DNS, hosts...) ?**
```
/etc/nsswitch.conf
```

**Q : Directive /etc/resolv.conf pour domaines recherche ?**
```
search example.com internal.local
```

---

**Formation LPIC-1 102 - Préparation Certification Linux**  
**Cours 109.2 - Configuration Réseau Persistante**  
**Maxime Chenaud - 12 Février 2026**
