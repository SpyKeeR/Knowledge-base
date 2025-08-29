# 🎯 **📌 Objectifs de ce module**

### • Utiliser les **boucles while / until / for** 🔄

### • Maîtriser les **opérations arithmétiques** 🧮

### • Lire des **fichiers ligne par ligne** avec while couplé à read 📂



## 🧠 **🔄 Qu’est-ce qu’une boucle ?**

Une **boucle** permet de **répéter un bloc d’instructions** plusieurs fois selon :

- ✅ Une **condition** (ex : tant que $i < 10, on continue)
- 📚 Une **liste d’éléments à parcourir** (ex : noms de fichiers, arguments, etc.)

💡 Important : quand tu utilises une condition, **pense à faire évoluer la variable** testée, sinon la boucle tourne en rond ! (ex : $i=$(($i+1)))



## 🔧 **🧪 Trois types de boucles Shell**

1.  `while` → Tant que la condition est vraie
2.  `until` → Tant que la condition est fausse
3.  `for` → Pour chaque élément d’une liste (fixe ou générée)

👉 Ces structures permettent de traiter des actions répétitives, faire des tests, lire des fichiers, traiter des lots d’objets (fichiers, lignes, paramètres...).



## 🧮 **🧾 Calculs arithmétiques**

➕ Pour faire des calculs dans tes boucles (compteurs, conditions, etc.), tu peux utiliser :

- `expr` → commande historique (nécessite des **espaces autour des opérateurs**)
- `let` ou `((...))` → plus modernes, pratiques et lisibles

Exemple d’usage typique :

- Incrémenter une variable : `let i=i+1` ou `((i++))`
- Comparer deux valeurs : `if ((i < 5))`

