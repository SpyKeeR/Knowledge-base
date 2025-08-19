# 5 - Configuration de Centreon🖥️ **Découverte de l’interface de Centreon**

➡️ L’interface est **web-based**, très structurée :

→ Menu principal : *Configuration*, *Monitoring*, *Administration*, etc.

→ Partie *Configuration* = cœur du paramétrage (commandes, hôtes, services, templates…)

➡️ Chaque élément (commande, template…) est **créé puis lié** aux autres pour former une config cohérente.

➡️ Pas de modification en live : il faut **générer et redémarrer la configuration** pour appliquer.



⚙️ **Création des commandes personnalisées**

➡️ Une **commande** dans Centreon représente l’appel à une **sonde (plugin)** avec ses paramètres.

Exemples : check_ping -H $HOSTADDRESS$ -w 100,20% -c 200,50%

➡️ Une commande a plusieurs **champs importants** : → *Nom*, *ligne de commande*, *arguments*, *macro de remplacement*

➡️ Objectif : rendre la commande **réutilisable** via des macros ($ARG1$, $ARG2$...)

➡️ Centreon va **utiliser cette commande** au moment de superviser un service



🧩 **Templates (Modèles) : standardisation au max**

➡️ Un **template de service ou d’hôte** = un modèle réutilisable

➡️ Il contient des réglages types :

→ Commande à utiliser, fréquence de check, seuils, notifications, etc.

➡️ Les hôtes/services héritent des paramètres du template → **gain de temps énorme**

➡️ Plusieurs templates peuvent être combinés (héritage multiple)

➡️ Ex : Template Linux-Base + SNMP-Linux sur un serveur Debian → prêt à l'emploi



🧱 **Application de la configuration dans Centreon**

➡️ Une fois commandes et templates créés, tu dois :

→ Lier les templates aux hôtes/services

→ Vérifier la cohérence de la configuration

→ **Générer la conf** via le menu Administration > Centreon Engine > Export Configuration

→ **Redémarrer le moteur** pour prise en compte (Reload, Restart…)

jeudi 12 juin 2025

18:57
