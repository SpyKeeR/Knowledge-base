# Informations réseaux

## 🔍 **Pourquoi c’est important ?**

Avant de configurer quoi que ce soit, **on doit absolument savoir** :

- Quelle IP a notre machine ? Quel est son masque de sous-réseau ? Quelle est la passerelle par défaut ? Quels serveurs DNS sont utilisés ?

Sans ça, impossible d’assurer une bonne communication réseau entre les serveurs, les clients, et Internet.



## **🛠️ Commandes essentielles**

### **📌 1. Voir l'adresse IP et les interfaces**

**Commande** : `ip a` ou `ip address`

**À savoir** :

- **lo** : interface "localhost" (127.0.0.1), à **ne jamais désactiver**.
- **ens33** (ou autre nom) : ta carte réseau principale.
  - On y voit l’**IPv4** (ex : 192.168.1.50) et l’**IPv6** (ex : fe80::...).



### **📌 2. Voir la route par défaut (passerelle)**

**Commande** : `ip r`

**À savoir** :

- **default via 192.168.1.1** → ça veut dire que 192.168.1.1 est notre **passerelle vers Internet**.
- C’est ultra important pour la connectivité externe.



### **📌 3. Voir les serveurs DNS utilisés**

**Commande** : `cat /etc/resolv.conf`
```bash
nameserver 10.1.2.20  
nameserver 10.1.2.21  
search exemple.domaine
```

**À savoir** :

- **search** pour domaines à utiliser pour compléter les hostname seuls
- **domain** pour 1 domaine à utiliser pour compléter les hostnames (déprécié)
- **nameserver** pour designer des Serveurs résolveurs DNS
- **options** pour configurer des options supplémentaires




