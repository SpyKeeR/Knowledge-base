# Rescue via CD**1️⃣ Pourquoi utiliser cette méthode ?**

- **Pratique pour la récupération** quand tu n’as pas de solution de récupération sur le disque dur.
- Pas de mot de passe **root** requis pour accéder à l’environnement de récupération.
- Permet de **monter des partitions** et de corriger des fichiers système, tout en **réparant** les erreurs critiques.

**2️⃣ Démarrer depuis un CD-ROM**

**a. Accéder au BIOS**

- Appuie sur la touche **Suppr** (ou **F2** selon la marque) pendant le démarrage.

**b. Sélectionner l’amorçage du CD-ROM**

- Dans le BIOS, **va dans le menu de démarrage** (boot menu).

**3️⃣ Sélectionner "Rescue Mode"**

- Une fois le **CD-ROM démarré**, tu accèdes à un menu.
- **Choisis "Rescue Mode"** (et non le "Graphical Rescue Mode", car celui-ci n'apporte pas d'avantages significatifs).

**Astuce** : Le mode rescue est minimal et puissant, te permettant d’effectuer des réparations sans fioritures graphiques.



**4️⃣ Fonctionnalités en Mode Rescue**

- **Accès root** : Une fois en mode rescue, tu as un accès direct au compte **root** (pas de mot de passe nécessaire).
- **Montage de partitions** : Tu peux monter les partitions de ton système défaillant et y accéder pour modifier ou corriger des fichiers.

**Exemple** : Monter une partition : *mount /dev/sda1 /mnt*

Tu peux ensuite éditer des fichiers critiques ou effectuer des réparations.



**5️⃣ Avantages de l’utilisation du CD-ROM**

**✅ Avantages :**

- **Langue française** : L’interface de récupération peut être en français.
- **Aucun mot de passe root requis** pour accéder au système de récupération.
- **Extinction propre** : Tu peux éteindre proprement ton système à partir du mode rescue, ce qui est plus sûr qu’une extinction forcée.

**❌ Inconvénients :**

- **Nécessité d'un CD-ROM** ou d’une **image ISO** pour démarrer.
- **Difficultés en virtualisation** : Si tu travailles en machine virtuelle, démarrer depuis un CD-ROM peut être plus complexe.
