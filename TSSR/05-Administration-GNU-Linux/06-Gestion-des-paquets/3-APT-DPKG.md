# APT/DPKG

## **🛠 Préparer la gestion des paquets**

Avant tout, vérifie ton fichier /etc/apt/sources.list.

Ensuite, retiens que tu utiliseras principalement trois commandes : `apt`, `apt-get` et `apt-cache`.

- `apt` est plus moderne, il gère mieux les dépendances.
- `apt-get` est plus ancien mais toujours utilisé.
- `Conseil :` reste fidèle à l'un des deux pour éviter les conflits internes.

⚡ aptitude existe mais n’est plus installé par défaut depuis Debian 10.



## **🔄 Gestion des paquets - Tableau récapitulatif**

|     Action                                       |     Commande                              |     Commentaire rapide                          |
|--------------------------------------------------|-------------------------------------------|-------------------------------------------------|
| Mettre à jour la base de données                 | apt[-get] update                          | Préfère apt update pour plus de confort         |
| Mettre à jour les paquets (et supr. obsolète)    | apt[-get] upgrade (ou full[dist]-upgrade) | Met à jour tous les paquets (supr. obsolète)    |
| Installer un paquet                              | apt[-get] install nom_du_paquet           | Peut installer plusieurs paquets d’un coup      |
| Désinstaller un paquet                           | apt[-get] remove nom_du_paquet            | Ne touche pas aux fichiers de config            |
| Désinstaller complètement (purge)                | apt[-get] purge nom_du_paquet             | Supprime aussi les fichiers de config           |
| Nettoyer le cache local                          | apt[-get] clean                           | Libère de la place                              |
| Chercher un paquet                               | apt[-cache] search mot_clef               | Pour trouver un paquet                          |
| Afficher les détails d'un paquet                 | apt[-cache] show nom_du_paquet            | Pour voir la description, dépendances…          |
| Voir les paquets installés                       | dpkg -l                                   | Liste tous les paquets du dépôt local           |
| Lister les fichiers installés par un paquet      | dpkg -L nom_du_paquet                     | Super utile pour voir où sont les fichiers      |
| Trouver à quel paquet appartient un fichier      | dpkg -S /chemin/vers/fichier              | Indispensable pour du dépannage                 |
| Installer un paquet .deb                         | dpkg -i /chemin/vers/paquet.deb           | Installation manuelle d'un .deb                 |

## **🗃️ Consulter l'historique des paquets**

- Historique apt/apt-get : /var/log/apt/history.log
- Historique dpkg : /var/log/dpkg.log

👉 Pratique pour comprendre ce qui a été installé, mis à jour ou supprimé.

