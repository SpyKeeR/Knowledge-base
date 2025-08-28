# **🌐 Routage : limites et besoin de NAT**

➡️ Le **routage** classique repose sur des **tables de routage** : chaque routeur doit connaître les réseaux à atteindre.

❗Mais ce n’est **pas suffisant** dans certains cas :

- Réseaux **non routables**
- Réseaux **privés (RFC1918)** non joignables directement sur Internet
- Réseaux **temporaires ou isolés** (ex. maquettes, labos, etc.)

📍 Résultat : ces machines ne peuvent **pas sortir d’elles-mêmes**, elles ont besoin qu’un autre équipement **modifie l’adresse** pour elles.



## **🔁 NAT – Network Address Translation**

🎯 Le NAT permet de **convertir les adresses IP** entre un réseau local et un réseau externe.

Deux formes principales :

- **SNAT (Source NAT)** 👉 remplace **l'adresse IP source** du paquet
- **DNAT (Destination NAT)** 👉 remplace **l'adresse IP de destination**

➡️ **Utilité principale** : permettre à des machines en IP privée de **sortir sur Internet** ou être **accessibles de l’extérieur**.



### **🧩 Cas d’usage typiques**

| **Type de réseau** | **Besoin** | **NAT utilisé** |
|----|----|----|
| Réseau privé vers Internet | Navigation, MAJ, accès web | **SNAT** |
| Internet vers serveur local (DMZ ou LAN) | Hébergement, services accessibles | **DNAT** |
| Réseaux non routés (maquette, test) | Accès à des services hors LAN | **SNAT + DNAT** si bidirectionnel |

📝 **Important** : NAT **ne remplace pas** un vrai routage, il le **complète** quand le routage ne peut pas suffire.



## **🛠️ Mise en œuvre du NAT sous Linux & PfSense**

💡 Plusieurs façons d'implémenter le NAT :

- **iptables** : solution native Linux (manuel ou scripté)
- **Shorewall** : framework facilitant la gestion d’iptables via des fichiers de conf
- **PfSense** : solution utilisée dans ce cours (web UI intuitive, puissante)

