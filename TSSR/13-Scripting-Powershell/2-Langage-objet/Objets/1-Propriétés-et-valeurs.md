# **🧱 Les objets PowerShell : focus sur les propriétés**

Les objets PowerShell sont composés de **propriétés** (attributs = état) et de **méthodes** (actions). 

Les **propriétés** contiennent des **valeurs** décrivant l’objet (nom, statut, description, etc.). 

C’est la **clé** pour identifier, filtrer ou modifier des objets dans PowerShell.



## **🔎 Types de propriétés courantes**

Selon le type d’objet, les propriétés peuvent contenir des **types de données différents** :

- String → texte (ex : "Service DHCP")
- Integer → chiffre entier (ex : 3, 12345)
- Boolean → logique : `$True`, `$False`, `$Null`
- Array → tableau indexé de plusieurs valeurs
- Object → objet imbriqué (ex : propriété contenant un autre objet)

➡️ Ces types définissent **ce qu’on peut faire** avec chaque propriété (filtrer, comparer, manipuler…).



## **⚙️ Inspecter les propriétés avec Get-Member**

Pour **voir les propriétés** d’un objet (et leurs types), on utilise :

`Get-Service | Get-Member`

→ Retourne : nom des propriétés (DisplayName, Status...), méthodes dispo (`Start()`, `Stop()`, etc.) et **type de l’objet** (ex : `System.ServiceProcess.ServiceController`)

➡️ C’est l’un des **3 outils clés** avec Get-Command et Get-Help.



### **🧪 Exemple pratique**

Tu veux inspecter un service précis ?

`Get-Service -Name WSearch` → Renvoie l’objet du service "WSearch" avec ses propriétés actuelles.

Ex : DisplayName = Windows Search, Status = Running, etc.


## **🛠 Modifier une propriété avec Set**

Certaines propriétés peuvent être **modifiées** (attention, pas toutes) avec une commande Set-.

Ex : `Set-Service -Name WSearch -DisplayName "Service de recherche Windows"`

⚠️ Les valeurs avec espace doivent être **entre guillemets**.

Tu peux vérifier ensuite avec : `Get-Service -Name WSearch` → et lire la nouvelle valeur de DisplayName.

