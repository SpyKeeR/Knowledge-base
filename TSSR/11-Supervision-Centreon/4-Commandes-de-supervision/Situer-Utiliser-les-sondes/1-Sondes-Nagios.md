## **📁 Localisation des plugins**

- 📦 Les **plugins Nagios** sont dans :/usr/lib64/nagios/plugins (Centreon récent)  
  /usr/lib/nagios/plugins (CentOS/Nagios plus anciens)

- Contiennent des **binaires/scripts** exécutables avec des paramètres pour effectuer des **tests ciblés**
- Utilisent différents protocoles selon le plugin : SNMP, TCP/IP, ICMP, etc.



## **🛠️ Paramètres communs**

Tous les plugins supportent **-h** (aide).

La plupart acceptent aussi :

- -w : seuil **Warning**
- -c : seuil **Critical**
  - options spécifiques (ex : -H, -p, -t, -4/-6 pour check_ping)

👉 Syntaxe uniforme = compréhension rapide / scripting facilité



## **🧪 Fonctionnement général**

Lorsqu’un plugin est exécuté, il retourne 3 types de données :

1.  **Texte lisible** : ex. PING OK - Paquets perdus = 0%, RTA = 0.34 ms
2.  **Perfdata** : ex. rta=0.344000ms;1.000000;2.000000;0.000000 pl=0%;50;80;0
3.  **Code retour** : interprété par Nagios pour l'état du service :

    - **0 = OK, 1 = WARNING, 2 = CRITICAL, 3 = UNKNOWN**



## **📌 Exemple : check_ping**

Commande type :

`/usr/lib64/nagios/plugins/check_ping -H 172.16.1.100 -w 1,50% -c 2,80% -p 3`

🔍 Détails :

- -H : IP ou FQDN de l’hôte à tester
- -w : seuil warning → ici 1ms RTT ou 50% perte
- -c : seuil critique → ici 2ms RTT ou 80% perte
- -p : nombre de paquets envoyés (défaut 5)

Sortie standard analysée en 2 parties :

🟢 Avant le | = **résumé lisible pour l’interface web**

📈 Après le | = **données pour les graphes de perf**

🔙 echo $? = **code retour à exploiter côté moteur**

