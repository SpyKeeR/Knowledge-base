# Détails et fonctions

## 🛠️ **🔍 Caractéristiques techniques de GLPI**

- 💻 C’est une **application Web** : tu y accèdes depuis un navigateur (Chrome, Firefox…)
- 🧠 Fonctionne en **PHP** avec une base **MySQL ou MariaDB**
- 🌍 Disponible en **plusieurs langues** (dont le français évidemment)
- 🔌 Tu peux lui ajouter **plein de plugins** (formulaires, authentification, télédistribution, etc.)
- 💡 **Compatible Windows et Linux** côté serveur comme côté agents
- 🔄 Scalable : fonctionne aussi bien dans **un collège avec 30 PC** que dans **un ministère avec 30 000 postes**
- 🚀 Première version en 2003, aujourd’hui maintenue par **TECLIB** (ils assurent aussi le support pro)



## 🧱 **⚙️ Architecture de base d’un serveur GLPI**

Voici ce qu’il faut pour faire tourner GLPI en local ou sur un serveur :

1.  **Un serveur Web >** Apache (le plus courant) ou IIS si tu es sur Windows Server
2.  **Un interpréteur PHP >** GLPI est écrit en PHP, donc il faut un moteur PHP installé
3.  **Une base de données MySQL/MariaDB**

    - **Tous les objets (tickets, users, matériels…) sont stockés dedans**
    - **Un user SQL avec des droits sur la base GLPI est nécessaire pour que l’interface fonctionne**

🎯 C’est donc une **application Web LAMP (Linux, Apache, MySQL, PHP)** classique, mais qui peut aussi tourner sur un serveur Windows si besoin.



## 🎯 **Fonctionnalités clés de GLPI**

- 🗂️ **Gestion de parc informatique** → Gérer les postes, serveurs, imprimantes, téléphones, logiciels installés. Tout est structuré, avec fiches et relations entre éléments.
- 📦 **Inventaire** → Collecte automatique ou manuelle des équipements + informations détaillées (RAM, OS, IP, etc.)
- 🎫 **Gestion des tickets** (Incidents et demandes) → Interface pour que les utilisateurs déclarent un problème, et que les techniciens le résolvent.
- 🔥 **Gestion des problèmes** → Identifier les causes profondes des incidents récurrents, et les éradiquer.
- 🔄 **Gestion des changements** → Encadrer les modifications importantes (ex : mise à jour d’un serveur) avec des étapes de validation.
- ⏱️ **SLAs/OLAs** → Engagements de qualité de service (temps de réponse, temps de résolution). Très utile pour prioriser les tickets. *Résoudre les pannes en moins de 2h.*
- 📚 **Base de connaissances** → FAQ interne avec des procédures, des astuces, pour éviter de réinventer la roue à chaque ticket.
- 📊 **Statistiques & rapports** → Pour suivre l’activité, voir les équipes surchargées, identifier les équipements à renouveler, etc.


