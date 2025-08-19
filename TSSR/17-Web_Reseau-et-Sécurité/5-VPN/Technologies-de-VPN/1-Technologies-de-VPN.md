# Technologies de VPN- ❌ **Inconvénients** : protocole propriétaire.
- 🎯 **Utilisation** : utilisateurs d’ExpressVPN > performance et sécurité.



**SSL VPN (ou VPN basé sur TLS)**

**a) SSL VPN type "portail web"**

- 🔗 Accès via **navigateur web HTTPS (port 443)**
- ➕ Aucun logiciel client requis
- 💡 Propose :
  - Accès RDP/VNC via web
  - Portail d’applis internes
  - Téléversement/téléchargement de fichiers
- ❌ Pas de tunnel IP → pas d’interface réseau créée
- 🔒 Utilise **TLS (SSL déprécié)**
- 🌐 **Couche Présentation (6)** pour TLS

**Exemples** : Cisco WebVPN, Fortinet Web SSL VPN, Palo Alto, Sophos...



**b) SSL VPN avec tunnel complet (type AnyConnect, OpenVPN)**

- 🔐 Création d’une **interface réseau virtuelle** sur le client
- 📦 Utilise généralement **le port TCP ou UDP 443**
- ✅ Hautement compatible avec tout type de réseaux (Wi-Fi public strict)
- 💡 **TLS est utilisé pour chiffrer le tunnel VPN**
- 🌐 Couches : Application → Présentation → Transport

OpenVPN, Cisco AnyConnect, Forticlient SSL, etc.



**🔐 IKEv2/IPSec (Internet Key Exchange v2)**

- ✅ **Avantages** : rapide, stable, sécurisé
- ❌ **Inconvénients** : support variable sur anciens systèmes.

📱 **Utilisation** : très efficace sur smartphones et tablettes en mobilité (roaming)



**📦 OpenVPN (Open Source)**

- ✅ **Avantages** : hautement configurable, sécurisé, multi-plateforme, supporte TCP/UDP.
- ❌ **Inconvénients** : configuration plus complexe.
- 🎯 **Utilisation** : recommandé pour la plupart des scénarios d’accès distant.

💡 **Note** : pfSense intègre un **serveur OpenVPN**, facilitant sa mise en œuvre.

samedi 26 juillet 2025

09:10

**PPTP (Point-to-Point Tunneling Protocol)**

- 📅 Ancien protocole Microsoft (années 90)
- 🔒 **Chiffrement possible** via MPPE, mais totalement obsolète et cassé
- 📦 Port : **TCP 1723** + **Protocole GRE (47)**
- ✅ Facile à configurer, multiplateforme
- ❌ **Insecure**, déconseillé en production
- 🌐 Travaille **au-dessus de la couche 3 (Réseau)** via encapsulation PPP + GRE

⚠️ N'est **pas réellement en couche 2** OSI.



**L2TP / IPsec (Layer 2 Tunneling Protocol + IPsec)**

- 👥 Co-développé par **Microsoft & Cisco**
- 🔐 Combine L2TP (tunneling) + IPsec (chiffrement & authentification)
- 📦 Ports :
  - UDP **1701** → L2TP
  - UDP **500** → IKE
  - Protocole **50 (ESP)** → Transport sécurisé
- ✅ Bonne sécurité, assez compatible (mais parfois bloqué par NAT/pare-feux)
- 🌐 L2TP : **couche Liaison (2)** / IPsec : **couche Réseau (3)**

⚠️ Pas de chiffrement natif dans L2TP seul.



**IPsec seul (en mode tunnel)**

- 🔐 Protocoles : IKEv2, ESP, AH
- 📦 Ports : UDP 500, UDP 4500, protocole 50
- 📌 Excellent en site-à-site, mais **plus complexe** à configurer côté client nomade
- 🌐 Fonctionne en couche **Réseau (3)**
- ✅ Très sécurisé, mais peut être bloqué dans les environnements NAT stricts



**SSTP (Secure Socket Tunneling Protocol)**

- 💻 Protocole Microsoft, Windows natif ⚠️ Propriétaire Microsoft
- 🔒 Utilise **SSL/TLS** pour encapsuler du PPP
- 📦 Port : **TCP 443**
- ✅ Très pratique pour traverser les firewalls/NAT
- 🌐 Couches : Application → Présentation (via TLS)
