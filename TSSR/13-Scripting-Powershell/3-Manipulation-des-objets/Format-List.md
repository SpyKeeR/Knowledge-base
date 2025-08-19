# Format-List🧩 **📌 Objectif de la commande**

Format-List permet de personnaliser l’affichage des propriétés d’un objet sous forme de liste verticale, propriété par propriété. C’est utile quand trop d’infos sont masquées ou que l’affichage par défaut (tableau) est illisible.



🔍 **📋 Quand l’utiliser ?**

👉 Quand on veut *voir toutes les propriétés* ou *filtrer précisément* ce qu’on affiche.

👉 Idéal pour les objets complexes comme les utilisateurs AD, services, processus, etc.

👉 Pratique pour *éviter les colonnes tronquées* dans un Format-Table.



🛠️ **⚙️ Syntaxe & usage du paramètre -Property**

On peut préciser **quelles propriétés** on veut afficher, et dans **quel ordre**.

Exemple courant : Get-ADUser -Filter * | Format-List Name, Enabled, SamAccountName

Ici, on choisit d’afficher uniquement ces 3 propriétés, ce qui rend la sortie claire et ciblée.



🎯 **🧠 À retenir**

- Format-List est un *outil de mise en forme*, **pas de filtrage fonctionnel**.
- Il *modifie l'affichage* mais pas le contenu de l'objet.
- Il est souvent utilisé en fin de pipeline, juste avant l’affichage à l’écran.
- Peut être combiné avec Where-Object, Select-Object pour *cibler des objets précis*.
- Si on veut **toutes les propriétés**, on peut faire : Format-List *.
