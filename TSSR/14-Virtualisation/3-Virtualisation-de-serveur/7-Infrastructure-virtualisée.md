# Infrastructure virtualisée

## **🖥️ Choix de la machine hôte**

- Le **serveur physique** (ou "machine hôte") doit être **dimensionné** selon :
  - Le **nombre de machines virtuelles** à héberger
  - Les **performances attendues** (CPU, RAM, stockage)

Il est recommandé d’opter pour un **serveur modulable ou haut de gamme** si la charge est importante.



## **⚙️ Installation de la solution de virtualisation**

- L’**hyperviseur** s’installe sur la machine hôte via un **logiciel propriétaire** (VMware ESXi, Hyper-V…).
- Une fois l’hyperviseur en place :
  - Déploiement et gestion des VM via une **console dédiée** : **VMM (Virtual Machine Manager)**.



## **👤 Gestion autonome des machines virtuelles**

- Dans une gestion autonome :
  - Les VM sont **liées à un seul hyperviseur**, installé sur **une seule machine physique**.
  - Chaque hyperviseur est **indépendant**, sans lien avec d’autres hôtes.



## **🌐 Gestion globale des hyperviseurs**

- Pour centraliser la gestion :
  - Nécessité d’un **composant de gestion globale** :
    - **vCenter** pour **vSphere** (VMware)
    - **Contrôleur de domaine Active Directory** pour **Microsoft Hyper-V**
- Ce composant peut être hébergé :
  - Sur une **VM** au sein d’un hyperviseur
  - Ou sur une **machine autonome**

## **🔗 Interconnexion des hyperviseurs**

- Une fois la solution de gestion globale en place :
  - Tous les hyperviseurs sont **interconnectés**
  - La gestion des VM se fait de manière **centralisée**, selon les **ressources disponibles** sur chaque hôte
  - Ex : **vCenter** répartit dynamiquement les VM dans un cluster VMware



## **🌍 Interfaces de gestion**

- **VMware (vSphere)** :
  - Interface **web** pour la gestion des VM et des hôtes

- **Microsoft (Hyper-V)** :
  - Interface **graphique** via **MMC Hyper-V**
  - Ou gestion **en ligne de commande** via **PowerShell**



## **🧱 Microsoft : Prérequis pour la gestion globale**

- Un **contrôleur de domaine Active Directory** est nécessaire
- Chaque **hyperviseur Hyper-V** (Windows Server) doit être **intégré au domaine**
- Cela permet une **administration centralisée** des hôtes et des VM



## **🚀 Fonctionnalités avancées disponibles**

- Une fois la **gestion globale** mise en place :
  - Accès aux **fonctionnalités avancées** :
    - **Migration à chaud**
    - **Répartition automatique des charges**
    - **Failover / Haute disponibilité**
    - **Surveillance des performances**
    - **Automatisation des déploiements**


