# Aide-Tips utilisation**📜 Historique des commandes**

🧠 Revoir ce qu’on a tapé : history

⬆️⬇️ **Flèches haut/bas** : naviguer dans l’historique.

🔁 Rappeler une commande :

- !! → dernière commande
- !apt → dernière commande qui commence par apt
- !?passwd → dernière commande contenant passwd
- !42 → commande n°42 dans l’historique

📁 L’historique est stocké ici : ~/.bash_history

📦 Variables liées :

- $HISTFILE → chemin vers le fichier .bash_history
- $HISTSIZE → nombre de lignes retenues



**🔁 Autocomplétion (gros gain de temps)**

✅ Appuie sur **TAB** pour compléter :

- Une **commande** : pw + TAB → peut compléter en pwd
- Un **chemin de fichier** : cd /e + TAB → /etc/
- Une **variable** : tape $, puis TAB deux fois

⚠️ Si plusieurs options possibles, elles s’affichent toutes.



**🆘 Obtenir de l’aide**

📘 Aide rapide : commande --help

📚 Page de manuel (version longue) : man commande

🧭 Navigation dans man :

- /mot → recherche dans la page
- n → résultat suivant
- q → quitter

📂 Spécifier une **section** : man -s 5 passwd

(section 5 = fichiers de config)

Whatis = man -f > Affiche toutes les sections d'une cmd

Apropos = man -k > Recherche un mot-clé pour trouver une cmd

Info : Permet d'obtenir un guide un peu plus narratif.
