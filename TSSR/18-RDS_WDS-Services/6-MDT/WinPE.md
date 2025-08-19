# WinPE🔧 **Rôle de Windows PE**

Windows PE (Preinstallation Environment) est un mini système d’exploitation utilisé pour :

- 🛠️ **Préparer un poste** avant l’installation complète de Windows
- 🚀 **Initialiser les séquences de tâches** de MDT
- 📦 Charger une **image système principale** à déployer

Il est généré à partir du **partage de déploiement** MDT et constitue un élément essentiel du processus.



📂 **Formats d’image de boot disponibles**

- 🗂️ **WIM** : utilisé pour un **boot réseau (PXE)**
- 💿 **ISO** : utilisé pour un **boot local via média physique** (DVD, clé USB)  
  → Permet de démarrer un poste même sans réseau disponible.

Les deux formats sont générés pour les architectures **x86 (32 bits)** et **x64 (64 bits)**.



📁 **Stockage et structure**

- Les fichiers d’amorçage (WIM et ISO) sont situés dans le dossier **Boot** de l’arborescence du **partage de déploiement MDT**.



⚙️ **Paramétrage via la console MDT**

Accès aux options dans les **propriétés du partage de déploiement**, onglet :

- 🪟 **Windows PE**
  - 🎚️ Ajout de **fonctions complémentaires**
  - 🌐 Intégration des **pilotes réseau**, contrôleurs de disque, etc.



🔁 **Mise à jour indispensable après modification**

Après tout changement lié à Windows PE (paramètres, pilotes, etc.) :

- 🔄 Cliquer sur **Update Deployment Share**
- 🧱 Cela régénère les fichiers **Boot.wim** et **ISO** pour refléter les nouvelles configurations
