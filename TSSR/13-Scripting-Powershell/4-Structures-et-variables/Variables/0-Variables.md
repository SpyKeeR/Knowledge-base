# Variables

Les variables permettent de **stocker des données en mémoire vive** pour les réutiliser plus tard. 

Elles sont **temporaires** (disparaissent à la fermeture de la console) et peuvent contenir **n'importe quel type de donnée** : texte, chiffre, objet, résultat de commande...

💬 *Imagine-les comme des boîtes que tu étiquettes et remplis d’infos utilisables tout au long de ton script.*



## **🖋️ Déclaration et assignation**

Pour créer une variable ➡️ `$NomVariable = Valeur`

Exemples : 
- `$a = 1` (entier)
- `$a = "Bonjour"` (chaîne de caractères)

PowerShell détecte **automatiquement le type** selon ce que tu lui donnes.

Pas besoin de le déclarer manuellement.


## **🔣 Les types de variables courants**

- **String** : texte ("Salut")
- **Integer** : chiffre (42)
- **Bool** : vrai/faux ($true, $false)
- **Objet** : résultat d'une commande (`$user = Get-ADUser -Filter *`)

ℹ️ Tu veux connaître le type d'une variable ? → `$a.GetType()`



## **🧪 Etendues des variables**

Les variables initialisées dans un script ont un usage et une portée locales.

Pour les étendre :

- `$script:variable` - Portée étendue au script
- `$global:variable` - Portée étendue à toute la session



## **🖋️ Typer une variable**

On peut forcer le type de données stockée dans une variable

- `[int]$variable = 1`
- `[string]$variable = "Ceci est du texte"`



## **🧪 Manipulation & affichage**

Quelques commandes pratiques :

- `Write-Host "Ma variable : $a"` → Affiche le contenu
- `Get-Variable` → Affiche toutes les variables en mémoire
- `Read-Host "Votre nom ?"` → Récupère une **saisie utilisateur** et la stocke

Exemple concret : 
```powershell
$nom = Read-Host "Quel est ton prénom ?"
Write-Host "Bienvenue, $nom !"
```


## **🧼 Bonnes pratiques de nommage**

Donne des noms **clairs et explicites** à tes variables pour faciliter la lecture et la maintenance :

- ✅ $logFile, $username, $eventList
- ❌ $a, $x, $data1

