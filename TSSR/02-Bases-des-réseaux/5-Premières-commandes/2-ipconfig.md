# **⚡ 1. La commande ipconfig (Windows)**

📌 **ipconfig** est une commande Windows permettant d'afficher et de gérer la configuration réseau d'un PC. Elle est essentielle pour **diagnostiquer et résoudre des problèmes de connexion**.

## **🔍 Principales utilisations**

| **🛠️ Commande** | **📖 Description** |
|----|----|
| 🔹 ipconfig | Affiche un résumé de la configuration réseau (IP, passerelle, masque) |
| 🔹 ipconfig /all | Affiche des **détails complets** (MAC, DHCP, DNS, bail IP, etc.) |
| 🔹 ipconfig /release | 🚀 **Libère** l’adresse IP DHCP (utile en cas de conflit IP) |
| 🔹 ipconfig /renew | 🔄 **Demande** une nouvelle adresse IP au serveur DHCP |
| 🔹 ipconfig /flushdns | 🗑️ Vide le **cache DNS** (utile si un site ne se charge pas) |
| 🔹 ipconfig /displaydns | 📜 Affiche le cache DNS actuel |
| 🔹 ipconfig /registerdns | 🔁 Renouvelle les **enregistrements DNS dynamiques** |

## **📌 Cas d’utilisation courants**

✅ **Vérifier la configuration réseau** :

ipconfig /all

✅ **Corriger un problème de connexion** (ex : pas d’IP ou conflit IP) :

ipconfig /release  
ipconfig /renew

✅ **Résoudre un problème DNS** (ex : un site web ne se charge pas) :

ipconfig /flushdns

# **🐧 2. La commande ip (Linux)**

📌 **ip** est une commande avancée sous Linux permettant de gérer **les interfaces réseau, les adresses IP et la table de routage**. Elle **remplace** l’ancienne commande ifconfig.

##  **🔍 Commandes courantes**

| **🛠️ Commande** | **📖 Description** |
|----|----|
| 🔹 ip link show | 📜 Affiche les **interfaces réseau** actives |
| 🔹 ip addr show | 📜 Affiche les **adresses IP** des interfaces |
| 🔹 ip addr add 192.168.1.200/24 dev eth0 | ➕ Ajoute une **adresse IP** à une interface |
| 🔹 ip addr del 192.168.1.200/24 dev eth0 | ❌ Supprime une **adresse IP** |
| 🔹 ip route show | 📜 Affiche la **table de routage** |
| 🔹 ip route add 192.168.2.0/24 via 192.168.1.1 dev eth0 | ➕ Ajoute une **route** |
| 🔹 ip route del 192.168.2.0/24 | ❌ Supprime une **route** |
| 🔹 ip neigh show | 📜 Affiche la **table ARP** (voisins réseau) |
