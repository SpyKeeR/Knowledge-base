# Groupements de ports

## **🔌 Définition**

Les **groupements de ports** (ou *Port Groups*) dans un vSwitch standard sont l’équivalent des ports d’un commutateur physique.

Ils servent à **définir le type d’utilisation réseau** d’un vSwitch sur un hyperviseur ESXi.

Un **vSwitch standard** peut héberger **deux types de groupements de ports** :



## **🖧 Groupement de ports de type VM Network**

🔹 Destiné **aux machines virtuelles**

🔹 Permet la **communication entre VM** sur le même port group

🔹 Permet également aux VM de bénéficier :

- Des **liaisons montantes** (NIC physiques de l’ESXi)
- Des **services réseau externes**

🛑 **Isolation native** :

- Les machines virtuelles **connectées à des groupements de ports différents** (même sur le même vSwitch) **ne peuvent pas communiquer** entre elles.
- Cela permet une **segmentation logique** des réseaux dans un vSwitch.

**🏷️ Segmentation via VLAN Tagging**

🔸 Chaque **groupement de port peut être associé à un VLAN ID**

🔸 Cela permet :

- Une **segmentation réseau** fine
- L’appartenance explicite de chaque machine à un **réseau VLAN isolé**

📌 Une machine virtuelle connectée à un port group avec un **VLAN tag** ne verra que le trafic de ce VLAN.



## **⚙️ Groupement de ports de type VMkernel**

🔹 Réservé **aux services internes de l’hyperviseur ESXi**

🔹 Non utilisable par des machines virtuelles

💡 Utilisé pour héberger des services comme :

- 🖥️ Connexion au **vCenter**
- 🔁 **vMotion**
- 🗂️ **NFS / iSCSI**
- 📡 **Fault Tolerance**
- 🔍 Surveillance et gestion à distance

🔸 Chaque port group de type VMkernel :

- A une **adresse IP propre**
- Est lié à un **service spécifique**

## **🧠 Synthèse**

✅ Un **vSwitch standard** peut héberger :

- Plusieurs **port groups de type VM Network**
- Un ou plusieurs **port groups de type VMkernel**

✅ Il peut être :

- **Connecté à une ou plusieurs NIC physiques**
- **Utilisé pour isoler des flux réseau** entre machines virtuelles

