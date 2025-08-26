## 🧭 **Où ça se passe ?** ↳ **Accueil \> Outils \> Data Injection \> Importation du fichier**

Ce plugin est **hyper pratique** pour importer des **données en masse** dans GLPI à partir d’un fichier (CSV par exemple). Tu peux t’en servir pour ajouter ou mettre à jour des matériels, utilisateurs, logiciels, etc., **en évitant de tout faire à la main**.


## 🧩 **Ajout d’un modèle d’importation**

Avant d’importer un fichier, tu dois créer un **modèle**. Ce modèle sert à dire à GLPI **comment lire ton fichier**.

### 👤 **Paramètres du modèle :**

- **Nom** : Donne-lui un nom clair, genre "Import PC mai 2025"

- **Visibilité** :
  - *Privé* : visible que pour toi
  - *Public* : visible pour tous les utilisateurs ayant les droits
  - *Entité / Sous-entités* : utile si ton GLPI gère plusieurs sites

📂 **Type de données à importer :** Tu dois choisir la **table principale** sur laquelle tu veux injecter des données (Ordinateurs, Utilisateurs, Moniteurs, etc.).

⚙️ **Comportement de l'import** : 
- **Création des lignes** : Active si tu veux créer de nouveaux objets 
- **Mise à jour des lignes** : Active si tu veux mettre à jour ceux existants

🧠 **Exemple concret :** Tu veux importer une liste de 100 postes client d’une ancienne base Excel. Tu crées un modèle qui cible la table **Ordinateurs**, et tu choisis "Créer les lignes".

 

## 🛠️ **Options avancées du modèle**

Tu peux aller plus loin avec des réglages fins :

- **Ajouter des intitulés** : Pour créer de nouvelles valeurs (ex : lieux, marques…) non existantes dans GLPI

- **Format des dates** : Si tu bosses en français, souvent **JJ/MM/AAAA**

- **Format des décimaux** : Virgule (,) ou point (.)

- **Mise à jour des champs existants** : Précise si certains champs doivent être écrasés

 

📄 **Préparer le fichier d'import**

Tu dois avoir un **fichier bien formé**, souvent au format CSV.

🔧 Paramètres importants : 
- **Présence d’un en-tête** : coche si ton fichier a une première ligne avec les noms des colonnes
- **Délimiteur** : souvent ; ou ,
- **Encodage** : UTF-8 recommandé, mais GLPI peut faire une détection automatique
- Ensuite, tu le **charges dans GLPI** via l’interface du plugin.

 

### 🔁 **Correspondance des champs**

C’est l’étape **clé** : tu dois dire à GLPI **à quoi correspond chaque colonne** de ton fichier.

💡 Conseil : Essaie d’utiliser les **mêmes noms** de colonnes que ceux des champs GLPI dans ton fichier → la correspondance sera **automatique**.

Et tu peux sauvegarder un **exemple** dans la base de connaissances pour les prochains imports.

 

### 🚀 **Utilisation du modèle**

Une fois ton modèle prêt : 
- Tu le sélectionnes dans le plugin, Tu choisis le fichier contenant les données,
- Tu indiques le **champ de liaison** (ex : nom, numéro d'inventaire) pour que GLPI sache **quoi mettre à jour ou créer,**
- Tu lances l’import \> GLPI va te faire un petit **récap** de ce qui a été fait : lignes créées, modifiées, ou ignorées.




