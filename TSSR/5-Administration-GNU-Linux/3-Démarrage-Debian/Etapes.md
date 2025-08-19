# Etapes**1️⃣ Phase POST (Power-On Self-Test)**

- 🧠 Au tout premier allumage, la carte mère lance le **POST**. Son job ? Vérifier le **hardware de base** (RAM, CPU, clavier, écran...) ✅.

**2️⃣ BIOS ou UEFI + BootOrder**

- Le **BIOS** (ancien) ou **UEFI** (moderne) gère la **BootOrder** 📋 : Il décide **dans quel ordre** essayer de démarrer les périphériques

🔵 **Si BIOS** :

- Le BIOS va chercher un **MBR** (**Master Boot Record**) sur le disque système principal.
- Le **MBR** fait **512 octets** et contient :
  - Le **bootloader** de première étape (Soit dans 446 octets du MBR, soit en redirigeant vers le VBR de la partition active)
  - La table de partitions + Signature disque

🔵 **Si UEFI** : L'UEFI lit directement la **partition EFI** (formatée en FAT32). Elle contient un **boot manager** et les exécutables de démarrage (**.efi**)

**3️⃣ Chargement du Bootloader (GRUB2)**

- Après BIOS+MBR ou UEFI+EFI 👉 **GRUB2** prend le relais 🚀.
- GRUB2 est : 🛠️ Un **BootManager** (il propose un menu) ET ⚙️ Un **BootLoader** (il charge le système)

📜 GRUB2 est en deux parties : **Stage 1** dans le MBR et **Stage 2** dans **/boot/grub/**

**4️⃣ GRUB charge le noyau et l'initramfs**

- GRUB2 va charger en mémoire : **vmlinuz** → Le noyau Linux compressé 🛡️ **initrd** ou **initramfs** → Système de fichiers temporaire 📦

✍️ Explication rapide : **initrd** ➔ Ancienne méthode basée sur un fichier de **bloc**. / **initramfs** ➔ Actuelle méthode basée sur un **tmpfs** en RAM et des fichiers **cpio**.

**5️⃣ Décompression du noyau et initialisation**

- **vmlinuz** se décompresse automatiquement en RAM 💥.
- Il exécute ensuite **initramfs/initrd** pour :
  - Charger les **modules du noyau** (drivers de disques, systèmes de fichiers, etc.).
  - Monter un **système de fichiers temporaire** en RAM.

**6️⃣ Montage du système de fichiers racine (pivot_root)**

- Dès que les bons drivers sont chargés :
  - Le **vrai disque** contenant **le système Debian** est monté 🔗.
  - Un **pivot_root** a lieu : Le FS temporaire **initramfs** est effacé de la RAM 🧹. Le noyau transfère la racine du système vers le vrai disque dur.

**7️⃣ Lancement de systemd**

- Une fois le vrai FS en place, **vmlinuz** lance **/sbin/init**. (Sur Debian moderne, **/sbin/init** est un lien vers **systemd** 🔥)

**Systemd** va alors : Lire ses fichiers de config. Charger les services selon les **targets** (ex : graphical.target, multi-user.target).

**8️⃣ Authentification utilisateur (PAM & Shell)**

- Quand tout est prêt : Le système lance **PAM (Pluggable Authentication Modules)** pour gérer la connexion 🔒.
  - Si tu te connectes en console : **login** démarre. Une fois loggué 👉 Ton **shell** est lancé (bash, zsh, etc.) 🎉.

📜 Fichier de config GRUB: *cat /boot/grub/grub.cfg*

⚠️ Attention : **NE JAMAIS modifier directement grub.cfg** !

👉 Il est **régénéré automatiquement** lors des mises à jour du noyau.

**🛠️ Modifier GRUB**

- Tu modifies **/etc/default/grub** pour changer la conf 🛠️.
- Des scripts sont aussi utilisés dans **/etc/grub.d/**.

Une fois prêt, tu régénères la conf avec : *sudo grub-mkconfig -o boot/grub/grub.cfg* Ou, plus simple sur Debian : *sudo update-grub*
