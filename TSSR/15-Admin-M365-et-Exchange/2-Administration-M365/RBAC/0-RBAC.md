# **🔐 Principe RBAC (Role-Based Access Control)**

RBAC permet de **déléguer des autorisations de manière granulaire** sur Microsoft 365 et Microsoft Entra ID, à travers des **rôles prédéfinis ou personnalisés**.

## 🎯 **Objectif** : appliquer le **principe du moindre privilège** → ne donner que les droits strictement nécessaires à chaque utilisateur.



### **🛠️ Gestion des rôles dans Microsoft 365 (Entra ID)**

Microsoft utilise un système RBAC basé sur :

- des **rôles d’annuaire** (Directory Roles) gérés via **Microsoft Entra**
- des **rôles spécifiques aux services** (Exchange, Intune, Defender...)

📌 Les droits doivent être **délégués à des comptes dédiés à l’administration**, pas aux comptes personnels → éviter l’**escalade de privilèges**.



## **👤 Comptes administrateurs sans licence**

Bonne pratique : créer des comptes **sans licence utilisateur** dédiés à l’administration.

Ils peuvent quand même accéder aux portails d’admin (M365, Entra, Exchange...).



## **🏷️ Principaux rôles prédéfinis (Microsoft Entra ID)**

| **📛 Rôle** | **🔧 Nom technique (PowerShell)** | **🎯 Description** |
|----|----|----|
| **Administrateur global** | Company Administrator | Accès total à toutes les fonctionnalités. À limiter à 1 ou 2 personnes. |
| **Administrateur de facturation** | Billing Administrator | Gère les abonnements, achats et support. |
| **Administrateur du support** | Service Support Administrator | Accès à l’état des services, création de tickets support. |
| **Administrateur des utilisateurs** | User Administrator | Gère utilisateurs/groupes, mots de passe. |
| **Administrateur de l’assistance** | Helpdesk Administrator | Réinitialise les mots de passe uniquement. |
| **Administrateur des licences** | License Administrator | Gère les affectations de licences (⚠ souvent oublié). |

✅ **D'autres rôles existent**, notamment :

- Exchange Administrator
- Security Administrator
- Teams Administrator
- Compliance Administrator  
  *(→ Voir tout via PowerShell ou Entra Portal)*



## **📧 RBAC dans Exchange Online**

Exchange possède son propre système RBAC intégré, via l’**Exchange Admin Center (EAC)** ou PowerShell.

⚙️ Possibilité de :

- Créer des **rôles personnalisés**
- Associer des **permissions précises** à des groupes d’administration

➡️ Exemple : rôle **"Mail Recipients"** pour gérer uniquement les boîtes mail et alias.

📝 Chaque rôle inclut une **liste d’autorisations** visible dans l’interface.


## **✅ Bonnes pratiques RBAC**

1️⃣ **Limiter les administrateurs globaux** à l'essentiel

2️⃣ **Créer des comptes admin dédiés** (pas de double usage)

3️⃣ **Revoir les rôles régulièrement** pour éviter les droits excessifs

4️⃣ **Utiliser des rôles personnalisés** pour répondre à des besoins spécifiques

5️⃣ **Suivre les attributions de rôle** via logs et alertes.

