# NAT

Le **NAT (Network Address Translation)** est une réponse directe à la **pénurie d’adresses IPv4 publiques**.

Il permet à plusieurs machines internes, en **IP privée**, de partager une **même adresse IP publique** pour accéder à Internet.

### 🔧 **Fonctions principales** du NAT :

- 🔁 Modifie l’**adresse IP source ou destination**
- 🎯 Modifie aussi le **port source ou destination** (quand combiné avec NAPT)

🧪 Très utilisé dans les box Internet ou les pare-feux pour faire le lien entre ton réseau privé et le monde extérieur.



### **⚙️ Comment fonctionne la NAT ?**

La NAT agit comme un **pont traducteur** entre :

- 🔒 Réseau privé (`ip nat inside`)
- 🌍 Réseau public (`ip nat outside`)

🎯 Exemple typique : Quand un PC interne (ex : 192.168.1.10) envoie une requête vers Internet, le routeur NAT la **traduit** avec une IP publique (ex : 200.0.0.1), masque l’IP interne, et gère le retour de paquet.



### **🧠 Composants essentiels de la NAT**

- **Inside local** : IP privée de l’hôte
- **Inside global** : IP publique utilisée pour représenter l’hôte privé sur Internet
- **Outside global/local** : IP publique de la destination (identique dans NAT classique)



## **🧪 Exemple de configuration dynamique**

➡️ Commande clé (avec surcharge) : `ip nat inside source list 1 interface Serial0/0/0 overload`

Cela signifie :

- La **liste d’accès 1** définit les IP à traduire
- Le **port série** est l’interface sortante NAT
- **overload** = PAT (Port Address Translation), permet à **plusieurs hôtes privés de partager une seule IP publique**



### **🛡️ Avantages de la NAT**

- 🌍 **Économie d’IP publiques** (une seule IP pour des dizaines/centaines de machines)
- 🔐 **Sécurité accrue** : l’IP privée est **non exposée** à Internet
- 🔧 **Souplesse** : facile à déployer sans reconfigurer le réseau interne
- 🧱 **Masquage** de la structure réseau interne



#### **🏢 Cas d’usage courant**

Une entreprise avec des hôtes en **192.168.x.x** veut accéder au web. Grâce à la NAT, tout le trafic sort via **une IP publique unique**, tout en **gardant l’architecture privée invisible** depuis l’extérieur.

