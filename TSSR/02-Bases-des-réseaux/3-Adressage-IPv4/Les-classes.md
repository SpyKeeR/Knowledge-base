# Les classesUne **adresse IPv4** est composée de **32 bits** (4 octets) et s’écrit sous forme de **quatre nombres décimaux** séparés par des points, par exemple :

🖥 **Adresse IP :** 192.168.1.1

📟 **Équivalent binaire :** 11000000.10101000.00000001.00000001

Chaque octet (8 bits) peut contenir **256 valeurs possibles (0 à 255)**.



**📊 Les Classes IPv4 et le Nombre d’Hôtes**

L’adressage IPv4 est divisé en **classes**, qui déterminent la répartition des **bits réseau et hôte** :

| **Classe** | **Plage d'adresses** | **Bits Réseau / Hôte** | **Masque par défaut** | **Nombre d’hôtes possibles (*2^bits_hôte - 2*)** |
|----|----|----|----|----|
| **A** | 0.0.0.0 → 127.255.255.255 | 8 bits Réseau / 24 bits Hôte | 255.0.0.0 (/8) | **16 777 214** (2²⁴ - 2) |
| **B** | 128.0.0.0 → 191.255.255.255 | 16 bits Réseau / 16 bits Hôte | 255.255.0.0 (/16) | **65 534** (2¹⁶ - 2) |
| **C** | 192.0.0.0 → 223.255.255.255 | 24 bits Réseau / 8 bits Hôte | 255.255.255.0 (/24) | **254** (2⁸ - 2) |
| **D** | 224.0.0.0 → 239.255.255.255 | Multidiffusion (pas d’hôtes) | *Non applicable* | *Réservé multicast* |
| **E** | 240.0.0.0 → 255.255.255.255 | Expérimental (pas d’hôtes) | *Non applicable* | *Non utilisé* |

💡 **Pourquoi soustraire 2 au calcul des hôtes ?**

- Une adresse IPv4 ne peut pas être **entièrement à 0** → Adresse **réseau**
- Une adresse IPv4 ne peut pas être **entièrement à 1** → Adresse **broadcast**



**🎯 Les Adresses Spéciales**

💻 **Adresse de boucle locale (localhost)** : 127.0.0.1

→ Utilisée pour les tests et la communication interne à un appareil.



🌍 **Adresse réseau** : Ex. 192.168.1.0

→ Identifie un **réseau entier**, utilisée dans le routage.



📢 **Adresse de diffusion (broadcast)** : Ex. 192.168.1.255

→ Permet d’envoyer des **données à tous les hôtes** d’un même réseau.

**🏷 Comment identifier rapidement une classe ?**

📌 **Regardez le premier octet (1er nombre de l’adresse IPv4)** :

- **0 → 127** ➝ **Classe A**
- **128 → 191** ➝ **Classe B**
- **192 → 223** ➝ **Classe C**
- **224 → 239** ➝ **Classe D (Multicast)**
- **240 → 255** ➝ **Classe E (Expérimental)**

Exemple 🔎 :

- 10.1.2.3 → 1er octet = **10** → **Classe A**
- 172.16.5.8 → 1er octet = **172** → **Classe B**
- 192.168.1.1 → 1er octet = **192** → **Classe C**
