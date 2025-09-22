# Présentation

Apache HTTP Server, aussi appelé **httpd**, est issu de plusieurs webmasters ayant corrigé et amélioré un serveur existant.

- Ces contributions ont été centralisées sous l’entité **Apache Foundation**.
- Le nom **"Apache"** viendrait du jeu de mots **"A Patchy Server"**.
- La **première version stable**, 0.62, date d’**avril 1995**.
- Aujourd’hui, Apache est le **serveur web le plus utilisé au monde**, tant par les entreprises que les particuliers.

## **💿 Installation d’Apache sous Linux**

- Apache est présent dans **tous les dépôts officiels** des distributions Linux.
- Installation simplifiée avec : `apt install apache2` (Debian/Ubuntu)
- Activation et démarrage du service :
  - `systemctl enable apache2` (démarrage au boot)
  - `systemctl start apache2` (démarrage immédiat)
- Vérification du statut : `systemctl status apache2`
- Ce paquet installe :
  - Le serveur Apache
  - Sa documentation
  - Les modules complémentaires nécessaires à son bon fonctionnement



## **🔍 Vérification de l’installation**

- Accès via un **navigateur web** :
  - <http://@IP> (depuis une autre machine)
  - <http://localhost> (en local)
- Depuis un OS serveur :
  - Utilisation des mêmes URLs
  - Accès au répertoire `/etc/apache2` pour vérifier la configuration
- Objectif : s’assurer que la **page d’accueil par défaut** est bien accessible



## **🗂️ Structure des fichiers de configuration**

📁 `/etc/apache2` (Debian/Ubuntu) 

- **apache2.conf** : fichier **principal de configuration**
  - Contient des directives globales
  - Utilise `Include` pour charger les autres fichiers
  - Directives utiles : `ServerSignature off` / `ServerTokens Prod`
- **Répertoires complémentaires** :
  - `conf-available/` : fichiers de configuration disponibles
  - `conf-enabled/` : **liens symboliques** vers ceux activés
  - `envvars` : définit les **variables d’environnement apache2ctl**
  - `ports.conf` : configure les **ports d’écoute** (par défaut : 80 pour HTTP, 443 pour HTTPS)

## **🧩 Gestion des modules Apache**

📁 `/etc/apache2/mods-*`

- `mods-available/` : contient les fichiers de configuration des **modules**
- `mods-enabled/` : **liens symboliques** vers les modules activés
- Permet d’activer/désactiver dynamiquement des fonctionnalités (SSL, PHP, etc.)



## **🌐 Hébergement de sites web**

📁 `/etc/apache2/sites-*`

- `sites-available/` : fichiers de configuration pour **chaque site web** (virtual host)
- `sites-enabled/` : **liens symboliques** vers les sites activés
- Chaque fichier peut définir :
  - Le **port d’écoute**
  - Le **chemin vers les fichiers web**
  - Les **règles d’accès**
  - Les paramètres SSL



## **🎧 Directive Listen**

- Présente dans `ports.conf`
- Utilisée pour définir **sur quelles interfaces et quels ports** Apache doit écouter
- Syntaxe : `Listen [adresse IP:]port`
- Exemples :
  - `Listen 80` → toutes interfaces, port HTTP
  - `Listen 443` → toutes interfaces, port HTTPS
  - `Listen 192.168.1.10:8080` → IP spécifique sur port 8080



## **📄 Directive DirectoryIndex**

- Fichier : `/etc/apache2/mods-enabled/dir.conf`
- Définit **quel fichier doit être affiché par défaut** lorsqu’un user accède à un dossier
- Exemple de configuration : 
```bash 
  <IfModule mod_dir.c>  
  DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm  
  </IfModule>
```
- L’ordre indique la **priorité de chargement** (ex : `index.html` avant `index.php`)
