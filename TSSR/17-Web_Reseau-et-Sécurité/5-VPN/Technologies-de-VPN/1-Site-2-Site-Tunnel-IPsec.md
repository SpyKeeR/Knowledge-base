# Site 2 Site : Tunnel IPsec

## **🔐 Principe de fonctionnement d’un VPN IPSec**

Le protocole **IPSec** (Internet Protocol Security) permet de **sécuriser les communications intersites** via Internet. Il repose sur **deux tunnels distincts** et complémentaires :



## **🛠️ Tunnel IKE (Internet Key Exchange)**

### 🔧 **Rôle : négociation des paramètres de sécurité**

Le tunnel IKE est **la première phase** de l’établissement du VPN. Il permet :

- 🤝 **Négociation des paramètres** :  
  Échange d’algorithmes de chiffrement, méthodes d’authentification, durées de vie, etc.

- 🔑 **Établissement de clés de session** :  
  Génération de clés temporaires qui seront utilisées par le tunnel IPSec.

🎯 Ce tunnel est **essentiel à la phase d’initiation** et assure que les deux extrémités sont en accord avant l’échange de données.



## **📦 Tunnel IPSec**

### 🔐 **Rôle : transport sécurisé des données**

Le tunnel IPSec est **la seconde phase**, responsable du **chiffrement des données** échangées après l’établissement du tunnel IKE.

Il permet :

- 🕵️‍♂️ **Chiffrement des données** :  Garantit la **confidentialité** des communications.

- 🧪 **Vérification d’intégrité** :  Empêche la modification ou la falsification des paquets.

- ✅ **Authentification des sources** : S'assure que les données proviennent bien de l’émetteur légitime.


### **🔑 4 aspects clés d’IPSec**

- 🔐 **Confidentialité** : chiffrement des données
- 👤 **Authentification** des partenaires
- 🛡️ **Intégrité** des données (non altérées)
- 🚫 **Anti-rejeu** : protection contre la répétition des paquets


## **🧭 Application : VPN site-à-site**

Utilisé pour connecter **deux réseaux distants** (par exemple deux sites d’entreprise) via un tunnel sécurisé :

- 🔄 Les deux sites échangent via leurs **passerelles VPN (routeurs/firewalls)**.
- 🧷 Les communications passent par les **tunnels IKE/IPSec**, rendant invisibles les données aux yeux d’Internet.
- 🔒 **Authentification mutuelle** par PSK (clé partagée) ou certificats.

### **🛠️ Usage principal**

- Relier **deux sites distants géographiquement** via un tunnel sécurisé.
- Utilise le protocole **IPSec**, qui opère en **couche 3** (réseau) du modèle OSI.




## **⚙️ Établissement du tunnel IPSec**

### **Phase 1 :**

- Création d’un **canal sécurisé d’authentification** entre les deux sites.
- Basée sur un **échange de clés** (généralement clés pré-partagées).
- Aucun transfert de données réelles à ce stade.
- Utilisation du **mode Main** pour les connexions site à site (entre deux IP publiques).

### **Phase 2 :**

- Mise en place du **tunnel chiffré** pour le transport sécurisé des données.
- Utilisation du **mode Tunnel** pour encapsuler les paquets IP.
- Application des **politiques de sécurité** définies (chiffrement, intégrité, etc.).
- Transfert sécurisé des données entre les deux réseaux distants.