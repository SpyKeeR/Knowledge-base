# Services de rôle

## 🖥️ **Présentation générale**

Le rôle **RDS (Remote Desktop Services)** se compose de **six services** appelés **services de rôle**, chacun ayant une fonction précise dans l’infrastructure de bureaux virtuels et d'applications.

Ces services peuvent être :

- Tous installés sur **un seul serveur** (déploiement rapide)
- Répartis sur **plusieurs serveurs dédiés** (déploiement standard)


## 🧱 **Services nécessaires à l'infrastructure**

🛠️ Quatre services sont **indispensables** pour faire fonctionner une infrastructure RDS de base :

1.  **RD Session Host** – Héberge les bureaux ou applications à distance
2.  **RD Connection Broker** – Gère la répartition des connexions utilisateurs
3.  **RD Web Access** – Fournit un portail web pour accéder aux ressources
4.  **RD Licensing** – Gère les licences d'accès client (CAL)

🧩 Deux services complémentaires peuvent s’ajouter selon les besoins, comme **RD Gateway** (accès sécurisé via HTTPS).



## ⚙️ **Déploiement des services de rôle**

🛠️ **Installation**

- Installer le **rôle RDS** sur les serveurs de l'infrastructure
- Sélectionner les **services de rôle souhaités**



🚀 **Choix du déploiement**

- **Standard** : services de rôle répartis sur plusieurs serveurs
- **Rapide** : tous les services hébergés sur un seul serveur (environnement de test ou formation)
