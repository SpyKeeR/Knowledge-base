# Config pfSense LDAP

## **🧾 Configuration générale**

- ➕ Ajout d’une **Autorité de Certification (AC)** :  
  🔒 Sécurise la communication VPN via des **certificats**

- ➕ Ajout d’une **authentification LDAP**
- 🔗 Connexion de pfSense à **Active Directory (AD)**
- ✅ Permet une **double authentification**



## **👥 Configuration de l’authentification LDAP**

📍 Menu : **Système** → **Gestionnaire d’usagers** → **Serveurs d’authentification** → **Ajouter**

**Paramètres de base**

- ✏️ **Nom descriptif** du serveur
- 🌐 **Nom du domaine AD**
- 🧬 **Type** : `LDAP`



## **⚙️ Paramètres serveur LDAP**

- 🖥️ **Nom d’hôte / IP** : FQDN ou adresse IP du contrôleur de domaine
- 🔌 **Port** : `389|636` (LDAP|s)
- 🔁 **Transport** : TCP standard
- 🏛️ **AC du pair** : choisir l’AC créée précédemment
- 🔢 **Version du protocole** : 3 (par défaut)
- ⏱️ **Délai de connexion** : 25 (par défaut)



## **🔍 Recherche LDAP**

- 📚 **Champ de recherche** :
  - 🌲 Sous-arbre entier → explore toute l’arborescence
  - 📁 Niveau unique → restreint à une OU spécifique
- 📌 **Base DN** : ex. `dc=entreprise,dc=local`
- 📂 **Conteneurs d’authentification** : OU(s) contenant les utilisateurs VPN
- ❌ Ne pas cocher “Activer les requêtes étendues”


## **🔐 Liaison LDAP à AD**

- 🔓 **Lier anonyme** : décoché → compte requis
- 👤 **Identifiants de liaison** : DN + mot de passe d’un compte AD
- 🧩 **Modèle initial** : Microsoft AD
- 🔠 **Attribut utilisateur** : `SamAccountName`
- 🏷️ **Attribut groupe** : `cn`
- 👥 **Membres du groupe** : `memberOf`
- ⚙️ Autres paramètres : laisser les **valeurs par défaut**



## **✅ Test de la configuration**

📍 Menu : **Diagnostics** → **Authentification**

- 🎯 Choisir le **serveur d’authentification LDAP**
- 👤 Saisir un **nom d’utilisateur** + **mot de passe**
- 🟢 Vérifier la **réussite du test**



## **🌐 Résolution DNS dans pfSense**

📍 Menu : **Système** → **Configuration générale**

- 🧩 **Domaine** : ajouter le **suffixe DNS** (ex. .entreprise.local)
- 📡 **Serveur DNS** : ajouter l’IP du serveur DNS interne
- ⚠️ **Remplacer DNS local** : décocher la case
- 🔧 **Comportement DNS** : Utiliser les serveurs DNS distants, ignorer le DNS local
