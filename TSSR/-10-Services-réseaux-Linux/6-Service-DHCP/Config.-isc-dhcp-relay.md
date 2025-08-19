# Config. isc-dhcp-relayQuand un **serveur DHCP** est **sur un autre réseau** que ses clients, les paquets de type **DHCPDISCOVER (broadcast)** ne peuvent **pas traverser les routeurs**. Donc :

➡️ Les clients n’obtiendront pas d’IP. ➡️ On a besoin d’un **agent relais DHCP** sur le même réseau que les clients pour **capturer leurs requêtes DHCP** et les **relayer (en unicast)** vers le serveur DHCP. C’est un **intermédiaire obligatoire** dès qu’on sort du **même domaine de broadcast**.



**🔧 Paquet à installer**

👉 isc-dhcp-relay > C’est lui le petit frère du isc-dhcp-server, sauf qu’il ne sert pas les baux lui-même, il les relaie uniquement.



**📁 Fichier principal de config :**

**/etc/default/isc-dhcp-relay >** Contient les **paramètres de lancement du service**, pas la config réseau proprement dite (pas comme le dhcpd.conf). Voici les 3 directives principales à connaître :



**⚙️ Contenu d’un fichier /etc/default/isc-dhcp-relay**

SERVERS="192.168.42.2"  
INTERFACES="eth0 eth1"  
OPTIONS=""**🧭 SERVERS="IP1 IP2 ..."**

Définit **vers quel(s) serveur(s) DHCP** le relais doit transmettre les requêtes des clients. 📌 C’est l’IP du vrai serveur DHCP, accessible via une des interfaces du relais.

**🔁 INTERFACES="eth0 eth1 ..."**

Liste les interfaces **sur lesquelles écouter les requêtes clients** (DHCPDISCOVER sur les VLAN locaux) **et** envoyer les paquets au serveur DHCP.

👉 Il peut y en avoir **plusieurs**, c’est utile si ton relais est placé entre plusieurs VLANs.

Exemple : eth0(VLAN1) reçoit les broadcasts des clients / eth1(VLAN2) parle avec le serveur DHCP

**🛠️ OPTIONS="" (optionnel mais puissant)**

Tu peux y ajouter des **arguments supplémentaires** comme :

- -d : mode debug (log dans la console)
- -q : mode silencieux
- -a : active giaddr (indique au serveur d’où vient la requête)

👀 Très utile pour diagnostiquer si les paquets arrivent bien au serveur, notamment avec tcpdump.



**🔁 Redémarrage du service**

Une fois le fichier édité :

➡️ systemctl restart isc-dhcp-relay : Le service redémarre avec les bons paramètres.

➡️ Tu peux aussi lancer en **mode debug** pour voir passer les requêtes : isc-dhcp-relay -d -i eth0 -i eth1 192.168.42.2
