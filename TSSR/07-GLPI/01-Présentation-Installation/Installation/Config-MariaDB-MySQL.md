# Config MariaDB/MySQL⚡️ Cette commande installe à la fois le **serveur de base de données** (mariadb-server) et l’outil **client en ligne de commande** (mariadb-client) pour interagir avec lui.

**🔐 Sécurisation de MariaDB**

Une fois installé, il faut sécuriser l’installation avec un script intégré : *mysql_secure_installation*

Ce script est **interactif** : il te pose plusieurs questions. Voici les réponses recommandées pour une installation de base sécurisée :

| **Étape** | **Explication rapide** | **Réponse conseillée** |
|----|----|----|
| Changer le mot de passe de root MySQL ? | Ce n’est **pas** le root Linux, mais celui de MariaDB. | Oui |
| Supprimer les utilisateurs anonymes ? | Oui, sinon n’importe qui peut se connecter sans login. | Oui |
| Interdire l'accès distant à root ? | Oui, pour éviter qu’on se connecte en root à distance. | Oui |
| Supprimer la base de données de test ? | Oui, inutile et peu sécurisée. | Oui |
| Recharger les tables de privilèges ? | Oui, pour appliquer tous les changements faits. | Oui |



**✅ Test de connexion à MariaDB**

Une fois sécurisé, tu peux tester la connexion avec : *mysql -u root -p*

Cela ouvre le **client MariaDB**, un genre de console où tu peux taper des **requêtes SQL**. Tape ton mot de passe root MySQL quand demandé.



**🛠️ Création d’une base de données pour GLPI**

Dans le prompt MariaDB (le client), crée la base de données GLPI : *CREATE DATABASE glpidata;*

Le point-virgule ; est indispensable à la fin de chaque commande SQL !

💡 *Si tu veux gérer plusieurs versions de GLPI, tu peux créer une base du style :* *CREATE DATABASE glpi_10;*



**👤 Création d’un utilisateur et attribution des droits**

Toujours dans le client MySQL, tu peux maintenant créer un utilisateur **et lui donner tous les droits** sur la base :

*GRANT ALL PRIVILEGES ON glpidata.* TO 'glpiuser'@'localhost' IDENTIFIED BY 'MotDePasseFort';*

- glpidata.* : donne accès à **toutes les tables** de la base glpidata
- 'glpiuser'@'localhost' : l’utilisateur n’aura le droit de se connecter **que localement**
- IDENTIFIED BY : définit un mot de passe (il sera **chiffré automatiquement**)

⚠️ **Attention** : ici, glpiuser est un **utilisateur MariaDB**, pas un utilisateur système Linux !



**🔄 Rechargement des privilèges**

Si tu modifies des droits sans passer par une commande GRANT, tu peux forcer le rechargement avec : *FLUSH PRIVILEGES;*

Mais pas nécessaire après GRANT, qui recharge automatiquement.

lundi 19 mai 2025

10:37
