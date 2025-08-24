# Cmds / Shortcuts

Pour ouvrir un fichier avec Vim, tu peux taper : `vim nom_du_fichier`

Options utiles à connaître :

- **-R** : ouvre le fichier en lecture seule (pratique pour ne pas modifier accidentellement un fichier système).
- **+n** : ouvre le fichier directement à la ligne n (ex : +42 va à la ligne 42).
- **+/motif** : ouvre le fichier en positionnant le curseur sur la première occurrence du mot ou motif recherché.

Tu peux aussi ouvrir **plusieurs fichiers** d’un coup (ex : vim fichier1.txt fichier2.txt) et naviguer entre eux avec :n (suivant) ou :prev (précédent).

## **🖥️ Interface de Vim**

Vim est composé de deux zones :

- **Zone de travail** : là où s’affiche ton texte et où tu écris.
- **Ligne d’information** : tout en bas, elle affiche des infos utiles comme la position du curseur (ex : 7,15 pour ligne 7, colonne 15), ou les messages système.

## **🔀 Les modes de Vim**

| **Mode** | **Fonction principale** | **Pour y accéder** |
|----|----|----|
| Mode normal | Navigation, suppression, copier/coller… | Par défaut ou avec Échap |
| Mode insertion | Écriture de texte | i, a, o, O |
| Mode visuel | Sélection de texte | v (caractères), V (lignes), Ctrl+v (bloc) |
| Ligne de commande | Commandes comme :w, :q, :set number, etc. | Touche deux-points : |

## **🎮 Déplacement rapide du curseur**

- **h, j, k, l** : gauche, bas, haut, droite
- **0** : début de la ligne
- **^** : début du premier mot de la ligne
- **$** : fin de la ligne (fin de fichier?)
- **w** : début du mot ou ponctuation suivant
- **b** : début du mot ou ponctuation précédent
- **e** : fin du mot ou ponctuation suivant
- **gg** : tout début du fichier
- **G** : dernière ligne du fichier
- **:n** : va à la ligne numéro n
- **%** : saute à l’accolade ou parenthèse correspondante
- **/mot** : cherche "mot", puis n pour suivant, N pour précédent

## **💾 Sauvegarde et sortie**

- **:w** : enregistre les modifications
- **:wq** : enregistre et quitte
- **:wq!** : enregistre et quitte, même si normalement c’est en lecture seule
- **:x** : enregistre si besoin et quitte
- **:q** : quitte sans enregistrer (si aucun changement)
- **:q!** : quitte en forçant, sans sauvegarder

## **✍️ Passer en mode insertion**

- **i** : pour insérer du texte avant le curseur
- **a** : pour insérer après le curseur
- **o** : pour créer une nouvelle ligne en dessous et écrire dessus
- **O** : pour créer une nouvelle ligne au-dessus
- **Échap** : pour revenir en mode normal
- **u** : Action précédente


