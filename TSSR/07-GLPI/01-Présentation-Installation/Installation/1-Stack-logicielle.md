# Stack logicielle

GLPI est écrit en **PHP**, donc il a besoin d’une **infrastructure logicielle spécifique** pour fonctionner. En gros, on ne peut pas le lancer tout seul : il lui faut un environnement complet, qu’on appelle **LAMP** (Linux – Apache – MySQL/MariaDB – PHP).



## **🔥 Serveur Web (Apache, Nginx ou IIS)**

- **Apache** est le serveur web le plus utilisé avec GLPI.
- Il prend en charge les **requêtes HTTP** faites par les navigateurs et sait comment traiter les fichiers .php.
- Alternatives possibles : **IIS** (Windows) ou **Nginx** (mais Apache reste le plus courant et recommandé avec GLPI).



## **🧠 PHP (le langage de GLPI)**

PHP est **l’interpréteur** qui exécute le code de GLPI.

Mais attention, pour que GLPI fonctionne bien, **plusieurs modules PHP complémentaires sont nécessaires entre autres** :

| php-mysql | Pour communiquer avec la base MySQL/MariaDB |
|----|----|
| php-ldap | Pour l’authentification avec Active Directory |
| php-imap | Pour créer des tickets à partir d’e-mails |
| php-xmlrpc | Pour faire des appels à distance (RPC) |
| php-intl | Pour l’internationalisation |
| php-apcu | Pour le cache (accélère les performances) |
| php-bz2 | Pour gérer les fichiers compressés |
| php-cas | Pour l’authentification unique (SSO) avec Windows (Single Sign-On) |



## **🧮 Base de données (MySQL ou MariaDB)**

GLPI a besoin d’un **SGDBR** (Système de Gestion de Base de Données Relationnelle).

Il stocke toutes les données : tickets, utilisateurs, matériels, etc.

Deux options possibles : 
- **MySQL** : historiquement utilisé 
- **MariaDB** : fork de MySQL, plus courant aujourd’hui sur les distros Linux (par défaut sur Debian).



## **📡 Communication entre les composants**

Voici **comment tout fonctionne ensemble** (important à bien visualiser) :

1.  L’utilisateur entre une URL dans son navigateur.
2.  Le navigateur appelle **Apache**, le serveur web.
3.  Apache détecte que le fichier est en PHP → il passe le relais à **PHP**.
4.  PHP lit le code, **interprète les instructions** (ex : afficher un ticket).
5.  Si besoin, PHP **fait une requête SQL** à la base de données (MySQL/MariaDB).
6.  La base renvoie les résultats → PHP **prépare la page web**.
7.  Apache envoie la page finalisée **au navigateur de l'utilisateur**.

Chaque étape dépend des autres. Si un seul composant est mal configuré, GLPI ne fonctionnera pas.

