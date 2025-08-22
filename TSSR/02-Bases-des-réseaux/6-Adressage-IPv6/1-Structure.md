# 🌍 Structure d'une Adresse IPv6**

L’**IPv6** (Internet Protocol version 6) est conçu pour remplacer **IPv4**, qui souffre d’un manque d’adresses. Avec **128 bits**, il offre **un espace d’adressage immense** et des **fonctionnalités avancées** pour l’Internet moderne.



## **📌 Format d’une Adresse IPv6**

Une adresse IPv6 est constituée de **8 groupes de 4 chiffres hexadécimaux**, séparés par :.

Exemple : 2001:0db8:85a3:0000:0000:8a2e:0370:7334

Par convention, les 64 premiers bits sont destinés à définir la partie réseau.  

Exemple : L'IANA fourni aux RIR (RIPE NCC) un réseau en **2a00::/*12***, qui fourni a Free SAS le **2a01:e00::/*26*** qui me fourni le **2a01:0e0a:bXX:XXa0::/*60*** que je peux découper en **2a01:0e0a:bXX:XXa*0*::/*64***, **2a01:0e0a:bXX:XXa*1*::/*64***, **2a01:0e0a:bXX:XXa*2*::/*64*** …

✅ **Chaque quartet représente 16 bits** (soit **2 octets**).



## **🏷️ Compression des Adresses IPv6**

L’IPv6 utilise plusieurs règles pour **simplifier** l’affichage des adresses :

1️⃣ **Supprimer les zéros initiaux** de chaque quartet.

👉 2001:0db8:0000:0000:8a2e:0370:7334 devient **2001:db8:0:0:8a2e:370:7334**

2️⃣ **Remplacer une série continue de zéros par ::** (une seule fois).

👉 2001:db8:0:0:0:ff00:42:8329 devient **2001:db8::ff00:42:8329**



## **🚀 Avantages de l’IPv6**

✅ **3,4 × 10³⁸ adresses** → Plus de pénurie comme en IPv4.

✅ **Fin du NAT** → Chaque appareil peut avoir une adresse publique unique.

✅ **Auto-configuration Stateless** → Génération automatique d’adresses sans serveur DHCP.

✅ **Sécurité intégrée (IPsec)** → Chiffrement et authentification par défaut.



## **🔄 Coexistence IPv6 & IPv4**

IPv6 et IPv4 **ne sont pas directement compatibles**, mais plusieurs solutions existent :

- **Double Stack** → Utilisation simultanée des deux protocoles.
- **Tunneling** → Encapsulation d’IPv6 dans IPv4.
- **NAT64** → Traduction entre IPv6 et IPv4.

