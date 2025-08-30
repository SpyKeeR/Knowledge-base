# Enchainement commande

## ⚙️ **Exécution sur une seule ligne avec ;**

- Utilisé pour exécuter **plusieurs commandes indépendantes** sur une seule ligne
- Chaque commande s'exécute **sans dépendre du résultat précédent**

Syntaxe : `Commande1 ; Commande2 ; Commande3`

Exemple : `Get-ADUser -Filter * ; Get-ADComputer -Filter *`

→ Affiche d'abord les utilisateurs, puis les ordinateurs

## 🔁 **Différence avec le pipe |**

- Le `|` transfère la **sortie de gauche** comme **entrée à droite**
- Le `;` **enchaîne sans transmettre** les résultats

→ `Commande1 | Commande2` = exécution liée
→ `Commande1 ; Commande2` = exécution indépendante



## 🧩 **Répartition de commande sur plusieurs lignes avec backtick `**

- Utilisé pour **améliorer la lisibilité** des commandes longues
- Permet de **continuer une commande à la ligne suivante** sans l’exécuter
- Le backtick doit être **le dernier caractère** de la ligne

- Exemple classique :
```powershell
Get-ADUser -Filter *`
| Where-Object { $_.Name -like '*R*' -or $_.GivenName -like '*R*' }
```



## 🧠 **Règles et bonnes pratiques**

- `;` : sépare les commandes – usage pour **enchaînements rapides**
- backtick : prolonge une commande – usage pour **paramètres ou filtres complexes**
- Ne pas confondre le backtick avec l’apostrophe `'`
- Toujours **terminer la ligne par le backtick**, sinon la commande s’exécute immédiatement
- Le backtick est fragile → éviter les espaces après
