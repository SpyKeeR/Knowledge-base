# Machine virtuelles

## **🧩 Définition**

- Les **machines virtuelles** sont des **instances indépendantes** créées à l’aide d’un **hyperviseur**.
- Elles permettent de **simuler un environnement informatique complet** sans besoin de matériel physique supplémentaire.
- Elles sont **hébergées sur la machine hôte** et dépendent des **ressources matérielles** que l’hyperviseur leur alloue.



## **⚙️ Fonctionnement général**

- Chaque VM repose sur un **ensemble de fichiers** définissant :
  - Sa **plateforme matérielle virtuelle**
  - Son **système d’exploitation invité**
  - Ses **composants techniques** (RAM, CPU, disques, réseau)



## **📁 Fichiers associés à une VM**

- 📄 **Fichier de configuration** : définit les caractéristiques matérielles de la VM (CPU, RAM, réseau…)
- 💽 **Fichier d’image disque** (par disque dur virtuel) : contient les données de la VM.
- 🧠 **Fichier VRAM** : représente la mémoire vive utilisée (temporaire, supprimé à l’arrêt de la VM)



## **🧠 Ressources utilisées**

- 🧮 **CPU** : les cœurs processeur sont **réservés et alloués** par l’hyperviseur à chaque VM.
- 🧠 **RAM** : mémoire vive attribuée selon la configuration (via SLAT si disponible).
- 🌐 **Réseau** :
  - Chaque VM peut avoir **plusieurs cartes réseau virtuelles**
  - Plusieurs **modes de connexion** sont possibles (ex : **Bridge** = accès direct au réseau physique)

