# Sort-Object🎯 **Objectif de la commande Sort-Object**

Permet de trier les objets retournés par une commande, selon une ou plusieurs **propriétés précises**, avec un **ordre croissant** ou **décroissant**, selon le type de données (texte ou nombre).



🧠 **Fonctionnement général**

- La commande trie les objets après un pipe | (ex : Get-Service | Sort-Object Status)
- Par défaut, tri croissant (A → Z, ou plus petit → plus grand)
- Option -Descending : inverse l’ordre (Z → A ou plus grand → plus petit)
- Utilisable sur **toutes les propriétés**, même System (ex : Length, Id, CPU, etc.)



🧪 **Exemples pratiques courants**

- Get-ChildItem C:\Windows | Sort-Object Length → trie les fichiers du plus léger au plus lourd
- Get-Process | Sort-Object ProcessName -Descending → trie les noms de processus de Z à A
- Get-Process | Select Name, Id | Sort Id -Descending → trie les processus selon leur ID décroissant
- Get-Service | Select Name, Status | Sort Status → trie les services selon leur état



📌 **À retenir**

- Sort-Object peut suivre un Select-Object pour trier des propriétés déjà filtrées
- Le type de donnée **détermine le mode de tri** : String → alphabétique, Integer → numérique
- Fonctionne **avec ou sans** Select-Object
- Particulièrement utile pour rendre une sortie **lisible**, **organisée** et **exploitable**



💡 **Petit mémo express**

- Propriété → -Property NomPropriété
- Ordre décroissant → -Descending
- Pipe (|) = ALT GR + 6
- Combinez avec Select-Object pour **filtrer puis trier** efficacement
