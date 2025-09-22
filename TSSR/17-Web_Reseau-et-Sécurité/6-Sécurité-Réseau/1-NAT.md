# **Définition et objectifs**

Le **NAT** (Translation d’adresses réseau) est une technique qui permet de convertir des **adresses IP** en d'autres adresses IP.



## 📍 **Caractéristiques principales** :

- Agit sur la **couche Réseau** (couche 3 du modèle OSI)
- Permet de **masquer les adresses IP privées** pour des raisons de sécurité
- Résout la **pénurie d'adresses IPv4 publiques**
- Compatible avec **IPv4** et **IPv6**



## ✅ **Avantages du NAT**

- 🔐 **Sécurité** : les IP internes sont invisibles depuis Internet
- 🌐 **Économie d’IP publiques** : plusieurs appareils partagent une seule IP publique
- 🔄 **Routage simplifié** : permet l’interconnexion de réseaux ayant des plans d’adressage différents



## 🛡️ **Types de NAT sur pfSense**

### 🔁 **NAT Sortant**

- Utilisé lorsque les **clients internes accèdent à Internet**
- Masque les IP privées avec une **IP publique unique**
- Peut être manuel ou automatique dans pfSense

### 📨 **Transfert de port (Port Forwarding)**

- Redirige un **port spécifique** sur l’IP publique vers un **serveur interne**
- Permet d’**exposer un service interne** (ex : HTTP, SSH) de manière contrôlée

### 🔄 **1:1 NAT**

- Associe une **adresse IP publique** à une **adresse IP interne**
- Redirige **tout le trafic** (tous les ports) vers une seule machine interne
- Diffère du Port Forwarding qui est ciblé **port par port**
