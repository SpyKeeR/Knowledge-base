# Prérequis🔑 **DHCP (obligatoire)**

✅ Un service **DHCP doit être opérationnel** pour permettre aux postes clients de **recevoir une adresse IP** au démarrage PXE.

➡️ Sans DHCP, les clients ne pourront **pas amorcer le processus de déploiement via le réseau**.

🧭 **DNS (optionnel mais recommandé)**

📌 **Pas requis en mode autonome**

Cependant, dans un environnement intégré à un domaine Active Directory, le **service DNS** devient **hautement recommandé** car il facilite :

- La **localisation des ressources WDS**
- L’**intégration à l’Active Directory**
- Le **nommage automatique** des postes clients



🏢 **Environnement recommandé**

🧬 **Intégration à Active Directory**

🟢 Recommandée pour une **gestion centralisée** des déploiements

✅ Avantages :

- Meilleure **automatisation**
- Possibilité de définir des **stratégies de déploiement**
- Gestion facilitée des **images et configurations**



⚠️ **Note importante**

📌 Il est **tout à fait possible** d’utiliser **WDS en mode autonome**, c’est-à-dire **sans Active Directory ni DNS**.

➡️ Cette option convient à de **petits environnements ou à des réseaux de test**.
