# Types et inodes**📌 Tout est fichier !**

Sous **Linux/Unix**, **TOUT** est considéré comme un fichier : 🖥️ clavier, 💾 disque dur, 🖱️ souris, 📁 dossier… même les périphériques. ➤ Pas une figure de style, c’est une base du système 🧠

**🔎 Pas d'extension obligatoire**

Linux se **fiche de l’extension** d’un fichier ! → .txt, .jpg ne sont **que des conventions humaines**.

✅ Pour connaître le vrai type de fichier : *file monfichier*



**🧬 Les inodes : les cartes d'identité des fichiers**

Un **inode** contient **les métadonnées** du fichier :

- 🆔 Type de fichier
- 👤 UID / GID (propriétaire/groupe)
- 🔐 Permissions
- 📅 Dates (création/modification/Accès [noatime > /etc/fstab])
- 📦 Taille
- 📍 Emplacement des données (pointeurs)



⚠️ L’**inode ne contient PAS le nom du fichier** ! → Le système fait un lien entre le **nom** et l’**inode** associé 🧷
