# Interfaces et profils**📌 Les deux types d'interfaces**

GLPI propose deux types d’interfaces, selon le type d’utilisateur et l’usage :

**1. Interface simplifiée**

- **Destinée aux utilisateurs finaux** (utilisateurs “quotidiens” non techniques) Très **épurée** pour ne pas distraire ni perdre l’utilisateur.
- Permet uniquement : **La création de tickets, L’accès à la FAQ** et **La réservation de ressources** (ex : projecteurs, salles).
- **Pas d’accès** aux configurations avancées, équipements, ou autres modules. **Rattachée aux profils** comme celui de “Service”.

**2. Interface standard**

- Utilisée par les **techniciens, administrateurs**, ou autres profils avancés et Affiche **plus d’informations et de fonctionnalités** : Gestion du parc, Suivi des tickets, Accès à la configuration, aux équipements, à la base de connaissances, etc.
- Permet de **personnaliser l’affichage** : Vue personnelle, Vue par groupe, Vue globale (tous les utilisateurs).



**👥 Notion de profils (ou rôles)**

Un **profil** dans GLPI = un **ensemble de droits** attribués à un utilisateur selon son rôle dans l’organisation.

- Un utilisateur peut avoir **plusieurs profils** (changer de “casquette”).
- Les droits dépendent aussi de **l’entité** à laquelle l’utilisateur est rattaché (principe d’héritage possible entre entités → “récursivité”).



**🔐 Profils prédéfinis (modifiables ou personnalisables) :**

| **Profil** | **Description** |
|----|----|
| **Super-Admin** | Accès **complet à tout GLPI**. Utilisé par défaut par l'utilisateur glpi. |
| **Admin** | Accès large, sauf certaines fonctions critiques comme la **configuration de GLPI**. |
| **Technicien** | Accès en **lecture sur l’inventaire**, peut **gérer les tickets**. |
| **Hotliner** | Peut **créer/saisir des tickets** (ex : via appels ou mails). Ne peut pas les attribuer. |
| **Observateur** | Accès **lecture seule**, pour être notifié ou suivre les événements. |
| **Self-Service** | Utilisateur “de base”,**interface simplifiée**. Peut : Créer ses propres tickets, Consulter la FAQ, Réserver du matériel, voir ses propres demandes |



**⚙️ Gestion des droits par profil**

GLPI permet d’**affecter des droits par fonctionnalité** à chaque profil.

**Exemples de droits possibles : Lecture** : voir les éléments. / **Création** : ajouter de nouveaux éléments. / **Modification** : modifier des éléments existants. / **Suppression / Purge** : supprimer définitivement.

**Domaines concernés :** 🎒 Parc / 🎫 Assistance / 🧾 Cycles de vie (Ticket) / 🛠 Outils / Gestion / ⚙️ Administration / Configuration
