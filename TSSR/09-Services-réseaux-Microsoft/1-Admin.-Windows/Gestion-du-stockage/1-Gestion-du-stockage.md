# Gestion du stockage**💽 Types de partitionnement : MBR vs GPT**

Deux grands formats pour structurer un disque 🧱 :

- **MBR (Master Boot Record)** : ancien, lié aux BIOS Legacy, limite à 4 partitions primaires (ou 3 + 1 étendue), max 2 To/disque. Faible tolérance aux pannes (tout repose sur le secteur 0).
- **GPT (GUID Partition Table)** : moderne, utilisé avec UEFI, jusqu’à 128 partitions, supporte les disques > 2 To, meilleure résilience (table copiée sur plusieurs secteurs). Recommandé avec OS 64 bits.

⚠️ **Conversion MBR → GPT** possible, mais ⚠️ nécessite un disque vide (ou sauvegardé). 📌 **Cas courant** : un SSD neuf sera déjà en GPT, surtout sur machines récentes.



**🧰 Outils pour gérer les disques sous Windows Server**

- **Gestion des disques (diskmgmt.msc)** : interface graphique simple, clic-droit > initialiser > choisir MBR ou GPT.
- **DiskPart (CMD)** : outil CLI puissant pour script ou dépannage (init, clean, create…).
- **PowerShell** : commandes modernes pour automatiser (ex : Initialize-Disk, New-Partition...).



**🧿 Notion de RAID (Redundant Array of Independent Disks)**

🌐 RAID = regroupement logique de disques pour **perf**, **redondance** ou les deux :

- **RAID 0** : striping, + perf, pas de redondance / **RAID 1** : mirroring, + sécurité (copie exacte)
- **RAID 5** : striping + parité, bon équilibre (min. 3 disques) / **RAID 10** : mirroring + striping, perf + sécurité (min. 4 disques)

🧱 RAID peut être **géré matériellement (BIOS RAID / carte RAID)** ou **logiciellement (via OS ou stockage virtuel)**.

🔧 Sous Windows Server, on peut créer du RAID logiciel via la **Gestion des disques**, mais c’est limité comparé à une vraie solution matérielle.



**💽 Disques de base vs disques dynamiques**

➡️ **Disques de base** : Les plus classiques. Ils utilisent des **partitions** (primaire, étendue, logique). Ex : disque C: pour le système, D: pour les données. Tout repose sur **un seul disque physique**.

➡️ **Disques dynamiques** : Permettent de créer des **volumes** sur plusieurs disques. Utiles pour : RAID logiciel (striped, mirroring, spanning…), fusionner plusieurs disques logiques, créer des volumes étendus. 📌 Nécessaire pour le RAID **logiciel Windows** (RAID 0, 1, 5 sans carte RAID).



**🔄 Conversion entre types de disques**

- **Base → Dynamique** : possible **sans perte de données**. Utile pour RAID logiciel.
- **Dynamique → Base** : ⚠️ nécessite **suppression des volumes** → **perte de données** si non sauvegardé.

📌 Pense à toujours faire une **sauvegarde** avant ce genre d’opérations. 🔧 Exemple de commande en CLI : convert dynamic (à taper dans DiskPart). Pour revenir à un disque de base, tu dois **supprimer tous les volumes** avant.



**📦 Types de volumes dynamiques**

**Volume simple** : classique, comme sur un disque de base. / **Volume fractionné (spanned)** : agrège plusieurs disques, capacité étendue, pas de redondance. / **Volume agrégé par bande (striped)** : RAID 0, + performances, pas de redondance. / **Volume en miroir** : RAID 1, copie exacte entre deux disques. / **Volume RAID-5** : tolérance aux pannes + perf, nécessite 3 disques min (version Server uniquement).
