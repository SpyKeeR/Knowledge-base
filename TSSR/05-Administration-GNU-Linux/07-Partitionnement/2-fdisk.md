# fdisk


Pour partitionner nos disques sous Linux, on a plusieurs outils :

- `fdisk` ➡️ en ligne de commande 🖥️
- `[g]parted` ➡️ en ligne de commande aussi \[et graphique]



## **🚀 Comment utiliser fdisk simplement**

### **🔍 Voir la table de partitions**

- Pour voir **toutes** les partitions de **tous** les disques : `fdisk -l`
- Pour voir **uniquement** celles d'un disque particulier (ex : /dev/sda) : `fdisk -l /dev/sda`

### **✏️ Modifier un disque**

Pour bosser directement sur un disque : `fdisk /dev/sdb`

👀 Tu entres alors dans un mode **interactif** :

- Tape m ➡️ pour **afficher l'aide** 📜
- Plein de raccourcis rapides dispo !



## **🧩 Commandes importantes en mode interactif**

| m   | Afficher le menu d’aide                                |
|-----|--------------------------------------------------------|
| p   | Voir la table des partitions actuelle                  |
| n   | Créer une nouvelle partition                           |
| d   | Supprimer une partition                                |
| t   | Changer le type d'une partition                        |
| a   | Activer/désactiver le drapeau bootable                 |
| w   | Écrire (sauvegarder) les changements sur le disque     |
| q   | Quitter **sans** enregistrer ❌                        |
| v   | Vérifier la table de partitions pour des incohérences  |
| x   | Activer les fonctions avancées (experts uniquement 🧙) |

## **✍️ Créer une partition avec fdisk**

1.  Entrer dans l’outil : `fdisk /dev/sdb`
2.  Tape n ➡️ pour créer une nouvelle partition :

    - Choix entre **primaire** (p) ou **étendue** (e).
    - Choisir **le numéro** de la partition (1, 2, 3, 4).
    - Choisir **le premier secteur** ➡️ (par défaut, laisse vide pour auto).
    - Choisir **la taille** ➡️ (ex : +20G pour 20 Go).

3.  Tape p pour **vérifier** la table des partitions 📋
4.  Tape t pour **changer le type** si besoin :

    - Exemple : 83 ➡️ Linux natif
    - Exemple : 8e ➡️ LVM

5.  Tape w pour **enregistrer** les changements 💾  
    (Si tu t'es trompé ➡️ q pour quitter sans rien enregistrer.)

