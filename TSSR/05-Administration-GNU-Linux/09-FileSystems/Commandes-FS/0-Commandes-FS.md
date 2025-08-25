# Commandes FS

## **🏗️ Comment on formate ?**

- `mkfs.ext4 /dev/sdc1` ➔ Formater en EXT4 (utilise mke2fs en interne)
- `mkfs.ntfs /dev/sde1` ➔ Formater en NTFS (si paquets installés)
**Petit +** : On peut ajouter directement un **label** (nom du volume) avec certaines options !



## **🎛️ Modifier un système de fichiers sans tout casser**

Pour **modifier** un système de fichiers **déjà existant**, on utilise `tune2fs`.

| -L NOM | Changer ou ajouter un **label** | `tune2fs -L home /dev/sdc1` |
|----|----|----|
| -l | Lister toutes les **infos** du superbloc | `tune2fs -l /dev/sdc1` |
| -i TEMPS | Définir un **intervalle de vérif** automatique | `tune2fs -i 1m /dev/sdc1 (1 mois)` |
| -c NOMBRE | Définir un **nbre max de montages** avant check | `tune2fs -c 20 /dev/sdc1 (après 20 montages)` |

**Pourquoi c'est utile ?** ➔ Pour éviter de te retrouver avec un disque défectueux sans t'en rendre compte ! 🛡️



## **📏 Redimensionner un système de fichiers**

Quand tu modifies **la taille** d'une partition ou d'un volume logique, pense à ajuster ton système de fichiers avec **resize2fs**.

`resize2fs /dev/sdX1` ➔ Redimensionner le système de fichiers à la nouvelle taille

**Important** ➔ Ce n’est **pas automatique** sauf indication contraire. Toujours vérifier après un changement de taille !



## **🧹 Vérifier et corriger un système de fichiers**

Si tu soupçonnes un problème sur un disque ➔ **Check et réparation** avec **fsck** :

`fsck /dev/sdX1` ➔ Vérifie et tente de corriger le système de fichiers |

**fsck** est super utile après : Une coupure de courant ⚡ Un arrêt brutal du serveur 💥 Des erreurs système suspectes 🧐



## **🔍 Récupérer des infos sur les volumes**

### Commande **blkid** :
Affiche **les infos détaillées** comme : 
- **UUID** (identifiant unique)
- **Label** (nom donné)
- **Type de système de fichiers**

`blkid` ➔ Voir UUID, label, type, etc.


### Commande **lsblk** :
Affiche **l'arborescence** des disques et partitions.

- `lsblk`    ➔ Vue graphique simple (pas de label/UUID)
- `lsblk -f` ➔ Vue avec **labels et UUID** 


