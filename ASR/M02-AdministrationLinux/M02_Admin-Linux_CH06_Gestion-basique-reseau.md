# M02 - Administration Linux

## CH06 - Gestion basique du réseau

La mise en réseau d'un système Linux repose sur une configuration IP correcte : adresse IP, masque réseau, passerelle par défaut, et serveur(s) DNS.

---

### 6.1 - Prise d'information en ligne de commande

#### 🔍 Connaître l'adresse IP

La commande `ip` (paquet `iproute2`) est l'outil moderne. `ifconfig` est obsolète mais encore présent sur certains systèmes.

```bash
# ip addr show (abrégé : ip a)
$ ip a
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 ...
    inet 10.6.6.6/16 brd 10.9.255.255 scope global dynamic ens33
    inet6 fe80::20c:29ff:fe3b:f4b/64 scope link
```

💡 Les interfaces réseau sur RHEL/Oracle Linux suivent la convention de nommage **Predictable Network Interface Names** : `ens33`, `enp0s3`, `eno1`, etc. (plus de `eth0`).

#### 🔍 Connaître la route par défaut

```bash
$ ip r    # ou ip route show
default via 10.6.255.254 dev ens33 proto static metric 100
10.6.0.0/16 dev ens33 proto kernel scope link src 10.9.50.16 metric 100
```

#### 🔍 Connaître les serveurs DNS

```bash
$ cat /etc/resolv.conf
search eni.fr
nameserver 10.6.0.66
```

📌 Ce fichier peut être géré manuellement ou automatiquement par **NetworkManager**. Sur les systèmes modernes, c'est souvent NetworkManager qui le génère.

---

### 6.2 - Commandes réseau essentielles

Résumé des commandes les plus utilisées pour le diagnostic et la configuration :

| Commande | Usage |
| --- | --- |
| `ip a` | Voir les adresses IP de toutes les interfaces |
| `ip r` | Voir la table de routage |
| `ip link show` | État des interfaces (up/down) |
| `ip addr add 10.0.0.1/24 dev ens33` | Ajouter une IP (temporaire) |
| `ip route add default via 10.0.0.254` | Ajouter une route par défaut (temporaire) |
| `ping -c 4 <ip>` | Test de connectivité ICMP |
| `ss -tulpn` | Ports en écoute (remplace `netstat`) |
| `dig <hostname>` | Résolution DNS détaillée |
| `host <hostname>` | Résolution DNS simple |

⚠️ Les commandes `ip addr add` et `ip route add` sont **non persistantes** : elles disparaissent au reboot. Pour persister, il faut passer par NetworkManager.

---

### 6.3 - Configuration réseau en CLI (sans interface graphique)

C'est le service **NetworkManager** qui gère le réseau sur RHEL/Oracle Linux. Deux outils CLI pour le piloter :

#### 🔧 `nmtui` (Text User Interface)

Interface texte interactive, pratique pour une config rapide sur un serveur sans GUI.

#### 🔧 `nmcli` (Command Line Interface)

Outil en ligne de commande complet :

```bash
# Voir l'état des interfaces
$ nmcli device status

# Voir les connexions configurées
$ nmcli connection show

# Créer une connexion statique
# nmcli con add type ethernet ifname ens33 con-name static \
    ip4 10.y.x.1/16 gw4 10.y.255.254
# nmcli con mod static ipv4.dns "10.6.0.66"

# Activer / désactiver une connexion
# nmcli con up static
# nmcli con down static

# Modifier une connexion existante
# nmcli con mod static ipv4.addresses 10.y.x.1/16
# nmcli con mod static ipv4.gateway 10.y.255.254
```

📌 Après modification d'une connexion avec `nmcli con mod`, il faut la réactiver (`nmcli con up <nom>`) pour appliquer les changements.

---

### 6.4 - Configuration d'un système graphique

Sur un système avec GUI, la configuration réseau se fait via **Activités > Réseau** (GNOME).

Après modification, on peut relancer le service :

```bash
# systemctl restart NetworkManager
```

---

### 6.5 - Fichiers de configuration réseau

| Fichier / Répertoire | Rôle |
| --- | --- |
| `/etc/resolv.conf` | Serveurs DNS et domaine de recherche |
| `/etc/hosts` | Résolution locale (prioritaire sur DNS) |
| `/etc/nsswitch.conf` | Ordre de résolution (`files dns`) |
| `/etc/hostname` | Nom d'hôte du système |
| `/etc/sysconfig/network-scripts/ifcfg-*` | Config interfaces (RHEL legacy, avant NM) |

🔧 Pour modifier le hostname de manière persistante :

```bash
# hostnamectl set-hostname srv-gui
```

---

### 🧪 Atelier 5 - Gestion réseau

Configuration des VMs en adressage IPv4 statique sur le réseau `10.y.x.0/16` :

| VM | Adresse IP |
| --- | --- |
| `srv-gui` | `10.y.x.1` |
| `srv-cli` | `10.y.x.11` |

- Passerelle et DNS : identiques à ceux du poste physique
- `srv-gui` : config via l'outil graphique
- Test de connectivité entre les deux VMs (`ping`)
- Test de résolution DNS (`host www.github.com` ou `dig www.github.com`)

---

### 📌 À retenir

1. **`ip a`** pour les adresses, **`ip r`** pour les routes, **`cat /etc/resolv.conf`** pour les DNS.
2. `ifconfig` et `route` sont **obsolètes**, utiliser les commandes `ip` et `ss`.
3. **NetworkManager** gère le réseau sur RHEL/Oracle Linux. `nmcli` et `nmtui` sont les outils CLI pour le configurer.
4. Les modifications faites avec `ip addr add` / `ip route add` sont **temporaires**. Pour persister : `nmcli`.
5. `/etc/hosts` permet la résolution locale et est prioritaire sur DNS (selon `/etc/nsswitch.conf`).
