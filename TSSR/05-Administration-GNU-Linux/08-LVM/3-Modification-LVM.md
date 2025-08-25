# Modification LVM

## **📦 Étendre un Groupe de Volumes (VG)**

`vgextend vg-group1 /dev/sdd` → Ajoute /dev/sdd au volume group **vg-group1**

✅ Résultat : plus d’espace pour créer ou agrandir des volumes logiques.



## **📈 Agrandir un Volume Logique (LV)**

`lvextend -l +512M /dev/vg-group/lv1` → Ajoute **512 Mo** à **lv1**
`lvextend -L 1G /dev/vg-group/lv2` → Fixe la taille de **lv2** à **1 Go**

💬 **Astuce** :

- Option -r → redimensionne aussi directement le système de fichiers (ext3/ext4).
- -L [+]size → ajoute une taille définie. (+ permet d'ajouter a la quantité actuelle)



## **⚠️ Réduire un Volume Logique (lvreduce)**

`lvreduce -L 500M /dev/vg-group/lv1` → Réduit la taille de **lv1** à **500 Mo**

**Attention** 🚨 :

- Réduire un LV est **risqué** : risque de **perte de données**.
- Il faut **manuellement** réduire le système de fichiers avant (ex: resize2fs).
- À éviter **si possible** en production.


## **🛠️ Astuce Bonus : Rescan SCSI à chaud**

Quand tu ajoutes un disque sans redémarrer, voici comment forcer le système à le détecter :

| **Étape** | **Commande** | **Commentaire** |
|----|----|----|
| 1. Trouver la chaîne SCSI | `udevadm info --query=path --name=sda` | /devices/00/0000:00:10.0/host0/target0:0:0/0:0:0:0/block/sda |
| 2. Scanner la chaîne SCSI | `echo "- - -" > /sys/class/scsi_host/host0/scan` | Ici, c'est host0, mais adapte selon ce que tu trouves |


