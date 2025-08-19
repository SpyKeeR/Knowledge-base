# 3 - Présentation de Centreon🔍 **Objectifs du module**

Ce module vise à poser les bases de l’utilisation de **Centreon**, une solution française open source de supervision IT. L’idée est de comprendre d’où elle vient, pourquoi elle est utilisée, et comment l’installer dans un environnement de production ou de test.

📌 Objectifs principaux :

- Comprendre **l’origine et l’évolution** de Centreon 🏗️
- Connaître ses **cas d’usage** et sa **valeur ajoutée** 🧩
- Apprendre les **étapes clés de l’installation** ⚙️



📜 **Historique de Centreon**

À l’origine, Centreon est né comme **interface graphique pour Nagios**, pour pallier les limites de son interface en ligne de commande.

Progressivement, il a évolué pour devenir une **solution de supervision complète**, tout en gardant une compatibilité avec Nagios et ses dérivés (Shinken, Icinga…).

📈 Évolution :

- Initialement nommé **Oreon** (projet open source).
- Refonte majeure vers 2005-2010, avec l’ajout d’un moteur de collecte, d’un système de base de données, et de dashboards avancés.
- Aujourd’hui, Centreon est une **plateforme de supervision modulaire**, utilisée en entreprise pour surveiller des parcs de serveurs, équipements réseau, applications métier, etc.



🖥️ **Installation de Centreon**

L’installation de Centreon est une étape critique : elle conditionne la stabilité et les performances de la supervision. Elle implique plusieurs briques logicielles à maîtriser.

📌 Composants clés :

- **Centreon Web** : interface utilisateur (PHP)
- **Centreon Engine** : moteur de supervision (fork de Nagios)
- **Centreon Broker** : collecte et export des données
- **Base de données** : stocke l’historique et les confs (MySQL/MariaDB)
- **Serveur Apache + PHP + SNMP utils + Perl modules**, etc.

💡 Mode de déploiement courant : **OS CentOS / AlmaLinux / RHEL** (installation via dépôt officiel Centreon ou ISO préconfigurée)
