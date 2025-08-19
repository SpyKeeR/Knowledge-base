# 3 - Gestion du stockage🎯 **Objectifs du module**

Ce module vise à explorer les **technologies de stockage** utilisées pour les **sauvegardes**, à travers plusieurs grands axes :



📌 **Critères de choix d'une solution de stockage**

Pour bien choisir une solution de stockage, plusieurs critères doivent être évalués : ⚡ **Performance** 🔐 **Sécurité** 🧰 **Capacité** 🔄 **Fiabilité** 💰 **Coût** 🧩 **Compatibilité avec l’environnement existant**



🔌 **DAS – Direct Attached Storage**

Stockage **directement connecté** à un serveur (USB, SATA, SAS, etc.)

✅ Avantages :

- 🔧 Simplicité de mise en œuvre
- 💲 Coût réduit
- 🚀 Performances locales élevées

❌ Inconvénients :

- 🔗 Pas de mutualisation
- 🌐 Non accessible via le réseau
- 📈 Difficile à faire évoluer

📍 Cas d’usage :

- 🖥️ Petits serveurs
- 🔒 Données locales non partagées



🌐 **NAS – Network Attached Storage**

Stockage **en réseau** accessible à plusieurs utilisateurs

/appareils via protocole (SMB/NFS/FTP)

✅ Avantages :

- 👥 Partage facile entre utilisateurs
- 🧠 Interface de gestion centralisée
- 📊 Bon rapport coût/capacité

❌ Inconvénients :

- 🌐 Dépend de la qualité du réseau
- 🔄 Performances limitées par la bande passante

📍 Cas d’usage :

- 🏠 Réseaux domestiques ou PME
- 🗂️ Centralisation et partage de fichiers



🧠 **SAN – Storage Area Network**

Infrastructure dédiée pour le **stockage hautes performances** (Fibre Channel, iSCSI)

✅ Avantages : ⚡ Très haut débit 🔁 Haute disponibilité 🧩 Intégration avec virtualisation et clusters

❌ Inconvénients : 💰 Coût élevé 🧑‍🔧 Complexité de mise en œuvre

📍 Cas d’usage :

- 🏢 Environnements critiques ou virtualisés
- 🧱 Datacenters / Grands serveurs d’entreprise



💾 **Exécution des sauvegardes**

🔧 Mise en œuvre de stratégies efficaces selon :

- 📍 Le type de stockage utilisé
- 🕒 La fréquence des sauvegardes
- 🔄 La rotation des supports
- 🛡️ La redondance et la reprise d’activité
