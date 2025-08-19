# chmod/chown**🔧 Commande chmod – Modifier les droits d'accès**

Permet de modifier (*Utilisable par root et proprio fichier*) les droits pour :

- **u** : utilisateur propriétaire
- **g** : groupe
- **o** : autres utilisateurs
- **a** : tous (user + group + others)

Symbole à utiliser :

- + pour ajouter un droit
- - pour retirer un droit
- = pour définir uniquement ce droit (écrase les autres)

**Exemple :** chmod g+w,o-rx /data/commun ➡️ Ajoute le droit d’écriture au groupe, retire lecture et exécution pour les autres.



**🔢 Notation octale – Plus directe**

chmod 770 /data/commun



**🔁 Attention avec -R (récursif)**

- Applique la commande à tous les fichiers **et** sous-dossiers.
- Attention à ne pas retirer les droits d’exécution sur les dossiers, sinon ils deviennent inaccessibles.

**Exemple risqué :** chmod -R -x /data/communs ❌ Retire le droit d’exécution partout → dossiers inaccessibles.

**Solution :** chmod -R +X /data/communs ✔️ Ajoute le droit d’exécution uniquement sur les dossiers (et sur les fichiers déjà exécutables).



**👑 Commande chown – Changer le propriétaire et/ou le groupe**

Utilisable **que par root** en cas de **changement d'user propriétaire**, **root et propriétaire** en cas de changement de **groupe propriétaire.**

**Exemple :** chown maxime:admin /data/commun ➡️ maxime devient propriétaire, groupe admin est associé.

**Avec récursivité :** chown -R maxime:admin /data/commun ➡️ Appliqué à tout le contenu du dossier.



**👥 Commande chgrp – Changer uniquement le groupe**

Utilisable par **root et propriétaire du fichier**.

**Exemple :** chgrp reseau /data/commun ➡️ Change seulement le groupe, sans toucher au propriétaire.
