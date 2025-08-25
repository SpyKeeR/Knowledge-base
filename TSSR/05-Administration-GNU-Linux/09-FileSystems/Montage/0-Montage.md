# Montage

Monter un système de fichiers = **le rendre accessible** dans l’arborescence Linux.

Concrètement :

- Sous Windows ➔ ça apparaît en lettre (D:, E:…).
- Sous Linux ➔ ça utilise un **dossier** (/mnt, /media/usb…).

**Important** : toujours utiliser un **dossier vide** pour éviter de cacher des données déjà présentes.

## **📁 Où monter ses volumes ?**

| /mnt   | Points de montage **temporaires**                  |
|--------|----------------------------------------------------|
| /media | Points de montage **automatiques** (USB, CD, etc.) |

**Astuce** 💡 : Tu peux créer tes propres sous-dossiers dans /mnt (ex: /mnt/disque1).



## **⚙️ Monter un système de fichiers manuellement**

Commande de base : `mount -t TYPE /dev/nom_périphérique /chemin/point_de_montage`

Exemple : `mount -t ext4 /dev/sdc1 /mnt`

- Ici : -t ext4 ➔ Précise que le système de fichiers est **ext4**.
- /dev/sdc1 ➔ Partition que l’on veut monter.
- /mnt ➔ Dossier qui servira de point d’accès.



## **🎛️ Options utiles de mount**

On peut ajouter des options avec -o, séparées par des virgules :

| sync | Force l’écriture **immédiate** sur le disque (plus sûr, mais plus lent) |
|----|----|
| suid | Autorise les permissions SUID pour certains programmes spéciaux |
| exec | Autorise l’exécution de programmes depuis ce système de fichiers |
| remount | **Re-monte** un volume déjà monté (modifie ses options sans démonter) |
| ro | Monte le système de fichiers en **lecture seule** |
| rw | Monte le système de fichiers en **lecture/écriture** |

Exemple avec options : `mount -t ext4 -o rw,sync /dev/sdc1 /mnt`



## **👀 Voir les systèmes de fichiers montés**

- Méthode 1 : `mount` ➔ Affiche **tout** ce qui est monté (affichage brut). 
- Méthode 2 : `findmnt /mnt` ➔ Affiche de manière plus propre.



## **🔥 Démonter un système de fichiers**

Quand tu as fini d’utiliser un disque ou une partition ➔ il faut démonter proprement !

Commandes possibles : 
- `umount /mnt` ➔ Démonte par le **point de montage**.
- `umount /dev/sdc1` ➔ Démonte par le **nom du périphérique**.

🛑 Attention : si un fichier du volume est encore utilisé, umount renverra une erreur.
