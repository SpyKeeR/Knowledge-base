# Les coûts

Depuis le rachat de VMware par **Broadcom**, la tarification et les licences ont **radicalement changé**.

Les coûts sont désormais liés à :

🔢 **Nombre de cœurs de CPU** (et non plus de sockets).

🏢 **Souscription annuelle obligatoire**, plus de licences perpétuelles.

🔐 **Accès uniquement via abonnements Broadcom Advantage** (plus de vente au détail en ligne).



## **🧪 VMware ESXi Free — de retour depuis avril 2025 !**

💥 **Gros revirement de Broadcom** : après avoir supprimé la version gratuite de l’hyperviseur **ESXi** en 2024, la société a **discrètement réintroduit une version gratuite** avec la sortie de **vSphere 8.0 Update 3e**.

📜 Elle est mentionnée dans la documentation comme un **hyperviseur "entry-level"**, et **téléchargeable gratuitement** via le **portail Broadcom Support**.



### **✅ Fonctionnalités et limites :**

| **Caractéristique** | **Détail** |
|----|----|
| 🖥️ Version | ESXi 8.0 Update 3e (et plus) |
| 💵 Licence | Gratuite, sans expiration |
| 📥 Accès | Téléchargement via le portail Broadcom (compte requis) |
| 🧠 Fonctionnalités principales | Hyperviseur bare-metal complet (pas une version allégée) |
| 🧩 Gestion VM | Pas de vCenter, uniquement en standalone |
| 🚫 Limitation notable | **8 vCPUs maximum par machine virtuelle** |
| ⚙️ Autres restrictions possibles | API limitées, pas de vMotion, HA, DRS, etc. |

⚠️ Si tu attribues **plus de 8 vCPUs à une VM**, l’interface te le permet, **mais la VM ne pourra pas démarrer**.

## **📦 Nouvelles offres vSphere (post-Broadcom)**

Broadcom propose désormais des **bundles** regroupant ESXi + vCenter + autres services.

### **🔐 vSphere Foundation (VSF)**

💼 Version d’entrée de gamme, obligatoirement sous abonnement.

Inclut :

- VMware ESXi (pour plusieurs hôtes)
- VMware vCenter
- Services de base (DRS, HA, vMotion...)
- Mise à jour et support inclus

💰 **Tarif opaque** : uniquement accessible via distributeurs agréés.

⚠️ **Minimum requis** : 16 cœurs CPU.

💳 Licence annuelle obligatoire.

### **🏢 vSphere+ (vSphere Plus)**

🌐 Accès cloud et fonctionnalités avancées.

Inclut :

- ESXi + vCenter
- Accès à **VMware Cloud Console**
- Intégration Tanzu Kubernetes
- Gestion centralisée cloud/on-premise

🎯 Ciblé entreprises de taille intermédiaire à grande.

💡 Disponible uniquement **via abonnement annuel**.

⚠️ Peut inclure des frais suppl. en fonction du niveau de support choisi.


