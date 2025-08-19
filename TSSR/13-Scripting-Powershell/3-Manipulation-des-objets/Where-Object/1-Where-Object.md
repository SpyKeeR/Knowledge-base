# Where-ObjectLa commande **Where-Object** permet de filtrer des objets en fonction des **valeurs de leurs propriétés**. Elle est incontournable pour affiner les résultats issus de commandes comme Get-Process, Get-Service, Get-ADUser, etc.



**🧠 Fonctionnement de base**

Commande | Where-Object { $_.Propriété -Opérateur Valeur }

Exemple : Get-Service | Where-Object { $_.Status -eq 'Running' }

Seuls les objets dont Status est "Running" sont retournés.



**⚙️ Opérateurs de comparaison**

- **-eq / -ne / -gt / -lt / -ge / -le** → égal, différent, supérieur, inférieur, etc.
- **-like / -notlike** → pour les motifs (*wildcards*) comme * ou ?.
- **Versions sensibles à la casse** : -ceq, -cgt, -clike, etc.  
  📝 PowerShell est **insensible à la casse par défaut**.



**🔀 Combiner plusieurs conditions**

- **ET logique** ➤ -and (toutes les conditions doivent être vraies)
- **OU logique** ➤ -or (au moins une doit être vraie)  
  Exemples : $_ .Name -like 'Power*' -and $_.ID -eq 825 - $_ .Name -eq 'Toto' -or $_.SID -eq 'S-1-5-21…'



**🧬 Tests sur plusieurs propriétés**

➡️ Get-Process | Where-Object { $_.Name -like '*Power*' -and $_.Id -gt 1500 -and $_.Id -lt 2000 }

➡️ Get-LocalUser | Where-Object { $_.Name -like 'Toto' -or $_.SID -eq 'S-1-5-21…' -or $_.Enabled -eq $true }

⚠️ **Pense à inclure les propriétés testées avec Select-Object avant** si tu l’utilises !



**🧑‍💼 Cas concret avec Active Directory**

Tu veux cibler des utilisateurs selon leurs propriétés dans l’AD ? Facile :

- Get-ADUser -Filter * -Properties * | Where-Object { $_.Name -like 'R*' }
- … | Where-Object { $_.Enabled -eq $false }
- … | Where-Object { $_.Name -like '*R*' -and $_.Enabled -eq $false }
- … | Where-Object { ($_.Name -like '*R*' -and $_.Enabled -eq $false) -or ($_.GivenName -like '*A*') }



**⚠️ À ne pas oublier**

- La commande Where-Object filtre après génération des objets.
- N’utilise **que des propriétés déjà retournées**, sinon l’objet n’aura pas l’info.
- Réduction de syntaxe possible : $_ est un alias de $PSItem.
