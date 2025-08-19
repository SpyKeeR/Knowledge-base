# Relations / Jointures**🔑 Clé primaire (Primary Key) : l’élément unique d’une table**

- **C’est quoi ?  **
  Une clé primaire, c’est une colonne (ou un groupe de colonnes) qui identifie **de manière unique** chaque enregistrement dans une table.  
  Exemple : un ID numérique auto-incrémenté, genre id_client = 1, 2, 3...

- **Pourquoi c’est important ?  **
  Ça évite les doublons, garantit qu’on a bien un seul et unique enregistrement par clé.

- **Clé primaire multiple ?  **
  On peut utiliser plusieurs colonnes ensemble (ex : prénom + nom + date de naissance), mais ça reste risqué car deux personnes peuvent quand même avoir les mêmes infos.  
  Bref, c’est moins fiable que d’avoir un ID numérique unique.

- **Astuce utile** :  
  L’ID numérique est super pour l’indexation, c’est plus rapide pour la base que de chercher dans des noms ou textes.



**🔗 Clé étrangère (Foreign Key) : le lien entre les tables**

- **C’est quoi ?  **
  Une clé étrangère, c’est une colonne dans une table qui fait référence à une clé primaire dans une autre table.  
  Exemple : dans la table achat, la colonne id_client est une clé étrangère qui pointe vers id dans la table client.

- **Pourquoi c’est utile ?  **
  Ça garantit l’intégrité des données : pas possible d’insérer un achat avec un id_client qui n’existe pas dans la table client.

- **Contraintes d’intégrité  **
  La base de données vérifie automatiquement ces relations et empêche les erreurs (comme des ids qui n’existent pas). C’est la clé étrangère qui fait ce contrôle.



**🔄 Relation entre tables & jointures (JOIN)**

- Pour récupérer des infos de plusieurs tables liées, on utilise **les jointures**.
- La jointure va créer une **table virtuelle** qui réunit les données des deux tables sur une colonne commune (ex : la clé primaire d’une table avec la clé étrangère de l’autre).

**Exemple de jointure simple** (INNER JOIN) : SELECT * FROM achat INNER JOIN client ON client.id = achat.id_client;

Cette requête va chercher tous les achats avec les infos clients correspondantes. **Pourquoi INNER JOIN ?** Elle sélectionne seulement les enregistrements où les clés correspondent dans les deux tables.



**📝 En pratique : retrouver les jeux achetés par un client**

Imaginons que tu veux trouver tous les jeux achetés par "Yan" : SELECT achat.nom_jeu, client.prenom, client.nom FROM achat INNER JOIN client ON client.id = achat.id_client WHERE client.prenom = 'Yan';

- Joindre les tables achat et client sur leurs clés liées
- Filtrer pour ne prendre que les clients dont le prénom est "Yan"
- Afficher le nom du jeu acheté et les infos client
