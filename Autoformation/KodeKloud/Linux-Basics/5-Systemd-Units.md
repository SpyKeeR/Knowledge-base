# 5 - Systemd Units📂 **Localisation des fichiers d’unités Systemd**
- Les fichiers .service, .target, .timer… sont stockés dans :
  - /etc/systemd/system/ : personnalisations locales (prioritaires)
  - /usr/lib/systemd/system/ : unités installées par les paquets
  - /run/systemd/system/ : unités temporaires (runtime)

🔁 Priorité : /etc > /run > /usr/lib

📌 Tu devrais toujours mettre les unités custom dans /etc/systemd/system/.



🧱 **Structure d’un fichier d’unité**

Un fichier unit est divisé en 3 sections principales :

🧩 **[Unit]**

- Description= : résumé de l’unité
- Documentation= : lien vers doc (optionnel)
- After= : ordre de démarrage relatif (ex : network.target)

🔧 **[Service]**

- ExecStart= : commande à exécuter pour lancer le service
- User= : user sous lequel tourner (utile pour services non-root)
- Restart= : comportement en cas d’échec (on-failure, always, no, etc.)
- RestartSec= : délai (sec) avant relance

📌 **[Install]**

- WantedBy= : précise le target auquel le service est lié (ex : multi-user.target)



🔄 **Recharger les unités et modifier les fichiers**

- **systemctl daemon-reexec** ➜ relance *systemd* lui-même (rare)
- **systemctl daemon-reload** ➜ recharge les fichiers d’unité modifiés  
  ⚠️ À faire **après** modif manuelle d’un fichier .service

📝 **systemctl edit --full nom.service** ➜ ouvre le fichier complet dans l’éditeur, te permet de le modifier proprement (génère une copie dans /etc si nécessaire)

🧰 **Arguments systemctl essentielles**

- start nom.service → démarre le service
- stop nom.service → l’arrête
- restart nom.service → redémarre
- reload nom.service → recharge la config (sans redémarrer)
- enable nom.service → active au boot
- disable nom.service → désactive au boot
- status nom.service → affiche état du service



📊 **Statuts possibles d’un service**

- active ➜ service actif et fonctionnel
- inactive ➜ service arrêté
- failed ➜ erreur au démarrage ou crash



📋 **Lister les unités**

- **systemctl list-units --all** ➜ affiche toutes les unités (actives, inactives, failed…)



📜 **Journalctl : lecture des logs**

- **journalctl** ➜ tout le journal
- **journalctl -u nom.service** ➜ logs d’un service spécifique
- **journalctl -b** ➜ logs du **dernier boot**
