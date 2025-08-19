# 6 - Gestion du stockage**🧱 Block Devices : Les bases du stockage**

Un **block device** c’est tout périphérique de stockage qu’on accède par blocs (pas par flux comme un CD audio par exemple). Sur Linux, tu les vois dans /dev :

lsblk # Liste les disques et partitions  
ls -l /dev/ | grep "^b" # Liste les fichiers de type block Ex : /dev/sda, /dev/sda1, etc.

Chaque périphérique a un **major number** (type du périphérique) et un **minor number** (numéro unique sur le système).



**🧩 Disk Partitions : Organisation logique du disque**

Un disque (ex: /dev/sda) peut être divisé en **partitions** :

- /dev/sda1: souvent /boot/efi
- /dev/sda2: un répertoire de stockage comme /media/...
- /dev/sda3: racine (/)

📌 **Astuce** : On peut utiliser fdisk -l ou lsblk pour afficher les partitions.

Tu as deux schémas de partitionnement :

- **MBR** (2 To max, 4 partitions primaires)
- **GPT** (ultra flexible, pas de limite réelle de partitions ni de taille)

**Types :**

- **Primaire** : directement utilisable pour le boot
- **Étendue** : contient des partitions logiques
- **Logique** : dans une étendue (pour contourner la limite des 4 primaires)



**⚒️ Créer une partition (GPT) avec gdisk**

Tu bosses avec un disque vierge /dev/sdb ? Voici les étapes : sudo gdisk /dev/sdb  
> n # créer une nouvelle partition  
> W # écrire les changements

Tu obtiens /dev/sdb1.



**🗂 Les systèmes de fichiers EXT2 / EXT3 / EXT4**

Un **système de fichiers** transforme une partition en espace structuré exploitable par le noyau.

| **Système** | **Journalisation** | **Taille max fichier** | **Remarques** |
|----|----|----|----|
| ext2 | ❌ | 2 To | Rapide, pas tolérant aux pannes |
| ext3 | ✅ | 2 To | Ajoute un journal (sécurité) |
| ext4 | ✅ | 16 To | Le plus utilisé aujourd’hui |

**🛠 Créer et monter un système de fichiers EXT4**

sudo mkfs.ext4 /dev/sdb1 # Formatage en EXT4  
sudo mkdir /mnt/ext4 # Point de montage  
sudo mount /dev/sdb1 /mnt/ext4 # Montage manuel



**🔁 Montage automatique (au boot) : /etc/fstab**

Ajoute une ligne dans /etc/fstab : /dev/sdb1 /mnt/ext4 ext4 defaults 0 2

Utilise blkid pour récupérer l'UUID à la place du nom (/dev/sdb1), c’est plus fiable.



**Résumé rapide 🧠**

| **Commande**        | **Utilité**                 |
|---------------------|-----------------------------|
| lsblk               | Voir les disques/partitions |
| fdisk -l /dev/sdX   | Voir ou modifier MBR        |
| gdisk /dev/sdX      | Gérer les disques en GPT    |
| mkfs.ext4 /dev/sdXN | Créer un FS EXT4            |
| mount               | Monter manuellement         |
| /etc/fstab          | Montage auto au démarrage   |
