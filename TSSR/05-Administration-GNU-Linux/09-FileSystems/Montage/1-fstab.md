# fstab

/etc/fstab ➔ **fichier de configuration** où Linux lit **quoi monter**, **où**, et **comment** dès le **démarrage**.

- 🔥 **Indispensable** : avant toute modif ➔ faire une **sauvegarde** ! (genre cp /etc/fstab /etc/fstab.backup)

Si erreur ➔ Linux risque de **ne plus démarrer correctement** ! 😱



## **🛠️ Structure du fichier /etc/fstab**

Chaque ligne de /etc/fstab suit cette structure :

| **file system** | Source à monter (UUID, LABEL, /dev/xxx) |
|----|----|
| **mount point** | Dossier où on monte |
| **type** | Type du système de fichiers (ext4, ntfs, vfat...) |
| **options** | Options de montage |
| **dump** | Sauvegarde par dump (0 ou 1) |
| **pass** | Priorité de vérification fsck au boot (0(no verif), 1(verif en 1er) ou 2(verif aprés)) |


## **🎯 Comment identifier un volume à monter ?**

| **UUID=** | blkid /dev/sdXN | Immuable même si les disques bougent |
|----|----|----|
| **LABEL=** | blkid ou lsblk -o LABEL | Plus lisible (si labels définis) |
| **/dev/xxx** | /dev/sda1, /dev/nvme0n1p1 | Basique mais peut changer au reboot |


## **⚙️ Principales options de montage (colonne "options")**

| defaults | Options standard (rw, suid, dev, exec, auto, nouser, async) |
|----------|-------------------------------------------------------------|
| exec     | Autorise l’exécution de fichiers binaires                   |
| nouser   | Seul root peut monter/démonter                              |
| ro       | Lecture seule                                               |
| suid     | Permet le comportement set-user-ID                          |
| dev      | Interprète les fichiers spéciaux (périphériques)            |
| auto     | Monte automatiquement au boot                               |
| netdev   | Attend que le réseau soit disponible (utile pour NFS/CIFS)  |

**💡 Astuce** : Combine plusieurs options avec une virgule : defaults,noexec,nouser

### **🛡️ Comment sécuriser avant de redémarrer ?**

- **Ne JAMAIS redémarrer direct** après un changement fstab.
- Toujours **tester** avec : mount -a (ou mount /dev/xxx pour cibler)  
  ➔ Cela tente de monter **tout ce qui est déclaré** dans fstab, sans reboot.  
  ➔ Si erreur ➔ tu la vois tout de suite et tu peux corriger.

