# Communication inter-couches**📦 Encapsulation & Transmission**

✔️ **Le PDU d'une couche devient le SDU de la couche suivante**

✔️ **Ajout du PCI (Protocol Control Information)** → Création d'un nouveau PDU

✔️ Transmission du **nouveau PDU** à la couche suivante



**Exemple :**

1️⃣ La couche transport crée un **segment TCP (PDU)**

2️⃣ Ce segment devient un **SDU pour la couche réseau**

3️⃣ La couche réseau ajoute son **PCI** (adresses IP)

4️⃣ Formation d'un **paquet IP (nouveau PDU)**

5️⃣ Transmission à la couche inférieure



**🔁 Désencapsulation & Réception**

✔️ **Chaque couche interprète le PDU reçu**

✔️ **Retrait du PCI** pour retrouver le SDU original

✔️ Transmission du **SDU** à la couche supérieure



**Exemple :**

1️⃣ La couche réseau reçoit un **paquet IP (PDU)**

2️⃣ Elle **retire son PCI réseau** et récupère le **SDU** (segment TCP)

3️⃣ Le SDU est transmis à la **couche transport**, qui fait de même
