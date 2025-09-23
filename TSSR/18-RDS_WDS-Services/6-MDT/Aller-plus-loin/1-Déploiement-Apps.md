# Déploiement Apps

- MDT permet le **déploiement automatisé d’applications** après celui du système
- Prise en charge également du **déploiement de pilotes**
- Fonctionnalité accessible depuis le répertoire **"Applications"** du partage de déploiement



## 🗂️ **Organisation des applications**

- Possibilité de créer une **structure hiérarchique** dans le répertoire *Applications*
- Importation d’applications via un **assistant dédié**
- Choix du type d’application à intégrer :
  - Application locale (sur le serveur MDT) ✅
  - Application sur un **partage réseau**
  - **Groupe d'applications**



## 📝 **Paramétrage lors de l'importation**

- Informations à renseigner :
  - **Nom de l’application**
  - **Numéro de version**
  - **Répertoire de stockage** de l’installeur
  - **Nom du dossier** dans MDT
  - **Commande d’installation silencieuse** (obligatoire)

📌 *Exemple de commande silencieuse* : firefox.exe -ms



## ⚙️ **Configuration de CustomSettings.ini**

- Ajout ou modification du paramètre SkipApplications=NO pour activer la fenêtre de sélection des apps
- Permet de :
  - **Sélectionner manuellement** les applications à déployer
  - **Forcer l'installation** automatique de certaines applications (ex : FileZilla, Firefox)
- Ce fichier centralise les instructions à suivre lors du déploiement

Pour récupérer les GUIDs des applications dans MDT : Clic droit sur l’application > Propriétés > onglet General > copier le champ Application GUID
```ini
SkipApplications=YES/NO
MandatoryApplications001={4e5a6b7c-1d2e-3f4a-5678-abcdef123456}
MandatoryApplications002={6f7e8d9c-2b3a-4c5d-6789-fedcba987654}
Applications001={7a8b9c0d-3e4f-5a6b-7890-112233445566}
```


✅ **Résumé**

- MDT offre une méthode **structurée et automatisée** pour intégrer des applications
- Chaque application doit être **préparée** avec ses métadonnées, son installeur, et une commande **silencieuse**
- La configuration finale se fait via l’édition du fichier CustomSettings.ini pour garantir un **déploiement fluide et sans intervention** 👨‍💻
