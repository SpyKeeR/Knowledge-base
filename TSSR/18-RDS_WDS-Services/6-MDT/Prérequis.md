# Prérequis🔗 **Composants complémentaires nécessaires**

Pour garantir le bon fonctionnement de MDT, plusieurs éléments doivent être récupérés et installés au préalable :

1️⃣ **Windows ADK (Assessment and Deployment Kit)**

- 📦 Contient les outils essentiels pour le déploiement (DISM, USMT, etc.)
- 💻 Téléchargement depuis le site officiel de Microsoft
- ✅ À installer en priorité avant MDT

2️⃣ **Add-on WinPE (Windows Preinstallation Environment)**

- 🧱 Indispensable pour créer des images de démarrage personnalisées
- 📤 Complément au kit ADK
- 💾 À installer après l’ADK, depuis le site de Microsoft



⬇️ **Téléchargement et installation de MDT**

- 🌐 Télécharger l’installeur MDT depuis le site officiel Microsoft
- ⚙️ Lancer l’installation une fois les prérequis précédents en place
- 📡 Connexion Internet requise pour l’installation (mode **en ligne**)
- 📴 Un mode **hors ligne** est également disponible si nécessaire



🧩 **Sélection des composants dans l’ADK**

Lors de l’installation du Windows ADK, il est essentiel de cocher au minimum :

- ✅ **Deployment Tools**
- ✅ **Windows Preinstallation Environment (WinPE)** *(via l’add-on)*
