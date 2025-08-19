# Installation WDS📥 **Méthodes d'installation**

🔹 **Interface graphique – Gestionnaire de serveur**

- Méthode intuitive pour les utilisateurs préférant une interface GUI
- Accessible depuis le **Gestionnaire de serveur > Ajouter des rôles et fonctionnalités**

🔹 **Ligne de commande – PowerShell**

- Méthode rapide pour les administrateurs systèmes
- 💻 Commande utilisée : Install-WindowsFeature -Name WDS -IncludeManagementTools

🧩 **Services de rôle à sélectionner**

Lors de l'installation du rôle WDS, deux **services de rôle** sont proposés :

- 📦 **Serveur de déploiement**
- 🚚 **Serveur de transport**

✅ **Pour un déploiement standard de systèmes via WDS**, il est **impératif d’installer les deux** services.

👉 Par défaut, ils sont généralement **précochés** lors de l'installation.



⚠️ **Cas particulier – Serveur de transport seul**

🔄 Ce rôle peut être installé **indépendamment**, mais uniquement dans **des cas spécifiques** (ex : transfert de fichiers via **multicast**).

❌ Ces scénarios **ne sont pas abordés** dans le cadre d’une installation classique de WDS.
