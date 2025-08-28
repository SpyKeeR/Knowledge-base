# Config. isc-dhcp-server

## 🧱 **1. Installation du paquet**

Le serveur DHCP utilisé est **isc-dhcp-server**, à installer depuis les dépôts classiques.

🔧 Ce paquet installe :

- Le **service** isc-dhcp-server
- Les **fichiers de config** /etc/default/isc-dhcp-server & /etc/dhcp/dhcpd.conf
- Le **fichier de baux dynamiques** /var/lib/dhcp/dhcpd.leases
- Les **logs** dans /var/log/syslog



## 🎛️ **2. Configuration : /etc/default/isc-dhcp-server**

Ce fichier définit **sur quelle(s) interface(s)** le service DHCP doit écouter.

📌 Exemple : `INTERFACESv4="ens33"` (séparer par espace si plusieurs)

⚠️ Obligatoire ! Sinon le service refusera de démarrer.


## 🧾 **3. Configuration principale : /etc/dhcp/dhcpd.conf**

Contient toute la logique de distribution des IPs.

**🛠️ Paramètres globaux (entête du fichier) :**

- `option domain-name` → nom de domaine transmis (ex: "lab.local")
- `option domain-name-servers` → DNS à fournir aux clients (ex: 8.8.8.8)
- `default-lease-time` / `max-lease-time` → durées des baux en secondes (21666 et 42000)
- `ddns-update-style none` → désactive les mises à jour DNS dynamiques
- `authoritative` → déclare que ce serveur est l'autorité DHCP sur ce réseau
- `log-facility local7` → niveau de journalisation, rarement modifié



### **🌐 Paramétrage des sous-réseaux (subnet) :**

Chaque bloc subnet configure un réseau précis.
```bash
subnet 172.19.0.0 netmask 255.255.255.0 {  
range 172.19.0.10 172.19.0.250;  
option routers 172.19.0.1;  
option domain-name "lab.local";  
option domain-name-servers 8.8.8.8;  
}
```
💡 Si ton serveur est sur un réseau différent du réseau client, il te faut *quand même* déclarer un bloc subnet correspondant à son interface réseau, même si tu n’y attribues pas d’IP.



## 📌 **4. Attribution d’adresses IP statiques (host)**

Utile pour fixer une IP à une machine précise selon son adresse MAC :
```bash
host imprimante_hp {  
hardware ethernet 08:00:27:aa:bb:cc;  
fixed-address 172.19.0.5;  
}
```
🎯 Tu peux combiner des plages dynamiques *et* des IP fixes dans une même config.



## 🧪 **5. Vérification & lancement du service**

Avant toute chose, **teste ta config** avec : `dhcpd -t`

### ✅ Si pas d’erreurs, tu peux ensuite :

- Redémarrer le service : `systemctl restart isc-dhcp-server`
- Vérifier son état : `systemctl status isc-dhcp-server`
- L’arrêter / démarrer : `systemctl stop/start isc-dhcp-server`

🔍 **Debug en direct :** Lance en mode verbeux : `dhcpd -d` → Affiche les échanges DHCP en console (Discover, Offer, Request, ACK…).
