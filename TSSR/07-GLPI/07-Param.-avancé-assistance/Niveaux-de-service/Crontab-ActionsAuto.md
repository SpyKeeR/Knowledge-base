# Crontab - ActionsAuto**⏳ Qu’est-ce que la crontab dans GLPI ?**

La crontab, c’est une tâche planifiée dans Linux qui lance régulièrement un script. Pour GLPI, c’est le script qui va scanner les tickets, déclencher les escalades, envoyer des mails, mettre à jour les états… Sans elle, rien ne bouge automatiquement.



**⚙️ Vérifier et éditer la crontab**

La crontab qu’il faut regarder est celle de l’utilisateur **www-data**, parce que GLPI tourne souvent sous cet utilisateur web (Apache/Nginx).

- **Commande pour éditer la crontab** :  
  crontab -u www-data -e  
  (le -u www-data signifie "modifier la crontab de l’utilisateur www-data")



**📝 Ligne à vérifier ou ajouter**

Il faut que cette ligne soit présente, ou bien tu peux l’ajouter si elle n’existe pas :

* * * * * /usr/bin/php8.8 /var/www/glpi/front/cron.php >/dev/null 2>&1

- Le * * * * * signifie que la tâche s’exécute **toutes les minutes** (c’est important pour que les escalades se déclenchent rapidement).
- /usr/bin/php8.8 est le chemin vers PHP (ajuste selon ta version de PHP installée).
- /var/www/glpi/front/cron.php est le script de GLPI qui réalise toutes les actions planifiées.
- >/dev/null 2>&1 signifie que tu ne veux pas afficher les sorties ou erreurs dans le terminal, ça nettoie la sortie.



**🔄 Redémarrer et vérifier**

Après modification, tu peux vérifier la crontab avec :

crontab -u www-data -l

Ça te liste les tâches planifiées pour www-data, tu dois voir ta ligne.

Pas besoin vraiment de "redémarrer" un service pour la crontab, mais il faut s’assurer que le cron tourne bien sur ta machine (service cron ou crond selon ta distro).



**🎯 Pourquoi c’est crucial ?**

Si cette crontab n’est pas configurée ou mal configurée :

- Les **escalades ne fonctionnent pas** (pas d’alerte sur tickets en retard)
- Pas de **notifications** automatiques envoyées
- Pas de mise à jour automatique des statuts ou synchronisation
- Bref, GLPI perd son automatisation et devient quasi manuel, ce qui casse tout l’intérêt d’un ITSM.
