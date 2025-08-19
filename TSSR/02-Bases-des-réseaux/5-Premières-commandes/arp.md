# arp**🔄 Fonctionnement d'ARP**

1️⃣ **Recherche dans la table ARP** (sinon → requête ARP broadcast).

2️⃣ **L’appareil cible répond avec son MAC**.

3️⃣ **Ajout dans le cache ARP** (évite une nouvelle requête).

| **Commande**            | **Description**                  | **OS**          |
|-------------------------|----------------------------------|-----------------|
| arp -a                  | Afficher la table ARP (IP ↔ MAC) | Windows / Linux |
| arp -s <IP> <MAC>   | Ajouter une entrée statique      | Windows         |
| arp -d *               | Vider toute la table ARP         | Windows         |
| sudo ip neigh flush all | Vider la table ARP               | Linux           |



**⚠ Problèmes Courants**

❌ **Cache obsolète** → Entrées invalides.

🔓 **ARP Spoofing** → Usurpation d’IP/MAC.

🛠 **Entrées incorrectes** → Problèmes de connexion.

**🌍 Limites**

🚫 **Uniquement en LAN & Ethernet** (remplacé par **NDP en IPv6**).
