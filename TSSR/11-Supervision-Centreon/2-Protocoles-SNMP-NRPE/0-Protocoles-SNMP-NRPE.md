# 2 - Protocoles SNMP/NRPE

Ce module explore deux protocoles essentiels en supervision :
- **SNMP (Simple Network Management Protocol)** : gestion d'équipements réseau à distance.
- **NRPE (Nagios Remote Plugin Executor)** : exécution de plugins de supervision sur des machines distantes.

👉 Objectif : Comprendre comment surveiller efficacement des équipements sans perturber leur fonctionnement.



## **🧠 Découverte du protocole SNMP**

**SNMP** est un protocole de supervision standardisé utilisé pour :

- Collecter des infos systèmes (CPU, RAM, interfaces, uptime...),
- Déclencher des alertes (via traps),
- Gérer à distance certains paramètres.

💡 **Fonctionnement** :

- Le superviseur (manager) interroge un **agent SNMP** sur l'équipement.
- Réponse avec des données issues d’une **MIB**.



### *📘 MIB et OID : l’annuaire de SNMP**

- **MIB (Management Information Base)** : base de données hiérarchique contenant les infos disponibles.
- **OID (Object Identifier)** : identifiant unique d’un objet dans la MIB (ex : .1.3.6.1.2.1.1.3.0 = uptime).

🧭 Pour chaque donnée à interroger : on a un OID unique ➜ facile à cibler.



### **🔧 Mise en place de SNMP**

🔒 **Sécurité** : version 2c (communauté) ou 3 (auth + chiffrement).

👣 Étapes clés :

- Activer le service SNMP sur l'équipement (switch, routeur, OS…),
- Configurer la communauté SNMP (ex : "public"),
- Ouvrir le port UDP 161 (et 162 pour les traps),
- Définir les OID ou groupes à autoriser.

📍 Sur les équipements réseaux : souvent déjà intégré.

📍 Sur les OS : nécessite installation (paquet snmpd sous Linux).



## **🚀 Introduction à NRPE**

**NRPE** est un protocole développé pour Nagios qui permet de :

- **Lancer à distance** des plugins de supervision,
- **Accéder à des infos locales** (non visibles via SNMP).

👨‍💻 Utilisation typique :

- Surveiller l’espace disque, CPU, services, logs... depuis un serveur Nagios ou Centreon,
- Exécution locale de scripts via le démon NRPE, avec retour vers le superviseur.

🔐 NRPE communique en TCP sur le port 5666 et nécessite que le serveur distant autorise le superviseur dans sa conf.

