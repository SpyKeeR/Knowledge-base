# Config Wireguard**🧩 Présentation de WireGuard**

WireGuard est un protocole VPN de nouvelle génération, privilégié pour sa **simplicité**, ses **performances élevées** et son **design minimaliste**.

🔎 **Avantages**

- Code source léger, facile à auditer
- Très rapide, faible latence
- Installation et configuration simplifiées

⚠️ **Inconvénients**

- Moins mature que d'autres protocoles (OpenVPN, IKEv2)
- Support limité sur certaines plateformes plus anciennes

🎯 **Utilisation idéale** : environnements recherchant **des performances optimales**, une **configuration rapide** et une **sécurité moderne**



**🛠️ Configuration de WireGuard sur pfSense**

📌 Accès via : VPN > WireGuard > Tunnels

1.  **Création du tunnel**

    - **✅ Activer WireGuard (Enable WireGuard)**
    - **🏷️ Nommer le tunnel**
    - **🔐 Générer les clés privées/publiques**
    - **📋 Copier la clé publique (pour le peer distant)**

2.  **Interface réseau**

    - **📶 Assigner un réseau tunnel (IPv4) à cette interface**
    - **🎛️ Activer l’interface dans Interfaces > Assignments**



**🔄 Lien entre le client Linux et pfSense**

1.  🧷 **Sur pfSense > onglet Peers** :

    - Sélection du tunnel WireGuard
    - Coller la **clé publique du client Linux** dans le champ *Public Key*

2.  🛠️ **Sur Linux** :

    - Lancer le VPN : systemctl enable --now wg-quick@wg0
    - 🧭 Activer le routage IP : Ajouter dans /etc/sysctl.conf : "net.ipv4.ip_forward=1"
    - Appliquer : sudo sysctl -p

**🔍 Outils de supervision**

📄 Vérification de l’état du tunnel : wg show

Affiche les clés, les adresses IP, le trafic et la connectivité des pairs actifs.

**🐧 Configuration WireGuard sur Linux**

📦 Installation : sudo apt install wireguard

📁 Répertoire de configuration : /etc/wireguard

🔐 Génération des clés :

- Clé privée : /etc/wireguard/privatekey
- Clé publique à copier dans pfSense : cat /etc/wireguard/publickey



📄 Création du fichier de configuration wg0.conf :

[Interface]  
PrivateKey = <clé privée du client>  
Address = <IP du client dans le réseau tunnel>

[Peer]  
PublicKey = <clé publique du serveur pfSense>  
Endpoint = <IP publique du pfSense>:<port>  
AllowedIPs = <réseau autorisé à travers le tunnel>  
PersistentKeepalive = 25
