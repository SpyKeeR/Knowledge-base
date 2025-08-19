# Ajout image de démarrage📦 **Objectif**

Ajouter une image **WinPE** (boot) pour permettre aux clients de démarrer en PXE et initier le processus de déploiement via WDS.



💿 **Préparation du fichier boot.wim**

- 📥 Monter l’**ISO** ou insérer le **CD d’installation Windows**
- 📁 Naviguer vers le répertoire : \sources\
- 🔍 Localiser le fichier boot.wim
- 📤 Copier ce fichier sur le **serveur WDS**



🖥️ **Importation via la console WDS**

- 📂 Ouvrir la **console WDS**
- 🖱️ Clic droit sur **Images de démarrage**
- ➕ Sélectionner **Ajouter une image de démarrage**
- 📎 Parcourir et sélectionner le fichier boot.wim copié
- ✅ Suivre les étapes de l’assistant jusqu’à la validation finale



📌 **Résumé**

1️⃣ Monter ISO/CD

2️⃣ Extraire boot.wim depuis \sources\

3️⃣ L’ajouter via la console WDS dans le conteneur *Images de démarrage*

🔁 Cette image WinPE sera utilisée à chaque démarrage réseau d’un client PXE.
