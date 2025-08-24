# Lister des fichiers

## **🔹 Commande de base**

commande : `ls`

→ Affiche les fichiers/dossiers du répertoire courant.



## **🔧 Options utiles**

| **Option** | **Effet** |
|----|----|
| -l | Format long (permissions, proprio, taille, date…) |
| -a | Affiche les fichiers cachés (commencent par un .) |
| -h | Affiche les tailles lisibles (K, M…) |
| -d | Affiche les infos d’un dossier sans lister son contenu (Impératif pour le globbing) |
| -t | Trie par date de modif |
| -r | Inverse l’ordre de tri |
| -R | Récursivité au sein des sous-dossiers. |

✅ Exemple : `ls -ltr`

→ Liste triée par date de modification, du plus ancien au plus récent.



## **🧠 Types de fichiers (1er caractère de ls -l)**

| **Caractère** | **Type**                                          |
|---------------|---------------------------------------------------|
| -            | Fichier standard                                  |
| d             | Dossier                                           |
| l             | Lien symbolique                                   |
| b, c, p, s    | Fichiers spéciaux (Bloc, Character, Pipe, Socket) |
