# Utilisateurs / RôlesDans un datacenter, la **gestion des droits** est cruciale pour garantir la sécurité et l’intégrité des infrastructures.

Chaque objet (VM, datastore, vSwitch, etc.) peut nécessiter des autorisations spécifiques selon les tâches à effectuer.

🔹 Les utilisateurs peuvent être **regroupés** dans des groupes d’administration.

🔹 À chaque **groupe**, on peut attribuer un **rôle**, c’est-à-dire un ensemble de privilèges.

🔹 Cela permet de **contrôler finement** qui peut faire quoi (ex : empêcher un technicien d’éteindre une VM critique).



**🏗️ Méthodologie d’affectation des droits**

1.  **Créer les utilisateurs**
2.  **Créer les groupes** et y affecter les utilisateurs
3.  **Créer les rôles** avec des privilèges précis (lecture, arrêt, clonage, etc.)
4.  **Associer les groupes et rôles** aux objets de l’inventaire (VM, hôtes, datastores…)

Cette logique permet de **lier des droits précis à des objets précis**, en fonction du **besoin réel** des équipes.



**🏢 Deux niveaux de gestion des comptes**

🔸 **Sur ESXi** : Gestion **locale** des utilisateurs → les droits s’appliquent uniquement à cet hôte ou aux VMs qu’il héberge.

🔸 **Sur vCenter** : Gestion **centralisée** des droits sur toute l’infrastructure vSphere.

→ Intégration possible avec un **contrôleur de domaine Active Directory**.

**🔐 Intégration avec Active Directory et SSO**

- Le vCenter peut **se connecter au domaine AD** pour importer utilisateurs et groupes.
- Grâce au **SSO (Single Sign-On)**, il est possible de s’authentifier avec les **comptes du domaine**.
- Cela permet une **meilleure traçabilité**, une **authentification centralisée** et une gestion simplifiée.



**🔗 Lien entre rôles, utilisateurs et objets**

Chaque **rôle** (Support, Admin, Lecture seule...) peut être appliqué à des **objets spécifiques** de l'inventaire : VM, ESXi, Cluster, Datastore, Réseaux et Actions spécifiques (supprimer, migrer, modifier, etc.)

Les rôles permettent d’**accorder uniquement les privilèges nécessaires** aux bonnes personnes, au bon endroit.



**✅ Bonnes pratiques de gestion des privilèges**

- ✅ **Utiliser des groupes** plutôt que des utilisateurs seuls → facilite la gestion en cas de changement.
- ✅ **Attribuer le minimum de droits nécessaires** (principe du moindre privilège).
- ✅ Si un rôle est trop permissif, le **dupliquer et adapter** selon le besoin.
- ✅ **Définir les droits au niveau le plus haut** possible → les droits se propagent **par héritage** vers les éléments enfants.
- ✅ **Documenter clairement** qui a accès à quoi.
