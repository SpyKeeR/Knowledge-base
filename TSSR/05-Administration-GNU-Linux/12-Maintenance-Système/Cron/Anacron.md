# Anacron🔧 **/etc/crontab**

C’est le fichier utilisé pour lancer les tâches **système planifiées**. Il est un peu spécial, car contrairement aux fichiers crontab utilisateur (crontab -e), il **nécessite d’indiquer explicitement l’utilisateur** qui exécute la tâche. Par exemple : 25 6 * * * root …

De plus, il **ne charge pas automatiquement l’environnement** de l’utilisateur. Donc si ta commande dépend de certaines variables (comme PATH), il faut les **définir manuellement** en haut du fichier. Exemple : PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin



🚦 **Comment cron délègue à anacron**

Dans /etc/crontab, il y a souvent une ligne qui dit : 25 6 * * * root test -x /usr/sbin/anacron || { cd / && run-parts --report /etc/cron.daily; }

Traduction :

- Tous les jours à 6h25, cron vérifie si le fichier /usr/sbin/anacron existe (avec test -x).
- S’il **n’existe pas**, alors cron **exécute lui-même** les scripts de /etc/cron.daily.
- S’il **existe**, le || fait que la commande **ne s’exécute pas** → donc **cron laisse anacron faire le boulot.**



🔁 **Pourquoi utiliser Anacron ?**

Cron fonctionne à des horaires **précis**. Donc si ta machine est **éteinte** à 6h25, **la tâche est perdue**.

Anacron, lui, est fait pour ça :

- Il garde en mémoire la **date de la dernière exécution** dans des fichiers comme /var/spool/anacron/cron.daily
- Quand tu rallumes ton PC, il vérifie s’il y a **du retard** sur une tâche (daily, weekly, monthly) en comparant la date actuelle avec celle de son fichier horodaté.
- Si le délai est dépassé, **il relance la tâche**, même si le PC n’était pas allumé au moment prévu.

👉 Même si le PC est allumé en continu, anacron **continue de surveiller** l’écart de temps entre deux exécutions.

Si le délai prévu est passé, **il exécute la tâche quand même**.



⚙️ **Mais qui lance anacron ?**

Anacron(non installé par défaut sur serveur) **n’est pas un service (daemon)** comme cron. Il doit **être lancé par un autre processus**.

Avant, c’était **cron lui-même** qui appelait anacron via un script dans /etc/cron.daily/ ou /etc/cron.d.

Aujourd’hui, sur les systèmes modernes (**Debian, Ubuntu, Fedora, etc.**), c’est **systemd** qui prend le relais via un **timer** systemd.

Un timer systemd va dire par exemple :

- Lance anacron 5 minutes après le boot (OnBootSec=5min)
- Puis relance-le toutes les 24h (OnUnitActiveSec=1d)

Tu peux vérifier ça avec la commande : systemctl list-timers | grep anacron
