# Bonnes pratiques**✅ Tester les ACL**

- **Testez les accès** des utilisateurs pour valider les permissions configurées.
- Idéalement, **vérifiez directement avec les utilisateurs** en temps réel.
- **Contactez-les pour valider** les accès pendant que vous êtes en train de les configurer.
- Utilisez l'onglet **Accès effectif** (menu avancé) pour vérifier les permissions.
- **Ne jamais annoncer un partage en production** sans avoir testé les ACL. 🔒



**⚙️ Bonnes pratiques à suivre**

- **Utilisez les groupes** dans les DACL plutôt que les utilisateurs individuels. 👥
- Privilégiez les **ACLs de base** : Lecture, Modification, Contrôle Total.
- **Gardez à l'esprit l'héritage** des permissions pour simplifier la gestion. 🌱
- **Refus implicite** > Refus explicite : évitez de **cocher "Refuser"** inutilement. ❌
- **Testez toujours avant de finaliser** les configurations, en particulier pour les utilisateurs et administrateurs. 🧑‍💻



**⚠️ Précautions pour les administrateurs**

- **L'UAC** (Contrôle de Compte Utilisateur) limite les actions des administrateurs pour des raisons de sécurité.
- Même administrateur, le système **considère** l'utilisateur comme un **utilisateur standard** par défaut. 🔐
- Vous pouvez **désactiver ce comportement** via les **stratégies de sécurité** si nécessaire.
