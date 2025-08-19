# vMotion / Storage vMotion**🔵 vMotion** Permet de **déplacer une machine virtuelle en cours d'exécution** d’un **hôte physique vers un autre** sans interruption de service.

➡️ Aucune coupure pour l’utilisateur final.

**🟡 Storage vMotion** Permet de **déplacer le stockage d'une VM** (datastore source → datastore cible), **sans arrêter la machine virtuelle**.

➡️ Peut être combiné avec vMotion pour une migration complète à chaud.



**🛠️ Pré-requis techniques pour vMotion**

- ✅ La **fonctionnalité vMotion** doit être **activée** sur au moins **un vSwitch** sur l’hôte **source** et **destination**.
- 🌐 Le **réseau vMotion** doit disposer :
  - D’un **groupement de ports vMkernel**
  - D’un **vSwitch** connecté à une **interface réseau physique**
  - D’un **adressage IP dédié** (même domaine de broadcast)
- 📦 Le **stockage** de la VM (datastore) doit être **accessible simultanément** depuis les **deux hôtes** (via SAN ou NAS partagés).



**🌐 Configuration réseau pour vMotion**

- Utiliser un **réseau dédié** au vMotion (IP privée) pour **optimiser les performances**.
- Prévoir un **commutateur réseau (switch)** de **grande capacité** pour **accélérer les transferts** de données pendant la migration.



**🗂️ Stockage des VM**

- Les **datastores** doivent être **disponibles et montés** sur **tous les hôtes** du cluster.
- Le **stockage partagé** est obligatoire pour vMotion, sauf dans le cas spécifique de **Storage vMotion**.



**⚠️ Précautions avant migration**

- ❌ Supprimer les **périphériques virtuels** non essentiels (images ISO, lecteurs DVD, disquettes, etc.).
- ❌ Vérifier que la **VM n’utilise pas** un **port group non disponible** sur l’hôte de destination.
- 🧠 Tenir compte des **différences de processeurs** :
  - Si les **CPUs** sont de **fabricants différents**, la **compatibilité peut impacter les performances**.
  - vSphere adapte dynamiquement la topologie CPU mais peut provoquer des écarts de performance.

**📋 Procédure de déplacement**

1.  Dans l’interface de gestion du DC, **sélectionner l’action de migration** sur la VM.
2.  L’assistant propose :

    - Le déplacement de la VM **vers un autre hôte** (vMotion).
    - Le déplacement du **stockage** vers un **autre datastore** (Storage vMotion).

**🚫 Problématiques fréquentes lors des migrations**

- 📀 Présence d’un **fichier ISO** monté dans la VM.
- ❌ Le **port group** de connexion réseau **n’existe pas** sur l’hôte cible.
- 🔁 **Incompatibilité CPU** entre hôtes (instructions non supportées identiques).
