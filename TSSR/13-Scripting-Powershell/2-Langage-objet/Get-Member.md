# Get-Member**🔧 Qu’est-ce que Get-Member ?**

Get-Member est une **cmdlet clé** pour explorer le contenu des **objets PowerShell**. Elle te montre :

- Le **type de l’objet retourné**
- Les **méthodes** disponibles
- Les **propriétés** exposées

Elle s’utilise **après** une commande (souvent un Get-) via le **pipe** |, qui s'obtient avec **ALT GR + 6**.



**🧪 Exemple avec Get-Service**

Commande : Get-Service | Get-Member

→ Affiche que l’objet retourné est de type System.ServiceProcess.ServiceController

Tu verras :

- Méthodes : Start(), Stop(), Pause(), Continue(), Dispose()...
- Propriétés : ServiceName, Status, StartType, DisplayName, etc.



**🛠️ Quand utiliser Get-Member ?**

✅ Sur les **commandes Get-** (ex : Get-Process, Get-Item)

🚫 À éviter sur les **cmdlets Set-**, car elles attendent souvent des paramètres obligatoires non fournis via Get-Member

C’est un **outil d’exploration** — il ne modifie rien, il t’apprend **ce que tu peux faire avec un objet**.



**🧭 Les 3 commandes clés de PowerShell**

💡 Pour bien débuter et t’orienter dans PowerShell, retiens ce trio gagnant :

- Get-Command → Trouver les cmdlets existantes
- Get-Help → Comprendre leur usage
- Get-Member → Découvrir les **objets** retournés par ces cmdlets



**📌 Résumé**

Get-Member = **scanner d’objets PowerShell** 🧬

➡️ Utilise-le **systématiquement** pour savoir ce que contient une commande Get-.

C’est **essentiel** pour bien comprendre les objets, et surtout **pour automatiser intelligemment**.

**Pipe** + Get-Member = vision **complète** de ce que tu peux exploiter dans l'objet.
