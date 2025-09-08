# 📦 **Les Datastores dans vSphere**

Les datastores sont des objets indispensables pour la gestion du stockage dans un environnement virtualisé. Ils permettent à vSphere d’héberger les machines virtuelles sur des espaces disques accessibles via les adaptateurs de stockage.



## 📊 **Typologie des besoins en stockage**

Avant de créer des datastores, il est important de recenser les besoins selon trois critères principaux :

| **Type de besoin** | **Performances** | **Tolérance aux pannes** | **Criticité** |
|--------------------|------------------|--------------------------|---------------|
| VM_PROD            | 🔥 3             | 🔁 3                     | 🚨 3          |
| VM_TESTS           | ⚡️ 2             | 🔄 2                     | ⚠️ 2          |
| MODELES            | 🧩 1             | 💾 2                     | 📁 2          |
| ISO                | 🧊 1             | ⬇️ 1                     | 📦 1          |



## 🗃️ **Types de Datastores**

Trois types principaux de banques de données sont utilisés :



### **1️⃣ VMFS (VMware File System)**

- 📏 Capacités jusqu’à **64 To**
- 📂 Format de partition : **GPT**
- 🔢 Taille des blocs : **1 Mo**
- ⚡️ Haute performance et accès simultané en lecture/écriture
- 💽 Généralement basé sur des **solutions SAN**



### **2️⃣ RDM (Raw Device Mapping)**

- 🧲 Accès direct au **périphérique physique distant**
- 🔄 Permet de contourner les datastores classiques
- 🎯 Idéal pour des **cas d’utilisation spécifiques** (bases de données, clusters…)



### **3️⃣ NFS (Network File System)**

- 🌐 Protocole de partage **Open Source**, courant sous Unix/Linux
- 🐢 Moins performant que iSCSI
- 📁 Utilisé pour les **ISO, modèles OVF** ou données peu critiques
- 📶 Fonctionne en mode fichier via un réseau

