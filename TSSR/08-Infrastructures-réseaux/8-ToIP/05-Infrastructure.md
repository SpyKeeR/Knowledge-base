# Infrastructure

## **🧭🔌 Le PABX : le central historique**

Le **PABX** (Private Automatic Branch Exchange) permet de relier le réseau interne au RTC. Fonctions principales :

- Appels internes/externes, SDA (sélection directe à l’arrivée), transferts, conférences, gestion des droits d’appel, interphonie, etc.

➡️ C’est **l’autocommutateur téléphonique** encore très présent, mais peu à peu remplacé.



## **🌐📲 L’IPBX : le successeur logique**

L’**IPBX** prend le relais grâce à la **VoIP** :

➡️ Il gère appels internes + externes via le WAN.

💡 Avantages : fonctionnalités PABX + intégration applicative, **CTI (Couplage Téléphonie-Informatique)**, supervision, mobilité, etc.

🕳️ Nécessaire avec l’**extinction du RTC**.



## **📊🕓 Serveur de taxation : qui appelle qui, quand, combien de temps**

Permet d’**enregistrer et tracer les communications** : numéro appelant/appelé, durée, date/heure.

➡️ Infos souvent récupérées directement via les **logs IPBX**.

- Statistiques, analyse des temps agents/clients, reporting 📈



## **🛤️🌍 La Media Gateway : ponts entre réseaux**

C’est le **passerelle de traduction protocolaire** entre ToIP et d’autres réseaux (RTC, 4G, Wi-Fi).

💡 Parfois embarquée directement dans un routeur ou intégrée à un soft PABX en mode léger.



## **📦🪪 Les petites passerelles**

Pour **interconnecter un autocom avec le RTC**, on utilise :

- Carte d’interface, Gateway Cisco, modem/routeur, ou **boîtiers PATTON** (très répandus jusqu’à 2 T0).  
  ➡️ Attention : peu adaptés aux **liaisons T2**, mais pratiques pour petites infra.



## **📱💻 Les terminaux de téléphonie**

2 grandes familles :

- **Softphones** : appli PC avec micro-casque, parfois webcam (Teams, Zoiper, Jami…).
- **Téléphones IP** : écran tactile, micro-switch intégré, PoE possible, etc.

➡️ Le choix dépend du besoin (mobilité, confort, intégration UC…).



## **⚡🔌 Le PoE (Power over Ethernet)**

➡️ **Permet d’alimenter les téléphones IP via un switch PoE**, pas besoin de prise électrique.

→ Simplifie câblage + meilleure résilience (via onduleur réseau).

📏 **Types de PoE** : 
- **IEEE 802.3af** : jusqu’à 12,9 W (modèles classiques)
- **IEEE 802.3at (PoE+)** : entre 24 et 30 W (modèles haut de gamme)


