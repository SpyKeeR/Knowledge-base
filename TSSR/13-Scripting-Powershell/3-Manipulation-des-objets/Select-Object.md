# Select-Object**🎯 Objectif de la commande Select-Object**

Sélectionner des **propriétés précises** issues d’objets PowerShell via le **pipe ( | )**, pour **simplifier l'affichage**, éviter les doublons ou isoler des infos pertinentes.



**🧰 Fonctionnement de base**

- Select-Object s'utilise **après une commande** (souvent avec Get-*)
- Paramètre -Property → choisit les propriétés à afficher → ex. Name, Status, etc.
- 📌 Écriture courte : Select tout court fonctionne aussi
- 

**📦 Sélection multiple et ordre**

- Liste les propriétés avec des virgules → l’ordre donné dans la commande est respecté
- Exemple : Get-Service | Select Name, Status



**🔍 Affichage ciblé : premiers/derniers résultats**

- -First n → affiche les *n premiers* objets
- -Last n → affiche les *n derniers*
- Utile pour limiter l'affichage dans de grandes listes



**🧹 Suppression des doublons**

- -Unique → affiche une seule fois chaque valeur identique (ex. processus svchost)
- Permet de dégager une vue plus claire (ex. Get-Process | Select -Unique ProcessName)



**🪄 Expand-Property**

- -ExpandProperty NomPropriété → extrait *uniquement* la valeur, sans entête ni structure objet
- Résultat brut → idéal pour traitement en pipeline ou export



**🔬 Exemples utiles**

- Get-Process | Select Id, ProcessName | Measure → pour compter les processus
- Get-ADUser -Properties * | Select Name, Department, City → pour sortir des données *non visibles* par défaut
