# Recherches fichiers / findLa commande find peut sembler complexe au début, mais elle est ultra puissante et flexible une fois maîtrisée. Sa syntaxe de base se compose de :

1.  **Le chemin** de départ de la recherche (dossier ou répertoire). / **Les critères** de recherche. / **Les actions** à effectuer sur les résultats.

Rechercher tous les fichiers dont le nom commence par "T" et contient "EL" : *find . -name "T*EL*"*



**🔄 Recherche récursive**

**find** est **récursif par défaut**, ce qui signifie qu’il va chercher dans tous les sous-dossiers sans qu'il soit nécessaire de spécifier cette option. C’est l'un de ses atouts majeurs.



**🎯 Critères de recherche**

Voici les critères les plus utilisés dans find :

- **-name** : Recherche un fichier par son nom. Utilise des métacaractères comme * pour la correspondance. / **-user** : Recherche par propriétaire (nom d'utilisateur).
- **-group** : Recherche par groupe propriétaire. / **-type** : Recherche par type de fichier (fichier classique, dossier, lien symbolique, etc.).

**Exemple :** Recherche des dossiers contenant "test" dans leur nom : *find . -type d -name "*test*"*



**📏 Recherche par taille et date**

Tu peux affiner la recherche en fonction de la **taille** ou de la **date de modification** des fichiers.

- **-size** : Recherche par taille (ex : -size +500k pour les fichiers de plus de 500 Ko).
- **-mtime** : Recherche selon la date de modification (ex : -mtime -1 pour les fichiers modifiés dans les dernières 24 heures).

**Exemple de recherche pour des fichiers de taille inférieure à 100 Ko :** *find . -size -100k*



**🕒 Types de temps sur les fichiers**

Les fichiers sous Linux ont trois types de **compteurs de temps** :

- **mtime** : Date de modification. / **ctime** : Date de création. / **atime** : Date d'accès.

Ces informations peuvent être utilisées avec find pour rechercher des fichiers en fonction de leur âge ou de leur dernier accès.



**⚡ Actions sur les résultats**

Une fois que find a retourné les fichiers qui correspondent à tes critères, tu peux lui faire effectuer des actions sur ces fichiers.

**🖨 Affichage des résultats**

- **-print** : Affiche le chemin des fichiers trouvés (par défaut). **-printf** : Permet d’afficher les résultats de manière formatée, à la manière de printf en C.

**🛠 Exécution de commandes avec exec**

L'option **-exec** permet d’appliquer une commande à chaque fichier trouvé. Par exemple, pour copier les fichiers trouvés dans un répertoire spécifique :

*find . -type f -name "edition" -exec cp {} /tmp/edition.txt \* > (Ici, {} est remplacé par le fichier trouvé).

**🚨 Confirmation avant suppression**

Lorsque tu utilises des commandes potentiellement destructrices comme rm, l'option **-ok** te permet de demander une confirmation avant chaque suppression. C’est une sécurité pour éviter les erreurs : *find . -name "*.bak" -ok rm {} \*
