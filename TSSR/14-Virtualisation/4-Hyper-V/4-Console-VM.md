# Console VM

## **🧩 Console dédiée à chaque VM**

- Chaque **machine virtuelle** possède une **console graphique unique**
- Permet de gérer le **comportement de base** de la VM hébergée sur un serveur Hyper-V



## **⚙️ Paramètres disponibles**

- 🔌 **Commandes d’allumage et d’extinction**
- 🛠️ **Modification des paramètres matériels** de la VM
- 🎛️ **Réglage du comportement** des raccourcis clavier (touches d’action, Ctrl+Alt+Suppr, etc.)



## **🎮 Bandeau inférieur : raccourcis rapides**

- 📎 Fonctions essentielles accessibles rapidement
- 🖱️ Bouton pour envoyer **Ctrl + Alt + Suppr** à la VM
- 🔁 **Allumage** et **extinction** de la machine
- ⚠️ **Fermeture de la console ≠ arrêt de la VM**
  → La VM continue de fonctionner en arrière-plan sur l’hôte Hyper-V



## **🕓 Gestion des instantanés**

- 📸 **Création d’un instantané**
- 📂 **Consultation de l’historique**
- 🔄 Flèche retour : **restauration rapide** à l’état instantané précédent



## 🧾 **Gestion des fichiers VM**

- Les fichiers sont stockés dans C:\ProgramData\Microsoft\Windows\Hyper-V\ (dossier caché)
- Fichiers principaux :
  - **.vhdx** : disque dur virtuel
  - **.vmcx, .vmrs, .vmgs, .vmgf** : fichiers de config et état




