# PartitionnementLe partitionnement est un **processus essentiel** en gestion du stockage, permettant de diviser un disque en plusieurs **espaces logiques indépendants**.



**📌 1️⃣️ Pourquoi partitionner un disque ?**

Partitionner un disque, c'est comme poser des **cloisons dans un entrepôt** pour organiser le stockage de manière distincte. Chaque partition peut avoir :

✅ Un **système de fichiers** différent (NTFS, EXT4, etc.).

✅ Une **utilisation spécifique** (OS, données, sauvegarde...).

✅ Une **meilleure isolation** des données.

Pour que l’ordinateur puisse naviguer entre les partitions, il utilise une **table de partitionnement**, qui est stockée au début du disque (secteur 0).



**🗂️ 2️⃣️Les types de table de partition**



**🔵 MBR (Master Boot Record)**

📌 Ancien format, **compatible avec le BIOS**.

➡️ Max **4 partitions principales** (ou 3 principales + 1 étendue).

➡️ Ne supporte pas les disques de **plus de 2,2 To**.

📌 Stocke la table de partition sur **le secteur 0** (512 octets).

📌 Ne permet pas de démarrer un OS depuis un **lecteur logique**.

💡 **Solution pour contourner la limite des 4 partitions** :

➡️ **Créer une partition étendue** contenant plusieurs **lecteurs logiques**.



**🟢 GPT (GUID Partition Table)**

📌 Format **moderne**, remplaçant MBR.

📌 **Avantages** :

➡️ **Jusqu'à 128 partitions** (Limite de Windows)

➡️ **Taille de partition max : 8/9.4 Zebio/Zettao (256TO Win10 NTFS limit)**

➡️ **Aucune limite de taille de disque** (gère des disques > 2,2 To).

➡️ Stocke la table de partitions sur **plusieurs secteurs** (sécurisation).

📌 **Compatibilité** :

➡️ Nécessite un **firmware UEFI** (non compatible BIOS).

➡️ Fonctionne uniquement avec un OS **64 bits**.

**💿 3️⃣️Disques de Base vs Disques Dynamiques (Windows)**

**📌 Disque de Base**

✅ Partitionnement classique (MBR ou GPT).

✅ 4 partitions max en MBR (ou une étendue + lecteurs logiques).

✅ Pas de fonctionnalités avancées comme l’extension sur plusieurs disques.



**📌 Disque Dynamique**

✅ Introduit par **Windows** pour offrir plus de flexibilité.

✅ Utilise des **volumes dynamiques** au lieu de partitions.

✅ **Avantages** :

➡️ Étendre un volume **même si l’espace libre n’est pas contigu**.

➡️ Étendre un volume **sur plusieurs disques physiques**.

➡️ Compatible avec certaines technologies **RAID**.

✅ Possible de **convertir un disque de base en disque dynamique sans perte de données**.

⚠ **Attention** : Un disque dynamique n'est pas toujours compatible avec d'autres OS.
