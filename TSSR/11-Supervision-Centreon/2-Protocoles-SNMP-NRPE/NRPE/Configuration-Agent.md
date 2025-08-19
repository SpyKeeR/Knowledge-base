# Configuration Agent**💾 Installation de NSClient++**

- Téléchargement via un fichier **.msi**
- L’installation en mode **"Generic"** est suffisante pour un usage supervision standard
- L’assistant permet :
  - Ajout des **IP autorisées** à interroger l’agent (*ex. : IP du superviseur + 127.0.0.1*)
  - Définition du **mot de passe** (pour NSClient et accès web, penser à le personnaliser)
  - Activation de **modules** utiles : check plugins, NRPE server, web server

⚙️ Un **service Windows** est installé en démarrage automatique.



**📁 Fichier de config principal**

Le fichier **nsclient.ini** se trouve ici : C:\Program Files\NSClient++\

🛠️ Il reste modifiable à la main, mais depuis les dernières versions, un **serveur web intégré** permet de tout piloter plus facilement.



**🌐 Interface Web NSClient++**

Accessible via : [**https://localhost:8443**](https://localhost:8443)

🔑 Connexion avec le **mot de passe défini à l’installation**

📋 Menus disponibles :

- **Modules** : activer les plugins nécessaires (ex. CPU, disque, RAM)
- **Settings** : configurer les services NRPE, Web, NCSA…
- **Queries** : tester les plugins avec des requêtes manuelles
- **Log** : voir les événements générés
- **Console** : ligne de commande directe NSClient++
- **Changes** : gérer les sauvegardes et annulations de modif
- **Help** : accès à la doc officielle
- **Control** : redémarrer / arrêter le service
- **Logout** : fermer la session

🚫 *Ne pas utiliser Internet Explorer pour y accéder (même si tu veux vivre dangereusement).*



**🔐 Sécurité NRPE**

Le **chiffrement SSL** est possible, mais **non abordé ici** (gestion des certificats jugée trop complexe).

En mode **"insecure"**, NRPE fonctionne en clair.

🔐 ⚠️ Ne jamais exposer ce port sur Internet sans protection réseau !
