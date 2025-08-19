# Configuration**🌍 Sites hébergés**

- 🗂️ **Liste des sites** : tous les sites configurés dans IIS.
- 💻 **Default Web Site** : site créé par défaut à l’installation, hébergé dans C:\inetpub\wwwroot.



**💾 Stockage des sites**

- 🔄 Possibilité de stocker les sites ailleurs que sur le disque système :
  - 📁 Créer un répertoire global (D:\Sites_web)
  - 📁 Créer un sous-dossier par site (D:\Sites_web\intranet.tssr.lcl)
  - 📥 Copier les fichiers du site (ex : index.html) dans le dossier du site



**📝 Déclaration d’un site web**

Dans le **Gestionnaire des services Internet** :

1.  ➕ **Ajouter un site web**
2.  🏷️ Définir :

    - Nom du site
    - Chemin local ou UNC du dossier contenant le site
    - @IP spécifique (facultatif)
    - Port spécifique (ex : 8080, 443)
    - FQDN si le site est accédé par un nom (ex : intranet.tssr.lcl)



**🚫 Autorisations et restrictions**

- 🧑‍💼 **Règles d’autorisation** :
  - Autoriser ou bloquer des utilisateurs ou groupes
- 🌍 **Restrictions par @IP ou domaine** :
  - Autoriser ou bloquer une IP ou une plage IP
  - Permet de filtrer les accès à l’échelle du site ou du serveur

**🧩 Paramétrage général du serveur**

- 📄 **Document par défaut** : définit la page servie en l'absence de fichier spécifié dans l’URL (ex : index.html, default.aspx).
- 📃 **Liste des fichiers de démarrage** : ensemble de fichiers testés dans l’ordre (index.html → default.htm → etc).



**🔐 Authentification**

- 👤 **Authentification** : permet de contrôler l'accès aux ressources.
- 🔄 **Types d’authentification** :
  - Authentification anonyme
  - Authentification Windows
  - Authentification par formulaire
  - Authentification de base ou Digest



**🔑 Certificats**

- 📜 **Certificats de serveur** : utilisés pour sécuriser les connexions via HTTPS.
- 🛠️ **Gestion des certificats** :
  - Création/importation de certificats SSL
  - Attribution à un site
  - Gestion de la liaison port 443 + nom d’hôte

**📂 Exploration de répertoire**

- 🧭 **Affichage du contenu du répertoire** :
  - Active ou désactive l’affichage des fichiers si aucun index n’est présent.
  - À activer uniquement pour du débogage ou des besoins précis.
