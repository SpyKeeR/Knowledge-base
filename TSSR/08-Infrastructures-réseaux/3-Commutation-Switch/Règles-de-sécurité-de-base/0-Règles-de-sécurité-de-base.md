# Règles de sécurité de base

## **🔐 Sécurisation des accès au commutateur**

### 🔸 **Mots de passe** :

Ajoute un mot de passe sur *chaque mode* (console, VTY, exec privilégié). C’est ton premier verrou 🔒.

### 🔸 **SSH plutôt que Telnet** :

Telnet = données en clair ⚠️ / SSH = chiffrement 🔐.

→ Active SSH + désactive Telnet sur les lignes VTY.



## **🛡️ Sécurité côté VLANs et ports**

### 🔸 **Quitter le VLAN 1 par défaut** :

Le VLAN 1 est la cible classique des attaques internes (ex : VLAN hopping).

→ Affecte tes ports utilisateurs à un autre VLAN par défaut (ex : VLAN 10).

### 🔸 **Désactiver les ports inutilisés** :

Un port libre = une porte d’entrée 📬.

→ Met les interfaces inutilisées en shutdown et place-les dans un VLAN "parking".



#### **📄 Recommandations officielles**

Une ressource mentionnée : le guide de l'**ANSSI** pour sécuriser les switchs de desserte.

➡️ À consulter plus tard pour affiner avec : port-security, BPDU Guard, DHCP Snooping, etc.

