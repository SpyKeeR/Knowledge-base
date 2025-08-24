# Aide-Tips utilisation

## **📜 Historique des commandes**

🧠 Revoir ce qu’on a tapé : `history`

⬆️⬇️ **Flèches haut/bas** : naviguer dans l’historique.

🔁 Rappeler une commande :

- `!!` → dernière commande
- `!apt` → dernière commande qui commence par apt
- `!?passwd` → dernière commande contenant passwd
- `!42` → commande n°42 dans l’historique

📁 L’historique est stocké ici : ~/.bash_history

📦 Variables liées :

- `$HISTFILE` → chemin vers le fichier .bash_history
- `$HISTSIZE` → nombre de lignes retenues



## **🔁 Autocomplétion (gros gain de temps)**

✅ Appuie sur **TAB** pour compléter :

- Une **commande** : pw + TAB → peut compléter en pwd
- Un **chemin de fichier** : `cd /e` + TAB → `cd /etc/`
- Une **variable** : tape $, puis TAB deux fois

⚠️ Si plusieurs options possibles, elles s’affichent toutes.



## **🆘 Obtenir de l’aide**

📘 Aide rapide : `commande --help`

📚 Page de manuel (version longue) : `man commande`

🧭 Navigation dans man :

- /mot → recherche dans la page
- n → résultat suivant
- q → quitter

📂 Spécifier une **section** : `man -s 5 passwd`

- Section 1 → Commandes utilisateurs
- Section 2 → Appels systèmes
- Section 3 → Fonctions des libraries
- Section 4 → Fichiers spéciaux
- Section 5 → Fichiers de configs
- Section 6 → Jeux
- Section 7 → Conventions, bonnes pratiques, RFC, autres...
- Section 8 → Commandes d'administration

`whatis` = `man -f` > Affiche toutes les sections d'une cmd

`apropos` = `man -k` > Recherche un mot-clé pour trouver une cmd

`info` : Permet d'obtenir un guide un peu plus narratif.
