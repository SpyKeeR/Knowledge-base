# **🧩 PCA, PRA, PCI, PRI – Plans de gestion de crise et de continuité**

La gestion de la continuité et de la reprise d’activité repose sur plusieurs plans complémentaires : PCA, PRA, PCI et PRI. Chacun a un rôle précis dans la protection des activités critiques d’une organisation.


## **PCA – Plan de Continuité d’Activité**

Le PCA regroupe les **mesures préventives et organisationnelles** pour **maintenir les activités essentielles** sans interruption, même lors d’une crise majeure (incendie, cyberattaque, panne…).  
Il vise à **assurer la survie immédiate** de l’entreprise, en permettant de continuer à produire, vendre, échanger et communiquer.

**Principaux éléments du PCA :**
- 🛡️ **Solutions proactives** : haute disponibilité, redondance, basculement automatique
- 🧰 **Organisation structurée** pour absorber les chocs
- 🧠 **Gouvernance de crise** anticipée
- 🏢 **Repli physique** (site de secours, télétravail)
- 📣 **Communication de crise** (interne/externe)
- 🚨 **Gestion des risques** (cybersécurité, sanitaire…)
- 🌐 **Continuité du SI** (cluster, failover…)

### **PCI – Plan de Continuité Informatique**

Le PCI est le volet informatique du PCA. Il définit les **mécanismes techniques** pour garantir la disponibilité des systèmes et des données en cas d’incident, sans interruption ou avec une interruption minimale.


## **PRA – Plan de Reprise d’Activité**

Le PRA est une **sous-partie du PCA**, centré sur la **reprise des systèmes informatiques** après un sinistre.  
Il s’active lorsque la continuité n’a pas pu être assurée.

**Objectif :** **Redémarrer rapidement les services IT** dans un état acceptable, à partir de sauvegardes ou de redéploiement.

**Principaux éléments du PRA :**
- 💾 **Stratégie de sauvegarde** fiable et testée
- 🗃️ **Stockage sécurisé** des supports de restauration
- 🔄 **Procédures documentées** de restauration
- 🧾 **Gestion des licences** post-sinistre
- 📅 **Délais de reprise cibles** : RTO (Recovery Time Objective) / RPO (Recovery Point Objective)

### **PRI – Plan de Reprise Informatique**

Le PRI est le volet informatique du PRA. Il détaille les **procédures de restauration** des systèmes, applications et données après un sinistre, pour permettre la reprise des activités informatiques.


## **Distinction entre PCA et PRA**

- **PCA** : vise à **éviter l’interruption** des activités critiques, grâce à des mesures anticipées et une organisation adaptée.
- **PRA** : intervient **après une interruption**, pour **restaurer** les services et systèmes dans les meilleurs délais.


## **📋 Plan de Sauvegarde**

Le plan de sauvegarde est une **brique technique essentielle** du PRA/PRI. Il décrit **quoi, quand, comment et où** sauvegarder.

**Il précise :**
- 🎯 **Périmètre** (fichiers, VMs, BDD…)
- 📂 **Types de sauvegardes** (complète, différentielle, incrémentale…)
- ⏱️ **Fréquences et rétentions**
- 💽 **Supports utilisés** (disque, bande, cloud…)
- 🌍 **Stockage sur site / hors site / offline**
- 🧪 **Tests réguliers** de restauration
- 💣 **Destruction sécurisée** des supports obsolètes


**Un plan rigoureux, sécurisé et testé est indispensable pour préserver l'intégrité et la disponibilité des données.**