# Les composants/outils

## **🧠 Élément central : l’hyperviseur**

L’hyperviseur est le cœur de toute solution de virtualisation.

Il permet l'exécution simultanée de plusieurs machines virtuelles en isolant leurs ressources.



## **🖥️ Console de gestion des machines virtuelles (VMM)**

- Permet la **création, modification et suppression** de machines virtuelles.
- Disponible en **interface graphique** (GUI) ou **ligne de commande** (CLI).
- Gère les aspects de **démarrage**, d’**arrêt**, de **sauvegarde**, ou de **snapshot** des VM.



## **🌐 Réseaux virtuels**

Utilisés pour définir la **connectivité** des VM :

- 🔒 **Réseau interne** (communication uniquement entre VM)
- 🌍 **Mode pont (Bridge)** (accès réseau direct comme un PC physique)
- 🌐 **Mode NAT** (accès internet via l’hôte)
- 🛜 **LAN dédié** ou privé



## **💾 Formats de disques virtuels**

Selon la solution utilisée, les VM utilisent des formats spécifiques :

- 📦 **VHD / VHDX** → Hyper-V
- 📦 **VMDK** → VMware
- 📦 **VDI** → VirtualBox

Certains outils permettent de :

- 📈 **Étendre** ou 📉 **réduire** la taille de ces fichiers.
- 🔁 **Convertir** entre formats selon les besoins.



## **📂 Fichiers de configuration des VM**

Chaque solution a son propre fichier de description de VM :

- 🗂️ **XML** → Hyper-V
- 🗂️ **VMX** → VMware
- 🗂️ **VBOX** → VirtualBox

Ces fichiers contiennent toutes les **informations de configuration matérielle** de la machine virtuelle.

## **🧳 Consoles d'accès aux machines virtuelles**

- Interface graphique dédiée à **chaque machine virtuelle**.
- Peut se présenter sous :
  - 💡 **Console unique** (ex. : Hyper-V).
  - 🧩 **Interface à onglets** (ex. : VMware Workstation).


## **🧩 Composants complémentaires**

Permettent d’ajuster le **paramétrage matériel** d'une VM :

- 🧬 **Architecture**, **BIOS/UEFI**, **type de processeur**
- 🧠 **Mémoire vive (RAM)**
- 💽 **Disques virtuels**
- 📀 **Lecteurs DVD** (montage ISO)
- 🔌 **Périphériques USB**
- 💾 **Disquettes** au format FLP ou IMG (optionnel)

