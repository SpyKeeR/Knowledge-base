# Remoting/Invoke-CommandC’est la possibilité d’exécuter **des commandes sur une ou plusieurs machines distantes**, sans se déplacer. Un peu comme du SSH, mais version Windows, basé sur le **protocole WS-MAN** via le service **WinRM**.



**🛠️ Activation du Remoting**

**Sur la machine distante (cible)** : Enable-PSRemoting -Force

🔹 Lance et configure **WinRM**

🔹 Ouvre les **ports 5985 (HTTP)** et **5986 (HTTPS)** dans le pare-feu

🔹 Nécessite les droits **Admin**

⚠️ Sur Windows Serveur : activé par défaut depuis 2012

⚠️ Sur Windows Client (10, 11...) : désactivé par défaut



**🔁 Les sessions PSSession**

**✅ Créer et entrer dans une session directe :** Enter-PSSession -ComputerName DC01

📌 Le prompt change → tu es connecté à distance.



**⛔ Quitter la session :** Exit-PSSession

La session reste active, elle n’est juste plus utilisée.



**💾 Gérer une session persistante avec variable :** $session = New-PSSession -ComputerName DC01  
Invoke-Command -Session $session -ScriptBlock { Get-Service }  
Remove-PSSession -Session $session

**👀 Voir les sessions en cours :**Get-PSSession

**🔄 Reconnecter une session déconnectée :**Connect-PSSession -Session $session



**📡 Exécution directe avec Invoke-Command**

Pas besoin de maintenir une session ouverte :Invoke-Command -ComputerName DC01 -ScriptBlock { Get-Process }

👉 Peut viser **plusieurs machines** :Invoke-Command -ComputerName DC01,SRV01 -ScriptBlock { Get-Service }



**📤 Passer des variables locales à la machine distante**

**Avec $using: :** $process = 'explorer'  
Invoke-Command -ComputerName DC01 -ScriptBlock { Get-Process -Name $using:process }



**🔐 Authentification avec identifiants personnalisés**

**Demande d’identifiants à l’exécution :** Invoke-Command -ComputerName DC01 -Credential (Get-Credential) -ScriptBlock { ... }

**🧭 Importer des modules distants**



$session = New-PSSession -ComputerName DC01  
Import-PSSession -Session $session -Module ActiveDirectory

💡 Tu peux aussi utiliser un **prefix** pour éviter les conflits :

Import-PSSession -Session $session -Module ActiveDirectory -Prefix D



**🧱 Attention au multi-hop**

Par défaut, une machine A ne peut pas utiliser PSSession vers B pour C.

Il faut utiliser **CredSSP** ou **Kerberos avec délégation contrainte**.



**⚠️ Sécurité**

- **Risques élevés** si mal configuré (exécution de commandes à distance = impact direct)
- Utiliser le **pare-feu**, **filtrage IP**, **limitation des utilisateurs autorisés**, etc.
