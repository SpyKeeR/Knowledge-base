# Functions/return/shebang**🐚 Choisir le bon Shell : #!/bin/bash**

Utilise toujours un **shebang** en tête de script si tu veux garantir un comportement cohérent :

➡️ #!/bin/bash force l’usage de **bash**, même si ton shell par défaut est autre (genre sh ou dash).

Ex. : {0..10} ne fonctionne pas sans bash. Si tu lances ton script avec sh, il ne développera pas la séquence.

**✅ Code de sortie : $? à la rescousse**

Chaque commande retourne un **exit code** : 0 → succès 👍 ≠0 → échec ⚠️

Affiche-le via echo $? après chaque commande pour savoir si elle a échoué.

**Bon réflexe :** dans tes scripts, utilise exit 1 ou return 1 pour **signaler un échec**, sinon ça retourne 0 même si ça a planté...

Exemple : Si rocket-status retourne failed ➡️ fais exit 1 (ou return 1 en fonction).

**🔄 Fonctions : un code modulaire et propre**

Regroupe les blocs réutilisables dans des **fonctions** : ➡️ function nom() { ... } ou nom() { ... }

Paramètres accessibles via $1, $2...

Ex. : launch-rocket "mars-mission" → Et dedans tu fais tout : mkdir, rocket-add, etc.

**Bonnes pratiques :**

- Défini les fonctions **avant** de les appeler
- return ≠ exit : return reste dans le script, exit le termine
- Pour retourner une valeur : echo + $(...)

**🧠 Développer en CLI : Vim & menu interactif** 🔥 Vim + syntaxe colorée = erreurs repérées à la volée !



**🔍 Analyse des scripts : ShellCheck**

Utilise **ShellCheck** pour repérer les erreurs courantes et améliorer la qualité : ➡️ apt install shellcheck ➡️ shellcheck monscript.sh

Il détecte : syntaxe, oublis de quotes, pièges courants, etc.



**🖥️ IDE pour le scripting : PyCharm, VS Code, Atom**

Si tu préfères un environnement visuel :

- **PyCharm CE** + plugin bash
- **VS Code** avec extension Shell Script

Avantages :

✔️ Highlighting

✔️ Linting temps réel

✔️ Suggestions de bonnes pratiques

Pour un style pro, va voir le [Shell Style Guide de Google](https://google.github.io/styleguide/shellguide.html) 💡
