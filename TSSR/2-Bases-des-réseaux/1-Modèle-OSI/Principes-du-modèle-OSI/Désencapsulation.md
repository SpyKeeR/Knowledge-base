# Désencapsulation- **💾 Couche 1 - Physique**
  - **Rôle :** Réception des bits sous forme de signal physique
  - **PDU :** 🔢 **Bits** (Signal physique reçu)
  - **En-tête :** ❌ Aucune en-tête
  - **Transfert :** ⬇ Vers Couche 2



- **🔌 Couche 2 - Liaison de Données**
  - **Rôle :** Décodage des bits en trame (LPDU)
  - **PDU :** 📡 **LPDU** (Trame avec adresses MAC)
  - **En-tête :** 🏷️ En-tête Liaison + adresses MAC source et destination
  - **Queue :** 🔚 Suppression de la queue de trame Ethernet
  - **Transfert :** ⬇ Vers Couche 3



- **🌐 Couche 3 - Réseau**
  - **Rôle :** Extraction des adresses IP et acheminement
  - **PDU :** 📬 **RPDU** (Paquet avec adresses IP)
  - **En-tête :** 🏷️ En-tête Réseau + adresses IP source et destination
  - **Transfert :** ⬇ Vers Couche 4



- **🚚 Couche 4 - Transport**
  - **Rôle :** Contrôle de la fiabilité et de la transmission (TCP/UDP)
  - **PDU :** 📦 **TPDU / Segment** (Contrôle : ports, séquence, erreurs)
  - **En-tête :** 🏷️ En-tête Transport + ports, numéros de séquence
  - **Transfert :** ⬇ Vers Couche 5

&nbsp;

- **📝 Couche 5 - Session**
  - **Rôle :** Gestion de la session (synchronisation)
  - **PDU :** 💬 **SPDU** (Données avec synchronisation)
  - **En-tête :** 🏷️ En-tête Session + ID de session, mécanismes de synchro
  - **Transfert :** ⬇ Vers Couche 6
- **🔐 Couche 6 - Présentation**
  - **Rôle :** Conversion des données compréhensible et déchiffrement
  - **PDU :** 🔒 **PPDU** (Données avec cryptage)
  - **En-tête :** 🏷️ En-tête Présentation + méthode de cryptage (si utilisé)
  - **Transfert :** ⬇ Vers Couche 7
- **🖥️ Couche 7 - Application**
  - **Rôle :** Reconstitution de l'email complet et affichage à l'utilisateur
  - **PDU :** 📧 **APDU** (Email complet + pièces jointes)
  - **En-tête :** 🏷️ En-tête Application
  - **Reconstruction :** L'email est reconstitué pour l'utilisateur.
