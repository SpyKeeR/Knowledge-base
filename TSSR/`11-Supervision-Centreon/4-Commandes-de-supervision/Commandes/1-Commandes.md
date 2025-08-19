# Commandes**⏱️ Rôle du moteur de supervision**

- Il **ordonnance les tâches de supervision**, c’est-à-dire qu’il exécute des commandes selon un intervalle régulier (🕔 par défaut : toutes les 5 minutes).
- Chaque commande exécutée est un **check** (ou *sonde de supervision*).
- Il peut s'agir de : charge CPU 🧠, mémoire RAM 💾, réseau 🌐, espace disque 📂, etc.



**🛠️ Définition des sondes (checks)**

Chaque **hôte** a des **services**, et chaque service utilise une **commande spécifique** pour récupérer une info.

💡 Une *sonde de supervision* = une **commande d’interrogation** précise, déclenchée par le moteur, qui retourne un **code de statut**.

Exemple : service "CPU" → commande "check_cpu" → résultat : OK/WARNING/CRITICAL/UNKNOWN



**📦 Types de commandes disponibles**

Deux grands "jeux" de commandes dispo :

- **Nagios Plugins** 🧮 (historiques, installés via paquets → /usr/lib64/nagios/plugins)
- **Centreon Plugins** 🧬 (via *plugin packs* → /usr/lib/centreon/plugins)
- **NRPE Plugin** (/usr/lib/nagios/plugins)

📝 Ces plugins sont souvent en **Perl**. Certains *plugin packs* sont inclus, d'autres à installer (gratuits ou payants).



**🧰 Installation des plugins**

- Dépend du type d’installation : ISO, paquets, ou compilation.
- Les **plugin packs** Centreon ajoutent automatiquement un lot de sondes prêtes à l’emploi.
- On peut aussi ajouter ses propres scripts personnalisés.



**⚙️ Fonctionnement d'une sonde**

1.  🧠 **Le moteur lit la config** et récupère la commande associée au service/hôte.
2.  🚀 **Il exécute la commande** (en local ou à distance).
3.  📥 **La commande interroge le système**, récupère une valeur (charge, disponibilité, etc.).
4.  🎯 **Elle retourne un code de statut** : 0 = OK, 1 = WARNING, 2 = CRITICAL, 3 = UNKNOWN, 4 = PENDING
5.  🔄 **Le moteur lit le code** et met à jour l’état du service/hôte.
