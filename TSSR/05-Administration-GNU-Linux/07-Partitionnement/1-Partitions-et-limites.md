# Partitions et limites

## **📚 Comment Linux voit les disques**

- Un disque est vu comme un **périphérique de type bloc**.
- Il est listé dans /dev :
  - Pour les **disques IDE** ➡️ /dev/hda, /dev/hdb, etc.
  - Pour les **disques SCSI/SATA/USB** ➡️ /dev/sda, /dev/sdb, etc.

🔎 Exemple : /dev/sda ➔ premier disque détecté - /dev/sdb ➔ deuxième disque, et ainsi de suite.

### **🧱 Partitionnement — MBR**

- **MBR (Master Boot Record)** ➡️ utilisé historiquement par IBM en 1983, pour les PC **architecture x86 Intel**.
- MBR = premiers **512 octets** du disque :
  - **446 octets** ➔ code du bootloader (ex: GRUB)
  - **64 octets** ➔ table de partition
- Limites du MBR :
  - **4 partitions principales max** ❗
  - Pour contourner : créer 1 partition **étendue** ➡️ qui peut contenir **jusqu’à 56 partitions logiques** (limite de l'outil **fdisk**), 63 en théorie.
  - Taille max d'une partition : **2,2 To** ➡️ à cause de l’**adressage 32 bits des secteurs** dans la MBR.



### **🚀 GPT — La solution moderne**

- **GPT (GUID Partition Table)** ➔ introduit en 1993 pour dépasser les limites de MBR.
- GPT = architecture moderne :
  - **128 partitions** par défaut ➡️ parfois **256** suivant les systèmes et outils.
  - Taille max théorique : **9,4 zettaoctets (Zo)** (merci à l’**adressage 64 bits** 🎯).

🧠 Attention : ces limites (128/256 partitions et 9,4 Zo[8 Zio]) **dépendent de l’OS** et des outils utilisés, pas juste du format GPT lui-même.



## **🔢 Numérotation des partitions**

- Partitions **primaires** et **étendues** ➔ numéros **1 à 4** (/dev/sda1, /dev/sda2, etc.)
- **Partitions logiques** ➔ **toujours à partir de 5** (/dev/sda5, /dev/sda6, etc.).
- Une partition **étendue** est juste **un conteneur** ➡️ elle n'est pas utilisée directement pour stocker des fichiers.



