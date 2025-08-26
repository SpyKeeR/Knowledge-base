# Intitulés et lieux

## **🧠 C’est quoi un « intitulé » dans GLPI ?**

Un **intitulé**, c’est **un nom ou une étiquette** utilisée dans les listes déroulantes de GLPI.

Exemples : nom d’un lieu, statut d’un matériel, type de ticket, etc.

👉 L’idée, c’est de **nommer les choses avec des termes parlants pour ton organisation**.

Tu peux donc **personnaliser les intitulés** pour refléter le vocabulaire utilisé dans ton entreprise.



### **⚙️ Où on les gère ?**

- Menu **Administration > Gestion des intitulés** C’est ici que tu peux : Ajouter, Modifier, Supprimer, Organiser les intitulés.



### **📂 Deux types d’intitulés :**

1.  **Valeurs à plat** : une simple liste (ex. : "En stock", "Au rebut", "En panne").
2.  **Valeurs arborescentes** : tu peux **lier des intitulés entre eux** (ex. : un lieu peut avoir un lieu "parent", comme "Bâtiment A > Étage 1 > Bureau 104").



### **🧩 Exemples d’intitulés personnalisables :**

- **Lieux** : pour localiser géographiquement utilisateurs, matériels, etc.
- **Statuts matériels** : pour dire si un équipement est en stock, cassé, prêt, etc.
- **Fournisseurs** / **Constructeurs** : à gérer directement via les intitulés.
- **Catégories de tickets** : pour classer les tickets (bureautique, réseau, logiciel…).
- **Types de logiciels** : organiser les logiciels avec ta propre nomenclature.
- **Types de calendriers** : jours ouvrés, 24h/24, 7j/7… utiles pour la gestion d’interventions.

#### **⚠️ Très important : la différence entre « entité » et « lieu »**

- Une **entité** = séparation fonctionnelle ou organisationnelle (ex. : deux entreprises différentes).
- Un **lieu** = une position géographique (ex. : un site, un bâtiment, un étage).

🎯 **Bon à savoir** : Ne crée **une nouvelle entité** que si c’est **un organisme réellement indépendant** (ex. : qui finance ses achats, paie ses techniciens, etc). Sinon, **préfère utiliser les lieux** pour garder une gestion centralisée **plus simple**.

#### **🔄 Active Directory et lieux :**

Si tu relies GLPI à un **Active Directory**, tu peux : Définir les **lieux dans l’AD,** GLPI les récupérera automatiquement lors de l'import des utilisateurs.

🔧 Il faut bien sûr **paramétrer GLPI** pour qu’il sache où lire ces infos dans l’AD.



#### **🚀 Pourquoi c’est important ?**

GLPI est un **PGI (Progiciel de Gestion Intégré)** → il faut que **tous les services parlent le même langage**.

👉 Avant de te lancer dans la config, **prends du recul** : Réfléchis avec les équipes : comment on appelle les lieux, équipements, statuts, etc. ? **Crée une convention de nommage claire et** Implante ensuite ces conventions dans GLPI via les intitulés.


