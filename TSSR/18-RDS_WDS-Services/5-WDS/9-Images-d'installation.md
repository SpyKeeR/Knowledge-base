# 📦 **Objectif**

Importer une image install.wim pour permettre le **déploiement automatisé de systèmes d’exploitation** sur les postes clients via WDS.



## 💿 **Préparation du fichier install.wim**

- 📥 Monter l’**ISO** ou insérer le **CD d’installation Windows**
- 📁 Naviguer vers : `sources\`
- 🔍 Repérer le fichier `install.wim`
- 📤 Copier ce fichier sur le **serveur WDS** *(facultatif mais recommandé)*

## 🖥️ **Importation via la console WDS**

- 📂 Ouvrir la **console WDS**
- 🖱️ Clic droit sur **Images d’installation**
- ➕ Cliquer sur **Ajouter une image d’installation**
- 🧱 L’image doit être associée à un **groupe d’images**
  - 🔸 Le groupe peut être créé à la volée si inexistant

- 📎 Indiquer le chemin du fichier install.wim  
  - 🔸 Cela peut être :  
    - ✔ le fichier du média Windows  
    - ✔ une image personnalisée capturée  
    - ✔ toute autre image WIM valide
- ✅ Suivre les étapes de l’assistant jusqu’à la validation finale


## 📑 **Sélection des éditions à importer**

- 📚 Le fichier install.wim contient **plusieurs éditions** de Windows (Home, Pro, N, etc.)
- ✅ Sélectionner uniquement les **versions utiles**
  - 💼 En entreprise, privilégier les éditions **Pro ou Entreprise**



## 📌 **Résumé**

- 1️⃣ Accéder à install.wim via ISO ou CD
- 2️⃣ L’ajouter dans la console WDS via un assistant
- 3️⃣ Associer à un groupe d’images
- 4️⃣ Choisir les éditions pertinentes pour le déploiement

🚀 Cela permet de centraliser et automatiser l’installation de Windows sur tous les postes du réseau.
