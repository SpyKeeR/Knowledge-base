# Partage de déploiement🧱 **Fondamentaux du partage de déploiement**

Le **partage de déploiement** est l’élément central de la configuration de MDT. Il permet de :

- 📦 Héberger les **images de démarrage**
- 💻 Stocker les **systèmes d’exploitation à déployer**
- 🔁 Gérer les **séquences de tâches**
- 🧩 Ajouter des **pilotes** et des **applications à déployer**



🧰 **Création du partage de déploiement**

1️⃣ Lancer la console **Deployment Workbench** (console de gestion de MDT)

2️⃣ Clic droit sur **Deployment Share** → *New Deployment Share*

3️⃣ Indiquer :

- 📁 Le **chemin du dossier de déploiement** (à créer si nécessaire)
- 🏷️ Le **nom et la description du partage**
- 💽 Prévoir un **espace disque suffisant** pour les fichiers lourds (images WIM)



⚙️ **Assistant de configuration**

L’assistant guide à travers plusieurs étapes :

- 🔧 Chaque réponse est traduite en paramètres dans le fichier CustomSettings.ini
  - 📝 Exemple de configuration minimaliste :  
    [Settings]  
    Priority=Default  
    [Default]  
    OSInstall=Y  
    SkipCapture=YES

🛠️ Le fichier peut être modifié manuellement par la suite en cas d’erreur ou d’ajustement.



🎛️ **Personnalisation via les propriétés du partage**

- 📄 **Onglet General** :
  - Activation du **multicast** *(via WDS)*
- 📏 **Onglet Rules** :
  - Configuration des fichiers Bootstrap.ini et CustomSettings.ini
- 💾 **Onglet Windows PE** :
  - Paramétrage des **médias de démarrage** (x86 et x64)
- 📡 **Onglet Monitoring** :
  - Activation du **suivi des déploiements** en temps réel dans la console MDT
