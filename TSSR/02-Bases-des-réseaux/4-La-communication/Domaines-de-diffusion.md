# Domaines de diffusionUn **domaine de diffusion** est une zone du réseau où une trame de **broadcast** est transmise à **tous les appareils** connectés. Il est crucial pour certaines communications, mais peut aussi devenir une source de congestion si mal géré.



**📡 1️⃣️Qu'est-ce qu’une diffusion (broadcast) ? ?**

🔹 Une trame de **diffusion** est envoyée à **tous les appareils** d’un réseau.

🔹 Elle utilise une **adresse de broadcast**, qui a tous ses bits hôtes à 1 : Exemple : **192.168.1.255** pour un réseau **192.168.1.0/24**.

🔹 Tous les appareils **reçoivent** le message, même s’ils ne sont pas concernés.

📌 **Exemples d’usages** :

✔ **DHCP** → Un PC cherche un serveur DHCP. ✔ **ARP** → Un PC demande l’adresse MAC d’une IP.



**🏗 2️⃣️ Quels équipements influencent un domaine de diffusion ?**

| **Switch**  | Transmet les diffusions à **tous les ports d’un VLAN**. |
|-------------|---------------------------------------------------------|
| **Routeur** | **Bloque** la diffusion entre sous-réseaux.             |
| **VLAN**    | **Crée un domaine de diffusion indépendant**.           |

**🚀 3️⃣️ Pourquoi segmenter un domaine de diffusion ?**

📌 **Problème :** Si un domaine de diffusion est **trop grand**, chaque appareil doit traiter **chaque trame de broadcast**, ce qui **ralentit** le réseau.

📌 **Solutions :**

✔ **Utiliser des routeurs** → Chaque sous-réseau a son propre domaine.

✔ **Créer des VLANs** → Chaque VLAN est **un domaine distinct**.



**📡 Le Domaine de Diffusion et le Modèle OSI**

📌 **Niveau Couche 2 (Liaison de données)**

- Utilise l’adresse MAC **FF:FF:FF:FF:FF:FF**.
- Diffusion par **switch** dans un même VLAN.

📌 **Niveau Couche 3 (Réseau)**

- Utilise des adresses IP comme **192.168.1.255**.
- Bloquée par un **routeur** entre sous-réseaux.
