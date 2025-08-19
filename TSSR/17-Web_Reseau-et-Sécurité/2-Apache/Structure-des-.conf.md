# Structure des .conf📁 **Emplacement des fichiers de configuration des sites**

- 📂 Dossier : /etc/apache2/sites-available
- 📄 Nom du fichier de configuration :
  - Composé du nom du site
  - Suivi de l’extension .conf
  - Exemple : [www.tssr.lcl.conf](http://www.tssr.lcl.conf)



🌐 **Définition de l’hôte virtuel (VirtualHost)**

- Balise d'ouverture : <VirtualHost …>
- Balise de fermeture : </VirtualHost>
- Exemple : <VirtualHost *:80> * : toutes les adresses IP locales - 80 : port par défaut HTTP
- Remplaçables par :
  - 🎯 Adresse IPv4 / IPv6
  - 🔁 Port personnalisé : 443 (HTTPS), 81, 8182, etc.



📂 **Données du site web**

- Directive : DocumentRoot
- 📁 Dossier par défaut : /var/www
- 🗂️ Un répertoire séparé pour chaque site hébergé
- Exemple pour [www.tssr.lcl](http://www.tssr.lcl) :
  - 📄 Fichier de conf : /etc/apache2/sites-available/www.tssr.lcl.conf
  - 📁 Dossier racine : /var/www/www.tssr.lcl



🔐 **Sections de configuration**

Permettent de contrôler l’accès à des parties du site :

- 📂 <Directory> ... </Directory> : contrôle sur un répertoire
- 📄 <Files> ... </Files> : contrôle sur un fichier spécifique
- 📡 <Location> ... </Location> : contrôle sur des URL's/Paths



🧠 **Ordre de traitement des sections**

- 📌 L’ordre d’apparition dans le fichier est **important** 🔝 Les sections sont interprétées **dans l’ordre d’écriture**
  - <Directory> → du chemin le plus court au plus long
  - <Files> → ensuite
  - <Location> → en dernier
