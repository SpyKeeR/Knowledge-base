# vSphere Standard Switch

## **🧠 Définition du VSS**

- Le **vSphere Standard Switch (VSS)** est le **composant central de gestion réseau** d’un hyperviseur **ESXi**.
- Il assure la **connexion des machines virtuelles au réseau physique** via les cartes réseau de l’hôte.
- Le VSS est **géré localement** sur chaque hôte ESXi (🖥️), indépendamment des autres.



## **🧩 Fonctionnement du VSS**

- Chaque VSS est associé à **un ou plusieurs groupements de ports**.
- Ces groupements sont l’équivalent **virtuel des ports d’un switch physique**.
- Chaque **machine virtuelle** se connecte à un **groupement de ports**, comme un PC branché à un port de switch.



## **🔧 Types de groupements de ports**

- **Groupement de ports standard** : utilisé pour **les interfaces réseau des VMs**.
- **Groupement de type VMkernel** : utilisé pour les **services de l’hyperviseur** comme :
  - L’interface web d’administration 🌐
  - Le vMotion 🔄
  - Le stockage réseau 📦



## **🖧 Connectivité physique**

- Un VSS est **lié à une ou plusieurs cartes réseau physiques** de l’hôte :
  - 🔁 **Redondance** : permet une **tolérance de panne**
  - 📶 Meilleure **disponibilité réseau** pour les VMs et les services ESXi



## **🛠️ Par défaut : vSwitch0**

- Créé automatiquement à l’installation d’ESXi
- Utilisable :
  - Pour la **gestion de l’ESXi**
  - Pour **les machines virtuelles**
- Connecté à la **carte réseau détectée à l’installation**

