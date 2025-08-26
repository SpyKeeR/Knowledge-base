# Entités et récursivité dans GLPI

## 🧩 Notion d’entité
Une **entité** dans GLPI est un **compartiment organisationnel** permettant de **cloisonner les données, les utilisateurs et les droits**.  
C’est le **squelette structurel** de l’outil : chaque entité définit un périmètre de gestion.

### 🔸 Exemples d’utilisation
- **Services internes** : DSI, RH, Commercial…
- **Clients externes** : une ESN peut créer une entité par client (Client A, Client B…).
- **Groupes d’entreprises** : une maison mère avec plusieurs filiales → une entité par filiale.

👉 Chaque entité a sa propre configuration, ses propres techniciens, ses tickets, ses règles et ses ressources.

---

## 🌳 Hiérarchie et sous-entités
Les entités sont **hiérarchiques** :  
- Tu peux créer des **sous-entités** (ex. “Siège” avec “Support”, “Infra” et “Développement”).  
- Les droits peuvent se propager de façon **récursive** (ou pas).

Un utilisateur peut avoir accès à une seule entité, à plusieurs, ou à toute la hiérarchie, selon ses habilitations.

---

## 🔁 Notion de récursivité
La **récursivité** définit si une entité **enfant hérite** ou non de certains éléments de son entité **parente** :

- **Activée 🔄** : les sous-entités héritent des éléments définis au niveau supérieur (fournisseurs, modèles, règles, etc.).  
- **Désactivée 🚫** : chaque entité reste totalement indépendante.

**Exemple concret :**
- Un fournisseur défini dans l’entité **racine** → visible dans toutes les sous-entités si récursivité activée.  
- Un fournisseur défini localement dans une entité → visible uniquement dans cette entité.

---

## ⚙️ Paramétrage d’une entité
📍 Menu : **Accueil > Administration > Entité**

**Principaux onglets :**
| Onglet              | Description |
|---------------------|-------------|
| **Entité**          | Nom, description, parent éventuel |
| **Entités**         | Création/visualisation des sous-entités |
| **Adresse**         | Adresse postale, géolocalisation |
| **Infos avancées**  | LDAP, intégrations externes, visibilité |
| **Notifications**   | Mails automatiques spécifiques à l’entité |
| **Assistance**      | Paramètres tickets (modèles, calendriers, satisfaction) |
| **Parc**            | Règles de transfert, modèles, dates |
| **Interface**       | Personnalisation visuelle (ex : CSS) |
| **Utilisateurs**    | Membres rattachés, profils, droits, récursivité |
| **Règles**          | Règles d’affectation ou d’import |
| **Documents / Notes** | Ressources locales à l’entité |
| **Base de connaissances** | Articles associés à l’entité |
| **Historique**      | Suivi et traçabilité des actions |

---

## 🎯 Objectif des entités et de la récursivité
- **Structurer et segmenter** les données
- **Cloisonner les accès** pour plus de sécurité
- **Adapter la visibilité** aux besoins métier
- **Conserver une gestion centralisée** si nécessaire (via les entités parentes et la récursivité)

💡 En résumé :  
Les entités permettent d’organiser finement l’accès aux données et aux fonctionnalités.  
La récursivité ajoute de la souplesse en autorisant (ou pas) le partage d’éléments entre les niveaux de la hiérarchie.
