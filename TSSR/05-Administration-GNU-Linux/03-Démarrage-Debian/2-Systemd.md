# Systemd

## **1️⃣ Qu'est-ce que systemd ?**

- C'est **le premier processus** lancé après le noyau ➔ Il porte **l'ID 1**.
- **But principal** : gérer tout le démarrage et la vie des services 🔥. 🛠️ Depuis **Debian 8**, systemd a remplacé les anciens scripts **SystemV**.



## **2️⃣ Que fait systemd ?**

- Il **démarre** les programmes du système. Il **gère** les programmes comme des **services** (démarrage, arrêt, redémarrage...).
- Il **surveille** leur état (actif, en erreur, etc.). Il s'appuie sur des **unités** stockées dans /etc/systemd/system/ (admin) et /lib/systemd/system (système/APT)



## **3️⃣ Notions clés : Targets et Services**

🔵 **Targets** (= cibles) : Une **target** est une collection de **services** à activer ensemble. Permet d'atteindre un **état du système** (ex: multi-user, graphical, rescue...).

💡 **Important :** Sous Debian 10 ➔ **graphical.target** est la cible par défaut, même sans interface graphique installée.

🔵 **Services** : Un **service** est un programme qui tourne en arrière-plan (daemon). Chaque service est géré par un fichier .service.



## **4️⃣ La commande maître : systemctl**

| **Commande** | **Rôle** |
|----|----|
| systemctl get-default | Voir la cible par défaut |
| systemctl set-default multi-user.target | Changer la cible par défaut |
| systemctl isolate rescue.target | Passer en mode maintenance |
| systemctl list-units | Voir les unités actives |
| systemctl list-units --all | Voir toutes les unités (actives + inactives) |
| systemctl status [service] | Voir l'état d'un service |
| systemctl start [service] | Démarrer un service |
| systemctl stop [service] | Arrêter un service |
| systemctl restart [service] | Redémarrer un service |
| systemctl enable [--now] [service] | Activer [et démarrer] un service |
| systemctl disable [--now] [service] | Désactiver [et arreter] un service |



## **5️⃣ Comprendre les unités de systemd**

| .service | Un service (Apache, SSH, etc.)               |
|----------|----------------------------------------------|
| .target  | Une cible (multi-user, graphical, rescue...) |
| .socket  | Communication réseau/IPC                     |
| .device  | Périphérique matériel (clé USB, disque...)   |

