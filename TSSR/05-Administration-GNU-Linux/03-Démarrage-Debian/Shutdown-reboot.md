# Shutdown / reboot**

## 1️⃣ Pourquoi c’est important ?**

- Un système Linux, ça **se démarre proprement**, mais **ça s’arrête aussi proprement** !
- Objectif : **éviter de casser** des fichiers ou corrompre le système 🚑.


## **2️⃣ La commande principale : shutdown**

✅ `shutdown` permet :

- **d’arrêter** (halt) le système
- **de redémarrer** (reboot) le système
- **de planifier** un arrêt ou un redémarrage
- **d’envoyer des messages** aux utilisateurs connectés



## **3️⃣ Arrêter le système**

➡️ **Arrêt immédiat** : `shutdown -h now`

- -h = halt ➔ arrêter / now = tout de suite 🚀



## **4️⃣ Programmer un arrêt**

➡️ **Arrêt dans 10 minutes** : `shutdown -h +10` "Arrêt du système dans 10 minutes"*

- +10 ➔ dans 10 minutes / Le message sera affiché aux utilisateurs 📢.



## **5️⃣ Redémarrer le système**

➡️ **Redémarrage immédiat** : `reboot`

➔ Simple, rapide, efficace ⚡

➡️ **Planifier un redémarrage** : `shutdown -r 16:30`

- -r = reboot ➔ redémarrage / 16:30 ➔ à 16h30 pile 🕟



## **6️⃣ Annuler un shutdown en attente**

➡️ **Annuler un arrêt ou redémarrage programmé** : `shutdown -c`

