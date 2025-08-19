# Outils de gestion🧭 **Gestion graphique depuis le Gestionnaire de serveur**

Depuis **Windows Server 2012**, la gestion des **Services Bureau à Distance (RDS)** s’effectue directement dans le **Gestionnaire de serveur**.

📌 Une fois le rôle RDS installé, un **onglet dédié** apparaît pour centraliser la gestion de l’infrastructure.

✔️ Ce panneau permet de :

• Déployer les services de rôle

• Créer des collections

• Publier des applications

• Surveiller les connexions et l’état des serveurs



💻 **Gestion en ligne de commande avec PowerShell**

Le module **RemoteDesktop** offre une **gestion exhaustive** du service RDS en ligne de commande.

Idéal pour les déploiements automatisés, les scripts de maintenance ou la supervision avancée.

🔧 **Cmdlets PowerShell utiles** :

• 🔍 *Lister les sessions actives* > Get-RDUserSession

• ❌ *Déconnecter une session distante >* Disconnect-RDUser -HostServer "NomServeur" -UserName "NomUtilisateur"

• 📊 *Obtenir les infos sur un déploiement RDS >* Get-RDDeployment

🛠️ **Cmdlets avancées pour la configuration RDS** :

• 🚀 *Déployer un environnement RDS complet (bureaux basés sur une session) >* New-RDSessionDeployment

• 🗂️ *Créer une collection de sessions >* New-RDSessionCollection

• 🧩 *Publier une application RemoteApp >* New-RDRemoteApp
