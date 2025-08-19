# Gestion**🖥️ Gestion via systemctl (service systemd)**

Apache2 étant un service système, il se gère avec systemctl en ligne de commande.

| **Commande** | **Description** |
|----|----|
| systemctl start apache2 | 🚀 Démarre le service Apache |
| systemctl stop apache2 | 🛑 Arrête le service Apache |
| systemctl restart apache2 | ♻️ Redémarre Apache avec coupure complète (recommandé) |
| systemctl reload apache2 | 🔄 Recharge la configuration sans interrompre les connexions |
| systemctl enable apache2 | 🔌 Active le démarrage automatique au boot |
| systemctl disable apache2 | 🚫 Désactive le démarrage automatique |
| systemctl status apache2 | 📊 Affiche l’état du service (démarré, erreurs, derniers logs…) |





**⚙️ Gestion des modules Apache**

Les modules permettent d’ajouter des fonctionnalités (ex : proxy, SSL, etc.).

| **Commande**        | **Description**        |
|---------------------|------------------------|
| a2enmod nom_module  | ✅ Active un module    |
| a2dismod nom_module | ❌ Désactive un module |

🔄 Nécessite un redémarrage (restart ou graceful) pour prendre effet.





**🌐 Gestion des sites (Virtual Hosts)**

Les sites web sont gérés via des fichiers .conf dans /etc/apache2/sites-available/.

| **Commande**               | **Description**                              |
|----------------------------|----------------------------------------------|
| a2ensite nom_fichier.conf  | 🟢 Active un site web (publication en ligne) |
| a2dissite nom_fichier.conf | 🔴 Désactive un site web (dépublication)     |

📌 Comme pour les modules, un redémarrage est requis après chaque modification.

**🧰 Commandes spécifiques à Apache : apache2ctl**

apache2ctl est un outil dédié à Apache, indépendant du système d’exploitation, permettant une gestion fine

| **Commande** | **Description** |
|----|----|
| apache2ctl start | 🚀 Démarre Apache |
| apache2ctl stop | 🛑 Arrête Apache |
| apache2ctl restart | ♻️ Redémarre Apache avec **vérification de la configuration** |
| apache2ctl reload | 🔄 Recharge la configuration |
| apache2ctl graceful | 🌿 Redémarre en douceur (pas de coupure des connexions en cours) |
| apache2ctl graceful-stop | 🌿🛑 Arrêt en douceur (sans couper brutalement les connexions) |
| apache2ctl configtest | 🧪 Vérifie la syntaxe des fichiers de conf avant redémarrage |
| apache2ctl status | 📋 Affiche un **état simplifié** du service |
| apache2ctl fullstatus | 📈 Affiche un **rapport détaillé** (nécessite mod_status activé) |
| apache2ctl -S | 🌐 Affiche les **Virtual Hosts actifs** |
| apache2ctl -l | 🧩 Liste les modules **compilés statiquement** |
| apache2ctl -M | 🧩 Liste les modules **chargés dynamiquement** |
| apache2ctl -h ou --help | 📚 Liste toutes les options disponibles |
