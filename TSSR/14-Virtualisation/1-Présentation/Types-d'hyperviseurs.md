# Types d'hyperviseurs**🏗️ Définition générale**

L’**hyperviseur** est l’élément central d’une solution de virtualisation.

Il fait le lien entre les **ressources matérielles physiques** (CPU, RAM, stockage, etc.) et les **machines virtuelles (VM)**.

Il existe **deux grandes catégories** d’hyperviseurs :

- **Type 1** : natif ou "bare-metal"
- **Type 2** : hébergé ou "hosted"



**⚙️ Hyperviseur de type 1 (Bare-metal)**

- Fonctionne **directement sur le matériel** sans système d’exploitation intermédiaire.
- Intègre son **propre OS spécialisé** pour la gestion des VM.
- Communication via un **module de translation binaire** entre les VM et le matériel.
- Échanges **très rapides** (performances proches du natif).
- Très utilisé en **production** et **datacenters** pour les **serveurs virtualisés**.



📦 **Exemples :**

- Microsoft **Hyper-V (Autonome)**
- Linux **KVM**
- **VMware ESXi**
- **Citrix XenServer**



🔁 **Architecture simplifiée :** Matériel → Hyperviseur → Machines virtuelles (1 seul niveau intermédiaire)

**🖥️ Hyperviseur de type 2 (Hosted)**

- Nécessite un **système d’exploitation hôte** (Windows, Linux…).
- L'hyperviseur est une **application logicielle** installée sur ce système.
- Le système d’exploitation exploite le **matériel via des pilotes**, puis fournit les ressources aux VM.
- Moins performant que le type 1 (à cause d'une couche supplémentaire), mais **plus simple à mettre en œuvre**.



📦 **Exemples :**

- **Oracle VirtualBox**
- **Windows Virtual PC**
- **VMware Workstation / Player**
- 

🔁 **Architecture simplifiée :** Matériel → OS hôte → Hyperviseur → Machines virtuelles (2 niveaux intermédiaires)
