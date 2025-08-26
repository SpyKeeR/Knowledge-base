# CiblesUne **cible**, c’est l’**action automatique** que GLPI doit effectuer **à partir des réponses du formulaire**.

En gros : tu construis un beau formulaire 👉 il est rempli 👉 une cible agit 👉 un **ticket**, un **changement** ou un **problème** est généré.



**📋 Types de cibles disponibles :**

**Ticket cible** (le plus courant) 🧾 / **Changement cible** 🔁 / **Problème cible** 🛠️



**➕ Ajouter une cible**

**Champs à remplir : Nom** 🏷️ : Donne un nom clair à ta cible (ex : “Demande de matériel”) / **Type** 📂 : Choisis le type d’objet à créer (ticket / changement / problème)



**🔧 Propriétés d’une cible de type *ticket***

Tu vas configurer **tout ce que contient le ticket final** :

**📝 Contenu du ticket : Titre du ticket** : souvent calqué sur le nom de la cible. / **Description** : récupère **toutes les réponses du formulaire**, **personnalisable** avec les **variables** (ex : ##nom_utilisateur##).

**🧩 Propriétés techniques**

| **Propriété** | **Description** |
|----|----|
| **Entité de destination** | Où va le ticket (utile si plusieurs entités dans GLPI) |
| **Gabarit de ticket** | Permet d'appliquer un modèle de ticket |
| **TTR / SLA / OLA** | Permet d’assigner un temps de traitement / accord de service |
| **Source de la demande** | Email, téléphone, formulaire, etc. |
| **Request type** | Demande ou incident |
| **Type** | Type d’intervention ou de ticket |
| **Associated elements** | Lier un élément GLPI (ex : PC, imprimante) |
| **Catégorie ITIL** | Affecter une catégorie (matériel, logiciel, etc.) |
| **Lieu** | Affecter un lieu spécifique |

**👤 Acteurs**

- **Demandeur** : qui fait la demande
- **Observateur** : personne à notifier
- **Attribué à** : technicien ou groupe responsable

**🛑 Validation**

- Tu peux activer un workflow de **validation**.
- Exemple : si la demande est “Demande de licence payante” → *validation par le responsable avant création du ticket*.



**🔁 Conditions de création**

Tu peux aussi automatiser ou bloquer la création selon les réponses :

| **Condition** | **Effet** |
|----|----|
| **Toujours généré** | La cible est toujours déclenchée |
| **Désactivé sauf si...** | Elle ne s’active **que si une condition** est remplie |
| **Généré sauf si...** | Elle est active **sauf si une condition** est remplie |

Super utile pour faire des formulaires "multiples" où seule une partie des demandes génère des tickets !
