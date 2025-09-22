# Export Agent/CLients

📦 Pour le déploiement sur les postes clients, il est nécessaire d’**installer le package** `openvpn-client-export` : **System** > **Package Manager** > **Installed Packages**.



**🧰 Génération des fichiers client**

Depuis **pfSense**, après avoir configuré OpenVPN et installé le package `openvpn-client-export`, il est possible d’exporter les fichiers nécessaires à l’installation sur les postes clients :

- 🔐 **Use Microsoft Certificate Storage** :  
  Permet de stocker le certificat dans le **magasin de certificats Windows**, évitant de laisser des fichiers sensibles sur le disque.

- 🛡️ **Password Protect Certificate Export** :  
  Option permettant de protéger le certificat exporté par un mot de passe.

- 🚫 **auth-nocache** :  
  Ajoute une directive dans la configuration du client pour **éviter la mise en cache des identifiants AD** sur la machine, renforçant la sécurité.



**💻 Téléchargement du package client**

- 📥 Le fichier exporté contient :
  - Le **programme d’installation d’OpenVPN**
  - Le **fichier de configuration (.ovpn)**
  - Le **certificat client intégré**
- ✅ **Aucune configuration manuelle n’est requise côté client**.



**🔄 Installation côté client**

- 🧩 **Exécution du fichier d’installation** :  
  L’utilisateur lance le programme téléchargé : Install Now

- 🔑 **Connexion** :
  - Lancement d’OpenVPN GUI
  - Saisie des **identifiants Active Directory** lors de la connexion
- 🌐 **Accès distant** :
  - Une fois connecté, l’utilisateur accède **au réseau local de l’entreprise** comme s’il était sur site.
