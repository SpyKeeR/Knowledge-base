# Azure AD/Entra ID

Connexion aux **applications cloud** (Microsoft 365, SaaS tiers, etc.)
- **Authentification centralisée** (SSO)
- **Contrôle d’accès conditionnel**
- **Sécurité renforcée** (MFA, détection de risque)
- **Gestion des appareils et des utilisateurs**
- **Provisioning automatique** (via SCIM, Graph, etc.)



## **🔄 Entra Connect (anciennement Azure AD Connect)**

Permet de **synchroniser un AD local (AD DS)** vers Microsoft Entra ID. 💡 Utilisé dans les environnements **hybrides**.



### **📡 Flux de synchronisation**

- 🔽 **Du local vers le cloud** (sens principal)
- 🔁 **Writeback** (retour vers le local) activable pour :
  - Réinitialisation de mot de passe
  - Écriture des groupes (Group Writeback)
  - 📌 **Requiert une licence P1/P2 ou Microsoft 365 Business Premium**

🔐 Les mots de passe sont **hashés (SHA-256)**, **jamais transmis en clair**.



### **🛠️ Étapes de déploiement d’Entra Connect**

1.  ➕ Ajouter un **suffixe UPN personnalisé** dans l’AD local
2.  🔁 Modifier le **UPN des utilisateurs** pour correspondre au domaine vérifié M365
3.  👤 Créer un **compte de service dédié** (pas d’admin global !)
4.  🧙 Exécuter l’**assistant Entra Connect**
5.  🔐 Saisir les identifiants des tenants locaux et cloud
6.  🌐 Vérifier la **routabilité DNS** du domaine personnalisé

#### 📌 **Bonnes pratiques** :

- Utiliser une **OU temporaire** pour tester la synchro
- Toujours valider dans **Synchronization Service Manager** avant production



### **🕒 Cycle de synchronisation**

- ⏱️ **Automatique toutes les 30 minutes**
- 🔁 Manuelle via PowerShell :
```powershell
Start-ADSyncSyncCycle -PolicyType Initial # Full sync 
Start-ADSyncSyncCycle -PolicyType Delta` # Sync des changements
```

### **🧰 Outils de gestion d’Entra Connect**

- 🔄 **Synchronization Service Manager** : monitoring et diagnostic
- ⚙️ **Synchronization Rules Editor** : création/modification de règles (⚠️ attention aux conflits)
- 🌐 **Web Service Configuration Tool** : accès aux options avancées



### **Options de reconfiguration via l'assistant :**

- **Privacy settings :** Gérer les identifiants de connexions
- **View current configuration :** Votre configuration actuelle
- **Customize synchronization options :** Gestion des filtres de synchronisation
- **Refresh direcory schema :** Mets à jour votre schéma si vous l'avez modifié
- **Configure staging mode :** Mode de préproduction afin de tester une config.
- **Change user sign-in :** Gérer la méthode de connexion de vos utilisateurs
- **Manage federation :** Si vous utilisez l'authentification par AD FS
- **Troubleshoot :** Ouvre une console PowerShell de gestion des problèmes



### **⚠️ À savoir**

- Les **utilisateurs synchronisés** n'ont **pas de licence M365** automatiquement
- Entra ID **≠** AD local : ce sont **deux bases indépendantes** sauf si connectées
  - 1 mot de passe dans chaque base sans SSO
  - Possibilité d’unifier via SSO et/ou Password Hash Sync (PHS)
