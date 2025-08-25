# 🌐 Note – Utilisation de **nmcli** pour configurer une interface réseau

---

## 📖 Introduction
`nmcli` est un outil en ligne de commande permettant de gérer **NetworkManager** sous Linux.  
Il permet de **créer, modifier et activer des connexions réseau** sans passer par l’édition manuelle des fichiers de configuration.  
C’est particulièrement utile pour configurer une **interface réseau filaire avec une adresse IP statique**.

---

## ⚙️ Commande de création d’une connexion Ethernet avec configuration manuelle

```bash
nmcli con add con-name my-con-em1 ifname em1 type ethernet \
  ip4 192.168.100.100/24 gw4 192.168.100.1 ip4 1.2.3.4 ip6 abbe::cafe
```

📌 **Explications :**
- `con-name my-con-em1` ➝ Nom de la connexion (arbitraire, utilisé pour la gestion avec `nmcli`)
- `ifname em1` ➝ Interface physique concernée (`em1`)
- `type ethernet` ➝ Type de connexion (ici Ethernet filaire)
- `ip4 192.168.100.100/24` ➝ Adresse IPv4 statique et son masque
- `gw4 192.168.100.1` ➝ Passerelle IPv4 par défaut
- `ip4 1.2.3.4` ➝ Ajout d’un DNS IPv4
- `ip6 abbe::cafe` ➝ Adresse IPv6 statique

---

## 📑 Ajout / modification des serveurs DNS

```bash
nmcli con mod my-con-em1 ipv4.dns "8.8.8.8 8.8.4.4"  
nmcli con mod my-con-em1 +ipv4.dns 1.2.3.4  
nmcli con mod my-con-em1 ipv6.dns "2001:4860:4860::8888 2001:4860:4860::8844"  
```

📌 **Explications :**
- `ipv4.dns "8.8.8.8 8.8.4.4"` ➝ Définit les DNS IPv4 principaux et secondaires
- `+ipv4.dns 1.2.3.4` ➝ Ajoute un DNS IPv4 supplémentaire
- `ipv6.dns ...` ➝ Définit les DNS IPv6 principaux et secondaires

---

## 🔍 Vérification de la configuration

```bash
nmcli -p con show my-con-em1  
```
📌 Cette commande affiche les **détails complets de la connexion** (adresses, DNS, routes, etc.).

---

## 📂 Fichiers de configuration générés

Les commandes `nmcli` modifient les fichiers gérés par **NetworkManager** :  

- `/etc/NetworkManager/system-connections/my-con-em1.nmconnection`  
  → Contient la configuration complète (IP, DNS, routes, etc.).  
  → Format : INI (lisible et modifiable manuellement si nécessaire).  

⚠️ Les fichiers traditionnels `/etc/sysconfig/network-scripts/ifcfg-*` (CentOS/RHEL) ou `/etc/netplan/*` (Ubuntu) peuvent être utilisés selon la distribution, mais **NetworkManager privilégie désormais le format `.nmconnection`**.
