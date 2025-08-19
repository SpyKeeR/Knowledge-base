# 2 - Concepts clés Linux- **Applications / Processus** ↔ tournent en **User Space**
- **Linux Kernel** gère : mémoire, CPU, périphériques
- Pilotes matériels (device drivers), gestion des appels système (ex : open(), close(), strlen()), sécurité
- 2 types de noyaux : **Monolithique** (tout intégré) / **Modulaire** (modules chargeables dynamiquement)



🧬 **Versions du noyau Linux**

- **uname -r** → ex : 4.15.0-72-generic ➜ 4 = version du kernel ➜ 15 = version majeure ➜ 0 = mineure ➜ 72 = patch release ➜ generic = spécifique à la distro



⚙️ **Kernel Space vs User Space**

- **Kernel Space** : gère le matos, les pilotes, la mémoire, les appels système
- **User Space** : les applis que tu lances, tes scripts, interfaces CLI/GUI
- **Communication via syscall** : read(), write(), readdir()…
- Ex : insérer une clé USB → Kernel déclenche un **uevent** → udev crée /dev/sdb1 dans User Space



🛠️ **Outils pour interagir avec le noyau et le matos**

- **dmesg | grep -i usb** : messages du noyau (détection périphériques, erreurs)
- **udevadm info --query=path --name=/dev/sda5** : infos sur le chemin PCI complet
- **udevadm monitor** : affiche les uevents en temps réel
- **lspci** : liste les périphériques PCI
- **lsblk** : liste les blocs (disques, partitions)
- **lsmem --summary**, **free -m** : état mémoire (total, utilisé, libre)
- **lscpu** : infos CPU (sockets * cores * threads = nb CPU logiques)



🚀 **Séquence de démarrage Linux**

- **BIOS POST** → **GRUB2** → **Décompression/init du Kernel + initrd** → **Processus init**

**→ /sbin/init pointe vers : systemd ou SysVinit**



🎯 **Runlevels et Targets systemd**

| 0     | Halt/poweroff      | poweroff.target   |
|-------|--------------------|-------------------|
| 1     | Rescue/single user | rescue.target     |
| 2,3,4 | Multi-user CLI     | multi-user.target |
| 5     | GUI                | graphical.target  |
| 6     | Reboot             | reboot.target     |

➡️ **systemctl get-default** / **set-default** pour afficher ou modifier la target ➡️ Lien symbolique : /etc/systemd/system/default.target



🗂️ **Types de fichiers Linux**

- **file** ➜ affiche le type d’un fichier ➜ Types : **Character** (ex : terminal), **Block** (disques), **Regular**, **Directory**, **Hard Link**, **Soft Link**, **Named Pipe**, **Sockets**, **Special**

📁 **Structure FHS**

| **/bin**   | Commandes essentielles               |
|------------|--------------------------------------|
| **/boot**  | Fichiers de démarrage (GRUB, initrd) |
| **/dev**   | Fichiers spéciaux pour périphériques |
| **/etc**   | Config système                       |
| **/home**  | Dossiers utilisateurs                |
| **/lib**   | Bibliothèques partagées système      |
| **/media** | Montages temporaires (USB/CD)        |
| **/mnt**   | Montages manuels                     |
| **/opt**   | Logiciels additionnels               |
| **/tmp**   | Fichiers temporaires                 |
| **/usr**   | Programmes utilisateurs + libs       |
| **/var**   | Données variables (logs, spool…)     |
