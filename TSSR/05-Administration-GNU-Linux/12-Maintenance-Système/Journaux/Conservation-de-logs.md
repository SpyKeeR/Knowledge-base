# Conservation de logs**⚙️ Par défaut : journald stocke en mémoire**

- journald utilise une base volatile (dans /run/log/journal)
- Donc : **les logs disparaissent au redémarrage**

🔧 Logs **persistants**, crée un répertoire : mkdir /var/log/journal → journald y écrira automatiquement s’il existe (par défaut sur debian12)



**🧠 Attention à l’espace disque !**

- journald peut prendre **jusqu’à 10%** du FS (modifiable)
- Préfère que /var/log/journal soit **sur une partition séparée**
- Sinon risque de **saturation et crash système**

Tu peux affiner via /etc/systemd/journald.conf :

- SystemMaxUse= → limite globale des logs
- SystemMaxFileSize= → taille max par fichier

⚠️ Si journald est en persistant, tu peux **désactiver rsyslog** pour éviter les doublons.



**🔁 LogRotate : rotation automatique des logs**

- Gère les fichiers logs classiques (/var/log/*.log)
- Rotation configurée dans /etc/logrotate.conf ou dans /etc/logrotate.d/

Exemple pour /var/log/squid/access.log :

- daily → rotation tous les jours
- rotate 366 → garde 366 fichiers (1 an)
- create 640 → fixe les permissions (rw pour proprio, r pour groupe)
- Compression automatique des anciens fichiers (ex: .gz)

✅ Rotation = évite les logs trop gros + conserve l’historique
