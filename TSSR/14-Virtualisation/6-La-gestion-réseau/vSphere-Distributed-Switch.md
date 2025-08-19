# vSphere Distributed Switch**🧠 Définition du VDS**

- Le **vSphere Distributed Switch (VDS)** est une **infrastructure réseau virtuelle centralisée** partagée entre plusieurs hôtes ESXi.
- Contrairement au **vSphere Standard Switch (VSS)**, le VDS **n'est pas lié à un hyperviseur unique**, mais géré **depuis le vCenter**.
- Il permet une **gestion unifiée** du réseau virtuel pour un ensemble d'hôtes ESXi.



**🔄 VSS vs VDS**

| **🧱 VSS (Standard)**           | **☁️ VDS (Distribué)**             |
|---------------------------------|------------------------------------|
| Géré localement sur chaque ESXi | Géré globalement depuis le vCenter |
| Dépendant d’un hôte             | Indépendant des hôtes              |
| Moins adapté aux migrations     | Parfait pour la mobilité des VMs   |
| Simplicité                      | Fonctionnalités avancées           |



**🚀 Fonctionnalités du VDS**

- 🔗 **Connexion persistante** : assure la **continuité réseau** lors de la **migration des machines virtuelles** entre hôtes.
- 🌐 **Infrastructure partagée** : un **même VDS peut être utilisé par plusieurs hôtes ESXi**.
- 🛠️ **Configuration centralisée** : les **groupements de ports**, les **politiques réseau** et les **paramètres de sécurité** sont appliqués **depuis le vCenter**.
- 📡 **Tolérance aux pannes** : les cartes réseau physiques associées assurent la **résilience réseau**.



**🧩 Mise en œuvre du VDS**

- Lors de la **création du VDS**, on choisit :
  - Les **hôtes** ESXi concernés
  - Les **interfaces réseau physiques** à associer
- Le VDS **n'existe physiquement sur aucun ESXi** mais reste **activé et opérationnel** à travers toute l’infrastructure via le vCenter.



**🛑 Prérequis**

- 💼 **Licence Enterprise Plus obligatoire**
- 👥 Recommandé pour des **environnements avec plusieurs hôtes** pour une gestion efficace.
