# Interface de supervision🔍 **Surveillance en temps réel**

Une fois les hôtes/services configurés et le moteur rechargé, l'interface de supervision devient notre tableau de bord principal.

Depuis *Monitoring => Status Details => Services* ou *Hosts*, on visualise :

- Le **statut** (OK, WARNING, CRITICAL…)
- Le **type d’état** (Soft/Hard)
- L’**ancienneté** du dernier test
- Les **plugins exécutés** (ex. NRPE CPU/Memory)
- Le **nombre de tentatives**, et l’historique d’exécution

C’est l’outil de base pour juger de la **santé globale** de l’infra.



🛠️ **Actions immédiates**

Centreon permet d’agir directement sur un hôte/service :

- **Immediate Check** : replanifie un test au prochain cycle de supervision
- **Redémarrage forcé** : exécute un test immédiatement
- **Mass actions** possibles (sélection multiple de services/hôtes)

→ Idéal après une correction (ex : patch appliqué sur un serveur) pour **vérifier instantanément** si le service remonte en OK.



📁 **Groupes : Organisation logique**

On peut regrouper des objets pour une lecture plus claire de l’infra :

- **Par technologie** (Win, Linux, switchs...)
- **Par fonction** (intranet, base de données...)
- **Par site** (Paris, Lyon…)

Ces groupes servent à la **navigation**, aux **stats globales**, mais **ne peuvent être utilisés dans les modèles**. On les applique manuellement à chaque objet.



🔐 **Catégories : Accès & héritage**

Contrairement aux groupes, les **catégories** :

- Permettent de **filtrer les droits d’accès** (visibilité par rôle)
- Peuvent être appliquées dans les **modèles** (héritage actif)
- Sont utilisées par certains **modules payants** pour des vues avancées ou rapports détaillés

Elles renforcent la **gestion fine** de l’interface (accès restreints, délégation).
