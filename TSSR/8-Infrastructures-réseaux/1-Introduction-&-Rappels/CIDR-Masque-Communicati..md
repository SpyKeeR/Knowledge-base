# CIDR / Masque / Communicati.**🧮 Notation CIDR et masque de sous-réseau**

Le **masque de sous-réseau** sert à **découper un réseau IP** en petits blocs appelés **sous-réseaux**.

👉 La notation **CIDR (/xx)** indique combien de bits sont utilisés pour désigner la partie **réseau** dans une adresse IP.

🧠 Exemple : 192.168.0.0/29 ➜ 29 bits pour le réseau, 3 bits restants pour les hôtes.

Le **masque décimal** correspondant : **255.255.255.248**.

**📊 Calcul de plage et rôles des adresses**

Dans un sous-réseau donné, on identifie :

- 🆔 **Adresse réseau** : 1ère adresse (non attribuable) ➜ identifie le sous-réseau
- 🌍 **Plage valide** : les adresses utilisables pour les hôtes
- 📣 **Adresse de broadcast** : dernière adresse ➜ utilisée pour diffuser à tout le réseau
- 🔁 **Adresse du sous-réseau suivant** : directement après la broadcast

🧪 Exemple : 192.168.0.0/29 ➜ Réseau : 192.168.0.0 | Plage : 192.168.0.1 à .6 | Broadcast : 192.168.0.7 | Réseau suivant : 192.168.0.8

**🧩 Création de sous-réseaux**

Réduire le nombre de bits hôtes ➜ **plus de sous-réseaux, moins d’adresses par réseau**.

Ex : en /30 (2 bits hôtes), on obtient **2 adresses valides** par sous-réseau.

Tu peux donc diviser un /29 en **2 sous-réseaux /30** :

192.168.0.0/30 (plage .1 à .2) et 192.168.0.4/30 (plage .5 à .6).



**🌐 Domaine de diffusion et communication**

Chaque **sous-réseau est un domaine de diffusion**. Un **routeur** sépare ces domaines.

⚠️ Deux machines sur **des réseaux différents** doivent **passer par une passerelle** (routeur) pour communiquer.



**🤖 Communication inter-réseaux (algorithme simplifié)**

Voici ce qui se passe quand un hôte veut parler à un autre :

1.  🧠 **Calcul du réseau** (avec l’IP et le masque)
2.  🔍 **Comparaison réseau source ≠ destination** ?

    - Si **oui** ➜ passage par **passerelle**
    - Si **non** ➜ communication locale possible

3.  📓 **Consultation de la table ARP**

    - Si l’IP cible y est ➜ on a l’adresse MAC ➜ envoi direct
    - Sinon ➜ requête **ARP** pour la découvrir

4.  📶 **Réponse ARP reçue ?**

    - Oui ➜ table mise à jour ➜ envoi du ping
    - Non ➜ erreur IP ou **ICMP unreachable**
