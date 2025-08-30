# **🔍 Qu’est-ce que Get-Command ?**

`Get-Command` est **l’outil de recherche intégré** de PowerShell pour retrouver une cmdlet, une fonction, un alias ou un script dispo dans l’environnement.

📊 PowerShell 5.1 propose + de **3000 commandes** ➜ Get-Command est **essentiel** pour s’y retrouver.



## **🛠️ Utilité principale**

Permet de :

- 🔎 **Lister toutes les commandes disponibles**
- 🧭 **Trouver une commande à partir d’un mot-clé**
- 🧩 **Filtrer par nom ou verbe**

C’est LA cmdlet à connaître quand on ne connaît… pas encore les cmdlets 😄



## **🧩 Recherche par nom (-Name)**

Utilise -Name pour cibler un mot contenu dans le **nom de la commande**.

Tu peux utiliser le **joker *** pour remplacer une partie du mot.

🧪 Exemples :

- `Get-Command -Name *service` ➜ liste : Get-Service, Restart-Service, New-Service
- `Get-Command -Name *user` ➜ trouve Get-LocalUser, New-ADUser (si modules AD chargés)

📌 * = **remplace 0 ou plusieurs caractères** ➜ puissant pour les recherches approximatives



## **⚙️ Recherche par verbe (-Verb)**

Tu veux **voir toutes les cmdlets d’une même action** ? Utilise -Verb.

🧪 Exemples :

- `Get-Command -Verb Get` ➜ toutes les cmdlets de récupération
- `Get-Command -Verb Stop` ➜ ex : Stop-Service, Stop-Process, Stop-Computer

📌 Idéal quand tu sais **ce que tu veux faire**, mais pas **sur quoi** tu peux agir

