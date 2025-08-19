# Firewall/Iptables🌐 **Pare-feu : hôte vs réseau**

- **Pare-feu d’hôte** ➜ local à la machine → ex : iptables, firewalld
- **Pare-feu réseau** ➜ à la frontière du LAN → ex : Cisco ASA, Fortinet, Juniper, NGFW  
  🧠 Les deux peuvent coexister (defense-in-depth)



🔄 **Fonctionnement d’un pare-feu (ex : iptables)**

- Les règles sont lues **dans l’ordre**
- Dès qu’un paquet correspond à une règle, **l’action est appliquée** (stop immédiat)
- 🧠 Mettre les règles **spécifiques en premier**, générales ensuite
- Toujours **un DROP explicite à la fin**, ou policy par défaut en DROP



📋 **Rappels sur les chaînes iptables**

- **INPUT** : trafic entrant vers la machine
- **OUTPUT** : trafic sortant depuis la machine
- **FORWARD** : trafic qui traverse la machine (routeur)



🛠️ **Commandes iptables à retenir**

- **iptables -L** ➜ liste les règles
- **iptables -A** ➜ ajoute règle en **fin** de chaîne
- **iptables -I** ➜ insère en **début** de chaîne
- -p tcp/udp ➜ spécifie protocole
- -s / -d ➜ IP source/destination
- --sport / --dport ➜ port source/destination
- -j ACCEPT / DROP ➜ action à appliquer
- -D ➜ supprime une règle par son n° dans la chaîne
