# Fonctionnalités**

## 🧱 Pré-requis pour les fonctionnalités avancées**

- Minimum **3 hyperviseurs ESXi** de **même version** et aux **capacités identiques**.
- Un environnement homogène permet un **fonctionnement optimal** des fonctions avancées.



## **🧠 Rôle du vCenter**

- Le **vCenter** est **central** : il ne dépend pas d’un hôte et peut **orchestrer les actions** à l’échelle du cluster.
- Il permet :
  - La **consolidation de l'infrastructure**.
  - Le **déplacement de machines virtuelles** sans interruption de service.
  - La **gestion des défaillances** d'hyperviseurs.



## **🔄 vMotion**

- Fonctionnalité permettant le **déplacement d'une VM d’un hôte ESXi vers un autre**.
- 🧊 Fonction dite **à froid**, nécessite une configuration spécifique.
- Le déplacement est **manuel** (intervention de l’administrateur).
- **Pas d'arrêt de la VM** pendant l’opération.

## **💾 Storage vMotion**

- Permet de **déplacer les fichiers de stockage d’une VM** d’un datastore vers un autre.
- Requiert un **stockage externe** non lié directement à un hyperviseur.
- Rend le **stockage indépendant** de l’hôte, assurant une **tolérance aux pannes** matérielles.
- La VM continue de fonctionner même si l’hyperviseur tombe.

## **🤖 Fonctionnalités avancées supplémentaires (non détaillées ici)**

| **Fonction** | **Description** |
|----|----|
| 🔁 **DRS** (Distributed Resource Scheduler) | Déplacement **automatisé** des VM vers les hôtes avec plus de ressources. |
| 💽 **Storage DRS** | Déplacement **automatisé** des VM vers les **datastores** les moins sollicités. |
| 🌙 **DPM** (Distributed Power Management) | Met en **veille** ou réactive des hyperviseurs en fonction de la charge globale. |
| 💥 **HA** (High Availability) | Redémarrage automatique des VM sur un autre hôte en cas de **panne**. |
| 🛡️ **FT** (Fault Tolerance) | Protection sans **aucune interruption** pour certaines VM en cas de défaillance hôte. |

