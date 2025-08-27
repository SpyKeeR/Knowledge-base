# Routage

**🚪 Principe du routage**

Quand plusieurs **domaines de diffusion** existent (ex : 3 réseaux), il faut un **routeur** pour permettre aux machines de ces réseaux de **communiquer entre elles**.

🧭 Chaque machine envoie vers sa **passerelle par défaut**, qui est l’**interface locale du routeur** (ex : 192.168.31.1), et c’est lui qui redirige le paquet.

## **🧠 Logique IP de routage**

Quand un hôte envoie un paquet, le protocole **IP** fait un test logique :

- Adresse IP de destination + **masque** = calcul du **réseau cible**
- Si réseau local ➜ envoi direct
- Si **réseau distant** ➜ passage par **passerelle**



## **🗺️ Table de routage**

Si destination = **hors réseau local**, le routeur consulte sa **table de routage** pour trouver le chemin :

- S’il y a une **route explicite**, il envoie dans la bonne direction
- Sinon, il utilise la **passerelle par défaut**
- Et rebelote à chaque routeur traversé

💥 Si aucune route n’est trouvée à un moment ➜ **erreur ICMP** vers l’émetteur



## **⏳ Transmission & TTL**

Le paquet avance de **saut en saut** (hop par hop) à chaque routeur ➜ c’est la **transmission par paquets**

Le champ **TTL (Time To Live)** est décrémenté à chaque saut ➜ protège contre les boucles

🛑 Si TTL = 0 ➜ paquet abandonné, message d’erreur envoyé



## **🔧 Types de routage**

Deux grandes familles :

- **Statique** : routes configurées **manuellement** par l’admin, pas d’échange d'infos ➜ stable mais peu flexible
- **Dynamique** : les routeurs **échangent leurs infos** automatiquement via des **protocoles de routage** ➜ adaptatif mais plus complexe

📡 Protocoles de routage connus :

- RIP (ancien, simple)
- OSPF (efficace, utilisé en entreprise)
- IGRP/EIGRP (Cisco)



