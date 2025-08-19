# VCSA / vCenterLe **VCSA** (vCenter Server Appliance) est une **VM Linux prête à l’emploi**, qui embarque :

- vCenter Server (gestion d’infra vSphere)
- PostgreSQL intégré (base de données embarquée)
- Services vCenter (SSO, PSC, etc.)
- Serveur Web HTML5 intégré (interface client)
- Interface d’administration système (VAMI)

➡️ **Installé via un ISO dédié**, avec un assistant d’installation (Windows/Linux/macOS).

**🌐 Accès au VCSA – Ports et rôles**

**🔹 Port 5480 : VAMI (VMware Appliance Management Interface)**

Interface d’administration **système** du VCSA (pas pour gérer l’infra vSphere).

- Accès :
- Sert à :
  - Configurer IP / DNS / NTP
  - Gérer les MAJ de l’appliance
  - Sauvegarde/restauration
  - Redémarrer services internes
  - Voir la charge CPU, RAM, espace disque
- ⚠️ Ne pas confondre avec la console de gestion vSphere

➡️ À voir comme le **"BIOS/DRAC du vCenter"**, pas comme l’interface d’admin des VM ou hôtes.



**🔹 Port 443 : vSphere Client HTML5 (interface vCenter)**

Interface Web pour **gérer toute l’infrastructure vSphere**, via vCenter.

- Accès :
- Tu peux y :
  - Gérer clusters, ESXi, réseaux, stockages, VM
  - Gérer snapshots, templates, HA, DRS, etc.
- Depuis vSphere 6.5 :
  - Entièrement HTML5
  - Interface Flash obsolète (port 9443 supprimé)



**📊 Capacités réelles du VCSA**

| **Version VCSA** | **Hôtes max** | **VMs max (global)**          |
|------------------|---------------|-------------------------------|
| VCSA 6.7         | ~2 000        | ~25 000                       |
| VCSA 7.x / 8.x   | 2 000         | 35 000 VMs (avec Linked Mode) |

**🎯 VCSA vs vCenter Server sur Windows**

| **Version vSphere** | **vCenter Windows** | **VCSA (appliance Linux)**      |
|---------------------|---------------------|---------------------------------|
| 5.x                 | ✅ Oui              | ✅ Stable mais limité           |
| 6.0                 | ✅ Oui              | ✅ Recommandé (parité atteinte) |
| 6.5                 | ✅ Oui              | ✅ Migré en priorité            |
| 7.x                 | ❌ Supprimé         | ✅ Seule option                 |
| 8.x                 | ❌ Supprimé         | ✅ Seule option                 |

📌 **Aujourd’hui (vSphere 7 et 8)**, le **vCenter Server ne peut être déployé que sous forme de VCSA**, une **VM appliance basée sur Photon OS**, le Linux interne de VMware.

- Il n'existe **plus** de version installable sur **Windows Server**.
- Le cours qui affirme ça est **obsolète ou erroné**.
