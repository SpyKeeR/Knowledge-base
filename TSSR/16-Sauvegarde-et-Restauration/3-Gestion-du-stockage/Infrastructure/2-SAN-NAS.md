## 💾 **SAN – Storage Area Network**

- 🖧 Réseau dédié à la gestion des dispositifs de stockage
- 📁 Accès à des espaces de stockage centralisés, vus comme des disques locaux
- 🖥️ Le serveur gère le système de fichiers sur ces espaces
- ⚡ Réponse rapide et fiable aux besoins d’accès aux données

## 📡 **NAS – Network Attached Storage**

- 📶 Appareil de stockage connecté au réseau local (LAN)
- 💾 Permet de sauvegarder et partager des fichiers sur le réseau
- 🗂️ Possède son propre système de fichiers, souvent :  
   • **CIFS** (Common Internet File System) → monde Windows  
   • **NFS** (Network File System) → monde Unix/Linux

🧑‍💻 **Usage domestique**

- Le NAS est très courant chez les particuliers pour les sauvegardes personnelles



## ⚔️ **Comparaison SAN vs NAS**

| **Critère** | **SAN** | **NAS** |
|----|----|----|
| 📡 Protocoles | SCSI (iSCSI), Fibre Channel, SATA | Serveurs de fichiers : NFS, CIFS |
| 🔄 Partage de fichiers | Repose sur le système d’exploitation | Partage important multi-OS (Unix, NT) |
| 🆔 Identification | Par blocs de disque | Par nom de fichier + offset octet |
| 🎯 Dispositifs ciblés | Serveurs avec canal Fibre Channel SCSI | Périphériques réseau local (NFS, CIFS, SMB, HTTP(S)) |
| 💰 Coût et complexité | Plus cher, plus complexe à mettre en place | Rentable, quasi plug & play |
