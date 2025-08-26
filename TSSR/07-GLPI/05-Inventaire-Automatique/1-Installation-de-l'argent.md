L’**agent d’inventaire GLPI** est un petit logiciel qu’on installe sur les postes à inventorier.

➡️ Il scanne le matériel, les logiciels, les périphériques… puis il **envoie ces infos à GLPI**.

📌 Il s’agit d’un **fork de FusionInventory**, donc basé sur la même logique, mais :

- 🆕 Il utilise un **nouveau protocole** de comm’
- 🎁 Il apporte des **fonctionnalités supplémentaires**
- 🔄 Il est **compatible avec le plugin FusionInventory** si tu l’utilises déjà

- Installation automatique via l’utilisation d’un script VBS (Possibilité de déploiement via GPO)

🧠 [**Documentation complète (à garder sous le coude)**](https://glpi-agent.readthedocs.io/en/latest) et [DL Sur GitHub](<https://github.com/glpi-project/glpi-agent/releases>)

 

## 💻 **Installation sous Windows**

👣 **Étapes principales** : 
- **Télécharge l’installateur,**
- **Lance-le**, puis suis l’assistant (Welcome screen / Licence utilisateur / Choix du dossier d’installation)
- **Setup Type** → Choisis *Typical* (standard)
- **Target URL** → Indique l’URL du serveur GLPI : ➡️ [**http://glpi.tondomaine.tld/front/inventory.php**](http://glpi.tondomaine.tld/front/inventory.php)

### 🛠️ **Personnalisation possible** :

- 🔐 **SSL** et **proxy** si tu es derrière un réseau un peu complexe
- 🌍 **HTTP Server Options** → autoriser certaines IP à accéder au mini serveur de l’agent
- 🏷️ **TAG** → très utile pour classer automatiquement les postes dans la bonne entité
- 🐞 **Debug Options** → activer les logs détaillés pour le support
- 🔁 Cocher l’option *Démarrer l’inventaire après installation*
- 🧱 Tu peux aussi installer l’agent **comme un service Windows**

🌐 **Accès au client** : Une fois installé, tu peux voir l’état de l’agent sur le port 62354 via ton navigateur. Ex : <http://localhost:62354>

 

## 🐧 **Installation sous Linux / Unix / BSD**

### 📦 **Téléchargement** :

- Pour **Debian/Ubuntu** → paquets fournis par le projet
- Pour **CentOS/RHEL** → via le dépôt EPEL
- Pour **FreeBSD** et **Solaris** → paquets dédiés

### 📥 **Installation** :

- Télécharge le paquet recommandé
- Installe-le via la commande de ton système
- Modifie la conf de l’agent : agent.cfg (fichier permanent) et 00-install.cfg (volatile, modifié à chaque mise à jour)

🔧 **Paramètres clés** à modifier : 
- server= → URL du serveur GLPI
- tasks= → tâches à exécuter, ex. INVENTORY
- tag= → pour classer la machine dans la bonne entité
- Autres options possibles selon ton réseau

💡 **Tips** : fais attention aux **droits d’exécution** et au **langage Perl**, parfois requis pour certains modules.

 
## 🍏 **Installation sur macOS**

📦 Le projet fournit des **packages MPKG** prêts à installer.

Même logique que sous Linux : → tu télécharges, tu installes, tu paramètres (TAG, URL serveur, etc.)



