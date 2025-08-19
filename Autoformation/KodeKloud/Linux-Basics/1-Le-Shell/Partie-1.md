# Partie 1🌡️ **Variables d’environnement**

- VAR=valeur : variable dispo uniquement dans le shell courant
- **export** VAR=valeur : dispo dans les shells enfants
- Persistance utilisateur : ajouter dans ~/.bashrc ou ~/.profile
- Persistance system : **sudo nano /etc/environment** (global, pour tous)



🔍 **Trouver une commande avec which**

- **which nom_commande** ➜ donne le chemin complet vers l’exécutable
- ⚠️ Ne montre que la **1re occurrence** trouvée dans le PATH



🎨 **Personnaliser le prompt avec la variable PS1**

- Exemple simple ➜ PS1="ubuntu-server: "
- Exemple avancé ➜ PS1="[\d \t \u@\h:\w ] $ "  
  🧩 Signification des éléments :

  - \d : la date au format « Jour Mois date » (« mar. mai 26 »)
  - \e : un caractère d’échappement ASCII (033)
  - \h : le nom d’hôte abrégé (hostname)
  - \H : le nom d’hôte complet (FQDN)
  - \n : saut de ligne
  - \r : retour chariot
  - \s : le nom du shell
  - \t : l’heure actuelle au format 24h avec secondes
  - \T : l’heure actuelle au format 12h avec secondes
  - \ : l’heure actuelle au format 12h avec am/pm
  - \A : l’heure actuelle au format 24h sans secondes
  - \u : le nom d’utilisateur
  - \w : le répertoire de travail courant, avec $HOME abrégé en ~
  - \W : le nom de base du répertoire courant, avec $HOME abrégé
  - \ : affiche # si l’UID effectif est 0 (root), sinon $

🐚 **Commandes internes vs externes**

- **Internes (built-in)** : intégrées au shell ➜ **cd, echo, pwd, set**, etc.
- **Externes** : binaires dans le système ➜ **mv, cp, date, uptime**, etc.
- 🔍 Vérification avec type : **type mv** → affiche le chemin et s’il est en cache (*hashed*)

🧠 **Pushd / Popd : navigation rapide entre répertoires**

- **pushd** ajoute un dossier dans la pile et s’y rend ➜ **pushd /etc**
- **popd** revient au précédent dossier sauvegardé ➜ **popd**  
  💡 Astuce : facilite les allers-retours entre 2-3 répertoires, + pratique que cd -



📥 **Redirection d’entrée avec cat**

- **cat > fichier.txt** : attend une saisie clavier → Ctrl+D pour terminer
- Crée ou écrase un fichier avec le contenu tapé en direct (entrée standard)



📚 **Aide système : man, whatis, apropos**

- **man** : affiche le manuel complet d’une commande
- **whatis** : résumé rapide de la commande
- **apropos** : cherche une commande à partir de mots-clés (utile si tu ne sais plus le nom exact)



🔄 **Changer le shell par défaut d’un utilisateur**

- **sudo chsh -s /bin/sh nom_utilisateur**
- 🔄 Changement appliqué au prochain login (affecte les scripts, prompt, comportement)



🐚 **Types de shells principaux**

- sh (Bourne), bash (Bourne Again), zsh, ksh, csh, tcsh
- bash = par défaut sur la majorité des distros GNU/Linux, très utilisé
