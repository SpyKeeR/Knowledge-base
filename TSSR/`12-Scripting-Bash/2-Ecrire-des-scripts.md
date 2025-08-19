# 2 - Ecrire des scriptsUn **script Shell** est un fichier **texte brut** contenant une suite de **commandes Bash**. Tu peux le créer et l’éditer avec un éditeur comme **vim**, ou tout autre éditeur en ligne de commande ou graphique.

**Le Shell lit les lignes** et **exécute chaque commande dans l’ordre**. ➡️ L’ordre des lignes **est crucial**, car chaque instruction peut dépendre de la précédente.



**🧭 Le Shebang : choisir le bon interpréteur**

- 🔝 Doit être **placé en 1ère ligne du script** ✍️ Format : #!chemin/interpréteur (ex : #!/bin/bash)
- ✅ Chemins courants : /bin/bash, /bin/sh, /usr/bin/python3, /usr/bin/perl…
- 🛠️ Vérifie avec which bash, which python3, etc.



**📝 Commenter ses scripts**

- 💡 Utile pour **clarifier**, **corriger plus tard**, **aider un tiers** 🔕 Tout texte après # = **non interprété**
- ✅ On peut commenter :
  - Avant une ligne d’instruction
  - Sur la même ligne qu’une commande (précédé d'un espace : " # commentaire")
- 📄 Exemples de commentaire standard ou avancé :
  - # Script de test Hello World
  - En-tête structuré type : # FILE: script.sh, # AUTHOR: Maxime, # CREATED: 11.07.2025, etc.



**🔐 Rendre le script exécutable**

- 📦 Un script n’est **pas exécutable par défaut**
- ➕ Ajoute les droits avec :
  - chmod +x monscript.sh
  - ou plus ciblé : chmod ug+x monscript.sh
- 🧠 Le droit x est obligatoire pour pouvoir **lancer directement le fichier**



**🧪 Suivre l’exécution du script (débogage)**

**🛠️ Méthode 1 : Mode trace**

- ➕ Lance avec : bash -x monscript.sh
- 🧾 Affiche chaque commande avant exécution avec un +
- 🔍 Très utile pour repérer les erreurs de logique ou de syntaxe



**🛠️ Méthode 2 : Insertion manuelle de commandes de debug**

- 📍 Utilise echo pour afficher des variables ou infos internes
- ⏸️ read peut servir à **mettre une pause** entre deux blocs du script - ex : echo "valeur de x : $x" puis read
- 🎯 Permet de contrôler l'état du script à un moment donné

**⚠️ Avant mise en prod - Commente toutes les commandes de debug** avant déploiement ! ex : # echo "debug...", # read
