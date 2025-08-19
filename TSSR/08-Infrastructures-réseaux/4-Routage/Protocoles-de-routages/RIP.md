# RIP**📛 Présentation du protocole RIP**

RIP (*Routing Information Protocol*) est un **protocole de routage dynamique standard**, basé sur un **vecteur de distance** (distance-vector).

Il existe en 3 variantes :

- **RIPv1** (classful),
- **RIPv2** (classless),
- **RIPng** (supporte IPv6).



**🧠 Caractéristiques techniques à retenir**

- **Type** : IGP (Interior Gateway Protocol)
- **Distance administrative** : 120
- **Métrique** : Nombre de **sauts (hops)**
- **Identifiant dans la table de routage** : **R**

💡 *Exemple dans show ip route* : R 192.168.3.0/24 [120/2] via 192.168.66.1

➤ "R" pour RIP, "120" = distance admin, "2" = sauts, "192.168.66.1" = passerelle.



**🛰️ Fonctionnement réseau**

- **Protocole de transport** : UDP (n°17)
- **Ports utilisés** : 520 (IPv4) / 521 (IPv6)
- **Type d’adresses utilisées pour les updates** :
  - RIPv1 : **Broadcast** → 255.255.255.255
  - RIPv2 : **Multicast** → 224.0.0.9
- **Fréquence des updates** : toutes les **30 secondes**

**⚙️ Configuration rapide de RIP**

📌 En RIPv2 (le plus courant) :

router rip  
version 2  
network 10.0.0.0  
network 192.168.1.0

🔁 Chaque network permet d’inclure un sous-réseau dans le processus RIP.

✅ Après configuration, les routes RIP apparaissent dans show ip route.



**🔍 À quoi faire attention ?**

- RIP limite à **15 sauts max** (au-delà = inatteignable).
- Convergence **lente**, sensible aux **boucles** sans mécanismes de prévention (split horizon, hold-down, etc.).
- Pratique en **lab ou petits réseaux**, mais souvent remplacé en prod par **OSPF** ou **EIGRP**.
