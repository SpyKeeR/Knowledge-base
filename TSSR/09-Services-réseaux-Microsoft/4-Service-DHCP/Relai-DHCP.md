# Relai DHCP**🌐 Problème des réseaux de diffusion multiples**

Un serveur DHCP ne **répond qu’aux clients de son propre domaine de broadcast** 📴

➡️ Si plusieurs VLAN/réseaux sont isolés, les clients **n’entendent plus** le DHCP

💡 Solution : utiliser un **agent relais DHCP (DHCP Relay Agent)**



**📡 Rôle de l’agent relais DHCP**

Un relais DHCP joue les **intermédiaires** entre clients et serveur :

- Il **écoute les requêtes DHCP** dans son propre réseau
- Il les **transmet au serveur DHCP** situé ailleurs (via unicast)
- Il **reçoit les réponses** du serveur et les **redirige au client**

➡️ Permet de **centraliser** un serveur DHCP pour plusieurs réseaux isolés 🎯



**🔁 Le cycle DORA avec un relais**

Le fonctionnement est le même qu’un DHCP classique, avec **le relais comme intermédiaire** :

1.  📢 **Discover** : le client broadcast → le relais capte
2.  🎁 **Offer** : le serveur envoie une IP au relais → qui l’envoie au client
3.  📝 **Request** : le client accepte l’IP → relais transmet la demande
4.  ✅ **Acknowledge** : le serveur confirme → relais transmet au client

💡 Le relais **ne modifie pas** les données, il **relaye simplement** l'échange
