# **🧩 Qu’est-ce qu’un plugin dans GLPI ?**

- Un **plugin (ou extension)** permet **d'étendre les fonctionnalités natives** de GLPI.
- Ils ne sont **pas toujours développés par l’équipe officielle** de GLPI.
- N’importe qui peut en créer et en proposer sur la plateforme officielle.
- Les plugins sont **autonomes** du cœur de GLPI, et sont gérés à part.



**🌐 Le site officiel des plugins :** <https://plugins.glpi-project.org>

- C’est le **catalogue centralisé** des plugins disponibles.
- On y retrouve :
  - 🔎 Une **description des fonctionnalités** proposées.
  - 🔄 La **compatibilité** des plugins selon la version de GLPI.
  - 🧠 Un classement par **catégories de besoins** : Reporting 📊 / Inventaire 🖥️ (ex : FusionInventory) / Réseau 🌐 / Gestion administrative et financière 💼 et Export/Import de données 🗂️ (ex : Data-Injection)



**📥 Méthodes d’installation d’un plugin**

**Manuellement (méthode historique) :**

- Télécharger l’archive du plugin (.tar.bz2 ou .zip)
- Copier l’archive dans le dossier : /var/www/html/glpi/plugins
- Extraire le contenu et **supprimer l’archive**
- Se rendre sur l’**interface web de GLPI** : Aller dans **Configuration > Plugins /** Le plugin apparaît comme “non installé” / **Installer** → crée les tables en base de données / **Activer** → rend le plugin utilisable

📌 *Chaque plugin peut apparaître dans des menus différents (pas toujours Configuration ou Administration). Se référer à sa documentation pour le trouver.*

**Via le Marketplace GLPI (plus moderne & graphique) :**

- Permet d’**installer directement des plugins** via l’interface GLPI.
- Nécessite : 💻 Un **accès Internet** depuis le serveur GLPI. /🔑 Une **clé API Registration GLPI Network** (à récupérer sur le site officiel après inscription).
- Cette clé se configure dans : Configuration > Général > Clé d’authentification Marketplace
- Permet un accès direct aux plugins compatibles avec ta version de GLPI 🎯



**⚠️ Attention à la compatibilité des plugins :**

- Lors d’une **mise à jour de GLPI**, vérifie :
  - Que tes plugins principaux sont **compatibles** avec la nouvelle version.
  - Sinon, cela peut **casser des fonctionnalités** ou rendre le plugin inutilisable.
- 🛠️ Mieux vaut **mettre à jour GLPI et les plugins en parallèle**.

