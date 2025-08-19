# Sauvegarde BDD🗃️ **Sauvegarde des Bases de Données**

Une **base de données (BDD)** est un **ensemble structuré de données** conçu pour être **consulté et mis à jour efficacement**. La **sauvegarde régulière** de ces données est essentielle pour garantir leur **sécurité**, leur **intégrité** et leur **disponibilité**.



🧠 **Principes fondamentaux**

- 📚 **Gérée via un SGBD (Système de Gestion de Base de Données)**
- 🧩 **Types de BDD** :
  - 🧮 **Relationnelles** : PostgreSQL, MySQL, Oracle…
  - 🌐 **NoSQL** : MongoDB, Cassandra, etc.
- 🌍 **Déploiement** :
  - 🖥️ **Localement** (serveur dédié)
  - ☁️ **Dans le cloud**, selon la complexité du projet



💾 **Méthodes de Sauvegarde**

Il n'existe **pas de méthode unique** : chaque SGBD a ses **propres outils** de sauvegarde. Voici des exemples concrets :

1.  🏢 **SQL Server >** 🔧 Sauvegarde via **SQL Server Management Studio** (interface graphique)



2.  🐬 **MySQL >** 📝 Création d’un **fichier dump.sql** à l’aide de la commande :  
    mysqldump -u username -p "password" [options] database_name table_name > dumpfilename.sql



3.  🧱 **Oracle >** 🛠️ Utilisation de **RMAN (Recovery Manager)** pour effectuer une sauvegarde :  
    rman target /  
    RMAN> backup incremental level 0 section size 512m database plus archivelog;



✅ **Avantages**

- Disposer d’un dump à jour permet de restaurer rapidement l’état antérieur de la base et de minimiser les pertes
- Conserver des copies datées aide à suivre l’évolution des données au fil du temps.
- Les dumps peuvent être dupliqués facilement et ainsi être en adéquation avec les préconisations ANSSI sur la règle 3-2-1

❌ **Inconvénients**

- Consommation de ressources et temps d'exécution élevés (si base volumineuse)
- Gestion complexe si le backup produit un fichier de grande taille (problèmes de stockage, de transfert, etc.)



🛡️ **Pourquoi sauvegarder ?**

- 🔒 **Sécurité des données**
- 📈 **Prévention contre la perte ou la corruption**
- 🔁 **Capacité de restauration rapide en cas d’incident**
