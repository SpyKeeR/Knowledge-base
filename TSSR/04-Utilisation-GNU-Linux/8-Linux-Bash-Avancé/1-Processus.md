# Processus

## **🧠 C’est quoi un processus ?**

Un **processus** est un programme en cours d’exécution. Il a :

- un **PID** (identifiant unique),
- un **état** (actif, en veille, arrêté…),
- un **propriétaire** (l’utilisateur qui l’a lancé),
- des **ressources** utilisées (CPU, RAM, fichiers…).

## **📊 Surveiller les processus**

- `ps aux` → Liste les processus actifs.
- `top` → Affiche les processus en temps réel.
- `htop` (si installé) → Version interactive de top.

## **🛠️ Gérer les processus**

- `kill 1234` → Tue le processus avec le PID 1234.
- `killall nginx` ou pkill → Tue tous les processus nommés nginx.
- `nice -n 10 commande` → Lance une commande avec une priorité faible.
- `renice 5 -p 1234` → Modifie la priorité d’un processus déjà lancé.



## **🌀 États possibles d’un processus**

- **R (Running)** : en cours ou prêt à s’exécuter.
- **S (Sleeping)** : en attente (d’un input ou événement).
- **T (Stopped)** : arrêté manuellement ou par un signal.
- **Z (Zombie)** : terminé, mais pas encore “nettoyé” par son parent.

## **🔄 Exécuter en arrière-plan**

- `commande &` → Lance un processus en arrière-plan.
- `jobs` → Affiche les tâches en arrière-plan.
- `fg %1` → Ramène le job 1 au premier plan.
- Ctrl + Z → Suspend un processus.
- `bg %1` → Relance un processus suspendu en arrière-plan.



## **📣 Utiliser les signaux**

- `kill -15 PID` → SIGTERM : arrêt propre.
- `kill -9 PID` ou *kill -kill PID*→ SIGKILL : arrêt forcé.
- `kill -19 PID` → SIGSTOP : suspend.
- `kill -18 PID` → SIGCONT : reprend.
- `kill -1 PID` → SIGHUP : Arrêt de session
- `kill -2 PID` → SIGINT : Ctrl+C



## **⏱️ Mesurer le temps d'exécution**

- `time commande` → Affiche :
- **real** : durée totale écoulée,
- **user** : temps CPU utilisé en mode utilisateur,
- **sys** : temps CPU utilisé par le noyau.



## **🔒 Garder un processus actif même après fermeture du terminal**

- `nohup commande ` → Continue même après déconnexion.
- `nohup commande > sortie.log 2>&1 &` → Redirige proprement la sortie et les erreurs.

