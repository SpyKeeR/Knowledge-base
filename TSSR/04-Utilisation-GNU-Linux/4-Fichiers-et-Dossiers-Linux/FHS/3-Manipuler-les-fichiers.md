# Manipuler les fichiers

## **✨ Créer un fichier vide**
- `touch fichier.txt`

→ Crée un fichier vide (taille = 0 octet).

🔄 Si le fichier existe déjà, touch met juste à jour sa **date de modification** sans toucher au contenu.



## **📦 Déplacer / Renommer un fichier** 
- `mv source.txt destination.txt`

🔹 Renomme un fichier si le 2e argument est un nom.

🔹 Le déplace si c’est un dossier.

🧩 Options :

- -v : mode verbeux (affiche les actions)
- -f : force le remplacement sans confirmation
- -i : demande confirmation avant d’écraser
- -n : No Clobber (Pas d'écrasement si déjà présent)



## **📋 Copier un fichier** 
- `cp fichier1.txt fichier2.txt`

🧩 Options :

- -v : affiche les actions
- -i : confirmation si le fichier existe
- -r : copie récursive (pour les dossiers)
- -p : Préserver permissions/details
- -n : No clobber (Pas de dossier, pas d'écrasement)



## **🗑 Supprimer un fichier** 
- `rm fichier.txt`

⚠️ **Pas de corbeille** → suppression directe !

🧩 Options :

- -r : suppression récursive (dossiers et contenu)
- -f : force sans confirmation  
  ➡️ rm -rf = **combo très dangereux**. À manier avec précaution !



## **🧹 Supprimer un dossier vide** 
- `rmdir dossier_vide`
