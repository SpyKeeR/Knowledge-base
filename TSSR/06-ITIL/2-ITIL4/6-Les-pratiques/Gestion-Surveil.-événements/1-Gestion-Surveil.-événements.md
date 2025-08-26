# Gestion/Surveil. événementsUn **événement** est tout **changement d’état significatif** détecté dans : Un **service** (ex : messagerie, appli métier), Un **composant technique** (ex : disque, switch), Un **processus métier,** Ou encore la **sécurité de l'information** 👉 Il est détecté automatiquement via des **outils de supervision** comme **Centreon**, **Nagios**, **Zabbix**, etc.



**🧪 Pourquoi c’est important ?**

- Anticiper les **incidents**, Suivre la **santé des systèmes en temps réel,** Réagir **avant** que l'utilisateur soit impacté, S'assurer que le **SLA** est respecté



**⚙️ Traitement des événements**

| 🔧 **Mineur / informatif** | Juste logué (pas d'action humaine) |
|----|----|
| ⚠️ **Précurseur d'incident** | Alerte + possible ticket → intervention **préventive** |
| ❌ **Déjà un incident** | Création automatique d’un **incident utilisateur** → prise en charge support |

🧠 **Tous les événements ne mènent pas à une intervention.**



**💡 Exemple pédagogique (RAID 5)**

🔄 RAID 5 peut tolérer la perte **d’un disque** sans perte de données. / ❌ Si **un deuxième disque** tombe avant remplacement, **l’incident** est critique.

**➕ Ce qui se passe :** 👀 Supervision détecte le disque KO = événement / 🛠️ Un ticket est créé → technicien intervient = évite l’incident / ✅ Si le disque est remplacé à temps → pas d’incident utilisateur



**📊 Autres exemples d’événements courants :**

| 🧮 Espace disque faible | Serveur HS si on n'agit pas |
|----|----|
| 💡 Serveur éteint dans un cluster | Perte de redondance |
| 🔌 Câble débranché dans une agrégation de ports | Perte de bande passante / redondance |
| 📉 Pertes de ping sur un hôte ESXi | Risque d’indisponibilité / crash VM |



**🔄 Retour à la normale = aussi un événement**

Exemple : Un hôte repasse à « OK » dans Centreon → on le log comme un événement positif.



**🧩 Événements, incidents, problèmes : bien distinguer**

| **Événement** | Back-office, outil ITSM | Disque à risque, ping perdu, alerte mémoire |
|----|----|----|
| **Incident** | Front-office / support | Plus de messagerie, utilisateur bloqué |
| **Problème** | Back-office / experts | Répétition d’un incident, cause inconnue |

➡️ Un **problème** peut avoir une **solution de contournement** dans la **KEDB** (base des erreurs connues).
