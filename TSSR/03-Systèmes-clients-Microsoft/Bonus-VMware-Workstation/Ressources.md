# Ressources**⚙️ 1️⃣ Configuration du CPU**

📌 **Chaque VM peut recevoir un nombre défini de processeurs et de cœurs**.

📌 **Plus une VM consomme de CPU, plus elle peut impacter les performances de l’hôte**.

📌 **Attention aux surallocations !** Si l’hôte est surchargé, la VM peut **ralentir** à cause des files d’attente CPU.

📌 **Ajout/Modification du CPU** : Possible seulement **quand la VM est éteinte**.

📌 **VMware Workstation propose des recommandations adaptées** en fonction de l’OS invité.



**🏗️ 2️⃣️Gestion de la Mémoire Vive (RAM)**

🔹 La RAM allouée à une VM est **réservée dès son démarrage**.

🔹 Si l’hôte n’a pas assez de mémoire disponible, **la VM peut ne pas démarrer**.

🔹 En cas de **mise en pause**, la RAM est sauvegardée sur disque, puis restaurée au redémarrage.

🔹 **Éviter la surallocation** : Laisser de la RAM libre pour l’OS hôte et d’autres applications.



**💾 3️⃣️Disque Dur Virtuel (VMDK)**

📌 Un disque dur de VM est **un fichier** sur l’hôte.

📌 **Avantages** :

✅ Facile à **copier, déplacer, compresser ou supprimer**.

✅ **Stockage dynamique** : Le fichier grandit au fur et à mesure des besoins.

📌 **Exemple** :

- Si une VM a un disque alloué de **60 Go**, mais n’utilise que **10 Go**, le fichier ne pèsera que **10 Go**.
- On peut **surallouer** l’espace disque (*overprovisioning*), mais cela peut poser problème si l’hôte manque de stockage.  
  📌 **Précaution** :

- **Une VM ne pourra jamais dépasser la taille de son disque virtuel défini**.
- Si l’hôte manque d’espace, **les VM peuvent ne plus fonctionner correctement**.



**🔥 4️⃣️Bonnes Pratiques**

✅ **Ne pas surcharger l’hôte** en CPU/RAM pour éviter les ralentissements.

✅ **Adapter les ressources à l’OS invité** (VMware donne des recommandations).

✅ **Anticiper le stockage** : Vérifier l’espace disque disponible avant d’ajouter de nouvelles VM.

✅ **Ne pas oublier l’hôte** : Toujours garder des ressources libres pour assurer la stabilité du système.
