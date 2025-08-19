# Groupes/catégories/ACL**🧑‍🤝‍🧑 Groupes : organisation logique des objets**

- **Définition** : Permettent de regrouper les *hôtes* ou *services* selon une logique métier, technique ou géographique.
- **Exemples d’usage** :
  - Hôtes : par OS (Win, Linux), par site (Paris, Lyon...)
  - Services : regrouper ceux liés à l'intranet, messagerie, sauvegarde, etc.
- **Utilité principale** :
  - Vue synthétique dans l’interface de supervision (état, stats, filtres)
  - Pas utilisés pour la gestion des droits
- **Limitation** :
  - ⛔️ Non compatibles avec les *Templates*
  - ➕ Doivent être assignés directement à chaque hôte/service concerné



**🗂️ Catégories : gestion des droits et héritage**

- **Définition** : Regroupent aussi hôtes/services mais avec une finalité *fonctionnelle* : affectation de droits, filtres d’affichage, règles de gestion
- **Utilité** :
  - 🛡️ Servent dans la gestion des permissions (ACL)
  - 🎛️ Intégrées dans certains modules *payants* (pas vus ici)
- **Avantage clé** :
  - ✅ Compatibles avec les modèles (Templates)
  - ✅ Supportent **l’héritage** → gain de temps et cohérence



**🔐 Objectif des ACL**

- Permettre un **contrôle fin** des accès à l'interface :  
  → Qui peut voir quoi, et faire quoi

- S’applique aux *utilisateurs*, *groupes d’utilisateurs*, *objets supervisés*

**🏗️ Structure des ACL**

Centreon utilise une logique en **étages imbriqués**, avec 3 blocs principaux :

1.  **ACL Resources** ➡️ Définissent *ce qu’on protège* :

    - ***Groupes d’hôtes*, *Groupes de services*, *Catégories*, *Commandes*...**

2.  **ACL Menus** ➡️ Définissent *ce qu’on affiche dans l’UI* :

    - **Menu Monitoring, Configuration, Reporting…**

3.  **ACL Actions** ➡️ Définissent *ce que l’utilisateur peut faire* :

    - **Lire, Éditer, Supprimer, Envoyer une commande, etc.**

🧠 **Tout se regroupe dans un** : ➡️ **ACL Group** :

Un conteneur qui agrège **Resources + Menus + Actions** puis s’associe à **un utilisateur ou un groupe d’utilisateurs**
