# Switch🧠 **🧩 Définition et rôle de switch**

La structure switch permet d’exécuter une ou plusieurs actions selon la valeur d’une variable.

Contrairement au if qui teste une seule condition à la fois, switch gère **plusieurs cas possibles** de manière optimisée, sans imbrication.



📐 **⚙️ Règles de fonctionnement**

- Chaque bloc est **exécuté si la valeur correspond exactement**
- La **valeur testée** peut être string, integer, ou élément d’un tableau
- Si **plusieurs cas matchent**, tous les blocs correspondants s’exécutent sauf en cas de break
- Un switch peut contenir un **nombre illimité** de cas

📦 **📊 Exemple d’utilisation simple**

switch ($adGroup) { 'user' { Write-Host "Groupe utilisateur" } 'admin' { Write-Host "Groupe admin" } default { Write-Host "Groupe inconnu" } }

→ Affiche le texte correspondant à la valeur contenue dans $adGroup

🧰 **🔩 Commandes spéciales break / continue**

- break : **interrompt immédiatement le switch**, aucune autre condition testée
- continue : **ignore le reste du switch**, passe à la prochaine itération d’une boucle englobante

Exemple : 'user' { continue } 'admin' { Write-Host "Admin" ; break }



🧪 **🧮 Lecture de saisie utilisateur**

Exemple avec menu : $choix = Read-Host "Choix (a ou b)"

switch ($choix) { 'a' { Write-Host "Choix A" } 'b' { Write-Host "Choix B" } default { Write-Host "Invalide" } }

→ Permet un menu à choix multiples → Facile à réutiliser avec menus dynamiques



🧬 **🔍 Expressions régulières dans un switch**

Utiliser -regex pour tester des motifs : switch -regex ($variable) { '[a-z]' { action } '[0-9]' { action } default { action } }

→ [a-z] : détecte une lettre → [0-9] : détecte un chiffre

Permet de valider des formats plus complexes dans des scripts utilisateur



📚 **📈 Compatibilité avec les collections**

Si $variable est un tableau ou un ArrayList, switch teste **chaque élément**.

Dans le cas d’un ArrayList, **couplage avec foreach nécessaire** pour lecture ligne à ligne :

foreach ($item in $liste) { switch ($item) { ... } }

🔁 **🛠 Syntaxe de base**

switch ($variable) {

 'valeur1' { commande }

 'valeur2' { commande }

 default { commande }

}

→ $variable : donnée testée

→ 'valeur1', 'valeur2' : cas testés

→ default : cas par défaut si aucune correspondance



🧷 **🧠 À retenir**

- Plus clair et performant que plusieurs if
- Supporte valeurs multiples, menus, expressions régulières
- break arrête, continue ignore et passe au suivant
- S'utilise avec string, int, tableaux, et entrées utilisateur
- Très utile pour la **création de menus interactifs** ou traitements conditionnels multiples
