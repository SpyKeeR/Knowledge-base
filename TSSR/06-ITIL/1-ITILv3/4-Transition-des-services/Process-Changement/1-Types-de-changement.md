# Types de changement

## **🛠️ Les types de changements ITIL**

### **🔁 Changement standard**

- C’est un changement **routinier**, **fréquent**, **documenté**, déjà **testé et validé**.
- Il est **prévisible**, avec un **risque faible** ou **nul**.
- On les retrouve dans un **mini-catalogue de changements standards**.

✅ **Exemples** : Remplacement de toner dans une imprimante ou Remplacement d’une alimentation d’un PC.
- **Aucun besoin de CAB** pour validation (car c’est du déjà-vu).

### **⚙️ Changement normal**

- C’est un changement **non-automatique**, **non routinier**, qui **nécessite une évaluation complète**.
- Il passe par un **CAB (Change Advisory Board)** : un comité qui analyse et décide.
- Le **CAB étudie la RFC**, évalue le changement et :
  - ✅ L’accepte (avec justification), ❌ Le refuse (avec explication).

### **🚨 Changement urgent**

- C’est un changement **exceptionnel**, à traiter rapidement (ex : faille de sécurité critique, panne majeure).
- Géré par le **ECAB (Emergency CAB)** : version réduite du CAB, mais plus réactive.
- Il y aura souvent une **revue postérieure** pour comprendre ce qui s’est passé, et **capitaliser l’expérience**.



## **🔄 Cycle de vie d’un changement (workflow ITIL)**

1.  **RFC (Request For Change)** émise par un client / utilisateur.
2.  **Gestion des changements** :

    - **Analyse, classification, priorisation de la demande.**

3.  **Gestion de la mise en production** :

    - **Prépare, planifie et évalue le changement.**

4.  **Bilan préparatoire + Tests** :

    - **Tests techniques et tests d’exploitabilité.**
    - **Création d’une procédure de mise en production.**

5.  **Passage devant le CAB** :

    - **Validation, refus ou report.**

6.  **Implémentation** du changement validé.
7.  **Revue post-implémentation** :

    - **Retour d’expérience : le changement est-il concluant ?**
    - **Si non : possibilité d’annuler ou de corriger.**

8.  **Mise à jour de la base de connaissances (CMDB)**.

## **🧠 Les éléments clés à retenir**

| **Terme** | **Définition** |
|----|----|
| **CI (Configuration Item)** | Élément du SI (matériel ou logiciel) qui contribue à un service. |
| **CMS (Configuration M.S.)** | Logiciel qui stocke et gère tous les CI. |
| **GP (ex : GLPI)** | Outil de gestion de parc + tickets. Peut faire partie du CMS. |
| **CAB** | Comité consultatif pour les **changements normaux**. |
| **ECAB** | Version d’urgence du CAB, pour les **changements urgents**. |
| **Bilan post-implémentation** | Analyse finale pour valider ou rejeter le changement définitivement. |


