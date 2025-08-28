# **📌 Qu’est-ce que le protocole DNS ?**

Le **DNS** (Domain Name System) est un **service de résolution de noms**. Il traduit les **noms de domaine** en **adresses IP** (et vice versa).

➡️ Exemple : 10.1.1.1 devient monposte.local = plus lisible pour l’utilisateur 🧠

C’est un **protocole fondamental d’Internet**, défini par des **RFC** (notamment la RFC 1035). Il repose sur :

- Un **espace de noms hiérarchique** 🌳
- Un **système distribué de serveurs** 🌍
- Des requêtes/réponses en UDP ou TCP selon le besoin (port 53)
- Une **architecture extensible**, notamment grâce à **EDNS**



## **🧬 Principales caractéristiques du DNS**

- 🧭 **Résolution de noms** → associer un nom (FQDN) à une IP
- 🌐 **Hiérarchie**
  - .,
  - .com,
  - .exemple.com,
  - srv1.exemple.com
- 🔁 **Routage distribué** → résolution progressive via serveurs racine, TLD, autoritaires
- 🧱 **Gestion décentralisée** → chaque organisation gère sa propre zone
- 📥 **Protocoles standardisés** (RFCs)
- 📡 **Multifonction** → résolution directe (nom → IP) et inverse (IP → nom)



## **🚀 Fonctionnement global du DNS**

Le DNS utilise une **chaîne de serveurs** pour répondre à une requête :

1.  🧑‍💻 Client → envoie une requête pour srv1.exemple.com
2.  📍 Serveur racine → indique le TLD (.com)
3.  🧭 Serveur .com → donne le NS d’exemple.com
4.  🏢 Serveur autoritaire → renvoie l'IP exacte de srv1.exemple.com
5.  ✅ Résultat → stocké en cache pour accélérer les futures requêtes



## **🧰 Fichiers hosts vs DNS**

Le fichier hosts est **local** et permet de créer des correspondances **manuelles** (nom ↔ IP).

Le DNS, lui, agit **à grande échelle** et permet une **résolution automatisée**, via le réseau, souvent de manière récursive ou itérative.



## **🧠 EDNS : Extension Mechanisms for DNS**

L’**EDNS** (RFC 6891) ajoute des **capacités avancées** au DNS classique :

- 📦 Support de **paquets DNS > 512 octets** (pour IPv6, DNSSEC…)
- ⚙️ Permet d’ajouter des **options** supplémentaires sans modifier le protocole de base
- 🔄 Compatible avec les serveurs DNS récents
- Indispensable pour les fonctionnalités modernes (sécurité, performance)


