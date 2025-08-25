# Groupes - Fichiers

## **📁 /etc/group – Fichier principal de définition des groupes**

Ce fichier contient les informations de base sur **chaque groupe existant** dans le système. Chaque ligne représente un groupe, et elle est structurée en **quatre champs**, séparés par des deux-points : :

👉 **group_name** : le nom du groupe (ex : informatique, sudo, audio)

👉 **password** : champ réservé à un mot de passe de groupe (très peu utilisé). On y trouve souvent un x, ce qui indique que le vrai mot de passe est stocké dans /etc/gshadow.

👉 **GID** : l’identifiant unique du groupe, utilisé par le système (ex : 1000, 27, 1500…)

👉 **user_list** : liste des utilisateurs qui ont ce groupe en **groupe secondaire**, séparés par des virgules (ex : toto,tata,maxime)

⚠️ Un utilisateur ne figurant **pas** dans cette liste peut quand même faire partie du groupe si c’est son **groupe principal**. Dans ce cas, il n’apparaît pas ici (c’est le comportement normal avec la convention **UPG**).



## **🌑 /etc/gshadow – Fichier complémentaire, sécurisé**

Ce fichier stocke les **informations sensibles** liées aux groupes, et complète /etc/group.

Là encore, chaque ligne est composée de **quatre champs**, séparés par des deux-points :

👉 **group_name** : le nom du groupe (doit correspondre à celui du fichier /etc/group)

👉 **password** : le mot de passe chiffré du groupe (quand il y en a un)

👉 **admins** : les utilisateurs autorisés à administrer ce groupe (ajouter/supprimer des membres, via certaines commandes)

👉 **members** : les membres du groupe (comme dans /etc/group, mais potentiellement géré différemment)

### **🔐 Symboles spéciaux dans le champ *password* de /etc/gshadow**

✅ * → Aucune possibilité d’utiliser un mot de passe pour rejoindre ce groupe via newgrp : seul un admin peut ajouter un membre.

⛔ ! → Le groupe est verrouillé. Même si un mot de passe était défini auparavant, il est **désactivé**. Impossible de rejoindre ce groupe avec newgrp, même avec un mot de passe connu.

💡 En pratique, ces champs sont rarement utilisés car la plupart des distributions modernes évitent les mots de passe de groupe pour des raisons de sécurité.

