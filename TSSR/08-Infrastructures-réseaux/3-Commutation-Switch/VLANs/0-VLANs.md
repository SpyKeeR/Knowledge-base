# VLANs

**🧩 VLAN – Virtual Local Area Network**

Un **VLAN** permet de créer des **réseaux logiques indépendants** au sein d’un même switch physique.

🎯 Objectif : **segmenter le réseau** sans multiplier les équipements.



## **⚙️ Fonctionnement des VLANs**

Un VLAN agit comme un **sous-réseau isolé** → il a :

• sa **propre plage IP (réseau logique couche 3)**,

• son **propre domaine de broadcast** de couche 2,

• une séparation **logique** mais pas physique.

Chaque VLAN isole les flux **broadcast, multicast et unicast**, ce qui **réduit la portée** des diffusions inutiles.

🧠 Exemple courant : 1 VLAN = 1 service (admin, prod, invités, etc.)



## **🛠️ Avantages des VLANs**

Créer des VLANs, c’est :

• 🔒 Réduire les domaines de diffusion → moins de bruit réseau,

• 🔐 Augmenter la sécurité → seuls les hôtes d’un même VLAN peuvent échanger directement,

• ⚡ Optimiser les perfs → bande passante mieux utilisée,

• 💸 Réduire les coûts → un seul switch physique pour plusieurs groupes,

• 🤝 Simplifier la gestion → groupes logiques selon les besoins (RH, dev, etc.)

💡 Le **dot1Q** indique le marquage VLAN 802.1Q. À préciser si le VLAN est **natif** (non tagué).



## **🧱 Types de VLANs**

Selon le trafic à gérer, plusieurs VLANs peuvent coexister :

• **📧 VLAN de données** → trafic classique (web, mail, etc.)

• **🟡 VLAN natif** → utilisé sur les trunks 802.1Q pour les trames non taguées

• **🔐 VLAN de gestion** → dédié à l’admin distante (SSH, Telnet, VTY), séparé du trafic utilisateur

• **🎙️ VLAN voix** → conçu pour les flux VoIP, priorisé via la QoS, isolement nécessaire

📝 Note : Tous ces VLANs peuvent exister sur le même switch, avec leurs ports associés et parfois transportés via un trunk vers d’autres switches.

