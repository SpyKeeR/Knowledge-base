# Groupes - Base localeUn **groupe** est un ensemble d’utilisateurs, qui peut aussi contenir d’autres groupes (sous-groupes) ou des objets (matos, tickets, projets). Les groupes simplifient la gestion : au lieu de gérer utilisateur par utilisateur, tu peux gérer les droits et notifications par groupe.
- Tu retrouves tout ça dans Accueil > Administration > Groupes.

**⚙️ Les paramètres principaux d’un groupe**

Dans la fiche d’un groupe, tu as plusieurs sections :

- **Nom du groupe** : facile, c’est le nom qui identifie ton groupe dans GLPI.
- **Arborescence** : la structure hiérarchique (avec sous-groupes).
- **Visibilité et droits dans un ticket** :
  - *Demandeur* : le groupe peut-il créer un ticket ?
  - *Observateur* : peut être ajouté comme observateur (tu suis l’évolution sans intervenir).
  - *Tâche* : peut recevoir des tâches à réaliser.
  - *Peut-être notifié* : le groupe reçoit des alertes par mail.
- **Visibilité dans un projet** : Le groupe peut être *superviseur* (il a les droits de gestion sur un projet).
- **Peut contenir** : *Eléments* (objets GLPI : ordinateurs, imprimantes, etc.) et *Utilisateurs* (les membres du groupe)
- **Sous-groupes** : liste des groupes enfants (pratique pour faire une organisation en pyramide).



**🛠️ Autres contenus liés au groupe**

- **Eléments utilisés** : objets que le groupe utilise (exemple : un parc d’ordinateurs alloué à ce groupe).
- **Eléments gérés** : objets dont le groupe est responsable (exemple : matériel à gérer, suivre).
- **Tickets** : tickets ouverts par ce groupe (s’il peut en créer).
- **Problèmes & Changements** : incidents et demandes de changements liés au groupe.
- **Notes** : notes créées par les membres du groupe.
- **Historique** : trace des modifications sur le groupe (utile pour audit).



**🔄 Import de groupes depuis un annuaire LDAP (exemple Active Directory)**

- Tu peux **importer en masse des groupes** depuis ton AD, comme pour les utilisateurs.
- GLPI affiche la liste des groupes AD, tu sélectionnes ceux que tu veux importer.
- Si tu utilises des **entités** (type services ou départements dans GLPI), tu peux choisir à quelle entité associer les groupes importés.
- Tu peux aussi choisir la récursivité, c’est-à-dire importer les sous-groupes automatiquement.
- Enfin, tu cliques sur **Importer** pour que ça crée les groupes dans GLPI.



**🎯 Pourquoi gérer les groupes est clé ?**

- Tu gagnes en efficacité : un changement sur un groupe impacte tous les utilisateurs associés.
- La gestion des droits et notifications est simplifiée et plus cohérente.
- En associant groupes et entités, tu peux adapter précisément les accès à la structure de ton organisation.

**💡 Exemple concret**

Imagine une entreprise avec ces groupes AD :

- *Support* (regroupe les techniciens)
- *Compta* (regroupe les comptables)
- *RH* (ressources humaines)

Tu importes ces groupes dans GLPI et tu leur donnes des rôles :

- *Support* peut créer des tickets et recevoir des notifications.
- *Compta* est juste observateur sur certains tickets.
- *RH* supervise certains projets de gestion du personnel.
