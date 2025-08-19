# Sous-réseauxLe **sous-réseautage** permet de diviser un réseau IP en plusieurs **sous-réseaux plus petits** afin d'optimiser l'utilisation des adresses IP, d'améliorer la **gestion du trafic** et de renforcer la **sécurité** du réseau.



**🎯 Pourquoi faire du sous-réseautage ?**

✔ **Optimiser l'espace IP** → Éviter de gaspiller des adresses inutiles.

✔ **Améliorer les performances** → Réduction du **domaine de broadcast** (moins de trafic inutile).

✔ **Sécuriser le réseau** → Contrôle des flux entre sous-réseaux (ex: VLANs).



**🏗 Structure d’une Adresse IPv4 et du Masque de Sous-Réseau**

🔹 Une **adresse IPv4** est composée de **32 bits**, divisés en **deux parties** :

- **Partie Réseau** : Identifie le réseau.
- **Partie Hôte** : Identifie chaque appareil (hôte) dans le réseau.

🔹 Le **masque de sous-réseau** sert à indiquer **quelle partie est dédiée au réseau** et **quelle partie est réservée aux hôtes**.



**🔢 Exemple : Sous-réseautage d’un Réseau 192.168.1.0/24**

📌 **Adresse initiale :** 192.168.1.0/24

- Masque : 255.255.255.0
- **Hôtes disponibles** : 2⁸ - 2 = 254 (on enlève **l’adresse réseau** et **l’adresse de broadcast**).

💡 **Si on veut diviser ce réseau en 2 sous-réseaux**, on **emprunte 1 bit** de la partie hôte → **Masque devient /25** (255.255.255.128).

| **Sous-réseau** | **Plage d'adresses** | **Adresse Réseau** | **Broadcast** | **Nombre d’hôtes** |
|----|----|----|----|----|
| **Sous-réseau 1** | 192.168.1.0 → 192.168.1.127 | 192.168.1.0 | 192.168.1.127 | 126 |
| **Sous-réseau 2** | 192.168.1.128 → 192.168.1.255 | 192.168.1.128 | 192.168.1.255 | 126 |



**🧮 Formules Importantes**

📌 **Nombre de sous-réseaux** : 2ⁿ avec n = bits empruntés à la partie hôte.

📌 **Nombre d’hôtes par sous-réseau** : 2ʰ - 2 avec h = bits restants pour les hôtes.
