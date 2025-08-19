# Sondes SNMP**📂 Emplacement des sondes SNMP**

- Localisées dans /usr/lib/Centreon/plugins
- Scripts Perl : *.pl, organisés par domaine
- Exemple : pour tester le CPU d’une machine Linux → centreon_linux_snmp.pl



**🧭 Navigation dans les plugins**

- --help : affiche l’aide générale du script
- --list-plugin : liste les plugins disponibles dans le script (ex : os::linux::snmp::plugin)
- --plugin=... : sélection du plugin
- --list-mode : liste les modes de supervision (ex : cpu, interfaces, list-storages, etc.)
- --mode=... --help : affiche les options disponibles pour un mode spécifique



**🔧 Structure de l’aide par mode**

1.  **Output Options** : personnalisation de l’affichage (rarement utilisé)
2.  **Snmp Options** : version, communauté, options SNMPv3
3.  **Mode** : options spécifiques au type de test (seuils --warning, --critical, etc.)



**🧪 Exemple d’utilisation : CPU via SNMP**

Commande complète :

centreon_linux_snmp.pl --plugin=os::linux::snmp::plugin --mode=cpu --hostname=172.16.1.3 --snmp-community=public --snmp-version=2 --warning-average=80 --critical-average=90

Retour console :

- ✅ Résultat lisible : OK: 1 CPU(s) average usage is 1.00 %
- 📊 Perfdata : 'total_cpu_avg'=1.00%;0:80;0:90;0;100 'CPU'=1.00%;;;0;100



**🧙‍♂️ Astuce : tester comme le moteur**

Les sondes seront exécutées **par le moteur Centreon** (centreon-engine).

Donc, pour tester comme en prod :

👉 Deviens cet utilisateur avec : su - centreon-engine
