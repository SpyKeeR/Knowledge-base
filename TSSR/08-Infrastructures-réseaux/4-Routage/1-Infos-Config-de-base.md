# Infos/Config de base

## **⚙️ Modes & mémoires du routeur**

Rien de nouveau côté fonctionnement interne :

- **Modes d’accès** : utilisateur (limité), privilégié (enable), configuration (conf t)
- **Mémoires** :
  - ROM (bootstrap, POST, mini-IOS),
  - Flash (stocke l’IOS),
  - RAM (running-config, table de routage),
  - NVRAM (startup-config, registre de config)



## **🔌 Configuration d’une interface**

Tu veux qu’un routeur parle sur le réseau ? Il faut activer ses interfaces

(souvent désactivées par défaut sur routeur) :

- Aller dans l’interface : interface GigabitEthernet0/0
- Donner une IP + masque : ip address 192.168.X.X 255.255.255.0
- Activer l’interface : no shutdown



## **🏷️ Ajout de descriptions sur les interfaces**

C’est une **bonne pratique** souvent négligée, mais super utile pour :

- Diagnostiquer rapidement un problème
- Identifier à quoi/qui l’interface est reliée
- Documenter l’install (ex : FAI, client, switch, salle, baie, etc.)

Commande : description Lien vers le switch de la salle serveur

📌 Conseil : prends l’habitude **dès maintenant** de documenter chaque interface, même sur tes labs !



## **🤝 Similitudes avec les switches**

Les routeurs IOS partagent : Les **mêmes modes de configuration,** Les **mêmes mémoires internes et** Une **logique de configuration réseau identique**

Un **routeur** est un équipement **essentiel pour faire circuler les données** entre plusieurs réseaux.

Il **interconnecte des réseaux différents** (ex : deux VLAN, deux LAN, ou un LAN et Internet).

🧭 Ses missions principales :

- Acheminer les **paquets IP** jusqu’à leur **destination finale**
- **Bloquer le broadcast** (trafic local inutile pour les autres réseaux)
- **Filtrer et orienter** le trafic réseau selon des règles logiques



## **⚙️ Fonctionnement du routeur (étapes clés)**

Quand un routeur reçoit une trame qui lui est adressée :

1.  📩 **Désencapsulation** : il retire la trame Ethernet pour accéder au **paquet IP**
2.  🔢 **Décrémentation du TTL** (*Time To Live*) : il réduit de 1 pour éviter les boucles infinies
3.  📚 **Consultation de la table de routage** : il cherche la **meilleure route** vers la destination
4.  📦 **Réencapsulation** : il emballe le paquet IP dans une **nouvelle trame Ethernet**
5.  🚀 **Transmission** vers le **prochain saut** (next hop)

💡 Il agit comme un **facteur réseau** : il ouvre l'enveloppe, lit l'adresse, vérifie que le courrier peut continuer, le remet dans une nouvelle enveloppe, et l’envoie plus loin !



### **🧬 Couches OSI concernées**

Le routeur travaille sur **trois couches du modèle OSI** :

- **Couche 1 (physique)** : transmission du signal brut
- **Couche 2 (liaison)** : lecture/création des **trames Ethernet**
- **Couche 3 (réseau)** : traitement des **paquets IP** et routage

