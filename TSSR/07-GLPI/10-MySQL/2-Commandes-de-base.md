# Commandes de base

## **🧑‍💻 Connexion au moteur MariaDB**

📌 Pour lancer le client en ligne de commande : `mysql -u root -p`

- -u root : on se connecte avec l’utilisateur MariaDB nommé root (ce **n’est pas** l’utilisateur système Linux root)
- -p : demande la saisie du mot de passe

💡 Une fois connecté, le **prompt change** et on entre dans l’environnement de commandes mysql>.



## **🗂️ Afficher les bases de données**

📌 Commande pour lister toutes les bases visibles par l’utilisateur connecté : `SHOW DATABASES;`

- Cette commande affiche **uniquement les bases auxquelles l’utilisateur a accès**
- En tant que root, toutes les bases seront listées



## **🔁 Changer de base de données**

📌 Pour **utiliser** une base en particulier : `USE nom_de_la_base;`
Cela permet de cibler les futures requêtes vers la base concernée.

💡 Par défaut, lors d’une première connexion, on est lié à une base système interne nommée mysql.



## **🧹 Supprimer une base de données**

📌 Pour **supprimer une base et toutes ses données** : `DROP DATABASE nom_de_la_base;`

⚠️ Cette commande est **définitive**.



## **📋 Afficher les tables d’une base**

📌 Une fois connecté à une base : `SHOW TABLES;`

- Liste toutes les tables disponibles dans la base sélectionnée



## **📝 Commandes SQL non couvertes ici**

📌 Les commandes comme `CREATE`, `INSERT INTO` … `VALUES` (ajout) ou `UPDATE` … `SET` … `WHERE`..(modification) ne sont **pas abordées dans ce module**

📌 Elles relèvent davantage de **l’écriture de requêtes SQL**, que de l’apprentissage du moteur MariaDB

