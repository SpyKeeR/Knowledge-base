# Composants Infrastructure**🖥️ Hyperviseur ESXi : le cœur de vSphere**

- **ESXi** remplace l'ancien hyperviseur **ESX** depuis la version 5.
- Il permet l’accès aux **ressources physiques** : CPU, RAM, stockage, réseau.
- Il intègre une **interface web d'administration** autonome.
- Le **vCenter** centralise la gestion de plusieurs hôtes ESXi et leurs ressources.



**🛠️ Le client vSphere**

- Permet d'administrer les hyperviseurs **ESXi** et le **vCenter**.
- Disponible sur **Windows et Linux**.
- Utilisable en mode **autonome (ESXi seul)** ou **centralisé (via vCenter)**.
- Complète l’interface web native d’ESXi.



**📦 Machines virtuelles et gestion**

- Hébergées sur chaque hôte **ESXi**.
- Gérées soit via l’interface **web locale** de l’hôte, soit via **vCenter**.
- **vCenter** permet :
  - Une **vue d’ensemble** de l’infrastructure.
  - La **gestion centralisée** des ressources.
  - Le **déplacement de VM** entre hôtes ESXi.
  - L’application de **règles de restriction** des ressources.



**🔁 Déplacements & flexibilité**

- Grâce à **vCenter**, il est possible de : Déplacer dynamiquement des **VM d’un ESXi à un autre**, Adapter la charge selon l’activité des hôtes

**Optimiser les performances** globales.



**⚙️ Compatibilité des versions**

- Tous les composants doivent être **versionnés de manière cohérente** :
  - Le **vCenter** doit être en **version égale ou supérieure** aux hyperviseurs qu’il administre.
  - Exemple : pour des hôtes ESXi en 6.0 et 6.5 → le vCenter doit être en **6.5 minimum**.
- ❌ Éviter les mélanges incompatibles (ex : ESXi 5.5 + vCenter 6.0).



**✅ Bonnes pratiques pour une infrastructure stable**

- Utiliser une **version homogène** entre les hôtes ESXi.
- Déployer le **vCenter dans sa version la plus récente** compatible.
- ⚠️ Ne jamais administrer des hôtes ESXi avec un vCenter de version **inférieure**. Harmonisation = **fiabilité**, **performance** et **évolutivité**.
