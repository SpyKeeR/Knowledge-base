# Manipulations des donnéesImagine un classeur 📁 avec des intercalaires, où chaque intercalaire est une **colonne** et chaque feuille entre deux intercalaires est une **ligne**. C’est exactement ça une table en base de données : **Colonnes** = attributs (ex: prénom, nom, âge) / **Lignes** = enregistrements (chaque personne, par exemple)

**🔍 La commande SELECT : récupérer des données**

SELECT sert à demander des données dans une ou plusieurs colonnes d’une table.

- SELECT * FROM client = afficher toutes les colonnes et tous les enregistrements / SELECT nom, ville FROM client = afficher uniquement la colonne nom et ville



**⚖️ Filtrer avec WHERE : précision dans la recherche**

La clause WHERE permet d’affiner ta recherche. **Exemple :** SELECT prénom, nom FROM client WHERE ville = 'Caen'

Ça affiche les clients qui habitent à Caen. Note : les valeurs texte sont entre apostrophes ou guillemets.

Tu peux utiliser des opérateurs : = égal / <, >, <=, >= inférieur, supérieur, etc. / != ou <> différent

IS NULL pour les valeurs non définies (null) / IS NOT NULL / <=> Opérateur permettant la comparaison des valeurs NULL.



**🔗 Combiner les critères avec AND, OR, NOT**

AND = toutes les conditions doivent être vraies / OR = au moins une condition est vraie / NOT = inverse la condition (exclut)

**Exemple complexe :** SELECT prénom, nom FROM client WHERE id >= 2 AND (ville = 'Vouvray' OR ville = 'Vernon')

Ici, tu cherches les clients avec ID ≥ 2 **et** qui habitent soit Vouvray **ou** Vernon. Les parenthèses permettent de grouper les conditions.



**🔢 Trier avec ORDER BY**

ORDER BY sert à trier les résultats par une ou plusieurs colonnes, soit en ordre **ascendant** (par défaut), soit **descendant**.

**Exemple :** SELECT prénom FROM client ORDER BY âge DESC, nom ASC (Trie par âge décroissant, puis nom croissant)



**🚦 Limiter le nombre de résultats avec LIMIT et OFFSET**

- LIMIT restreint le nombre de lignes retournées / OFFSET permet de sauter un certain nombre de lignes avant de commencer à afficher

**Exemple :** SELECT prénom FROM client ORDER BY âge LIMIT 2 OFFSET 1 / Ça récupère 2 résultats, mais en commençant à la 2ᵉ ligne (car offset = 1).

**🔢 Compter avec COUNT**

COUNT compte le nombre d’enregistrements non nuls dans une colonne. **Exemple :** SELECT COUNT(âge) FROM client Ça donne le nombre de clients qui ont un âge renseigné (exclut les NULL).



**✏️ Alias : renommer les colonnes dans le résultat**

Tu peux renommer une colonne dans le résultat pour plus de clarté. **Exemple :** SELECT prénom AS firstname FROM client



**📊 Fonctions d’agrégation : calculer sur une colonne**

Les fonctions courantes : MAX() : valeur maximale / MIN() : valeur minimale / SUM() : somme des valeurs / AVG() : moyenne des valeurs

Elles ignorent les valeurs NULL. / **Exemple :** SELECT AVG(âge) FROM client / Donne la moyenne des âges des clients.
