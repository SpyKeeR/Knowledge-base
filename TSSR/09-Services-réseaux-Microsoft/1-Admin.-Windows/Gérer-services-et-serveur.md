# Gérer services et serveur**🧭 Gestion avec le Gestionnaire de serveurs**

🖥️ Le **Server Manager** reste le point de départ principal pour :

- 📦 Installer des rôles/fonctionnalités
- 🔍 Superviser les services
- 🧑‍🤝‍🧑 Ajouter et gérer plusieurs serveurs en distant
- ⚡ Démarrer/arrêter des services installés

💡 Pratique pour les admins qui préfèrent la **gestion graphique centralisée**.



**🧩 Les consoles MMC (Microsoft Management Console)**

🪟 Les **MMC** sont des **interfaces modulables** permettant de gérer des éléments précis (services, disques, utilisateurs…).

🧠 Avantages clés :

- 📡 **Installables sur un poste client**, même si le rôle n’est pas présent dessus
- 🎛️ Ajout d’**addons (snap-ins)** : services, stratégie de sécurité, AD, etc.
- 🧳 Peuvent être **enregistrées et réutilisées** (format .msc)
- 🔄 Permettent une **gestion à distance simplifiée**

📍 Exemples courants : services.msc, compmgmt.msc, gpmc.msc, etc.



**💻 Commandes CMD & PowerShell**

🧪 Pour tout ce qui est rapide, scriptable ou sans interface :

**🔸 Invite de commandes (CMD)**

- 📄 Moins riche que PowerShell mais suffisant pour certaines tâches : ping, ipconfig, netstat, etc.

**🔹 PowerShell = couteau suisse ultime**

- 🧠 Accès profond aux rôles/fonctions/sécurité
- 🔄 Automatisation de masse
- 🔍 Exemple :  
  Get-Service → liste les services (statuts, noms, dépendances…)

💡 Obligatoire à maîtriser **en mode Core**, mais **hyper utile** aussi en mode GUI pour gagner du temps ou faire des scripts de déploiement.
