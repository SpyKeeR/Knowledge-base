# pNIC, vmnic, vNIC, vmkNIC**🧩 pNIC — *Physical Network Interface Card***

**🔹 Nom complet :** Physical NIC

**🔹 Alias dans ESXi :** vmnicX (ex: vmnic0, vmnic1)

**🔹 Rôle :**

C’est **la carte réseau physique** installée sur l’hyperviseur ESXi (serveur physique).

Elle sert à faire la **liaison entre le monde physique (réseau LAN, Internet, etc.) et l’environnement virtuel.**

👉 Un **vSwitch** (commutateur virtuel) peut être **connecté** à un ou plusieurs vmnic pour donner accès aux VM au réseau externe.

**🧩 vmnic — *Virtual Machine Network Interface Card (vue ESXi)***

⚠️ Ne pas confondre le nom **vmnic** (dans l'interface ESXi) qui **désigne une pNIC**, avec le terme générique "vNIC" qu'on verra juste après.

Dans ESXi, **vmnicX = pNIC**.

C’est donc juste le **nom logique attribué à une carte réseau physique** dans l’interface VMware.



**🧩 vNIC — *Virtual Network Interface Card***

**🔹 Nom complet :** Virtual NIC

**🔹 Alias dans vSphere/ESXi :** eth0, ens160, etc. dans la VM (dépend de l’OS invité)

**🔹 Dans l’interface vSphere :** Network Adapter 1, Network Adapter 2

**🔹 Rôle :**

C’est **la carte réseau virtuelle** de la machine virtuelle.

Elle est connectée à un **vSwitch** (commutateur virtuel) à travers un **port virtuel**.

🎯 Le vNIC donne l’accès réseau à la VM, comme si c’était une vraie carte réseau.

**🧩 vmkNIC — *VMkernel NIC***

**🔹 Alias dans ESXi :** vmk0, vmk1, etc.

**🔹 Rôle :**

C’est une **interface réseau logicielle spéciale** utilisée par l’hyperviseur lui-même.

Elle sert pour :

- Accès SSH à ESXi
- Gestion via vCenter
- vMotion
- NFS / iSCSI
- Fault Tolerance
- etc.

📌 Une vmkNIC est **reliée à un vSwitch**, tout comme les vNIC des VM.
