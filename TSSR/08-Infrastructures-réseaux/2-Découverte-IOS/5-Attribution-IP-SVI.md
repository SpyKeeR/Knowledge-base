# Attribution IP - SVI

**🌐 Qu’est-ce qu’une SVI ❓**

Une **SVI (Switch Virtual Interface)** est une interface **logique** sur un VLAN, utilisée pour **gérer un switch via IP** (ex : via SSH ou Telnet).

➡️ Elle est associée à un VLAN (souvent le VLAN 1 par défaut)

➡️ Elle ne fonctionne que si **au moins une interface physique du VLAN est en état "up"**



## **🛠️ Étapes de configuration**

Pour attribuer une IP à une SVI et activer l’accès distant :

1.  **Entrer en mode config globale** ➜ `configure terminal`
2.  **Sélectionner l’interface VLAN 1** ➜ `interface vlan 1`
3.  **Attribuer l’adresse IP + masque** ➜ `ip address 192.168.1.20 255.255.255.0`
4.  **Activer l’interface** ➜ `no shutdown`

💡 Sans no shutdown, la SVI reste down même si elle est logique.



### **🧪 Exemple typique**

Pour un switch connecté à un réseau 192.168.1.0/24 :

- IP : 192.168.1.20
- Masque : 255.255.255.0  
  ➡️ Le switch sera **accessible à distance** depuis un PC du même réseau.



### **⚠️ Rappels importants**

- SVI **≠** port physique, mais dépend de lui pour passer "up"
- Pense à tester l’**accessibilité avec un ping** depuis un autre appareil
- Ne pas oublier de **sauvegarder** la config si tout fonctionne ➜ copy running-config startup-config



### **✅ Pourquoi c’est crucial**

✔️ Permet l’**administration à distance** du switch

✔️ Nécessaire pour **accéder au switch sans câble console**

✔️ Premier pas pour configurer SSH, Telnet, SNMP…

