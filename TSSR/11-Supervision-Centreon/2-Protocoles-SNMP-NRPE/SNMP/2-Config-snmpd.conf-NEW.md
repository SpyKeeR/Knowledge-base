# Config snmpd.conf - NEW

## **📡 agentAddress : interface et port d'écoute**

Le démon SNMP (snmpd) écoute par défaut sur le port **161/UDP**.

👉 Directive : `agentAddress udp:161`

Tu peux adapter : exemple `udp:127.0.0.1:161` si tu veux restreindre l'écoute à localhost.



## **🔐 Contrôle d'accès : deux approches**

2 modèles possibles (⚠️ non combinables) :

- **Traditionnel** (celui étudié ici)
- **VACM** (*non traité ici, plus granulaire mais plus complexe*)



## **🧭 Création d’une vue (View)**

Une *vue* = un regroupement d’OID (branches MIB) qu'on veut rendre accessibles.

👉 Exemple :

- `view VueSysteme included .1.3.6.1.2.1.1`
- `view VueSysteme included .1.3.6.1.2.1.25.1.1`
  💡 Le nom de la vue est libre, mais doit rester parlant.



## **👥 Attribution des droits aux communautés**

Deux directives pour gérer les accès à une vue :

- rocommunity = accès lecture seule
- rwcommunity = accès lecture/écriture

Exemple :

- `rocommunity public default -V VueSysteme`
- `rwcommunity private default -V VueSysteme`

🔐 default peut être remplacé par une IP pour limiter l’accès à un superviseur donné.



### **⚠️ Attention aux bonnes pratiques**

- Ne jamais mixer le modèle **traditionnel** avec **VACM**
- Toujours bien filtrer par IP ou plage (192.168.1.10, @192.168.1.0/24)
- Éviter les communautés par défaut (public, private) en prod
- Vérifie que snmpd écoute bien sur le port voulu avec `ss`

