# Dépôts**⚙️ Configuration de base des dépôts**

Quand tu installes Debian, les **dépôts officiels** sont automatiquement configurés selon ta version.

Ils permettent de :

- **Mettre à jour** toute la distribution (surtout pour les patchs de sécurité).
- **Installer** de nouveaux logiciels à la demande.

Mais tu peux aussi **ajouter d’autres dépôts** selon tes besoins !



**➕ Pourquoi ajouter des dépôts supplémentaires ?**

- **Accès à des logiciels plus récents** ou **non disponibles** dans Debian.
- **Mises à jour automatiques** quand le logiciel évolue.
- **Gestion automatique des dépendances** (librairies, paquets nécessaires).

👉 Petit parallèle Windows : comme si tu ajoutais un pack de **DLL** pour faire tourner un logiciel !



**📝 Le fichier sources.list**

Le cœur de la config des dépôts, c’est le fichier : /etc/apt/sources.list

deb <http://ftp.fr.debian.org/debian> buster main  
deb-src <http://ftp.fr.debian.org/debian> buster main  
deb [http://security.debian.org/debian-security buster/updates](http://security.debian.org/debian-security%20buster/updates) main  
deb <http://ftp.fr.debian.org/debian> buster-updates main



**🛠 Décryptage d’une ligne sources.list**

- **deb** → paquets **binaires** (installables).
- **deb-src** → paquets **sources** (à recompiler).
- **URL** → adresse du serveur (http, ftp, file ou CD-ROM).
- **Nom de version** → sélection de la branche.
  - Oldstable, ancienne version
  - Stable, version stable (suit la release du canal stable)
  - Buster, pour éviter les erreurs en mise à jour (en cas de stay sur une version qui sort du canal stable)
  - Testing, prochaine version stable
  - Unstable, version Sid
  - Experimental, pour tester les paquets en cours de développement
- **Section** → catégories :
  - **main** → logiciels 100% libres.
  - **contrib** → logiciels libres avec dépendances non-libres.
  - **non-free** → logiciels non-libres.
