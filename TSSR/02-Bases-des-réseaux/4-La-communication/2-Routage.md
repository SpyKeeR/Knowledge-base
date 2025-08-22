# Routage

Un **routeur** analyse les paquets et les envoie vers la bonne destination en consultant sa **table de routage**.

## 🎯 **Fonctions du routeur :**

🔹 Lit l'**adresse IP de destination** dans le paquet.

🔹 Consulte sa **table de routage** pour voir si une route est disponible.

🔹 Envoie le paquet vers le bon réseau ou la bonne passerelle.

🔹 Si aucune route n'est trouvée → Il utilise **la passerelle par défaut** (ou drop le paquet si aucune passerelle n'est définie).



## **🗺️ La Table de Routage : Le GPS du Routeur**

Chaque routeur possède une **table de routage**, qui liste **les réseaux qu'il peut atteindre** et **par quel chemin**.

Exemple d’une table de routage :

| **Réseau Destination** | **Masque** | **Passerelle** | **Interface Sortante** |
|----|----|----|----|
| 192.168.1.0 | 255.255.255.0 | 192.168.0.1 | Ethernet 0 |
| 10.0.0.0 | 255.0.0.0 | 192.168.0.2 | Ethernet 1 |
| 0.0.0.0 | 0.0.0.0 (default) | 192.168.0.254 | Ethernet 2 |

💡 **Interprétation :**

- Si un paquet est destiné à 192.168.1.x, il sort par Ethernet 0.
- Si un paquet est destiné à 10.x.x.x, il sort par Ethernet 1 via passerelle 192.168.0.2.
- Si le routeur **ne connaît pas le réseau**, il envoie le paquet à la passerelle **par défaut**.



## **🔀 Types de Routage**

### **📝 Routage Statique**

🔹 Routes **configurées manuellement**.

🔹 Idéal pour **les petits réseaux** ou les connexions fixes.

🔹 **Avantage :** Simple et stable.

🔹 **Inconvénient :** Si un lien tombe, l’administrateur doit **modifier manuellement** la route.

### **🔄 Routage Dynamique**

🔹 Routes mises à jour **automatiquement** par des protocoles de routage (RIP, OSPF, BGP).

🔹 **Avantage :** S’adapte aux changements du réseau (pannes, nouvelles routes, congestion).

🔹 **Inconvénient :** Consomme plus de ressources CPU et nécessite une configuration plus avancée.

### 🗺️ **Principaux protocoles de routage dynamique :**

- **RIP** (Routing Information Protocol) → Distance (nombre de sauts).
- **OSPF** (Open Shortest Path First) → Coût basé sur la bande passante.
- **BGP** (Border Gateway Protocol) → Utilisé sur Internet.



## **🚀 Processus de Routage : Exemple Pratique**

Un PC (192.168.1.100) veut contacter un serveur (10.0.0.1) :

1️⃣ **Le PC détecte que 10.0.0.1 n'est pas sur son réseau (192.168.1.0/24).**

2️⃣ Il envoie le paquet à sa **passerelle par défaut (192.168.1.1)**.

3️⃣ **Le routeur consulte sa table de routage** et voit une route vers 10.0.0.0/8.

4️⃣ **Il envoie le paquet vers un autre routeur** ou directement au serveur.

5️⃣ **Le serveur répond et suit le chemin inverse**.



## **📌 Pourquoi le Routage est Essentiel ?**

✅ **Permet la communication entre plusieurs réseaux.**

✅ **Optimise les performances en choisissant le meilleur chemin.**

✅ **Évite la surcharge réseau en segmentant le trafic.**

✅ **Supporte la scalabilité des réseaux complexes (ex : Internet).**
