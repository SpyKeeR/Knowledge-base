# Structures**🔍 IF – Condition**

Permet de tester une ou plusieurs conditions et d'exécuter du code uniquement si ces conditions sont vraies. Peut être utilisée pour : tester la valeur d'une propriété, la présence d'un objet, ou l'existence d'une propriété.

**Syntaxe** : if (condition) { bloc1 } elseif (autre_condition) { bloc2 } else { bloc3 }

**Opérateurs courants** :

- Comparaison : -eq, -ne, -gt, -lt, -ge, -le
- Logiques : -and, -or, -not
- Existence : -is, -like, -match, -contains



**🔁 WHILE – Boucle conditionnelle**

Exécute un bloc tant qu'une condition est vraie. Potentiellement infinie si aucune condition d'arrêt n’est définie.

**Syntaxe** : while (condition) { bloc }

Sortie manuelle possible via break ou ajustement de la condition dans la boucle.

Utilisé pour : surveiller une saisie, vérifier l’état d’un service, attendre un événement, limiter un nombre de tentatives.



**🔄 FOREACH – Boucle objet**

Parcourt une collection d’objets et exécute un bloc pour chaque élément.

**Syntaxe** : foreach ($item in $collection) { bloc }

Très utilisée pour : parcourir les résultats d’une commande, traiter les lignes d’un fichier, manipuler des éléments AD, services, processus, fichiers, etc.



**🔘 SWITCH – Sélecteur de cas**

Permet d’exécuter un bloc différent selon la valeur d'une variable (généralement issue d'une saisie utilisateur).

**Syntaxe** : switch ($valeur) { "choix1" { bloc1 } "choix2" { bloc2 } default { bloc_defaut } }

Utilisé pour : créer des menus, dispatcher des traitements, automatiser des réponses à choix multiples. Peut être combiné avec while pour afficher le menu en boucle.



**🧱 Combinaisons fréquentes**

- while + switch : boucle de menu interactif
- foreach + if : traitement conditionnel d'une collection
- switch + if : cas multiples avec traitements personnalisés
