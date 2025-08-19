# LVM/NFS**🧱 LVM (Logical Volume Manager)**

**✅ Pré-requis**

Assure-toi que le paquet lvm2 est installé : apt-get install lvm2

**🧪 Étapes pratiques**

**1️⃣ Créer un volume physique (PV)**

C’est une "brique brute", comme un disque ou une partition. > pvcreate /dev/sdb

**2️⃣ Créer un groupe de volumes (VG)**

On regroupe un ou plusieurs PV dans un "pool" de stockage. vgcreate caleston_vg /dev/sdb

✔️ Tu peux vérifier avec : pvdisplay et vgdisplay

**3️⃣ Créer un volume logique (LV)**

C’est ce que tu vas formater et monter pour l’utiliser. > lvcreate -L 1G -n vol1 caleston_vg

✔️ Vérifie avec : lvs

**4️⃣ Formater et monter**

Tu peux maintenant utiliser ton volume comme un disque classique :

mkfs.ext4 /dev/caleston_vg/vol1  
mount /dev/aleston_vg/vol1 /mnt/vol1

**5️⃣ Redimensionner à chaud (🔥)**

Si besoin, agrandis ton LV (Tips : tu peux resize le fs a la volée avec l'option -r de lvresize) :  
lvresize -L +1G /dev/caleston_vg/vol1  
resize2fs /dev/caleston_vg/vol1

📌 Vérifie avec : df -hP /mnt/vol1

**🧭 Accès à ton volume**

Tu peux y accéder via : /dev/caleston_vg/vol1 OU /dev/mapper/caleston_vg-vol1

Ces deux chemins pointent vers le même volume !



**🌐 NFS (Network File System)**

**🧪 Exemple concret**

Un serveur a ce dossier : /software/repos

Il le partage aux clients via **NFS**, qui peuvent le monter en local et bosser dessus comme s’il était sur leur machine.

**🛠️ Mise en place**

**1️⃣ Export côté serveur**

Tu déclares ce que tu veux partager dans /etc/exports : /software/repos 10.61.35.201 10.61.35.202 10.61.35.203

🔒 Tu peux aussi mettre un réseau (10.61.35.0/24) ou * (risqué)

🔥 Vérifie le pare-feu (ports NFS à ouvrir)

**2️⃣ Activer l’export >** exportfs -a # tout réexporter OU exportfs -o 10.61.35.201:/software/repos # ciblé

**3️⃣ Monter côté client >** mount 10.61.112.101:/software/repos /mnt/software/repos
