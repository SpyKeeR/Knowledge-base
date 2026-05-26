# M02 - Administration Linux

## CH05 - Mode maintenance au démarrage

Trois méthodes existent pour passer un système en mode maintenance via GRUB, chacune avec ses contraintes et ses cas d'usage.

---

### 5.1 - Méthode 1 : le commutateur `single`

🛠️ **Cas d'usage** : maintenance standard, on connaît le mot de passe root.

⚠️ **Ne fonctionne PAS** sur un système en mode sudo pur (sans mot de passe root activé).

#### Procédure (méthode single)

1. Au menu GRUB, sélectionner un noyau avec `↑↓`
2. Appuyer sur `e` pour éditer la ligne de lancement
3. Sur la ligne commençant par `linux`, supprimer `rhgb quiet` et ajouter `single` :

```bash
linux ($root)/vmlinuz-xxx root=/dev/mapper/ol-root ro crashkernel=auto
  resume=/dev/mapper/ol-swap rd.lvm.lv=ol/root rd.lvm.lv=ol/swap single
```

1. Démarrer avec `Ctrl+x`

⚠️ Le clavier est en **QWERTY** dans l'éditeur GRUB.

🔧 Pour quitter le mode maintenance (`rescue.target`) : `Ctrl+d`

---

### 5.2 - Méthode 2 : `init=/bin/bash`

🛠️ **Cas d'usage** : on ne connaît **aucun** mot de passe du système (ni root, ni utilisateur). Permet de modifier le système sans authentification.

⚠️ Cette méthode est plus **brutale** : elle nécessite une extinction électrique forcée pour redémarrer (pas de `reboot` possible car systemd n'est pas lancé).

#### Procédure (init=/bin/bash)

1. Au menu GRUB, appuyer sur `e` pour éditer
2. Sur la ligne `linux`, supprimer `quiet` et ajouter `init=/bin/bash` :

```bash
linux ($root)/vmlinuz-xxx root=/dev/mapper/ol-root ro crashkernel=auto
  resume=/dev/mapper/ol-swap rd.lvm.lv=ol/root rd.lvm.lv=ol/swap init=/bin/bash
```

1. Démarrer avec `Ctrl+x`, puis appuyer sur `Entrée`

⚠️ Le clavier est en **QWERTY**.

#### Une fois dans le shell

Le système démarre mais il est en mode **protégé** : SELinux est actif et le FS racine est en lecture seule.

🔧 Étapes à suivre dans l'ordre :

```bash
# Désactiver temporairement la politique SELinux
/usr/sbin/load_policy -i

# Remonter la racine en lecture-écriture
mount -o remount,rw /

# ... faire les modifications nécessaires ...

# Créer le fichier autorelabel pour que SELinux remette les contextes au prochain boot
touch /.autorelabel

# Synchroniser le cache disque (IMPORTANT avant l'extinction forcée)
sync
```

📌 **SELinux** (Security-Enhanced Linux) est un module noyau de contrôle d'accès obligatoire (MAC), activé par défaut sur RHEL. Le fichier `/.autorelabel` force la remise en contexte de tous les fichiers au prochain démarrage, ce qui peut prendre du temps.

💡 Après le `sync`, éteindre la machine physiquement (pas de commande `reboot` disponible dans ce mode).

---

### 5.3 - Méthode 3 : média d'installation

🛠️ **Cas d'usage** : impossible d'intervenir via GRUB, ou disque corrompu.

#### Procédure (média d'installation)

1. Démarrer sur le média d'installation (ISO / USB)
2. Dans le menu, aller dans **Troubleshooting**
3. Choisir **Rescue**
4. Suivre les indications à l'écran

💡 Cette méthode monte le système existant dans un environnement de secours, ce qui permet de corriger des problèmes de configuration, réinitialiser un mot de passe root, réparer GRUB, etc.

---

### 🧪 Ateliers associés

- **Atelier 3** : Manipulation des 3 méthodes sur `srv-gui` (configuration GRUB, démarrage en `single`, démarrage en `init=/bin/bash` avec remontage en rw et création de fichier)
- **Atelier 4** : Gestion des services et des cibles systemd

---

### 📌 À retenir

1. **Méthode `single`** : maintenance classique, nécessite le mot de passe root. Ajout de `single` sur la ligne du noyau.
2. **Méthode `init=/bin/bash`** : accès sans mot de passe, mais nécessite extinction forcée. Penser à `load_policy -i`, `mount -o remount,rw /`, `touch /.autorelabel` et `sync`.
3. **Méthode média d'installation** : dernier recours, boot sur l'ISO et mode Rescue via Troubleshooting.
4. Le clavier est toujours en **QWERTY** dans l'éditeur GRUB.
5. SELinux impose de créer `/.autorelabel` après toute modification en mode rescue pour éviter des problèmes de contextes de sécurité au redémarrage.
