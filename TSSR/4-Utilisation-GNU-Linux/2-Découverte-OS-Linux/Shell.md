# ShellLe **Shell** est l’interface texte qu’on utilise pour discuter avec un système Unix/Linux. 👉 C’est un **interpréteur de commandes** = CLI (Command Line Interface).

**💬 Exemple simple :** Commande : echo "Bonjour le monde" > Résultat affiché : Bonjour le monde



**🔁 Les Flux standards (Streams)**

Quand tu tapes une commande, le Shell utilise 3 **flux** pour traiter les infos :

- **stdin (0)** ➡️ entrée standard (le clavier 🧑‍💻)
- **stdout (1)** ➡️ sortie standard (écran 🖥️)
- **stderr (2)** ➡️ sortie d’erreur (⚠️ les messages d’erreurs)

💡 Ces flux peuvent être **redirigés** vers un fichier, une commande, un périphérique, etc.



**💡 Les différents types de Shells**

Voici quelques **Shells** connus sous Unix/Linux :

- **sh** : le classique (Bourne Shell)
- **csh** : syntaxe proche du langage C
- **ksh** : le Korn Shell, amélioré et compatible avec sh
- **bash** : Bourne Again SHell, le plus répandu sur Linux 🐧
- **zsh** : Shell moderne, très apprécié pour :
  - L’**auto-complétion intelligente /** Un **prompt personnalisable /** La **correction automatique /** Une intégration avec **Oh My Zsh** (Perso. ++)



**🔠 Sensibilité à la casse**

Linux fait la différence entre **majuscules et minuscules** :

- ✅ echo "Bonjour" → OK / ❌ Echo "Bonjour" → Erreur : commande introuvable



**💻 Le prompt**

Le **prompt** est l’invite du Shell, il ressemble souvent à ça : user30@Host $



**⚙️ Commandes, options et arguments**

1.  **Commande** = l’action (ex : ls)
2.  **Option(s)** = modifie le comportement (ex : -l pour un affichage long)
3.  **Argument(s)** = ce que tu veux traiter (ex : un fichier ou un dossier)

    - **who → sans argument**
    - **ls /etc → un argument (/etc)**
    - **head -n 10 fichier.txt → une option (-n 10) + un fichier**
    - **cp fichier.txt /home/user → deux arguments (source + destination)**

📌 Les options commencent souvent par - ou --, parfois +, et peuvent être groupées (ls -la)
