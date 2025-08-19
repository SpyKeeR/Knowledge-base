# Chemins**🌳 Arborescence Unix (FHS)**

📌 **Une seule racine** :

- Contrairement à Windows (C:, D:, etc.), **tout commence par /**.
- Exemple : /home/user30/rep01



📍 Tous les répertoires, périphériques, disques, clés USB… sont **intégrés dans cette unique arborescence** à travers des **points de montage**.

📁 Quelques dossiers classiques :

- /home → contient les dossiers utilisateurs
- /etc → fichiers de config
- /bin, /sbin, /usr/bin → commandes système
- /media, /mnt → points de montage des périphériques



**🧭 Chemins absolus vs relatifs**

**📌 Chemin absolu :**

- Commence toujours par /
- Donne le **chemin complet**, depuis la racine
- Exemple : *cat /home/user30/rep01/bonjour.txt*

**🔁 Chemin relatif :**

- Part de l’endroit où tu te trouves (pwd)
- Utilise les noms de dossiers ou des raccourcis

&nbsp;

- Exemple : *cd dossier01*

🔼 Monter d’un dossier : *cd ..*

🔁 Rester dans le dossier courant (utile dans des scripts, par ex.) : *cd .*
