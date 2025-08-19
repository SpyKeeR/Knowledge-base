# Commutateurs virtuels **🔒 Privé (Private)**

- **Les VM peuvent communiquer entre elles uniquement**
- ❌ **Pas de communication avec l'hôte**
- ❌ **Pas d'accès à Internet ni au réseau local**
- ✅ Utile pour : tests isolés, simulations réseau totalement autonomes



**🧩 Interne (Internal)**

- **Les VM peuvent communiquer entre elles**
- ✅ **Et avec l’*hôte*****
- ❌ **Mais pas avec le réseau externe (pas d'Internet)**
- ✅ L’hôte peut donc jouer un rôle de serveur DHCP, DNS, ou AD
- ✅ Très pratique pour un environnement de test avec un "contrôleur de domaine" sur l’hôte

✳️ **⚠️ Ton cours inverse ces deux-là !**

Il décrit **"Privé"** comme s’il avait un lien avec l’hôte, alors que c’est **le rôle de "Interne"**.



**🌐 Externe (External)**

- **Les VM peuvent communiquer entre elles**
- ✅ **Avec l’hôte**
- ✅ **Et avec le réseau local / Internet (via la carte réseau physique)**
- ⛔ **Attention : si non partagé avec l’hôte**, l’hôte perd l’accès au réseau via cette carte



**🎯 Externe dédié**

- Variante de "Externe" :
- 🧠 **La carte physique est *exclusivement* dédiée à une VM**
- ❌ L’hôte **n’a plus accès du tout à cette carte**
- ✅ Pratique pour des usages spécifiques (ex : VM de firewall, iSCSI, VLAN dédiés)



**Mise en œuvre d'un Switch Externe**

- La **carte réseau physique** est capturée par Hyper-V → plus de rôle direct pour l’OS hôte, elle devient **port uplink Layer 2** du **Virtual Switch**.
- Le **Virtual Switch** (niveau Hyper-V) devient une espèce de **mini switch Ethernet software**, auquel tu peux raccorder :
  - Des VM
  - Une carte virtuelle (vNIC) pour l’hôte (si partage activé)
- L’**interface vEthernet (NomDuSwitch)** est cette fameuse carte virtuelle, ajoutée à l’OS hôte, **branchée sur le switch virtuel**, et c’est elle qui porte **l’IP, la passerelle.**
