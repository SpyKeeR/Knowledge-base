# APT/DPKG**🛠 Préparer la gestion des paquets**

Avant tout, vérifie ton fichier /etc/apt/sources.list.

Ensuite, retiens que tu utiliseras principalement trois commandes : **apt**, **apt-get** et **apt-cache**.

- **apt** est plus moderne, il gère mieux les dépendances.
- **apt-get** est plus ancien mais toujours utilisé.
- **Conseil :** reste fidèle à l'un des deux pour éviter les conflits internes.

⚡ aptitude existe mais n’est plus installé par défaut depuis Debian 10.



**🔄 Gestion des paquets - Tableau récapitulatif**



[TABLE]



**🗃️ Consulter l'historique des paquets**

- Historique apt/apt-get : /var/log/apt/history.log
- Historique dpkg : /var/log/dpkg.log

👉 Pratique pour comprendre ce qui a été installé, mis à jour ou supprimé.
