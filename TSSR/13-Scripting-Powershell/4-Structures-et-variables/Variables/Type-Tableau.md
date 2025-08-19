# Type Tableau📦 **Array (tableaux à taille fixe)**

- Déclaration : $array = "PC1", "PC2", "PC3"
- Indexation : commence à 0, ex : $array[1] = PC2
- Séparateur : virgule (,)
- Taille **fixe** : pas d’ajout/suppression directe sans redéfinir la variable
- Ajout forcé (réassignation) : $array += "PC4" ⇒ recrée un tableau avec +1 élément
- Modification d'une valeur : $array[0] = "PC11"
- Méthodes limitées : uniquement remplacement (Replace)

🔁 **ArrayList (tableaux à taille dynamique)**

- Nécessite typage :
  - Méthode 1 : $list = New-Object System.Collections.ArrayList
  - Méthode 2 : [System.Collections.ArrayList]$list = "PC1", "PC2"
- Type vérifiable : $list.GetType().FullName ⇒ System.Collections.ArrayList
- Taille **dynamique** : permet Add, Remove, Insert, etc.
- Méthodes disponibles :
  - .Add("valeur") ⇒ ajout à la fin
  - .Remove("valeur") ⇒ supprime par contenu
  - .RemoveAt(index) ⇒ supprime à un index précis
  - .Insert(index, "valeur") ⇒ insère à une position
  - .Replace(valeurAncienne, valeurNouvelle) ⇒ changement de contenu



🧬 **Différences clés Array vs ArrayList**

- 📌 *Taille fixe* vs *taille variable*
- 📌 *Peu de méthodes* vs *méthodes nombreuses*
- 📌 *Pas de typage requis* vs *typage obligatoire*
- 📌 *Redéfinition nécessaire* vs *manipulation dynamique*

🛠 **Aide et documentation**

- Get-Help about_Array ⇒ infos sur Array
- Get-Help about_ArrayList (ou Get-Member sur une var ArrayList) ⇒ méthodes disponibles



🧩 **Utilisation typique**

- Array : liste fixe simple (ex : noms machines statiques)
- ArrayList : gestion dynamique (ex : ajout/suppression d’hôtes pendant l’exécution d’un script)
