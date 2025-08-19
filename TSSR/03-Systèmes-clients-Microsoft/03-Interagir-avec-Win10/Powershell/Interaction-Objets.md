# Interaction Objets**🎯 1️⃣️Comprendre les Objets dans PowerShell**

📌 PowerShell manipule des **objets** : utilisateurs, fichiers, imprimantes, etc.

📌 Un objet possède des **propriétés** et des **méthodes**.

📌 Ex : Un ballon a comme propriétés sa **couleur**, **forme**, **taille**.

**🔹 Afficher les informations d’un objet : *get-monballon***

📌 Montre uniquement les propriétés principales.



**🔍 2️⃣️Afficher Toutes les Propriétés d’un Objet**

***get-monballon | select ****  
📌 * permet d'afficher **toutes les propriétés** de l’objet.

**🔹 Sélectionner certaines propriétés spécifiques : *get-monballon | select diamètre, forme, couleur***

📌 Permet de voir uniquement ce qui nous intéresse.



**🔄 3️⃣️Trouver les Commandes Disponibles**

✅ **Lister toutes les commandes disponibles : *get-command***

✅ **Lister uniquement les commandes commençant par "get-" : *get-command get-****

✅ **Rechercher des commandes spécifiques :**

***get-command *printer**** # Recherche tout ce qui concerne les imprimantes  
***get-command new-*user**** # Recherche les commandes liées à la création d'utilisateurs



**⚙️ 4 Modifier un Objet PowerShell**

✅ **Modifier un objet existant : *set-monballon -name "basket" -description "ballon de basket"***

✅ **Créer un nouvel objet : *new-monballon -name "rugby" -forme "ovale" -couleur "blanc" -description "ballon de rugby"***

✅ **Supprimer un objet : *remove-monballon -name "football"***
