# Présentation🧩 **Qu’est-ce que WDS ?**

Windows Deployment Services est un **rôle** que l’on peut activer sur tout **serveur Windows Server**. Il permet de déployer des systèmes d’exploitation à distance, via le réseau, sans support physique.

🔧 **Console de gestion**

WDS dispose de sa **propre console d’administration**, accessible dans les outils du serveur. Elle permet de :

- Gérer les images d’installation et de démarrage
- Configurer le démarrage PXE
- Ajouter des pilotes
- Contrôler les options de déploiement



🚀 **Fonctionnalités principales de WDS**

🔌 **1. Démarrage réseau (PXE boot)**

WDS fournit les **informations nécessaires à l’amorçage des clients** via le réseau.

🔹 Cela permet à un poste **sans OS** de démarrer et charger un environnement de préinstallation pour lancer le déploiement.

📦 **2. Hébergement d’images**

WDS permet d’héberger :

- **Images de démarrage** (boot.wim) : mini OS pour lancer l’installation
- **Images d’installation** (install.wim) : système d’exploitation à déployer

🧱 **3. Intégration de pilotes**

WDS peut intégrer **des pilotes** dans les images.

🎯 Cela garantit que les périphériques (réseau, stockage, etc.) fonctionnent **dès la première utilisation** du système déployé.
