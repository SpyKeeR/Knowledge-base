# Config OpenVPN🔧 **Assistant de configuration OpenVPN**

- **Menu** : VPN > OpenVPN > Assistants
- **Assistant utilisé** : *OpenVPN Remote Access Server Setup*
- **Choix du type d’authentification** :
  - 👤 Local User Access
  - 🧩 LDAP
  - 📡 RADIUS



📡 **Configuration de l’annuaire LDAP**

- **Sélection du serveur LDAP** depuis la liste
- **Chemin** : *Assistant > LDAP Server Selection*
- **Choisir le serveur LDAP préalablement configuré*



🔐 **Configuration de la Certification**

- **Choix de l’AC (Autorité de Certification)**
  - *Assistant > Certificate Authority Selection*
  - ✅ Sélectionner l’AC créée dans pfSense
- **Choix du certificat serveur**
  - *Assistant > Server Certificate Selection*
  - ✅ Sélectionner le certificat valide pour le serveur OpenVPN



⚙️ **Paramètres généraux du serveur OpenVPN**

- **Interface** 🎧 : Choisir l’interface réseau qui écoutera les connexions VPN
- **Protocole** 📶 : Choisir entre UDP ou TCP
- **Port local** 🔌 : Indiquer le port utilisé (par défaut : 1194 UDP)
- **Description** 📝 : Donner un nom explicite au serveur VPN
- **Paramètres cryptographiques** 🔒 : Laisser par défaut sauf besoins spécifiques



🌐 **Paramètres du tunnel VPN**

- **Réseau tunnel** 🛤️ : Spécifier le réseau IP attribué aux clients VPN
- **Passerelle de redirection** 🚦 : Cocher cette option pour faire transiter **tout le trafic** des clients par le tunnel
- **Local Network** 🏠 : Indiquer les réseaux internes accessibles via le VPN
- **Connexions simultanées** 🔁 : Définir le nombre maximum de clients autorisés



📲 **Paramètres client**

- **IP dynamique** 🔄 :
  - Cocher pour que les clients restent connectés même si leur IP change
- **Topologie** 🗺️ :
  - Sous-réseau : Une IP par client dans le même réseau
  - Net30 : Un sous-réseau par client
- **DNS par défaut** 🌍 :
  - Indiquer les serveurs DNS à utiliser via le VPN
  - Également applicable aux serveurs NTP et WINS



🚦 **Règles de pare-feu associées**

- **Traffic from clients to server** 🛡️ :
  - Créer une règle permettant aux clients de se connecter au serveur VPN
- **Traffic from clients through VPN** 🌐 :
  - Créer une règle permettant aux clients de communiquer via le tunnel VPN



**📶 Activation de l’interface VPN**

- 📋 Aller dans Interfaces > Interface Assignments
- ➕ Ajouter l’interface OpenVPN pour permettre la gestion fine (routage, QoS, monitoring)
