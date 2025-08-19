# Les habilitationsSans habilitations bien gérées, c’est comme si tu laissais **tout le monde entrer partout** dans ton GLPI… donc autant dire que c’est **critique** !

Voyons ça ensemble étape par étape, avec un bon mix de théorie et de concret 💪



**🛡️ C’est quoi une habilitation ?**

Une **habilitation**, c’est une autorisation qui donne des **droits à un utilisateur dans une entité**.

Elle est composée de :

- **Un profil** : Définit ce que l’utilisateur peut faire (ex. lire, modifier, supprimer…).
- **Une entité** : Détermine **où** l’utilisateur peut agir.
- **Une récursivité** (oui ou non) : Est-ce que les droits s’appliquent aussi aux **sous-entités** ?

🧠 Un utilisateur peut avoir **plusieurs habilitations**, par exemple :

- Une pour le support technique dans l’entité "Agence Tours".
- Une autre pour un rôle limité dans "Agence Paris".



**🔄 Habilitations statiques VS dynamiques**

GLPI te permet deux approches :

**✅ Habilitations statiques (manuelles)**

Tu vas directement dans la fiche utilisateur :

➡️ **Accueil > Administration > Utilisateurs > Habilitations**

Et là, tu choisis :

- L'entité
- Le profil (technicien, administrateur, etc.)
- La récursivité (coche ou pas la case)

👉 Simple, mais à faire **à la main pour chaque utilisateur**.

**🔁 Habilitations dynamiques (automatiques)**

Ça se fait via des **règles d’affectation d’habilitations** :

➡️ **Accueil > Administration > Règles > Règles d’affectation d’habilitations à un utilisateur**

Tu crées une règle qui dit : **"Si un utilisateur remplit certains critères ➜ alors il reçoit automatiquement telle habilitation."**

🎯 Exemple concret : Tous les utilisateurs dont l'adresse mail **contient "@entreprise.fr"** reçoivent le **profil Utilisateur** dans l’entité "Siège".



**🔍 La récursivité, c’est quoi exactement ? 🌳**

**Sans récursivité** : L’utilisateur **voit uniquement les objets de l’entité** où il est placé.

**Avec récursivité activée** : Il voit aussi les **objets des sous-entités**.

🧪 Contexte simple : Tu gères une entité "France"

- En dessous : "Agence Paris", "Agence Lyon"

Si tu donnes à un utilisateur une habilitation sur "France" **avec récursivité**, il voit les tickets de toutes les agences.

**Sans récursivité**, il ne voit que "France" et **pas les agences**.

**🧭 Création d’une règle dynamique**

Voici les étapes classiques :

1.  **Ajouter une règle**
2.  **Nom** : Donne un titre parlant (ex : "Habilitation utilisateurs internes")
3.  **Opérateur logique** :

    - **ET si tous les critères doivent être vrais**
    - **OU si un seul suffit**

4.  **Actif** : Mets "OUI" sinon la règle sera ignorée
5.  **Description / Commentaire** : Optionnel, mais pratique pour documenter

🎯 Ensuite, tu configures :

- **Critères** : Qu’est-ce qui déclenche la règle ?
  - Ex : email contient "@client-a.com"
  - OU groupe LDAP est "IT Support"
- **Actions** :
  - Définir l’entité
  - Choisir le profil
  - Activer ou non la récursivité

🧠 Tous les utilisateurs concernés par la règle reçoivent les droits **à chaque connexion**.
