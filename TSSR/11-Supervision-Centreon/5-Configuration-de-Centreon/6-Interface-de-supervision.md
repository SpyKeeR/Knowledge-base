## 🔍 **Surveillance en temps réel**

Une fois les hôtes/services configurés et le moteur rechargé, l'interface de supervision devient notre tableau de bord principal.

Depuis *Monitoring => Status Details => Services* ou *Hosts*, on visualise :

- Le **statut** (OK, WARNING, CRITICAL…)
- Le **type d’état** (Soft/Hard)
- L’**ancienneté** du dernier test
- Les **plugins exécutés** (ex. NRPE CPU/Memory)
- Le **nombre de tentatives**, et l’historique d’exécution

C’est l’outil de base pour juger de la **santé globale** de l’infra.



## 🛠️ **Actions immédiates**

Centreon permet d’agir directement sur un hôte/service :

- **Immediate Check** : replanifie un test au prochain cycle de supervision
- **Redémarrage forcé** : exécute un test immédiatement
- **Mass actions** possibles (sélection multiple de services/hôtes)

→ Idéal après une correction (ex : patch appliqué sur un serveur) pour **vérifier instantanément** si le service remonte en OK.
