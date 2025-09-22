# Config IKE/IPsec

## **⚙️ Création du tunnel — Phase 1 (P1)**

🔽 Accès : **VPN > IPsec > Tunnels > Add**

### **🔐 Paramètres de la phase 1 :**

- **Version IKE** : IKEv2
- **Protocole Internet** : généralement IPv4
- **Interface locale** : interface WAN de pfSense
- **Passerelle distante** : IP publique de l’autre routeur

### **🔑 Authentification :**

- **Méthode** : Pre-Shared Key (PSK)
- Générer une **clé secrète partagée** identique sur les deux routeurs

📌 Cette phase négocie les algorithmes de chiffrement et d’authentification ainsi que les clés de session.



## **🔄 Création de la phase 2 (P2)**

🔽 Depuis l’interface du tunnel P1 : **+ Add P2**

### **🧭 Paramètres de la phase 2 :**

- **Mode** : Tunnel
- **Réseau local** : réseau interne à partager côté pfSense (ex : 192.168.1.0/24)
- **Réseau distant** : réseau interne du site distant (ex : 192.168.2.0/24)

📌 Il est possible d’ajouter plusieurs phases 2 si plusieurs sous-réseaux doivent être joints.



## **🔥 Configuration du pare-feu**

🔽 Accès : **Firewall > Rules > IPsec**

### **📜 Ajout de règles :**

- Autoriser le **trafic en provenance des réseaux distants**
- Destination : les **réseaux locaux** définis en Phase 2
- Action : Pass
- Protocole : Any ou filtré selon le besoin

📌 Ces règles permettent aux hôtes distants d'accéder aux ressources locales via le tunnel.



## **✅ Résultat attendu**

- Le tunnel s’établit automatiquement lorsque du trafic circule.
- Les hôtes des deux réseaux peuvent communiquer **en toute sécurité** via le tunnel IPSec.
