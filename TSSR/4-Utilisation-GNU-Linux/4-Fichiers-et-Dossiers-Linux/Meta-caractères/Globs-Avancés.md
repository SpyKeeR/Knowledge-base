# Globs AvancésOn passe à la **vitesse supérieure** avec des méta-caractères plus puissants pour faire des recherches **plus fines et précises** dans les noms de fichiers



**🧩 Parenthèses et quantificateurs**

Ces symboles permettent de répéter ou conditionner la présence de certains motifs dans un nom de fichier. 💡

⚠️ Nécessite l’activation de extglob avec : shopt -s extglob

**🎛️ Les quantificateurs disponibles :**

| **Syntaxe** | **Signification**                       |
|-------------|-----------------------------------------|
| ?(...)      | 0 ou 1 fois (optionnel)                 |
| *(...)     | 0, 1 ou plusieurs fois                  |
| +(...)      | 1 fois ou plus                          |
| @(...)      | exactement 1 fois                       |
| !(...)      | l'expression **ne doit pas** apparaître |
|            |                                        |

**🧪 Exemples concrets**

ls *+([eE])* # Fichiers contenant au moins un 'e' ou 'E'  
ls !(*.txt) # Tous les fichiers SAUF ceux en .txt



**🧰 Vérifier ou activer extglob**

shopt # Vérifie l'état actuel  
shopt -s extglob # Active le mode avancé



**🎯 Accolades {} — Choix multiples**

Les accolades permettent de faire plusieurs recherches ou opérations d’un coup :

ls *.{jpg,gif,png} # Fichiers en .jpg OU .gif OU .png  
mkdir images videos # Crée deux dossiers d’un coup
