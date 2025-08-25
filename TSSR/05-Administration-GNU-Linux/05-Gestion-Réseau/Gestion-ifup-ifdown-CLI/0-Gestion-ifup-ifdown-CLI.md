# Gestion ifup/ifdown - CLI

## **🧩 Pourquoi ce fichier est super important ?**

Quand t’as **pas d’interface graphique** sur ton Linux, c’est **/etc/network/interfaces** qui gère **tout ton réseau** :

- IP / Masque / Passerelle / Activation des cartes réseau / Et parfois DNS (en combo avec resolv.conf)

💡 **Attention** : ce fichier est sensible. Une erreur et tu perds la connexion !

### **📌 1. Configurer une carte réseau (ex: ens33)  
Ne jamais toucher à la conf Looback**

**Activation automatique** : auto ens33

**Activation au branchement** : allow-hotplug ens33

Les deux sont utiles selon si tu veux que ta carte démarre **systématiquement** ou seulement **quand elle est branchée**.



### **📌 2. DHCP (automatique)**

Ton interface choppe une IP tout seule :

iface ens33 inet dhcp



### **📌 3. IP statique**

Si tu veux tout contrôler toi-même :

iface ens33 inet static  
address 10.1.1.10  
netmask 255.255.255.0  
gateway 10.1.1.1

👉 Tu peux aussi écrire ton IP en format CIDR : address 10.1.1.10/24

### **📌 4. Redémarrer proprement le réseau**

**Commandes** :

```bash
sudo systemctl stop networking  
sudo systemctl start networking
```

✅ **stop + start** est souvent mieux que **restart**, qui peut être brutal et mal recharger certaines interfaces.


