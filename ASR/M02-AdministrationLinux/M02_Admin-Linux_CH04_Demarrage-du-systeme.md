# M02 - Administration Linux

## CH04 - Démarrage du système

Les systèmes GNU/Linux se distinguent par la **transparence** de leur processus de démarrage. Chaque étape est clairement définie et observable.

---

### 4.1 - Chargeur d'amorçage (boot loader) : GRUB2

Le chargeur d'amorçage est le **tout premier programme** exécuté par le BIOS (legacy ou UEFI). Sa mission principale : lancer le (ou un des) noyau(x) installé(s) sur le disque.

Sous RHEL, le boot loader est **GRUB2**. Il se divise en plusieurs stages :

| Stage | Emplacement |
| --- | --- |
| Stage 1 | Master Boot Record (MBR), tout premiers secteurs du disque |
| Stage 1.5 | Début du disque, avant les données de la première partition |
| Stage 2 | Répertoire `/boot/grub2/` |

💡 GRUB est rarement modifié manuellement en production, mais il est utile pour gérer le multiboot sur une plateforme personnelle.

#### 📁 Fichiers de configuration

Le fichier principal de GRUB est :

```bash
/boot/grub2/grub.cfg
```

⚠️ **Ne jamais éditer ce fichier directement.** Il est regénéré automatiquement (notamment à chaque mise à jour du noyau).

Les modifications se font dans :

- `/etc/default/grub` : paramètres et options globales
- `/etc/grub.d/` : scripts de configuration spécifiques (détection d'autres OS, etc.)

🔧 Après modification, regénérer la configuration :

```bash
# grub2-mkconfig -o /boot/grub2/grub.cfg
```

📂 Le répertoire `/boot/loader/entries/` contient les fichiers servant à la construction du menu GRUB.

#### 🔧 Forcer l'affichage du menu GRUB (RHEL 8+)

Par défaut sur RHEL 8+, le menu GRUB peut être masqué si le dernier boot a réussi. Pour forcer son affichage, deux actions sont nécessaires :

**1. Modifier `/etc/default/grub`** :

```bash
GRUB_TIMEOUT=3
GRUB_TIMEOUT_STYLE=menu
```

**2. Supprimer la variable d'environnement GRUB qui masque le menu** :

```bash
# grub2-editenv list              # Lister les variables env de GRUB
# grub2-editenv - unset menu_auto_hide   # Supprimer la variable de masquage auto
```

⚠️ C'est une spécificité RHEL 8+ : la variable `menu_auto_hide` est positionnée dans l'environnement GRUB et empêche l'affichage du menu si le boot précédent a réussi. Même si `GRUB_TIMEOUT` est configuré, le menu ne s'affichera pas tant que cette variable existe.

🔧 Ne pas oublier de regénérer la config après modification :

```bash
# grub2-mkconfig -o /boot/grub2/grub.cfg
```

---

### 4.2 - GRUB au démarrage

Quand le menu GRUB est affiché, on peut :

- Sélectionner un noyau ou un mode de démarrage avec les touches `↑↓`
- Appuyer sur `e` pour **éditer** la ligne de lancement du noyau sélectionné (modifications temporaires)
- Appuyer sur `Ctrl+x` pour **démarrer** avec les modifications

📌 Par défaut, RHEL conserve un maximum de **4 noyaux** dans le menu.

---

### 4.3 - Le noyau Linux

Le noyau (kernel) fait le lien entre le matériel (via le BIOS) et le système d'exploitation. Il est dit **modulaire** car constitué de plusieurs fichiers.

#### 📁 Fichiers du noyau dans `/boot/`

Deux fichiers sont indispensables au démarrage :

| Fichier | Rôle |
| --- | --- |
| `vmlinuz-<version>` | Image principale du noyau |
| `initramfs-<version>.img` | Image secondaire, regénérée à chaque mise à jour. Contient la config spécifique au matériel et les options importantes (RAID, chiffrement, pilotes FS, etc.) |

Le noyau a la charge de :

- Gérer les systèmes de fichiers
- Gérer le chiffrement logiciel
- Charger les pilotes de périphériques
- Lancer le gestionnaire de système **systemd**

#### 🔗 Constitution de la ligne de lancement du noyau

```bash
linux ($root)/vmlinuz-4.18.0-xxx root=/dev/mapper/ol-root ro crashkernel=auto
  resume=/dev/mapper/ol-swap rd.lvm.lv=ol/root rd.lvm.lv=ol/swap rhgb quiet
```

Détail des paramètres :

| Paramètre | Rôle |
| --- | --- |
| `($root)/vmlinuz-xxx` | Image noyau à charger |
| `root=/dev/mapper/ol-root` | Système de fichiers contenant `/` |
| `ro` | Montage en lecture seule (nécessaire pour la vérification d'intégrité du FS) |
| `crashkernel=auto` | Activation de kdump (dump mémoire en cas de kernel panic) |
| `resume=/dev/mapper/ol-swap` | Emplacement des données de mise en veille prolongée |
| `rd.lvm.lv=ol/root` | Activation des volumes LVM spécifiés |
| `rhgb` | Chargement graphique (Plymouth) |
| `quiet` | Lancement non verbeux |

---

### 4.4 - Le gestionnaire de système : systemd

`systemd` remplace les scripts System V depuis RHEL 7. C'est le **premier programme lancé par le noyau** (PID 1).

Ses responsabilités :

- Lancer tous les programmes/services dans un ordre spécifique (démarrage **parallèle** avec gestion des dépendances)
- Mettre à disposition les systèmes de fichiers via les points de montage

📂 Les fichiers de service sont dans `/lib/systemd/system/`.

🔁 RHEL 8 conserve une compatibilité avec les services de type System V.

#### 4.4.1 - La notion de cible (target)

Les cibles définissent quels services doivent être exécutés ou arrêtés selon le mode de fonctionnement souhaité.

| Cible | Rôle |
| --- | --- |
| `poweroff.target` | 🔴 Arrêt du système |
| `rescue.target` | 🛠️ Mode maintenance |
| `multi-user.target` | 🖥️ Mode console (pas d'interface graphique) |
| `graphical.target` | 🖼️ Mode graphique |
| `reboot.target` | 🔄 Redémarrage |

#### 4.4.2 - Commandes de gestion systemd

##### 🎯 Cible par défaut

```bash
# systemctl get-default            # Voir la cible actuelle
# systemctl set-default multi-user.target   # Changer la cible par défaut
```

##### 📋 Lister les unités

```bash
# systemctl list-units             # Unités chargées et actives
# systemctl list-units --all       # Toutes les unités (y compris inactives)
```

##### 🔍 Statut d'un service

```bash
# systemctl status NetworkManager
```

💡 Appuyer sur `q` pour quitter l'affichage du statut.

##### ✅ Activer / Désactiver un service

```bash
# systemctl enable smb.service     # Active au démarrage (crée un lien symbolique)
# systemctl disable smb.service    # Désactive au démarrage (supprime le lien)
```

📌 L'activation crée un lien symbolique dans `/etc/systemd/system/`. Sur RHEL et dérivés, les services installés ne sont **pas systématiquement activés** par défaut.

##### ▶️ Démarrer / Arrêter un service

```bash
# systemctl start bind9
# systemctl stop bind9
# systemctl start service1 service2    # Plusieurs services à la fois
```

##### 🔀 Changer de cible à chaud

```bash
# systemctl isolate multi-user.target
```

---

### 4.5 - Fichiers et configurations personnalisés

| Emplacement | Rôle |
| --- | --- |
| `/usr/lib/systemd/system/` | Fichiers de service d'origine (ne pas modifier ici) |
| `/etc/systemd/system/` | Fichiers personnalisés (prioritaires sur ceux de `/usr/lib/`) |
| `/etc/systemd/system/default.target` | Lien symbolique vers la cible par défaut |

⚠️ Pour modifier un service : copier son fichier de `/usr/lib/systemd/system/` vers `/etc/systemd/system/` puis modifier la copie. Le fichier dans `/etc/` est **toujours prioritaire**.

💡 Pour créer un service custom : placer le fichier `.service` dans `/etc/systemd/system/` puis l'activer avec `systemctl enable`.

---

### 4.6 - Éteindre / redémarrer le système

Même si `systemctl isolate` peut éteindre/redémarrer, la commande `shutdown` est recommandée en production car elle permet de :

- Planifier l'heure de l'opération
- Envoyer un message aux utilisateurs connectés

```bash
shutdown [option] <heure> <message>
```

| Option | Effet |
| --- | --- |
| `-h` | Arrêter le système |
| `-r` | Redémarrer |
| `-c` | Annuler un shutdown programmé |

---

### 🧪 Ateliers associés

- **Atelier 3** : Configuration de GRUB (affichage menu, édition temporaire du noyau, accès rescue sans mot de passe)
- **Atelier 4** : Gestion des services (temps de boot avec `systemd-analyze`, changement de cible, exploration des services ssh/cron/firewalld)

---

### 📌 À retenir

1. **GRUB2** est le boot loader de RHEL. Ne jamais éditer `/boot/grub2/grub.cfg` directement, modifier `/etc/default/grub` puis regénérer.
2. Sur **RHEL 8+**, penser à supprimer la variable `menu_auto_hide` avec `grub2-editenv` pour que le menu s'affiche.
3. Le noyau a besoin de deux fichiers : `vmlinuz` (image) et `initramfs` (config matérielle).
4. **systemd** (PID 1) gère les services et les cibles. `systemctl` est la commande centrale.
5. Les fichiers dans `/etc/systemd/system/` sont **prioritaires** sur `/usr/lib/systemd/system/`.
