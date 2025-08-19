# Situer/Utiliser les sondes🔧 **Principe de fonctionnement des sondes de supervision**

Une **sonde** est un script ou binaire qui exécute un test sur un service ou un hôte, puis renvoie :

→ un **code de statut** (0 à 3 voire 4),

→ un **message lisible**,

→ des **données de performance** exploitables en graphique.

Elle est **exécutée régulièrement** par le moteur de supervision (Centreon Engine/Nagios).



📁 **Emplacements des sondes selon le type**

- **Sondes Nagios** ➤ /usr/lib64/nagios/plugins ou/et /usr/lib/nagios/plugins
- **Sondes Centreon** ➤ /usr/lib/centreon/plugins (souvent triées par domaine : Windows, Linux, SNMP…)
- **NRPE (Remote Execution)** ➤ Plugin client check_centreon_nrpe3 côté superviseur, interrogeant des sondes installées sur l’agent distant (NRPE)





📦 **Sondes Nagios : souples et répandues**

➡️ Disponibles dans les *plugin packs officiels* ou *repos tiers*

➡️ Options classiques : -h (aide), -c (seuil CRITICAL), -w (seuil WARNING)

➡️ Ex : check_ping -H 192.168.1.1 -w 100,20% -c 200,50%

➡️ Affiche un **résultat lisible** + code de retour + données perf (rta=56.5ms;100.0;200.0...)



🧪 **Sondes Centreon : intégrées & spécialisées**

➡️ Déployées avec les **plugin packs officiels** Centreon

➡️ Triées par domaine : centreon_plugins.pl --plugin=os::windows::snmp::plugin

➡️ Fonctionnent via un **plugin Perl unique (ou pas)** + options (ex : --hostname, --snmp-community, etc.)



📡 **Sondes NRPE : pour supervision distante active**

➡️ Permettent d’exécuter des sondes **localement sur l’hôte à superviser**, même sans SNMP

➡️ Le superviseur envoie une requête via **check_centreon_nrpe3**

➡️ Le plugin NRPE exécute la commande distante et retourne les résultats
