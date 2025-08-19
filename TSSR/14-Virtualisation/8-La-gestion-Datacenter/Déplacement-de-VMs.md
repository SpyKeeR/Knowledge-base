# Déplacement de VMs**🧩 Ressources nécessaires au fonctionnement d’une VM**

Une machine virtuelle (VM) utilise plusieurs types de **ressources** pour fonctionner :

- **CPU** (processeur)
- **RAM** (mémoire vive)
- **Réseau**
- **Stockage** (disques)

🔸 Les **CPU, RAM et réseau** sont directement fournis par l’hyperviseur hôte.

🔸 Le **stockage** peut être **local** à l’hôte ou **externe**, via un SAN ou autre solution partagée.



**🔄 Déplacement des VM : principes généraux**

Le déplacement d’une VM peut se faire **à chaud** (sans interruption de service) si certaines conditions sont respectées :

- La **VM ne doit pas dépendre** du stockage local d’un hôte.
- Le **stockage** doit être **accessible à plusieurs hyperviseurs** (partagé ou via réseau).
- Le **réseau** utilisé par la VM doit être géré par une **infrastructure centralisée** comme le **vDS (Virtual Distributed Switch)**.
- L’hyperviseur de destination doit disposer de **ressources disponibles** (CPU, RAM).
- La fonctionnalité **vMotion** doit être activée et opérationnelle.



**⚙️ Fonctionnalités de migration**

Deux fonctionnalités principales sont utilisées pour la migration des VM :

**🔵 vMotion**

Permet le **déplacement à chaud** d’une machine virtuelle **d’un hyperviseur vers un autre**, sans arrêt ni interruption.

Le transfert inclut :

- Les **états de la VM** (RAM, CPU)
- Les **fichiers de configuration**
- Les connexions réseau en temps réel

👉 Ce transfert utilise un **réseau dédié vMotion** dont les performances doivent être élevées pour réduire le temps de migration.

**🟡 Storage vMotion**

Permet le **déplacement à chaud** du **stockage** d’une VM **d’un datastore vers un autre**, sans interruption.

➡️ Seul le **disque** est déplacé, sans toucher à l’emplacement d’exécution de la VM.



**🔁 Migration combinée (vMotion + Storage vMotion)**

Il est possible de combiner les deux migrations :

- Déplacement **simultané** du **stockage** (datastore) et du **fichier de configuration** (hyperviseur).
- Permet de **changer complètement d’hôte et de datastore**, toujours **sans arrêt de la VM**.

**✅ Pré-requis pour un déplacement réussi**

- 📦 **Stockage indépendant** et partagé entre hôtes
- 💪 **Ressources disponibles** sur l’hyperviseur cible (CPU/RAM)
- 🌐 **Accès réseau non dépendant** d’un hôte grâce au vDS
- 🔧 **Fonctionnalités activées** : vMotion et Storage vMotion
- 🚀 **Réseau performant** pour le transfert des données de configuration
