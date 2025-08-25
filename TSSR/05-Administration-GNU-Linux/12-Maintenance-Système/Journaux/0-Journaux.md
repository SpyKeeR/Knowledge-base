# Journaux

## **🧩 Les deux systèmes de journalisation**

### 🔹 **journald**

- C’est le système de journalisation **intégré à systemd**.
-  Il enregistre tous les événements dans une **base de données binaire**, accessible facilement via des commandes comme journalctl.



### 🔹 **rsyslog (ex-syslog)**

- C’est l’ancienne méthode, encore utilisée **en parallèle** dans certaines config.
-  Il permet la **compatibilité avec les anciens services** ou outils qui s’appuient encore sur syslog.


🧠 journald et rsyslog peuvent cohabiter pour offrir une **vue plus complète** des logs du système.



## **⚙️ Comment interagir avec journald ?**

Grâce à l’intégration directe dans systemd, on peut interroger les logs avec des commandes comme :

- **Consulter les logs d’un service précis** (ex : SSHD) : `journalctl -u sshd`
- **Filtrer par date, priorité, boot, etc.** : très pratique pour diagnostiquer un souci sans se noyer dans des tonnes d’entrées.



## **🛠️ Et systemctl dans tout ça ?**

Même si systemctl sert principalement à **gérer les services**, il s’appuie aussi sur **journald** pour récupérer des infos. Du coup, quand tu examines un service via `systemctl status nom_du_service`, tu obtiens aussi les **derniers logs** associés à ce service. 

Pratique pour comprendre ce qu’il se passe.

