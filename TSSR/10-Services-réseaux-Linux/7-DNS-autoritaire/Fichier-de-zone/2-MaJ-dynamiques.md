# MaJ dynamiques

L’activation se fait dans la déclaration de zone (dans named.conf.local) avec `allow-update`.

✅ Effets de l’activation :

• Bind9 **prend le contrôle total** du fichier de zone

• **Interdiction de modifier directement** le fichier (sauf procédure spéciale)

• Incrémentation automatique du **serial SOA**

• Création d’un fichier .jnl qui stocke les modifications en attente de fusion

🧱 Exemple : Zone eni.demo autorisant le réseau 192.168.53.0/24 + localhost à faire des updates.



## 🖥️ **🧍‍♂️ Mises à jour côté client (Postes utilisateurs)**

Pour que les clients fassent des updates automatiques :

• Il faut que le **suffixe DNS** du domaine soit bien configuré

• 🪟 **Windows** : Paramètre "Suffixe DNS Principal" dans le nom de la machine

• 🐧 **Linux** : Valeur définie dans le hostname ou dans /etc/hosts



## 🧊 **⛔ Modifier manuellement une zone dynamique**

🔹 Utiliser nsupdate (paquet dnsutils) pour ajouter/supprimer dynamiquement via terminal

🔹 Sinon, pour toucher au fichier manuellement :

• **Geler la zone** avec `rndc freeze nom.zone`

• Modifier le fichier (⚠️ incrémenter le SOA manuellement !)

• **Dégeler** ensuite avec `rndc unfreeze nom.zone` → la zone est rechargée automatiquement



## 🤝 **🤖 Mises à jour dynamiques via serveur DHCP**

Plutôt que laisser tous les clients faire des updates (risque de désordre), on peut confier ce rôle au **serveur DHCP** (idéal en environnement GNU/Linux).

### 🔹 Côté DNS (Bind9) :

• Adapter `allow-update` pour ne cibler que le ou les serveurs DHCP (adresse IP ou clé TSIG)

### 🔹 Côté DHCP (dhcpd.conf) :

• `ddns-updates on;` → Active les updates

• `ddns-update-style standard;` → Mode classique

• `ddns-domainname "demo.eni";` → Nom du domaine DNS

• `ignore client-updates;` → Le serveur DHCP prend le relais, pas les clients

• `update-static-leases on;` → Pour que les réservations soient aussi mises à jour

### 🔹 Déclarer les zones gérées par le DHCP :

• `zone demo.eni { primary 10.5.3.10; }`

• `zone 42.168.192.in-addr.arpa { primary 10.5.3.10; }`

→ Zones directe et inverse + serveur principal cible


