# Image de captureLes **images de capture** permettent de :

- 🧲 Capturer le contenu du disque/partition d’un poste fonctionnel
- 🗃️ Enregistrer cette image dans WDS pour la déployer sur d’autres machines
- 🔁 Réutiliser une **configuration prête à l’emploi** comme base d’installation



🔗 **Association avec une image de boot**

- 🔧 Une image de capture doit être liée à une **image de démarrage (boot)**
- 📥 Créer une image de boot dédiée à la capture dans WDS
- 🖱️ Clic droit > **Créer une image de capture**
- 🏷️ Renseigner un **nom** et un **chemin de stockage local**
- 📁 Sélectionner le **groupe d’images** WDS de destination



🧹 **Préparation du poste à capturer**

- 💡 Utiliser la commande sysprep sur le poste modèle  
    • 🔒 Supprime les identifiants uniques du système  
    • 🔄 Réinitialise la machine comme si elle venait d’être déballée

- 📴 Éteindre le poste après le sysprep



🌐 **Démarrage en PXE et lancement de la capture**

- 🔌 Démarrer le poste en **boot PXE réseau**
- 📦 Sélectionner l’**image de capture** configurée dans WDS
- 🧙‍♂️ L’assistant de capture s’ouvre automatiquement



🎯 **Ciblage et stockage de la capture**

- 💽 Choisir le **volume contenant le système** à capturer (ex : C:)
- 🏷️ Nommer l’image de capture
- 📂 Renseigner :  
    • Le **répertoire local** temporaire de stockage  
    • Le **groupe d’images** WDS pour l’intégration finale



✅ **Finalisation**

- 📤 L’image capturée est **envoyée au serveur WDS**
- ♻️ Elle est ensuite disponible pour le **déploiement automatisé** sur d'autres machines
