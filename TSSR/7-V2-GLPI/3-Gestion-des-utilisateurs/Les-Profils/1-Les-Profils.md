# Les ProfilsLes **profils** définissent **qui a le droit de faire quoi** dans GLPI. C’est un des piliers de la gestion des accès et de la sécurité. Tu vas voir, une fois que t’as capté la logique, ça devient hyper fluide ! 💡



**🗂️ Liste des profils standards de GLPI**

Voici les profils par défaut que tu trouves à l’installation de GLPI :

- **Super-Admin** : tous les droits sans restriction (Dieu dans GLPI 🤣).
- **Admin** : droits d’administration, mais quelques limites (sur la config avancée notamment).
- **Supervisor** : technicien + gestion d’équipe et d’organisation.
- **Technician** : gestion du parc + gestion complète des tickets.
- **Hotliner** : uniquement gestion des tickets (pas d’accès au parc).
- **Observer** : lecture seule du parc, mais peut participer aux tickets.
- **Self-Service** : peut juste créer et suivre ses propres tickets.
- **Read-only** : consultation uniquement, aucun droit d’action.

🧠 **À retenir** : Les profils sont **modifiables** et **supprimables**, mais :

- Il **ne faut pas supprimer** Super-Admin (grosse galère sinon).
- Ils **ne sont pas duplicables**, mais tu peux **créer des profils personnalisés** à la main.



**🔐 Détail des rôles par profil**

| **Profil** | **Parc** | **Tickets** | **Interface** |
|----|----|----|----|
| **Super-Admin** | ✅ Tout | ✅ Tout | Interface standard |
| **Admin** | ✅ Admin | ✅ Admin | Interface standard |
| **Supervisor** | ✅ (comme Tech) | ✅ + gestion équipe | Interface standard |
| **Technician** | ✅ Créa/Modif/Supp | ✅ Cycle de vie complet | Interface standard |
| **Hotliner** | ❌ | ✅ Cycle de vie complet | Interface standard |
| **Observer** | ✅ Lecture | 🔶 Participe mais ne gère pas | Interface standard |
| **Self-Service** | ❌ | ✅ Création + suivi persos | Interface simplifiée |
| **Read-only** | ✅ Lecture | ❌ | Interface standard |

🔶 = Cas particulier → Observer peut prendre en charge un ticket, mais sans accès à toutes les fonctionnalités d’assistance.

**🧠 Contexte concret : un scénario typique**

👉 Imagine une entreprise classique avec :

- Des techniciens IT internes (profil **Technician**)
- Un superviseur IT (profil **Supervisor**)
- Des agents en hotline (profil **Hotliner**)
- Les utilisateurs finaux (profil **Self-Service**)

Les techniciens peuvent gérer tout le parc et les tickets.

Le superviseur, lui, voit tout et peut attribuer les tickets, surveiller les tâches des autres, etc.

La hotline crée les tickets en prenant les appels, mais ne touche pas au matériel.

Les utilisateurs, eux, créent juste leurs tickets via l’interface simplifiée, et c’est tout.

🎯 **Gain** : meilleure sécurité, moins de risques d’erreurs, et chaque utilisateur a un outil à sa mesure.
