# Interaction avec l'hôte

## **🎯 Importance de l’interaction**

Comprendre le lien entre l’hôte physique et les machines virtuelles est **essentiel pour une expérience fluide**. L’interaction couvre :

- Le **contrôle des périphériques** (clavier, souris, etc.)
- Le **partage de ressources** (réseau, dossiers, USB...)
- La **gestion des combinaisons de touches**

## **🖱️ Gestion des périphériques**

- Par défaut, **la VM capture automatiquement le clavier et la souris**.
- Pour **reprendre le contrôle de l’hôte**, utiliser Ctrl + Alt (modulable dans les préférences).
- Pour envoyer Ctrl + Alt + Suppr dans la VM ➡️ utiliser Ctrl + Alt + Inser.
- Possibilité de connecter :
  - 📀 Lecteurs CD/DVD
  - 💾 Périphériques USB
  - 🧮 Lecteurs de disquettes (émulés)

## **📂 Partage de dossiers**

- Fonction **Shared Folder** :
  - Permet de **mapper un dossier de l’hôte** dans la VM comme un **lecteur réseau**.
  - Pratique pour échanger facilement des fichiers de config ou ressources entre les deux environnements.



## **🌐 Interaction réseau**

VMware Workstation crée des **interfaces réseau virtuelles** pour simuler plusieurs modes de communication :

**🛜 Modes de connexion disponibles**

| **Mode réseau** | **Interface** | **Description** |
|----|----|----|
| **Host-only** | VMnet1 | Accès limité à l’hôte et aux autres VMs en Host-only |
| **NAT** | VMnet8 | Accès à Internet via l’hôte (adresse IP privée locale) |

## **🧰 Services associés**

- **VMware NAT Service** : Fournit l'accès Internet en mode NAT.
- **VMware DHCP Service** : Gère l’adressage IP automatique dans les réseaux virtuels.

⚠️ Si un **serveur DHCP tiers** est déjà en place, **désactiver le DHCP de VMware** pour éviter les conflits d’adressage.

