# Outils**🛠️ 1️⃣️ Outils de Gestion des Disques**

💾 **Windows propose plusieurs outils pour gérer le stockage local**, notamment :

**🔹 Console graphique : Gestion des disques**

📌 Accessible via :

✔ **Clic droit** sur le **menu Démarrer** → **"Gestion des disques"**

✔ **Commande** : diskmgmt.msc

📌 Permet de : ✅ **Initialiser** un disque (création de la table de partitions) ✅ **Partitionner** un disque ✅ **Formater** des partitions ✅ **Étendre** ou **réduire** un volume ✅ **Attribuer une lettre de lecteur** ✅ **Gérer les volumes dynamiques**



**⌨️ 2️⃣ Administration en Ligne de Commande**

🔹 **diskpart** est un outil très utilisé par les techniciens, car il est rapide et fiable en production.

✅ **Démarrer diskpart** : diskpart  
✅ **Lister les disques disponibles** : list disk  
✅ **Sélectionner un disque** : select disk X # Remplace X par le numéro du disque  
✅ **Créer une partition** : create partition primary  
✅ **Formater une partition** : format fs=ntfs quick  
✅ **Attribuer une lettre de lecteur** : assign letter=E  
✅ **Activer une partition** (pour un OS bootable) : active  
✅ **Supprimer une partition** : delete partition

**📜 3️⃣️Gestion des Disques avec PowerShell**

PowerShell permet aussi de gérer les disques via des **cmdlets** :

✅ **Lister les disques** : Get-Disk  
✅ **Initialiser un disque** : Initialize-Disk -Number 1 -PartitionStyle GPT  
✅ **Créer une nouvelle partition** : New-Partition -DiskNumber 1 -UseMaximumSize -AssignDriveLetter  
✅ **Formater une partition** : Format-Volume -DriveLetter E -FileSystem NTFS -NewFileSystemLabel "DATA"
