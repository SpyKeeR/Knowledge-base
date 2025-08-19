# Les modules**📦 Qu’est-ce qu’un module PowerShell ?**

Un **module PowerShell**, c’est un **ensemble de commandes (Cmdlets)** regroupées selon un thème : Active Directory, Azure, Exchange, etc.

Chaque module est généralement un **fichier .PSM1** et peut enrichir la console avec des fonctions supplémentaires non disponibles par défaut.



**🧠 Fonctionnement des modules**

➡️ **Chargés automatiquement** au démarrage (partiels, pour économiser la RAM)

➡️ **Auto-chargés à la volée** si une commande liée est utilisée

➡️ **Stockés dans des répertoires** définis par la variable **$env:PSModulePath**



**🔍 Découverte des modules**

- Get-Module ➜ affiche les modules **actuellement chargés**
- Get-Module -ListAvailable ➜ liste **tous les modules installés**, même non chargés
- echo $env:PSModulePath ➜ montre les chemins où PowerShell cherche les modules



**🧰 Ajout d’un module personnalisé**

1.  **Récupère le fichier .PSM1**

&nbsp;

2.  Dépose-le dans un des chemins listés par $env:PSModulePath

    - Ex: %ProgramFiles%\WindowsPowerShell\Modules (global) ou %UserProfile%\Documents\WindowsPowerShell\Modules (local)

&nbsp;

3.  **Importe le module** avec Import-Module NomDuModule (NomDuModule = chemin fichier .psm1 ou nom du répertoire au sein d'un chemin listé dans le PATH)

&nbsp;

4.  Vérifie l'import avec Get-Command -Module NomDuModule



**🔐 Prérequis pour utiliser des modules**

- Exécution en **PowerShell admin** ✅
- **Stratégie d’exécution** adaptée (ex: RemoteSigned ou Unrestricted) ✅
- Les fichiers .PSM1 sont des **scripts PowerShell**, donc soumis aux mêmes règles de sécurité
