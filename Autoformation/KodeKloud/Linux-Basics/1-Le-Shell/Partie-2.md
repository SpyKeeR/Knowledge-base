# Partie 2- **stdout (1)** : sortie standard
- **stderr (2)** : sortie erreur
- > fichier : stdout vers fichier (remplace)
- >> fichier : stdout **append**
- 2> fichier : stderr vers fichier
- 2>> fichier : stderr append
- > fichier 2>&1 ou &> fichier : stdout + stderr vers même fichier
- < fichier : fichier vers stdin (ex : pour un script)
- commande1 | commande2 : pipe (stdout → stdin)



📝 **Éditeur vi / vim – Le classique imbattable**

**3 modes :**

- **Command Mode** : navigation & commandes (Esc/premier mode)
- **Insert Mode** : saisie
- **Last Line Mode** : commandes commençant par :



**Entrée en mode Insert :**

- i (avant), a (après), I (début ligne), A (fin ligne), o (ligne sous), O (ligne au-dessus)



**Command Mode :**

- yy ➜ copier ligne, dd ➜ couper ligne, 3dd ➜ couper 3 lignes
- p ➜ coller, x ➜ supprimer caractère
- u ➜ undo, Ctrl+r ➜ redo
- /mot ➜ chercher, ?mot ➜ chercher en arrière, n / N ➜ suivant/précédent



**Last Line Mode :**

- :w ➜ sauvegarde
- :q ➜ quitter
- :wq ➜ save + quit
- :q! ➜ quitte sans save



🧠 **VIM – Version améliorée de vi**

- **vim** = Vi **Improved**
- Pour le définir par défaut : **update-alternatives --config editor**
- Vérif. lien : **ls -l /usr/bin/vi** ➜ souvent → /etc/alternatives/vi → /usr/bin/vim.basic

mardi 24 juin 2025

17:05



📏 **du – Analyser l’espace disque**

- **du** ➜ taille occupée par dossier/fichier
- -s ➜ **résumé total**, pas de détails (≠ sans -s, affichage récursif)
- -h ➜ en taille humaine (Ko, Mo…)



📦 **tar – Archivage (pas compression)**

- -c ➜ **create archive**
- -t ➜ **test / list content**
- -x ➜ **extract**
- **-f** ➜ fichier d'archive
- -z/-j/-J ➜ compression : gzip (-z), bzip2 (-j), xz (-J)
- Exemple : **tar -czf archive.tar.gz dossier/** ➜ crée et compresse en gzip



🌀 **Compression – Algo & outils**

- Algo : gzip (.gz), bzip2 (.bz2), xz (.xz)
- Compression ➜ **gzip, bzip2, xz**
- Décompression ➜ **gunzip, bunzip2, unxz**
- Lecture directe sans extraire ➜ **zcat, bzcat, xzcat**



🔍 **Recherche de fichiers & contenu**

- **locate** ➜ ultra rapide, utilise une base (à mettre à jour avec updatedb)
- **find** ➜ recherche directe sur le FS (lent mais puissant)

ex : **find / -name fichier.txt**

- **grep** ➜ chercher dans le contenu :
  - -i : ignore la casse
  - -r : récursif
  - -v : inverse (exclut)
  - -w : mot entier
  - -Ax / -Bx : affiche x lignes **après / avant** le pattern



📤 **tee – Dupliquer un flux**

- Écrit la sortie dans un fichier **ET** affiche à l’écran (-a ➜ mode append)
- Pratique pour logger tout en visualisant en live !
