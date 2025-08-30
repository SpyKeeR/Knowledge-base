# **📌 Définition rapide**

Les **méthodes** sont des **actions** qu’on peut appliquer sur un **objet PowerShell**.

Contrairement aux propriétés (valeurs statiques), elles **modifient** ou **interagissent** avec l’objet complet ou une de ses valeurs.



## **🕹️ Exemple parlant : Get-Date**

- La commande `Get-Date` retourne un objet **System.DateTime**
- Cet objet a des **méthodes intégrées** comme `.AddDays()`, `.AddMonths()`, `.ToString()`, etc.
- `(Get-Date).AddDays(2)` → ajoute 2 jours à la date actuelle  
  👉 Le point `.` appelle la méthode ; les `()` servent à passer des **paramètres**



## **🔬 Méthodes ≠ Propriétés**

- **Propriétés** : statiques, consultables (Date, Day, Month, Hour…)
- **Méthodes** : dynamiques, modifient ou transforment l’objet (AddDays, ToShortDateString, ToInt16, etc.)



## **🛠️ Syntaxe d’appel**

- Envelopper la commande de base avec ()
- Ajouter un **point** `.` pour appeler la méthode
- Placer le(s) argument(s) entre ()  
  Ex : `(Get-Date).AddDays(5)` ou `(Get-Date).ToShortDateString()`



## **📚 Get-Member et Méthodes**

Utilise `| Get-Member` après une commande pour voir ses méthodes.

Ex : `(Get-Date).AddDays(1) | Get-Member` → liste les méthodes retournées par l'objet modifié.

💡 Beaucoup de méthodes sont **communes** à plusieurs types d’objets, comme les `.ToString()` ou `.Equals()`.



## **🔁 Types & Conversions**

Les méthodes comme AddDays attendent des types précis : souvent **int**, parfois **double**.

Les méthodes `ToType()` (ex: `.ToInt16()`) servent à **convertir** un type en un autre.

➡️ Bien utile pour forcer ou formater un résultat avant traitement ou affichage.

