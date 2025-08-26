# Production/déploiements

### **🎯 Objectif principal :**

Ce processus vise à **planifier, organiser et valider le déploiement de nouveaux services ou de services modifiés**, de manière **industrielle**, c’est-à-dire structurée, fiable et reproductible.

Le but est de **garantir de la valeur ajoutée pour les utilisateurs**, tout en respectant les délais, les coûts et la qualité attendus (les fameux *SLA* : Service Level Agreements).



### **🛠️ Rôles et responsabilités :**

- Créer la **documentation de mise en production** et d’utilisation des nouveaux services.
- S’assurer que :
  - Les **utilisateurs sont formés** à ces nouveautés.
  - Les **techniciens de support ont accès à une base de connaissances** à jour.
- Gérer :
  - Les **différentes versions** des services.
  - Les **composants déployés**.
- Utiliser le modèle **RACI** pour répartir les rôles :
  - **R**ealize (Réalise)
  - **A**ccountable (Responsable)
  - **C**onsulted (Consulté)
  - **I**nformed (Informé)



### **🚀 Types de déploiement :**

| **Type** | **Description** |
|----|----|
| **Poussé (push)** | L’**initiative vient du service IT**. Les utilisateurs subissent le changement. |
| **Tiré (pull)** | Le service est mis à disposition, **l'utilisateur choisit quand l'utiliser**. |
| **Big Bang** | Déploiement massif d’un coup, **tout le monde est impacté en même temps**. |
| **Par phases** | **Petit groupe testé d’abord**, puis élargissement si tout se passe bien. |
| **Automatique** | Fait par script, sans intervention humaine. |
| **Manuel** | Fait avec assistance (technicien présent). |



✅ **Ces modes peuvent être combinés** : par exemple un *push*, *Big Bang*, *automatique* (ex : déploiement du nouveau Pack Office à tous les postes d’un service pendant le week-end).

🧠 **Important** : Il faut **toujours prévenir les utilisateurs** pour éviter la surcharge du support et la panique générale.

**💡 À retenir :**

- Ce processus fait le lien entre **conception du service** et **exploitation réelle**.
- Il évite que des services soient mis en production à l’arrache ou sans préparation. 
- Il est **essentiel pour garantir la stabilité** de l’environnement de travail et pour **éviter les tickets à répétition** au support.



