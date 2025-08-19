# iSCSI🔄 **Principe général**

Le protocole **iSCSI** permet d’accéder à un **stockage en mode bloc** via un **réseau IP**, en encapsulant des **commandes SCSI** et des **blocs de données** dans des **paquets TCP/IP**.



📦 **Détail du fonctionnement** :

- Capture les **commandes SCSI** et les données associées.
- Transporte ces informations via un **réseau TCP/IP Ethernet**, au lieu d’un câble SCSI physique.
- Permet une communication à distance, ce qui en fait une **solution de stockage centralisé**.



🧭 **Architecture iSCSI**

- 🟢 **Initiator** : logiciel installé sur l’hôte (le client)
  - Envoie une requête vers une target
- 🔴 **Target** : service disponible sur un **périphérique de stockage**
  - Reçoit les commandes et renvoie les données demandées



📡 **Échange réseau** :

- Requête → encapsulée dans un **paquet TCP/IP**
- Réponse → contient les **blocs de données**

💡 **Cas d’usage**

- Utilisation d’un **stockage distant** comme s’il était **local**
- Solution idéale pour un **SAN basé sur IP**
- Compatible avec les environnements **de virtualisation** et de **stockage centralisé**



📌 **À retenir**

- iSCSI = **SCSI + TCP/IP**
- Permet d’utiliser une **infrastructure réseau standard (Ethernet)** pour accéder à un **stockage bloc distant**
- Rend un **volume distant accessible** comme un **disque local**, sans câble physique direct
