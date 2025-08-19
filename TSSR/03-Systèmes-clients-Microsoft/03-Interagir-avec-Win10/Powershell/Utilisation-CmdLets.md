# Utilisation CmdLets**🔎 Lister les verbes et commandes disponibles**

- **Afficher la liste des verbes utilisables** : ***get-verb***
  - Cela affiche tous les verbes qui peuvent être utilisés dans les cmdlets.
- **Lister toutes les commandes disponibles** : *get-command*
  - Cette commande affiche **toutes** les commandes existantes dans PowerShell.



**📂 Comprendre la classification des commandes**

- Lorsque vous exécutez *get-command*, vous verrez une colonne **"Commande Type"** qui classe les commandes en **trois grandes familles** :

  1.  **Cmdlets** : Commandes intégrées à PowerShell.
  2.  **Fonctions** : Scripts personnalisés ou intégrés.
  3.  **Aliases** : Raccourcis pour des commandes plus longues (ex. ls est un alias de Get-ChildItem).



**🎯 Filtrer les commandes**

- Si vous souhaitez **afficher uniquement les cmdlets**, utilisez : *get-command -commandtype cmdlet*
  - Cela permet d'afficher uniquement les cmdlets et non les fonctions ou les alias.



- Autocomplétions via la tabulation
