# SAN / NAS

## 🔌 **SAN (Storage Area Network)**

Le **SAN** est un réseau dédié au **stockage en mode bloc**, particulièrement adapté aux **environnements virtualisés** ou aux **serveurs nécessitant des performances élevées**.



### 📦 **Caractéristiques** :

- Forme un **réseau de stockage dédié** via des **commutateurs spécialisés** appelés "fabrique".
- Relie des **baies de stockage physiques** : disques durs, SSD ou hybrides.
- Permet aux serveurs de voir les volumes comme **des disques locaux**.



### 🔗 **Protocoles utilisés** :

- **Fibre Channel (FC)**
- **FC over Ethernet**
- **iSCSI (Internet Small Computer Systems Interface)**



### 📊 **Avantage principal** :

Accès **bas niveau** (mode bloc) aux disques, idéal pour les bases de données, serveurs virtualisés, etc.



## 🖧 **NAS (Network Attached Storage)**

Le **NAS** est un **système autonome** de stockage orienté **partage de fichiers** sur le réseau IP de l'entreprise.



### 🧰 **Composants intégrés** :

- Carte réseau, processeur, RAM, disques
- Fonctionne comme un **serveur de fichiers dédié**



### 📡 **Protocoles pris en charge** :

- **CIFS/SMB** (Windows / Samba)
- **NFS** (Unix/Linux)
- **AFP** (Apple)
- **FTP**
- Parfois aussi **iSCSI** dans certaines distributions Linux



## 🗃️ **Fonctionnement** :

- Fournit des **partages de fichiers** accessibles via le réseau.
- Peut jouer le rôle de **plusieurs serveurs de fichiers**.

