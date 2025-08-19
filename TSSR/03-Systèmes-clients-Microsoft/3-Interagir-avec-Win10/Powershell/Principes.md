# Principes**🔎 Vérification de la version de PowerShell**

- **Commande** : $PSVersionTable
  - Cette commande affiche des informations importantes, comme la version de PowerShell utilisée.
- **Pourquoi c'est important ?**
  - Connaitre la version est crucial, surtout dans des environnements avec des systèmes d'exploitation variés (Windows 10, Windows 7, serveurs 2012 R2, 2016, 2019).



**🔄 Importance de la rétrocompatibilité**

- **Problème** : Un script écrit sur PowerShell 5.1 pourrait ne pas fonctionner sur une version antérieure.
- **Solution** : La variable $PSVersionTable contient la clé **PSCompatibleVersion**, indiquant la compatibilité des versions de PowerShell, ce qui aide à éviter des erreurs lors de l'exécution de scripts sur différents systèmes.



**🧑‍💻 Commandlets : structure et utilisation**

- **Structure** : Chaque commandlet PowerShell suit une structure simple : **verbe-nom**.
  - **Exemples de verbes courants** : Get, Set, Remove, Add, New.
  - **Exemple de commande** :
    - Get-Alias : Affiche la liste des alias disponibles dans PowerShell.
    - Get-Verb : Affiche les verbes utilisables dans PowerShell.
  - **Note** : Les commandlets ne sont pas sensibles à la casse, vous pouvez écrire en majuscule ou minuscule.



**⚙️ Utilisation des paramètres avec les commandlets**

- **Paramètres** : Certains commandlets utilisent des paramètres pour spécifier des détails supplémentaires. Ces paramètres sont introduits par un tiret suivi d'un mot.
  - **Exemple** : Get-ChildItem -Path C:\users
    - Ici, -Path est un paramètre qui nécessite un argument, dans ce cas, le chemin C:\users.
