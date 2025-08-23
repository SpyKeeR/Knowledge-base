# Configuration NIC

## **🌍 Attribution d'une adresse IP**

### 📌 **Accès aux paramètres réseau** :

- **Panneau de configuration** → **Centre Réseau et Partage**
- **Commande** ncpa.cpl
- **Paramètres Windows**


### 📌 **Configuration IP** :

- **Manuelle (statique)** : saisie directe de l'IP, masque, passerelle et DNS.
- **Automatique (DHCP)** : attribution dynamique via un serveur DHCP.
- **APIPA (169.254.0.0/16)** : IP temporaire si aucun DHCP disponible, permet une communication locale.



## **🔍 Diagnostic et dépannage**

### 🖥️ **Outils graphiques** :

- **Icône réseau** (zone de notification)
- **Centre Réseau et Partage**
- **Assistant "Résoudre les problèmes"** (orienté utilisateur)


### 💻 **Outils en ligne de commande** (plus efficaces) :

- ipconfig /all ➜ Infos IP et cartes réseau
- ping [adresse] ➜ Tester la connectivité
- tracert [adresse] ➜ Suivi du chemin des paquets
- netstat ➜ Vérifier les connexions réseau
- nslookup [nom_de_domaine] ➜ Vérifier la résolution DNS

