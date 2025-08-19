# Pipe**🔗 Définition du pipe**

Le **pipe |** permet de *transmettre la sortie d’une commande comme entrée à la suivante*. Il sert à **chaîner les commandes** pour **filtrer, trier, formater ou conditionner** les données issues d'objets PowerShell.



**🧱 Fonctionnement général**

- Le **pipe** fonctionne uniquement si la commande produit un ou plusieurs **objets**.
- Chaque commande *traite la sortie* de la précédente.
- Le **caractère |** s’obtient avec **AltGr + 6**.
- On peut **enchaîner plusieurs pipes** à la suite.



**🔎 Commandes courantes après un pipe**

- **Select-Object** : pour *sélectionner* certaines **propriétés**.  
  ➤ ex : Get-Service | Select-Object Name, Status

- **Format-Table** : pour *présenter proprement* les résultats en **tableau**.  
  ➤ ex : … | Format-Table

- **Where-Object** : pour *filtrer* les objets selon des **conditions**.  
  ➤ ex : … | Where-Object { $PSItem.Name -eq "WSEARCH" }



**🧠 Variable spéciale $PSItem**

- $PSItem (ou alias $_) contient **l’objet courant** du pipeline.
- Elle permet de **tester des conditions** dans une commande comme Where-Object.
- Exemple : Get-Process | Where { $PSItem.Id -gt 1500 }



**📊 Application typique**

- **Réduction** de la sortie avec Select-Object
- **Tri et mise en forme** avec Sort-Object, Format-Table
- **Filtrage** précis avec Where-Object
- **Exportation** des résultats avec Export-Csv ou Out-File
