# Déploiements/Partage**🔐 Partage et autorisations des imprimantes**

Comme un dossier partagé, une **imprimante doit être partagée** pour être dispo en réseau.

🛠️ Les autorisations se règlent via l’onglet **Sécurité**, mais elles diffèrent un peu des fichiers classiques.

**Autorisations de base** :

🖨️ **Imprimer** (envoyer un job),

📑 **Gérer les documents** (annuler/modifier les impressions),

⚙️ **Gérer l’imprimante** (modifier la config, redémarrer le périphérique)

**Autorisations étendues** :

👁️ **Lire/modifier les autorisations** + **prendre possession de l’imprimante** (cas avancés)

➡️ En pratique, on reste majoritairement sur **les 3 permissions principales** : imprimer / gérer imprimante / gérer documents



**🛠️ Méthodes de déploiement**

Il existe plusieurs méthodes pour installer les imprimantes sur les postes. Le choix dépend du niveau d'automatisation souhaité.



**🖱️ Déploiement manuel**

📁 L’utilisateur ouvre [\serveur-impression](file://serveur-impression), double-clique sur l’imprimante → **installation automatique** avec récupération du **pilote depuis le serveur**.

⚠️ Cette méthode est simple, mais dépend d’une action manuelle (ou script automatisé).



**🧠 Déploiement par script**

👨‍💻 En PowerShell ou VBS, on peut **automatiser l’ajout d’imprimantes** à l’ouverture de session.

💡 Astuce : utile en petit environnement ou si tu ne veux pas toucher aux GPO.



**🏢 Déploiement via GPO**

💼 Solution idéale en entreprise :

🎯 Une **GPO cible une OU** (ex. : utilisateurs du service RH → imprimante RH)

🔁 Application silencieuse à chaque démarrage/session

🛡️ Gère aussi les **droits utilisateurs, installation de pilotes** et la **suppressions des anciennes imprimantes**
