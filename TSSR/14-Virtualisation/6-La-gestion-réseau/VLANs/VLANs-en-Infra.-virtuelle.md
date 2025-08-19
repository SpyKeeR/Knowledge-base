# VLANs en Infra. virtuelle**🧠 1Au niveau du vSwitch**

- Tout le trafic du vSwitch est **tagué avec un seul VLAN**
- Le vSwitch devient alors dédié à ce VLAN unique



**🧃 Au niveau du port group**

- Seules les VMs reliées à ce port group utilisent le VLAN attribué
- 🛠️ Permet de faire cohabiter plusieurs VLANs sur un même vSwitch



**💻 Au niveau des vNIC (VM)**

- La carte réseau virtuelle de la VM peut être configurée pour **gérer elle-même le tagging 802.1Q**
- ⚠️ Requiert une **configuration du système d’exploitation invité**



**🛠️ Configuration des liaisons montantes (uplinks)**

Pour que les VLANs fonctionnent correctement entre l’environnement virtuel et le réseau physique :

- La **VMNIC** (carte réseau physique) doit **accepter le trafic des VLANs utilisés**
- Cela implique une configuration en **mode trunk** sur le switch physique

**🔗 Mode Trunk : Obligatoire dans certains cas**

Lorsque :

- Les **VLANs sont gérés par l’hyperviseur**
- Plusieurs VLANs doivent circuler sur une même liaison physique

Alors :

- Le **vSwitch doit être en mode trunk**
- Le lien entre le **commutateur physique et le vSwitch** doit aussi être un **trunk**



**🧠 Résumé Visuel des Affectations VLAN**

| **🔧 Élément** | **🎯 Utilisation du VLAN** | **🛑 Remarques techniques** |
|----|----|----|
| vSwitch | Un VLAN unique pour tout le trafic | ➕ Simplicité – ➖ Manque de flexibilité |
| Port Group | VLAN dédié à un groupe de ports | ➕ Flexible – Permet plusieurs VLANs sur un switch |
| vNIC (VM) | VLAN géré au niveau de la VM | ⚠️ Le système d'exploitation doit gérer 802.1Q |
