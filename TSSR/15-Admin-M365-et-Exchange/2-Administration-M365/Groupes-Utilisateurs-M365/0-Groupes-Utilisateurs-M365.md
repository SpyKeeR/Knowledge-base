# Groupes/Utilisateurs M365

## 🏠 **Écran d’accueil & Console Microsoft 365**

- 🎛️ Accès à toutes les applications et panneaux d'administration
- 🔧 Utilisation de l’**Admin Center** comme point central
- 🔗 Accès aux centres d’administration spécialisés : Teams, SharePoint, Exchange Online, etc.

## 👤 **Utilisateurs Microsoft 365**

• Un utilisateur Microsoft 365 est propre à l’environnement cloud (**Azure AD**)

### 👥 **Types d’utilisateurs** :

  ○ Utilisateur *cloud only*

  ○ Utilisateur *synchronisé* (via Azure AD Connect depuis un AD local)

  ○ 🔒 Mot de passe synchronisé ou non selon la configuration

### 🌍 **Choix du domaine** :

  ○ Important pour l’adresse mail de l’utilisateur (ex : @entreprise.com)

### 🔑 **Gestion du mot de passe** :

  ○ Laisser Microsoft générer un mot de passe sécurisé

  ○ OU définir manuellement un mot de passe

### 📌 **Méthodes de création** :

• ➕ Individuellement via l’Admin Center

• 📂 Par lots via un fichier .CSV

• 🖥️ Via PowerShell

• 🔄 Par synchronisation avec Azure AD Connect V2



## 🛡️ **Rôles et délégations**

- 🧍‍♂️ Utilisateur simple (sans privilèges)
- 👑 Administrateur général
- ⚙️ Administrateur personnalisé
- 🎯 Attribution via **RBAC** (Role-Based Access Control)  
  → Permet de déléguer précisément les permissions en fonction du rôle

🔄 La stratégie de délégation doit être **structurée et réfléchie**

### 🏷️ **Groupes Microsoft 365**

• 🤝 Collaboration entre utilisateurs

• 🔁 Groupe dynamique possible (via règles, ex. LDAP)

• 📥 Boîte mail Outlook partagée

• 📚 Bibliothèque SharePoint

• 📝 Bloc-notes OneNote partagé

### 📨 **Types de groupes** :

#### 📩 **Groupe de distribution**

  → Envoi d’un mail à plusieurs utilisateurs d’un seul coup

#### 🛡️ **Groupe de sécurité**

  → Sert à attribuer des droits d’accès à des ressources

  → Facilite la gestion centralisée des accès

## 👥 **Rôles dans un groupe M365** :

### 👑 **Propriétaire**

  ○ Peut y en avoir plusieurs

  ○ Contrôle total

- ajouter/supprimer des membres,
- modifier paramètres,
- gérer ressources du groupe

### 👤 **Membre**

  ○ Ajouté par le propriétaire

  ○ Accède aux fichiers, calendrier, discussions, etc.

### 🌐 **Invité**

  ○ N’appartient pas à l’organisation (adresse externe)

  ○ Peut collaborer dans le groupe (si autorisé) :

- lire,
- commenter,
- modifier selon les droits

