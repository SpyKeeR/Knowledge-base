# Mise en Place de l'infrastructure

## **🧑‍💼 Active Directory**

📘 Un **domaine Active Directory** est un ensemble d'ordinateurs liés à une base commune, régie par des règles de sécurité et administrée de manière centralisée.

## 🧱 **Objets du domaine** :

Tout élément intégré (utilisateur, groupe, poste) est un **objet**.

Un poste non intégré est **autonome** et ne peut accéder aux ressources du domaine.

## 🛡️ **Contrôleur de domaine (DC)** :

Serveur essentiel hébergeant la **base AD**, assurant :

- L’authentification des utilisateurs/ordinateurs
- L’application des stratégies de sécurité
- La gestion des objets du domaine


## **📡 DHCP – Attributions automatiques**

### 🔄 Le **serveur DHCP** fournit automatiquement :

- Adresse IP
- Masque de sous-réseau
- Passerelle par défaut
- Serveur DNS

### 📋 **Message DHCP** contient :

- Une **adresse IP** attribuée depuis une plage disponible
- Une **durée de bail** (ex. 2 jours)
- D’autres paramètres réseau

### 🏗️ **Étendue DHCP** :

Zone de distribution configurée avec :

- Un **nom descriptif** (ex. LAN_Serveurs)
- Une **plage IP** (ex. 192.168.0.1 à 192.168.0.254 /24)
- Une **durée de bail** (ex. 2 jours)

### 🚫 **Exclusions** :

Permettent d’empêcher l’attribution de certaines IP (ex. imprimantes fixes).

Exemple : exclure 192.168.0.5 à 192.168.0.10.

## **🌐 DNS – Système de noms de domaine**

### 🧭 **DNS Resolver** :

Agit comme **intermédiaire** entre les clients et les serveurs DNS pour résoudre les noms.

Il **n’héberge pas** les zones de noms, ne fait **pas autorité**.

### 📘 **Fonctionnement** :

- Reçoit une requête du client
- Interroge d’autres serveurs DNS
- Transmet la réponse au client

### 🏛️ **DNS Hébergeur** :

- Fait **autorité** sur les **espaces de noms** qu’il gère.
- Contient une base de données locale des correspondances noms/IP.

✅ Les réponses du DNS hébergeur sont **fiables** car elles proviennent de l’autorité.


## **🛠️ Outils de gestion Windows**

### 🖥️ **Consoles graphiques** :

- Interface conviviale (ex. Gestionnaire de serveur)
- Permettent la gestion visuelle des rôles et services

### 💻 **PowerShell** :

- Interface puissante en ligne de commande
- Permet l’**automatisation** et la gestion avancée

### 🔍 **Exemple de commande** : 
`Get-Service | Where-Object {$_.Status -eq 'Running'}`

📊 Affiche les **services en cours d’exécution** sur la machine.