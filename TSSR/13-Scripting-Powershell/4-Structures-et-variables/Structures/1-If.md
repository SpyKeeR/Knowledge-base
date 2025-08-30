# If

La structure if permet de tester une condition et d’exécuter des commandes en fonction du résultat logique (`$true` / `$false`).

- `if (condition)` : exécute les commandes si la condition est vraie.
- La condition peut comparer des **valeurs**, **objets**, **propriétés**, ou tester la **présence** d’un élément.
- Les **opérateurs de comparaison** (ex. : -eq, -gt, -lt, -ne, -ge, -le) permettent d’exprimer la logique conditionnelle.

➡️ *Exemple classique* : Si $X -eq $Y → affiche une confirmation. Sinon → rien ne se passe (sans else).



## 🧱 **Opérateur ELSE : Action si la condition est fausse**

Permet de déclencher un bloc de commande alternatif si la condition est fausse :

- `else { ... }` : exécuté si le if renvoie $false.
- Utilisé pour gérer un plan B, une action de secours ou une sortie.

➡️ *Structure* : Si $X -eq $Y → faire A, Sinon → faire B.



## 🔀 **Opérateur ELSEIF : Tests multiples chaînés**

Permet de tester plusieurs conditions à la suite :

- `elseif (condition2)` : testé uniquement si le if initial est faux.
- Possibilité d’enchaîner plusieurs elseif, toujours suivis d’un éventuel else.

➡️ *Structure complète* : Si $X -eq $Y → faire A, Sinon si $X -eq $Z → faire B, Sinon → faire C.



## 🎯 **Utilisation typique :**

- **Gestion de flux conditionnels** dans un script.
- **Contrôle des valeurs d’entrée** (objets, int, string...).
- **Réponse en cascade** selon les états d’une variable.



## 🔧 **Exemples courants de test :**

- $X -gt 20 : valeur supérieure
- $X -lt 20 : valeur inférieure
- $X -eq 20 : égalité
- Combiné à else pour afficher un message différent à chaque cas.
