# **🧱 Objets AD : deux grandes familles**

Dans Active Directory, les **objets** sont divisés en **entités de sécurité** et **conteneurs**.

### **🔐 Entités de sécurité**

Ce sont les **éléments qui peuvent se voir attribuer des droits d'accès** :

- **Utilisateurs** : comptes humains (ex. Toto, Tata)
- **Ordinateurs** : machines jointes au domaine (ex. Windows Server 2019)
- **Groupes** : collections d’utilisateurs/machines avec des droits communs

👉 Ces entités sont **sécurisables** : elles possèdent un **SID** et peuvent recevoir des permissions.



## **📂 Conteneurs (OU et système)**

### **📁 Unités d’organisation (OU)**

Comparables à des **dossiers logiques**, elles servent à **organiser et structurer** les objets (ex. par service, site ou fonction).

Elles peuvent contenir :

- Utilisateurs, groupes, ordinateurs
- D'autres OU imbriquées (sous-hiérarchies)

💡 Elles permettent aussi l'application ciblée de **GPO (stratégies de groupe)**.

### **🛠️ Conteneurs système**

Contiennent des **objets techniques ou internes à AD** (DNS, services, configuration…). Moins manipulés en usage courant, on y reviendra plus tard.



## **🛠️ Gestion des objets AD**

Pour créer, modifier ou supprimer des objets, plusieurs outils sont dispo :

- **Console "Utilisateurs et ordinateurs Active Directory" (dsa.msc)** : l'outil principal, en **MMC**
- **Centre d’administration AD (dsac.exe)** : interface graphique plus moderne
- **PowerShell** : pour l'automatisation, la gestion en masse, ou les tâches avancées

💡 L’outil MMC devient dispo **dès l’installation du rôle AD DS** sur un serveur.


