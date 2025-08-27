# ACL Etendue



## **🔍 ACL Étendues : à quoi ça sert ?**

Les **ACL étendues** filtrent les paquets **IP** selon :

- 🧭 **Adresse IP source**
- 🎯 **Adresse IP de destination**
- 📦 **Protocole (TCP, UDP, ICMP…)**
- 🚪 **Port(s) spécifique(s)** (ex : HTTP = 80, DNS = 53)
- 📌 Numérotation : 100 à 199 (classique) et 2000 à 2699 (plage étendue)

📍 **À positionner au plus près de la source** du trafic à bloquer (pour éviter de filtrer trop large trop tôt)



### **🧾 Syntaxe & exemple**

Deux manières de les créer, comme pour les ACL standards :


#### **🧱 Numérotée :**

`access-list <num> remark <commentaire>`

#### **🏷️ Nommée :** 

`ip access-list extended <nom>`

#### **Complète one-line**

`{ip} access-list <num>{nom} deny|permit <proto> <IP src> <wildcard> <IP dst> <wildcard> eq <port>`

#### Exemple : 

![](../../../media/Cours-Infrastructures-réseaux-ACL-Etendue-image1.png)

## **🔧 Protocole et ports gérés**

Les **protocoles possibles** dans une ACL étendue : tcp, udp, icmp, ip, eigrp, ospf, gre, etc.

Les **ports** peuvent être précisés : eq 80 (HTTP), eq ftp (21), eq dns (53), eq smtp (25), etc.  

✳️ Tu peux taper ? après eq dans Cisco IOS pour avoir les noms dispo



## **🚦 Appliquer une ACL étendue**

Sur une interface, avec `ip access-group` : En **entrée** (in) ou en **sortie** (out), selon le sens du trafic à filtrer

Si tu veux bloquer les requêtes HTTP sortant de LAN20 → mettre l’ACL **en entrée** de l’interface LAN20 OU Si tu veux bloquer les réponses → **en sortie** de LAN40



### **🧪 Vérification**

🔍 Commandes utiles :

- `show access-lists` → affiche les règles + compteur de matches
- `show ip interface <iface>` → pour voir si une ACL est appliquée en in ou out
- `clear access-list counters` → reset des compteurs si besoin



