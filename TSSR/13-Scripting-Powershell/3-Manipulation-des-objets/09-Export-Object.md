# 📤 **Présentation de l’exportation d’objets**

Les objets, une fois filtrés et formatés, peuvent être exportés pour archivage ou réutilisation. 

C’est là qu’interviennent des commandes comme `Export-Csv`, `Export-CliXml` ou encore la combinaison `ConvertTo-*` + `Out-File`.



## 📄 **Exportation vers un fichier CSV (natif)**

PowerShell permet nativement d’exporter en CSV après récupération des objets. Exemple typique : `Get-ADUser -Filter * | Export-Csv`.

🔸 `-Path` : chemin complet du fichier (UNC recommandé)

🔸 `-Delimiter` : souvent ; (ou , selon le logiciel)

🔸 `-NoTypeInformation` : supprime la ligne de type Microsoft.* en tête de fichier

✔ Permet d’obtenir un fichier prêt à être ouvert dans Excel ou LibreOffice.



## 🔁 **Ajout d'objets (Append)**

Il est possible d’ajouter du contenu dans un fichier existant sans le supprimer grâce à `-Append`.

Ex : ajouter des objets ADComputer à un fichier export-computer.csv.



## 📦 **Autres formats d'export supportés nativement**

PowerShell sait exporter vers :

🔹 **CliXML** via `Export-CliXml` → format XML structuré (interprétable par PowerShell)

🔹 **HTML** → via `ConvertTo-Html | Out-File` pour produire une page web contenant les données

🔹 **JSON** → via `ConvertTo-Json | Out-File`, pratique pour les API ou applis web

🛠 Ces formats nécessitent souvent une **conversion préalable** avant écriture dans le fichier.



### 💡 **Modules supplémentaires**

Modules comme **ImportExcel** permettent d’exporter vers .xlsx sans passer par CSV (mais nécessitent installation).

