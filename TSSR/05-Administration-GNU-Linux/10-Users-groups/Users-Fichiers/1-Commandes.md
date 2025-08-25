# Commandes

## **➕ useradd – Ajouter un utilisateur**

Commande de base : `useradd [options] nom_utilisateur`

**Options principales :**

- -m : Crée le répertoire personnel (par défaut /home/nom_utilisateur).
- -M : Empêche la création automatique du home (même si CREATE_HOME=yes).
- -d /chemin : Spécifie un chemin personnalisé pour le home.
- -b /base : Change le répertoire de base pour le home (par défaut /home).
- -u UID : Définit un UID spécifique.
- -g GID : Définit le groupe principal.
- -G groupe1,groupe2 : Groupes secondaires.
- -s /chemin/shell : Définit le shell de connexion.
- -k /chemin/skel : Utilise un dossier "squelette" différent de /etc/skel.
- -r : Crée un utilisateur système (UID inférieur à 1000).

Avec -m, le contenu du /etc/skel (ou skel via -k) est copié dans le home.



## **❌ userdel – Supprimer un utilisateur**

Commande de base : `userdel [options] nom_utilisateur`

**Option importante :**

- -r : Supprime le rép.personnel (home)

## **🔐 passwd – Gérer les mots de passe**

Commande de base : `passwd [options] [nom_utilisateur]`

**Options à connaître :**

- -e : Force l’expiration immédiate du mot de passe → l’utilisateur devra le changer à la prochaine connexion.
- -l : Verrouille le mot de passe (désactive temporairement le compte).
- -u : Déverrouille un mot de passe verrouillé par -l.

Sans argument → change le mot de passe de l’utilisateur courant.

En root → permet de changer celui de n’importe quel utilisateur.

## **✏️ usermod – Modifier un utilisateur**

Commande de base : `usermod [options] nom_utilisateur`

**Options utiles :**

- -c "Commentaire" : Modifie le champ GECOS (ex : nom complet).
- -d /nouveau/home : Change le répertoire personnel.
- -m : Déplace les fichiers vers le nouveau home (à combiner avec -d).
- -e YYYY-MM-DD : Date d’expiration du compte.
- -f N : Nombre de jours d’afk après expiration du PW avant disable.
- -g groupe : Change le groupe principal.
- -G groupe1,groupe2 : Remplace tous les groupes secondaires.
- -aG groupe1,groupe2 : Ajoute à la liste existante des groupes.
- -l nouveau_login : Change le nom de l’utilisateur.
- -s /chemin/shell : Change le shell.
- -u UID : Change l’UID.
- -L : Verrouille le compte (empêche la connexion).
- -U : Déverrouille le compte.

