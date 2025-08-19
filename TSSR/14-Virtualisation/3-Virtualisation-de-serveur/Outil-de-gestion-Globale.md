# Outil de gestion - Globale**🧱 Notion de virtualisation de serveurs**
- Une **solution de virtualisation** repose sur un **hyperviseur** connecté à un **hôte physique**.
- Les **ressources matérielles** de l’hôte sont mises à disposition pour héberger des **machines virtuelles (VM)**.
- Dans une **gestion globale**, il est possible de **regrouper plusieurs hôtes physiques** pour mutualiser les ressources dans une solution unique.



**🧠 Rôle des hyperviseurs**

- 🔗 Les hyperviseurs peuvent être **autonomes** ou **regroupés dans un contexte centralisé** :
  - Microsoft : intégration dans un **domaine Active Directory** (AD).
  - VMware : ajout du **vCenter**, composant centralisant la gestion des hôtes **ESXi**.
- Ce regroupement permet une **répartition optimisée des ressources** entre les hôtes.



**⚡ Fonctionnalité de failover (basculement)**

- Le **failover** garantit la **continuité de service** :
  - En cas de panne ou perte d’un hyperviseur, ses VM sont **automatiquement transférées** vers un autre hyperviseur disponible.
- Cela offre une **tolérance accrue aux pannes** et une **meilleure résilience** des infrastructures virtualisées.

**🚀 Fonctionnalités avancées de vCenter**

- 🔄 **Déplacement automatique** des VM entre hyperviseurs selon :
  - L’état de la **mémoire RAM**
  - L’espace **disque disponible**
  - Les **performances système**
- 🔍 **Surveillance en temps réel** de l’infrastructure
- 🔁 **Transfert dynamique** des VM en cas de surcharge ou de défaillance d’un hôte



**🖥️ Interface de gestion centralisée**

- **vCenter** (VMware) : interface **web** d’administration globale
- **Hyper-V** (Microsoft) : gestion centralisée via **Active Directory**
- Ces interfaces permettent un **pilotage à grande échelle** des hyperviseurs et des VM dans un **datacenter virtualisé**.



**📌 Résumé**

✅ Les hyperviseurs peuvent être **centralisés** dans une solution de **gestion globale**, pour :

- 🌐 **Mutualiser** les ressources de plusieurs hôtes
- 🛡️ **Garantir la haute disponibilité** via le **failover**
- 🧑‍💻 **Simplifier l’administration** via des outils comme **vCenter** ou l’**AD**
- ⚙️ **Optimiser l’allocation dynamique des ressources**
