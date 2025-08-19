# SCSI💡 **Présentation générale**

Le protocole **SCSI (Small Computer System Interface)** est un protocole de communication conçu pour le **transfert de blocs de données**. Il permet un échange fluide et performant entre un système d'exploitation et un périphérique de stockage, en se distinguant notamment par son **indépendance vis-à-vis de l’OS et du matériel** utilisé.



🔄 **Fonctionnement du protocole SCSI**

- Utilise un **mode bloc**, optimisant la vitesse de transfert des données.
- Passe par les **couches de liaison et de transport** pour assurer les échanges.
- Intègre un **contrôleur SCSI**, présent soit sur la **carte mère**, soit en **extension** dans le serveur, chargé de gérer les communications.



📨 **Commandes SCSI**

Les échanges ne concernent pas des fichiers entiers, mais uniquement :

- Des **commandes SCSI** envoyées par l’OS au contrôleur via un canal dédié.
- Des **blocs de données** retournés par le contrôleur en réponse aux commandes.  
  Avantage : **gain de performance** en ne transférant que le nécessaire.



⚠️ **Limites du stockage local en SCSI**

- Performances impactées lors des **sauvegardes massives**.
- **Capacités limitées** selon le matériel (ex. RAID 5 = un disque réservé à la tolérance de panne).
- **Distance physique réduite** entre le serveur et son stockage.
- Création de volumes **dépendante des contraintes matérielles**.



🏢 **Solutions de stockage centralisé / mutualisé**

Pour dépasser ces limites, mise en place de **solutions réseau** telles que :

- **SAN** (Storage Area Network) : mode **bloc**, protocole **iSCSI** ou **Fibre Channel**.
- **NAS** (Network Attached Storage) : mode **fichier**, protocoles **SMB** ou **NFS**.



Ces solutions permettent :

✅ Une **meilleure flexibilité** dans la gestion des ressources de stockage.

✅ Une **meilleure performance** grâce à la mutualisation.

✅ Une **connexion multi-hyperviseurs** à une même source de stockage.
