# Détails

Les **ACL (Access Control Lists)** permettent de gérer les **permissions sur les fichiers et dossiers** en entreprise. Voici comment elles fonctionnent et comment les configurer efficacement.


## **📌 1️⃣️ Liste de contrôle d'accès discrétionnaire (DACL)**

🔹 **La DACL contient** :

✔ Les **groupes locaux** créés par l’administrateur (ex : *Voisins*).

✔ Les **groupes prédéfinis** (ex : Utilisateurs bureau à distance).

✔ Les **entités de sécurité intégrées** (ex : *Administrateurs*).

❌ **Bonne pratique** : **Ne pas ajouter directement des utilisateurs individuels** dans la DACL !

👉 Il est **préférable d’attribuer des permissions aux groupes**, puis d’ajouter les utilisateurs à ces groupes.



## **🛠 2️⃣️ ACE (Access Control Entries) : Les droits d’accès**

Chaque entrée dans la DACL est un **ACE**, qui définit **quelles actions sont autorisées pour un groupe**.

📜 **Les niveaux d’accès standard** :

✅ **Lecture** 📖 : Lire, exécuter des programmes, voir le contenu.

✅ **Modification** ✍️ : Modifier, créer, supprimer des fichiers/dossiers.

✅ **Contrôle total** 🔧 : Toutes les permissions + gestion des ACL.

🔹 **Les permissions sont cumulatives** : Un utilisateur appartenant à plusieurs groupes obtient le **plus haut niveau d’accès** parmi ses groupes.



## **🎯 3️⃣️ Configurer les ACL dans l’onglet Sécurité**

💻 **Via l’interface graphique (GUI)** :

📂 **Clic droit** sur un fichier/dossier → **Propriétés** → **Onglet Sécurité**

➡ **Bouton "Modifier"** pour changer les permissions.

➡ **Bouton "Avancé"** pour configurer des autorisations spéciales.

