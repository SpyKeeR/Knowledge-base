# FileSystems / Outils**💾 Systèmes de fichiers : FAT32 vs NTFS**

Petit match ⚔️ entre deux systèmes bien connus :

- **FAT32** : ancien standard, ultra-compatible, mais limité → Fichier max : **4 Go**, Partition max : **2 To**
- **NTFS** : actuel standard Windows → Fichier max : **256 To**, Sécurité (droits NTFS), compression, journalisation, chiffrement
- **ReFS** (mentionné mais moins utilisé) : orienté stockage à haute résilience, usage serveur

👉 En environnement pro/Windows, **NTFS est incontournable** pour ses fonctionnalités avancées.



**🧱 Étapes d’initialisation & formatage**

1.  **Initialisation** du disque (choix MBR/GPT)
2.  **Création** des partitions (primaire, logique)
3.  **Formatage** (choix du système de fichiers)
4.  **Utilisation** (écriture des données)

🧠 Tant qu’un disque n’est pas initialisé, il n’est pas exploitable par l’OS.

Une fois les partitions créées → il faut formater pour qu’elles soient montées et utilisables.



**🛠️ Outils de gestion des disques**

Voici les outils à connaître absolument :

- **diskmgmt.msc** → interface graphique MMC (via Windows + R)
- **diskpart** → console CMD, gestion avancée (init, part, format…)
- **PowerShell Storage Module** → moderne, scriptable :  
  ex. Get-Command -Module Storage pour voir toutes les commandes liées aux volumes, partitions, disques.

📌 Chaque outil a son avantage :

- GUI : visuel, rapide
- Diskpart : scriptable, accessible en mode sans échec
- PowerShell : automatisation, déploiement massif, cloud-ready
