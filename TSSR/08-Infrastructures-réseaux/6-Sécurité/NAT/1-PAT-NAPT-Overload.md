# PAT/NAPT/Overload

## **📦 NAPT – Un type particulier de NAT**

Le **NAPT (Network Address and Port Translation)** est une extension du NAT. Il ajoute la **gestion des ports** à la traduction des IP.

Grâce à lui, **plusieurs machines privées** peuvent **partager une même IP publique** tout en maintenant **des connexions distinctes** grâce aux ports.

🧠 Ex : PC1 et PC2 sortent via la même IP publique, mais :

- PC1 = 192.168.1.10:1234 → IP_publique:45000
- PC2 = 192.168.1.20:1234 → IP_publique:45001



### **🏠 RFC1918 – Les plages d’adresses privées**

La **RFC1918** définit les **blocs d’adresses IP privées**, qui ne sont **pas routables sur Internet** :

- 🧱 **Classe A** : 10.0.0.0 à 10.255.255.255 /8
- 🧱 **Classe B** : 172.16.0.0 à 172.31.255.255 /12
- 🧱 **Classe C** : 192.168.0.0 à 192.168.255.255 /16

📌 Ces adresses sont **réservées aux réseaux internes** (LAN) et doivent être **traduits par NAT** pour sortir sur le Net.



## **🎙️ Sockets – L’identité complète d’une communication**

Un **socket** identifie de façon **unique** une communication réseau. Il est formé de :

- 🧠 IP + Port  
  Ex : 192.168.1.10:443

C’est comme une **adresse complète** : la maison (IP) et la sonnette (port).



### **🧩 RFC2782 – Les plages de ports**

La **RFC2782** catégorise les **plages de ports TCP/UDP** :

- 🔒 **0–1023** : Ports **connus** (ex : HTTP=80, DNS=53)
- 📝 **1024–49151** : Ports **enregistrés** (utilisés par des applis définies)
- 🧪 **49152–65535** : Ports **dynamique/privés** (choisis aléatoirement pour les connexions sortantes)

🔐 Ces plages sont cruciales en NAT/NAPT pour créer des correspondances uniques de sessions sortantes.

![](../../../media/Cours-Infrastructures-réseaux-PAT-NAPT-Overload-image1.png)


