# **🧾 Qu'est-ce qu'une autorisation NTFS ?**

Les autorisations **NTFS** (New Technology File System) permettent de **contrôler l'accès aux fichiers et dossiers** sur des volumes NTFS.

👉 Gérées via l'onglet **Sécurité** (clic droit > Propriétés > Sécurité)

👉 Elles permettent d’**accorder ou de restreindre** des actions (lecture, écriture, suppression...)



## **🧱 Deux niveaux d’autorisations**

🔹 **Autorisations de base** : Lecture, Liste de dossier, Lecture/Exécution, Écriture, Modification, Contrôle total

🔹 **Autorisations avancées** : Création de fichiers/dossiers, Suppression, Lecture des autorisations, Appropriation, Écriture d’attributs étendus…

📌 Les règles peuvent **accorder ou refuser un accès**.

📌 En cas de **refus explicite**, il l’emporte toujours sur les autorisations accordées.



## **🔁 Héritage et propagation**

Les autorisations se **propagent automatiquement aux objets enfants** (fichiers/sous-dossiers) via l’**héritage** :

✅ Pratique pour gérer les droits à grande échelle depuis un répertoire racine

❗ Mais attention : il est possible de **rompre l’héritage** sur un dossier spécifique si besoin

➡️ Déplacement/Copie :

- **Même volume** = conserve les autorisations
- **Volume différent** = reprend les autorisations du dossier de destination



## **🔄 Cumul des autorisations**

Un utilisateur peut hériter de plusieurs autorisations s’il appartient à **plusieurs groupes** :

🎯 **Le cumul des permissions s’applique** (ex : Lecture + Écriture = Modification)

🚫 Mais si **une règle refuse un droit**, elle l’emporte quoi qu’il arrive

👉 On privilégie toujours l’**application des autorisations aux groupes**, pas aux comptes individuels



## **📏 Règles et bonnes pratiques**

- ✅ Donner des droits **par groupe**, pas directement aux utilisateurs
- ✅ Préférer l’**ajout de droits explicites**, les refus doivent rester rares
- ✅ Toujours vérifier l’**héritage** avant d’attribuer manuellement des autorisations
- ✅ Documenter les droits sensibles (ex : partages RH, dossiers de direction)

