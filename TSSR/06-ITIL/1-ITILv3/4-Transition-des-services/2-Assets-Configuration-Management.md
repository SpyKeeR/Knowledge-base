# Assets/Configuration Management

### **🎯 Objectif du processus**

- **Identifier**, **contrôler** et **suivre** tous les éléments de ton système d’information.
- Chaque élément est appelé un **CI** (Configuration Item), que ce soit un matériel, un logiciel, un contrat, un document, un équipement réseau, etc.
- Chaque CI doit être **identifié de manière unique** (numéro d'inventaire ou référence visible physiquement sur l’équipement).

### **🔎 À quoi ça sert concrètement ?**

- Gérer le **parc informatique** de manière précise.
- **Éviter les erreurs** ou les oublis (matériel fantôme ou introuvable).
- Être plus **efficace en dépannage** grâce à une vision claire des équipements et de leurs relations.
- **Auditer régulièrement** pour vérifier si la réalité du terrain correspond bien à ce que le système dit.

## **🧠 Le CMDB & le CMS**

- **CMS (Configuration Management System)** = logiciel qui stocke, organise et gère tous les CI.
- **CMDB (Configuration Management Database)** = base de données où sont enregistrés tous les CI et leurs relations.
- **SACM (Service Asset Configuration Management)** = **ITAM (IT Asset Management)**


### **📦 Exemples de CI**

| **Type d’élément**      | **Exemples**                       |
|-------------------------|------------------------------------|
| **Matériel**            | Tour, switch, serveur, imprimante… |
| **Logiciel**            | OS, antivirus, bureautique…        |
| **Équip.réseau**        | Routeur, pare-feu, switch…         |
| **Téléphonie**          | Téléphones IP                      |
| **Contrats de service** | SLA (Service Level Agreements)     |
| **Documentation**       | Procédures, notices techniques     |
| **Environnement**       | Câblage, code d’accès              |

## **🛠️ Activités principales**

### **Planification initiale**

- Définir **le périmètre** (qu’est-ce qu’on va suivre ?).
- Définir **la granularité** (est-ce qu’on veut suivre jusqu’à la carte son d’un PC ou juste le PC complet ?).

### **Chargement initial >** Entrée de toutes les données de base dans le CMS.

### **Formation du personnel**

- Former les techniciens support / parc à bien utiliser le CMS, sinon les données seront fausses.

### **Mise à jour continue**

- Le parc évolue : le CMS doit être **tenu à jour en continu**.
- Si ce n’est pas le cas, ça devient vite inutilisable.

### **Audits réguliers**

- Faire des inventaires tous les mois ou tous les 6 mois.
- Comparer **ce que dit le CMS** avec **la réalité terrain** et Corriger les écarts.

### **🔍 Analyse et reporting**

- Grâce au CMS, on peut sortir des **rapports** :
  - Taux de panne d’un CI
  - Nombre d’incidents liés à un composant
  - Historique des maintenances
- Ces infos permettent : De **planifier des remplacements**.
  - D’identifier les **points faibles** du parc.
 
### **🔄 Schéma logique d’utilisation du SACM (ITAM)**

- 📋 Planifier ce qu’on veut suivre (quoi, jusqu’à quel niveau de détail).
- 📥 Importer les CI (chargement initial).
- 👨‍🏫 Former les équipes.
- 🔄 Mettre à jour en continu (installation, retrait, changement).
- 📊 Exploiter les données (rapport, panne, ticket…).
- 🔍 Faire des audits réguliers pour corriger les écarts.

## **🛡️ Petit bonus : DML (Definitive Media Library)**

- Bibliothèque des **logiciels validés** par l’organisation.
- Ce qui **n’est pas dans la DML = interdit** (sécurité, compatibilité…).
- Elle permet de **garder un environnement logiciel maîtrisé** et cohérent.

