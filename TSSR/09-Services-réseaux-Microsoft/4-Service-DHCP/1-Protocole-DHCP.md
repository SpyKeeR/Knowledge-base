# **🌐 Qu’est-ce que le DHCP ?**

DHCP = **Dynamic Host Configuration Protocol**

📡 Protocole réseau qui **automatise l’attribution d’adresses IP** aux machines.

➡️ Il fournit aussi d’autres paramètres réseau : **passerelle, DNS, nom de domaine...**

🎯 Objectif : **éviter la config manuelle** sur chaque poste et faciliter la gestion.

🛠️ Fonctionnement :

Quand un poste démarre,

- il envoie une **demande d’adresse (DHCPDISCOVER)**
- → le serveur DHCP **répond avec une IP et des infos réseau (DHCPOFFER)**
- → le poste **accepte (DHCPREQUEST)**
- → le serveur **confirme (DHCPACK)**.

✅ Toute cette séquence est automatique et rapide.



## **✅ Les avantages du DHCP**

🧠 Centralisation → config unique sur le serveur, déploiement automatique aux clients

⏱️ Gain de temps → plus besoin de toucher à chaque poste

📦 Paramétrage uniforme : DNS, passerelle, domaine

🔄 Mise à jour facile → tout changement se propage sur tout le parc

🚫 Moins d’erreurs de config manuelle (conflits IP, fautes de saisie…)

💡 Résultat : un réseau plus propre, plus cohérent, plus simple à maintenir.



## **⚠️ Les inconvénients du DHCP**

🔓 **Risque DoS (épuisement d’IP)** → un attaquant spamme le serveur de requêtes

🌍 **DHCP = protocole ouvert** → écoute possible par des clients non autorisés

🛡️ Solution : **filtrage DHCP** sur les switches, contrôle d’accès au réseau, segmentation

🎯 Le DHCP, bien que pratique, doit être **sécurisé et surveillé**.

Une **mauvaise config = impact global**, donc chaque option doit être pensée.

