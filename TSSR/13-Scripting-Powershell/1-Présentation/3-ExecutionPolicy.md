# **🛡️ Qu'est-ce que la stratégie d'exécution ?**

PowerShell **bloque l’exécution de scripts par défaut** 🔒.

Cette stratégie est là pour **protéger ton système** contre les scripts malveillants non signés. 

Mais pour développer ou tester, il faut souvent l’assouplir.



## **🧰 Modifier la stratégie : Set-ExecutionPolicy**

La commande pour ajuster ce niveau est : `Set-ExecutionPolicy -ExecutionPolicy <valeur>`

Elle permet de **définir le niveau de sécurité** autorisé pour l’exécution des scripts.

Exemple : `Set-ExecutionPolicy -ExecutionPolicy Unrestricted`

📌 **À lancer en admin** ! Sinon, la commande échouera sans effet.



## **🧱 Les différents niveaux d'exécution**

| **Niveau** | **Description rapide** |
|----|----|
| 🔐 **Restricted** | Par défaut. Aucun script n’est autorisé |
| ✍️ **AllSigned** | Seulement les scripts **signés** sont exécutables |
| 🌐 **RemoteSigned** | Scripts locaux : OK / Scripts distants : signés |
| 🚫 **Unrestricted** | Tout peut s'exécuter (⚠️ Risqué) |
| 🪄 **Bypass** | Aucune restriction, pas même d'avertissement |
| ⚙️ **Default** | Réinitialise selon le contexte (souvent Restricted) |
| ❓ **Undefined** | Aucun niveau défini → hérite de la stratégie du système |





## **⚠️ Conseils & bonnes pratiques**

- Ne laisse **jamais** Unrestricted en permanence.
- Privilégie RemoteSigned pour bosser en dev.
- Utilise `Get-ExecutionPolicy` pour vérifier le niveau actif.
- Tu peux aussi spécifier la portée via -Scope (Process, CurrentUser, LocalMachine).

