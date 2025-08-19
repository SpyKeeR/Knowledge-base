# Usage Master> **🖥️ Masterisation et Déploiement d'Images**

**🛠️ Ne Pas Redémarrer Après Sysprep**

- **Attention** : Ne redémarrez pas un poste après avoir exécuté **Sysprep** avant de capturer l'image. Si vous redémarrez, vous perdez tous les avantages du système préparé.
- Si cela se produit, il faut **réexécuter Sysprep** pour recommencer le processus.

**💾 Récupérer une Image d'un Système**

- Vous pouvez capturer l'image **à distance** sans affecter l'ordinateur cible.
- Utilisez un autre système d'exploitation pour **exporter** l'image du master.

**🌐 Méthodes de Capture de l'Image**

- **Amorçage PXE** (Preboot Execution Environment)
  - **Utilisez la carte réseau pour démarrer l'ordinateur et récupérer une image d'un serveur de déploiement.**
  - **Ce serveur automatise la capture du système et la sauvegarde de l'image au format VHD ou WIM.**
- **Support Externe**
  - **Si vous n'avez pas de serveur de déploiement, vous pouvez utiliser un DVD, une clé USB, ou tout autre support amorçable.**
  - **Windows Preinstallation Environment (WinPE) : Un mini-OS en ligne de commande qui contient des outils pour capturer et déployer des images.**

**🛠️ Outils et Commandes**

- **DISM** (Deployment Imaging Service and Management Tool)
  - Utilisé pour **mettre à jour** ou modifier une image capturée, par exemple, en ajoutant des **pilotes** ou des **applications**.
- **Vérification matérielle** :
  - Avant de déployer l'image, assurez-vous que le **poste cible** peut accueillir le système.
  - Utilisez des outils comme **PowerShell**, **Diskpart**, ou **BCDBoot** pour valider la configuration matérielle et préparer les volumes nécessaires.

**🔄 Mise à Jour d'une Image**

- **Mise à jour offline** : Après avoir capturé l'image, vous pouvez l'**ajuster** en mode offline (ajouter des logiciels, pilotes, etc.).
- Utilisez **DISM** pour effectuer ces modifications et assurer la compatibilité.

**✅ Assurer la Préparation de la Machine Cible**

- Téléchargez des utilitaires sur **Microsoft** pour vérifier que le poste cible est prêt à accueillir l'image.
- Assurez-vous que les **volumes** nécessaires sont créés et prêts à recevoir l'OS.
