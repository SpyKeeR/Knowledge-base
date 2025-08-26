# Sections/types questions

## **🗂️ Les sections**

Une **section**, c’est comme un *chapitre* dans ton formulaire : elle sert à **regrouper plusieurs questions par thème** (ex : Infos utilisateur / Détails de la demande / Contexte, etc.)

**➕ Ajouter une section** 
- 🏷️ **Nom** : titre de la section
- **Condition d’affichage** (logique conditionnelle 🔁)
  - **Toujours visible** : elle est là dès l’ouverture.
  - **Masquée par défaut, sauf si…** : elle est invisible sauf si une condition est remplie.
  - **Affichée par défaut, sauf si…** : visible au départ, mais peut se cacher selon certaines réponses.
  
👉 Très utile pour éviter d’afficher des sections inutiles selon la situation !



## **❓ Les questions**

Chaque section contient des **questions** que l’utilisateur devra remplir.

**➕ Ajouter une question** 🏷️ 
- **Nom** : Nom interne pour retrouver la question
- **Section** 📦 : Choix de la section dans laquelle la question va apparaître
- **Type** 📋 : Type de réponse attendue (voir plus bas)
- **Champ obligatoire** ✅ : Oui / Non.
- **Description** 📝 : Texte d’aide pour guider la personne qui remplit le formulaire.
- **Condition d’affichage** : Comme pour les sections, tu peux rendre chaque question visible ou non selon les réponses précédentes.



### **🔀 Types de questions**

C’est là que ça devient **super puissant** 

💪 Tu peux capter **des données simples ou complexes**, interroger **le système**, intégrer des **données GLPI**, **LDAP**, etc.

#### **🔎 Exemples de types disponibles :**

| **Type** | **Description** |
|----|----|
| **Acteur** | Sélection d’un utilisateur GLPI |
| **Adresse IP** | Récupère l’IP de l’appareil qui remplit le formulaire |
| **Hostname** | Récupère le nom machine via reverse DNS |
| **Texte** | Champ texte simple (une ligne) |
| **Zone de texte** | Texte long (plusieurs lignes) |
| **Courriel** | Vérifie que l’entrée est une adresse email |
| **Entier / Flottant** | Champs numériques (int ou décimaux) |
| **Date / Heure / Date & Heure** | Sélection de date et/ou heure |
| **Boîtes à cocher** | Choix **multiple** parmi une liste |
| **Boutons radio** | Choix **unique** parmi une liste |
| **Sélection / Sélection multiple** | Menu déroulant simple ou à choix multiples |



#### **🎯 Pourquoi c’est puissant ?**

Tu peux : Créer des formulaires **dynamiques** qui s’adaptent selon les réponses, Guider l’utilisateur avec des champs conditionnels, Récupérer **des infos système automatiquement**, Intégrer tes objets GLPI et ton LDAP.

| **Sélection LDAP** | Recherche dans ton annuaire LDAP (si configuré) |
|----|----|
| **Objet GLPI** | Lien avec un objet GLPI (ex : ordinateur, imprimante…) |
| **Intitulé** | Liste issue des "intitulés" GLPI |
| **Champ caché** | Invisible pour l’utilisateur mais stocke une valeur |
| **Description** | Zone d’infos sans champ à remplir |
| **Tags** | Intègre un tag dans le formulaire (plugin requis) |
| **Type de demande** | Permet de choisir entre une demande ou un incident |
| **Urgence** | Permet de spécifier un niveau d’urgence |
| **Additional fields** | Appelle les champs créés avec le plugin *Champs additionnels* |

