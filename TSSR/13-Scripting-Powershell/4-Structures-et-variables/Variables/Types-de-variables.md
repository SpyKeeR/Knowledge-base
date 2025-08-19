# Types de variables**🧱 Type Objet (Object)**

- PowerShell manipule majoritairement des objets (résultats de commandes, services, utilisateurs AD…)
- Un objet peut contenir des **propriétés** ($service.ServiceName) et des **méthodes** ($services[3].Stop())
- Les objets peuvent être **affichés** directement en appelant la variable ($services)
- Pour explorer un objet : Get-Member (ou | gm)
- Les collections (ex : liste d’utilisateurs) sont indexées à partir de **0** ($adUser[2] = 3ᵉ élément)



**✍️ Type String (Chaîne de caractères)**

- Valeur entourée de doubles guillemets ($a = "ABC")
- Opération d’**addition = concaténation** ($c = $a + $b → ABCDEF)
- Plusieurs propriétés et méthodes disponibles (ex : .Length, .Replace(), .ToUpper())



**🔢 Type Integer (Entier)**

- Valeur numérique **sans guillemets** ($a = 1)
- Supporte les **opérations arithmétiques** : +, -, *, /
- Mélange de types à éviter (ex : addition d’un string avec un int → erreur)
- Méthodes disponibles : .Round(), .ToString(), etc.



**🧮 Type Array (Tableau)**

- Déclaration : $array = ["v1", "v2", "v3"]
- Accès par **index** : $array[0], $array[1]…
- Type retourné : System.Array (vérifiable via .GetType())
- Méthodes accessibles via Get-Member
- La méthode .Replace() peut être utilisée pour modifier une valeur
- **Taille fixe** après création (impossible d’ajouter ou supprimer un élément sans recréer)



**📌 Récapitulatif méthodes utiles**

- .GetType() → Connaître le type de la variable
- .ToString("format") → Conversion de type Date en String
- .Round(n) → Arrondir un float à n décimales
- .Replace("ancien", "nouveau") → Remplacement dans une chaîne ou tableau
- Get-Member (| gm) → Voir les propriétés & méthodes d’un objet



**⚠️ Règles importantes**

- Type déterminé automatiquement selon la **valeur affectée**
- Variables typées dynamiquement, mais le **mélange de types** provoque des erreurs
- Une méthode ou opération **ne fonctionne que sur le type adapté**
- Toujours vérifier les types avant d’effectuer des opérations (.GetType())
