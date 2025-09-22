# Fonctionnalités de pfSense

## 🧠 **Système de règles pfSense**

- Filtrage détaillé (IP, port, protocole)
- Règles par interface
- Actions multiples : autoriser, bloquer
- Alias et planning intégrés
- Gestion d’état des connexions
- Journalisation possible
- Intégration avec NAT
- Flexibilité totale



## 🔁 **Gestion des flux par interface (pfSense)**

- 🎯 Priorités
- 🎛️ QoS
- 🔄 Gestion de la concurrence
- ✂️ Segmentation
- 🧩 Allocation des ressources



## 🧰 **Services intégrés dans pfSense**

- 🌐 **DNS Dynamique** : MAJ IP dynamiques
- 🚀 **DNS Forwarder** : Redirection DNS externe
- 🕒 **NTP** : Synchronisation horaire
- 🛑 **Portail Captif** : Authentification réseau
- 📺 **Proxy IGMP** : Trafic multicast
- 📦 **Relais DHCP/DHCPv6** : Transmission entre segments
- 🔍 **Résolveur DNS** : DNS local avec cache
- 💾 **Sauvegarde** : Sauvegarde/restauration config
- 📡 **DHCP Serveur** : Attribution IP auto
- 🌐 **RA DHCPv6** : Config IPv6 automatique
- 📊 **SNMP** : Supervision à distance
- 🔁 **UPnP / NAT-PMP** : Redirection auto de ports
- 🌙 **Wake-on-LAN** : Réveil distant des machines
- 🔐 **OpenVPN** : VPN sécurisé
- 🔐 **IPsec** : VPN sécurisé standard
- 🔒 **Captive Portal** : Authentification web
- 🛡️ **Suricata** : IDS/IPS avancé
- 🛡️ **Snort** : IDS/IPS populaire
- 📜 **Règles de pare-feu** : Filtrage réseau avancé
- 📈 **Statistiques** : Surveillance du trafic
- 📚 **Packages** : Extensions pour fonctionnalités supplémentaires


## ⚙️ **Fonctionnalités du pare-feu**

- 🏷️ **Alias** : Groupes d’objets (IP, ports, URL)
- 🌐 **IPs virtuelles** : IPs additionnelles sur interface
- 🔁 **NAT** : Masquage ou redirection d’adresses
- 📅 **Plannings** : Activation/désactivation automatique
- 📏 **Règles** : Politique de filtrage
- 🚦 **Régulateur de flux (QoS)** : Priorisation et bande passante



## 🏷️ **Alias**

- Regroupement sous nom unique (réseaux, ports…)
- Facilite création, maintenance et documentation des règles

## 🌐 **IPs Virtuelles**

- IP supplémentaires sur une interface

###  **Types principaux** :

- IP Alias
- CARP (haute disponibilité)
- Proxy ARP

###  **Cas d’usage pfSense** :

- 🔁 **Haute disponibilité** (CARP)
- 🌍 **Exposition de services** avec différentes IP publiques

🛠️ *Exemple* : Ajout de l’IP 88.66.44.254 en plus de 88.66.44.1 sur l’interface WAN



## 📅 **Plannings dans pfSense**

- Création de plages horaires pour activer/désactiver des règles
- Automatisation des politiques
- Cas pratiques :
    → Restriction hors horaires
    → Sécurité renforcée en heures creuses
- Facilité de gestion des règles temporelles
