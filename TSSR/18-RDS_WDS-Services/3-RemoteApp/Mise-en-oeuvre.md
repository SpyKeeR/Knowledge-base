# Mise en oeuvre📁 **Étapes de mise en œuvre des RemoteApp**

1.  Ouvrir le **Gestionnaire de serveur**
2.  Naviguer vers **Services Bureau à distance > Collections**
3.  Sélectionner la **collection existante** cible 🎯
4.  Aller dans la section **PROGRAMMES REMOTEAPP**
5.  Cliquer sur **Tâches > Publier des programmes RemoteApp** 📦



🔍 **Listage automatique des applications**

• Le serveur **scanne le disque dur** pour détecter les programmes installés 🔎💽

• L'administrateur peut **sélectionner dans la liste** les applications à publier (ex : LibreOffice) 📋✅



➕ **Ajout manuel d'applications**

• Si une application n’est pas détectée automatiquement, on peut l’**ajouter manuellement** 🖱️📂

• Cliquer sur **Ajouter** et **parcourir l’arborescence** pour sélectionner le fichier exécutable de l’application

🔧 **Exemple de commande PowerShell**

Add-RDRemoteApp -CollectionName "MaCollection" -Alias "LibreOffice" -DisplayName "LibreOffice" -FilePath "C:\Program Files\LibreOffice\program\soffice.exe"
