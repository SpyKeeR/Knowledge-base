# Terminologie / Concepts

Le **stockage** est un **élément critique** d'une infrastructure virtualisée.

📉 Un mauvais dimensionnement ou un manque de performance impacte directement le bon fonctionnement des machines virtuelles.



## **🧩 Terminologie & Concepts clés**

## **🔷 Mode Bloc**

- 📦 Les **données** sont découpées en **blocs** identifiés de manière unique.
- 🔍 Ce mode permet un **accès plus rapide** aux données que le mode fichier.
- 📡 Utilisé via des **protocoles de stockage bas niveau** comme **SCSI**.
- 🔗 Exemple d’usage : **baies de stockage SAN**.

### **🛠️ Protocole SCSI (exploité par le mode bloc)**

- 🔁 Transmet des **blocs de données** et des **commandes spécifiques** (lecture, écriture)
- 🧠 Pris en charge par les **pilotes** et **contrôleurs SCSI**
- 🔄 Permet des communications très performantes entre hôtes et périphériques


### **🛜 SAN – Storage Area Network**

- 🧠 Réseau dédié **au transport de blocs de données** entre hôtes et périphériques de stockage.
- 📶 Fonctionne avec des **protocoles basés sur le bloc** (SCSI, iSCSI, Fibre Channel...).
- 🧩 Permet aux serveurs d’accéder à un stockage **comme s’il était local**.
- 💡 Indépendant de l’hyperviseur et très performant.



### **🌐 NAS – Network Attached Storage**

- 💽 Périphérique de stockage **autonome** relié au réseau.
- 📁 Fonctionne en **mode fichier** (protocoles **SMB**, **NFS**...).
- 🌍 Accessible à travers un **réseau standard (LAN ou WAN)**.
- 🔐 Idéal pour **le partage de fichiers sécurisé**, même à distance.



### **💻 DAS – Direct Attached Storage**

- ⚙️ Stockage **local directement relié** à l’hôte.
- 🔌 Accès lié à :
  - La **connectique** (SAS, SATA…)
  - Le **protocole** utilisé
  - Le **mode d’accès** aux données
- ⚠️ Risque élevé en cas de **panne** → pas de redondance ni de partage



## **🧭 Choisir la bonne solution**

| **💡 Solution** | **⚙️ Mode** | **🔄 Accès** | **🔐 Sécurité** | **🌐 Réseau** |
|----|----|----|----|----|
| **DAS** | Local / Bloc | Direct | ❌ Faible | Aucun |
| **NAS** | Réseau / Fichier | Par partages | ✅ Élevée | LAN / WAN |
| **SAN** | Réseau / Bloc | Comme un disque local | ✅ Élevée | Réseau dédié |

### **📚 Interaction entre services et données**

- 🧠 Les **services** fournis par l’infrastructure **reposent sur les données**.
- 🔁 Chaque service génère des **requêtes** vers le stockage pour accéder à ses données.
- 💾 Les **requêtes de stockage** (lecture, écriture) sont traitées en continu par l’hôte via :
  - 🎛️ Le **pilote disque**
  - 🔄 Le **protocole SCSI**
  - 🔌 Le **contrôleur de stockage**
