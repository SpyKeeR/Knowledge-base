# Création LVM**📋 Prérequis avant de créer du LVM**

- Préparer le disque avec **fdisk**.
- Définir la partition en **type 8E** (**Linux LVM**).
- Sauvegarder et redémarrer pour que le système reconnaisse la partition.

👉 Ce type 8E est **obligatoire** pour que LVM fonctionne nickel au redémarrage !



**🔨 Création des Volumes Physiques (PV)**

On convertit nos partitions ou disques en volumes physiques avec **pvcreate**.

| *pvcreate /dev/sdb1 /dev/sdb2 /dev/sdc1 /dev/sdd* | Transforme ces partitions/disques en volumes physiques |
|----|----|

💬 **Astuce** : Même si tu peux utiliser un disque entier, c’est plus propre de partitionner avant !



**🏗️ Création du Groupe de Volumes (VG)**

Ensuite, on crée un **Volume Group** avec **vgcreate**.

| *vgcreate vggroup1 /dev/sdb1 /dev/sdb2 /dev/sdc1* | Crée un VG nommé **vggroup1** avec les 3 PV |
|----|----|

💬 **Astuce** : Mets "vg" dans le nom de ton VG, genre **vgdata**, **vgsystem**… ça aide à s’y retrouver !



**📦 Création des Volumes Logiques (LV)**

Puis on découpe notre espace avec **lvcreate**.

| *lvcreate -n LvHome -L 20G vggroup1* | Crée un LV nommé **LvHome** de 20 Go dans **vggroup1** |
|----|----|

💬 **Astuce** : Utilise "Lv" dans tes noms de volumes logiques (**LvRoot**, **LvSwap**, etc).

**🔍 Accéder aux Volumes Logiques**

Deux chemins possibles pour utiliser nos volumes :

| Classique | /dev/vgSystem/LvHome        |
|-----------|-----------------------------|
| Mapper    | /dev/mapper/vgSystem-LvHome |

On utilise surtout le premier car c’est **plus clair**, mais les deux existent selon les outils que tu utilises.
