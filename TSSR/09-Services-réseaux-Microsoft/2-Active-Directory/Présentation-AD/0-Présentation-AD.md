# **🏛️ Qu’est-ce qu’Active Directory ?**

Active Directory (AD) est **le cœur de la gestion réseau sous Windows**. Il centralise :

- 👤 les utilisateurs 
- 💻 les ordinateurs
- 🖨️ les ressources (imprimantes, partages...)

👉 Objectif : **authentifier, organiser, sécuriser et simplifier la gestion** de tout ça dans un domaine Windows.



## **🧰 Fonctions principales d’Active Directory**

Avec AD, on peut :

🔐 Gérer les mots de passe, appartenances aux groupes, stratégies de sécurité

👥 Organiser les objets selon des unités logiques (OU)

🛠️ Utiliser des consoles comme la **MMC** pour tout piloter

⚙️ Servir de base à d’autres services Microsoft (GPO, Exchange, SCCM…)



## **🌐 Protocoles sur lesquels repose Active Directory**

### **📍 DNS — Résolution de noms & localisation**

Permet de lier noms d’hôtes ↔ adresses IP. Fondamental pour retrouver les serveurs AD et services réseau.

### **📚 LDAP — Accès à l’annuaire**

Permet de **parcourir l’annuaire sous forme arborescente** : utilisateurs, groupes, droits, etc.

💡 C’est comme un "explorateur de données" du domaine.

### **🎟️ Kerberos — Authentification sécurisée**

Fonctionne avec des *tickets d’accès*.

🧾 L’utilisateur prouve son identité une fois → reçoit un ticket → accède aux services sans se réauthentifier.

🔐 Sécurisé, rapide, intégré dans Windows.



## **🏛️ Domaine Active Directory : définition**

Un **domaine AD** = **ensemble de machines (clients/serveurs)** partageant une **base de données commune** et **administré centralement** via des règles et politiques unifiées.

👑 Le **contrôleur de domaine (DC)** est **le cœur du domaine** : Gère l’authentification, Héberge la base AD et Permet la gestion des objets (utilisateurs, PC, imprimantes…)



### **🧩 Composants clés d’un domaine AD**

- **Clients** : postes utilisateurs connectés au domaine
- **Serveurs** : machines avec rôles ou services (ex. fichiers, appli)
- **Contrôleur de domaine (DC)** : point central avec base AD, LDAP, Kerberos
- **Objets AD** : utilisateurs, groupes, unités organisationnelles (OU), machines…

🧠 AD ne contient pas les objets physiques, mais les **infos nécessaires à leur identification, gestion et authentification**.

