# Utilisateurs - Base locale- **Où ?** : Menu Accueil > Administration > Utilisateurs
- C’est la liste **complète des utilisateurs** qui ont un accès ou des droits dans GLPI.
- En gros, ce sont les profils locaux créés directement dans GLPI, pas ceux qui viennent de l’Active Directory (on verra ça plus tard).



**📝 Paramètres d’un utilisateur**

Chaque utilisateur a une fiche détaillée avec plusieurs onglets :

- **Utilisateur**
  - **Infos de base : Nom, prénom, email, téléphone, adresse… / Gestion du mot de passe (tu peux forcer un changement) / Lieu de travail affiché (utile pour savoir où il est physiquement) / Profil (ex : technicien, gestionnaire, utilisateur final) / Entité (on verra plus tard, c’est comme une branche ou un service) / Groupe par défaut (pour simplifier ses actions dans GLPI)**
- **Habilitations**
  - **Montre les profils et permissions que l’utilisateur a dans chaque entité.**
  - **Exemple : un utilisateur peut être technicien dans une entité, mais juste utilisateur final dans une autre.**
- **Groupes**
  - **Liste les groupes auxquels il appartient.**
  - **Responsable ? : Oui/Non — ça donne un rôle de manager pour ce groupe.**
  - **Délégataire : Peut ouvrir un ticket au nom d’un autre utilisateur (pratique pour assistants ou chefs d’équipe).**
- **Préférences**
  - **Paramètres personnels (langue, affichage, notifications) que l’utilisateur peut modifier.**
- **Éléments Utilisés**
  - **Objets que l’utilisateur a utilisé (exemple : un PC, une imprimante).**
- **Éléments Gérés**
  - **Objets dont il est responsable ou qu’il gère (exemple : un technicien en charge d’un parc de PC).**
- **Tickets, Problèmes, Changements, Documents, Réservations**
  - **Tout ce que l’utilisateur a créé ou est lié à lui dans GLPI.**
- **Synchronisation**
  - **Comment les données de cet utilisateur sont synchronisées (utile si on lie à l’Active Directory).**
- **Liens**
  - **On peut attacher des liens externes (vers un intranet, un dossier partagé, etc.).**
- **Certificats**
  - **Liste des certificats numériques associés (si besoin).**
- **Historique**
  - **Toutes les modifications faites sur ce compte utilisateur.**
- **Tous**
  - **Vue globale de tous les paramètres en un seul endroit.**

**🎯 Pourquoi c’est important ?**

Si tu bosses sur GLPI, maîtriser les utilisateurs locaux, c’est gérer les personnes qui peuvent **ouvrir des tickets**, **réparer des équipements**, **gérer des docs**, **superviser**... bref tout le système d’accès et de rôles dans l’outil.
