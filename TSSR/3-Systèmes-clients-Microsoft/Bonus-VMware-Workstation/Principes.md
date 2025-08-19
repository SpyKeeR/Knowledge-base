# Principes**🎯 Définition**

📌 La **virtualisation** permet d'exécuter **plusieurs systèmes d'exploitation** sur une même machine physique (*hôte*).

📌 Un logiciel spécial, l'**hyperviseur**, gère ces systèmes appelés **machines virtuelles (VM)**.



**⚙️ 1️⃣ Rôle de l’hôte et des VM**

✅ **Hôte** : La machine physique qui fournit les ressources (CPU, RAM, stockage, réseau).

✅ **Machines Virtuelles (VM)** :

- Systèmes isolés tournant sur l'hôte.
- Peuvent être sous Windows, Linux, etc.

&nbsp;

- Stockées sous forme de **fichiers** gérés par l’hyperviseur.

**🛠️ 2️⃣️Fonctionnement des Machines Virtuelles**

🔹 **L’hyperviseur** gère l’attribution des ressources aux VM.

🔹 **Les VM pensent accéder directement au matériel**, mais en réalité, **c'est l'hyperviseur qui fait l’intermédiaire**.

🔹 Avantages : ✅ **Optimisation des ressources** ✅ **Isolation des environnements** ✅ **Flexibilité pour tester différents OS**



**🔄 3️⃣️Types d’Hyperviseurs**

🔹 **Hyperviseur Type 1** (Bare Metal) : Installé directement sur le matériel. Ex : **VMware ESXi, Microsoft Hyper-V Server**.  
🔹 **Hyperviseur Type 2** (Hébergé) : S'exécute sous un OS existant. Ex : **VMware Workstation, VirtualBox**.



**💾 4️⃣️Ressources Nécessaires pour une VM**

✅ **Processeur (CPU)** : Partagé entre les VM.

✅ **Mémoire Vive (RAM)** : Définie à l’avance pour chaque VM.

✅ **Stockage (VMDK, VDI, QCOW2, etc.)**.

✅ **Carte Réseau** : Peut être en **NAT, Bridged, Host-Only**.

✅ **Autres composants** : BIOS/UEFI, carte graphique virtuelle, lecteur DVD.



**🔍 5️⃣️ Schéma de la Virtualisation**

**Matériel Hôte** → **Hyperviseur (Type 1 ou 2)** → **VMs (Windows, Linux, etc.)**

Chaque VM croit fonctionner seule, mais l’hyperviseur gère **l'accès aux ressources** et assure **l’isolement** entre elles.
