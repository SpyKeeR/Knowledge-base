## **🔁 Préparation côté hôte supervisé (Windows)**

- **NSClient++** : installé et accessible via l'interface web <https://IP:8443>
- Menu **Modules** → Vérifie que **NRPE server** est activé
- Menu **Queries** → Choisis une commande (check_cpu) → Barre espace = suggestions
- Pour définir des seuils personnalisés : ajoute warn=load > 50 et crit=load > 70
- Clique sur **Run** si tu ne connais pas la syntaxe → ça te renvoie une erreur utile avec l’aide intégrée 🧠

⚠️ Par défaut, **NSClient++ bloque les options personnalisées** → Active ces 2 réglages :

**Settings → NRPE → Server → onglet Basic**

- ✅ Allow arguments = true
- ✅ Allow nasty characters = true  
  (pour autoriser les paramètres et caractères spéciaux comme >)  
  Sauvegarde et recharge NSClient++.



## **🖥️ Lancement d’une sonde NRPE depuis Centreon**

- Le binaire est : /usr/lib/nagios/plugins/check_centreon_nrpe3
- Version 3 = compatible NRPE v3 (chiffrement TLS obligatoire, + sécurisé)

Commande minimale :

`check_centreon_nrpe3 -H <IP hôte> -c <commande> -a <args>`

💡 Exemple réel :

check_centreon_nrpe3 -H 10.0.0.100 -c check_cpu -a "warn=load > 50" "crit=load > 70"

➡️ Ce que ça fait :

- -H : IP de l’hôte supervisé
- -c : nom de la commande référencée dans NSClient++
- -a : les arguments à transmettre (seuils, options...)



**📌 À retenir**

- Les sondes NRPE permettent d’exécuter à distance **les commandes déclarées dans NSClient++**
- Très utile pour superviser précisément un Windows sans passer par SNMP
- ⚠️ Les seuils doivent être envoyés dans le bon format ET les paramètres activés côté NSClient++

