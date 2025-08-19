# Connexions**🔗 Connexions entre équipements**

Ce ne sont pas **que des connexions réseau** !

On peut **lier** :

- 🖥️ Ordinateur ↔️ 🖨️ Imprimante
- 🖥️ Ordinateur ↔️ 📺 Écran ou vidéoprojecteur
- 🖥️ Ordinateur ↔️ 📱 Téléphone
- Etc.

👉 Le but est de **représenter les connexions réelles** entre éléments dans GLPI, **quelle que soit leur nature** (USB, Ethernet, Wi-Fi, etc.).



**🧩 Ports réseau et connectiques**

Chaque équipement peut avoir **plusieurs ports réseau**, caractérisés par :

- Un **nom** (ex : Port 1, eth0, LAN-A)
- Un **type de connectique** : 🔌 Ethernet / 📶 Wi-Fi / 🌐 Fibre / 🔄 Agrégation (bonding de plusieurs ports)

Tu peux aussi gérer :

- Le **nombre de ports** sur un équipement (utile pour les switchs)
- Le **type de carte réseau** associée



**🌍 Paramètres de connexion réseau**

Un port réseau peut être lié à un **réseau défini** dans GLPI, avec :

- Adresse IP
- Masque de sous-réseau
- Nom de domaine
- VLAN ID (si VLAN utilisé)

Tout ça est regroupé dans les **fiches de ports réseau** → permet une **cartographie logique et physique** du réseau.



**🧱 Modéliser l’infrastructure physique**

GLPI permet de modéliser :

- 📌 Une prise murale (ex : P001 en S331)
- ↔️ Le lien avec un brassage (baie informatique)
- 🔀 Le brassage relié à un switch, ou à une autre prise/équipement

Exemple : Port 1 de l’ordinateur  
→ relié à la prise murale P001 (lieu : S331)  
→ connectée au port G0/1 du switch Cisco

👉 Très utile pour la **traçabilité physique des câbles et prises réseau**.
