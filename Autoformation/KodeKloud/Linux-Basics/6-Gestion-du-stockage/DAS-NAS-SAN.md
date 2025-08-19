# DAS/NAS/SAN**🧱 DAS — Direct Attached Storage**

**Définition** : Stockage directement connecté à une machine.

**Exemples** :

- Clé USB branchée sur ton PC
- Disque dur externe en USB, eSATA ou Thunderbolt
- Un disque SATA ou NVMe interne à ton ordi

**Caractéristiques** :

- **Aucune connexion réseau** : c’est un périphérique local.
- **Performant** car directement connecté
- **Pas partagé** : seul l’ordi auquel il est branché y a accès
- Très simple à mettre en œuvre

**Quand l’utiliser ?** Pour une machine individuelle, des sauvegardes locales rapides ou un serveur unique qui n’a pas besoin de partager son stockage.





**🌐 NAS — Network Attached Storage**

**Définition** : Stockage accessible via un **réseau (IP)**, présenté comme un **fichier partagé**.

**Exemples** :

- Un boîtier Synology ou QNAP chez toi ou en entreprise
- Un serveur Linux avec Samba ou NFS activé

**Caractéristiques** :

- Se connecte au réseau (RJ45 ou Wi-Fi)
- Utilise des **protocoles de partage de fichiers** : NFS, SMB/CIFS, FTP, etc.
- Accès multi-utilisateur : tout le monde sur le réseau peut y accéder
- Souvent équipé d’un système d’exploitation dédié (DSM, TrueNAS…)
- Gère les **droits d’accès**, les **RAID**, les **snapshots**, etc.

**Quand l’utiliser ?** Parfait pour les PME, les serveurs de fichiers, les sauvegardes centralisées, ou les utilisateurs à domicile qui veulent un "mini-serveur cloud" local.





**🧠 SAN — Storage Area Network**

**Définition** : Réseau de stockage haute performance, souvent utilisé dans les **data centers**.

**Caractéristiques** :

- Connecté via un **réseau dédié** (souvent en **Fibre Channel** ou **iSCSI** sur Ethernet)
- Présente les volumes de stockage comme **des disques bruts (block-level)**, pas des fichiers
- Très **performant**, **faible latence**, **haute disponibilité**
- Utilisé avec des serveurs qui gèrent eux-mêmes le système de fichiers
- Complexe à configurer, mais très puissant

**Quand l’utiliser ?** Environnements critiques : virtualisation (VMware, Hyper-V), bases de données, serveurs web à forte charge, etc.
