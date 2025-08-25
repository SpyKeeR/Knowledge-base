# Présentation

## **📚 Contexte : Pourquoi LVM ?**

Quand on utilise le **partitionnement classique** (legacy), agrandir une partition est super **galère** :

❌ Besoin de passer **hors ligne** (PC éteint)

❌ Besoin de **place libre** juste à côté de la partition

❌ Système **rigide** et limité…

👉 Avec **LVM**, tout devient **fluide** et **modulable** 🛠️ !



## **🧠 C'est quoi LVM ?**

**LVM** (**Logical Volume Manager**) est une **couche logique** au-dessus des disques :

➡️ Tu peux **ajouter**, **agrandir**, **réduire** ton espace disque **à chaud** sans te prendre la tête 💪 !

C'est un peu comme les **disques dynamiques** de Windows, mais **en mieux** sous Linux.



## **🔑 Les 3 éléments clés :**

| PV  | Physical Volume | Disque ou partition de base                        |
|-----|-----------------|----------------------------------------------------|
| VG  | Volume Group    | Fusion de plusieurs PVs en un grand espace         |
| LV  | Logical Volume  | Zone logique où installer les systèmes de fichiers |



## **🛠️ Créer une config LVM**

| Initialiser un volume physique | pvcreate /dev/sda1                    |
|--------------------------------|---------------------------------------|
| Créer un groupe de volumes     | `vgcreate VGsystem /dev/sda1 /dev/sda2` |
| Créer un volume logique        | `lvcreate -n LVroot -L 10G VGsystem`    |

**Exemple concret** :

➡️ Tu peux créer des volumes comme **LVroot**, **LVhome**, **LVvar**, chacun accueillant ses données !



## **🔎 Commandes utiles pour gérer LVM :**

| Créer un PV, VG, LV            | pvcreate, vgcreate, lvcreate    |
|--------------------------------|---------------------------------|
| Afficher l’état                | pvdisplay, vgdisplay, lvdisplay |
| Étendre un volume ou un groupe | lvextend, vgextend              |

