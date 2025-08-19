# Veeam Backup & RestoreVeeam Backup & Replication est un logiciel de **sauvegarde et de reprise d’activité** pour les machines virtuelles **VMware vSphere** et **Microsoft Hyper-V**.

Il prend en charge **le protocole VSS** (Volume Shadow Copy) pour les bases de données Microsoft.



💾 **Fonctionnalités de sauvegarde**

- Sauvegarde **complète** de machines virtuelles
- **Restauration complète** de la VM
- **Restauration granulaire** (fichiers spécifiques, éléments ciblés)



🚨 **Réplication – Récupération d'urgence (Disaster Recovery)**

- Réplication utilisée en cas de **défaillance matérielle** ou de **crash système**
- Permet de **redémarrer rapidement** un réplica
- Réplicas stockés obligatoirement sur **datastore ESXi ou Hyper-V**
- La **taille du réplica = taille exacte de la VM source**
- Ne jamais démarrer un réplica **manuellement** — uniquement via **la console Veeam**



⏱️ **Gestion des RTO (Recovery Time Objective)**

- La gestion des réplicas est essentielle pour limiter le **temps d’interruption maximal toléré**
- Une fois la VM restaurée, possibilité de **fusionner les deux VM** pour éviter toute perte de données

🗂️ **Configuration des supports de stockage**

- Définition des **Backup Repositories** avant toute opération
- Types de stockages compatibles :
  - 📀 Disques locaux
  - 🌐 Partages CIFS / SMB / NFS
  - 🔌 Disques durs externes
- Options disponibles :
  - Sauvegarder **toutes les VM**
  - **Exclure certains disques**
  - Choix du **mode de sauvegarde** :
    - ➕ Incrémental
    - 🔁 Reverse incrémental

🔄 **Backup Copy Job – Copie de sauvegarde distante**

Le **plan de backup Copy** dans Veeam permet de créer **une seconde copie des sauvegardes existantes** sur un **repository distant**

🧩**Duplication régulière** des sauvegardes initiales vers un autre emplacement.



🌐 **External Repository – Sauvegarde dans le cloud**

☁️ Veeam permet d’ajouter un **external repository** chez : **AWS, Azure**, **GCP**



📼 **Sauvegarde sur bandes – Archivage longue durée**

Veeam peut également intégrer un **serveur équipé d’un lecteur de bandes (tape server)** pour :

- **Archivage de long terme**
- **Stockage à froid**, difficilement altérable
- **Sécurité renforcée** contre les attaques (ex : ransomware)



🛠️ **Planification des jobs de sauvegarde**

- Création et gestion des **jobs de sauvegarde/restauration**
- Utilisation d’**onglets clairs** pour configurer facilement les tâches
- Planification automatique des sauvegardes selon des critères définis



📧 **Alertes et notifications**

- Configuration d’**alertes email** pour les rapports de sauvegarde et de réplication
- Affichage quotidien des **rapports dans la boîte mail**
- Importance de **filtrer les alertes** pour ne suivre que les informations critiques
