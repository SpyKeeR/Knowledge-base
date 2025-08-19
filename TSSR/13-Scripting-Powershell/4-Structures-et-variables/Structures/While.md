# WhileExécute un bloc de commandes **tant que** la condition est $true.

Si la condition devient $false, la boucle s'arrête immédiatement.

- while (condition) { commandes }
- Condition évaluée **avant chaque itération**
- Si $x -ne "q" → boucle continue, sinon elle s’arrête
- ⚠️ Boucle infinie si la variable n’évolue pas dans le bloc

Exemple : $x = "a" → while ($x -ne "q") { $x = Read-Host "Saisir une valeur" } > La boucle ne s'arrête que si l'utilisateur saisit "q".



📌 **🔧 Initialisation obligatoire**

La variable testée **doit être définie avant** d’entrer dans une boucle while.

- Si $x = $null, la condition échoue, **aucune itération**
- Nécessite souvent une **valeur manuelle de départ**

→ Contourné par do { } while (condition) qui permet une première exécution sans initialisation préalable.



📌 **🔂 Structure Do While — Condition en fin de boucle**

Exécute une première fois le bloc, puis **teste la condition après**.

Utile pour **forcer au moins une exécution** sans init initiale.

- do { commandes } while (condition)
- Tant que $x -ne "q" → relance la boucle

Exemple : do { $x = Read-Host "Choix (Q pour quitter)" } while ($x -ne "q")



📌 **🔄 Structure Do Until — Inverse logique de While**

Identique à do while, mais la boucle continue **tant que la condition est fausse**.

- do { commandes } until (condition)
- Continue **jusqu’à ce que** $x -eq "q" soit vrai

Exemple : do { $x = Read-Host "Choix" } until ($x -eq "q") > La sortie se fait **quand la condition devient vraie**.



📌 **⚠️ Risque de boucle infinie**

Si la condition reste toujours vraie (ou jamais atteinte dans until) :

- Boucle ne se termine jamais
- Nécessité d’une **modification de la variable testée** dans la boucle ($x++, changement de chaîne...)
- Sinon, CTRL-C pour forcer l’arrêt



📌 **🧮 Exemple de compteur avec While**

Utilisé pour répéter un bloc un certain nombre de fois : $x = 1 → while ($x -lt 10) { $x++ } > Incrémente $x jusqu’à atteindre 10 > Contrôle précis sur **le nombre d’itérations**
