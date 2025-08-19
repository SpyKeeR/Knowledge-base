# Import-CSV**📥 Importer des objets depuis un fichier CSV**

PowerShell permet d’importer des **objets structurés** depuis un fichier .csv via Import-Csv, rendant les données exploitables comme n'importe quel objet PowerShell.



**🔍 Fonctionnement de Import-Csv**

- Lit un fichier .csv ligne par ligne
- Chaque ligne devient un **objet** avec propriétés selon les **entêtes** du fichier
- Option -Delimiter : pour spécifier le séparateur (souvent ;)
- Utilisable en **pipeline** vers d'autres commandes (New-ADUser, Set-Item, etc.)

🧠 Exemple simple : Import-Csv "chemin\fichier.csv" -Delimiter ";" | New-AdUser

→ Crée des utilisateurs selon les données du CSV.

**🧱 Prérequis côté CSV**

- Les entêtes doivent **correspondre exactement** aux noms de propriétés attendus par la commande cible (ex : Name, GivenName, SamAccountName, etc.)
- Attention aux **erreurs** si des propriétés ne sont pas reconnues.



**⚠️ Erreurs classiques**

- **Utilisateurs déjà existants** → provoqueront des erreurs (ex: Administrator, Guest, KRBTGT)
- **Données mal alignées** → la commande New-ADUser échouera

🔄 En cas d'import massif, penser à :

1.  Supprimer les objets existants (Get-ADUser -Filter * | Remove-ADUser)
2.  Puis lancer l’import.



**📤 Export ≠ Affichage**

Get-Content ne suffit pas : il lit du texte brut. Pour créer des objets exploitables → Import-Csv.



**👁️ Affichage des objets importés**

Utilise Select-Object pour filtrer les colonnes à afficher : Get-ADUser | Select-Object Name, GivenName

**🧾 Résumé rapide**

- 📌 Import-Csv = transforme un fichier texte en **objets PowerShell**
- ➡️ Utilisable dans des **pipelines** pour l’automatisation
- 📂 Format attendu : .csv avec entêtes corrects
- 🛑 Attention aux conflits d’existants
- 🧹 Pense à **nettoyer** la base avant réimportation
