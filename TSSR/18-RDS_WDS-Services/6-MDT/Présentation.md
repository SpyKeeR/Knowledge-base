# Présentation🧰 **Définition et rôle de MDT**

- MDT est un **ensemble d’outils gratuits** permettant d’**automatiser le déploiement** des postes de travail.
- Il ne remplace pas WDS, mais le **complète** en apportant des **fonctionnalités avancées**.
- Contrairement à WDS (qui est un rôle serveur), MDT est un **logiciel téléchargeable**.



🗂️ **Fonctionnalités avancées de MDT**

- Création de **partages de déploiement** contenant :  
   📦 Images d’installation  
   📂 Images de démarrage générées par MDT (LiteTouchPE)  
   🧩 Séquences de tâches  
   ⚙️ Fichiers de configuration (ADF1) pour l’automatisation

- MDT dispose de **sa propre arborescence de fichiers** indépendante de celle de WDS.



🔗 **Interconnexion entre MDT et WDS**

- MDT **ne gère pas le démarrage réseau** (PXE). Il s’appuie sur **WDS pour la diffusion** des images de démarrage.
- Le processus se déroule ainsi :  
   1️⃣ WDS envoie l’image de démarrage (LiteTouchPE) générée par MDT  
   2️⃣ Le poste la charge en RAM  
   3️⃣ L’environnement LiteTouchPE se connecte au **partage MDT  **
   4️⃣ Le poste télécharge l’**image d’installation** via MDT

📋 **Étapes du déploiement avec MDT**

- **Étape 1** :  
   📡 Le poste démarre via PXE  
   📥 Récupère l’image de démarrage fournie par WDS

- **Étape 2** :  
   📤 Le poste contacte MDT pour récupérer l’image d’installation à déployer



🔄 **Les séquences de tâches**

- 🔁 Listes d’**actions automatisées à exécuter** dans l’ordre (format chronologique)
- ⚙️ Permettent une **personnalisation poussée** et un **contrôle complet** sur le processus
- 📦 Exemples : partitionnement du disque, déploiement OS, installation logiciels, etc.
