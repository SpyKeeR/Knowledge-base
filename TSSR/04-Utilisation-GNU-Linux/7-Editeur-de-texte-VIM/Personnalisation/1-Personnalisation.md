# PersonnalisationVim est **hautement personnalisable** : tu peux adapter ton environnement selon tes préférences, que ce soit pour coder, configurer ou éditer des fichiers système. Toutes les options peuvent être **temporaires** (via :set dans une session) ou **permanentes** (via le fichier ~/.vimrc).



**📁 Modifier les paramètres de base**

**Fichier de config** à créer ou éditer : *vim ~/.vimrc*

Dedans, tu ajoutes les commandes suivantes pour que tes réglages soient appliqués à chaque lancement.



**🔢 Affichage des numéros de ligne**

- Activer temporairement : :set number
- Désactiver : :set **no**number
- Pour que ce soit permanent : ajoute set number dans ~/.vimrc



**🔧 Indentation automatique**

- Activer l’auto-indentation : :set autoindent
- Largeur d’un tab : :set tabstop=4
- Remplacer les tabulations par des espaces : :set expandtab

À ajouter dans ~/.vimrc :

set autoindent  
set tabstop=4  
set expandtab



**🎨 Changer les couleurs et thèmes**

- Voir les thèmes dispo : :colorscheme
- Appliquer un thème : :colorscheme desert
- Rendre ce thème permanent : ajoute colorscheme desert dans ~/.vimrc

Tu peux aussi télécharger des thèmes personnalisés et les placer dans ~/.vim/colors

**🔧 Commandes de personnalisation utiles**

- **:set all** > Affiche toutes les options existantes
- **:set** > Affiche les options actives sur la session
- **:set option?** > Affiche la valeur actuelle d’une option précise.
- **:help** ou **:h** ou **:help** option > Affiche l'aide de VIM
- **:set autoindent** > Active l’indentation automatique à chaque nouvelle ligne
- **:set nocompatible** > Active les déplacements et fonctions Linux de VIM
- **:set showmode** > Affiche dans la barre du bas le mode actif
- **:set number** > Affiche les numéros de ligne.
- **:set tabstop=4** > Définit la largeur d’une tabulation à 4 espaces
- **:syntax on** > Active la coloration syntaxique selon le langage détecté
