# **🔍 Objectif de Measure-Object**

Permet d’analyser et de résumer des données issues d’objets PowerShell : comptage, somme, moyenne, min/max… au lieu d’un simple affichage brut.



## **🧠 Fonctionnement général**

- Par défaut : **compte** le nombre d'objets passés en pipeline  
  👉 `Get-Process | Measure-Object` ⟶ nombre total de processus

- Peut être combiné avec une propriété de type **Integer** pour calculer :
  - `-Average` ➜ Moyenne
  - `-Sum` ➜ Somme
  - `-Minimum` / `-Maximum` ➜ Valeur min / max

⚠️ **Important** : la propriété mesurée doit être de type numérique (INT), sinon aucun calcul possible.



## **⚙️ Exemples concrets**

- `Get-ChildItem -File | Measure-Object -Property Length -Average` ➜ Calcule la taille moyenne des fichiers

- `Get-Process | Measure-Object -Property ID -Sum -Minimum -Maximum` ➜ Statistiques sur les ID des processus (somme, plus petit, plus grand)

- `Get-ChildItem C:\Windows | Measure-Object -Property Length -Average -Minimum -Maximum -Sum` ➜ Fait un récap' complet des tailles de fichiers du dossier (moyenne, min, max, total)



## **🧩 Mesures avancées & position de la commande**

- Se place **en fin de pipeline** après les filtres (Get, Select, Sort, etc.)
- Peut s’utiliser avec `Select-Object` pour affiner le ciblage de la propriété
- Peut recevoir des objets déjà triés ou filtrés
- Utile pour **valider un traitement** (ex. : vérifier si un filtre a bien réduit la sortie)
