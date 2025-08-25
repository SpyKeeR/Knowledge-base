# journalctl

### **📖 Affichage global**

Lancer journalctl sans option affiche **tous les journaux du système**, dans un format lisible avec les touches de navigation de less (haut/bas, recherche /, etc.).

### **⚙️ Configuration**

Le fichier de config de journald est situé ici : /etc/systemd/journald.conf. Tu peux ajuster la rétention, la persistance, la taille max des logs, etc.

### **📺 Suivi en temps réel**

L’option ***-f*** te permet de **voir les logs en direct** (comme `tail -f`). Super utile pour surveiller un service ou un comportement système en live.

### **🎯 Ciblage par service**

Tu veux voir uniquement les logs d’un service (par exemple sshd) ? Utilise ***-u*** nom_du_service → Exemple : `journalctl -u sshd`

### **🔢 Filtrage par PID**

Si tu veux suivre un processus en particulier, utilise le ***_PID=*** : → Exemple : `journalctl _PID=1234`

### **📦 Filtrage par binaire**

Pour obtenir les logs liés à un **exécutable spécifique** (comme /usr/bin/sshd), tu peux le cibler directement dans ta commande.

### **🚨 Filtrage par niveau de priorité**

Voici les niveaux de priorité que tu peux utiliser avec l’option ***-p*** : → Exemple : `journalctl -p err` pour ne voir que les erreurs

| emerg   | 0   | Système inutilisable (urgence absolue) |
|---------|-----|----------------------------------------|
| alert   | 1   | Intervention immédiate requise         |
| crit    | 2   | État critique                          |
| err     | 3   | Erreur non bloquante                   |
| warning | 4   | Avertissement                          |
| notice  | 5   | Notification (non critique)            |
| info    | 6   | Information                            |
| debug   | 7   | Détail pour débogage                   |

### **🧪 Utilisation combinée**

Et le plus cool ? Tu peux **cumuler toutes les options** ! 

Exemple : `journalctl -u sshd -p info -f` → Affiche en temps réel uniquement les messages de niveau informatif du service sshd.

