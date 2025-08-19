# 6 - Objet sécurisé / CredentialQuand tu veux exécuter une commande **avec des droits élevés** ou sur une **machine distante**, il te faut souvent un **compte admin** ou un **compte avec des droits spécifiques**. 👉 Hors de question de **taper ton mot de passe en clair** dans un script ! PowerShell utilise des **objets sécurisés**, notamment le type SecureString et PSCredential, pour éviter toute fuite.



**🔐 Capturer des identifiants avec Get-Credential**

La cmdlet Get-Credential est **la méthode standard pour obtenir des identifiants** de manière sécurisée. Elle ouvre une petite **fenêtre de saisie** avec :

- 📛 Un champ pour l'identifiant (ex: user@domaine.local ou domaine\utilisateur)
- 🔑 Un champ pour le mot de passe, masqué

🧠 Exemple typique : $credential = Get-Credential  
Le résultat est un **objet PSCredential** contenant :

- un **nom d’utilisateur**
- un **mot de passe au format SecureString**



**🎨 Personnaliser la fenêtre d’identification**

Tu peux rendre la fenêtre plus claire pour l’utilisateur avec :

- 📝 Un message explicite et 👤 Un nom d’utilisateur pré-rempli 🎯 Ça rend le script **plus pro**, et ça **limite les erreurs humaines**.

Exemple : $credential = Get-Credential -Message "Entrez votre login Admin du Domaine" -UserName "enieni\Administrator"



**🔎 Vérifier le type d’objet**

Si tu veux t’assurer que la variable $cred contient bien un **objet PSCredential** : $cred.GetType()

Tu verras un type du genre : System.Management.Automation.PSCredential



**🚀 Utilisation des credentials dans des commandes distantes**

Quand une commande distante (ou locale avec élévation) **nécessite des droits**, tu passes l’objet $credential avec le paramètre -Credential.

🛠 Exemple avec Invoke-Command : Invoke-Command -ComputerName DC01 -Credential $credential -ScriptBlock { Get-ADUser -Filter * }

💡 Tu peux aussi utiliser $credential dans d’autres cmdlets, comme : New-PSSession Enter-PSSession Start-Process (en local avec élévation)



**⚠️ Éviter les erreurs fréquentes**

❌ N'utilise **jamais** de String pour passer un mot de passe à une commande qui attend un objet sécurisé. PowerShell **rejette les String simples** pour tout ce qui touche à l’authentification. ✅ Toujours utiliser un objet **PSCredential** contenant un **SecureString**.



**🔁 Authentification à la volée dans un script**

Tu peux démarrer ton script avec une **fenêtre d’identification automatique**, ce qui permet : de **ne pas coder les identifiants en dur et** de **réutiliser l’objet $Cred dans toutes les parties du script**

🧠 Exemple de début de script : $Cred = Get-Credential -Message "Connexion nécessaire" -UserName "eni\Admin"
