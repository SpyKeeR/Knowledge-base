# Gabarits

Un **gabarit**, c’est un **modèle de ticket** utilisé pour **pré-remplir** certains champs selon le contexte (type + catégorie).

🔧 Ce que permet un gabarit :

- Définir une **interface simplifiée ou complète**, selon le **profil utilisateur**
- Rendre certains **champs obligatoires** ✅
- Masquer certains **champs non nécessaires** ❌
- Définir des **valeurs par défaut**, même masquées (non modifiables mais prises en compte)

📌 Par défaut, **tous les tickets utilisent un gabarit**, même si ce n’est pas visible. On peut ensuite **affiner** en choisissant un gabarit précis lié à une **catégorie**.



## **⚙️ Fonctionnement des déclencheurs**

Le **déclencheur** active un gabarit en fonction du **type** et de la **catégorie** du ticket.

📝 Étapes lors de la création d’un ticket : L’utilisateur clique sur **"Nouveau ticket" /** Il choisit le **type** (incident / demande) / Il peut saisir une **catégorie /** → Un **déclencheur** est activé qui charge le **gabarit associé**

🎯 Rôle de la catégorie : Aide à **classer correctement** le ticket / Permet de cibler les **bonnes équipes** de résolution / Important de **former les utilisateurs** à comprendre la **nomenclature**



## **📂 Où créer les gabarits de ticket ?**

🔍 Menu : **Assistance > Tickets >** ⚠️ Attention à bien vérifier **l’entité active** lors de la création ! Les gabarits sont **propres à chaque entité**, et **non visibles** ailleurs sans récursivité activée.



## **🧱 Structure d’un gabarit**

Onglets disponibles lors de la création :

- **Champs obligatoires / Champs définis (valeurs par défaut) / Champs masqués / Interface standard** vs **interface simplifiée / Catégorie de ticket** (type + catégorie)



## **✅ Bonnes pratiques**

🗂️ **Créer d’abord les catégories** dans :

- Menu **Gestion > Intitulés > Catégories utiles**
- Éviter les arbres trop profonds ➜ Privilégier **2 niveaux max** (parent + enfant)
- Analyser les catégories surchargées ➜ les utilisateurs sont peut-être perdus

🧾 **Nommer les gabarits clairement** :

- Inclure **Type + Catégorie** dans le nom > Ex : Incident_Logiciel ou Demande_Matériel

🧠 **Ne pas "figer" certains champs** : Ex : **priorité** ou **SLA (contrat de service)** ➜ mieux vaut les gérer **dynamiquement** via **règles métiers** ⚙️



### **🔄 Nombre de gabarits par catégorie**

➡️ On peut avoir jusqu’à **4 gabarits par catégorie** dans certaines versions : 2 classiques : **Incident**, **Demande, Changement, Problème.**

