# VMDK/vRDM/Thick/Thin

## 🗂️ **Les deux formats de disques principaux**

### **VMDK (Virtual Machine Disk)**
  - Format **propriétaire VMware**
  - Représenté par un **fichier dans le datastore**
  - ✅ Facile à copier, sauvegarder, déplacer, exporter
  - 🔺 Ancienne limite à **2 To** (supprimée depuis VMFS5 → maintenant **64 To**)
  - 🔁 Convient pour la **majorité des usages classiques**



### **RDM (Raw Device Mapping)**
  - Accès **direct à un périphérique de stockage physique** (ex : LUN d’un SAN)
  - ➕ Bypass les limites de taille du VMDK
  - 🔄 Utilisé dans des cas spécifiques :
    - 🧰 Stockage avancé (SAN)
    - 🧷 Cluster Microsoft
  - ⚖️ Performances proches du VMDK avec les versions récentes de vSphere



## 🧰 **Mécanismes de provisionnement des disques VMDK**

Deux types à connaître, à choisir **au moment de la création** (⚠️ non modifiable ensuite) :

### **🧱 Thick Provisioning (provisionnement fixe)**

- Réservation immédiate **de tout l’espace disque annoncé**
- Ex : un VMDK de 100 Go prendra **100 Go** tout de suite
- ✅ Performances constantes, **pas de fragmentation**
- ❌ Temps de création plus long
- ❌ Espace utilisé, même si vide



### **🌱 Thin Provisioning (provisionnement dynamique)**

- Allocation de l’espace **au fur et à mesure de l’usage réel**
- ✅ Création instantanée
- ✅ Espace non utilisé disponible pour d'autres VM
- ❌ Risque de **sur-allocation** du datastore si mal géré
- ❌ Performances légèrement moindres selon le contexte

