# Historique**🏗️ Fonctionnement de l’architecture distribuée**

Un système de supervision distribué repose sur :

- **Serveur central** : interface Web (config + visu), collecte toutes les données.
- **Pollers (ou satellites)** : agents distants répartis sur le réseau, chacun en charge d’un périmètre de supervision, envoie les résultats vers le central.
- **Communication** : les pollers utilisent SSH ou ZMQ pour transmettre configs et données.
- **Résilience** : en cas de coupure réseau, les pollers continuent en autonomie et synchronisent les données à la reconnexion.

🎯 But : répartir la charge & superviser des zones non accessibles directement depuis le central.



**🔄 Raisons d’utiliser une architecture distribuée**

Deux cas typiques :

- **💥 Charge importante** : trop d’hôtes à gérer depuis un seul serveur → besoin de répartir la supervision sur plusieurs moteurs.
- **🌐 Supervision distante** : zones isolées ou sécurisées, inaccessibles depuis le central → un poller local est déployé à proximité.



**🛰️ Autonomie des pollers**

- Chaque **poller fonctionne seul** : il exécute ses vérifications même si le central est inaccessible.
- En cas de coupure, il **stocke les résultats localement**, puis les **remonte dès que la connexion revient**.
- La **configuration est centralisée** : depuis l’interface du serveur central, on affecte les hôtes à un poller et on pousse les confs via **Gorgone**.



**🛠️ Gorgone : service de déploiement**

- **Gorgone** est le service chargé de **pousser les configurations** aux pollers via SSH (ou ZMQ).
- Il se lance sur le serveur central et gère la synchronisation des fichiers de configuration des collecteurs.



**📜 Historique de l’évolution technique**

- **Avant** :
  - Nagios = moteur principal
  - Interface Web = Centreon
  - Transmission via **NDOUtils** (NDOMOD ↔️ NDO2DB) vers base MySQL/PostgreSQL
  - NDOMOD récupérait les données de Nagios et les transmettait via TCP 5669
  - NDO2DB = daemon chargé de les stocker en base
- **Aujourd’hui** :
  - Nagios est remplacé par **Centreon Engine**
  - NDOUtils est remplacé par **Centreon Broker**
    - **broker-module** : récupère les données du moteur
    - **broker-database** : insère les données en BDD + génère les graphes (RRD)
