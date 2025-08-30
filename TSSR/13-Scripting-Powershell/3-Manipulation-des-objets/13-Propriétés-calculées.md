# **🛠️ Qu'est-ce qu'une propriété calculée ?**

Une **propriété calculée** est une **propriété personnalisée** qu’on ajoute à la sortie d’une commande PowerShell avec `Select-Object`. Elle sert à **adapter le nom ou la valeur d’une propriété existante**.

## **🏗️ Structure d’une propriété calculée**

Une propriété calculée se construit avec :

- `@{}` : Début de la propriété
- `n='Nom'` ou `Name='Nom'` : Nom affiché
- `e={ expression }` ou `Expression={ expression }` : Valeur calculée

🔸 Exemple :

`@{n='Taille';e={($_.Length / 1MB).ToString("N2") + " Mo"}}`



## **💬 Cas d’usage fréquents**

1.  **Traduction des entêtes** : Name devient Nom, Length devient Taille
2.  **Conversion d’unités** : octets → KB / MB / GB avec division puis arrondi
3.  **Création d’un objet** : compatibilité entre noms CSV (nom) et noms attendus (Name)
4.  **Formatage visuel** : meilleure lisibilité des tailles ou durées



## **📦 Exemples pratiques**

- 🎯 *Conversion simple* :  
 ` Get-ChildItem -File | Select Name, @{n='Taille';e={[math]::Round($_.Length/1MB,2)}}`

- 🌍 *Traduction multi-propriétés* :  
 ` Get-Volume | Select @{n='Lecteur';e={$_.DriveLetter}}, @{n='Taille';e={($_.Size/1GB).ToString("N2") + " GB"}}, @{n='Espace Libre';e={($_.SizeRemaining/1GB).ToString("N2") + " GB"}}`

- 🧬 *Création d’objet compatible AD* :  
  Depuis CSV avec nom, on recrée Name via `Select-Object` avant un `New-ADUser`.



## **🧾 Résumé des points-clés**

- ✅ Les propriétés calculées permettent d’adapter noms *et* valeurs des propriétés
- ✅ On les utilise pour **renommer, convertir, formater**
- ✅ Syntaxe : `@{n='NouveauNom';e={expression}}`
- ✅ Très utile pour **l'import/export**, **les rapports**, ou **les scripts d’automatisation**

