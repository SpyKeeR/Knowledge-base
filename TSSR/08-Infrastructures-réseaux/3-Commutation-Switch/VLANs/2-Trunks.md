# Trunks

**🔗 Qu’est-ce qu’un Trunk VLAN ?**

Un **trunk** est une **liaison point-à-point** entre deux équipements réseau (souvent des switches), capable de **transporter plusieurs VLANs** sur un seul lien.

🎯 Objectif : Étendre les VLANs d’un switch à l’autre pour que tous les ports dans un même VLAN (peu importe le switch) puissent communiquer.



## **⚙️ Configuration d’un Trunk**

🔹 Sur chaque interface de liaison entre switches :

`interface <ID>` → `switchport mode trunk` → `switchport trunk native vlan <ID>` → `switchport trunk allowed vlan <liste VLANs>` → `end`

🧠 Détails :

• mode trunk : passe l’interface en mode trunk permanent

• native vlan <ID> : VLAN non tagué (souvent utilisé pour la gestion ou le trafic par défaut)

• allowed vlan <IDs> : VLANs autorisés à passer (ex. 10,20,30 ou all)



### **🧪 Exemple de config simple**

🔸 Port FastEthernet 0/1 :

• Mode trunk activé

• VLAN natif = 99

• Tous les VLANs autorisés (config par défaut)

✳️ À noter :

Un **OU** devant une commande indique que cette config a **été supprimée** (ex : suppression du VLAN natif précédemment défini).



## **🔍 Vérification de la config Trunk**

🔹 Commande principale : `show interfaces <ID> switchport` ET `show interface trunk`

➡️ Permet de voir :

• Mode (access/trunk)

• Type d’encapsulation

• VLAN natif

• VLANs autorisés

🧠 À utiliser dès qu’un trunk ne fonctionne pas comme prévu. C’est ta loupe réseau 🔎

