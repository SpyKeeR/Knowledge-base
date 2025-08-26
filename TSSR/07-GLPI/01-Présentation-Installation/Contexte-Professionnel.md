# Contexte Professionnel**🏢 Contexte simulé**

Nous évoluons dans une **mise en situation professionnelle**, autour de l’entreprise fictive :

- **Nom** : *Olympus*
- **Activité** : Gestionnaire de patrimoine et réseau informatique
- **Nom de domaine** : olympus.gr



**🌐 Architecture réseau**

Le réseau est virtualisé via un **switch logiciel (VMNet18)** et utilise le **plan d’adressage suivant** :

- **Plage IP** : 192.168.1.0/24

Matériel et services présents sur ce réseau :

- 🖥️ **Contrôleur de domaine Active Directory (Windows Server)  **
  → Contient les **comptes et groupes** d’utilisateurs de l’entreprise

- 🐧 **Serveur Linux GLPI  **
  → Contient :

  - Serveur Web (Apache2 ou équivalent)
  - Service de base de données (MySQL/MariaDB)
  - Application GLPI
- 💼 **Poste stagiaire / administrateur  **
  → Poste client raccordé au réseau (souvent Windows 10)  
  → Utilise **navigateur web**, **SSH** (pour Linux) ou **RDP** (pour Windows)



**🔐 Sécurité réseau**

Le réseau est **isolé du reste du monde** grâce à un **pare-feu logiciel (PFSense)** :

- Sert essentiellement de **routeur** pour cloisonner le réseau
- Permet d’éviter les interférences avec d’autres réseaux locaux (domicile, salle de formation, etc.)



**🎯 Objectif de la mission**

En tant qu’**administrateur système junior**, la mission est de :

1.  **Déployer GLPI** sur le serveur Linux
2.  **Assurer l’hébergement** de l’application en local, chez Olympus
3.  **Configurer l’authentification** via l’**Active Directory**

    - **Récupération des comptes utilisateurs du domaine**

4.  **Réaliser un inventaire manuel** du parc informatique
5.  **Gérer les tickets d’incident / demandes d’intervention**

    - **Suivi, traitement global et automatisé**

6.  **Mettre en place un inventaire automatisé** avec un **plugin GLPI : FusionInventory**
