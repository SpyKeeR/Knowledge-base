# Performances - Affiche les connexions réseau en **temps réel** (trafic entrant/sortant).
- Classement des IP par trafic.
- Nécessite une installation : apt install iftop

💡 Très utile pour repérer un pic réseau ou une IP suspecte.

**nethogs**

- Classe les processus **par bande passante utilisée**.
- On voit *qui consomme quoi* : ex. Firefox, SSH, apt, etc.
- apt install nethogs
- 

**💾 Disque & I/O**

**iotop**

- Comme top, mais pour **les entrées/sorties disque**.
- Permet de voir quels processus sollicitent le disque.
- apt install iotop (nécessite sudo et un noyau avec le flag CONFIG_TASK_DELAY_ACCT)

**iostat**

- Donne le **taux d’utilisation des disques** et des CPU.
- Fait partie du paquet sysstat.



**🔧 Supervision plus large**

**netstat ou ss**

- Affiche les **ports ouverts**, connexions actives.
- ss -tuln : ports TCP/UDP écoutés

**vmstat**

- Résumé complet mémoire, CPU, swap, I/O disque, etc.

jeudi 24 avril 2025

21:13

**🧠 Outil principal : top**

- Affichage en **temps réel** des processus et ressources.
- Raccourcis utiles :
  - L : active la coloration
  - M : trie par mémoire
  - Q : quitter
  - H : aide intégrée  
    💡 Comparable au gestionnaire de tâches Windows.



**🎨 Alternative plus visuelle : htop**

- Nécessite une installation : apt install htop
- Interface plus claire et interactive.
- Utilisation intuitive, navigable au clavier.



**🧠 Outil avancé : glances**

- Affiche CPU, RAM, disques, réseau, IO, etc.
- Peut fonctionner en **mode client/serveur** (supervision distante).
- Installation via apt install glances



**🔍 Lister les processus : ps**

- ps -ef : vue complète des processus actifs (utilisateur, PID, cmd)

💡Utile pour scripts ou inspection ponctuelle.



**📊 Utilisation mémoire : free -h**

- Donne les valeurs en Mo/Go pour :
  - mémoire totale / utilisée / libre
  - buffers / caches
  - espace swap  
    💡 Ne pas se fier uniquement à la ligne "free" : bien regarder la **mémoire disponible**, qui inclut la mémoire tampon récupérable.
