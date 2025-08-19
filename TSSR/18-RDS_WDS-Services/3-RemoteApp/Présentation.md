# Présentation🖥️ **Principe de RemoteApp**

• La **publication d’applications** est un service intégré à **Remote Desktop Services (RDS)**

• La solution **RemoteApp** permet aux utilisateurs de **lancer une application distante** comme si elle était exécutée localement 🪟✨

• L’application tourne **côté serveur RDS**, seul l’affichage est transmis au poste client 💻📡



⚙️ **Fonctionnement technique**

• L’utilisateur clique sur l’icône → l’application s’ouvre comme une application locale

• L’exécution réelle a lieu sur le **serveur**, pas sur la machine de l’utilisateur 🖥️🔁

• Le **client léger** reçoit uniquement l’interface graphique de l'application 🪟🧠



✅ **Avantages de la publication d'applications**

• **Mise à jour centralisée** : une seule mise à jour sur le serveur = tous les utilisateurs à jour 🔄🧩

• **Maintenance simplifiée** : pas besoin d’intervenir sur les postes clients 🧹💼

• **Réduction des coûts matériels** : les clients peuvent être plus légers (thin clients) 🪙💻



🛠️ **Configuration des applications publiées**

1.  **Installer l'application** sur le serveur RDS 🧑‍🔧Install-WindowsFeature -Name Remote-Desktop-Services
2.  **Configurer l’accès** via le **Gestionnaire des Services Bureau à distance** ⚙️
3.  **Créer une collection d'applications** pour organiser les logiciels accessibles par les utilisateurs 📂🧑‍💻
