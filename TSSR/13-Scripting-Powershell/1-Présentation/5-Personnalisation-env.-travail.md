# **🎨 PowerShell : un environnement de travail modulable**

PowerShell n’est pas qu’une simple console : c’est un **véritable espace de travail** que tu peux adapter à tes besoins (OS, contexte, confort visuel...).

Tu peux personnaliser son **apparence**, son **comportement au démarrage**, et même **précharger des scripts** via un fichier profil.



## **🛠️ Personnalisation de l'apparence**

▶️ Clique droit sur le bandeau de la console ➜ **Propriétés**

▶️ Personnalisation possible :

- **Police**, **taille**, **couleur de texte/fond**
- **Disposition de la fenêtre**, curseur, transparence, etc.

Cela te permet d’adapter ton environnement pour plus de lisibilité ou de confort visuel.



## **⚙️ Le fichier de profil PowerShell**

PowerShell peut **exécuter automatiquement des commandes** à chaque ouverture via un **fichier de profil** (.ps1).

Utile pour : précharger des modules, définir des alias, afficher un message de bienvenue, etc.

➡️ Pour le créer :

`New-Item -Path $Profile -Type File -Force`

→ Crée un **fichier .ps1** dans **Documents\WindowsPowerShell** (ou PowerShell\ pour PS7), nommé automatiquement selon le contexte utilisateur et shell.



### **📝 Modifier son profil**

Après création, tu peux l’éditer (avec *notepad $Profile* ou ton éditeur préféré).

Ce que tu écris dedans sera **exécuté à chaque démarrage** de ta console PowerShell.

Exemples :
```psh
Import-Module ActiveDirectory
Set-Location C:\Scripts
Write-Host "Bienvenue Maxime 👋"
Set-Alias ll Get-ChildItem
```


## **🧠 À retenir**

➡️ **Personnalisation visuelle** : rapide, clic droit → Propriétés

➡️ **Personnalisation fonctionnelle** : via **fichier de profil**

➡️ Le profil PowerShell est comme un *fichier .bashrc* sous Linux : simple, mais ultra puissant quand bien utilisé.

