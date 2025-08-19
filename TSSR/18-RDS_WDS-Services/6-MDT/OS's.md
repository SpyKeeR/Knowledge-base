# OS's📥 **Importation des systèmes d’exploitation**

MDT ne contient aucun système d’exploitation par défaut. Il est indispensable d’en importer manuellement pour permettre le déploiement.

🔹 Méthodes d’importation disponibles :

- 💿 **Depuis un média d’installation Windows  **
  → Sélectionner le chemin vers le DVD ou ISO d'installation

- 📁 **Depuis un fichier install.wim localisé manuellement  **
  → Nécessite un téléchargement préalable depuis une source fiable

- 🌐 **Depuis un serveur WDS (Windows Deployment Services)  **
  → MDT doit être configuré pour interagir avec le service WDS

📌 Pour lancer l’importation :

📂 Clic droit sur le dossier **Operating Systems** → **Importer** → Suivre l’assistant



🧱 **Gestion des fichiers WIM**

Les fichiers WIM (Windows Imaging Format) peuvent contenir **plusieurs images** d’OS (ex : Pro, Education, Enterprise...).

✔️ MDT permet de :

- 🎯 **Sélectionner uniquement les images utiles**
- 🧹 **Supprimer les versions non désirées** pour alléger et simplifier les déploiements

🗂️ **Association au partage de déploiement**

Chaque image importée doit être **liée au partage de déploiement MDT** pour pouvoir être utilisée dans les séquences de tâches.



📌 **Résumé**

Une bonne gestion des systèmes d’exploitation dans MDT permet de :

- Optimiser les déploiements
- Sécuriser les sources d’installation
- Simplifier la structure des images disponibles
- Gagner du temps lors de la création de séquences de tâches  
  ✅ Une étape incontournable pour une infrastructure de déploiement efficace et bien organisée.
