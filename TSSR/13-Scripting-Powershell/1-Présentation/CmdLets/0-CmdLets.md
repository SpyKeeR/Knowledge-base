# **🧱 Qu’est-ce qu’une Cmdlet ?**

Les **Cmdlets** sont les briques de base de PowerShell.

Contrairement aux commandes classiques (CMD), elles sont **structurées, lisibles** et **orientées objet**.

➡️ Syntaxe standard : Verbe-Nom

Ex. : `Get-Process`, `Get-Service`, `Get-ChildItem`

📌 *Toujours au singulier pour le nom* ➜ logique et prévisible



## **🧠 Verbes + Objets : clarté et logique**

Les **verbes** indiquent l'action : Get, Set, Remove, Start, Stop, etc.

Les **noms** représentent des objets PowerShell : User, Item, Volume, Service...

➡️ Très facile de **deviner ce que fait la commande** juste en la lisant.

📦 Les verbes disponibles dépendent du **module PowerShell chargé**.



## **🎯 Utilisation des paramètres**

Cmdlets = commandes + **paramètres optionnels ou obligatoires**, avec ou sans **arguments**.

Chaque paramètre commence toujours par **un tiret** -.

Exemples classiques :

- `Get-LocalUser -Name "Administrateur"`
- `Get-LocalGroupMember -Group "Administrateurs" -Member "Maxime"`

🔸 Paramètre = -Name, -Group

🔸 Argument = "Administrateur", "Maxime"



## **🚨 Paramètres obligatoires ou non**

Certaines cmdlets fonctionnent **sans paramètre**, d'autres **en exigent** un ou plusieurs.

➡️ Si un paramètre obligatoire est absent, la commande **échoue** ou demande une valeur.

🧪 Ex : `Get-Process` fonctionne seul, mais Set-Item nécessite un chemin et une valeur.


