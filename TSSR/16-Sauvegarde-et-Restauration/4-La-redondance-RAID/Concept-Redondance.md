# Concept - Redondance📌 **Définition**

La **redondance de données** consiste à **dupliquer** des composants ou des données essentielles d’un système pour en améliorer la **fiabilité** et garantir sa **disponibilité**.

⚙️ **Fonctionnement**

- 🔧 Mise en œuvre via une **configuration matérielle** ou **logicielle**
- 🎯 Objectif : **reconstruire les données** en cas de **défaillance** du système



💽 **Technologie RAID – Redondance par disques durs**

- Utilisation de plusieurs disques organisés en **grappes RAID**
- Permet de **répartir les données** et de compenser la panne d’un disque
- Niveaux RAID :  
   • RAID 0 – Striping sans redondance  
   • RAID 1 – Miroir des données  
   • RAID 5 – Striping avec parité  
   • RAID 6 – Striping avec double parité  
   • RAID 10 – Striping + Mirroring



🖥️ **Redondance via serveurs**

- 🔁 **Réplication en temps réel** des données entre deux serveurs
- ⚙️ Utilisation du **clustering** : si un serveur tombe, l’autre prend le relais automatiquement



🌍 **Redondance multisite**

- Implémentation sur **plusieurs sites géographiques**
- ✅ Résilience face à des incidents majeurs :  
   • Coupure d’électricité ⚡  
   • Incendie 🔥  
   • Inondation 💧

- Le site secondaire prend le relais en cas de sinistre sur le site principal

🔄 **Résumé**

La **redondance de données** est une **stratégie clé** pour assurer la **continuité de service**, éviter la perte d’informations critiques et maintenir la **haute disponibilité** des systèmes.

Elle peut s’appuyer sur des technologies complémentaires :

💽 RAID • 🖥️ Clustering • 🌍 Multisite
