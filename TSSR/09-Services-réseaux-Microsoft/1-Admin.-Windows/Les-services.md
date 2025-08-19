# Les services**🧩 Services principaux de Windows Server**

🔒 **Active Directory (AD)** est le **pilier central** :

Il gère les utilisateurs, groupes, ordinateurs, stratégies, droits d’accès dans un **environnement de domaine**. C’est la base pour une **administration centralisée, sécurisée et scalable**. 💡 Grâce à AD, tu peux automatiser la création de comptes, appliquer des règles GPO, et gérer les accès aux ressources réseau (fichiers, applis, imprimantes…).



**🛠️ Services additionnels incontournables**

Windows Server embarque plusieurs **services réseau** complémentaires à AD :

- 🌐 **DNS** : traduit les noms de domaine en adresses IP
- 📡 **DHCP** : distribue automatiquement les adresses IP aux clients
- 🔄 **WSUS (Windows Server Update Services)** : centralise et pilote les mises à jour des postes clients
- 🧪 **Hyper-V** : plateforme de **virtualisation native**, permet de créer et gérer des machines virtuelles

🎯 Ces services permettent de **structurer un réseau pro fiable et cohérent**, de la couche IP jusqu'à la gestion des VM.



**⚙️ Installation & configuration**

**🧰 Gestionnaire de serveur (Server Manager)**

🖥️ C’est l’outil graphique dispo **uniquement en mode Standard (GUI)**.

Il te permet de : ⚙️ Ajouter/Supprimer rôles et fonctionnalités, 📊 Visualiser l’état des services, 🧭 Gérer plusieurs serveurs à distance depuis un seul point

✅ C’est le **moyen le plus visuel et pédagogique** pour bosser sur les rôles, notamment en formation.

**🧱 Les rôles = services serveur**

🔑 Un **rôle** = une **fonction principale** fournie par le serveur pour les clients.

Exemples : 🗃️ **Service de fichiers** : partage réseau, 📡 **Serveur DHCP** : distribue des IP, 🌐 **Web Server (IIS)** : héberge un site, 🧩 Chaque rôle peut embarquer **plusieurs services** associés

**🧰 Les fonctionnalités = compléments**

🧩 Une **fonctionnalité**, c’est **un outil ou un composant** utilisé **par le système ou d'autres rôles**.

Exemples : 🗣️ **Support multilingue,** 🛠️ **.NET Framework,** 🔍 **RSAT (Outils d’administration à distance),** 📊 **Outils de diagnostics et performance**

Elles n'apportent **pas de service direct aux clients**, mais **améliorent le fonctionnement du serveur**.

**💻 PowerShell en mode Core**

🔒 En **mode Core**, pas de GUI → **PowerShell obligatoire**.

Tu utilises Install-WindowsFeature -Name NomDuRole pour ajouter un rôle ou une fonctionnalité.

Exemple courant : 🌐 Web-Server → installe IIS ⚙️ Option -IncludeManagementTools pour les outils de gestion associés

💡 Même si tu bosses en GUI, **connaître ces commandes** te sera utile dans les scripts, l’automatisation ou en environnement distant.
