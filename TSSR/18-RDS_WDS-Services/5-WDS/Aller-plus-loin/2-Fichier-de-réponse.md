# Fichier de réponse

Les fichiers de réponse permettent :

- 🤖 D’automatiser totalement l’installation d’un système via WDS
- ✅ De répondre automatiquement aux différentes étapes du déploiement
- 🧩 De supprimer l’intervention manuelle durant l’installation



## 🛠️ **Méthodes de création**

Plusieurs approches possibles :

- 📄 Adapter un **exemple de fichier unattend.xml** à ses besoins
- 🧪 Utiliser PowerShell : `New-ShieldingDataAnswerFile` depuis la PowerShell Gallery
- 🧰 Utiliser **WSIM** (Windows System Image Manager) disponible dans **ADK**
- 📑 Les fichiers contiennent des paramètres configurant **toutes les étapes de l’installation**



## 📍 **Positionnement possible**

### 1️⃣ **Lié à une image de démarrage (boot)**

- Si le fichier contient des infos pour la phase **windowsPE** (étape 1)
- 🔐 Le fichier **unattend.xml** doit être placé dans :  
    `\\<Serveur_WDS>\REMINST\WDSClientUnattend`
- ⚙️ Configuration via l’onglet **Client** dans la console WDS
- 📦 Le fichier est appliqué lors du **chargement de l’image de boot** selon l’architecture sélectionnée

### 2️⃣ **Lié à une image d’installation**

- Si le fichier contient des infos pour les **étapes 2 à 7** du processus d’installation
- 🔧 Sur l’image d’installation ciblée > onglet **Général** :  
  - Cocher **"Autoriser l’image à s’installer sans assistance"**
  - Indiquer le chemin du fichier `unattend.xml` à utiliser
- 🚀 Le fichier est appliqué **pendant le déploiement** de cette image



## 🎯 **Résumé visuel**

🖥️ **WDS seul** → 🧾 **Fichiers de réponse (unattend.xml)**

🧪 Création via : PowerShell / WSIM / [Schneegans](https://schneegans.de/windows/unattend-generator/)

📌 Placement :

- 1️⃣ Image de démarrage → étape **windowsPE**
- 2️⃣ Image d’installation → étapes **2 à 7**

📤 Appliqué automatiquement pour **déploiement sans intervention**
