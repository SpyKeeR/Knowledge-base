## **📌 1. Rôle et Fonctionnement**

🔹 **Troisième couche** du modèle OSI, elle gère la **transmission des paquets** entre des réseaux différents.

🔹 Fonctionne avec des **adresses logiques (IP)** 📡, contrairement à la couche 2 qui utilise les **adresses MAC** 🏷️.

🔹 Assure le **routage** 🚦 en déterminant le chemin optimal pour les paquets.

🔹 Gère la **fragmentation et le réassemblage** des paquets trop volumineux.



## **🔗 2. Structure d’une Adresse IP**

  🖥️ **ID Réseau + ID Hôte** → Identifie un appareil sur un réseau logique.
  
  📌 **Masque de sous-réseau** → Détermine la partie réseau et la partie hôte.

📶 Tous les postes d’un même réseau peuvent **communiquer entre eux**.

**🔍 Exemple : Ping d’un poste A vers un poste B**

  📍 **Adresse MAC** → Identifie physiquement l’appareil (Couche 2).
  
  🌍 **Adresse IP** → Identifie logiquement l’appareil (Couche 3).


## **🚦 3. Fonctions Principales de la Couche Réseau**

🔄 **Routage** → Trouver le chemin optimal pour envoyer un paquet d’un réseau à un autre.

🏷 **Adressage** → Attribution d’une adresse IP unique aux appareils.

📦 **Fragmentation/Réassemblage** → Découpe les paquets trop gros et les reconstruit à la destination.

⚠ **Contrôle de la congestion** → Ajuste le flux de paquets pour éviter la surcharge du réseau.

🚨 **Gestion des erreurs** → Détecte et signale certaines erreurs lors du routage.


## **📡 4. Protocoles Associés à la Couche Réseau**

🌍 **Internet Protocol (IP)** → Gère l’adressage et le routage.

  - **IPv4 (32 bits)** → Format classique (ex: 192.168.1.1).
  - **IPv6 (128 bits)** → Nouveau format pour un plus grand nombre d’adresses.

🔍 **Internet Control Message Protocol (ICMP)** → Messages de diagnostic (ex: ping).

📍 **Protocoles de Routage**

  - **RIP (Routing Information Protocol)** → Routage à distance basée sur le nombre de sauts.
  - **OSPF (Open Shortest Path First)** → Choix du meilleur chemin en fonction de l’état du réseau.


## **🚛 5. Équipements Utilisant la Couche Réseau**

🌐 **Routeur** → Achemine les paquets entre réseaux différents.

🛜 **Passerelle (Gateway)** → Relie des réseaux avec des protocoles distincts.


## **📦 6. Exemple de Transmission d’un Paquet**

🖥 **PC A (192.168.1.10) veut envoyer des données à PC B (192.168.2.20)**

✅ **Même réseau ?** → NON, l’adresse IP cible est sur un autre réseau.

🔄 **Passage par un routeur** → Le paquet est routé vers la passerelle par défaut.

🏁 **Paquet acheminé vers PC B** via la meilleure route trouvée.

