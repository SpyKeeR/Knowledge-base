# Présentation IPv4

Une **adresse IPv4** est utilisée pour identifier un **appareil** sur un réseau. Elle est constituée de deux parties :

1️⃣ **L'ID Réseau** → Identifie le **réseau logique** auquel appartient l’hôte

2️⃣ **L'ID Hôte** → Identifie un **appareil unique** au sein de ce réseau


## **🖧 Les éléments nécessaires à la communication**

Pour qu’un appareil puisse communiquer avec d’autres sur le même réseau, il doit disposer de :

✅ **Une adresse IP** (ex: 192.168.1.10)

✅ **Un masque de sous-réseau** (ex: 255.255.255.0)

Le **masque de sous-réseau** permet de **déterminer la partie réseau et la partie hôte** d’une adresse IP.


## **🔢 Calcul des adresses essentielles**

Avec l'adresse IP et le masque de sous-réseau, un hôte peut **déduire** :

🔹 **L’adresse du réseau logique** (ex: 192.168.1.0) → Identifie le réseau

🔹 **L’adresse de diffusion (broadcast)** (ex: 192.168.1.255) → Permet d’envoyer des messages à **tous** les hôtes du réseau

📌 Ces calculs sont essentiels pour la communication entre appareils sur un même sous-réseau.
