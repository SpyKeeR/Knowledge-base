# locatelocate est une commande super rapide pour **rechercher des fichiers ou dossiers** par leur **nom ou chemin** dans tout le système. Contrairement à find, elle ne parcourt pas le disque en temps réel, elle utilise une **base de données préindexée**.



**⚙️ Fonctionnement**

- locate s’appuie sur une base de données contenant **tous les chemins de fichiers**.
- Cette base est **mise à jour régulièrement** par le système (souvent via un **cron**).
- Elle rend la recherche **quasiment instantanée**, même sur un gros système.
- Attention : les **nouveaux fichiers ne seront visibles** qu’après une mise à jour de la base.



**🔍 Syntaxe de base >** *locate fichier.txt*

➡️ Retourne **tous les chemins** contenant le motif exact.



**🔧 Mise à jour de la base**

Pour mettre à jour la base manuellement : sudo updatedb

🔁 Utile si tu viens de créer ou déplacer des fichiers et que locate ne les trouve pas encore.

⚠️ Cette commande peut être **automatisée** via un **cron systemd (mlocate.timer)** ou autre service de fond (selon ta distrib : mlocate, plocate, slocate, etc.).



**🧰 Options utiles**

**-i → Ignore la casse (maj/min) >** locate -i fichier.TXT

→ Trouve fichier.txt, FICHIER.TXT, Fichier.Txt, etc.

**-r → Active les expressions régulières POSIX >** locate -r '.*\conf$'

→ Tous les fichiers se terminant par .conf

**-n <nombre> → Limite le nombre de résultats >** locate -n 10 bash

→ Affiche seulement les **10 premiers résultats**



✅ **À retenir** : utilise locate pour chercher **vite**, et find quand tu veux faire des recherches **avancées ou très précises**.



**🧠 Astuces bonus**

- Combine locate avec grep pour **affiner** ta recherche : locate .conf | grep apache
- Exclure certains dossiers (ex : les snapshots ou backups) en modifiant le fichier de config /etc/updatedb.conf : PRUNEPATHS="/tmp /var/spool /mnt/backup"
- locate affiche les **chemins complets**, tu peux l’associer à xargs, head, less, wc -l, etc.

**🆚 locate vs find**

| **locate**                  | **find**                                |
|-----------------------------|-----------------------------------------|
| Super rapide (base indexée) | Plus lent (analyse en temps réel)       |
| Noms et chemins uniquement  | Peut filtrer par date, taille, contenu… |
| Requiert une mise à jour    | Pas besoin de base, toujours à jour     |
| Très simple à utiliser      | Plus puissant mais plus complexe        |
