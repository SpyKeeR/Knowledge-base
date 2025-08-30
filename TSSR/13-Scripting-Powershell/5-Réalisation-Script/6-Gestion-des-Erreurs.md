# Gestion des Erreurs

Lorsqu’une commande échoue (syntaxe, conditions, algo, etc.), PowerShell affiche une erreur et peut stopper l’exécution du script. 

La gestion des erreurs permet de **prévoir**, **contrôler**, et **documenter** ces situations pour éviter des plantages non maîtrisés, notamment en environnement utilisateur.



## **🗃️ La variable $Error**

- Toutes les erreurs sont stockées dans **un tableau automatique** : `$Error`
- Chaque nouvelle erreur est ajoutée en **première position** : `$Error[0]` contient **la dernière erreur**
- Il est possible d’accéder à l’historique d’erreurs en parcourant les indices du tableau



## **🧪 Types d’erreurs**

- **Erreurs bloquantes** : arrêtent l’exécution de la commande (voire du script)
- **Erreurs non bloquantes** : affichées, mais l’exécution continue
- Le comportement peut être modifié au **niveau de la commande** ou de l’**environnement**



## **🧰 Paramètres de gestion des erreurs**

### **🔸 -ErrorAction**

Permet de définir le comportement **d’une commande** en cas d’erreur.

Valeurs possibles :

- Continue : affiche l’erreur et continue *(valeur par défaut)*
- SilentlyContinue : ignore l’affichage, mais stocke l’erreur dans `$Error`
- Ignore : ne fait rien, **n’enregistre pas** l’erreur
- Stop : stoppe immédiatement l’exécution
- Inquire : demande à l’utilisateur quoi faire
- Suspend : suspend un workflow en cours

### **🔸 -ErrorVariable**

Stocke l’erreur dans **une variable personnalisée** (sans le $ dans la déclaration).

Exemple :

`-ErrorVariable customError` → accès via $customError

### **🔸 $ErrorActionPreference**

Définit un comportement **global** à toutes les commandes du script.

Exemples :

- `$ErrorActionPreference = "Stop"` → tout le script s'arrête dès qu'une erreur est levée
- Peut être temporairement redéfini dans une partie du script selon les besoins

## **🧱 Structure try / catch / finally**

Permet une gestion **structurée** et **précise** des erreurs dans les scripts.

### **🔹 try**

Contient **la commande à tester**. 

Si l’erreur survient et que le comportement est Stop, on passe à catch.

### **🔹 catch**

S’exécute **uniquement** si une erreur est générée dans le bloc try.

Utile pour afficher un message personnalisé ou prendre une autre action.

### **🔹 finally**

S’exécute **systématiquement**, qu’il y ait erreur ou non.

Souvent utilisé pour **afficher un message final**, **libérer des ressources**, etc.



## **🧭 Résumé des bonnes pratiques**

- Toujours prévoir **une gestion des erreurs** dans les scripts destinés à des utilisateurs.
- Utiliser `$Error` pour **consulter l’historique des erreurs**.
- Employer `-ErrorAction` pour affiner le comportement **commande par commande**.
- Utiliser `try` / `catch` / `finally` pour **contrôler les blocages** et **ajouter de la logique conditionnelle** à l'exécution du script.
- Si besoin d'un suivi précis, stocker les erreurs dans une **variable personnalisée**.



