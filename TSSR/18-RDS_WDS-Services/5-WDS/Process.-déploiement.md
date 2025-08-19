# Process. déploiement📁 **Structure des images dans WDS**

- 🗂️ Le partage WDS contient :
  - 🔧 **Images de démarrage** (Boot Images)
  - 💿 **Images d'installation** (Install Images)
- 📌 Chaque type d’image a un rôle spécifique dans le processus de déploiement



📦 **Étape 1 : Image de démarrage (WinPE)**

- 🖥️ Lors du **boot réseau PXE**, le client récupère une **image WinPE**
- 🧠 Cette image est **chargée en RAM** et fournit un **OS minimaliste**
- 🛠️ Elle permet d’utiliser les **outils nécessaires** pour la suite du déploiement
- 📏 Taille : quelques **centaines de Mo**



💽 **Étape 2 : Image d’installation**

- ✅ Une fois l'image WinPE chargée, le client récupère l'**image d’installation de l'OS**
- 📥 Cette image contient **le système d’exploitation complet** à déployer
- ⚙️ L’installation de Windows débute à partir de cette image



📌 **Résumé**

1️⃣ **WinPE (boot image)** : OS léger pour préparer le déploiement

2️⃣ **Install.wim (install image)** : OS complet installé sur le poste cible

🧩 Ces deux éléments sont **indispensables** au bon fonctionnement de WDS
