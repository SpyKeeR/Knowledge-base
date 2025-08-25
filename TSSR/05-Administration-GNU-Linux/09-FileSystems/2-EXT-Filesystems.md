# EXT Filesystems

Sous Linux, **les systèmes de fichiers** sont ce qui permet au système d'**organiser** les données sur un disque, une clé USB, un SSD...

Parmi eux, la **famille EXT** est ultra-importante : **EXT2**, **EXT3** et surtout **EXT4**, qui est **le standard sur Debian 10** (et plein d'autres distributions 🐧).



## **🧬 Zoom sur EXT2, EXT3 et EXT4**

- 📜 **EXT2**
  - ➔ Premier de la série : **pas de journalisation**. Si ton PC crash, il doit tout rescanner au redémarrage pour vérifier les fichiers.
- 📜 **EXT3**
  - ➔ **Ajoute la journalisation** 📝. Ça veut dire que le système **garde une trace** des opérations en cours ➔ En cas de plantage, récupération beaucoup plus rapide et fiable !
- 📜 **EXT4**
  - ➔ **Réécriture complète** d'EXT3, pas juste une amélioration.  Résultat ➔ **Plus rapide, plus stable, moins de fragmentation**. C'est celui qu'on utilise **par défaut** aujourd'hui sur Debian et beaucoup d'autres distributions modernes !



## **🌟 Caractéristiques de EXT4**

- 📦 **Taille max d’un fichier** : **16 To** (avec des blocs de 4 Ko)
- 📚 **Nombre max de fichiers** : **4 milliards**
- 📝 **Taille max d’un nom de fichier** : **255 octets**
- 🗄️ **Taille max d’un volume** : théoriquement **1 exaoctet**… ➔ En pratique : **16 To** avec des blocs de 4 Ko.
- ✨ **Préallocation de blocs** ➔ Quand on crée un fichier, EXT4 **réserve d’avance** des blocs proches ➔ **beaucoup moins de fragmentation** = **performances au top** !



## **📚 Autres systèmes de fichiers supportés sur Debian**

Même si on se concentre sur EXT4 ici, Debian sait aussi gérer :

- NTFS (Windows)
- FAT32
- exFAT (clé USB, carte SD)
- XFS (gros volumes, serveurs)
- BTRFS (avancé, snapshots...)
