# EncapsulationL'encapsulation consiste à ajouter des en-têtes à chaque niveau du modèle OSI, afin de préparer les données pour le transport entre les couches. Voici comment cela se passe lors de l'envoi d'un email :



**🖥️ Couche 7 - Application**
- **Rôle :** Rédaction de l'email (SMTP)
- **PDU :** 📧 **APDU** (Email + pièces jointes)
- **En-tête :** 🏷️ En-tête Application
- **Transfert :** ⬇️ Vers Couche 6



**🔐 Couche 6 - Présentation**
- **Rôle :** Format standard, chiffrement (SSL/TLS)
- **PDU :** 🔒 **PPDU** (Encapsulation de l'APDU)
- **En-tête :** 🏷️ En-tête Présentation + méthode de cryptage (si utilisé)
- **Transfert :** ⬇️ Vers Couche 5



**📝 Couche 5 - Session**
- **Rôle :** Gestion de session (synchronisation)
- **PDU :** 💬 **SPDU** (Encapsulation du PPDU)
- **En-tête :** 🏷️ En-tête Session + ID de session, mécanismes de synchronisation
- **Transfert :** ⬇️ Vers Couche 4



**🚚 Couche 4 - Transport**
- **Rôle :** Transport fiable (TCP/UDP)
- **PDU :** 📦 **TPDU / Segment** (Contrôle : ports, séquence, erreurs)
- **En-tête :** 🏷️ En-tête Transport + ports, numéros de séquence
- **Transfert :** ⬇️ Vers Couche 3

**🌐 Couche 3 - Réseau**
- **Rôle :** Acheminement via IP
- **PDU :** 📬 **RPDU** (Paquet avec adresses IP)
- **En-tête :** 🏷️ En-tête Réseau + adresses IP source et destination
- **Transfert :** ⬇️ Vers Couche 2



**🔌 Couche 2 - Liaison de Données**
- **Rôle :** Transmission fiable sur lien local (MAC)
- **PDU :** 📡 **LPDU** (Trame avec adresses MAC)
- **En-tête :** 🏷️ En-tête Liaison + adresses MAC source et destination
- **Queue :** 🔚 Queue de trame Ethernet
- **Transfert :** ⬇️ Vers Couche 1



**💾 Couche 1 - Physique**
- **Rôle :** Conversion en bits et transmission
- **PDU :** 🔢 **Bits** (Signal physique)
- **En-tête :** ❌ Aucune en-tête
- **Transfert :** 🚶 Transmission physique des bits
