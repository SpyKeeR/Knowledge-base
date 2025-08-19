# Alias Bash**🧠 C’est quoi un alias ?**

Un **alias** = un raccourci personnalisé pour une commande (ou une suite de commandes).

💡 Objectif : taper moins, aller plus vite, et éviter des erreurs.

**⚙️ Créer un alias >** *alias LL='ls -al'*

- Pas d’espace autour du =
- Utilise ' ou " pour encadrer la commande
- L’alias peut **enchaîner** plusieurs commandes avec des pipes |



**🛡️ Alias sécurisants >** *alias rm='rm -i'*

➡️ Demande une confirmation avant suppression. Super utile pour éviter les boulettes !



**📋 Voir les alias actifs >** *alias*

- Affiche **tous** les alias de la session
- Inclut ceux définis automatiquement (par ton shell ou ta distro)



**♻️ Rendre un alias permanent**

1.  Ouvre ~/.bashrc
2.  Ajoute ton alias à la fin du fichier :

*alias maj='sudo apt update && sudo apt upgrade'*

3.  Recharge le fichier :

*source ~/.bashrc*



**❌ Supprimer un alias >** *unalias LL*

- Supprime l’alias **dans la session en cours**
- Ne modifie pas le .bashrc (fais-le manuellement si besoin)

**🔥 Exemples utiles**

*alias ll='ls -lh'  
alias gs='git status'  
alias c='clear'  
alias ..='cd ..'  
alias ...='cd ../..'*

**💡 Astuce** : Si besoin d'utiliser des alias avec des conditions ou définir des paramètres d'options présent dans un alias traditionnel, utiliser les fonctions shell.  
**💡 Astuce** 2 : Pour ne pas appeler l'alias, mais la commande originelle prefixer d'un \ > \ls au lieu de ls
