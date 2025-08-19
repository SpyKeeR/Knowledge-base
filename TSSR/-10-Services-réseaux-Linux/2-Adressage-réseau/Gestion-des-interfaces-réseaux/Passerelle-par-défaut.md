# Passerelle par défaut**🌐 Qu’est-ce qu’une passerelle par défaut ?**

La **gateway (passerelle par défaut)** est le point de sortie du réseau local vers d'autres réseaux, comme Internet. C’est souvent l’IP interne du routeur.

Si un paquet n’a pas de route connue, il est redirigé vers cette passerelle.



**🛠️ Méthodes de configuration de la gateway**

Tu peux définir/modifier la passerelle de trois façons différentes :

- **Via NetworkManager (nmcli)** → nmcli connection modify <nom_interface> ipv4.gateway <gateway>
- **Via le fichier /etc/network/interfaces** → ajouter la ligne gateway 192.168.X.X
- **Via la commande ip route** → ip route add/change/del default via <gateway>

Chaque méthode permet la configuration de la gateway, mais à ne pas mélanger !



**🔍 Vérification de la passerelle actuelle**

Tu peux consulter la **table de routage** avec :

- ip route (ou plus court : ip r)
- Cherche une ligne du type : default via 192.168.0.254 dev ens37

Cela signifie : passerelle par défaut → 192.168.0.254, via l’interface ens37.



**➕ Ajouter une route par défaut**

Ajout d’une nouvelle gateway (il ne peut y en avoir **qu’une seule par défaut**) :

- ip route add default via 10.11.0.254

🔄 **Modifier** la route par défaut :

- ip route change default via 10.11.255.254

❌ **Supprimer** une route par défaut :

- ip route del default via 192.168.0.254
