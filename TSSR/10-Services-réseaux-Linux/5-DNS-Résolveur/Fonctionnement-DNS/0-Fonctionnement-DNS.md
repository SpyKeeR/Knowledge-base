# **🌐 Rôle du service DNS**

Le **DNS (Domain Name System)** permet :

- 🔁 Faire le lien entre un **FQDN** (nom de domaine complet) et une adresse IP
- ↩️ Faire l'inverse (résolution inverse / PTR)
- ✉️ Localiser les serveurs de mail (via les enregistrements **MX**)
- 🔌 C’est un **service-clé d’Internet**, indispensable pour la navigation
- 📑 Standardisé par l’IETF (RFCs)

## **📲 Fonctionnement global du DNS**

Chaque machine (PC, serveur, smartphone) utilise un **serveur DNS configuré localement** pour traduire les noms en IP.

Exemple classique : « Quelle est l’@IP de [www.baidu.com](http://www.baidu.com) ? »

Le DNS répond : **103.235.46.39**

C’est automatique, invisible… mais critique ! 😅



## **🌳 Structure hiérarchique des noms DNS**

- Le **nommage DNS est arborescent**, lu **de droite vers la gauche**
- Le **point final** (.) représente la **racine DNS**
- Exemples :
  - .com ➜ domaine racine → TLD (géré par l’IANA)
  - indiatimes.com. ➜ domaine + sous-domaine
  - [www.indiatimes.com](http://www.indiatimes.com). ➜ **FQDN** : nom d’hôte pleinement qualifié
- Le **point (.)** est le séparateur de niveaux



### **🏢 Cas de domaines privés**

Le DNS ne sert pas que sur Internet :

- On peut créer des **zones internes**, non publiques, pour un usage en entreprise
- Exemple :
  - masuperentreprise.bzh : domaine visible depuis Internet
  - enigmes.corp : domaine **privé**, utilisé uniquement en interne



## **🔄 Deux types de fonctionnement DNS**

1.  **Résolveur DNS complet** 🧭  
    → Résout n’importe quelle requête de ses clients en interrogeant Internet

2.  **Serveur faisant autorité** 📚  
    → Gère uniquement des zones précises (ex : entreprise.local), il **détient l'info officielle**

