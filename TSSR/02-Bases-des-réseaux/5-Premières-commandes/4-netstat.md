# netstat

📌 **netstat** (abréviation de *Network Statistics*) est un outil puissant permettant **d'afficher des informations sur les connexions réseau actives, les ports ouverts et les statistiques réseau**. Il est essentiel pour **diagnostiquer les problèmes réseau, repérer les connexions suspectes et analyser l'activité réseau d’un système**.  
  
## **🎯 Rôle de la commande netstat**

Avec netstat, on peut :

✅ **Lister les connexions réseau actives** (**TCP/UDP**)

✅ **Voir les ports ouverts** et en attente de connexion

✅ **Identifier les adresses IP distantes** avec lesquelles la machine communique

✅ **Afficher la table de routage** du système

✅ **Obtenir des statistiques sur l’interface réseau** (nombre de paquets envoyés/reçus, erreurs)  
  
## **🔍 Interprétation des résultats**

Quand vous exécutez netstat, vous obtenez une liste avec plusieurs colonnes :

Protocole / Adresse locale / Adresse distante / Etat  
  
## 📌 **Les différents états TCP :**

| **⚠️ État** | **🔍 Signification** |
|----|----|
| 📡 LISTEN | Le port **attend des connexions entrantes** |
| ✅ ESTABLISHED | Connexion **active et en cours d’utilisation** |
| ⏳ TIME_WAIT | Connexion **fermée, mais en attente avant suppression** |
| 🛑 CLOSE_WAIT | Connexion fermée **par l’autre machine**, en attente de fermeture locale |
| 🔄 SYN_SENT | **Demande de connexion envoyée**, en attente de réponse |



## **🔎 Pourquoi utiliser netstat ?**

🚀 **Diagnostic réseau** : Trouver les connexions anormales

📊 **Analyse de performance** : Identifier les ports surchargés

🛡️ **Sécurité** : Détecter les services suspects ou non sécurisés

🖥️ **Dépannage d’applications** : Voir quels ports sont utilisés par les logiciels

⚠️ **Attention !** netstat est aujourd’hui **remplacé par des outils plus modernes** :

- **Linux** : ss / **Windows** : Get-NetTCPConnection (PowerShell)

## **🛠️ Options courantes de netstat**

| **🏷️ Option** | **📖 Description** |
|----|----|
| 🔹 netstat -a | Affiche **toutes** les connexions actives (TCP, UDP) et les ports d’écoute |
| 🔹 netstat -n | Affiche **les adresses IP et ports sous forme numérique** (évite la résolution de noms) |
| 🔹 netstat -t | Affiche uniquement les connexions **TCP** |
| 🔹 netstat -u | Affiche uniquement les connexions **UDP** |
| 🔹 netstat -l | Affiche **uniquement les ports en écoute** |
| 🔹 netstat -p | Associe les connexions aux **processus** (nécessite les droits root sous Linux) |
| 🔹 netstat -r | Affiche la **table de routage** du système |
| 🔹 netstat -e | Affiche des **statistiques détaillées** des interfaces réseau (paquets, erreurs, etc.) |

📌 **Exemple : Voir toutes les connexions en cours avec les processus associés > sudo netstat -tulpn**

