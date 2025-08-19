# Installation GLPIAvant tout, il faut **récupérer les fichiers d’installation de GLPI**, qui se présentent sous la forme d’une archive (.tar.gz ou .zip).

**Deux méthodes :**

- **Depuis un navigateur web (sur ton poste client)** :
  - Va sur le site officiel de GLPI : <https://glpi-project.org> / Télécharge l’archive
  - Transfère-la ensuite sur ton serveur Linux via : **SFTP/FTP** (avec FileZilla, par ex.) ou **SCP/SSH** (scp ou rsync)
- **Directement depuis le serveur Linux** : wget <https://github.com/glpi-project/glpi/releases/download/X.Y.Z/glpi-X.Y.Z.tgz>

**🗂 Décompression et installation des fichiers**

Une fois l’archive sur ton serveur :

- **Décompresse-la** dans /var/www/html, qui est le répertoire par défaut d’Apache : tar -xvzf glpi-X.Y.Z.tgz -C /var/www/html
- Tu auras un dossier /var/www/html/glpi/ contenant tous les fichiers de l'application.
- **Vérifie les droits** pour qu'Apache puisse accéder au dossier : chown -R www-data:www-data /var/www/html/glpi - chmod -R 755 /var/www/html/glpi

**🌍 Accès via navigateur**

Tu peux maintenant accéder à GLPI en ouvrant un navigateur et en tapant : <http://192.168.1.100/glpi>

**⚙️ Assistant d’installation GLPI**

Une fois sur la page web, tu arrives sur **l’assistant d’installation graphique** de GLPI :

**Étapes principales : Choix de la langue** → sélectionne *Français*

**Choix de l’action** : Installation (nouvelle instance) ou Mise à jour (si tu avais déjà une version précédente) → Ici, on choisit **Installation**

**Vérification des prérequis** : Le système vérifie si tous les **modules PHP requis** sont présents. Si tout est bon : clique sur **Suivant**



**🛢 Connexion à la base de données**

Tu dois maintenant configurer l'accès à **MySQL/MariaDB** : **Adresse du serveur** : 127.0.0.1 *(localhost) /* **Nom d’utilisateur** : root *(ou un autre compte que tu as créé pour GLPI) /* **Mot de passe** : celui du compte MySQL / Ensuite : GLPI te montre la liste des bases accessibles. / Sélectionne celle que tu avais créée pour GLPI (ex: glpi_db).



**🔐 Comptes utilisateurs par défaut**

| Admin       | glpi      | glpi      |
|-------------|-----------|-----------|
| Technicien  | tech      | tech      |
| Utilisateur | normal    | normal    |
| Post-only   | post-only | post-only |

➡️ Il est **fortement recommandé** de **changer les mots de passe par défaut** ou **désactiver les comptes inutiles**. Sinon, un bandeau jaune s’affichera dans l’interface pour te le rappeler.

**🧹 Sécurisation post-installation**

Dernière étape : **supprimer ou renommer le fichier d'installation**. Va dans le dossier GLPI : cd /var/www/html/glpi/install et Supprime install.php : rm install.php
